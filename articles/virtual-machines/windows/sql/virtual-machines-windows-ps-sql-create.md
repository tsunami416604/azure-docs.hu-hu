---
title: Az SQL Server virtuális gépek azure PowerShelllel való kiépítésére vonatkozó útmutató | Microsoft dokumentumok
description: Lépéseket és PowerShell-parancsokat tartalmaz az SQL Server virtuálisgép-lemezképekkel rendelkező Azure virtuális gép létrehozásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790635"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>SQL Server virtuális gépek kiépítése az Azure PowerShell használatával

Ez az útmutató ismerteti a Windows SQL Server virtuális gépek azure PowerShell használatával való létrehozásának lehetőségeit. A több alapértelmezett értékkel rendelkező, egyszerűsített Azure PowerShell-példát az [SQL VM Azure PowerShell rövid útmutatója tartalmazza.](quickstart-sql-vm-create-powershell.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

1. Nyissa meg a PowerShellt, és a **Connect-AzAccount** parancs futtatásával létesítsen hozzáférést az Azure-fiókjához.

   ```powershell
   Connect-AzAccount
   ```

1. A hitelesítő adatok megadásához meg kell jelennie egy képernyőnek. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="define-image-variables"></a>Képváltozók definiálása
Az értékek újrafelhasználásához és a parancsfájlok létrehozásának egyszerűsítéséhez kezdje számos változó definiálásával. A megadott értékek módosításakor módosítsa a paraméterértékeket, de a megadott értékek módosításakor vegye figyelembe a névhosszra és a speciális karakterekre vonatkozó elnevezési korlátozásokat.

### <a name="location-and-resource-group"></a>Hely és erőforráscsoport
Adja meg az adatterületet és azt az erőforráscsoportot, amelybe a többi virtuálisgép-erőforrást létrehozza.

Módosítsa a kívánt módon, majd futtassa ezeket a parancsmagokat a változók inicializálásához.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Tárolási tulajdonságok
Adja meg a tárfiókot és a virtuális gép által használandó tárterület típusát.

Módosítsa a kívánt módon, majd futtassa a következő parancsmast a változók inicializálásához. Azt javasoljuk, [hogy prémium szintű SSD-k](../disks-types.md#premium-ssd) éles számítási feladatokhoz.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózati tulajdonságok
Adja meg a hálózat által a virtuális gépben használandó tulajdonságokat. 

- Hálózati illesztő
- TCP/IP allokációs módszer
- Virtuális hálózat neve
- Virtuális alhálózat neve
- A virtuális hálózat IP-címtartománya
- Az alhálózat IP-címtartománya
- Nyilvános tartománynév-címke

Módosítsa a kívánt módon, majd futtassa ezt a parancsmarunt a változók inicializálásához.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Virtuális gép tulajdonságai
Adja meg a virtuális gép nevét, a számítógép nevét, a virtuális gép méretét és a virtuális gép operációs rendszer lemezének nevét.

Módosítsa a kívánt módon, majd futtassa ezt a parancsmarunt a változók inicializálásához.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server-lemezkép kiválasztása

A következő változók segítségével határozza meg a virtuális géphez használandó SQL Server-lemezképet. 

1. Először sorolja fel az ÖSSZES SQL `Get-AzVMImageOffer` Server lemezkép-ajánlatot a paranccsal. Ez a parancs az Azure Portalon elérhető aktuális lemezképeket, valamint a csak a PowerShelllel telepíthető régebbi lemezképeket sorolja fel:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Ebben az oktatóanyagban az alábbi változók segítségével adja meg az SQL Server 2017-et Windows Server 2016 rendszeren.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ezután sorolja fel az ajánlathoz rendelkezésre álló kiadásokat.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Ebben az oktatóanyagban használja az SQL Server 2017 Developer edition ( SQLDEV )**kiadását.** A fejlesztői kiadás szabadon engedélyezett tesztelésre és fejlesztésre, és csak a virtuális gép futtatásának költségeit kell fizetnie.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az Erőforrás-kezelő központi telepítési modelljével az első létrehozott objektum az erőforráscsoport. A [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával hozzon létre egy Azure-erőforráscsoportot és annak erőforrásait. Adja meg azokat a változókat, amelyeket korábban inicializált az erőforráscsoport nevéhez és helyéhez.

Futtassa ezt a parancsmast az új erőforráscsoport létrehozásához.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Create a storage account
A virtuális gép nek tárolási erőforrásokra van szüksége az operációs rendszer lemezéhez, valamint az SQL Server-adatokhoz és a naplófájlokhoz. Az egyszerűség kedvéért egyetlen lemezt hoz létre mindkettőhez. Az [Azure-lemez hozzáadása](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) parancsmag használatával később további lemezeket is csatolhat az SQL Server-adatok és a naplófájlok dedikált lemezekre helyezéséhez. A [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmag segítségével hozzon létre egy szabványos tárfiókot az új erőforráscsoportban. Adja meg a korábban inicializált változókat a tárfiók nevéhez, a tártermék nevéhez és helyéhez.

Futtassa ezt a parancsmasat az új tárfiók létrehozásához.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A tárfiók létrehozása eltarthat néhány percig.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális géphez számos hálózati erőforrásra van szükség a hálózati kapcsolathoz.

* Minden virtuális géphez virtuális hálózat szükséges.
* A virtuális hálózatnak legalább egy alhálózattal kell rendelkeznie.
* A hálózati adaptert nyilvános vagy privát IP-címmel kell definiálni.

### <a name="create-a-virtual-network-subnet-configuration"></a>Virtuális hálózati alhálózati konfiguráció létrehozása
Először hozzon létre egy alhálózati konfigurációt a virtuális hálózathoz. Ebben az oktatóanyagban hozzon létre egy alapértelmezett alhálózatot a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmag használatával. Adja meg azokat a változókat, amelyeket korábban inicializált az alhálózat nevéhez és címelőtaghoz.

> [!NOTE]
> A virtuális hálózat alhálózati konfigurációjának további tulajdonságait ezzel a parancsmaggal határozhatja meg, de ez túlmutat az oktatóanyag hatókörén.

Futtassa ezt a parancsmast a virtuális alhálózati konfiguráció létrehozásához.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Ezután hozza létre a virtuális hálózatot az új erőforráscsoportban a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) parancsmag használatával. Adja meg a névhez, helyhez és címelőtaghoz korábban inicializált változókat. Használja az előző lépésben definiált alhálózati konfigurációt.

Futtassa ezt a parancsmast a virtuális hálózat létrehozásához.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Nyilvános IP-cím létrehozása
Most, hogy a virtuális hálózat definiálva van, be kell állítania egy IP-címet a virtuális géphez való kapcsolódáshoz. Ebben az oktatóanyagban hozzon létre egy nyilvános IP-címet dinamikus IP-címzéssel az internetkapcsolat támogatásához. A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) parancsmag segítségével hozza létre a nyilvános IP-címet az új erőforráscsoportban. Adja meg a korábban inicializált változókat a névhez, a helyhez, a foglalási módhoz és a DNS-tartománynév címkéjéhez.

> [!NOTE]
> A nyilvános IP-cím további tulajdonságait ezzel a parancsmaggal határozhatja meg, de ez túlmutat a kezdeti oktatóanyag hatókörén. Létrehozhat egy privát címet vagy egy statikus címmel rendelkező címet is, de ez az oktatóanyag hatókörén is kívül esik.

Futtassa ezt a parancsmast a nyilvános IP-cím létrehozásához.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
A virtuális gép és az SQL Server-forgalom biztonságossá tételéhez hozzon létre egy hálózati biztonsági csoportot.

1. Először hozzon létre egy hálózati biztonsági csoportszabályt az RDP számára a távoli asztali kapcsolatok engedélyezéséhez.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfiguráljon olyan hálózati biztonsági csoportszabályt, amely engedélyezi a forgalmat az 1433-as TCP-porton. Ezzel lehetővé teszi az SQL Server hez való csatlakozást az interneten keresztül.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Hozza létre a hálózati biztonsági csoportot.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása
Most már készen áll a virtuális gép hálózati felületének létrehozására. A [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) parancsmag segítségével hozza létre a hálózati adaptert az új erőforráscsoportban. Adja meg a korábban definiált nevet, helyet, alhálózatot és nyilvános IP-címet.

Futtassa ezt a parancsmagát a hálózati adapter létrehozásához.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Virtuálisgép-objektum konfigurálása
Most, hogy a tárolási és hálózati erőforrások definiálva vannak, készen áll a virtuális gép számítási erőforrásainak definiálására.

- Adja meg a virtuális gép méretét és az operációs rendszer különböző tulajdonságait.
- Adja meg a korábban létrehozott hálózati illesztőt.
- Blob storage definiálása.
- Adja meg az operációs rendszer lemezét.

### <a name="create-the-vm-object"></a>A virtuális gép objektumának létrehozása
Kezdje a virtuális gép méretének megadásával. Ehhez az oktatóanyaghoz adjon meg egy DS13-at. A [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) parancsmag segítségével konfigurálható virtuálisgép-objektumot hozhat létre. Adja meg a korábban inicializált változókat a névhez és a mérethez.

Futtassa ezt a parancsmast a virtuálisgép-objektum létrehozásához.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Hitelesítő adatok alapján a helyi rendszergazdahitelesítő adatok nevének és jelszavának tárolására szolgáló hitelesítő adatok létrehozása
A virtuális gép operációsrendszer-tulajdonságainak beállítása előtt biztonságos karakterláncként meg kell adnia a helyi rendszergazdai fiók hitelesítő adatait. Ehhez használja a [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) parancsmag.

Futtassa a következő parancsmast, és a PowerShell hitelesítő adatok kérésablakában írja be a nevet és a jelszót a virtuális gép helyi rendszergazdai fiókjához.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációsrendszer-tulajdonságainak beállítása
Most már készen áll a virtuális gép operációs rendszer tulajdonságainak beállítására a [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) parancsmaggal.

- Állítsa be az operációs rendszer típusát Windows-ként.
- A [virtuálisgép-ügynök](../../extensions/agent-windows.md) telepítésének megkövetelése.
- Adja meg, hogy a parancsmag engedélyezi-e az automatikus frissítést.
- Adja meg a korábban inicializált változókat a virtuális gép nevéhez, a számítógép nevéhez és a hitelesítő adatokhoz.

Futtassa ezt a parancsmast a virtuális gép operációs rendszer tulajdonságainak beállításához.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Hálózati adapter hozzáadása a virtuális géphez
Ezután az [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) parancsmag segítségével adja hozzá a hálózati adaptert a korábban megadott változó használatával.

Futtassa ezt a parancsmahagyó a virtuális gép hálózati illesztőjének beállításához.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>A virtuális gép által használandó lemez blobtárolási helyének beállítása
Ezután állítsa be a blob tárolási helyét a virtuális gép lemezén a korábban definiált változók használatával.

Futtassa ezt a parancsmasaa a blob tárolási hely beállításához.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>A virtuális gép operációsrendszer-lemeztulajdonságainak beállítása
Ezután állítsa be a virtuális gép operációsrendszer-lemezének tulajdonságait a [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) parancsmag használatával. 

- Adja meg, hogy a virtuális gép operációs rendszere lemezképből származik-e.
- Állítsa a gyorsítótárazást írásvédetté (mert az SQL Server ugyanazon a lemezen van telepítve).
- Adja meg a virtuális gép nevéhez és az operációs rendszer lemezéhez korábban inicializált változókat.

Futtassa ezt a parancsmacst a virtuális gép operációsrendszer-lemeztulajdonságainak beállításához.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>A virtuális gép platformlemezképének megadása
Az utolsó konfigurációs lépés a platformlemezkép megadása a virtuális géphez. Ebben az oktatóanyagban használja a legújabb SQL Server 2016 CTP-lemezképet. A [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) parancsmag segítségével használja ezt a lemezképet a korábban definiált változókkal.

Futtassa ezt a parancsmasamot a virtuális gép platformlemezképének megadásához.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása
Most, hogy befejezte a konfigurációs lépéseket, készen áll a virtuális gép létrehozására. A [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmag segítségével hozza létre a virtuális gépet a megadott változók használatával.

> [!TIP]
> A virtuális gép létrehozása eltarthat néhány percig.

Futtassa ezt a parancsmast a virtuális gép létrehozásához.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A virtuális gép jön létre.

> [!NOTE]
> Ha hibaüzenetet kap a rendszerindítási diagnosztikával kapcsolatban, figyelmen kívül hagyhatja azt. A rendszerindítási diagnosztikához egy standard szintű tárfiók jön létre, mivel a virtuális gép lemezének megadott tárfiókja egy prémium szintű tárfiók.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése
Az SQL Server virtuális gépek támogatják az automatikus felügyeleti szolgáltatásokat az [SQL Server IaaS Agent Extension segítségével.](virtual-machines-windows-sql-server-agent-extension.md) Az ügynök telepítése az új virtuális gépen, és regisztrálja az erőforrás-szolgáltató, futtassa a [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) parancsot a virtuális gép létrehozása után. Adja meg az SQL Server virtuális gép licenctípusát, és válassza ki a felosztó-ki-ki-kiosztó vagy a saját licencét az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)segítségével. A licenceléssel kapcsolatos további tudnivalókat a [licencelési modell című](virtual-machines-windows-sql-ahb.md)témakörben talál. 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Virtuális gép leállítása vagy eltávolítása

Ha nincs szüksége a virtuális gép folyamatos futtatásához, elkerülheti a szükségtelen díjakat, ha leállítja, ha nem használja. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Az **Eltávolítás-AzResourceGroup** paranccsal véglegesen törölheti a virtuális géphez társított összes erőforrást is. Ezzel véglegesen törli a virtuális gépet is, ezért óvatosan használja ezt a parancsot.

## <a name="example-script"></a>Példaszkript
A következő parancsfájl tartalmazza az oktatóanyag teljes PowerShell-parancsfájlt. Feltételezi, hogy már beállította az Azure-előfizetést a **Connect-AzAccount** és a **Select-AzSubscription** parancsokkal való használatra.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>További lépések
A virtuális gép létrehozása után a következőket teheti:

- Csatlakozás a virtuális géphez RDP használatával
- Konfigurálja az SQL Server beállításait a portálon a virtuális géphez, többek között:
   - [Tárolási beállítások](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatizált felügyeleti feladatok](virtual-machines-windows-sql-server-agent-extension.md)
- [Kapcsolat konfigurálása](virtual-machines-windows-sql-connect.md)
- Ügyfelek és alkalmazások összekapcsolása az új SQL Server-példánysal

