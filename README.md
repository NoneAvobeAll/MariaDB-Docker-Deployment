# MariaDB Docker Deployment

## Overview
Enterprise-grade MariaDB 11.4 LTS containerized deployment with comprehensive configuration management and production-ready optimizations.

---

**Author**: Abubakkar Khan  
**Position**: System Engineer  
**Organization**: Schertech  
**Version**: 1.0  
**Last Updated**: September 2025

---

## Deployment Instructions

### Minimal Deployment
Execute the following command for immediate deployment:
```bash
docker-compose up -d
```

✅ **Production Ready**: This configuration is fully functional without additional setup requirements.

## Configuration Specifications

### Default Parameters
- **Root Credentials**: `2hmAbH42984kff`
- **Application Database**: `backend` (automatically provisioned)
- **Application User**: `appuser`
- **Application Password**: `SuperAjairaPassword`
- **External Port**: `3308` → Internal Port: `3306`

## Database Connectivity

### Administrative Access
```bash
mysql -h 127.0.0.1 -P 3308 -u root -p
```

### Application Connection String
```
mysql://appuser:apppass@localhost:3308/appdb
```

## Advanced Configuration (Optional)

### Environment Variable Management

Create `.env` file for production credential management:

```env
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_DATABASE=your_app_database
MYSQL_USER=your_app_user
MYSQL_PASSWORD=your_app_password
```

**Implementation Status**: ✅ **Optional** - Recommended for production environments

### Performance Optimization Configuration

Directory structure for advanced customization:

```bash
mkdir -p mariadb/conf.d mariadb/init
```

#### Database Engine Optimization
**Location**: `mariadb/conf.d/custom.cnf`
```ini
[mysqld]
# Performance optimizations
innodb_flush_log_at_trx_commit = 2
innodb_file_per_table = 1
query_cache_type = 1
query_cache_size = 256M

# Security enhancements
skip_name_resolve = 1
```

**Implementation Status**: ✅ **Optional** - Performance tuning only

#### Database Schema Initialization
**Location**: `mariadb/init/01-init.sql`
```sql
-- Automated schema deployment
-- Executes during initial container provisioning

USE appdb;

CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Implementation Status**: ✅ **Optional** - Custom schema requirements only

## Complete Folder Structure

```
your-project-folder/
├── docker-compose.yml         # ✅ Required
├── .env                       # ✅ Optional (recommended)
└── mariadb/                   # ✅ Optional (advanced config)
    ├── conf.d/
    │   └── custom.cnf         # ✅ Optional
    └── init/
        └── 01-init.sql        # ✅ Optional
```

## What Happens Without Optional Files?

| Missing File/Folder | Result |
|---------------------|--------|
| `.env` | Uses default credentials from compose file |
| `mariadb/conf.d/` | Uses MariaDB default configuration |
| `mariadb/init/` | Only creates the database specified in `MYSQL_DATABASE` |
| Everything optional | **Works perfectly!** Uses all defaults |

## Container Management

```bash
# Start container
docker-compose up -d

# Stop container
docker-compose down

# View logs
docker-compose logs -f mariadb

# Restart container
docker-compose restart mariadb

# Remove everything (including data)
docker-compose down -v
```

## System Specifications

### Resource Allocation
- **CPU Allocation**: 2.0 cores maximum, 1.0 core reserved
- **Memory Allocation**: 4GB maximum, 1GB reserved  
- **InnoDB Buffer Pool**: 1GB (configurable based on host specifications)
- **Connection Pool**: 4000 concurrent connections maximum

### Storage Management
- **Data Persistence**: Docker volume `mariadb_data3308`
- **Configuration Persistence**: Survives container lifecycle operations
- **Backup Strategy**: Standard `docker-compose down` preserves data, `docker-compose down -v` performs complete removal

## Security Framework

### Access Control
- Modify default credentials for production deployments
- Implement `.env` file for credential externalization
- Consider Docker secrets integration for enterprise environments
- Network isolation via dedicated bridge network (`mariadb_net`)

### Monitoring and Health Checks
- **Health Check Interval**: 30-second monitoring cycles
- **Timeout Threshold**: 10-second response limit
- **Retry Logic**: 5 consecutive failure tolerance
- **Log Management**: JSON driver with 100MB rotation, 3-file retention

## Operations and Maintenance

### Performance Monitoring
```bash
# Resource utilization
docker stats mariadb_server_3308

# Connection monitoring
docker exec mariadb_server_3308 mysql -u root -p -e "SHOW PROCESSLIST;"

# Performance metrics
docker exec mariadb_server_3308 mysql -u root -p -e "SHOW GLOBAL STATUS LIKE 'Connections';"
```

### Troubleshooting Procedures

**Service Initialization Failure**:
```bash
docker-compose logs mariadb
```

**Network Connectivity Issues**:
- Verify port 3308 availability on host system
- Monitor health check status (maximum 2.5 minutes for service ready state)

**Performance Degradation**:
- Adjust `innodb-buffer-pool-size` parameter based on workload requirements
- Modify resource constraints in deployment configuration

---

**Deployment Summary**: Execute `docker-compose up -d` for immediate production deployment. All supplementary configurations are optional enhancements.
