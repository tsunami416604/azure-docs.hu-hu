---
title: Virtuális gép létrehozása speciális lemezről az Azure-ban
description: Hozzon létre egy új virtuális gép csatlakoztatásával egy speciális nem felügyelt lemez, az Erőforrás-kezelő telepítési modell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073346"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Virtuális gép létrehozása egy speciális virtuális merevlemezből egy tárfiókban

Hozzon létre egy új virtuális gép csatlakoztatásával egy speciális nem felügyelt lemez, mint az operációs rendszer lemez powershell használatával. A speciális lemez egy meglévő virtuális gép virtuális merevlemezének másolata, amely az eredeti virtuális gép felhasználói fiókjait, alkalmazásait és egyéb állapotadatait tartja karban. 

Erre két lehetősége van:
* [VHD feltöltése](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Meglévő Azure-virtuális gép virtuális merevlemezének másolása](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>1. lehetőség: Speciális virtuális merevlemez feltöltése

Feltöltheti a virtuális merevlemezt egy speciális virtuális gépről, amelyet egy helyszíni virtualizációs eszközzel, például a Hyper-V-vel vagy egy másik felhőből exportált virtuális géptel hoztak létre.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Feltölthet egy speciális virtuális merevlemezt, amelyet egy helyszíni virtuális gép vagy egy másik felhőből exportált virtuális merevlemez használatával hoztak létre. Egy speciális virtuális merevlemez a felhasználói fiókokat, alkalmazásokat és az eredeti virtuális gép egyéb állapotadatait tartja karban. Ha a virtuális merevlemezt egy új virtuális gép létrehozása ként kívánja használni, győződjön meg arról, hogy a következő lépések befejeződtek. 
  
  * [Készítsen elő egy Windows vHD-t az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) **Ne** általánosítsa a virtuális gép segítségével Sysprep.
  * Távolítson el minden vendég virtualizációs eszközt és ügynököt, amely telepítve van a virtuális gépre (azaz VMware-eszközök).
  * Győződjön meg arról, hogy a virtuális gép úgy van beállítva, hogy lekéri az IP-címét és a DNS-beállításait a DHCP-n keresztül. Ez biztosítja, hogy a kiszolgáló beszerez egy IP-címet a virtuális hálózaton belül, amikor elindul. 


### <a name="get-the-storage-account"></a>A tárfiók beszerezése
A feltöltött virtuálisgép-lemezkép tárolásához szüksége van egy tárfiókra az Azure-ban. Használhatja a meglévő tárfiókot, vagy hozzon létre egy újat. 

A rendelkezésre álló tárfiókok megjelenítéséhez írja be a következőt:

```powershell
Get-AzStorageAccount
```

Ha egy meglévő tárfiókot szeretne használni, folytassa a Virtuálisgép-lemezkép feltöltése szakasz.

Ha tárfiókot kell létrehoznia, kövesse az alábbi lépéseket:

1. Szüksége van annak az erőforráscsoportnak a nevére, amelyhez a tárfiókot létre kell hozni. Az előfizetésben lévő összes erőforráscsoport megkereséséhez írja be a következőt:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Ha az **USA nyugati régiójában** létre szeretne hozni egy **myResourceGroup** nevű erőforráscsoportot, írja be a következőt:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Hozzon létre egy **mystorageaccount** nevű tárfiókot ebben az erőforráscsoportban a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>A VHD feltöltése a tárfiókba
Az [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) parancsmag használatával töltse fel a lemezképet a tárfiók ban lévő tárolóba. Ez a példa feltölti a **myVHD.vhd** fájlt egy `"C:\Users\Public\Documents\Virtual hard disks\"` **myResourceGroup** **erőforráscsoport myResourceAccount nevű tárfiókba.** A fájl a **mycontainer** nevű tárolóba kerül, és az új fájlnév **a myUploadedVHD.vhd**lesz.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Ha sikeres, a következőhöz hasonló választ kap:

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

A hálózati kapcsolattól és a virtuális merevlemez-fájl méretétől függően ez a parancs eltarthat egy ideig.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>2. lehetőség: A virtuális merevlemez másolása egy meglévő Azure virtuális gépről

A virtuális merevlemez t átmásolhat egy másik tárfiókba, amelyet egy új, ismétlődő virtuális gép létrehozásakor használhat.

### <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy:

* Információa forrás- **és céltárfiókokról.** A forrás virtuális gép, rendelkeznie kell a tárfiók és a tároló nevét. Általában a tároló neve **lesz vhds**. Azt is meg kell, hogy egy cél tárfiók. Ha még nem rendelkezik ilyen, létrehozhat egyet a portál **(Minden szolgáltatás** > Storage-fiókok > hozzáadás) vagy a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával. 
* Az [AzCopy eszköz](../../storage/common/storage-use-azcopy.md)letöltése és telepítése . 

### <a name="deallocate-the-vm"></a>A virtuális gép felszabadítása
A virtuális gép felszabadítása, amely felszabadítja a virtuális merevlemez másolni. 

* **Portál:** Kattintson **a virtuális gépek** > **myVM** > Stop
* **Powershell**: A [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) segítségével állítsa le (szabadítsa fel) a **myVM** nevű virtuális gép nevét a **myResourceGroup**erőforráscsoportban.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

A virtuális gép **állapota** az Azure Portalon **leállítva** **(felszabadított)** állapotról változik.

### <a name="get-the-storage-account-urls"></a>A tárfiók URL-címének beszereznie
Szüksége van a forrás- és a céltárfiókok URL-címeire. Az URL-ek így `https://<storageaccount>.blob.core.windows.net/<containerName>/`néznek ki: . Ha már ismeri a tárfiókot és a tároló nevét, egyszerűen cserélje le az adatokat a zárójelek között az URL-cím létrehozásához. 

Az Azure Portalon vagy az Azure Powershellben lejuthat az URL-cím:

* **Portál:** Kattintson **>** az **Összes szolgáltatás** > **tárfiókok** > *tárfiók* > **blobok** és a forrás VHD-fájl valószínűleg a **vhds** tárolóban. Kattintson a tároló **tulajdonságai** elemre, és másolja a **szövegurl-címét.** A forrás- és a céltárolók URL-címeire is szüksége lesz. 
* **Powershell**: A [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) segítségével lekéri a **myVM** nevű virtuális gép adatait a **myResourceGroup**erőforráscsoportban. Az eredmények, nézd meg a **Tárolási profil** szakaszban a **Vhd Uri.** Az Uri első része a tároló URL-címe, az utolsó pedig a virtuális gép operációs rendszer virtuális merevlemezének neve.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>A tároló-hozzáférési kulcsok beszerezése
Keresse meg a forrás- és a céltárfiókok hozzáférési kulcsait. A hozzáférési kulcsokról további információt az [Azure storage-fiókok – készülődés című témakörben](../../storage/common/storage-create-storage-account.md)talál.

* **Portál**: Kattintson az **Összes szolgáltatás** > **Tárfiókok** > *tárfiók* > **hozzáférési kulcsok**elemre. Másolja a **key1**címkével ellátott kulcsot.
* **Powershell**: A [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) segítségével lekéri a storage-kulcs a tárfiók **mystorageaccount** az erőforráscsoport **ban myResourceGroup**. Másolja a keycímkével ellátott **billentyűt1.**

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>A virtuális merevlemez másolása
Az AzCopy segítségével fájlokat másolhat a tárfiókok között. A céltároló, ha a megadott tároló nem létezik, akkor jön létre az Ön számára. 

Az AzCopy használatához nyisson meg egy parancssort a helyi számítógépen, és keresse meg azt a mappát, ahol az AzCopy telepítve van. Hasonló lesz a *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy mappához.* 

A tárolóban lévő összes fájl másolásához használja a **/S** kapcsolót. Ez az operációs rendszer virtuális merevlemezének és az összes adatlemeznek másolására használható, ha ugyanabban a tárolóban vannak. Ez a példa bemutatja, hogyan másolhatja az összes fájlt a tárolóban **mysourcecontainer** a storage-fiók **mysourcestorageaccount** a tároló **mydestinationcontainer** a **mydestinationstorageaccount** tárfiókban. Cserélje le a tárfiókok és tárolók nevét a sajátjára. Cserélje `<sourceStorageAccountKey1>` `<destinationStorageAccountKey1>` ki, és a saját kulcsokat.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Ha csak egy adott virtuális merevlemezt szeretne másolni egy több fájlt tartalmazó tárolóba, a /Pattern kapcsolóval is megadhatja a fájl nevét. Ebben a példában csak a **myFileName.vhd** nevű fájl lesz másolva.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Amikor elkészült, akkor kap egy üzenetet, hogy néz ki, mint:

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
* Az AZCopy használatakor, ha a "Kiszolgáló nem tudta hitelesíteni a kérelmet" hibaüzenet jelenik meg, ellenőrizze, hogy az engedélyezési fejléc értéke megfelelően van-e kialakítva az aláírással együtt. Ha a Key 2 vagy a másodlagos tárolókulcsot használja, próbálja meg az elsődleges vagy az első tárolókulcsot használni.

## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Létre kell hoznia a hálózati és egyéb virtuális gép erőforrásait az új virtuális gép által használandó.

### <a name="create-the-subnet-and-vnet"></a>Az alhálózat és a virtuális hálózat létrehozása

Hozza létre a [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)virtuális hálózatát és alhálózatát.

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy **mySubNet**nevű alhálózatot a **myResourceGroup**erőforráscsoportban, és az alhálózati cím előtagot **10.0.0.0/24-re állítja.**
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Ez a példa a virtuális hálózat nevét **myVnetName-re**, az **USA nyugati helyére,** a virtuális hálózat címelőtagját **pedig 10.0.0.0/16-ra állítja.** 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
   Ahhoz, hogy az RDP használatával be tudjon jelentkezni a virtuális gépre, rendelkeznie kell egy biztonsági szabállyal, amely lehetővé teszi az RDP-hozzáférést a 3389-es porton. Mivel az új virtuális gép virtuális merevlemeze egy meglévő speciális virtuális gépről jött létre, a virtuális gép létrehozása után használhatja a meglévő fiókot a forrás virtuális gépről, amely engedéllyel rendelkezik az RDP használatával történő bejelentkezéshez.
   Ezt a hálózati csatoló létrehozása előtt kell elvégezni.  
   Ez a példa az NSG-nevet **myNsg-re,** az RDP-szabály nevét pedig **myRdpRule-re állítja.**

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

A végpontokról és az NSG-szabályokról további információt a [Portok megnyitása az Azure-ban a PowerShell használatával című témakörben talál.](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozza létre a nyilvános IP-címet. Ebben a példában a nyilvános IP-cím neve myIP .In this example, the public IP address name is set to **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozza létre a hálózati adaptert. Ebben a példában a hálózati adapter neve **myNicName**. Ez a lépés a korábban létrehozott hálózati biztonsági csoportot is társítja ezzel a hálózati adapterrel.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>A virtuális gép nevének és méretének beállítása

Ebben a példában a virtuális gép nevét "myVM", a virtuális gép méretét pedig "Standard_A2" adja meg.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Az operációs rendszer lemezének konfigurálása

1. Állítsa be a feltöltött vagy másolt virtuális merevlemez URI-ját. Ebben a példában a **myOsDisk.vhd** nevű virtuális merevlemez-fájl egy **myStorageAccount** nevű tárfiókban van tárolva egy **myContainer**nevű tárolóban.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adja hozzá az operációs rendszer lemezét. Ebben a példában az operációs rendszer lemezének létrehozásakor az "osDisk" kifejezés kerül a virtuális gép nevéhez az operációs rendszer lemeznevének létrehozásához. Ez a példa azt is meghatározza, hogy ez a Windows-alapú virtuális merevlemez kell csatolni a virtuális gép, mint az operációs rendszer lemeze.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Nem kötelező: Ha adatlemezekkel rendelkezik, amelyeket a virtuális géphez kell csatolni, adja hozzá az adatlemezeket az adatvirtuális merevlemezek URL-címei és a megfelelő logikai egységszám (Lun) használatával.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Tárfiók használatakor az adat- és az operációs rendszer lemezURL-címei a következőkkel például a következőkre néznek ki: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Ezt megtalálhatja a portálon a céltárolótárolóra való böngészéssel, a másolt operációs rendszerre vagy adatvirtuális merevlemezre kattintva, majd az URL tartalmának másolásával.


### <a name="complete-the-vm"></a>A virtuális gép befejezése 

Hozza létre a virtuális gép az imént létrehozott konfigurációk használatával.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Ha ez a parancs sikeres volt, a következő hez hasonló kimenet jelenik meg:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>A virtuális gép létrehozásának ellenőrzése
Az újonnan létrehozott virtuális gépet az [Azure Portalon](https://portal.azure.com)kell látnia, a **Minden szolgáltatás** > **virtuális gép e**és a következő PowerShell-parancsok használatával:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>További lépések
Jelentkezzen be az új virtuális gépre. További információt a [Csatlakozás és bejelentkezés Windows rendszerű Azure-alapú virtuális gépre](connect-logon.md)című témakörben talál.

