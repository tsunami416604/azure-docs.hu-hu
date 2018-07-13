---
title: Az Azure Kubernetes Service (AKS) belső terheléselosztó létrehozása
description: Ismerje meg, hogyan hozhat létre és tölthet fel szolgáltatásokat az Azure Kubernetes Service (AKS) egy belső terheléselosztó használata.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001395"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztó az Azure Kubernetes Service (AKS) használatához

Belső terheléselosztási funkció lehetővé teszi egy Kubernetes-szolgáltatást a Kubernetes-fürt ugyanazon a virtuális hálózaton futó alkalmazások számára is elérhető. Ez a cikk bemutatja, hogyan hozhat létre és használhat egy belső terheléselosztó az Azure Kubernetes Service (AKS). Az Azure Load Balancer kétféle termékváltozatban érhető el: alap és Standard. Az AKS az alapszintű Termékváltozatot használja.

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Belső terheléselosztó létrehozásához a szolgáltatásjegyzék szolgáltatás típusú *terheléselosztó* és a *azure-load-balancer – belső* jegyzet az alábbi példában látható módon:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

A telepítés után `kubetctl apply`, az Azure load balancer létrehozása és elérhetővé az AKS-fürt ugyanazon a virtuális hálózaton.

![Belső load balancer AKS képe](media/internal-lb/internal-lb.png)

A szolgáltatási adatainak megtekintésekor az IP-cím található a *EXTERNAL-IP* oszlop a belső terheléselosztó IP-címe, az alábbi példában látható módon:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>IP-cím megadása

Ha szeretné a belső terheléselosztó használata a megadott IP-címet, adja hozzá a *loadBalancerIP* a load balancer specifikációja tulajdonságot. A megadott IP-cím az AKS-fürt azonos alhálózaton kell lennie, és kell már nem lehet hozzárendelni egy erőforráshoz.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

A szolgáltatási adatainak megtekintésekor az IP-cím az az *EXTERNAL-IP* tükrözi a megadott IP-cím:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Magánhálózatok használata

Az AKS-fürt létrehozásakor a speciális hálózati beállításokat is megadhat. Ez a megközelítés lehetővé teszi egy meglévő Azure virtuális hálózat és alhálózatok a fürt üzembe helyezéséhez. Egy forgatókönyvet, hogy a helyszíni környezet csatlakozik egy magánhálózaton az AKS-fürt üzembe helyezése, és belsőleg futtatása csak elérhető szolgáltatások. További információkért lásd: [az aks-ben a speciális hálózati konfiguráció][advanced-networking].

Az előző lépések módosítása nélkül egy belső terheléselosztót egy magánhálózaton használó egy AKS-fürt üzembe helyezéséhez szükségesek. A terheléselosztó ugyanazt az erőforráscsoportot az AKS-fürtöt hoz létre, de csatlakozik a privát virtuális hálózatot és alhálózatot, az alábbi példában látható módon:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Előfordulhat, hogy kell biztosítania az AKS-fürthöz tartozó egyszerű szolgáltatás a *hálózati közreműködő* szerepkör ahhoz az erőforráscsoporthoz, amelyben az Azure virtuális hálózati erőforrások üzembe. Az egyszerű szolgáltatások megtekintéséhez [az aks show][az-aks-show], mint például `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Szerepkör-hozzárendelés létrehozásához használja a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot.

## <a name="specify-a-different-subnet"></a>Adjon meg egy másik alhálózatot

Adja meg a terheléselosztó egy alhálózatot, adja hozzá a *azure load-balancer-belső-alhálózattal* jegyzet a szolgáltatáshoz. A megadott alhálózat az AKS-fürt ugyanazon a virtuális hálózaton kell lennie. Ha üzembe helyezve, a terheléselosztó *EXTERNAL-IP* cím a megadott alhálózat része.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>A terheléselosztó törlése

A belső terheléselosztót használó összes szolgáltatások törlésekor a terheléselosztó maga is törlődik.

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes-szolgáltatás, a [Kubernetes services dokumentációja][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create