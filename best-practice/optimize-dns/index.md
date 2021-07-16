---
title: "CoreDNS 性能优化"
type: book
date: "2021-07-16"
---

## 概述

CoreDNS 作为 TKE 集群的域名解析组件，如果性能不够可能会影响业务，本文介绍在 TKE 环境中优化 CoreDNS 性能的几种方法。

## 通用部分

关于 CoreDNS 的通用优化，参考 [Kubernetes 最佳实践: CoreDNS 性能优化](https://imroc.cc/k8s/best-practice/optimize-dns/) ，下文主要介绍在 TKE 环境中的一些优化方式。

## 使用 DNSAutoscaler

社区有开源的 [cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler) ，可以根据集群规模自动扩缩容，支持比较灵活的扩缩容算法。在 TKE 中已经将其产品化成 `DNSAutoscaler 扩展组件`，在扩展组件中直接安装即可，组件说明请参考 [TKE 官方文档](https://cloud.tencent.com/document/product/457/49305) 。

## 部署 NodeLocal DNSCache

`NodeLocal DNSCache` 以 DaemonSet 形式部署在集群中，可以为节点上的 Pod 缓存 DNS 解析，减轻集群 CoreDNS 的压力，并且能够解决内核 bug 导致的 DNS 5 秒延时问题。

在 TKE 中已经将其产品化，可以直接在扩展组件中安装，目前只支持 iptables 转发模式的集群，ipvs 的暂不支持，需要自行安装社区版 (主要是要修改 kubelet 的 `--cluster-dns` 参数并重启。

扩展组件说明请参考 [TKE 官方文档](https://cloud.tencent.com/document/product/457/49423) ，社区版请参考 K8S 官方文档 [Using NodeLocal DNSCache in Kubernetes clusters](https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/) 。
