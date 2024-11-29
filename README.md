# Testcontainers Mattermost Module for Go

This module provides a [Testcontainers](https://www.testcontainers.org) implementation for Mattermost. It allows you to run Mattermost in a container during integration tests.

## Usage

```go
package example_test

import (
    "context"
    "testing"
    mmcontainer "github.com/mattermost/testcontainers-mattermost-go"
)

func TestMattermost(t *testing.T) {
    ctx := context.Background()
    
    // Start Mattermost container
    container, err := mmcontainer.RunContainer(ctx)
    if err != nil {
        t.Fatal(err)
    }
    defer container.Terminate(ctx)
    
    // Get admin client
    client, err := container.GetAdminClient(ctx)
    if err != nil {
        t.Fatal(err)
    }
    
    // Use the client to interact with Mattermost
    // ...
}
```

## Features

- Automatically starts a PostgreSQL container
- Creates initial admin user and team
- Provides methods to:
  - Get admin and regular user clients
  - Create users and teams
  - Install plugins
  - Update configuration
  - Access logs
  - And more...

## Container Methods

The `MattermostContainer` provides the following methods:

### Basic Operations
- `URL(ctx)` - Returns the URL of the Mattermost instance
- `Terminate(ctx)` - Terminates both Mattermost and PostgreSQL containers
- `PostgresDSN(ctx)` - Returns the PostgreSQL connection string
- `PostgresConnection(ctx)` - Returns a direct SQL connection to PostgreSQL

### Client Access
- `GetAdminClient(ctx)` - Returns a Mattermost client logged in as admin
- `GetClient(ctx, username, password)` - Returns a Mattermost client logged in as specified user

### User Management
- `CreateAdmin(ctx, email, username, password)` - Creates an admin user
- `CreateUser(ctx, email, username, password)` - Creates a regular user

### Team Management
- `CreateTeam(ctx, name, displayName)` - Creates a new team
- `AddUserToTeam(ctx, username, teamname)` - Adds a user to a team

### Configuration
- `SetConfig(ctx, configKey, configValue)` - Sets a single config value
- `UpdateConfig(ctx, cfg)` - Updates the entire Mattermost configuration

### Plugin Management
- `InstallPlugin(ctx, pluginPath, pluginID, pluginConfig)` - Installs and enables a plugin

### Logging
- `GetLogs(ctx, lines)` - Returns the specified number of log lines

### Command Execution
- `RunCtlCommand(ctx, command, args)` - Runs an mmctl command inside the container

## Options

The `RunContainer` function accepts the following options to customize the Mattermost instance:

### Authentication Options
- `WithAdmin(email, username, password)` - Sets the admin user credentials
  - Default: admin@example.com/admin/admin

### Team Options  
- `WithTeam(name, displayName)` - Sets the initial team name and display name
  - Default: test/Test

### Plugin Options
- `WithPlugin(pluginPath, pluginID, pluginConfig)` - Installs and enables a plugin
  - pluginPath: Path to the plugin file (.tar.gz)
  - pluginID: Unique identifier for the plugin
  - pluginConfig: Optional plugin-specific configuration

### Configuration Options
- `WithConfig(cfg)` - Updates the entire Mattermost configuration
- `WithConfigFile(path)` - Uses a config file from the host system
- `WithEnv(key, value)` - Sets an environment variable
- `WithLicense(licenseBase64)` - Sets the Mattermost license

### Network Options
- `WithNetwork(network)` - Uses a custom Docker network
  - Adds container to the network with alias "mattermost"
  - Enables communication between containers

### Logging Options
- `WithLogConsumers(consumers...)` - Sets log consumers for container output

Example usage:

```go
container, err := mmcontainer.RunContainer(ctx,
    // Set custom admin credentials
    mmcontainer.WithAdmin("admin@example.com", "admin", "password"),
    
    // Set custom team
    mmcontainer.WithTeam("myteam", "My Team"),
    
    // Install plugin
    mmcontainer.WithPlugin("/path/to/plugin.tar.gz", "com.example.plugin", nil),
    
    // Update config
    mmcontainer.WithConfig(&model.Config{
        // Custom config...
    }),
    
    // Set environment variables
    mmcontainer.WithEnv("MM_SERVICESETTINGS_SITEURL", "http://localhost:8065"),
    
    // Use custom network
    mmcontainer.WithNetwork(network),
    
    // Add log consumer
    mmcontainer.WithLogConsumers(myLogConsumer),
)
```

## Requirements

- Docker
- Go 1.21 or later

## License

This project is licensed under the MIT License - see the LICENSE file for details.
