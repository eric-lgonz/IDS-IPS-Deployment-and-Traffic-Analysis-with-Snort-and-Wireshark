# Network-Traffic-Analysis-and-NIDS-NIPS-Configuration

This guide provides a step-by-step walkthrough on how to:

- Deploy and configure Snort to monitor network traffic for potential threats such as DDoS attacks, malware, and unauthorized access attempts
- Capture and analyze network traffic using Wireshark to identify various protocols and detect anomolies
- Create custom detection rules based on known attack patterns and automated alerts for suspicious behavior

<h2> Technologies Used </h2>

- <a href="https://www.kali.org/get-kali/#kali-platforms"> Kali Linux<a/> (version 2025.1a)
- <a href="https://www.snort.org/"> Snort<a/> (version 3.1.82)
- <a href="https://www.wireshark.org/"> Wireshark<a/> (version 4.4.4)

<h2> What is Snort? </h2>

Snort is a widely used open-source Intrustion Prevention System (IPS) and Intrusion Detection System (IDS). It uses a set of rules to analyze and block network traffic, as well as generate alerts for the users. Snort describes its three primary uses as being a packet sniffer, packet logger, or a full-scale network intrusion prevention system. It is open-source and free, meaning anyone can download, modify, and distribute the code free of charge. This guide will be using Snort 3, which has many changes to its predecessor, Snort 2. If you are looking for guidance in using Snort 2, I suggest looking at my writeup for TryHackMe's Snort Challenge - Live Attacks room, which uses Snort 2.

<h1> 1. Installing Snort </h1>
First, open up a terminal by clicking on the terminal icon on the desktop:

_insert image_

Check if Snort is installed by running the command <code>snort -V</code>.

_insert image_

If you don't have Snort, you will be given an option to install it. Hit <code>y</code> and press enter to confirm the installation:

_insert image_

You will then receive a "Name or service not known" message, and will prompted to install the required dependencies and packages. Press <code>y</code> to continue, and enter your password if prompted:

_insert image_

Snort will now be installed, and you can verify the installation by using the <code>snort -V</code> command:

_insert image_




