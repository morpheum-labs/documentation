# 🚀 **Morpheum Infrastructure Orchestrator**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go)](https://golang.org/)
[![Docker](https://img.shields.io/badge/Docker-Required-2496ED?logo=docker)](https://www.docker.com/)

**Morpheum Orchestrator** is a comprehensive infrastructure management system for the Morpheum blockchain ecosystem. It orchestrates the deployment, scaling, monitoring, and management of all node types including consensus validators, market daemons, router daemons, bucket engines, bridge nodes, and blockchain explorers.

## 🎯 **Features**

### **Git-Integrated Consensus Deployment**
- ✅ **Clone and build from Git repositories** - Deploy directly from any branch or tag
- ✅ **Local and remote repository support** - Use GitHub, GitLab, or local repositories
- ✅ **Automatic Docker image building** - Build custom images from source code
- ✅ **Multi-node cluster deployment** - Deploy clusters with automatic port management
- ✅ **Environment-specific resource scaling** - Automatic resource allocation per environment

### **Node Lifecycle Management**
- ✅ Automated provisioning of validators, full nodes, and light clients
- ✅ Dynamic scaling based on network demand and DEX activity
- ✅ Graceful shutdown with data consistency guarantees
- ✅ Rolling upgrades with zero downtime
- ✅ Automatic failure detection and recovery

### **Multi-Node Type Support**
- **Consensus Nodes**: Validators, leaf nodes, aggregator nodes, root nodes
- **Market Daemons**: Per-pair trading engines (BTC/USDT, ETH/USDT, etc.)
- **Router Daemons**: API gateways and request routers
- **Risk Engines**: Liquidation and margin management
- **Bridge Nodes**: Cross-chain communication
- **Blockchain Explorers**: Data indexing and APIs
- **Monitoring Nodes**: Metrics collection and alerting

### **Configuration Management**
- ✅ Environment-specific configurations (testnet, mainnet, development)
- ✅ Dynamic configuration updates without restarts
- ✅ Secure secrets management
- ✅ Template-based configuration generation

### **Deployment Orchestration**
- ✅ Docker Compose for local development
- ✅ Kubernetes manifests for production
- ✅ Cloud provider integrations (AWS, GCP, Azure)
- ✅ Multi-region deployment strategies
- ✅ Load balancing and traffic routing

### **Monitoring & Health Checks**
- ✅ Real-time node health monitoring
- ✅ Performance metrics collection
- ✅ Automated alerting and notifications
- ✅ Centralized logging and audit trails
- ✅ Network topology visualization

### **Security & Compliance**
- ✅ Role-based access control (RBAC)
- ✅ TLS encryption for all communications
- ✅ DDoS protection and rate limiting
- ✅ Compliance reporting and audit logs
- ✅ Secure key management and rotation

## 🏗️ **Architecture**

The orchestrator follows **Domain-Driven Design** principles with **Hexagonal Architecture** for clean separation of concerns:

```
📁 domains/           # Business logic domains
├── node/            # Node lifecycle management
├── config/          # Configuration management
├── deployment/      # Infrastructure deployment
├── monitoring/      # Health checks and metrics
├── security/        # Access control and secrets
└── network/         # Network topology management

📁 infrastructure/    # External integrations
├── docker/          # Docker container management
├── k8s/            # Kubernetes orchestration
├── cloud/          # Cloud provider APIs
└── monitoring/     # Prometheus, Grafana, etc.

📁 application/      # Use cases and orchestration
├── services/       # Application services
├── commands/       # Command handlers
├── queries/        # Query handlers
└── events/         # Event handling

📁 interfaces/       # External interfaces
├── api/            # REST/gRPC APIs
├── cli/            # Command-line interface
└── web/            # Web dashboard (optional)
```

## 🚀 **Quick Start**

### **Prerequisites**
- Go 1.21+
- Docker & Docker Compose
- Local consensus project (available at `../consensus` relative to orchestrator)

### **Installation**
```bash
# Clone the repository (if not already done)
git clone https://github.com/morpheum/orchestrator.git
cd orchestrator

# Build the orchestrator
make build

# Create minimal config file
cat > config.yaml << EOF
environment: development
database:
  database: orchestrator_dev
deployment:
  cloud:
    provider: docker
EOF
```

## 🌐 **Dynamic Multi-Validator Local Swarms**

The orchestrator now supports **dynamic deployment** of any number of validators that form a **unified consensus network**. This replaces the old static docker-compose approach with a flexible, scalable solution.

### **🔥 Quick Deploy Commands**

#### **Deploy 2 Validators (Minimal Swarm)**
```bash
./bin/orchestrator nodes deploy basic-swarm --validators 2 --workspace ./deployments
cd ./deployments/basic-swarm && docker-compose up -d --build
```

#### **Deploy 5 Validators (Standard Swarm)**
```bash
./bin/orchestrator nodes deploy standard-swarm --validators 5 --workspace ./deployments
cd ./deployments/standard-swarm && docker-compose up -d --build
```

#### **Deploy 10 Validators (Large Swarm)**
```bash
./bin/orchestrator nodes deploy large-swarm --validators 10 --workspace ./deployments
cd ./deployments/large-swarm && docker-compose up -d --build
```

#### **Deploy Mixed Node Types (Validators + Sentries)**
```bash
# 3 Validators + 2 Sentries (IMPORTANT: Use --build flag!)
./bin/orchestrator nodes deploy mixed-swarm --validators 3 --sentries 2 --workspace ./deployments
cd ./deployments/mixed-swarm && docker-compose up -d --build

# 2 Validators + 1 Sentry + 1 Observer
./bin/orchestrator nodes deploy production-swarm \
  --validators 2 \
  --sentries 1 \
  --observers 1 \
  --workspace ./deployments
cd ./deployments/production-swarm && docker-compose up -d --build
```

#### **Benchmark Testing 5 Validators 5000000txs**
```bash
./orchestrator benchmark consensus --nodes 5 --tx-counts 5000000 --timeout 20m
```

**⚠️ IMPORTANT**: Always use the `--build` flag when starting mixed deployments to ensure containers include the latest consensus code with sentry voting fixes.

### **📊 Port Allocation**

The orchestrator automatically allocates unique ports for each node type:

| Node Type | Port Range | Example Ports |
|-----------|------------|---------------|
| Validators | 8000-8999 | API: 8000, P2P: 8004, DB: 8005 |
| Sentries | 9000-9999 | API: 9000, P2P: 9004 |
| Observers | 10000+ | API: 10000, P2P: 10004 |

**Example for 3 validators:**
- **validator-1**: API: `localhost:8000`, P2P: `localhost:8004`, Database: `localhost:8005`
- **validator-2**: API: `localhost:8010`, P2P: `localhost:8014`, Database: `localhost:8015`  
- **validator-3**: API: `localhost:8020`, P2P: `localhost:8024`, Database: `localhost:8025`

### **🎯 Unified Consensus Verification**

After deployment, verify all nodes are producing **identical blocks**:

#### **For Validator-Only Deployments:**
```bash
# Check all validators have same block height and hash
cd ./deployments/your-swarm-name
for i in {1..5}; do
  echo "VALIDATOR-$i:"
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done
```

#### **For Mixed Deployments (Validators + Sentries):**
```bash
# Check validators
cd ./deployments/mixed-swarm
echo "=== Validators ==="
for i in {1..3}; do
  echo "VALIDATOR-$i:"
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done

# Check sentries
echo "=== Sentries ==="
for i in {1..2}; do
  echo "SENTRY-$i:"
  docker-compose logs sentry-$i | grep -E "(Hash:|STORED|BROADCASTED)" | tail -1
done

# Verify sentry voting behavior
echo "=== Sentry Voting Check ==="
docker-compose logs sentry-1 | grep "SENTRY MODE" | tail -3
```

**Success indicators:**
- ✅ All validators show **identical block heights**
- ✅ All validators show **identical block hashes**  
- ✅ **All sentries show identical block hashes** (same as validators)
- ✅ Logs show **"📡 BLOCK BROADCASTED to X peers"** (X > 0)
- ✅ Logs show **"📥 BLOCK RECEIVED from peer"** messages
- ✅ Sentries show **"🔇 SENTRY MODE: Skipping vote casting"** (no voting conflicts)

### **🔧 Management Commands**

#### **List Deployments**
```bash
./bin/orchestrator nodes list
```

#### **Check Node Status**
```bash
./bin/orchestrator nodes status your-swarm-name
```

#### **Scale Existing Swarm**
```bash
# Scale up to 7 validators
./bin/orchestrator nodes scale your-swarm-name --validators 7

# Scale down to 3 validators  
./bin/orchestrator nodes scale your-swarm-name --validators 3
```

#### **Stop Swarm**
```bash
./bin/orchestrator nodes stop your-swarm-name
# Or manually:
cd ./deployments/your-swarm-name && docker-compose down
```

### **Example: Deploy Market Daemon Cluster**
```bash
# Deploy market daemons for multiple trading pairs
./bin/orchestrator deploy market-daemons \
  --pairs BTC/USDT,ETH/USDT,SOL/USDT \
  --instances-per-pair 2 \
  --region us-west-2
```

### **🚨 Troubleshooting Guide**

#### **Problem: Containers fail to start**
```bash
# 1. Check port conflicts
lsof -i :8000-8100 || echo "Ports available"

# 2. Clean up previous deployments
docker-compose down -v
docker system prune -f

# 3. Check Docker resources
docker system df
```

#### **Problem: Sentry nodes restarting continuously**
```bash
# This happens when cached Docker images don't include sentry voting fixes
# SOLUTION: Always use --build flag for mixed deployments
docker-compose down -v
docker-compose up -d --build
```

#### **Problem: Old cached images**
```bash
# Remove old images and rebuild with latest fixes
docker-compose down -v
docker-compose up -d --build
```

#### **Problem: Genesis block coordination issues**
```bash
# 1. Verify shared genesis exists
ls -la ../shared-genesis/genesis.json

# 2. Check mount path in compose file
grep -A 2 "shared-genesis" docker-compose.yml

# 3. Restart with clean volumes
docker-compose down -v && docker-compose up -d
```

### **🔐 Sentry Nodes**

Sentry nodes are non-validator nodes that participate in the network by:
- **Receiving and storing blocks** from validators
- **Broadcasting blocks** to other peers  
- **Offloading network tasks** from validators (API serving, data streaming)
- **NOT participating in voting** (no consensus participation)

#### **Sentry Node Features:**
- ✅ **P2P Network Participation**: Connect to all network peers
- ✅ **Block Synchronization**: Receive and store identical blocks as validators
- ✅ **API Endpoints**: Serve blockchain data via REST/RPC APIs
- ✅ **Data Streaming**: Stream trading data, fills, and market events
- ✅ **No Voting Conflicts**: Automatically skip consensus voting
- ✅ **Resource Efficient**: Lower hardware requirements than validators

#### **Example Sentry Deployments:**
```bash
# Basic mixed deployment: 2 validators + 1 sentry
./bin/orchestrator nodes deploy basic-mixed --validators 2 --sentries 1 --workspace ./deployments
cd ./deployments/basic-mixed && docker-compose up -d --build

# Production setup: 3 validators + 2 sentries + 1 observer
./bin/orchestrator nodes deploy production --validators 3 --sentries 2 --observers 1 --workspace ./deployments
cd ./deployments/production && docker-compose up -d --build

# High availability: 5 validators + 3 sentries
./bin/orchestrator nodes deploy ha-setup --validators 5 --sentries 3 --workspace ./deployments
cd ./deployments/ha-setup && docker-compose up -d --build
```

#### **Sentry Node Ports:**
Sentries use the 9000+ port range:
- **sentry-1**: API: `localhost:9030`, P2P: `localhost:9034`, Database: `localhost:9035`
- **sentry-2**: API: `localhost:9040`, P2P: `localhost:9044`, Database: `localhost:9045`

#### **🎯 Key Success Factors:**
1. **Always use `--build` flag** for mixed deployments with sentries
2. **Verify unified consensus** - all nodes must show identical block hashes
3. **Check sentry logs** for `"🔇 SENTRY MODE: Skipping vote casting"` messages
4. **Confirm P2P connectivity** - sentries should broadcast to multiple peers
5. **No container restarts** - stable operation indicates successful deployment

### **🔄 Lifecycle Management**

#### **Testing Different Swarm Sizes**
```bash
# Test 2 → 4 → 6 → 2 validator lifecycle
./bin/orchestrator nodes deploy test-swarm --validators 2 --workspace ./test
cd ./test/test-swarm && docker-compose up -d

# Wait for consensus, then scale up
./bin/orchestrator nodes scale test-swarm --validators 4
docker-compose up -d

# Scale up again
./bin/orchestrator nodes scale test-swarm --validators 6  
docker-compose up -d

# Scale back down
./bin/orchestrator nodes scale test-swarm --validators 2
docker-compose up -d

# Clean up
docker-compose down -v
```

#### **Performance Testing**
```bash
# Deploy large swarm for stress testing
./bin/orchestrator nodes deploy stress-test --validators 20 --workspace ./test
cd ./test/stress-test && docker-compose up -d

# Monitor consensus performance
while true; do
  echo "=== $(date) ==="
  docker-compose logs --tail=100 | grep "Hash:" | tail -5
  sleep 5
done
```

## 🎯 **Legacy Multi-Node Guide (Deprecated)**

> ⚠️ **Note**: The static `deployments/multi-node` approach is **deprecated**. Use the new **dynamic node deployment** system above for better scalability and flexibility.

For legacy compatibility, the old static 2-node deployment is still available:

```bash
cd /Users/blee/projects/MorpheumLab/orchestrator/deployments/multi-node
docker-compose up -d
```

**Migration to Dynamic System:**
```bash
# Replace old static deployment with dynamic equivalent
./bin/orchestrator nodes deploy migrated-cluster --validators 2 --workspace ./deployments
cd ./deployments/migrated-cluster && docker-compose up -d
```

## 📋 **Validator Swarm Examples**

### **Example 1: Minimal Development Setup (2 Validators)**
Perfect for local development and testing:

```bash
# Deploy minimal swarm
./bin/orchestrator nodes deploy dev-minimal --validators 2 --workspace ./deployments
cd ./deployments/dev-minimal && docker-compose up -d

# Verify unified consensus
sleep 30
for i in {1..2}; do
  echo "VALIDATOR-$i:"
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done

# Expected: Both validators show identical hashes
```

### **Example 2: Small Testnet (3 Validators)**
Good for testing consensus with Byzantine fault tolerance:

```bash
# Deploy small testnet
./bin/orchestrator nodes deploy testnet-small \
  --validators 3 \
  --environment testnet \
  --workspace ./deployments

cd ./deployments/testnet-small && docker-compose up -d

# Monitor real-time consensus
docker-compose logs -f | grep -E "(PROPOSER PRODUCED|NON-PROPOSER RECEIVED)"
```

### **Example 3: Standard Network (5 Validators)**
Recommended for testing production-like scenarios:

```bash
# Deploy standard network
./bin/orchestrator nodes deploy standard-net --validators 5 --workspace ./deployments
cd ./deployments/standard-net && docker-compose up -d

# Check peer connectivity (should show 4 peers for each validator)
docker-compose logs | grep "BLOCK BROADCASTED.*to.*peers" | tail -5
```

### **Example 4: Large Network (10 Validators)**
For stress testing and performance evaluation:

```bash
# Deploy large network
./bin/orchestrator nodes deploy large-net --validators 10 --workspace ./deployments
cd ./deployments/large-net && docker-compose up -d

# Monitor consensus performance
while true; do
  echo "=== $(date) ==="
  echo "Latest blocks from all validators:"
  for i in {1..10}; do
    latest=$(docker-compose logs validator-$i | grep "Hash:" | tail -1 | grep -o "Height [0-9]*.*Hash: [a-f0-9]*")
    echo "  validator-$i: $latest"
  done
  echo ""
  sleep 10
done
```

### **Example 5: Production Simulation (Mixed Node Types)**
Simulates a production environment with different node roles:

```bash
# Deploy production-like setup
./bin/orchestrator nodes deploy prod-sim \
  --validators 4 \
  --sentries 2 \
  --observers 2 \
  --environment mainnet \
  --workspace ./deployments

cd ./deployments/prod-sim && docker-compose up -d

# Check different node types are running
docker-compose ps
echo ""
echo "Validator endpoints:"
for i in {1..4}; do
  echo "  validator-$i: http://localhost:$((8000 + (i-1)*10))"
done
echo ""
echo "Sentry endpoints:"
for i in {1..2}; do
  echo "  sentry-$i: http://localhost:$((9000 + (i-1)*10))"
done
echo ""
echo "Observer endpoints:"
for i in {1..2}; do
  echo "  observer-$i: http://localhost:$((10000 + (i-1)*10))"
done
```

### **Example 6: Dynamic Scaling Demo**
Demonstrates runtime scaling capabilities:

```bash
# Start with small swarm
./bin/orchestrator nodes deploy scaling-demo --validators 2 --workspace ./deployments
cd ./deployments/scaling-demo && docker-compose up -d

echo "Started with 2 validators..."
sleep 30

# Scale up to 4
echo "Scaling up to 4 validators..."
cd /Users/blee/projects/MorpheumLab/orchestrator
./bin/orchestrator nodes scale scaling-demo --validators 4
cd ./deployments/scaling-demo && docker-compose up -d
sleep 30

# Scale up to 6
echo "Scaling up to 6 validators..."
cd /Users/blee/projects/MorpheumLab/orchestrator
./bin/orchestrator nodes scale scaling-demo --validators 6
cd ./deployments/scaling-demo && docker-compose up -d
sleep 30

# Verify all 6 validators have unified consensus
echo "Verifying unified consensus across 6 validators:"
for i in {1..6}; do
  echo "VALIDATOR-$i:"
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done
```

### **Example 7: High-Availability Testing (Failure Simulation)**
Tests network resilience by stopping/starting validators:

```bash
# Deploy 5-validator network
./bin/orchestrator nodes deploy ha-test --validators 5 --workspace ./deployments
cd ./deployments/ha-test && docker-compose up -d
sleep 30

echo "=== Testing High Availability ==="

# Stop 2 validators (network should continue with 3)
echo "Stopping 2 validators..."
docker-compose stop validator-4 validator-5
sleep 20

# Check remaining 3 validators still have consensus
echo "Checking consensus with 3 validators:"
for i in {1..3}; do
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done

# Restart stopped validators
echo "Restarting validators..."
docker-compose start validator-4 validator-5
sleep 30

# Verify all 5 validators rejoin consensus
echo "Verifying all 5 validators rejoined:"
for i in {1..5}; do
  docker-compose logs validator-$i | grep "Hash:" | tail -1
done
```

## 📊 **Monitoring Dashboard**

Access the monitoring dashboard at `http://localhost:3000` to view:
- Node status and performance metrics
- Network topology and connections
- Transaction throughput and latency
- Resource utilization and costs
- Security events and alerts

## 🔧 **Configuration**

Example configuration for a testnet deployment:

```yaml
# config/environments/testnet.yaml
environment: testnet
network:
  chain_id: morpheum-testnet-1
  
clusters:
  consensus:
    nodes:
      validators: 7
      leaf_nodes: 12
      aggregator_nodes: 3
      root_nodes: 1
    
  market:
    pairs:
      - BTC/USDT
      - ETH/USDT
      - SOL/USDT
    instances_per_pair: 1
    
  infrastructure:
    monitoring: true
    explorer: true
    bridge: false

resources:
  cpu_limit: "2"
  memory_limit: "4Gi"
  storage: "100Gi"
```

## 🔐 **Security**

The orchestrator implements enterprise-grade security:
- **mTLS** for all inter-service communication
- **RBAC** with fine-grained permissions
- **Secrets encryption** at rest and in transit
- **Network policies** for micro-segmentation
- **Audit logging** for compliance

## 🤝 **Contributing**

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes and add tests
4. Run tests: `make test`
5. Submit a pull request

## 📖 **Documentation**

- [Architecture Guide](./docs/architecture/README.md)
- [API Reference](./docs/api/README.md)
- [Deployment Guide](./docs/deployment/README.md)
- [Configuration Reference](./docs/configuration/README.md)
- **[Dynamic Node Deployment Guide](./docs/DYNAMIC_NODE_DEPLOYMENT.md)** ⭐ **New!**
- **[Consensus Quick Reference](./docs/consensus-quick-reference.md)** ⭐

## 🎉 **What's New in Dynamic Node Deployment**

### **✅ Key Benefits**
- **🚀 Zero Configuration**: Deploy any number of validators with a single command
- **🔄 Dynamic Scaling**: Scale from 2 to 20+ validators without downtime
- **🌐 Unified Consensus**: All nodes produce identical blocks with same hashes
- **🔧 Auto Port Management**: No manual port configuration or conflicts
- **📊 Mixed Node Types**: Deploy validators, sentries, and observers together
- **🛡️ Production Ready**: LibP2P networking with peer discovery and fault tolerance

### **🚀 Quick Reference Commands**

| Use Case | Command |
|----------|---------|
| **Minimal Dev** | `./bin/orchestrator nodes deploy dev --validators 2 --workspace ./deployments` |
| **Standard Test** | `./bin/orchestrator nodes deploy test --validators 5 --workspace ./deployments` |
| **Production Sim** | `./bin/orchestrator nodes deploy prod --validators 4 --sentries 2 --observers 1 --workspace ./deployments` |
| **Large Network** | `./bin/orchestrator nodes deploy large --validators 10 --workspace ./deployments` |
| **Scale Up** | `./bin/orchestrator nodes scale cluster-name --validators 7` |
| **Check Status** | `./bin/orchestrator nodes status cluster-name` |
| **List All** | `./bin/orchestrator nodes list` |

### **📋 Success Verification Checklist**
```bash
cd ./deployments/your-cluster-name

# ✅ All containers running
docker-compose ps

# ✅ Unified consensus (identical hashes)
for i in {1..5}; do echo "VALIDATOR-$i:" && docker-compose logs validator-$i | grep "Hash:" | tail -1; done

# ✅ Peer connectivity (>0 peers)
docker-compose logs | grep "BLOCK BROADCASTED.*to.*peers" | tail -3

# ✅ Block progression
docker-compose logs | grep "Height.*Hash" | tail -10
```

### **🎯 Migration from Legacy**
```bash
# Replace old static deployments
cd /Users/blee/projects/MorpheumLab/orchestrator/deployments/multi-node
docker-compose down

# Use new dynamic system
cd /Users/blee/projects/MorpheumLab/orchestrator
./bin/orchestrator nodes deploy my-cluster --validators 2 --workspace ./deployments
cd ./deployments/my-cluster && docker-compose up -d
```

## 📜 **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Built with ❤️ by the Morpheum team**