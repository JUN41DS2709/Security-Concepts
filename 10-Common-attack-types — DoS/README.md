# DOS ATTACK

A DoS (**Denial-of-Service**) attack is a cyberattack designed to make a computer system, server, application, or network unavailable or extremely slow for legitimate users by deliberately exhausting or overwhelming its resources, so legitimate users cannot access the service normally.

## How does this work?

Suppose there is a web application server as a target for an attacker — an **e-shopping website** that the attacker is targeting.

### About the target

An e-shopping company where a large number of users are active every day and buy goods/products from that website.

Now the attacker will send a large amount of traffic or requests from his computer to the target.

The target will start slowing down, and slowly it may become unavailable to legitimate users.

For example, the application can normally handle **50,000 requests per second**, but the incoming workload reaches **100,000 requests per second**.

This can result in **financial loss** and **reputational loss** for the company.

That's the basic idea of a **DoS (Denial-of-Service) attack**.

---

## 1. Basic idea

Imagine a restaurant with **10 tables**.

* Normally, 5 customers arrive → everyone gets served.
* An attacker sends 10,000 fake customers at once.
* The restaurant becomes full.
* Genuine customers cannot get a table.

A DoS attack works similarly:

```text
Attacker → huge/exhausting workload → server resources consumed → legitimate users cannot access the service
```

The resources being exhausted might include:

* CPU
* RAM
* Network bandwidth
* Connection slots
* Database connections
* Application worker threads
* Storage
* Other limited system resources

---

## How a DoS attack works

A typical attack can be understood in four stages:

### Step 1 — Target selection

The attacker chooses a service such as:

* Website
* Web application
* DNS service
* Game server
* API
* Network server

### Step 2 — Resource exhaustion

The attacker generates excessive traffic or requests, or causes the target to perform unusually expensive operations.

For example:

```text
Legitimate users
      ↓
   Requests
      ↓
 ┌─────────────┐
 │   Server    │
 └─────────────┘
      ↑
      │
 Attacker traffic
```

### Step 3 — Capacity is reached

Every system has finite capacity.

For example, suppose a server can effectively handle **1,000 requests/second**:

```text
Capacity:       1,000 requests/sec
Incoming load:  5,000 requests/sec
                         ↑
                  capacity exceeded
```

The server may begin **queuing, delaying, or dropping requests**.

### Step 4 — Legitimate users are affected

Eventually, users may experience:

* Very slow pages
* Connection timeouts
* Failed logins
* Errors
* Intermittent availability
* Complete service outage

---

# DDoS

This is also similar to DoS (**Denial-of-Service**), but the main difference is that a DoS attack does not necessarily involve multiple distributed sources, while a **DDoS (Distributed Denial-of-Service)** attack uses multiple distributed sources.

In a simple DoS scenario, the attacker may generate the attack from a single source, such as their own computer.

But in a **DDoS**, the attacker uses a **botnet**.

A **botnet** is an army of compromised or malware-infected computers/devices that can be controlled by an attacker.

So, the attacker uses these botnet devices to send a very large amount of traffic or workload to the target.

### So why DDoS and not DoS?

Because the larger the target, the larger its capacity and resources may be.

So the attacker may also need a much larger amount of resources to overwhelm the target.

This is where **DDoS** comes into the picture.

A DDoS attack can provide things such as:

* Much greater aggregate traffic/workload
* Many different source addresses
* More difficulty distinguishing malicious traffic from legitimate traffic
* Greater resilience against blocking a single source

---

## Basic Difference

```text
DoS

Attacker
   │
   │
   ▼
Target Server
   │
   ▼
Service becomes slow/unavailable
```

```text
DDoS

          ┌── Compromised Device
          │
          ├── Compromised Device
          │
Attacker ─┼── Compromised Device ──► Target Server
          │
          ├── Compromised Device
          │
          └── Compromised Device
```

The core idea is:

> **DoS/DDoS attacks attempt to exhaust or overwhelm resources so that legitimate users cannot access a service normally.**
