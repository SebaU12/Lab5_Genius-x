# GENIUS-X AGENT HARNESS

```text
GENIUS-X AGENT HARNESS

┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ AUTH & PERMISSIONS                                                   │   │
│   │ Identity & Authorization Service                                     │   │
│   │ Identidad • Roles • Permisos • Least Privilege                       │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                  │                                           │
│                                  ▼                                           │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ SESSION & CAPACITY                                                   │   │
│   │ Session Service + Queue & Priority                                   │   │
│   │ 5 sesiones • 100k tokens • Timers • Cola • SLA                      │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                  │                                           │
│                                  ▼                                           │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ CONTEXT                                                              │   │
│   │ Incident Context Service + Trail Service                             │   │
│   │ Estado actual + Historia autorizada                                  │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                  │                                           │
│                                  ▼                                           │
│                         ┌──────────────────────┐                               │
│                         │      LLM LOCAL       │                               │
│                         │                      │                               │
│                         │ Razona               │                               │
│                         │ Propone              │                               │
│                         │ Decide siguiente paso│                               │
│                         └──────────┬───────────┘                               │
│                                    │                                          │
│                                    ▼                                          │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ AGENT LOOP                                                           │   │
│   │ LLM Orchestrator                                                     │   │
│   │ Plan → Tool → Resultado → Reevaluar → Responder                     │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                  │                                           │
│                      ┌───────────┴───────────┐                               │
│                      ▼                       ▼                               │
│   ┌──────────────────────────────┐   ┌──────────────────────────────┐       │
│   │ TOOL ACCESS                  │   │ APPROVAL / HITL              │       │
│   │ Tool Access Service          │   │ Action & Approval            │       │
│   │ DB • Slack • Repo • Docs     │   │ Critical actions             │       │
│   └──────────────┬───────────────┘   └──────────────┬───────────────┘       │
│                  │                                  │                       │
│                  └────────────────┬─────────────────┘                       │
│                                   ▼                                         │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ MEMORY & TRACEABILITY                                                │   │
│   │ Trail Service + Audit Service                                       │   │
│   │ Historia • Evidencia • Acciones • Aprobaciones                      │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                  │                                           │
│                                  ▼                                           │
│   ┌──────────────────────────────────────────────────────────────────────┐   │
│   │ RELIABILITY & OBSERVABILITY                                          │   │
│   │ Health • P95/P99 • Timeouts • Circuit Breakers                      │   │
│   │ Availability • Reliability • Partial Degradation                     │   │
│   └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└──────────────────────────────────┬───────────────────────────────────────────┘
                                   │
                                   ▼
                        ┌────────────────────────┐
                        │    EXTERNAL SYSTEMS    │
                        │                        │
                        │ Ticketera              │
                        │ Database               │
                        │ Slack                  │
                        │ Repositories           │
                        │ Documentation          │
                        └────────────────────────┘
```

