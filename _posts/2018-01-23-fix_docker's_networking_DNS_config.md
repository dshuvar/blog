---
published: true
categories: work
tags: docker
date: '2018-01-23 15:00:00 +0300'
---
## Fix Docker's networking DNS config


When I made build container I recieve some errors like that:

`[Errno -3] Temporary failure in name resolution`

or

`[Errno -2] Name or service not known`

the second mistake looks full like that:

```
Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'NewConnectionError('<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at  Failed to establish a new connection: [Errno -2] Name or service not known',)': /simple/django/
```

and after day serach for solution I finded it by [@nottrobin](https://twitter.com/nottrobin) at link https://development.robinwinslow.uk/2016/06/23/fix-docker-networking-dns/

## just in case I made copy article bellow:

> Sometimes, Docker’s internet connectivity won’t be working properly, which can lead to a number of obscure errors with your applications. In my experience, this is usually because DNS lookups are failing in Docker images. 

> If you know it’s a DNS problem and you’re in a hurry, jump straight to the system-wide solution.
Is DNS the problem?

> Fortunately it’s easy to test Docker’s DNS.

> First, check that basic internet connectivity is working by pinging a public IP address. It should succeed, giving you output similar to this:

```
$ docker run busybox ping -c 1 192.203.230.10  # Ping a London-based NASA root nameserver
PING 192.203.230.10 (192.203.230.10): 56 data bytes
64 bytes from 192.203.230.10: seq=0 ttl=53 time=113.866 ms

--- 192.203.230.10 ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 113.866/113.866/113.866 ms
```

> But now try resolving the domain google.com:

```
$ docker run busybox nslookup google.com
Server:    8.8.8.8
Address 1: 8.8.8.8
nslookup: can't resolve 'google.com'
```

>If it fails as shown above then there is a problem resolving DNS.
Why?

>By default, if Docker can’t find a DNS server locally defined in your /etc/resolv.conf file, containers will default to using Google’s public DNS server, 8.8.8.8, to resolve DNS.

>In some networks, like Canonical’s London office network where I work, the administrators intentionally block the use of public DNS servers to encourage people to use the network’s own DNS server.

>In this case, Docker containers using the default configuration won’t be able to resolve DNS, rendering the internet effectively unuseable from within those containers.

>I’ve filed a bug about this issue, although I don’t yet know when or if it might be addressed.
The quick fix: Overriding Docker’s DNS

>Fortunately, it’s fairly easy to directly run a docker container with a custom DNS server.
Discover the address of your DNS server

>You can find out what network’s DNS server from within Ubuntu as follows:

```
$ nmcli dev show | grep 'IP4.DNS'
IP4.DNS[1]:                             10.0.0.2
```

>Run Docker with the new DNS server

>To run a docker container with this DNS server, provide the --dns flag to the run command. For example, let’s run the command we used to check if DNS is working:

```
$ docker run --dns 10.0.0.2 busybox nslookup google.com
Server:    10.0.0.2
Address 1: 10.0.0.2
Name:      google.com
Address 1: 2a00:1450:4009:811::200e lhr26s02-in-x200e.1e100.net
Address 2: 216.58.198.174 lhr25s10-in-f14.1e100.net
```

>And that’s what success looks like.
The permanent system-wide fix

>The above solution is all very well if you’re only temporarily inside a restrictive network and you only need to run containers directly.

>However, most of the time you’ll want this to work by default and keep working on your system, and for any other programs that rely on Docker.
Update the Docker daemon

>To achieve this, you need to change the DNS settings of the Docker daemon. You can set the default options for the docker daemon by creating a daemon configuration file at /etc/docker/daemon.json.

>You should create this file with the following contents to set two DNS, firstly your network’s DNS server, and secondly the Google DNS server to fall back to in case that server isn’t available:

```
/etc/docker/daemon.json:

{
    "dns": ["10.0.0.2", "8.8.8.8"]
}
```

>Then restart the docker service:

>sudo service docker restart

>Testing the fix

>Now you should be able to ping google.com successfully from any Docker container without explicitly overriding the DNS server, e.g.:

```
$ docker run busybox nslookup google.com
Server:    10.0.0.2
Address 1: 10.0.0.2
Name:      google.com
Address 1: 2a00:1450:4009:811::200e lhr26s02-in-x200e.1e100.net
Address 2: 216.58.198.174 lhr25s10-in-f14.1e100.net
```
