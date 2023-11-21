# ROS through WIFI on diffenrent computers

We had a problem that didn't allow us to have access of the ROS's topics using a diffenrent computer. Turns out that this was because of the configuration of robot's internet config. 

To solve this we followed the tutorial presented in [here](https://gist.github.com/dbaldwin/b31835f87f16450a956cf3c89e15a289).

## GO1 Unitree Robot Network Configuration
This guide provides steps to configure the network settings on a Unitree GO1 robot's Raspberry Pi and a Linux host for efficient communication.

### Steps on the Robot's Raspberry Pi

SSH into the Raspberry Pi: Securely log into the Raspberry Pi on the robot using SSH (Secure Shell)

``` ssh pi@192.168.12.1 ```

Open the sysctl.conf file:

``` sudo nano /etc/sysctl.conf ```

Uncomment the line ``` net.ipv4.ip_forward=1 ```. This Enables IP forwarding to allow the Raspberry Pi to route traffic between its network interfaces.

Reload the sysctl configuration to apply the updated system configuration.

```sudo sysctl -p```

### Configure iptables for Network Address Translation (NAT)

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
### Configuration on Your Linux Host
Set Default Gateway to route all traffic through the robot's network, enabling your Linux host to communicate with the robot.

```sudo route add default gw 192.168.12.1```




