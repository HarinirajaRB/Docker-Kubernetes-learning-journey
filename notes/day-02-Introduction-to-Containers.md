# Day 02 – Introduction to Containers
**Date:** 18-03-2026
**Platform:** Udemy – Docker and Kubernetes
**Section:** Introduction to Containers

---

## Why Containers?

As a developer, you've likely faced this scenario:

> *"It works on my machine — but not on the server."*

This is the core problem containers solve.

---

## The Problem with Traditional Deployment

Running an application traditionally requires manually setting up every target machine:

1. Install the runtime (Node.js / Java / Python)
2. Install system-level dependencies
3. Install application dependencies
4. Configure environment variables
5. Run the application

This process must be repeated across **developer machines**, **test environments**, and **production servers** — and each environment can differ in OS, runtime version, or installed libraries.

### Key Pain Points

| Problem | Impact |
|---|---|
| Environment inconsistency | App works locally, fails in production |
| Dependency conflicts | App A needs Node 18, App B needs Node 22 |
| Multi-language complexity | Java + Node.js + Python on one machine is messy |
| Slow deployment cycles | Dev → Ops handoff breaks repeatedly |

---

## How Containers Solve This

A **container** packages everything needed to run an application into a single, portable unit:

- Application code
- Runtime (JDK, Node, Python, etc.)
- Dependencies and libraries
- Configuration

### Benefits at a Glance

| Benefit | What it means |
|---|---|
| **Consistency** | Same container runs identically everywhere |
| **Isolation** | Each app has its own environment — no version clashes |
| **Portability** | Runs on any machine with a container runtime installed |
| **Simplified Deployment** | No manual setup — just run the container |
| **Efficiency** | More containers per machine vs. VMs — lighter on resources |

### Developer + Ops Workflow with Containers

```
Developer Side                  Operations Side
─────────────────               ─────────────────────────────
Write Dockerfile         →      Pull image from registry
Build & push image       →      Provide env variables / config
                         →      Deploy container  
```

No more environment mismatch. No more "install this first."

---

## Containers vs Virtual Machines (VMs)

Both containers and VMs solve the environment consistency problem — but they do it differently.

### Virtual Machines (Virtualization)

```
┌──────────────────────────────────┐
│  App A      │  App B             │
│  Guest OS   │  Guest OS          │
├──────────────────────────────────┤
│         Hypervisor               │
├──────────────────────────────────┤
│       Host Operating System      │
├──────────────────────────────────┤
│       Hardware Infrastructure    │
└──────────────────────────────────┘
```

- Each VM contains a **full Guest OS**
- **Hypervisor** translates VM instructions to the host OS
- Strong isolation — each VM is fully independent
- **Heavy** — slower to start, consumes more memory

### Containers (Containerization)

```
┌──────────────────────────────────┐
│  App A      │  App B             │
│  (code + deps only)              │
├──────────────────────────────────┤
│        Container Engine          │
├──────────────────────────────────┤
│       Host Operating System      │
├──────────────────────────────────┤
│       Hardware Infrastructure    │
└──────────────────────────────────┘
```

- No Guest OS inside each container — just **code + dependencies**
- **Container Engine** (e.g., Docker) manages containers directly on the host OS
- Slightly less isolation than VMs, but containers offer configurable isolation rules
- **Lightweight** — faster startup, lower resource usage

### Comparison Table

| Feature | Virtual Machine | Container |
|---|---|---|
| Includes OS | Yes Full Guest OS | No OS overhead |
| Startup time | Minutes | Seconds |
| Resource usage | High | Low |
| Isolation level | Very strong | Strong (configurable) |
| Portability | Limited | High |

> **As a Java developer:** Think of a container like a JAR file — but for your entire runtime environment, not just your application code.

---

## Docker Architecture and Components

Docker is made up of three main parts that work together.

```
┌─────────────────┐        ┌──────────────────────────────┐        ┌──────────────────┐
│  Docker Client  │        │         Docker Host           │        │  Image Registry  │
│                 │        │                               │        │                  │
│  Docker CLI  ───┼──────► │  REST API                     │        │  Docker Hub      │
│  API Calls      │        │  Docker Daemon                │◄──────►│  Private Reg.    │
│                 │        │  ┌──────────┐ ┌────────────┐  │        │                  │
└─────────────────┘        │  │Containers│ │Image Cache │  │        └──────────────────┘
                           │  └──────────┘ └────────────┘  │
                           └──────────────────────────────-─┘
```

### 1. Docker Client
The interface through which you interact with Docker.

- **Docker CLI** — the primary tool; commands like `docker run`, `docker build`
- **API Calls** — CLI translates every command into a REST API request sent to the Docker Host

> You never directly touch the Docker Host — the client handles all communication.

### 2. Docker Host
Where all the real work happens.

| Component | Role |
|---|---|
| **REST API** | Receives requests from the Docker Client |
| **Docker Daemon** | The core engine — manages all containers (running & stopped) |
| **Containers** | Running or stopped instances living inside the host |
| **Image Cache** | Locally stored images used to create containers |

> Docker Client and Docker Host are two separate components — even when both run on the same machine (e.g., Docker Desktop on your laptop).

### 3. Image Registry
A remote store for Docker images.

- **Docker Hub** is the default public registry
- Images are uploaded (pushed) and downloaded (pulled) from here
- Public images can be pulled without authentication
- Pushing or using a **private registry** requires authentication

---

## How the Components Work Together

### Scenario 1: Running a Container (`docker run`)

```
1. You issue:         docker run <image-name>
2. CLI translates →   REST API request to Docker Host
3. Docker Daemon      checks Image Cache
4. Image not found? → Downloads from Image Registry  (network dependent)
   Image found?    → Uses cached version  (fast)
5. Docker Host        instantiates a new container from the image
```

> **Key analogy:** Image = Java Class | Container = Instance of that class

### Scenario 2: Building and Pushing an Image (`docker build` + `docker push`)

```
1. You issue:         docker build .
2. CLI sends →        REST API request + Dockerfile + build context to Docker Host
3. Docker Daemon      builds the image per Dockerfile instructions
                      (may pull base images from registry during build)
4. Image is tagged    and stored in local Image Cache
                      Not pushed to registry automatically

5. You issue:         docker push <image-name>
6. Docker Host        must be authenticated to the registry
7. Image uploads →    from local cache to Image Registry
```

---

## Key Concepts (Quick Reference)

| Term | Meaning |
|---|---|
| **Image** | Blueprint / template for a container (like a Java class) |
| **Container** | A running instance of an image (like a Java object) |
| **Dockerfile** | Instructions to build an image |
| **Docker Daemon** | Core background process managing all containers |
| **Image Cache** | Local storage of pulled/built images on the Docker Host |
| **Image Registry** | Remote store for sharing and distributing images (e.g., Docker Hub) |

---

## Summary

> **Containers** package an application along with its runtime, dependencies, and configuration into a self-contained unit. Unlike Virtual Machines, containers do not include a full OS — they share the host OS kernel via a container engine. Docker's architecture consists of three components: the **Docker Client** (CLI/API), the **Docker Host** (Daemon + image cache + containers), and the **Image Registry** (e.g., Docker Hub). The client sends commands to the host via REST API, the daemon manages containers, and images are pulled from or pushed to the registry as needed.

---

## Section Reference
- Course Repo: https://github.com/HarinirajaRB/Docker-Kubernetes-learning-journey
- Notes Path:  https://docs.google.com/document/d/1AAKqycJITB99RjZ_9PnJR3XrO8RkfTnP2CaNja_gscQ/edit?usp=sharing
