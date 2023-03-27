### Overview
The OpenPnP Feeder type `PhotonFeeder` supports feeders running [Photon Firmware](https://github.com/photonfirmware/photon). Photon is Open-Source firmware for pick and place feeders.

Photon was originally developed as part of the LumenPnP project, but it is designed to support many types of hardware. It comes with its own communication protocol sent over RS-485. 

Photon expects that feeders are mounted in certain locations in a pick and place machine called "slots", and it also expects that the feeder is able to identify which slot it's in. This is accomplished with the LumenPnP feeders by putting a 1-Wire EEPROM on each slot that is programmed with the slot address. When a feeder is mounted in the slot, it reads the address off the EEPROM.

The `PhotonFeeder` feeder type in OpenPnP will automatically scan the RS-485 bus for feeders, and auto-populate them into the `Feeders` tab. It also handles sending feed commands through Photon, and even includes a wizard for folks to program their slots using a single feeder.

Photon and its packet structure is currently only supported by Marlin as a host, using the `M485` command.