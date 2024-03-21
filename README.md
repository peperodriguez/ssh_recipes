# ssh_recipes
SSH Recipes, tips and tricks
For example [chaining](####Chaining)
## Tunneling

### Port forwarding
You want that a connection to <local_host>:<port_0> is directed to <server_2>:<port_1> through an ssh tunnel established on <server_ssh>:

    $ ssh -L <port_0>:<server_2>:<port_1> <user>@<server_ssh>

If this goes Ok, then all your traffic routed to localhost:<port_0> is ssh-tunnel'd to <server_2>:<port_1>.

For example, you want to establish an Windows RDP (Remote DesktoP) connection to a Windows machine named <win_server> which is only accesible from an ssh-enabled <linux_server>:

    $ ssh -L 13389:<win_server>:3389 <user>@<linux_server>
    
NOTES : `3389` is the standard Windows RDP port. You use `13389` in your machine, hoping it is not used for anything else :-).

### Relaying connections (`ProxyCommand`)

Edit/Create `~/.ssh/config/` and add:

```
Host <target_alias>
    Hostname <target>
    ProxyCommand ssh -W %h:%p <usr_at_hop_0>@<hop_0>
    User <user_at_target>
```

Then, `ssh <target_alias>` will reach `<target>` through an ssh-tunnel in `<hop_0>`.

#### Chaining connections

```
Host <hop_1_alias>
    Hostname <hop_1>
    ProxyCommand ssh -W %h:%p <usr_at_hop_0>@<hop_0>
    User <user_at_hop_1>
    
Host <target_alias>
    Hostname <target>
    ProxyCommand ssh -W %h:%p <hop_1_alias>
    User <user_at_target>
```

Then, `ssh <target_alias>` will reach `<target>` through an ssh-tunnel 1st in `<hop_0>` and then in `<hop_1>`.
