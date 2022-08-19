---
layout: post
title:  "Traffic Mirroring using VXLAN for Windows Users"
date:   2022-08-19 10:00:00 -0400
---
The first step in network monitoring is to arrange for a copy of network traffic to be sent to selected devices for analysis, threat detection and troubleshooting.  Port mirroring is commonly used to achieve this if the analyzer tool is within your local network.  If the analyzer tool is outside of your local network domain, then a VXLAN tunnel can be used to send traffic to the remote analyzer tool.

Depending on the source of network traffic that you wish to monitor, there are 3 scenarios to consider if you want to use VXLAN to send local traffic to a remote analyzer tool:

1. If the traffic source is a Linux-based server, then you can easily modify the server to send out mirrored traffic using VXLAN.  An example on Debian 11 can be found in our [VXLAN Mirror Traffic][vxlan-repo] repository.

1. If the traffic source is a cloud-based server, then you can use your cloud provider's traffic mirroring (such as [AWS Traffic Mirroring][aws-mirror]) to send out mirrored traffic using VXLAN.

1. If the traffic source is a Windows-based server, then you can run a Linux-based virtual machine on top of Microsoft Hyper-V to send out mirrored traffic using VXLAN.  We commonly refer to this VM as a **VXLAN Collector**.  The remainder of this post will describe how to setup a VXLAN collector on top of Microsoft Hyper-V.

# VXLAN Collector on Windows

The only requirement of the Windows host is that it must support Microsoft Hyper-V.

The first step is to launch a VM on top of Microsoft Hyper-V using the standard Debian 11 ISO.  Once the VM is launched, there are 2 files that must be edited to turn it into a VXLAN Collector.

1. Create `/etc/vxlan` with the content below, changing the local IP address to correspond to the VM, and the remote IP addresses to correspond to the remote analyzer tool:

   ```
   ip link set eth0 promisc on
   tc qdisc add dev eth0 root pfifo
   ip link add vxlan0 type vxlan id 128 local 192.168.1.157 remote 192.168.1.107 dev eth0 dstport 4789
   tc qdisc add dev vxlan0 root pfifo
   ip link set vxlan0 up
   tc qdisc add dev eth0 handle ffff: ingress
   tc filter add dev eth0 parent ffff: matchall action mirred egress mirror dev vxlan0
   ```
1. Create `/etc/rc.local` with the content below:

   ```
   #!/bin/bash
   /etc/vxlan & > /dev/tty1
   ```
1. Reboot

VXLAN Collector setup is now complete.

All traffic received by the primary interface on the VM will be mirrored to the remote analyzer tool, specified by the remote IP address in `/etc/vxlan`.

[vxlan-repo]: https://github.com/PrilinkLtd/VXLAN-mirror-traffic
[aws-mirror]: https://docs.aws.amazon.com/vpc/latest/mirroring/
