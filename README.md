# python-network-automation
📌 Overview
---
This repository contains Python-based network automation scripts developed to configure, manage, and maintain enterprise routers and switches.

The project focuses on automating routing and switching tasks using SSH-based device access with Netmiko.

## 🛠 Technologies Used
- Python 3
- Netmiko
- SSH
- Cisco IOS Devices (Lab Environment)
- GNS3 (Lab Testing)

## 1. Cloud to Router Connection Setup
This lab shows how to connect a local PC to a GNS3 router using VMware VMnet1 (Host-Only) through the GNS3 Cloud.

Router interface is configured with an IP address and SSH is enabled. Connectivity is verified using ping from the PC, allowing secure remote access and automation testing.

<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/cloud%20to%20router%20connection%20setup.png">

## ssh enable command
```bash
conf t
ip domain-name lab.local
username admin privilege 15 secret admin
crypto key generate rsa
1024
ip ssh version 2
line vty 0 4
login local
transport input ssh
do show ip ssh
```
⚠️ Note: SSH must be manually enabled on the router/switch before running the script. The device should be reachable and properly configured for remote login.

## python script

```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.137",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

     #  Check Current Prompt
    prompt = connection.find_prompt()
    print("Current Prompt is:", prompt)

    # Show Interface Before Config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.1 255.255.255.0",
        "no shutdown",
        "interface loopback1",
        "ip address 2.2.2.2 255.255.255.255",
        "no shutdown"
    ]
    # Send multiple configuration commands to the router (Netmiko automatically enters config mode)
    output = connection.send_config_set(config_commands)
    
    # Print the router response after applying configuration
    print("\n===== Configuration Output =====")
    print(output)
    
    # Save Configuration (Write Memory)
    # Mwthod 1
    save_output = connection.save_config()
    print("\n===== Save Config Output =====")
    print(save_output)

    # Method 2
    #  Interactive Command Example
    print("\n===== Save Config Output =====")
    save_output = connection.send_command_timing(
        "copy running-config startup-config"
    )
    # Step 1: Destination filename?
    if "Destination filename" in save_output:
        save_output += connection.send_command_timing("\n")
        print(save_output)

    #  Verify After Config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
 ```
## Run program 
- open cmd on host pc
- do folder path
- like: C:\Users\kurre computers\Documents>
- type python test.py (file name)
- like: C:\Users\kurre computers\Documents>python test.py

  ## 2. DHCP Configuration
  <img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/DHCP%20AUTOMATION.png">

## python script
```bash
from netmiko import ConnectHandler

# Router Details connection establish
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 10.0.0.1 255.0.0.0",
        "no shutdown",
        "exit",
        "ip dhcp pool HR",
        "network 10.0.0.0 255.0.0.0",
        "dns-server 8.8.8.8",
        "default-router 10.0.0.1"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output = connection.send_command("show run")
    print("\n===== DHCP Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 3. Static Routing Configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/stactic%20routing%20witch%20automation.png">

## script router 1
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
     "interface f0/0",
     "ip address 192.168.10.1 255.255.255.0",
     "no shut",
     "interface f1/0",
     "ip address 192.168.20.1 255.255.255.0",
     "no shut",
     "interface f2/0",
     "ip address 192.168.1.1 255.255.255.0",
     "no shut",
     "exit",
     "ip route 192.168.30.0 255.255.255.0 192.168.1.2"

 ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output = connection.send_command("show ip route")
    print("\n===== Static Routing Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## script router 2
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
     "interface f0/0",
     "ip address 192.168.30.1 255.255.255.0",
     "no shut",
     "interface f1/0",
     "ip address 192.168.1.2 255.255.255.0",
     "no shut",
     "exit",
     "ip route 192.168.10.0 255.255.255.0 192.168.1.1",
     "ip route 192.168.20.0 255.255.255.0 192.168.1.1"

 ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output = connection.send_command("show ip route")
    print("\n===== Static Routing Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 4. RIP configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/rip.png">

## script
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
       "router rip",
       "version 2",
       "no auto-summary",
       "network 192.168.10.0"
           
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output1 = connection.send_command("show ip protocol")
    output2 = connection.send_command("show ip route")
    print("\n===== RIP Routing Configuration =====")
    print(output1)
    print(output2)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 5. EGRP configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/EIGRP%20AUTOMATION.png">

## python script router 1
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.1 255.255.255.0",
        "no shutdown",
        "exit",
        "router eigrp 100",
        "no auto-summary",
        "network 192.168.10.0 0.0.0.255"
        
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output1 = connection.send_command("show ip protocol")
    output2 = connection.send_command("show ip route")
    print("\n===== EIGRP Routing Configuration =====")
    print(output1)
    print(output2)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 6. OSPF configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/ospf.png">

## script router 1
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.1 255.255.255.0",
        "no shutdown",
        "exit",
        "router ospf 1",
        "network 192.168.10.0 0.0.0.255 area 0"
        
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Show Running Config
    output1 = connection.send_command("show ip protocol")
    output2 = connection.send_command("show ip route")
    print("\n===== OSPF Routing Configuration =====")
    print(output1)
    print(output2)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 7. HSRP configuration (cisco protocol)
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/HSRP%20AUTOMATION.png">

## script router 1 (Active)
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.1",
        "no shut",
        "interface f1/0",
        "standby version 2",
        "standby 1 ip 192.168.10.254",  # virtual ip
        "standby 1 priority 100",
        "standby 1 preempt",
        "no shutdown"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Interface check
    output = connection.send_command("show ip interface brief")
    print("\n===== Show interface =====")
    print(output)

    # Configuration check
    output = connection.send_command("show standby brief")
    print("\n===== HSRP Configuration R1 =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## script router 2 (Standby)
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
         "interface f1/0",
        "ip address 192.168.10.3",
        "no shut",
        "interface f1/0",
        "standby version 2",
        "standby 1 ip 192.168.10.254",  # virtual ip
        "standby 1 priority 90",
        "standby 1 preempt",
        "no shutdown"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Interface check
    output = connection.send_command("show ip interface brief")
    print("\n===== Show interface =====")
    print(output)
    
    # Configuration check
    output = connection.send_command("show standby brief")
    print("\n===== HSRP Configuration R2 =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 8. VRRP configuratin (opensource protocol)
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/VRRP%20auomation.png">

## script router 1 (master router)
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.1",
        "no shut",
        "interface f1/0",
        "vrrp 1 ip 192.168.10.254",  # virtual ip
        "vrrp 1 priority 100",
        "vrrp 1 preempt",
        "no shutdown"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Interface check
    output = connection.send_command("show ip interface brief")
    print("\n===== Show interface =====")
    print(output)
    
    # Verify Again
    output = connection.send_command("show vrrp brief")
    print("\n===== VRRP Configuration R1 =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## script router 2 (backup router)
```bash
from netmiko import ConnectHandler

# Router Details
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
        "interface f1/0",
        "ip address 192.168.10.3",
        "no shut",
        "interface f1/0",
        "vrrp 1 ip 192.168.10.254",  # virtual ip
        "vrrp 1 priority 90",
        "vrrp 1 preempt",
        "no shutdown"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)
    
    # Interface check
    output = connection.send_command("show ip interface brief")
    print("\n===== Show interface =====")
    print(output)
    
    # Verify Again
    output = connection.send_command("show vrrp brief")
    print("\n===== VRRP Configuration R2 =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```

## 9. switchport vlan and trunk configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/switchport%20trunk%20automation.png">

## script switch 1
```bash
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")


    # Configuration Commands
    config_commands = [
        "interface range e0/1 -2",
        "switchport mode access",
        "switchport access vlan 10",
        "exit",
        "vlan 10",
        "name HR",
        "exit",
        "interface range e0/3,e1/0",
        "switchport mode access",
        "switchport access vlan 20",
        "exit",
        "vlan 20",
        "name SALES",
        "exit",
        "interface range e1/1 -2",
        "switchport mode access",
        "switchport access vlan 30",
        "exit",
        "vlan 30",
        "name ENGINEERING",
        "exit",
        # Trunk configuration
        "interface e1/3",
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "switchport trunk allowed 10,20,30",
        "switchport trunk native vlan 1001"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Show VLAN
    output = connection.send_command("show vlan brief")
    print("\n===== VLAN Configuration =====")
    print(output)
    
     # Show VLAN
    output = connection.send_command("show interface trunk")
    print("\n===== VLAN TRUNK Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
## script switch 2
```bash
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.140",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")


    # Configuration Commands
    config_commands = [
        # vlan configuration
        "interface range e0/1 -2",
        "switchport mode access",
        "switchport access vlan 10",
        "exit",
        "vlan 10",
        "name HR",
        "exit",
        "interface range e0/3,e1/0",
        "switchport mode access",
        "switchport access vlan 20",
        "exit",
        "vlan 20",
        "name SALES",
        "exit",
        "interface range e1/1 -2",
        "switchport mode access",
        "switchport access vlan 30",
        "exit",
        "vlan 30",
        "name ENGINEERING",
        "exit",
        # Trunk configuration
        "interface e1/3",
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "switchport trunk allowed 10,20,30",
        "switchport trunk native vlan 1001"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Show VLAN
    output = connection.send_command("show vlan brief")
    print("\n===== VLAN Configuration =====")
    print(output)
    
     # Show VLAN
    output = connection.send_command("show interface trunk")
    print("\n===== VLAN TRUNK Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
## 10. Router on stick configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/Route%20on%20a%20stick.png">

## script on router 
```bash
from netmiko import ConnectHandler

# Router Details connection establish
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.141",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Check interface before config
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status Before Config =====")
    print(output)

    # Configuration Commands
    
    config_commands = [
        "interface f1/0",
        "no shutdown",
        "interface f1/0.10",
        "encapsulation dot1q 10",
        "ip address 192.168.10.1 255.255.255.0",
        "interface f1/0.20",
        "encapsulation dot1q 20",
        "ip address 192.168.20.1 255.255.255.0",
        "interface f1/0.30",
        "encapsulation dot1q 30",
        "ip address 192.168.30.1 255.255.255.0"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Again
    output = connection.send_command("show ip interface brief")
    print("\n===== Interface Status After Config =====")
    print(output)

    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 11. Etherchannel configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/etherchannel.png">

## python script switch 1
```bash
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")


    # Configuration Commands
    config_commands = [
        "interface range e0/1 -2",
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "channel-protocol pagp",  # cisco router protocol
       # "channel-protocol lacp",  All router proltocol 
        "channel-group 1 mode auto",
        "exit",
        "interface po1",
        "no shut",
        "switchport mode trunk"
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Show interface trunk configuration
    output = connection.send_command("show interface trunk")
    print("\n===== TRUNK Configuration =====")
    print(output)
    
    # Show Eterchannel Configuration
    output = connection.send_command("show etherchannel summary")
    print("\n===== ETHERCHANNEL Configuration =====")
    print(output)

    # Show Interface 
    output = connection.send_command("show ip interface brief")
    print("\n===== show INTERFACE  =====")
    print(output)

    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
## python script switch 2
```
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.140",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")


    # Configuration Commands
    config_commands = [
        "interface range e0/1 -2",
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "channel-protocol pagp",  # cisco router protocol
       # "channel-protocol lacp",  All router proltocol 
        "channel-group 1 mode desirable",
         "exit",
        "interface po1",
        "no shut",
        "switchport mode trunk"
    ]
  # Show interface trunk configuration
    output = connection.send_command("show interface trunk")
    print("\n===== TRUNK Configuration =====")
    print(output)
    
    # Show Eterchannel Configuration
    output = connection.send_command("show etherchannel summary")
    print("\n===== ETHERCHANNEL Configuration =====")
    print(output)

    # Show Interface 
    output = connection.send_command("show ip interface brief")
    print("\n===== show INTERFACE  =====")
    print(output)
    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
# 12. ACL configuration
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/ACL%20automation.png">

## script router 1 Standard ACL configuratin
```bash
from netmiko import ConnectHandler

# Router Details connection establish
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
        "ip access-list standard BLOCK_LAN1",
        "deny 192.168.50.0 0.0.0.255", # this ip deny
        "permit any",
        "exit",
        "interface f1/0",
        "ip access-group BLOCK_LAN1 out"  # interfce f1/0 ka out me legao
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Acl Configuration
    output = connection.send_command("show ip access-lists")
    print("\n===== ACL STANDARD Config =====")
    print(output)

   
    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## script router 2 Extended ACL configuration
```bash
from netmiko import ConnectHandler

# Router Details connection establish
router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.138",
    "username": "admin",
    "password": "admin",
}

try:
    # Connect to Router
    connection = ConnectHandler(**router)
    print("Connected Successfully\n")

    # Configuration Commands
    config_commands = [
        "ip access-list extended BLOCK_LAN1",
        "deny ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.255", # source ip destination ip me deny karo
        "permit ip any any",
        "exit",
        "interface f1/0",
        "ip access-group BLOCK_LAN1 in" # interfce f1/0 ka in me legao
    ]

    output = connection.send_config_set(config_commands)
    print("\n===== Configuration Output =====")
    print(output)

    # Verify Acl Configuration
    output = connection.send_command("show ip access-lists")
    print("\n===== ACL EXTENDED Config =====")
    print(output)

   
    # Disconnect
    connection.disconnect()
    print("\nDisconnected Successfully")

except Exception as e:
    print("Error:", e)
```
## 13. Automated switch configuration by pushing commands from a text file using Python and Netmiko over SSH.
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/switchport%20trunk%20automation.png">

## script switch 1
```bash
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.139",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")

 #  Push Config From File
    # (Make sure switchconfig.txt file same folder me ho)
    file_output = connection.send_config_from_file("switchconfig.txt")
    print("\n===== Configuration Output (From File) =====")
    print(file_output)

    # Show VLAN
    output = connection.send_command("show vlan brief")
    print("\n===== VLAN Configuration =====")
    print(output)
    
     # Show VLAN
    output = connection.send_command("show interface trunk")
    print("\n===== VLAN TRUNK Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
## script switch 2
```bash
from netmiko import ConnectHandler

# Router Details connection establish
switch = {
    "device_type": "cisco_ios",
    "host": "192.168.138.140",
    "username": "admin",
    "password": "admin",
}

try:
    # connect to switch
    connection = ConnectHandler(**switch)
    print("Connected Successfully")

    #  Push Config From File
    # (Make sure switchconfig.txt file same folder me ho)
    file_output = connection.send_config_from_file("switchconfig.txt")
    print("\n===== Configuration Output (From File) =====")
    print(file_output)

    # Show VLAN
    output = connection.send_command("show vlan brief")
    print("\n===== VLAN Configuration =====")
    print(output)
    
     # Show VLAN
    output = connection.send_command("show interface trunk")
    print("\n===== VLAN TRUNK Configuration =====")
    print(output)

    # Disconnect
    connection.disconnect()

except Exception as e:
    print("Error:", e)
```
## switch configuration text file
```bash
        interface range e0/1 -2
        switchport mode access
        switchport access vlan 10
        exit
        vlan 10
        name HR
        exit
        interface range e0/3,e1/0
        switchport mode access
        switchport access vlan 20
        exit
        vlan 20
        name SALES
        exit
        interface range e1/1 -2
        switchport mode access
        switchport access vlan 30
        exit
        vlan 30
        name ENGINEERING
        exit
        # Trunk configuration
        interface e1/3
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed 10,20,30
        switchport trunk native vlan 1001
```
## 14. Automated secure retrieval and backup of router running configurations.
<img align="right" src="https://github.com/kumleshkurre/python-network-automation/blob/main/screenshot/cloud%20to%20router%20connection%20setup.png">

## scrept
```bash
from netmiko import ConnectHandler
from datetime import datetime

router = {
    "device_type": "cisco_ios",
    "host": "192.168.138.137",
    "username": "admin",
    "password": "admin",
}

connection = ConnectHandler(**router)

# Get Running Config
output = connection.send_command("show running-config")

# Create file name with timestamp
timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
filename = f"R1_backup_{timestamp}.txt" # f-string syntax (formatted string literal)

# Save to file
with open(filename, "w") as router_backup_file:
    router_backup_file.write(output)

print("Backup Saved Successfully")

connection.disconnect()
```
## ⚠️ Important Notes (Practice Project)

- This project is created for learning and practice purposes only.
- All router and switch configurations are tested in a lab environment (GNS3).
- The usernames and passwords used in the scripts (e.g., admin/admin) are for practice only and are not real or production credentials.
- Do NOT upload real device credentials to GitHub.
- Never expose working router/switch usernames, passwords, or IP addresses from production environments.
- Always use environment variables or secure credential management methods in real-world projects.

## 👨‍💻 Author

- Kumlesh Kurre
- Network Engineer | Python Network Automation Enthusiast
- CCNA-Level Networking & Infrastructure Automation

## ⭐ Support
If you like this project, please ⭐ star the repository to support my work!
