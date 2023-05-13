---
layout: post
title: Idea on abstractions of analog design
math: true
date: 2023-05-13
---


## Why 
The real world is analog. The mathematics of quantum electrodynamics explain the
nano-scale world with extreme precision. The mathematics can predict the
probabilities of future events, thus we know the mathematics is sound. Time,
space, and probabilities of state-transitions are all
complex number continuous variables (at least as far as we've been able
to see). 

The momentum  ($p = \hbar k$) and Energy ($E = \hbar \omega$) are
sometime said to be quantized, but since $k$ and $\omega$ can take on any value
for a free particle, I don't think the quantization is general. For bound
fermions, however, the energies and momentum are
quantized. Only fixed values of $k$ and $\omega$ are
possible. Two electrons (a fermion, or spin $1/2$ particle) cannot exist in the
same space and the same energy. That's why when you bring two atoms close
together, like a crystal, the wave-functions interact, and the discrete energy level
split into bands of allowed energy. 

Since the real world is analog, there will always, to the end of human time,
be a need for engineers that can make analog circuits. Independent of technology.

## Abstraction principle

In software there is an [Abstraction
Principle](https://en.wikipedia.org/wiki/Abstraction_principle_(computer_programming))
for how software engineers should think about software development. The general
principle is [Don't repeat
yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). We violate
that often in analog circuit design. 

For the past soon 70 years of integrated circuit design the way we make analog
circuits have not changed much. Every 5 ish years we have to change technology
(180 nm to 55 nm to 22 nm). Every time we change technologies we  **have to
repeat our self**. We have to create the schematics, and the layout all over again.
For every technology we have to spend months to years to again make analog
circuits that work. There is virtually no reuse of analog circuit design between technologies. 

Analog circuit design have a intimate, entangled, unbreakable bond with the integrated
circuit technology. A current proportional to temperature (PTAT) circuit made in 55 nm CMOS from TSMC is unlikely to work
in 55 nm CMOS from UMC. All technologies require tweaks to the analog
circuit.

We rely on a process design kit for a technology with simulation
models that describe as detailed as possible the physical behavior of a
 transistor. Since no two factories, or
companies, have exactly the same manufacturing process, two transistors from different companies will
not match in their physical behavior. 

For digital circuits, we've abstracted away the transistor. We know we can
configure two PMOS and two NMOS into a 2-input NAND. A NAND is a circuit where when both inputs are high,
the output is low. Otherwise the output is high. Based on a NAND we can
construct any digital circuit. To transition to a new technology it's just one
cell we need to make. In practice we make a few more "standard cells", but
that's due to area, speed or power optimization.

Analog circuits play between high
and low. The currents, charges and voltages between nodes, and how they change,
create insanely complex interactions. To my knowledge, no-one has found a
general abstraction level for analog circuits.


## Circuit abstraction layers

Although there is no general abstraction level for transistors, we can reuse
ideas between technologies. A PTAT current may have almost same
schematic and the same interface in two technologies, however, the size of transistors, resistors, bipolars
and capacitors, and their physical properties will be different.

I think it's pointless to find a general abstraction level,  for devices
(transistors, resistors, capacitors, inductors, bipolars, diodes). Those will
always be different, they are in the real world, and their physical behavior is tied
to how the integrated circuit is manufactured. 

I think we should focus on reusing the idea. We must disentangle the analog
circuit idea from the technology. 

I've been working on the problem of "idea reuse" since 2009, and have made some
progress. Below is a status, and my current thoughts. I think for each circuit
abstraction layer the choice of data source for the design and layout should
change. 

Devices should definetly be compiled, probably the same for cells (level
shifters, standard cells). 

For cells it does depend on the complexity. A cell
that is too complex to be easily understood by looking at the
netlist, should not be considered a Cell, but rather, a Block. 

A typical Block can be an OTA, comparator, bias current generator, voltage
regulator or similar. At some point a Block consists of only Blocks, with few
analog interfaces between them. As soon as analog interfaces are few, 
I think it makes more sense to transition to SystermVerilog.

| Status | Abstraction | Design | Layout | Why |
|:------:|:------------|:-------|:-------|:----|
| :construction:       | Chip        | SystemVerilog     | digital     | Complex connections, few analog interfaces              |
| :construction:       | Module      | SystemVerilog     | digital     | Large amount of digital signals, few analog signals     |
| :warning:       | Block       | Schematic         | programmatic | Large amount of critical analog interfaces, few digital |
| :white_check_mark:      | Cell         | Netlist/JSON | compiled    | Few analog interfaces, few digital interfaces                                 |
| :white_check_mark:      | Device      | JSON              | compiled    | Polygon pushing                                         |
| :white_check_mark:      | Technology  | JSON/Rules        | compiled    | Custom for each technology                              |

See  [My thoughts on
analog](https://analogicus.com/aic2023/2022/12/03/My-thoughts-on-analog-design.html)
for more information on Technology, Device and Cell abstraction layers.

## Block 
An analog block, like a PTAT current source have large amount of critical analog
interfaces, and a few digital signals.

From books, papers, or in rare instances a bright mind, we get the "idea" for the
PTAT current source. 

A schematic, like the one below, is how we store the idea.
Analog designers are trained schematic readers and most can immediately
understand how the circuit works.

![](/ideas/assets/bias_ibp3.svg)

Making the idea work in a particular technology, however, is fundamentally a
different task. With a quick glance an analog designer can see that the circuit
won't work in 55 nm for core transistors if the supply is low (1.0 V). 

The VD1
is always a diode voltage above ground (0.9 V ish at low temperature). $V(VBN,VD1)$ will at
least be a threshold voltage (0.5 V ish). $V(VBNC,VBN)$ will be at least 0.2 V,
and $V(AVDD_CV,VBNC)$ will be 0.2 V ish. Thus the minimum AVDD_CV of the circuit
is $ 0.9 + 0.5 + 0.2*2 = 1.8 V$. As a result, in 55 nm with core transistor the
"idea" is a "bad idea", but in the same technology with IO transistors (3.0 V)
it may be a "good idea" and could be worth a try.

To answer whether the "idea" is good in a technology, the analog designer must
tweak all the width, lengths, multipliers, of each device and run through
hundreds of simulations. Afterwards, the schematic may look the same, but all
devices will have tweaked values. 

Porting to another technology will again change device values,
but the schematic will look the same.

I think it would be a good idea to separate the "idea" from the technology
implementation. Each technology port is about 5 years from each-other. Accordingly, it's
rare that the port is done by the same person as last time. That means, a new
person will try and copy the old idea, and maybe even redraw schematic again. 

Humans are error prone, and it's easy to draw a wire wrong, or insert a wrong
component, or forget a power down transistor. Had the "idea" been stored in a
technology independent format, there would be less chance of introducing human
errors. 

## Idea

What I'd love to do is to use Xschem schematics to store the "idea", then store
the technology specific items, like exact transistor types, widths, lengths,
in another format (YAML or python). 

The "idea"
schematic and technology layer could go through a compiler to generate a technology
specific schematic. 

Exactly how to do that, I'm not sure yet, but I think that's my next step.









