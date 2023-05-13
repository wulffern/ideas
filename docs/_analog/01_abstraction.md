---
layout: post
title: Abstraction of analog design
math: true
---

## Why 
The real world is analog. The mathematics of quantum electrodynamics explain the
nano-scale world with extreme precision. The mathematics can predict the
probabilties of future events, thus we know the mathematics is sound. Time,
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
need for engineers that can make analog circuits. Independent of CMOS technology.

## Abstraction principle

In software there is an [Abstraction
Principle](https://en.wikipedia.org/wiki/Abstraction_principle_(computer_programming))
for how software engineers should think about software development. The general
principle is [Don't repeat
yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). We violate
that often in analog circuit design. 

For the past soon 70 years of integrated circuit design the way we make analog
circuits have not changed much. Every 5ish years we have to change technology
(180 nm to 55 nm to 22 nm). Every time we change technologies we  **have to
repeat ourself**. We have to create the schematics, and the layout all over again.
Every transition we have to spend months to years to again make a voltage
reference that works. There is virtually no reuse of analog circuit design between technologies. 

Analog circuits have a intimate, entangled, unbreakable bond with the integrated
circuit technology. A voltage reference circuit made in 55 nm CMOS from TSMC is unlikely to work
in 55 nm CMOS from UMC. All technologies require tweaks to the
circuit.

We rely on a process design kit for a technology with simulation
models that describe as detailed as possible the physical behavior of a
 transistor. Since no two factories, or
companies, have exactly the same manufacturing process, two transistors from different companies will
not match in their physical behavior. 

For digital circuits, we've abstracted away the transistor. We know we can
configure two PMOS and two NMOS into a 2-input NAND. When both inputs are high,
the output is low. Otherwise the output is high. Based on a NAND we can
construct any digital circuit. To transition to a new technology it's just one
cell we need to make. In practice we make a few more "standard cells", but
that's due to area, speed or power optimization.

Analog circuits play between high
and low. The currents, charges and voltages between nodes, and how they change,
create insanely complex interactions. To my knowledge, no-one has found a
general abstraction level for analog circuits.


## Circuit abstaction layers

Although there is no general abstraction level for transitors, we can reuse
ideas between technologies. A voltage reference may have almost same
schematic and the same interface. The sizes of transistors, resistors, bipolars
and capacitors, and their physical properites will be different, but idea will
remain the same.

I think it's pointless to find a general abstraction level, or API, for devices
(transistors, resistors, capacitors, inductors, bipolars, diodes). Those will
always be different, they are in the real world, and their properties are tied
to how the integrated circuit is manufactured. 

I think we should focus on reusing the idea. We should focus on "not repeating
ourself" by not reproducing the circuit idea. We must disentangle the analog
circuit idea from the technology. 

I've been working on the problem of "idea reuse" since 2009, and have made some
progress. Below is a status, and my current thoughts.

| Status | Abstraction | Design | Layout | Why |
|:------:|:------------|:-------|:-------|:----|
| :construction:       | Chip        | SystemVerilog     | digital     | Complex connections, few analog interfaces              |
| :construction:       | Module      | SystemVerilog     | digital     | Large amount of digital signals, few analog signals     |
| :warning:       | Block       | Schematic         | programatic | Large amount of critical analog interfaces, few digital |
| :white_check_mark:      | Cell         | Schematic/JSON | compiled    | Few analog interfaces, few digital interfaces                                 |
| :white_check_mark:      | Device      | JSON              | compiled    | Polygon pushing                                         |
| :white_check_mark:      | Technology  | JSON/Rules        | compiled    | Custom for each technology                              |

See  [My thoughts on
analog](https://analogicus.com/aic2023/2022/12/03/My-thoughts-on-analog-design.html)
for more information on Technology, Device and Cell

## Block 
An analog block, like a voltage regulator have large amount of critical analog
interfaces, and a few digitial signals

From books, papers, or rare instances a bright mind we get the "idea" for the
voltage reference. A schematic, like the one below, is how we store the idea.
Analog designers are trained schematic readers and most can immediately
understand how the circuit below works.

![](/ideas/assets/bias_ibp3.svg)

Making the idea work in a particular technology, however, is a fundamentally a
different task. With a quick glance an analog designer can see that the circuit
won't work in 55 nm for core transistors if the supply is low (1.0 V). 

The VD1
is always a diode voltage above ground (0.9 V ish at low temperature). $V(VBN,VD1)$ will at
least be a threshold votlage (0.5 V ish). $V(VBNC,VBN)$ will be at least 0.2 V,
and $V(AVDD_CV,VBNC)$ will be 0.2 V ish. Thus the minimum AVDD_CV of the circuit
is $ 0.9 + 0.5 + 0.2*2 = 1.8 V$. As a result, in 55 nm with core transistor the
"idea" is a "bad idea", but in the same technology with IO transistors (3.0 V)
it may be a "good idea" and could be worth a try.

To get an answer to wether the "idea" is actually good in a specific technology, the analog designer must
tweak all the width, lengths, multipliers, of each device and run through
hundreds of simulations. At the end, the schematic may look the same, but all
devices will have tweaked values for that technology. 

Porting to another technology will again require tweaking of all device values,
but the schematic may still look the same.

I think it would be a good idea to separate the "idea" from the technology
implementation. Each technology port is about 5 years from eachother. Thus it's
rare that the port is done by the same person as last time. That means, a new
person will try and copy the old idea, and maybe even redraw schematic again. 

Humans are error prone, and it's easy to draw a wire wrong, or insert a wrong
component, or forget a power down transistor. Had the "idea" been stored in a
technology independent format, there would be less chance of introducing human
errors. 

What I'd love to do is to use Xschem schematics to store the "idea", then store
the technology specific items, like exact transistor types, widths, lengths,
layout information, in another format (YAML or python). Then pass the "idea"
schematic and technology layer through a compiler to generate a technology
specific schematic. 

Exactly how to do that, I'm not sure yet, but I think that's my next step.

## Module and Chip
Further up in the integrated circuit hierarchy there are less analog signals,
and more digital signals. At some point schematics no longer convey useful
information, there is simply too much information. It's a jumble of wires
between blocks, and there immediate understanding of what the circuit does is
lost. 

For complex schematics I think that schematics is the wrong approach. A
hardware description language like SystemVerilog is much better. 








