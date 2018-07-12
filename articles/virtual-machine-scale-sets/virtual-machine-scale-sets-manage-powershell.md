---
title: Kezelheti az Azure PowerShell használatával a Virtual Machine Scale Sets |} A Microsoft Docs
description: Közös kezelése a Virtual Machine Scale Sets, például a következőkkel elindíthatja és leállíthatja a példány, vagy módosítsa a méretezési csoport Azure PowerShell-parancsmagok kapacitás beállítása.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: a300e2f2febab8436f8d52b71955b3614bd10605
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38707442"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Az Azure PowerShell használatával egy virtuálisgép-méretezési csoport kezelése
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk részletesen néhány gyakori Azure PowerShell-parancsmagok, amelyekkel ezeket a feladatokat.

Felügyeleti feladatok elvégzéséhez szüksége van a legújabb Azure PowerShell-modul. További információ: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps). Ha szeretne létrehozni egy virtuálisgép-méretezési csoportot, akkor az [hozzon létre egy méretezési csoportot az Azure PowerShell-lel](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Egy méretezési csoportot adatainak megtekintése
Egy méretezési csoportot vonatkozó általános információk megtekintéséhez használja [a Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Az alábbi példa lekéri a nevű méretezési információ *myScaleSet* a a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
Egy méretezési csoportban lévő Virtuálisgép-példányok listájának megtekintéséhez használja [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Az alábbi példa felsorolja az összes Virtuálisgép-példányt a méretezési elnevezett *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg ezeket a neveket a saját értékeit:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Egy adott Virtuálisgép-példánnyal kapcsolatos további információk megtekintéséhez adja hozzá a `-InstanceId` paramétert [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) és a egy példány megtekintéséhez adja meg. Az alábbi példa megjeleníti a Virtuálisgép-példánnyal kapcsolatos információk *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsokban bemutatta a méretezési csoport és a Virtuálisgép-példányok kapcsolatos információkat. Növelheti vagy csökkentheti a méretezési csoportban lévő példányok számát, módosíthatja a kapacitást. A méretezési csoport automatikusan hoz létre vagy eltávolítja a szükséges virtuális gépek száma, majd konfigurálja a virtuális gépek alkalmazás forgalom fogadására.

Először hozzon létre egy méretezési csoportot a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) paranccsal, majd adja meg az `sku.capacity` új értékét. A kapacitás módosításának alkalmazásához használja az [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) parancsot. Az alábbi példa frissítések *myScaleSet* a a *myResourceGroup* kapacitását erőforráscsoport *5* példányok. A saját értékeit adja meg a következőképpen:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását beállítása, a virtuális gépek azonosítók rendszer távolítja el először a legmagasabb példánnyal.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Állítsa le, és a egy méretezési csoportban lévő virtuális gépek indítása
A méretezési csoportban lévő egy vagy több virtuális gép leállításához használja a [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához külön mindegyik Példányazonosítója vesszővel válasszon el.

A következő példa leállítja a példányt *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.


### <a name="start-vms-in-a-scale-set"></a>Egy méretezési csoportban lévő virtuális gépek indítása
A méretezési csoportban lévő egy vagy több virtuális gép indításához használja a [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához külön mindegyik Példányazonosítója vesszővel válasszon el.

A következő példa elindítja a példány *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Egy méretezési csoportban lévő virtuális gépek újraindítása
A méretezési csoportban lévő egy vagy több virtuális gép újraindításához használja a [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához külön mindegyik Példányazonosítója vesszővel válasszon el.

A következő példa újraindítja a példányt *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Távolítsa el a virtuális gépek méretezési csoportból
Egy méretezési csoportban lévő egy vagy több virtuális gép eltávolításához használja [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). A `-InstanceId` paraméter lehetővé teszi, hogy adjon meg egy vagy több virtuális gépet eltávolítja. Ha nem ad meg Példányazonosítót, a rendszer eltávolítja a méretezési csoportban lévő összes virtuális gép. Több virtuális gép eltávolításához külön mindegyik Példányazonosítója vesszővel válasszon el.

A következő példa eltávolítja az előfordulást *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>További lépések
A méretezési csoportokkal kapcsolatos egyéb gyakori feladatok közé tartozik annak [alkalmazás üzembe helyezése](virtual-machine-scale-sets-deploy-app.md), és [Virtuálisgép-példányok frissítése](virtual-machine-scale-sets-upgrade-scale-set.md). Használhatja az Azure PowerShell használatával is [konfigurálása az automatikus skálázási szabályok](virtual-machine-scale-sets-autoscale-overview.md).
