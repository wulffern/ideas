---
layout: post
title: Idea on problems
math: true
date: 2024-02-29
---

## Why 
We train students in the intricacy of solid state physics, analog circuit
design, digital circuit design, circuit implementation, project management and
team work, but I think we sometimes forget to tell them about the value of a
problem. 

In academia we have extremely bright people working on fantastic technical
solutions and innovations. In the absence of real problems, academics will
invent theoretical problems, and sometimes, the problems they invent should not
be solved. 

## Inventing problems
One of my pet peeves is the horrible efficiency of ambient energy
harvesting, the idea that one can harvest the ambient 2.4 GHz radio waves that
are all around us. A normal phone transmitting Bluetooh will transmit at 10 dBm
(10 mW), or maybe up to 20 dBm (100 mW) in some instances. Assuming an antenna
that transmits equally in all directions we loose about 40 dB on the first
meter. After the first meter we loose between 6 dB to 12 dB per doubling of
distance, sometimes even more. If I place an energy harvester 1 meter way from
my transmitter, I can maximum get -20 dBm (10 $\mu$W), but normally I would get
-30 dBm (1 $\mu$W). That's not a lot of energy. An invented problem is to make a 
more efficient energy harvester of that
insignificant energy.

People do work on more efficient harvesters, 
take [Design of Sub-Gigahertz Reconfigurable RF Energy Harvester From −22 to 4
dBm With 99.8% Peak MPPT Power
Efficiency](https://ieeexplore.ieee.org/document/8742574). The publication is 
in Journal of Solid State Circuits, which means the work is excellente, and they
have measurements of real silicon. I'm sure the technical details, the circuits
cool, and the people smart. 

I question the reason to do the work from the get go. The minimum input power is
-22 dBm, which means the transmitter needs to be pretty close (although the
frequency is approx 1 GHz, which makes the path loss in the first meter a bit
less). 

Best case, the system efficiency (Power harvested divided by Power transmitted)
is 1/1000, or 0.1 %, a horrible efficiency. So why work on the problem in the
first place?

## The power of real problems

There are real problems in this world. In industry, we are lucky, problems come
and find us. Customers will sometimes tell us what they are struggling with. Our
circuits will fail, and we have to find inventive solutions. Specifications can
be impossible, which can drive innovations of new solutions. 

In case of energy harvesting, I think the real problem is the system efficiency,
so that's what should be attacked first. Is it possible to have beamforming in
order to improve the energy transfer between transmitter and reciever? Can a
beamforming antenna scan the 3 dimensional space in search of IoT devices, and
direct energy efficiently to the reciever? Like a laser? That could make the
system efficiency acceptable.

The power of a real problem is that you know it's valuable. I think Elon's way
of thinking is a good example. For example:

1. Humans have to become a multiplanatery species if we are to survive long term 
1. Mars is our closest option.
1. To make travel to Mars low cost, and efficient we need vertical take of and
   landing rockets 
1. Solve VTOL rockets. 

There is a clear link from the ultimate goal "Saving the human race" to the
"Solve VTOL rockets"


# A collection of real problems 

Maybe I'll add more with time 

## Sub 100 mV oscillators (as low as possible)
If there is a temperature differential in a system, we can use thermoelectric
generators to harvest energy. 

Thermoelectric generators can provide significant current, 
but the voltage is low. See [Energy
Soruces](https://analogicus.com/aic2023/2023/04/20/Lecture-X-Energy-Sources.html)
for details. 

In order to drive a circuit we need to boost the voltage
up multiple hundred millivolts. 

To boost a voltage, we need a clock.

> Design a "as low as possible" oscillator of around a kHz





