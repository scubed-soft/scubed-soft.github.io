---
layout: post
title: "Pi-hole in the Sky"
date: 2020-09-07T15:46:00-07:00
comments: false
author: Hubert Lee
---
Ads are an ever-present nuisance on the modern web. Recently, I've gotten
frustrated enough that I looked into setting up a [Pi-hole][1] to block ads
before my devices ever load them. But I don't have a Raspberry Pi at home and I
didn't want to order one either. Instead, I looked into setting up Pi-hole on
Amazon Web Services.

<!--more-->

The Pi-hole documentation includes [a guide][2] on setting up a Pi-hole on
Digital Ocean secured with OpenVPN. There's a lot of manual configuration and
provisioning required to get through the guide and set things up correctly, so I
wondered how much of this work could possibly be done automatically. That
thought led me to AWS and the relatively new
[AWS Cloud Development Kit (CDK)][3]. AWS offers powerful infrastructure
provisioning and configuration features through CloudFormation and the CDK makes
using CloudFormation significantly easier and more intuitive.

There are quite a lot of guides online about setting up a Pi-hole on AWS, but
none of them provisioned the infrastructure exactly the way I wanted: Pi-hole
running on a Docker container with ECS Fargate secured behind OpenVPN,
automatically provisioned and configured to the greatest extent possible.

To that end, I created a CDK stack that provisions all of these resources and
have published it [on GitHub][4]. Here's what the CDK stack provisions for you:
* A VPC with a single private subnet accessible through a VPN Gateway
* An ECS cluster attached to the VPC's private subnet with a Fargate task
  configured to run a container with Pi-hole (container image downloaded from
  [dockerhub][5])
* A Client VPN endpoint to control access to the Pi-hole container
* All the associated networking configuration to get these components talking to
  each other and nothing else

## Caveats
### VPN configuration
I wasn't able to provision everything automatically though. One major component
that isn't automatically provisioned is the VPN certificates. For now, you must
generate and sign your own CA and client certificates and upload them to AWS
Certificate Manager in order to complete VPN configuration. ACM does offer a
hosted private CA service, but that [costs $400/month][6]. If you can afford
that, you might as well just buy and install physical Raspberry Pis with Pi-hole
everywhere you need and it'll still be cheaper.

### Cost
There are a couple infrastructure components that contribute to the majority of
the cost of this setup:
1. AWS Client VPN endpoint ([pricing page][7]) - AWS charges an hourly rate for each
  endpoint associated with a subnet and an additional hourly rate for each
  client connected to the VPN endpoint
1. NAT gateway ([pricing page][8]) - AWS charges an hourly rate for each NAT gateway
  and an additional rate per GB of data transferred through the NAT gateway

## Conclusion
Although I was able to get a significant portion of the infrastructure
provisioned automatically through the CDK, it was disappointing that I could not
also easily provision the VPN certificates without incurring significant costs.
I ran this setup off and on for a couple weeks to try it out, but ultimately
decided not to stick with it. It was easier to setup a local machine on my
network to run Pi-hole than to VPN into the AWS Pi-hole instance each time I
wanted to use the internet. Having a local machine also ensured that all other
devices on my network benefitted from Pi-hole as well. Finally, the cost of
maintaining an AWS Client VPN endpoint and a NAT gateway is prohibitively
expensive. It is much cheaper to buy your own Raspberry Pi and connect it to
your network. Nevertheless, this was a fun experiment and I learned a lot about
VPC networking.

## Additional references
* [An Exercise Program for the Fat Web][r1]
* [Pie in the Sky-Hole][r2]
* [OpenVPN+PiHole ad-blocking on AWS Lightsail for 3.50$/mo][r3]
* [How to Setup Pi-hole on an AWS Instance][r4]
* [A CloudFormation script to get a docker instance of pi-hole running in your AWS account][r5]

[1]: https://pi-hole.net/
[2]: https://docs.pi-hole.net/guides/vpn/overview/
[3]: https://docs.aws.amazon.com/cdk/latest/guide/home.html
[4]: https://github.com/hube/aws-pihole
[5]: https://hub.docker.com/r/pihole/pihole/
[6]: https://aws.amazon.com/certificate-manager/pricing/
[7]: https://aws.amazon.com/vpn/pricing/#AWS_Client_VPN_pricing
[8]: https://aws.amazon.com/vpc/pricing/#natgatewaypricing
[r1]: https://blog.codinghorror.com/an-exercise-program-for-the-fat-web/
[r2]: https://dlaa.me/blog/post/skyhole
[r3]: https://medium.com/fillory/openvpn-pihole-ad-blocking-on-aws-lightsail-for-3-50-mo-7e814eafff84
[r4]: https://blog.sethmay.net/2020/05/how-to-setup-pi-hole-on-an-aws-instance/
[r5]: https://www.observian.com/blog/cloudformation-pi-hole-and-you
