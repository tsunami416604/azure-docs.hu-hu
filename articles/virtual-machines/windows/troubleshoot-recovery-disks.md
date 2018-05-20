---
title: Használja a virtuális gép az Azure PowerShell hibaelhárítás Windows |} Microsoft Docs
description: Megtudhatja, hogyan elhárítása Windows virtuális gép az Azure-ban az operációs rendszer lemezének csatlakozva egy helyreállítási virtuális gép Azure PowerShell használatával
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
ms.openlocfilehash: 408429d0f8697b8b807e386dbcf2eade29938249
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>A Windows virtuális gépek hibaelhárításáról az operációsrendszer-lemez csatolása a helyreállítási virtuális gép Azure PowerShell használatával
Ha a Windows rendszerű virtuális gép (VM) az Azure-ban rendszerindító vagy a lemez hibát tapasztal, szükség lehet végezze el a virtuális merevlemez hibaelhárítási lépéseket. Ilyenek például a sikertelen frissítés, amely megakadályozza a virtuális gép sikeresen rendszerindító lenne. Ez a cikk részletezi az Azure PowerShell használata a virtuális merevlemez egy másik Windows virtuális gépre, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép csatlakozni.


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémák észlelése, a virtuális merevlemezek tartása.
2. Csatolja, és csatlakoztassa a virtuális merevlemez egy másik Windows VM hibaelhárítási célból.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkesztheti a fájlokat, vagy futtassa a problémák megoldásával kapcsolatban az eredeti virtuális merevlemez olyan eszközöket.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Az eredeti virtuális merevlemez virtuális gép létrehozása.

A virtuális gép számára, amely felügyelt lemezt használ, tekintse meg [hibaelhárításáról egy kezelt lemez virtuális Gépet egy új operációsrendszer-lemez csatolása](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Győződjön meg arról, hogy rendelkezik [a legújabb Azure PowerShell](/powershell/azure/overview) telepítve, és az előfizetés jelentkezett be:

```powershell
Connect-AzureRmAccount
```

A következő példákban cserélje le a saját értékeit paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.


## <a name="determine-boot-issues"></a>Határozza meg a rendszerindítási problémák
Megtekintheti egy Képernyőkép a virtuális Gépet a rendszerindítási problémák elhárítása érdekében az Azure-ban. Ezen a képernyőfelvételen látható segítségével azonosíthatók, ezért a virtuális gép rendszerindító sikertelen lesz. Az alábbi példa lekérdezi a képernyőfelvételen a Windows nevű virtuális gépről `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Tekintse át a képernyőkép meghatározni, miért sikertelen a virtuális gép rendszerindító. Megjegyzés: a vonatkozó hibaüzeneteket vagy a megadott hibakódok.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez egy másik virtuális gép csatolhat, mielőtt kell nevét, a virtuális merevlemez (VHD).

Az alábbi példa nevű virtuális gép adatainak beolvasása `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Keressen `Vhd URI` belül a `StorageProfile` az előző parancs kimenetében szakasza. A következő példa kimenet látható csonkolja a `Vhd URI` a kódblokk vége felé:

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
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemez, az operációs rendszert illeti, alkalmazások és konfigurációk tárolására. A virtuális gép magát a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és hivatkozik arra az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC). Minden virtuális merevlemez létrehozásakor kell a virtuális Géphez csatlakozik, a címbérlet rendelkezik. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is fennáll, az operációs rendszer lemezének társítandó egy virtuális Gépet, akkor is, ha ezt a virtuális Gépet felszabadított és leállított állapotban van.

Az első lépés a virtuális gép helyreállításához, hogy törli a virtuális gép erőforrásához magát. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez, és javítsa ki a hibákat.

A következő példa törli a virtuális gép nevű `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Várjon, amíg a virtuális gép törlése a virtuális merevlemez egy másik virtuális géphez csatolása előtt befejeződött. A virtuális merevlemezen, amely a virtuális Gépet társít a címbérlet kell helyezni, előtt a virtuális merevlemez egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez egy másik virtuális géphez csatolása
A következő néhány lépést, a másik virtuális gép a hibaelhárításhoz használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárítási virtuális géppel, keresse meg és a lemez tartalmának szerkesztéséhez. Ez a folyamat teszi javíthatja az esetleges konfigurációs hibákat, vagy tekintse át például további alkalmazás vagy a rendszer naplófájljait. Válassza ki vagy hozzon létre egy másik virtuális Gépet a hibaelhárításhoz használja.

A meglévő virtuális merevlemez csatolása, adja meg a lemezt az előző beszerzett URL-címe `Get-AzureRmVM` parancsot. Az alábbi példa csatolja a létező virtuális merevlemezt a hibaelhárítási nevű virtuális gép `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Lemez hozzáadása megköveteli, hogy a lemez méretét adja meg. Mivel jelenleg egy meglévő lemez csatolása a `-DiskSizeInGB` megadott `$null`. Ez az érték biztosítja az adatok lemez megfelelően csatlakoztatva van, és nem állapítható meg az IGAZ mérete adatlemez kell.


## <a name="mount-the-attached-data-disk"></a>A csatolt adatlemez csatlakoztatása

1. Az RDP a hibaelhárítási virtuális géphez a megfelelő hitelesítő adatokkal. A következő példa a nevű virtuális gép RDP-kapcsolat fájljának letöltése `myVMRecovery` az erőforráscsoport neve `myResourceGroup`, és úgy, hogy letölti `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Az adatlemez automatikusan észlelt és csatolva. Az alábbiak szerint határozza meg a meghajtó betűjelével és csatolt kötetek listájának megtekintéséhez:

    ```powershell
    Get-Disk
    ```

    Az alábbi példa kimenetben látható a csatlakoztatott lemezt a virtuális merevlemez **2**. (Is `Get-Volume` megtekintéséhez a meghajtó-betűjelet):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez kapcsolatos problémák megoldása
A meglévő virtuális merevlemezzel csatlakoztatva is képes lemezvizsgálatok elvégzésére bármely karbantartási és hibaelhárítási lépéseket, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le a lemezképet, és válassza le az eredeti virtuális merevlemez
Ha a hibák fakadó problémák megoldásával válassza le, és a meglévő virtuális merevlemez válassza le a hibaelhárítási virtuális Gépet. A virtuális merevlemez nem használható a többi virtuális Géphez, amíg a címbérlet, a virtuális merevlemez csatolását a hibaelhárítási VM.

1. Az az RDP-munkameneten belül leválasztása a adatlemez a helyreállítási virtuális gép. A lemezek számát, az előző kell `Get-Disk` parancsmag. Ezt követően `Set-Disk` a lemez offline állapotúként beállításához:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Győződjön meg arról, a lemez most értéke offline használatával `Get-Disk` újra. Az alábbi példa kimenetben látható van a lemez most már offline módúra állítja:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Az RDP-munkamenetbe való kilépéshez. Az Azure PowerShell-munkamenetben távolítsa el a hibaelhárítási virtuális gépről a virtuális merevlemezt.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép eredeti merevlemez létrehozása
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). A tényleges JSON-sablon jelenleg a következő hivatkozásra:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

A sablont egy virtuális Gépet létrehozni meglévő virtuális hálózatban, a virtuális merevlemez URL-CÍMÉT a korábbi parancs használatával központilag telepíti. A következő példa telepíti a sablon nevű erőforráscsoport `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Válaszolja meg az utasításokat a sablonhoz, például a virtuális gép neve, típusa és Virtuálisgép-méretet. A `osDiskVhdUri` van azonos az előzőleg használt csatolni a meglévő virtuális merevlemez a hibaelhárítási virtuális géphez.


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika

Amikor a virtuális Gépet hoz létre a meglévő virtuális merevlemez, rendszerindítási diagnosztika automatikusan nem lehet engedélyezni. A következő példában engedélyezzük a diagnosztikai kiterjesztés nevű virtuális gép `myVMDeployed` az erőforráscsoport neve `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Egy kezelt lemez virtuális Gépet egy új operációsrendszer-lemez csatolásával hibaelhárítása
1. Állítsa le az érintett kezelt lemez Windows virtuális Gépet.
2. [Hozzon létre egy felügyelt lemezes pillanatképet](snapshot-copy-managed-disk.md) , az operációsrendszer-lemezképet, a felügyelt lemezt a virtuális gépet.
3. [Hozzon létre egy felügyelt lemezes a pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [A felügyelt lemezt csatolni a virtuális gép adatok lemezként](attach-disk-ps.md).
5. [4. lépés a adatlemez váltson az operációsrendszer-lemez](os-disk-swap.md).

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, tekintse meg [hibáinak elhárítása az RDP-kapcsolatok egy Azure virtuális gépre](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A virtuális gépen futó alkalmazások elérésével problémákkal kapcsolatban lásd: [alkalmazás csatlakozási problémák a Windows virtuális gép](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
