---
title: Azure Kubernetes Service- (AKS-) fürt frissítése
description: Ismerje meg, hogyan frissíthet egy Azure Kubernetes-szolgáltatási (ak-) fürtöt a legújabb funkciók és biztonsági frissítések beszerzéséhez.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632609"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service- (AKS-) fürt frissítése

Egy AK-alapú fürt életciklusának részeként gyakran frissítenie kell a legújabb Kubernetes-verzióra. Fontos, hogy alkalmazza a legújabb Kubernetes biztonsági kiadásokat, vagy frissítsen a legújabb funkciók beszerzéséhez. Ez a cikk bemutatja, hogyan frissítheti a fő összetevőket vagy egyetlen, alapértelmezett Node-készletet egy AK-fürtben.

A több csomópontot vagy Windows Server-csomópontokat (jelenleg előzetes verzióban) használó AK-fürtök esetében lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!WARNING]
> Az AK-fürtök frissítése kiváltja a csomópontjait, és kiüríti a csomópontokat. Ha alacsony számítási kvóta áll rendelkezésre, előfordulhat, hogy a frissítés meghiúsul. További információért lásd: [kvóták növelése](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
> Ha a saját fürthöz tartozó automéretezéses üzembe helyezést futtatja, tiltsa le (ezt a replikálást nulla replikára méretezheti) a frissítés során, mert fennáll a lehetősége, hogy a frissítés megzavarja a frissítési folyamatot. A felügyelt automatikus méretezés automatikusan kezeli ezt. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Elérhető AK-fürt frissítésének keresése

A fürthöz elérhető Kubernetes-kiadások vizsgálatához használja az az [AK Get-upgrade][az-aks-get-upgrades] parancsot. Az alábbi példa a *myResourceGroup*nevű erőforráscsoport *myAKSCluster* nevű fürtjében elérhető frissítéseit ellenőrzi:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AK-fürt frissítésekor a Kubernetes alverziók nem hagyhatók ki. Például a *1.12. x* -> *1.13. x* vagy *1.13. x* -> *1.14. x* közötti frissítések engedélyezettek, a *1.12. x* -> *1.14. x* azonban nem.
>
> A frissítéshez *1.12. x* -> *1,14. x*-ről először a *1.12. x* -> *1.13. x*verzióról kell frissíteni, majd az 1.13. *x* -> *1.14. x*verziójáról kell frissíteni.

A következő példa kimenete azt mutatja, hogy a fürt frissíthető a *1.13.9* és a *1.13.10*verzióra:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Ha nincs elérhető frissítés, a következőket kapja:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AK-fürt elérhető verzióinak listájával frissítse a frissítést az az [AK upgrade][az-aks-upgrade] paranccsal. A frissítési folyamat során az AK egy új csomópontot ad hozzá a fürthöz, amely a megadott Kubernetes-verziót futtatja, majd alaposan [kiüríti és kiüríti][kubernetes-drain] az egyik régi csomópontot, hogy csökkentse az alkalmazások futtatásának megszakadását. Ha az új csomópontot alkalmazásként futtatja, a rendszer törli a régi csomópontot. Ez a folyamat addig ismétlődik, amíg a fürt összes csomópontja nem frissült.

A következő példa egy fürtöt frissít a *1.13.10*verzióra:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

A fürt frissítése néhány percet vesz igénybe, attól függően, hogy hány csomóponttal rendelkezik. 

> [!NOTE]
> A fürt frissítésének befejezéséhez teljes megengedett idő szükséges. Ezt az időt a termékének kiszámításával `10 minutes * total number of nodes in the cluster`számítjuk ki. Egy 20 csomópontos fürtben például a frissítési műveleteknek 200 percen belül sikeresnek kell lenniük, vagy az AK-ban sikertelen lesz a művelet, hogy elkerülje a nem helyreállítható fürt állapotát. A frissítési hiba helyreállításához próbálkozzon újra a frissítési művelettel az időkorlát lejárta után.

A frissítés sikerességének ellenőrzéséhez használja az az [AK show][az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A következő példa kimenete azt mutatja, hogy a fürt most már a *1.13.10*-t futtatja:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, hogyan frissíthet egy meglévő AK-fürtöt. Az AK-fürtök üzembe helyezésével és kezelésével kapcsolatos további információkért tekintse meg az oktatóanyagokat.

> [!div class="nextstepaction"]
> [AK-oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
