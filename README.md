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
- The following code will work with wireguard & openvpn aswell with the nordvpn client with nordlynx technology. It will not allow traffic from your own device e.g. WLAN/LAN to any other Port than DNS (53), local (192.168.0.0/16), NORDVPN(51820) & OPENVPN (1197, 1194). 
- **If you want to allow outgoing you must add tcp 443 to your ethernet or wlan adapter**


If connected to VPN we will allow only http, https & DNS:
```shell
# Reset and enable UFW
sudo ufw --force reset
sudo ufw enable

# =====================
# ====== GLOBAL =======
# =====================

# Set default policies to deny all traffic
sudo ufw default deny forward comment 'Default deny forward traffic'
sudo ufw default deny incoming comment 'Default deny incoming traffic'
sudo ufw default deny outgoing comment 'Default deny outgoing traffic'

# Allow loopback traffic (essential)
sudo ufw allow in on lo
sudo ufw allow out on lo

# =====================
# ====== VPN ==========
# =====================

sudo ufw allow out on wg-CH-CZ-1 to any comment 'Allow ALL traffic over wg-CH-CZ-1'
sudo ufw allow out on wg-IS-CZ-1 to any comment 'Allow ALL traffic over wg-IS-CZ-1'
sudo ufw allow out on wg-CH-DK-2 to any comment 'Allow ALL traffic over wg-CH-DK-2'
sudo ufw allow out on main_os-CH-AT-1 to any comment 'Allow ALL traffic over main_os-CH-AT-1'
sudo ufw allow out on nordlynx to any comment 'Allow ALL traffic over nordlynx'
sudo ufw allow out on nordtun to any comment 'Allow ALL traffic over nordtun'

# Git SSH rules
sudo ufw allow out on nordlynx to any port 22 proto tcp comment 'Allow Git SSH over nordlynx'
sudo ufw allow out on nordtun to any port 22 proto tcp comment 'Allow Git SSH over nordtun'
sudo ufw allow out on main_os-CH-AT-1 to any port 22 proto tcp comment 'Allow Git SSH over main_os-CH-AT-1'
sudo ufw allow out on wg-CH-CZ-1 to any port 22 proto tcp comment 'Allow Git SSH over main_os-CH-AT-1'

# Docker IPv6 rules
sudo ufw allow out on nordlynx from fe80::/64 to any port 22 comment 'Allow Docker IPv6 over nordlynx'
sudo ufw allow out on nordtun from fe80::/64 to any port 22 comment 'Allow Docker IPv6 over nordtun'
sudo ufw allow out on main_os-CH-AT-1 from fe80::/64 to any port 22 comment 'Allow Docker IPv6 over main_os-CH-AT-1'
sudo ufw allow out on wg-CH-CZ-1 from fe80::/64 to any port 22 comment 'Allow Docker IPv6 over main_os-CH-AT-1'

# OpenVPN rules
sudo ufw allow out on tun0 comment 'Allow all traffic on tun0'
sudo ufw allow out on tun1 comment 'Allow all traffic on tun1'
sudo ufw allow out 1194/udp comment 'Allow OpenVPN UDP'
sudo ufw allow out 1194/tcp comment 'Allow OpenVPN TCP'

# =====================
# ====== MINIKUBE =====
# =====================

# Allow Minikube IP access
sudo ufw allow out on br-66dceb3ba20e to 192.168.49.2 comment 'Allow Minikube IP access'

# Kubernetes API Server
sudo ufw allow out on br-66dceb3ba20e to any port 8443 proto tcp comment 'Allow Kubernetes API Server'

# NodePorts for Kubernetes Services
sudo ufw allow out on br-66dceb3ba20e to any port 30000:32767 proto tcp comment 'Allow NodePorts for Kubernetes Services'

# etcd communication
sudo ufw allow out on br-66dceb3ba20e to any port 2379:2380 proto tcp comment 'Allow etcd communication'

# DNS rules for Minikube
sudo ufw allow out on br-66dceb3ba20e to any port 53 proto udp comment 'Allow DNS UDP'
sudo ufw allow out on br-66dceb3ba20e to any port 53 proto tcp comment 'Allow DNS TCP'

# Kubelet communication
sudo ufw allow out on br-66dceb3ba20e to any port 10250 proto tcp comment 'Allow Kubelet communication'

# SSH access for Minikube
sudo ufw allow out on br-66dceb3ba20e to any port 22 proto tcp comment 'Allow SSH access'

# =====================
# ====== WLAN =========
# =====================

# DNS rules - DNS Auflösung (um VPN Servernamen zu finden, falls nicht IP genutzt wird)
sudo ufw allow out on wlp0s20f3 to any port 53 proto udp comment 'Allow DNS UDP over WLAN'
sudo ufw allow out on wlp0s20f3 to any port 53 proto tcp comment 'Allow DNS TCP over WLAN'

# 2. WireGuard Verbindung zum Server wg-CH-CZ-1 (IP und Port aus .conf)
sudo ufw allow out on wlp0s20f3 to 185.159.157.217 port 51820 proto udp comment 'Allow WG wg-CH-CZ-1 connection via WLAN'
sudo ufw allow out on wlp0s20f3 to 62.169.136.239 port 51820 proto udp comment 'Allow WG wg-CH-DK-2 connection via WLAN'

# 3. WireGuard Verbindung zu anderen Servern (!!! PRÜFE die IPs/Ports in deren .conf Dateien !!!)
# Beispiel: Finde Endpoint IP/Port für wg-IS-CZ-1 in /etc/wireguard/wg-IS-CZ-1.conf
# sudo ufw allow out on wlp0s20f3 to <IP_VON_WG_IS_CZ_1> port <PORT_VON_WG_IS_CZ_1> proto udp comment 'Allow WG wg-IS-CZ-1 connection via WLAN'
# Beispiel für main_os-CH-AT-1
# sudo ufw allow out on wlp0s20f3 to <IP_VON_main_os-CH-AT-1> port <PORT_VON_main_os-CH-AT-1> proto udp comment 'Allow WG main_os-CH-AT-1 connection via WLAN'
# Beispiel für NordLynx (falls du es direkt nutzt, Port ist oft 51820, IP variiert stark!) - Besser über die App steuern?

# 4. Notwendige Kontrollverbindung (laut Logs)
sudo ufw allow out on wlp0s20f3 to 185.70.42.36 port 443 proto tcp comment 'Allow Proton control connection via WLAN'
# --> Falls Logs *andere* IPs auf Port 443 (TCP oder UDP) zeigen, die für VPN nötig sind, spezifisch hinzufügen!

# Wireguard
sudo ufw allow out on wg-IS-CZ-1 to any comment 'Allow all outgoing traffic over wg-IS-CZ-1'
sudo ufw allow out on wg-CH-CZ-1 to any comment 'Allow all outgoing traffic over wg-CH-CZ-1'
sudo ufw allow out on main_os-CH-AT-1 to any comment 'Allow all outgoing traffic over main_os-CH-AT-1'
sudo ufw allow out on wg-CH-DK-2 to any comment 'Allow all outgoing traffic over wg-CH-DK-2'

# NordVPN
sudo ufw allow out on wlp0s20f3 to any port 51820 proto udp comment 'Allow NordVPN UDP over WLAN'
sudo ufw allow out on wlp0s20f3 to any port 51820 proto tcp comment 'Allow NordVPN TCP over WLAN'

# OpenVPN
sudo ufw allow out on wlp0s20f3 to any port 1197 proto udp comment 'Allow OpenVPN UDP over WLAN'
sudo ufw allow out on wlp0s20f3 to any port 1197 proto tcp comment 'Allow OpenVPN TCP over WLAN'
sudo ufw allow out on wlp0s20f3 to any port 1194 proto udp comment 'Allow OpenVPN alternate UDP over WLAN'
sudo ufw allow out on wlp0s20f3 to any port 1194 proto tcp comment 'Allow OpenVPN alternate TCP over WLAN'

# IPsec/ISAKMP rules for VPN key exchange and tunnel management
sudo ufw allow out on wlp0s20f3 to any port 500 proto udp comment 'Allow IPsec ISAKMP UDP'
sudo ufw allow out on wlp0s20f3 to any port 500 proto tcp comment 'Allow IPsec ISAKMP TCP'

# IPsec NAT Traversal
sudo ufw allow out on wlp0s20f3 to any port 4500 proto udp comment 'Allow IPsec NAT Traversal UDP'
sudo ufw allow out on wlp0s20f3 to any port 4500 proto tcp comment 'Allow IPsec NAT Traversal TCP'

# Local network access
sudo ufw allow out on wlp0s20f3 to 192.168.0.0/16 comment 'Allow local network over WLAN'

# =====================
# ===== ETHERNET ======
# =====================

# DNS rules
sudo ufw allow out on enp0s31f6 to any port 53 proto udp comment 'Allow DNS UDP over Ethernet'
sudo ufw allow out on enp0s31f6 to any port 53 proto tcp comment 'Allow DNS TCP over Ethernet'

# NordVPN
sudo ufw allow out on enp0s31f6 to any port 51820 proto udp comment 'Allow NordVPN UDP over Ethernet'
sudo ufw allow out on enp0s31f6 to any port 51820 proto tcp comment 'Allow NordVPN TCP over Ethernet'

# OpenVPN
sudo ufw allow out on enp0s31f6 to any port 1197 proto udp comment 'Allow OpenVPN UDP over Ethernet'
sudo ufw allow out on enp0s31f6 to any port 1197 proto tcp comment 'Allow OpenVPN TCP over Ethernet'
sudo ufw allow out on enp0s31f6 to any port 1194 proto udp comment 'Allow OpenVPN alternate UDP over Ethernet'
sudo ufw allow out on enp0s31f6 to any port 1194 proto tcp comment 'Allow OpenVPN alternate TCP over Ethernet'

# Local network access
sudo ufw allow out on enp0s31f6 to 192.168.0.0/16 comment 'Allow local network over Ethernet'

# Final steps
sudo ufw status verbose
sudo ufw enable
```
- If it is not working and you can not connect to your vpn then try check_
  ```shell
  netstat -u
  netstat -t
  ```

<br><br>

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


