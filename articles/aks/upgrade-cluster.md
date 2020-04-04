---
title: Azure Kubernetes Service- (AKS-) fürt frissítése
description: Ismerje meg, hogyan frissítheti az Azure Kubernetes-szolgáltatás (AKS) fürtjeit a legújabb funkciók és biztonsági frissítések leéséhez.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632609"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service- (AKS-) fürt frissítése

Az AKS-fürt életciklusának részeként gyakran kell frissíteni a legújabb Kubernetes-verzióra. Fontos, hogy alkalmazza a legújabb Kubernetes biztonsági kiadásokat, vagy frissítsen a legújabb funkciók lekérni. Ez a cikk bemutatja, hogyan frissítheti a fő összetevőket vagy az AKS-fürt egyetlen alapértelmezett csomópontkészletét.

Több csomópontkészletet vagy Windows Server-csomópontot használó AKS-fürtök esetében (jelenleg előzetes verzióban az AKS-ben) című témakörben az [AKS csomópontkészletének frissítése][nodepool-upgrade]című témakörben található.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy az Azure CLI 2.0.65-ös vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!WARNING]
> Az AKS-fürtfrissítés kordont és a csomópontok kiürítését indítja el. Ha alacsony számítási kvótával rendelkezik, a frissítés sikertelen lehet. További információkért lásd: [Kvóták növelése.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
> Ha saját fürtautomatikus skálázó üzembe helyezését futtatja, tiltsa le (nullára méretezheti) a frissítés során, mivel van esély arra, hogy zavarja a frissítési folyamatot. A felügyelt automatikus méretező automatikusan kezeli ezt. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Az elérhető AKS-fürtfrissítések ellenőrzése

Annak ellenőrzéséhez, hogy mely Kubernetes-kiadások érhetők el a fürthöz, használja az [aks get-upgrades parancsot.][az-aks-get-upgrades] A következő példa a *myResourceGroup*nevű erőforráscsoportban a *myAKSCluster* nevű fürthöz elérhető frissítéseket keresi:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS-fürt frissítésekor a Kubernetes alverzióit nem lehet kihagyni. Az *1.12.x* -> *1.13.x* vagy *az 1.13.x* -> *1.14.x* közötti frissítések például engedélyezettek, de az *1.12.x* -> *1.14.x* nem.
>
> A frissítéshez *1.12.x* -> *1.14.x*- először *frissítsen 1.12.x* -> *1.13.x*- ről , majd frissítsen *1.13.x* -> *1.14.x-ről.*

A következő példa kimenetazt mutatja, hogy a fürt frissíthető *az 1.13.9* és *az 1.13.10*verzióra:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Ha nem érhető el frissítés, a következőket kapja:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AKS-fürt höz elérhető verziók listájával a frissítéshez használja az [aks frissítési][az-aks-upgrade] parancsot. A frissítési folyamat során az AKS hozzáad egy új csomópontot a fürthöz, amely a megadott Kubernetes-verziót futtatja, majd gondosan [kordont, és kiüríti][kubernetes-drain] az egyik régi csomópontot, hogy minimalizálja a futó alkalmazások megszakítását. Amikor az új csomópont ot az alkalmazáspodok futóként megerősíti, a régi csomópont törlődik. Ez a folyamat addig ismétlődik, amíg a fürt összes csomópontja frissítésre nem került.

A következő példa a fürtöt *az 1.13.10-es*verzióra frissíti:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

A fürt frissítése afürt frissítése attól függően, hogy hány csomóvan. 

> [!NOTE]
> A fürtfrissítés befejezéséhez összesen engedélyezett idő áll. Ezt az időt úgy számítjuk ki, hogy a szorzatát vesszük. `10 minutes * total number of nodes in the cluster` Egy 20 csomós fürtben például a frissítési műveleteknek 200 percen belül sikeresnek kell lenniük, különben az AKS nem sikerül a művelet a helyreállíthatatlan fürtállapot elkerülése érdekében. A frissítési hiba helyreállítása érdekében próbálkozzon újra a frissítési művelettel az időmegtalálat után.

A frissítés sikeressese ellenőrzéséhez használja az [aks show][az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példa kimenetazt mutatja, hogy a fürt most fut *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan frissíthet egy meglévő AKS-fürtöt. Az AKS-fürtök üzembe helyezéséről és kezeléséről az oktatóanyagok című témakörben olvashat bővebben.

> [!div class="nextstepaction"]
> [AKS oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
