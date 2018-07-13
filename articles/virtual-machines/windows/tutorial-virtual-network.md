---
title: Oktatóanyag – Azure-alapú virtuális hálózatok létrehozása és felügyelete Windows rendszerű virtuális gépeken | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-beli virtuális hálózatokat Windows rendszerű virtuális gépekhez az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8bf8388829504b18e71ec597d21b844e29a82346
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931134"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Azure-alapú virtuális hálózatok létrehozása és felügyelete Windows rendszerű virtuális gépeken az Azure PowerShell-lel

Az Azure-beli virtuális gépek Azure hálózatkezelést használnak a belső és külső hálózati kommunikációhoz. Ez az oktatóanyag végigvezeti két virtuális gép telepítésén és az Azure hálózatkezelés konfigurálásán ezen virtuális gépekhez. Az oktatóanyagban szereplő példák feltételezik, hogy a virtuális gépek üzemeltetnek egy webalkalmazást egy adatbázis-alapú háttérrendszerrel, de az oktatóanyag során nem telepítünk ilyen alkalmazást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

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
- *myBackendVM* – A *myFrontendVM* virtuális géppel a 1433-as porton keresztül kommunikáló virtuális gép.


## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Ebben az oktatóanyagban egy virtuális hálózatot hozunk létre két alhálózattal, egy előtérbeli alhálózatot egy webalkalmazás üzemeltetéséhez, és egy háttérbeli alhálózatot egy adatbázis-kiszolgáló üzemeltetéséhez.

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példa létrehoz egy *myRGNetwork* nevű erőforráscsoportot az *EastUS* régióban:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Alhálózat-konfigurációk létrehozása

Hozzon létre egy alhálózati konfigurációt *myFrontendSubnet* néven a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) paranccsal:

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Továbbá hozzon létre egy alhálózati konfigurációt *myBackendSubnet* néven:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

A [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal hozzon létre egy VNET-et *myVNet* néven a *myFrontendSubnet* és a *myBackendSubnet* használatával:

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

Hozzon létre egy nyilvános IP-címet *myPublicIPAddress* néven a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) paranccsal:

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Ha az -AllocationMethod paramétert `Static` értékre állítja, egy statikus nyilvános IP-címet oszt ki.

## <a name="create-a-front-end-vm"></a>Előtérbeli virtuális gép létrehozása

Ahhoz, hogy a virtuális gép kommunikálhasson a virtuális hálózatban, szüksége van egy virtuális hálózati adapterre (NIC). Hozzon létre egy hálózati adaptert a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) paranccsal:

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal. Ezekkel a hitelesítő adatokkal csatlakozhat a virtuális géphez a további lépésekben:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal.

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

A [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) paranccsal hozzon létre egy bejövő szabályt *myFrontendNSGRule* néven, amely lehetővé teszi a *myFrontendVM* virtuális géppel folytatott bejövő webes forgalmat:

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

Ha létrehoz egy NSG-t a háttéralhálózat számára, azzal a *myBackendVM* belső forgalmát korlátozhatja csak a *myFrontendVM* gépről érkező forgalomra. Az alábbi példa egy *myBackendNSGRule* nevű NSG-szabályt hoz létre:

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

Adjon hozzá egy hálózati biztonsági csoportot *myFrontendNSG* néven a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) paranccsal:

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Most adjon hozzá egy másik hálózati biztonsági csoportot is *myBackendNSG* néven szintén a New-AzureRmNetworkSecurityGroup paranccsal:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adja hozzá a hálózati biztonsági csoportokat az alhálózatokhoz:

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

Az oktatóanyagban lévő háttérbeli virtuális gépek legegyszerűbben egy SQL Server-rendszerképpel hozhatók létre. Ez az oktatóanyag csak létrehozza a virtuális gépet az adatbázis-kiszolgálóval, az adatbázis elérésével kapcsolatos információkat nem részletezi.

A *myBackendNic* létrehozása:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a Get-Credential paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a *myBackendNic* hálózati adaptert.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

A használt rendszerképen telepítve van az SQL Server, azonban azt ebben az oktatóanyagban nem használjuk. Azért szerepel itt, hogy bemutathassuk, hogyan konfigurálható egy virtuális gép a webes forgalom kezelésére és egy másik az adatbázis-felügyelet intézésére.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban virtuális gépekhez csatolva hozta létre és biztosította az Azure-hálózatokat. 

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

Folytassa a következő oktatóanyaggal, amely a virtuális gépeken lévő adatok Azure Backuppal való biztonságba helyezésének monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Windows rendszerű virtuális gépek biztonsági mentése az Azure-ban](./tutorial-backup-vms.md)
