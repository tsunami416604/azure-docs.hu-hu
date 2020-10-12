---
title: Windows hibaelhárítást biztosító virtuális gép használata Azure PowerShell használatával | Microsoft Docs
description: Útmutató a Windowsos virtuális gépek Azure-beli problémáinak elhárításához az operációsrendszer-lemez egy helyreállítási virtuális géphez való összekapcsolásával Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 63d5e4bcc57a734fc5ea455da7c2db940a4b8ec3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654684"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Windows rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával Azure PowerShell használatával
Ha az Azure-beli Windows rendszerű virtuális gép rendszerindítási vagy lemezhiba-hibát észlel, előfordulhat, hogy a lemezen hibaelhárítási lépéseket kell végrehajtania. Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan csatlakoztathatók a lemezek egy másik Windows rendszerű virtuális géphez a hibák javításához, majd az eredeti virtuális gép kijavításához Azure PowerShell használatával. 

> [!Important]
> A cikkben szereplő parancsfájlok csak a [felügyelt lemezt](../managed-disks-overview.md)használó virtuális gépekre vonatkoznak. 

 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
Most már használhatja a Azure PowerShellt a virtuális gép operációsrendszer-lemezének megváltoztatásához. Már nem kell törölnie és újra létrehozni a virtuális gépet.

A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gépet.
2. Hozzon létre egy pillanatképet a virtuális gép operációsrendszer-lemezéről.
3. Hozzon létre egy lemezt az operációsrendszer-lemez pillanatképből.
4. Csatlakoztassa a lemezt adatlemezként egy helyreállítási virtuális géphez.
5. Kapcsolódjon a helyreállítási virtuális géphez. Szerkessze a fájlokat, vagy futtasson eszközöket a másolt operációsrendszer-lemez problémáinak kijavításához.
6. Lemez leválasztása és leválasztása a helyreállítási virtuális gépről.
7. Módosítsa az érintett virtuális gép operációsrendszer-lemezét.

Az 1., 2., 3., 4., 6. és 7. lépések automatizálásához használhatja a virtuális gép javítási parancsait. További dokumentációért és útmutatásért lásd: [Windows rendszerű virtuális gép javítása az Azure-beli virtuális gépek javítási parancsaival](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Győződjön meg arról, hogy telepítve van-e [a legújabb Azure PowerShell](/powershell/azure/) , és jelentkezzen be az előfizetésbe:

```powershell
Connect-AzAccount
```

Az alábbi példákban cserélje le a paraméterek nevét a saját értékeire. 

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
A rendszerindítási problémák elhárításához az Azure-beli virtuális gép képernyőképét tekintheti meg. Ez a képernyőkép segítséget nyújt annak meghatározásában, hogy a virtuális gép miért nem indul el. A következő példa beolvassa a képernyőképet a nevű Windows virtuális gépről `myVM` `myResourceGroup` :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Tekintse át a képernyőképet annak meghatározásához, hogy miért sikertelen a virtuális gép indítása. Jegyezze fel a megadott hibaüzeneteket vagy hibakódokat.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A következő példa leállítja a nevű virtuális gépet `myVM` a nevű erőforráscsoporthoz `myResourceGroup` :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Várjon, amíg a virtuális gép el nem végezte a törlést a következő lépéshez való feldolgozás előtt.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Pillanatkép létrehozása a virtuális gép operációsrendszer-lemezéről

Az alábbi példa egy, a `mySnapshot` "myVM" nevű virtuális gép operációsrendszer-lemezéről származó nevű pillanatképet hoz létre. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. Nem csatlakoztatható virtuális géphez. A következő lépésben létrehozunk egy lemezt ebből a pillanatképből.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből

Ez a szkript létrehoz egy nevű felügyelt lemezt `newOSDisk` a nevű pillanatképből `mysnapshot` .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Most már rendelkezik az eredeti operációsrendszer-lemez másolatával. A lemez egy másik Windows rendszerű virtuális gépre is csatlakoztatható hibaelhárítási célból.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>A lemez csatolása egy másik Windows rendszerű virtuális géphez hibaelhárításhoz

Most csatoljuk az eredeti operációsrendszer-lemez másolatát egy virtuális géphez adatlemezként. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, illetve a lemezen található további alkalmazás-vagy rendszernaplók áttekintését. A következő példa a nevű virtuális géphez tartozó lemezt csatolja `newOSDisk` `RecoveryVM` .

> [!NOTE]
> A lemez csatlakoztatásához az eredeti operációsrendszer-lemez és a helyreállítási virtuális gép másolatának ugyanazon a helyen kell lennie.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Kapcsolódjon a helyreállítási virtuális géphez, és javítsa ki a csatlakoztatott lemezen lévő problémákat

1. Az RDP-t a helyreállítási virtuális géphez a megfelelő hitelesítő adatok használatával. Az alábbi példa letölti a nevű nevű virtuális gép RDP-kapcsolati fájlját `RecoveryVM` `myResourceGroup` , és letölti a `C:\Users\ops\Documents` következőre:

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Az adatlemezt automatikusan észlelni és csatolni kell. Tekintse meg a csatlakoztatott kötetek listáját a meghajtóbetűjel megállapításához a következő módon:

    ```powershell
    Get-Disk
    ```

    A következő példa kimenetében a **2**. lemezhez csatlakoztatott lemez látható. ( `Get-Volume` A meghajtó betűjelének megtekintéséhez használhatja a következőt is:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Az eredeti operációsrendszer-lemez másolatának csatlakoztatása után szükség esetén bármilyen karbantartási és hibaelhárítási lépést elvégezhet. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-os-disk"></a>Az eredeti operációsrendszer-lemez leválasztása és leválasztása
A hibák megoldása után leválaszthatja a meglévő lemezt a helyreállítási virtuális gépről, és leválaszthatja azt. A lemezt nem használhatja más virtuális géppel, amíg a lemezt a helyreállítási virtuális géphez csatlakoztató bérlet fel nem szabadítja.

1. Az RDP-munkameneten belül válassza le az adatlemezt a helyreállítási virtuális gépen. Az előző parancsmag lemezének számát kell használnia `Get-Disk` . Ezután használja a következőt `Set-Disk` a lemez offline állapotba állításához:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Ellenőrizze, hogy a lemez most már offline állapotban van-e beállítva `Get-Disk` . A következő példa kimenete azt mutatja, hogy a lemez most már offline állapotban van beállítva:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Lépjen ki az RDP-munkamenetből. A Azure PowerShell-munkamenetből távolítsa el a nevű lemezt `newOSDisk` a "RecoveryVM" nevű virtuális gépről.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Az érintett virtuális gép operációsrendszer-lemezének módosítása

Az operációs rendszer lemezeit a Azure PowerShell használatával cserélheti le. Nem kell törölnie és újból létrehoznia a virtuális gépet.

Ez a példa leállítja a nevű virtuális gépet `myVM` , és hozzárendeli a nevű lemezt `newOSDisk` az új operációsrendszer-lemezként. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Rendszerindítási diagnosztika ellenőrzése és engedélyezése

A következő példa engedélyezi a diagnosztikai bővítményt a `myVMDeployed` nevű erőforráscsoport-beli virtuális gépen `myResourceGroup` :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>További lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).
