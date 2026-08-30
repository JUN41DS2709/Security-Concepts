# MITM

* MITM stands for **Man-in-the-Middle**.
* A MITM attack is when an attacker positions themselves in between two communication parties and can **listen to their communications, intercept them, modify them, and impersonate one of the parties** without the parties realizing it.
* The core idea is that Alice thinks she is communicating with Bob directly, but the attacker has inserted themselves into the communication path.

### Normal Communication

```text
Alice ───────────────────────► Bob
       "Send $100 to Bob"
```

### MITM Communication

```text
Alice ───────► Attacker ───────► Bob
                 │
                 ├── Read
                 ├── Modify
                 ├── Inject
                 └── Forward
```

MITM is **not a single vulnerability or a single tool**. It is a class of attacks where the attacker gets into a position between communicating endpoints.

## Why Does MITM Exist?

To understand MITM, we first need to understand how networking normally works.

For example:

Alice wants to send a message to Bob.

For us, the message looks like it is directly getting transferred from Alice to Bob easily.

But we don't see the networking mechanisms that help the message transfer.

* First, the connection is established.
* Network services are assigned.
* Which MAC address will receive the data?
* What is Bob's IP address?
* Is the data encrypted?
* Is the session properly managed?
* What is the proper path so that the message reaches its correct destination?

So, in MITM attacks, the attacker exploits weaknesses in **one or more of these mechanisms**.

```text
Application
    ↓
TCP
    ↓
IP
    ↓
Ethernet
```

## Things an Attacker Can Do

### Eavesdrop

The attacker can **see or listen to the traffic** between Alice and Bob.

### Modify

The attacker can **modify the traffic** and then forward it to Bob.

### Inject

The attacker can **inject data into the traffic** without letting Alice and Bob know.

For example, in some situations, an attacker may inject malicious content into unencrypted traffic.

### Impersonate

The attacker can **impersonate Bob to Alice and Alice to Bob**, making each party believe they are communicating directly with the other party.

## Core Idea

The main idea behind a MITM attack is:

```text
Alice thinks:

Alice ───────────────────────► Bob


But the actual communication is:

Alice ───────► Attacker ───────► Bob
```

The attacker tries to position themselves between the two communicating parties so they can potentially **read, modify, inject, or impersonate** the communication.
