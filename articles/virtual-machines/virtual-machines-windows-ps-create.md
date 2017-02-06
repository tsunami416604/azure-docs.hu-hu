---
title: "Azure virtuális gép létrehozása a PowerShell használatával | Microsoft Docs"
description: "Az Azure PowerShell és az Azure Resource Manager használatával egyszerűen létrehozhat egy új, Windows Server rendszerű virtuális gépet."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: 701a5515cd1d52f7ca8d3562dabcdf0e4d31183d


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Windowsos virtuális gép létrehozása a Resource Manager és a PowerShell használatával
Ez a cikk bemutatja, hogyan hozhat létre gyorsan egy Windows Server rendszert futtató Azure virtuális gépet és az ehhez szükséges erőforrásokat a [Resource Manager](../azure-resource-manager/resource-group-overview.md) és a PowerShell használatával. 

Egy virtuális gép létrehozásához a cikkben található összes lépést el kell végeznie, amelyek összesen nagyjából 30 percet vesznek igénybe. A parancsokban szereplő minta paraméterértékeket cserélje le olyan nevekkel, amelyek az Ön saját környezetében értelmesek.

## <a name="step-1-install-azure-powershell"></a>1. lépés: Az Azure PowerShell telepítése
Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="step-2-create-a-resource-group"></a>2. lépés: Erőforráscsoport létrehozása
Mivel egy erőforráscsoportnak kell tartalmaznia valamennyi erőforrást, ezért először ezt hozzuk létre.  

1. Szerezzen be egy listát az összes elérhető helyről, ahol erőforrásokat lehet létrehozni.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. Állítsa be az erőforrások helyét. Ez a parancs a **centralus**-t állítja be az erőforrások helyeként.
   
    ```powershell
    $location = "centralus"
    ```
3. Hozzon létre egy erőforráscsoportot. Ez a parancs a korábban beállított helyen létrehoz egy **myResourceGroup** elnevezésű erőforráscsoportot.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>3. lépés: Tárfiók létrehozása
A létrehozott virtuális gép által használt virtuális merevlemez tárolásához egy [tárfiók](../storage/storage-introduction.md) szükséges. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

1. Ellenőrizze, hogy a tárfióknév neve egyedi-e. Ez a parancs ellenőrzi a **myStorageAccount** nevet.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Ha ez a parancs **Igaz** eredményt ad, a választott név egyedi az Azure-ban. 
2. Hozza létre a tárfiókot.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>4. lépés: Virtuális hálózat létrehozása
Minden virtuális gép egy [virtuális hálózat](../virtual-network/virtual-networks-overview.md) része.

1. Hozzon létre egy alhálózatot a virtuális hálózat számára. Ez a parancs létrehoz egy **mySubnet** elnevezésű alhálózatot 10.0.0.0/24 címelőtaggal.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. Hozza létre a virtuális hálózatot. Ez a parancs az imént létrehozott alhálózat felhasználásával létrehoz egy **myVnet** elnevezésű virtuális hálózatot **10.0.0.0/16** címelőtaggal.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>5. lépés: Nyilvános IP-cím és hálózati adapter létrehozása
A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Hozza létre a nyilvános IP-címet. Ez a parancs létrehoz egy **dinamikus** elosztási módszerrel rendelkező, **myPublicIp** elnevezésű nyilvános IP-címet.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Hozza létre a hálózati adaptert. Ez a módszer létrehoz egy **myNIC** elnevezésű hálózati adaptert.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>6. lépés: Virtuális gép létrehozása
Most, hogy minden a helyére került, ideje létrehozni a virtuális gépet.

1. Futtassa ezt a parancsot a virtuális gép rendszergazdai fióknevének és jelszavának megadásához.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    A jelszónak 12–123 karakter hosszúnak kell lennie, és tartalmaznia kell legalább egy kisbetűt, egy nagybetűt, egy számot és egy különleges karaktert. 
2. Hozza létre a konfigurációs objektumot a virtuális gép számra. Ez a parancsa létrehoz egy **myVmConfig** elnevezésű konfigurációs objektumot, amely meghatározza a virtuális gép nevét és méretét.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    A virtuális gépekhez elérhető méretek listáját lásd: [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek méretei az Azure-ban).
3. Konfigurálja a virtuális gép operációsrendszer-beállításait. Ez a parancs beállítja a virtuális gép számítógépnevét, operációs rendszere típusát és a fiókhoz tartozó hitelesítő adatait.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. Határozza meg a virtuális gép kiépítéséhez használni kívánt rendszerképet. Ez a parancs meghatározza a virtuális géphez használandó Windows Server-rendszerképet. 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    További információ a rendszerképek kiválasztásáról: [Windows virtuális gépek lemezképeinek navigálása és kiválasztása az Azure-ban a PowerShell vagy a parancssori felület használatával ](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Adja hozzá a konfigurációhoz a létrehozott hálózati adaptert.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Adja meg a virtuális gép merevlemezlének nevét és helyét. A virtuális merevlemezfájl tárolása egy tárolóban történik. Ez a parancs az Ön által létrehozott tárfiókban található **vhds/myOsDisk1.vhd** névvel ellátott tárolóban hozza létre a lemezt.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Adja hozzá a virtuális gép konfigurációjához az operációs rendszerre vonatkozó információkat. Cserélje le a **$diskName** értéket az operációs rendszer lemezének nevére. Hozza létre a változót, és adja hozzá a konfigurációhoz a lemezinformációkat.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Végül hozza létre a virtuális gépet.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Következő lépések
* Ha problémák merültek fel az üzembe helyezés során, a következő lépésről az [Azure-telepítések gyakori hibáinak elhárítása az Azure Resource Manager használatával](../azure-resource-manager/resource-manager-common-deployment-errors.md) eljárásokat ismertető cikkben talál információt
* A létrehozott virtuális gép felügyeletét a következő cikk ismerteti: [Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Virtuális gépek felügyelete az Azure Resource Manager és a PowerShell használatával).
* A virtuális gépek sablonokkal történő létrehozásáról a következő cikkben találhat hasznos információkat: [Create a Windows virtual machine with a Resource Manager template](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows rendszerű virtuális gép létrehozása egy Resource Manager-sablonnal)




<!--HONumber=Feb17_HO1-->


