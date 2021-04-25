## Actually use your Pifi DAC+ with spotify

As i got my Pifi DAC+ i had some trouble get it running, not using a Distro like Volumino. After lots of grinding through forums and getting seriously annoyed i finally made it. To prevent you from having the same experience, i decided to gather the information i found here. 


### My Goal

Get Spotify running an my Raspberry Pi and use Pifi DAC+ as audio-output

### My Hardware
1. Raspberry Pi 3b+ (Including all required things to use it)
2. Pifi DAC+ 
3. Some active Speaker or Hifi-system

### My Software
1. I used MacOS and balenaEtcher to burn the Image to my SD Card
2. [Raspian OS](https://www.raspberrypi.org/software/operating-systems/), i use "Raspberry Pi OS with desktop March 4th 2021"
3. [raspotify](https://github.com/dtcooper/raspotify) 

### Note

I want tell you how to get startet with your Pi. There are lots of tutorials out there e.g. [this](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up)


### Install your Soundcard

There is actually not much u can do wrong connecting your Pi with your Soundcard. It fits only one way. Usually it comes with some screws and some Spacers. Prepare the Pi with the Spacers and just put the soundcard on top of your pi, carefully pushing down, so you don't break the pins. Some force is needed.

After this you have the choice (if ssh is enabled) to do the next part via ssh or directly on your pi. In both cases you need to connection to the internet and electricity. 

Note that this card has an IR receiver built in. The IR receiver is connected to GPIO26. Remove JP2 to disable the IR receiver.
```markdown
sudo nano /etc/modules
```
Comment out (#) the following line:
```markdown
snd_bcm2835
```
Add the following lines:
```markdown
bcm2708_dmaengine
snd_soc_pcm512x
snd_soc_hifiberry_dacplus
snd_soc_bcm2708_i2s
```
Now we need to load the correct device tree file by editing /boot/config.txt:
```markdown
sudo nano /boot/config.txt
```
Add the following lines at the end of the file:
```markdown
dtparam=i2c_arm=on
dtparam=i2s=on
dtoverlay=hifiberry-dacplus
```
Save the file (Ctrl-X, Y, Enter)

Create the sound.conf file as follows:
```markdown
sudo nano /etc/asound.conf

pcm.!default  {
 type hw card 0
}
ctl.!default {
 type hw card 0
}

```
Save the file (Ctrl-X, Y, Enter) and reboot
```markdown
sudo reboot
```
After reboot, check if the DAC is selected as your sound card:
```markdown
aplay -l
```
You should see this:
```markdown
card 0: sndrpihifiberry [snd_rpi_hifiberry_dacplus], device 0: HiFiBerry DAC+ HiFi pcm512x-hifi-0
Subdevices: 0/1
Subdevice #0: subdevice #0
```

[Here i found this solution](https://github.com/guussie/PiDS/wiki/09.-How-to-make-various-DACs-work)


### Get Spotify running

To get spotify running is easy.
```markdown
curl -sL https://dtcooper.github.io/raspotify/install.sh | sh
```

go ahead 
```markdown
sudo aptitude install alsa-utils
sudo modprobe snd_bcm2835
sudo amixer cset numid=3 1
sudo alsactl store
```

Now you need to set the volume. Just use this program.

```markdown
alsamixer
```

Now you just need to tell spotify which device it should use. Therefor use
```markdown
aplay -l
```
To see in which position your soundcard is. Normally it should be listed as Card 2

Now tell Spotify the device number
```markdown
sudo nano /etc/default/raspotify
```

Add this to the file
```markdown
OPTIONS="--device hw:2,0"
```
Save the file (Ctrl-X, Y, Enter)

The first number is for the device the second for the subdevice. As this Card has no subdevices use 0
**Note:** The numbers function like a Position in an array so the first device has the position 0. In my case the soundcard is at position 2. This can be different in your system, so please be shure you don't skip the previous step 

Last step is to restart the service
```markdown
sudo service raspotify restart
```

### Play music

**Important:** You need Spotify Premium to use this feature. Also make shure the device you use Spotify on and the pi are in the same network

1. Connect the Soundcart to your Hifi-System
2. Start Spotify on your device e.g. Smartphone
3. Choose a song and play it
4. Now you have the choice on which device in the Network it should play
5. Choose raspotify
6. Enjoy

[Here i found this tutorial](https://strobelstefan.org/2020/05/06/spotify-ueber-den-raspberry-pi-abspielen/)

### Thank you 
[STEFAN'S WEBLOG](https://strobelstefan.org/2020/05/06/spotify-ueber-den-raspberry-pi-abspielen/) and [guussie](https://github.com/guussie) for providung this information to me. 

### Support or Contact

I hope this will help all those, who desperatly searching for help. If something is not clear, just write me message. Even though i can not guarantee that I can help I will try!
gl hf
