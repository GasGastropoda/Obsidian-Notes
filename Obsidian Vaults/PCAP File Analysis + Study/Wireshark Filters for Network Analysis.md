1. **IP Address Filters**
	1. Captures all traffic where 10.0.0.1 is either src or dest:
		1. ```ip.addr == 10.0.0.1 ```
	2. Captures all traffic to or from any address in the 10.0.0.0/24 subnet:
		1. ```ip.addr == 10.0.0.0/24 ```
	3. Filters traffic originating from 10.0.0.1 and destined for 10.0.0.2
		1. ```ip.src == 10.0.0.1 && ip.dst == 10.0.0.2```
	4. excludes all traffic to or from 10.0.0.1
		1. ```!(ip.addr == 10.0.0.1)```
2. **ICMP Filters - refer to [[ICMP Types and Messages]] for more.**
	1. Filters ICMP messages by types
		1. ```icmp.type == 3```
3. **Protocol Filters:**
	1. Captures all TCP and UDP traffic
		1. ```tcp or udp```
	2. Captures HTTP or DNS traffic
		1. http or dns
	3. an example of a useful filter for network administrators to analyze http traffic to secure websites:
		1. ```tcp.dnsport == 443```
4. **Port-Based Filters:**
	1. capturing TCP traffic on Port 80:
		1. ```tcp.port == 80```
	2. capturing traffic originating from source port 80:
		1. ```tcp.srcport == 80 ```
5. **TCP Flag Filters - refer to [[TCP Flags and Hex Values]] for more**
	1. showing communications with a SYN flag, which is used for connection establishment:
		1. ```tcp.flags.syn == 1```
	2. Filters by packets with both SYN and ACK flags set:
		1. ```tcp.flags == 0x012```
	3. can be used to analyze the start of TCP connections or detect SYN Floods
6. **HTTP Filters - Capturing [[HTTP Requests and Response Codes]]**
	1. filtering by HTTP GET requests:
		1. ```http.request.method == "GET"```
	2. filtering by responses with 404 error:
		1. ```http.response.code == 404```
	3. filtering traffic by host:
		1. ```http.host == "www.abc.com"```
7. **TLS Filters - refer to [[TLS Handshake Codes]] for more info**
	1. filtering the process where the client and server agree on encryption methods and exchange keys for secure communication:
		1. ```tls.handshake```
	2. Isolating 'ClientHello' packets in the handshake
		1. ```tls.handshake.type == 1```
8. **DHCP and MAC Address filters**
	1. filtering DHCP traffic in the 10.0.0.0/24 subnet:
		1. ```dhcp and ip.addr == 10.0.0.0/24```
		2. filtering DHCP traffic for a specific MAC address:
			1. ```dhcp.hw.mac_addr == 00:11:22:33:44:55```
9. **DNS Filters**
	1. filtering DNS responses resolving to "cnn.com"
		1. ```dns.resp.name == cnn.com```
	2. Security scenario: analyzing suspicious DNS queries (DNS Tunneling) using the filter:
		1. ```dns.query.name contains "xyz"```
10. **Frame Content and Length Filters**
	1. filtering packets by a specific keyword contained:
		1. ```frame contains "keyword"```
	2. capturing packets larger than 1000 bytes
		1. ```frame.len > 1000```
11. **Ethernet Filters**
	1. filtering traffic with a specific MAC address:
		1. ```eth.addr == 00:11:22:33:44:55```
	2. filtering eth frames with specific byte patterns
		1. ```eth[0x47:2] == 01:80```
	3. useful when diagnosing ethernet-level issues or detecting hardware traffic
12. **Special Filters:**
	1. excluding any traffic from resolution protocols (ARP, ICMP, STP)
		1. ```!(arp or icmp or stp)```
	2. filtering packets with a VLAN ID of 100
		1. ```vlan.id == 100```
		
		   