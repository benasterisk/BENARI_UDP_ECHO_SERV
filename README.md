
BENARI_UDP_ECHO_SERV
Asterisk Python ARI (Rest Interface) basic demonstration with embedded Dynamic UDP Socket Server

Introduction
This script is designed to serve as a basic framework for developing more comprehensive Asterisk applications. It demonstrates the functionalities of the Asterisk Stasis interface and its REST API, focusing on External Media for UDP socket connections to transport audio, as well as managing bridges and channels. The purpose of this script is to help users understand the common mechanics employed during Asterisk development. It should be noted that this script is a demonstration and does not follow professional development standards for secure and robust implementations.

Initially, there was an attempt to abstract the ARI library and implement the solution using Python 3 with Websocket/Request. However, after spending time on this approach, it was decided to utilize the ari-py libraries with Python 2.7.18. This decision does not imply that the libraries are incompatible with Python 3 (see ari-py compatibility), but just a workaround to save time.

In real-life scenarios, the RTP Echo Server behind UDP sockets would be replaced with real-time redirection to Speech-To-Text/Text-To-Speech API websockets. This would allow for more dynamic and interactive audio processing capabilities, leveraging modern speech recognition and synthesis technologies for Voice Bot applications.

Script Functionality
Connects to Asterisk REST Interface (ARI) at http://10.0.0.1:8088 with specified credentials.
Port Management: Manages a pool of UDP ports for External Media connections.
Event Handling: Handles Stasis events for channels and manages call flow.
RTP Echo Server: Implements a simple RTP echo server to demonstrate external media handling.
Cleanup Mechanism: Ensures proper resource cleanup, including hanging up channels, destroying bridges, and releasing ports.
Global Sequence
An inbound call enters the Stasis application voicebot1.
External Media Setup: The script sets up an external media connection to an RTP echo server, emulating a future voicebot.
Waiting Period: The script waits for 7 seconds.
Call Origination: A new call is originated to redirect the caller to an external Avaya extension 4438007, which plays music infinitely.
Event Monitoring: If the caller or the dialed Avaya destination hangs up, the script monitors events and destroys the corresponding bridge and channel, and frees the UDP socket.
Ensure you have all libraries installed with pip3:

requests
ari > https://github.com/asterisk/ari-py
socket
Script Execution
python3 benaridudpechoserv.py

The script runs a Stasis application named voicebot1. When an inbound call is received, it triggers the main call flow, sets up an external media connection, and manages RTP traffic using the echo server. After 7 seconds, it originates a new call to redirect the caller to an external Avaya extension 4438007, which plays music infinitely. If the caller or the dialed Avaya destination hangs up, the script monitors events, destroys the corresponding bridge and channel, and frees the UDP socket.

extensions.conf
Defines the dial plan for routing calls.
[default]
exten => _3308487,1,Answer()
same => n,Stasis(voicebot1,inbound)
same => n,Hangup()

exten => _XXXXXXX,1,Set(CALLERID(num)=+3303303)
same = n,Dial(PJSIP/${EXTEN}@AVAYA-endpoint)
same = n,Hangup()

; Public network via Avaya
exten => _000.,1,Set(CALLERID(num)=+33611111111)
same = n,Dial(PJSIP/${EXTEN}@AVAYA-endpoint)
same = n,Hangup()

rtp.conf
Defines the RTP port range.
[general]
rtpstart=25000
rtpend=26000

http.conf
Enables and configures the HTTP server for ARI.
[general]
enabled = yes
bindaddr = 0.0.0.0
bindport = 8088
sessionlimit = 100

ari.conf
Configures ARI users and access permissions.
[general]
enabled = yes
allowed_origins=http://ari.asterisk.org
pretty = yes

[asterisk]
type = user
read_only = no
password = password

Conclusion
This script is a starting point for understanding and developing Asterisk-based applications using ARI. It covers the basics of managing channels, bridges, and external media connections. For production use, it is essential to implement robust error handling, security measures, and follow best practices as outlined in the official Asterisk documentation available at asterisk.org. With a touch of humor, it’s important to note that the resource management aspects of this script, such as hanging up channels, releasing ports and UDP sockets, and destroying unused bridges, showed weaknesses under simulated high load using SIP stress testers. This is likely more due to the developer's inexperience with event handlers and multithreading than the stability of the Asterisk system itself.

Contributing
Contributions to BENARI_UDP_ECHO_SERV are welcome! Please feel free to submit a Pull Request.

License
This project is licensed under the MIT License - see the LICENSE file for details.

Copyright (c) 2024 Michaël Benarouch (benasterisk@gmail.com) GitHub Repository: https://github.com/benasterisk/BENARI_UDP_ECHO_SERV

Acknowledgments
Asterisk (https://www.asterisk.org/) for the PBX system integration
BENARI_UDP_ECHO_SERV is provided as-is, without any guarantees or warranty. The authors are not responsible for any damage or data loss that may occur from the use of this script.