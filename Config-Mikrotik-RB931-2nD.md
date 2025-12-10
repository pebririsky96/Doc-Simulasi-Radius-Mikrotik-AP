# 2025-12-10 13:37:35 by RouterOS 7.20.2
# software id = P22F-XEMU
#
# model = RB931-2nD
# serial number = HD3084Y7BKJ
/interface bridge
add name=Bridge1
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n disabled=no frequency=auto mode=\
    ap-bridge ssid=TEST-RADIUS
/interface ethernet
set [ find default-name=ether1 ] name=ether1-WAN
set [ find default-name=ether3 ] name=ether3-LAN
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
add authentication-types=wpa2-eap eap-methods=peap mschapv2-username=\
    pebri_risky17@student.uns.ac.id name=profile-8021x supplicant-identity=\
    MikroTik
add authentication-types=wpa-psk,wpa2-psk eap-methods="" mode=dynamic-keys name=\
    profile-indihome supplicant-identity=""
add authentication-types=wpa-psk eap-methods="" mode=dynamic-keys name=cek \
    supplicant-identity=""
/ip hotspot profile
add dns-name=test.hotspot.radius hotspot-address=192.168.100.1 login-by=\
    mac,cookie,http-chap,http-pap,mac-cookie name=hsprof1 use-radius=yes
/ip pool
add name=dhcp_pool0 ranges=192.168.222.2-192.168.222.254
add name=hs-pool-1 ranges=192.168.100.2-192.168.100.254
/ip dhcp-server
# Interface not running
add address-pool=hs-pool-1 interface=wlan1 name=dhcp1
/ip hotspot
add address-pool=hs-pool-1 disabled=no interface=wlan1 name=hotspot1 profile=\
    hsprof1
/routing ospf instance
add disabled=no name=default-v2
/routing ospf area
add disabled=yes instance=default-v2 name=backbone-v2
/interface bridge port
add bridge=Bridge1 disabled=yes interface=ether3-LAN
add bridge=Bridge1 interface=ether1-WAN
add bridge=Bridge1 interface=ether2
/ip firewall connection tracking
set udp-timeout=10s
/ip neighbor discovery-settings
set discover-interface-list=!dynamic
/ip settings
set max-neighbor-entries=8192
/ipv6 settings
set disable-ipv6=yes max-neighbor-entries=8192
/interface ovpn-server server
add auth=sha1,md5 mac-address=FE:CB:41:F4:75:91 name=ovpn-server1
/ip address
add address=192.168.100.1/24 interface=wlan1 network=192.168.100.0
/ip dhcp-client
add default-route-tables=main interface=Bridge1
/ip dhcp-server network
add address=192.168.100.0/24 comment="hotspot network" gateway=192.168.100.1
/ip dns
set allow-remote-requests=yes servers=8.8.8.8
/ip firewall filter
add action=passthrough chain=unused-hs-chain comment="place hotspot rules here" \
    disabled=yes
/ip firewall nat
add action=passthrough chain=unused-hs-chain comment="place hotspot rules here" \
    disabled=yes
add action=masquerade chain=srcnat
add action=masquerade chain=srcnat comment="masquerade hotspot network" \
    src-address=192.168.100.0/24
/ip ipsec profile
set [ find default=yes ] dpd-interval=2m dpd-maximum-failures=5
/radius
add address=10.10.0.225 radsec-timeout=10ms require-message-auth=no service=\
    hotspot timeout=10ms
/radius incoming
set accept=yes
/routing bfd configuration
add disabled=no interfaces=all min-rx=200ms min-tx=200ms multiplier=5
/system clock
set time-zone-name=Asia/Jakarta
/system identity
set name=RouterOS