---
title: Az Azure virtuális hálózatok és a Windows virtuális gépek |} Microsoft Docs
description: Az oktatóanyag - kezelése az Azure virtuális hálózatok és a Windows virtuális gépek az Azure PowerShell használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: feaef679a3090491b64c69ac69bf22153c281d31
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Egy Azure virtuális hálózatot és az Azure PowerShell használatával Windows virtuális gépek kezelése

Az Azure-beli virtuális gépek Azure hálózatkezelést használnak a belső és külső hálózati kommunikációhoz. Ez az oktatóanyag végigvezeti két virtuális gép telepítésén és az Azure hálózatkezelés konfigurálásán ezen virtuális gépekhez. Az oktatóanyagban szereplő példák feltételezik, hogy a virtuális gépek üzemeltetnek egy webalkalmazást egy adatbázis-alapú háttérrendszerrel, de az oktatóanyag során nem telepítünk ilyen alkalmazást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása



Ehhez az oktatóanyaghoz az AzureRM.Compute modul 4.3.1-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM.Compute`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="vm-networking-overview"></a>Virtuális gépek hálózatkezelése – áttekintés

Az Azure virtuális hálózatok biztonságos hálózati kapcsolatokat tesznek lehetővé virtuális gépek, az internet és más Azure-szolgáltatások (pl. az Azure SQL Database-adatbázis) között. A virtuális hálózatok alhálózatnak nevezett logikai szegmensekre oszthatók. Az alhálózatok segítségével szabályozható a hálózati forgalom, valamint biztonsági határként is használhatók. Egy virtuális géphez a telepítéskor általában egy virtuális hálózati adapter tartozik, amely egy alhálózathoz csatlakozik.

Az oktatóanyag végrehajtása során a következő erőforrások jönnek létre:

![Virtuális hálózat két alhálózattal](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Az a virtuális hálózat, amely segítségével a virtuális gépek kommunikálnak egymással és az internettel.
- *myFrontendSubnet* – A *myVNet* előtérbeli erőforrásai által használt alhálózat.
- *myPublicIPAddress* – Az internet felől a *myFrontendVM* elérésére használt nyilvános IP-cím.
- *myFrontentNic* – A *myFrontendVM* által a *myBackendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myFrontendVM* – Az internet és a *myBackendVM* közötti kommunikációra használt virtuális gép.
- *myBackendNSG* – A *myFrontendVM* és a *myBackendVM* közti kommunikációt szabályozó hálózati biztonsági csoport.
- *myBackendSubnet* – *myBackendNSG* csoporthoz társított, és a háttérbeli erőforrások által használt alhálózat.
- *myBackendNic* – A *myBackendVM* által *myFrontendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myBackendVM* -1433-as port használatával kommunikálni a virtuális gépi *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Ebben az oktatóanyagban egy virtuális hálózatot hozunk létre két alhálózattal, egy előtérbeli alhálózatot egy webalkalmazás üzemeltetéséhez, és egy háttérbeli alhálózatot egy adatbázis-kiszolgáló üzemeltetéséhez.

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

Ekkorra létrehoztunk egy hálózatot, és két alhálózatra osztottuk, amelyek közül az egyik az előtérbeli szolgáltatásokhoz, a másik a háttérbeli szolgáltatásokhoz tartozik. A következő szakaszban a virtuális gépeket hozzuk létre, majd csatlakoztatjuk azokat az alhálózatokhoz.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetők legyenek az interneten. A nyilvános IP-cím kiosztási módszere konfigurálható dinamikusnak vagy statikusnak. Alapértelmezés szerint a rendszer dinamikusan osztja ki a nyilvános IP-címeket. A dinamikus IP-címek az egyes virtuális gépek felszabadításakor felszabadulnak. Ennek következtében az IP-cím minden, virtuálisgép-felszabadítást is tartalmazó művelet során módosul.

A kiosztási módszer átállítható statikusra. Ez biztosítja, hogy a virtuális géphez hozzárendelve maradjon az IP-cím felszabadított állapotában is. A statikusan kiosztott IP-cím használata esetén nem adható meg az IP-cím. Ehelyett a rendszer osztja ki az IP-címet az elérhető címek készletéből.

Hozzon létre egy nyilvános IP-cím nevű *myPublicIPAddress* használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

A - AllocationMethod paramétert is módosíthatja `Static` egy statikus nyilvános IP-címet hozzárendelni.

## <a name="create-a-front-end-vm"></a>Előtérbeli virtuális gép létrehozása

A virtuális gépek számára egy virtuális hálózatot virtuális hálózati kártya (NIC) szükséges. Hozzon létre egy hálózati adapter által használt [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Állítsa be a felhasználónevet és jelszót a rendszergazdai fiókhoz az a virtuális gép használata a szükséges [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Ezek a hitelesítő adatok segítségével csatlakoztassa a virtuális Gépet, a további lépéseket:

```azurepowershell-interactive
$cred = Get-Credential
```

A virtuális gépek létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Biztonságos hálózati adatforgalom

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatók alhálózatokhoz vagy egyedi hálózati adapterekhez. Amikor egy NSG-t egy hálózati adapterhez társít, az csak a társított virtuális gépre vonatkozik. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra.

### <a name="network-security-group-rules"></a>Hálózat biztonsági csoportok szabályai

Az NSG-szabályok határozzák meg azokat a hálózatkezelési portokat, amelyeken engedélyezett vagy tiltott a forgalom. A szabályok között szerepelhetnek forrás és cél IP-címtartományok, így szabályozható az adatforgalom adott rendszerek vagy alhálózatok között. Az NSG-szabályok között megadható a prioritás is (1–4096). A szabályokat a rendszer prioritás szerinti sorrendben értékeli. A 100-as prioritású szabályt a rendszer a 200-as prioritású szabály előtt ellenőrzi.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat.

- **Virtuális hálózat** – A virtuális hálózatból kiinduló és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van.
- **Internet** – A kimenő forgalom engedélyezett, de a bejövő forgalom le van tiltva.
- **Terheléselosztó** – Engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem terheléselosztásos készletet használ, ezt a szabályt felül lehet bírálni.

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

## <a name="create-a-back-end-vm"></a>Háttérbeli virtuális gép létrehozása

A legegyszerűbb módja a háttér-virtuális gép létrehozása az ebben az oktatóanyagban az SQL-kiszolgálói lemezkép használatával. Ez az oktatóanyag csak az adatbázis-kiszolgáló a virtuális Gépet hoz létre, de nem ismertetik az adatbázis elérése közben.

Hozzon létre *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Állítsa be a felhasználónevet és jelszót a rendszergazdai fiókhoz az a virtuális Géphez a Get-Credential szükséges:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozzon létre *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

A használt kép SQL Server telepítve van, de nem szerepel ebben az oktatóanyagban. Azt mutatjuk be, hogyan konfigurálhatja a virtuális gépek webes forgalmat fog kezelni, hogy adatbázis kezelheti a virtuális gépek tartalmazza.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban virtuális gépekhez csatolva hozta létre és biztosította az Azure-hálózatokat. 

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

A következő oktatóanyag az Azure backup használatával virtuális gépek védelmének biztosítása adatok figyelésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Készítsen biztonsági másolatot a Windows virtuális gépek Azure-ban](./tutorial-backup-vms.md)
