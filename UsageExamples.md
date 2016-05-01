# Introduction #

<p>Coly is a Scapy based python tool, that can form neighbourship to EIGRP routers and inject route.</p>

# Requirements #

Scapy 2.2.0 and above with EIGRP module.<br>
tcpdump, for listening and filtering EIGRP packets.<br>

<h1>Usage Examples</h1>

<p>Everything start with defining interface that must be used as a source. EIGRP Autonomous System number is essential to form neighborship. AS could be discover by "discover" command or manually set with "asn". "discover" is recommended step, as far as it will listen all hellos, collects all active peers and AS number automatically.</p>

<p>EIGRP neighbourship is formed with Hello packets sent to multicast address: 224.0.0.10 periodically. The interval  by default is 5 sec. After receiving Hello packet from a new router, all active routers will add the sender to its neighbor list, send an Init Update packet and waits for ACK Update response. All these tasks are done with "hi" command.</p>

<pre><code>SuSe:~/coly # ./coly.py <br>
EIGRP route injector, v0.1 Source: http://code.google.com/p/coly/<br>
suse(router-config)#interface tap0<br>
Interface set to tap0, IP: 10.0.0.1<br>
suse(router-config)#discover<br>
Discovering Peers and AS<br>
Peer found: 10.0.0.3 AS: 33 <br>
AS set to 33<br>
Peer found: 10.0.0.2 AS: 33 <br>
<br>
suse(router-config)#hi<br>
Hello thread started<br>
suse(router-config)#<br>
</code></pre>

<pre><code>R1#<br>
*Mar  1 02:04:08.771: %DUAL-5-NBRCHANGE: IP-EIGRP(0) 33: Neighbor 10.0.0.1 (FastEthernet0/0) is up: new adjacency<br>
R1#<br>
</code></pre>

At this point, neighbourship is successfully formed, and we can participate into EIGRP Process.<br>
<br>
<pre><code><br>
R1#sh ip route<br>
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP<br>
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area <br>
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2<br>
       E1 - OSPF external type 1, E2 - OSPF external type 2<br>
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2<br>
       ia - IS-IS inter area, * - candidate default, U - per-user static route<br>
       o - ODR, P - periodic downloaded static route<br>
<br>
Gateway of last resort is not set<br>
<br>
     10.0.0.0/24 is subnetted, 1 subnets<br>
C       10.0.0.0 is directly connected, FastEthernet0/0<br>
R1#<br>
<br>
</code></pre>

<pre><code><br>
suse(router-config)#inject 192.168.1.0/24<br>
Sending route to 10.0.0.3<br>
Sending route to 10.0.0.2<br>
suse(router-config)#<br>
<br>
</code></pre>

<pre><code><br>
R1#sh ip route<br>
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP<br>
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area <br>
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2<br>
       E1 - OSPF external type 1, E2 - OSPF external type 2<br>
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2<br>
       ia - IS-IS inter area, * - candidate default, U - per-user static route<br>
       o - ODR, P - periodic downloaded static route<br>
<br>
Gateway of last resort is not set<br>
<br>
     10.0.0.0/24 is subnetted, 1 subnets<br>
C       10.0.0.0 is directly connected, FastEthernet0/0<br>
D    192.168.1.0/24 [90/156160] via 10.0.0.1, 00:00:17, FastEthernet0/0<br>
R1#<br>
<br>
<br>
</code></pre>

<p>With "inject" command, EIGRP Internal Route Update packet is sent to all active peers directly. So one thing that you have to be sure is that, all the peers are correctly detected. If you define ASN statically or if it's automatically discovered, with both ways, peer IP addresses are saved and updates are sent individually. To check active EIGRP peers use "peers" command.</p>

<pre><code><br>
suse(router-config)#peers<br>
10.0.0.3<br>
10.0.0.2<br>
suse(router-config)#<br>
<br>
</code></pre>