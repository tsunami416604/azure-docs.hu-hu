---
title: Belső terheléselosztó létrehozása az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan hozhat létre és tölthet fel szolgáltatásokat az Azure Kubernetes Service (AKS) egy belső terheléselosztó használata.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: a26eab83f567a46f613e3bfda95fd99aba2b79c0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404314"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztó az Azure Kubernetes Service (AKS) használatához

Az alkalmazások az Azure Kubernetes Service (AKS) való hozzáférés korlátozásához, hozzon létre, és a egy belső terheléselosztó használata. Belső terheléselosztó csak a Kubernetes-fürt ugyanazon a virtuális hálózaton futó alkalmazások számára elérhető lesz az egy Kubernetes-szolgáltatást. Ez a cikk bemutatja, hogyan hozhat létre és használhat egy belső terheléselosztó az Azure Kubernetes Service (AKS).

> [!NOTE]
> Az Azure Load Balancer érhető el két termékváltozata - *alapszintű* és *Standard*. Az AKS jelenleg támogatja a *alapszintű* Termékváltozat. Ha használni szeretné a *Standard* SKU, használhatja a felsőbb rétegbeli [aks-motor][aks-engine]. További információkért lásd: [Azure load balancer Termékváltozat összehasonlító][azure-lb-comparison].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

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

A belső terheléselosztó használatával telepíteni a [kubectl alkalmazása] kubectl-alkalmazása], és adja meg a YAML-jegyzékfájl neve:

```console
kubectl apply -f internal-lb.yaml
```

Az Azure load balancer csomópont az erőforráscsoportban létrehozott és csatlakoztatva az AKS-fürt ugyanazon a virtuális hálózaton.

Amikor a szolgáltatás részletes adatainak megtekintéséhez a belső terheléselosztó IP-címe jelenik meg a *EXTERNAL-IP* oszlop. Ebben a környezetben *külső* nincs a terheléselosztót külső adapterén viszonyítva, hogy a nyilvános, külső IP-címet kap. Eltarthat néhány percig is a módosítani kívánt IP-cím *\<függőben lévő\>* tényleges belső IP-címhez, az alábbi példában látható módon:

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

Telepítésekor a szolgáltatás adatait, az IP-címet, és a *EXTERNAL-IP* oszlop mutatja a megadott IP-cím:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Magánhálózatok használata

Az AKS-fürt létrehozásakor a speciális hálózati beállításokat is megadhat. Ez a megközelítés lehetővé teszi egy meglévő Azure virtuális hálózat és alhálózatok a fürt üzembe helyezéséhez. Egy forgatókönyvet, hogy a helyszíni környezet csatlakozik egy magánhálózaton az AKS-fürt üzembe helyezése, és belsőleg futtatása csak elérhető szolgáltatások. További információkért tekintse meg a saját virtuális hálózati alhálózat konfigurálása [Kubenet] [ use-kubenet] vagy [Azure CNI][advanced-networking].

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
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli