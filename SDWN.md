# SDWN for ONOS/LEDE

SDWN is short for Software-Defined Wireless Networking. This project comprises an SDWN control application running on top of an [ONOS](https://onosproject.org) SDN controller as well as an agent for [LEDE](https://ledeproject.org). The two communicate through an OpenFlow control channel. The agent provides the controller with information about about wifi access points and their associated clients. The controller offers the ability to centrally manage parameters of the wireless network and to monitor its state. For example, access points can be instructed to change their operating channel and clients can be forced to disconnect or banned from association altogether.

## Controller

The SDWN controller runs on top of an ONOS SDN controller that has been provided with the necessary protocol driver.

Source code:
- [ONOS](https://github.com/berlin-open-wireless-lab/sdwn-onos)
- [SDWN controller](https://github.com/berlin-open-wireless-lab/sdwn-controller)

TODO: description

## Agent

The agent is a daemon written in C using the indigo OpenFlow agent framework by the [Floodlight project](http://www.projectfloodlight.org/).

Source code:
- [wlanflow](https://github.com/berlin-open-wireless-lab/sdwn-agent)

TODO: description

## Protocol

The SDWN controller and the SDWN agent speak an extended version of OpenFlow 1.3. Libraries for the controller and agent are generated using a modified version of Loxigen. 

Source code:
- [Loxigen with SDWN extensions](https://github.com/berlin-open-wireless-lab/sdwn-loxigen)

TODO: description
