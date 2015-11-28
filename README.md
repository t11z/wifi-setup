# wifi-setup
Simple boot script to associate with known wifi-networks or create instant hotspot, if none is in range.

## Requirements
====================

- wpa_supplicant
- ISC DHCP server
- bash

## Installation and configuration
====================
Clone repository to /etc/init.d, then do

    # chmod +x /etc/init.d/wifi-setup
    # update-rc.d wifi-setup defaults
    # apt-get install isc-dhcp-server wpasupplicant
    # cat <<EOT >>/etc/dhcp/dhcpd.conf
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.168.255;
        option routers 192.168.168.1;
        subnet 192.168.168.0 netmask 255.255.255.0 {
            range 192.168.168.100 192.168.168.199;
        }
    EOT

The script relies on proper wifi entries in /etc/wpa_supplicant/wpa_supplicant.conf. These are best made using wpa_supplicant's interactive commandline **wpa_cli**.

Here is an example:

    # wpa_cli
    Selected interface 'wlan0'
    
    Interactive mode

    > scan
    OK
    <3>CTRL-EVENT-SCAN-RESULTS 
    > scan_results 
    bssid / frequency / signal level / flags / ssid
    ca:ff:ee:ca:ff:ee	2412	56	[WPA2-PSK-CCMP][WPS][ESS]   MyWifi
    > add_network 0
    > set_network 0 ssid "MyWifi"
    > set_network 0 psk "MyPresharedKey"
    > enable_network 0
    <2>CTRL-EVENT-CONNECTED - Connection to ca:ff:ee:ca:ff:ee completed (reauth) [id=0 id_str=]
    > save_config
    
Depending on your hardware it may also be needed to change the wifi-driver used by wpa_supplicant. In this case you have to change the *-D* parameter passed to wpa_supplicant in the script.
