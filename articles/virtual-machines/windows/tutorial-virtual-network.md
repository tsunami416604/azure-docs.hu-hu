---
title: "Az Azure virtuális hálózatok és a Windows virtuális gépek |} Microsoft Docs"
description: "Az oktatóanyag - kezelése az Azure virtuális hálózatok és a Windows virtuális gépek az Azure PowerShell használatával"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/12/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 21f2d586a4c468071bec55c65005b35baf323fe7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Egy Azure virtuális hálózatot és az Azure PowerShell használatával Windows virtuális gépek kezelése

Az Azure virtuális gépek Azure hálózatkezelés belső és külső hálózati kommunikációhoz használni. Ez az oktatóanyag végigvezeti a két virtuális gép telepítése és konfigurálása ezen a virtuális gépek Azure hálózatkezelés. Ebben az oktatóanyagban szereplő példák feltételezik, hogy, hogy a virtuális gépeket üzemeltető egy adatbázis-háttér webalkalmazás azonban egy alkalmazás nincs telepítve az oktatóanyag. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózat és alhálózat
> * Hozzon létre egy nyilvános IP-címet
> * Előtér-virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttér-virtuális gép létrehozása

Az oktatóanyag végrehajtása során létrehozott ezeket az erőforrásokat látható:

![Két alhálózat virtuális hálózat](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – a virtuális hálózat, amely a virtuális gépek használatával kommunikálnak egymással, és az interneten.
- *myFrontendSubnet* – az alhálózati *myVNet* az előtér-erőforrások használják.
- *myPublicIPAddress* -használt nyilvános IP-cím elérésére *myFrontendVM* az internetről.
- *myFrontentNic* -által használt hálózati illesztőt *myFrontendVM* kommunikálni *myBackendVM*.
- *myFrontendVM* – az internet közötti kommunikációra használja a virtuális gép és *myBackendVM*.
- *myBackendNSG* – a hálózati biztonsági csoportot, amely a közötti kommunikáció a *myFrontendVM* és *myBackendVM*.
- *myBackendSubnet* -a társított alhálózati *myBackendNSG* és a háttér-erőforrások használják.
- *myBackendNic* -által használt hálózati illesztőt *myBackendVM* kommunikálni *myFrontendVM*.
- *myBackendVM* -1433-as port használatával kommunikálni a virtuális gépi *myFrontendVM*.

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="vm-networking-overview"></a>VM-hálózat – áttekintés

Egy Azure virtuális hálózatot a virtuális gépek, az internetes és más Azure-szolgáltatások például az Azure SQL-adatbázis között a biztonságos hálózati kapcsolatok engedélyezése. Virtuális hálózatok logikai szegmensekben – ún alhálózatok osztható. Alhálózatok folyamatábrán hálózati és biztonsági határaként szolgálnak. A virtuális gép telepítésekor általában tartalmazza a virtuális hálózati adaptert, alhálózat van csatolva.

## <a name="create-a-virtual-network-and-subnet"></a>Hozzon létre egy virtuális hálózat és alhálózat

Ebben az oktatóanyagban egy virtuális hálózaton, két alhálózattal jön létre. A webalkalmazás üzemeltetéséhez előtér-alhálózatot, és egy adatbázis-kiszolgálót futtató háttér-alhálózatot.

Virtuális hálózat létrehozása előtt hozzon létre egy erőforrás csoport használatával [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myRGNetwork* a a *EastUS* helye:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Az alhálózati beállítások létrehozása

Egy alhálózat-konfiguráció létrehozásának *myFrontendSubnet* használatával [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

És egy alhálózat-konfiguráció létrehozásának *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy VNETET nevű *myVNet* használatával *myFrontendSubnet* és *myBackendSubnet* használatával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Ezen a ponton a hálózati létrehozott és szegmentált két alhálózat, egy az előtér-szolgáltatásokat, és egy másikat a háttér-szolgáltatásaihoz. A következő szakaszban virtuális gépek létrehozása és ezek alhálózatok csatlakozik.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetővé az interneten. A kiosztási módszer a nyilvános IP-címet, dinamikus vagy statikus konfigurálhatók. Alapértelmezés szerint egy nyilvános IP-cím dinamikusan történik. Dinamikus IP-címek van kiadva, ha egy virtuális gép felszabadítása. Ennek következtében az IP-cím módosítása bármely, amely tartalmazza a virtuális gép felszabadítás művelet során.

A kiosztási módszer statikus, amely biztosítja, hogy az IP-cím egy megszüntetett állapot esetén egy virtuális géphez hozzárendelt maradnak állítható be. A statikusan lefoglalt IP-cím használata esetén nem adható meg az IP-címet saját magát. Ehelyett az történik a rendelkezésre álló címek készletét.

Hozzon létre egy nyilvános IP-cím nevű *myPublicIPAddress* használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

A - AllocationMethod paramétert is módosíthatja `Static` egy statikus nyilvános IP-címet hozzárendelni.

## <a name="create-a-front-end-vm"></a>Előtér-virtuális gép létrehozása

A virtuális gépek számára egy virtuális hálózatot virtuális hálózati kártya (NIC) szükséges. Hozzon létre egy hálózati adapter által használt [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Állítsa be a felhasználónevet és jelszót a rendszergazdai fiókhoz az a virtuális gép használata a szükséges [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Ezek a hitelesítő adatok segítségével csatlakoztassa a virtuális Gépet, a további lépéseket:

```azurepowershell-interactive
$cred = Get-Credential
```

A virtuális gépek létrehozása [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Hozzáadása AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface), és [új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="secure-network-traffic"></a>Biztonságos hálózati adatforgalom

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Lehet, hogy az NSG-k társított alhálózat vagy az egyes hálózati adapterek. Amikor egy NSG-t egy adott hálózati csatoló kapcsolódik, csak a kapcsolódó virtuális gép vonatkozik. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra.

### <a name="network-security-group-rules"></a>Hálózati biztonsági csoportszabályok

NSG-szabályok határozza meg, amelyben forgalom engedélyezett vagy megtagadott hálózati portok. A szabályok lehetnek forrás és cél IP-címtartományok, hogy az egyes rendszerek vagy az alhálózatok közötti forgalmat szabályozott. NSG-szabályok terjednie a prioritással (1 – és 4096). Szabályok prioritási sorrendben értékeli ki a rendszer. A 100 prioritással kiértékeli előtt szabály 200 prioritással.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat.

- **Virtuális hálózati** - származó forgalmat, és a befejezési egy virtuális hálózat bejövő és kimenő irányban is.
- **Internet** – a kimenő forgalom engedélyezve van, de a bejövő forgalmat blokkol.
- **Terheléselosztó** -engedélyezése Azure terheléselosztó számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem használ elosztott terhelésű készlet, ez a szabály lehet felülbírálni.

### <a name="create-network-security-groups"></a>Hálózati biztonsági csoportok létrehozása

Hozzon létre egy bejövő forgalomra vonatkozó szabály nevű *myFrontendNSGRule* ahhoz, hogy a bejövő webes forgalomra kiszolgálón *myFrontendVM* használatával [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Korlátozhatja a belső forgalom *myBackendVM* csak *myFrontendVM* hozzon létre egy NSG-t a háttér-alhálózat. Az alábbi példa létrehoz egy NSG-szabály nevű *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Adja hozzá a hálózati biztonsági csoport nevű *myFrontendNSG* használatával [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Ezután adja hozzá a hálózati biztonsági csoport nevű *myBackendNSG* New-AzureRmNetworkSecurityGroup használatával:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

A hálózati biztonsági csoportok hozzáadása az alhálózatok:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Háttér-virtuális gép létrehozása

A legegyszerűbb módja a háttér-virtuális gép létrehozása az ebben az oktatóanyagban az SQL-kiszolgálói lemezkép használatával. Ez az oktatóanyag csak az adatbázis-kiszolgáló a virtuális Gépet hoz létre, de nem ismertetik az adatbázis elérése közben.

Hozzon létre *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Állítsa be a felhasználónevet és jelszót a rendszergazdai fiókhoz az a virtuális Géphez a Get-Credential szükséges:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozzon létre *myBackendVM*:

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

A használt kép SQL Server telepítve van, de nem szerepel ebben az oktatóanyagban. Azt mutatjuk be, hogyan konfigurálhatja a virtuális gépek webes forgalmat fog kezelni, hogy adatbázis kezelheti a virtuális gépek tartalmazza.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre és a védett virtuális gépek kapcsolatos Azure-hálózatok. 

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózat és alhálózat
> * Hozzon létre egy nyilvános IP-címet
> * Előtér-virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttér-virtuális gép létrehozása

A következő oktatóanyag az Azure backup használatával virtuális gépek védelmének biztosítása adatok figyelésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Készítsen biztonsági másolatot a Windows virtuális gépek Azure-ban](./tutorial-backup-vms.md)
