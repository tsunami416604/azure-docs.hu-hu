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
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603681"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatikus példányjavítások az Azure virtuálisgép-méretezési készleteihez

Az Azure virtuálisgép-méretezési készletek automatikus példányjavításának engedélyezése segít az alkalmazások magas rendelkezésre állásának elérésében a kifogástalan állapotú példányok készletének fenntartásával. Ha a méretezési csoport egy példánya nem megfelelő állapotúnak talál, ahogy azt az [Application Health bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy a [Terheléselosztó állapotmintái jelentik,](../load-balancer/load-balancer-custom-probe-overview.md)akkor ez a szolgáltatás automatikusan végrehajtja a példány javítását a nem megfelelő állapotú példány törlésével és egy új cseréjével.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Az automatikus példányjavítások használatának követelményei

**Alkalmazásállapot-figyelés engedélyezése méretezési készlethez**

A méretezési csoportnak engedélyeznie kell az alkalmazások állapotfigyelését a példányokhoz. Ez az Alkalmazás [állapotbővítménye](./virtual-machine-scale-sets-health-extension.md) vagy [a Terheléselosztó állapotmintáival végezhető](../load-balancer/load-balancer-custom-probe-overview.md)el. Ezek közül egyszerre csak egy engedélyezhető. Az alkalmazás állapotbővítménye vagy a terheléselosztó mintavételezi a virtuálisgép-példányokon konfigurált alkalmazásvégpontpingelését az alkalmazás állapotának meghatározásához. Ezt az állapotot a méretezési csoport orchestrator használja a példány állapotának figyelésére és szükség esetén javítások végrehajtására.

**Végpont konfigurálása állapotállapot megadására**

Az automatikus példányjavítási házirend engedélyezése előtt győződjön meg arról, hogy a méretezési csoport példányai alkalmazásvégpontot konfiguráltak az alkalmazás állapotának emlésére. Ha egy példány 200 (OK) állapotot ad vissza ezen az alkalmazásvégponton, akkor a példány "Kifogástalan" jelzővel rendelkezik. Minden más esetben a példány "Nem kifogástalan" jelöléssel van ellátva, beleértve a következő eseteket:

- Ha nincs alkalmazásvégpont konfigurálva a virtuálisgép-példányokban az alkalmazás állapotának biztosításához
- Ha az alkalmazásvégpont helytelenül van konfigurálva
- Ha az alkalmazás végpontja nem érhető el

A "Nem kifogástalan" jelölésű példányok esetében az automatikus javításokat a méretezési csoport váltja ki. Győződjön meg arról, hogy az alkalmazásvégpont megfelelően van konfigurálva, mielőtt engedélyezne az automatikus javítási házirendet a nem kívánt példányjavítások elkerülése érdekében, miközben a végpont konfigurálása van.

**Egyetlen elhelyezési csoport engedélyezése**

Ez a funkció jelenleg csak az egyetlen elhelyezési csoportként üzembe helyezett méretezési csoportok esetében érhető el. A *singlePlacementGroup* tulajdonságot *true* értékre kell állítani a méretezési készlethez az automatikus példányjavítási szolgáltatás használatához. További információ az [elhelyezési csoportokról.](./virtual-machine-scale-sets-placement-groups.md#placement-groups)

**API-verzió**

Az automatikus javítási szabályzat a 2018-10-01-es vagy újabb számítási API-verzióhoz támogatott.

**Az erőforrás- vagy előfizetésáthelyezések korlátozásai**

Erőforrás- vagy előfizetésáthelyezések jelenleg nem támogatottak a méretezési csoportok, ha az automatikus javítási szolgáltatás engedélyezve van.

**A szolgáltatásháló-méretezési csoportok korlátozása**

Ez a szolgáltatás jelenleg nem támogatott a szolgáltatás háló méretezési csoportok.

## <a name="how-do-automatic-instance-repairs-work"></a>Hogyan működnek az automatikus példányjavítások?

Az automatikus példányjavítási szolgáltatás a méretezési csoport egyes példányainak állapotfigyelésén alapul. A méretezési csoport virtuálisgép-példányai beállíthatók úgy, hogy az alkalmazás állapotának állapotát az [Alkalmazásállapot-bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy a [Terheléselosztó állapotminta](../load-balancer/load-balancer-custom-probe-overview.md)segítségével adják ki. Ha egy példány nem kifogástalannak talál, majd a méretezési csoport javítási műveletet hajt végre a nem megfelelő állapotú példány törlésével és egy új cseréjével. A legújabb virtuálisgép-méretezési csoport modellje az új példány létrehozásához használatos. Ez a funkció engedélyezhető a virtuálisgép méretezési készlet modell segítségével az *automaticRepairsPolicy* objektum.

### <a name="batching"></a>Kötegelés

Az automatikus példányjavítási műveletek kötegekben történnek. Adott időpontban a méretezési csoport példányainak legfeljebb 5%-a javítható az automatikus javítási házirenden keresztül. Ez segít elkerülni az egyidejű törlést és a nagyszámú példány újbóli létrehozását, ha egyidejűleg nem kifogástalannak talál.

### <a name="grace-period"></a>Türelmi időszak

Amikor egy példány a méretezési csoporton végrehajtott PUT, PATCH vagy POST művelet miatt egy állapotváltozási műveleten megy keresztül (például újraképzés, újratelepítés, frissítés stb.), akkor az adott példányon végzett javítási művelet et csak a türelmi időszakra való várakozás után hajtják végre. Türelmi idő az az időtartam, amely lehetővé teszi, hogy a példány kifogástalan állapotba. A türelmi időszak az állapotváltozás befejezése után kezdődik. Ez segít elkerülni a korai vagy véletlen javítási műveleteket. A türelmi időszak a méretezési csoport ban újonnan létrehozott példányok (beleértve a javítási művelet eredményeként létrehozott példányt is) tiszteletben lesztartva. A türelmi idő percben, ISO 8601 formátumban adható meg, és az *automaticRepairsPolicy.gracePeriod*tulajdonsággal állítható be. A türelmi idő 30 perc és 90 perc között lehet, és az alapértelmezett értéke 30 perc.

### <a name="suspension-of-repairs"></a>A javítások felfüggesztése 

A virtuálisgép-méretezési készletek lehetővé teszik az automatikus példányjavítások ideiglenes felfüggesztését, ha szükséges. A *serviceState* automatikus javítások a tulajdonság *vezénylésiSzolgáltatások* a virtuálisgép-méretezési csoport példánynézetében mutatja az automatikus javítások aktuális állapotát. Ha egy méretezési csoport automatikus javításra van beadva, a *serviceState* paraméter értéke *Futás*értékre van állítva. Amikor az automatikus javítások at egy méretezési csoport, a paraméter *serviceState* van *beállítva,* hogy Felfüggesztett . Ha az *automaticRepairsPolicy* méretezési csoporton van definiálva, de az automatikus javítási szolgáltatás nincs engedélyezve, akkor a *serviceState* paraméter *nem fut*.

Ha a nem megfelelő állapotúpéldányok méretezési csoportban való cseréjére újonnan létrehozott példányok továbbra is nem kifogástalanállapotúak maradnak a javítási műveletek ismételt végrehajtása után is, akkor biztonsági intézkedésként a platform frissíti a *serviceState automatikus* javítást *Felfüggesztett*. Az automatikus javítást újra folytathatja, ha az automatikus javításokhoz a *serviceState* értékét *futásra állítja.* Részletes utasításokat a méretezési csoport [automatikus javítási szabályzatszolgáltatás-házirendszolgáltatás-házirendjének megtekintéséről és frissítéséről](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) szóló szakasz tartalmaz. 

Az automatikus példányjavítási folyamat a következőképpen működik:

1. [Alkalmazásállapot-bővítmény](./virtual-machine-scale-sets-health-extension.md) vagy [terheléselosztó állapot-mintavételek](../load-balancer/load-balancer-custom-probe-overview.md) ping elping el az alkalmazás végpontja belül minden virtuális gép a méretezési készlet az alkalmazás állapotának lehívása az egyes példányok.
2. Ha a végpont 200-as (OK) állapottal válaszol, akkor a példány "Kifogástalan" jelzővel van megjelölve. Az összes többi esetben (beleértve, ha a végpont nem érhető el), a példány van jelölve "Nem kifogástalan".
3. Ha egy példány nem kifogástalannak találja, a méretezési csoport a nem megfelelő állapotú példány törlésével és a lecseréléséhez egy új példány létrehozásával javítási műveletet indít el.
4. A példányjavítások kötegekben történik. Adott időpontban a méretezési csoportban szereplő összes példány legfeljebb 5%-a javítható. Ha egy méretezési csoport 20-nál kevesebb példányt, a javítások egy nem megfelelő állapotú példány egy időben történik.
5. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes nem megfelelő állapotú példányát meg nem javítják.

## <a name="instance-protection-and-automatic-repairs"></a>Példányvédelem és automatikus javítás

Ha egy méretezési csoport egy példányát a [védelmi házirendek](./virtual-machine-scale-sets-instance-protection.md)egyikének alkalmazásával védi, akkor az adott példányon nem történik automatikus javítás. Ez egyaránt vonatkozik a védelmi házirendek: A méretezési csoport és *a Méretezési csoport* hoz való védelem elleni *védelem.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Értesítés és automatikus javítás leállítása

Ha a [leszakító értesítési](./virtual-machine-scale-sets-terminate-notification.md) funkció engedélyezve van egy méretezési csoportban, majd az automatikus javítási művelet során egy nem megfelelő állapotú példány törlése követi a megszakított értesítési konfigurációt. A rendszer az Azure metaadatszolgáltatásán – ütemezett eseményeken – keresztül küldi el a megszakítási értesítést, és a példányok törlése a beállított késleltetési időtúllépés időtartama alatt késik. Azonban egy új példány létrehozása a nem megfelelő állapotú helyett nem várja meg a késleltetési időtúltöltés befejezését.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatikus javítási házirend engedélyezése új méretezési csoport létrehozásakor

Az automatikus javítási házirend engedélyezéséhez, miközben új méretezési csoport létrehozása, győződjön meg arról, hogy a szolgáltatás engedélyezéséhez [való összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, hogy elkerülje a nem kívánt javítások indítását, amíg a végpont konfigurálása van. Az újonnan létrehozott léptékkészletek esetén minden példány javítása csak a türelmi időszak időtartamára való várakozás után történik. Az automatikus példányjavítás engedélyezéséhez egy méretezési csoportban használja az *automaticRepairsPolicy* objektumot a virtuálisgép méretezési csoport modelljében.

Ezzel a [gyorsindítási sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) is üzembe helyezhet egy virtuálisgép-méretezési készletet terheléselosztó állapotmintával és 30 perces türelmi idővel engedélyezett automatikus példányjavításokkal.

### <a name="azure-portal"></a>Azure Portal
 
A következő lépések, amelyek lehetővé teszik az automatikus javítási házirend et új méretezési csoport létrehozásakor.
 
1. Lépjen a **Virtuálisgép-méretezési csoportokra.**
1. Új méretezési csoport létrehozásához válassza a **+ Add** lehetőséget.
1. Nyissa meg az **Egészség** lapot. 
1. Keresse meg az **Állapot** szakaszt.
1. Engedélyezze az **alkalmazás állapotának figyelése** beállítást.
1. Keresse meg az **Automatikus javítási házirend szakaszt.**
1. **Kapcsolja** be az **Automatikus javítás** beállítást.
1. A **Türelmi időszakban (min)** adja meg a türelmi időszakot percben, az engedélyezett értékek 30 és 90 perc között vannak. 
1. Ha végzett az új méretezési csoport létrehozásával, válassza a **Véleményezés + létrehozás** gombot.

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

A következő példa engedélyezi az automatikus javítási házirendet, miközben új méretezési csoportot hoz létre *[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* használatával. Először hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot, amelynek automatikus javítása házirend türelmi időszak 30 perc.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

A fenti példa egy meglévő terheléselosztót és állapotmintát használ a példányok alkalmazásállapotának figyelésére. Ha inkább egy alkalmazás állapotbővítményt szeretne használni a figyeléshez, létrehozhat egy méretezési csoportot, konfigurálhatja az alkalmazás állapotbővítményét, majd engedélyezheti az automatikus példányjavítási házirendet az *az vmss frissítés*használatával, a következő szakaszban leírtak szerint.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatikus javítási házirend engedélyezése meglévő méretezési csoport frissítésekor

Az automatikus javítási házirend engedélyezése előtt egy meglévő méretezési csoportban győződjön meg arról, hogy a szolgáltatás engedélyezéséhez [vonatkozó összes követelmény](#requirements-for-using-automatic-instance-repairs) teljesül. Az alkalmazásvégpontot megfelelően kell konfigurálni a méretezési csoport példányaihoz, hogy elkerülje a nem kívánt javítások indítását, amíg a végpont konfigurálása van. Az automatikus példányjavítás engedélyezéséhez egy méretezési csoportban használja az *automaticRepairsPolicy* objektumot a virtuálisgép méretezési csoport modelljében.

Egy meglévő méretezési csoport modelljének frissítése után győződjön meg arról, hogy a legújabb modell a méretezési skála összes példányára vonatkozik. Tekintse meg a virtuális gépek naprakészen ítására vonatkozó utasításokat [a legújabb méretezési modellel.](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>Azure Portal

Módosíthatja egy meglévő méretezési csoport automatikus javítási szabályzatát az Azure Portalon keresztül. 
 
1. Lépjen egy meglévő virtuálisgép-méretezési csoportra.
1. A bal oldali menü **Beállítások területén** válassza az Egészség **és javítás**lehetőséget.
1. Engedélyezze az **alkalmazás állapotának figyelése** beállítást.
1. Keresse meg az **Automatikus javítási házirend szakaszt.**
1. **Kapcsolja** be az **Automatikus javítás** beállítást.
1. A **Türelmi időszakban (min)** adja meg a türelmi időszakot percben, az engedélyezett értékek 30 és 90 perc között vannak. 
1. Amikor elkészült, válassza a **Mentés** gombot. 

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbiakban például egy meglévő méretezési csoport automatikus példányjavítási házirendjének frissítésére, az *[vmss frissítés](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* használatával.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Az automatikus példányjavítási házirend szolgáltatásállapotának megtekintése és frissítése

### <a name="rest-api"></a>REST API 

A 2019-12-01-es vagy újabb API-verzióval használja a [Példánynézet begetvétele](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) a virtuálisgép-méretezési csoporthoz a *serviceState* automatikus javításhoz a *vezénylési szolgáltatások*tulajdonság alatt. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Használja *a setOrchestrationServiceState* API-t a 2019-12-01-es vagy újabb API-verzióval egy virtuálisgép-méretezési csoportban az automatikus javítások állapotának beállításához. Miután a méretezési csoport bevan választva az automatikus javítási funkció, ezzel az API-val felfüggesztheti vagy folytathatja az automatikus javításokat a méretezési csoporthoz. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

A [get-instance-view](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) parancsmag használatával megtekintheti a *serviceState* automatikus példányjavítás. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

A [set-orchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) parancsmag használatával frissítse a *serviceState* automatikus példányjavítás. Miután a méretezési csoport bevan választva az automatikus javítási funkció, akkor használhatja ezt a parancsmamot, hogy függessze fel vagy folytassa az automatikus javításokat a méretezési készlet. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Használja [a Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) parancsmag paraméter *InstanceView* az automatikus példányjavítások *ServiceState* megtekintéséhez.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Használja a Set-AzVmssOrchestrationServiceState parancsmag az automatikus példányjavítások *serviceState* frissítéséhez. Miután a méretezési csoport bevan választva az automatikus javítási funkció, használhatja ezt a parancsmamot, hogy függessze fel vagy folytassa az automatikus javítás tméretezési készlet.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Hibaelhárítás

**Az automatikus javítási házirend engedélyezésének elmulasztása**

Ha "BadRequest" hibaüzenet jelenik meg a "Nem található "automaticRepairsPolicy" nevű objektumon a "properties" objektumon, akkor ellenőrizze a virtuális gép méretezési készletéhez használt API-verziót. Ehhez a funkcióhoz a 2018-10-01-es vagy újabb API-verzió szükséges.

**A példány nem javítható akkor sem, ha a házirend engedélyezve van**

A példány lehet türelmi idő. Ez az az idő, amely et a példány állapotváltozása után várni kell a javítások végrehajtása előtt. Ennek célja, hogy elkerüljék a korai vagy véletlen javításokat. A javítási műveletnek akkor kell megtörténnie, amikor a türelmi időszak befejeződött a példányhoz.

**A méretezési csoport példányainak alkalmazásállapotának megtekintése**

A Get [Instance View API-t](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) egy virtuálisgép-méretezési csoport példányai az alkalmazás állapotának megtekintéséhez használhatja. Az Azure PowerShell segítségével használhatja a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsmagot az *-InstanceView* jelzővel. Az alkalmazás állapotának állapota a *vmHealth*tulajdonság alatt található.

Az Azure Portalon is láthatja az állapotállapotát. Lépjen egy meglévő méretezési csoport, válassza **a példányok** a menüben a bal oldalon, és tekintse meg az **állapot** oszlop az egyes méretezési csoport példányok állapotát. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan konfigurálhatja [az Alkalmazásállapot-bővítményt](./virtual-machine-scale-sets-health-extension.md) vagy [a terheléselosztó állapotmintáit](../load-balancer/load-balancer-custom-probe-overview.md) a méretezési készletekhez.
