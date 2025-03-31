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

<a href="https://www.snort.org/"> Snort<a/> is a widely used open-source Intrustion Prevention System (IPS) and Intrusion Detection System (IDS). It uses a set of rules to analyze and block network traffic, as well as generate alerts for the users. Snort describes its three primary uses as being a packet sniffer, packet logger, or a full-scale network intrusion prevention system. It is open-source and free, meaning anyone can download, modify, and distribute the code free of charge. This guide will use Snort 3, which introduces many changes compared to its predecessor, Snort 2. If you are looking for guidance in using Snort 2, I suggest looking at my writeup for <a href="https://github.com/eric-lgonz/TryHackme-Snort-Challenge---Live-Attacks"> TryHackMe's Snort Challenge - Live Attacks room<a/>.

<h1> 1. Installing Snort </h1>

First, open up a terminal by clicking on the terminal icon on the desktop:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Installing%20Snort%20-%201.png">

Check if Snort is installed by running the command <code>snort -V</code>.

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Installing%20Snort%20-%202.png">

If you don't have Snort, you will be given an option to install it. Hit <code>y</code> and press enter to confirm the installation:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Installing%20Snort%20-%203.png">

You will then be prompted to install the required dependencies and packages. Press <code>y</code> to continue, and enter your password if prompted:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Installing%20Snort%20-%204.png">

Snort will now be installed, and you can verify the installation by using the <code>snort -V</code> command:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Installing%20Snort%20-%205.png">

<h1>2. Making a Workspace for Snort</h1>

Once Snort is installed, a directory for Snort will be made at <code>/etc/snort</code>. This directory contains Snort's configuration files and rules directory. The important file to note here is <code>snort.lua</code>, which we will modify to include our custom rules. But before we do that, let's first move to our Desktop directory and create a directory on our Desktop for Snort. This directory is where we will store our custom rules file, log files, and generated alerts. We can do this by following the commands shown in the screenshot:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%201.png">

Once we are in the directory, create a file called <code>local.rules</code>, which is where we will write our custom rules:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%202.png">

Now, let's get the full path of our directory with <code>pwd</code> so we can add our <code>local.rules</code> file location to <code>/etc/snort/snort.lua</code>. We can use the following command to edit the <code>/etc/snort/snort.lua</code> file while still remaining in our current directory:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%203.png">
<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%204.png">

Scroll down to section 5 of the file, and add <code>include = "...local.rules",</code>, replacing the three dots with the results of the <code>pwd</code> command you did earlier. Don't forget to add the comma at the end of the quotes! Forgetting to do so will cause an error. See the screenshot below for an example of how the file should look after adding the line:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%205.png">

To save and quit the file, press <code>Ctrl+S</code> followed by <code>Ctrl+X</code>.

We can verify that the configuration file works by typing <code>sudo snort -c /etc/snort/snort.lua</code>. If you get an error, ensure that you entered the right path in the <code>snort.lua</code> file, and don't forget the comma! You'll know that the configuration file works if Snort runs and you get a message saying that Snort successfully validated the configuration:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Making%20a%20Workspace%20for%20Snort%20-%206.png">

<h1>3. Writing Rules in Snort</h1>

Now that we have verified that the configuration is working, it's time to start writing our own custom rules. Let's go over Snort 3's rule writing structure:

<table align="center">
  <tr>
    <th> Action </th>
    <th> Protocol </th>
    <th> Source IP </th>
    <th> Source Port </th>
    <th> Direction </th>
    <th> Destination IP </th>
    <th> Destination Port </th>
    <th> Options </th>
  </tr>
  <tr>
    <th> Alert<br>Drop<br>Reject </th>
    <th> TCP<br>UDP<br>ICMP </th>
    <th> any </th>
    <th> any </th>
    <th> <> </th>
    <th> any </th>
    <th> any </th>
    <th> Msg<br>Content<br>Sid </th>
  </tr>
</table>

As you can see above, each rule has several parts. Let's look at what each part of the rule means:

<b>Action:</b> This is what Snort does when a rule gets triggred. Some common actions are alert, drop, and reject.

<b>Protocol:</b> This tells Snort the kind of protocol it should look for. Snort 3 supports IP, ICMP, TCP, and UDP.

<b>Source IP:</b> The source IP address a rule should apply to. This can be set manually in the rules file, set as a variable in the snort config file, or it can be set to any.

<b>Source Port:</b> The source port field specifies which port(s) to look at. This can be a static port, range, a list, or set to any.

<b>Direction:</b> The direction operator indicates the flow of traffic. This can be either <code>-></code> or <code><></code>

<b>Destination IP:</b> The destination IP address a rule should apply to. This can be set manually in the rules file, set as a variable in the snort config file, or it can be set to any.

<b>Destination Port:</b> The destination port field specifies which port(s) to look at. This can be a static port, range, a list, or set to any.

<b>Options:</b> There are several options that go along with each rule, separated by a <code>;</code>. Some important ones include <code>msg</code>, <code>content</code>, and <code>sid</code>. Msg appears in the console or log when the rule is triggered. It is normally a few words that summarize the event. Content indicates that a specific match of something must be found in payload data. For example, if content is set to "hello", then the packet would have to include "hello" in order for the rule to be triggered. Sid is the rule's signature id. Local rules should have sid values starting above 1000000, as Snort reserves values 0-999999 for rules included with the Snort distribution.

In order to write our rules, let's find out the IP address of our machine so we could use it in the source/destination ip parameters. In the terminal, type <code>ifconfig</code> and hit enter. You will see your loopback address and another interface. As shown in the screenshot, mine is called "eth0", but yours might be called something different.

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Writing%20Rules%20in%20Snort%20-%201.png">

Now turn on promiscuous mode to ensure that all of our icmp traffic can be seen. Just type in <code>sudo ip link set eth0 promisc on</code>, replacing "eth0" with the name of your interface. Also, let's take note that our "inet", or internet address, is 10.0.2.15. Once again, yours may be different.

Now we have everthing we need to begin writing rules in our <code>local.rules</code> file. Open up the file in your preferred text editor and make sure that the file is empty.

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Writing%20Rules%20in%20Snort%20-%202.png">

Let's write three simple rules that generate alerts:

1. When any ICMP traffic is detected
2. When outgoing tcp traffic is detected
3. When incoming HTTP traffic contains a certain keyword in the payload

Now using your knowledge of the Snort rule structure, write the corresponding rules in the file, with each rule on a separate line:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Writing%20Rules%20in%20Snort%20-%203.png">

Now save the file and exit out of it, and we can begin testing our rules!

<h1>4. Running Snort</h1>

The first Snort command we will use is <code>sudo snort -c /etc/snort/snort.lua -A cmg -i eth0 --daq afpacket</code>. The "-c /etc/snort/snort.lua" specifies that we want to run snort with the default configuration file (which includes our local rules); "-A cmg" will provide alerts in the console with packet details in plaintext and hex; "-i eth0" specifies which interface we want to listen on, and "--daq afpacket" specifies that we want to use the afpacket module, which is optimized for high-performance packet capture.

Run the command, then open up another terminal and enter the command <code>ping -c 3 google.com</code>. This will simulate ICMP traffic in your Snort terminal. Make sure to do <code>Ctrl+C</code> in the terminal to stop the Snort program::

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%201.png">
<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%202.png">

In each alert triggered, you can see the date and time it was triggered, which rule was triggered, and the details of the packet.

So now we know that our first rule works, we could test our other two rules in a similar way where we start Snort and then open up our browser to capture tcp traffic. While this method works just fine, let's practice using Wireshark to capture traffic and then feed that packet capture into Snort to detect alerts.

Let's begin by typing <code>wireshark &</code> to open Wireshark:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%203.png">

Double click the interface that you used to capture the icmp traffic, and then open up any browser and navigate to any website:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%204.png">

Your wireshark page should have filled up with all kinds of packets. Press the red square in the top left to end the packet capture, and save the capture by pressing "File" and "Save As". Label the file <code>basic_tcp.pcapng</code> and save it to the <code>Desktop/Snort</code> folder.

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%205.png">

Now let's capture traffic from "http://gaia.cs.umass.edu/wiresharklabs/alice.txt". In Wireshark, begin a new packet capture by pressing the button shaped like a blue shark fin, and then visit "http://gaia.cs.umass.edu/wiresharklabs/alice.txt" in any browser. Once the page loads, end the capture and save it as <code>http_payload.pcapng</code>:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%206.png">

Now let's go back to the terminal and test out rules 2 and 3 with our new pcap files!

We can tell Snort that we want to read in a certain file by using the <code>-r [filename]</code> command. Let's run <code>sudo snort -c /etc/snort/snort.lua -r basic_tcp.pcapng -A cmg</code>:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%207.png">
<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%208.png">

As shown in the image above, we can see that rule number 2 was triggered several times, and that it tells us that outgoing tcp traffic was found.

To test our last rule, let's use a similar command, <code>sudo snort -c /etc/snort/snort.lua -r http_payload.pcapng -A cmg</code>:

<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%209.png">
<img src="https://github.com/eric-lgonz/Network-Traffic-Analysis-and-NIDS-NIPS-Configuration/blob/main/assets/Running%20Snort%20-%2010.png">

And there we go! We were able to search the entire packet capture and find the matching packet that contained "HTTP/" in the payload data, telling us which HTTP version was used. This was done using the all-important "content" rule option. This option is often used to detect malware, exploitation attempts, or suspicious file transfers, and it can be customized even further with additional parameters that we did not cover here.

<h1>Conclusion</h1>

In this walkthrough, we covered the basics of Snort 3. We went over the installation process, writing custom rules, and analyzing traffic both in real time and using packet captures. There are many more intricacies to Snort, and I encourage you to visit the <a href="https://www.snort.org/"> Snort website<a/> to learn more. Thank you for reading this guide, and you can find more projects and writeups like this <a href="https://github.com/eric-lgonz"> here<a/>.








