*This project has been created as part of the 42 curriculum by tmattela.*

# NetPractice

## Description

NetPractice is a hands-on introduction to computer networking. The goal is to fix small simulated networks (hosts, switches, routers and the Internet) by configuring IP addresses, subnet masks, default gateways and routing tables until every objective of each level is reached.

The networks are not real: they run in a training interface opened in the web browser. Each of the 10 levels displays a non-working network diagram and a list of goals (for example "host A needs to communicate with host B"). Only the unshaded fields can be edited. The **Check again** button validates the configuration, and the logs at the bottom of the page explain why a packet is dropped (duplicate IP, no matching route, private subnet routed over the Internet, etc.).

Overview of what the project covers:

- Choosing valid IPv4 addresses and subnet masks for each link.
- Making sure two interfaces of the same device never share a network.
- Setting the default gateway (next hop) of hosts and routers.
- Writing routing tables, with both specific destinations and the default route `0.0.0.0/0`.
- Building the forward path and the return path of every communication.

## Instructions

### Running the training interface

1. Download the archive attached to the project page and extract it in any folder.
2. In this folder, run the launch script:

   ```sh
   ./run.sh
   ```

   It starts a local web server and opens the interface in the default browser.
3. If `run.sh` does not work, start the server manually and open the page yourself:

   ```sh
   python3 -m http.server 49242
   ```

   then go to `http://localhost:49242` (the port number can be changed).
4. In the **Training** tab, enter your intranet login (`tmattela`) and click **Start!**. Entering the login is essential, because the configuration is generated for it. The **Evaluation** tab generates a random configuration, which is also what is used during the defense.

### Solving a level

1. Read the goals displayed at the top of the page.
2. Edit the unshaded fields: IP addresses, masks, and routes (destination and next hop).
3. Click **Check again** and read the logs to understand any remaining error.
4. When the level is complete, a **Next level** button appears.

### Exporting configurations

Before moving to the next level, click **Get my config** to download the configuration of the current level. This must be done for each of the 10 levels.

### Submission requirements

- 10 exported configuration files (one per level) must be placed at the root of the Git repository, next to this `README.md`.
- Only the content of the repository is evaluated.
- During the defense, three random levels must be completed within a limited time. No external tools are allowed; a simple calculator such as `bc` is tolerated.

## Method used to solve the levels

For every address, the following steps were applied:

1. Write the subnet mask in binary.
2. Count the bits set to 1 to get the CIDR prefix.
3. Compute the increment: `256 - value of the mask in the interesting octet` (equivalently `2^host bits`).
4. Compute the network address (IP AND mask), then the broadcast address (network + increment - 1) and the usable range (network + 1 to broadcast - 1).

Number of usable hosts: `2^(32 - CIDR) - 2`.

Rules checked on every level:

- Devices on the same link share the same network address and the same mask.
- No device uses the network address or the broadcast address of its network.
- Two interfaces of the same router are always in different networks, and networks never overlap.
- A next hop must be an IP in a network that is directly connected to the device. It is never one of its own interfaces.
- A host only needs a default route towards its router. A router needs routes only for networks that are not directly connected to it.
- Every communication needs a forward path and a return path. The Internet needs a route back to the internal networks, and private addresses (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) are not routed over the Internet.

## Resources

### Networking concepts studied

- **TCP/IP addressing**: IPv4 addresses (32 bits, 4 octets), network part and host part, public and private ranges, special addresses (loopback `127.0.0.0/8`, `0.0.0.0`).
- **Subnet masks and CIDR notation**: binary representation, prefix length, increment, subnetting, network and broadcast addresses, usable host range.
- **Default gateways**: the router interface a host sends packets to when the destination is outside its own network.
- **Routers and routing tables**: directly connected networks, static routes (destination and next hop), the default route `0.0.0.0/0`, forward and return paths.
- **Switches**: layer 2 devices that connect machines of the same network without routing and without an IP address.
- **OSI layers**: layer 2 (switching, frames) versus layer 3 (IP addressing and routing), and how a packet moves between the two.

### References

- RFC 791, *Internet Protocol*: https://datatracker.ietf.org/doc/html/rfc791
- RFC 1918, *Address Allocation for Private Internets*: https://datatracker.ietf.org/doc/html/rfc1918
- RFC 4632, *Classless Inter-domain Routing (CIDR)*: https://datatracker.ietf.org/doc/html/rfc4632
- RFC 1122, *Requirements for Internet Hosts - Communication Layers*: https://datatracker.ietf.org/doc/html/rfc1122
- Wikipedia, *Subnetwork*: https://en.wikipedia.org/wiki/Subnetwork
- Wikipedia, *OSI model*: https://en.wikipedia.org/wiki/OSI_model

### Use of AI

An AI assistant (Claude) was used as a tutor for the learning part of the project:

- To explain the theory before starting: IP addressing, subnet masks, CIDR, network and broadcast addresses, default gateways and routing tables.
- To check my manual subnet calculations (binary conversion, CIDR, increment, network, broadcast and range) and to point out the mistakes in my reasoning.
- To help read the simulator logs and understand the cause of failing goals (duplicate IP, overlapping networks, next hop outside a directly connected network, private addresses over the Internet), step by step.

The AI was not used as a blind source of answers. Every configuration was entered, tested with **Check again** and understood by me, and the results were meant to be reviewed with peers.
