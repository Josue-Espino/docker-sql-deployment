# Docker SQL Server Homelab

## Quickstart

1. Set the `MSSQL_SA_PASSWORD` environment variable locally.
2. Run `docker-compose up -d`.
3. Initialize the lab database:
   ```bash
   docker exec mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U SA -P "$MSSQL_SA_PASSWORD" -i init_labdata.sql
   ```

## Files

- `docker-compose.yml`: spins up the SQL Server container
- `init_labdata.sql`: initializes the `labdata` database
- `docker-run.txt`: original `docker run` command template

## Security Review

**Review status:** Audited  
**Last reviewed:** September 2026

### TCP/1433 exposure

The SQL Server container publishes TCP port 1433:

```yaml
ports:
  - "1433:1433"
```

This makes SQL Server reachable through the Docker host's published port. Publishing a database port is not inherently a vulnerability in an isolated homelab, but the access boundary should match the systems that actually need SQL connectivity.

### Planned remediation

Before changing the port mapping, the existing deployment should be checked to determine:

- which hosts or applications require SQL Server access;
- whether access needs to remain available across the LAN;
- whether the port can be restricted to a specific host address or management/application sources;
- whether a host firewall should provide an additional access boundary.

**No network configuration change is being made as part of this documentation update.**

### Validation plan

After the intended access boundary is chosen:

1. Validate the Docker Compose configuration.
2. Apply the smallest necessary port/access change.
3. Confirm authorized SQL connectivity.
4. Confirm unauthorized sources can no longer reach TCP/1433.
5. Verify the container and database remain healthy.

## Credential handling

The SA password must be supplied through the local `MSSQL_SA_PASSWORD` environment variable and must not be committed to the repository.

The password that was previously committed to this project is considered compromised and must not be reused.

## Container Image Versioning

**Review status:** Audited  
**Last reviewed:** September 2026

The SQL Server Compose deployment currently uses:

```yaml
image: mcr.microsoft.com/mssql/server:2022-latest
```

The `2022-latest` tag keeps the deployment within the SQL Server 2022 release family, but it still allows the image revision to change when the environment is rebuilt.

This is primarily a reproducibility and change-control concern. A future rebuild could use a different image revision than the one previously validated.

### Planned remediation

The next step is to determine the exact SQL Server image currently running in the live homelab before changing the Compose file.

The remediation will then:

1. Record the currently validated SQL Server image/version.
2. Select an explicit version appropriate for the lab.
3. Update the Compose file to use that version.
4. Recreate the container in a controlled maintenance window.
5. Validate database availability, authentication, persistent data, and application connectivity.

**No image tag has been changed as part of this documentation update.**

Version pinning should be treated as a controlled upgrade task rather than an automatic replacement.
