---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
# title: "PACStack: an Authenticated Call Stack"
---


> A popular run-time attack technique is to compromise the control-flow integrity of a program by modifying function return addresses on the stack. So far, shadow stacks have proven to be essential for _comprehensively preventing_ return address manipulation. Shadow stacks record return addresses in integrity-protected memory secured with hardware-assistance or software access control. Software shadow stacks incur high overheads or trade off security for efficiency. Hardware-assisted shadow stacks are efficient and secure, but require the deployment of special-purpose hardware.
>
> We present _authenticated call stack_ (ACS), an approach that uses chained message authentication codes (MACs). Our prototype, PACStack, uses the ARM general purpose hardware mechanism for pointer authentication (PA) to implement ACS. Via a rigorous security analysis, we show that PACStack achieves security comparable to hardware-assisted shadow stacks _without requiring dedicated hardware_. We demonstrate that PACStack's performance overhead is small (~3%).

**PACStack: an Authenticated Call Stack** (2019)  
*Hans Liljestrand*,
*Thomas Nyman*,
*Lachlan J. Gunn*,
*Jan-Erik Ekberg*,
*N. Asokan*  
[arXiv:1905.10242 \[cs.CR\]](https://arxiv.org/abs/1905.10242)

[Poster](/assets/pdfs/PACStack.DAC56-LBR-poster.pdf) **Late Breaking Results: Authenticated Call Stack** (2019)  
*Hans Liljestrand*,
*Thomas Nyman*,
*Jan-Erik Ekberg*,
*N. Asokan*  
ACM DAC '19,  [DOI:10.1145/3316781.3322469](http://doi.acm.org/10.1145/3316781.3322469)

## Source Code

Source code for the LLVM PACstack implementation is available at
[github.com/pacstack/pacstack-llvm](https://github.com/pacstack/pacstack-llvm).

## About

This work is part of the [hardware-assisted runtime
protection](https://ssg.aalto.fi/research/projects/harp) efforts of the [Secure
Systems Group](https://ssg.aalto.fi) at the [Aalto
University](https://www.aalto.fi) in Espoo, Finland.
