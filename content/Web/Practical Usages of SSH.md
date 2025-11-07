---
title: Practical Usages of SSH
tags:
  - Network
date: 2025-11-06
---
`SSH` provides many powerful features. In this article, I demonstrate several useful commands that I frequently use in practice.

## 1. How to Authenticate on a Headless Server

In my lab, Internet access requires authentication via username and password through a browser. However, the server doesn't have a desktop environment installed.

### Preconditions

- Our client `A` can connect to server `B`
- The authentication IP is `10.9.0.4` (*this is the actual IP used in my lab*)

**Goal:** Complete the Internet authentication for `B` from `A`.
### Solution

We execute the following command on `A`:

```bash
ssh -L 8080:10.9.0.4:80 user@B
```

> The IP address here can also be replaced by its domain name.

Then we can use `localhost:8080` on `A` to access the authentication web page.

If we only want to forward the port without executing a remote command, we can use the `-N` option:

```
ssh -N -L 8080:10.9.0.4:80 user@B
```

### Explanation

```bash
-L [bind_address:]port:host:hostport
-L [bind_address:]port:remote_socket
-L local_socket:host:hostport
-L local_socket:remote_socket
```

> Specifies that connections to the given TCP port or Unix socket on the local (client) host are to be forwarded to  the  given  hostand  port, or Unix socket, on the remote side.  
> 
> This works by allocating a socket to listen to either a TCP port on the local side,optionally bound to the specified bind_address, or to a Unix socket.  Whenever a connection is made to the local  port  or  socket,the  connection  is  forwarded  over  the secure channel, and a connection is made to either host port hostport, or the Unix socketremote_socket, from the remote machine.Port forwardings can also be specified in the configuration file.  Only the superuser can forward privileged ports.  IPv6 addresses can be specified by enclosing the address in square brackets.By default, the local port is bound in accordance with the GatewayPorts setting.  
> 
> However, an explicit bind_address may be used  tobind  the  connection  to a specific address.  The bind_address of “localhost” indicates that the listening port be bound for localuse only, while an empty address or ‘*’ indicates that the port should be available from all interfaces.

Therefore, this command will **forward** the local port `8080` to the remote host `B`'s `10.9.0.4:80`.

```mermaid
sequenceDiagram
    participant Browser as Browser (A: localhost:8080)
    participant A as Server A (Local)
    participant Tunnel as SSH Tunnel (A → B)
    participant B as Host B (SSH Server)
    participant Service as Internal Service (10.9.0.4:80)

    Note over A,B: A initiates SSH connection to B (A → B)
    A->>B: Establish SSH connection
    B-->>A: SSH tunnel established

    Note over A: A listens on port 8080 (created by -L)

    Browser->>A: Request http://localhost:8080
    A->>Tunnel: Forward request through SSH tunnel
    Tunnel->>B: Send data to B
    B->>Service: B accesses 10.9.0.4:80 locally
    Service-->>B: Return web response
    B-->>Tunnel: Send response back through tunnel
    Tunnel-->>A: Response arrives at A
    A-->>Browser: Browser displays page from 10.9.0.4
```


### Solution 2: Reverse Tunneling

If `B` can connect to `A`, we can also use *SSH reverse tunneling* with the `-R` option.

```bash
-R [bind_address:]port:host:hostport
-R [bind_address:]port:local_socket
-R remote_socket:host:hostport
-R remote_socket:local_socket
-R [bind_address:]port
```


> Specifies that connections to the given TCP port or Unix socket on the remote (server) host are to be forwarded to the local side.

Therefore, we can execute the following command on `B`:

```bash
ssh -N -R 8080:10.9.0.4:80 user@A
```

This way, we can also access the authentication web page via `localhost:8080` on `A`.


```mermaid
sequenceDiagram
    participant Browser as Browser (A: localhost:8080)
    participant A as Server A (Remote)
    participant Tunnel as SSH Tunnel (B → A)
    participant B as Host B (SSH Client)
    participant Service as Internal Service (10.9.0.4:80)

    Note over B,A: B initiates SSH connection to A (B → A)
    B->>A: Establish SSH connection
    A-->>B: SSH tunnel established

    Note over A: A listens on port 8080 (created by -R)

    Browser->>A: Request http://localhost:8080
    A->>Tunnel: Forward request through SSH tunnel
    Tunnel->>B: Send data to B via tunnel
    B->>Service: Access 10.9.0.4:80 locally
    Service-->>B: Return web response
    B-->>Tunnel: Send response back through tunnel
    Tunnel-->>A: Response arrives at A
    A-->>Browser: Browser displays page from 10.9.0.4

    Note over A,B: A does not need access to B — only B→A must be reachable
```


## 2. How to Access the Internet Through a Proxy

In China, many websites are blocked, so we need to use proxies. Typically, our desktop computer (client `A`) has a proxy service installed via `Clash`. However, it would be tedious to set up such proxy services on every remote server.

### Preconditions

- `A` provides proxy services in *TUN* mode
- `B` can connect to `A`

**Goal:** Use the proxy service on `B`.

### Solution

On `B`, we execute the command:

```bash
ssh -N -D 1080 user@A
```

```bash
-D [bind_address:]port
```


> Specifies a local “dynamic” application-level port forwarding.  This works by allocating a socket to listen to port on the local side,  optionally  bound to the specified bind_address.  
> 
> Whenever a connection is made to this port, the connection is forwarded over the secure channel, and the application protocol is then used to determine where to connect to  from  the  remote  machine.Currently  the  SOCKS4 and SOCKS5 protocols are supported, and ssh will act as a SOCKS server.  Only root can forward privileged ports.  Dynamic port forwardings can also be specified in the configuration file. IPv6 addresses can be specified by enclosing the address in square brackets.  Only the superuser can forward  privileged  ports. 
> 
> By  default, the local port is bound in accordance with the Gateway Ports setting.  However, an explicit bind_address may be used to bind the connection to a specific address.  The bind_address of “localhost” indicates that the listening port  be  bound  for local use only, while an empty address or ‘*’ indicates that the port should be available from all interfaces.


Next, we set up the proxy address on `B`:

```bash
export ALL_PROXY="socks5h://127.0.0.1:1080"
export HTTP_PROXY="socks5h://127.0.0.1:1080"
export HTTPS_PROXY="socks5h://127.0.0.1:1080"
```

The *dynamic* forwarding also works well if server `A` is located outside China (i.e., `A` can access the Internet freely). We can verify the new IP address via `curl ipinfo.io`.
## 3. How to View Localhost Pages Remotely

This method is identical to the one described in *1. How to Authenticate on a Headless Server*. For example, suppose `B` hosts a localhost web page, such as Jupyter Notebook, and you want to view it on `A`.

Assuming the port on `B` is 8000, execute:

```bash
ssh -N -L 8080:127.0.0.1:8000 user@B
```