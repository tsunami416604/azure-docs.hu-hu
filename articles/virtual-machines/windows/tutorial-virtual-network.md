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
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 568631705b590bb2ee312b9519164be17c8443ab
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984239"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Hozzon létre, és a Windows virtuális gépek az Azure PowerShell-lel az Azure virtuális hálózatok kezelése

Az Azure-beli virtuális gépek Azure hálózatkezelést használnak a belső és külső hálózati kommunikációhoz. Ez az oktatóanyag végigvezeti két virtuális gép telepítésén és az Azure hálózatkezelés konfigurálásán ezen virtuális gépekhez. Ebben az oktatóanyagban szereplő példák feltételezik, hogy, hogy a virtuális gépek üzemeltetnek egy webalkalmazást egy adatbázis-alapú háttérrendszerrel, azonban egy alkalmazás nincs telepítve az oktatóanyagban. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása


## <a name="vm-networking-overview"></a>Virtuális gépek hálózatkezelése – áttekintés

Az Azure virtuális hálózatok biztonságos hálózati kapcsolatokat tesznek lehetővé virtuális gépek, az internet és más Azure-szolgáltatások (pl. az Azure SQL Database-adatbázis) között. A virtuális hálózatok alhálózatnak nevezett logikai szegmensekre oszthatók. Az alhálózatok segítségével szabályozható a hálózati forgalom, valamint biztonsági határként is használhatók. Egy virtuális géphez a telepítéskor általában egy virtuális hálózati adapter tartozik, amely egy alhálózathoz csatlakozik.

Az oktatóanyag végrehajtása során a következő erőforrások jönnek létre:

![Virtuális hálózat két alhálózattal](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Az a virtuális hálózat, amely segítségével a virtuális gépek kommunikálnak egymással és az internettel.
- *myFrontendSubnet* – A *myVNet* előtérbeli erőforrásai által használt alhálózat.
- *myPublicIPAddress* – Az internet felől a *myFrontendVM* elérésére használt nyilvános IP-cím.
- *myFrontendNic* – A *myFrontendVM* által a *myBackendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myFrontendVM* – Az internet és a *myBackendVM* közötti kommunikációra használt virtuális gép.
- *myBackendNSG* – A *myFrontendVM* és a *myBackendVM* közti kommunikációt szabályozó hálózati biztonsági csoport.
- *myBackendSubnet* – *myBackendNSG* csoporthoz társított, és a háttérbeli erőforrások által használt alhálózat.
- *myBackendNic* – A *myBackendVM* által *myFrontendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myBackendVM* – A *myFrontendVM* virtuális géppel a 1433-as porton keresztül kommunikáló virtuális gép.


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="create-subnet"></a>Alhálózat létrehozása 

Ebben az oktatóanyagban egy virtuális hálózatot hozunk létre két alhálózattal, egy előtérbeli alhálózatot egy webalkalmazás üzemeltetéséhez, és egy háttérbeli alhálózatot egy adatbázis-kiszolgáló üzemeltetéséhez.

Virtuális hálózat létrehozása előtt hozzon létre egy erőforrás csoport [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). A következő példa létrehoz egy *myRGNetwork* nevű erőforráscsoportot az *EastUS* régióban:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Hozzon létre egy alhálózati konfigurációt nevű *myFrontendSubnet* használatával [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Továbbá hozzon létre egy alhálózati konfigurációt *myBackendSubnet* néven:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy VNETET nevű *myVNet* használatával *myFrontendSubnet* és *myBackendSubnet* használatával [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Ekkorra létrehoztunk egy hálózatot, és két alhálózatra osztottuk, amelyek közül az egyik az előtérbeli szolgáltatásokhoz, a másik a háttérbeli szolgáltatásokhoz tartozik. A következő szakaszban a virtuális gépeket hozzuk létre, majd csatlakoztatjuk azokat az alhálózatokhoz.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetők legyenek az interneten. A nyilvános IP-cím kiosztási módszere konfigurálható dinamikusnak vagy statikusnak. Alapértelmezés szerint a rendszer dinamikusan osztja ki a nyilvános IP-címeket. A dinamikus IP-címek az egyes virtuális gépek felszabadításakor felszabadulnak. Ennek következtében az IP-cím minden, virtuálisgép-felszabadítást is tartalmazó művelet során módosul.

A kiosztási módszert statikusra, amely gondoskodik arról, hogy az IP-cím felszabadított állapotában során is egy virtuális géphez hozzárendelt marad beállítható. Ha statikus IP-címet használ, az IP-cím nem adható meg. Ehelyett, akkor foglalja le az elérhető címek készletéből.

Hozzon létre egy nyilvános IP-címet *myPublicIPAddress* használatával [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Ha az -AllocationMethod paramétert `Static` értékre állítja, egy statikus nyilvános IP-címet oszt ki.

## <a name="create-a-front-end-vm"></a>Előtérbeli virtuális gép létrehozása

Ahhoz, hogy a virtuális gép kommunikálhasson a virtuális hálózatban, szüksége van egy virtuális hálózati adapterre (NIC). Hozzon létre egy hálózati adapter [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
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

A virtuális gépek létrehozása [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
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

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatók alhálózatokhoz vagy egyedi hálózati adapterekhez. Az NSG-t kapcsolódó és a hálózat adapter csak vonatkozik a társított virtuális Gépre. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra.

### <a name="network-security-group-rules"></a>Hálózat biztonsági csoportok szabályai

Az NSG-szabályok határozzák meg azokat a hálózatkezelési portokat, amelyeken engedélyezett vagy tiltott a forgalom. A szabályok között szerepelhetnek forrás és cél IP-címtartományok, így szabályozható az adatforgalom adott rendszerek vagy alhálózatok között. Az NSG-szabályok között megadható a prioritás is (1–4096). A szabályokat a rendszer prioritás szerinti sorrendben értékeli. A 100-as prioritású szabályt a rendszer a 200-as prioritású szabály előtt ellenőrzi.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályok nem törölhetők, de a legalacsonyabb prioritást rendelték hozzájuk, mert azok az Ön által létrehozott szabályok szerint felülbírálható.

- **Virtuális hálózat** – A virtuális hálózatból kiinduló és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van.
- **Internet** – A kimenő forgalom engedélyezett, de a bejövő forgalom le van tiltva.
- **Terheléselosztó** – Engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem terheléselosztásos készletet használ, ezt a szabályt felül lehet bírálni.

### <a name="create-network-security-groups"></a>Hálózati biztonsági csoportok létrehozása

Create an inbound rule named *myFrontendNSGRule* to allow incoming web traffic on *myFrontendVM* using [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
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
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
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

Adjon hozzá egy hálózati biztonsági csoport nevű *myFrontendNSG* használatával [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Ezután adjon hozzá egy hálózati biztonsági csoport nevű *myBackendNSG* New-AzNetworkSecurityGroup használatával:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adja hozzá a hálózati biztonsági csoportokat az alhálózatokhoz:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Háttérbeli virtuális gép létrehozása

Az oktatóanyagban lévő háttérbeli virtuális gépek legegyszerűbben egy SQL Server-rendszerképpel hozhatók létre. Ez az oktatóanyag csak létrehozza a virtuális gépet az adatbázis-kiszolgálóval, az adatbázis elérésével kapcsolatos információkat nem részletezi.

A *myBackendNic* létrehozása:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
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
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Ebben a példában a rendszerképre telepített SQL Server, de ebben az oktatóanyagban nem használható. Megmutatjuk, hogyan konfigurálhatja a webes forgalom kezelése virtuális gépek és a egy virtuális gép adatbázis Eszközfelügyelet részét képezi.

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
