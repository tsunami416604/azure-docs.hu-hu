---
title: Azure-beli virtuálisgép-méretezési csoportokkal rendelkező automatikus példányok javítása
description: Útmutató a méretezési csoportokban lévő virtuálisgép-példányok automatikus javítási házirendjének konfigurálásához
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274813"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Előzetes verzió: Azure-beli virtuálisgép-méretezési csoportok automatikus példányának javítása

Az Azure-beli virtuálisgép-méretezési csoportokhoz tartozó automatikus példányok javításának engedélyezése lehetővé teszi, hogy az alkalmazások magas rendelkezésre állást biztosítson a megfelelő állapotok mellett. Ha a méretezési csoport egyik példánya nem kifogástalan állapotú, mint az [Application Health Extension](./virtual-machine-scale-sets-health-extension.md) vagy a [Load Balancer Health](../load-balancer/load-balancer-custom-probe-overview.md)mintavétele, akkor ez a funkció automatikusan elvégzi a példányok javítását a nem kifogástalan példány törlésével, és újat hoz létre a helyére.

> [!NOTE]
> Ez az előzetes verziójú funkció szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.

## <a name="requirements-for-using-automatic-instance-repairs"></a>A példányok automatikus javításának használatára vonatkozó követelmények

**Az automatikus példány javításának előzetes verziója**

Használja a REST API vagy a Azure PowerShell lehetőséget az automatikus példány javításának előzetes verziójának megválasztásához. Ezek a lépések regisztrálják az előfizetését az előzetes verzió szolgáltatáshoz. Vegye figyelembe, hogy ez a funkció használatához csak egyszer kell beállítani. Ha az előfizetés már regisztrálva van az automatikus példány javításának előzetes verziójára, akkor nem kell újra regisztrálnia. 

A REST API használata 

1. Regisztráljon a szolgáltatáshoz a features [-Register](/rest/api/resources/features/register) használatával 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Várjon néhány percet, amíg az *állapot* *regisztrálva*értékre vált. A következő API-val ellenőrizheti ezt.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Ha az *állapot* *regisztrálva*értékre változott, futtassa az alábbi parancsot.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Az Azure PowerShell használata

1. Regisztráljon a szolgáltatáshoz a következő parancsmaggal: register [-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) , majd [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Várjon néhány percet, amíg a *RegistrationState* be nem vált a *regisztrált*értékre. Ezt az alábbi parancsmaggal ellenőrizheti.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 A válasznak a következőnek kell lennie:

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Regisztrálva              |

3. Ha a *RegistrationState* a *regisztrált*értékre vált, futtassa a következő parancsmagot.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Alkalmazás állapot-figyelésének engedélyezése a méretezési csoport számára**

A méretezési csoportnak rendelkeznie kell az alkalmazás állapotának figyelésével a példányok engedélyezéséhez. Ez az [alkalmazás állapotának](./virtual-machine-scale-sets-health-extension.md) vagy a [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md)használatával végezhető el. Egyszerre csak az egyik engedélyezhető. Az alkalmazás állapota vagy a terheléselosztó-tesztek pingelik a virtuálisgép-példányokon konfigurált alkalmazás-végpontot az alkalmazás állapotának megállapításához. Ezt az állapotot a méretezési csoport Orchestrator a példány állapotának figyelésére, valamint szükség esetén a javítások elvégzésére használja.

**Végpont beállítása állapot biztosításához**

A példányok automatikus javításának engedélyezése előtt gondoskodjon arról, hogy a méretezési csoport példányai rendelkezzenek az alkalmazás állapotának kibocsátására konfigurált alkalmazás-végponttal. Ha egy példány a 200 (OK) állapotot adja vissza ezen az alkalmazás-végponton, akkor a példány "kifogástalan" jelöléssel jelenik meg. Minden más esetben a példány "nem megfelelő" állapotúként van megjelölve, beleértve az alábbi eseteket:

- Ha a virtuálisgép-példányokban nincs konfigurálva alkalmazás-végpont az alkalmazás állapotának megadásához
- Ha az alkalmazás végpontja helytelenül van konfigurálva
- Ha az alkalmazás végpontja nem érhető el

A "nem megfelelő" állapotú példányok esetén a méretezési csoport automatikusan kijavítja az automatikus javításokat. Győződjön meg arról, hogy az alkalmazás végpontja megfelelően van konfigurálva az automatikus javítási házirend engedélyezése előtt, hogy elkerülje a nem kívánt példányok javítását, a végpontot pedig konfigurálja.

**Önálló elhelyezési csoport engedélyezése**

Ez az előzetes verzió jelenleg csak az önálló elhelyezési csoportként üzembe helyezett méretezési csoportok esetében érhető el. A *singlePlacementGroup* tulajdonságot *igaz* értékre kell állítani a méretezési csoport automatikus példány-javítási funkciójának használatához. További információ az [elhelyezési csoportokról](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**API-verzió**

Az automatikus javítási szabályzat a 2018-10-01-es vagy újabb számítási API-verzió esetén támogatott.

**Az erőforrás-vagy előfizetés-áthelyezésre vonatkozó korlátozások**

Ebben az előzetes verzióban az erőforrás-vagy előfizetési lépések jelenleg nem támogatottak a méretezési csoportokban, ha engedélyezve van az automatikus javítási szabályzat.

**Service Fabric-méretezési csoportok korlátozása**

Ez az előzetes verziójú funkció jelenleg nem támogatott a Service Fabric-méretezési csoportokban.

## <a name="how-do-automatic-instance-repairs-work"></a>Hogyan működik az automatikus példányok javítása?

Az automatikus példány-javítási funkció a méretezési csoportokban lévő egyes példányok állapotának figyelésére támaszkodik. A méretezési csoportokban lévő virtuálisgép-példányok [az alkalmazás állapotának vagy a](./virtual-machine-scale-sets-health-extension.md) [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md)használatával állíthatók be az alkalmazás állapotának kiosztására. Ha egy példány nem kifogástalan állapotú, akkor a méretezési csoport javítási műveletet hajt végre, ha törli a nem megfelelő állapotú példányt, és létrehoz egy újat a helyére. Ez a funkció a *automaticRepairsPolicy* objektum használatával engedélyezhető a virtuálisgép-méretezési csoport modelljében.

### <a name="batching"></a>Kötegelés

Az automatikus példány-javítási műveletek kötegekben lesznek elvégezve. Egy adott időpontban a méretezési csoport példányainak legfeljebb 5%-a javítása az automatikus javítási szabályzattal történik. Ez segít elkerülni a nagy számú példány egyidejű törlését és újbóli létrehozását, ha nem megfelelő állapotot észlelt.

### <a name="grace-period"></a>Türelmi időszak

Ha egy példány állapot-módosítási művelettel halad át a méretezési csoporton végrehajtott PUT, PATCH vagy POST művelet miatt (például rendszerkép, újratelepítés, frissítés stb.), akkor az adott példányon végzett javítási műveletek csak a türelmi időszakra való várakozás után történnek. A türelmi időszak azt az időtartamot adja meg, amely lehetővé teszi, hogy a példány Kifogástalan állapotba térjen vissza. A türelmi időszak az állapotváltozás befejeződése után kezdődik. Ez segít elkerülni a korai vagy véletlen javítási műveleteket. A türelmi időszakot a méretezési csoport minden újonnan létrehozott példánya esetében tiszteletben kell venni (beleértve a javítási művelet eredményeképpen létrehozott egyet is). A türelmi időszak ISO 8601 formátumú percekben van megadva, és a *automaticRepairsPolicy. gracePeriod*tulajdonsággal állítható be. A türelmi időszak 30 perc és 90 perc között lehet, és alapértelmezett értéke 30 perc.

Az automatikus példány javításának folyamata a következőképpen működik:

1. Az [alkalmazás állapotának vagy a](./virtual-machine-scale-sets-health-extension.md) [terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md) vizsgálatával pingelheti az alkalmazás végpontját a méretezési csoport minden egyes virtuális gépén az egyes példányok állapotának lekéréséhez.
2. Ha a végpont 200-as (OK) állapottal válaszol, akkor a példány "kifogástalan" állapotúként lesz megjelölve. Az összes többi esetben (ha a végpont nem érhető el), a példány "nem megfelelő" állapotúként van megjelölve.
3. Ha egy példány sérült állapotba kerül, a méretezési csoport kivált egy javítási műveletet a nem kifogástalan példány törlésével, és újat hoz létre a helyére.
4. A példányok javítása kötegekben történik. Egy adott időpontban a méretezési csoport összes példányának legfeljebb 5%-a javításra kerül. Ha a méretezési csoport kevesebb, mint 20 példánya van, a javításokat egyszerre egy nem kifogástalan állapotú példányhoz kell elvégezni.
5. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes nem kifogástalan állapotú példánya ki van javítva.

## <a name="instance-protection-and-automatic-repairs"></a>A példányok védelme és az automatikus javítás

Ha a méretezési csoport egy példánya védett, a védelem a *[méretezési csoportra vonatkozó műveletek védelmi házirendjének](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* alkalmazásával, akkor az automatikus javításokat a rendszer nem hajtja végre az adott példányon.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatikus javítási szabályzat engedélyezése új méretezési csoport létrehozásakor

Ha új méretezési csoport létrehozásakor szeretné engedélyezni az automatikus javítási szabályzatot, ellenőrizze, hogy teljesülnek-e a szolgáltatásba való bejelentkezéshez [szükséges összes követelmény](#requirements-for-using-automatic-instance-repairs) . Az alkalmazás-végpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, így elkerülhető a nem szándékolt javítások elindítása a végpont konfigurálása közben. Az újonnan létrehozott méretezési csoportok esetében minden példány javítása csak a türelmi időszak időtartamára való várakozás után történik. A méretezési csoportokban az automatikus példányok javításának engedélyezéséhez használja a *automaticRepairsPolicy* objektumot a virtuálisgép-méretezési csoport modelljében.

### <a name="rest-api"></a>REST API

Az alábbi példa bemutatja, hogyan engedélyezheti az automatikus példányok javítását egy méretezési csoport modelljében. Az API 2018-10-01-es vagy újabb verzióját használja.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Az automatikus példány-javítási funkció engedélyezhető egy új méretezési csoport létrehozásakor a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag használatával. Ez a minta parancsfájl végigvezeti a méretezési csoport és a kapcsolódó erőforrások létrehozásán a konfigurációs fájl használatával: [hozzon létre egy teljes virtuálisgép-méretezési készletet](./scripts/powershell-sample-create-complete-scale-set.md). A *EnableAutomaticRepair* és a *AutomaticRepairGracePeriod* paramétereknek a méretezési csoport létrehozásához való hozzáadásával konfigurálhatja az automatikus példányok javítási házirendjét. A következő példa egy 30 perces türelmi időszakot engedélyez a szolgáltatás számára.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatikus javítási szabályzat engedélyezése egy meglévő méretezési csoport frissítésekor

Mielőtt engedélyezné az automatikus javítási szabályzatot egy meglévő méretezési csoporton belül, győződjön meg arról, hogy a szolgáltatásba való bejelentkezéshez [szükséges összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazás-végpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, így elkerülhető a nem szándékolt javítások elindítása a végpont konfigurálása közben. A méretezési csoportokban az automatikus példányok javításának engedélyezéséhez használja a *automaticRepairsPolicy* objektumot a virtuálisgép-méretezési csoport modelljében.

### <a name="rest-api"></a>REST API

Az alábbi példa a 40 perces türelmi idővel engedélyezi a szabályzatot. Az API 2018-10-01-es vagy újabb verzióját használja.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag segítségével módosíthatja egy meglévő méretezési csoport automatikus példány-javítási funkciójának konfigurációját. A következő példa a türelmi időszakot 40 percre frissíti.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Hibaelhárítás

**Nem sikerült engedélyezni az automatikus javítási szabályzatot**

Ha "BadRequest" hibaüzenet jelenik meg, amely azt jelzi, hogy "a" nem található a "Properties" típusú objektum "automaticRepairsPolicy" tagja, akkor ellenőrizze a virtuálisgép-méretezési csoporthoz használt API-verziót. Ehhez a szolgáltatáshoz a 2018-10-01-es vagy újabb API-verzió szükséges.

**A példány nem kerül javításra, még akkor is, ha a szabályzat engedélyezve van**

A példány lehet türelmi időszak. Ez az az időtartam, ameddig a rendszer az állapot megváltozása után várakozik a példányon a javítások végrehajtása előtt. Ezzel elkerülhető az idő előtti vagy véletlen javítás. A javítási műveletnek akkor kell történnie, ha a türelmi időszak befejeződött a példányhoz.

**A méretezési csoport példányainak alkalmazás-állapotának megtekintése**

A virtuálisgép-méretezési csoport példányaihoz tartozó példányok [megtekintése API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) használatával megtekintheti az alkalmazás állapotát. A Azure PowerShell a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsmagot használhatja a *-InstanceView* jelzővel. Az alkalmazás állapotának állapotát a *vmHealth*tulajdonság alatt kell megadnia.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan konfigurálhatja a méretezési csoportokhoz az [alkalmazás állapotának kiterjesztését](./virtual-machine-scale-sets-health-extension.md) vagy a [terheléselosztó állapotát](../load-balancer/load-balancer-custom-probe-overview.md) .
