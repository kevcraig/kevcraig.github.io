# DC Metro Prediction Board


{{< figure src="Metro-Sign-Image-1.jpg" title="" >}}

## Overview

In April, I wrote about a [network analysis](https://kevcraig.github.io/DC%20Metro%20Network%20Analysis/) of the DC metro system. In the process of finding metro data for that project, I gained developer access to the WMATA metro API. One endpoint of the API was current predictions for each train and station within the DC metro network, which sparked an idea for a DIY metro board for my U-Street apartment. The goal was to build something that resembles the actual train prediction screens found in DC metro stations but have it in my own living room, all made possible by the WMATA API and some hardware found on the internet. 

## Process
Now that the project is complete, I wanted to share my process and what I learned in my first project dealing with both hardware & software. All the code is on my [Github](https://github.com/kevcraig/LED-Metro-Sign) and can be cloned if you want to build a board for your own WMATA station. 

### Hardware:
To build a real time metro prediction board, you need a screen, a computer to connect to the screen and API, and a power connection. Hardware costs $118 if you buy everything new from [Adafruit](https://www.adafruit.com/)
* Raspberry Pi ZeroWH
  * Raspberry Pi with WIFI access and GPIO headers to connect to the AdaFruit Bonnet. Any Raspberry Pi / Arduino with these specs would work, but this was the cheapest option I could find 
* 16GB Micro SD Card
  * SD card used for the Raspberry Pi OS and storage 
* 64 x 32 RGB LED Matrix - 6mm pitch
  * LED Board that displays metro predictions. The pitch is the space between pixels, I choose 6mm
* Adafruit RGB Matrix Bonnet for Raspberry Pi
* Bonnet hardware between Raspberry Pi and LED board 


Basic architecture for the system is displayed below:
{{< figure src="Basic_Arch.png" title="" >}}

### Software:
I thought about software in two groups: code to pull and store train predictions from the WMATA API, and code to display train predictions on the LED board. 
1. *Requirements*: Pull and store train predictions from WMATA API
  * Connect to the WMATA API through WIFI
  * Update every 10 seconds
  * Don’t get rate limited
  * Store predictions as PNG
  * Allow user to change station as needed
2. *Requirements*: Display train predictions on LED board
  *  Make sure text fits on board
  * Refresh text every 10 seconds

Pulling WMATA train predictions for a given station every 10 seconds was simple enough through the WMATA's developer API. The harder part was figuring out how to display and update the predictions on the LED board. Luckily, the [rpi-rgb-led-matrix library](https://github.com/hzeller/rpi-rgb-led-matrix) built by Henner Zeller can be used to control LED panels from a computer like a Raspberry Pi. With the rpi-rgb-led-matrix Python bindings, you can choose to display scrolling text or static images on a single board or string of boards. I preferred the PNG image option so I could display three incoming train predictions at once, which meant I needed to convert JSON API responses to a PNG image file the rpi-rgb-led-matrix library could handle.  I've played with scraping text off images with OCR packages but had never thought of converting a text to an image. Luckily, the Python Imaging Library (PIL/Pillow) simplified this process to a few lines of code.

A basic process flow for the system is outlined below. The Prod_Prediction_Ticker.py and Prod_Display_Image.py files run concurrently and refresh every 10 seconds.
{{< figure src="Process_Flow.png" title="" >}}


## What I learned </h3>
* Building something physical is super rewarding</li>
  * Most of my projects end up as a model or dashboard. It was fun to make something with a physical end product that I can keep on my desk
            
* Cloud services are awesome
  * Imaging and setting up a Raspberry Pi for an SSH connection take time and know how. This really puts into perspective how nice it is to spin up an EC2 instance and be ready to code in a couple minutes. Working with a new Linux computer first hand gave me an appreciation for what goes on behind the scenes with VMs. 

* Linux is nice to know</li>
  * Everything I did on the Raspberry Pi from downloading libraries, scping files, updating python scripts was through the command line. A basic understanding of the useful commands was super important to work quickly. I included useful commands in this project's <a href="" target="_blank"> [Github repo](https://github.com/kevcraig/LED-Metro-Sign) that I used throughout this project.

If you are interested in building one of these for DC or another city with real-time accessible data, let me know!


