go-junos
========

[![GoDoc](https://godoc.org/github.com/scottdware/go-junos?status.svg)](https://godoc.org/github.com/scottdware/go-junos)

A Go package that interacts with Junos devices and allows you to do the following:

* Run operational mode commands, such as `show`, `request`, etc..
* Compare the active configuration to a given rollback config.
* Rollback the configuration to a given state or a "rescue" config.
* Configure devices by uploading a local file or from an FTP/HTTP server.

Visit the [GoDoc][4] page for complete package documentation.

> **Note:** This package makes all of it's calls over [Netconf][1] using the [go-netconf][2] package from [Juniper Networks][3]

Example
-------
```Go
package main

import (
	"fmt"
	"github.com/scottdware/go-junos"
	"os"
)

func main() {
    // Establish a connection to a device.
	jnpr := junos.NewSession("srx-1", "admin", "juniper123")
    defer jnpr.Close()

    // View only the security section of the configuration in text format.
    security, _ := jnpr.GetConfig("text", "security")
    fmt.Println(security)
    
    // Compare the current running config to "rollback 1."
	diff, _ := jnpr.ConfigDiff(1)
	fmt.Println(diff)

    // Create a rescue configuration.
    jnpr.Rescue("save")
    
    // Load a configuration file with "set" commands, and commit it.
	err := jnpr.LoadConfig("C:/Configs/juniper.txt", "set", true)
	if err != nil {
		fmt.Println(err)
	}
    
    // Rollback to a previous config.
    err = jnpr.RollbackConfig(5)
    if err != nil {
        fmt.Println(err)
    }
    
    // or 
    err = jnpr.RollbackConfig("rescue")
    if err != nil {
        fmt.Println(err)
    }
    
    // Run a command and return the results in "text" format (similar to CLI).
    output, err := jnpr.Command("show security ipsec inactive-tunnels", "text")
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(output)
    
    // Show platform and software information
    jnpr.Facts()
}
```

[1]: https://tools.ietf.org/html/rfc6241
[2]: https://github.com/Juniper/go-netconf
[3]: http://www.juniper.net
[4]: https://godoc.org/github.com/scottdware/go-junos