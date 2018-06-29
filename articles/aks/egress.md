---
title: Engedélyezési lista kimenő forgalom Azure Kubernetes szolgáltatás (AKS) fürtből
description: Engedélyezési lista kimenő forgalom Azure Kubernetes szolgáltatás (AKS) fürtök
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100145"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Az Azure Kubernetes szolgáltatás (AKS) kimenő forgalom

Alapértelmezés szerint a kimenő forgalom cím Azure Kubernetes szolgáltatás (AKS) fürtök véletlenszerűen. Ez a konfiguráció esetén nem ideális kellene azonosításához külső szolgáltatások eléréséhez szükséges IP-címet. Ez a dokumentum létrehozása és karbantartása a statikusan hozzárendelt virtuális IP-cím AKS-fürtben lévő adatokat.

## <a name="egress-overview"></a>Kimenő forgalom áttekintése

Kimenő forgalom AKS fürtök Azure Load Balancer szabályokat, amelyeket követi [Itt][outbound-connections]. A következő típusú első Kubernetes szolgáltatást előtt `LoadBalancer` jön létre, az ügynök csomópontok nem részei egyetlen Azure Load Balancer címkészletből sem. Ebben a konfigurációban a csomópontok egy példányszintű nyilvános IP-cím nem. Azure lefordítja a kimenő folyam, amely nem konfigurálható vagy determinisztikus nyilvános forrás IP-címre.

Egyszer típusú Kubernetes szolgáltatás `LoadBalancer` ügynök csomópontokat ad hozzá egy Azure Load Balancer készlet létrehozása. A kimenő folyam Azure több azt az első nyilvános IP-cím a terheléselosztó hasonló adataival.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Érdekében, hogy a véletlenszerű IP-címeket használ, hozzon létre egy statikus IP-címet, és gondoskodjon arról, a terheléselosztó használja ezt a címet. Az IP-címet kell létrehozni a AKS a **csomópont** erőforráscsoportot.

Az erőforráscsoport neve az beszerzése a [az erőforrás megjelenítése] [ az-resource-show] parancsot. Az erőforráscsoport-név és a fürt neve egyezik a környezet frissítése.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Ezután használhatja a [létrehozása az hálózati nyilvános ip-] [ public-ip-create] parancs futtatásával hozzon létre egy statikus nyilvános IP-címet. Frissíteni kell egyeznie a neve gatherred az előző lépésben az erőforráscsoport neve.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>A statikus IP-szolgáltatás létrehozása

Most, hogy egy IP-címet, hozzon létre Kubernetes szolgáltatást, azzal a típussal `LoadBalancer` és az IP-címet rendel a szolgáltatást.

Hozzon létre egy fájlt `egress-service.yaml` és a következő YAM másolja. A környezeti felel meg az IP-címet frissíteni.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

A szolgáltatás és a központi telepítés létrehozása a `kubectl apply` parancsot.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Ez a szolgáltatás létrehozása az Azure Load Balancer egy új előtérbeli IP-cím konfigurálása. Ha nincs más IP-címek konfigurálása, majd **összes** kimenő forgalom használja ezt a címet. Az Azure terheléselosztó több címekkel van konfigurálva, amikor kilépő az első IP-cím, hogy terheléselosztót használja.

## <a name="verify-egress-address"></a>Kimenő forgalom cím ellenőrzése

Győződjön meg arról, hogy a nyilvános IP-cím használatban van-e, használjon egy szolgáltatás például `checkip.dyndns.org`.

Indítsa el, és csatolja a pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Szükség esetén telepítse curl a tárolóban:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, amely a virtuális IP-címet adja vissza:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Megtekintheti, hogy az IP-cím megegyezik-e a statikus IP-cím, az Azure load balancer csatolva.

## <a name="ingress-controller"></a>Érkező vezérlő

Az Azure Load Balancer több nyilvános IP-cím fenntartva elkerüléséhez fontolja meg az érkező vezérlőhöz. Érkező-vezérlők biztosítanak előnye van, például hálózati terheléselosztást, a TLS/SSL-lezárást, URI újraírások, és a felsőbb rétegbeli SSL/TLS titkosítás támogatása. AKS érkező tartományvezérlőinek kapcsolatos további információkért tekintse meg a [konfigurálása NGINX érkező vezérlő AKS-fürtben lévő] [ ingress-aks-cluster] útmutató.

## <a name="next-steps"></a>További lépések

További információ a jelen dokumentumban bemutatott szoftver.

- [Helm parancssori felület][helm-cli-install]
- [NGINX érkező vezérlő][nginx-ingress]
- [Az Azure Load Balancer kimenő kapcsolatok][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
