---
title: 如何定义多机只读的 PV
type: book
date: "2021-07-23"
---

## COS

1. `accessModes` 指定 `ReadOnlyMany`。
2. `csi.volumeAttributes.additional_args` 指定 `-oro`。

yaml 示例:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: registry
spec:
  accessModes:
  - ReadOnlyMany
  capacity:
    storage: 1Gi
  csi:
    readOnly: true
    driver: com.tencent.cloud.csi.cosfs
    volumeHandle: registry
    volumeAttributes:
      additional_args: "-oro"
      url: "http://cos.ap-chengdu.myqcloud.com"
      bucket: "roc-**********"
      path: /test
    nodePublishSecretRef:
      name: cos-secret
      namespace: kube-system
```

## CFS

1. `accessModes` 指定 `ReadOnlyMany`。
2. `mountOptions` 指定 `ro`。

yaml 示例:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: test
spec:
  accessModes:
  - ReadOnlyMany
  capacity:
    storage: 10Gi
  storageClassName: cfs
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem
  mountOptions:
  - ro
  csi:
    driver: com.tencent.cloud.csi.cfs
    volumeAttributes:
      host: 10.10.99.99
      path: /test
    volumeHandle: cfs-********
```
