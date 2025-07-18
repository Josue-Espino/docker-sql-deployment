# Docker SQL Server Homelab

## Quickstart

1. `docker-compose up -d`
2. `docker exec mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U SA -P "P@ssw0rd2025" -i init_labdata.sql`

## Files

- `docker-compose.yml`: spins up the SQL Server container  
- `init_labdata.sql`: initializes the `labdata` database  
- `docker-run.txt`: original `docker run` command  
