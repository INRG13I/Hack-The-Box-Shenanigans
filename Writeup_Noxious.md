Writeup_Noxious
Raoul-Gabriel Nicolaescu

1. Givven that it is known that it is a possibility of a LLMNR Poisoning attack, is known that:
    1) DNS request is made
    2) DNS failure
    3) LLMNR Fallback
    4) Bad actor claims to be the local resource needed
So, for a mental map of the ips involved a filter so only dns traffic is made and alot of the packets resulted are related to IP address 172.17.79.4 so that is the Domain controller.In the dns filter it can be seen the mistake too, 172.17.79.136 made a request for DCC01 instead of DC01, resulting in a resolution fail and a fallback to LLMNR. After that, a filter with LLMNR is made and in the results it can be seen that responder to the request is:

    172.17.79.135

2. By filtering for 172.17.79.135 and the name resolution(this beign a dhcp Req-ACK) the hostnamecan eb found, and on packet 12714 the hostname can be found as:

    kali

3. After filtering to see only smb2 traffic, the results show alot of NTLMSSL Setup requests and those happen right after the fialed DNS lookup. the username whose hash was captured is:

    john.deacon

4. The relay of the victim credentials happend in a short burts at:

    2024-06-24 11:18:30

5. As it was explained in more details at 1. , the typo that tiggered the whole attack was that the victim tried to type DC01 but it typed:

    DCC01

6. To find the NTLM server challange value is needed to check the first challange sent(packet no. 9291) and by looking inside it cna be foudn that the server challange value is:

    601019d191f054f1

7. To find the NTProofStr value is needed to look in the request sent from FORELA\john.deacon (packet no 9292) and inside the value can be found as:

    c0cc803a6d9fb5a9082253a04dbd4cd4

8. It is needed to try to use hashcat on the info that has been gathered. In a file structured like this:

User::Domain:ServerChallenge:NTProofStr:NTLMv2Response(without first 16 bytes).
The NTLMv2 Response value can be found from where we found NTProofStr

It results to:

john.deacon::FORELA:601019d191f054f1:c0cc803a6d9fb5a9082253a04dbd4cd4:010100000000000080e4d59406c6da01cc3dcfc0de9b5f2600000000020008004e0042004600590001001e00570049004e002d00360036004100530035004c003100470052005700540004003400570049004e002d00360036004100530035004c00310047005200570054002e004e004200460059002e004c004f00430041004c00030014004e004200460059002e004c004f00430041004c00050014004e004200460059002e004c004f00430041004c000700080080e4d59406c6da0106000400020000000800300030000000000000000000000000200000eb2ecbc5200a40b89ad5831abf821f4f20a2c7f352283a35600377e1f294f1c90a001000000000000000000000000000000000000900140063006900660073002f00440043004300300031000000000000000000

after running hashcat(hashcat -a0 -m5600 path/to/file rockyouwordlist.txt) it is foudn that the password can be cracked and the password is:

    NotMyPassword0K?

9. In the following packets(filtered with smb2) it can be seen that 172.17.79.136 wsa tryign to acces:

    \\DC01\DC-Confidential

Steps to remdy:
1. Block Rogue host
2. Enforce Network access control
3. Reset compromised credentials with enforced complexity
4. Restrict NTLM
5. Enforce SMB Signing