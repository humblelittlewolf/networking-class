Routing
=======

Install Telnet
--------------

First, install telnet on your Raspberry Pi. (TODO: What is telnet?)

::

  sudo apt-get update
  sudo apt-get install telnet

Hook to switch
--------------

Use a USB to serial converter. (TODO: What is serial?)

Download driver
https://www.tripplite.com/support/model/mid/USA-19HS

Use MobaXTerm to connect

Find Documentation
------------------

Get manual for
"hpj9085a manual"
Difficult to find because of bad links. Go to HP and find via serial number.

The manual on page 4-12 shows you how to reset switch so we can start fresh.

On startup, you will see::

	ROM information:
	   Build directory: /sw/rom/build/nemorom(ndx)
	   Build date:      Nov 28 2007
	   Build time:      16:36:54
	   Build version:   R.10.06
	   Build number:    14201

	OS identifier found at @ 0xbc020000
	Verifying Image validity ...
	CRC on OS image header Passed
	CRC on complete OS image file Passed
	Valid OS image @ 0xbc020000

	Decompressing...done.
	CRC of image is 0x2e3a30b1
	CRC @ 0x80001000 Len 10873888 is 0x2e3a30b1

	ROM information:
	   Build directory: /sw/rom/build/nemorom(ndx)
	   Build date:      Nov 28 2007
	   Build time:      16:36:54
	   Build version:   R.10.06
	   Build number:    14201

	OS identifier found at @ 0xbc020000
	Verifying Image validity ...
	CRC on OS image header Passed
	CRC on complete OS image file Passed
	Valid OS image @ 0xbc020000

	HP ProCurve Switch 2610-24 (J9085A)
	ROM Build Directory: /sw/rom/build/nemorom(ndx)
	        ROM Version: R.10.06
	     ROM Build Date: 16:36:54 Nov 28 2007
	   ROM Build Number: 14201

	Copyright (c) 1995-2001 Hewlett-Packard Company. All rights reserved.

	                         RESTRICTED RIGHTS LEGEND

	Use, duplication, or disclosure by the Government is subject to restrictions
	as set forth in subdivision (b) (3) (ii) of the Rights in Technical Data and
	Computer Software clause at 52.227-7013.

	    Hewlett-Packard Company, 3000 Hanover Street, Palo Alto, CA 94303

	Enter h or ? for help.

	=>
	ROM information:
	   Build directory: /sw/rom/build/nemorom(ndx)
	   Build date:      Nov 28 2007
	   Build time:      16:36:54
	   Build version:   R.10.06
	   Build number:    14201

	OS identifier found at @ 0xbc020000
	Verifying Image validity ...
	CRC on OS image header Passed
	CRC on complete OS image file Passed
	Valid OS image @ 0xbc020000

	Decompressing...done.
	CRC of image is 0x2e3a30b1
	CRC @ 0x80001000 Len 10873888 is 0x2e3a30b1



	initializing..initialization done.



	Waiting for Speed Sense.  Press <Enter> twice to continue.


After a few more data screens, you get::

  ProCurve Switch 2610-24#

Type ``help`` to see a list of commands.

We want to use the ``setup`` command to set it up.
  * It will ask for a name. Call it 'CMSC 340 Switch 1' or similar.
  * Leave contact info, password blank.
  * We won't use a gateway yet. (TODO: Explain what we'd do for a gateway)
  * Spanning Tree - No if we know things will be hooked up right. Yes if we might have loops. (TODO: Possible exercise: Turn if off, hook up a loop to see what happens. Turn it on and see what happens.)
  * Skip time server setup. (TODO: Talk about time servers.)
  * IP. We will manually set. Hit spacebar twice Use 192.168.1.10 (0 is broadcast, 1 is router. (TODO: Explain broadcast, and number conventions)
  * Netmask: 255.255.255.0 (TODO: Explain netmask and /24 type notation)

Connect to switch with Telnet
-----------------------------

At this point, can use telnet.
	* Plug in Raspberry Pi to switch
	* Do ifconfig, won't have IP address. No magic yet.
	* Set ip: ``sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0``
	* Set gateway: ``sudo route add default gw 192.168.1.1`` (TODO: What is a
	  gateway)
	* Type ``telnet 192.168.1.10``
	* Yay! We are there

There is also a browser interface.
  * Open browser and go to 192.168.1.10
  * Oooh, requires a java plugin. Whatever. We'll use the command line


Router
------

(TODO: How to reset router)
* Connect
* Power up
* Hit enter
* Go into 'initial configuration dialog'

::

	Would you like to enter the initial configuration dialog? [yes/no]: yes

	At any point you may enter a question mark '?' for help.
	Use ctrl-c to abort configuration dialog at any prompt.
	Default settings are in square brackets '[]'.

	Basic management setup configures only enough connectivity
	for management of the system, extended setup will ask you
	to configure each interface on the system

	Would you like to enter basic management setup? [yes/no]: yes
	Configuring global parameters:

	  Enter host name [Router]: cmsc340router

	  The enable secret is a password used to protect access to
	  privileged EXEC and configuration modes. This password, after
	  entered, becomes encrypted in the configuration.
	  Enter enable secret: cmsc340secret

	  The enable password is used when you do not specify an
	  enable secret password, with some older software versions, and
	  some boot images.
	  Enter enable password: cmsc340password

	  The virtual terminal password is used to protect
	  access to the router over a network interface.
	  Enter virtual terminal password: cmsc340vt
	  Configure SNMP Network Management? [yes]: no

	Current interface summary


	Any interface listed with OK? value "NO" does not have a valid configuration

	Interface                  IP-Address      OK? Method Status                Protocol
	FastEthernet0/0            unassigned      NO  unset  up                    down
	FastEthernet0/1            unassigned      NO  unset  up                    down
	Serial0/0/0                unassigned      NO  unset  down                  down

	management network from the above interface summary: FastEthernet0/0

	Configuring interface FastEthernet0/0:
	  Use the 100 Base-TX (RJ-45) connector? [yes]:
	  Operate in full-duplex mode? [no]: yes
	  Configure IP on this interface? [yes]:
	    IP address for this interface: 192.168.1.1
	    Subnet mask for this interface [255.255.255.0] :
	    Class C network is 192.168.1.0, 24 subnet bits; mask is /24

	The following configuration command script was created:

	hostname cmsc340router
	enable secret 5 $1$7wzp$GmYsBze2WVxkuoaOvbAuP0
	enable password cmsc340password
	line vty 0 4
	password cmsc340vt
	no snmp-server
	!
	no ip routing

	!
	interface FastEthernet0/0
	no shutdown
	media-type 100BaseX
	full-duplex
	ip address 192.168.1.1 255.255.255.0
	no mop enabled
	!
	interface FastEthernet0/1
	shutdown
	no ip address
	!
	interface Serial0/0/0
	shutdown
	no ip address
	!
	end


	[0] Go to the IOS command prompt without saving this config.
	[1] Return back to the setup without saving this config.
	[2] Save this configuration to nvram and exit.

	Enter your selection [2]:

	Building configuration...
	Use the enabled mode 'configure' command to modify this configuration.


	Press RETURN to get started!


	*Sep 17 19:11:28.055: SERVICE_MODULE(Serial0/0/0): self test finished: Passed
	*Sep 17 19:11:45.007: %VPN_HW-6-INFO_LOC: Crypto engine: aim 0  State changed to: Initialized
	*Sep 17 19:11:45.011: %VPN_HW-6-INFO_LOC: Crypto engine: aim 0  State changed to: Enabled sslinit fn

	*Sep 17 19:11:48.371: %VPN_HW-6-INFO_LOC: Crypto engine: onboard 0  State changed to: Initialized
	*Sep 17 19:11:48.371: %VPN_HW-6-INFO_LOC: Crypto engine: onboard 0  State changed to: Disabled
	*Sep 17 19:11:49.071: %LINEPROTO-5-UPDOWN: Line protocol on Interface VoIP-Null0, changed state to up
	*Sep 17 19:11:49.071: %LINK-3-UPDOWN: Interface Serial0/0/0, changed state to down
	*Sep 17 19:11:50.071: %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial0/0/0, changed state to down
	*Sep 17 19:11:50.607: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down
	*Sep 17 19:11:50.607: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to down
	*Sep 17 19:20:38.795: %LINK-5-CHANGED: Interface Serial0/0/0, changed state to administratively down
	*Sep 17 19:20:39.007: %LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down
	*Sep 17 19:20:43.643: %SYS-5-RESTART: System restarted --
	Cisco IOS Software, 1841 Software (C1841-ADVIPSERVICESK9-M), Version 12.4(3d), RELEASE SOFTWARE (fc3)
	Technical Support: http://www.cisco.com/techsupport
	Copyright (c) 1986-2006 by Cisco Systems, Inc.
	Compiled Tue 18-Apr-06 19:10 by alnguyen
	*Sep 17 19:20:43.647: %SNMP-5-COLDSTART: SNMP agent on host cmsc340router is undergoing a cold start
	cmsc340router>



----
 cmsc340router

 Enter a secret 'cmsc340secret' - Enables configuration
 Enter a password 'cmsc340password' - Enables monitoring
 Enter virtual terminal password - 'cmsc340vt' for hooking up via network

Now type:
``help``

Then type:

``?``

then type:

``show ?``

To be able to do make configuration changes,
let's get into a higher mode. Type::

  enable

Then use our password ``cmsc340enable``.

You should have a # for a prompt to show privilege elevation.

Now type ``show ?`` and see all the cool new commands that we have.

Type ``show version`` See ROM, uptime, etc.

Type ``show interface`` See interface details.

Wait! I see that the ``line protocol is down``. Let's fix that.

Plug into 0/0 plug into switch. You should see messages on the
See if it gives you messages.

Type ``show interface`` to see ``line protocol is up``

We want to set up the other interface.
Type ``configure`` and get::

	cmsc340router#configure
	Configuring from terminal, memory, or network [terminal]? t
	Enter configuration commands, one per line.  End with CNTL/Z.

Now we want to select our second card::

	cmsc340router(config)#interface FastEthernet0/1

See the commands available::

	cmsc340router(config-if)#?
	Interface configuration commands:
	[etc]

I did a Google search and found the syntax for the command
to set the IP address here:

http://www.cisco.com/c/en/us/td/docs/ios/12_2/ip/configuration/guide/fipr_c/1cfipadr.html#wp1000918

Most computers give you a "Syntax error" when you type something wrong.
Cisco routers assume you typed in a machine name, and tries to telnet
to it. Along with a really long pause while it tries.

Now to set the IP of that second card::

	cmsc340router(config-if)#ip address 192.168.2.1 255.255.255.0

Turn on routing. This is supposed to be on by default, but that didn't happen for me::

	cmsc340router(config-if)#ip routing

Make the card 'active'::

    cmsc340router(config-if)#no shut

Exit::

	cmsc340router(config-if)#exit
	cmsc340router(config)#exit
	cmsc340router#

Now type ``show interface`` and see our second card is configured.



Save Changes
------------
After you are done, you need to save your changes::

	cmsc340router#copy running config
	Destination filename [config]? cmsc340
	%Warning:There is a file already existing with this name
	Do you want to over write? [confirm]

	872 bytes copied in 1.204 secs (724 bytes/sec)
	cmsc340router#


(TODO: http://www.informit.com/library/content.aspx?b=CCNP_Studies_Switching&seqNum=47)

Port Based VLAN
---------------

Telnet to router.

Type ``show vlan``. No VLAN yet.

Get into configuration mode with ``configuration``.

Create a new vlan with ``vlan 13``. Or whatever number. Prompt changes to let
you know what VLAN you are configuring.

Assign ports 1-5 to VLAN 13. ``untagged 1-5``. We won't really tag the
outgoing packets. We will just do it internally. This says 'no tags.'

Type ``exit`` to get out of VLAN 13 config. Type ``exit`` to get out VLAN
config. Type ``show vlan`` and see our new vlan. Type ``show running`` (``sh
ru`` for short) to see the details. Including what ports are part of the VLAN.


