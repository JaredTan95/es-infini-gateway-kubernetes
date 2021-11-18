# Elasticsearch INFINI Gateway On Kubernetes HA(高可用方案)

> **极限网关**（*INFINI Gateway*）是一个面向 Elasticsearch 的高性能应用网关，它包含丰富的特性，使用起来也非常简单。极限网关工作的方式和普通的反向代理一样，我们一般是将网关部署在 Elasticsearch 集群前面， 将以往直接发送给 Elasticsearch 的请求都发送给网关，再由网关转发给请求到后端的 Elasticsearch 集群。因为网关位于在用户端和后端 Elasticsearch 之间，所以网关在中间可以做非常多的事情， 比如可以实现索引级别的限速限流、常见查询的缓存加速、查询请求的审计、查询结果的动态修改等等。

更多请移步 👉 [INFINI Gateway](https://gateway.infini.sh/)

## 前置条件

INFINI Gateway 本身提供了浮动IP(floating IP) 的方式实现高可用。

目前只验证了 **Calico v3.16+** 网络下的方案，在此之前，请确保你的 Calico 网络开启了**浮动IP** 的特性。

```json
 "feature_control": {
         "floating_ips": true
     }
```

详细指南请参考：[Calico add-floating-ip](https://docs.projectcalico.org/networking/add-floating-ip) 

## 部署 INFINI Gateway

- 为 Pod 配置浮动IP，更改 `es-infini-gateway-deployment.yml`：

```bash
    metadata:
      labels:
        app: es-infini-gateway
      annotations:
        # NOTE: update floating ip
        cni.projectcalico.org/floatingIPs: "[\"10.233.70.10\"]"
```

更改 Configmap 中的配置,比如 floating_ip：

```bash
    floating_ip:
      enabled: true
      # NOTE: update floating ip
      ip: 10.233.70.10
```

- 依次 apply 部署文件即可：

```
kubectl apply -f es-infini-gateway-configmap.yaml
kubectl apply -f es-infini-gateway-svc.yaml
kubectl apply -f es-infini-gateway-deployment.yml
```

