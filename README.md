# Raspberry Pi のネットワーク設定
## 有線LAN の場合
Raspberry Pi Jessie では，NICデバイスの定義と設定を行う場所が別れている．
```
// NIC デバイスの定義
sudo vim /etc/network/interfaces
  | auto eth0
  | iface eth0 inet manual

// ネットワーク設定
sudo vim /etc/dhcpcd.conf
  | interface eth0
  | static ip_address=192.168.10.150/24
  | static routers=192.168.10.1
  | static domain_name_servers=192.168.10.1

// 再起動
shutdown -r now
```

## 無線LAN の場合
```
sudo vim /etc/wpa_supplicant/wpa_supplicant.conf
  | ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
  | update_config=1
  | 
  | # ここにwifi 設定を書く
  | country=JP
  | network={
  | ssid="your_ssid"
  | psk="your_password"
  | }

// 再起動
shutdown -r now
```

# ブリッジデバイスの作成方法

