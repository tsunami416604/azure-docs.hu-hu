---
title: Használja a hibaelhárító virtuális Géphez az Azure PowerShell használatával Windows |} A Microsoft Docs
description: Ismerje meg, az Azure-beli Windows virtuális gép hibáinak elhárítása a az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure PowerShell-lel való összekapcsolásával
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 1e87704e7d8cf3c7cc21e537d36f95a97265061b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903516"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Hibaelhárítás Windows virtuális gépek az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure PowerShell használatával történő csatlakoztatásával
Ha a Windows virtuális gép (VM) az Azure-ban egy indítási vagy hibát észlel, szükség lehet a hibaelhárítási lépések végrehajtásához a virtuális merevlemezen magát. Ilyenek például egy sikertelen alkalmazásfrissítés megakadályozza, hogy a virtuális gép képes arra, hogy sikeresen lenne. Ez a cikk részletesen bemutatja a virtuális merevlemezt egy másik Windows virtuális Géphez való csatlakozáshoz kijavítani a hibákat, majd hozza létre újból az eredeti virtuális gép az Azure PowerShell használatával.


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémái, így a virtuális merevlemezek.
2. Csatolja, és a egy másik Windows virtuális géphez a virtuális merevlemez csatlakoztatása hibaelhárítás céljából.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. A fájlok szerkesztésével, vagy minden olyan eszközök futtatásával javítsa problémák az eredeti virtuális merevlemezen.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális Gépet az eredeti virtuális merevlemez használatával.

A virtuális gép felügyelt lemez, amely használja, lásd: [egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Győződjön meg arról, hogy rendelkezik-e [az Azure PowerShell legújabb verzióját](/powershell/azure/overview) telepítve, és naplózza előfizetéséhez:

```powershell
Connect-AzureRmAccount
```

A következő példákban cserélje le a saját értékeire paraméterek nevei. Példa a paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.


## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Megtekintheti a virtuális gép Képernyőkép az Azure-beli rendszerindítási problémáinak hibaelhárításához. Ezen a képernyőfelvételen látható segítségével azonosítható, hogy miért a virtuális gép rendszerindító sikertelen lesz. Az alábbi példa lekéri a képernyőképen nevű Windows virtuális gépről `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Tekintse át a képernyőképen meghatározni, miért a virtuális gép rendszerindító sikertelen. Megjegyzés: minden olyan konkrét hibaüzeneteket vagy megadott hibakódok.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez csatlakoztathat egy másik virtuális Géphez, mielőtt a virtuális merevlemez (VHD) nevére azonosítania kell.

Az alábbi példa lekéri az információkat a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Keressen `Vhd URI` belül a `StorageProfile` az előző parancs kimenetéből szakaszában. Az alábbi kimeneti példa csonkolja a `Vhd URI` a kódblokk vége felé:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. Virtuális merevlemez, az operációs rendszer magát, alkalmazások és konfigurációk tárolására. A virtuális gépre a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC) hivatkozik. Minden egyes virtuális merevlemezhez egy bérletet, amikor egy virtuális Géphez csatolva van. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is az operációsrendszer-lemez társíthat egy virtuális Gépet, akkor is, ha a virtuális gép leállított vagy felszabadított állapotban van.

Az első lépés a virtuális gép helyreállításához, ami törli magát a VM-erőforrás. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez a hibák elhárítására.

Az alábbi példával törölhet nevű virtuális Gépet `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Várjon, amíg a virtuális gép törlése a virtuális merevlemezt egy másik virtuális géphez csatolása előtt befejeződött. A bérlet társítja azt a virtuális gép a virtuális merevlemezen kell megjelenése előtt a virtuális merevlemezt egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez csatlakoztatása egy másik virtuális géphez
A következő néhány lépést, egy másik virtuális Géphez hibaelhárítás céljából használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárító virtuális Géphez, és szerkessze a lemez tartalma. Ez a folyamat lehetővé teszi, hogy javítsa az esetleges konfigurációs hibákat, vagy további alkalmazás vagy a rendszer naplófájljait, például tekintse át. Válassza ki, vagy hozzon létre egy másik virtuális Géphez hibaelhárítás céljából használja.

Ha a meglévő virtuális merevlemezt csatlakoztat, adja meg a lemezt az előző beszerzett URL-címe `Get-AzureRmVM` parancsot. Az alábbi példa csatlakoztat egy meglévő virtuális merevlemezt a hibaelhárító virtuális géphez nevű `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Lemez hozzáadása megköveteli, hogy adja meg a lemez méretét. Ahogy azt egy meglévő lemez csatolása a `-DiskSizeInGB` megadott `$null`. Ez az érték biztosítja, hogy az adatlemez megfelelően van csatolva, és nem szükséges valódi adatlemez mérete határozza meg.


## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott lemez csatlakoztatása

1. RDP-vel a hibaelhárító virtuális Géphez a megfelelő hitelesítő adatokkal. Az alábbi példa a nevű virtuális gép RDP-kapcsolat fájljának letöltése `myVMRecovery` az erőforráscsoport neve `myResourceGroup`, és letölti azt `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Az adatlemez automatikusan észlelte és csatolva. A meghajtó betűjeléhez meghatározni a következő csatolt kötetek listájának megtekintése:

    ```powershell
    Get-Disk
    ```

    Az alábbi példa kimenetében látható, hogy a csatlakoztatott lemezt a virtuális merevlemez **2**. (Is `Get-Volume` meghajtóbetűjelet megtekintése):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Hárítsa el a problémákat az eredeti virtuális merevlemez
A meglévő virtuális merevlemezzel csatlakoztatva van most már elvégezheti karbantartási és hibaelhárítási lépések, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le, és az eredeti virtuális merevlemez leválasztása
Miután a hibák megoldódnak, válassza le, és le a meglévő virtuális merevlemezt a hibaelhárító virtuális gépről. Nem használhat a virtuális merevlemez más virtuális gép mindaddig, amíg a virtuális merevlemez csatlakoztatása a hibaelhárító virtuális géphez bérlet.

1. A belül az RDP-munkamenet leválasztása az adatlemezt a helyreállítási virtuális gép. A lemez számát az előző kell `Get-Disk` parancsmagot. Ezután használja `Set-Disk` a lemez offline állapotúként beállításához:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Ellenőrizze a lemez most már van beállítva a kapcsolat nélküli használatával `Get-Disk` újra. Az alábbi példa kimenetében látható, a lemez most már offline állapotúként van beállítva:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Lépjen ki az RDP-munkamenetet. Az Azure PowerShell-munkamenetben távolítsa el a virtuális merevlemezt a hibaelhárító virtuális gépről.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezről
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). A tényleges JSON-sablon jelenleg a következő hivatkozásra:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

A sablon egy meglévő virtuális hálózatban, a korábbi paranccsal a virtuális merevlemez URL-cím használatával helyez üzembe egy virtuális Gépet. A következő példa telepíti a sablont elnevezésű erőforráscsoportot `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

A kérések megválaszolásával a sablon például a virtuális gép neve, az operációs rendszer típusa és a Virtuálisgép-méretet. A `osDiskVhdUri` van azonos az előzőleg használt csatolása a meglévő virtuális merevlemezt a hibaelhárító virtuális géphez.


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika

Amikor a meglévő virtuális merevlemezről hoz létre a virtuális gép, a rendszerindítási diagnosztika lehetséges, hogy nem automatikusan engedélyezni. A következő példában engedélyezzük a diagnosztikai bővítmény nevű virtuális gépre `myVMDeployed` az erőforráscsoport neve `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását
1. Az érintett felügyelt lemez Windows virtuális gép leállításához.
2. [Hozzon létre egy felügyelt lemez pillanatképének](snapshot-copy-managed-disk.md) felügyelt lemezt a virtuális gép operációsrendszer-lemezről.
3. [Felügyelt lemez létrehozása pillanatképből a](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [A felügyelt lemez csatolása a virtuális gép adatlemezként](attach-disk-ps.md).
5. [Módosítsa az adatlemezt a 4. lépéssel operációsrendszer-lemez](os-disk-swap.md).

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása RDP-kapcsolatai egy Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [a Windows virtuális gép alkalmazások csatlakozási hibáinak elhárítása](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
