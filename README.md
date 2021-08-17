# AnsibleF5

## Simple Ansible Builder for F5 using a CVS as the user's data input

Can build and add:

- Virtual Server
- Pools
- Nodes

Can also:

- Add Nodes to Pools
- Add a Pool to a Virtual Server

Input CSV:

> Raw View

```csv
VIP_Name,VIP_IP,VIP_Port,VIP_Pool,PoolMonitors,PoolMonitorsType,PoolSlowRampTime,PoolLBMethod,PoolNodes,Node_IP,Node_Port
lab-vip-10.1.1.12,10.1.1.12,443,lab-pool-Web-02,/Common/http,and_list,120,ratio-member,mytestserver112,10.1.2.112,443
lab-vip-10.1.1.12,10.1.1.12,443,lab-pool-Web-02,/Common/http,and_list,120,ratio-member,mytestserver113,10.1.2.113,443
```

Excel View:

| VIP_Name          | VIP_IP    | VIP_Port | VIP_Pool        | PoolMonitors | PoolMonitorsType | PoolSlowRampTime | PoolLBMethod | PoolNodes       | Node_IP    | Node_Port | 
|-------------------|-----------|----------|-----------------|--------------|------------------|------------------|--------------|-----------------|------------|-----------| 
| lab-vip-10.1.1.12 | 10.1.1.12 | 443      | lab-pool-Web-02 | /Common/http | and_list         | 120              | ratio-member | mytestserver112 | 10.1.2.112 | 443       | 
| lab-vip-10.1.1.12 | 10.1.1.12 | 443      | lab-pool-Web-02 | /Common/http | and_list         | 120              | ratio-member | mytestserver113 | 10.1.2.113 | 443       | 

> The CSV does not build directly it updates a device specific hostvar file

```yml
---
f5:
  hostname: 'lab-f5'
  location_domain_name: "example.com"
  ipv4: 10.100.1.10
  mask: 255.255.255.0
  gw: 10.100.1.1

bigip_virtual_servers:
  - name: "lab-vip-10.1.1.11"
    vip_destination: "10.1.1.11"
    vip_pool: "lab-pool_Web-01"
    vip_port: "443"
    vip_snat: "Automap"
    vip_all_profiles:
      - "tcp-lan-optimized"
      - "clientssl"
      - "http"
      - "analytics"
    state: ignore
    activation: ignore
big_ip_pools:
  - name: "lab-pool_Web-01"
    pool_monitors: "/Common/http"
    pool_monitor_type: "and_list"
    pool_slow_ramp_time: "120"
    pool_lb_method: "ratio-member"
    state: ignore
    activation: ignore
big_ip_nodes:
  - name: mytestserver110
    node_ip: 10.1.2.110
    node_port: 443
    node_pool_membership: "lab-pool_Web-01"
    state: ignore
    activation: ignore

  - name: mytestserve111
    node_ip: 10.1.2.111
    node_port: 443
    node_pool_membership: "lab-pool_Web-01"
    state: ignore
    activation: ignore

  - name: mytestserver74
    node_ip: 10.200.1.112
    node_port: 22
    node_pool_membership: "lab-pool_SSH-01"
    state: ignore
    activation: ignore
#EOF
...
```