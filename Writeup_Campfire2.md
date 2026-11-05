Writeup Campfire 2
Raoul-Gabriel Nicolaescu

1. From the info abot the Sherlock scenario, it is known that there is an old admin account(that was not in use) was requesting tickets form KDC and that preauth is dissabled, this indicating a possible AsREP roasting attack. With this knowledge the next step is to look inside the SECURITY.evtx file and filter by Event ID __4768__, this being related to a TGT requests. By looking over the filtered logs it can be seen that a reqeust was made by the user __arthur.kyle__, this event happening at:

    2024-05-29 06:36:40

2. As said above, the username of the targeted account is:

    arthur.kyle

3. In the log entry, in Details -> Event data, the __TargetSid__ is the SID of the account, this being:

    S-1-5-21-3239415629-1862073780-2394361899-1601

4.  Still in the same log entry, still under Details -> Event data, the ip address of the compromised account can be found in hte __IpAddress__ field, adn this being:

    172.17.79.129

5. By filtering for Event ID __4769__(TGS request) too, after the TGT request that was mentioned above, there is a TGS request made by __happy.grunwald__ and givven the fact that the request was made from the same IP address from above strongly sugests that this is the account was being used on the attacking source machine so the answer is:

    happy.grunwald
