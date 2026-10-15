---
title: "uranus-analog-front-end"
github: "https://github.com/silllies/uranus-analog-front-end"
description: "An analog front end prepares an analog signal for digitization and digitizes it. Preparation includes line impedance selection, attenuation, amplification, low-pass filtering, and more (that has to be determined). My greatest fear is signal degradation."
created_at: "2026-10-14"
total_time: "5h"
---

# October 15, 2026: So it begins

**What is an analog front end?**

An analog front end (AFE) is a device that prepares an analog signal for digitization and digitizes it. Preparing the signal for digitization is necessary because of the limits of the analog to digital converter (ADC). For example, an ADC might have a limit of 200 MSPS. To prepare a signal going into the ADC, frequencies above 100 MHz (theoretically. In practice it is better to go a bit lower to capture more of the signal. 100 MHz is the absolute max because that would mean the ADC would be able to sample the frequency at its peak and valley). Other examples of preparation include attenuating the signal to a safe voltage for the components, amplifying very low voltage signals to use the ADC's full range, selecting a line impedance (50 Ohm or 1 MOhm) to reduce reflections or circuit load, and coupling mode (DC coupling routes the entire signal to the ADC, AC coupling removed the DC component).

**Why make an AFE?**

I think it would be very cool to be able to do stuff with analog signals. I also think the engineering involved with preserving signal integrity, especially at GHz level frequencies, to be very interesting. Sadly, some stuff to do that (like a Rogers board) is very expensive, so I will try my very best to make this tier 1 to do everything I can.

![example AFE](https://github.com/silllies/uranus-analog-front-end/blob/main/images/exampleAFE.jpg)

I thought this one was an interesting example as it includes an RF shield (I intend to include one, too), but it is definitely larping. Those holes in the shield decrease shielding effectiveness, so it isn't doing as much as it could be.  
An RF shield is a wall or box made of metal that blocks radio frequency waves. An example of one of these you are probably familiar is the mesh on the door of a microwave (yes this has holes, but it reduces the waves making it through to a safe margin). For my AFE, a wall is not enough, as it needs to block waves from all directions. A simple box placed over the components will do some, but there is still the underside. A ground plane on the bottom is also much better, but there is still a small gap between that ground plane and the start of the RF shield box. That is handled with a wall of vias that connect to the ground plane and RF shield, spaced no further than 1/20th of the highest frequency you are trying to block's wavelength. Yes, these are holes, too. However, I can greatly reduce the number of holes if I use a plated slot, which I believe I can do at JLCPCB, for my purposes, completely free! Now, you might be wondering "How are you supposed to let signals in and out of the RF shield if there is a wall surrounding it?" Well, you route them in between the vias, or, if you are using a plated slot, compromise and add a via wall where your signals exit/enter.

![A simple drawing of what an ADC does](https://github.com/silllies/uranus-analog-front-end/blob/main/images/ADCdrawing.png)

**The ADC**

If I can get to tier 1, I will probably use the Zynalog ZGAD250D14, which is a 2 channel ADC that takes 250 MSPS at 14 bits. However, its max input frequency is 1.6 GHz, which means I can do something called equivalent time sampling (ETS). Basically, it samples across its usable frequencies (0-125 MHz) for a set amount of time. After that set amount of time, the processor waits a very tiny amount of time, then the ADC starts sampling again. Whatever is processing the data can then reconstruct a signal at a maximum frequency far higher than the actual sampling rate. The Zynalog sends data through LVDS (Low Voltage Differential Signaling). This means that every signal line has a differential pair that travels closely alongside it. This requires very precise routing to maintain 100 Ohms of differential impedance, match lengths, and minimize vias. Because of the impedance requirements, it would be really nice to use a rogers (or other impedance controlled) board, but they are too gatekept.

I intend the processor that processes this data to be an FPGA, and I will have it communicate over an FPGA Mezzanine Connector (FMC). This is a very common connector with pin standards, which allows my AFE to plug into any FPGA board with this connector.

**Total time spent: 5h**