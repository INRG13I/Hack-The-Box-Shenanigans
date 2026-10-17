Writeip CronJewel1
Raoul-Gabriel Nicolaescu

1. We can find the moment when the Volume Shadow Copy service entered a running state by lookign in the event logs. WE open the SYSTEM.evtx and search for the Volume Shadow Copy service and we find it with the eventId 7036. We look in the Details tab(because in the General we get the local time not the UTC) and we find the time as:

    2024-05-14 03:42:16

2. We knwo that when a Volume shadow copy is made, the system does a check on the privilages of the account and the groups is part of. So in the SECURITY.evtx we look for checks in that period. We see some checks(Event ID: 4799) On the accoutn name DC01$ adn it was  check on the group Administrators and one on Backup Operators so tha answer is:

    Administrators, Backup Operators, DC01$

3. The process that did these cheks was C:\Windows\System32\VSSVC.exe. It's process id is 0x1190. We need to give the ID in decimal so we go in cyberchef, as the recepie we ad From Base with radix 16(because From Hex takes each Byte individualy not the whole thing) adn we get the answer:

    4496

4. To find the GUID of the Shadow copy we need to look into the Microsoft-Windows-NTFS.evtx adn in that period of time we find a mount, scan ...  from DC01 adn the Volume correlation ID is:

    {06c4a997-cca8-11ed-a90f-000c295644f9}

5. To find that it is needed to parse the $MFT file via MFTEcmd and the result is in a .csv. The file is walked through via Timeline Expliorer, filtered by the date(14/05/2024 filter on Created0x10) and the name of the file(ntds.dit filter on Filename) and on line 123915 the path can be seen. So the answer is  :

    C:\Users\Administrator\Documents\backup_sync_Dc\Ntds.dit

6.In the same entry in the Created0x10 by which it was filtered it can be seen the exact time when the dump was created:

    2024-05-14 03:44:22

7. By filtering only by the date and the path(.\Users\Administrator\Documents\backup_sync_dc filter on Parent Path) it can be seen another , that being the reistery hive. So the name and size of that are:

    SYSTEM, 17563648


Remedy steps:
1. isconnect the Infected machine from the network 
2. Delete Unauthorised Sahdow Copies
3. Reset krbtgt password
4. Restrict Execution of utilities like vssadmin.exe and diskshadow.exe to authorised personnel only
5. Set up alerts for when suspicious actions are done(suspicious VSS or NTDS access patterns)
