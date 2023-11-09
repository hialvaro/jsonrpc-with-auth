## Example

This is an example that gets a list of hosts filtered by hostname from Zabbix.

```go
package main

import (
	"context"
	"log"
	"my-mod/jsonrpc"
)

type ZabbixHostGet struct {
	HostID string `json:"hostid"`
	Host   string `json:"host"`
}

func main() {
	rpcClient := jsonrpc.NewClient("https://zabbix.example.com/api_jsonrpc.php")
	resp, err := rpcClient.Call(context.Background(), "user.login", "", map[string]string{"user": "ZabbixUser", "password": "some_zabbix_password"})

	if err != nil {
		log.Printf("%v", err)
	}

	log.Printf("%+v", resp.Result)
	token := resp.Result.(string)

	params := map[string]interface{}{
		"filter": map[string]interface{}{
			"host": []string{"myhost"},
		},
	}

	var hosts []ZabbixHost
	err = rpcClient.CallFor(context.Background(), &hosts, "host.get", token, params)

	if err != nil {
		log.Printf("ERROR: %v", err)
	}

	log.Printf("%+v", hosts)
}
```
