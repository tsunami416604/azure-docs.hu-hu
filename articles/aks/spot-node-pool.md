---
title: Előnézet – direktszínes csomópont-készlet hozzáadása egy Azure Kubernetes Service (ak) fürthöz
description: Ismerje meg, hogyan adhat hozzá egy helyszíni csomópont-készletet egy Azure Kubernetes Service-(ak-) fürthöz.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: d1dc54810371c4258616ca95b9f0f0abeee4c415
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462957"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Előnézet – direktszínes csomópont-készlet hozzáadása egy Azure Kubernetes Service (ak) fürthöz

A direktszínes csomópont-készlet egy [helyszíni virtuálisgép-méretezési csoport][vmss-spot]által támogatott csomópont-készlet. A helyszíni virtuális gépek használata az AK-fürttel rendelkező csomópontok esetében jelentős költségmegtakarítást biztosít az Azure-beli nem hasznosított kapacitás kihasználásához. A rendelkezésre álló kihasználatlan kapacitás mennyisége számos tényezőtől függ, többek között a csomópont méretétől, a régiótól és a napszaktól függően.

Egy helyszíni csomópont-készlet telepítésekor az Azure kiosztja a hely csomópontjait, ha rendelkezésre áll kapacitás. A helyszíni csomópontok esetében azonban nincs SLA. Egy tartalék tartományba helyezi a direktszín-készletet, amely biztonsági másolatot készít a helyszíni csomópontról, és nem biztosít magas rendelkezésre állású garanciát. Az Azure-infrastruktúra bármikor kizárja a helyszíni csomópontokat, amikor az Azure-nak szüksége van a kapacitás visszahívására.

A helyszíni csomópontok kiválóan alkalmasak olyan munkaterhelésekhez, amelyek kezelhetik a megszakításokat, a korai megszakításokat vagy a kizárásokat. A munkaterhelések, például a kötegelt feldolgozási feladatok, a fejlesztési és tesztelési környezetek, valamint a nagyméretű számítási munkaterhelések lehetnek jó jelöltek a helyszíni csomópont-készleteken.

Ebben a cikkben egy másodlagos helyszíni csomópont-készletet ad hozzá egy meglévő Azure Kubernetes-szolgáltatásbeli (ak-) fürthöz.

Ez a cikk a Kubernetes és a Azure Load Balancer fogalmak alapszintű megismerését feltételezi. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Amikor létrehoz egy fürtöt egy direktszínes csomópont-készlet használatára, a fürtnek Virtual Machine Scale Sets kell használnia a csomópont-készletekhez és a *szabványos* SKU Load Balancerhez is. A fürt létrehozása után további csomópont-készletet is fel kell vennie a helyszíni csomópont-készlet használatára. Egy későbbi lépésben egy további Node-készlet hozzáadására van szükség, de először engedélyeznie kell az előzetes verziójú szolgáltatást.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló, választhatók. A felhasználók visszajelzéseket és hibákat biztosítanak a Közösségtől. Az előzetes verzióban ezek a szolgáltatások éles használatra nem használhatók. A nyilvános előzetes verzió funkciói a "legjobb erőfeszítés" támogatás alatt állnak. Az AK technikai támogatási csapatának segítsége csak a munkaidőn kívüli időzóna (PST) időpontjában érhető el. További információkért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Spotpoolpreview előzetes funkciójának regisztrálása

Ha egy olyan AK-fürtöt szeretne létrehozni, amely egy direktszín-készletet használ, engedélyeznie kell a *spotpoolpreview* funkció jelzőjét az előfizetésén. Ez a szolgáltatás a fürt konfigurálásakor a legújabb szolgáltatás-fejlesztéseket biztosítja.

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

Regisztrálja a *spotpoolpreview* szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Ha egy olyan AK-fürtöt szeretne létrehozni, amely egy direktszín-készletet használ, szüksége lesz az *AK-előnézeti CLI-* bővítmény 0.4.32 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek az AK-fürtök helyszíni csomópont-készlettel való létrehozásakor és kezelésekor:

* A helyszíni csomópont-készlet nem lehet a fürt alapértelmezett csomópont-készlete. Egy helyszíni csomópont-készletet csak másodlagos készlethez lehet használni.
* Nem lehet frissíteni egy direktszín-készletet, mert a Direktszínű csomópontok készletei nem garantálják a Kordon és a kiürítést. A meglévő helyszíni csomópont-készletet egy újat kell cserélnie a műveletekhez, például a Kubernetes verziójának frissítéséhez. A direktszínek készletének lecseréléséhez hozzon létre egy új, a Kubernetes eltérő verzióját tartalmazó helyszíni csomópont-készletet, várjon, amíg az állapota *elkészült*, majd távolítsa el a régi csomópont-készletet.
* A vezérlő sík és a csomópont-készletek nem frissíthetők egyszerre. Ezeket külön kell frissítenie, vagy el kell távolítania a direktszín-készletet a vezérlő síkja és a többi csomópont-készlet egyidejű frissítéséhez.
* A helyhez tartozó csomópont-készletnek Virtual Machine Scale Setst kell használnia.
* A létrehozás után nem módosítható a ScaleSetPriority vagy a SpotMaxPrice.
* A SpotMaxPrice beállításakor az értéknek-1 vagy pozitív értéknek kell lennie, legfeljebb öt tizedesjegyre.
* Egy helyszíni csomópont-készletben a címke *kubernetes.Azure.com/scalesetpriority:spot*, a szennyező *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule*és a rendszer-hüvelyek is affinitással lesznek ellátva.
* Hozzá kell adnia egy [megfelelő tolerancia][spot-toleration] a számítási feladatok ütemezett csomópont-készleten való beosztásához.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Helyszíni csomópont-készlet hozzáadása AK-fürthöz

Egy olyan meglévő fürthöz kell hozzáadni egy direktszín-csomópontot, amelyhez engedélyezve van több Node-készlet. Az AK-fürtök több csomópontos készlettel való létrehozásával kapcsolatban [itt][use-multiple-node-pools]talál további információt.

Hozzon létre egy csomópont-készletet az az [AK nodepool Add][az-aks-nodepool-add]paranccsal.
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

Alapértelmezés szerint létre kell hoznia egy olyan csomópont-készletet, amelynek *prioritása* *normál* az AK-fürtben, amikor több csomópontos készlettel rendelkező fürtöt hoz létre. A fenti parancs egy kiegészítő csomópont-készletet hoz létre egy meglévő AK-fürthöz, amelynek *prioritása* a *spot*. A *spot* *prioritása* lehetővé teszi a csomópontok számára a direktszínek készletét. A *kizárás – házirend* paraméter a *delete* értékre van állítva a fenti példában, amely az alapértelmezett érték. Amikor beállítja a *törlési* [szabályzatot][eviction-policy] , a rendszer törli a csomópont mögöttes méretezési készletben lévő csomópontokat a kizáráskor. Megadhatja a kizárási házirendet a *felszabadításhoz*is. Ha a kizárási házirendet *felszabadítja*, a rendszer az alapul szolgáló méretezési csoport csomópontjait leállított állapotra állítja a kizárás után. A leállított kiosztott állapotú csomópontok száma a számítási kvótán belül van, és problémákat okozhat a fürtök skálázásával vagy frissítésével kapcsolatban. A *prioritás* és a *kizárás – a házirend* értékei csak a csomópont-készlet létrehozásakor állíthatók be. Ezek az értékek később nem frissíthetők.

A parancs a [fürt automéretezőjét][cluster-autoscaler]is engedélyezi, amelyet a helyszíni csomópont-készletekkel való használatra ajánlott használni. A fürtben futó munkaterhelések alapján a fürt autoskálázása méretezi és méretezi a csomópontok számát. A helyszíni csomópont-készletek esetében a fürt automatikusan méretezhető, ha további csomópontok is szükségesek. Ha módosítja a csomópontok maximális számát, akkor a `maxCount` fürthöz tartozó automéretezőhöz társított értéket is módosítania kell. Ha nem használ a fürt automatikus méretezését, a kizáráskor a helyszíni készlet végül nullára csökken, és manuális műveletre van szükség a további hely-csomópontok fogadásához.

> [!Important]
> Csak olyan helyszíni csomópont-készleteken ütemezhet munkaterheléseket, amelyek kezelhetik a megszakításokat, például a kötegelt feldolgozási feladatokat és a tesztelési környezeteket. Javasoljuk, hogy a helyszíni csomópont-készleten állítsa be a [megfertőzés és a tolerancia][taints-tolerations] beállítását annak biztosítására, hogy csak a csomópont-kizárásokat kezelő munkaterhelések legyenek ütemezve egy direktszínes csomópont-készleten. Például a fenti New York-i parancs alapértelmezett értéke *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* , így a csomóponton csak a megfelelő tolerálható hüvelyek lesznek ütemezve.

## <a name="verify-the-spot-node-pool"></a>A helyszíni csomópont-készlet ellenőrzése

Annak ellenőrzéséhez, hogy a csomópont-készlet hozzá van-e adva egy direktszínes csomópont-készletként:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Erősítse *meg a* *scaleSetPriority* .

Ahhoz, hogy egy Pod-t egy helyszíni csomóponton futtasson, adjon hozzá egy, a helyszíni csomóponton alkalmazott szennyező adatmennyiséget. Az alábbi példa egy olyan YAML-fájl egy részét mutatja be, amely egy, az előző lépésben használt *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* -adatmennyiségnek megfelelő tolerancia definiálására szolgál.

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

Ha olyan Pod-t alkalmaz, amelynél ez a tolerancia van telepítve, a Kubernetes sikeresen ütemezhetik a pod-t a csomópontokon az alkalmazott szennyező adataival.

## <a name="max-price-for-a-spot-pool"></a>Helyszíni készlet maximális díja
A [helyszíni példányok díjszabása][pricing-spot]a régió és az SKU alapján változó. További információ: a [Linux][pricing-linux] és a [Windows][pricing-windows]díjszabása.

A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. A *0,98765* érték például a maximális $0,98765 USD/óra. Ha a maximális árat a *-1*értékre állítja, a példány nem lesz kizárva az ár alapján. A példány díja a helyszínen érvényes, vagy a standard példány díjszabása, attól függően, hogy a kapacitás és a kvóta rendelkezésre áll-e.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan adhat hozzá egy helyszíni csomópont-készletet egy AK-fürthöz. További információ a hüvelyek csomópontok közötti szabályozásáról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][operator-best-practices-advanced-scheduler].

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