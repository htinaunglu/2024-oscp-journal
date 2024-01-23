# Active Information Gathering
- Learn to perform Netcat and Nmap port scanning
- Conduct DNS, SMB, SMTP, and SNMP Enumeration
- Understand Living off the Land techniques
		**When "Living off the Land", we can leverage several pre-installed and trusted Windows binaries to perform post-compromise analysis. These binaries are shortened as _LOLBins_ or, more recently, _LOLBAS_ to include Binaries, Scripts and Libraries.**
---

# SMB 
- **SMB Protocol Security History:**
    - Poor track record due to complex implementation and open nature.
    - From unauthenticated SMB null sessions to numerous bugs and vulnerabilities.
- **SMB Protocol Updates:**
    - Continuously updated and improved in tandem with Windows releases.
- **NetBIOS Service:**
    - Listens on TCP port 139 and several UDP ports.
    - Independent session layer protocol for local network communication.
- **SMB and NetBIOS Relationship:**
    - SMB uses TCP port 445.
    - NetBIOS is separate but often enabled with NBT for backward compatibility.
    - Enumeration of SMB and NetBIOS commonly performed together.