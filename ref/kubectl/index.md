---
title: "常用 kubectl 脚本"
type: book
date: "2021-10-8"
---

k8s 通用场景的 kubectl 脚本参考 [这里](https://imroc.cc/k8s/ref/kubectl/)

## ENI 相关

查询节点的 eni-ip Allocatable 情况:
```bash
kubectl get nodes -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.allocatable.tke\.cloud\.tencent\.com\/eni-ip}{"\n"}{end}'
```

指定可用区节点的 eni-ip Allocatable 情况:
```bash
kubectl get nodes -o=jsonpath='{range .items[?(@.metadata.labels.failure-domain\.beta\.kubernetes\.io\/zone=="100003")]}{.metadata.name}{"\t"}{.status.allocatable.tke\.cloud\.tencent\.com\/eni-ip}{"\n"}{end}'
```

查看各节点 ENI 的子网网段:
```bash
kubectl get nec -o json | jq -r '.items[] | select(.status.eniInfos!=null)| { name: .metadata.name, zone: , subnetCIDR: [.status.eniInfos[].subnetCIDR]|join(",") }| "\(.name)\t\(.subnetCIDR)"'
```

查可以绑指定子网ENI的节点都是在哪个可用区:
```bash
# 查询哪些节点可以绑这个子网的 ENI
kubectl get nec -o json | jq -r '.items[] | select(.status.eniInfos!=null)| { name: .metadata.name, subnetCIDR: [.status.eniInfos[].subnetCIDR]|join(",") }| "\(.name)\t\(.subnetCIDR)"' | grep 11.167.0.0/20 | awk '{print $1}' > node-cidr.txt
# 查询所有节点的可用区
kubectl get nodes -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.failure-domain\.beta\.kubernetes\.io\/zone}{"\n"}{end}' > node-zone.txt
# 筛选出可以绑这个子网的节点都是在哪个可用区
awk 'BEGIN{while(getline<"node-cidr.txt") a[$1]=1;} {if(a[$1]==1) print $0;}' node-zone.txt
```