---
title: "常用 kubectl 脚本"
type: book
date: "2021-10-8"
---

k8s 通用场景的 kubectl 脚本参考 [这里](https://imroc.cc/k8s/ref/kubectl/)

## 节点相关

查询节点的 eni-ip Allocatable 情况:
```bash
kubectl get nodes -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.allocatable.tke\.cloud\.tencent\.com\/eni-ip}{"\n"}{end}'
```

指定可用区节点的 eni-ip Allocatable 情况:
```bash
kubectl get nodes -o=jsonpath='{range .items[?(@.metadata.labels.failure-domain\.beta\.kubernetes\.io\/zone=="100003")]}{.metadata.name}{"\t"}{.status.allocatable.tke\.cloud\.tencent\.com\/eni-ip}{"\n"}{end}'
```