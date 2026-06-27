# The Journey - CIPHER

*This is not the technical documentation. The README covers that.*
*This is everything else.*

---

## Where It Started

I was scrolling YouTube, not looking for anything in particular. A video was suggested - someone had built a cyberdeck, some kind of custom handheld computer. I do not even remember whose it was. I watched it and a thought formed immediately.

*I want to build something like this.*

One thought led to another. A friend had a dead ASUS TUF A15 sitting in his closet - motherboard gone, out of warranty, beyond economic repair. He had moved on to a MacBook. The laptop was doing nothing.

*What if I took it apart and used the parts?*

The idea of a portable gaming device had been sitting somewhere in the back of my mind for a while. Sitting at a desk to work all day and then sitting at the same desk to play games felt like a problem worth solving. The Steam Deck was the obvious commercial answer - not first-party available in India, no local service, expensive to import. A Raspberry Pi with salvaged components was a different route entirely. Cheaper, smaller, GPIO-programmable, and something I could build exactly the way I wanted it.

By the time I put the phone down that night, the direction was clear. A handheld gaming device. Custom-built. Using what was already available.

That was enough to start.

---

## The Salvage

I had never properly disassembled a laptop before. I set up the study table, got a screwdriver set, and started working through the TUF A15 methodically - searching for part-specific replacement videos when I was unsure how something came apart, so I could see the correct technique before touching it.

<div align="center">

| | |
|:---:|:---:|
| <img width="320" alt="Friend Laptop disassembly table setup" src="https://github.com/user-attachments/assets/5ea07d70-c218-4de2-b5c7-1659d33b6f5a" /> | <img width="320" alt="Friend Laptop opened and lying on table" src="https://github.com/user-attachments/assets/e6d0e6a2-cbcb-43e0-b55a-f88b366caa67" /> |
| Table setup | Opened and ready |
| <img width="320" alt="Firend Laptop disassembly half done" src="https://github.com/user-attachments/assets/f43e7faf-c1a5-4e19-89e1-a085fc6d2966" /> | <img width="320" alt="Firend Laptop disassembly all salvaged components layed out" src="https://github.com/user-attachments/assets/f4101f41-b0ef-4a93-8ed5-635f4c39eb9e" /> |
| Halfway through | Everything salvaged |

</div>

What came out: two laptop speakers, two cooling fans, a trackpad, a keyboard with RGB, a 15-inch 1080p 144hz display panel, a USB 3.0 female connector, a laptop battery, and a strip of five LEDs on a 10-pin FFC cable. The RAM went back to my friend - he could use or sell it.

I cleaned each component with a brush and laid everything on the bed behind me. The laptop that had been rotting in a closet was now a set of parts with potential.

The speakers would go in. The fans would go in. The LEDs I did not know about yet - that would come later, and in a way I did not expect.

---

## Finding the Heart

A SBC made more sense than sourcing a replacement motherboard - smaller, cheaper, GPIO-programmable, and something I had a history with. My first Raspberry Pi was a 3B. I had fried it. Powered it wrong. That was a lesson I did not forget.

This time I chose the Pi 5 4GB. Not the 4B - because the Pi 5 has a dedicated NVMe slot, and gaming needs fast storage. Not 8GB - because Batocera v42 is the plan, and for retro gaming the GPU is the bottleneck long before RAM is.

Then came the problem of actually buying one.

The Pi 5 was in shortage. Every website I knew - Robu, Robokits, Flyrobo - had it out of stock. I started to feel a specific kind of disheartening that makers know well: the project that cannot even begin because the one component that makes it possible is nowhere to be found. The heart of the thing, absent.

Then I found [CrazyPi](https://www.crazypi.com/). Every variant of the Pi, in stock, at reasonable prices. My immediate reaction was suspicion. If the major Indian electronics retailers don't have it, why does this random website I've never heard of?

I searched for reviews. I found enough to be cautiously optimistic. Then I did something that felt slightly unusual - I contacted their support on WhatsApp, asked specific questions about authenticity, got fast and clear answers. Only when I was satisfied did I place the order. Pi 5 4GB. Pi 4 4GB as a backup. Geekworm X1001 NVMe HAT. Consistent 512GB M.2 SSD (SSD on Amazon).

The order went through. The project could begin.

---

## A Month of Research

What I had not accounted for was how much I did not know.

I had never built a complete electronics stack from scratch. Audio, video, power, controls - every subsystem was new territory. It took close to a month just researching components. Finding what existed, what was compatible, what was in stock, what the datasheets actually said versus what the product listing claimed.

I discovered around ten to fifteen Indian electronics suppliers I had not known about before. I was comparing prices across tabs, asking Claude to verify component compatibility from datasheets, checking if model numbers matched exactly. For someone who had previously ordered components from three or four websites, this was an education in itself.

While the orders were arriving in batches, I started designing the wiring diagrams. I had no prior experience with circuit diagram tools. I chose Canva Whiteboard - I know Canva well, and speed of iteration mattered more than convention. Power module first, then audio. I knew that if I got those two right, everything else would be manageable.

I also bought a soldering iron. A multimeter. A set of proper tools. The build had not started, and already I was learning things I had not expected to learn.

---

## The First Win

The power stack was the first thing I built.

Three Panasonic NCR18650GA cells. Three individual 1S BMS boards - I could not find a 3S BMS anywhere, so I gave each cell its own protection. The cells paralleled at a junction, wired to the QC4.0 power bank module with a rocker switch on the B+ line.

I had never soldered at this level before. I watched tutorials. I learned what good solder joints actually look like. I did the work.

<div align="center">

| | |
|:---:|:---:|
| <img width="320" alt="Battery wiring setup 1" src="https://github.com/user-attachments/assets/57e2c51f-5939-44e7-a1d1-f6329b37813e" /> | <img width="320" alt="Battery wiring setup 2" src="https://github.com/user-attachments/assets/79368597-5c23-4368-80bf-8f8e7ac7036b" /> |
| Power stack assembled | Power stack - alternate view |

</div>

First test: plugged a phone into the USB-A port. Charging. First test passed.

Second test: USB-C PD port. Worked. PD negotiation confirmed.

Third test - the one I had been both waiting for and nervous about. I plugged in the Pi 5, remembering what happened to the 3B. Powered on. No undervoltage warning. The Pi booted clean.

I showed my mother. "I've made a power bank," I told her. That was technically accurate - it was a power bank, just inside out.

There was one problem. The module auto-shutoff when PD negotiation was too slow. The fix was bridging the S1 button on the PCB underside so the module stayed permanently on. It was not a clean solder job - I was still new to it - but it worked. Half worked, precisely: the USB-C PD port stayed live, the USB-A ports still needed a replug to wake up. That was a problem for later.

That first win mattered more than the technical detail of it. When you are building something this large and this unknown, the beginning is all uncertainty. You are not sure the goal is reachable. You are not sure you can actually do it. The power stack working gave me something to stand on. *Okay. I can do this. This is real.*

---

## Audio: The Day I Didn't Eat

The audio stack broke me, then worked, then broke me again in a different way.

<div align="center">
  <img width="700" alt="CIPHER - Audio wiring" src="https://github.com/user-attachments/assets/1dd5fdb8-af51-4322-a379-c28468d9e187" />
  <br/>
  <em>Audio module wiring diagram</em>
</div>

I wired the PCM5102A DAC to the Pi 5's I2S GPIO pins, powered it correctly, and plugged in my earphones. Nothing. No sound. The DAC powered on - its red indicator lit - but audio output was silence. I had moved too fast on the power stack; this was the correction.

I spent the whole day on it. I tried config changes in Batocera. I tried a second DAC board - same result. I could hear a faint hum if I listened carefully, something rhythmic going up and down, like a signal trying to exist but not quite making it.

Then I left Claude and went to the old method - searching StackExchange, Reddit, forum threads. I found a post on raspberrypi.stackexchange.com. A different Pi, a different person, but the same DAC board. The solution was shown for Pi Zero but still I read through it in hope of finding something useful, and waht I found was the answer. The answer was in the PCB pads at the bottom of the board - specific solder bridges that needed to be set: SCK on the front, H1L/H2L/H4L to middle-right, and critically H3L to middle-left to unmute the board.

The pads are tiny. This was the most precise soldering I had done. I stopped breathing for those seconds. Did it. Plugged in the earphones.

Clean, crisp audio.

My parents had been calling me for dinner. I had not eaten anything that whole day. I left the setup running on the table and went to eat. Came back. Played an hour of gaming on battery power alone. The power stack held. The audio held. Two modules, running together, stable.

I went to sleep with a good mind that night.

The next day - AMP, speakers, and the switching jack. Signal switching through the 5-pin PJ-359B jack so audio routes to speakers by default and cuts to earphones on plug-in without any software involvement. The salvaged TUF A15 speakers wired up. A quick trick to find speaker polarity - a cell and listening for whether the drum went in or out.

The speakers worked. When earphones went in, speakers went silent. The audio stack was functional.

<div align="center">
  <img width="400" alt="Gif Audio test" src="https://github.com/user-attachments/assets/a22ecfde-bb0a-4ca2-a06a-ac9a47582504" />
  <br/>
  <em>Full audio stack in operation</em>
</div>

There were still problems - AMP power through USB-A auto-shutting off, a pop and cutout on loud transients, TRRS-TRS mismatch with the earphone jack. Each had a fix. The AMP power solution required soldering directly to the C15 capacitor on the power module - a cap so small that a millimetre of error would bridge two components and cause an explosion. I did it carefully. It worked.

The 1000µF capacitor on the AMP input fixed the transient cutout. Swapping to a 7-pin jack fixed the TRRS issue.

The audio module was done.

---

## The Display Problem

<div align="center">
  <img width="400" alt="Gif 7 inch display not working" src="https://github.com/user-attachments/assets/f7ed074d-71d1-447c-ab3c-719dcf423911" />
  <br/>
  <em>The red LED error pattern - display detecting a problem it would not explain</em>
</div>

The first display was a mistake. A Waveshare 5-inch DSI panel - thin, elegant, looked right for a handheld. It arrived. I spent a full day trying to configure it. It was not going to work: Batocera v42 has no DSI support for the Pi 5. The listing said plug-and-play. That was true for Pi 4 and earlier. Not for this.

I moved to the 7-inch HDMI model instead. It worked immediately. 1024×600, touch carried over a single USB cable. I was playing God of War: Ghost of Sparta on it that same day.

<div align="center">

| | |
|:---:|:---:|
| <img width="320" alt="Testing 7 inch display with Pi 1" src="https://github.com/user-attachments/assets/b6941e74-4017-42fa-861b-993f075ff1cc" /> | <img width="320" alt="Testing 7 inch display with Pi 2" src="https://github.com/user-attachments/assets/3c5b3a61-08ad-4848-b256-d2154d4f062f" /> |
| First game on the display | Touch working on a single cable |

</div>

Then I broke it.

I was testing whether I could use the display's built-in audio output instead of the full DAC-AMP stack - fewer wires, cleaner routing. I plugged earphones into the display's 3.5mm jack. It worked for about a minute. Then a pop. Then nothing. The display stopped outputting video entirely. The green and red indicator LEDs on the back showed a pattern - regular, deliberate, the kind of pattern that means something is wrong and the board knows exactly what.

I spent the rest of that day debugging. Every configuration fix I could find. Every setting. Checking if the Pi was even detecting the display. The display worked with a laptop - so the panel itself was not dead. With the Pi: nothing.

That specific frustration is its own category. A working thing that has stopped working, with no clear error message, no obvious cause, and hours of attempted fixes that go nowhere. I had not eaten. The day was gone.

I finally contacted the retailer. I had built a solid relationship with them over the course of the build - regular questions, component sourcing, compatibility checks. I explained the situation. They asked me to send the display for inspection.

When the replacement arrived, it had the same problem with the Pi. Same behaviour. I almost contacted them again - then thought to try powering the display from an external source rather than the Pi's USB-A port.

It worked.

The Pi's USB-A ports could not supply enough current to drive the display reliably. The solution: leech power directly from the C15/C16 capacitor tap on the power module. Three cables now - FFC HDMI, USB touch, and the power line. Not ideal. But it works, and it is stable.

---

## Controls: Two Roads Not Taken

The original plan was to salvage the cheap Enter gamepad I had been using for testing - take its internals and reuse the concept in the final build. One session with it convinced me otherwise. Button ghosting. Clunky feel. Not good enough for something this involved.

<div align="center">

| | |
|:---:|:---:|
| <img width="320" alt="Cheap gamepad opened - case attached" src="https://github.com/user-attachments/assets/d8710b85-3fa8-44fe-96aa-47159e7b4bba" /> | <img width="320" alt="Cheap gamepad opened only inside part" src="https://github.com/user-attachments/assets/5093b66c-8912-4a93-a38a-50c209350cfd" /> |
| Gamepad opened | Internals evaluated |

</div>

The second plan was a PS4 JDM-055 ribbon cable and silicone conductive pads, with an ESP32 as the controller brain. Better feel than the Enter gamepad. I went looking for a damaged PS4 controller locally, sourced the ribbon.

<div align="center">
  <img width="700" alt="PS4 Dualshock Ribbon strip" src="https://github.com/user-attachments/assets/8167096b-552a-471a-b7d2-af53a22a2970" />
  <br/>
  <em>PS4 JDM-055 ribbon - the plan before the plan changed</em>
</div>

Then the reality of flexible PCBs settled in. You cannot solder a flex PCB without a hotplate. Repairs and modifications become nearly impossible once it is inside a sealed enclosure. If one trace goes, the entire control surface goes.

Not worth the risk. Not in something this complex, this long in the making.

Cherry MX Low Profile Red switches instead. Through-hole. Individually replaceable. Excellent feel. Smaller than full-size mechanicals, shorter travel, exactly right for a controller context. Sixteen switches for the full button layout. Hall effect joysticks for the analog sticks. An ESP32-S3 running TinyUSB HID presenting as a standard USB gamepad.

The switches arrived. The joystick modules arrived. The RP2040, RP2350, and FT232 UART board arrived - the cybersecurity tooling I had ordered after the Kali dual-boot idea arrived and changed what this device was supposed to be.

And then I stopped.

---

## The Gradual Pause

It was not a sudden halt. Life rarely works that cleanly.

Before the controls could be wired, I needed to learn CAD - properly, not just theory. The enclosure for CIPHER cannot be designed without real Fusion 360 experience. So I did what I always do: built something first. A separate project, deliberately smaller and simpler, to learn on before applying the knowledge somewhere it really mattered.

<div align="center">
  <img width="700" alt="Countdown Timer Hero Shot" src="https://github.com/user-attachments/assets/e96e554a-d238-4fd0-9683-bef82af7542d" />
</div>
<div align="center">
  Countdown Timer V1
</div> <br>

That became Countdown Timer V1 - an ESP32-based countdown timer with a snap-fit 3D printed case, built in 7 days from zero CAD experience. The prototype cases are sitting in a box on my desk. Every failed print from every wrong iteration. All the lessons in solid plastic form. If you want to see what learning CAD by building actually looks like, that project has its own repository: [Countdown Timer V1](https://github.com/Work-KewalShah/Countdown-Timer-V1). It's even open sourced, and you can build it easily in half a day's work.

After Countdown Timer was done, exam preparation began. Network+ first - 814/900 on the first attempt. Then Security+, currently ongoing.

CIPHER is on the desk. The components are there. The half-finished controller harness, the modules all wired and running, the fans and LED strip and audio and display all doing exactly what they are supposed to do. It is not abandoned. It is waiting.

I think when I return to it, it will not be a sudden rush of excitement. It will be more like easing back into something familiar - the same way you pick up a book you set down for a while and find the rhythm again within a page or two. The process will feel natural again. The enclosure work will begin.

---

## The LED Night

There was a second bad night in this build, and I want to record it honestly.

The LED indicator module had five LEDs. Four were working exactly as designed. The fifth was supposed to show audio status - solid when sound was on, off when muted.

The logic was straightforward. The script worked when I ran it after Batocera had fully booted. But when the script started on boot via `custom.sh`, the audio device was not being detected - the LED showed the error code for "audio device missing" even though audio was working fine and I could hear it.

I started at debugging this issue at 11pm.

I tried delaying the script. I tried moving the volume script to the very beginning of the boot sequence. I tried launching the LED service only after EmulationStation had initialised. I tried checking whether the GPIO pin was already claimed by another process. I searched for the same issue in forums and found nothing directly applicable.

My Claude tokens expired sometime around 1am.

By 3am I had tried everything I could think of. Nothing worked. The audio LED would not detect the audio device correctly at boot. It worked every other way. Just not the one way that mattered.

At some point around 3am, I made a decision. Not a frustrated one - more like a clear-headed one that comes after enough hours of honest effort. I scrapped the audio indication entirely and replaced it with temperature monitoring instead. Solid below 60°C, slow blink between 60 and 72°C, fast blink above that, rapid blink above 82°C.

It is more practical. It does not depend on audio driver timing. It gives real useful information at a glance. It was the right decision.

But I want to record that night honestly because it was real. Not every problem has a solution you find. Sometimes you exhaust every avenue and the honest move is to build something better in its place.

---

## The Name

CIPHER arrived late.

For a long time the device did not have a name, I just called it handheld or Pi Deck. I was not thinking about names - I was thinking about power rails and audio switching and display connectors. Names felt premature.

When the Kali dual-boot idea came in, something shifted. The device was no longer just a gaming handheld with an SBC inside. It had a hidden layer. Something underneath the surface that most people looking at it would never know about. The gaming console was a cover. Intentionally.

I started thinking about the aesthetic - what the device should look like, what the boot splash should feel like. I landed on black and yellow. Not as a first choice - it arrived gradually, through iteration, through thinking about what the device was. Cyberpunk adjacent. High contrast. A colour language that said something without being loud about it.

<div align="center">
  <img width="700" alt="Full setup with display on monitor visible" src="https://github.com/user-attachments/assets/161a707d-4dd9-4985-9779-caeaf672e161" />
  <br/>
  <em>The setup that was becoming CIPHER - before it had the name</em>
</div>

And then the name came. Not through a structured search - through the natural collision of things already true.

Cypher. The agent I had mained in Valorant during it's initial early access days, even when Valo was not launched in India. Dominant in my lobbies. Information-based, patient, I was literally feared in lobies because of what how I played, extracted information that opponents did not now they could even reveal. I had not thought about that game in a long time, but the thread, that link to it was there.

Cipher - the cryptographic core of cybersecurity. What this device was built to work with.

Cipher - the hidden meaning underneath an apparent surface. Exactly what the device was.

Three layers, one word, all of them true simultaneously. The name stuck immediately. It has not changed since.

---

## The Streaming Discovery

<div align="center">
  <img width="700" alt="Game streaming test on Pi via Moonlight on Laptop" src="https://github.com/user-attachments/assets/0f81ff4b-6edd-4fa0-8fdc-86789b5519c5" />
  <br/>
  <em>Hollow Knight on CIPHER - streamed from the TUF A15 via Moonlight</em>
</div>

At some point during testing I had a thought that was not in the original plan.

Retro games on Batocera. That was always there. But the TUF A15 with an RTX 3050 was sitting on the same desk. What if the handheld could also stream PC games from it - like a Steam Deck, but built?

Moonlight is built into Batocera. Sunshine on the laptop side. Setup took an afternoon. A few configuration errors on the Sunshine side, some manual `.moonlight` files for games that were not auto-detected. Then it was working.

I played Hollow Knight, Dark Souls, Hades on the Pi handheld, streamed live from the laptop across local WiFi, controlled the character on my laptop via controller attached to Pi. No HDMI cable between them. No physical connection. The laptop doing the rendering, CIPHER doing the display and input.

It requires a good WiFi connection on both ends. When the connection is good, it is indistinguishable from local play. When it is not, there is lag that makes the whole thing unpleasant. But when it works, it works completely - and it was not something I had planned for at the start.

The best features of this build were not all planned.

---

## Where It Lives Now

<div align="center">
  <img width="700" alt="Complete final setup till now layed on table" src="https://github.com/user-attachments/assets/63e7f747-1c66-40bd-90ba-82397d338792" />
  <br/>
  <em>CIPHER as it stands - all modules wired, case not yet designed</em>
</div>

The build is roughly 70% complete.

Power, display, audio, volume buttons, cooling fans, LED indicators, game streaming - all done. The Batocera scripts run at boot, stable on v42. The LED strip runs through its POST sequence every time the Pi starts. The fans kick in above 65°C and settle below 58°C. The speakers play audio through a DAC I had to solder at the pad level to unmute. The display shows 1024×600 on a 7-inch panel driven by a custom modeline in config.txt.

What remains: the controller wiring and firmware, the Kali dual boot, and the enclosure. The hardest CAD work I will have done - more complex than the Countdown Timer case by a significant margin. Pockets for every component, joystick cutouts, cherry MX through-holes, port clearances, a form factor that has to feel right in the hands, not just look right on a desk.

Security+ is what comes first. Then CIPHER resumes.

I do not expect to sit down one day and suddenly feel the rush to get back to it. It will be more gradual than that. A component touched here. A measurement taken there. The Fusion 360 file opened and closed and opened again. The way most long builds actually go - not a single continuous sprint but a series of committed returns.

---

## What Finishing This Will Mean

I think about what it will feel like to hold the finished device sometimes.

Not just the technical completion - the case printed and fitted, the controller working, Batocera booting to the custom made splash screen, the hidden Kali environment accessible to anyone who knows the combination. Not just that.

The thing underneath the thing.

I started this project from a low point. Needing a win. Needing to prove something to myself that I could not articulate clearly at the time but felt acutely. The build has given me wins along the way - every working module, every solved problem, every night where I went to sleep with something running that was not running before. But the finished device is different. That is the original goal, the whole shape of it, completed.

There is also the other layer. This device will be a real cybersecurity tool - not a replica, not a toy, not a showcase prop. A functional field device that can run Kali, carry pentest tooling, do real work, and look exactly like a gaming console to anyone who does not know what they are looking at. That started as a late addition to the build. It has now become central to what the device means.

CIPHER is the place where the gaming part of me and the cybersecurity part of me exist in the same object. Those are not separate interests. They are the same instinct - understanding systems from the inside, finding what is underneath the surface, knowing more than what the interface shows.

The device is not done yet. But it exists. It runs. It is more real every time I return to it.

That counts for more than I expected it to when I first sat down at the table with a screwdriver and a dead laptop.

---

*Kewal Shah* <br/>
*Build ongoing* <br/>
*Security+ next. Then the enclosure. Then CIPHER complete.*
