---
title: Belső terheléselosztó létrehozása az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan hozhat létre és tölthet fel szolgáltatásokat az Azure Kubernetes Service (AKS) egy belső terheléselosztó használata.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: e4b5b6085dbe9a09c90e059a5db8bee5d6d7a004
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699318"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztó az Azure Kubernetes Service (AKS) használatához

Az alkalmazások az Azure Kubernetes Service (AKS) való hozzáférés korlátozásához, hozzon létre, és a egy belső terheléselosztó használata. Belső terheléselosztó csak a Kubernetes-fürt ugyanazon a virtuális hálózaton futó alkalmazások számára elérhető lesz az egy Kubernetes-szolgáltatást. Ez a cikk bemutatja, hogyan hozhat létre és használhat egy belső terheléselosztó az Azure Kubernetes Service (AKS).

> [!NOTE]
> Az Azure Load Balancer érhető el két termékváltozata - *alapszintű* és *Standard*. További információkért lásd: [Azure load balancer Termékváltozat összehasonlító][azure-lb-comparison]. Az AKS jelenleg támogatja a *alapszintű* Termékváltozat. Ha használni szeretné a *Standard* SKU, használhatja a felsőbb rétegbeli [aks-motor][aks-engine].

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Egy belső terheléselosztót, hozzon létre egy Szolgáltatásjegyzék megnevezett `internal-lb.yaml` szolgáltatás típusú *terheléselosztó* és a *azure-load-balancer – belső* , ahogyan az alábbi jegyzet Példa:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

A telepítés után `kubectl apply -f internal-lb.yaml`, az Azure load balancer létrehozása és elérhetővé az AKS-fürt ugyanazon a virtuális hálózaton.

Amikor a szolgáltatás részletes adatainak megtekintéséhez a belső terheléselosztó IP-címe jelenik meg a *EXTERNAL-IP* oszlop. Eltarthat néhány percig is a módosítani kívánt IP-cím *\<függőben lévő\>* tényleges belső IP-címhez, az alábbi példában látható módon:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP-cím megadása

Ha szeretné a belső terheléselosztó használata a megadott IP-címet, adja hozzá a *loadBalancerIP* a load balancer YAML jegyzékfájl tulajdonságot. A megadott IP-cím az AKS-fürt azonos alhálózaton kell lennie, és kell már nem lehet hozzárendelni egy erőforráshoz.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

A szolgáltatási adatainak megtekintésekor az IP-cím található a *EXTERNAL-IP* oszlop mutatja a megadott IP-cím:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Magánhálózatok használata

Az AKS-fürt létrehozásakor a speciális hálózati beállításokat is megadhat. Ez a megközelítés lehetővé teszi egy meglévő Azure virtuális hálózat és alhálózatok a fürt üzembe helyezéséhez. Egy forgatókönyvet, hogy a helyszíni környezet csatlakozik egy magánhálózaton az AKS-fürt üzembe helyezése, és belsőleg futtatása csak elérhető szolgáltatások. További információkért lásd: [az aks-ben a speciális hálózati konfiguráció][advanced-networking].

Az előző lépések módosítása nélkül egy belső terheléselosztót egy magánhálózaton használó egy AKS-fürt üzembe helyezéséhez szükségesek. A terheléselosztó ugyanazt az erőforráscsoportot az AKS-fürtöt hoz létre, de csatlakozik a privát virtuális hálózatot és alhálózatot, az alábbi példában látható módon:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Előfordulhat, hogy kell biztosítania az AKS-fürthöz tartozó egyszerű szolgáltatás a *hálózati közreműködő* szerepkör ahhoz az erőforráscsoporthoz, amelyben az Azure virtuális hálózati erőforrások üzembe. Az egyszerű szolgáltatások megtekintéséhez [az aks show][az-aks-show], mint például `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Szerepkör-hozzárendelés létrehozásához használja a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot.

## <a name="specify-a-different-subnet"></a>Adjon meg egy másik alhálózatot

Adja meg a terheléselosztó egy alhálózatot, adja hozzá a *azure load-balancer-belső-alhálózattal* jegyzet a szolgáltatáshoz. A megadott alhálózat az AKS-fürt ugyanazon a virtuális hálózaton kell lennie. Ha üzembe helyezve, a terheléselosztó *EXTERNAL-IP* cím a megadott alhálózat része.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>A terheléselosztó törlése

A belső terheléselosztót használó összes szolgáltatások törlésekor a terheléselosztó maga is törlődik.

Emellett közvetlenül is törölheti, szolgáltatás, amely bármely Kubernetes-erőforrás, például `kubectl delete service internal-app`, amely is, majd törli az alapul szolgáló Azure load balancert.

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes-szolgáltatás, a [Kubernetes services dokumentációja][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus