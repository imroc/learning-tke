---
title: "实用注解"
type: book
---

## EKS Pod 注解

### 提升 receive buffer
```yaml
eks.tke.cloud.tencent.com/host-sysctls: '[{"name": "net.core.rmem_max","value": "26214400"}]'
```

### 设置时区

```yaml
internal.eks.tke.cloud.tencent.com/init-script: 'timedatectl set-timezone Asia/Shanghai'
```

### 设置默认 NFS v3 挂载

```yaml
internal.eks.tke.cloud.tencent.com/init-script: 'cp /etc/nfsmount.conf /etc/nfsmount.conf.back; echo "Defaultvers=3" >> /etc/nfsmount.conf; echo "Nfsvers=3" >> /etc/nfsmount.conf; echo "noresvport=True" >> /etc/nfsmount.conf'
```