Writeup Campfire 1
Raoul-Gabriel Nicolaescu

1. Givven the fact that it is know that Alonzo is the one that found the weird files and that a Kerberoasting attack is involved to find the time is needed to look for alot of auth tries adn then to look for a kerberos ticket request, this can be found in the Security.evtx. Given that Alonzo it is expected that the ticket might come from his account. Givven this info the log is found and the time that is happened:

    2024-05-21 03:18:09

2. In the same log it can be found that the service tht was targeted is:

    MSSQLService

3.  In the same log it can be found that the workstation that is involved(the oen that is used by Alonzo) is:

    172.17.79.129

4. To determine th etool used for Active Directory enumeration , Powershell-Operational.evtx was used and filtering for Event ID 4104 it can be seen that PwoerView was used. So, the answer is:

    powerview.ps1

5. In the block of all the 4104 events, looking for the firs indicates the moment when the script was executed, this being:

    2024-05-21 03:16:32

6. By parsing the pregetch files uisng PEcmd into a .csv and then going over it in Timeline Explorer, around the time of attack(2024-05-21 03:18:08) there can be seen an entry of Rubeus so the full path of the tool used to perform the actual kerberoasting attack is:

    C:\Users\Alonzo.spire\Downloads\Rubeus.exe

7. As metioned in th explenation above, the moment wehn the tool was executed is:

    2024-05-21 03:18:08


Steps to remedy:
1. Isolate the breached workstation(172.17.79.129)
2. Reset the Targeted Service Account Password (MSSQLService)
3. Reset Alonzo’s User Credentials
4. Apply Lesat Privilage
5. Restrict Execution and monitor
