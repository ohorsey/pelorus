## Table of Contents

- [[#Introduction]]
- [[#Bill of Materials]]
- [[#Instructions]]
	- [[Hardware]]
	- [[#Software]] 
	- [[#Firmware]]
- [[#Acknowledgements]]
## Introduction

I love my Moonlander, but one downside of a split keyboard is that it's no longer practical to type/use the whole keyboard with one hand while using a mouse, and going back and forth from my trackball to the right half of my keyboard was bumming me out.

![[PXL_20260125_225050999~2.jpg]]
![[PXL_20260125_225117174.jpg]]

Enter my slant on the ploopy Nano BTU mod.  Fair warning, the ZSA integration is specifically for linux and optimised for Fedora/centOS/RHEL/systemd, though it should be deployable anywhere.

## Bill of Materials

* [[https://ploopy.co/shop/nano-trackball-kit/|A ploopy nano build kit]], or a ploopy nano two if you can modify the .stl files
* [[https://store.livhaven.com/r053010810-bosch-rexroth-ball-transfer-unit|3 # R053010810 BTU]], currently around $50 including shipping - alternate sources listed on the [[https://github.com/gbrnt/ploopy-nano/tree/master/hardware/mechanicals-btu-mod|ploopy BTU mod github page]]
* [[https://www.amazon.com/dp/B0FWWW8VP1?ref=ppx_yo2ov_dt_b_fed_asin_title&th=1|2 M3 heat inserts]], the ones I used were 4mm wide and 6mm long, the length was good but I think longer threads/knurls would be easier to figure out.  It's probably possible to do without if you want to thread into plastic.
* Soldering iron, solder, all related supplies like a sponge/rag/solder/solder wick etc. Pre fluxed is fine but flux is good to have.
* A small screwdriver, maybe a drill if your 3d printing skills are lacking
* Either access to a 3d printer and a small amount of PLA or ordering a print
## Assembly

1. Before you begin, read these instructions, the [[https://www.zsa.io/moonlander/teardown#removing-ribbon-cable|ZSA teardown instructions, ]] and the ploopy nano [[https://github.com/ploopyco/nano-trackball/wiki/Ploopy-Nano-Trackball-Kit-Assembly|instructions on github]] in full and make sure you're prepared.
2. Print the 3d printed parts.  My software and printer (bambu) defaulted to most layers at 0.02, which was thinner than the 0.08 specified by @Shane, but my print came out much smoother.  I also enabled supports, it printed without them, but some of the edges were fraying. The 2 significant modifications I made to the design were to reduce the size of the screw holes for the heat inserts and add 100% infill on the hinge to increase stability, as well as removing a notch to accommodate the hinge of the Moonlanders ribbon cable slot,
3. Inspect and clean the prints.
4. Assemble the ploopy Nano circuit board per the [[https://github.com/ploopyco/nano-trackball/wiki/Ploopy-Nano-Trackball-Kit-Assembly|instructions on github]] up until step 9.
5. Now that you've soldered the circuit board together, it's time to insert the M3 inserts into the top housing.  I used 225 C into PLA with a printing temp of 220, and it took some experimenting to get it right.  Save any bad prints you have to practice inserts on, the trick is getting them in perpendicular.  If you don't have a press on a lot of tools, my advice is to insert them slow and steady until they're nearly flush, and then to quickly put down the iron and push the edge of the print against a very hard flat surface/bottom of a table. Watch videos on youtube.
6. On step 9 of the ploopy instructions you'd screw the self-tapping screw from ploopy through the hole on the bottom half of the housing and it will tap into the plastic channel on the top half.  That is sufficient for this screw.  You may need to reflash the nano firmware in several steps, which requires exposing the circuit board, so you may want to wait to secure the housing until you are finished.
7. Remove the Moonlander's thumb cluster and ribbon cable per the [[https://www.zsa.io/moonlander/teardown#removing-ribbon-cable|ZSA instructions]].  As ZSA notes, the ribbon cable and cluster hinge are delicate, you can break you equipment if you are not delicate. 
8. Use the screws from the Moonlander thumb cluster to affix the Nano to your keyboard.  I found it hard to determine whether these screw were M3 or M2.5 using the internet, both of them proved to be M3.  If you were to try and adapt this mod for the left thumb cluster I don't think you could use heat inserts here, due to the reverse rotation on one screw.
9. Re-assemble your keyboard, plug it and you nano in and confirm it tracks movement!
10. It may feel a bit off, if so use QMK firmware to edit this line `#definePOINTING_DEVICE_ROTATION_270`in "qmk_firmware/keyboards/ploopyco/trackball_nano/config.h" and reflash your nano.
    [[https://docs.qmk.fm/newbs|QMK intro here]].  Commenting it out/zeroing it is pretty close, I think I landed on 320 but this may be personal preference.  Even if you've never used QMK and are really rusty this should really only take 5-30 minutes.
11. HARDWARE IS COMPLETE!

## Software

1. These instructions are for systemd machines specifically, but the script is really simple and should be fine wherever.
2. The script needs to be able to read device input, which is generally restricted.  I decided to add my user to the group which owns the device, input, instead of modifying the device.
   Run `sudo usermod -aG input <username>` with your username
3. Download and [[https://github.com/zsa/kontroll|install kontroll]], preferably to ~/bin/kontrolljj
4. update the .service file and nanoKontroll for your environment per the comments in each file.
5. place nanoKontroll in ~/bin and run `sudo chmod +x nanoKontroll`
6. Place the .service file from /software/ in ~/.config/systemd/user/ and run 
   ```
   systemctl --user daemon-reload
   systemctl --user start nano-zsa-integration.service
   ```
7. You should be good to go, minus one note about the lock layer in the firmware section!!

## Firmware

* You probably already re-flashed the Nano to accommodate the change in rotation.  Other than that, I think you're good, though I have been experimenting with the [[https://github.com/pandrr/wiggleball|wiggleball]].  I'm not sure I can recommend it though.
* [[https://configure.zsa.io/moonlander/layouts/RRxy4/latest/6|This is my current oryx layout]], there's nothing really revolutionary there, the only thing to note is my use of Mouse Button 8 on my mouse layer.  The lock layer key in oryx doesn't work as intended with this solution, so instead I've created a shortcut in my window manager (hyprland) which runs the 'lockLayer' function in nanoKontroll when 'Mouse Button 8' is pressed
  `bind = , mouse:279, exec, bash -c "/home/<username>/bin/nanoKontroll lockLayer"`![[snapshot_2026-01-25_18-36-58.png]]
## Acknowledgements
This is an adaptation of other work, particular thanks goes to -
	* [Papa-o-ROM] (https://www.printables.com/@PapaoROM_885955) for getting the ball rolling on Moonlander trackballs
	* [Shane Deiley] (https://www.printables.com/model/520579-ploopy-nano-btu-mod-with-zsa-moonlander-right-thum) Shane took a couple models and spun them together, making the template I iterated on, some of those sources are as follows
		* [nov1n] (https://www.printables.com/model/232829-moonlander-thumb-pad-blanks) nov1n made a cool trackball, but the interface between the thumb cluster and the main board is what was sourced
		* [nivekmai] (https://www.printables.com/model/776160-moonrover-mk2) and [git] (https://github.com/nivekmai/qmk_firmware/tree/i2c-arduino-trackball) Similarly, nivekmai made some trackballs, but it was the housing they made that was sourced here.
		* [George Bryant] (https://github.com/gbrnt) Last but certainly not least, George Bryant made the original BTU mod for the ploopy Nano without which this would never have come together

As well as a general thanks to the [[https://www.zsa.io/|ZSA]] and [[ploopy.co/|ploopy]] teams for all the hardware, tools, support. Let me know if I missed someone!