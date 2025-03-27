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

Snort is a widely used open-source Intrustion Prevention System (IPS) and Intrusion Detection System (IDS). It uses a set of rules to analyze and block network traffic, as well as generate alerts for the users. Snort describes its three primary uses as being a packet sniffer, packet logger, or a full-scale network intrusion prevention system. It is open-source and free, meaning anyone can download, modify, and distribute the code free of charge. This guide will use Snort 3, which introduces many changes compared to its predecessor, Snort 2. If you are looking for guidance in using Snort 2, I suggest looking at my writeup for <a href="https://github.com/eric-lgonz/TryHackme-Snort-Challenge---Live-Attacks"> TryHackMe's Snort Challenge - Live Attacks room<a/>, which uses Snort 2.

<h1> 1. Installing Snort </h1>
First, open up a terminal by clicking on the terminal icon on the desktop:

_insert image_

Check if Snort is installed by running the command <code>snort -V</code>.

_insert image_

If you don't have Snort, you will be given an option to install it. Hit <code>y</code> and press enter to confirm the installation:

_insert image_

You will then be prompted to install the required dependencies and packages. Press <code>y</code> to continue, and enter your password if prompted:

_insert image_

Snort will now be installed, and you can verify the installation by using the <code>snort -V</code> command:

_insert image_

<h1>2. Making a Workspace for Snort</h1>

Once Snort is installed, a directory for Snort will be made at <code>/etc/snort</code>. This directory contains Snort's configuration files and rules directory. The important file to note here is <code>snort.lua</code>, which we will modify to include our custom rules. But before we do that, let's first move to our Desktop directory and create a directory on our Desktop for Snort. This directory is where we will store our custom rules file, log files, and generated alerts. We can do this by following the commands shown in the screenshot:

_insert image_

Once we are in the directory, create a file called <code>local.rules</code>, which is where we will write our custom rules:

_insert image_

Now, let's get the full path of our directory with <code>pwd</code> so we can add our <code>local.rules</code> file location to <code>/etc/snort/snort.lua</code>. We can use the following command to edit the <code>/etc/snort/snort.lua</code> file while still remaining in our current directory:

_insert image_





