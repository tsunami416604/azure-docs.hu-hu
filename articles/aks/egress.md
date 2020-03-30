---
title: Statikus IP-cím az Azure Kubernetes-szolgáltatás (AKS) kimenő forgalomához
description: Megtudhatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet az Azure Kubernetes-szolgáltatás (AKS) fürtjeiben lévő kimenő forgalomhoz
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 5850f8dfc08ed80dfe5e5e13f49808c3fd9338c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595756"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-cím használata az Azure Kubernetes-szolgáltatás (AKS) kimenő forgalomához

Alapértelmezés szerint az Azure Kubernetes-szolgáltatás (AKS) fürtkimenő IP-címét véletlenszerűen van hozzárendelve. Ez a konfiguráció nem ideális, ha például a külső szolgáltatásokhoz való hozzáféréshez szükséges IP-címet kell azonosítania. Ehelyett előfordulhat, hogy hozzá kell rendelnie egy statikus IP-címet, amely a szolgáltatáshoz való hozzáféréshez engedélyezési listán szerepel.

Ez a cikk bemutatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet az AKS-fürt kimenő forgalomhoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="egress-traffic-overview"></a>Kimenő forgalom – áttekintés

Az AKS-fürtből érkező kimenő forgalom az [Azure Load Balancer konvencióit][outbound-connections]követi. Az első Típusú Kubernetes-szolgáltatás `LoadBalancer` létrehozása előtt az AKS-fürt ügynökcsomópontjai nem részei az Azure Load Balancer készletnek. Ebben a konfigurációban a csomópontok nem rendelkeznek példányszintű nyilvános IP-címmel. Az Azure a kimenő folyamatot egy nyilvános forrás IP-címére fordítja, amely nem konfigurálható vagy determinisztikus.

Egy Kubernetes-szolgáltatás `LoadBalancer` létrehozása után ügynökcsomópontok hozzáadódnak egy Azure Load Balancer készlethez. Kimenő folyamat esetén az Azure fordítja le a terheléselosztón konfigurált első nyilvános IP-címre. Ez a nyilvános IP-cím csak az erőforrás élettartamára érvényes. Ha törli a Kubernetes LoadBalancer szolgáltatást, a társított terheléselosztó és IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy meg szeretne tartani egy IP-címet az áthelyezett Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Az erőforráscsoport nevének bekésezése az `--query nodeResourceGroup` [az aks show][az-aks-show] paranccsal, és adja hozzá a lekérdezési paramétert. A következő példa a *myResourceGroup*erőforráscsoport nevű erőforráscsoport ban az AKS-fürt *myAKSCluster* nevű csomóponterőforrás-csoportját kapja:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet az [az hálózati nyilvános ip create][az-network-public-ip-create] paranccsal. Adja meg az előző parancsban kapott csomóponterőforrás-csoport nevét, majd az IP-címerőforrás nevét, például *a myAKSPublicIP nevet:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím látható, ahogy az a következő tömörített kimeneti példában látható:

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
```

Később lekaphatja a nyilvános [IP-címet az az-hálózat nyilvános ip-lista][az-network-public-ip-list] paranccsal. Adja meg a csomópont erőforráscsoportjának nevét, majd kérdezze meg az *ipAddress-t* a következő példában látható módon:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása statikus IP-címvel

Ha egy szolgáltatást a statikus nyilvános IP-címet, adja hozzá a `loadBalancerIP` tulajdonság és a statikus nyilvános IP-cím értékét a YAML-jegyzékfájl. Hozzon létre `egress-service.yaml` egy elnevezett fájlt, és másolja a következő YAML.Create a file named and copy in the following YAML. Adja meg saját nyilvános IP-címét az előző lépésben létrehozott.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Hozza létre a szolgáltatást `kubectl apply` és a központi telepítést a paranccsal.

```console
kubectl apply -f egress-service.yaml
```

Ez a szolgáltatás egy új előtér-IP-címet konfigurál az Azure Load Balancer-en. Ha nincs konfigurálva más IP-cím, akkor **az összes** kimenő forgalomnak most ezt a címet kell használnia. Ha több cím van konfigurálva az Azure Load Balancer, kimenő forgalom az első IP-címet használja a terheléselosztó.

## <a name="verify-egress-address"></a>Kimenő forgalom címének ellenőrzése

A statikus nyilvános IP-cím használatának ellenőrzéséhez használhatja a DNS-keresési szolgáltatást, például `checkip.dyndns.org`a.

Kezdje el és csatolja az alapvető *Debian* podot:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Ha a tárolón belülről szeretne `apt-get` hozzáférni `curl` egy webhelyhez, telepítse a tárolóba.

```console
apt-get update && apt-get install curl -y
```

Most használja curl elérni a *checkip.dyndns.org* oldalon. A kimenő IP-cím jelenik meg, ahogy az a következő példa kimenet. Ez az IP-cím megegyezik a loadBalancer szolgáltatáshoz létrehozott és definiált statikus nyilvános IP-címmel:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>További lépések

Annak elkerülése érdekében, hogy több nyilvános IP-cím az Azure Load Balancer, ehelyett használhat egy be- ésfeleltetó. A rendszer-lefogadom a rendszer további előnyöket biztosít, például az SSL/TLS-végződtetést, az URI-újraírások támogatását és az upstream SSL/TLS titkosítást. További információ: [Hozzon létre egy alapvető bejövő kapcsolatvezérlőt az AKS-ben.][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
