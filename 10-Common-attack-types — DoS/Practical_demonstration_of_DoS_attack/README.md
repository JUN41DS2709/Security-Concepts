# Practical Demonstration of a DoS Attack Using Slowloris

> \\\\\\\*\\\\\\\*Lab scope:\\\\\\\*\\\\\\\* This demonstration was performed in an isolated lab
> environment using a Linux attacker machine and a Metasploitable 2
> target. The procedure should only be performed against systems that
> you own or are explicitly authorized to test.

## Lab Environment

* **Attacker machine:** Kali Linux
* **Target machine:** Metasploitable 2
* **Network:** Isolated lab network
* **Target IP:** `192.168.1.114`
* **Metasploit module:** `auxiliary/dos/http/slowloris`
* **Tools used:** `ifconfig`, `ping`, `nmap`, Metasploit Framework
(`msfconsole`), and `curl`

\---

## 1\. Verifying the Target IP Address

I first started both virtual machines: the Kali Linux machine and the
Metasploitable 2 machine.

On the Metasploitable 2 machine, I used `ifconfig` to verify its network
configuration and identify its IP address. The target was assigned the
address `192.168.1.114`.

!\[Metasploitable 2 IP configuration](images/1.png)

I then confirmed connectivity from the Kali Linux machine by sending
five ICMP echo requests:

``` bash
ping -c 5 192.168.1.114
```

The `-c 5` option limits the test to five packets. This provides a quick
way to verify that the target is reachable before performing further
enumeration.

!\[Ping connectivity test](images/2.png)

\---

## 2\. Enumerating Network Services with Nmap

After confirming connectivity, I performed service enumeration against
the target:

``` bash
nmap -sV 192.168.1.114
```

The `-sV` option attempts to identify the versions of services running
on the discovered ports.

Because the planned lab exercise uses **Slowloris**, an HTTP service is
required. The scan showed that the target was running a web server on
port 80, making it suitable for this demonstration.

!\[Nmap service enumeration](images/3.png)

\---

## 3\. Launching Metasploit Framework

Next, I launched the Metasploit Framework from the Kali Linux terminal:

``` bash
msfconsole
```

Metasploit provides a collection of modules for security testing,
including modules designed to demonstrate denial-of-service conditions
in controlled environments.

!\[Launching Metasploit](images/4.png)

\---

## 4\. Selecting the Slowloris Module

I selected the HTTP Slowloris denial-of-service module:

``` text
use auxiliary/dos/http/slowloris
```

> \\\\\\\*\\\\\\\*Correction:\\\\\\\*\\\\\\\* The module name is `slowloris`, not `solaris`.

!\[Selecting the Slowloris module](images/5.png)

### About the Slowloris Module

The `auxiliary/dos/http/slowloris` module demonstrates a **low-and-slow
HTTP denial-of-service technique**. Instead of sending a large volume of
complete requests, it maintains many HTTP connections and keeps them
open by periodically sending incomplete or keep-alive headers. This can
consume the web server's available connection resources and prevent
legitimate clients from being served normally. In this lab, the module
was used to demonstrate how an HTTP service can become unavailable under
connection exhaustion.

\---

## 5\. Reviewing the Module Configuration

After selecting the module, I reviewed its available options with:

``` text
show options
```

The module displayed several configurable parameters, including:

* `RHOSTS` --- the target IP address
* `RPORT` --- the target HTTP port
* `SOCKETS` --- the number of connections used by the module
* `DELAY` --- the interval between keep-alive headers
* `RAND\\\\\\\_USER\\\\\\\_AGENT` --- randomizes the User-Agent for requests
* `SSL` --- controls whether SSL/TLS is used

The default configuration shown in the lab used port `80`, `150`
sockets, a `15`-second delay, and SSL disabled.

!\[Slowloris module options](images/6.png)

!\[Slowloris module options](images/7.png)

!\[Slowloris module options](images/8.png)

\---

## 6\. Configuring the Target

I initially attempted to configure the target using:

``` text
set RHOSTS 192.168.1.114
```

Metasploit reported an unknown datastore option in the screenshot, which
indicated that the option name had not been entered correctly. After
correcting the configuration, `RHOSTS` was successfully set to
`192.168.1.114`.

!\[Setting the target address](images/9.png)

The relevant configuration then showed:

``` text
RHOSTS   192.168.1.114
RPORT    80
SOCKETS  150
SSL      false
```

\---

## 7\. Starting the Slowloris Demonstration

With the target configured, I started the module using:

``` text
run
```

The module reported that it was starting a server, attacking
`192.168.1.114` with 150 sockets, creating sockets, and repeatedly
sending keep-alive headers.

The repeated messages such as:

``` text
Sending keep-alive headers ... Socket count: 150
```

indicate that the module was maintaining the connections rather than
immediately closing them.

!\[Slowloris module running](images/10.png)

!\[Slowloris connections being maintained](images/11.png)

\---

## 8\. Verifying the Target During the Test

While the Slowloris module was running, I monitored the target from the
Kali Linux machine.

The Metasploitable 2 web page was initially accessible through the
browser, confirming that the HTTP service was responding.

!\[Metasploitable 2 web page during the lab](images/12.png)

I also used `curl` to inspect the HTTP response headers:

``` bash
curl -I http://192.168.1.114
```

The response showed:

``` text
HTTP/1.1 200 OK
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Content-Type: text/html
```

This confirmed that the web server was reachable and responding to HTTP
requests at that point in the demonstration.

!\[HTTP response from curl](images/13.png)

\---

## 9\. Observing the Availability Impact

I then started the Slowloris module again and allowed it to maintain the
connections. While the module was active, I tested the web service with
a short timeout:

``` bash
curl -I --max-time 5 http://192.168.1.114
```

This time, `curl` returned:

``` text
curl: (28) Operation timed out after 5003 milliseconds with 0 bytes received
```

This is the key observation from the lab. The HTTP request did not
receive a response within the five-second timeout while the Slowloris
connections were being maintained.

!\[HTTP request timing out during the test](images/14.png)

The result demonstrates the **availability impact** of the attack in
this intentionally vulnerable lab environment: a legitimate HTTP client
can experience delays or timeouts when the server's available connection
resources are consumed by long-lived connections.

\---

## 10\. Stopping the Demonstration

After observing the effect, I stopped the module with `Ctrl+C`.

Metasploit displayed messages indicating that it was stopping the attack
against the current target and that module execution had completed.

It is important to stop the test after the required observation,
particularly when working with intentionally vulnerable virtual
machines, so that the lab environment can be returned to its normal
state.

\---

## 11\. Result and Analysis

The practical demonstrated the following workflow:

1. Verified the target IP address.
2. Confirmed network connectivity using `ping`.
3. Enumerated services with `nmap -sV`.
4. Confirmed that HTTP was available on port 80.
5. Loaded the Metasploit Slowloris module.
6. Reviewed and configured the module options.
7. Started the controlled denial-of-service demonstration.
8. Monitored the target using a browser and `curl`.
9. Observed an HTTP request timeout while the Slowloris connections
were active.
10. Stopped the module after completing the observation.

The important takeaway is that a denial-of-service condition does not
necessarily require a massive amount of traffic. A **low-and-slow**
technique such as Slowloris can instead attempt to exhaust server-side
connection resources by keeping many HTTP connections open for an
extended period.

\---

## 12\. Conclusion

This lab provided a practical demonstration of how an HTTP service can
be affected by a connection-exhaustion denial-of-service technique. The
Metasploitable 2 server initially responded normally to HTTP requests,
while the controlled Slowloris test maintained a large number of
connections. During the test, a subsequent `curl` request timed out,
demonstrating a measurable reduction in HTTP availability.

Because this technique is designed to affect service availability, it
should only be tested in an isolated environment or against systems for
which explicit authorization has been obtained.

### Defensive Perspective

Administrators can reduce exposure to low-and-slow HTTP attacks by using
appropriate connection limits and timeouts, deploying a reverse proxy or
web application firewall where appropriate, monitoring unusual numbers
of long-lived connections, and keeping web-server software and
configurations properly maintained.

