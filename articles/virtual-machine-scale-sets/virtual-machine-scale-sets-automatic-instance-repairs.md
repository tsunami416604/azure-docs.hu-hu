---
title: Automatikus példányjavítás az Azure virtuálisgép-méretezési készleteivel
description: Megtudhatja, hogyan konfigurálhatja az automatikus javítási házirendet a virtuálisgép-példányok méretezési csoportban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274813"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Előzetes verzió: Automatikus példányjavítás az Azure virtuálisgép-méretezési készleteihez

Az Azure virtuálisgép-méretezési készletek automatikus példányjavításának engedélyezése segít az alkalmazások magas rendelkezésre állásának elérésében a kifogástalan állapotú példányok készletének fenntartásával. Ha a méretezési csoport egy példánya nem megfelelő állapotúnak talál, ahogy azt az [Application Health bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy a [Terheléselosztó állapotmintái jelentik,](../load-balancer/load-balancer-custom-probe-overview.md)akkor ez a szolgáltatás automatikusan végrehajtja a példány javítását a nem megfelelő állapotú példány törlésével és egy új cseréjével.

> [!NOTE]
> Ez az előzetes verziószolgáltatás szolgáltatásiszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Az automatikus példányjavítások használatának követelményei

**Az automatikus példányjavítási előzetes szolgáltatás letiltása**

Használja a REST API-t vagy az Azure PowerShellt az automatikus példányjavítási előzetes verzió letiltásához. Ezek a lépések regisztrálja az előfizetést az előzetes verziófunkcióhoz. Ne feledje, hogy ez csak egy egyszeri beállítás szükséges a funkció használatához. Ha az előfizetés már regisztrálva van az automatikus példányjavításelőzetes verzióhoz, akkor nem kell újra regisztrálnia. 

A REST API használata 

1. Regisztráljon a funkcióra a [Funkciók használatával - Regisztráció](/rest/api/resources/features/register) 

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

2. Várjon néhány percet, amíg az *állam* regisztrált ra *vált.* Ezt a következő API-t használhatja ennek megerősítéséhez.

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

3. Miután az *állapot* *regisztráltra*változott, futtassa a következőket.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Az Azure PowerShell használata

1. Regisztráljon a szolgáltatásra a [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) parancsmag használatával, amelyet [a Register-AzureRmProviderFeature követ](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Várjon néhány percet, amíg a *RegistrationState* regisztrált ra *változik.* Ezt a következő parancsmag segítségével erősítheti meg.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 A válasznak a következőnek kell lennie.

| Szolgáltatásnév                           | Szolgáltatóneve            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Regisztrálva              |

3. Miután a *RegistrationState-t* *regisztráltra*szeretné váltani, futtassa a következő parancsmast.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Alkalmazásállapot-figyelés engedélyezése méretezési készlethez**

A méretezési csoportnak engedélyeznie kell az alkalmazások állapotfigyelését a példányokhoz. Ez az Alkalmazás [állapotbővítménye](./virtual-machine-scale-sets-health-extension.md) vagy [a Terheléselosztó állapotmintáival végezhető](../load-balancer/load-balancer-custom-probe-overview.md)el. Ezek közül egyszerre csak egy engedélyezhető. Az alkalmazás állapotbővítménye vagy a terheléselosztó mintavételezi a virtuálisgép-példányokon konfigurált alkalmazásvégpontpingelését az alkalmazás állapotának meghatározásához. Ezt az állapotot a méretezési csoport orchestrator használja a példány állapotának figyelésére és szükség esetén javítások végrehajtására.

**Végpont konfigurálása állapotállapot megadására**

Az automatikus példányjavítási házirend engedélyezése előtt győződjön meg arról, hogy a méretezési csoport példányai alkalmazásvégpontot konfiguráltak az alkalmazás állapotának emlésére. Ha egy példány 200 (OK) állapotot ad vissza ezen az alkalmazásvégponton, akkor a példány "Kifogástalan" jelzővel rendelkezik. Minden más esetben a példány "Nem kifogástalan" jelöléssel van ellátva, beleértve a következő eseteket:

- Ha nincs alkalmazásvégpont konfigurálva a virtuálisgép-példányokban az alkalmazás állapotának biztosításához
- Ha az alkalmazásvégpont helytelenül van konfigurálva
- Ha az alkalmazás végpontja nem érhető el

A "Nem kifogástalan" jelölésű példányok esetében az automatikus javításokat a méretezési csoport váltja ki. Győződjön meg arról, hogy az alkalmazásvégpont megfelelően van konfigurálva, mielőtt engedélyezne az automatikus javítási házirendet a nem kívánt példányjavítások elkerülése érdekében, miközben a végpont konfigurálása van.

**Egyetlen elhelyezési csoport engedélyezése**

Ez az előnézet jelenleg csak az egyetlen elhelyezési csoportként üzembe helyezett méretezési csoportok esetében érhető el. A *singlePlacementGroup* tulajdonságot *true* értékre kell állítani a méretezési készlethez az automatikus példányjavítási szolgáltatás használatához. További információ az [elhelyezési csoportokról.](./virtual-machine-scale-sets-placement-groups.md#placement-groups)

**API-verzió**

Az automatikus javítási szabályzat a 2018-10-01-es vagy újabb számítási API-verzióhoz támogatott.

**Az erőforrás- vagy előfizetésáthelyezések korlátozásai**

Az előzetes verzió részeként az erőforrás- vagy előfizetésáthelyezések jelenleg nem támogatottak a méretezési csoportokszámára, ha az automatikus javítási szabályzat engedélyezve van.

**A szolgáltatásháló-méretezési csoportok korlátozása**

Ez az előzetes verzió funkció jelenleg nem támogatott a szolgáltatás háló méretezési csoportok.

## <a name="how-do-automatic-instance-repairs-work"></a>Hogyan működnek az automatikus példányjavítások?

Az automatikus példányjavítási szolgáltatás a méretezési csoport egyes példányainak állapotfigyelésén alapul. A méretezési csoport virtuálisgép-példányai beállíthatók úgy, hogy az alkalmazás állapotának állapotát az [Alkalmazásállapot-bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy a [Terheléselosztó állapotminta](../load-balancer/load-balancer-custom-probe-overview.md)segítségével adják ki. Ha egy példány nem kifogástalannak talál, majd a méretezési csoport javítási műveletet hajt végre a nem megfelelő állapotú példány törlésével és egy új cseréjével. Ez a funkció engedélyezhető a virtuálisgép méretezési készlet modell segítségével az *automaticRepairsPolicy* objektum.

### <a name="batching"></a>Kötegelés

Az automatikus példányjavítási műveletek kötegekben történnek. Adott időpontban a méretezési csoport példányainak legfeljebb 5%-a javítható az automatikus javítási házirenden keresztül. Ez segít elkerülni az egyidejű törlést és a nagyszámú példány újbóli létrehozását, ha egyidejűleg nem kifogástalannak talál.

### <a name="grace-period"></a>Türelmi időszak

Amikor egy példány a méretezési csoporton végrehajtott PUT, PATCH vagy POST művelet miatt egy állapotváltozási műveleten megy keresztül (például újraképzés, újratelepítés, frissítés stb.), akkor az adott példányon végzett javítási művelet et csak a türelmi időszakra való várakozás után hajtják végre. Türelmi idő az az időtartam, amely lehetővé teszi, hogy a példány kifogástalan állapotba. A türelmi időszak az állapotváltozás befejezése után kezdődik. Ez segít elkerülni a korai vagy véletlen javítási műveleteket. A türelmi időszak a méretezési csoport ban újonnan létrehozott példányok (beleértve a javítási művelet eredményeként létrehozott példányt is) tiszteletben lesztartva. A türelmi idő percben, ISO 8601 formátumban adható meg, és az *automaticRepairsPolicy.gracePeriod*tulajdonsággal állítható be. A türelmi idő 30 perc és 90 perc között lehet, és az alapértelmezett értéke 30 perc.

Az automatikus példányjavítási folyamat a következőképpen működik:

1. [Alkalmazásállapot-bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy [terheléselosztó állapot-mintavételek](../load-balancer/load-balancer-custom-probe-overview.md) ping elping el az alkalmazás végpontja belül minden virtuális gép a méretezési készlet az alkalmazás állapotának lehívása az egyes példányok.
2. Ha a végpont 200-as (OK) állapottal válaszol, akkor a példány "Kifogástalan" jelzővel van megjelölve. Az összes többi esetben (beleértve, ha a végpont nem érhető el), a példány van jelölve "Nem kifogástalan".
3. Ha egy példány nem kifogástalannak találja, a méretezési csoport a nem megfelelő állapotú példány törlésével és a lecseréléséhez egy új példány létrehozásával javítási műveletet indít el.
4. A példányjavítások kötegekben történik. Adott időpontban a méretezési csoportban szereplő összes példány legfeljebb 5%-a javítható. Ha egy méretezési csoport 20-nál kevesebb példányt, a javítások egy nem megfelelő állapotú példány egy időben történik.
5. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes nem megfelelő állapotú példányát meg nem javítják.

## <a name="instance-protection-and-automatic-repairs"></a>Példányvédelem és automatikus javítás

Ha egy méretezési csoport egy példánya védett a *[Méretezéskészlet-védelmi házirend](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* alkalmazásával, majd az automatikus javítások nem lesznek elvégezve az adott példányon.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatikus javítási házirend engedélyezése új méretezési csoport létrehozásakor

Az automatikus javítási házirend engedélyezéséhez, miközben új méretezési csoport létrehozása, győződjön meg arról, hogy a szolgáltatás engedélyezéséhez [való összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, hogy elkerülje a nem kívánt javítások indítását, amíg a végpont konfigurálása van. Az újonnan létrehozott léptékkészletek esetén minden példány javítása csak a türelmi időszak időtartamára való várakozás után történik. Az automatikus példányjavítás engedélyezéséhez egy méretezési csoportban használja az *automaticRepairsPolicy* objektumot a virtuálisgép méretezési csoport modelljében.

### <a name="rest-api"></a>REST API

A következő példa bemutatja, hogyan engedélyezheti az automatikus példányjavítást egy méretezési csoport modellben. Használja a 2018-10-10-01-es vagy újabb API-verziót.

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

Az automatikus példányjavítási szolgáltatás a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag használatával új méretezési csoport létrehozása közben engedélyezhető. Ez a mintaparancsfájl bemutatja egy méretezési csoport és a kapcsolódó erőforrások létrehozását a konfigurációs fájl használatával: [Teljes virtuálisgép-méretezési csoport létrehozása](./scripts/powershell-sample-create-complete-scale-set.md). Az automatikus példányjavítási házirend konfigurálásához adja hozzá az *EnableAutomaticRepair* és az *AutomaticRepairGracePeriod* paramétereket a méretezési csoport létrehozásához a konfigurációs objektumhoz. A következő példa 30 perces türelmi idővel engedélyezi a funkciót.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatikus javítási házirend engedélyezése meglévő méretezési csoport frissítésekor

Az automatikus javítási házirend engedélyezése előtt egy meglévő méretezési csoportban győződjön meg arról, hogy a szolgáltatás engedélyezéséhez [vonatkozó összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, hogy elkerülje a nem kívánt javítások indítását, amíg a végpont konfigurálása van. Az automatikus példányjavítás engedélyezéséhez egy méretezési csoportban használja az *automaticRepairsPolicy* objektumot a virtuálisgép méretezési csoport modelljében.

### <a name="rest-api"></a>REST API

A következő példa engedélyezi a szabályzat 40 perces türelmi idővel. Használja a 2018-10-10-01-es vagy újabb API-verziót.

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

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag segítségével módosítsa az automatikus példányjavítási szolgáltatás konfigurációját egy meglévő méretezési csoportban. A következő példa 40 percre frissíti a türelmi időszakot.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Hibaelhárítás

**Az automatikus javítási házirend engedélyezésének elmulasztása**

Ha "BadRequest" hibaüzenet jelenik meg a "Nem található "automaticRepairsPolicy" nevű objektumon a "properties" objektumon, akkor ellenőrizze a virtuális gép méretezési készletéhez használt API-verziót. Ehhez a funkcióhoz a 2018-10-01-es vagy újabb API-verzió szükséges.

**A példány nem javítható akkor sem, ha a házirend engedélyezve van**

A példány lehet türelmi idő. Ez az az idő, amely et a példány állapotváltozása után várni kell a javítások végrehajtása előtt. Ennek célja, hogy elkerüljék a korai vagy véletlen javításokat. A javítási műveletnek akkor kell megtörténnie, amikor a türelmi időszak befejeződött a példányhoz.

**A méretezési csoport példányainak alkalmazásállapotának megtekintése**

A Get [Instance View API-t](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) egy virtuálisgép-méretezési csoport példányai az alkalmazás állapotának megtekintéséhez használhatja. Az Azure PowerShell segítségével használhatja a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsmagot az *-InstanceView* jelzővel. Az alkalmazás állapotának állapota a *vmHealth*tulajdonság alatt található.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan konfigurálhatja [az Alkalmazásállapot-bővítményt](./virtual-machine-scale-sets-health-extension.md) vagy [a terheléselosztó állapotmintáit](../load-balancer/load-balancer-custom-probe-overview.md) a méretezési készletekhez.
