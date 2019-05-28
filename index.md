---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
# title: "PACStack: an Authenticated Call Stack"
---

> A popular run-time attack technique is to compromise the control-flow integrity of a program by modifying function return addresses on the stack. So far, shadow stacks have proven to be essential for comprehensively preventing return address manipulation. Shadow stacks record return addresses in integrity-protected memory, secured with hardware-assistance or software access control. Software shadow stacks incur high overheads or trade off security for efficiency. Hardware-assisted shadow stacks are efficient and secure, but require the deployment of special-purpose hardware.
>
> We present authenticated call stack (ACS), an approach that uses chained message authentication codes (MACs) to achieve comparable security without requiring additional hardware support. We present PACStack, a realization of ACS on the ARMv8.3-A architecture, using its general purpose hardware mechanism for pointer authentication (PA). Via a rigorous security analysis, we show that PACStack achieves security comparable to hardware-assisted shadow stacks without requiring dedicated hardware. We demonstrate that PACStack's performance overhead is negligible (<1%). 

**PACStack: an Authenticated Call Stack**
*Hans Liljestrand*,
*Thomas Nyman*,
*Lachlan Gunn*,
*Jan-Erik Ekberg*,
*N. Asokan*
[arXiv:1905.10242 \[cs.CR\]](https://arxiv.org/abs/1905.10242)

## Source Code

To be made available on publication.

## About

This work is part of the [hardware-assisted runtime
protection](https://ssg.aalto.fi/research/projects/harp) efforts of the [Secure
Systems Group](https://ssg.aalto.fi) at the [Aalto
University](https://www.aalto.fi) in Espoo, Finland.
