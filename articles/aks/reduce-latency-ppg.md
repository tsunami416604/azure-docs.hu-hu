---
title: Földrajzi elhelyezési csoportok használata az Azure Kubernetes Service (ak) fürtök késésének csökkentése érdekében
description: Megtudhatja, hogyan használhatja a közelségi csoportokat az AK-fürtök számítási feladatainak késésének csökkentése érdekében.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/10/2020
author: jluk
ms.openlocfilehash: 5b3dc3803cfb89f4a74d082b5913e69df1d03a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986712"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>A késés és a közelségi csoportok (előzetes verzió) csökkentése

> [!Note]
> Ha a közelségi elhelyezési csoportokat AK-on használja, az együttes elhelyezés csak az ügynök csomópontjaira vonatkozik. A csomópont csomópontra és a megfelelő üzemeltetett Pod-ról a pod-késésre való kijavítása megtörténik. Az egyhelyes elhelyezés nem befolyásolja a fürt vezérlő síkja elhelyezését.

Amikor üzembe helyezi az alkalmazást az Azure-ban, a virtuálisgép-(VM-) példányok elosztása a régiók vagy a rendelkezésre állási zónák között hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. Egyes alkalmazások, például a játékok, a mérnöki szimulációk és a nagy gyakoriságú kereskedelem (HFT) kis késést és gyorsan elvégezhető feladatokat igényelnek. A nagy teljesítményű számítástechnikai (HPC) forgatókönyvek esetében, például a következő esetekben érdemes lehet a [közeli elhelyezési csoportokat](../virtual-machines/linux/co-location.md#proximity-placement-groups) (PPG) használni a fürt csomópontjaihoz.

## <a name="limitations"></a>Korlátozások

* A közelségi elhelyezési csoport legfeljebb egy rendelkezésre állási zónához rendelhető.
* A csomópont-készletnek Virtual Machine Scale Sets kell használnia egy közelségi elhelyezési csoport hozzárendeléséhez.
* A csomópont-készletek csak létrehozási időpontot hozhatnak létre egy közelségi elhelyezési csoporthoz.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásokat kell telepítenie:

- Az AK – előzetes verziójú 0.4.53 bővítmény

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Az előzetes verzió funkciójának beállítása a Proximity elhelyezési csoportok számára

> [!IMPORTANT]
> Ha a közelségi elhelyezési csoportokat AK-beli csomópont-készletekkel használja, az együttes elhelyezés csak az ügynök csomópontjaira vonatkozik. A csomópont csomópontra és a megfelelő üzemeltetett Pod-ról a pod-késésre való kijavítása megtörténik. Az egyhelyes elhelyezés nem befolyásolja a fürt vezérlő síkja elhelyezését.

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

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Proximity elhelyezési csoportok konfigurálása rendelkezésre állási zónákkal

> [!NOTE]
> Míg a közelségi csoportok egy csomópont-készletet igényelnek a legfeljebb egy rendelkezésre állási zónában, a 99,9%-os alapszintű [Azure VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) még csak egyetlen zónában lévő virtuális gépek esetében érvényes.

A közelségi csoportok az egyes csomópont-készletekhez tartozó csomópont-Pool-fogalmak. A PPG-erőforrások használata nem befolyásolja az AK vezérlő síkja rendelkezésre állását. Ez hatással lehet arra, hogyan kell a fürtöt zónákhoz tervezni. Annak biztosítása érdekében, hogy a fürt több zónán legyen elosztva, a következő kialakítás ajánlott.

* Hozzon létre egy fürtöt az első rendszerkészlettel 3 zónával, és ne társítson közelségi elhelyezési csoportot. Ezzel biztosítható, hogy a rendszer egy dedikált csomópont-készletbe vigye, amely több zónában is elterjed.
* Adjon hozzá további felhasználói csomópont-készleteket az egyes készletekhez tartozó egyedi zónával és közelségi elhelyezési csoporttal. Egy példa az 1. zónában és a PPG1, a nodepool2 a 2. zónában és a PPG2, a 3. zónában a nodepool3 nodepool1. Ez a fürt szintjén biztosítja a csomópontok több zónában való megoszlását, és minden egyes csomópont-készlet egy dedikált PPG-erőforrással rendelkező kijelölt zónában található.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Új AK-fürt létrehozása közelségi elhelyezési csoporttal

Az alábbi példa az az [Group Create][az-group-create] paranccsal létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *CentralUS* régióban. Ezután létrejön egy *myAKSCluster* nevű AK-fürt az az [AK Create][az-aks-create] paranccsal.

A gyorsított hálózatkezelés nagy mértékben javítja a virtuális gépek hálózati teljesítményét. Ideális esetben a közeli elhelyezési csoportok használata a gyorsított hálózatkezeléssel együtt. Alapértelmezés szerint az AK gyorsított hálózatkezelést használ a [támogatott virtuálisgép-példányokon](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), beleértve a legtöbb Azure-beli virtuális gépet két vagy több vCPU.

Hozzon létre egy új AK-fürtöt az első rendszercsomópont-készlethez társított közelségi elhelyezési csoporttal:

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
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
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
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
