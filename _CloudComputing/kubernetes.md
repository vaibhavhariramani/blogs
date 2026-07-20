---
title: "Kubernetes: Orchestrating Containers at Scale"
excerpt: "Kubernetes turns a pile of containers into a self-healing, auto-scaling system — here's how pods, deployments, and services actually fit together."
header:
  image: /assets/images/unsplash-gallery-image-2.jpg
  teaser: images/cloud_computing/cc_teaser1.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

If you've already read about [Docker](/CloudComputing/Docker/), you know how to package an application into a container. Kubernetes answers the next question: what happens when you have hundreds of containers, across dozens of machines, and one of those machines just died at 3 AM?

## What Kubernetes Actually Is

Kubernetes (often shortened to "K8s" — 8 letters between the K and the s) is an open-source container **orchestration** platform, originally built by Google and now maintained by the Cloud Native Computing Foundation. It takes a set of machines (called a cluster) and treats them as one pool of compute, on which it schedules, runs, heals, and scales your containers automatically.

Docker answers "how do I package and run one container?" Kubernetes answers "how do I run thousands of containers reliably, across many machines, without babysitting them?"

## Why Not Just Use Plain Docker?

Docker alone is great for a single host. But production systems need things Docker by itself doesn't provide:

- **Self-healing** — if a container crashes or a node dies, something needs to notice and restart work elsewhere.
- **Scaling** — traffic spikes; you need more replicas of a service, automatically.
- **Service discovery & load balancing** — containers come and go with new IPs each time; something needs to route traffic to whichever ones are currently alive.
- **Rolling updates & rollbacks** — deploying a new version without downtime, and reverting cleanly if it breaks.
- **Declarative infrastructure** — you describe the *desired state* ("I want 3 replicas of this app"), and the system continuously works to match reality to that description.

Kubernetes provides all of this as a platform, so teams stop hand-rolling scripts around `docker run` and start describing what they want instead.

## Core Concepts

### Pods

The smallest deployable unit in Kubernetes is a **Pod**, not a container. A Pod wraps one or more tightly-coupled containers that share networking and storage. Most of the time, a Pod holds exactly one container — think of it as "a container, plus the Kubernetes metadata around it."

### Deployments

You rarely create Pods directly. Instead, you create a **Deployment**, which describes:

- which container image to run
- how many replicas (identical Pods) you want
- how updates should be rolled out

The Deployment controller constantly compares what's running against what you asked for, and reconciles the difference — if a Pod dies, it creates a new one; if you scale from 3 to 10 replicas, it creates 7 more.

### Services

Pods are disposable and get new IP addresses whenever they're recreated. A **Service** gives a stable name and IP/DNS entry that load-balances traffic across whichever Pods currently match a label selector — so the rest of your system never has to track individual Pod IPs.

### Namespaces, ConfigMaps, and Secrets

Beyond the core three, Kubernetes gives you Namespaces (logical isolation within a cluster), ConfigMaps (externalized configuration), and Secrets (sensitive values like API keys) — all so your container image itself stays generic and environment-agnostic.

## A Simple Example Manifest

Here's a minimal Deployment and Service for an `nginx` web app — the kind of thing you'd apply with `kubectl apply -f app.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
        - name: web-app
          image: nginx:1.25
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "250m"
              memory: "256Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  type: LoadBalancer
  selector:
    app: web-app
  ports:
    - port: 80
      targetPort: 80
```

Apply it, and Kubernetes will:

1. Schedule 3 Pods running `nginx:1.25` across available nodes.
2. Keep exactly 3 healthy replicas running at all times — restarting or rescheduling any that fail.
3. Expose them behind a single stable Service endpoint that load-balances requests across the live Pods.

Change `replicas: 3` to `replicas: 10` and re-apply, and Kubernetes scales out for you — no new scripts, no manual load balancer reconfiguration.

## Where It Fits in a Real Stack

In practice, the flow looks like this: you build an image with Docker (or `buildah`/`podman`), push it to a registry, and hand the image reference to a Kubernetes Deployment manifest. From there, Kubernetes handles placement, scaling, health checks, rolling updates, and networking. Logs and metrics from these Pods typically flow into an observability stack — which is exactly what the [ELK Stack](/CloudComputing/elk-stack/) or [Graylog](/CloudComputing/graylog/) posts in this series cover. And increasingly, Kubernetes is also the substrate for [AI/ML infrastructure](/CloudComputing/ai-infrastructure/), scheduling GPU-backed training and inference workloads the same way it schedules ordinary web apps.

Kubernetes has a learning curve, and it's overkill for a single hobby app on one server. But once you're running more than a handful of services that need to stay up, scale, and deploy safely, it earns its complexity fast — which is why it's the de facto standard for running containers in production today.

# Made with ❤️ by Vaibhav Hariramani
