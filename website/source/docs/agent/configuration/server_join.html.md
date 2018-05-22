---
layout: "docs"
page_title: "server_join Stanza - Agent Configuration"
sidebar_current: "docs-agent-configuration-server_join"
description: |-
  The "server_join" stanza configures the Nomad agent to enable retry_join logic for connecting to Nomad servers.
---

# `server_join` Stanza

<table class="table table-bordered table-striped">
  <tr>
    <th width="120">Placement</th>
    <td>
      <code>**acl**</code>
    </td>
  </tr>
</table>

The `server_join` stanza configures the Nomad agent to enable retry_join logic for connecting to Nomad servers.

```hcl
server_join {
  retry_join = [ "1.1.1.1", "2.2.2.2" ]
  start_join = [ "1.1.1.1", "2.2.2.2" ]
  retry_max = 3
  retry_interval = "15s"
}

## `server_join` Parameters

- `retry_join` `(array<string>: [])` - Specifies a list of server
  addresses to retry joining if the first attempt fails. This is similar to
  [`start_join`](#start_join), but only invokes if the initial join attempt
  fails, and is available to both Nomad servers and clients, while
  `start_join` is only defined for Nomad servers. The list of addresses will
  be tried in the order specified, until one succeeds. After one succeeds, no
  further addresses will be contacted. This is
  useful for cases where we know the address will become available eventually.
  Use `retry_join` with an array as a replacement for `start_join`, **do not use
  both options**.

Address format includes both using IP addresses as well as an interface to the
[go-discover](https://github.com/hashicorp/go-discover) library for doing
automated cluster joining using cloud metadata.
```
server_join {
  retry_join = [ "1.1.1.1", "2.2.2.2" ]
}
```

Using the `go-discover` interface, this can be defined both in a client or
server configuration as well as provided as a command-line argument.
```
server_join {
  retry_join = [ "provider=aws tag_key=..." ]
}
```
See the [server address format](#server-address-format) for more information
about expected server address formats.

- `retry_interval` `(string: "30s")` - Specifies the time to wait between retry
  join attempts.

- `retry_max` `(int: 0)` - Specifies the maximum number of join attempts to be
  made before exiting with a return code of 1. By default, this is set to 0
  which is interpreted as infinite retries.

- `start_join` `(array<string>: [])` - Specifies a list of server addresses to
  join on startup. If Nomad is unable to join with any of the specified
  addresses, agent startup will fail. See the
  [server address format](#server-address-format) section for more information
  on the format of the string. This field is defined only for Nomad servers and
  will result in a configuration parse error if included in a client
  configuration.

