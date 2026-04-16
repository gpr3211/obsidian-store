## Some endpoints
1. Services Catalog Endpoints:

- `/v1/catalog/services`: List all services
- `/v1/catalog/service/{serviceName}`: Get all instances of a specific service
- `/v1/catalog/nodes`: List all nodes in the cluster

2. Health Check Endpoints:

- `/v1/health/service/{serviceName}`: Get health information for a specific service
- `/v1/health/checks/{serviceName}`: Get specific health checks for a service
- `/v1/health/node/{nodeName}`: Get health checks for a specific node

3. Key-Value Store Endpoints:

- `/v1/kv/{key}`: Read/write/delete key-value pairs
- `/v1/kv/{key}?recurse=true`: List all keys with a given prefix

4. Agent Endpoints:

- `/v1/agent/members`: List members of the Consul cluster
- `/v1/agent/self`: Get local agent configuration
- `/v1/agent/services`: List services registered with the local agent

5. Status Endpoints:

- `/v1/status/leader`: Get the Raft leader
- `/v1/status/peers`: List Raft peers
