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

```powershell
pass
```
