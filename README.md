# Wi-Fi 6 Access Point Setup with hostapd
This guide explains how to configure a Wi-Fi 6 (802.11ax) access point (AP) on the Intel AX200 (wlp0s20f3) interface using hostapd on the 2.4 GHz band (channel 6, 2437 MHz).

# Configuration
Create or update /etc/hostapd/hostapd-fixed.conf with the following configuration:
```
interface=wlp0s20f3
driver=nl80211
ssid=AwesomeWifi
hw_mode=g
channel=6
country_code=US
ieee80211n=1
ht_capab=[SHORT-GI-20][DSSS_CCK-40]
ieee80211ax=1
he_oper_chwidth=0
he_oper_centr_freq_seg0_idx=6
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_passphrase=123@QWEasdzxc
rsn_pairwise=CCMP
ieee80211w=2
```
<h2>Ensure Interface Availability</h2>
Free the wlp0s20f3 interface to avoid "Device or resource busy" errors:

```
sudo systemctl stop NetworkManager
sudo nmcli device disconnect wlp0s20f3
sudo ip link set wlp0s20f3 down
sudo ip link set wlp0s20f3 up
```
<h2>3. Verify Regulatory Domain</h2>


```
iw reg get
sudo iw reg set US
```

<h2>Compile the Hostapd for 802.11AX</h2>

```
sudo apt install build-essential libssl-dev libnl-3-dev libnl-genl-3-dev pkg-config
cd ~/hostap/hostapd
cp defconfig .config
nano .config
```

Add to .config:

```
CONFIG_IEEE80211AX=y
CONFIG_IEEE80211N=y
CONFIG_SAE=y
```
Build and install:

```
make clean
make
sudo make install
```
<h2>7. Test the Wi-Fi 6 AP</h2>

Start hostapd with debug output:

```
sudo hostapd -d /etc/hostapd/hostapd-fixed.conf
```
