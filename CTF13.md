# Lab Report: Packet Sniffing and Spoofing


This lab focus on understanding what Siniffing and Spoofing is as well as how they can be exucuted and the dangers they can cause in network communication.

### 3.1 Task 1.1: Sniffing Packets

#### Task 1.1 A

The objective of this task is to create a Python program for packet capturing using Scapy. We started things of by running the ifconfig command to obtain the necessary interfaces for the Python script. After that, we developed a Python script to capture and display the captured packets, utilizing the provided lab template:

```python
#!/usr/bin/python

from scapy.all import *

def print_pkt(pkt):
    pkt.show()

interfaces = ['br-2c23230cebd7', 'enp0s3', 'lo']
pkt = sniff(iface=interfaces, filter='icmp', prn=print_pkt)
```

Upon completion, we runned the script in one container and initiated a ping to google.com in another container. Running the program with sudo permissions allowed successful packet capture. However, doing the same thing without sudo permissions resulted in an error indicating insufficient permissions to perform the operation.


#### Task 1.1 B

The objective of this task is to refine the packet capture process by implementing filters to exclude unwanted packets. This involved completing three specific tasks.

1. **Filtering ICMP Packets:**
   In the first task, we aimed to capture only ICMP packets. This was accomplished by setting the filter parameter of the sniff function to 'icmp':
   ```python
   sniff(iface=interfaces, filter='icmp', prn=print_pkt)
   ```
   Since we had already implemented this filter in the previous task, no additional action were required.

2. **Capturing Specific TCP Packets:**
   The second task involved capturing only TCP packets originating from a specific IP address (10.9.0.5) and destined for port 23. To achieve this, we adjusted the filter parameter to 'tcp && src host 10.9.0.5 && dst port 23'. The script was then runned, and in another VM, we initiated a ping to 10.9.0.5 using the command 'telnet 10.9.0.5'. The results were as follows:

   [Provide the specific results obtained during testing.]

3. **Capturing Packets from a Subnet:**
   The final task focused on capturing packets from a designated subnet, in this case, we used the subnet 128.230.0.0/16 as specified in the lab. To accomplish this, we changed the filter parameter to 'dst net 128.230.0.0/16'. Once that was done,we runned the script in one VM while pinging 128.230.0.0 from another VM. Obtained the following  results:

   [Provide the specific results obtained during testing.]



### 3.2 Task 1.2: Spoofing ICMP Packets

The objective of this task is to simulate ICMP echo request packets using Scapy for the purpose of packet spoofing. To accomplish this, we created the following Python script, which sends ICMP packets to our VM ('10.9.0.6') from an arbitrary source, in this case, '10.9.0.5':

```python
# [Insert the actual content of the Python script here]
```

Once that was finished, we initiated a Wireshark capture in our VM and runned the Python script in another VM. After analising the results we concluded that the VM successfully received and accepted the packets sent by us.


### 3.3 Task 1.3: Traceroute

The goal of this task is to create a script using Scapy to determine the distance, measured in terms of the number of routers, between the VM and a designated destination. For this purpose, we created the following Python script:

```python
# [Insert the actual content of the Python script here]
```

The program uses an automated approach to address the problem, eliminating the need to manually specify the Time-To-Live (TTL). For our testing, we selected the IP address '8.8.4.4' as the destination and runned the script, obatining the following results:

We can observe that it took 12 hops to reach the destination.



### 3.4 Task 1.4: Sniffing and-then-Spoofing

The objective of this task is to integrate both sniffing and spoofing techniques into a single program, creating a sniff-and-then-spoof application. The purpose is to capture packets sent by the user container , and subsequently, send responses to the user container. To achieve this, we developed the following Python script:

```python
# [Insert the actual content of the Python script here]
```

The script captures a packet, identifies if it is an echo request, and, if so, generates a response based on the request information, sending it back to the source. In our testing, we were asked to ping three different machines and analyze the results.

1. **Machine: 1.2.3.4 (Non-Existing Host on the Internet):**
   - Conducted a Wireshark capture in one of the user VMs.
   - Pinged 1.2.3.4 from the user VM without running the Python script. No response was observed, which is expected as the host does not exist.
   - Repeated the ping to 1.2.3.4, this time running the script in the attacker VM. Packets were received and accepted, demonstrating successful sniffing and spoofing.

2. **Machine: 10.9.0.99 (Non-Existing Host on the LAN):**
   - Repeated the process with the non-existing host on the LAN (10.9.0.99).
   - In both situations, the destination was unreachable. This is because the user VM lacks information on how to reach the destination in the ARP table. Consequently, even with the script running, no packets passed the sniffing filters, resulting in no packets being sent.

3. **Machine: 8.8.8.8 (Existing Host on the Internet):**
   - Repeated the same process with an existing host on the Internet (8.8.8.8).
   - Requests were received in both scenarios. The only difference was that, when the script was running, repeated packets were received and detected by the user machine.

