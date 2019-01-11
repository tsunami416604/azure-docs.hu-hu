---
title: Kiépítési útmutató az SQL Server virtuális gépek az Azure PowerShell-lel |} A Microsoft Docs
description: Egy Azure virtuális gép létrehozása az SQL Server virtuálisgép-katalógus rendszerképek biztosít lépéseket és a PowerShell-parancsokat.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 059c227f9a5a5701e3fceca94b643c30d006ce67
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199952"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Hogyan építheti ki az SQL Servert futtató virtuális gépek az Azure PowerShell használatával

Ez az útmutató ismerteti a lehetőségek Windows SQL Server virtuális gépek létrehozása az Azure PowerShell használatával. Egy egyszerű Azure PowerShell-példa több alapértelmezett értékekkel, lásd: a [SQL virtuális gép az Azure PowerShell rövid](quickstart-sql-vm-create-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ez a cikk az Azure PowerShell modul 3.6-os vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

1. Nyissa meg a PowerShellt, és állítsa be az Azure-fiókja elérését a **Connect-AzureRmAccount** parancs futtatásával.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Ekkor meg kell jelennie egy bejelentkezési képernyőnek, amely a hitelesítő adatainak megadását kéri. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="define-image-variables"></a>Kép változókat határozhat meg
Újbóli használata és parancsfájl-létrehozások leegyszerűsítése először számos változó megadása. Igény szerint, de ügyeljen arra, hogy ha módosítja a megadott értékek név hosszának és speciális karakterek kapcsolatos korlátozás elnevezési paraméterértékének megváltoztatásához.

### <a name="location-and-resource-group"></a>Helyen és erőforráscsoportban
Két változók használatával adja meg az adatterületen, és az erőforráscsoportot, amelybe a többi erőforrást a virtuális gép létrehozása.

Igény szerint módosíthatja, és ezek a változók inicializálása a következő parancsmagokat végre.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>A tárolótulajdonságok
A következő változókat használja a tárfiók és a virtuális gép által használandó tárolási típus meghatározása érdekében.

Igény szerint módosíthatja, és ezután hajtsa végre az alábbi parancsmagot, ezek a változók inicializálása. Vegye figyelembe, hogy ebben a példában használjuk [prémium szintű Storage](../premium-storage.md), amely a termelési számítási feladatokhoz ajánlott.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózat tulajdonságai
A következő változók használatával adja meg a hálózati adapterhez, a TCP/IP-kiosztási módszer, a virtuális hálózat neve, a virtuális alhálózat neve, az IP-címtartományt a virtuális hálózat, az IP-címtartományt az alhálózat és a nyilvános tartomány neve felirat a virtuális gép a hálózat által használt.

Igény szerint módosíthatja, és ezután hajtsa végre az alábbi parancsmagot, ezek a változók inicializálása.

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
A következő változók használatával adja meg a virtuális gép nevét, a számítógép nevét, a virtuális gép mérete és az operációs rendszer lemez a virtuális gép neve.

Igény szerint módosíthatja, és ezután hajtsa végre az alábbi parancsmagot, ezek a változók inicializálása.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Válassza ki az SQL Server-lemezképet
A következő változók használatával adja meg az SQL Server-rendszerképet a virtuális gép használatához.

1. Először listázza ki az SQL Server lemezkép típusú ajánlatok mindegyike a **Get-AzureRmVMImageOffer** parancsot:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Ebben az oktatóanyagban a következő változók használatával adja meg az SQL Server 2017-ben a Windows Server 2016-on.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ezután a az ajánlatban elérhető kiadások listázásához.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. A jelen oktatóanyag esetében használja az SQL Server 2017 Developer edition (**SQLDEV**). A Developer edition szabadon licencköteles teszteléshez és fejlesztéshez, és csak a virtuális gép futtatásával járó költségeket fizetni.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A Resource Manager üzemi modellel az első létrehozott objektum az erőforráscsoport. Használja a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal hozzon létre egy Azure-erőforráscsoportot és az erőforrások az erőforráscsoport-nevet és a változókat, amelyek korábban inicializálták által meghatározott helyre.

Hajtsa végre az alábbi parancsmagot az új erőforráscsoport létrehozásához.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A virtuális géphez szükséges tárolási erőforrások, az operációsrendszer-lemez és az SQL Server adat- és naplófájljai. Az egyszerűség kedvéért mindkét hozunk létre egy egyetlen lemezt. További lemez is csatlakoztatható később a a [hozzáadása Azure-lemez](/powershell/module/servicemanagement/azure/add-azuredisk) parancsmagot annak érdekében, hogy az SQL Server adat- és naplófájlok elhelyezése dedikált lemezeken található fájlokat. Használja a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmaggal hozzon létre egy standard szintű tárfiókot, az új erőforráscsoportban, és a tárfiók neve, a storage Sku-név és a korábban inicializált változókban meghatározott helyre .

Hajtsa végre az alábbi parancsmagot az új tárfiók létrehozása.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
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
Először hozzon létre egy alhálózati konfigurációt a virtuális hálózathoz. A jelen oktatóanyagban létrehozunk egy alapértelmezett alhálózati használata a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancsmagot. A virtuális hálózat alhálózati konfigurációt a korábban inicializált változókban meghatározott alhálózat nevét és címét előtaggal rendelkező hozunk létre.

> [!NOTE]
> Megadhatja, hogy a virtuális hálózat alhálózati konfigurációt a parancsmag használatának további tulajdonságainak, de ez az oktatóanyag hatókörén kívül esik.

Hajtsa végre a következő parancsmag hozza létre a virtuális alhálózati konfigurációt.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Ezután hozza létre a virtuális hálózat használatával a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) parancsmagot. A virtuális hálózat létrehozása az új erőforráscsoportban, a nevét, helyét és cím előtagja által definiált, a korábban inicializált változók használata, és az alhálózati konfigurációt, amelyet az előző lépésben megadott használ.

Hajtsa végre a következő parancsmagot a virtuális hálózat létrehozásához.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása
Most, hogy a megadott virtuális hálózat, kell az IP-cím a kapcsolatot a virtuális géphez. Ebben az oktatóanyagban létrehozunk egy nyilvános IP-cím dinamikus IP-címzési támogató internetkapcsolat használatával. Használja a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) parancsmaggal hozzon létre a nyilvános IP-címet a korábban létrehozott erőforráscsoportot és a neve, a hely, a kiosztási módszer és a DNS-tartománynév címkét a változókban meghatározott korábban inicializálták.

> [!NOTE]
> A nyilvános IP-cím, ez a parancsmag használatával további tulajdonságok adhatók, de ez az első oktatóanyag hatókörén kívül esik. Is létrehozhat saját cím vagy -cím statikus-címmel, de, amely egyben ebben az oktatóanyagban hatókörén kívül esik.

Hajtsa végre a következő parancsmagot a nyilvános IP-cím létrehozásához.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
A virtuális gép és az SQL Server forgalom védelmére, hozzon létre egy hálózati biztonsági csoportot.

1. Először hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az RDP-vel a távoli asztali kapcsolatok.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurálja a hálózati biztonsági csoportra vonatkozó szabályt, amely engedélyezi a forgalmat a 1433-as TCP-porton. Ez lehetővé teszi SQL Serverhez való csatlakozásának az interneten keresztül.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Ezután hozzon létre a hálózati biztonsági csoport.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása
Mi most már készen áll a virtuális gép által használt hálózati adapter létrehozása. Nevezzük a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) korábban definiált parancsmagot, hogy a hálózati adaptert létrehozni a korábban létrehozott erőforráscsoportot és a nevét, helyét, alhálózat és nyilvános IP-címet.

Hajtsa végre a következő parancsmagot a hálózati adapter létrehozásához.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Egy Virtuálisgép-objektum konfigurálása
Most, hogy meghatározott tárolási és hálózati erőforrásokat, hogy készen áll a virtuális gép számítási erőforrásokat határoz meg. A jelen oktatóanyagban azt adja meg a virtuális gép mérete és a különböző operációs rendszeri tulajdonságok, adja meg a hálózati adaptert, amely a korábban létrehozott, adja meg a blob storage-ba, és adja meg az operációsrendszer-lemez.

### <a name="create-the-vm-object"></a>A Virtuálisgép-objektum létrehozása
Indítsa el a virtuális gép mérete megadásával. Ebben az oktatóanyagban egy DS13 útjához azt. Nevezzük a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) parancsmaggal hozzon létre egy konfigurálható virtuálisgép-objektum nevére, illetve a korábban inicializált változókban megadott méret.

Hajtsa végre a következő parancsmag a virtuálisgép-objektum létrehozásához.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Hozzon létre egy hitelesítő objektumot, amely tárolja a nevét és jelszavát a helyi rendszergazdai hitelesítő adatait
Azt is állítsa be a virtuális gép operációs rendszer tulajdonságait, mielőtt kell adnia a hitelesítő adatokat a helyi rendszergazdai fiók biztonságos karakterláncként. Ehhez használja a [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) parancsmagot.

Hajtsa végre az alábbi parancsmagot, és a hitelesítő adatok kérelem PowerShell ablakban írja be a nevét és a virtuális gépen a helyi rendszergazdai fiók jelszavát.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációs rendszer tulajdonságainak beállítása
Most a virtuális gép operációs rendszer tulajdonságainak a készen állunk [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) megkövetelése-parancsmaggal beállítható az operációs rendszer típusa Windows, mint a [virtuálisgép-ügynök](../../extensions/agent-windows.md) legyen telepítve, adja meg, hogy a parancsmag lehetővé teszi, hogy az automatikus frissítés, és állítsa be a virtuális gép nevét, a számítógép nevét és a hitelesítő adatok a korábban inicializált változók használatával.

Hajtsa végre a következő parancsmagot a virtuális gép operációs rendszeri tulajdonságok beállításához.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>A hálózati adapter hozzáadása a virtuális géphez
Ezután hozzáadunk a létrehozott hálózati adapter korábban a virtuális géphez. Hívja a [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) parancsmag hozzáadása a hálózati adaptert, amely a korábban megadott hálózati adapter változó használatával.

Hajtsa végre a következő parancsmagot a hálózati adaptert a virtuális gép beállításához.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>A lemez a virtuális gép által használandó blob tárolási helyének beállítása
Következő lépésként állítsa a lemezt a virtuális gép által korábban definiált változókat használ által használt blob tárolási helyét.

Hajtsa végre a következő parancsmagot a blob storage helyének beállítása.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása
Következő lépésként állítsa az operációs rendszer a virtuális gép lemez tulajdonságait. Használja a [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) parancsmag használatával adja meg, hogy a virtuális gép operációs rendszerét származnak képet, a virtuális gép meghatározásához és gyorsítótár-olvasható (mivel az SQL Server telepítése ugyanazon a lemezen a) név és az operációsrendszer-lemez, amely a korábban meghatározott változókban meghatározott.

Hajtsa végre az alábbi parancsmagot, az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Adja meg a platform-lemezképet a virtuális gép
Az utolsó konfigurációs lépéssel, hogy adja meg a platform-lemezképet a virtuális gép számára. A jelen oktatóanyagban a legújabb SQL Server 2016 CTP-rendszerképet használunk. Használja a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) parancsmagot a rendszerkép használata, ahogyan korábban definiált változókat a.

Hajtsa végre az alábbi parancsmagot, adja meg a platform-lemezképet a virtuális gép.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása
Most, hogy befejezte a konfigurálás lépéseinek végrehajtásához, készen áll a virtuális gép létrehozásához. Használja a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal hozzon létre a virtuális gépet, hogy meghatároztuk a változók használatával.

Hajtsa végre a következő parancsmagot a virtuális gép létrehozásához.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A virtuális gép jön létre.

> [!NOTE]
> Információ a rendszerindítási diagnosztika a hiba figyelmen kívül hagyhatja. A standard szintű tárfiók létrejön a rendszerindítási diagnosztika, mert a virtuálisgép-lemez a megadott tárfiók premium storage-fiók.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése
Az SQL Server virtuális gépek támogatják a automatizált felügyeleti funkcióinak a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md). Az ügynök az új virtuális gépen való telepítéséhez futtassa a következő parancsot a gép létrehozása után.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Tesztelési virtuális gép eltávolítása

Ha nem szükséges, hogy a virtuális gép folyamatosan fusson, a szükségtelen költségeket elkerülendő leállíthatja az épp használaton kívüli gépet. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Emellett véglegesen is törölheti a virtuális géppel társított erőforrásokat a **Remove-AzureRmResourceGroup** paranccsal. Ez véglegesen törli magát a virtuális gépet is, ezért ezt a parancsot körültekintően alkalmazza.

## <a name="example-script"></a>Példaszkript
A következő parancsfájl ebben az oktatóanyagban a teljes PowerShell-parancsfájlt tartalmazza. Azt feltételezi, hogy már a telepítő az Azure-előfizetés használata az **Connect-AzureRmAccount** és **Select-AzureRmSubscription** parancsokat.

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
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>További lépések
A virtuális gép létrehozása után is:

- Csatlakozzon a virtuális gép távoli asztali (RDP) használatával.
- SQL Server-beállítások konfigurálása a portálon a virtuális gép, többek között:
   - [Tárolási beállítások](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatizált felügyeleti feladatokat](virtual-machines-windows-sql-server-agent-extension.md)
- [Kapcsolat konfigurálása](virtual-machines-windows-sql-connect.md).
- Csatlakozzon az ügyfelek és alkalmazások az új SQL Server-példányon.

