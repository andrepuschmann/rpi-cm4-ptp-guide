# Guide to using the hardware PTP support in the Raspberry Pi CM4

*This is still a work-in-progress. Suggestions for improvements are very welcome and can be made by creating issues.*

The Raspberry Pi Compute Module 4 (CM4) has hardware support for the Precision Time Protocol (PTP). This repository is a guide to how to use this to provide an accurate source of time within your local network.

Although the CM4 does not have an Ethernet jack, it does have an Ethernet PHY, the Broadcom [BCM54210PE](https://www.broadcom.com/products/ethernet-connectivity/phy-and-poe/copper/gigabit/bcm54210), which has support for PTP. Linux kernel support for this was added recently (2022), as discussed in https://github.com/raspberrypi/linux/issues/4151. This support has now been merged into the mainline Linux kernel.

The PTP support involves the Ethernet PHY having its own clock, called the PTP hardware clock (PHC), and being able to use this clock to timestamp incoming and outgoing network packets. This enables the CM4 to make use of PTP, but it is not by itself particularly exciting: similar functionality is available on many NICs. The exciting part is that the CM4 provides a pin that allows the PHC to be synchronized with an external pulse per second (PPS) signal. When this pin is connected to the PPS output of a GPS receiver, the CM4 can providech a highly accurate source of time for PTP within a local area network. (As far as I know, the only other inexpensive, easy way to to get this functionality is using an Intel i210-T1 PCIe card.) The CM4 can also generate a PPS signal from the PHC on this pin: this makes it possible to measure the accuracy of the PHC.

There's a useful introductory [blog](https://www.jeffgeerling.com/blog/2022/ptp-and-ieee-1588-hardware-timestamping-on-raspberry-pi-cm4) from Jeef Geerling and also a [video](https://www.youtube.com/watch?v=RvnG-ywF6_s).

This goal of this repository is to be a guide to taking advantage of this. The guide is split into the following sections:

* [parts needed to create a CM4-based computer](cm4-computer.md)
* [how to install and configure the operating system](os.md)
* [GPS receiver hardware options](gps-hw.md)
* [how to  the configure the GPS receiver](gps-config.md)
* [how to configure time synchronization](time-config.md)
* [inexpensive network switches with PTP support](switches.md)
* [how to use the PTP client on Windows 10/11 with this](ptp-windows.md)
* [how to measure time synchronization](measure.md)

## Results

This shows the results from using a TAPR TICC in interval mode with two CM4 PTP slaves 

- both slaves synchronized to a CM4 master using a U-blox RCB-F9T connected to a roof-mounted, dual-frequency antenna
- E2E, UDP IPv4 multicast (i.e. linuxptp default)
- all three machines connected to a FS IES3110-series switch (configured as transparent, one-step)

![tdev-20221129](https://user-images.githubusercontent.com/499966/204719830-a2631c24-f9f0-4f81-bc41-e34bdd3bc6e9.png)
![mtie-20221129](https://user-images.githubusercontent.com/499966/204719880-3effc69f-ad6c-4d38-8f64-1ca5fad3a5b7.png)

## References

Fedora has great [docs](https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/servers/Configuring_PTP_Using_ptp4l/) for PTP, although some details are different for Raspberry Pi OS, which is Debian-based.



