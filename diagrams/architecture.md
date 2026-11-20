
# Adventis Global — Lab 03 Architecture

```mermaid
flowchart TD
    USER["Windows Browser<br/>localhost:8080"]
    SSH["Encrypted SSH Tunnel<br/>Port Forwarding"]

    subgraph AZURE["Microsoft Azure"]
        subgraph VM["ADV-WEB01 — Ubuntu 24.04"]
            NGINX["Nginx<br/>Port 80"]
            GUNICORN["Gunicorn<br/>Port 8000"]
            FLASK["Flask Employee Portal"]
            MI["System-Assigned Managed Identity"]

            NGINX --> GUNICORN
            GUNICORN --> FLASK
        end

        KV["Azure Key Vault<br/>adv-kv-lab03-2"]
        SQL["Azure SQL Database<br/>ADV-EmployeeDB"]
    end

    USER --> SSH
    SSH --> NGINX

    FLASK --> MI
    MI -->|"Authenticate and retrieve secret"| KV
    KV -.->|"SQL password returned to application"| FLASK
    FLASK -->|"Encrypted SQL connection<br/>TCP 1433"| SQL
```

## Architecture Notes

- The Ubuntu VM hosts Nginx, Gunicorn and Flask.
- Nginx forwards requests to Gunicorn on localhost.
- The VM uses a system-assigned managed identity to access Azure Key Vault.
- Key Vault stores the SQL administrator password.
- Flask retrieves the password at runtime and queries Azure SQL.
- The portal was accessed through an SSH tunnel without publicly exposing HTTP port 80.
- Azure SQL and Key Vault were deployed in UK South; the VM was deployed in East US.
