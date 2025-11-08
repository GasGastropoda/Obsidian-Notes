
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