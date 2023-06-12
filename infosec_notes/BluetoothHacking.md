# Bluetooth Hacking

## Basics

- universal protocol for low power, near-field communication
- operating at 2.4-2.485 GHz using spreading spectrum, frequency hopping at 1600 hops per second
- minimum range of 10 meters (many devices have range of up to 100 meters)
- connection is called pairing
- any two Bluetooth devices can connect to each other but they can pair only if they are in discoverable mode
- Device in discoverable mode transmits follwing information:
  - Name
  - Class
  - List of services
  - Technical Information
- when two devices pair they exchange a secret or link key
- each device stores the key so it can identify the other in future pairings

## Scanning and Recon

- Linux protocol stack: BlueZ (preinstalled in Kali)
- important BlueZ tools:
  - `hciconfig`: very similar to `ifconfig` but for Bluetooth devices
    - check if Bluetooth adapter is recognized:
      `hciconfig` --> if this returns an entry with an adapter name and MAC-Address everything is fine
    - enable Bluetooth device:
      `hciconfig hci0 up`
  - `hcitool`
    - inquiry tool which can provide us with
      - device name
      - device-ID
      - device class (indicates type of Bluetooth device) --> can be checked online
      - clock-information
    - enables the devices to work synchronously
    - Scanning for Bluetooth devices nearby:
      `hcitool scan`
    - get more info (MAC-Address, clock-offset, class of device) for enumerated devices with:
      `hcitool inq`
  - `hcidump`: to sniff Bluetooth communication
  - `sdptool`: SDP (Service Discovery Protocol)
    - Bluetooth Protocol for searching for Bluetooth services on a enumerated (with `hcitool`) Bluetooth device
    - Syntax: `sdptool browse MACAddressOfDevice`
