#Remote Painting Machine

##### This project builds up a distributed system which streams live image from 3 cameras in 3 remote locations over the internet. A computer-vision application paints the results using 3 robotic painting machines in a gallery space.

NOTE: Te project has been tested on a raspberry pi B for the camera app, centos OS server (main online bridge server) and ubuntu-gnome 15.10 for the computer vision.

### The System:

There are 3 main software elements in this repository, all the hardware (machine part) is developed separately.

1. Cameras: Each camera is a raspberry pi with a nodejs appication taking still images using the camera module, watching for any new picture taken and posting them to an online server.

2. Server: The server runs online and receives the POST from each raspberry pi. The posts are routed to /uploads and classified based on each's camera id.

3. Remote OpenCV Application: a software (built in openframeworks) running on a PC in the gallery space, calls each camera stream on the online server and processes the images. It searches for moving objects and clasiffies them by size in an attempt to distinguish between cars, bicycles and persons. The contours are sent to the different machines which then draw the result image on a canvas. (NOT INCLUDED ON THIS REPOSITORY)

### Dependencies:

1. Camera: a raspberry pi with nodejs installed, a camera module and the proper camera libraries.

+ [Raspberry Pi](https://www.raspberrypi.org/)
+ [Raspberry Pi Camera Module](https://www.raspberrypi.org/products/camera-module/)
+ [nodejs](https://nodejs.org/en/)
+ [raspicam library](https://www.raspberrypi.org/wp-content/uploads/2013/07/RaspiCam-Documentation.pdf)

2. Server: nodejs and forever installed.

+ [nodejs](https://nodejs.org/en/)
+ [forever](https://www.npmjs.com/package/forever)

### Installing and Running

Clone the repository on each device (raspberry pi, online server and computer to run openframeworks):
		
		git clone https://github.com/rxf992/camera-server-LXD.git

##### In the raspberry pi --> 

		$sudo apt-get install nodejs
		$sudo apt-get install nodejs-legacy
		$sudo apt-get install npm --fix-missing
        
		$cd ~/remotePaintingMachine/camera/
        
		$sudo npm install
		$sudo npm install -g forever
        
		$check if the deployment is ready with cmd:
		$"nodejs app.js cam1"
        
		$crontab -u pi -e
		add the following text:
		@reboot /usr/bin/sudo -u pi -H /usr/local/bin/forever start /home/pi/remotePaintingMachine/camera/app.js cam1


Make sure you first modify app.js to POST images to your own server. Open the app.js file and change the following line (replacing SEVER_PUBLIC_IP with your server's address): 

		var url = "http://SEVER_PUBLIC_IP:3333/stream?id=" + id;

run app.js forever:

		forever start app.js cam1

Notice: there is an argument to this command: "cam1"... app.js is programmed to receive either cam1, cam2 or cam3 as IDs for each camera.

To have the app.js script executed at boot we have to execute some commands. Run the following replacing the “pi” with your desired runtime user for the node process. If you choose a different user other then yourself, you will have to run this with sudo.
	
		crontab -u pi -e

It will ask you which editor you wish to edit with. Select nano.
Once in the editor add the following line (make sure you add your right path):

		@reboot /usr/bin/sudo -u pi -H /usr/local/bin/forever start /home/pi/remotePaintingMachine/camera/app.js cam1

##### In the Server --> 
		#apt-get install nodejs
		#apt-get install nodejs-legacy
		#apt-get install npm --fix-missing
		#cd remotePaintingMachine/server
		#npm install
		#npm install -g forever

run server.js forever:

		forever start server.js
		
To have the app.js script executed at boot we have to execute some commands. Run the following replacing the “pi” with your desired runtime user for the node process. If you choose a different user other then yourself, you will have to run this with sudo.
	
		#crontab -e

It will ask you which editor you wish to edit with. Select nano.
Once in the editor add the following line (make sure you add your right path):

		@reboot forever start /root/remotePaintingMachine/server/server.js
