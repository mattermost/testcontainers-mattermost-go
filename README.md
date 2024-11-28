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
)
```

## Requirements

- Docker
- Go 1.21 or later

## License

This project is licensed under the MIT License - see the LICENSE file for details.
