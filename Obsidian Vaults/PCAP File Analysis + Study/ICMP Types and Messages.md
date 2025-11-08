
**The following table lists default ICMP Types:**

| ICMP TYPE | MESSAGE                                                                 |
| --------- | ----------------------------------------------------------------------- |
| 0         | Echo Reply                                                              |
| 3         | Destination Unreachable                                                 |
| 4         | Source quench                                                           |
| 5         | Redirect                                                                |
| 8         | Echo                                                                    |
| 9         | Router advertisement                                                    |
| 10        | Router selection                                                        |
| 11        | Time exceeded                                                           |
| 12        | Parameter Problem                                                       |
| 13        | Timestamp                                                               |
| 14        | Timestamp reply                                                         |
| 15        | Information request                                                     |
| 16        | Information reply                                                       |
| 17        | Address mask request                                                    |
| 18        | Address mask reply                                                      |
| 30        | Traceroute                                                              |
| 40        | Photuris                                                                |
| 41        | ICMP messages utilized by experimental mobility protocols (eg. Seamoby) |
| 42        | Extended Echo request                                                   |
| 43        | Extended echo reply                                                     |
| 253       | RFC3692-style Experiment 1                                              |
| 254       | RFC3692-style Experiment 2                                              |
| 255       | Reserved                                                                |

**The following tables list the default ICMP codes:**

*Table 1. ICMP Type 3: Destination Unreachable Codes*

| Destination Unreachable Code | Description                                                           |
| ---------------------------- | --------------------------------------------------------------------- |
| 0                            | Net is unreachable                                                    |
| 1                            | Host is unreachable                                                   |
| 2                            | Protocol is unreachable                                               |
| 3                            | Port is unreachable                                                   |
| 4                            | Fragmentation is needed and ```Don't Fragment``` was set              |
| 5                            | Source route failed                                                   |
| 6                            | Destination network is unknown                                        |
| 7                            | Destination host is unknown                                           |
| 8                            | Source host is isolated                                               |
| 9                            | Communication with destination network is administratively prohibited |
| 10                           | Communication with destination host is administratively prohibited    |
| 11                           | Destination network is unreachable for type of service                |
| 12                           | Destination host is unreachable for type of service                   |
| 13                           | Communication is administratively prohibited                          |
| 14                           | Host precedence violation                                             |
| 15                           | Precedence cutoff is in effect                                        |

*Table 2. ICMP Type 5: Redirect Codes*

| Redirect Code | Description                                           |
| ------------- | ----------------------------------------------------- |
| 0             | Redirect datagram for the network (or subnet)         |
| 1             | Redirect datagram for the host                        |
| 2             | Redirect datagram for the type of service and network |
| 3             | Redirect datagram for the type of service and host    |

*ICMP Type 9: Router Advertisement*

| Code | Description                   |
| ---- | ----------------------------- |
| 0    | Normal router advertisement   |
| 16   | Does not route common traffic |



*Table 3. ICMP Type 11: Time Exceeded Codes*

| Time Exceeded Code | Description                       |
| ------------------ | --------------------------------- |
| 0                  | TTL exceeded in transit           |
| 1                  | Fragment reassembly time exceeded |

*Table 4. ICMP Type 12: Parameter Problem Codes*


| Parameter Problem Code | Description                 |
| ---------------------- | --------------------------- |
| 0                      | Pointer indicates the error |
| 1                      | Missing a required option   |
| 2                      | Bad length                  |

*ICMP Type 40: Photuris*


| Code | Description           |
| ---- | --------------------- |
| 0    | Bad SPI               |
| 1    | Authentication failed |
| 2    | Decompression failed  |
| 3    | Decryption failed     |
| 4    | Need authentication   |
| 5    | Need authorization    |

*ICMP Type 43: Extended Echo Reply*


| Codes | Description                       |
| ----- | --------------------------------- |
| 0     | No error                          |
| 1     | Malformed query                   |
| 2     | No such interface                 |
| 3     | No such table entry               |
| 4     | Multiple interfaces satisfy Query |
