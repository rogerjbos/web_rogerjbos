---
title: Obi202 as ITSP
author: 'Roger J. Bos, CFA'
date: '2019-04-05'
slug: voip
categories:
  - voip
tags:
  - voip
---

I have been using Google Voice since it started as Grand Central, before Google bought it.  Google Voice became even better when Obihai came out with an analog telephone adapter (ATA) that would connect directly to it.  To be able to make free calls to relatives, I installed an Obi110 device at their house as well.  Learning VoIP has been a long and confusing journey, but it has also been a good learning experience.  Basically you need to know about trunks and dialplans.  A trunk is a service provider and that service provider could be a landline (also referred to as publicly switched telephone network - PSTN) or a VoIP provider.  There is also something called a DID, which stands for direct inward dial.  It is just your phone number.  A dialplan is used to match inbound and outbound calls and route them appropriately.  You never had to worry about this with a landline because you only had one service provider.  The Obi devices can handle either 2 or 4 service providers, so you have to learn how to use dialplans if you want to use more than one provider or other advanced features.

### Google Voice Service

If you just want to use Google Voice as your home phone, you don't need to read any of this post.  You would buy an Obi200 (or Obi202) and use the configuration wizard to set it up with Google Voice.  One of the best features of the Obi devices is the Obitalk portal that allows for remote, browser based, configuration.  The reason I originally choose the Obi110 for the remote location is so the user can make both local and US calls with the same phone and the dialplan will choose the appropriate trunk.  This setup worked flawlessly for many many years.  Then Google Voice changed its underlying structure and stopped supporting XMPP.  Obihai (now Polycom) also decided to end-of-life (EOL) the Obi1xx series of devices, so they cannot connect to the new version of Google Voice.  There are two options going forward.  The easy option is to buy a new Obi212, which is the new version of the Obi110, where you can bridge a regular landline with VoIP.  The hard way, which I am outlining here, keeps the Obi110 in service by using SIP only and using a spare line on another Obi202 for the Google Voice connection.  The Obi110 is not dead, it just doesn't connect to Google Voice service anymore.  

### PBX

PBX stands for Private Branch eXchange.  You phone number is part of the public telephone network because anyone can call you.  A set of extensions within a company is private in that the public cannot call an extension directly, they need to call a main number and connects to the PBX, which will route the call to the appropriate extension.  This is similar to how a router bridge the public internet (the WAN) with the private computers behind the router (the LAN).  PBX used to consist of expensive hardware that was hard to program, now a PBX is mostly software based and a easier (but still tricky) to prgram.  Whereas Google Voice uses proprietary connections, a PBX uese standardized SIP (and IAX) connections.  As I mentioned above, the Obi110 still works fine with standardized SIP.

So for a time the answer was to use free/low cost [PBXes](www.pbxes.com) account as a SIP server and NAF's GVSIP as the new method to make Google Voice a trunk.  This setup too worked really well, until Google Voice changed their servers again to prevent GVSIP connections.  According to Google's terms of service only Obi2xx devices (and certain other Polycom devices) can connect to the Google Voice service.

### Putting it all together

Below I explain my current setup that allows me to keep the Obi110 working with Google Voice and doesn't violate Google Voice's terms of service.  I have an Obi202 at my house that is capable of four service providers.  I use the first service provider as my home phone line, so the other three went unused.  Here I describe how I configured my Obi202 to provide service to 2 additional Google Voice phone numbers.  This is a specific application using an Obi202, Obi110, and PBXes.com and is largely based on general instructions by [Robert Stampfli](https://cboh.org/voip/obi/OBi_As_ITSP.html) that explain how to do this with an Asterisk server.^(His approach used PJSIP.  PBXes doesn't offer PJSIP, only SIP, but this version works fine with SIP on PBXes.com.)

Each Obi device has a unique ID, which can be used to call from one Obi to another Obi.  My Obi110 has an id of 200800100.  We will use that for incoming calls from the US.  Here are the four service providers I have configured on my Obi202:

* SP1 is GV1 (main) home phone line
* SP2 is GV2 linked to an overseas Obi110
* SP3 is GV3 (extra) phone line routed thru pbxes.com
* SP4 is SIP registered to pbxes.org

The summary of what is happening is that the Obi202 will be registered with 3 Google Voice accounts and PBXes.com will register to SP4 of the Obi202 and the Obi110 will be registered as an extension on PBXes.com.  *Remember, this is the hard way, primarily for people who want to learn more about VoIP.  There is an easier way, jut buy an Obi212.*  You can change the setup to suit you, but you just need to make all the appropiate changes.  I will attempt to explain the settings below.

### Obi202 SIP service - SP1
```
Voice Services > SP1 Service > X_InboundCallRoute                  {ph} (optionally {(200800100):aa},{ph})

```
X_InboundCallRoute is the dialplan for the inbound calls that show up to the Obi from service provider 1.  All we need here is the regular setting of {ph} which will ring line 1.  Dialplans can get complicated fast, if you are using an Obi202 with two phone lines and wanted both to ring when you got a call on SP1, then you would change the X_InboundCallRoute to {ph1},{ph2}.  If you wanted the auto attendant to answer if the caller was the Obi110, you would add {(2000800100):aa} to the front of the dialplan.  This uses the obi device id as caller id and if it matches it routes the call to the auto attendant, otherwise it will ring the phone.  I was going to use the Auto Attendant as an easy way for the Obi110 to make outgoing calls, but I figured out a better way, which I describe below.

### Obi SIP service - SP2
```
Voice Services > SP2 Service > X_InboundCallRoute                  pp(200800100)

```
SP2 is configured with the GV number I want to use on the Obi110.  Since this GV phone number is configured on an Obi202, it is compliance with Google Voice's terms of service.  For outgoing calls I use PBXes.com, but incoming calls are easier.  I can configure the X_InboundCallRoute of my Obi202 to be the address of the Obi110 so that whenever anyone calls the GV2 phone number the Obi110 will ring.  You do this by using pp and the id of the Obi device, so pp(200800100) will ring the Obi110.  Incoming calls to the Obi110 are now complete.  You can test this out by calling the GV2 phone number, which is configured on the Obi202, and it should ring the Obi110.  This test will work even before the Obi110 is registered with PBXes.com because that is only needed for outgoing calls.  The other way to do outgoing calls, which doesn't even need a PBXes.com account is to use the auto attendant I mentioned earlier.  Any Obi device can call another Obi device, without any service providers registered.  If the Obi device being called is configured to have the auto attendant answer, the calling device can place an outgoing call using the auto attendant.  That is a two step process.  I use my PBXes.com account to make outgoing calls more seamless for the Obi110 users.  I little bit more work for me makes their life easier.

### Obi GV service - SP3
```
Voice Services > SP3 Service > X_InboundCallRoute                  SP4(pbx-Obi202@pbxes.com)

```
GV3 is just a test Google Voice number to show that it is possible to handle more than one Google Voice number with this setup.  This service provider is optional and you don't need to set it.  Here I am not dialing another Obi directly, but instead routing the call to PBXes.com.  Within PBXes.com I set up a sub-pbx with the name Obi202 and *pbx* is the name of my PBXes.com account.  This shows that you can still use Google Voice as a trunk for a PBX.

Next we configure SP4 of the Obi202 to register with the sub-pbx I mentioned above.  This effectively makes the Obi202 an internet telephone service provider (ITSP) for the sub-pbx.   

### Obi SIP service - SP4
```
Service Providers > ITSP Profile D > SIP > ProxyServer             pbxes.org
Service Providers > ITSP Profile D > SIP > ProxyServerPort         5060
Service Providers > ITSP Profile D > SIP > RegistrationPeriod      120
Service Providers > ITSP Profile D > SIP > X_SpoofCallerID         checked
Service Providers > ITSP Profile D > SIP > X_NoNonceAuth           checked
Service Providers > ITSP Profile D > General > X_EarlyICEEnableIn  checked

Voice Services > SP4 Service > X_ServProvProfile                   ITSP Profile D
Voice Services > SP4 Service > X_InboundCallRoute                  {4122125555:SP2},{SP3}
Voice Services > SP4 Service > X_AcceptsSipFromRegistrarOnly       check
Voice Services > SP4 Service > X_NoRegNoCall                       check
Voice Services > SP4 Service > AuthUserName                        pbx-OBi202
Voice Services > SP4 Service > AuthPassword                        (secret)
Voice Services > SP4 Service > X_EnforceRequestUserID              not checked

```

As you can see above, you will want to use pbxes.org (not pbxes.com) as the proxy server, and the standard SIP port, 5060.  In my setup I check spoof caller id (and leave the caller id field blank in the sub-pbx setup screen) so the caller id of the extension will get passed through to the Obi202.  The X_InboundCallRoute performs the dialplan magic to route the outgoing call to the correct Google Voice trunk.  The setting {4122125555:SP2},{SP3} states that if the caller id is 4122125555, route to SP2, which is the GV2 trunk, otherwise route to SP3.  4122125555 would be whatever caller id you set for the extension you configure in pbxes.com (see below).  AuthUserName and AuthPassword are whatever you choose when you set up the sub-pbx (see below).  

### pbxes.com service
```
Sub PBXes > username pbx-Obi202
Sub PBXes > password (secret)

Outbound Routing > Trunk Sequence > SIP/pbx-Obi202
Outbound Routing > Extension > (your extension)

```

That is all for the Obi202.  Now you need to configure your pbxes.com account by adding a sub-pbx, and extension, and an outbound trunk sequence.  The sub-pbx tab has a number of fields, but you don't need to fill in all of them.  The primary ones are the username and password, and you can choose anything you like, just make it match what you used in SP4 on the Obi202.  As I mentioned before, I left caller id blank so it would use the extension caller id.  If you only have one line, you can fill in caller id and it will override the extension caller id.  Once you have saved these configs, give it a few minutes and then check the pbxes.com status tab and see if pbx-Obi202 is registered.  If so, you are in good shape, if not, check the configs again and check the system log for any hints as to why registration has failed.

You would need at least one extension.  The extension is what you would connect the phone (or soft phone) to actual use the pbx.  Next choose Outbound Routing, create a new route, and choose your sub-pbx name as the trunk sequence and what extension(s) you want to use with this route.  If you have more than one outbound route, you have to choose the order properly so that your calls with get routed correctly.

Here is how you register the Obi110 to the extension on PBXes.com:

### Obi110 - SP1: Generic Service Provider
```
Service Provider Proxy Server       pbxes.org
Service Provider Proxy Server Port  5060
UserName                            (extension username)
Password                            (extension password)
```

I hope you enjoy this setup and that you learned a little bit about VoIP.
