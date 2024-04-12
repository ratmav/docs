miscellaneous
=============

## networking

### tcp/ip and osi models

* tcp/ip mnemonic: "***a***nonymous ***t***urtles ***i***nspect ***l***ightbulbs"
* osi mnemonic:    "***a***erodyanmic ***p***lanes ***s***eem ***t***o ***n**eed ***d***aring ***p***ilots"

#### comparison table

| tcp/ip layer number | tcp/ip layer name | tcp/ip mnemonic phrase | osi layer number | osi layer name | osi mnemonic phrase | protocol data unit (pdu) | use
|---------------------|-------------------|------------------------|------------------|----------------|---------------------|--------------------------|------------------------------------------------------------------------------------------------|
| 4                   | application       | ***a***nonymous        | 7                | application    | ***a***erodyanmic   | data                     | https, ssh, ntp, etc.                                                                          |
| 4                   | application       | ***a***nonymous        | 6                | presentation   | ***p***lanes        | data                     | typically encryption/decryption and serialization*                                             |
| 4                   | application       | ***a***nonymous        | 5                | session        | ***s***eem          | data                     | typically session management*                                                                  |
| 3                   | transport         | ***t***urtles          | 4                | transport      | ***t***o            | segment, datagram        | segmentation, acknowledgement, and multiplexing (tcp, udp)                                     |
| 2                   | internet          | ***i***nspect          | 3                | network        | ***n**eed           | packet                   | addressing, routing, traffic control (ip, icmp, arp)                                           |
| 1                   | link              | ***l***ightbulbs       | 2                | data Link      | ***d***aring        | frame                    | transmitting frames between physically connected nodes (mac, ppp, ieee 802.2 ethernet framing) |
| 1                   | link              | ***l***ightbulbs       | 1                | physical       | ***p***ilots        | bit, symbol              | transmission and reception of raw bit streams over physical media (twisted pair, coax, fiber)  |

*data handling protocols may overlap at the osi application, presentation, and session layers, for example https, tls, etc. because the osi model is a reference/logical model.

## operating systems

### *nix

#### passwordless `sudo`

modify the sudoers file (`$ sudo visudo`) and add the following content:
```bash
# passwordless sudo for your-user for all commands.
your-user ALL=(ALL) NOPASSWD: ALL
```

**note**: this is a lax security policy, and should be implemented judiciously. it's more reasonable to observe the principle of least privilege, which would usually be implemented as only whitelisting the exact commands a given user needs to run with escalated permissions.

#### remapping capslock

in the `/etc/default/keyboard` (debian) file, set `XKBOPTIONS`:
    * `="ctrl:swapcaps"` (swaps capslock and ctrl).
    * `="ctrl:nocaps"` (capslock becomes an _additional_ ctrl)

#### avoiding the esc key

`Ctrl-[` is a valid escape sequence in at least some (including vi/vim/nvim) terminal applications.
