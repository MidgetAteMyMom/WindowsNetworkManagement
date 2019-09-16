# WindowsNetworkManagement
 C# Static class to change network settings under Windows 10

## Contents
 There are two static classes contained in this project. **IPv4Validator** and **NetworkSettingsControler**
 The IPv4Validator can be used do verify ipaddresses and subnetmasks for validity and to check if two addresses are in the same network.
 The NetworkSettingsController class will work ***only** if ran under administrator privilege.* It can be used to set IP-Adresses and Nameservers statically or dynamically.

## Example
```C#
// Dependencies
using System;
using System.Collections.Generic;
using System.Net.NetworkInformation;

string networkInterfaceName = "Ethernet";
string ipAddress = "192.168.1.1";
string subnetMask = "255.255.255.0";
string defaultGateway = "192.168.1.254";
List<string> nameservers = new List<string>() { "192.168.1.10", "192.168.1.11" };
NetworkInterface nInterface;

// Get NetworkInterface object
foreach (NetworkInterface nic in NetworkInterface.GetAllNetworkInterfaces()) {
    if (nic.Name == networkInterfaceName) {
        nInterface = nic;
        break;
    }
}

// Validate Inputs
bool validConfiguration = true;
foreach (string nameserver in nameservers) {
    if (!IPv4Validator.ValidateAddress(nameserver)) {
        validConfiguration = false;
    }
}

List<string> ipAddresses = new List<string>() { ipAddress, defaultGateway };
try {
    if (!IPv4Validator.ValidateSameSubnet(ipAddresses, subnetMask)) {
        validConfiguration = false;
    }
} catch(FormatException e) { //If any of these Addresses is not a valid ipv4 address, this exception will be raised
    validConfiguration = false;
}

// Apply configuration
if (validConfiguration) {
    NetworkSettingsControler.SetStaticIp(nInterface, ipAddress, subnetMask);
    NetworkSettingsControler.SetStaticGateway(nInterface, defaultGateway);
    NetworkSettingsControler.SetStaticNameservers(nInterface, nameservers);
}
```