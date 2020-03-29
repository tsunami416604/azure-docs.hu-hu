---
title: Windows hibaelhárítási virtuális gép használata az Azure PowerShell használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a Windows virtuálisgépekkel kapcsolatos problémákat az Azure-ban az operációs rendszer lemezének az Azure PowerShell használatával történő helyreállítási virtuális géphez való csatlakoztatásával
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
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374132"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Windows virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure PowerShell használatával egy helyreállítási virtuális géphez való csatlakoztatásával
Ha a Windows virtuális gép (VM) az Azure-ban észlel egy rendszerindító vagy lemezhiba, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania a lemezen is. Egy gyakori példa lenne egy sikertelen alkalmazásfrissítés, amely megakadályozza, hogy a virtuális gép sikeresen eltudja indítani. Ez a cikk ismerteti, hogyan azure PowerShell segítségével csatlakoztassa a lemezt egy másik Windows virtuális gép a hibák kijavítása, majd javítsa ki az eredeti virtuális gép. 

> [!Important]
> A cikkben szereplő parancsfájlok csak a [felügyelt lemezt](../windows/managed-disks-overview.md)használó virtuális gépekre vonatkoznak. 

 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
Most már használhatja az Azure PowerShell a virtuális gép operációs rendszer lemezének módosításához. Már nem kell törölni, és újra a virtuális gép.

A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gép.
2. Hozzon létre egy pillanatképet a virtuális gép operációs rendszerlemezéről.
3. Hozzon létre egy lemezt az operációs rendszer lemezének pillanatképéből.
4. Csatlakoztassa a lemezt adatlemezként egy helyreállítási virtuális géphez.
5. Csatlakozzon a helyreállítási virtuális géphez. Fájlok szerkesztése vagy bármilyen eszköz futtatása a másolt operációsrendszer-lemezen felmerülő problémák megoldásához.
6. Válassza le és válassza le a lemezt a helyreállítási virtuális gépről.
7. Módosítsa az érintett virtuális gép operációsrendszer-lemezét.

A virtuális gép javítási parancsaival automatizálhatja az 1., 2., 3., 4., 6. További dokumentációt és utasításokat a [Windows virtuális gép javítása az Azure virtuálisgép-javítási parancsokkal című témakörben talál.](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Győződjön meg arról, hogy [a legújabb Azure PowerShell](/powershell/azure/overview) telepítve van, és bejelentkezett az előfizetésbe:

```powershell
Connect-AzAccount
```

A következő példákban cserélje le a paraméterneveket a saját értékeire. 

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Megtekintheti a virtuális gép képernyőképét az Azure-ban a rendszerindítási problémák elhárításához. Ez a képernyőkép segít azonosítani, hogy miért a virtuális gép nem indul el. A következő példa a képernyőképet a `myVM` Windows virtuális `myResourceGroup`gép neve a nevű erőforráscsoportban:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Tekintse át a képernyőképet annak meghatározásához, hogy a virtuális gép miért nem indul el. Jegyezze fel a megadott hibaüzeneteket vagy hibakódokat.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A következő példa leállítja a névvel ellátott erőforráscsoportból megnevezett `myVM` virtuális gépnevét: `myResourceGroup`

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Várjon, amíg a virtuális gép befejezte a törlést, mielőtt feldolgozna a következő lépésre.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Pillanatkép létrehozása a virtuális gép operációs rendszerlemezéről

A következő példa létrehoz `mySnapshot` egy pillanatképet a "myVM" nevű virtuális gép operációsrendszer-lemezének nevével. 

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

A pillanatkép a virtuális merevlemez teljes, csak olvasható példánya. Nem csatolható virtuális géphez. A következő lépésben létrehozunk egy lemezt ebből a pillanatképből.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből

Ez a parancsfájl egy `newOSDisk` felügyelt lemezt `mysnapshot`hoz létre névvel a snapshot nevű .  

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
Most már van egy másolatot az eredeti operációs rendszer lemez. Ezt a lemezt hibaelhárítási célból csatlakoztathatja egy másik Windows virtuális géphez.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>A lemez csatolása egy másik Windows virtuális géphez hibaelhárításhoz

Most csatolja az eredeti operációs rendszer lemezének másolatát egy virtuális géphez adatlemezként. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását vagy a lemezen lévő további alkalmazás- vagy rendszernaplófájlok áttekintését. A következő példa a `newOSDisk` nevű lemezt `RecoveryVM`a nevű virtuális géphez csatolja.

> [!NOTE]
> A lemez csatolásához az eredeti operációsrendszer-lemez és a helyreállítási virtuális gép másolatának ugyanazon a helyen kell lennie.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Csatlakozás a helyreállítási virtuális géphez, és a csatlakoztatott lemezen lévő problémák megoldása

1. RDP a helyreállítási virtuális gép a megfelelő hitelesítő adatok használatával. A következő példa letölti a névnek nevezett `RecoveryVM` `myResourceGroup`erőforráscsoportban megnevezett virtuális gép `C:\Users\ops\Documents`RDP-kapcsolatfájlját, és letölti a következőre: "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Az adatlemezt a rendszer automatikusan észleli és csatolja. A csatolt kötetek listájának megtekintésével határozza meg a meghajtó betűjelét az alábbiak szerint:

    ```powershell
    Get-Disk
    ```

    A következő példa kimeneti kimenet a lemez csatlakoztatva van egy **2**lemezhez. (A meghajtó `Get-Volume` betűjelét is megtekintheti):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Az eredeti operációsrendszer-lemez másolatának csatlakoztatása után szükség szerint elvégezheti a karbantartási és hibaelhárítási lépéseket. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-os-disk"></a>Eredeti operációsrendszer-lemez leválasztása és leválasztása
Miután a hibák megoldódtak, leválasztja és leválasztja a meglévő lemezt a helyreállítási virtuális gépről. A lemez nem használható más virtuális gép, amíg a lemezt a helyreállítási virtuális géphez csatoló bérlet fel nem oldható.

1. Az RDP-munkameneten belül válassza le az adatlemezt a helyreállítási virtuális gépen. Az előző `Get-Disk` parancsmag lemezszámának kell meglennie. Ezután `Set-Disk` állítsa be a lemezt kapcsolat nélküli módba:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Ellenőrizze, hogy a lemez `Get-Disk` újra kapcsolat nélküli módban van-e beállítva. A következő példakimenet azt mutatja, hogy a lemez most antól offline állapotban van:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Lépjen ki az RDP-munkamenetből. Az Azure PowerShell-munkamenetből távolítsa `newOSDisk` el a "RecoveryVM" nevű virtuális gépről a nevű lemezt.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Az érintett virtuális gép operációsrendszer-lemezének módosítása

Az Azure PowerShell segítségével cserélheti fel az operációs rendszer lemezeit. Nem kell törölnie, és újra létre kell hoznia a virtuális gép.

Ez a példa leállítja a megnevezett virtuális gép, `myVM` és hozzárendeli a lemezt, `newOSDisk` mint az új operációsrendszer-lemez. 

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

A következő példa engedélyezi a diagnosztikai `myVMDeployed` bővítményt a `myResourceGroup`névvel ellátott erőforráscsoportban megnevezett virtuális gépen:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>További lépések
Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az RDP-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákról a [Windows virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)témakörben nyújt fel problémát.

Az Erőforrás-kezelő használatáról az Azure Resource Manager – áttekintés című témakörben olvashat [bővebben.](../../azure-resource-manager/management/overview.md)
