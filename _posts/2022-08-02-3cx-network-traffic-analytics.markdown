---
layout: post
title:  "3CX Network Traffic Analytics"
date:   2022-08-02 10:00:00 -0400
---
The [3CX Phone System][3cx-home] is an open standards communications solution which provides advanced contact center features to businesses of any size. For businesses that have a large 3CX installation, possibly including multiple locations and a mixture of cloud and on-premise deployment, it can be a challenge to gain reliable network traffic analytics.

Before discussing how to obtain **Network Traffic Analytics** (NTA) in a complex 3CX environment, let's take a look at why NTA is important in the first place.

# Why is NTA important?

For a large or multi-site 3CX installation, there are 3 main reasons why NTA are important:

1. **Network Performance**: Network is an integral part of contact center and UC operations.  Since everything goes through your network, it is the ideal reference point to look at the dynamics of 3CX.  By monitoring network service level, bandwidth usage and QoS, you can measure the network experiences of your customers and respond to connectivity issues more quickly.

1. **Application Performance**: It is possible to make better decisions in a more timely manner if you have access to application telemetry that is quick and easy.  This includes call blockage, SIP response codes, voice quality measurement, peak hour channel usage, and most active endpoints.

1. **Network Security and Audit**: NTA can be used to establish network baseline for cybersecurity, by recording metadata of all network activities.

# How to implement Prilink NTA

Prilink NTA is based on Traffic Mirroring.  Mirrored traffic is a duplicate of Internet and SIP Trunk traffic that is sent to an analyzer device called a **t-meter**. The t-meter compiles compact NTA in real-time, which can be accessed through online dashboards or downloaded to your PC.

There are 2 options for sending traffic from a 3CX server to a t-meter for analysis:

1. If you are using a Linux-based 3CX server, you can modify the server to send out mirrored traffic using VXLAN.  It is simple to configure a 3CX server on Debian 11 to send out mirrored traffic to a t-meter.   Consult our [VXLAN Mirror Traffic][vxlan-repo] repository which contains a script to automate the setup process on Debian 11.  This is the simplest option if your 3CX server is Linux based and you are comfortable making modifications.

1. Option 1 above will not apply if you are using a Windows-based 3CX server, or if you are hesitant to modify your 3CX server. If this is the case, you can utilize a secondary device provided by Prilink called a **VXLAN collector**. This device will accept traffic from your 3CX server, package it using VXLAN and send it to a t-meter for analysis.
    - If your 3CX server is in the cloud, then a VXLAN collector can be launched within your cloud, and you can use your cloud provider's traffic mirroring (such as [AWS Traffic Mirroring][aws-mirror]) to send traffic from the 3CX server to the VXLAN collector.  The VXLAN collector is available as an AMI within the AWS Marketplace
    - If your 3CX server is on premise, then a VXLAN collector can be installed on premise, and you can setup port mirroring on a switch or router to send a copy of traffic from the 3CX server to the VXLAN collector.  You can download our VXLAN collector ISO and install on a physical or virtual server of your choosing.
    - If your 3CX server is running on top of Windows Hyper-V, then a VXLAN collector can be installed as a virtual machine on top of Hyper-V, and you can setup port mirroring in Hyper-V manager to send a copy of traffic from the 3CX server VM to the VXLAN collector VM.  Simply set 3CX as the port mirror source and VXLAN collector as the port mirror destination.

Whichever option you use, traffic from your 3CX server will be sent to a t-meter for analysis, either directly or through a VXLAN collector.  By default, the t-meter is deployed within the AWS Ohio Region.  However, any alternate AWS region can also be selected by request, and you can even install the t-meter in a private cloud if do not want your 3CX traffic to be sent over the internet.

# Free Trial

[Contact Prilink][contact] about our free 30-day trial.

[3cx-home]:   https://www.3cx.com/
[vxlan-repo]: https://github.com/PrilinkLtd/VXLAN-mirror-traffic
[aws-mirror]: https://docs.aws.amazon.com/vpc/latest/mirroring/what-is-traffic-mirroring.html
[contact]:    https://prilink.com/contact/
