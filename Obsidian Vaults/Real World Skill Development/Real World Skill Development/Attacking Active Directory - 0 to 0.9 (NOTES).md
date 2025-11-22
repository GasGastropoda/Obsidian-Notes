
# What is Active Directory?
- System allowing you to manage a set of computers and users that are all connected on the same network, from a central server
- AD allows you to do this by maintaining a centralized database that stores all the information about users. computers, policies, and permissions are all stored in one place.
- also allows IT to configure multiple devices using one server instead of individually configuring all devices every time, and allows users to access their content from any device, instead of having multiple devices configured under their profile.

# Domains
- Domain = the active directory network; set of connected computers sharing the AD DB
- Domain Controllers = central servers that manage the AD DB

## Domain Name
- each domain has an associated DNS name
- in many companies, the domain is just linked to their website's name. 
		- example: `contoso.com` 
- other companies prefer to use a different internal domain
	- example: `contoso.local`
		- SIDE NOTE: you don't have to own the domain name to use it as your AD Domain Name. Domain names are internal only. Companies name their AD Domain the same as their public domain for legacy, simplicity, and identity alignment. 
			- It's cleaner when everyone's email, UserPrincipalName (UPN), and login information match, making SSO, office, and Azure integration less confusing
			- Modern hybrid environments (public and private cloud = M365/Syncing services + AD) prefer to have the UPN's suffix matches a verified public domain
			- Public Cert Authorities can't issue certs for private TLDs, so if they a company has any  internal apps or servers that require certs for browsers or mobile devices, they need a public domain, otherwise you're stuck with self-signed certificates
			- enterprises built their domains when practices were different, so renaming an AD forest is too much work and it's best to keep the same structure
			- HOWEVER:
				- using the same name means you now have two DNS Zones with identical names (internal and external) = Split Brain DNS/Split Horizon
					- requires careful DNS management so users inside and outside of the network can resolve to it. ==If misconfigured, internal users might not be able to reach the public website, or external users might have access to private IPs==
			- The modern compromise to this is to keep the public root domain, but creating a dedicated internal subdomain `corp.contoso.com` or `ad.contoso.com`
- To resolve current user domain in powershell 
	- `$env:USERDNSDOMAIN`
	- `(Get-ADDomain).DNSRoot`
- To resolve current computer domain in powershell
	- `(Get-WmiObject Win32_ComputerSystem).Domain`
- Every domain can also be identified by its NetBIOS name
	- NetBIOS = Network Basic Input/Output System
		- designed by IBM before TCP/IP became dominant protocol. method for computers to identify and talk to each other on a local network instead of an IP address
		- Windows didn't kill NetBIOS, they wrapped it inside of IP, hence NetBIOS over TCP/IP (NBT)
		- Includes the following services:
			- Name Service (NBNS) = translates NetBIOS names into IP Addresses (mini-DNS for local networks)
				- example: Fileserver -> 192.168.1.10
			- Datagram Services = allows connectionless messages
			- Session Services = allows two applications to open a connection and exchange data
	- Whenever AD asks for the NetBIOS domain name, it'll be a short single-label version of the domain
		- the short name would appear in logins like `CONTOSO\Alice`
	- Although it's legacy, it's still needed for:
		- Windows networks, SMB shares, or environments that aren't fully on DNS
		- AD assigns a NetBIOS name to all domains to allow backwards compatibility
		- Newer systems rely almost entirely on DNS, not NetBIOS
	- EXPLOITABLE:
		- NetBIOS and Link-Local Multicast Name Resolution (LLMNR) can be abused for credential capture. Disabling them in secure environments is a part of network hardening.
- Every domain can also be identified by its Security Identifier (SID)
	- SID is used more by programs than users, but it's good to know how to obtain it
	- Resolving DNS name, NetBIOS name, and SID of a domain in powershell:
		- `Get-ADDomain | select DNSRoot,NetBIOSName,DomainSID`

## Forests
- using DNS is nice, especially since it allows for subdomains.
	- example: company can have a root domain (`contoso.local`) and also have subdomains for different departments or offices (ex. `it.contoso.local`, `sales.contoso.local`)
- The "tree" of domains = Forest
- The name of a forest = root domain of the tree
- retrieving forest information in powershell:
	- `Get-ADForest`
- within a forest, all domains have their own database and domain controller. 
- NOTE TO SELF: there's no "root DC" all DCs to each domain are like a gate allowing entrance to the domain, and DCs are modified through communication protocols in AD. 
- The forest's root domain anchors the forest, and it stores all schema, configurations, and holds the global catalog
- the forest itself is a collection of all the domains (including the root and subdomains) that share the same schema, catalogue, and hold an automatic two-way transitive trust
- every subdomain has it's own DC and directory database, alongside trusting all other domains in the same forest
- user accounts are created in one domain, not in the forest root. Users in this separate domain can authenticate to resources in other domains IF the forest is configured to allow this to happen
- The use of complex forests has reduced in modern use, as companies implement Organizational Units (OUs) to group users logically (department, privilege, role). Many organizations use one forest, one domain, and several OUs.
- Why this is important for penetration testing:
	- Inter-domain and inter-forest trust is a great lateral movement path
		- misconfigured/overly broad trust within a forest creates attacker pivot opportunity
		- cross domain ACLs, DCSync rights, or shared service accounts can pose a risk to a forest-wide compromise
		- understanding these relationships can also help you trace how a single domain breach can result in forest-wide control

## Functional Modes
- domains/forests have their own "version" known as **functional mode**. 
- characteristics are available depending on the mode used
- modes are named based on the minimum Windows Server OS required to work with them:
	- Windows2000
	- Windows2000MixedDomains
	- Windows2003
	- Windows2008
	- Windows2008R2
	- Windows2012
	- Windows2012R2
	- Windows2016
- to find the mode of a forest or domain in powershell: 
	- `(Get-ADForest).ForestMode`
	- `(Get-ADDomain.DomianMode`
- example: finding a domain/forest in Windows2012 mode means that all DCs are at least WinServer 2012.
- you need to know the mode in order to use characteristics of the domain.
	- ex. the protected users group requires Windows2012R2  mode

## Trusts
- allow users to access other domains in the same forests via linked connections
- formally defined as a connection from one domain to another (authentication/authorization connection)

### Trust Direction
- considered to be the opposite to the access direction
- trust in this situation doesn't flow both ways, one side requests access, the other allows it.
- the access direction is the result of accepting the trust direction
- **Inbound/Incoming trust** = trust directed through the current domain
	- allows users of domain to access the other domain
- when two domains are connected by an incoming and outgoing trust = **bidirectional trust** (although it's two concurrent trusts)
- seeing domain trusts in powershell:
	- `nltest /domain_trusts`

### Trust Transitivity
- **nontransitive trust** = trust can only be used by the two sides of the trust (trusting, trusted)
- **Transitive trust** = acts as a bridge and being used for a 3rd domain connected with the domains connected by transitive trust
	- "transit" = "crossing the bridge"

### Trust Types
- **Parent-Child** = default trusts created between a parent and child domain
- **Forest** = trust to share resources between forests. Any domain of the forest can access any other domain on the other forest (if direction and transitivity allows)
	- if Forest trust is misconfigured, this may allow control over another forest
- **External**  = trust to connect to a specific domain in a non-trusted forest
- **Realm** = special trust to connect AD and a non-windows domain
- **Shortcut** = when two domains in a forest communicate but aren't directly connected, you can create a direct trust shortcut

### Trust Key
- trust is actually communication between the DC of the trusted and the DC of the truster (or any transitive domain's DC)
- how communication is established depends on the protocol used, but the DC needs to share a key to keep communications secure. this is the **trust key**
- whenever a trust is established, a **trust account** is created in the domain DB as if it was a user (name ends in $). 
- trust key is stored as if it was the password of that trust account (in NT hash and kerberos Keys)

### Further Reading On Trusts
-  [It’s All About Trust – Forging Kerberos Trust Tickets to Spoof Access across Active Directory Trusts](https://adsecurity.org/?p=1588)
- [Active Directory forest trusts part 1 - How does SID filtering work?](https://dirkjanm.io/active-directory-forest-trusts-part-one-how-does-sid-filtering-work/)
-  [Inter-Realm Key Roasting (well… within the first 30 days)](https://blog.xpnsec.com/inter-realm-key-roasting/)

## Users
- to easily manage users in AD, they're stored as objects in the central DB, which allows it to be consulted and manipulated from any point in the domain

### User Properties
- **User Identifiers**
	- user object stores different kinds of data and each are defined by specific attributes
	- to identify a user by its username = **SAMAccountName** attribute; the SID can also identify a user
		- User SID is similar to domain SID (it's actually a combination of domain SID and the user's Relative Identifier (RID) (the last number appearing in the user's SID))
	- some tools display SID instead of the username (since it's used in structures like security descriptors) so being aware of its format is important
	- `DistinguishedName` is used by LDAP API to identify objects. If doing a query to the database via LDAP, you'll see objects be referenced in that manner. 
- **User Secrets**
	- the database needs to store secrets to allow the DC to authenticate the user
	- passwords are not stored in plaintext, but there are secrets derived from it that are saved:
		- NT hash (older accounts have the LM hash)
		- kerberos keys
	- user secrets cannot be retrieved by non-admin users, nor do domain computers have access to them. Authentication is left to the DC.
	- user secrets can only be retrieved with admin privileges (or equivalent) to:
		- dump the domain database with a desync attack
		- grabbing `C:\Windows\NTDS\ntds.dit` file from the DC
- **LM/NT Hashes**
	- both stored in windows local Security Account Manager (SAM) and AD NTDS DBs to authenticate local and domain users
	- both hashes are 16 bytes long
	- LM Hashes are weak and have not been used since Vista/Server 2008
	- ==Procedure to create an LM hash:==
		1.  convert password to uppercase (reduces search space for brute force attack)
		2. if pwd < 14 char, pad with NULL characters until the length is 14 char. if pwd > 14 char, it's truncated (useless to have passwords greater than 14 characters)
		3. password is split into two strings of 7 bytes
		4. each string is used a key to encrypt the `KGS!+#$%` string with DES, this results in two hashes
		5. the results are combined in order to form the hash
	- ==Procedure to create an NT hash:==
		- keep in mind, it does not use a salt, so it can be cracked with rainbow tables or any other precomputed values
		1. starts with the user's password in plaintext
		2. converts the password into UTF-16LE byte sequence
		3. applies MD4 to the bytes
		4. the result is a 128-bit value represented as a 32 char hex string
	- Keep in mind that NT hashes can also be called an NTLM hash. This can be confusing since the NTLM protocol has hashes of its own.
	- tools extracting LM and NT hashes will return an output with several lines. Each line is a user, and the format would be: `<username>: <RID>:<LM>:<NT>:::` 
		- if LM is not in use, there will be the hash of an empty string: `aad3b435b51404eeaad3b435b51404ee`
	- Pentesters need to recognize NT hashes. Even though they're not passwords, they're used for authentication in windows machines. getting your hands on them allows you to perform:
		- **Pass-The-Hash (PtH)** = stealing a hashed credential and using it to create a new user session
		- **Overpass-The-Hash** = also known as "pass-the-key" or "hash-to-ticket"; attacker uses the has to request and receive a valid kerberos TGT.
	- you can try to crack the hashes themselves with hashcat to recover the original password. 
- **Kerberos Keys**
	- they're also derived from the user password
	- used in the kerberos authentication protocol
	- used to ask for kerberos tickets representing the user in the authentication protocol
	- there are several keys, each one used by different versions of kerberos encryption:

| Kerberos Key | Algorithm it's used by                                                                                        |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| AES 256      | AES256-CTS-HMAC-SHA1-96; commonly used by kerberos. Pentesters should use this one to avoid triggering alarms |
| AES 128      | AES128-CTS-HMAC-SHA1-96                                                                                       |
| DES          | deprecated DES-CBC-MD5                                                                                        |
| RC4          | NT hash of the user; RC4-HMAC                                                                                 |
- **UserAccountControl**
	- user class property
	- not to be confused with User Account Control mechanism
	- contains a series of flags relevant to security and domain.

| Flag                           | Description                                                                                                                                |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| ACCOUNTDISABLE                 | account is disabled                                                                                                                        |
| DONT_REQUIRE_PREAUTH           | account doesn't need kerberos pre-authentication                                                                                           |
| NOT_DELEGATED                  | account cannot be delegated through kerberos                                                                                               |
| TRUSTED_FOR_DELEGATION         | Kerberos Unconstrained Delegation is enabled for this account and its services. `SeEnableDelegationPrivilege`is needed to modify this flag |
| TRUSTED_TO_AUTH_FOR_DELEGATION | kerberos S4U2Self extension is enabled for the account and its services. `SeEnableDelegationPrivilege` is needed to modify this flag       |
- **Other user properties**
	- other properties that are useful when pentesting:


| property                 | description                                                                                                                                                          |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description              | user description. can give an idea of what the user's permissions are. Can sometimes include their password                                                          |
| AdminCount               | indicates if the user (or group) is protected by the `AdminSDHolder` object, or has previously been so. Since it's sometimes not updated, it's used only a reference |
| MemberOf                 | the groups which the user is a member. Logical property and generated from the groups Members property                                                               |
| PrimaryGroupID           | user's primary group. Doesn't appear under MemberOf property.                                                                                                        |
| ServicePrincipalName     | user's services. useful in a kerberoast attack.                                                                                                                      |
| msDS-AllowedToDelegateTo | services which the user (and its own services) can impersonate clients via Kerberos Constrained Delegation. `SeEnableDelegationPrivilege`is required to modify.      |

### Important Users
- multiple ways to check what users are available:
	- on the DC: `net user /domain`
	- in powershell: `get-aduser -filter * | select samaccountname`
	- both commands don't need special privileges
- the default administrator user is the most privileged account in the domain. if you can compromise this account, you have control of the domain and even a forest via SID History attack
- `krbtgt` account is important. secrets (NT hash and kerberos keys) are used to encrypt the tickets used by kerberos to authenticate users. if you can compromise this account, you can create "Golden Tickets".
	- this account can only be compromised by dumping the domain database (since it's only used in the DC, an act requiring administrative privileges in the domain)

### Computer Accounts
- in an organization, each person has their own user, and certain people can have more than one user to perform specific tasks
- each computer in a domain has its own user, since they also need to perform actions of their own in a domain (ex. updating group policies, verifying domain credentials, etc)
- difference between user accounts and computer accounts: 
	- users are stored in a **user class** within the database
	- others are stored as instances of the **computer class** (a subclass of user class)
	- computer account names are the hostname followed by a dollar sign
		- you can check them using this command in powershell:
			- `get-adobject -ldapfilter "objectlcass=user" -properties samaccountname | select samaccountname`
- computer objects save information about their OS. the info can be retrieved using the attributes `operatingsystem` and `operatingsystemversion`
- many organizations follow a naming convention, so if you can figure that out, you can determine which accounts and computers are privileged and can be used to access sensitive information. 
	- you can check attributes of objects to find information about them (and potentially cleartext passwords) by using attributes like `description`
		- the `find-domainobjectpropertyoutlier` cmdlet of [PowerView ](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)can be useful for this purpose!

### Trust Accounts
- some usernames can end with a dollar sign!
- when a trust is established, a user object is created in each domain that stores the trust key. The name of the user is the NetBIOS name of the other domain, followed with a dollar sign
	- if you can get the secrets of this account, you can create inter-realm kerberos tickets
	- the object stores trust keys


## Groups
- groups are stored in the domain database and can be identified by `samaccountname` attribute or SID
- to list the groups and their members: `get-adgroup -filter * | select samaccountname`

### Important Groups
- **administrative groups:**
	- for attackers, the best default group to look into is the Domain Admins group, which gives admin privileges to its member in the domain. Knowing who's in this domain is important.
		- checking domain admins group information in powershell: `get-adgroup "Domain Admins" -Properties members,memberof`
	- there are other groups that give privileges. 
	- ***Enterprise Admins:*** provide admin privileges in the entire forest.
		- only exists in the forest's root domain, but added by default to the administrator's group of all domains in the forest
	- ***Domain Admins*** are added to the administrators group of the domain, as well as the administrators groups of the domain computers. 
- **DNSAdmins**:
	- allow its members to ==execute code in DCs as SYSTEM by using an arbitrary DLL==
- **Protected Users**:
	- allows the enforcement of security of accounts. 
	- members of the group aren't allowed to:
		- authenticate with NTLM (only kerberos)
		- use DES or RC4 encryption types in kerberos pre-authn
		- be delegated with unconstrained/constrained delegation
		- renew kerberos TGTs beyond initial 4hr lifetime