---
title: "SQL Server virtuális gép létrehozása az Azure PowerShell (erőforrás-kezelő) |} Microsoft Docs"
description: "Lépéseket és a PowerShell-parancsfájlok biztosít az Azure virtuális gép létrehozása az SQL Server virtuális gép a gyűjtemény lemezképei."
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
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 33c306258b6be40f2c5cbc016e3c84e36bf61e0d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Egy SQL Server rendszerű virtuális gép az Azure PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portál](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan hozzon létre egy egyetlen Azure virtuális gép használata a **Azure Resource Manager** telepítési modell Azure PowerShell-parancsmagok használatával. Ebben az oktatóanyagban létre fogunk hozni egy virtuális gép egyetlen lemezmeghajtó lemezkép az SQL-katalógusában. A tárolási, hálózati és számítási erőforrásokat, amelyek a virtuális gép által használható új szolgáltatók létre fogunk hozni. Ha meglévő szolgáltatók bármely ezeket az erőforrásokat, helyette használhatja azokat a szolgáltatókat.

Ha ez a témakör a klasszikus verziója van szüksége, tekintse meg [egy SQL Server rendszerű virtuális gép használata a klasszikus Azure-PowerShell](../classic/ps-sql-create.md).

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban lesz szüksége:

* Egy Azure-fiókot és az előfizetés megkezdése előtt. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* [Az Azure PowerShell)](/powershell/azure/overview), minimális verziója 1.4.0 vagy újabb (Ez az oktatóanyag verziójával 1.5.0 írása).
  * A verzió, írja be a következőt **Azure Get-Module - ListAvailable**.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása
Nyissa meg a Windows Powershellt, és hozzáférés az Azure-fiók létrehozásához a következő parancsmag futtatásával. Választhat egy bejelentkezési képernyő hitelesítő adatait. Használja az ugyanazon e-mailek és az Azure-portálon való bejelentkezéshez használt jelszó.

```PowerShell
Add-AzureRmAccount
```

Sikeres bejelentkezés után kapcsolatban egyes információk a képernyőn, beleértve az előfizetés nevét és az alapértelmezett előfizetés azonosítója. Ez egy, az előfizetés, amely ebben az oktatóanyagban az erőforrások létrehozza kivéve, ha módosítja egy másik előfizetést. Ha több előfizetéssel rendelkezik, futtassa a következő parancsmag az összes, az előfizetések listáját adja vissza:

```PowerShell
Get-AzureRmSubscription
```
Ha másik előfizetést, futtassa a következő parancsot a célkiszolgálóhoz SubscriptionName.

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>Adja meg a lemezkép változókat
Egyszerűbbé teheti a használhatóság és a befejezett parancsfájl az oktatóanyagot megértése, először lesz számos változó meghatározásával. Igényei, de figyeljen a megadott értékek módosításakor név hosszának és speciális karakterek kapcsolatos korlátozás elnevezési paraméterértékének megváltoztatásához.

### <a name="location-and-resource-group"></a>Hely és az erőforráscsoport
Két változókkal adható meg az adatterületen és az erőforráscsoport, amelybe a virtuális gép további erőforrásokat hoz létre.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmagok változókhoz inicializálása.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>Tárolási tulajdonságai
A következő változókkal adható meg a tárfiók és a virtuális gép által használandó tárolási típusát.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása. Vegye figyelembe, hogy a jelen példában használjuk [prémium szintű Storage](../premium-storage.md), amely a termelési számítási feladatokhoz ajánlott. Ez az útmutató és más javaslatokról a részletekért lásd: [teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-performance.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Hálózat tulajdonságai
Adja meg a hálózati kapcsolat, a TCP/IP-kiosztási módszerrel, a virtuális hálózat neve, a virtuális alhálózati név, a virtuális hálózat az IP-címeket, az alhálózat és nyilvános tartománynév-címke az IP-címeket a következő változó segítségével a hálózat a virtuális gép által használható.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
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

### <a name="image-properties"></a>Lemezkép tulajdonságai
A következő változók segítségével határozza meg a virtuális géphez használni kívánt lemezkép. Ebben a példában az SQL Server 2016 Developer edition lemezképet használja. A fejlesztői edition szabadon licenccel tesztelési és fejlesztési, és csak kell fizetnie a virtuális gép futtatásának.

Módosítsa a kívánt módon működjenek, és majd hajtható végre a következő parancsmag változókhoz inicializálása.

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

Vegye figyelembe, hogy kaphat a teljes listáját az SQL Server kép ajánlatokat a Get-AzureRmVMImageOffer paranccsal:

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

És a Get-AzureRmVMImageSku paranccsal egy ajánlat elérhető termékváltozatok látható. A következő parancsot az összes elérhető termékváltozatok jeleníti meg a **SQL2016SP1-WS2016** kínálnak.

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A Resource Manager üzembe helyezési modellben az Ön által létrehozott első objektum az erőforráscsoportot. Ezzel a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmag segítségével hozzon létre egy Azure-erőforráscsoportot és az erőforrások erőforráscsoport-név és hely határozza meg a változókat, amelyek korábban inicializálták.

Hajtsa végre az alábbi parancsmagot az új erőforráscsoport létrehozásához.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Create a storage account
A virtuális gép igényel a tárolási erőforrások, az az operációsrendszer-lemez, és az SQL Server adatainak és naplókönyvtárainak fájlok. Az egyszerűség kedvéért létre fogunk hozni egy lemezt is. További lemezek csatolhat később a [Add-Azure lemez](/powershell/module/azure/add-azuredisk) dedikált lemezeken fájlok parancsmag ahhoz, hogy az SQL Server adatainak és naplókönyvtárainak helyezze el. Ezzel a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmaggal hozzon létre egy standard tárolási fiók az új erőforráscsoportban, és a tárfiók neve, a tárolási termékváltozat és a hely megadva a változók használata, amikor korábban inicializálni.

Hajtsa végre a következő parancsmaggal hozhat létre az új tárfiók.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális gép hálózati erőforrások több hálózati kapcsolatot igényel.

* Minden virtuális gép virtuális hálózat szükséges.
* A virtuális hálózati rendelkeznie kell legalább egy meghatározott alhálózatot.
* Egy adott hálózati csatoló nyilvános vagy magán IP-címet kell megadni.

### <a name="create-a-virtual-network-subnet-configuration"></a>Hozzon létre egy virtuális hálózati alhálózat beállítása
Először hozzon létre a virtuális hálózati alhálózat konfigurációját fogja. A jelen oktatóanyagban létrehozunk egy alapértelmezett alhálózati használata a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancsmag. Létre fogunk hozni a virtuális hálózati alhálózat beállítása a változókat, amelyek korábban inicializálták megadott alhálózat nevét és címét előtagot.

> [!NOTE]
> A virtuális hálózati alhálózat konfiguráció Ez a parancsmag segítségével további tulajdonságok adhatók, de ez az oktatóanyag terjed.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuális alhálózati konfigurációt.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Következő lépésként létre fogunk hozni a virtuális hálózat használatával a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) parancsmag. Létre fogunk hozni a virtuális hálózat az új erőforráscsoportban, a nevét, helyét és cím előtag meghatározni a változókat, amelyek korábban inicializálták használ, és az alhálózati konfigurációt, amelyet az előző lépésben megadott használni.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuális hálózat.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása
Most, hogy a megadott virtuális hálózat, igazolnia kell a hálózati kapcsolatot a virtuális gép IP-címének beállítása. Ebben az oktatóanyagban létre fogunk hozni egy nyilvános IP-címet, dinamikus IP-címzés támogatja az internetkapcsolat működését használatával. Ezzel a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) parancsmaggal hozhat létre a nyilvános IP-cím-a az erőforrás létrehozott csoport prevously és a nevét, a hellyel, a elosztási módszert és a DNS tartománynév-címke definiálva a változók használata, amikor korábban inicializálták.

> [!NOTE]
> A nyilvános IP-cím, ez a parancsmag segítségével további tulajdonságok adhatók, de ez a kezdeti oktatóanyag terjed. Is létrehozhatja saját cím vagy a cím statikus cím, de ez az oktatóanyag túlmutató is ez.

Hajtsa végre a következő parancsmaggal hozhat létre a nyilvános IP-cím.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>A hálózati illesztő létrehozása
A Microsoft most már készen áll a virtuális gép által használt hálózati kapcsolat létrehozásához. Ezzel a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmag a hálózati kapcsolat létrehozásához, a korábban létrehozott erőforráscsoportot és a nevét, a hellyel, az alhálózat és a nyilvános IP-címet korábban már definiálva.

Hajtsa végre a következő parancsmaggal hozhat létre a hálózati illesztőhöz.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>A Virtuálisgép-objektum konfigurálása
Most, hogy meghatározott tárolási és hálózati erőforrásokat, azt határozza meg a számítási erőforrásokat a virtuális gép készen áll. A jelen oktatóanyagban azt fogja adja meg a virtuális gép méretét és a különböző operációs rendszer tulajdonságai, adja meg a hálózati adaptert, korábban létrehozott, adja meg a blob-tároló, és adja meg az operációs rendszer tárolására.

### <a name="create-the-vm-object"></a>A Virtuálisgép-objektum létrehozása
Azt a virtuálisgép-méret megadásával indul el. Ebben az oktatóanyagban egy DS13 meg azt. Ezzel a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) parancsmag segítségével hozzon létre egy konfigurálható virtuálisgép-objektum neve és korábban inicializálták változókkal megadott méret.

Hajtsa végre a következő parancsmaggal hozhat létre a virtuálisgép-objektumot.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Tartsa a nevet és jelszót a helyi rendszergazdai hitelesítő adatokat a hitelesítőadat-objektum létrehozása
Azt is a virtuális gép operációs rendszer tulajdonságainak beállításához igazolnia kell a helyi rendszergazdai fiók, mint egy biztonságos karakterláncot kell megadnia a hitelesítő adatok megadását. Ennek megvalósítása érdekében használjuk a [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) parancsmag.

Hajtsa végre az alábbi parancsmagot, és a Windows PowerShell hitelesítő adatok kérelem ablakban írja be a nevet és a a Windows rendszerű virtuális gép helyi rendszergazdai fiók jelszavát.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>A virtuális gép operációs rendszer tulajdonságainak beállítása
Most azt készen áll a virtuális gép operációs rendszer beállításainak megadása. Fogjuk használni a [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) parancsmag Windows, mint az operációs rendszer típusát igényelnek a [virtuális gép ügynökének](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) legyen telepítve, adja meg, hogy a parancsmag lehetővé teszi, hogy az automatikus frissítés és Állítsa be a virtuális gép nevét, a számítógép nevét és a hitelesítő adatok használata a változókat, amelyek korábban inicializálták.

Hajtsa végre a következő parancsmagot a virtuális gép operációs rendszer tulajdonságainak beállításához.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adjon hozzá a hálózati illesztő a virtuális géphez
A következő a Microsoft felveszi a hálózati illesztő létrehozott korábban a virtuális gép. Ezzel a [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) parancsmaggal adja hozzá a hálózati adapter a hálózati illesztő változóval, amelyet korábban megadott.

A hálózati illesztő a virtuális gép beállítása a következő parancsmag végrehajtása.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>A blob tárolási helyét a lemezt a virtuális gép által használandó beállítása
A következő azt állítja be a lemezen a változókat, amelyek korábban meghatározott használó virtuális gépek által használható blob tárolási helyét.

Blob storage helyének megadása a következő parancsmag végrehajtása.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása
A következő azt állítja be az operációs rendszer lemez tulajdonságai, a virtuális géphez. Ezzel a [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) parancsmag használatával adja meg, hogy a az operációs rendszer a virtuális gép képfájl, a gyorsítótár-olvasni a csak (mivel az SQL-kiszolgáló van telepítés alatt azon a lemezen) származik, és adja meg a virtuális számítógép neve és a változókat, amelyek korábban meghatározott megadott operációsrendszer-lemeze.

Hajtsa végre az alábbi parancsmagot, az operációs rendszer a virtuális gép lemez tulajdonságainak beállítása.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Adja meg a platform-lemezképet a virtuális géphez
Az utolsó konfigurációs lépés annak adja meg a platform-lemezképet a virtuális gép. A jelen oktatóanyagban használjuk a legújabb SQL Server 2016 CTP-lemezképet. Ezzel a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) parancsmag által korábban meghatározott változókat által meghatározott a kép használható.

Hajtsa végre az alábbi parancsmagot, adja meg a platform-lemezképet a virtuális gép számára.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása
Most, hogy befejezte a konfigurációs lépésekkel, készen áll a virtuális gép létrehozásához. Ezzel a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal hozhat létre a virtuális gépet a változókat, amelyek definiáltuk használatával.

Hajtsa végre a következő parancsmagot a virtuális gép létrehozásához.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A virtuális gép jön létre. Figyelje meg, hogy egy standard szintű tárfiókot létre vonatkozó rendszerindítási diagnosztika, mert a virtuális gép lemezét a megadott tárfiók egy prémium szintű storage-fiók.

Az Azure portálon, hogy most már megtekintheti a gép [nyilvános IP-címének és a teljes tartománynév](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>A példaként megadott parancsfájlt
A következő parancsfájl tartalmazza a teljes PowerShell parancsprogramot ehhez az oktatóanyaghoz. Azt feltételezi, hogy már beállítása az Azure-előfizetés használata a **Add-AzureRmAccount** és **Select-AzureRmSubscription** parancsok.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>Következő lépések
A virtuális gép létrehozása után készen áll az RDP és a telepítő kapcsolatot használó virtuális gépek csatlakozni. További információkért lásd: [csatlakozás az SQL Server virtuális gép Azure (erőforrás-kezelő)](virtual-machines-windows-sql-connect.md).
