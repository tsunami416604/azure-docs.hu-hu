---
title: Virtuális gép létrehozása speciális lemezről az Azure-ban |} A Microsoft Docs
description: Új virtuális gép létrehozása a Resource Manager-alapú üzemi modellben egy specializált nem felügyelt lemez csatlakoztatásával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3465a891d91bc54bfe39b9501a7ad0e970932860
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309624"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Virtuális gép létrehozása a tárfiókban lévő speciális virtuális merevlemezből

Hozzon létre egy új virtuális Gépet a Powershell-lel operációsrendszer-lemezként speciális nem felügyelt lemez csatlakoztatásával. Egy speciális lemez VHD-t, amely fenntartja a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép meglévő virtuális gépről egy példányát. 

Erre két lehetősége van:
* [VHD feltöltése](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Másolja a VHD-t a meglévő Azure virtuális gépek](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Option 1: Egyéni VHD feltöltése

A virtuális Merevlemezt egy specializált virtuális eszközzel létrehozott egy a helyszíni virtualizálási, mint a Hyper-V vagy a virtuális gép exportál egy másik felhőalapú tölthet fel.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Feltölthet egy specializált virtuális Merevlemezt, amely egy helyszíni virtuális gép vagy egy virtuális Merevlemezt egy másik felhőben exportálva lett létrehozva. Egyéni VHD tárolja a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gépről. Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket. 
  
  * [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** a Sysprep használata virtuális gép általánosítása.
  * A Vendég virtualizációs eszközök és a virtuális gépen (azaz a VMware-eszközök) telepített ügynökök eltávolítása.
  * Győződjön meg arról, kérje le az IP-cím és DNS-beállítások a DHCP-n keresztül a virtuális gép van konfigurálva. Ez biztosítja, hogy a kiszolgáló a virtuális hálózaton belül egy IP-címet kap, indításkor. 


### <a name="get-the-storage-account"></a>A storage-fiók létrehozása
Az Azure-ban tárolja a virtuális gép feltöltött kép egy tárfiókra van szükség. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tár fiókokat jeleníti meg, írja be:

```powershell
Get-AzStorageAccount
```

Ha egy meglévő tárfiókot használni kívánt, folytassa a feltöltés a virtuális gép rendszerkép szakaszban.

Ha szeretne hozzon létre egy tárfiókot, kövesse az alábbi lépéseket:

1. Szüksége lesz az erőforráscsoport, amelyben kell létrehozni a tárfiók nevére. Ismerje meg, hogy minden erőforráscsoport az előfizetésben található, írja be:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Hozzon létre egy erőforráscsoportot a **myResourceGroup** a a **USA nyugati RÉGIÓJA** régió, írja be:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű **mystorageaccount** használatával az erőforráscsoport a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmagot:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>A virtuális lemezek feltöltése a storage-fiókba
Használja a [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmaggal töltse fel a rendszerképet egy tárolót a tárfiókjában. Ez a példa feltölti a fájlt **myVHD.vhd** a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókhoz nevű **mystorageaccount** a a **myResourceGroup** erőforráscsoportot. A fájlt a tárolóba nevű kerülnek **mycontainer** és az új fájl neve lesz **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres művelet esetén olyan ehhez hasonló választ kap:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

A hálózati kapcsolat és a VHD-fájl méretétől függően ez a parancs is igénybe vehet igénybe.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Option 2: Másolja a VHD-t a meglévő Azure virtuális gépből

Virtuális merevlemez másolhatja, amikor egy új, ismétlődő virtuális Gépet hoz létre egy másik tárfiókba.

### <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy Ön:

* Kapcsolatos információkkal rendelkezzen a **forrás- és storage-fiókok**. A forrás virtuális gép szüksége lesz a storage-fiók és a tároló nevét. Általában a tároló neve lesz **VHD-k**. Emellett szüksége lesz a cél tárfiók. Ha még nem rendelkezik egy, létrehozhat egyet vagy a portál használatával (**minden szolgáltatás** > tárfiókok > hozzáadása) vagy a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmagot. 
* Letöltötte és telepítette a [AzCopy eszköz](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>A virtuális gép felszabadítása
Szabadítsa fel a virtuális Gépet, így Önnek be kell másolni a virtuális merevlemez. 

* **Portál**: Kattintson a **virtuális gépek** > **myVM** > leállítása
* **PowerShell**: Használja [Stop-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) , állítsa le (szabadítsa fel) nevű virtuális Gépet **myVM** erőforráscsoportban **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

A **állapot** a virtuális gép az Azure Portalon a változik **leállítva** való **leállítva (felszabadítva)**.

### <a name="get-the-storage-account-urls"></a>A storage-fiók URL-címek lekérése
Szüksége lesz a forrás- és storage-fiókok URL-címei. Az URL-címek néz ki: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Ha már ismeri a tárolási fiók és a tároló nevét, egyszerűen lecserélheti az adatok az URL-cím létrehozása a zárójelek között. 

Használhatja az Azure portal vagy Azure PowerShell-lel az URL-Címének lekéréséhez:

* **Portál**: Kattintson a **>** a **minden szolgáltatás** > **tárfiókok** > *tárfiók*  >  **Blobok** és a forrás VHD-fájl valószínűleg a **VHD-k** tároló. Kattintson a **tulajdonságok** a tároló és a címkével ellátott szöveg másolása **URL-cím**. Szüksége lesz a forrás- és tárolók URL-címei. 
* **PowerShell**: Használat [Get-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) nevű virtuális gép esetében a adatok **myVM** erőforráscsoportban **myResourceGroup**. Az eredmények között, tekintse meg a **tárolóprofil** szakasz a **Vhd Uri**. Az URI-t első része az URL-címe a tárolóhoz, a második pedig a rendszert tartalmazó virtuális Merevlemezt a virtuális gép nevét.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>A tárelérési kulcsok beolvasása
Keresse meg a hozzáférési kulcsok a forrás- és storage-fiókok. Hozzáférési kulcsokkal kapcsolatos további információkért lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

* **Portál**: Kattintson a **minden szolgáltatás** > **tárfiókok** > *tárfiók* > **hozzáférési kulcsok**. Másolja a kulcsot a következő címkét: **key1**.
* **PowerShell**: Használat [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) beolvasni a tárfiókhoz a tárfiók hívóbetűjét **mystorageaccount** erőforráscsoportban **myResourceGroup**. Másolja a címkével ellátott kulcsot **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Másolja a VHD-t
Másolhatja a fájlokat a storage-fiókok az AzCopy használata között. Céltárolóját Ha a megadott tároló nem létezik, akkor létrejön az Ön számára. 

Az AzCopy használata, nyisson meg egy parancssort a helyi gépen, és lépjen abba a mappába, amelyen telepítve van-e az AzCopy. Hasonló lesz *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Az összes olyan tárolóban fájl másolásához használja a **/S** váltani. Ez a rendszert tartalmazó virtuális Merevlemezt és az összes adatlemezét másolása, ha azok ugyanabban a tárolóban is használható. Ez a példa bemutatja, hogyan másolhatja az összes fájlt a tárolóban **mysourcecontainer** tárfiókban **mysourcestorageaccount** a tárolóhoz **mydestinationcontainer**a a **mydestinationstorageaccount** storage-fiókot. Cserélje le a storage-fiókok és a tárolók neve a saját. Cserélje le `<sourceStorageAccountKey1>` és `<destinationStorageAccountKey1>` saját kulcsokkal.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Ha csak egy adott virtuális Merevlemezt másolja egy tárolóban több fájlt, adja meg a fájl nevét a /Pattern kapcsoló használatával. Ebben a példában csak a nevű fájl **myFileName.vhd** lesznek másolva.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Amikor elkészült, egy, a következőhöz hasonló üzenetet fog kapni:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Hibaelhárítás
* AZCopy használatakor, ha a hibaüzenetet a "Kiszolgáló nem tudta hitelesíteni a kérelmet", győződjön meg arról, az engedélyezési fejléc értékének formátuma megfelelő többek között az aláírás. Ha a kulcs 2-es vagy a másodlagos tárelérési kulcsot használ, próbáljon az elsődleges vagy 1-től tárkulcsra.

## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Az új virtuális gép által használt hálózati és más Virtuálisgép-erőforrások létrehozásához szükséges.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és virtuális hálózat létrehozása

Hozza létre a virtuális hálózat és alhálózat, a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md).

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy nevű alhálózatot **mySubNet**, az erőforráscsoport **myResourceGroup**, és az alhálózati cím előtag **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. A virtuális hálózat létrehozása. Ebben a példában a virtuális hálózat nevét, hogy beállítja **myVnetName**, a hely **USA nyugati RÉGIÓJA**, és a címelőtag a virtuális hálózat **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és a egy RDP-szabály létrehozása
Az, hogy jelentkezzen be a virtuális gép RDP-vel, szüksége lesz egy biztonsági szabályt, amely lehetővé teszi a 3389-es RDP-hozzáférést. Mert a virtuális Merevlemezt az új virtuális gép egy meglévő specializált virtuális gép, miután a virtuális gép létrejött, akkor használhatja egy meglévő fiókot, a forrás virtuális gépen, amely RDP használatával kíván bejelentkezni engedély volna.
Ezt manuálisan kell végrehajtani, társítva lesz hálózati adapter létrehozása előtt.  
Ebben a példában állítja be az NSG neve **myNsg** és az RDP-szabály neve, **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

További információ a végpontok és NSG-szabályok: [egy virtuális gép portjainak megnyitása az Azure PowerShell-lel](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Hozzon létre egy nyilvános IP-cím és a hálózati adapter
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. A nyilvános IP-cím létrehozása. Ebben a példában a nyilvános IP-cím neve van beállítva, **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozzon létre a hálózati adaptert. Ebben a példában a hálózati adapter neve van beállítva, **myNicName**. Ez a lépés is társítja a hálózati adapteren. a korábban létrehozott hálózati biztonsági csoport
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>A virtuális gép neve és mérete

Ebben a példában állítja be, a virtuális gép neve a "myVM" és "Standard_A2" a virtuális gép méretét.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Az operációsrendszer-lemez konfigurálása

1. Állítsa be az URI-t a virtuális merevlemez, amely feltölteni, vagy másolja. Ebben a példában a VHD-fájl nevű **myOsDisk.vhd** nevű storage-fiókban tárolt **myStorageAccount** nevű tárolóban **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adja hozzá az operációsrendszer-lemez. Ebben a példában az operációsrendszer-lemez létrehozásakor a "osDisk" kifejezés appened létrehozása az operációs rendszer lemezének neve a virtuális gép nevére. Ebben a példában is megadja, hogy a Windows-alapú virtuális Merevlemezt a virtuális Gépet az operációsrendszer-lemezként akarjuk csatolni.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Nem kötelező: Ha kell lennie a virtuális Géphez csatolt adatlemezek, adja hozzá az adatlemezeket az URL-címeket, az adatok VHD-k és a megfelelő logikai egységen (Lun) használatával.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Storage-fiók használata esetén az adatokat és operációsrendszer-lemez URL-címek a következőhöz hasonló: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Megtalálhatja a portálon keresse meg a cél tároló, az operációs rendszer vagy a másolt virtuális majd majd másolja az URL-címet a tartalmát.


### <a name="complete-the-vm"></a>Végezze el a virtuális gép 

Hozzon létre a virtuális Gépet az imént létrehozott konfigurációk használatával.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Ha ez a parancs sikeres volt, akkor ehhez hasonló kimenet jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy a virtuális gép létrejött-e.
Megjelenik az újonnan létrehozott virtuális gép vagy a a [az Azure portal](https://portal.azure.com)alatt **minden szolgáltatás** > **virtuális gépek**, vagy a következő PowerShell-lel parancsok:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gépet. További információkért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md).

