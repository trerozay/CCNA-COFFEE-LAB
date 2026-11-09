# LinkedIn Post Draft

I’m excited to share a Cisco Packet Tracer project I’ve been building: a segmented network for a small coffee shop.

The goal was to design a network that supports business devices and customer Wi-Fi while keeping guest traffic separated from internal resources.

In this lab, I configured:

- VLANs for office devices, corporate Wi-Fi, guest Wi-Fi, and network management
- An 802.1Q trunk and router-on-a-stick inter-VLAN routing
- DHCP pools for each client network
- Separate corporate and guest wireless access points
- An extended ACL that blocks guest users from the office, corporate, and management VLANs
- Basic switch hardening with encrypted passwords, PortFast, BPDU Guard, and an MOTD banner

I also created a test plan to verify DHCP, wireless association, gateway reachability, inter-VLAN routing, trunk operation, and guest isolation in Packet Tracer Simulation Mode.

One troubleshooting lesson from this project was that creating an ACL is not enough—it must be applied to the correct interface and in the correct direction. I applied the guest policy inbound on the VLAN 30 router subinterface and used ACL counters to verify the results.

This project gave me more hands-on experience with network segmentation, routing, access control, and technical documentation as I continue preparing for the CCNA.

GitHub: [ADD YOUR REPOSITORY LINK]

#Cisco #CCNA #Networking #PacketTracer #VLAN #Cybersecurity #ITSupport #NetworkEngineering #LearningInPublic
