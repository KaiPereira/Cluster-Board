---
title: Cluster Board
author: Kai Pereira
description: An open source 8 node ITX cluster board running custom compute modules, supports up to 256 GB of ram
created_at: 2025-08-14
---
## Day 1 - Research Galore - 6 Hours

Recently, I came across a video from one of my favorite content creators, [Bitluni](https://www.youtube.com/@bitluni) about building a Risc-V supercluster. This was one of the coolest concepts I've ever seen in my life, combining the power of 100's of micro controllers to create a decently powerful system.

But then I thought to myself, what if we scaled this up, what if we went BIG.

I started doing some research on cluster boards and how they actually work. I noticed that Bitluni's board was very similar to my Cheetah MX4, 3DP motherboard, so knowing that I kind of had some previous knowledge to what I was looking into, really helped alot!

I figured out that Bitluni basically had this carrier board, and then on that carrier board, it had these connectors called PCIe which allowed fast data transfer but also to basically plug in cards to your board like a PC.

This was such a cool concept, creating a cluster of computers that acted as a swarm, from basically many mini computers, I had to make one too.

You already know how I function though, I needed to go a bit bigger than Bitluni so I started researching about carrier boards, specifically CM4 carrier boards. Basically, rasp pi makes these things called "pi compute modules" (CMx) which can basically plug into these types of motherboards using PCIe or other connectors to turn their plain PCB, into a devboard of sorts with extra features on the carrier board.

Jeff Geerling is a really interesting guy I came across who loves messing around with carrier boards like this, I'd highly suggest checking out his stuff and learning how it all works https://www.jeffgeerling.com/

Anyways, I kind of know exactly what I want to make now, a carrier board with custom compute modules for maximum power, it takes a bit of time to wrap your head around this concept of PCB's on PCB's but it's really cool. I also kind of want to go overkill, I never like making stuff that the ordinary person would make, so something that sounds impressive, like 100's of gb's of ram, or 100's of gpu's, just the wow factor.

I was on a plane basically doing all this research, so it gave me a solid 6 hours of time on research what exactly a carrier board is and how everything works, but I've only just scratched the surface.

## Day 2 - Linux, linux and linux

Before I wanted to dive more into this project, I wanted to understand some of the software behind what I'm actually doing. I came across this insanely cool resource called https://www.thirtythreeforty.net/series/mastering-embedded-linux/ which teaches Linux embedded systems from a very high level standpoint.

The reason I wanted to look into this, is to understand what goes into making a compute module. You see, you need the RAM, storage, boot ROM, SoC and peripherals to make the board, like USB, UART, SPI, ADC, etc. All the basically combined to form a compute module.

On the software side, it looks something like the bootloader -> linux -> the ssh server, web server, interface, programs anything the user can interact with or run on. So you basically boot your device into linux, and then you can interact with the miniature filesystem that you have access to, it's like a mini computer.

Now you're probably wondering, what's an SoC... SoC stands for System on Chip, it's basically a complete system squeezed onto a tiny piece of silicon. On the SoC, you'll find:
- The CPU
- Sometimes accelerators like an NPU, GPU, DSP, etc.
- I/O controllers
- Peripherals
- Sometimes other things like bluetooth/wifi, security, an SoC can have *anything*

But anyways, let's go over some of the core parts...

The CPU is the brain of the SoC, I could talk for years about how the CPU works, but it basically executes the instructions of your programs.

The accelerators are specialized parts of the chips that are really good at specific things like training AI, or math, for example, a GPU is really good at math and AI, a DSP is really good at audio acceleration, there's so many different types of accelerators.

The I/O controllers handle like the different communication protocols like USB, ethernet, PCIe, etc. Just internal data flow in general, transferring information to and from inside the SoC.

The peripherals actually do something, produce sound, display stuff, control motors, etc. They're GPIO's, ADC's, DAC's, PWM controllers, everything that controls stuff.

And then sometimes chips have some other cool things like wifi/bluetooth, these are just other little "blocks" of hardware inside of the SoC that does stuff.

We've only talked about INSIDE of the SoC thought, what about outside of it?

Let's talk about the SoM, the System on Module.... This is basically those compute modules I was talking about, those are SoM's. When you add RAM to the outside of your SoC, that's part of the SoM, so what are all the things you need on the SoM.

The SoM will typically have RAM, the SoC, storage, power management, clocking, connectors, everything that you would find on a pi compute module persay. It turns the little SoC complete system, into a full computer ready to run programs, operating systems, anything your heart desires.

Now lots of people are probably wondering what RAM actually is.. It's basically like storage for the data and instructions that the SoC is actively using, so more RAM is more computational power!

The storage is long term memory (while RAM is short term), it'll hold the operating system, programs, data, everything. Sometimes there's a thing called the bootloader, which is a small program that runs when the SoM turns on, it's sometimes stored in the storage or sometimes in the SoC.

Power management is another things that's really important. The cluster board get's a primary voltage of like 12 - 48V, and you need to regulate it down for the SoC, RAM, peripherals, etc. All the different parts have different voltage requirements so you have to properly regulate the voltage and distribute it to the components on the compute module. You'll also usually want monitoring or power protection, just to make sure you don't actually ruin a component on the board.

And finally, there's all those extra parts on the board like the clock, connectors, etc. The clock is usually pretty important because it makes sure that everything is working in unison together. Think of it like an orchestra, it would be the conductor, it's very important to have a crystal for things like external connectors where data needs to leave the SoC at proper intervals, and generally just the SoC to make sure everything's working right. 

And then there's the connectors, depending on what you make, you'll need more or less, I'm making compute modules so I don't actually need like USB or ethernet ports or anything on the actual card, I just need PCIe lanes where all the information from the motherboard will travel to the cards, think of it like a really big connectors that everything will travel through. This makes it very modular, and very fast, because it goes directly to the motherboard. You can also put like debugging headers on the compute module to make sure that each card is working properly, I'll decide on this later.

