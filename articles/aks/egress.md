---
title: Statikus IP-címet a kimenő forgalmat az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan hozhat létre és használhat egy statikus nyilvános IP-címet a kimenő forgalmat az Azure Kubernetes Service (AKS)-fürt
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: aeffe172fd422f18e2828c5274e9a2ed13cc546a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103360"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>A kimenő forgalmat az Azure Kubernetes Service (AKS) egy statikus nyilvános IP-cím használata

Alapértelmezés szerint a kimenő IP-cím az Azure Kubernetes Service (AKS) fürtök véletlenszerűen rendeli. Ez a konfiguráció esetén nem ideális külső szolgáltatásokhoz való hozzáférés IP-cím például azonosítania kell. Ehelyett szükség lehet egy statikus IP-címet, amely szerepel az engedélyezési listán a szolgáltatás-hozzáférés hozzárendelése.

Ez a cikk bemutatja, hogyan hozhat létre és használhat egy statikus nyilvános IP-címet a kimenő forgalmat az AKS-fürtben való használatra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.46-os vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="egress-traffic-overview"></a>Kimenő adatforgalom áttekintése

Kimenő forgalmát egy AKS-fürtöt a következő [Azure Load Balancer konvenciók][outbound-connections]. Az első Kubernetes-szolgáltatás típusú előtt `LoadBalancer` létrejött, az ügynök egy AKS-fürtöt a csomópontok nem részei egyetlen Azure Load Balancer-készlet. Ebben a konfigurációban a csomópontok nincsenek példányszintű nyilvános IP-cím van. Az Azure a kimenő folyamat egy nyilvános IP-forráscím, amely nem konfigurálható vagy determinisztikus fordítja le.

Egyszer típusú Kubernetes szolgáltatás `LoadBalancer` létrejött, az ügynök csomópontokat ad hozzá egy Azure Load Balancer-készletet. A kimenő flow Azure fordítja le azt első nyilvános IP-címet a terheléselosztó a következőn:. A nyilvános IP-cím csak a gyűjteményszintű az erőforrás érvényes lesz. Ha törli a terheléselosztó Kubernetes szolgáltatás, a kapcsolódó terheléselosztót és IP-cím is törlődik. Ha szeretné hozzárendelni a megadott IP-címet vagy az újratelepített Kubernetes-szolgáltatás egy IP-cím megőrzése, hozzon létre, és statikus nyilvános IP-cím.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

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
```

Később a nyilvános IP cím használatával lekérheti a [az network public-ip list] [ az-network-public-ip-list] parancsot. Adja meg a csomópont erőforráscsoport nevét, és ezután lekérdezi a *IP-cím* az alábbi példában látható módon:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása a statikus IP-címmel

Szolgáltatás létrehozása a statikus nyilvános IP-címmel, adja hozzá a `loadBalancerIP` a YAML jegyzékfájlhoz-tulajdonság és a statikus nyilvános IP-cím értékét. Hozzon létre egy fájlt `egress-service.yaml` másolja be a következő yaml-kódot. Adja meg saját nyilvános IP-címet az előző lépésben létrehozott.

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

Hozzon létre, majd az üzembe helyezés a `kubectl apply` parancsot.

```console
kubectl apply -f egress-service.yaml
```

Ez a szolgáltatás az Azure Load Balancer konfigurálása egy új előtérbeli IP-címet. Ha nem rendelkezik más IP-címek konfigurálva, majd **összes** irányuló kimenő adatforgalmat most ezt a címet kell használnia. Az Azure Load Balancer több címekkel van konfigurálva, kimenő forgalom használja az első IP-cím a terheléselosztón.

## <a name="verify-egress-address"></a>Kimenő forgalom címének ellenőrzése

Ellenőrizze, hogy a statikus nyilvános IP-címet használja, a DNS-keresési szolgáltatás használhatja például `checkip.dyndns.org`.

Indítsa el, és a egy alapszintű csatolja *Debian* pod:

```console
kubectl run -it --rm aks-ip --image=debian
```

A tároló a webhely elérésére, `apt-get` telepítéséhez `curl` a tárolóba.

```console
apt-get update && apt-get install curl -y
```

Most már a curl használatával eléréséhez a *checkip.dyndns.org* hely. A kimenő IP-cím jelenik meg, ahogy az alábbi példa kimenetében megjelennek. Az IP-cím megegyezik a statikus nyilvános IP-cím létrehozása és a terheléselosztó szolgáltatás meghatározása:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

## <a name="next-steps"></a>További lépések

Az Azure Load Balancer több nyilvános IP-címek fenntartására elkerüléséhez bejövőforgalom-vezérlőjéhez helyette használhatja. Bejövő forgalom tartományvezérlők további előnyökkel, például a TLS/SSL-lezárást, támogatási URI újraírások, és a felsőbb rétegbeli SSL/TLS-titkosítás adja meg. További információkért lásd: [az aks-ben hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez][ingress-aks-cluster].

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