---
layout: post
title: "Архитектура проекта ZettaDNS"
subtitle: ""
tags: [dns, bind, zettadns, cloudns]
diagrams: true
---

![Архитектура проекта ZettaDNS](/assets/posts/2026/zettadns-conceptual-architecture.png)

## Блок-схема

```mermaid
flowchart TD
    subgraph Control [Control plane]
        WebUI[WebUI]
        DB[(PostgreSQL)]
        Cache[(Redis)]
    end
    subgraph Data [Data plane]
        Agent[Agent]
        Bind9[Bind9]
    end
    Browser[Браузер]
    Clients[DNS-клиенты]
    Prometheus[Prometheus]
    Browser --> WebUI
    WebUI --> DB
    WebUI --> Cache
    WebUI <-->|REST API| Agent
    Agent -->|конфигурация зон| Bind9
    Clients -->|DNS-запросы| Bind9
    Prometheus -.->|метрики| Bind9
    Prometheus -.->|метрики| Agent
```

## Компоненты

- WebUI
- PostgreSQL
- Redis
- Agent
- Bind9
- Prometheus
