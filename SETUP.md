# Setup & Execution Guide

## Table of Contents

- [Keycloak Configuration](#keycloak-configuration)
  - [First Access and Administration](#first-access-and-administration)
  - [Realm Configuration](#realm-configuration)
  - [User and Role Management](#user-and-role-management)
  - [External Authentication](#external-authentication)
  - [Production Configuration](#production-configuration)
- [Execution: MQTT Script](#execution-mqtt-script)
- [MQTT Client Management](#mqtt-client-management)
  - [Main MQTT Manager](#main-mqtt-manager)
  - [Individual MQTT Client](#individual-mqtt-client)

## Keycloak Configuration

Keycloak provides authentication and authorization management for the project. Initial configuration is required before using any service (backend, frontend, scripts).

> **Console Access**: http://localhost:8080

### First Access and Administration

1. **Initial Access**:
   - Use the temporary credentials defined in `compose.yml`
   - You will access the "master" realm

2. **Create Main Administrator**:
   - Navigate to: Users -> Add user
   - Fill in the new administrator's details
   - In the Credentials tab: Set a non-temporary password
   - In Role mapping: Assign the "admin" role, filtering by "realm roles"
   - ⚠️ Delete the initial temporary user

3. **Passwords and Export/Import**:
   - When exporting a realm, passwords and secrets are not included for security reasons
   - After importing a realm, it is necessary to:
     - Regenerate and configure the client secrets for OAuth2 clients
     - Reconfigure credentials for Social Logins (Google, GitHub)
     - Update environment variables with the new secrets
   - It is recommended to document client IDs and secrets in a secure place

> **Note**: The admin role in the master realm grants full control over Keycloak. Do not confuse this with roles from other realms.

### Realm Configuration

1. **Create New Realm**:
   - Click on "Create realm"
   - Import the provided `realm-export.json` file
2. **MQTT Client Configuration**:
   - Navigate to: Clients -> mqtt-service -> Credentials
   - To regenerate the client_secret: Click on "Regenerate"
   - Update the new secret in the `.env` file

### User and Role Management

1. **Mandatory Groups**:
   - Every user must belong to one of these groups:
     - **admins**: Full access to the backend API
     - **users**: Restricted access to the backend API

2. **Server Access Groups**:
   - Every user must belong to at least one of these groups:
     - **all-servers**: Access to all servers (recommended only for administrators)
     - **servers-X**: Specific groups per server (e.g., servers-A, servers-B)
   - A user can belong to multiple servers-X groups simultaneously
   - Accessible servers will be the sum of all their groups

3. **Server Management**:
   - When creating a new server in the database:
     - Create a new servers-X group with the allowedServers attribute, if necessary
     - Update the allowedServers attribute in existing groups according to access needs
     - The all-servers group is automatically updated to include new servers

4. **allowedServers Attribute**:
   - Defined at the group level
   - Contains a list of accessible server IDs
   - Automatically inherited by group members
   - For users in multiple servers-X groups, permissions are cumulative
   - Update when servers are added/removed

5. **User Creation**:
   - Via administrative console
   - Through registration with social login
   - Required configuration:
     - Assign to admins or users (mandatory)
     - Assign to all-servers or one or more servers-X (mandatory)
     - Verify that the assigned groups cover all necessary servers

### External Authentication

The project includes configuration for authentication via:

- Google
- GitHub

To modify or add providers:

1. Navigate to Identity Providers
2. Configure the provider's credentials
3. Adjust the authentication flow according to your needs

### Production Configuration

The project uses Keycloak in development mode (allows HTTP). For production:

1. Enable production mode
2. Configure HTTPS
3. Set up SSL certificates
4. Configure hostname resolution

> For more details: [Official Keycloak Documentation](https://www.keycloak.org/server/configuration-production)

## Execution: MQTT Script

To run the MQTT manager you have two options:

1. **Using Docker CLI**:

```bash
docker exec -it proyecto-sensores-mqtt-manager-1 python mqtt_manager.py
```

2. **Using Docker Desktop**:
   - Open Docker Desktop
   - Go to the "Containers" tab
   - Access the container "proyecto-sensores-mqtt-manager-1"
   - Click on the "Exec" button
   - Run: `python mqtt_manager.py`

The script will provide an interactive interface to manage MQTT connections.

## MQTT Client Management

The project implements an MQTT connection management system consisting of two main components: a central manager and individual clients.

### Main MQTT Manager

```bash
# Run with default configuration
python mqtt_manager.py

# Customize refresh interval and log retention
python mqtt_manager.py -r <seconds> -d <days>
```

**Main features:**

- **Interactive interface** with options to:
  - Show the current status of clients and connections
  - Start/stop individual clients
  - Pause automatic restart of manually stopped clients
  - Verify and update the status of all connections
- **Automatic maintenance**:
  - Configurable periodic client refresh (`-r/--refresh`)
  - Daily log rotation with configurable retention (`-d/--retention`)

### Individual MQTT Client

```bash
# Run the MQTT client for a specific server
python mqtt_client.py <server_id> [--debug]
```

**Features:**

- **Connection** to an MQTT server using credentials stored in the database
- **Flexible MQTT message processing**:
  - Configurable topic format per server
  - Only requires the presence of a `serial` identifier and an `apikey` in the topic
  - Default format: `/{apikey}/{serial}/{type}`
  - Supports custom variables via templates (e.g., `/{client}/{serial}/{sensor}/...`)
- **Device update and creation** in the database
- **Message storage** received in the backend
- **Automatic reconnection management** and failure recovery
- **Detailed log handling**, with dynamic log level option via the `--debug` argument
