# Card Network Participant Services

BIAN Service Domain microservice — part of the [bian-platform](../../bian-platform/) landscape.

| | |
|---|---|
| **Business Area** | Operations and Execution |
| **Business Domain** | Cards |
| **Functional Pattern** | Operate |
| **Asset Type** | Card Network Membership |
| **Control Record** | Card Network Membership Operating Session |
| **K8s Namespace** | `bian-operations` |
| **Stack** | Java 21 · Spring Boot 3 · Resilience4j · Cilium mesh |

> ⚠️ **Phase 1 (shallow):** real REST API over an in-memory store. Phase 2 replaces the store with per-domain persistence and real domain logic. This repo was stamped from `bian-platform/generator` — regenerate rather than hand-editing boilerplate.

## BIAN Semantic API

| Method | Path | BIAN action term |
|---|---|---|
| GET | `/v1/service-domain` | — (SD metadata) |
| POST | `/v1/card-network-membership-operating-session/initiate` | Initiate |
| GET | `/v1/card-network-membership-operating-session` | Retrieve (list) |
| GET | `/v1/card-network-membership-operating-session/{crId}/retrieve` | Retrieve |
| PUT | `/v1/card-network-membership-operating-session/{crId}/update` | Update |
| PUT | `/v1/card-network-membership-operating-session/{crId}/control` | Control — body `{"action": "suspend"\|"resume"\|"terminate"}` |

OpenAPI UI: `/swagger-ui.html` · Health: `/actuator/health` · Metrics: `/actuator/prometheus`

## Run locally

```bash
mvn spring-boot:run
curl localhost:8080/v1/service-domain

# lifecycle smoke test
curl -X POST localhost:8080/v1/card-network-membership-operating-session/initiate -H 'content-type: application/json' -d '{"note":"hello"}'
```

## Build & containerize

```bash
mvn -B verify
docker build -t bian/sd-card-network-participant-services:0.1.0 .
```

## Deploy (Helm → K8s with Cilium mesh)

```bash
helm upgrade --install sd-card-network-participant-services ./helm -n bian-operations
```

Exposed through the platform Gateway at path prefix `/sd-card-network-participant-services` (Cilium Gateway API). Mesh policy (`CiliumNetworkPolicy`) allows: gateway ingress, same-area peers, Prometheus — everything else denied.
