## Manual Enumeration
### Enumerate Active Directory using legacy Windows applications

```powershell
# display users in a domain
net user /domain
```

```powershell
# look for fishy names and query that specific user
net user jeffadmin /domain
```

```powershell
# display groups in the domain
net group /domain
```

```powershell
# display members in a group
net group "Sales Department" /domain
```
### Use PowerShell and .NET to perform additional AD enumeration

***LDAP is not exclusive to AD. Other directory services use it as well.***

```powershell
# LDAP PATH FORMAT
LDAP://HostName[:PortNumber][/DistinguishedName]
```

we need to look for _Primary Domain Controller_ (PDC). Only one in a domain.
DC with `PdcRoleOwner` property.

```powershell
# DN(Distinguished Name) Example
CN=Stephanie,CN=Users,DC=corp,DC=com
# CN = Common name
# DC = Domain Component (only in DN) | Top of the LDAP tree | Domain itself
# CN=Users | container's common name | parent container
```

In above LDAP path, we are interested in the Domain Component object, which is _DC=corp, DC=com_. If we added _CN=Users_ to our LDAP path, we would restrict ourselves by only being able to search objects within that given container.

```powershell
# Domain class from System.DirectoryServices.ActiveDirectory namespace
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
# SNIP
PdcRoleOwner        : DC1.corp.com
# SNIP
```

```powershell
## enumeration_a1.ps1

# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Print the variable
$domainObj
```

```Powershell
# bypass execution policy (to run ps1 scripts)
powershell -ep bypass

# run script
.\enumeration_a1.ps1
```

```powershell
## enumeration_a2.ps1

# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Store the PdcRoleOwner name to the $PDC variable
$PDC = $domainObj.PdcRoleOwner.Name

# Print the $PDC variable
$PDC
```

```powershell
# Using ADSI to obtain DN
([adsi]'').distinguishedName

# OUTPUT
DC=corp,DC=com
```

```powershell
## enumeration_a3.ps1

# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Store the PdcRoleOwner name to the $PDC variable
$PDC = $domainObj.PdcRoleOwner.Name

# Store the Distinguished Name variable into the $DN variable
$DN = ([adsi]'').distinguishedName

# Print the $DN variable
$DN
```

```powershell
# full LDAP path script
## enumeration_aFinal.ps1

$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"
$LDAP
```

```powershell
# run
.\enumeration_aFinal.ps1
## OUTPUT
LDAP://DC1.corp.com/DC=corp,DC=com
```

## Adding Search Functionality
*DirectoryEntry*,*DirectorySearcher* in *System.DirectoryServices*

```powershell
## enumeration_b1.ps1
# Directory & DirectorySearcher in `enumeration_aFinal.ps1`
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.FindAll()
```

```powershell
.\enumeration_b1.ps1

# OUTPUT
Path
----
LDAP://DC1.corp.com/DC=corp,DC=com
LDAP://DC1.corp.com/CN=Users,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Computers,DC=corp,DC=com
LDAP://DC1.corp.com/OU=Domain Controllers,DC=corp,DC=com
LDAP://DC1.corp.com/CN=System,DC=corp,DC=com
LDAP://DC1.corp.com/CN=LostAndFound,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Infrastructure,DC=corp,DC=com
LDAP://DC1.corp.com/CN=ForeignSecurityPrincipals,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Program Data,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Microsoft,CN=Program Data,DC=corp,DC=com
LDAP://DC1.corp.com/CN=NTDS Quotas,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Managed Service Accounts,DC=corp,DC=com
LDAP://DC1.corp.com/CN=Keys,DC=corp,DC=com
LDAP://DC1.corp.com/CN=WinsockServices,CN=System,DC=corp,DC=com
LDAP://DC1.corp.com/CN=RpcServices,CN=System,DC=corp,DC=com
LDAP://DC1.corp.com/CN=FileLinks,CN=System,DC=corp,DC=com
LDAP://DC1.corp.com/CN=VolumeTable,CN=FileLinks,CN=System,DC=corp,DC=com
LDAP://DC1.corp.com/CN=ObjectMoveTable,CN=FileLinks,CN=System,DC=corp,DC=com
...
```

Filtering `$dirsearcher.filter` is needed in our script! Go for *samAccountType* (attribute applied to all user, computer and group objects)
- start with 0x30000000 (decimal 805306368) for all user objects in the domain.

```powershell
## enumeration_b2.ps1
# filter normal user accounts
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368" # <-- here
$dirsearcher.FindAll()
```

```powershell
.\enumeration_b2.ps1

# OUTPUT

Path                                                         Properties
----                                                         ----------
LDAP://DC1.corp.com/CN=Administrator,CN=Users,DC=corp,DC=com {logoncount, codepage, objectcategory, description...}
LDAP://DC1.corp.com/CN=Guest,CN=Users,DC=corp,DC=com         {logoncount, codepage, objectcategory, description...}
LDAP://DC1.corp.com/CN=krbtgt,CN=Users,DC=corp,DC=com        {logoncount, codepage, objectcategory, description...}
LDAP://DC1.corp.com/CN=dave,CN=Users,DC=corp,DC=com          {logoncount, codepage, objectcategory, usnchanged...}
LDAP://DC1.corp.com/CN=stephanie,CN=Users,DC=corp,DC=com     {logoncount, codepage, objectcategory, dscorepropagatio...
LDAP://DC1.corp.com/CN=jeff,CN=Users,DC=corp,DC=com          {logoncount, codepage, objectcategory, dscorepropagatio...
LDAP://DC1.corp.com/CN=jeffadmin,CN=Users,DC=corp,DC=com     {logoncount, codepage, objectcategory, dscorepropagatio...
LDAP://DC1.corp.com/CN=iis_service,CN=Users,DC=corp,DC=com   {logoncount, codepage, objectcategory, dscorepropagatio...
LDAP://DC1.corp.com/CN=pete,CN=Users,DC=corp,DC=com          {logoncount, codepage, objectcategory, dscorepropagatio...
LDAP://DC1.corp.com/CN=jen,CN=Users,DC=corp,DC=com           {logoncount, codepage, objectcategory, dscorepropagatio
```

When enumerating AD, we are very interested in the _attributes_ of each object, which are stored in the _Properties_ field.

```powershell
## enumeration_b3.ps1
# adding nested loop, print each property on own line
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }

    Write-Host "-------------------------------"
}
```

```powershell
.\enumeration_b3.ps1

# OUTPUT
...
logoncount                     {173}
codepage                       {0}
objectcategory                 {CN=Person,CN=Schema,CN=Configuration,DC=corp,DC=com}
dscorepropagationdata          {9/3/2022 6:25:58 AM, 9/2/2022 11:26:49 PM, 1/1/1601 12:00:00 AM}
usnchanged                     {52775}
instancetype                   {4}
name                           {jeffadmin}
badpasswordtime                {133086594569025897}
pwdlastset                     {133066348088894042}
objectclass                    {top, person, organizationalPerson, user}
badpwdcount                    {0}
samaccounttype                 {805306368}
lastlogontimestamp             {133080434621989766}
usncreated                     {12821}
objectguid                     {14 171 173 158 0 247 44 76 161 53 112 209 139 172 33 163}
memberof                       {CN=Domain Admins,CN=Users,DC=corp,DC=com, CN=Administrators,CN=Builtin,DC=corp,DC=com}
whencreated                    {9/2/2022 11:26:48 PM}
adspath                        {LDAP://DC1.corp.com/CN=jeffadmin,CN=Users,DC=corp,DC=com}
useraccountcontrol             {66048}
cn                             {jeffadmin}
countrycode                    {0}
primarygroupid                 {513}
whenchanged                    {9/19/2022 6:44:22 AM}
lockouttime                    {0}
lastlogon                      {133088312288347545}
distinguishedname              {CN=jeffadmin,CN=Users,DC=corp,DC=com}
admincount                     {1}
samaccountname                 {jeffadmin}
objectsid                      {1 5 0 0 0 0 0 5 21 0 0 0 30 221 116 118 49 27 70 39 209 101 53 106 82 4 0 0}
lastlogoff                     {0}
accountexpires                 {9223372036854775807}
...

```

Just an another way to filter more properties!
```powershell
## enumeration_b3i.ps1
# Adding the name property to the filter and only print the "memberof" attribute in the nested loop

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="name=jeffadmin"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop.memberof
    }

    Write-Host "-------------------------------"
}
```

```powershell
.\enumeration_b3i.ps1

# OUTPUT
CN=Domain Admins,CN=Users,DC=corp,DC=com
CN=Administrators,CN=Builtin,DC=corp,DC=com
```

encapsulate the current functionality of the script into an actual function.

```powershell
## .\enumeration_b_func.ps1
A function that accepts user input

function LDAPSearch {    # <--- FUNCTION
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

```powershell
# Importing
Import-Module .\enumeration_b_func.ps1
```

```powershell
# Testing for user object search
LDAPSearch -LDAPQuery "(samAccountType=805306368)"
```

**objectClass=group**

```powershell
# Searching groups
LDAPSearch -LDAPQuery "(objectclass=group)"
```
	ONE NOTE: script can get you more groups than net.exe, why? Because new.exe shows just the global groups, while our script search for Domain Local Groups.


```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

```powershell

```

