---
title: Virtuálisgép-méretezési csoport létrehozása a PowerShell-lel | Microsoft Docs
description: Virtuálisgép-méretezési csoport létrehozása a PowerShell-lel
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2016
ms.author: davidmu

---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Windowsos virtuálisgép-méretezési csoport létrehozása az Azure PowerShell-lel
Ezek a lépések behelyettesítésen alapuló megközelítéssel hoznak létre egy Azure-beli virtuálisgép-méretezési csoportot. További információ a méretezési készletekről: [Virtuálisgép-méretezési csoportok áttekintése](virtual-machine-scale-sets-overview.md).

A cikkben található lépések elvégzése nagyjából 30 percet vesz igénybe.

## <a name="step-1:-install-azure-powershell"></a>1. lépés: Az Azure PowerShell telepítése
Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="step-2:-create-resources"></a>2. lépés: Erőforrások létrehozása
Hozza létre az új méretezési csoporthoz szükséges erőforrásokat.

### <a name="resource-group"></a>Erőforráscsoport
A virtuálisgép-méretezési csoportoknak egy erőforráscsoporton belül kell lenniük.

1. Kérjen le egy listát az összes elérhető helyről és támogatott szolgáltatásról:
   
        Get-AzureLocation | Sort Name | Select Name, AvailableServices
   
    Ennek nagyjából a következő példához hasonlóan kell kinéznie:
   
        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}
2. Válassza ki az Ön számára legalkalmasabb helyet, cserélje le a **$locName** értéket a hely nevére, majd hozza létre a következő változót:
   
        $locName = "location name from the list, such as Central US"
3. Cserélje le a **$rgName** értéket a használni kívánt új erőforráscsoport nevére, majd hozza létre a következő változót: 
   
        $rgName = "resource group name"
4. Hozza létre az erőforráscsoportot:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Ennek nagyjából a következő példához hasonlóan kell kinéznie:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Tárfiók
A virtuális gép által a tárfiókban tárolja az operációsrendszer-lemezt és a méretezéshez használt diagnosztikai adatokat. Az ajánlott eljárás az, hogy ha lehetséges, a méretezési csoportban létrehozott minden egyes virtuális géphez tartozzon egy tárfiók. Ha nem lehetséges, akkor tárfiókonként legfeljebb 20 virtuális géppel tervezzen. A cikkben szereplő példa három virtuális gép számára létrehozott három tárfiókot tartalmaz.

1. Cserélje le a **$stName** értéket a tárfiók nevére. Ellenőrizze, hogy a név egyedi-e. 
   
        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName
   
    Ha a válasz **Igaz** eredményt ad, a választott név egyedi.
2. Cserélje le a **$saType** értéket a tárfiók típusára, majd hozza létre a változót:  
   
        $saType = "storage account type"
   
    Lehetséges értékek: Standard_LRS, Standard_GRS, Standard_RAGRS vagy Premium_LRS.
3. Felhasználói fiók létrehozása:
   
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName
   
    Ennek nagyjából a következő példához hasonlóan kell kinéznie:
   
        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
4. Ismételje meg az 1-4. lépést három tárfiók létrehozásához (például myst1, myst2 és myst3).

### <a name="virtual-network"></a>Virtuális hálózat
A méretezési csoportban található virtuális gépekhez szükséges egy virtuális hálózat.

1. Cserélje le a **$subnetName** értéket az alhálózat kívánt nevére a virtuális hálózatban, majd hozza létre a következő változót: 
   
        $subnetName = "subnet name"
2. Az alhálózat-konfiguráció létrehozása:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    A címelőtag eltérő lehet a virtuális hálózatán belül.
3. Cserélje le a **$netName** értéket a virtuális hálózat kívánt nevére, majd hozza létre a következő változót: 
   
        $netName = "virtual network name"
4. Virtuális hálózat létrehozása:
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="public-ip-address"></a>Nyilvános IP-cím
Egy hálózati adapter létrehozása előtt létre kell hoznia egy nyilvános IP-címet.

1. Cserélje le a **$domName** értéket a nyilvános IP-címével használni kívánt tartománynév-címkére, majd hozza létre a következő változót:  
   
        $domName = "domain name label"
   
    A címke csak betűket, számokat és kötőjeleket tartalmazhat (az utolsó karakternek betűnek vagy számnak kell lennie).
2. Ellenőrizze, hogy a név egyedi-e:
   
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName
   
    Ha a válasz **Igaz** eredményt ad, a választott név egyedi.
3. Cserélje le a **$pipName** értéket a használni kívánt nyilvános IP-cím nevére, majd hozza létre a változót. 
   
        $pipName = "public ip address name"
4. Nyilvános IP-cím létrehozása:
   
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### <a name="network-interface"></a>Hálózati illesztő
Most, hogy már rendelkezik a nyilvános IP-címmel, létrehozhatja a hálózati adaptert.

1. Cserélje le a **$nicName** értéket a hálózati adapter kívánt nevére, majd hozza létre a következő változót: 
   
        $nicName = "network interface name"
2. Hálózati adapter létrehozása:
   
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### <a name="configuration-of-the-scale-set"></a>A méretezési csoport konfigurálása
Minden erőforrás rendelkezésre áll a méretezési csoport konfigurálásához, így most már létrehozhatja.  

1. Cserélje le az **$ipName** értéket az IP-konfiguráció kívánt nevére, majd hozza létre a következő változót: 
   
        $ipName = "IP configuration name"
2. Az IP-konfiguráció létrehozása:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Cserélje le a **$vmssConfig** értéket a méretezésicsoport-konfiguráció kívánt nevére, majd hozza létre a következő változót:   
   
        $vmssConfig = "Scale set configuration name"
4. A méretezésicsoport-konfiguráció létrehozása:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    Ez a példa egy három virtuális géppel létrehozott méretezési csoportot mutat be. A méretezési csoportok kapacitásával kapcsolatos információkért lásd:[Virtuálisgép-méretezési csoportok áttekintése](virtual-machine-scale-sets-overview.md). Ez a lépés a csoportban található virtuális gépek méretének (más néven SkuName) a beállítását is magában foglalja. Az igényeinek megfelelő méret megtalálásához lásd: [Virtuális gépek méretei](../virtual-machines/virtual-machines-windows-sizes.md).
5. Adja hozzá a hálózatiadapter-konfigurációt a méretezési csoporthoz:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Ennek nagyjából a következő példához hasonlóan kell kinéznie:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Operációs rendszer profilja
1. Cserélje le a **$computerName** értéket a használni kívánt számítógépnév-előtagra, majd hozza létre a következő változót: 
   
        $computerName = "computer name prefix"
2. Cserélje le az **$adminName** értéket a virtuális gépeken található rendszergazdai fiók nevére, majd hozza létre a következő változót:
   
        $adminName = "administrator account name"
3. Cserélje le az **$adminPassword** értéket a fiók jelszavára, majd hozza létre a következő változót:
   
        $adminPassword = "password for administrator accounts"
4. Az operációs rendszer profiljának létrehozása:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Tárolóprofil
1. Cserélje le a **$storageProfile** értéket a tárterületprofil kívánt nevére, majd hozza létre a változót:  
   
        $storageProfile = "storage profile name"
2. Hozza létre a használni kívánt rendszerképet meghatározó változókat:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    További információ a használható egyéb rendszerképekről:[Azure virtuális gépek rendszerképeinek keresése és kiválasztása a Windows PowerShell és az Azure CLI használatával](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
3. Cserélje le a **$vhdContainers** értéket egy olyan listára, amely a virtuális merevlemezek tárolási helyére mutató elérési utakat tartalmazza (például „https://mystorage.blob.core.windows.net/vhds”), majd hozza létre a következő változót:
   
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
4. A tárolóprofil létrehozása:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport
Végül létrehozhatja a méretezési csoportot.

1. Cserélje le a **$vmssName** értéket a virtuálisgép-méretezési csoport nevére, majd hozza létre a következő változót:
   
        $vmssName = "scale set name"
2. A méretezési csoport létrehozása:
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    A sikeres üzembe helyezés után a következő példához hasonló tartalom látható:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3:-explore-resources"></a>3. lépés: Erőforrások vizsgálata
Ezekkel az erőforrásokkal megvizsgálhatja a létrehozott virtuálisgép-méretezési csoportot:

* Azure Portal – A portál használatával elérhető korlátozott mennyiségű információ.
* [Azure Resource Explorer](https://resources.azure.com/) – Ezzel az eszközzel megtekintheti a méretezési csoport aktuális állapotát.
* Azure PowerShell – A következő paranccsal kérhet le további információkat:
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Következő lépések
* A létrehozott méretezési csoport kezelésével kapcsolatos információkért lásd: [Virtuálisgép-méretezési csoportban lévő virtuális gépek felügyelete](virtual-machine-scale-sets-windows-manage.md)
* Az [Automatikus méretezés és virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-autoscale-overview.md) című dokumentumban foglalt információk alapján beállíthatja a méretezési csoport automatikus méretezését
* További információ a vertikális skálázásáról: [Vertikális automatikus méretezés a virtuálisgép-méretezési csoportokkal](virtual-machine-scale-sets-vertical-scale-reprovision.md)

<!--HONumber=Oct16_HO3-->


