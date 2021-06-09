how to get wifi working on raspberry pi 4 running fedora

note for this guide I was running fedora 34. It might be possible on fedora 33 or it might be that the drivers needed were only added in fedora 34. I am not sure and have not been able to test it on fedora 33.

I needed to get access to the internet via an ethernet cable in order install the packages needed to get wifi working, but it might be possible to download them on another computer and put them on to the pi from a flash drive, again something that might be worth investigating.

By default when I ran network manager I could see information about the wifi radio and turn it on or off, but when I ran `nmcli device wifi list --rescan yes` to try and view available networks, nothing would show up. After much googling, I discovered the following packages that once installed allowed me to get wifi:

`sudo dnf install NetworkManager-wifi iwd wpa_supplicant`

some of these may be already installed, but after I had ensured that all of them were when I ran:

`nmcli device wifi list --rescan yes`

it listed the wifi networks available around me. I saw my local netowrk and ran the command

`nmcli device wifi connect NAME_OF_WIFI_NETWORK_HERE -a`

which prompted me for the password and I was able to join.

