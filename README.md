# neo4j test på kthcloud

## Vad gör configsen

```mermaid
graph TB
    subgraph "Yttre proxyn/loadbalancern"
        NGINX_Proxy["NGINX Proxy *.app.cloud.cbh.kth.se"]
    end

    subgraph "deploy"
        NGINX_Internal["NGINX (deployment)"]
        Neo4j["Neo4j (deployment)"]
    end

    NGINX_Proxy -- "HTTPS (443) → HTTP (7474)" --> Neo4j

    NGINX_Proxy -- "WebSocket/Bolt (443 → 7687)" --> NGINX_Internal
    NGINX_Internal -- "Stream (0.0.0.0:7687 → [neo4j].deploy.svc.cluster.local:7687)" --> Neo4j
```

## Deploya med `kthcloud-cli`

> [!NOTE]
> Namnen måste vara unika i `kthcloud.docker-compose.yml` så ändra dom om du vill deploya från den.

```bash
kthcloud login # logga in
# öppna storage managern i browsern så vi kan sno cookies
kthcloud compose sm check   # om den inte ger Passed så försök igen (funkar ej på windows)
                            # alternativit kan man manuellt skapa mapparna via browsern
kthcloud compose up --try-volumes   # try volumes flaggan försöker skapa mapparna och filerna som ska mountas
                                    # lägg till -d om du vill detatcha från deploymentsen, default betende ger SSE strömmar med logs från alla deployments
                                    # ctrl + c kan användas för att detactha och man har då möjligheten att disablea deploymentsen
kthcloud compose down  # tar ner alla deployments som beskrivs i filen
``` 