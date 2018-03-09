---
title: "Helyszíni általánosított virtuális Merevlemezt a felügyelt Azure virtuális gép létrehozása |} Microsoft Docs"
description: "Általánosított virtuális merevlemez feltöltése az Azure-ba, és ennek segítségével létrehozott egy új virtuális gépet, a Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: cynthn
ms.openlocfilehash: 2e78ecf6bd281bd5d30f59413789eb1e6fc7b5bc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Egy általánosított virtuális merevlemez feltöltéséhez, és beállítsa, hogy létrehozott egy új virtuális gépet az Azure-ban

Ez a témakör végigvezeti egy általánosított virtuális gép virtuális merevlemez feltöltése az Azure-ba, képet készíteni a VHD-t, és hozzon létre egy új virtuális Gépet erről a képről a PowerShell használatával. Egy helyszíni virtualizálási eszköz vagy egy másik felhőben az exportált virtuális merevlemez feltöltése Használatával [kezelt lemezek](managed-disks-overview.md) VM egyszerűbbé teszi az új virtuális gép, és jobb rendelkezésre állást biztosít, amikor a virtuális gép rendelkezésre állási csoportba helyez. 

Ha egy minta parancsfájlt használni kívánt, lásd: [mintaparancsfájl virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Előkészületek

- Kövesse a virtuális merevlemez feltöltése az Azure-ba, mielőtt [Windows VHD vagy VHDX az Azure-bA feltöltendő előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Felülvizsgálati [kezelt lemezek az áttelepítés megtervezése](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) az áttelepítés megkezdése előtt [kezelt lemezek](managed-disks-overview.md).
- Győződjön meg arról, hogy rendelkezik-e a legújabb verzióját a AzureRM.Compute PowerShell-modult. A következő parancsot a telepítéshez.

    ```powershell
    Install-Module AzureRM.Compute -RequiredVersion 2.6.0
    ```
    További információkért lásd: [Azure PowerShell Versioning](/powershell/azure/overview).


## <a name="generalize-the-windows-vm-using-sysprep"></a>A Windows virtuális gép Sysprep generalize

A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).

Győződjön meg arról, hogy a Sysprep által a gépen futó kiszolgálói szerepkörök támogatottak. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Ha előtt a virtuális merevlemez feltöltése az Azure-bA az első alkalommal futtatja a Sysprep, győződjön meg arról, hogy [a virtuális gép előkészített](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a Sysprep futtatása előtt. 
> 
> 

1. Jelentkezzen be a Windows rendszerű virtuális gép.
2. Nyissa meg a parancssort rendszergazdaként. Lépjen be **%windir%\system32\sysprep**, majd futtassa a `sysprep.exe`.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg a rendszer Out-of-Box élmény (OOBE)**, és győződjön meg arról, hogy a **Generalize** jelölőnégyzet be van jelölve.
4. A **leállítási beállítások**, jelölje be **leállítási**.
5. Kattintson az **OK** gombra.
   
    ![Indítsa el a Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. A Sysprep befejezését követően a virtuális gép leáll. Ne indítsa újra a virtuális Gépet.



## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Ha még nem rendelkezik PowerShell 1.4-es verzió vagy újabb operációs rendszerre telepíteni, olvassa el [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

1. Nyissa meg az Azure PowerShell, és jelentkezzen be az Azure-fiókjával. Egy előugró ablak nyílik meg Azure-fiók hitelesítő adatait.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Az előfizetési azonosítók az elérhető előfizetések beolvasása.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Állítsa be a megfelelő előfizetés az előfizetés-azonosítójával. Cserélje le  *<subscriptionID>*  , azonosító: a megfelelő előfizetés.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>A storage-fiók beszerzése
Kell egy a feltöltött Virtuálisgép-lemezkép tárolásához Azure storage-fiókot. Meglévő tárfiók használata, vagy hozzon létre egy újat. 

Fog használni a virtuális Merevlemezt a virtuális gépek kezelt lemez létrehozása, ha a tárfiók helyének meg kell egyeznie a hely, ahol létrehozni a virtuális Gépet.

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

    Nevű erőforráscsoport létrehozásához **myResourceGroup** a a **USA keleti régiója** régió, írja be:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "East US"
    ```

2. Hozzon létre egy tárfiókot, nevű **mystorageaccount** Ez az erőforráscsoport segítségével a a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmagot:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "East US"`
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName érvényes értékei:
   
   * **Standard_LRS** -helyileg redundáns tárolás. 
   * **Standard_ZRS** -redundáns tárolási zóna.
   * **Standard_GRS** -földrajzi redundáns tárolás. 
   * **Standard_RAGRS** -írásvédett georedundáns redundáns tárolás. 
   * **Premium_LRS** -prémium helyileg redundáns tárolás. 

## <a name="upload-the-vhd-to-your-storage-account"></a>A VHD-fájlt feltölti a tárfiók

Használja a [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) parancsmaggal töltse fel a VHD-t a tárfiókban lévő tárolót. Ebben a példában a fájl feltöltését *myVHD.vhd* a *"C:\Users\Public\Documents\Virtual merevlemezek\"*  tárfiókba nevű *mystorageaccount* a a *myResourceGroup* erőforráscsoportot. A fájl bekerülnek a nevű tárolót *mycontainer* és az új fájlnevet lesz *myUploadedVHD.vhd*.

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

Attól függően, hogy a hálózati kapcsolat és a VHD-fájl mérete a parancs is igénybe vehet igénybe

Mentse a **cél URI** elérési későbbi használatra, ha egy felügyelt lemezes vagy a feltöltött virtuális merevlemez egy új virtuális gép létrehozásához.

### <a name="other-options-for-uploading-a-vhd"></a>Más beállításokat a virtuális merevlemez feltöltése
 
 
Feltöltheti a virtuális merevlemez a tárfiókhoz, az alábbiak egyikének használatával:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Az Azure Storage Explorer feltöltése a BLOB](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export szolgáltatás REST API-referencia](https://msdn.microsoft.com/library/dn529096.aspx)
-   Ajánlott Import/Export szolgáltatás használata, ha becsült a 7 napnál hosszabb idő feltöltése. Használhat [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) becsléséhez adatok méretét és átviteli egység időpontját. 
    Importálási/exportálási segítségével másolja egy standard szintű tárfiókot. Szüksége lesz a prémium szintű storage-fiókra egy eszköz, például az AzCopy standard tárolási másolja.

> [!IMPORTANT]
> Ha a virtuális merevlemez feltöltése az Azure-bA AzCopy használ, győződjön meg arról, állított [/BlobType:page](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) futtatása előtt töltse fel a parancsfájlt. Ha a cél egy blobot, és ez a beállítás nincs megadva, alapértelmezés szerint az AzCopy egy blokkblob hoz létre.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Hozzon létre egy felügyelt képre a feltöltött virtuális merevlemezből 

Hozzon létre egy felügyelt képre az operációs rendszer általánosított példányát VHD használatával. Cserélje le az értékeket a saját adatait.


1.  Első lépésként állítsa be a következő általános paramétereket:

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    $vmSize = "Standard_DS1_v2"
    $location = "East US" 
    $imageName = "yourImageName"
    ```

4.  A lemezkép az operációs rendszer általánosított példányát VHD létrehozása.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $urlOfUploadedImageVhd
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozza létre a virtuális hálózat és az alhálózati a [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

1. Hozza létre az alhálózatot. Ez a példa-alhálózatot hoz létre nevű *mySubnet* a címelőtagot rendelkező *10.0.0.0/24*.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Ebben a példában egy virtuális hálózatot nevű hoz létre *myVnet* a címelőtagot rendelkező *10.0.0.0/16*.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Hozzon létre egy nyilvános IP-cím és a hálózati kapcsolat

A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozzon létre egy nyilvános IP-címet. Ez a példa létrehoz egy nyilvános IP-cím nevű *myPip*. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Hozzon létre a hálózati adaptert. Ez a példa létrehoz egy hálózati adapter nevű **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása

Nem fogja tudni jelentkezzen be a virtuális gép RDP Funkciót használnak, meg kell rendelkeznie a hálózati biztonsági szabály (NSG), amely lehetővé teszi a hozzáférést RDP a 3389-es porton. 

Ez a példa létrehoz egy NSG nevű *myNsg* nevű szabályt tartalmaz, amelyek *myRdpRule* RDP-forgalmát, amely engedélyezi a 3389-es porton keresztül. Az NSG-k kapcsolatos további információkért lásd: [az Azure PowerShell használatával egy virtuális gép portok megnyitása](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>A virtuális hálózat változó létrehozása

Hozzon létre egy változót, a befejezett virtuális hálózat. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>A hitelesítő adatokat lekérni a virtuális géphez

A következő parancsmag megnyit egy ablakot, ahol egy új felhasználónevet és jelszót ehhez a helyi rendszergazda fiókot távoli eléréséhez a virtuális gép módba lép. 

```powershell
$cred = Get-Credential
```

## <a name="add-the-vm-name-and-size-to-the-vm-configuration"></a>A Virtuálisgép-konfiguráció hozzáadása a virtuális gép nevét és méretét.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>A Virtuálisgép-lemezkép beállítása forráslemezkép a új virtuális gép számára

Állítsa be a forrásképből, a felügyelt Virtuálisgép-lemezkép Azonosítóját használja.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Az operációs rendszer konfigurációjának, és adja hozzá a hálózati adaptert.

Adja meg a tároló típusa (PremiumLRS vagy StandardLRS) és az operációs rendszer lemezének mérete. Ebben a példában a fiók típusát állítja *PremiumLRS*, a lemez mérete *128 GB-os* és lemezes gyorsítótárazás *ReadWrite*.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Virtuális gép létrehozása

A tárolt konfiguráció az új virtuális gép létrehozása a **$vm** változó.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Győződjön meg arról, hogy létrejött-e a virtuális gép
Amikor végzett, megjelenik az újonnan létrehozott virtuális gép a [Azure-portálon](https://portal.azure.com) alatt **Tallózás** > **virtuális gépek**, vagy a következő PowerShell-parancsok használatával:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>További lépések

Jelentkezzen be az új virtuális gép, navigáljon a virtuális gép a [portal](https://portal.azure.com), kattintson a **Connect**, és nyissa meg a távoli asztal RDP-fájlt. A fiók hitelesítő adatait az eredeti virtuális gép jelentkezzen be az új virtuális gép használja. További információkért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

