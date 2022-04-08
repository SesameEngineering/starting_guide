![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.001.png)

**LEOPARD V2 ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.001.png)**

![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.002.png)

**Starting Guide** 

**(version 1.0)** 

**Table of content** 

Table of content  2 Introduction  3 Prerequisites  3 Server commissioning  3 IPMI Management  4 Check IPMI interface is up  4 Perform IPMI requests  5 BIOS setup access  7 Hardware maintenance  8 

**Introduction** 

Leopard V2 servers is a single 1.5 U (1OU) and ⅓ width barebone for Open Rack V2 

Each motherboard have a SFP+ interface based on Mellanox CX4421A-ACQN chipset with Out-of-band IPMI remote management. 

Out-of-the-box, servers does not have any operating system installed. 

**Prerequisites** 

IPMI interfaces are by default configured to get an IP address through DHCP. So, servers remote interfaces have to be wired on a network with a DHCP server configured to give them IP address. 

**Server commissioning** 

First, SFP+ for remote management must be plugged. It is important to do it prior to plug the power cable because BMC chips on both motherboards will start immediately after the power cable is plugged and they will try to perform the DHCP sequence to get their IP addresses. If network is not reachable already the BMC startup will fail. 

![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.003.png) *After power on, it usually takes less a minute for IPMI interface to be up and running. ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.004.png)*

**IPMI Management** 

**Check IPMI interface is up** 

On servers, IPMI Out-of-band interface is by default activated in the BIOS and the interface should be able to receive UDP IPMI requests. IPMI calls can be done for instance using a client like IPMITool1. IPMITool is available for many operating systems. In the rest of this document, IPMI request examples will be formalized using this tool. 

*The remote management is only available using the IPMI interface. There is no Web interface embedded in the BMC. ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.005.png)![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png)*

Here comes some checks that can be done to ensure IPMI Out-of-band interface is up and running. 

1. **Check network** 

First, be sure that you plugged network cable on server before you plug it on the bus bar. IPMI interface starts and tries to get an IP address through DHCP (also, that means a DHCP server must be present on the network). 

2. **Look for IPMI IP addresses** 

Then, check that IPMI interface have an IP address. You can achieve that looking at leases in your DHCP server or using a nmap command to look for IP addresses listening on UDP 623. 

Example of nmap command to discover Leopard’s IPMI interfaces 

$ nmap -sU -p 623 192.168.0.0/24 ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.007.png)



|||||||
| :- | :- | :- | :- | :- | :- |
||![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png)||1. *Don't forget to replace the network address by your own* |
||||2. *With a network bigger than **/24** this can be long* ||
|||
|||
|||
1 https://sourceforge.net/projects/ipmitool/ 

Using this command, you should get an output looking something like 

Nmap scan report for DCMI089E013CEC63 (192.168.0.145) Host is up (0.074s latency). ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.008.png)

PORT    STATE         SERVICE 

623/udp open|filtered asf-rmcp 

MAC Address: 08:9E:01:3C:EC:63 (Quanta Computer) 

\-- 

Nmap scan report for 192.168.0.187 

Host is up (0.30s latency). 

PORT    STATE         SERVICE 

623/udp open|filtered asf-rmcp 

MAC Address: 08:9E:01:3C:FB:CF (Unknown) 

If so, UDP IPMI interfaces are up. 

*On  the  motherboard,  near  the  SFP+  connector,  a  sticker  ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.009.png)![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.010.png)![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.011.png)![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png)shows two MAC addresses, the ME MAC is the IPMI MAC  address and the P0 MAC  is the “normal” MAC address used  by the operating system for the network interface.*  

**Perform IPMI requests** 

IPMI requests examples using IPMITool 

![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png) *The default credentials are login: USERID and password: PASSW0RD ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.012.png)*

**1/ Checking the status of the server** 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password chassis power status Chassis Power is off ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.013.png)

**2/ Starting the server** 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password chassis power on Chassis Power Control: Up/On ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.014.png)

**3/ Checking the status of the server again** 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password chassis power status Chassis Power is on ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.015.png)

**4/ Show some metrics about the server** 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password sdr ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.016.png)

Outlet Cntr Temp | 36 degrees C      | ok Inlet Temp       | 27 degrees C      | ok PCH Temp         | 50 % degrees C    | ok P0 Therm Margin  | -33 degrees C     | ok P1 Therm Margin  | -24 degrees C     | ok P0 DIMM Temp     | no reading        | ns P1 DIMM Temp     | no reading        | ns 

HSC0 Input Power | 146 Watts         | ok HSC0 Input Volt  | 12.62 Volts       | ok CPU0 Tjmax       | 90 degrees C      | ok CPU1 Tjmax       | 90 degrees C      | ok SYS\_Fan0         | 2925 RPM          | ok SYS\_Fan1         | 3000 RPM          | ok TSOD SMBus Sts   | 0xe8              | ok CPU Therm Trip   | 0x36              | ok Pwr Thresh Evt   | 0xfb              | ok 

Battery Mon      | 0xe1              | ok SEL Status       | 0xb7              | ok DCMI Watchdog    | 0xa6              | ok Processor Fail   | 0x91              | ok Chassis Pwr Sts  | 0x76              | ok Thermal Limit 1  | Not Readable      | ns 

![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png) *Don’t forget to replace hostname, username and password by your own. ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.017.png)*

**BIOS setup access** 

Access to the BIOS configuration can be done in two differents ways: 

1. Using a screen and a keyboard 
1. Using IPMI Serial-Over-Lan (SOL) feature 
1. **Using a screen and a keyboard** 

First, the server is does not have a graphic card out-of-the-box. So, you need to install one on the PCIe slot. 

Then, follow this steps 

- Plug a screen on the graphic card 
- Plug an USB keyboard on the external USB port 
- Start the server (either with IPMI or with the red power button on the motherboard) 
- Wait for the server to boot. 
- Press DEL or F2 key to enter bios setup 
2. **Using IPMI Serial-Over-Lan (SOL) feature** 

The Serial-Over-Lan (SOL) feature allows to redirect the serial port the network interface encapsulated in IPMI protocol. As long as the redirection of BIOS to the serial is enabled (this is the default configuration), we can access to BIOS setup screens from a terminal using the SOL feature with IPMITool. 

To do so, we need first to ask the server to go into the BIOS setup after the next restart 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password chassis bootdev bios ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.018.png)Then, we either start or reset the server depending its current power status 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password power on ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.019.png)Or 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password power reset ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.020.png)

Finally, we activate the SOL and wait for the BIOS screen 

$ ipmitool -I lanplus -H 192.168.0.187 -U user -P password sol activate ![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.021.png)



|||||||
| :- | :- | :- | :- | :- | :- |
||![](Aspose.Words.4f4c71a2-85ee-41aa-b716-2acccd6d81fe.006.png)|||*Don’t forget to replace hostname, username and password by your own.* ||
|||
|||
Once this is done, you should get the BIOS setup screen into your console. You can then navigate through menus using your keyboard as usual. 

`       `Aptio Setup Utility - Copyright (C) 2018 American Megatrends, Inc. 

`    `Main  Advanced  IntelRCSetup  Server Mgmt  Boot  Save & Exit /----------------------------------------------------+-------------------------\ |  BIOS Information                                 ^|Set the Date. Use Tab    | |  BIOS Vendor             American Megatrends      \*|to switch between Data   | |  Core Version            5.11                     \*|elements.                | |  Compliancy              UEFI 2.4.0; PI 1.3       \*|                         | |  Grantley RC version     4.03                     \*|                         | |  Project Version         F06\_3B17                 \*|                         | |  Build Date and Time     03/16/2018 12:28:06      \*|                         | |                                                   \*|                         | |  CPU Information                                  \*|                         | |  Intel(R) Xeon(R) CPU E5-2678 v3 @ 2.50GHz        \*|-------------------------| 

|  CPU Signature           000306F2                 \*|><: Select Screen        | |  Processor Cores         12                       +|^v: Select Item          | |  Microcode Patch         0000003C                 +|Enter: Select            | |                                                   +|+/-: Change Opt.         | |  Memory Information                               +|F1: Help for more Keys   | |  Current Memory Speed    1866 MHz                 +|F8: Previous Values      | |  Total Memory            256 GB (DDR4)            +|F9: Optimized Defaults   | |                                                   v|F10: Save & Reset        | |                                                    |ESC: Exit                | \----------------------------------------------------+-------------------------/         Version 2.17.1249. Copyright (C) 2018 American Megatrends, Inc. 

**Hardware maintenance** 

For hardware maintenance, see online documentation https://opencompute.dozuki.com/c/Leopard\_for\_Open\_Rack\_V2 
*Leopard V2 Server - Starting Guide (version 1.2)                8* 
