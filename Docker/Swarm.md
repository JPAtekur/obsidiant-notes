### Open protocols and ports between the hosts[](https://docs.docker.com/engine/swarm/swarm-tutorial/#open-protocols-and-ports-between-the-hosts)

The following ports must be available. On some systems, these ports are open by default.

-   **TCP port 2377** for cluster management communications
-   **TCP** and **UDP port 7946** for communication among nodes
-   **UDP port 4789** for overlay network traffic