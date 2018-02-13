---
title: "Egy statikus IP-címet használ az Azure-tároló szolgáltatás (AKS) terheléselosztó"
description: "Egy statikus IP-címet használ az Azure-tároló szolgáltatás (AKS) terheléselosztót."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>Egy statikus IP-címet használ az Azure-tároló szolgáltatás (AKS) terheléselosztó

Bizonyos esetekben az Azure-tároló szolgáltatás (AKS) betöltése terheléselosztó újból létrejön, illetve terheléselosztó típussal rendelkező Kubernetes szolgáltatások jönnek létre újból, mint például a nyilvános IP-cím a Kubernetes szolgáltatás módosíthatja. Ez a dokumentum adatokat egy statikus IP-címet a Kubernetes szolgáltatások konfigurálása.

## <a name="create-static-ip-address"></a>Statikus IP-cím létrehozása

Hozzon létre egy statikus nyilvános IP-címet a Kubernetes szolgáltatás. Az IP-címet kell létrehozni, de a fürt központi telepítése során automatikusan létrehozott erőforráscsoportban. A különböző AKS erőforráscsoportok és az automatikusan létrehozott erőforráscsoport azonosítása kapcsolatos tudnivalókat lásd: a [AKS gyakran ismételt kérdések][aks-faq-resource-group].

Használja a [az hálózati nyilvános IP-cím létrehozása] [ az-network-public-ip-create] parancsot az IP-cím létrehozásához.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Jegyezze fel az IP-cím.

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

 Ha szükséges, a cím lekérhető használatával a [az nyilvános ip-lista] [ az-network-public-ip-list] parancsot.

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Szolgáltatás létrehozása az IP-címmel

Ha a statikus IP-cím van megadva, a Kubernetes szolgáltatás hozhatja létre a `loadBalancerIP` tulajdonság és a statikus IP-cím érték.

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

Ha a statikus IP-cím nem lett létrehozva, vagy a megfelelő erőforráscsoport létrehozása befejeződött, a szolgáltatás létrehozása sikertelen lesz. Elhárításával kapcsolatos tudnivalókat térjen vissza a szolgáltatás folyamatlétrehozási eseményeket a [kubectl ismertetik] [ kubectl-describe] parancsot.

```console
$ kubectl describe service azure-vote-front

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
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list