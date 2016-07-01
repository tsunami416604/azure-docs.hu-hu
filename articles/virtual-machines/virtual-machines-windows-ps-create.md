<properties
    pageTitle="Azure virtuális gép létrehozása a PowerShell használatával | Microsoft Azure"
    description="Az Azure PowerShell és az Azure Resource Manager használatával egyszerűen létrehozhat egy új, Windows Server rendszerű virtuális gépet."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/02/2016"
    ms.author="davidmu"/>

# Windowsos virtuális gép létrehozása a Resource Manager és a PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre gyorsan egy Windows Server rendszert futtató Azure virtuális gépet és az ehhez szükséges erőforrásokat a [Resource Manager](../resource-group-overview.md) és a PowerShell használatával. 

Egy virtuális gép létrehozásához a cikkben található összes lépést el kell végeznie, amelyek összesen nagyjából 30 percet vesznek igénybe.

## 1. lépés: Az Azure PowerShell telepítése

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és az Azure-fiókba való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).
        
## 2. lépés: Erőforráscsoport létrehozása

Először is létre kell hoznia egy erőforráscsoportot.

1. Szerezzen be egy listát az összes elérhető helyről, ahol erőforrásokat lehet létrehozni.

        Get-AzureLocation | sort Name | Select Name
        
    Ennek nagyjából a következőképpen kell kinéznie:
    
        Name
        ----
        Australia East
        Australia Southeast
        Brazil South
        Central India
        Central US
        East Asia
        East US
        East US 2
        Japan East
        Japan West
        North Central US
        North Europe
        South Central US
        South India
        Southeast Asia
        West Europe
        West India
        West US

2. Cserélje le a **$locName** értéket egy helyre a listából. Hozza létre a változót.

        $locName = "Central US"
        
3. Cserélje le a **$rgName** értéket az új erőforráscsoport nevére. Hozza létre a változót és az erőforráscsoportot.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## 3. lépés: Tárfiók létrehozása

A létrehozott virtuális gép által használt virtuális merevlemez tárolásához egy [tárfiók](../storage/storage-introduction.md) szükséges.

1. Cserélje le a **$stName** értéket a tárfiók nevére. Ellenőrizze, hogy a név egyedi-e.

        $stName = "mystorage1"
        Test-AzureName -Storage $stName

    Ha ez a parancs **Hamis** eredményt ad, a választott név egyedi az Azure-ban. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
    
2. Futtassa a parancsot a tárfiók létrehozásához.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## 4. lépés: Virtuális hálózat létrehozása

Minden virtuális gép egy [virtuális hálózat](../virtual-network/virtual-networks-overview.md) része.

1. Cserélje le a **$subnetName** értéket az alhálózat nevére. Hozza létre a változót és az alhálózatot.
        
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Cserélje le a **$vnetName** értéket a virtuális hálózat nevére. Hozza létre a változót és a virtuális hálózatot az alhálózattal együtt.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    Használjon olyan értékeket, amelyek az alkalmazás és a környezet szempontjából jelentéssel bírnak.
        
## 5. lépés: Nyilvános IP-cím és hálózati adapter létrehozása

A virtuális hálózaton a virtuális géppel való kommunikáció biztosításához egy [nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md) és egy hálózati adapter szükséges.

1. Cserélje le az **$ipName** értéket a nyilvános IP-cím nevére. Hozza létre a változót és a nyilvános IP-címet.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Cserélje le a **$nicName** értéket a hálózati adapter nevére. Hozza létre a változót és a hálózati adaptert.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## 6. lépés: Virtuális gép létrehozása

Most, hogy minden a helyére került, ideje létrehozni a virtuális gépet.

1. Futtassa a parancsot a virtuális gép rendszergazdai fióknevének és jelszavának megadásához.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    A jelszónak 8–123 karakter hosszúnak kell lennie, és tartalmaznia kell legalább hármat a következők közül: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. 
        
2. Cserélje le a **$vmName** értéket a virtuális gép nevére. Hozza létre a változót és a virtuálisgép-konfigurációt.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    A virtuális gépekhez elérhető méretek listáját lásd: [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) (Virtuális gépek méretei az Azure-ban).
    
3. Cserélje le a **$compName** értéket a virtuális gép számítógépnevére. Hozza létre a változót, és adja hozzá a konfigurációhoz az operációs rendszerre vonatkozó információkat.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Határozza meg a virtuális gép kiépítéséhez használni kívánt rendszerképet. 

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    További információ a rendszerképek kiválasztásáról: [Navigate and select Windows virtual machine images in Azure with PowerShell or the CLI](virtual-machines-windows-cli-ps-findimage.md) (Windows virtuális gépek lemezképeinek navigálása és kiválasztása az Azure-ban a PowerShell vagy a parancssori felület használatával).
        
5. Adja hozzá a konfigurációhoz a létrehozott hálózati adaptert.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Cserélje le a **$blobPath** értéket annak a tárnak az elérési útjára és fájlnevére, amelyet a virtuális merevlemez használni fog. A virtuálismerevlemez-fájlt általában egy tároló tartalmazza, például a **vhds/WindowsVMosDisk.vhd**. Hozza létre a változókat.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Cserélje le a **$diskName** értéket az operációs rendszer lemezének nevére. Hozza létre a változót, és adja hozzá a konfigurációhoz a lemezinformációkat.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Végül hozza létre a virtuális gépet.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Az Azure portálon látszania kell az erőforráscsoportnak és a hozzá tartozó összes erőforrásnak, a PowerShell-ablakban pedig egy sikeres állapotnak:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## További lépések

- Ha problémák merültek fel az üzembe helyezés során, a következő lépésről lásd: [Troubleshooting resource group deployments with Azure Portal](../resource-manager-troubleshoot-deployments-portal.md) (Erőforráscsoportok üzemelő példányainak hibaelhárítása az Azure portálon)
- A létrehozott virtuális gép felügyeletét a következő cikk ismerteti: [Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-ps-manage.md) (Virtuális gépek felügyelete az Azure Resource Manager és a PowerShell használatával).
- A virtuális gépek sablonokkal történő létrehozásáról a következő cikkben találhat hasznos információkat: [Create a Windows virtual machine with a Resource Manager template](virtual-machines-windows-ps-template.md) (Windows rendszerű virtuális gép létrehozása egy Resource Manager-sablonnal)



<!--HONumber=Jun16_HO2--->


