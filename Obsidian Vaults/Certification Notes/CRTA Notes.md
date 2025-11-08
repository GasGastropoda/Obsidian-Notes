# 1.1 What is Red Teaming?

- ==Red Team==: group of hackers that test an organization's infrastructure. 
- red team attacks are divided into 3 groups:
	- Cyber (web, network, cloud technologies)
	- Social Engineering
	- Physical Penetration Testing
- Red Team Ops are similar to a pentest but more targeted
- main goal is to test an org's Detection and Response capabilities
- Red Team attacks are similar to TA's
	- ==Emulation==: Copying attack techniques
	- ==Simulation==: Mimicking TA behavior
- RT will try to exfiltrate without leaving footprints or getting caught
- the attack simulations used by the read team are used to improve the effectiveness of an organization's structure
- big companies like microsoft and other defense agencies use red teams to assess their own networks

## Pentesting  vs  Red Teaming


| Pentesting                                                                                                                                   | Red Teaming                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| An attack against a host, network, or application to measure and identify the risk associated with exploiting that target in the environment | the process  of using real world tactics that are employed by threat actors to measure the effectiveness of the people and technologies used to defend the environment |
| has a bigger emphasis on reducing  exposed vulnerabilities                                                                                   | has a bigger emphasis on training and measuring risk and defense capabilities of an organization                                                                       |
| scope is limited to 1 to 2 specific systems or networks                                                                                      | can be a specific part of an organization or the whole company                                                                                                         |
| makes assumptions about the environment, may not be allowed to test where they're told not to go                                             | makes no assumptions, attempts to compromise everything and changes strategies and techniques when needed                                                              |
| uses tools present at that time during testing                                                                                               | constantly searches new exploits, vulnerabilities, and implements new attacks as soon as they're discovered                                                            |


# 1.2 Red Team Attack Lifecycle
- STAGE 1: Extensive OSINT
- STAGE 2: Initial access and execution
- STAGE 3: persistence and privilege escalation
- STAGE 4: lateral movement with defensive evasion
- STAGE 5: discovery and data collection
- STAGE 6: data exfiltration and high level persistence

## Extensive OSINT
- gathering more and more information about the target organization
- social media sites, platforms where employees are active
- attackers with tons of information available tries to find the sensitive ones and use that information for further exploitation purposes

## Initial Access and Execution
- using multiple entry vectors to access the internal network
- some forms of exploitation include:
	- external remote services
	- misconfigurations in web applications
- all of which may lead to the internal network's gateway
- it all depends on the technologies used by the organization that were identified in the previous step
- execution is the process in which code controlled by the attacker is launched onto a target machine
	- ex. an adversary might use a remote access tool to run a command prompt tool that runs network discovery

## Persistence and Privilege Escalation
- attackers look for techniques to keep their access on a system even after a system restarts or if the credentials are changed
	- ex. resetting the password of a low profile user and using their account as a network backdoor
- privilege escalation is the act of gaining higher level permissions on a network or system. common approaches of doing so include:
	- exploiting system weaknesses
	- device misconfigurations
	- device vulnerabilities
- examples of elevated access accounts:
	- SYSTEM/root level
	- Local administrator
	- User with any admin capabilities
	- privileged groups

## Lateral Movement
- when an attacker compromises  or gain control of an asset in a network and then uses that asset to move onto other devices in the same network
- an attacker might install a remote access tool to accomplish lateral movement or use legitimate credentials with native network and OS tools, which at times can be stealthier
- examples of lateral movement:
	- internal phishing - gaining access to additional information to exploit other users within the same organization after they already have access to accounts or systems in the environment
	- remote services - attackers may use valid credentials to log into services and then perform operations in those command line interfaces

## Defensive Evasion
- avoiding detection throughout the compromise
- attackers bypass detection via obfuscation, hiding in trusted processes, and disabling security software
- defensive evasion benefits from discovery, but it's related to understanding how an attacker can avoid network defenders (by understanding what security tools are on a system or what processes are running
- an example of defensive evasion::
	- impairing defenses - this could be disabling firewalls or AV capabilities that defenders use to audit activity and identify any malicious behavior

## Discovery
