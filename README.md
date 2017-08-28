# Google Summer of Code 2017 - OpenWifi & SDWN

This is the documentation of two projects that were realized for Freifunk during Google Summer of Code 2017: [SDWN](SDWN.md) and [OpenWifi](https://berlin-open-wireless-lab.github.io/OpenWifiCore/#api). Together they form a centralized management system for Wifi deployments.

- OpenWifi manages the configuration of LEDE/OpenWRT nodes, it also provides an API to be used by external application to make changes to configurations, it also provides automatic node detection/registration
- SDWN keeps track of wireless resources and actors in the network such as access points, wireless clients, and their communication parameters.

For detailed documentation of both OpenWifi and SDWN, please refer to the documentation on their respective pages and in their source repositories. This page mainly describes how the two interact to realize a comprehensive monitoring and configuration tool of the wireless network.

## Getting the automatic node detection to work

For OpenWifi to be able to automatically detect new nodes the nodes need to have the [openwifi-boot-notifier](https://github.com/berlin-open-wireless-lab/openwifi-feed/tree/master/boot-notifier) installed. To do so please add this line to your feeds.conf in your build root:

    src-git openwifi https://github.com/berlin-open-wireless-lab/openwifi-feed.git

and update your feeds and install the package:

    ./scripts/feeds update
    ./scripts/feeds install openwifi-boot-notifier-umdns

Now you can select the package via `make menuconfig` in Administration->OpenWifi.

This notifier packages uses either a preconfigured server (in /etc/config/openwifi), a special dns entry or mdns to detect and register to the OpenWifi server application.

SDWN uses the [service api](https://berlin-open-wireless-lab.github.io/OpenWifiCore/#services) to interact with OpenWifi.

## Bootstrapping

This section explains how to integrate SDWN with OpenWifi. It assumes you have both the SDWN and OpenWifi controllers running. Since this mechanism uses the OpenWifi REST API, the SDWN controller must be able to reach it.

1. Build and install the OpenWifi integration app for SDWN. The [SDWN agent]() repository contains the source code and instructions on how to build and deploy SDWN applications.
2. on the ONOS shell, register the SDWN controller with OpenWifi: ```openwifi-register <OpenWifi REST API URL> <SDWN ADDRESS> <SDWN PORT> <NAME> "wlanflow - 1-1" "opkg list-installed | grep wlanflow"```, were:
	
	- ```<OpenWifi REST API URL>``` is the URL of the OpenWifi controller's REST API in the following format: ```http://<address/hostname>:<port>/<path to REST API>```.
	- ```<SDWN ADDRESS>``` is the IP address of the ONOS instance where the SDWN controller is running. This option is relevant to the LEDE boxes in the network, so make sure they can reach this address.
	- ```<SDWN PORT>``` is ONOS' south-bound OpenFlow listening port (default: 6633).
	- ```<NAME>``` is an arbitrary name which OpenWifi uses to distinguish registered services.

	The final two arguments are for OpenWifi's capability mechanism so it can decide whether a LEDE node is able to use the service that gets registered. The second part, ```opkg list-installed | grep wlanflow``` gets executed on the LEDE shell. If its output matches the first part (```"wlanflow - 1-1"``` in our case) OpenWifi  knows that the node can handle the service (SDWN in our case). Then, OpenWifi installs the relevant configuration options into the node's UCI configuration. On a LEDE node where wlanflow is installed, this will trigger the daemon to reload the just-updated configuration file and connect to the SDWN controller.

You can query which services are registered with OpenWifi from the ONOS shell by executing ```openwifi-services``` and remove a registration with ```openwifi-unregsiter```.
