---
title: Előzetes verzió – Azonnali csomópontkészlet hozzáadása egy Azure Kubernetes-szolgáltatás (AKS) fürthöz
description: Megtudhatja, hogyan adhat hozzá egy pontcsomópont-készletet egy Azure Kubernetes-szolgáltatás (AKS) fürthöz.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622041"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Előzetes verzió – Azonnali csomópontkészlet hozzáadása egy Azure Kubernetes-szolgáltatás (AKS) fürthöz

A direktcsomópont-készlet egy csomópontkészlet, amelyet egy [virtuálisgép-méretezési csoport][vmss-spot]támogat. Az AKS-fürtdel rendelkező csomópontok hoz való azonnali virtuális gépek használata lehetővé teszi, hogy az Azure-ban a kihasználatlan kapacitás előnyeit jelentős költségmegtakarítással használja. A rendelkezésre álló kihasználatlan kapacitás mennyisége számos tényezőtől függ, beleértve a csomópont méretét, a régiót és a napszakot.

Egy direktcsomópont-készlet üzembe helyezésekor az Azure lefoglalja a pontcsomópontokat, ha van rendelkezésre álló kapacitás. De nincs SLA a direkt csomópontokhoz. A direktméretezési skálázási készlet, amely támogatja a direkt csomópont készlet egyetlen tartalék tartományban van telepítve, és nem nyújt magas rendelkezésre állási garanciákat. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja a pontcsomópontokat.

A direktcsomópontok kiválóan alkalmas a megszakítások, a korai felmondások vagy a kilakoltatások kezelésére szolgáló számítási feladatokhoz. Például a számítási feladatok, például a kötegelt feldolgozási feladatok, a fejlesztési és tesztelési környezetek és a nagy számítási számítási feladatok jó jelöltek lehetnek ütemezve egy helyszíni csomópontkészletben.

Ebben a cikkben egy másodlagos helycsomópont-készletet ad hozzá egy meglévő Azure Kubernetes-szolgáltatás (AKS) fürthöz.

Ez a cikk feltételezi a Kubernetes és az Azure Load Balancer fogalmak alapvető megértését. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Amikor létrehoz egy fürtöt egy direktszíncsomópont-készlet használatához, a fürtnek virtuálisgép-méretezési készleteket is kell használnia a csomópontkészletekhez és a *szabványos* termékváltozat terheléselosztóhoz. A fürt létrehozása után egy további csomópontkészletet is hozzá kell adnia egy pontcsomópont-készlet használatához. Egy további csomópontkészlet hozzáadása egy későbbi lépésben jelenik meg, de először engedélyeznie kell egy előnézeti funkciót.

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgálóak, opt-in. Azért biztosítjuk őket, hogy visszajelzéseket és hibákat gyűjtsenek a közösségünktől. Előzetes verzióban ezek a funkciók nem éles környezetben való használatra vannak szánva. A nyilvános előzetes verzió funkciói a "legjobb erőfeszítés" támogatás alá tartoznak. Az AKS technikai támogató csapatai csak munkaidőben állnak rendelkezésre a csendes-óceáni időzónában. További információt az alábbi támogatási cikkekben talál:
>
> * [Az AKS támogatási irányelvei][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Spotpoolpreview-előnézeti funkció regisztrálása

Azonnali csomópontkészletet használó AKS-fürt létrehozásához engedélyeznie kell a *spotpoolpreview* szolgáltatásjelzőt az előfizetésen. Ez a szolgáltatás a fürt konfigurálásakor a legújabb szolgáltatásfejlesztéseket biztosítja.

> [!CAUTION]
> Amikor regisztrál egy funkciót egy előfizetésen, jelenleg nem tudja törölni a funkciót. Miután engedélyezte az előzetes verzió néhány szolgáltatását, az alapértelmezett értékek et az összes AKS-fürthöz használni lehet, majd az előfizetésben létre kell hozni. Az éles előfizetések előzetes funkcióinak engedélyezése nem lehetséges. Használjon külön előfizetést az előzetes funkciók teszteléséhez és a visszajelzések összegyűjtéséhez.

Regisztrálja a *spotpoolpreview* szolgáltatásjelzőt az [az szolgáltatásregiszter][az-feature-register] parancsával, ahogy az a következő példában látható:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Az állapot megjelenítése néhány percet *vesz igénybe.* A regisztrációs állapotot az [az szolgáltatáslista][az-feature-list] paranccsal ellenőrizheti:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Ha kész, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI bővítmény telepítése

Azonnali csomópontkészletet használó AKS-fürt létrehozásához az *aks-preview* CLI bővítmény 0.4.32-es vagy újabb verziójára van szükség. Telepítse az *aks-preview* Azure CLI bővítményt az [az bővítmény hozzáadása][az-extension-add] paranccsal, majd ellenőrizze az elérhető frissítéseket az az extension [update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek, ha direktcsomópont-készlettel rendelkező AKS-fürtöket hoz létre és kezel:

* A direktcsomópont-készlet nem lehet a fürt alapértelmezett csomópontkészlete. A pontcsomópont-készlet csak másodlagos készlethez használható.
* A direktcsomópont-készlet nem frissíthető, mivel a pontcsomópont-medencék nem tudják garantálni a kordont és a kiürítést. Le kell cserélnie a meglévő direktcsomópont-készletet egy újra, például a Kubernetes-verzió frissítéséhez. Egy direkthelycsomópont-készlet cseréjéhez hozzon létre egy új pontcsomópont-készletet a Kubernetes egy másik verziójával, várja meg, amíg az állapota *Kész*, majd távolítsa el a régi csomópontkészletet.
* A vezérlősík és a csomópontkészletek nem frissíthetők egyszerre. Külön kell frissítenie őket, vagy el kell távolítania a pontcsomópont-készletet a vezérlősík és a fennmaradó csomópontkészletek egyidejű frissítéséhez.
* A direktcsomópont-készletnek virtuálisgép-méretezési készleteket kell használnia.
* A ScaleSetPriority vagy a SpotMaxPrice nem módosítható a létrehozás után.
* A SpotMaxPrice beállításakor az értéknek -1-nek vagy legfeljebb öt tizedesjegypontossággal rendelkező pozitív értéknek kell lennie.
* A direktcsomópont-készlet címkéje *kubernetes.azure.com/scalesetpriority:spot*, a *romlottság kubernetes.azure.com/scalesetpriority=spot:NoSchedule,* a rendszerpodok affinitása pedig anti-affinitás.
* Hozzá kell adnia egy [megfelelő tűrést][spot-toleration] a helyszíni csomópontkészlet munkaterhelésének ütemezéséhez.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Azonnali csomópontkészlet hozzáadása AKS-fürthöz

Hozzá kell adnia egy pontcsomópont-készletet egy meglévő fürthöz, amelynek több csomópontkészlete engedélyezve van. A több csomópontkészlettel rendelkező AKS-fürt létrehozásáról [itt][use-multiple-node-pools]olvashat bővebben.

Hozzon létre egy csomópontkészletet az [aks nodepool add][az-aks-nodepool-add]használatával.
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Alapértelmezés szerint az AKS-fürtben *normál* *prioritású* csomópontkészletet hoz létre, ha több csomópontkészlettel rendelkező fürtöt hoz létre. A fenti parancs egy kiegészítő csomópontkészletet ad hozzá egy meglévő AKS-fürthöz, *amelynek prioritása* *Spot*. A *Spot* *prioritása* a csomópontkészletet azonnali csomópontkészletté teszi. A *kilakoltatási házirend* paraméter értéke *Törlés* a fenti példában, amely az alapértelmezett érték. Ha a [kilakoltatási házirendet][eviction-policy] Törlés re *állítja,* a csomópontkészlet mögöttes méretezési készletében lévő csomópontok törlődnek, amikor kilakoltatják őket. A kilakoltatási házirendet a Felszabadításbeállításra is *állíthatja.* Ha a kilakoltatási házirendet *Felszabadításra állítja,* az alapul szolgáló méretezési halmaz csomópontjai a kilakoltatáskor leállított felszabadított állapotra vannak állítva. A leállított állapotban lévő csomópontok száma a számítási kvótával szemben, és problémákat okozhat a fürt méretezése vagy frissítése. A *prioritás* és *a kilakoltatási házirend* értékei csak a csomópontkészlet létrehozása során állíthatók be. Ezek az értékek később nem frissíthetők.

A parancs lehetővé teszi a [fürt automatikus skálázóját][cluster-autoscaler]is, amelyet a pontcsomópont-készletekkel való használatra ajánlott használni. A fürt ben futó számítási feladatok alapján a fürt automatikus skálázó felskálázása és a csomópontkészletben lévő csomópontok száma. A direktcsomópont-készletek esetében a fürt automatikus skálázója a kilakoltatás után növeli a csomópontok számát, ha további csomópontokra van még szükség. Ha módosítja a csomópontkészlet ben lévő csomópontok maximális számát, akkor `maxCount` a fürt automatikus skálázóhoz társított értéket is módosítania kell. Ha nem használ fürtautomatikus skálázót, a kilakoltatáskor a direktszínkészlet végül nullára csökken, és manuális műveletre van szükség a további pontcsomópontok fogadásához.

> [!Important]
> Csak a helyszíni csomópontkészletek, amely képes kezelni a megszakítások, például a kötegelt feldolgozási feladatok és tesztelési környezetek számítási feladatok ütemezése. Javasoljuk, hogy állítsa be [a taints és a tolerations][taints-tolerations] a helyszíni csomópont készlet annak érdekében, hogy csak a számítási feladatok, amelyek képesek kezelni a csomópont kilakoltatások vannak ütemezve a helyszínen csomópont készlet. A fenti parancs ny alapértelmezett értékével például *kubernetes.azure.com/scalesetpriority=spot:NoSchedule,* így csak a megfelelő tűrésű podok vannak ütemezve ezen a csomóponton.

## <a name="verify-the-spot-node-pool"></a>A pontcsomópont-készlet ellenőrzése

Annak ellenőrzése, hogy a csomópontkészlet azonnali csomópontkészletként lett-e hozzáadva:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confirm *scaleSetPriority* is *Spot*.

Ha egy podot egy pontcsomóponton szeretne futtatni, adjon hozzá egy olyan tűrést, amely megfelel a pontcsomópontra alkalmazott taint-nek. A következő példa egy yaml fájl egy részét mutatja be, amely *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* az előző lépésben használt kubernetes.azure.com/scalesetpriority=spot:NoSchedule-ttavandő tűrést definiálja.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Ha egy pod ezzel a tűrés tűrés telepítve van, Kubernetes sikeresen ütemezheti a pod a csomópontokon a taint alkalmazott.

## <a name="max-price-for-a-spot-pool"></a>Azonnali pool maximális ára
[A direktpéldányok díjszabása változó][pricing-spot], régió és termékváltozat alapján. További információ: Árak [Linux][pricing-linux] és [Windows][pricing-windows].

A változó árképzés, akkor lehetősége van arra, hogy állítsa be a maximális ár, USA dollárban (USD), akár 5 tizedesjegy. A *0,98765* érték például óránként 0,98765 USD maximális ár. Ha a maximális árat *-1-re*állítja, a példány nem lesz kizárva az ár alapján. A példány ára a Spot aktuális ára vagy egy szabványos példány ára lesz , attól függően, hogy melyik a kisebb, mindaddig, amíg van kapacitás és kvóta.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá egy pontcsomópont-készletet egy AKS-fürthöz. A podok csomópontkészletek közötti vezérléséről az [AKS speciális ütemező szolgáltatásainak gyakorlati tanácsai című témakörben][operator-best-practices-advanced-scheduler]talál további információt.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md