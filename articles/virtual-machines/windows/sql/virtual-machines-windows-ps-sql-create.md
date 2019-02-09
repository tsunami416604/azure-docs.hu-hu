---
title: Kiépítési útmutató az SQL Server virtuális gépek az Azure PowerShell-lel |} A Microsoft Docs
description: Egy Azure virtuális gép létrehozása az SQL Server virtuálisgép-katalógus rendszerképek biztosít lépéseket és a PowerShell-parancsokat.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c37347928e75986fa8aee0d3d0a4db2667312f56
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984375"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Hogyan építheti ki az SQL Servert futtató virtuális gépek az Azure PowerShell használatával

Ez az útmutató ismerteti a lehetőségek Windows SQL Server virtuális gépek létrehozása az Azure PowerShell használatával. Egy egyszerű Azure PowerShell-példa több alapértelmezett értékekkel, lásd: a [SQL virtuális gép az Azure PowerShell rövid](quickstart-sql-vm-create-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

1. Nyissa meg a Powershellt, és állítsa be az Azure-fiókja elérését futtatásával a **Connect-AzAccount** parancsot.

   ```PowerShell
   Connect-AzAccount
   ```

1. Megjelenik egy képernyő, amely a hitelesítő adatainak megadását. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="define-image-variables"></a>Kép változókat határozhat meg
Újból felhasználhatja az értékeket, és egyszerűsítheti a parancsfájl-létrehozási, indítsa el a változók egy szám megadásával. A paraméterek értékeit, de vegye figyelembe, ha módosítja a megadott értékek név hosszának és speciális karakterek kapcsolatos korlátozás elnevezési módosítása

### <a name="location-and-resource-group"></a>Helyen és erőforráscsoportban
Adja meg az adatterületen és az erőforráscsoportot, amelybe a többi virtuális gép erőforrást hoz létre.

Módosítani szeretné, és ezután futtassa a parancsmagokat, ezek a változók inicializálása.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>A tárolótulajdonságok
A tárfiók és a virtuális gép által használandó tárolási típus meghatározása.

Módosítani szeretné, és futtassa az alábbi parancsmagot, ezek a változók inicializálása. Azt javasoljuk, [prémium szintű Storage](../premium-storage.md) a termelési számítási feladatokhoz.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózat tulajdonságai
A hálózat a virtuális gép által használandó tulajdonságainak definiálásához. 

- Hálózati illesztő
- TCP/IP-címkiosztási módszer
- Virtuális hálózat neve
- Virtuális alhálózat neve
- IP-címtartományt a virtuális hálózat
- Az IP-cím az alhálózat
- Public domain name label

Módosítani szeretné, és futtassa a parancsmagot, ezek a változók inicializálása.

```PowerShell
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
Adja meg a virtuális gép nevét, a számítógép nevét, a virtuális gép mérete és az operációs rendszer lemez a virtuális gép neve.

Módosítani szeretné, és futtassa a parancsmagot, ezek a változók inicializálása.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Válassza ki az SQL Server-lemezképet

A következő változók használatával adja meg az SQL Server-rendszerképet a virtuális gép használatához. 

1. Először listázza ki az SQL Server lemezkép típusú ajánlatok mindegyike a `Get-AzVMImageOffer` parancsot. Ez a parancs felsorolja a meglévő rendszerképek az Azure Portalon elérhető, és csak a PowerShell segítségével telepíthető régebbi rendszerképek:

   ```PowerShell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Ebben az oktatóanyagban a következő változók használatával adja meg az SQL Server 2017-ben a Windows Server 2016-on.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ezt követően listázza ki a az ajánlatban elérhető kiadások.

   ```PowerShell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. A jelen oktatóanyag esetében használja az SQL Server 2017 Developer edition (**SQLDEV**). A Developer edition szabadon licencköteles teszteléshez és fejlesztéshez, és csak a virtuális gép futtatásával járó költségeket fizetni.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A Resource Manager üzemi modellel az első létrehozott objektum az erőforráscsoport. Használja a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmaggal hozzon létre egy Azure-erőforráscsoportot és az erőforrások. Adja meg az erőforráscsoport-név és hely inicializált változókat.

Futtassa ezt a parancsmagot az új erőforráscsoport létrehozásához.

```PowerShell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A virtuális géphez szükséges tárolási erőforrások, az operációsrendszer-lemez és az SQL Server adat- és naplófájljai. Az egyszerűség kedvéért egyetlen mindkét fog létrehozni. További lemez is csatlakoztatható később a a [hozzáadása Azure-lemez](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) dedikált lemez a parancsmag az SQL Server adat- és naplófájlok elhelyezése nevű fájlt. Használja a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) parancsmaggal hozzon létre egy standard szintű tárfiókot az új erőforráscsoportban. Adja meg a tárfiók nevét, tároló-termékváltozat neve és helye inicializált változókat.

Futtassa ezt a parancsmagot az új tárfiók létrehozása.

```PowerShell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A storage-fiók létrehozása pár percet is igénybe vehet.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális géphez több hálózati erőforrások a hálózati kapcsolat szükséges.

* Minden virtuális gép egy virtuális hálózatra van szükség.
* Virtuális hálózat rendelkeznie kell legalább egy alhálózatot meghatározva.
* Egy hálózati adaptert meg kell határozni egy nyilvános vagy magánhálózati IP-cím.

### <a name="create-a-virtual-network-subnet-configuration"></a>Hozzon létre egy virtuális hálózat alhálózati konfigurációt
Először hozzon létre egy alhálózati konfigurációt a virtuális hálózat. A jelen oktatóanyag esetében hozzon létre egy alapértelmezett alhálózati a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) parancsmagot. Adja meg az alhálózat nevét és címét előtag inicializált változókat.

> [!NOTE]
> Megadhatja, hogy a virtuális hálózat alhálózati konfigurációt a parancsmag használatának további tulajdonságainak, de ez az oktatóanyag hatókörén kívül esik.

Hozzon létre a virtuális alhálózati konfigurációt a parancsmag futtatásával.

```PowerShell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Ezután hozza létre a virtuális hálózat az új erőforrás csoport használatával a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) parancsmagot. Adja meg a nevét, helyét és címelőtag a korábban inicializált változókat. Az alhálózati konfigurációt, amelyet az előző lépésben megadott használja.

Futtassa ezt a parancsmagot, a virtuális hálózat létrehozásához.

```PowerShell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása
Most, hogy a virtuális hálózaton van definiálva, konfigurálnia kell a hálózati kapcsolatot a virtuális gép IP-címet. A jelen oktatóanyag esetében hozzon létre egy nyilvános IP-cím dinamikus IP-címzési támogató internetkapcsolat használatával. Használja a [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) parancsmagot, hogy a nyilvános IP-cím létrehozása az új erőforráscsoportban. Adja meg a nevét, a hely, a kiosztási módszer és a DNS-tartománynév címkét a korábban inicializált változókat.

> [!NOTE]
> A nyilvános IP-cím, ez a parancsmag használatával további tulajdonságok adhatók, de ez az első oktatóanyag hatókörén kívül esik. Is létrehozhat saját cím vagy -cím statikus-címmel, de, amely egyben ebben az oktatóanyagban hatókörén kívül esik.

Futtassa a parancsmagot a nyilvános IP-cím létrehozása.

```PowerShell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
A virtuális gép és az SQL Server forgalom védelmére, hozzon létre egy hálózati biztonsági csoportot.

1. Először hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az RDP-vel a távoli asztali kapcsolatok.

   ```PowerShell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurálja a hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi a forgalmat a 1433-as TCP-porton. Ez lehetővé teszi az SQL Serverhez való csatlakozásának az interneten keresztül.

   ```PowerShell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. A hálózati biztonsági csoport létrehozása.

   ```PowerShell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása
Most már készen áll a hálózati adaptert a virtuális gép létrehozásához. Használja a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) parancsmaggal hozzon létre a hálózati adapter az új erőforráscsoportban. Adja meg a nevét, helyét, alhálózat és nyilvános IP-címet korábban definiált.

Futtassa ezt a parancsmagot, a hálózati adapter létrehozásához.

```PowerShell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Egy Virtuálisgép-objektum konfigurálása
Most, hogy a tárolási és hálózati erőforrások meg vannak határozva, akkor készen áll a virtuális gép számítási erőforrásokat határoz meg.

- Adja meg a virtuális gép mérete és a különböző operációs rendszeri tulajdonságok.
- Adja meg a korábban létrehozott hálózati adaptert.
- Adja meg a blob storage-bA.
- Adja meg az operációsrendszer-lemez.

### <a name="create-the-vm-object"></a>A Virtuálisgép-objektum létrehozása
Indítsa el a virtuális gép mérete megadásával. Ebben az oktatóanyagban adja meg a DS13. Használja a [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) parancsmaggal hozzon létre egy konfigurálható virtuálisgép-objektumot. Adja meg a korábban inicializált nevét és méretét a változókat.

Ez a parancsmag a virtuálisgép-objektum létrehozásához futtassa.

```PowerShell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Hozzon létre egy hitelesítő objektumot, amely tárolja a nevét és jelszavát a helyi rendszergazdai hitelesítő adatait
Ahhoz, hogy a virtuális gép operációs rendszeri tulajdonságok, meg kell adnia a helyi rendszergazdai fiók hitelesítő adatait biztonságos karakterláncként. Ehhez használja a [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) parancsmagot.

Futtassa az alábbi parancsmagot, és a hitelesítő adatok kérelem PowerShell ablakban írja be a nevét és a virtuális gépen a helyi rendszergazdai fiók jelszavát.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációs rendszer tulajdonságainak beállítása
Most már készen áll a virtuális gép operációs rendszer tulajdonságainak a beállítása a [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) parancsmagot.

- Az operációs rendszer típusa Windows állítja be.
- Szükséges a [virtuálisgép-ügynök](../../extensions/agent-windows.md) kell telepíteni.
- Adja meg, hogy a parancsmag lehetővé teszi, hogy az automatikus frissítés.
- Adja meg a virtuális gép nevét, a számítógép nevét és a hitelesítő adatok a korábban inicializált változókat.

Futtassa ezt a parancsmagot, az operációs rendszer a virtuális gép tulajdonságainak beállítása.

```PowerShell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>A hálózati adapter hozzáadása a virtuális géphez
Ezután a [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) parancsmag hozzáadása a hálózati adaptert, amely korábban definiált változó használatával.

A hálózati adaptert a virtuális gép beállítása a parancsmag futtatásához.

```PowerShell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>A lemez a virtuális gép által használandó blob tárolási helyének beállítása
Ezután állítsa be a korábban megadott változók használata a Virtuálisgép-lemez blob tárolási helyét.

Futtassa a parancsmagot a blob storage helyének beállítása.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása
Ezután állítsa be az operációs rendszer a virtuális gépet a lemez tulajdonságait a [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) parancsmagot. 

- Adja meg, hogy a virtuális gép operációs rendszerét egy képet fog érkezni.
- A gyorsítótár olvasható (mivel az SQL Server telepítése a ugyanazt a lemezt) beállítása.
- Adja meg a változókat, amelyek a virtuális gép nevét és az operációsrendszer-lemez inicializálása.

Futtassa ezt a parancsmagot, az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása.

```PowerShell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Adja meg a platform-lemezképet a virtuális gép
Az utolsó konfigurációs lépéssel, hogy adja meg a platform-lemezképet a virtuális gép. A jelen oktatóanyag esetében használja a legújabb SQL Server 2016 CTP-rendszerképet. Használja a [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) parancsmagot, hogy ez a rendszerkép használata a korábban definiált változókat.

Adja meg a platform-lemezképet a virtuális gép a parancsmag futtatásával.

```PowerShell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása
Most, hogy végzett a konfigurációs lépések, készen áll a virtuális gép létrehozásához. Használja a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmaggal hozzon létre a virtuális gép által meghatározott változókat használ.

> [!TIP]
> A virtuális gép létrehozása néhány percet is igénybe vehet.

Futtassa ezt a parancsmagot, a virtuális gép létrehozásához.

```PowerShell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A virtuális gép jön létre.

> [!NOTE]
> A rendszerindítási diagnosztika kapcsolatos hibaüzenetet kap, ha figyelmen kívül hagyhatja azt. Mivel a virtuálisgép-lemez a megadott tárfiók premium storage-fiók a rendszerindítási diagnosztika a standard szintű tárfiók létrejön.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése
Az SQL Server virtuális gépek támogatják a automatizált felügyeleti funkcióinak a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md). Telepítse az ügynököt az új virtuális gép, futtassa a következő parancsot a létrehozást követően.


   ```PowerShell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Állítsa le vagy távolítsa el a virtuális gép

Ha már nincs szüksége a virtuális gép folyamatosan fusson, a szükségtelen díjak elkerüléséhez leállításával, amikor nincs használatban. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```PowerShell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

A virtuális géppel társított összes erőforrás véglegesen is törölheti a **Remove-AzResourceGroup** parancsot. Ezzel véglegesen törli a virtuális gépet is, ezért ezt a parancsot körültekintően.

## <a name="example-script"></a>Példaszkript
A következő parancsfájl ebben az oktatóanyagban a teljes PowerShell-parancsfájlt tartalmazza. Feltételezi, hogy már állított be az Azure-előfizetés használata az **Connect-AzAccount** és **Select-AzSubscription** parancsokat.

```PowerShell
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

# Add the SQL IaaS Extension
Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>További lépések
A virtuális gép létrehozása után is:

- Kapcsolódás a virtuális géphez RDP-vel
- SQL Server-beállítások konfigurálása a portálon a virtuális gép, többek között:
   - [Tárolási beállítások](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatizált felügyeleti feladatokat](virtual-machines-windows-sql-server-agent-extension.md)
- [Kapcsolat konfigurálása](virtual-machines-windows-sql-connect.md)
- Csatlakozás az ügyfelek és alkalmazások számára az új SQL Server-példány

