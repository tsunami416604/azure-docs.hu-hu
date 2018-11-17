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
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 1dd529b142de9815ed41f68bc9b60cdda5d47612
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820052"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Hibaelhárítás Windows virtuális gépek az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure PowerShell használatával történő csatlakoztatásával
Ha a Windows virtuális gép (VM) az Azure-ban egy indítási vagy hibát észlel, szükség lehet a hibaelhárítási lépésekkel magán a lemezen. Ilyenek például egy sikertelen alkalmazásfrissítés megakadályozza, hogy a virtuális gép képes arra, hogy sikeresen lenne. Ez a cikk részletesen bemutatja az Azure PowerShell használatával csatlakoztassa a lemezt egy másik Windows virtuális géphez, javítsa ki a hibákat, majd javítsa ki az eredeti virtuális Gépet. 

> [!Important]
> Ebben a cikkben a parancsfájlok csak használó virtuális gépeknél [Managed Disk](../windows/managed-disks-overview.md). 


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
Azure PowerShell használatával most már az operációsrendszer-lemez módosítása egy virtuális géphez. Már nincs szükségünk, törölje és hozza létre újra a virtuális Gépet.

A hibaelhárítási folyamat a következő:

1. Állítsa az érintett virtuális Gépeket.
2. Pillanatkép létrehozása a virtuális gép operációsrendszer-lemez alapján.
3. Lemez létrehozása az operációs rendszer lemez pillanatképéből.
4. Csatlakoztassa a lemezt adatlemezként egy helyreállítási virtuális Géphez.
5. Csatlakozzon a helyreállítási virtuális Gépet. Szerkesztheti a fájlokat, vagy futtassa az eszközöket az hibák javítása a másolt rendszerlemezt.
6. Válassza le a lemezképet, és leválasztani a lemezt a helyreállítási virtuális Gépet.
7. Az érintett virtuális gépek az operációsrendszer-lemez módosítása

A virtuális gép helyreállítási parancsfájlok segítségével automatizálhatja a lépéseket, 1, 2, 3, 4, 6 és 7. További dokumentáció és útmutatás: [szkripteket a Resource Manager-alapú virtuális gép](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Győződjön meg arról, hogy rendelkezik-e [az Azure PowerShell legújabb verzióját](/powershell/azure/overview) telepítve, és naplózza előfizetéséhez:

```powershell
Connect-AzureRmAccount
```

A következő példákban cserélje le a saját értékeit a paraméterek nevei. 

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Megtekintheti a virtuális gép Képernyőkép az Azure-beli rendszerindítási problémáinak hibaelhárításához. Ezen a képernyőfelvételen látható segítségével azonosítható, hogy miért a virtuális gép rendszerindító sikertelen lesz. Az alábbi példa lekéri a képernyőképen nevű Windows virtuális gépről `myVM` az erőforráscsoport neve `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Tekintse át a képernyőképen meghatározni, miért a virtuális gép rendszerindító sikertelen. Megjegyzés: minden olyan konkrét hibaüzeneteket vagy megadott hibakódok.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A következő példa leállítja a virtuális gép nevű `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```powershell
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Várjon, amíg a virtuális gép törlése előtt dolgozza fel a következő lépéssel véget ért.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Pillanatkép készítése a virtuális gép operációsrendszer-lemez

Az alábbi példa létrehoz egy pillanatképet nevű `mySnapshot` az operációs rendszer a lemezt a virtuális gép "myVM" nevű. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azurermvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzureRmSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Pillanatkép egy virtuális merevlemez teljes, a csak olvasható példányát. Egy virtuális géphez nem lehet csatolni. A következő lépésben hozunk létre egy lemezt a pillanatképből.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása pillanatképből a

Ez a szkript egy felügyelt lemezt hoz létre nevet `newOSDisk` a nevű pillanatkép `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzureRmLocation
$location = 'eastus'

$snapshot = Get-AzureRmSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzureRmDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Most már az eredeti operációsrendszer-lemez egy példányát. Akkor is csatlakoztathatja ezt a lemezt egy másik Windows virtuális géphez hibaelhárítás céljából.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Csatlakoztassa a lemezt egy másik Windows virtuális géphez hibaelhárítás

Most már tudjuk a példányának csatolása az eredeti operációsrendszer-lemez virtuális géphez adatlemezként. Ez a folyamat lehetővé teszi konfigurációs hibákat, vagy tekintse át a további alkalmazás vagy a lemez a rendszernapló fájljaiban. Az alábbi példa nevű lemezt csatlakoztat `newOSDisk` nevű VM `RecoveryVM`.

> [!NOTE]
> Csatlakoztassa a lemezt, az eredeti operációsrendszer-lemez és a helyreállítási virtuális Gépet a másolata ugyanazon a helyen kell lennie.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>A helyreállítási virtuális Géphez csatlakozik, és a csatlakoztatott lemez hibáinak javítása

1. RDP-vel a helyreállítási virtuális Gépet a megfelelő hitelesítő adatokat. Az alábbi példa a nevű virtuális gép RDP-kapcsolat fájljának letöltése `RecoveryVM` az erőforráscsoport neve `myResourceGroup`, és letölti azt `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Az adatlemez kell lehet automatikusan észlelt, és csatolva. A meghajtó betűjeléhez meghatározni a következő csatolt kötetek listájának megtekintése:

    ```powershell
    Get-Disk
    ```

    Az alábbi példa kimenetében látható, hogy a lemez csatlakoztatott lemez **2**. (Is `Get-Volume` meghajtóbetűjelet megtekintése):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Után az eredeti operációsrendszer-lemez másolatát csatlakoztatva van, karbantartási és hibaelhárítási lépések, igény szerint is végezhet. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-os-disk"></a>Válassza le, és az eredeti operációsrendszer-lemez leválasztása
Miután a hibák megoldódnak, válassza le, és válassza le a meglévő lemezt a helyreállítási virtuális Gépet a. A lemezek nem használhatóak a többi virtuális Géphez, mindaddig, amíg a bérlet, a lemez csatolása a helyreállítási virtuális Gépet jelent meg.

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

2. Lépjen ki az RDP-munkamenetet. Az Azure PowerShell-munkamenetben távolítsa el a lemezt, nevű `newOSDisk` "RecoveryVM" nevű virtuális gépről.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Az érintett virtuális gépek az operációsrendszer-lemez módosítása

Azure PowerShell használatával az operációsrendszer-lemez felcserélése. Nem kell törölnie kell, és hozza létre újra a virtuális Gépet.

Ebben a példában leállítja a virtuális gép nevű `myVM` és hozzárendeli a nevű lemez `newOSDisk` az új operációsrendszer-lemezként. 

```powershell
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Győződjön meg arról, és a rendszerindítási diagnosztika engedélyezése

A következő példában engedélyezzük a diagnosztikai bővítmény nevű virtuális gépre `myVMDeployed` az erőforráscsoport neve `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása RDP-kapcsolatai egy Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [a Windows virtuális gép alkalmazások csatlakozási hibáinak elhárítása](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
