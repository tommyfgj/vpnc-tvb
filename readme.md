1、
opkg update
opkg install openconnect

2、下载vpnc、证书到路由器

3、创建防火墙规则

uci set network.vpn0=interface
uci set network.vpn0.ifname=tun0
uci set network.vpn0.proto=none
uci set network.vpn0.auto=1
#Create firewall zone (named vpn) for new vpn0 network. By default, it will allow both incoming and outgoing connections being created within the VPN tunnel. Edit the defaults as required. This does not (yet) allow clients to access the LAN or WAN networks, but allows clients to communicate with services on the router and may allow connections between VPN clients if your OpenVPN server configuration allows. :!: If you are planning to use your OpenVPN client as a second (or replacement) WAN adapter, it's recommended that you reject incoming traffic by default:
uci set firewall.vpn=zone
uci set firewall.vpn.name=vpn
uci set firewall.vpn.network=vpn0
uci set firewall.vpn.input=ACCEPT #REJECT if using as WAN replacement
uci set firewall.vpn.forward=REJECT
uci set firewall.vpn.output=ACCEPT
uci set firewall.vpn.masq=1
#(Optional) If you plan to allow clients behind the VPN server to connect to computers within your LAN, you'll need to allow traffic to be forwarded between the vpn firewall zone and the lan firewall zone:
uci set firewall.vpn_forwarding_lan_in=forwarding
uci set firewall.vpn_forwarding_lan_in.src=vpn
uci set firewall.vpn_forwarding_lan_in.dest=lan
#And if you want to initiate connections to clients (or the internet) behind the VPN server, you'll need to allow traffic to be forwarded that direction as well.
uci set firewall.vpn_forwarding_lan_out=forwarding
uci set firewall.vpn_forwarding_lan_out.src=lan
uci set firewall.vpn_forwarding_lan_out.dest=vpn
#Commit the changes:
uci commit network
/etc/init.d/network reload
uci commit firewall
/etc/init.d/firewall reload


4、运行openconnect

openconnect -v -s /root/vpnc -c /root/t.crt -k /root/t.key hk-pccw.gate.huang.cool:444
 -b --no-cert-check --no-dtls 2>/dev/null
