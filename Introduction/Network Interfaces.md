Any network transaction is made through an interface, that is, a device that is able to exchange data with other hosts. Usually, and *interface* is a hardware device, but it might also be a pure software device, like the loopback interface. A network interface is in charge of sending and receiving data packets, driven by the network subsystem of the kernel, without knowing how individual transactions map to the actual packets being transmitted. Many network connections (especially those using TCP) are stream-oriented, but network devices are, usually, designed around the transmission and receipt of packets. A network driver knows nothing about individual connections; it only handles packets.

Not being a stream-oriented device, a network interface isn't easily mapped to a node in the filesystem, as */dev/tty1* is. The Unix way to provide access to interfaces is still assigning a unique name to them (such as eth0), but the name doesn't have a corresponding entry in the filesystem. Communication between the kernel and a network device driver is completely different from that used with char and block drivers. Instead of *read* and *write*, the kernel calls functions related to packet transmission.

> [!QUOTE] In short...
> Network devices, such as Ethernet and Wi-Fi adapters, transmit and receive packets. Network drivers must handle packet transmission and reception through functions like `ndo_start_xmit()` for sending packets and interrupt handling for receiving packets.

- No `/dev` file
- Works via network stack
- Uses `net_devices` structure
- Packet-based communication
