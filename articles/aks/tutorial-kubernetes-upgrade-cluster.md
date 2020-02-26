---
title: Az Azure-on futó Kubernetes oktatóanyaga – Fürtök frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő AKS-fürtöket a legújabb elérhető Kubernetes-verzióra.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 57cad9472c599bf0ad8f3e3d2ff53cb224db689a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593128"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Kubernetes Service (AKS) szolgáltatásban

Az alkalmazás és a fürt életciklusának részeként frissíthet a Kubernetes legújabb elérhető verziójára, és használhatja az új szolgáltatásokat. Az Azure Kubernetes Service- (AKS-) fürt frissíthető az Azure CLI segítségével.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hetedik része, egy Kubernetes-fürtöt frissítünk. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazás egy tároló-rendszerképbe van csomagolva. A rendszerkép fel lett töltve Azure Container Registryba, és létrehozott egy AK-fürtöt. Az alkalmazás ezután üzembe lett helyezve az AK-fürtön. Ha nem hajtja végre ezeket a lépéseket, és követni szeretné a lépést, kezdje az [1. oktatóanyag – tároló lemezképek létrehozása][aks-tutorial-prepare-app]című témakörben.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Az elérhető fürtverziók lekérése

A fürtök frissítése előtt az [az aks get-upgrades][] paranccsal ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példában az aktuális verzió a *1.13.10*, és az elérhető verziók a *frissítések* oszlopban láthatók.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Fürt frissítése

Az alkalmazások futtatásának minimalizálásához az AK-csomópontok körültekintően vannak kivezetve és kiürítve. Ebben a folyamatban a következő lépéseket hajtja végre:

1. A Kubernetes Scheduler megakadályozza, hogy a további hüvelyek a frissítendő csomóponton legyenek ütemezve.
1. A csomópontokon futó hüvelyek a fürt más csomópontjain vannak ütemezve.
1. Létrejön egy csomópont, amely a legújabb Kubernetes-összetevőket futtatja.
1. Ha az új csomópont készen áll, és csatlakozik a fürthöz, a Kubernetes ütemező elkezdi a hüvelyek futtatását.
1. A régi csomópont törölve lett, és a fürt következő csomópontja megkezdi a Kordon és a kiürítés folyamatát.

Az AKS-fürtök az [az aks upgrade][] paranccsal frissíthetők. A következő példa frissíti a fürtöt a Kubernetes verzió *1.14.6*.

> [!NOTE]
> Egyszerre csak egy alverzió frissíthető. Például a *1.12. x* verzióról *1.13. x*-re frissítheti, de a *1.12. x* -ről a *1.14. x* -re nem lehet közvetlenül frissíteni. A 1.12. *x* és *1.14. x*verzióra való frissítéshez először *a 1.12. x* -ről *1.13. x*-re kell frissíteni, majd egy újabb frissítést kell végrehajtani 1.13. *x* és *1.14. x*között.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

A következő összetömörített példa kimenete a *kubernetesVersion* most jelentések *1.14.6*mutatja:

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
  "kubernetesVersion": "1.14.6",
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

A következő példa kimenete azt mutatja, hogy az AK-fürt *KubernetesVersion 1.14.6*fut:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>A fürt törlése

Mivel ez az oktatóanyag a sorozat utolsó része, érdemes törölni az AK-fürtöt. Mivel a Kubernetes-csomópontok Azure-beli virtuális gépeken (VM) futnak, a csomópontok futtatása akkor is költségekkel jár, ha nem használja a fürtöt. Az az [Group delete][az-group-delete] paranccsal távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. Az egyszerű szolgáltatás eltávolításának lépéseiért lásd: az [AK szolgáltatással kapcsolatos főbb megfontolások és törlés][sp-delete].

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítettük a Kubernetest egy AKS-fürtben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

Az AKS-sel kapcsolatos további információkat erre a hivatkozásra kattintva érhet el.

> [!div class="nextstepaction"]
> [AK – áttekintés][aks-intro]

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
