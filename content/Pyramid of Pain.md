

# *Indicators*
****
# Hash Values 

### Basic Info 

> Hash value == result of a hashing algorithm.

Some common Hashes:
1. MD5 : 
	Now deprecated (Cryptographically Insecure)
	128 bit hash values
	Hash collision susceptible.

2. SHA-1: 
	Not recommended by NIST for digital signatures (SHA-2 or 3 recommended)
	160 bit hash value as a 40 digit hex num.
	Brute-force attack susceptible.

3. SHA-2:
	Quite common, and in use
	Many variants, SHA-256 being the most common
	256 bit hashes as a 64 digit hex num.

*Cryptographically Insecure Hashes:* If 2 files have the same hash value or digest, then the Hashing Algorithm is deemed Cryptographically insecure. (Hash collisions)

> Note:
> 	In Cryptography, hash values and digests are mostly the same thing. Think of Digests as being Hashes used in the context of a security related implementation, a Digital Signature for example.


### Relevance  

A mismatch in the hash values of a file means that the file has been tampered with.

Hash values can also be used to uniquely identify and label Malware files and related artefacts, and create better reference libraries.

> Tools for Hash Lookups:
> 	1. VirusTotal - [VirusTotal - Home](https://www.virustotal.com/gui/home/upload)
> 	2. Meta Defender Cloud - OPSWAT - [metadefender.opswat.com/?lang=en](https://metadefender.opswat.com/)


However, the very fact that slightly altering a file changes its hash values (that is useful) is also on the threat actor's side, since all it takes is a little change to a pre-existing malware to create a completely new and previously unidentified and undocumented hash.

This makes Threat Hunting using Hash values as a major IOC (Indicator of Compromise) non-viable.

# IP addresses 

### Basic info

IP addressing is an important networking concept on its own. However, for our purposes here, we need a simple working understanding.

IP addresses are, broadly speaking, a machine's identifiers on the internet. Whenever a machine connects to a network of any sort, it is assigned an IP address, and that IP address is essentially its "identity".

A lot of valuable information can be extracted through IPs, like the network which it originated from, the Internet Service Provider the deice uses (sometimes), and often the geo-location of the device with the IP. In essence, it is an identifier for the device.

> An IP address (IPv4 address) looks like this:
> 	172.168.10.1

For more details, read up on IPv4 addressing later.

### Relevance 

IP addresses are important defensively because malicious IPs can be identified and blocked on the Firewall or IPS (Intrusion Prevention Systems), effectively negating any attempts at communication from them.

Another method is to only allow certain trusted IPs to access the relevant resources. (Again, a part of networking and IT infrastructure designing.)

However, this blocking and dropping approach isn't the most reliable, since it is trivially easy to simply change your device's IP address.

**Fast Flux**
According to [Akamai](https://blogs.akamai.com/2017/10/digging-deeper-an-in-depth-analysis-of-a-fast-flux-network-part-one.html), Fast Flux is a DNS technique used by botnets to hide phishing, web proxying, malware delivery, and malware communication activities behind compromised hosts acting as proxies. The purpose of using the Fast Flux network is to make the communication between malware and its command and control server (C&C) challenging to be discovered by security professionals.

In summary, Fast Flux basically has multiple IP addresses associated to the same domain name (device name in this case), and it is constantly changing this IP.

# Domain Names

