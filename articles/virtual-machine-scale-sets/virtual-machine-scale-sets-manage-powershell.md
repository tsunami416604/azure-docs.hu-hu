---
title: Virtuálisgép-méretezési készletek kezelése az Azure PowerShell segítségével
description: Közös Azure PowerShell-parancsmagok a virtuális gép méretezési készletei kezeléséhez, például egy példány indításához és leállításához, illetve a méretezési csoport kapacitásának módosításához.
author: cynthn
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: cc83c8b73c73b2aa70bc36bad175e5c19c1ab700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275718"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Virtuálisgép-méretezési csoport kezelése az Azure PowerShell használatával

A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk néhány gyakori Azure PowerShell-parancsmag, amely lehetővé teszi, hogy ezeket a feladatokat.

Ha virtuálisgép-méretezési csoportot kell létrehoznia, [létrehozhat egy méretezési csoportot az Azure PowerShell használatával.](quick-create-powershell.md)

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Méretezési csoport adatainak megtekintése
A méretezési csoport általános információinak megtekintéséhez használja a [Get-AzVmss (Get-AzVmss ) segítségével.](/powershell/module/az.compute/get-azvmss) A következő példa a myResourceGroup erőforráscsoport *myResourceGroup* erőforráscsoportban található *myScaleSet* nevű méretezési csoportadatait kapja meg. Írja be saját nevét az alábbiak szerint:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A méretezési csoportban lévő virtuálisgép-példányok listájának megtekintéséhez használja a [Get-AzVmssVM-et.](/powershell/module/az.compute/get-azvmssvm) A következő példa felsorolja a *myScaleSet* nevű méretezési csoport és a *myResourceGroup* erőforráscsoport összes virtuálisgép-példányát. Adja meg saját értékeit a következő nevekhez:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Egy adott virtuálisgép-példánytovábbi adatainak `-InstanceId` megtekintéséhez adja hozzá a paramétert a [Get-AzVmssVM-hez,](/powershell/module/az.compute/get-azvmssvm) és adjon meg egy megtekinthető példányt. A következő példa a 0 *0* virtuálisgép-példány ra vonatkozó információkat tekinti meg a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Írja be saját nevét az alábbiak szerint:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsok a méretezési csoportés a virtuálisgép-példányok adatait mutatták. A méretezési csoport példányainak számának növeléséhez vagy csökkentéséhez módosíthatja a kapacitást. A méretezési készlet automatikusan létrehozza vagy eltávolítja a szükséges számú virtuális gépet, majd konfigurálja a virtuális gépeket az alkalmazásforgalom fogadására.

Először hozzon létre egy méretezési csoport objektumot [a Get-AzVmss](/powershell/module/az.compute/get-azvmss)segítségével, majd adjon meg egy új értéket a számára. `sku.capacity` A kapacitásmódosítás alkalmazásához használja az [Update-AzVmss szolgáltatást.](/powershell/module/az.compute/update-azvmss) A következő példa frissíti a *myScaleSet* a *myResourceGroup* erőforráscsoportban *5* példány kapacitására. Adja meg saját értékeit az alábbiak szerint:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását, a legmagasabb példányazonosítóval rendelkező virtuális gépek et először eltávolítja.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Virtuális gépek leállítása és elindítása egy méretezési csoportban
A méretezési halmaz egy vagy több virtuális gépének leállításához használja a [Stop-AzVmss .to stop-azvmss](/powershell/module/az.compute/stop-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához válassza el az egyes példányazonosítókat vesszővel.

A következő példa leállítja a *0* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.


### <a name="start-vms-in-a-scale-set"></a>Virtuális gépek indítása méretezési készletben
Egy vagy több virtuális gép méretezési készletben történő indításához használja a [Start-AzVmss .use start-AzVmss](/powershell/module/az.compute/start-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához válassza el az egyes példányazonosítókat vesszővel.

A következő példa elindítja a *0-as* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Virtuális gépek újraindítása egy méretezési csoportban
A méretezési készlet egy vagy több virtuális gépének újraindításához használja [a Restart-AzVmss kapcsolót.](/powershell/module/az.compute/restart-azvmss) Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához válassza el az egyes példányazonosítókat vesszővel.

A következő példa *0* újraindítja a 0.példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuális gépek eltávolítása méretezési készletből
A méretezési készlet egy vagy több virtuális gépének eltávolításához használja [az Eltávolítás-AzVmss parancsot.](/powershell/module/az.compute/remove-azvmss) A `-InstanceId` paraméter lehetővé teszi, hogy egy vagy több virtuális gépet távolítson el. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép törlődik. Több virtuális gép eltávolításához válassza el az egyes példányazonosítókat vesszővel.

A következő példa eltávolítja a *0-as* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportból. Adja meg saját értékeit az alábbiak szerint:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>További lépések
A méretezési csoportok egyéb gyakori feladatai közé tartozik [az alkalmazás telepítése](virtual-machine-scale-sets-deploy-app.md)és a [virtuálisgép-példányok frissítése.](virtual-machine-scale-sets-upgrade-scale-set.md) Az Azure PowerShell használatával [is konfigurálhatja az automatikus méretezési szabályokat.](virtual-machine-scale-sets-autoscale-overview.md)
