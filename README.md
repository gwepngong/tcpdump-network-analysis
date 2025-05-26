tcpdump-network-analysis
An exploration of network traffic using tcpdump to identify; unusual DNS queries through port 53, HTTP(s), for suspected anomalies. 
 Project Overview

This project demonstrates the use of `tcpdump` for basic network traffic analysis, focusing on identifying unusual DNS queries. As a Linux system administrator and venturing into IT security understanding network flow is critical for troubleshooting and security monitoring. This exercise simulates an investigation into potential malware beaconing or data exfiltration attempts via DNS.

 Tools Used

* `tcpdump`
* `grep` (for filtering `tcpdump` output)
* `vim` (text editor)

Scenario / Objective

The objective was to capture and analyze DNS (UDP port 53) and http(s)( port 80, 443) traffic to look for:
* Unusually long DNS queries.
* Queries to known suspicious domains (simulated).
* High volume of queries from a single host.

 `tcpdump` Commands Used

Below are the primary `tcpdump` commands executed during this investigation.

1. Capturing all DNS (UDP port 53) traffic on `eth2` and saving to a file:
```bash
sudo tcpdump -i eth2 -nn -s0 port 53 -w dns_capture.pcap

     Explanation:
     * -i eth2: Specifies the network interface eth2.
     * -nn: Don't convert hostnames and port numbers (display numerically).
     * -s0: Snaplen of 0, meaning capture the entire packet.
     * port 53: Filters for traffic on port 53 (DNS).
     * -w dns_capture.pcap: Writes the raw packet data to dns_capture.pcap for later analysis with tools like Wireshark (though not shown here).
     2. Displaying real-time DNS queries and responses (textual output):
     sudo tcpdump -i eth2 -nn -s0 port 53 and udp and 'not host <IP>'

     Explanation:
     * Added udp to specify UDP protocol.
     * 'not host <IP>': Excludes traffic to/from the local router/DNS server to focus on client queries.
     3. Filtering for specific DNS queries; in this case "malicious.com".
     sudo tcpdump -i eth2 -nn -s0 port 53 and udp | grep "malicious.com"

     Explanation:
     * Pipes the tcpdump output to grep to find specific strings.
     Observations and Analysis
     During the capture, the following observations were made:
     * High Volume from a Single Host: <IP> was observed making a significantly higher number of DNS queries compared to other hosts on the network. This raised a red flag.
     * Suspicious Queries: Several queries originating from <IP> were made to domains like malicious.com and exfil.badsite.org. This triggered immediate investigation.
     * Long DNS Queries: Some queries contained unusually long subdomains (e.g., long.string.of.data.exfil.badsite.org). This is often indicative of DNS tunneling for data exfiltration.
     Example Output Snippet Here..
     10:35:01.987654 IP <IP>.12345 > 8.8.8.8.53: UDP, length 36
(Query: A? malicious.com.)

10:35:02.123456 IP <IP>.12346 > 8.8.8.8.53: UDP, length 80
(Query: A? long.string.of.data.exfil.badsite.org
