---
title: Azure Kubernetes Service- (AKS-) fürt frissítése
description: Ismerje meg, hogyan frissíthet egy Azure Kubernetes-szolgáltatási (ak-) fürtöt a legújabb funkciók és biztonsági frissítések beszerzéséhez.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: 761df8abc60671341fcdd74e7c66111cfeb105ad
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259235"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service- (AKS-) fürt frissítése

Egy AK-alapú fürt életciklusának részeként gyakran frissítenie kell a legújabb Kubernetes-verzióra. Fontos, hogy alkalmazza a legújabb Kubernetes biztonsági kiadásokat, vagy frissítsen a legújabb funkciók beszerzéséhez. Ez a cikk bemutatja, hogyan frissítheti a fő összetevőket vagy egyetlen, alapértelmezett Node-készletet egy AK-fürtben.

A több csomópontot vagy Windows Server-csomópontokat (jelenleg előzetes verzióban) használó AK-fürtök esetében lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!WARNING]
> Az AK-fürtök frissítése kiváltja a csomópontjait, és kiüríti a csomópontokat. Ha alacsony számítási kvóta áll rendelkezésre, előfordulhat, hogy a frissítés meghiúsul. További információért lásd: [kvóták növelése](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Elérhető AK-fürt frissítésének keresése

A fürthöz elérhető Kubernetes-kiadások vizsgálatához használja az az [AK Get-upgrade][az-aks-get-upgrades] parancsot. Az alábbi példa a *myResourceGroup*nevű erőforráscsoport *myAKSCluster* nevű fürtjében elérhető frissítéseit ellenőrzi:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AK-fürt frissítésekor a Kubernetes alverziók nem hagyhatók ki. Például a *1.12. x*  ->  *1.13. x* vagy *1.13. x*  ->  *1.14. x* közötti frissítések engedélyezettek, a *1.12. x*  ->  *1.14. x* azonban nem.
>
> A frissítéshez *1.12. x*  ->  *1,14. x*-ről először a *1.12. x*1.13. x verzióról kell frissíteni,  ->  *1.13.x*majd az *1.13. x*  ->  *1.14. x*verziójáról kell frissíteni.

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

## <a name="customize-node-surge-upgrade-preview"></a>Csomópont-túlfeszültségek frissítésének testreszabása (előzetes verzió)

> [!Important]
> A csomópont-túllépések előfizetési kvótát igényelnek az egyes frissítési műveletekhez szükséges maximális túlfeszültségek számához. Például egy olyan fürt, amely 5 csomópontos készlettel rendelkezik, amelyek mindegyike 4 csomóponttal rendelkezik, összesen 20 csomóponttal rendelkezik. Ha az egyes csomópont-készletek maximális túllépésének értéke 50%, a frissítés befejezéséhez további számítási és IP-kvóta (2 csomópont * 5 készlet) szükséges.

Alapértelmezés szerint az AK egy további csomóponttal konfigurálja a verziófrissítést. Az egyik alapértelmezett értéke a maximális túllépési beállítás lehetővé teszi az AK számára a munkaterhelés megszakadásának minimalizálását egy további csomópont létrehozásával, mielőtt a meglévő alkalmazások kihelyezik a régebbi verziójú csomópontokat. A maximális túlfeszültség-érték testreszabható, és a frissítési sebesség és a frissítés megszakadása közötti kompromisszum lehetővé tételéhez. A maximális túlfeszültség-érték növelésével a verziófrissítési folyamat gyorsabban elvégezhető, de a maximális túllépés nagy értékének beállítása a frissítési folyamat során fennakadást okozhat. 

Például a 100%-os maximális túlfeszültség-érték biztosítja a lehető leggyorsabb frissítési folyamatot (a csomópontok számának megkettőzése), de a csomópont-készlet összes csomópontjának egyidejű kiürítését is okozhatja. Érdemes lehet magasabb értéket használni, például tesztelési környezetekhez. Üzemi csomópont-készletek esetén a 33%-os max_surge beállítást javasoljuk.

Az AK egész értékeket és százalékos értéket is elfogad a maximális túlfeszültséghez. Egy egész szám, például az "5", öt további csomópontot jelöl a megugráshoz. A (z) "50%" érték azt jelzi, hogy a készletben lévő csomópontok fele a felére mutat. A maximális túlterhelési százalék értéke lehet legalább 1%, legfeljebb 100%. A rendszer a legközelebbi csomópontok számára kerekíti a százalékos értéket. Ha a maximális túlfeszültség értéke alacsonyabb, mint az aktuális csomópontok száma a frissítéskor, a rendszer az aktuális csomópontok számát használja a maximális túlfeszültség értékhez.

A frissítés során a maximális túlfeszültség értéke lehet legalább 1, a maximális érték pedig a csomópont csomópontjainak számával egyenlő. Megadhat nagyobb értékeket, de a maximális túlfeszültséghez használt csomópontok maximális száma nem haladhatja meg a készletben lévő csomópontok számát a frissítéskor.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Az előzetes verzió funkciójának beállítása a csomópontok túlfeszültségének testreszabásához

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

A regisztráció több percet is igénybe vehet. A szolgáltatás regisztrálásának ellenőrzéséhez használja az alábbi parancsot:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

Az előzetes verzió ideje alatt a maximális túlterhelésnek az *AK-előnézeti CLI-* bővítményt kell használnia. Használja az az [Extension Add][az-extension-add] parancsot, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Egy csomópont-készleten a maximális túlfeszültség-beállítás állandó.  A későbbi Kubernetes-frissítések vagy a csomópont verziófrissítése ezt a beállítást fogja használni. A csomópont-készletek maximális túlfeszültség-értéke bármikor módosítható. A termelési csomópontok készletei esetében a 33%-os maximális túllépési beállítást javasoljuk.

A következő parancsokkal állíthatja be az új vagy a meglévő csomópont-készletek maximális túlfeszültségi értékeit.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AK-fürt elérhető verzióinak listájával frissítse a frissítést az az [AK upgrade][az-aks-upgrade] paranccsal. A frissítési folyamat során az AK egy új csomópontot ad hozzá a fürthöz, amely a megadott Kubernetes-verziót futtatja, majd alaposan [kiüríti és kiüríti][kubernetes-drain] az egyik régi csomópontot, hogy csökkentse az alkalmazások futtatásának megszakadását. Ha az új csomópontot alkalmazásként futtatja, a rendszer törli a régi csomópontot. Ez a folyamat addig ismétlődik, amíg a fürt összes csomópontja nem frissült.

A következő példa egy fürtöt frissít a *1.13.10*verzióra:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

A fürt frissítése néhány percet vesz igénybe, attól függően, hogy hány csomóponttal rendelkezik. 

> [!NOTE]
> A fürt frissítésének befejezéséhez teljes megengedett idő szükséges. Ezt az időt a termékének kiszámításával számítjuk ki `10 minutes * total number of nodes in the cluster` . Egy 20 csomópontos fürtben például a frissítési műveleteknek 200 percen belül sikeresnek kell lenniük, vagy az AK-ban sikertelen lesz a művelet, hogy elkerülje a nem helyreállítható fürt állapotát. A frissítési hiba helyreállításához próbálkozzon újra a frissítési művelettel az időkorlát lejárta után.

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
