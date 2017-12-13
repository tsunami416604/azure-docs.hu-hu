---
title: "SQL Server virtuális gépek létrehozása az Azure PowerShell |} Microsoft Docs"
description: "Lépéseket és a PowerShell-parancsok biztosít az Azure virtuális gép létrehozása az SQL Server virtuális gép a gyűjtemény lemezképei."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: 5babea628180501e959387f80dac55618051f552
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>SQL Server virtuális gépek létrehozása az Azure PowerShell

Ez az útmutató ismerteti a beállításokat, az Azure PowerShell Windows SQL Server virtuális gépek létrehozásához. Például egy egyszerű Azure PowerShell további alapértelmezett értékekkel, tekintse meg a [SQL virtuális gép Azure PowerShell gyors üzembe helyezés](quickstart-sql-vm-create-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A gyors üzembe helyezés az Azure PowerShell 3,6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

1. Nyissa meg a Powershellt, és létrehozza a hozzáférést az Azure-fiókjával futtatásával a **Add-AzureRmAccount** parancsot.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Meg kell jelennie egy bejelentkezési képernyő hitelesítő adatait. Használja az ugyanazon e-mailek és az Azure-portálon való bejelentkezéshez használt jelszó.

## <a name="define-image-variables"></a>Adja meg a lemezkép változókat
A újbóli és parancsfájl és egyszerűsítése érdekében először számos változó definiálása. Igényei, de figyeljen a megadott értékek módosításakor név hosszának és speciális karakterek kapcsolatos korlátozás elnevezési paraméterértékének megváltoztatásához.

### <a name="location-and-resource-group"></a>Hely és az erőforráscsoport
Két változókkal adható meg az adatterületen és az erőforráscsoport, amelybe további erőforrásokat a virtuális gép létrehozása.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmagok változókhoz inicializálása.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Tárolási tulajdonságai
A következő változókkal adható meg a tárfiók és a virtuális gép által használandó tárolási típusát.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása. Vegye figyelembe, hogy a jelen példában használjuk [prémium szintű Storage](../premium-storage.md), amely a termelési számítási feladatokhoz ajánlott.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózat tulajdonságai
Adja meg a hálózati kapcsolat, a TCP/IP-kiosztási módszerrel, a virtuális hálózat neve, a virtuális alhálózati név, a virtuális hálózat az IP-címeket, az alhálózat és nyilvános tartománynév-címke az IP-címeket a következő változó segítségével a hálózat a virtuális gép által használható.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása.

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
A következő változók segítségével határozza meg a virtuális gép nevét, a számítógép nevét, a virtuális gép méretét és az operációs rendszer lemez a virtuális gép neve.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Válassza ki az SQL Server-rendszerképet
A következő változó segítségével határozza meg a virtuális géphez használandó SQL Server-rendszerképet.

1. Először kijelentkezik az SQL Server kép ajánlatokat a listában a **Get-AzureRmVMImageOffer** parancs:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Ebben az oktatóanyagban a következő változó segítségével adja meg a Windows Server 2016 SQL Server 2017.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ezt követően lista ki az elérhető kiadásai a szolgáltatásokat.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. A jelen oktatóanyag esetében használja az SQL Server 2017 Developer edition (**SQLDEV**). A fejlesztői edition szabadon licenccel tesztelési és fejlesztési, és csak kell fizetnie a virtuális gép futtatásának.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A Resource Manager üzembe helyezési modellben az Ön által létrehozott első objektum az erőforráscsoportot. Használja a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmag segítségével hozzon létre egy Azure-erőforráscsoportot és az erőforrások erőforráscsoport-név és hely határozza meg a változókat, amelyek korábban inicializálták.

Hajtsa végre az alábbi parancsmagot az új erőforráscsoport létrehozásához.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Create a storage account
A virtuális gép igényel a tárolási erőforrások, az az operációsrendszer-lemez, és az SQL Server adatainak és naplókönyvtárainak fájlok. Az egyszerűség kedvéért mindkét azt létrehozhat egyetlen lemezt. További lemezek csatolhat később a [Add-Azure lemez](/powershell/module/azure/add-azuredisk) dedikált lemezeken fájlok parancsmag ahhoz, hogy az SQL Server adatainak és naplókönyvtárainak helyezze el. Használja a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmaggal hozzon létre egy standard szintű tárfiókot, az új erőforráscsoportban, és a tárfiók neve, tárolási termékváltozat és a változókat, amelyek korábban inicializálták megadott helyre .

Hajtsa végre a következő parancsmaggal hozhat létre az új tárfiók.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A tárfiók létrehozása eltarthat néhány percig.

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális gép hálózati erőforrások több hálózati kapcsolatot igényel.

* Minden virtuális gép virtuális hálózat szükséges.
* A virtuális hálózati rendelkeznie kell legalább egy meghatározott alhálózatot.
* Egy adott hálózati csatoló nyilvános vagy magán IP-címet kell megadni.

### <a name="create-a-virtual-network-subnet-configuration"></a>Hozzon létre egy virtuális hálózati alhálózat beállítása
Először hozzon létre a virtuális hálózati alhálózat konfigurációját. A jelen oktatóanyagban létrehozhatunk olyan alapértelmezett alhálózati használata a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancsmag. A virtuális hálózati alhálózat beállítása nem létrehozni a változókat, amelyek korábban inicializálták megadott előtagot nevét és címét.

> [!NOTE]
> A virtuális hálózati alhálózat konfiguráció Ez a parancsmag segítségével további tulajdonságok adhatók, de ez az oktatóanyag terjed.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuális alhálózati konfigurációt.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Ezután hozzon létre a virtuális hálózat használatával a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) parancsmag. A virtuális hálózat létrehozása az új erőforráscsoport, a nevét, helyét és cím előtag meghatározni a változókat, amelyek korábban inicializálták használ, és az alhálózati konfigurációt, amelyet az előző lépésben megadott használni.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuális hálózat.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása
Most, hogy a megadott virtuális hálózat, igazolnia kell a hálózati kapcsolatot a virtuális gép IP-címének beállítása. Ebben az oktatóanyagban létrehozhatunk egy nyilvános IP-címet, dinamikus IP-címzés támogatja az internetkapcsolat működését használatával. Használja a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) parancsmaggal hozhat létre a nyilvános IP-a korábban létrehozott erőforráscsoport és a nevét, a hellyel, a elosztási módszert és a DNS tartománynév-címke definiálva a változók használata, amikor korábban inicializálták.

> [!NOTE]
> A nyilvános IP-cím, ez a parancsmag segítségével további tulajdonságok adhatók, de ez a kezdeti oktatóanyag terjed. Is létrehozhatja saját cím vagy a cím statikus cím, de ez az oktatóanyag túlmutató is ez.

Hajtsa végre a következő parancsmaggal hozhat létre a nyilvános IP-cím.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Biztonságos a virtuális gép és az SQL Server-forgalomhoz, a hálózati biztonsági csoport létrehozása.

1. Először hozzon létre egy hálózati biztonsági szabály RDP távoli asztali kapcsolatok lehetővé tételéhez.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurálja a hálózati biztonsági csoport szabályt, amely lehetővé teszi a forgalom 1433-as TCP-porton. Ez lehetővé teszi az SQL Server kapcsolatok az interneten keresztül.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Ezután hozzon létre a hálózati biztonsági csoportot.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>A hálózati illesztő létrehozása
A Microsoft most már készen áll a virtuális gép által használt hálózati kapcsolat létrehozásához. Ezt nevezik a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmag a hálózati kapcsolat létrehozásához, a korábban létrehozott erőforráscsoportot és a nevét, a hellyel, az alhálózat és a nyilvános IP-címet korábban már definiálva.

Hajtsa végre a következő parancsmaggal hozhat létre a hálózati illesztőhöz.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>A Virtuálisgép-objektum konfigurálása
Most, hogy meghatározott tárolási és hálózati erőforrásokat, azt határozza meg a számítási erőforrásokat a virtuális gép készen áll. A jelen oktatóanyagban azt adja meg a virtuálisgép-méret és a különböző operációs rendszeri tulajdonságok, adja meg a hálózati adaptert, korábban létrehozott, adja meg a blob-tároló, és adja meg az operációs rendszer tárolására.

### <a name="create-the-vm-object"></a>A Virtuálisgép-objektum létrehozása
Indítsa el a virtuálisgép-méret megadása. Ebben az oktatóanyagban egy DS13 meg azt. Ezt nevezik a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) parancsmag segítségével hozzon létre egy konfigurálható virtuálisgép-objektum neve és korábban inicializálták változókkal megadott méret.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuálisgép-objektumot.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Tartsa a nevet és jelszót a helyi rendszergazdai hitelesítő adatokat a hitelesítőadat-objektum létrehozása
Azt is a virtuális gép operációs rendszer tulajdonságainak beállításához igazolnia kell a helyi rendszergazdai fiók, mint egy biztonságos karakterláncot kell megadnia a hitelesítő adatok megadását. Ehhez használja a [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) parancsmag.

Hajtsa végre az alábbi parancsmagot, és a hitelesítő adatok kérelem PowerShell ablakban írja be a nevét és a virtuális gépen a helyi rendszergazdai fiók jelszavát.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációs rendszer tulajdonságainak beállítása
Most már készen áll a virtuális gép operációs rendszer tulajdonságainak a beállítása azt [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) parancsmag Windows, mint az operációs rendszer típusát igényelnek a [virtuális gép ügynökének](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) legyen telepítve, adja meg, hogy a parancsmag lehetővé teszi, hogy az automatikus frissítés, és állítsa be a virtuális gép nevét, a számítógép nevét és a hitelesítő adatok használata a változókat, amelyek korábban inicializálták.

Hajtsa végre a következő parancsmagot a virtuális gép operációs rendszer tulajdonságainak beállításához.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adjon hozzá a hálózati illesztő a virtuális géphez
Ezután azt adja hozzá a hálózati illesztő létrehozott korábban a virtuális géphez. Hívja a [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) parancsmaggal adja hozzá a hálózati adapter a hálózati illesztő változóval, amelyet korábban megadott.

A hálózati illesztő a virtuális gép beállítása a következő parancsmag végrehajtása.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>A blob tárolási helyét a lemezt a virtuális gép által használandó beállítása
Következő lépésként állítsa a lemezt a változókat, amelyek korábban meghatározott használó virtuális gépek által használható blob tárolási helyét.

Blob storage helyének megadása a következő parancsmag végrehajtása.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása
Ezután állítsa be az operációs rendszer lemez tulajdonságait a virtuális gép. Használja a [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) parancsmag használatával adja meg, határozza meg, hogy a virtuális gép operációs rendszerét a képfájl, a gyorsítótár-olvasni a csak (mivel az SQL-kiszolgáló van telepítés alatt azon a lemezen), és adja meg a virtuális gép név és a változókat, amelyek korábban meghatározott megadott operációsrendszer-lemeze.

Hajtsa végre az alábbi parancsmagot, az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Adja meg a platform-lemezképet a virtuális géphez
Az utolsó konfigurációs lépés annak adja meg a platform-lemezképet a virtuális gép. A jelen oktatóanyagban használjuk a legújabb SQL Server 2016 CTP-lemezképet. Használja a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) parancsmag által korábban meghatározott változókat által meghatározott a kép használható.

Hajtsa végre az alábbi parancsmagot, adja meg a platform-lemezképet a virtuális gép számára.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása
Most, hogy befejezte a konfigurációs lépésekkel, készen áll a virtuális gép létrehozásához. Használja a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal hozhat létre a virtuális gépet a változókat, amelyek definiáltuk használatával.

Hajtsa végre a következő parancsmagot a virtuális gép létrehozásához.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A virtuális gép jön létre.

> [!NOTE]
> A hibaüzenet szerint a botot diagnosztika figyelmen kívül hagyhatja. Standard szintű tárfiók rendszerindítási diagnosztika, mert a virtuális gép lemezét a megadott tárfiók egy prémium szintű storage-fiók jön létre.

## <a name="install-the-sql-iaas-agent"></a>Az SQL Iaas-ügynök telepítése
SQL Server virtuális gépek támogatják a automatizált felügyeleti funkcióinak használatát a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md). Az ügynök telepítése az új virtuális Gépet, futtassa a következő parancsot, miután az létrejött.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>A teszteléshez használt virtuális gép eltávolítása

Ha nincs szükség a virtuális gép folyamatosan fut, elkerülheti a felesleges költségek leállításával, ha nincsenek használatban. A következő parancsot a virtuális gép leáll, de hagyja elérhető további használatra.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

A virtuális géphez tartozó összes erőforrást is véglegesen törölheti a **Remove-AzureRmResourceGroup** parancsot. Véglegesen törli a virtuális gép is, így gondossággal az alábbi parancsot használja.

## <a name="example-script"></a>A példaként megadott parancsfájlt
A következő parancsfájl tartalmazza a teljes PowerShell parancsprogramot ehhez az oktatóanyaghoz. Azt feltételezi, hogy már beállítása az Azure-előfizetés használata a **Add-AzureRmAccount** és **Select-AzureRmSubscription** parancsok.

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

## <a name="next-steps"></a>Következő lépések
A virtuális gép létrehozása után is:

- Csatlakoztassa a virtuális géphez a távoli asztal (RDP) használatával.
- A portál SQL Server-beállítások konfigurálása a virtuális Gépet, beleértve:
   - [Tárolási beállítások](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatikus felügyeleti feladatok](virtual-machines-windows-sql-server-agent-extension.md)
- [Kapcsolat konfigurálása](virtual-machines-windows-sql-connect.md).
- Csatlakoztassa az ügyfeleket és alkalmazásokat az új SQL Server-példányra.

