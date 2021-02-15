---
title: "Rural Internet"
date: 2020-10-22
draft: false
---

One of the joys of living out in the rural hither lands is a lack of 
broadband internet.

We have a satellite connection with Viasat, and while it’s fine for casual 
surfing it’s not usable for work stuff — with it connecting to a satellite 
in geosynchronous orbit (about 10% the distance to the moon; light takes a 
noticeable bit of time to go that far and back) the latency is way too high 
for video conferencing or VPN.

Until SpaceX gets their Starlink system released (which uses satellites in 
low-earth orbit so the latency is much reduced) I’m stuck with cellular 
internet.

I was using the system I had in the RV, but my yearly plan ran out and AT&T 
decided a price increase of 3000% was appropriate during a pandemic. So 
that’s out. But I did find a service that provides a suitable unlimited 
data plan for a bit over $100 a month on cellular networks. Which is nice 
since I use about 10GB a day for normal work stuff — it adds up over the month.

One problem is that I’m about 5 miles away from my local cell tower and we 
have a lot of trees and hills in the way so my signal strength is low and 
my connection isn’t reliable.

But the other day I was out on the deck with a telescope and noticed I had 
a line of sight to this tower if I looked though a gap in our trees and at 
the right location I can see the very top of the cell tower peeking over the 
distant ridge. That’ll work with the proper equipment.

So Amazon delivered some goodies. I got several log periodic dipole array 
(LPDA) antenna. These are highly directional wide bandwidth antenna tuned 
to 4G LTE frequencies. The trick with these is the installation — these work 
best in something called 2×2 MIMO to establish two data streams with the 
tower. They’re on the same frequency so for the spatial multiplexing to be 
effective the antennas must be isolated and configured to provide a low 
correlation coefficient. The easiest way to do this in a 2×2 system is to 
use orthogonal polarization; i.e. each antenna is rotated 90-degree from 
the other along a parallel axis pointing toward the distant tower. Then the 
antennas need to be between .5 and 2 λ (wavelength) apart so they don’t 
interfere with each other.

I’ll have to determine what band I’m using on the tower and do some math to 
get that measurement, but that’s not too hard and should around a foot or so.

After getting it hooked up and the antenna mounted it was time to do a 
little testing.

Measuring the signal, the RSRP (signal strength) went from -106 dBm to 
-86 dBm (marginal to excellent) and the RSRQ (signal quality) went from 
-16 dBm to -10 dBm (poor to excellent). My actual internet speeds didn’t 
change much (being around 4 Mbps up and down; good enough for out here) 
but the ping times are down and the connection should be more stable.

I couldn’t figure out a good way to get the thick cables inside the house 
without doing a lot of drilling, so I just ran the antenna leads under the 
deck and into a rubbermaid container out on the deck right outside from my 
office. In the container I put the LTE modem, a wifi router, a battery 
backup, and a Raspberry Pi for monitoring. There was already an electrical 
outlet on the deck for power and even though it’s all outside, my desk is 
close enough I get a full-strength wifi signal and I didn’t have to make 
any permanent alterations to the house. I hope I’ll only need this for a 
year or so until SpaceX gets Starlink operational as that should be a 
better fit for my internet needs.

&#x269B;
