# Rombi - An innovative mobile scarecrow using an old iRobot Roomba 
<br>
<p align="center">
 
![InHousePatrol](https://github.com/user-attachments/assets/44355cbc-a66c-48aa-9ca1-b6daf258144a)
</p>
</br>

## TL;DR:

Rombi is an innovative mobile scarecrow using an old iRobot 
Roomba 561, ESP32 Arduino board, and custom-made sensors to 
autonomously detect and repel birds (or other animals) from 
a balcony (or other spaces). 

It has been tested as an effective repel system, animal and 
environmentally friendly solution.

Developing this has been a true pleasure. I also improved my 
hardware and software skills, learned optical and RF sensors, 
used CAD for 3D printing / PCB editing and applied image 
processing techniques for the first time. 

Lastly, I didn’t forget my cybersecurity roots, so I embedded 
several hidden security protections and alerts in the esp32 server 
code to prevent hackers from remotely exploiting it.

## How it started ?
While organizing the storage room, I came across an old Roomba 
iRobot 561 that had been lying on the floor for months because 
we hadn’t decided what to do with it. 

It was in good working order, with a strong battery, 
but we had stopped using it. With young children in the house, 
the floor was often cluttered with toys, and having to clear 
the space every time before running the Roomba wasn’t always 
convenient. Over time, we reverted to using the regular vacuum 
cleaner.

On the other hand, it seemed a shame to leave it unused in the 
storage room. It was in excellent condition, so it would be 
better to either use it or donate it. 

As I continued organizing, I began brainstorming various ideas 
that might be suitable for this Roomba. When I stepped out of 
the storage room, I noticed the floor was dirty with bird 
droppings.

And so, the first practical prototype was born. A combination 
of an autonomous, motorized, intelligent platform capable of 
patroling, avoiding obstacles, and autonomously returning to its 
docking station a long with a large balcony plagued by various 
birds that dig into the plants and leave behind a lot of mess 
and clutter.

To test whether birds are afraid of my mobile scarecrow, I built 
a human-shaped body out of Styrofoam, to which I attached a 
printer picture of an owl. I made the eyes more intimidating by 
using shiny black marbles. I placed this figure on the Roomba 
using a mount that can be easily disassembled.

I waited for the birds to come and activated the Roomba to 
start moving around. This scarecrow turned out to be highly 
effective, as the birds flew away as soon as it started moving, 
even before it got close to them. Even those perched on the 
pergola above weren't indifferent and flew off to a nearby 
building.

The experiment was a success, and I moved on to the improvement 
phase. Since there's no need for the Roomba to vacuum the balcony 
while it's moving around because vacuuming consumes energy that 
I need to conserve for its patrols, I completely dismantled it 
and gently removed the parts related to vacuuming.
 
Now, the Roomba is lighter, which significantly saves energy, 
increasing its operational time. Occasionally, I left the Roomba 
on the balcony, letting it move around with only the original 
"schedule" feature which was built into this model. 

Due to the size of the balcony, each round of the Roomba took 
about 35 minutes. The moving scarecrow immediately scared the 
birds away, and they didn't return as long as the Roomba was 
active. 

The only thing left was to figure out when to activate the Roomba, 
once a day isn't enough. This Roomba model doesn't have Wi-Fi for 
remote control and can only perform one scheduled "patrol" a day, 
so some sensor development and remote-control capabilities need 
to be added.

I delved into researching the model and discovered that the 
Roomba has a dedicated serial protocol called the Open 
Interface (OI), which allows for far more operations than the 
buttons on the top of the device. To experiment with this, 
I needed an Arduino board, logic level converters (since the 
Roomba operates at TTL voltage levels, i.e., 0-5 volts), 
and DC-DC converters (the Roomba's battery is 20V).

<br>

<p align="center">


<p align="center">
  <img src="/media/testing the protocl.jpg" alt="web_server" width="300" height="500"/>
</p>

 </p>

<br>


I assembled an ESP32 on a breadboard, along with some voltage 
converters, and prepared a set of HEX commands in Roomba’s 
language using the OI protocol. For example:

Stop	 	(128) (131) (137) <br>
Shutdown	(128) (131) (133) <br>
Start		(128) (131) (135) <br>


[The full protocol is here](media/iRobot_Roomba_500_Open_Interface_Spec.pdf)

<br>

And it worked.
<br>



<br>
It’s possible to execute dozens of commands, from starting and 
stopping the Roomba to full control over its sensors, wheels, 
and speed, all via the Arduino board. Once I had the Arduino 
in place, it was a short step to adding a Web Server that allowed 
connection to the controller via WiFi.

<p align="center">
  <img src="/media/web_server_with_video.png" alt="web_server" width="300" height="500"/>
</p>

It works excellently when connected to the same Wi-Fi network. 
But sometimes it’s necessary to connect from outside the Wi-Fi 
range if you wish to operate it remotely. A few additional 
lines of code were added, and my digital scarecrow now supports 
remote commands control through a Telegram bot, providing 
responses and status updates.

<p align="center">
  <img src="/media/vlcsnap-2024-08-27-21h21m46s777.png" alt="Rombi" width="700" height="300"/>
</p>


I can even get a real time video from the Roomba's patrol.
The current version supports the following commands via the Web Server and Telegram:
- Start patrol
- Return to docking station
- Remote Camera on/off
- LED lighting control
- Send status reports, such as temperature and battery voltage.

The Roomba works excellently both remotely and up close when 
I initiate an action, but now it’s time for it to gain some autonomy. 
The Roomba will need to spring into action whenever birds are on 
the balcony and occasionally even when they aren’t, as some birds, 
like mynas and pigeons, observe from a distance before deciding to 
land on the balcony.

It’s time to detect birds on the balcony and automatically activate 
the Roomba. In most cases, the birds first land on the railing or 
pergola before descending to the floor. I explored several methods 
and sensors suitable for different surfaces.

I won’t go into too much detail about all the sensors I built, but 
using a simple optical sensor based on a laser break-beam, proved 
to be the most effective and reliable for the railing. For the 
pergola, to avoid false alarms from every bird flying by, 
metal spikes (type W) needed to be used. For the floor, detection 
is handled by a camera with image processing (I used the efficient 
libraries from eloquentarduino).

I placed the laser break-beam sensors about 3 cm above the railing, 
to detect various types of birds. When a bird stands or passes
over the railing, it breaks the laser beam, sending a signal to 
the Roomba to start its patrol.

I mounted the cameras at a high position on the wall aimed to the 
floor to avoid false alarms from open areas and only focus on where 
the birds land. Each camera easily covers a floor area of about 
5 x 3 meters for a reliable detection. If detection is needed at 
night, strong lighting, such as IR, is required.

The sensors are Arduino-based and communicate directly with the web 
server within the Roomba, all connected to the same Wi-Fi network.


## Summary:

<p align="center">
 
 https://github.com/user-attachments/assets/44740bdd-6202-4ac6-9fad-2e479e566b7c

</p>
<br>

This mobile scarecrow is far more efficient than any static 
solutions I have ever tried. It’s harmless to both the birds 
and the environment. While birds may become accustomed to various 
scarecrow, reflectors and chains, they don’t remain indifferent 
when confronted by an owl with dark eyes approaching them in an 
unusual manner.

And, of course, there’s a bonus. Implementing this idea allows me 
to learn more about Arduino hardware and software, image processing,
PCB design, CAD software,  sensor design and much more. 
It’s pure enjoyment.


## Notes:

1. Virtual Wall was used to prevent the Roomba from attempting to 
enter the house.
2. Several security protections was embedded in the Roomba server 
code to prevent hackers from connecting.
3. Small mirrors were placed along the railing to maintain one laser 
source.
4. The complete code for the sensors and the main board will be uploaded in the future.


## Planned Improvements for the Next Versions:

1. A hollow body (e.g., made of metal mesh) for the scarecrow boady
to make it less sensitive to strong winds.
2. Sending the Roomba directly to the sensor area from which the 
alert was triggered.
3. Adjusting the Roomba's travel path based on the shape and size 
of the balcony.
