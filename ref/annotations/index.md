---
title: "实用注解"
type: book
---

## EKS Pod 注解

### 提升 receive buffer
```yaml
eks.tke.cloud.tencent.com/host-sysctls: '[{"name": "net.core.rmem_max","value": "26214400"}]'
```