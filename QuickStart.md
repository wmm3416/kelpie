In order to use Kelpie, you need to have a server with a public IP address, and access to a DNS server capable of DNS SRV requests.  In this quick setup we'll assume you'll run kamailio and kelpie on the same box.   You will also need to have a 3rd party xmpp account we'll refer to it as juliet@jabber.org.

## Configure the DNS SRV ##
add a rule that  `_xmpp-server._tcp.`_{kelpie.hostname}_ points to your server port 5269
also let's add one for SIP  `_sip._udp.`_{kelpie.hostname}_ pointing to your server port 5060

## install kelpie ##

on your server check out the source by running
```
svn co http://kelpie.googlecode.com/svn kelpie
```


then run the install.sh script (install will require maven2 and java)

## configure kelpie ##
having it route sip traffic to itself port 5060 (kelpie on port 5070)
update the file /usr/lib/kelpie-0.1/conf/server.properties

the following should be updated:
```
com.voxbone.kelpie.ip={server_ip}
com.voxbone.kelpie.sip_port=5070
com.voxbone.kelpie.sip_gateway={server_ip}
com.voxbone.kelpie.hostname={kelpie.hostname}

# map juliet to sip:juliet@{kelpie.hostname}
com.voxbone.kelpie.mapping.juliet=juliet@jabber.org

```


## configure kamailio ##
the "simplest" way to get kamailio working is to configure it to use userloc, and text db

update the **LOCATION** section to requests to unknown users to kelpie (if not me, assume xmpp)

```

        if (!lookup("location")) {
                switch ($rc) {
                        case -1:
                        case -3:
                               # t_newtran();
                               # t_reply("404", "Not Found");
                               forward({server_ip}:5070);

                                exit;
                        case -2:
                                sl_send_reply("405", "Method Not Allowed");
                                exit;
                }
        }

```

## setup user agents ##

configure a presence enabled SIP client (like jitsi) to register to your server (as sip:romeo@_{kelpie.hostname}_), be sure to have enabled "Peer to Peer" presence.  Add sip:juliet@_{kelpie.hostname}_ to your contact list, juliet should see an invitation on her jabber.org account.  Likewise, juliet should add romeo@_{kelpie.hostname}_ to her account, and romeo should see a subscription request