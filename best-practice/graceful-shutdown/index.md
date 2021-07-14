---
title: "优雅终止"
type: book
date: "2021-07-14"
---

## 概述

本文讲解在 TKE 环境中如何实现优雅终止。

## Kubernetes 通用部分

TKE 底层是 Kubernetes，所以在 TKE 中如何实现优雅终止，大多场景就是在 Kubernetes 环境中如何实现优雅终止，参考 [Kubernetes 最佳实践: 优雅终止](https://imroc.cc/k8s/best-practice/graceful-shutdown/) 。

## CLB 直连 Pod 场景

在 TKE 中对外暴露服务支持 CLB 直连 Pod (不经过 NodePort)，参考 [如何启用 CLB 直通 Pod](https://imroc.cc/tke/faq/loadblancer-to-pod-directly/) 。

这种场景当后端 Pod 进行滚动更新，或后端 Pod 被删除时，默认是会直接将 Pod 从 LB 的 rs 列表中摘除，这会导致 Pod 已接收的请求，无法回包，即该该 Pod 的进出流量都不通，可能会造成 client 侧的抖动和错误，表现是出现 socket 的读或写 error，应用层的请求失败等，主要出现在长链接场景中。

TKE 已经支持了接入层组件的优雅停机能力，即在 Pod 删除过程中，不会理解摘除 Pod，仅仅是将其权重置为 0，禁止新请求进来，但能够让存量请求(连接)继续处理，直到退出 (前提是业务自身实现了优雅停止的逻辑，参考前面讲的通用部分的优雅终止最佳实践)。

这个能力没有默认启用，以下是启用方法:

**Service:**

如果是使用的 LoadBalancer 类型的 Service，可以给 Service 加上下面这个注解:

```yaml
service.cloud.tencent.com/enable-grace-shutdown: true
```

**Ingress:**

如果使用的 TKE 默认类型的 Ingress (CLB Ingress)，可以给 Ingress 加上下面这个注解:

```yaml
ingress.cloud.tencent.com/enable-grace-shutdown: true
```