It attempts to make SIP endpoints appear as XMPP endpoints and vice-versa


The gateway only focuses on protocol translation, a SIP proxy (such as Kamailio) and a federated xmpp server are required for routing.


## XMPP->SIP ##

This is the easiest case, the xmpp client adds user@_{kelpie.hostname}_ to his contact list, this gets translated as sip:user@_{outbound.siproxy}_ and the proxy routes the messages

## SIP->XMPP ##

This direction is more complicated.
The sip proxy should identify that user is actually an xmpp endpoint and forwards to the kelpie gateway.  Kelpie has a mapping of "user" to xmpp ids in it's properties file,  if it is found it forwards the message to the specified xmpp server.

**example:**
```
com.voxbone.kelpie.mapping.user=user@jabber.org
```

would map messages sent to sip:user@_{kelpie.hostname}_ to user@jabber.org

Alternatively, if you do not wish to create a mapping, you can send sip requests in the form of
sip:xmpp\_user+xmpp\_domain@_{kelpie.hostname}_ and it will be routed to xmpp\_user@xmpp\_domain


**Note:** It had been seen that offical gtalk rejects calls if it doesn't have the resource id.  if you setup the sip`<->`xmpp mapping in the properties, kelpie will discover the resource id by sending a subscribe to the xmpp user's presence.


## Sending digits from gtalk/gmail ##

Say that the sip endpoint is an ivr expecting a pin number to be dialed. What do you do since gtalk doesn't have a dial pad?
In the im window type:
```
/dial:1234
```