# Operating-System


## NFS file distribution
The project is about NFS file distribution such as mounting, unmounting, cascading mounting, load balance, redundancy. NFS consisted of 5 VM, 3 of them are clients and 2 of them are servers.
# Server 1 
allows all the clients without restriction using (*) command in /etc/exports and access permission for all of them are equall with this command chmod -R 777 /mnt/nfsshare
and chown  -R nobody:nogroup /mnt/nfsshare. /path/file ip_address(rw,sync,no_subtree_check) are used to export NFS file system, rw is for read and write, sync automatically updates every change and no
subtree check reduces work load for programmer and it also decreases security among servers and clients.

ex. IP address 192.168.42.0/24 this ip address in a /24 subnet, and it specifies the entire subnet range that includes IP addresses from 192.168.42.1 to 192.168.42.254 can access the server.

First we test the server client connection if they are connected and listening each other using ping ip_address_client and ping ip_address_server. In this project I used bridged adapter, this allows us
access external internet and the connection is cable connection and each VM client and server were assigned unique ip address, so we don't have to worry about changing ip address of each VM.

# Server 2
Allows some clients rw and denies some client access, for some clients it is just ro. So this server is similar to server 1 but it won't accept all the clients, the main difference is this.

# Client 1
Client 1 can access all the files from server 1 and server 2, primarily mounting between servers, everything that is visible to client 1 is also visible, accessible to server 1 and server 2. Every change
to the file, is also synced the rest of the servers and clients automatically. Here client whether the file creator is server, it accesses the file as if it is the local file inside the local filesystem.
The uniqueness of this client is it is at the same time server and client. Client for 2 servers and server for client 2.

Main advantage of NFS are concurrent access to the files and load balancing. In fact we are going to discuss load balancing and redundancy at the later stage when we discuss about haproxy.

# Client 2

Client 2 is just like client 1 except it is not directly connected to servers, it access server through client 1 featuring mounting cascading. Indirect Access: Unlike Client 1, Client 2 does not 
have a direct network connection to the servers. Instead, it relies on Client 1 to access the required resources.
Cascading Mounts: Client 2 mounts directories that are already mounted on Client 1. This setup allows Client 2 to indirectly access the servers' resources by routing through Client 1.
mount -t nfs client1:/mnt/server_directory /mnt/client1_directory
client1-"ip address", nfs-"protocol" and -t= "type"

Note: NFS doesn't support all the file system. It supports specific file types such as ext1,2,3,4 extensions. I used ext4 for NFS mounting

# Client 3

This is the last client. Unlike any other clients above it is not client but supports load balancing and redundancy for server 1 and server 2. First I installed haproxy in my VM. HAProxy can balance NFS 
traffic by acting as a reverse proxy. Clients connect to HAProxy, which then forwards requests to the appropriate NFS server based on configured load balancing algorithms e.g. round-robin.
Haproxy is a free, open-source software solution that provides high availability, load balancing, and proxying for TCP and HTTP-based applications.

Both servers are connected to haproxy using:
192.168.42.107:/mnt/nfsloadbalance /mnt/nfsserver1 nfs rw,proto=tcp,port=2049 0 0

192.168.42.107 is haproxy ip address, nfs-"protocol", the same command is also used for server 2.

/etc/fstab is used for automatic mounting at boot.

Note: after each update or change we have to use sudo systemctl restart nfs-kernel-server or systemctl start haproxy or systemctl daemon-reload. exportfs -a, -ra is also used when we change or update
/etc/exports directory

## Configuration and setting up the servers are discussed in each specific branch that is dedicated for each server and client
