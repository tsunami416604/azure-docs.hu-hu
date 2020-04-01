---
title: Az Azure-on futó Kubernetes oktatóanyaga – Fürtök frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő AKS-fürtöket a legújabb elérhető Kubernetes-verzióra.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 4d9ef061904fb1a0fff25506eedb82158971bed5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77622028"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Kubernetes Service (AKS) szolgáltatásban

Az alkalmazás és a fürt életciklusának részeként frissíthet a Kubernetes legújabb elérhető verziójára, és használhatja az új szolgáltatásokat. Az Azure Kubernetes Service- (AKS-) fürt frissíthető az Azure CLI segítségével.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hetedik része, egy Kubernetes-fürtöt frissítünk. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

A korábbi oktatóanyagokban egy alkalmazás egy tárolórendszerképbe lett csomagolva. Ezt a lemezképet feltöltötte az Azure Container Registry, és létrehozott egy AKS-fürt. Az alkalmazás ezután telepítve lett az AKS-fürtre. Ha még nem tette meg ezeket a lépéseket, és szeretné követni végig, kezdje [tutorial 1 - Hozzon létre tároló képeket][aks-tutorial-prepare-app].

Ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.53-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Az elérhető fürtverziók lekérése

A fürtök frissítése előtt az [az aks get-upgrades][] paranccsal ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példában az aktuális verzió *1.14.8*, és a rendelkezésre álló verziók a *Frissítések* oszlopban jelennek meg.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.14.8           1.14.8             1.15.5, 1.15.7
```

## <a name="upgrade-a-cluster"></a>Fürt frissítése

A futó alkalmazások megzavarásának minimalizálása érdekében az AKS-csomópontokat gondosan kordonnal és kiürítéssel kell elhangolni. Ebben a folyamatban a következő lépéseket hajtja végre:

1. A Kubernetes-ütemező megakadályozza, hogy további podok ütemezve egy csomóponton, amely frissítendő.
1. A csomóponton futó podok a fürt más csomópontjaira vannak ütemezve.
1. Létrejön egy csomópont, amely a legújabb Kubernetes-összetevőket futtatja.
1. Amikor az új csomópont készen áll, és csatlakozott a fürthöz, a Kubernetes-ütemező elkezdi futtatni a podok rajta.
1. A régi csomópont törlődik, és a fürt következő csomópontja megkezdi a kordont és a kiürítési folyamatot.

Az AKS-fürtök az [az aks upgrade][] paranccsal frissíthetők. A következő példa frissíti a fürtöt a Kubernetes *1.14.6-os verziójára.*

> [!NOTE]
> Egyszerre csak egy alverzió frissíthető. Például frissíthet *1.14.x-ről* *1.15.x-re,* de nem frissíthet közvetlenül *1.14.x-ről* *1.16.x-re.* Az *1.14.x-ről* *1.16.x-re*történő frissítéshez először frissítsen *1.14.x-ről* *1.15.x-re*, majd hajtson végre egy másik frissítést *1.15.x-ről* *1.16.x-re*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.15.5
```

A következő kondenzált példa kimenet azt mutatja, hogy a *kubernetesVersion* most *1.15.5*jelentéseket jelent:

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
  "kubernetesVersion": "1.15.5",
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

A következő példa kimeneti mutatja az AKS-fürt fut *KubernetesVersion 1.15.5*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.15.5               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>A fürt törlése

Mivel ez az oktatóanyag a sorozat utolsó része, érdemes törölni az AKS-fürtöt. Mivel a Kubernetes-csomópontok Azure-beli virtuális gépeken (VM) futnak, a csomópontok futtatása akkor is költségekkel jár, ha nem használja a fürtöt. Az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

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
