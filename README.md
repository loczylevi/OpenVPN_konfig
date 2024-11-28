# OpenVPN_konfig

## Debian op rendszer (*letarhálása*) leszedése:

https://www.debian.org/

alapvető __telepités__ (*lehetöleg asztali felület nélkül*) felhasználó, proxy, domain név megadás...
### Ajánlott zene telepités közben:

https://www.youtube.com/watch?v=MK6TXMsvgQg

ssh check command:

```bash
su
apt update
apt install openssh-server
systemctl status ssh
systemctl status ssh
```
### UFW telepítése és alapvető konfigurációja

```bash
su
apt update
apt install ufw
```

UFW debug mert nem találja a PATH-et:

```bash
dpkg -l | grep ufw
export PATH=$PATH:/usr/sbin
ufw status
ufw enable
ufw status
ufw status verbose
```

UFW beállitása:

```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 22/tcp
```

UFW chech commandok:


```bash
ufw status verbose
ufw status numbered
systemctl status ufw
```

# OpenVPN konfigurálás *végre valahára*

```bash
apt update
apt install openvpn easy-rsa
make-cadir ~/openvpn-ca
cd ~/openvpn-ca
nano vars
```
A fájlban módosíthatod a következőket:

export KEY_COUNTRY="US" — Itt cseréld ki az országkódot.
export KEY_PROVINCE="CA" — Az állam/megye kódja.
export KEY_CITY="SanFrancisco" — A város neve.
export KEY_ORG="MyOrg" — A szervezet neve.
export KEY_EMAIL="me@example.com" — Email cím.

### tanusitványok generálása

```bash
./easyrsa init-pki
./easyrsa build-ca
./easyrsa gen-req server nopass
./easyrsa sign-req server server
./easyrsa gen-dh
/usr/sbin/openvpn --genkey secret ta.key
```

VPN szerver beáálitásai:


```bash
nano /etc/openvpn/server.conf
```

Itt módosítsd az alábbi sorokat:

Az ca, cert, key, és dh fájlok elérési útjait a saját fájljaidra cseréld:

ca /etc/openvpn/keys/ca.crt
cert /etc/openvpn/keys/server.crt
key /etc/openvpn/keys/server.key
dh /etc/openvpn/keys/dh2048.pem

Aktiváld a HMAC kulcsot (ha létrehoztad):

```bash
tls-auth /etc/openvpn/keys/ta.key 0
```

Az IP-cím tartomány, amit az OpenVPN szerver oszt ki a klienseknek:

server 10.8.0.0 255.255.255.0

Mentés és kilépés.

```bash
sudo ufw allow 1194/udp
sudo ufw enable
```

IP Forwarding engedélyezése:

A rendszernek engedélyezni kell az IP forwardingot, hogy a VPN-kliens gépek internethez férhessenek:

Szerkeszd a /etc/sysctl.conf fájlt:
```bash
nano /etc/sysctl.conf
```
És add hozzá (vagy oldd fel a kommentet) a következő sort:

__net.ipv4.ip_forward=1__

Ezután töltsd újra a konfigurációt:

```bash
sudo sysctl -p
```

# openvpb szerver elinditás:

```bash
systemctl start openvpn@server
systemctl status openvpn@server
systemctl enable openvpn@server
systemctl status openvpn@server.service
journalctl -xeu openvpn@server.service
```




