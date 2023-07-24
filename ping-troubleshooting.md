[ICMP header](https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol)
[Ping man - Cisco](https://www.cisco.com/c/en/us/support/docs/ios-nx-os-software/ios-software-releases-121-mainline/12778-ping-traceroute.html)

#strategies for using ping
the user just might not be available. As an alternative, using different ping commands from different routers can help isolate the problem.
- issue the ping from the router nearest X, typically the router acting as host X’s default gateway.
- By default, the Cisco IOS ping command sends five echo messages, with a timeout of 2 seconds. If the command does not receive an echo reply within 2 seconds, the command considers that message to be a failure, and the command lists a period. If a successful reply is received within 2 seconds, the command displays an exclamation point.
- As a quick aside, the example shows a common and normal behavior with ping commands: the first ping command shows one failure to start, but then the rest of the messages work. This usually happens because some device in the end-to-end route is missing an ARP table entry.
- Now think about troubleshooting and what a working ping command tells us about the current behavior of this internetwork. First, focus on the big picture for a moment:
  - ■ R1 can send ICMP echo request messages to host B (172.16.2.101).
  - ■ R1 sends these messages from its outgoing interface’s IP address (by default), 172.16.4.1 in this case.
  - ■ Host B can send ICMP echo reply messages to R1’s 172.16.4.1 IP address (hosts send echo reply messages to the IP address from which the echo request was received).
- R1 must have a route that matches host B’s address (172.16.2.101);
- “R2 also needs a route for host B’s address”
- The working ping commands in Example 18-2 also require the data-link and physical layer details to be working.
- The WAN link must be working: The router interfaces must be up/up
- On the LAN, R2’s LAN interface must be in an up/up state.
- The switch interfaces in use are in a connected (up/up) state.
- Port security (discussed in the CCNA 200-301 Official Cert Guide, Volume 2) does not filter frames sent by R2 or host B.
- STP has placed the right ports into a forwarding state.
- confirms that IP access control lists (ACL) did not filter the ICMP messages
- ping command on R1 can also be used to reasonably predict that ARP worked and that switch SW2 learned MAC addresses for its MAC address table.

What ping from default gateway does not do (use extended ping instead):
- it does not test the reverse route back toward the original host. R1 has to pick a source IP address to use for the echo request, and routers choose the IP address of the outgoing interface.
- Extended ping allows R1’s ping command to use R1’s LAN IP address from within subnet 172.16.1.0/24
- The example shows the ping command on R1 that matches the logic in Figure 18-6. This same command could have been issued from the command line as ping 172.16.2.101 source 172.16.1.1.

neither the standard or extended ping commands in these two examples so far in this chapter can test for some kinds of problems, such as the following: 
- ■ IP ACLs that discard packets based on host A’s IP address but allow packets that match the router’s IP address
- ■ LAN switch port security that filters A’s frames (based on A’s MAC address)
- ■ IP routes on routers that happen to match host A’s 172.16.1.51 address, with different routes that match R1’s 172.16.1.1 address
- ■ Problems with host A’s default router setting

Test from gateway back to host
- Testing using a ping of another device on the LAN can quickly confirm whether the LAN can pass packets and frames.
- If the ping works, it confirms the following, which rules out some potential issues:
- ■ The host with address 172.16.1.51 replied.
- ■ The LAN can pass unicast frames from R1 to host 172.16.1.51 and vice versa.
- ■ You can reasonably assume that the switches learned the MAC addresses of the router and the host, adding those to the MAC address tables.
- ■ Host A and Router R1 completed the ARP process and list each other in their respective Address Resolution Protocol (ARP) tables.

If the ping 172.16.1.51 on R1 fails (Figure 18-7), that result points to this list of potential root causes: 
- ■ IP addressing problem: Host A could be statically configured with the wrong IP address.
- ■ DHCP problems: If you are using Dynamic Host Configuration Protocol (DHCP), many problems could exist. Chapter 7, “Implementing DHCP” in CCNA 200-301 Official Cert Guide, Volume 2, discusses those possibilities in some depth.
- ■ VLAN trunking problems: The router could be configured for 802.1Q trunking, when the switch is not (or vice versa).
- ■ LAN problems: A wide variety of issues could exist with the Layer 2 switches, preventing any frames from flowing between host A and the router.

A standard ping of a LAN host from a router does not test that host’s default router setting. However, an extended ping can test the host’s default router setting. Both tests can be useful, especially for problem isolation, because 
- ■ If a standard ping of a local LAN host works...
- ■ But an extended ping of the same LAN host fails...
- ■ The problem likely relates somehow to the host’s default router setting.
- 

Testing WAN neighbors
A successful ping of the IP address on the other end of an Ethernet WAN link that sits between two routers confirms several specific facts, such as the following: 
- ■ Both routers’ WAN interfaces are in an up/up state.
- ■ The Layer 1 and 2 features of the link work.
- ■ The routers believe that the neighboring router’s IP address is in the same subnet.
- ■ Inbound ACLs on both routers do not filter the incoming packets, respectively.
- ■ The remote router is configured with the expected IP address (172.16.4.2 in this case).
- 

pinging a hostname allows the network engineer to further test whether the Domain Name System (DNS) process works.
- The command, of course, tests the host’s own DNS client settings.
- a classic comparison is to first ping the destination host using the hostname, which requires a DNS request. Then, repeat the same test, but use the destination host’s IP address instead of its name, which does not require the DNS request.
- 
