---
title: Hozzon létre egy belső terheléselosztót
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre és használhat belső terheléselosztót a szolgáltatások azure Kubernetes-szolgáltatás (AKS) használatával való elérhetővé.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4decd66a558b031f1aaaf9c64556dae545ed05d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668410"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Belső terheléselosztó használata az Azure Kubernetes szolgáltatással (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazásaihoz való hozzáférés korlátozásához létrehozhat és használhat belső terheléselosztót. A belső terheléselosztó a Kubernetes-szolgáltatást csak a Kubernetes-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé. Ez a cikk bemutatja, hogyan hozhat létre és használhat belső terheléselosztót az Azure Kubernetes-szolgáltatással (AKS).

> [!NOTE]
> Az Azure Load Balancer két termékkódban érhető el – *alapszintű* és *standard*. Alapértelmezés szerint a standard termékváltozat az AKS-fürt létrehozásakor használatos.  LoadBalancer típusú szolgáltatás létrehozásakor ugyanazt az LB-típust kapja, mint amikor a fürtöt kiépíti. További információ: [Azure load balancer SKU comparison.][azure-lb-comparison]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

Meglévő alhálózat vagy erőforráscsoport használata esetén az AKS-fürt egyszerű szolgáltatásának engedélyre van szüksége a hálózati erőforrások kezeléséhez. Általában rendelje hozzá a *hálózat közreműködői* szerepkört a szolgáltatásnévhez a delegált erőforrásokon. Az engedélyekről további információt az [AKS-hozzáférés delegálása más Azure-erőforrásokhoz című témakörben talál.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Belső terheléselosztó létrehozásához hozzon létre `internal-lb.yaml` egy *LoadBalancer* szolgáltatástípussal és az *azure-load-balancer-internal* megjegyzéssel elnevezett szolgáltatásjegyzéket, ahogy az a következő példában látható:

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

Telepítse a belső terheléselosztót a [kubectl alkalmazásával,][kubectl-apply] és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f internal-lb.yaml
```

Egy Azure-terheléselosztó jön létre a csomópont erőforráscsoportban, és ugyanahhoz a virtuális hálózathoz csatlakozik, mint az AKS-fürt.

A szolgáltatás részleteinek megtekintésekor a belső terheléselosztó IP-címe az *EXTERNAL-IP* oszlopban jelenik meg. Ebben az összefüggésben a *Külső* a terheléselosztó külső felületéhez kapcsolódik, nem pedig nyilvános külső IP-címet kap. Az IP-cím * \<függőről tényleges\> * belső IP-címre való váltása egy-két percet is igénybe vehet, amint az a következő példában látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP-cím megadása

Ha egy adott IP-címet szeretne használni a belső terheléselosztóval, adja hozzá a *loadBalancerIP* tulajdonságot a yaml-jegyzékfájl terheléselosztóhoz. A megadott IP-címnek ugyanabban az alhálózatban kell lennie, mint az AKS-fürtnek, és még nem rendelhető hozzá egy erőforráshoz.

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

Amikor üzembe helyezi, és megtekinti a szolgáltatás részleteit, az EXTERNAL-IP oszlopban lévő *IP-cím* a megadott IP-címet tükrözi:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Magánhálózatok használata

Az AKS-fürt létrehozásakor speciális hálózati beállításokat adhat meg. Ez a megközelítés lehetővé teszi a fürt üzembe helyezését egy meglévő Azure virtuális hálózatés alhálózatok. Az egyik forgatókönyv az AKS-fürt központi telepítése a helyszíni környezethez csatlakoztatott magánhálózatba, és csak belsőleg elérhető szolgáltatások futtatása. További információ: Saját virtuális hálózati alhálózatok konfigurálása [kubenet][use-kubenet] vagy [az Azure CNI][advanced-networking]használatával.

Nincs szükség az előző lépések módosítására a belső terheléselosztó magánhálózatot használó AKS-fürtben történő üzembe helyezéséhez. A terheléselosztó ugyanabban az erőforráscsoportban jön létre, mint az AKS-fürt, de a magánvirtuális hálózathoz és az alhálózathoz csatlakozik, ahogy az a következő példában látható:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Előfordulhat, hogy az AKS-fürt szolgáltatásnévnek meg kell adnia a *hálózati közreműködői* szerepkört annak az erőforráscsoportnak, amelyben az Azure virtuális hálózati erőforrásai üzembe helyezése van. Az [aks show-val][az-aks-show]tekintse meg `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`a szolgáltatásnév , például . Szerepkör-hozzárendelés létrehozásához használja az [az szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancsot.

## <a name="specify-a-different-subnet"></a>Másik alhálózat megadása

Adja meg a terheléselosztó alhálózatát, adja hozzá az *azure-load-balancer-internal-subnet* annotációt a szolgáltatáshoz. A megadott alhálózatnak ugyanabban a virtuális hálózatban kell lennie, mint az AKS-fürtnek. Üzembe helyezéskor az *EXTERNAL-IP-cím* a megadott alhálózat része.

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

A belső terheléselosztót használó összes szolgáltatás törlésekor maga a terheléselosztó is törlődik.

Közvetlenül is törölheti a szolgáltatást, mint bármely Kubernetes-erőforrás, például `kubectl delete service internal-app`a , amely szintén törli az alapul szolgáló Azure load balancer.

## <a name="next-steps"></a>További lépések

A Kubernetes szolgáltatásairól a [Kubernetes-szolgáltatások dokumentációjában][kubernetes-services]olvashat bővebben.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
