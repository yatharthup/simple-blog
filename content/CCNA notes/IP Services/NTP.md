Network Time Protocol

It is important that network devices have an accurate and synced clock, so that all devices operate on the same time.

Time can :
1. Be configured manually without NTP 
2. Be configured automatically with NTP

# The need for time on network devices

Routers, Switches , PCs all devices have an internal clock.

		R1# show clock
// Used to show time on the device (CISCO IOS)
--> Works on UTC by default Coordinated Universal Time

		R1# show clock details

![[Pasted image 20240817224702.png]]


Time is necessary primarily to keep proper logs of the events. 

Hardware based time is not considered accurate because it drifts away with time.

> Syslog is the protocol used to keep logs on CISCO devices, more on it later


		R1# show logging
// Command shows the logs of the device

![[Pasted image 20240817224856.png]]
// OSPF logs on display here (OSPF neighbour going between up and down states)

well synced logs help with accurately troubleshooting.


# Configuring time manually

		R1# clock set <options>
// clock set helps to set the time manually (done from priv exec mode )
The clock configs are NOT part of the running config.


Hardware and Software clocks are separate and are configured separately.

> software clocks use *clock*
> hardware clock uses *calendar*

![[Pasted image 20240817225115.png]]

// Software clock config


![[Pasted image 20240817225318.png]]
// hardware clock config


*Usually, both clock and calendar time should be synced.*

		R1# clock update-calendar
// syncs the calendar's time to the Clock's time automatically

		R2# clock read-calendar
// syncs the clock's time acc to the calendar



### Configuring the time zone

		R2(config)# clock timezone <option>
// run from global config mode (is a part of the running config). Used to set the Timezone

![[Pasted image 20240817225701.png]]

Timezones are important as NTP only uses UTC.


# Daylight Saving time 

![[Pasted image 20240817225841.png]]

![[Pasted image 20240817230118.png]]

# Manual time config commands summary
![[Pasted image 20240817230142.png]]

# NTP

Manually configured clocks drift, resulting in inaccurate time, hence the importance of NTP becomes all the more clear.

also, Manually configuring time on all devices on larger networks is not feasible.

Network Time Protocol allows automatic syncing of time over a network.

![[Pasted image 20240817230425.png]]
// These addresses like time.windows.com are resolved into IPs via DNS.

![[Pasted image 20240817230517.png]]

NTP has 2 device types:
1. NTP servers
2. NTP clients

NTP clients request time from NTP servers and sync their own time to it.

> The same device can be both an NTP server and an NTP client.

NTP allows time accuracy fluctuations of:

1. 1 ms , if the NTP server is on the same LAN
2. 50 ms, if the NTP server is on the WAN/ Internet

> Stratum:
> 	The distance of an NTP server from the original reference clock is the stratum.
> Lower stratum means a "better" NTP server


> NTP uses UDP over Port 123.


### Reference clocks

Extremely accurate time devices that are actually keeping the time, like Atomic or GPS clocks.

These are Stratum 0.

NTP servers connected directly to reference clocks are Stratum 1.


![[Pasted image 20240817231057.png]]
// US naval stratum 0 clock.

Stratum 15 is the max, anything over it is unreliable.

> Symmetric Active:
> 	Devices can "peer" with devices on the same stratum for more accurate time.

CISCO devices can have 3 modes (and exist in all of them simultaneously):
1. NTP server mode
2. NTP client mode
3. Symmetric active mode

**An NTP client can sync to multiple NTP servers.**


## NTP hierarchy 

![[Pasted image 20240817231444.png]]

Servers with stratum 2 and above are secondary servers.

# NTP config
![[Pasted image 20240817231731.png]]
// we're configuring on R1


		r1(config)# ntp server <server_ip>
// Adds the ntp server 


		R1(config)# ntp server <server_ip> prefer
// makes this one the preferred one, and the others as backups.

		R1# show ntp associations
// important show command for NTP

![[Pasted image 20240817231826.png]]
\* means that this is the server to which the devices is being configured

\+ means that these are backups


		R1# show ntp status
//another important show command for NTP

![[Pasted image 20240817232016.png]]

NTP uses the UTC time zone only, you must configure the appropriate time zone on each device manually.

And BY DEFAULT, ntp does not update the hardware clock.

![[Pasted image 20240817232136.png]]

		R1(config)# ntp update-calendar
// updates the hardware clock

![[Pasted image 20240817232302.png]]


##### configuring r2 now

![[Pasted image 20240817232633.png]]


We'll also configure a loopback on r1

![[Pasted image 20240817232450.png]]
// loopback interfaces are useful because they provide a router an address that can be used to reach it, which isn't dependent on the status of any particular physical interface.


![[Pasted image 20240817232619.png]]


##### configuring r3

![[Pasted image 20240817232754.png]]
// R1 is preferred here, because it has the lower stratum.


> Every NTP client is automatically an NTP server.

However, what if there is NO ntp server to sync to ? (in a completely closed network)

There we'll have to manually configure an NTP server.

# Config NTP server mode

![[Pasted image 20240817232940.png]]
// this is the topology being used here

		R1(config)# ntp master
// configures the device as the master (server)

DEFAULT STRATUM IS 8.


![[Pasted image 20240817233057.png]]

// basically R1 is using itself as the reference clock (127.127.1.1 is a loopback address)

# Config Symmetric Active mode

		R2(config)# ntp peer 10.0.23.2
// R1 is the peer

![[Pasted image 20240817233241.png]]


# NTP authentication

Can be configured, but it is optional.

Allows NTP clients to ensure that they only sync to the intended servers. They'll check if the server is using the same password as them, and if yes, only hen will they configure.


		R1(config)# ntp authenticate
//this command is only needed when using NTP passive, broadcast client or multicast client (beyond the syllabus for ccna)
// automatically activated in our cases


		R1(config)# ntp authentication-key <key-number> md5 <password>
// creating key

		R1(config)# ntp trusted-key <key-numberd>
// specifies a trusted key (doesn't CREATE one)

		ntp server <server-ip> key <key-number>
// specify which key to use on which network.

This command is not needed on the server itself. R1 in this topology:

![[Pasted image 20240817233855.png]]


### Configuring R1, R2 and R3 for ntp authentication

![[Pasted image 20240817233928.png]]

# NTP Commands summary

![[Pasted image 20240817234003.png]]