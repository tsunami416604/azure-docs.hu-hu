---
title: Engedélyezett kimenő Forgalomért Azure Kubernetes Service (AKS)-fürtből
description: Engedélyezett kimenő forgalomért Azure Kubernetes Service (AKS) fürtök
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347800"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Az Azure Kubernetes Service (AKS)-kimenő

Alapértelmezés szerint a kimenő forgalom címének Azure Kubernetes Service (AKS) fürtök véletlenszerűen rendeli. Ez a konfiguráció esetén nem ideális megoldás azonosíthatja a külső szolgáltatásokhoz hozzáférő IP-címet kellene. Ez a dokumentum részletesen bemutatja egy statikusan hozzárendelt kimenő IP-címet az AKS-fürt létrehozásához és kezeléséhez.

## <a name="egress-overview"></a>Kimenő forgalom áttekintése

Kimenő forgalmát egy AKS-fürtöt a következő vannak dokumentálva az Azure Load Balancer konvenciókat [Itt][outbound-connections]. Mielőtt az első Kubernetes-szolgáltatás típusú `LoadBalancer` létrejött, az ügynök csomópontok nem részei egyetlen Azure Load Balancer-készlet. Ebben a konfigurációban a csomópontok használata nélkül egy példányszintű nyilvános IP-címet. Az Azure a kimenő folyamat egy nyilvános IP-forráscím, amely nem konfigurálható vagy determinisztikus fordítja le.

Egyszer típusú Kubernetes szolgáltatás `LoadBalancer` létrejött, az ügynök csomópontokat ad hozzá egy Azure Load Balancer-készletet. A kimenő flow Azure fordítja le azt első nyilvános IP-címet a terheléselosztó a következőn:.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Megakadályozni véletlenszerű IP-címeket használja, hozzon létre statikus IP-címet, és győződjön meg arról, a terheléselosztó használja ezt a címet. Az IP-címet kell hozhatók létre az AKS **csomópont** erőforráscsoportot.

Az erőforráscsoport nevét az első a [az resource show] [ az-resource-show] parancsot. Az erőforráscsoport-nevet és a fürt nevét, hogy megfeleljenek a környezet frissítése.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Ezután a [az network public-ip létrehozása] [ public-ip-create] paranccsal hozzon létre egy statikus nyilvános IP-címet. Az erőforráscsoport nevét az utolsó lépésnél a név gatherred megfelelően frissítse.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása a statikus IP-címmel

Most, hogy egy IP-címet, egy Kubernetes-szolgáltatás létrehozása a típus `LoadBalancer` és az IP-cím hozzárendelése a szolgáltatást.

Hozzon létre egy fájlt `egress-service.yaml` másolja be a következő yaml-kódot. Frissítse az IP-címet a környezetéhez.

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

Hozzon létre, majd az üzembe helyezés a `kubectl apply` parancsot.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Ez a szolgáltatás létrehozása az Azure Load Balancer egy új előtérbeli IP-Címmel konfigurálja. Ha nem rendelkezik más IP-címek konfigurálva, majd **összes** irányuló kimenő adatforgalmat most ezt a címet kell használnia. Az Azure Load Balancer több címekkel van konfigurálva, kimenő forgalom használja az első IP-cím a terheléselosztón.

## <a name="verify-egress-address"></a>Kimenő forgalom címének ellenőrzése

Győződjön meg arról, hogy a nyilvános IP-címet használja, használjon egy szolgáltatás például `checkip.dyndns.org`.

Indítsa el, majd rendelje hozzá egy pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Ha szükséges, telepítse a curl a tárolóban:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, a kimenő IP-címet adja vissza, amely:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Megtekintheti, hogy az IP-cím megegyezik-e a statikus IP-címet az Azure load balancer csatlakozik.

## <a name="ingress-controller"></a>Bejövőforgalom-vezérlőt

Az Azure Load Balancer több nyilvános IP-címek fenntartására elkerüléséhez érdemes bejövőforgalom-vezérlőt. Bejövő-vezérlők adja meg, további előnyöket, például a terheléselosztás, a TLS/SSL-lezárást, URI újraírások, és a felsőbb rétegbeli SSL/TLS-titkosítás támogatása. Bejövő forgalom-tartományvezérlőket az aks-ben kapcsolatos további információkért lásd: a [NGINX konfigurálása az AKS-fürt bejövőforgalom-vezérlőjéhez] [ ingress-aks-cluster] útmutató.

## <a name="next-steps"></a>További lépések

További információ az ebben a dokumentumban bemutatott szoftver.

- [Helm CLI][helm-cli-install]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]
- [Az Azure Load Balancer kimenő kapcsolatok][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
