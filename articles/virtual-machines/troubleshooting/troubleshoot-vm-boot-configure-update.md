---
title: A virtuális gép indítása beragadt a Windows rendszerre kész állapotba. Ne kapcsolja ki a számítógépet az Azure-ban | Microsoft Docs
description: Bemutatjuk azokat a lépéseket, amelyekkel elhárítható a virtuális gép indításakor beragadt "a Windows rendszer kész állapotba kerül. Ne kapcsolja ki a számítógépet.”
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: d0a946ede154561aaa49d335b7b91fdae72c51d3
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155559"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A virtuális gép indítása beragadt a Windows rendszerre kész állapotba. A számítógép kikapcsolása az Azure-ban

Ez a cikk segítséget nyújt a probléma megoldásához, amikor a virtuális gép (VM) beragadt a "Windows rendszerre kész állapotba. Az indítás során ne kapcsolja ki a számítógép "szakaszát.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Probléma

Ha **rendszerindítási diagnosztikát** használ a virtuális gép képernyőképének beszerzéséhez, az operációs rendszer nem indul el teljesen. A virtuális gép megjeleníti a "Felkészülés a Windowsba" üzenetet. Ne kapcsolja ki a számítógépet.”

![Példa a Windows Server 2012 R2-re](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Üzenet – példa](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Ok

Ez a probléma általában akkor fordul elő, ha a kiszolgáló a konfiguráció módosítása után a végső újraindítást végzi. Előfordulhat, hogy a konfigurációs módosítást a Windows-frissítések vagy a kiszolgáló szerepkörei/funkciójának módosításai inicializálják. Windows Update esetén, ha a frissítések mérete nagy, az operációs rendszernek több időre van szüksége a módosítások újrakonfigurálásához.

## <a name="back-up-the-os-disk"></a>Az operációsrendszer-lemez biztonsági mentése

A probléma elhárítása előtt biztonsági mentést készíthet az operációsrendszer-lemezről.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Titkosított lemezzel rendelkező virtuális gépek esetén először fel kell oldani a lemezek zárolását.

Az alábbi lépéseket követve megállapíthatja, hogy a virtuális gép titkosított virtuális gép-e.

1. A Azure Portal nyissa meg a virtuális gépet, majd keresse meg a lemezeket.

2. Tekintse meg a **titkosítás** oszlopot, és ellenőrizze, hogy engedélyezve van-e a titkosítás.

Ha az operációsrendszer-lemez titkosítva van, oldja fel a titkosított lemez zárolását. A lemez zárolásának feloldásához kövesse az alábbi lépéseket.

1. Hozzon létre egy olyan helyreállítási virtuális gépet, amely ugyanabban az erőforráscsoportban, Storage-fiókban és helyen található, mint az érintett virtuális gép.

2. A Azure Portal törölje az érintett virtuális gépet, és tartsa meg a lemezt.

3. Futtassa a PowerShellt rendszergazdaként.

4. Futtassa a következő parancsmagot a titkos kulcs nevének beolvasásához.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. A titkos kód neve után futtassa a következő parancsokat a PowerShellben.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konvertálja a Base64 kódolású értéket bájtra, és írja a kimenetet egy fájlba. 

    > [!Note]
    > Ha az USB-feloldási lehetőséget használja, a BEK-fájl nevének meg kell egyeznie az eredeti BEK GUID azonosítóval. Az alábbi lépéseket követve hozzon létre egy "BEK" nevű mappát a C meghajtón.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Miután létrehozta a BEK-fájlt a SZÁMÍTÓGÉPén, másolja a fájlt a helyreállítási virtuális gépre, amelyhez a zárolt operációsrendszer-lemez csatlakozik. Futtassa a következő parancsokat a BEK-fájl helyének használatával.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    Nem **kötelező**: Bizonyos esetekben szükség lehet a lemez visszafejtésére a parancs használatával.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Az előző parancs feltételezi, hogy a titkosított lemez az F betűn van.

8. Ha naplókat kell gyűjtenie, nyissa meg a Path **meghajtó betűjelét: \ Windows\System32\winevt\Logs**.

9. Válassza le a meghajtót a helyreállítási gépről.

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Pillanatkép létrehozásához kövesse a [pillanatkép egy lemezének](../windows/snapshot-copy-managed-disk.md)lépéseit.

## <a name="collect-an-os-memory-dump"></a>Operációs rendszer memóriaképének begyűjtése

Az operációs [rendszer memóriaképének begyűjtése](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) szakaszának lépéseit követve GYŰJTHET egy operációsrendszer-memóriaképet, ha a virtuális gép beragad a konfigurációnál.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A memóriakép-fájl összegyűjtése után forduljon a [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és elemezze a kiváltó okot.


## <a name="rebuild-the-vm-by-using-powershell"></a>A virtuális gép újraépítése a PowerShell használatával

A memóriakép fájljának összegyűjtése után a virtuális gép újraépítéséhez kövesse az alábbi lépéseket.

**Nem felügyelt lemezek esetén**

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

**Felügyelt lemezek esetén**

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
