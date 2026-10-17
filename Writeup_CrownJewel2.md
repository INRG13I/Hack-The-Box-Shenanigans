Writeup CrownJewel2
Raoul-Gabriel Nicolaescu

1. To find the timestamp it is required to go thorugh the SYSTEM event logs and search for the Event Id 7036 and through the results look for "Volume Shadow Copy" service. This was found at the time:

    2024-05-15 05:39:55

2. To find the dump NTDS file it is needed to look in the APPPLICATION event logs and look for the event that corelates creation of a new DB instance(that being Event ID 325). So it is needed to filter by event 325 and NTDS.dit name. The log entry that is needed is found and inside the path the dump was done, that being:

    C:\Windows\Temp\dump_tmp\Active Directory\ntds.dit

3. In the same entry in the Details tab, under System -> Time  Created the timestamp can be found:

    2024-05-15 05:39:56

4. The database is considered complete and ready for use is when the DB engine detaches from the DB(so event 327) so just 2 Entries above it can be found. In Details -> System -> Time Created the timestamp can be found:

    2024-05-15 05:39:58

5. The DB mangement engine manges the status data of the DB created, the name of it being(name that can be seen in source column of all the entries related to the DB events mentioned above):

    ESENT

6. In the SECURITY even logs it needs to be searched for the calls for ntdsutil.exe in that specific timeframe. The log entries are found and the 2 groups asossiated are:

    Administrators, Backup Operators

7. To find the Logon session start we filter by the Event ID 5379(to event sharing the same Logon ID as ntdsutil.exe) and from that we look for the oldest one tied to the groups tide to the ntdsutil.exe process. With that we find the entrie that has the timestamp:

    2024-05-15 05:36:31


Remedy steps:
1. Disconnect the Infected machine from the network 
2. Delete Unauthorised Sahdow Copies
3. Reset compromised account
4. Restrict Execution of ntdsutil.exe to authorised personnel only
5. Set up alerts for when suspicious actions are done(suspicious VSS or NTDS access patterns)