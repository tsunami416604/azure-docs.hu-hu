---
title: Statikus IP-cím használata az Azure Kubernetes szolgáltatás (ak) terheléselosztó használatával
description: Ismerje meg, hogyan hozhat létre és használhat statikus IP-címet az Azure Kubernetes Service (ak) terheléselosztó használatával.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614470"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statikus nyilvános IP-cím használata az Azure Kubernetes szolgáltatással (ak) terheléselosztó

Alapértelmezés szerint egy AK-fürt által létrehozott terheléselosztó-erőforráshoz hozzárendelt nyilvános IP-cím csak az adott erőforrás élettartama esetén érvényes. Ha törli a Kubernetes szolgáltatást, a rendszer a társított terheléselosztó és az IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy IP-címet kíván megőrizni az újratelepített Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre statikus nyilvános IP-címet, és hogyan rendelheti hozzá a Kubernetes szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

Jelenleg csak az alapszintű *IP SKU*támogatott. A művelet folyamatban van a *szabványos IP-* erőforrás SKU támogatásához. További információt [az IP-címek típusai és a kiosztási módszerek az Azure-ban][ip-sku]című témakörben talál.

## <a name="create-a-static-ip-address"></a>Statikus IP-cím létrehozása

Ha az AK-val való használatra statikus nyilvános IP-címet hoz létre, az IP-cím erőforrást a **csomópont** -erőforráscsoporthoz kell létrehozni. Ha el szeretné különíteni az erőforrásokat, tekintse meg a következő szakaszt a [csomópont-erőforráscsoport kívüli statikus IP-cím használatához](#use-a-static-ip-address-outside-of-the-node-resource-group).

Először kérje le a csomópont-erőforráscsoport nevét az [az AK show][az-aks-show] paranccsal, és adja hozzá `--query nodeResourceGroup` a lekérdezési paramétert. A következő példa lekéri a csomópont-erőforráscsoportot az AK-fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet az az [Network Public IP Create][az-network-public-ip-create] paranccsal. Adja meg az előző parancsban beszerzett csomópont-erőforráscsoport nevét, majd az IP-cím erőforrás nevét, például *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím jelenik meg, ahogy az a következő tömörített példában látható:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Később lekérheti a nyilvános IP-címet az az [Network Public-IP List][az-network-public-ip-list] paranccsal. Adja meg a létrehozott csomópont-erőforráscsoport és nyilvános IP-cím nevét, és az *IP* -cím lekérdezését az alábbi példában látható módon:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Szolgáltatás létrehozása a statikus IP-cím használatával

Egy statikus nyilvános IP-címmel rendelkező szolgáltatás létrehozásához adja hozzá a `loadBalancerIP` tulajdonságot és a statikus nyilvános IP-cím értékét a YAML jegyzékfájlhoz. Hozzon létre egy `load-balancer-service.yaml` nevű fájlt, és másolja a következő YAML. Adja meg az előző lépésben létrehozott saját nyilvános IP-címet.

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

Hozza létre a szolgáltatást és az üzembe `kubectl apply` helyezést a paranccsal.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Statikus IP-cím használata a csomópont-erőforráscsoporton kívül

A Kubernetes 1,10-es vagy újabb verziójával olyan statikus IP-címet használhat, amely a csomópont-erőforráscsoporton kívül jön létre. Az AK-fürt által használt egyszerű szolgáltatásnak delegált engedélyekkel kell rendelkeznie a másik erőforráscsoporthoz, az alábbi példában látható módon:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Ha IP-címet szeretne használni a csomópont-erőforráscsoport kívül, vegyen fel egy megjegyzést a szolgáltatás-definícióba. A következő példa a jegyzetet a *myResourceGroup*nevű erőforráscsoporthoz állítja be. Adja meg saját erőforráscsoport-nevét:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a Kubernetes szolgáltatás jegyzékfájljának *loadBalancerIP* tulajdonságában definiált statikus IP-cím nem létezik, vagy nem lett létrehozva a csomópont-erőforráscsoporthoz, és nincs további delegálás konfigurálva, a terheléselosztó szolgáltatás létrehozása sikertelen lesz. A hibák megoldásához tekintse át a szolgáltatás-létrehozási eseményeket a [kubectl leíró][kubectl-describe] paranccsal. Adja meg a szolgáltatás nevét a YAML jegyzékfájlban megadott módon, az alábbi példában látható módon:

```console
kubectl describe service azure-load-balancer
```

Megjelenik a Kubernetes szolgáltatás erőforrásával kapcsolatos információ. Az alábbi példa kimenetének végén lévő *események* azt jelzik, hogy a *felhasználó által megadott IP-cím nem található*. Ezekben a forgatókönyvekben ellenőrizze, hogy a csomópont erőforráscsoporthoz létrehozta-e a statikus nyilvános IP-címet, és hogy a Kubernetes szolgáltatás jegyzékfájljában megadott IP-cím helyes-e.

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

Az alkalmazásokra irányuló hálózati forgalom további szabályozása érdekében érdemes lehet [egy bejövő vezérlőt létrehozni][aks-ingress-basic]. Egy [statikus nyilvános IP-címmel rendelkező bejövő vezérlőt is létrehozhat][aks-static-ingress].

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
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
