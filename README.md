# Docker Database Stack

A production-ready Docker Compose setup for PostgreSQL, Redis, and RabbitMQ with optimized configurations, health checks, and comprehensive management tools.

## 🚀 Features

- **PostgreSQL 17** - Primary database with optimized configuration
- **Redis 7** - High-performance caching and session storage
- **RabbitMQ 4** - Message broker with management interface
- **Production-ready** configurations with resource limits
- **Health checks** for all services
- **Persistent data** storage with bind mounts
- **Security hardening** with disabled dangerous commands
- **Comprehensive Makefile** for easy management
- **Automated initialization** scripts

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose 2.0+
- Make utility
- 24GB+ RAM recommended (configurable)
- 8+ vCPU recommended (configurable)

## 🛠 Quick Start

### 1. Clone and Setup

```bash
git clone <repository-url>
cd docker-db
make setup
```

### 2. Configure Environment

Edit `.env` file with your credentials:

```bash
# PostgreSQL Configuration
POSTGRES_DB=zinixgames
POSTGRES_PORT=5432
POSTGRES_USER=zinix_admin
POSTGRES_PASS=your_secure_password

# Redis Configuration  
REDIS_PORT=6379
REDIS_USER=zinix_admin
REDIS_PASS=your_redis_password

# RabbitMQ Configuration
RABBITMQ_VHOST=/
RABBITMQ_PORT=5672
RABBITMQ_USER=zinix_admin
RABBITMQ_PASS=your_rabbitmq_password
RABBITMQ_MANAGEMENT_PORT=15672

# Data Storage Path
DATA_PATH=./data
```

### 3. Start Services

```bash
make start
```

### 4. Verify Health

```bash
make health
```

## 📊 Service Details

### PostgreSQL 17
- **Port**: 5432
- **Database**: zinixgames
- **Memory**: 16GB limit
- **CPU**: 4 cores limit
- **Features**: 
  - Optimized for 16GB RAM allocation
  - WAL replication enabled
  - Performance tuning for high concurrency
  - Custom timezone (Asia/Kolkata)

### Redis 7
- **Port**: 6379
- **Memory**: 4GB limit with LRU eviction
- **CPU**: 2 cores limit
- **Features**:
  - AOF persistence with everysec sync
  - Daily RDB snapshots
  - Dangerous commands disabled
  - IO threads for performance

### RabbitMQ 4
- **AMQP Port**: 5672
- **Management UI**: 15672
- **Memory**: 4GB limit
- **CPU**: 2 cores limit
- **Features**:
  - Management plugin enabled
  - Health checks configured
  - Custom virtual host support

## 🎯 Available Commands

### Setup & Management
```bash
make setup          # Initial setup - create directories and env file
make start           # Start all services
make stop            # Stop all services  
make restart         # Restart all services
make clean           # Stop and remove all data (⚠️ destructive)
```

### Monitoring & Status
```bash
make status          # Show service status
make ps              # Show running containers
make logs            # Show service logs (follow mode)
make health          # Check service health
make resources       # Show resource usage vs limits
make stats           # Live resource monitoring
make disk-usage      # Show disk usage
make docker-size     # Show Docker space usage
```

### Database Operations
```bash
make shell-pg        # Connect to PostgreSQL shell
make db-list         # List all databases
make db-tables       # List tables in database
make shell-redis     # Connect to Redis shell
make shell-rabbitmq  # Connect to RabbitMQ CLI
```

### Information
```bash
make info            # Show connection information
make rabbitmq-info   # Show RabbitMQ detailed status
make help            # Show all available commands
```

## 🔗 Connection Strings

### PostgreSQL
```
postgresql://zinix_admin:your_password@localhost:5432/zinixgames
```

### Redis
```
redis://:your_password@localhost:6379
```

### RabbitMQ
```
amqp://zinix_admin:your_password@localhost:5672/
```

### RabbitMQ Management UI
```
http://localhost:15672
Username: zinix_admin
Password: your_rabbitmq_password
```

## 📁 Project Structure

```
docker-db/
├── README.md                 # This file
├── LICENSE                   # MIT License
├── Makefile                  # Management commands
├── docker.yaml               # Docker Compose configuration
├── .env                      # Environment variables (create from .env.example)
├── .env.example              # Environment template
├── .gitignore               # Git ignore rules
├── conf/                    # Configuration files
│   ├── postgres.conf        # PostgreSQL configuration
│   └── redis.conf           # Redis configuration
├── pgsql/                   # PostgreSQL initialization
│   └── init-db.sql          # Database initialization script
└── data/                    # Persistent data (auto-created)
    ├── postgres/            # PostgreSQL data
    ├── redis/               # Redis data
    └── rabbitmq/            # RabbitMQ data
```

## ⚙️ Configuration Details

### PostgreSQL Configuration
- **Shared Buffers**: 1536MB (optimized for 16GB allocation)
- **Work Memory**: 16MB per operation
- **Maintenance Work Memory**: 512MB
- **Effective Cache Size**: 4096MB
- **Max Connections**: 200
- **WAL Level**: Replica (for replication support)
- **Checkpoint Target**: 0.9 (performance optimization)

### Redis Configuration
- **Max Memory**: 6GB with LRU eviction policy
- **Persistence**: AOF + RDB combination
- **AOF Sync**: Every second (balance of performance/durability)
- **RDB Snapshots**: Daily (86400 seconds, 1 change)
- **Security**: Dangerous commands renamed/disabled
- **Performance**: 4 IO threads enabled

### RabbitMQ Configuration
- **Default Virtual Host**: /
- **Management Plugin**: Enabled
- **Health Checks**: 30s interval with 3 retries
- **Resource Limits**: 4GB RAM, 2 CPU cores

## 🔒 Security Features

- **No new privileges** security option for all containers
- **Protected mode** enabled for Redis
- **Dangerous Redis commands** disabled (DEBUG, FLUSHDB, FLUSHALL, CONFIG)
- **SCRAM-SHA-256** authentication for PostgreSQL
- **Custom renamed commands** for Redis CONFIG access
- **Network isolation** with custom bridge network

## 📈 Resource Allocation

**Total System Requirements:**
- **RAM**: 24GB (16GB PostgreSQL + 4GB Redis + 4GB RabbitMQ)
- **CPU**: 8 cores (4 PostgreSQL + 2 Redis + 2 RabbitMQ)
- **Storage**: Depends on data size (persistent volumes)

**Network:**
- **Subnet**: 172.20.0.0/16
- **Driver**: Bridge with custom network

## 🚨 Important Notes

### Data Persistence
- All data is stored in `./data/` directory
- Data survives container restarts
- Use `make clean` to completely remove all data (⚠️ irreversible)

### First Run
- PostgreSQL will initialize with the database specified in `.env`
- Redis will start with empty dataset
- RabbitMQ will create default user and virtual host

### Backups
- PostgreSQL: Use `pg_dump` via `make shell-pg`
- Redis: RDB files are created daily in `./data/redis/`
- RabbitMQ: Configuration and queues persist in `./data/rabbitmq/`

### Scaling
- Adjust resource limits in `docker.yaml`
- Modify configuration files in `conf/` directory
- Update memory settings in PostgreSQL and Redis configs accordingly

## 🐛 Troubleshooting

### Services Won't Start
```bash
make logs           # Check service logs
make health         # Verify health status
docker ps -a        # Check container status
```

### Configuration Issues
```bash
# Restart with fresh configuration
make stop
make start

# Complete reset (⚠️ loses data)
make clean
make start
```

### Permission Issues
```bash
# Fix data directory permissions
sudo chown -R $(id -u):$(id -g) ./data/
```

### Memory Issues
- Reduce memory limits in `docker.yaml`
- Adjust PostgreSQL `shared_buffers` in `conf/postgres.conf`
- Lower Redis `maxmemory` in `conf/redis.conf`

## 📝 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📞 Support

For issues and questions:
- Check the troubleshooting section
- Review service logs with `make logs`
- Verify configuration files in `conf/` directory
- Ensure environment variables are properly set in `.env`

---

**Author**: Aashish Panchal  
**Project**: ZinixGames Database Stack  
**Year**: 2025
