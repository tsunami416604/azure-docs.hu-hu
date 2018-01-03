---
title: "Egy Azure virtuálisgép-méretezési csoport frissítése |} Microsoft Docs"
description: "Egy Azure virtuálisgép-méretezési csoport frissítése"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: gunegatybo
ms.openlocfilehash: fbdc9d40173a40f35eee60cadfdd258293509d53
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport frissítése
Ez a cikk ismerteti, hogyan lehet megkezdik az operációs rendszer frissítése egy Azure virtuális gép méretezése állásidő nélkül. Ebben a környezetben az operációs rendszer frissítés magában foglalja a módosítása az operációs rendszer Termékváltozata vagy a verziójával, vagy az egyéni lemezkép URI-Azonosítóinak módosítása. Erre a frissítése nélkül állásidő azt jelenti, hogy frissítési egyszerre helyett virtuális gépet egyszerre, vagy a csoportok (például egy tartalék tartomány egyszerre). Ezzel a módszerrel bármely nem frissített virtuális gépek adatközpontnak futnia.

Félreérthetőség elkerülése érdekében most különbözteti meg a négy típusú lehet, hogy szeretne végezni az operációs rendszer frissítése:

* A verzió vagy platformlemezkép Termékváltozata módosítása. Például megváltoztathatja az Ubuntu 14.04.2-LTS verzióra a 14.04.201506100 14.04.201507060, vagy váltás Ubuntu 15.10/legújabb SKU 16.04.0-LTS/latest. Ebben a forgatókönyvben cikkben szereplő.
* Az egyéni lemezkép új verziójának mutató URI-Azonosítóinak módosítása a beépített (**tulajdonságok** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **kép** > **uri**). Ebben a forgatókönyvben cikkben szereplő.
* A méretezési Azure felügyelt lemezek használatával létrehozott lemezképet hivatkozás módosítása.
* Javítás az operációs rendszer a virtuális gépen (ezt például a biztonsági javítás telepítése és a Windows Update futtatása). Ez a forgatókönyv használata támogatott, de nem tartalmazza az ebben a cikkben.

Virtuálisgép-méretezési csoportok részeként üzembe helyezett egy [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) fürthöz nem tartoznak ide. További információ a Service Fabric javítás: [javítás Windows operációs rendszer a Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-patch-orchestration-application)

Az alapvető sorrend módosítása az operációs rendszer verziója/Termékváltozata platformlemezkép vagy egy egyéni lemezkép URI a következőképpen néz ki:

1. A virtuális gép méretezési modellel beolvasása.
2. Módosítsa a verzió, SKU, Képhivatkozás vagy URI azonosítóját a modellben.
3. A modell frissítése.
4. Hajtsa végre a *manualUpgrade* hívja a méretezési csoportban lévő virtuális gépeken. Ez a lépés csak akkor releváns Ha *upgradePolicy* értéke **manuális** a méretezési készletben. Ha az érték **automatikus**, a virtuális gépek frissítik egyszerre, így üzemkiesése.

Vegye figyelembe az információ nézzük meg, hogyan sikerült frissíteni a méretezési készletben a PowerShell és a REST API használatával verzióját. Ezek a példák kiterjedjen platformlemezkép, de ez a cikk igazítja a folyamat egy egyéni kép is elegendő információt biztosít.

## <a name="powershell"></a>PowerShell
Ez a példa frissíti a Windows virtuálisgép-méretezési beállítása (az új verzióra 4.0.20160229 létrehozása. A modell frissítése, után végez el egy frissítés egy virtuálisgép-példány egyszerre.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

A platform lemezkép verziója módosítása helyett egyéni lemezkép URI-JÁNAK frissítésekor, cserélje le a "az új verzió beállítása" sor frissíti a forrás lemezkép URI parancsot. Például ha a méretezési Azure felügyelt lemezek használata nélkül hozták létre, a frissítés akkor néznek ki:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Ha egyéni lemezkép-alapú méretezési készült Azure felügyelt lemezt használ, a Képhivatkozás lesz frissítve. Példa:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>A REST API
Az alábbiakban néhány Python példák, amelyek az operációs rendszer verziója frissítés megkezdik az Azure REST API használatával. Mindkettő használja, a lightweight [azurerm](https://pypi.python.org/pypi/azurerm) Azure REST API burkoló függvényt a skálán GET könyvtárának beállítása modell egy frissített modellel PUT követ. Akkor is tekintse meg a virtuális gép példányok nézetek frissítési tartomány a virtuális gépek azonosításához.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) egy Python-parancsfájl, amellyel egy futó virtuális gép méretezése egy operációs rendszer frissítése megkezdik a frissítési tartományok egyszerre beállítani.

![A virtuális gépek vagy egy frissítési tartományt Vmssupgrade parancsfájl](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Ez a parancsfájl lehetővé teszi, hogy válassza ki az adott virtuális gépek frissítéséhez, vagy adjon meg egy frissítési tartományt. A platform kép verzió módosítása vagy az egyéni lemezkép URI-Azonosítóinak módosítása támogatja.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) van egy általános célú szerkesztő virtuálisgép-méretezési készlet, amely tartalmazza a virtuális gép állapotát a heatmap, ahol több sorban is jelenti a frissítési tartományok. Többek között a méretezési csoportban a modell frissítése egy új verziója, SKU vagy egyéni lemezkép URI, és ezután válassza ki a tartalék tartományok frissítése. Ha így tesz, a frissítési tartomány összes virtuális gépet az új modell frissítése. Másik lehetőségként a működés közbeni frissítés az Ön által választott a Köteg mérete alapján végezhet.  

Az alábbi képernyőfelvételen látható terjedő skálán Ubuntu 14.04-2LTS 14.04.201507060 verzió beállítása mintáját. Számos további lehetőség az eszköz ezen a képernyőfelvételen látható replikációt óta bővült.

![A skála Ubuntu 14.04-2LTS beállítása Vmsseditor modell](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Miután rákattintott **frissítése** , majd **részletek beszerzése**, virtuális gépek UD 0 frissítéséhez indítsa el.

![Vmsseditor ábrázoló frissítése folyamatban](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

