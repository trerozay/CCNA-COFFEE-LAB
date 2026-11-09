# Coffee Shop Lab Test Plan

Use this checklist after the topology has converged. Test in order so failures can be isolated quickly.

## 1. Physical and interface status

Confirm all intended links show green indicators in Packet Tracer.

On the router:

```text
show ip interface brief
```

Expected: `G0/1` and subinterfaces `.10`, `.20`, `.30`, and `.99` are `up/up`.

On the switch:

```text
show interfaces status
show ip interface brief
```

Expected: connected access ports and the router trunk are up; `Vlan99` is up.

## 2. VLAN membership

```text
show vlan brief
```

Expected:

- Office ports are in VLAN 10.
- Corporate AP port is in VLAN 20.
- Guest AP port is in VLAN 30.
- VLAN 99 exists for management.

If a device is in the wrong subnet, verify the switch port's access VLAN first.

## 3. Trunk operation

```text
show interfaces trunk
```

Expected:

- The switch-to-router link is trunking.
- VLANs 10, 20, 30, and 99 are allowed.
- VLAN 99 is the native VLAN on both ends.

## 4. DHCP

On each PC or laptop, open **Desktop > IP Configuration**, select **DHCP**, and confirm the address matches its VLAN.

| Client type | Expected address | Gateway |
|---|---|---|
| Office | `192.168.10.21-254` | `192.168.10.1` |
| Corporate Wi-Fi | `192.168.20.21-254` | `192.168.20.1` |
| Guest Wi-Fi | `192.168.30.21-254` | `192.168.30.1` |

On the router:

```text
show ip dhcp binding
show ip dhcp pool
```

If a client receives `169.254.x.x`, check its wireless association, access VLAN, trunk, and DHCP pool.

## 5. Wireless association

For each laptop:

1. Open **Desktop > PC Wireless**.
2. Select the correct SSID.
3. Enter the lab-only pre-shared key.
4. Confirm the display shows association with the access point.
5. Renew the DHCP address.

The corporate laptop should join the corporate SSID; guest laptops should join the guest SSID.

## 6. Gateway connectivity

From an office PC:

```text
ping 192.168.10.1
```

From the corporate laptop:

```text
ping 192.168.20.1
```

From a guest laptop:

```text
ping 192.168.30.1
```

Expected: all three tests pass. The first ping may time out while ARP resolves; repeat it.

## 7. Inter-VLAN routing

From the office PC, ping a corporate client address.

Expected: pass, unless an additional policy intentionally blocks office-to-corporate traffic.

On the router:

```text
show ip route
```

Expected: all four `/24` networks appear as directly connected routes.

## 8. Guest isolation ACL

From a guest laptop, test:

| Destination | Example | Expected |
|---|---|---|
| Guest gateway | `192.168.30.1` | Pass |
| Office gateway/client | `192.168.10.1` | Block |
| Corporate gateway/client | `192.168.20.1` | Block |
| Switch management | `192.168.99.2` | Block |
| Upstream destination | ISP/cloud test address | Pass if upstream route/NAT exists |

Then check counters:

```text
show access-lists GUEST_RESTRICTIONS
show running-config interface gigabitethernet0/1.30
```

Expected: deny and permit counters increase, and the ACL is applied inbound on `G0/1.30`.

## 9. Packet Tracer Simulation Mode

1. Select **Simulation**.
2. Filter for ARP, DHCP, ICMP, and DNS.
3. Use **Add Simple PDU** from a client to its gateway.
4. Step through events with **Capture/Forward**.
5. Repeat from a guest laptop to an office device.

Expected: permitted ICMP reaches its destination; guest-to-office traffic is dropped at the router ACL.

## Troubleshooting order

Work from the bottom up:

1. Cable/link status
2. Access VLAN and wireless association
3. Trunk and allowed VLANs
4. Client IP, mask, and gateway
5. Router subinterface status
6. Routing table
7. ACL direction and counters
8. Upstream route and NAT, if internet access is required
