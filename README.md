
- [ROS through WIFI on diffenrent computers(robot's network)](#ros-through-wifi-on-diffenrent-computersrobots-network)
  - [GO1 Unitree Robot Network Configuration](#go1-unitree-robot-network-configuration)
    - [Steps on the Robot's Raspberry Pi](#steps-on-the-robots-raspberry-pi)
    - [Configure iptables for Network Address Translation (NAT)](#configure-iptables-for-network-address-translation-nat)
    - [Configuration on Your Linux Host](#configuration-on-your-linux-host)
- [Connection Bitdog-Local Wifi(local network)](#connection-bitdog-local-wifilocal-network)
  - [Driver installation](#driver-installation)
  - [Connection tutorial](#connection-tutorial)


## ROS through WIFI on diffenrent computers(robot's network)

We had a problem that didn't allow us to have access of the ROS's topics using a diffenrent computer. Turns out that this was because of the configuration of robot's internet config. 

To solve this we followed the tutorial presented in [here](https://gist.github.com/dbaldwin/b31835f87f16450a956cf3c89e15a289).

### GO1 Unitree Robot Network Configuration
This guide provides steps to configure the network settings on a Unitree GO1 robot's Raspberry Pi and a Linux host for efficient communication.

#### Steps on the Robot's Raspberry Pi

SSH into the Raspberry Pi: Securely log into the Raspberry Pi on the robot using SSH (Secure Shell)

``` ssh pi@192.168.12.1 ```

Open the sysctl.conf file:

``` sudo nano /etc/sysctl.conf ```

Uncomment the line ``` net.ipv4.ip_forward=1 ```. This Enables IP forwarding to allow the Raspberry Pi to route traffic between its network interfaces.

Reload the sysctl configuration to apply the updated system configuration.

```sudo sysctl -p```

#### Configure iptables for Network Address Translation (NAT)

Flush existing iptables rules to clear all existing rules in iptables to start fresh.

```
sudo iptables -F 
sudo iptables -t nat -F
```
Set up NAT routing to Configure NAT to translate network addresses for outgoing connections and Allow forwarding of packets between the WLAN (wlan1) and Ethernet (eth0) interfaces.

```
sudo iptables -t nat -A POSTROUTING -o wlan1 -j MASQUERADE
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i wlan1 -o eth0 -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o wlan1 -j ACCEPT
```
#### Configuration on Your Linux Host
Set Default Gateway to route all traffic through the robot's network, enabling your Linux host to communicate with the robot.

```sudo route add default gw 192.168.12.1```




## Connection Bitdog-Local Wifi(local network)

### Driver installation
To connect the robot to our local network(PMEC-FENIX) we used a [USB Wireless Module](https://www.tp-link.com/br/home-networking/adapter/archer-t4u/) and followed the [tutorial](https://askubuntu.com/questions/1018375/how-do-i-install-driver-for-rtl88x2bu) described bellow:

Clone the Driver's Repository inside Raspberry Pi:

```
git clone https://github.com/RinCat/RTL88x2BU-Linux-Driver.git

```
Then Compile (build) and install:

```
make
sudo make install
```

Therefore, To use it, connect the module to RaspberryPi's USB port and execute:

```
sudo modprobe 88x2bu 
```

To make it permanent, add the module to the list of modules loaded at boot time:

```
sudo bash -c 'echo '88x2bu' >> /etc/modules'
```

### Connection tutorial
Disclaimer: If you use this method and want a connection with one of the Jetsons, you will need to connect to the Raspberry Pi first, and then to the Jetson. This means that in some cases where you need a direct connection to the Jetson you should not use this method, you will need to connect to the Raspberry Pi's wifi `cachorro_robo`, enabling direct connections.

Now, to connect to the Robot-Work-Environment(where we use buildMap and Patrol) we have to enter to RaspberryPi first through ssh(robot's network) to discover the IP of the local network.
```
ssh pi@192.168.12.1
ifconfig
```
Now we do the same process again using the new IP network to enter RaspberryPI and then the Jetson Xavier:

```
ssh pi@w.x.y.z
ssh unitree@192.168.123.15
``````
