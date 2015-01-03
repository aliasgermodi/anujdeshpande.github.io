Piano Stair at Phoenix Mall, Pune
=========

[YouTube Video](https://www.youtube.com/watch?v=vMV_lfypvD4)

The last 2 weeks have been fun at Revealing Hour Creations. We finished testing Tah boards on our homebrewed testbed and are in the process of ordering the final batch of PCBs. Since that whole process will take a few weeks at our supplier, we got busy with some other interesting things locally.  
Piano stairs are fun. Not many people online have tried it, but by no means is it a brand new concept. We decided to implement these using Tah at a nearby mall, called Phoenix Market City.  
Phoenix market city is probably the biggest mall in Pune when it comes to square footage, number of shops as well as the daily number of people visiting. It has multiple escalators and elevators as well as plain old stairs in 2-3 places. We decided to install our hardware on a staircase right in front of the north entrance of the mall, which is connected to the parking. This was a no brainer to drive traffic our way, also it was one of the few places where there are staircases and not escalators.  
The staircase was divided into three sets of 12 stairs each. There is an escalator right next to the staircase. Luckily there was a security desk at the top and a security guard at the bottom of the stairs.  

### Black & White or Just White
There were 2 ways to do this,
* Detect only footsteps on the white piano keys
* Detect footsteps on the white as well as black keys

We decided to initially only do the white keys as a poc, and then do the black keys if it all worked out. Based on the number of stairs that we had and the standard piano layout, we would need 8 black keys per 12 white keys. That would mean a total of 20 keys per set of 12 stairs.  
Eventually we decided to go with method 1 and detect only the white keys. More on that below.

### Detecting footsteps
There are half a dozen different methods which can be used to detect footsteps. Let's divide them into 2 categories,

* Transmitter/receiver based
  ** Laser and LDR
  ** IR leds

* Single piece of sensor
	** Pressure sensor
	** Piezo sensor
	** Capacitive touch sensor

##### Piezo sensors
We sstarted out with a simple piezo sensor connected to an Arduino and found out that we need some amplification. We followed the instructions given on this particular blog and found somewhat better results, but deploying using this method would have meant a lot of time and effort invested in the actual placement of sensors. Plus, too much wiring for 36 stairs.
##### IR leds
The next thing that we tried out was an IR led pair. There were plenty of issues with this method. For one, we would need to have the rx/tx pointing straight at each other, and if someone accidently kicked them on the stairs, it would stop working. Another issue was the amount of wires that would have been required.
##### Force sensors
They are great, but only if you have a couple of stairs to do. It's uneconomical to deploy 36 stairs using this
##### Capacitive touch sensors
This is the method that we finally decided to go with. MPR121 and CAP1188 are the two most commonly used ICs for this. Makey Makey, Bareconductive board, Sparkfun's series of touch sensors are just some of the examples of places where these are already used. Again, the MPR121 is the more commonly used one. It works off i2c and has 4 addresses available. It has 12 channels which was perfect for our usecase, as we could do one set of stairs using one MPR121. The CAP1188 has spi as well as i2c onboard, but has only 8 channels compared to MPRs 12.  
Procuring them in India was a pain. There were plenty of people who had them in stock but few had it in good numbers. Eventually we managed to get it from multiple resellers. We desperately need someone as good as sparkfun or adafruit here :) Capacitive touch sensing worked great. All we had to take care of was the ground, and that wasn't a big concern. There was plenty of metallic surfaces around. Plus this method required the minimum amount of wiring.

### Playing the notes
1. Use the audio shield
   We werent so sure about the audio quality here. So we didn't go for this.
2. Using Raspberry Pi
   In this method, we had a few different alternatives
   1. Using the Tah as a midi input
	  To achieve this, we used something known as ttymidi, which listened for a midi device on the serial port.
   2. Serial input
	  Read the footsteps serially, and play the right tone using pygame.midi. This worked quite well to be honest, but we had 2 separate issues with this.
		1. The first issue was with sustaining the notes.
		There was a lot of unnecessary sustaining of notes sometimes. We found out that this was because of the way we were running our note.on and note.off loops. We fixed that, but then had the next issue.
		2. Long queues aka not real time
		The processing power was enough. What was not enough was the length for which the sound notes were played. We would need threads here but we were already using up quite a lot of processing power, and creating so many threads probably would not have been a good idea. I would have loved to explore this option more, but we were short on time. This had to be implemented for the Christmas weekend, and we needed something working asap.
	3. Using vmpk, vkeybd
	   Again, this method worked, but it would mean that we should have an X session running on the pi. This was defeating the purpose of using a Pi to be honest, but this was still something that we could live with.

	Eventually we decided to ditch the Pi altogether, because the sound quality was not upto the mark. We had to amplify the sound using an external amp so that it would be heard loud enough in a mall. And the pi simply doesn't have a good enough DAC to do this.
3. Using a laptop
   This gave the most promising results. But someone who have to sacrifice their laptop for a whole week. And that's something that you simply can't ask a geek for :D. Again, the operating system in question gave us different results for the same software, vmpk. Mac showed the best results, followed by Windows and Linux coming in at a distant third. Different Linux machines showed different results. Also, the method of installation mattered a lot with Linux. Installing with the package manager meant that you had to take care of the syth that you used (amsynth, qsynth, etc) whereas if you downloaded the source and binary from sourceforge.
   
### Communication between Tah and the laptop(s)
1. Using bluetooth
This option was discarded because it wouldnt be as real time as the next option, plus we would have to write a desktop application to get data from 2 or more Tah boards and generate notes.
2. Tah as a USB HID keyboard
This was the most real time option of all, plus required no desktop application at all. All we had to do was have VMPK start up on boot, and have the right sendkeys on Tah. The tradeoff was that we are using USB wires stretched over a few meters, which is not how USB was meant to be used. Again, we can have a repeater circuit in the middle, but we instead decided to go with 2 machines. This was mostly because we needed this done on the very next day and we had less than a few hours remaining.  

The eventual architecture that was deployed was

1 MacBook Pro
	-> 1 Tah -> 1 MPR 121
	-> 1 Tah -> 1 MPR 121
1 Windows PC
	-> 1 Tah -> 1 MPR 121

Not the most optimal setup, but it worked.  

On the software end, we had VMPK running on both the machines and that's about it. You can find a link to the python code that would have gone on the Pi as well as the Tah Arduino sketches [here](https://github.com/RevealingHourCreations/piano-stairs)

All Credits to the team at [RHC](http://rhc.io)
