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

## 🌐 Cloud to Router Connection Setup
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
