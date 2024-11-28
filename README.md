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

The container can be customized using various options:

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
)
```

## Requirements

- Docker
- Go 1.21 or later

## License

This project is licensed under the MIT License - see the LICENSE file for details.
