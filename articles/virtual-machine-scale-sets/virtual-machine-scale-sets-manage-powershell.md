---
title: "Az Azure PowerShell rendelkező virtuálisgép-méretezési csoportok kezelése |} Microsoft Docs"
description: "Közös Azure PowerShell-parancsmagok segítségével kezelheti a virtuálisgép-méretezési csoportok, például indítása és leállítása egy példányát, vagy módosítsa a skála kapacitás beállítása."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 5b5f3eb05f0d6c10f7efe8af1b93b2cb4fc585c5
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>A virtuálisgép-méretezési beállítása az Azure PowerShell kezelése
A virtuálisgép-méretezési csoport életciklusa során szükség lehet egy vagy több felügyeleti feladatok futtatásához. Emellett érdemes lehet különböző életciklus-feladatokat automatizáló parancsfájlokat hozhatnak létre. Ez a cikk részletesen néhány gyakori Azure PowerShell-parancsmagok, amelyek lehetővé teszik, hogy ezeket a műveleteket.

Felügyeleti feladatok elvégzéséhez szüksége van a legújabb Azure PowerShell modul. Hogyan kell telepíteni, és a legújabb verzióját használja a további információkért lásd: [Ismerkedés az Azure PowerShell](/powershell/azure/get-started-azureps). Ha egy virtuálisgép-méretezési csoport létrehozásához szükséges, akkor [hozzon létre egy méretezési beállítása az Azure portálon](virtual-machine-scale-sets-create-portal.md).


## <a name="view-information-about-a-scale-set"></a>A méretezési adatainak megtekintése
A méretezési általános információ megtekintéséhez használja [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). A következő példa a méretezési készletben elnevezett információ lekérése *myScaleSet* a a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Nézet virtuális gépek méretezési csoportban lévő
Méretezési csoportban lévő Virtuálisgép-példány listájának megtekintéséhez használja [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Az alábbi példa listában az összes Virtuálisgép-példány a méretezési készletben elnevezett *myScaleSet* és a *myResourceGroup* erőforráscsoport. Adja meg ezeket a neveket a saját értékeit:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A megadott Virtuálisgép-példány további információkat tekinthet meg, vegye fel a `-InstanceId` paramétert [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) és adjon meg egy példány a megtekintéséhez. Az alábbi példa megtekinti az információ a Virtuálisgép-példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>A kapacitás, a méretezési módosítása
A fenti parancsok bemutatta, a méretezési és a Virtuálisgép-példányok kapcsolatos információkat. Növeléséhez vagy csökkentéséhez tegye a következőket a méretezési csoportban lévő példányok, módosíthatja a kapacitást. A méretezési készletben automatikusan hoz létre vagy eltávolítja a virtuális gépek szükséges számát, majd konfigurálja a virtuális gépek alkalmazás forgalom fogadására.

Először hozzon létre egy méretezési készlet objektum [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), majd adjon meg új értéket `sku.capacity`. A kapacitás módosítás segítségével [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Az alábbi példa frissítések *myScaleSet* a a *myResourceGroup* kapacitású erőforráscsoportot *5* példányok. Adja meg a saját értékeit az alábbiak szerint:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Ha a kapacitás és a skála frissítése néhány percet vesz be. Ha csökkenti a kapacitást és a skála beállítása, a virtuális gépek azonosítók eltávolítása először a legmagasabb példánnyal.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Állítsa le és indítsa el a virtuális gépek méretezési csoportban lévő
Méretezési csoportban lévő egy vagy több virtuális gép leállításához használja [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). A `-InstanceId` paraméter lehetővé teszi egy vagy több virtuális gépek leállítása. Ha nem adja meg a Példányazonosítót, a méretezési csoportban lévő összes virtuális gép le van állítva. Több virtuális gép leállításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példakód leállítja a példányt *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a következőképpen:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Alapértelmezés szerint a leállított virtuális gépek fel van szabadítva, és nem számítunk költségeket. Ha a virtuális gép leállítása kiépített állapotban marad, vegye fel a `-StayProvisioned` az előző parancs paramétert. Leállított virtuális gépek maradó kiosztott fel Önnek a rendszeres költségeket.


### <a name="start-vms-in-a-scale-set"></a>Méretezési csoportban lévő virtuális gépek elindítása
Méretezési csoportban lévő egy vagy több virtuális gép indításához használja [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). A `-InstanceId` paraméter lehetővé teszi egy vagy több virtuális gép elindításához. Ha nem adja meg a Példányazonosítót, a méretezési csoportban lévő összes virtuális gép indulnak el. Több virtuális gép elindításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példában elindul a példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a következőképpen:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Indítsa újra a virtuális gépek méretezési csoportban lévő
Méretezési csoportban lévő egy vagy több virtuális gép újraindításához használja [Retart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). A `-InstanceId` paraméter lehetővé teszi egy vagy több virtuális gépek újraindítására. Ha nem adja meg a Példányazonosítót, a méretezési csoportban lévő összes virtuális gép újraindul. Több virtuális gép újraindításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

Az alábbi példa újraindul példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a következőképpen:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Távolítsa el a virtuális gépek egy méretezési készlet
Méretezési csoportban lévő egy vagy több virtuális gép eltávolításához használja [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). A `-InstanceId` paraméter lehetővé teszi egy vagy több virtuális gépek eltávolítása. Ha nem ad meg, eltávolításuk Példányazonosítót, a méretezési csoportban lévő összes virtuális gépet. Több virtuális gép eltávolításához külön minden példány azonosítója egy vesszővel válassza el egymástól.

A következő példában eltávolítjuk példány *0* a méretezési készletben elnevezett a *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a következőképpen:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>További lépések
A méretezési készlet kapcsolatos további általános feladatok közé tartozik hogyan [alkalmazás üzembe helyezése](virtual-machine-scale-sets-deploy-app.md), és [Virtuálisgép-példányok frissítéséhez](virtual-machine-scale-sets-upgrade-scale-set.md). Használhatja az Azure PowerShell [automatikus méretezése szabályok konfigurálása](virtual-machine-scale-sets-autoscale-overview.md).
