---
layout: post
title:  "Cilium Trouble Shooting Reference"
date:   2019-02-14
categories: cilium
---

### TL;DR

## Cilium Status

## Cilium Health Status

## Check Current (Human Readable) BPF rules

For traditional situations, we could check the L2/L3 forwarding rules through:

* `ip route` or `route -n` for route entries (L3)
* `ip neigh` or `arp -n` for ARP entries (L2)
* `ovs-ofctl dump-flows <bridge>` for OVS forwarding entries, also called flows (L2)

Then, how to check the human readable BPF forwarding rules?

Unfortunately, there is no such commands.

However, we could get what we wanted by this:

```shell
$ cilium get endpoint <endpoint id>
    ...
    "policy": {
        "realized": {
          "allowed-egress-identities": [ 59687, 3, 105, ... 100 ],
          "allowed-ingress-identities": [ 101, 106, 59687, ... 102 ],
        },
        "spec": {
          "allowed-egress-identities": [ 59687, 1, ... 105 ],
          "allowed-ingress-identities": [ 59687, 103, ... 17593 ],
        },
```

`policy` section contains the identity list of allowed endpoints (L2?).

And, `realized` is the current state, and `spec` is the desired state.

Cilium iteratively works to bring the `realized` in line with the `spec`,
just as the Kubernetes control loop pattern.

## References

1. [Cilium Document: Troubleshooting](https://cilium.readthedocs.io/en/v1.4/troubleshooting/)

## Remaining Questions

1. integrate with BGP
1. IPAM state recovery from reboot
1. Add Identity section in prev post, used for [L3 enforcement](https://docs.cilium.io/en/v1.4/concepts/)
1. reload datapath (BPF code) will not break connection, how is that realized?
1. cilium-init container purpose?
1. ICMP path (no reserve path for ICMP packets)
