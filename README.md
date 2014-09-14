# setup-network-environment

## Usage

```
setup-network-environment
```

The network info will be written to `/etc/network-environment` by default.

```
LO_IPV4=127.0.0.1
ENO16777736_IPV4=192.168.12.10
DEFAULT_IPV4=192.168.12.10
```

You can write the network info to a different file using the `-o` flag.

```
setup-network-environment -o network-environment
```

### systemd Integration


#### setup-network-environment.service

```
[Unit]
Description=Setup Network Environment
Documentation=https://github.com/kelseyhightower/setup-network-environment
Requires=network-online.target
After=network-online.target

[Service]
ExecStartPre=-/usr/bin/mkdir -p /opt/bin
ExecStartPre=/usr/bin/wget -N -P /opt/bin https://github.com/kelseyhightower/setup-network-environment/releases/download/v1.0.0/setup-network-environment

ExecStartPre=/usr/bin/chmod +x /opt/bin/setup-network-environment
ExecStart=/opt/bin/setup-network-environment
```

#### Depending on the setup-network-environment.service

```
[Unit]
Requires=setup-network-environment.service
After=setup-network-environment.service

[Service]
EnvironmentFile=/etc/network-environment
ExecStart=/opt/bin/kubelet --hostname_override=${DEFAULT_IPV4}
```

The above unit file will override the kubelet hostname with the IP address of the interface that provides the default gateway. You can also reference a specific interface:

```
ExecStart=/opt/bin/kubelet --hostname_override=${ENO16777736_IPV4}
```

## Building

```
mkdir -p "${GOPATH}/src/github.com/kelseyhightower"
cd "${GOPATH}/src/github.com/kelseyhightower"
git clone https://github.com/kelseyhightower/setup-network-environment.git
cd setup-network-environment
godep go build .
```

Slightly smaller binary.

```
CGO_ENABLED=0 GOOS=linux godep go build -a -tags netgo -ldflags '-w' .
```
