---
title: Virtuális gép létrehozása egy speciális lemezről az Azure-ban
description: Hozzon létre egy új virtuális gépet egy speciális, nem felügyelt lemez csatolásával a Resource Manager-alapú üzemi modellben.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 2dc671e3aab7568da61e5dab870967d7fd2bb90f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525738"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Virtuális gép létrehozása egy speciális VHD-ből egy Storage-fiókban

Hozzon létre egy új virtuális gépet úgy, hogy egy speciális, nem felügyelt lemezt csatlakoztat az operációsrendszer-lemezzel a PowerShell használatával. A speciális lemez a virtuális merevlemez másolata egy meglévő virtuális gépről, amely karbantartja a felhasználói fiókokat, az alkalmazásokat és az eredeti virtuális gépről származó egyéb állapotinformációkat. 

Erre két lehetősége van:
* [VHD feltöltése](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Meglévő Azure-beli virtuális gép VHD-fájljának másolása](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>1. lehetőség: speciális VHD feltöltése

Feltöltheti a virtuális merevlemezt egy helyszíni virtualizációs eszközzel, például a Hyper-V-vel létrehozott speciális virtuális gépről, vagy egy másik felhőből exportált virtuális gépre.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Feltölthet olyan speciális virtuális merevlemezt, amelyet helyszíni virtuális géppel vagy más felhőből exportált VHD-vel hoztak létre. A speciális virtuális merevlemezek az eredeti virtuális gépről származó felhasználói fiókokat, alkalmazásokat és egyéb állapotinformációkat kezelik. Ha a virtuális merevlemezt egy új virtuális gép létrehozásához kívánja használni, győződjön meg arról, hogy a következő lépések befejeződtek. 
  
  * [Készítse elő a Windows VHD-t az Azure-ba való feltöltéshez](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne általánosítsa** a virtuális gépet a Sysprep használatával.
  * Távolítsa el a virtuális gépre telepített összes vendég virtualizációs eszközt és ügynököt (azaz a VMware-eszközöket).
  * Győződjön meg arról, hogy a virtuális gép úgy van konfigurálva, hogy az IP-címét és DNS-beállításait DHCP protokollon keresztül húzza Ez biztosítja, hogy a kiszolgáló az indításkor megszerezze az IP-címet a VNet belül. 


### <a name="get-the-storage-account"></a>A Storage-fiók beszerzése
A feltöltött virtuálisgép-rendszerkép tárolásához egy Azure Storage-fiókra van szüksége. Használhat meglévő Storage-fiókot, vagy létrehozhat egy újat. 

A rendelkezésre álló Storage-fiókok megjelenítéséhez írja be a következőt:

```powershell
Get-AzStorageAccount
```

Ha meglévő Storage-fiókot szeretne használni, folytassa a virtuális gép rendszerképének feltöltésével foglalkozó szakaszt.

Ha létre kell hoznia egy Storage-fiókot, kövesse az alábbi lépéseket:

1. Szüksége lesz annak az erőforráscsoport-csoportnak a nevére, amelyben létre szeretné hozni a Storage-fiókot. Az előfizetéshez tartozó összes erőforráscsoport megkereséséhez írja be a következőt:
   
    ```powershell
    Get-AzResourceGroup
    ```

    A **myResourceGroup** nevű erőforráscsoport létrehozásához az **USA nyugati** régiójában írja be a következőt:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy **mystorageaccount** nevű Storage-fiókot ebben az erőforráscsoporthoz a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>A VHD feltöltése a tárfiókba
Az [Add-AzVhd](/powershell/module/az.compute/add-azvhd) parancsmag használatával töltse fel a rendszerképet a Storage-fiókjában található tárolóba. Ez a példa feltölti a **myVHD. vhd** fájlt `"C:\Users\Public\Documents\Virtual hard disks\"` egy **mystorageaccount** nevű Storage-fiókba a **myResourceGroup** -erőforráscsoporthoz. A fájl a **mycontainer** nevű tárolóba kerül, és az új fájlnév **myUploadedVHD. vhd**lesz.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha a művelet sikeres, a következőhöz hasonló választ kap:

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

A hálózati kapcsolatban és a VHD-fájl méretétől függően ez a parancs hosszabb időt is igénybe vehet.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>2. lehetőség: a virtuális merevlemez másolása meglévő Azure-beli virtuális gépről

Új, duplikált virtuális gép létrehozásakor a VHD-t átmásolhatja egy másik Storage-fiókba.

### <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy:

* Információ a **forrás-és a cél Storage-fiókokról**. A forrásoldali virtuális gép esetében rendelkeznie kell a Storage-fiókkal és a tárolók nevével. A tároló neve általában **VHD**-k lesznek. Szükség van egy célként megadott Storage-fiókra is. Ha még nem rendelkezik ilyennel, létrehozhat egyet a portálon (az**összes szolgáltatás** > a Storage-fiókok > Hozzáadás) vagy a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával. 
* A [AzCopy eszköz](../../storage/common/storage-use-azcopy-v10.md)letöltése és telepítése megtörtént. 

### <a name="deallocate-the-vm"></a>A virtuális gép felszabadítása
Szabadítsa fel a virtuális gépet, amely felszabadítja a másolandó virtuális merevlemezt. 

* **Portál**: kattintson a **Virtual Machines**  >  **myVM** > leállítás elemre.
* **PowerShell**: a [stop-AzVM](/powershell/module/az.compute/stop-azvm) használatával állítsa le (szabadítsa fel) a **myVM** nevű virtuális gépet az erőforráscsoport **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

A Azure Portalban a virtuális gép állapota **Leállítva** **(leállítva)** **állapotra** módosult.

### <a name="get-the-storage-account-urls"></a>A Storage-fiók URL-címeinek beolvasása
Szüksége lesz a forrás és a cél Storage-fiókok URL-címeire. Az URL-címek így néznek ki: `https://<storageaccount>.blob.core.windows.net/<containerName>/` . Ha már ismeri a Storage-fiókot és a tároló nevét, egyszerűen cserélje le az adatokat a zárójelek között az URL-cím létrehozásához. 

Az URL-címet a Azure Portal vagy az Azure PowerShell használatával érheti el:

* **Portál**: kattintson a **>** **minden szolgáltatás**  >  **Storage accounts**  >  *Storage-fiók*  >  **Blobok** elemre, és a forrás vhd-fájl valószínűleg a **VHD** -tárolóban található. Kattintson a tároló **tulajdonságai** elemre, és másolja ki a szöveg címkével ellátott **URL-címét**. Szüksége lesz a forrás és a cél tárolók URL-címeire is. 
* **PowerShell**: a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával lekérheti a **myVM** nevű virtuális gép adatait az erőforráscsoport **myResourceGroup**. Az eredmények között keresse meg a **tárolási profil** szakaszt a **VHD URI**-hoz. Az URI első része a tároló URL-címe, az utolsó rész pedig a virtuális gép operációs rendszerének VHD-neve.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>A tárolási hozzáférési kulcsok beolvasása
Keresse meg a forrás és a cél Storage-fiókok hozzáférési kulcsait. További információ a hozzáférési kulcsokról: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-account-create.md).

* **Portál**: kattintson **a minden szolgáltatás**  >  **Storage-fiókok**  >  *Storage-fiók*  >  **hozzáférési kulcsa**elemre. Másolja a **key1**címkével ellátott kulcsot.
* **PowerShell**: a [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) használatával szerezze be a Storage-fiók **mystorageaccount** az erőforráscsoport **myResourceGroup**. Másolja a **key1**címkével ellátott kulcsot.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>A VHD másolása
A AzCopy használatával fájlokat másolhat a Storage-fiókok között. Ha a célként megadott tároló nem létezik, akkor a rendszer létrehozza az Ön számára. 

A AzCopy használatához nyisson meg egy parancssort a helyi gépen, és navigáljon arra a mappára, ahol a AzCopy telepítve van. Hasonló lesz a *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

A tárolóban lévő összes fájl másolásához használja az **/s** kapcsolót. Ez az operációs rendszer VHD-jét és az összes adatlemez másolását is felhasználhatja, ha ugyanabban a tárolóban vannak. Ez a példa bemutatja, hogyan másolhatja át a tároló **mysourcecontainer** lévő összes fájlt a Storage-fiók **mysourcestorageaccount** a **mydestinationstorageaccount** tároló **mydestinationcontainer** . Cserélje le a Storage-fiókok és-tárolók nevét a saját adataira. Cserélje `<sourceStorageAccountKey1>` le `<destinationStorageAccountKey1>` a és a billentyűkombinációt a saját kulcsaira.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Ha csak egy adott virtuális merevlemezt szeretne átmásolni egy több fájllal rendelkező tárolóban, a fájlnevet a/Pattern kapcsoló használatával is megadhatja. Ebben a példában csak az **myFileName. vhd** nevű fájl lesz átmásolva.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Ha elkészült, a következőhöz hasonló üzenet jelenik meg:

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
* Ha a AZCopy használatakor a "kiszolgáló nem tudta hitelesíteni a kérést" hibaüzenet jelenik meg, ellenőrizze, hogy az engedélyezési fejléc értéke helyesen van-e formázva, beleértve az aláírást. Ha a 2. kulcsot vagy a másodlagos tárterületet használja, próbálkozzon az elsődleges vagy az első tárolási kulcs használatával.

## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Létre kell hoznia egy hálózatkezelést és más virtuálisgép-erőforrásokat, amelyeket az új virtuális gép használni fog.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és a vNet létrehozása

Hozza létre a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vNet és alhálózatát.

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy **mySubNet**nevű alhálózatot az erőforráscsoport **myResourceGroup**, és az alhálózati címnek az **10.0.0.0/24**értékre állítja az előtagot.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a vNet. Ez a példa beállítja a **myVnetName**virtuális hálózat nevét, az **USA nyugati**régióját, valamint a virtuális hálózat **10.0.0.0/16**-ra való helyét. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
   Ahhoz, hogy az RDP használatával be tudja jelentkezni a virtuális gépre, rendelkeznie kell egy olyan biztonsági szabállyal, amely engedélyezi az RDP-hozzáférést a 3389-es porton. Mivel az új virtuális gép virtuális merevlemeze egy meglévő speciális virtuális gépről lett létrehozva, a virtuális gép létrehozása után meglévő fiókot használhat a forrásként szolgáló virtuális gépről, amely jogosult az RDP használatával történő bejelentkezésre.
   Ezt a hálózati adapter létrehozása előtt kell elvégezni.  
   Ez a példa a NSG nevét a **myNsg** és az RDP-szabály nevét állítja be a **myRdpRule**értékre.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

További információ a végpontokról és a NSG-szabályokról: [portok megnyitása virtuális gépen az Azure-ban a PowerShell használatával](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/public-ip-addresses.md) és egy hálózati adapter szükséges.

1. Hozza létre a nyilvános IP-címet. Ebben a példában a nyilvános IP-cím neve **myIP**értékre van állítva.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozza létre a hálózati adaptert. Ebben a példában a hálózati adapter neve **myNicName**értékre van állítva. Ez a lépés társítja a korábban a hálózati ADAPTERhez létrehozott hálózati biztonsági csoportot is.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>A virtuális gép nevének és méretének beállítása

Ez a példa a virtuális gép nevét "myVM" értékre állítja, a virtuális gép mérete pedig "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Az operációsrendszer-lemez konfigurálása

1. Állítsa be a feltöltött vagy másolt virtuális merevlemez URI azonosítóját. Ebben a példában a **myOsDisk. vhd** nevű VHD-fájlt egy **myStorageAccount** nevű Storage-fiók tárolja a **myContainer**nevű tárolóban.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adja hozzá az operációsrendszer-lemezt. Ebben a példában az operációsrendszer-lemez létrehozásakor a rendszer a "osDisk" kifejezést fűzi hozzá a virtuális gép nevéhez az operációsrendszer-lemez nevének létrehozásához. Ez a példa azt is megadja, hogy ezt a Windows-alapú virtuális merevlemezt operációsrendszer-lemezként kell csatlakoztatni a virtuális géphez.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Nem kötelező: Ha olyan adatlemezekkel rendelkezik, amelyeket csatlakoztatni kell a virtuális géphez, adja hozzá az adatlemezeket a virtuális merevlemezek URL-címei és a megfelelő logikai egység száma (LUN) használatával.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Storage-fiók használatakor az adatfájlok és az operációs rendszer lemezének URL-címe a következőhöz hasonló: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` . Ezt a portálon keresheti meg a cél Storage tárolóra való tallózással, a másolt operációs rendszerre vagy az adatvhd-re, majd az URL-cím tartalmának másolására.


### <a name="complete-the-vm"></a>A virtuális gép befejezése 

Hozza létre a virtuális gépet az imént létrehozott konfigurációk alapján.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Ha a parancs sikeres volt, a következőhöz hasonló kimenet jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Annak ellenőrzése, hogy a virtuális gép létrejött-e
Az újonnan létrehozott virtuális gépet a [Azure Portal](https://portal.azure.com) **összes szolgáltatás**  >  **virtuális gépe**alatt, vagy a következő PowerShell-parancsok használatával kell látnia:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Következő lépések
Jelentkezzen be az új virtuális gépre. További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md).
