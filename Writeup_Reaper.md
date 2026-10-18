Writeup Reaper

Raoul-Gabriel Nicolaescu

1. In the pcap it can be seen on entry 3 a NBNS Refresh for FORELA-WKSTN001 that has the source IP:

    172.17.79.129

2. By filtering the packets so there are shown only packets with protocol NBNS there can be found some refreshes from Forela-Wkstn002, it having the ip:

    172.17.79.136

3. At 2024-07-31 04:55:16 there can be found some suspicious logon entries in the Security event logs and it can be found that the compromised account was of :

    arthur.kyle

4. Filtering the pcap for nbns shows a unknown devices at 172.17.79.135(no associated hostname) sendign NBSTAT queries to go over hosts in the network(packets no 579, 585, 596). Filtering for  SMB2 and 172.17.79.135 confirms it is a MITM, intercepting the NTLMSSP_AUTH request for FORELA\arthur.kyle from FORELA-WKSTN002 (172.17.79.136) and relaying it to FORELA-WKSTN001 (172.17.79.129). So the answer is:

    172.17.79.135

5. After the mistype of the file share and the trigger of the NTLM relay attack, the victim tries to connect to the intended legitimate filesahre via SMB2. So filtering the pcap for smb2 and lookgin for Tree Connect Request packets is the way to go. Pachets such 418, which is followed by STATUS_BAD_NETWORK_NAME responses shows the fileshare path the victim was trying to navigate to is :

    \\DC01\Trip

6. Looking in the same event log entry as the one used for 3. there can be seen that the source port used to logon to target workstation using the compromised account is:

    40252

7. In the same event log entry as above the Logon ID for the malicious session is :

    0x64A799

8. In the same event log as above the workstation name and the source IP Address from which the malicious logon occur are:

    FORELA-WKSTN002, 172.17.79.135

9. As it was said in the description from 3, the UTC time that the malicious logon happen is:

    2024-07-31 04:55:16

10. Filtering the Security event logs for network share events(Event ID 5140) and corelating it with malicious session (0x64A799) and the Source IP:Port (172.17.79.135:40252)shows that the share Name accessed as part of the authentication process by the malicious tool used by the attacker is:

    \\*\IPC$

Remedy steps:
1. Enforce SMB signing
2. Restrict Outbound NTLM 
3. Audit NTLM traffic