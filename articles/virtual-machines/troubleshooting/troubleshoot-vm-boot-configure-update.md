---
title: Virtuális gép indítási elakadt a "Getting Windows készen áll. Ne kapcsolja ki a számítógép"az Azure-ban |} A Microsoft Docs
description: Vezessen be a lépéseket, amelyben virtuális gép indítási elakadt a "Getting Windows készen a hiba elhárításához. Ne kapcsolja ki a számítógépet.”
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c6d9f46582e1c618de6bfccea9328fb35aea7875
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485815"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Virtuális gép indítási elakadt a "Getting Windows készen áll. Ne kapcsolja ki a számítógép"az Azure-ban

Ez a cikk segítséget nyújt a probléma megoldásához, ha a virtuális gép (VM) elakadt be a "Getting Windows kész. Ne kapcsolja ki a számítógép"szakasz indításakor.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptoms"></a>Probléma

Ha használ **rendszerindítási diagnosztika** szeretne kapni a képernyőkép a virtuális gép, az operációs rendszer nem teljes mértékben elindításához. A virtuális Gépet jeleníti meg az üzenet "Getting Windows készen áll. Ne kapcsolja ki a számítógépet.”

![Üzenet például a Windows Server 2012 R2 rendszerhez](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Üzenet-példa](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Ok

Általában ez a probléma adódik, amikor a kiszolgáló állapotát az utolsó újraindítás után a konfiguráció módosult. A konfiguráció módosításának előfordulhat, hogy sikerült inicializálni, Windows-frissítéseket, vagy a módosítások a kiszolgálói szerepkörök/szolgáltatásával. A Windows Update, ha a frissítések mérete nagy, az operációs rendszer több időre van szüksége, konfigurálja újra a módosításokat.

## <a name="back-up-the-os-disk"></a>Készítsen biztonsági másolatot az operációsrendszer-lemez

Mielőtt megpróbálja kijavítani a problémát, készítsen biztonsági másolatot az operációsrendszer-lemez.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>A virtuális gépek egy titkosított lemezzel zárolásának feloldása a lemezek először

Kövesse az alábbi lépéseket annak megállapításához, hogy a virtuális gép egy titkosított virtuális gép.

1. Az Azure Portalon nyissa meg a virtuális gép, és keresse meg a lemezeket.

2. Tekintse meg a **titkosítási** oszlopban tekintheti meg, hogy engedélyezve van-e a titkosítás.

Ha az operációsrendszer-lemeze titkosítva van, a titkosított lemez zárolásának feloldásához. A lemez zárolásának feloldásához kövesse az alábbi lépéseket.

1. Hozzon létre egy helyreállítási virtuális Gépet, amely a ugyanazt az erőforráscsoportot, a Tárfiókot és a helyet az érintett virtuális gép található.

2. Az Azure Portalon az érintett virtuális gép törlése, és tartsa a lemezt.

3. Futtassa a Powershellt rendszergazdaként.

4. Futtassa a következő parancsmagot a titkos kód nevét.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Miután a titkos kód nevét, futtassa az alábbi parancsokat a PowerShellben.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. A Base64-kódolású érték átalakítása bájt, és a kimenet fájlba írása. 

    > [!Note]
    > Ha az USB zárolásának feloldása a beállítást, a rendelkeznek BEk-KEL fájl nevének meg kell egyeznie az eredeti rendelkeznek BEk-KEL GUID. Hozzon létre egy mappát a C meghajtó "Blokktitkosítási kulcsot" nevű, akkor kövesse ezeket a lépéseket.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. A számítógépen a blokktitkosítási kulcsot fájl létrehozása után másolja a fájlt a helyreállítási virtuális Gépet, az zárolt operációsrendszer-lemez csatolva van. Futtassa az alábbi parancsokat a blokktitkosítási kulcsot fájl helyét.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Nem kötelező**: Bizonyos esetekben lehet szükség, a lemez visszafejteni a következő paranccsal.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Az előző parancs feltételezi, hogy a lemez titkosításához a levél f

8. Naplók gyűjtése az kell, ha nyissa meg az elérési út **MEGHAJTÓ betűjele: \Windows\System32\winevt\Logs**.

9. A helyreállítási gépről a meghajtó leválasztása.

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Pillanatkép létrehozása lépésekkel [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Az operációs rendszer memóriakép begyűjtése

Kövesse a [gyűjtése az operációs rendszer memóriakép](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) egy operációs rendszer memóriakép gyűjtése, ha a virtuális gép konfigurációs megakadt szakasz.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Miután összegyűjtötte a memóriakép-fájl, lépjen kapcsolatba [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az alapvető ok elemzését.


## <a name="rebuild-the-vm-by-using-powershell"></a>Építse újra a virtuális Gépet PowerShell-lel

Miután összegyűjtötte a memóriakép, kövesse az alábbi lépéseket a virtuális gép számára.

**A nem felügyelt lemezek**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Felügyelt lemezek**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
