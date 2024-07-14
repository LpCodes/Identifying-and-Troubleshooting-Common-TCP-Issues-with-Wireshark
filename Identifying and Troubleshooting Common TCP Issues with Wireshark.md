### Identifying and Troubleshooting Common TCP Issues with Wireshark

How to identify the problem using Wireshark logs and suggest potential resolutions.

### 1. Connection Establishment Issues

**Scenario**: Clients are unable to establish a connection to the server.

**Symptoms**: SYN packets sent by the client do not receive SYN-ACK responses from the server.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
1       0.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
2       1.000000    192.168.1.2     192.168.1.1     TCP      60     [TCP Retransmission] 49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
3       2.000000    192.168.1.2     192.168.1.1     TCP      60     [TCP Retransmission] 49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
```

**Explanation**:
- **Packet 1**: The client (192.168.1.2) sends a SYN packet to the server (192.168.1.1).
- **Packets 2 & 3**: The client retransmits the SYN packet because it did not receive a SYN-ACK response.

**Resolution**:
- **Verify Server Availability**: Ensure the server is up and running.
- **Check Firewall Settings**: Confirm that there are no firewall rules blocking the connection.
- **Network Configuration**: Check for any network misconfigurations or issues with routing.

### 2. Retransmissions and Timeouts

**Scenario**: Delays in data transmission, leading to retransmitted packets.

**Symptoms**: Packets are retransmitted due to timeouts.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
10      3.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [PSH, ACK] Seq=1 Ack=1 Win=64240 Len=50
11      3.100000    192.168.1.1     192.168.1.2     TCP      60     [TCP Dup ACK 10#1] 80 → 49716 [ACK] Seq=1 Ack=1 Win=64240 Len=0
12      4.000000    192.168.1.2     192.168.1.1     TCP      60     [TCP Retransmission] 49716 → 80 [PSH, ACK] Seq=1 Ack=1 Win=64240 Len=50
```

**Explanation**:
- **Packet 10**: The client sends data (PSH, ACK) to the server.
- **Packet 11**: The server sends a duplicate ACK, indicating it didn't receive the expected data.
- **Packet 12**: The client retransmits the data.

**Resolution**:
- **Network Stability**: Ensure that the network is stable and free from high latency.
- **Packet Loss**: Check for packet loss and resolve any underlying issues causing it (e.g., faulty hardware, congested links).

### 3. Duplicate ACKs and Fast Retransmits

**Scenario**: Multiple duplicate ACKs received, leading to fast retransmits.

**Symptoms**: Multiple duplicate ACKs and fast retransmissions occur.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
20      5.000000    192.168.1.1     192.168.1.2     TCP      60     [TCP Dup ACK 19#1] 80 → 49716 [ACK] Seq=1 Ack=51 Win=64240 Len=0
21      5.000500    192.168.1.1     192.168.1.2     TCP      60     [TCP Dup ACK 19#2] 80 → 49716 [ACK] Seq=1 Ack=51 Win=64240 Len=0
22      5.001000    192.168.1.2     192.168.1.1     TCP      60     [TCP Fast Retransmission] 49716 → 80 [PSH, ACK] Seq=51 Ack=1 Win=64240 Len=50
```

**Explanation**:
- **Packets 20 & 21**: The server sends duplicate ACKs for the same sequence number, indicating a missing segment.
- **Packet 22**: The client performs a fast retransmission of the missing segment.

**Resolution**:
- **Network Reliability**: Improve network reliability to reduce the chances of packet drops.
- **Hardware Issues**: Replace or repair any faulty network hardware that may be causing packet corruption or loss.

### 4. TCP Window Size Problems

**Scenario**: Reduced throughput due to small TCP window size.

**Symptoms**: Slow data transfer rates.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
30      6.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [ACK] Seq=101 Ack=101 Win=256 Len=0
31      6.100000    192.168.1.1     192.168.1.2     TCP      60     80 → 49716 [PSH, ACK] Seq=101 Ack=101 Win=256 Len=50
```

**Explanation**:
- **Packets 30 & 31**: The TCP window size is set to a small value (256), limiting the amount of data that can be sent before waiting for an acknowledgment.

**Resolution**:
- **Window Size Adjustment**: Adjust TCP window size settings on the endpoints to a suitable value to maximize throughput.
- **Network Latency**: Ensure that network latency is minimized to allow for larger window sizes to be effective.

### 5. TCP Reset (RST) Issues

**Scenario**: Unexpected termination of connections.

**Symptoms**: TCP connections are reset unexpectedly.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
40      7.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [RST, ACK] Seq=151 Ack=151 Win=0 Len=0
```

**Explanation**:
- **Packet 40**: The client sends a TCP reset (RST) packet, terminating the connection.

**Resolution**:
- **Application Errors**: Check for application errors or misconfigurations that may be causing the connection to reset.
- **Protocol Violations**: Ensure that there are no protocol violations causing the reset.

### 6. Throughput Analysis

**Scenario**: Low data transfer rates affecting application performance.

**Symptoms**: Data transfer rates are significantly lower than expected.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
50      8.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [ACK] Seq=201 Ack=201 Win=64240 Len=1000
51      8.100000    192.168.1.1     192.168.1.2     TCP      60     80 → 49716 [ACK] Seq=201 Ack=1201 Win=64240 Len=0
52      8.200000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [ACK] Seq=1201 Ack=201 Win=64240 Len=1000
```

**Explanation**:
- **Packets 50 & 52**: The client sends data segments of 1000 bytes each.
- **Packet 51**: The server acknowledges the data received.

**Resolution**:
- **Bottleneck Identification**: Identify and resolve any bottlenecks in the network path.
- **Network Optimization**: Optimize network settings and configurations to improve data transfer rates.

### 7. Using Statistics and Graphs

Wireshark provides powerful statistical tools:
- **TCP Stream Graphs**: `Statistics > TCP Stream Graphs`
  - **Time-Sequence Graphs**: Visualize sequence numbers over time.
  - **Round-Trip Time Graphs**: Analyze round-trip times for packets.
  - **Throughput Graphs**: Measure data transfer rates.
- **IO Graphs**: `Statistics > IO Graphs` for visualizing overall traffic patterns and identifying spikes or drops in traffic.

### 8. Saving and Exporting Logs

- **Save Capture**: `File > Save As...` to save your captured traffic for later analysis.
- **Export Specific Packets**: `File > Export Specified Packets...` to export filtered packets of interest.

### 9. Practical Examples and Case Studies

#### Example 1: Slow Website Load



**Scenario**: Users report that a website is loading slowly.

**Capture**: Filter HTTP traffic using `tcp.port == 80`.

**Analysis**:
1. **Check TCP Handshake**: Ensure that the handshake completes successfully without delays.
2. **Look for Retransmissions**: Identify any retransmitted packets that may be causing delays.
3. **Analyze Throughput**: Use throughput graphs to see if data transfer rates are lower than expected.

**Resolution**:
- **Server Performance**: Ensure that the server has sufficient resources and is not overloaded.
- **Network Congestion**: Investigate and mitigate any network congestion issues.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
1       0.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
2       0.001000    192.168.1.1     192.168.1.2     TCP      60     80 → 49716 [SYN, ACK] Seq=0 Ack=1 Win=64240 Len=0 MSS=1460
3       0.002000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [ACK] Seq=1 Ack=1 Win=64240 Len=0
4       0.010000    192.168.1.2     192.168.1.1     HTTP     500    GET /index.html HTTP/1.1
5       0.100000    192.168.1.1     192.168.1.2     TCP      60     [TCP Retransmission] 80 → 49716 [PSH, ACK] Seq=1 Ack=1 Win=64240 Len=50
6       0.200000    192.168.1.1     192.168.1.2     HTTP     1000   HTTP/1.1 200 OK
```

#### Example 2: Intermittent Connectivity

**Scenario**: Users experience intermittent connectivity to a service.

**Capture**: Filter traffic from affected clients.

**Analysis**:
1. **Check for Resets**: Look for TCP reset packets indicating connection drops.
2. **Identify Timeouts**: Identify any packets that are retransmitted due to timeouts.

**Resolution**:
- **Hardware Issues**: Check for and replace any faulty network hardware.
- **Network Configuration**: Ensure there are no misconfigurations causing intermittent drops.

**Sample Log**:
```
No.     Time        Source          Destination     Protocol Length Info
1       0.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
2       0.001000    192.168.1.1     192.168.1.2     TCP      60     80 → 49716 [SYN, ACK] Seq=0 Ack=1 Win=64240 Len=0 MSS=1460
3       0.002000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [ACK] Seq=1 Ack=1 Win=64240 Len=0
4       5.000000    192.168.1.2     192.168.1.1     TCP      60     49716 → 80 [RST, ACK] Seq=1001 Ack=1001 Win=0 Len=0
5       5.100000    192.168.1.2     192.168.1.1     TCP      60     [TCP Retransmission] 49716 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460
```


###

If you have any suggestions, improvements, or additional examples that you would like to share, please help to contibute.