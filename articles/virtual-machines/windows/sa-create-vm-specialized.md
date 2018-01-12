---
title: "Virtuális gép létrehozása az Azure-ban speciális lemezről |} Microsoft Docs"
description: "Hozzon létre egy új virtuális gép lemezcsatlakoztatás egy speciális nem felügyelt, a Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
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
ms.openlocfilehash: 995437f5a4a6fe1bc99bfe7fee555d0ac53101a8
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Olyan virtuális merevlemezről speciális tárfiók a virtuális gép létrehozása

Hozzon létre egy új virtuális Gépet egy speciális nem kezelt lemez csatolása a Powershell használatával az operációs rendszer lemezeként. A speciális lemez egy példányát egy meglévő virtuális Gépet, amely kezeli a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép virtuális merevlemez. 

Erre két lehetősége van:
* [VHD feltöltése](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Egy meglévő Azure virtuális gép virtuális Merevlemezének másolni](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Előkészületek
Ha a PowerShell segítségével, győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modult. A következő parancsot a telepítéshez.

```powershell
Install-Module AzureRM.Compute 
```
További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>1. lehetőség: A speciális virtuális merevlemez feltöltéséhez.

Feltöltheti a virtuális merevlemez eszközzel létrehozott egy helyszíni virtualizációs, például a Hyper-V vagy a virtuális gép egy másik felhőből exportált speciális virtuális gép alapján.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Egy helyszíni virtuális gép vagy egy másik felhőből exportált virtuális merevlemez használatával létrehozott speciális virtuális merevlemez feltöltése Speciális virtuális merevlemez kezeli a felhasználói fiókok, alkalmazások és az eredeti virtuális gép más állapotba adatait. Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket. 
  
  * [Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** generalize a virtuális Gépet a Sysprep használata.
  * Távolítsa el a Vendég virtualizációs eszközök és a virtuális Gépre (azaz a VMware-eszközök) telepített ügynökök.
  * Győződjön meg arról, a virtuális Géphez való lekérésére, az IP-cím és DNS-beállítások DHCP van konfigurálva. Ez biztosítja, hogy a kiszolgáló a Vneten belül egy IP-címet kap, indításkor. 


### <a name="get-the-storage-account"></a>A storage-fiók beszerzése
Kell egy a feltöltött Virtuálisgép-lemezkép tárolásához Azure storage-fiókot. Meglévő tárfiók használata, vagy hozzon létre egy újat. 

A rendelkezésre álló tárfiókok megjelenítéséhez írja be:

```powershell
Get-AzureRmStorageAccount
```

Ha egy meglévő tárfiókot használni szeretne, ugorjon a [a Virtuálisgép-lemezkép feltöltése](#upload-the-vm-vhd-to-your-storage-account) szakasz.

Ha létrehoz egy tárfiókot van szüksége, kövesse az alábbi lépéseket:

1. Az erőforráscsoport, ahol kell létrehozni a tárfiók neve van szüksége. Tudja meg, amelyek az előfizetésében szereplő összes erőforráscsoport, írja be:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Nevű erőforráscsoport létrehozásához **myResourceGroup** a a **USA nyugati régiója** régió, írja be:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy tárfiókot, nevű **mystorageaccount** Ez az erőforráscsoport segítségével a a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmagot:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>A VHD-fájlt feltölti a tárfiók
Használja a [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) parancsmag segítségével feltölti a lemezképet a tárfiókban lévő tárolót. Ez a példa feltölti **myVHD.vhd** a `"C:\Users\Public\Documents\Virtual hard disks\"` tárfiókba nevű **mystorageaccount** a a **myResourceGroup** erőforráscsoportot. A fájl bekerülnek a nevű tárolót **mycontainer** és az új fájlnevet lesz **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Sikeres ellenőrzés esetén, a következőhöz hasonló választ kap:

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

Attól függően, hogy a hálózati kapcsolat és a VHD-fájl mérete a parancs is igénybe vehet igénybe.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>2. lehetőség: Másolja a VHD-t egy meglévő Azure virtuális Gépen

Virtuális merevlemez egy másik tárfiókhoz egy új, ismétlődő virtuális gép létrehozásakor használandó másolhatja.

### <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy Ön:

* Kapcsolatos információkkal rendelkezik a **forrás és cél tárfiókok**. A forrás virtuális gép kell rendelkeznie a tárolási fiók és a tároló neve. Általában a tároló neve lesz **VHD-k**. Szükség van egy cél tárfiókkal. Ha még nem rendelkezik egy, elkészítheti vagy a portál használatával (**több szolgáltatások** > tárfiókok > hozzáadása) vagy a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmag. 
* Letöltötte és telepítette a [AzCopy eszköz](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>A virtuális gép felszabadítása
A virtuális helyet szabadít fel a VHD-másolható felszabadítani. 

* **Portál**: kattintson a **virtuális gépek** > **myVM** > leállítása
* **PowerShell**: használata [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) leállítása (felszabadítása) nevű virtuális gép **myVM** erőforráscsoportban **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

A **állapot** a virtuális gép az Azure portálon módosítja **leállítva** való **leállítva (felszabadítva)**.

### <a name="get-the-storage-account-urls"></a>A storage-fiók URL-címek lekérése
Az URL-címeket, a forrás és cél tárfiók van szüksége. Az URL-címek megjelenését, például: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Ha már ismeri a tárolási fiók és a tároló neve, csak helyettesítheti az adatok az URL-cím létrehozása a szögletes zárójelek között. 

Az Azure portálon vagy az Azure Powershell használatával az URL-cím beszerzése:

* **Portál**: kattintson a  **>**  a **további szolgáltatások** > **Storage-fiókok** > *tárfiók* > **Blobok** és a forrás VHD-fájl valószínűleg a **VHD-k** tároló. Kattintson a **tulajdonságok** a tárolót, és másolja a dokumentum szövegét, címkével **URL-cím**. A forrás- és tárolók URL-címei lesz szüksége. 
* **PowerShell**: használata [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) nevű virtuális gép adatainak megszerzése **myVM** erőforráscsoportban **myResourceGroup**. Az eredmények között, tekintse meg a **tárolási profilban** című rész a **virtuális merevlemez Uri**. Az Uri első része a tároló URL-CÍMÉT, és az utolsó része a virtuális gép operációs rendszer virtuális merevlemez nevét.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>A tárelérési kulcsok beszerzése
Keresése a tárelérési kulcsokat a forrás és cél storage-fiókok. Tárelérési kulcsok kapcsolatos további információkért lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

* **Portál**: kattintson a **további szolgáltatások** > **tárfiókok** > *tárfiók*  >  **Hívóbetűk**. Másolja a vágólapra a kulcsot címkézve **key1**.
* **PowerShell**: használata [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) a kulcsot a tárfiók eléréséhez **mystorageaccount** erőforráscsoportban **myResourceGroup**. Másolja a vágólapra a kulcsot feliratú **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Másolja a VHD-
AzCopy alkalmazó tárfiókok között másolhatja. A cél a tároló Ha a megadott tároló nem létezik, az létrehozza meg. 

AzCopy használatához nyisson meg egy parancssort, a helyi számítógépen, és lépjen abba a mappába, amelyen telepítve van-e az AzCopy. Hasonló lesz *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Az összes olyan tárolóban fájl másolásához használja a **/S** váltani. Ez az operációs rendszer virtuális Merevlemezt és az adatok lemezeket másolása, ha ugyanabban a tárolóban vannak is használható. Ez a példa bemutatja, hogyan másolhatja az összes, a fájlok a tároló **mysourcecontainer** tárfiókban **mysourcestorageaccount** a tárolóhoz **mydestinationcontainer**a a **mydestinationstorageaccount** tárfiók. A saját cserélje le a storage-fiókok és a tároló nevét. Cserélje le `<sourceStorageAccountKey1>` és `<destinationStorageAccountKey1>` saját kulcsokkal.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Ha szeretné egy adott VHD másolja a tárolóban lévő több fájl, a fájl nevét a /Pattern kapcsoló használatával is megadhatja. Ebben a példában csak az nevű fájlt **myFileName.vhd** kerülnek.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Amikor elkészült, alábbihoz hasonló üzenet jelenik meg:

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
* AZCopy, használatakor, ha a hiba "A kiszolgáló nem tudta hitelesíteni a kérelmet", győződjön meg arról, hogy az engedélyezési fejléc értékének formátuma helyes többek között az aláírás. Ha a kulcs 2 vagy a másodlagos kulcsot használ, próbálja meg az 1. vagy elsődleges kulcs.

## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Az új virtuális gép által használt hálózati és más Virtuálisgép-erőforrások létrehozásához szükséges.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és a virtuális hálózat létrehozása

Hozza létre a virtuális hálózat és az alhálózati a [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

1. Hozza létre az alhálózatot. Ez a példa-alhálózatot hoz létre nevű **mySubNet**, erőforráscsoportban **myResourceGroup**, és beállítja a cím alhálózati előtagját **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. A virtuális hálózat létrehozása. Ez a példa beállítja a virtuálishálózat-névnek kell lennie **myVnetName**, a helyet, **USA nyugati régiója**, és a virtuális hálózat címelőtag **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és a hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. A nyilvános IP-cím létrehozása. Ebben a példában a nyilvános IP-cím neve értéke **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozzon létre a hálózati adaptert. Ebben a példában a hálózati adapter neve legyen **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Nem fogja tudni jelentkezzen be a virtuális gép RDP Funkciót használnak, szüksége, hogy egy biztonsági szabály, amely lehetővé teszi a hozzáférést RDP a 3389-es porton. Mivel az új virtuális gép virtuális merevlemez létrehozásának egy meglévő speciális virtuális gép, miután a virtuális gép létrehozása után használható a forrás virtuális gépről, akinek engedélye RDP használatával jelentkezzen be egy meglévő fiókkal.
Ebben a példában a NSG nevének beállítása **myNsg** és az RDP-szabály neve a **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Végpontok és NSG-szabályok kapcsolatos további információkért lásd: [az Azure PowerShell használatával egy virtuális gép portok megnyitása](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="set-the-vm-name-and-size"></a>Adja meg a virtuális gép neve és mérete

Ez a példa beállítja a virtuális gép nevét, a "myVM" és "Standard_A2" a virtuális gép méretét.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurálhatja az operációsrendszer-lemez

1. Az URI be, amely feltölteni, vagy másolja a VHD-t. Ebben a példában a VHD-fájl nevű **myOsDisk.vhd** nevű tárfiók maradjanak **myStorageAccount** nevű tároló **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adja hozzá az operációsrendszer-lemezképet. Ebben a példában az operációsrendszer-lemez létrehozásakor "osDisk" kifejezés appened a Virtuálisgép-névre létrehozni az operációsrendszer-lemez neve. Ebben a példában is megadja, hogy a Windows-alapú virtuális Merevlemezt kell csatolni a virtuális Gépet, az operációs rendszer lemezeként.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Nem kötelező: Ha adatlemezt, amelyek csatlakoztatva kell lennie a virtuális géphez, adja hozzá az adatlemezek adatok VHD-k URL-címei és a megfelelő logikai egységen (Lun) használatával.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

A storage-fiók használata esetén az adatokat és operációsrendszer-lemez URL-címek kinéznie: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Található ez a portálon keresse meg a célként megadott tárolási tárolóhoz, az operációs rendszer vagy a virtuális merevlemez másolt adatok majd majd másolja az URL-címet a tartalmát.


### <a name="complete-the-vm"></a>Végezze el a virtuális gép 

A virtuális gép létrehozása használatával az imént létrehozott konfigurációkat.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Ha ez a parancs sikeres, megjelenik a kimenet ehhez hasonló:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Megtekintheti az újonnan létrehozott virtuális gép vagy a a [Azure-portálon](https://portal.azure.com), a **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gép, navigáljon a virtuális gép a [portal](https://portal.azure.com), kattintson a **Connect**, és nyissa meg a távoli asztal RDP-fájlt. A fiók hitelesítő adatait az eredeti virtuális gép jelentkezzen be az új virtuális gép használja. További információkért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md).

