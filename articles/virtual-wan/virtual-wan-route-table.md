---
title: Hozzon létre egy Azure virtuális WAN virtuális központ útválasztási táblázatot az NVA figyelmeztetik |} A Microsoft Docs
description: Virtuális WAN virtuális központ útválasztási táblázatot figyelmeztetik a forgalmat egy hálózati virtuális berendezésre.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: fc8dd6770efa1c057a56374ddc0094c2d88d2eb5
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335731"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>A forgalom hálózati virtuális berendezésre figyelmeztetik virtuális központ útválasztási táblázat létrehozása

Ez a cikk bemutatja, hogyan figyelmeztetik egy virtuális központtal forgalmát egy hálózati virtuális berendezésre való. 

![A Virtual WAN ábrája](./media/virtual-wan-route-table/vwanroute.png)

Ebben a cikkben az alábbiakkal fog megismerkedni:

* WAN létrehozása
* Elosztó létrehozása
* Eseményközpont létrehozása virtuális hálózati kapcsolatok
* Hub útvonal létrehozása
* Útválasztási táblázat létrehozása
* Az útvonaltábla alkalmazása

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Győződjön meg arról, hogy a következő feltételek teljesüléséről:

1. Egy hálózati virtuális készüléket (NVA) rendelkezik. Ez az egy külső szoftver tetszőleges általában kiépített Azure Marketplace-ről egy virtuális hálózaton.
2. Rendelkezünk egy privát IP-címet hozzárendelni az NVA hálózati interfészhez. 
3. Az nva-t a virtuális központ nem telepíthetők. Egy külön virtuális hálózatban kell telepíteni. Ebben a cikkben az NVA virtuális hálózatok közötti nevezzük a szegélyhálózat (DMZ) VNet.
4. A szegélyhálózat (DMZ) VNet előfordulhat, hogy rendelkezik egy, vagy túl sok virtuális hálózathoz csatlakozik hozzá. Ebben a cikkben a virtuális hálózat neve "Közvetett küllő virtuális hálózat". Ezek a virtuális hálózatok a DMZ-t virtuális hálózathoz virtuális hálózatok közötti társviszony segítségével csatlakoztathatók.
5. Győződjön meg arról, hogy már létrehozott 2 virtuális hálózatok. Ezeket fogja használni, mint a küllő virtuális hálózatok. Ebben a cikkben a VNet-küllő címterek 10.0.2.0/24 és 10.0.3.0/24. Ha a virtuális hálózat létrehozása információra van szüksége, tekintse meg [hozzon létre egy virtuális hálózathoz a PowerShell használatával](../virtual-network/quick-create-powershell.md).
6. Győződjön meg róla, minden olyan virtuális hálózatok nem virtuális hálózati átjárók vannak.

## <a name="signin"></a>1. Bejelentkezés

Ellenőrizze, hogy a Resource Manager PowerShell-parancsmagok legújabb verzióját telepíti. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál. Ez azért fontos, mert a parancsmagok korábbi verziói nem tartalmazzák a feladatok elvégzéséhez szükséges aktuális értékeket.

1. Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és jelentkezzen be az Azure-fiókjával. Ez a parancsmag bejelentkezési hitelesítő adatokat kér. Bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell-lel.

   ```powershell
   Connect-AzAccount
   ```
2. Olvassa be az Azure-előfizetések listáját.

   ```powershell
   Get-AzSubscription
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="rg"></a>2. Erőforrások létrehozása

1. Hozzon létre egy erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Hozzon létre egy virtuális WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Hozzon létre egy virtuális központtal.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
   ```

## <a name="connections"></a>3. Kapcsolatok létrehozása

Hub létrehozása virtuális hálózati kapcsolatok közvetett küllő virtuális hálózat és a szegélyhálózat (DMZ) virtuális hálózat, a virtuális központ.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Hozzon létre egy virtuális központ útvonal

Ebben a cikkben a közvetett küllő virtuális hálózat címterei 10.0.2.0/24 és 10.0.3.0/24, és a DMZ NVA hálózati adapter magánhálózati IP-cím 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Virtuális központ útválasztási táblázat létrehozása

Hozzon létre egy virtuális központ útválasztási táblázatot, majd a alkalmazni rá a létrehozott útvonal.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. A változtatások véglegesítése

A virtuális központ, a módosítások véglegesítéséhez.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
