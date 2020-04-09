---
title: Statikus IP használata terheléselosztóval
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre és használhat statikus IP-címet az Azure Kubernetes-szolgáltatás (AKS) terheléselosztóval.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886738"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statikus nyilvános IP-cím és DNS-címke használata az Azure Kubernetes-szolgáltatás (AKS) terheléselosztójával

Alapértelmezés szerint az AKS-fürt által létrehozott terheléselosztó erőforráshoz rendelt nyilvános IP-cím csak az adott erőforrás élettartamára érvényes. Ha törli a Kubernetes szolgáltatást, a társított terheléselosztó és IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy meg szeretne tartani egy IP-címet az áthelyezett Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre egy statikus nyilvános IP-címet, és rendelje hozzá a Kubernetes szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

Ez a cikk egy *szabványos* termékváltozat IP-cím *standard* termékváltozat terheléselosztó használatával foglalkozik. További információt az [IP-címtípusok és a foglalási módszerek az Azure-ban című témakörben talál.][ip-sku]

## <a name="create-a-static-ip-address"></a>Statikus IP-cím létrehozása

Hozzon létre egy statikus nyilvános IP-címet az [az hálózati nyilvános ip create][az-network-public-ip-create] paranccsal. A következő létrehoz egy statikus IP-erőforrás nevű *myAKSPublicIP* a *myResourceGroup* erőforráscsoport:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Ha *egy alapszintű* termékváltozat terheléselosztót használ az AKS-fürtben, használja az *Alapszintű* a *termékváltozat* paraméter nyilvános IP-cím definiálásakor. Csak *az alapszintű* termékváltozat-azonosítók működnek az *alapszintű* termékváltozat-terheléselosztóval, és csak *a szabványos* termékváltozat-ip-szolgáltatások szabványos termékváltozat-terheléselosztókkal. *Standard* 

Az IP-cím a következő tömörített kimeneti példában látható módon jelenik meg:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Később lekaphatja a nyilvános [IP-címet az az-hálózat nyilvános ip-lista][az-network-public-ip-list] paranccsal. Adja meg a létrehozott csomóponterőforrás-csoport és nyilvános IP-cím nevét, és az *ipAddress lekérdezését* az alábbi példában látható módon:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Szolgáltatás létrehozása a statikus IP-címmel

A szolgáltatás létrehozása előtt győződjön meg arról, hogy az AKS-fürt által használt egyszerű szolgáltatás delegált engedélyekkel rendelkezik a másik erőforráscsoportnak. Például:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Azt is megteheti, hogy a rendszer hozzárendelt felügyelt identitás engedélyek helyett a szolgáltatás névsor. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

*LoadBalancer* szolgáltatás létrehozásához a statikus nyilvános IP-címet, adja hozzá a `loadBalancerIP` tulajdonság és a statikus nyilvános IP-cím értékét a YAML-jegyzékfájlhoz. Hozzon létre `load-balancer-service.yaml` egy elnevezett fájlt, és másolja a következő YAML.Create a file named and copy in the following YAML. Adja meg saját nyilvános IP-címét az előző lépésben létrehozott. A következő példa a notnotációt a *myResourceGroup*nevű erőforráscsoportra is beállítja. Adja meg a saját erőforráscsoport nevét.

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

Hozza létre a szolgáltatást `kubectl apply` és a központi telepítést a paranccsal.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>DNS-címke alkalmazása a szolgáltatásra

Ha a szolgáltatás dinamikus vagy statikus nyilvános IP-címet használ, `service.beta.kubernetes.io/azure-dns-label-name` a szolgáltatás jegyzetelésével nyilvános DNS-címkét állíthat be. Ez közzétesz egy teljesen minősített tartománynevet a szolgáltatáshoz az Azure nyilvános DNS-kiszolgálói és legfelső szintű tartománya használatával. A jegyzetelési érték nek egyedinek kell lennie az Azure-helyen belül, ezért ajánlott egy megfelelően minősített címkét használni.   

Az Azure ezután automatikusan hozzáfűzi `<location>.cloudapp.azure.com` az alapértelmezett alhálózatot, például (ahol a hely a kiválasztott régió), a megadott névhez, a teljesen minősített DNS-név létrehozásához. Például:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> A szolgáltatás közzététele a saját tartományban, lásd: [Azure DNS][azure-dns-zone] és a [külső dns-projekt.][external-dns]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a Kubernetes szolgáltatásjegyzék *loadBalancerIP* tulajdonságában definiált statikus IP-cím nem létezik, vagy nem jött létre a csomópont erőforráscsoportban, és nincs konfigurálva további delegálás, a terheléselosztó szolgáltatás létrehozása sikertelen lesz. A hibaelhárításhoz tekintse át a szolgáltatás létrehozási eseményeit a [kubectl leírás][kubectl-describe] paranccsal. Adja meg a szolgáltatás nevét a YAML-jegyzékfájlban megadott módon, ahogy az a következő példában látható:

```console
kubectl describe service azure-load-balancer
```

A Kubernetes szolgáltatáserőforrással kapcsolatos információk jelennek meg. A következő példa kimenetének végén található *események* azt jelzik, hogy a *felhasználó által megadott IP-cím nem található.* Ezekben az esetekben ellenőrizze, hogy létrehozta-e a statikus nyilvános IP-címet a csomópont erőforráscsoportban, és hogy a Kubernetes szolgáltatásjegyzékben megadott IP-cím helyes-e.

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

Az alkalmazások hálózati forgalmának további szabályozása érdekében célszerű lehet ehelyett [létrehozni egy be- ésres vezérlőt.][aks-ingress-basic] [Statikus nyilvános IP-címmel rendelkező be- és be- és átvihető tartományvezérlőt][aks-static-ingress]is létrehozhat.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
