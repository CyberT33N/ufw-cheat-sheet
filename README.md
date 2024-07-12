# ufw-cheat-sheet

<br><br>
<br><br>

## Logs
```shell
sudo tail -f /var/log/ufw.log
```




<br><br>
<br><br>
_____________________________________
_____________________________________
<br><br>
<br><br>

# Deny forward, incoming & outgoing 

The following code will not allow traffic from your own device e.g. WLAN/LAN to any other Port than DNS (53), local (192.168.0.0/16), NORDVPN(51820) & OPENVPN (1197). 

If connected to VPN we will allow only http, https & DNS:
```shell
sudo ufw reset
sudo ufw enable

# ---------------------------

# =====================
# ====== GLOBAL =======
# =====================

# Deny all
sudo ufw default deny forward
sudo ufw default deny incoming
sudo ufw default deny outgoing

# ---------------------------

# =====================
# ====== VPN ==========
# =====================

# If you want to allow any outgoing port
# sudo ufw allow out on nordlynx

# Allow only http, https & DNS
sudo ufw allow out on nordlynx to any port 80,443,53,8080 proto tcp
sudo ufw allow out on nordlynx to any port 80,443,53,8080 proto udp

# git ssh
sudo ufw allow out on nordlynx to any port 22 proto tcp

# docker - will be also used in minikube start- They use IPv6 Range
sudo ufw allow out on nordlynx from fe80::/64 to any port 22

# ---------------------------

# =====================
# ====== WLAN =========
# =====================

# Allow outgoing traffic from your device to DNS (53), NORDVPN(51820) & OPENVPN (1197)
sudo ufw allow out on wlp0s20f3 to any port 53,51820,1197 proto udp

# Allow NTP
# sudo ufw allow out on wlp0s20f3 to any port 123 proto udp comment 'allow NTP'

# Allow local networks (optional)
sudo ufw allow out on wlp0s20f3 to 192.168.0.0/16 comment 'allow local network'

# ---------------------------

sudo ufw status verbose
sudo ufw enable
```
- If it is not working and you can not connect to your vpn then try check_
  ```shell
  netstat -u
  netstat -t

  ```


If needed this code is a basic example for only enable http, https & DNS to your WLAN/LAN:
```shell
# Reset all Rules
sudo ufw reset

# ---------------------

# Enable Firewall
sudo ufw enable

# ---------------------

# deny all
sudo ufw default deny forward
sudo ufw default deny outgoing
sudo ufw default deny incoming

# ---------------------

# thunderbird
# sudo ufw allow 993

# thunderbird gmail send
# sudo ufw allow out 465

# thunderbird hotmail send
# sudo ufw allow out 587

# thunderbird
# sudo ufw allow out 993

# ---------------------

# qbittorrent
# sudo ufw allow 6969
# sudo ufw allow out 6969

# ---------------------

# dns
sudo ufw allow out 53

# ---------------------

# https & https
sudo ufw allow out http
sudo ufw allow out https
```


