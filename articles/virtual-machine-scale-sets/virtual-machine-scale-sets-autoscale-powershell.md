---
title: "Automatikus skálázási virtuálisgép-méretezési beállítja az Azure PowerShell |} Microsoft Docs"
description: "Az Azure PowerShell beállítja a virtuálisgép-méretezési automatikus skálázási szabályok létrehozása"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1fbfbbc79a415af5e874c304412854849e134eb7
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>A virtuálisgép-méretezési beállítása az Azure PowerShell automatikus méretezése
A méretezési csoport létrehozásakor megadhatja a futtatni kívánt Virtuálisgép-példányok száma. Az alkalmazás igény szerinti változásával automatikusan növeli vagy csökkenti a Virtuálisgép-példányok számát. Automatikus skálázás teszi lehetővé teszi keresletének tartani, vagy az alkalmazás életciklusa során alkalmazás teljesítmény változásait.

Ez a cikk bemutatja, hogyan automatikus skálázási szabályok létrehozását az Azure PowerShell, a méretezési csoportban lévő Virtuálisgép-példányok teljesítményének figyeléséhez. Ezek a szabályok automatikus skálázás növelje, vagy csökkentse a metrikák válaszul Virtuálisgép-példányok száma. Is hajthatja végre ezeket a lépéseket a [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) vagy a [Azure-portálon](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Előfeltételek
Automatikus skálázási szabályok létrehozásához szükséges egy meglévő virtuális gép méretezési készlet. Létrehozhat egy méretezési állítható be a [Azure-portálon](virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell), vagy [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli).

Könnyebb az automatikus skálázási szabályok létrehozása, adja meg a bizonyos változókat a méretezési készlet. Az alábbi példa meghatározza a méretezési készletben elnevezett változói *myScaleSet* az erőforráscsoport neve *myResourceGroup* és a *USA keleti régiója* régióban. Az előfizetés-azonosítója nem kapunk [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Ha a fiókjához társított több előfizetéssel rendelkezik, csak az első előfizetés ad vissza. A nevek és előfizetés-azonosító beállítása a következőképpen:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Hozzon létre egy szabályt, amely automatikusan horizontális felskálázása
Az alkalmazás igény szerinti egyenes arányban növekszik, ha a Virtuálisgép-példány a skála terhelését növeli állítsa be. Ha ez a megnövekedett terhelés egységes, ahelyett, hogy csak egy rövid igény szerinti, konfigurálhatja az automatikus skálázási szabályok a méretezési csoportban lévő Virtuálisgép-példányok számának növeléséhez. Ezek a Virtuálisgép-példányok jönnek létre, és az alkalmazások vannak telepítve, a méretezési hozzákezd terjesztése azokra a terheléselosztó forgalmát. Milyen metrikák figyeléséhez, mint a CPU vagy lemez, mennyi ideig alkalmazásterhelés meg kell felelnie a megadott küszöbértéket, és hány Virtuálisgép-példányok a skála hozzáadása set szabályozhatja.

Hozzon létre egy szabály [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) , amely növeli a terjedő skálán állítható be, ha a átlagos CPU-terhelést érték nagyobb, mint 70 % egy 5 perces időszakon át a Virtuálisgép-példányok számát. A szabály akkor váltja ki, ha Virtuálisgép-példányok száma 20 %-kal növekszik. A méretezési készlet egy Virtuálisgép-példányok kis mennyiségű, hagyja sikerült `-ScaleActionScaleType` és csak az egyiket meg `-ScaleActionValue` növelése érdekében azáltal *1* vagy *2* példányok. Virtuálisgép-példányok számos, a 10 % vagy 20 %-os növekedést méretezési készlet Virtuálisgép-példányok lehet jobban megfelelő.

A következő paramétert kell használni ehhez a szabályhoz:

| Paraméter               | Magyarázat                                                                                                         | Érték          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | A teljesítmény metrika figyelésére és a skála alkalmazni az alábbi műveletek beállítani.                                                   | Processzor-százalékos aránya |
| *-Időkeretben vannak*            | Milyen gyakran a metrikák gyűjtése történt elemzéséhez.                                                                   | 1 perc       |
| *-MetricStatistic*      | Határozza meg, hogy az összegyűjtött metrikák elemzések céljából összesíti kell-e.                                                | Átlagos        |
| *Az időtartomány értékének-*           | Az időtartam a metrika és a küszöbértéket az összehasonlítás előtt figyeli.                                   | 10 perc      |
| *-Operátor*             | Hasonlítsa össze a metrikaadatokat szemben a küszöbérték operátor.                                                     | Nagyobb, mint   |
| *-Küszöbérték*            | Az érték, amely az automatikus skálázási szabály elindítani egy műveletet okoz.                                                      | 70%            |
| *-ScaleActionDirection* | Meghatározza, hogy a méretezési kell méretezni felfelé vagy lefelé, amikor a szabály vonatkozik.                                             | Növelése       |
| *– ScaleActionScaleType* | Azt jelzi, hogy a Virtuálisgép-példányok száma a által százalékát módosítani kell-e.                                 | Készültségi módosítása |
| *-ScaleActionValue*     | Virtuálisgép-példányok hány százalékát módosítani kell, amikor a szabály gondoskodik.                                            | 20             |
| *-ScaleActionCooldown*  | Mennyi ideig várjon a szabály alkalmazza újra, így az automatikus skálázási műveletek érvénybe lépéséhez idő kell. | 5 perc      |

Az alábbi példakód létrehozza nevű objektum *myRuleScaleOut* , amely tartalmazza a skála szabályt. A *- MetricResourceId* korábban már meg van adva az előfizetés-azonosító, erőforráscsoport-név és skálázási nevének beállítása a változókat használ:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Hozzon létre egy szabályt, amely az automatikus méretezése
Egy este vagy hétvégi az alkalmazás igény szerinti csökkenhet. Ha egy meghatározott időtartamra vonatkozóan ez csökkentheti a betöltés egységes, konfigurálhatja az automatikus skálázási szabályok segítségével csökkentheti a méretezési csoportban lévő Virtuálisgép-példányok számát. A skálázási művelet csökkenti a költségeket a méretezési készletben a futtatásakor csak az aktuális igény kielégítésére szükséges példányok futtatásához.

Hozzon létre egy szabályt a [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) , csökken a Virtuálisgép-példány terjedő skálán állítható be, ha a átlagos CPU-terhelést, majd alá süllyed 30 % 10 perc alatt. A szabály akkor váltja ki, ha a Virtuálisgép-példányok száma 20 %-kal csökken. Az alábbi példakód létrehozza nevű objektum *myRuleScaleDown* , amely tartalmazza a skála szabályt. A *- MetricResourceId* korábban már meg van adva az előfizetés-azonosító, erőforráscsoport-név és skálázási nevének beállítása a változókat használ:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profilok
Az automatikus skálázás szabályokat társíthat egy méretezési, hozzon létre egy profilt. Az automatikus skálázási profil határozza meg az alapértelmezett, a minimális és maximális méretezési készlet kapacitását, és az automatikus skálázási szabályok társítja. Az automatikus skálázási profil létrehozása [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). Az alábbi példa beállítja az alapértelmezett, és minimális, kapacitásának *2* VM példányok, és legfeljebb *10*. A bővített kapacitású és a skála az előző lépésben létrehozott szabálykészlet, majd csatolja:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>A méretezési automatikus skálázási szabályok alkalmazása
Az utolsó lépés, hogy az automatikus skálázási profil alkalmazása a méretezési készlet. A skála elindulhatott automatikus méretezése a bejövő vagy kimenő az alkalmazás igény szerint. Az automatikus skálázási profil alkalmazása [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) az alábbiak szerint:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Méretezési csoportban lévő példányok száma figyelése
A szám és a Virtuálisgép-példányok állapotának megtekintéséhez példányok listájának megtekintéséhez állítsa a méretezési [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). Az állapot azt jelzi, ha a Virtuálisgép-példány a méretezési készletben automatikusan méretezi a kimenő, vagy van megszüntetés, mint a skála automatikusan méretezi a kiépítés-e. Az alábbi példa megtekinti a virtuális gép példány állapotát a méretezési készletben elnevezett *myScaleSet* az erőforráscsoport neve *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Ütemezett automatikus skálázás
Az előző példákban automatikusan átméretezi a bejövő vagy kimenő meg például a CPU-használat alapvető gazdagép metrikák méretezési. Ütemezés alapján automatikus skálázás szabályokat is létrehozhat. Ütemezésalapú szabályok lehetővé teszik automatikusan horizontális felskálázás előre várható növekedése alkalmazás igény szerinti core időpontokat a munkaidőhöz, például Virtuálisgép-példányok száma, és majd automatikus méretezése a példányok száma, amelyek várhatóan kisebb egyszerre igény szerint, például a hétvégi.

A gazdagép helyett ütemezés mérőszámok alapján automatikus skálázási szabályok létrehozásához használja az Azure-portálon. Ütemezésalapú szabályok jelenleg nem hozhatók létre az Azure PowerShell.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható az automatikus skálázási szabályok horizontálisan méretezhető és növeléséhez vagy csökkentéséhez a *szám* állítsa be a skálázási Virtuálisgép-példánya. Is méretezheti függőleges növelheti vagy csökkentheti a Virtuálisgép-példány *mérete*. További információkért lásd: [virtuálisgép-méretezési csoportok függőleges skálázva](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuálisgép-méretezési beállítja az Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Az automatikus skálázási szabályok eseményindító riasztást generáljon, lásd: [automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Emellett [használata naplókat küldeni e-mailek és a webhook riasztási értesítések az Azure-figyelő](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
