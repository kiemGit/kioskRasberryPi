# firewall raspberry <br><br>

	+ sudo apt update<br>
	+ sudo apt full-upgrade<br>
	+ sudo apt install ufw<br>
	+ sudo ufw allow ssh<br>
	+ sudo ufw allow ssh<br><br>

# check bit raspberry pi<br><br>

		+ getconf LONG_BIT<br><br>
		
# check user raspberry, check group raspberry<br><br>

	+ $ id<br><br>
	
		+ out put <br><br>
		
			<pre>
				uid=1000(sap1) gid=1000(sap1) groups=1000(sap1),4(adm),20(dialout),24(cdrom),27(sudo),29(audio),44(video),46(plugdev),60(games),100(users),104(input),106(render),108(netdev),117(lpadmin),997(gpio),998(i2c),999(spi)
			</pre><br><br>

# kiosk mode raspberry<br><br>

	+ requirement<br><br>
	
		+ Raspberry Pi (any model)<br>
		+ USB-C power supply unit (PSU)<br>
		+ microSD card<br>
		+ HDMI cable<br>
		+ Monitor, TV, or other suitable display<br><br>
		
	+ install respberry pi OS 64 bit = PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"<br><br>
	
		+ using raspberry pi imager [ https://www.raspberrypi.com/software/ }<br>
		+ ip address: 192.168.0.39, user: sap1, pass: sap123ok, group: sap1, <br>
		+ disable firewall<br>
		+ enable ssh<br>
		+ sudo apt install xdotool unclutter // for removes the mouse pointer from your display<br>
		+ sudo raspi-config<br>
		+ Navigate through the menu, selecting 1 System Options, then S5 Boot / Auto Login, and finally B4 Desktop Autologin — Desktop GUI, automatically logged in as ‘pi’ user:<br>
		+ sudo nano /home/pi/kiosk.sh<br>
		+ copy this script into kiosk.sh<br>
			<pre>
				#!/bin/bash

				xset s noblank
				xset s off
				xset -dpms

				unclutter -idle 0.5 -root &

				sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' /home/sap1/.config/chromium/Default/Preferences
				sed -i 's/"exit_type":"Crashed"/"exit_type":"Normal"/' /home/sap1/.config/chromium/Default/Preferences

				/usr/bin/chromium-browser --noerrdialogs --disable-infobars --kiosk https://www.raspberrypi.com/ https://time.is/London &

				while true; do
				   xdotool keydown ctrl+Tab; xdotool keyup ctrl+Tab;
				   sleep 10
				done
			</pre><br><br>
		+ Let’s break this script down into its component parts in order to understand what it does:<br><br>

			+ #!/bin/bash — This instructs the operating system to use bash as our command interpreter.<br>
			+ xset s noblank, xset s off, xset -dpms — These commands stop the screensaver function and the desktop environment from interfering with our display.<br>
			+ unclutter -idle 0.5 -root & — This stops the mouse pointer from displaying on the screen.<br><br>
			
		+ sudo nano /lib/systemd/system/kiosk.service<br>
		+ copy this script into kiosk.service<br><br>
		
			<pre>
				[Unit]
				Description=Chromium Kiosk
				Wants=graphical.target
				After=graphical.target

				[Service]
				Environment=DISPLAY=:0.0
				Environment=XAUTHORITY=/home/sap1/.Xauthority
				Type=simple
				ExecStart=/bin/bash /home/sap1/kiosk.sh
				Restart=on-abort
				User=sap1
				Group=sap1

				[Install]
				WantedBy=graphical.target
			</pre><br><br>
			
		+ sudo systemctl enable kiosk.service<br>
		+ sudo systemctl start kiosk.service<br>
		+ sudo systemctl stop kiosk.service // for stop service<br>
		+ reboot system<br><br>
