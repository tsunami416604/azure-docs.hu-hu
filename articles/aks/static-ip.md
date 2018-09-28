---
title: Az Azure Kubernetes Service (AKS) terheléselosztót statikus IP-cím használata
description: Ismerje meg, hogyan hozhat létre, és a egy statikus IP-cím használata az Azure Kubernetes Service (AKS) terheléselosztót.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 8aab091ed992a946cd78ecf4f0c8fdfff4185a08
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407552"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Az Azure Kubernetes Service (AKS) terheléselosztót statikus nyilvános IP-cím használata

Alapértelmezés szerint egy AKS-fürt által létrehozott terheléselosztó-erőforráshoz rendelt nyilvános IP-cím je platná pouze Pro a gyűjteményszintű az erőforrás elszámolási egységében. Ha törli a Kubernetes-szolgáltatást, a kapcsolódó terheléselosztót és IP-cím is törlődik. Ha szeretné hozzárendelni a megadott IP-címet vagy az újratelepített Kubernetes-szolgáltatás egy IP-cím megőrzése, hozzon létre, és statikus nyilvános IP-cím.

Ez a cikk bemutatja, hogyan hozhat létre egy statikus nyilvános IP-címet, és rendelje hozzá a Kubernetes-szolgáltatást.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.46 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-a-static-ip-address"></a>Hozzon létre statikus IP-cím

Amikor az aks-sel használható statikus nyilvános IP-címet hoz létre, az IP-cím erőforrás kell létrehozni a **csomópont** erőforráscsoportot. Az erőforráscsoport nevét az első a [az aks show] [ az-aks-show] parancsot, majd adja hozzá a `--query nodeResourceGroup` lekérdezési paraméter. Az alábbi példa lekéri az AKS-fürt nevét a csomópont erőforráscsoport *myAKSCluster* az erőforráscsoport nevét a *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet a [az hálózati nyilvános IP-cím létrehozása] [ az-network-public-ip-create] parancsot. Adja meg az előző paranccsal beszerzett a csomópont erőforráscsoport-név, és ezután a egy nevet az IP-cím erőforrás, például *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím jelenik meg, ahogyan az a következő sűrített példához kimenet:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
````

Később a nyilvános IP cím használatával lekérheti a [az network public-ip list] [ az-network-public-ip-list] parancsot. Adja meg a csomópont erőforráscsoport nevét, és ezután lekérdezi a *IP-cím* az alábbi példában látható módon:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>A statikus IP-címet használó szolgáltatás létrehozása

Szolgáltatás létrehozása a statikus nyilvános IP-címmel, adja hozzá a `loadBalancerIP` a YAML jegyzékfájlhoz-tulajdonság és a statikus nyilvános IP-cím értékét. Hozzon létre egy fájlt `load-balancer-service.yaml` másolja be a következő yaml-kódot. Adja meg saját nyilvános IP-címet az előző lépésben létrehozott.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Hozzon létre, majd az üzembe helyezés a `kubectl apply` parancsot.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a statikus IP-címet megadva a *loadBalancerIP* Kubernetes Szolgáltatásjegyzék tulajdonság nem létezik, vagy a csomópont erőforráscsoportban nem lett létrehozva, a load balancer szolgáltatás létrehozása sikertelen lesz. A hibaelhárításhoz tekintse át a szolgáltatás-létrehozási események és a [írja le a kubectl] [ kubectl-describe] parancsot. Adja meg a szolgáltatás a YAML-jegyzékfájlban megadott nevét, az alábbi példában látható módon:

```console
kubectl describe service azure-load-balancer
```

A Kubernetes szolgáltatás erőforrásra vonatkozó információk jelennek meg. A *események* az alábbi példa kimenetében végén jelzi, hogy a *megadott IP-cím nem található felhasználó*. Ezekben az esetekben a létrehozott statikus nyilvános IP-cím csomópont az erőforráscsoportban és, hogy helyesen szerepel-e a Kubernetes szolgáltatásjegyzékben megadott IP-ellenőrzése.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>További lépések

A hálózati forgalom az alkalmazások további szabályozásához érdemes inkább [hozzon létre egy bejövőforgalom-vezérlőjéhez][aks-ingress-basic]. Emellett [hozzon létre egy statikus nyilvános IP-cím bejövőforgalom-vezérlőjéhez][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
