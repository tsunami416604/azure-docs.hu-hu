---
title: Az Azure PowerShell rendelkező virtuálisgép-méretezési csoportok kezelése |} Microsoft Docs
description: Közös Azure PowerShell-parancsmagok segítségével kezelheti a virtuálisgép-méretezési csoportok, például indítása és leállítása egy példányát, vagy módosítsa a skála kapacitás beállítása.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: 39cd7fa2232329716ba16abf92ba4a5f2cc15487
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652780"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>A virtuálisgép-méretezési beállítása az Azure PowerShell kezelése
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk részletesen néhány gyakori Azure PowerShell-parancsmagok, amelyek lehetővé teszik, hogy ezeket a műveleteket.

Felügyeleti feladatok elvégzéséhez szüksége van a legújabb Azure PowerShell modul. További információ: [Ismerkedés az Azure PowerShell](/powershell/azure/get-started-azureps). Ha egy virtuálisgép-méretezési csoport létrehozásához szükséges, akkor [hozzon létre egy méretezési beállítása az Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>A méretezési adatainak megtekintése
A méretezési általános információ megtekintéséhez használja [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). A következő példa a méretezési készletben elnevezett információ lekérése *myScaleSet* a a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
Méretezési csoportban lévő Virtuálisgép-példány listájának megtekintéséhez használja [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Az alábbi példa felsorolja az összes Virtuálisgép-példány a méretezési készletben elnevezett *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg ezeket a neveket a saját értékeit:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A megadott Virtuálisgép-példány további információkat tekinthet meg, vegye fel a `-InstanceId` paramétert [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) és adjon meg egy példány a megtekintéséhez. Az alábbi példa megtekinti az információ a Virtuálisgép-példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
A fenti parancsok bemutatta, a méretezési és a Virtuálisgép-példányok kapcsolatos információkat. Növeléséhez vagy csökkentéséhez tegye a következőket a méretezési csoportban lévő példányok, módosíthatja a kapacitást. A méretezési készletben automatikusan hoz létre vagy eltávolítja a virtuális gépek szükséges számát, majd konfigurálja a virtuális gépek alkalmazás forgalom fogadására.

Először hozzon létre egy méretezési csoportot a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) paranccsal, majd adja meg az `sku.capacity` új értékét. A kapacitás módosításának alkalmazásához használja az [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) parancsot. Az alábbi példa frissítések *myScaleSet* a a *myResourceGroup* kapacitású erőforráscsoportot *5* példányok. Adja meg a saját értékeit az alábbiak szerint:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a kapacitást és a skála beállítása, a virtuális gépek azonosítók eltávolítása először a legmagasabb példánnyal.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Állítsa le és indítsa el a virtuális gépek méretezési csoportban lévő
A méretezési csoportban lévő egy vagy több virtuális gép leállításához használja a [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példakód leállítja a példányt *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.


### <a name="start-vms-in-a-scale-set"></a>Méretezési csoportban lévő virtuális gépek elindítása
A méretezési csoportban lévő egy vagy több virtuális gép indításához használja a [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép elindításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példában elindul a példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Indítsa újra a virtuális gépek méretezési csoportban lévő
A méretezési csoportban lévő egy vagy több virtuális gép újraindításához használja a [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

Az alábbi példa újraindul példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Távolítsa el a virtuális gépek egy méretezési készlet
Méretezési csoportban lévő egy vagy több virtuális gép eltávolításához használja [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). A `-InstanceId` paraméter lehetővé teszi egy vagy több virtuális gépek eltávolítása. Ha nem ad meg, eltávolításuk Példányazonosítót, a méretezési csoportban lévő összes virtuális gépet. Több virtuális gép eltávolításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példában eltávolítjuk példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját értékeit az alábbiak szerint:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>További lépések
A méretezési készlet kapcsolatos további általános feladatok közé tartozik hogyan [alkalmazás üzembe helyezése](virtual-machine-scale-sets-deploy-app.md), és [Virtuálisgép-példányok frissítéséhez](virtual-machine-scale-sets-upgrade-scale-set.md). Használhatja az Azure PowerShell [automatikus méretezése szabályok konfigurálása](virtual-machine-scale-sets-autoscale-overview.md).
