---
title: Földrajzi elhelyezési csoportok használata az Azure Kubernetes Service (ak) fürtök késésének csökkentése érdekében
description: Megtudhatja, hogyan használhatja a közelségi csoportokat az AK-fürtök számítási feladatainak késésének csökkentése érdekében.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 382849c30c3520bbb208886de5f302f59fdac98b
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374456"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>A késés és a közelségi csoportok (előzetes verzió) csökkentése

> [!Note]
> Ha az AK-val közelítő elhelyezési csoportokat használ, az együttes elhelyezés csak az ügynök csomópontjaira vonatkozik. A csomópont csomópontra és a megfelelő üzemeltetett Pod-ról a pod-késésre való kijavítása megtörténik. Az egyhelyes elhelyezés nem befolyásolja a fürt vezérlő síkja elhelyezését.

Amikor üzembe helyezi az alkalmazást az Azure-ban, a virtuálisgép-(VM-) példányok elosztása a régiók vagy a rendelkezésre állási zónák között hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. Egyes alkalmazások, például a játékok, a mérnöki szimulációk és a nagy gyakoriságú kereskedelem (HFT) kis késést és gyorsan elvégezhető feladatokat igényelnek. A nagy teljesítményű számítástechnikai (HPC) forgatókönyvek esetében, például a következő esetekben érdemes lehet [Proximity elhelyezési csoportokat](https://docs.microsoft.com/azure/virtual-machines/linux/co-location#proximity-placement-groups) használni a fürt csomópontjaihoz.

## <a name="limitations"></a>Korlátozások

* A közelségi elhelyezési csoport egyetlen rendelkezésre állási zónát ölel fel.
* A virtuális gépek rendelkezésre állási csoportjait használó AK-fürtök jelenleg nem támogatottak.
* A meglévő csomópont-készletek nem módosíthatók közelségi elhelyezési csoportok használatára.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A (z) és az ügyfél-támogatási szolgálatok a lehető leghatékonyabban fedezik az előzetes verziókat. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásokat kell telepítenie:

- Az AK – előzetes verziójú 0.4.53 bővítmény

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Az előzetes verzió funkciójának beállítása a Proximity elhelyezési csoportok számára

> [!IMPORTANT]
> Ha az AK-val közelítő elhelyezési csoportokat használ, az együttes elhelyezés csak az ügynök csomópontjaira vonatkozik. A csomópont csomópontra és a megfelelő üzemeltetett Pod-ról a pod-késésre való kijavítása megtörténik. Az egyhelyes elhelyezés nem befolyásolja a fürt vezérlő síkja elhelyezését.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

A regisztráció több percet is igénybe vehet. A szolgáltatás regisztrálásának ellenőrzéséhez használja az alábbi parancsot:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Az előzetes verzió ideje alatt az *AK-előnézet CLI-* bővítményre van szükség a közelségi elhelyezési csoportok használatához. Használja az az [Extension Add][az-extension-add] parancsot, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Csomópont-készletek és Proximity-elhelyezési csoportok

A közelségi elhelyezési csoporttal üzembe helyezett első erőforrás egy adott adatközponthoz csatlakozik. Az azonos közelségi elhelyezési csoporttal telepített további erőforrások ugyanabban az adatközpontban találhatók. Ha a közelségi elhelyezési csoportot használó összes erőforrás le lett állítva (fel van foglalva) vagy törölve lett, már nincs csatlakoztatva.

* Számos csomópont-készlet társítható egyetlen közelségi elhelyezési csoporttal.
* Egy csomópont-készlet csak egyetlen közelségi elhelyezési csoporthoz társítható.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Új AK-fürt létrehozása közelségi elhelyezési csoporttal

Az alábbi példa az az [Group Create][az-group-create] paranccsal létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *CentralUS* régióban. Ezután létrejön egy *myAKSCluster* nevű AK-fürt az az [AK Create][az-aks-create] paranccsal. 

A gyorsított hálózatkezelés nagy mértékben javítja a virtuális gépek hálózati teljesítményét. Ideális esetben a közeli elhelyezési csoportok használata a gyorsított hálózatkezeléssel együtt. Alapértelmezés szerint az AK gyorsított hálózatkezelést használ a [támogatott virtuálisgép-példányokon](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), beleértve a legtöbb Azure-beli virtuális gépet két vagy több vCPU.

Hozzon létre egy új AK-fürtöt közelségi elhelyezési csoporttal:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Futtassa a következő parancsot, és tárolja a visszaadott azonosítót:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

A parancs kimenetet hoz létre, amely tartalmazza a közelgő CLI-parancsokhoz szükséges *azonosító* értéket:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Használja az alábbi parancs *myPPGResourceID* értékének közelségi csoportjának erőforrás-azonosítóját:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Közelségi elhelyezési csoport hozzáadása meglévő fürthöz

Új csomópont létrehozásával hozzáadhat egy közelségi elhelyezési csoportot egy meglévő fürthöz. Ezután igény szerint áttelepítheti a meglévő munkaterheléseket az új csomópont-készletbe, majd törölheti az eredeti csomópont-készletet.

Használja ugyanazt a közelségi elhelyezési csoportot, amelyet korábban hozott létre, és ez biztosítja, hogy az AK-fürt mindkét csomópontjának ügynök-csomópontjai fizikailag ugyanabban az adatközpontban helyezkednek el.

Használja a korábban létrehozott közelségi elhelyezési csoport erőforrás-AZONOSÍTÓját, és adjon hozzá egy új csomópont-készletet a következő [`az aks nodepool add`][az-aks-nodepool-add] paranccsal:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A fürt törléséhez használja a [`az group delete`][az-group-delete] parancsot az AK-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

* További információ a [közelségi elhelyezési csoportokról][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: /virtual-machines/windows/co-location.md#proximity-placement-groups/virtual-machines/linux/co-location
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

