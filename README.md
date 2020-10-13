# Raspberry Pi のネットワーク設定
ここではRaspberry Pi 3, OS はRaspbian Jessie を想定している．
## 有線LAN の場合
Raspbian Jessie では，NICデバイスの定義と設定を行う場所が別れている．
```
// NIC デバイスの定義
$ sudo vim /etc/network/interfaces
  | auto eth0
  | iface eth0 inet manual

// ネットワーク設定
$ sudo vim /etc/dhcpcd.conf
  | interface eth0
  | static ip_address=192.168.10.150/24
  | static routers=192.168.10.1
  | static domain_name_servers=192.168.10.1

// 再起動
$ shutdown -r now
```

## 無線LAN の場合
```
$ sudo vim /etc/wpa_supplicant/wpa_supplicant.conf
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
$ shutdown -r now
```

# ブリッジデバイスの作成方法
Raspberry Pi のUSB ポートの一つに外付けNIC を接続し，NIC デバイスとして"eth0, eth1" が存在している時，Raspberry Pi をブリッジデバイスとして利用する方法をここに示す．
## ブリッジデバイスの作成
```
// まずはbridge-utils のインストール
$ sudo apt-get install bridge-utls

// IPフォワーディングを許可
$ systemctl net.ipv4.ip_foward=1
$ systemctl -p

// ブリッジデバイスの作成
$ brctl addbr br0

// ブリッジデバイスbr0 に転送するNIC の設定
$ brctl addif br0 eth0
$ brctl addif br0 eth1
$ brctl show
```

## NIC の設定
```
// ブリッジデバイスの定義
$ sudo vim /etc/network/interfaces
  | auto br0
  | iface br0 inet manual
  | 
  | bridge_ports eth0 eth1
  | bridge_stp off
  | bridge_maxwait 3

// ネットワーク設定
$ sudo vim /etc/dhcpcd.conf
  | # eth0, eth1 の設定
  | interface eth0
  | static ip_address=0.0.0.0
  | 
  | interface eth1
  | static ip_address=0.0.0.0
  |
  | # あるいは以下の様に書いてもok.
  | # denyinterfaces eth0 eth1
  | 
  | # br0 の設定
  | interface br0
  | static ip_address=192.168.10.100/24
  | static routers=192.168.10.1
  | static domain_name_servers=192.168.10.1

// 再起動
$ shutdown -r now
```
