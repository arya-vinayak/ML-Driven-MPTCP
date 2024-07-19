# ML-Driven-MPTCP


markdown
# MPTCP Configuration Guide

This guide provides step-by-step instructions for configuring Multipath TCP (MPTCP) on supported distributions (CentOS, Debian, Ubuntu 22.04+). Ensure that you have the recent kernel installed and MPTCP enabled.

## System Requirements

- **Supported Distributions**: CentOS, Debian, Ubuntu (22.04+)
- **Kernel**: Recent kernel installation required with MPTCP support enabled.

## Configuration Steps

### 1. Kernel Verification

Ensure that MPTCP is enabled in your kernel. You can check this by running:
```bash
sudo sysctl net.mptcp.enabled
```
If the output is `net.mptcp.enabled = 1`, MPTCP is enabled.

### 2. Client Configuration

#### a) Create Routing Tables
```bash
sudo ip rule add from 10.10.3.99 table 1
sudo ip rule add from 10.10.3.61 table 2
```

#### b) Configure Routing for `enp1s0` (Ethernet)
```bash
sudo ip route add 10.10.3.0/24 dev enp1s0 scope link table 1
sudo ip route add default via 10.10.3.1 dev enp1s0 table 1
```

#### c) Configure Routing for `wlp2s0` (WiFi)
```bash
sudo ip route add 10.10.3.0/24 dev wlp2s0 scope link table 2
sudo ip route add default via 10.10.3.1 dev wlp2s0 table 2
```

#### d) Set Default Route
```bash
sudo ip route add default scope global nexthop via 10.10.3.1 dev enp1s0
```

#### e) Add MPTCP Endpoints
```bash
sudo ip mptcp endpoint add 10.10.3.61 dev wlp2s0 subflow
sudo ip mptcp endpoint add 10.10.3.99 dev enp1s0 subflow
```

#### f) Set Subflow Limits
```bash
sudo ip mptcp limits set subflow 2 add_addr_accepted 2
```

### 3. Server Configuration

#### a) Create Routing Tables
```bash
sudo ip rule add from 10.10.3.105 table 1
sudo ip rule add from 10.10.3.54 table 2
```

#### b) Configure Routing for `enp1s0` (Ethernet)
```bash
sudo ip route add 10.10.3.0/24 dev enp1s0 scope link table 1
sudo ip route add default via 10.10.3.1 dev enp1s0 table 1
```

#### c) Configure Routing for `wlp2s0` (WiFi)
```bash
sudo ip route add 10.10.3.0/24 dev wlp2s0 scope link table 2
sudo ip route add default via 10.10.3.1 dev wlp2s0 table 2
```

#### d) Set Default Route
```bash
sudo ip route add default scope global nexthop via 10.10.3.1 dev enp1s0
```

#### e) Add MPTCP Endpoints
```bash
sudo ip mptcp endpoint add 10.10.3.54 dev wlp2s0 signal
sudo ip mptcp endpoint add 10.10.3.105 dev enp1s0 signal
```

#### f) Set Subflow Limits
```bash
sudo ip mptcp limits set subflow 2 add_addr_accepted 2
```

## Notes

- Replace IP addresses and interface names with your specific values.
- Ensure the kernel supports MPTCP and is properly configured.
- Verify routing and MPTCP settings using `ip mptcp` and `ip route` commands.
