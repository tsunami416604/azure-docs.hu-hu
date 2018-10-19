---
title: Az Azure-on futó Kubernetes oktatóanyaga – Fürtök frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő AKS-fürtöket a legújabb elérhető Kubernetes-verzióra.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c0710be11b95b66d16661b5aff9cbf739ccda92
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901937"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Kubernetes Service (AKS) szolgáltatásban

Az alkalmazás és a fürt életciklusának részeként frissíthet a Kubernetes legújabb elérhető verziójára, és használhatja az új szolgáltatásokat. Az Azure Kubernetes Service- (AKS-) fürt frissíthető az Azure CLI segítségével. A frissítési folyamat során a Kubernetes-csomópontok megfelelően [el vannak szigetelve és ki vannak ürítve][kubernetes-drain], hogy minimális hatással legyenek a futó alkalmazásokra.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hetedik része, egy Kubernetes-fürtöt frissítünk. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk. Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.44-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Az elérhető fürtverziók lekérése

A fürtök frissítése előtt az [az aks get-upgrades][] paranccsal ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példában az aktuális verzió a következő: *1.9.6*. Az elérhető verziók az *Upgrades* (Frissítések) oszlopban láthatók.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Fürt frissítése

Az AKS-fürtök az [az aks upgrade][] paranccsal frissíthetők. Az alábbi példa a Kubernetes *1.10.6*-as verziójára frissíti a fürtöt.

> [!NOTE]
> Egyszerre csak egy alverzió frissíthető. Frissíthet például az *1.9.6*-osról az *1.10.3*-as verzióra, az *1.9.6*-ról azonban nem frissíthet közvetlenül az *1.11.x* verzióra. Ha az *1.9.6*-os verzióról az *1.11.x*-re szeretne frissíteni, először frissítse az *1.9.6*-os verziót az *1.10.3*-as verzióra, majd frissítse az *1.10.3*-at az *1.11.x*-es verzióra.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

Az alábbi sűrített példa eredménye azt mutatja, hogy a *kubernetesVersion* az *1.10.6*-as verziót jelenti:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Frissítés ellenőrzése

Az alábbiak szerint ellenőrizze az [az aks show][] paranccsal, hogy sikerült-e a frissítés:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példa kimenetében látható, hogy az AKS-fürt a *KubernetesVersion 1.10.6*-ot futtatja:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>A fürt törlése

Mivel ez az oktatóanyag-sorozat utolsó része, érdemes törölni az AKS-fürtöt. Mivel a Kubernetes-csomópontok Azure-beli virtuális gépeken (VM) futnak, a csomópontok futtatása akkor is költségekkel jár, ha nem használja a fürtöt. Az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban frissítettük a Kubernetest egy AKS-fürtben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

Az AKS-sel kapcsolatos további információkat erre a hivatkozásra kattintva érhet el.

> [!div class="nextstepaction"]
> [Az AKS áttekintése][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
