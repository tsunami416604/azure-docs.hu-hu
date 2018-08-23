---
title: Az Azure Kubernetes Service (AKS) terheléselosztót statikus IP-cím használata
description: Statikus IP-cím használata az Azure Kubernetes Service (AKS) terheléselosztót.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 87fe014d5c19be675d4f6cac876548a31a4484b4
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054906"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Az Azure Kubernetes Service (AKS) terheléselosztót statikus IP-cím használata

Bizonyos esetekben például ha az Azure Kubernetes Service (AKS) betöltése terheléselosztó újból létrejön vagy a terheléselosztó típusú Kubernetes-szolgáltatás létrejönnek, a nyilvános IP-címét a Kubernetes-szolgáltatást változhat. Ez a dokumentum részletesen, statikus IP-címet a Kubernetes-szolgáltatás konfigurálása.

## <a name="create-static-ip-address"></a>Statikus IP-cím létrehozása

Hozzon létre egy statikus nyilvános IP-címet a Kubernetes-szolgáltatást. Az IP-címet kell hozhatók létre az AKS **csomópont** erőforráscsoportot. Az erőforráscsoport nevét az első a [az resource show] [ az-resource-show] parancsot.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Használja a [az hálózati nyilvános IP-cím létrehozása] [ az-network-public-ip-create] paranccsal hozza létre az IP-címet.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Jegyezze fel az IP-címet.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Ha szükséges, a cím lehet lekérni a [az network public-ip list] [ az-network-public-ip-list] parancsot.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Szolgáltatás létrehozása az IP-cím

A statikus IP-címet kiosztották, miután egy Kubernetes-szolgáltatást lehet létrehozni a `loadBalancerIP` tulajdonság és a egy statikus IP-cím értékét.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha statikus IP-címe nem lett létrehozva, vagy a helytelen erőforrás-csoport létrehozása, a szolgáltatás létrehozása sikertelen lesz. A vissza a szolgáltatás-létrehozási események elhárításához a [írja le a kubectl] [ kubectl-describe] parancsot.

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-resource-show]: /cli/azure/resource#az-resource-show
