# tor-multiple-instances

Lets you run multiple tor instances on one node. This allows you to run multiple relays on the same server as Tor is not multithreaded.

Just by setting multiple configuration files, you can run multiple tor instances on the same node.

Tor doesn't allow more than two instances per public IP:

`Note: You can only run two Tor relays per public IPv4 address. If you want to run more than two relays you will need more IPv4 addresses.`

[Source](https://trac.torproject.org/projects/tor/wiki/TorRelayGuide)

***

## How To:

### Patch init.d tor script to make it support multiple instances:
* Replace `/etc/init.d/tor` with the [Tor init.d script](tor) file from this repo

Or

* Patch the init.d tor script: `sudo patch /etc/init.d/tor tor.patch`

### Preprare Original Instance (not mandatory be easier to track everythin later):
* Edit your new `/etc/tor/torrc.1` to use a new folder IE: `DataDirectory /var/lib/tor1`
* Edit your new `/etc/tor/torrc.1` to use a log file IE: `Log notice file /var/log/tor/notices1.log`
* Edit your new `/etc/tor/torrc.1` to use a different pid IE: `PidFile /run/tor/tor.1.pid`
* Copy your original tor directory from `/var/lib/tor` to `/var/lib/tor1` : `sudo cp -ar /var/lib/tor /var/lib/tor1`

### Create a new Instance (repetable X times):
* Copy your `/etc/tor/torrc.1` `/etc/tor/torrc.X`
* Copy your `/var/lib/tor1`  to `/var/lib/torX` : `sudo cp -ar /var/lib/tor1 /var/lib/torX`
* Remove everything from the new `/var/lib/torX` folder : `sudo rm -rf /var/lib/torX/*` (you don't want to use same keys on another instance)
* Edit your new `/etc/tor/torrc.X` to use different Ports and folders such as:
```
SocksPort 900X
PidFile /run/tor/tor.X.pid
DataDirectory /var/lib/torX
Log notice file /var/log/tor/noticesX.log
#ORPort 901X
#DirPort 902X
#ControlSocket /var/lib/torX/control_socket
```

***

You can look at very minimalistic config files in [Instance 1 torrc](torrc.1) and [Instance 2 torrc](torrc.2)

Here are the main differences in my relay configurations on the same node:

```
-PidFile /run/tor/tor.1.pid
+PidFile /run/tor/tor.2.pid

-DataDirectory /var/lib/tor1
+DataDirectory /var/lib/tor2

-Address relay1.****.**
+Address relay2.****.**

-Nickname *******1
+Nickname *******2

-ORPort 443
+ORPort 9001

-DirPort 80
+DirPort 9030

-Log notice file /var/log/tor/notices.1.log
+Log notice file /var/log/tor/notices.2.log

-#ControlSocket /var/lib/tor1/control_socket
+#ControlSocket /var/lib/tor2/control_socket
```
