---
title: 'Virtuális WAN: Virtuális központ útvonaltáblájának létrehozása az NVA-hoz: Azure PowerShell'
description: Virtual WAN virtuális központi útvonaltábla a hálózati virtuális berendezés forgalmának irányításához.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645106"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Virtuális központ útvonaltáblájának létrehozása a forgalom hálózati virtuális berendezésre való irányításához

Ez a cikk bemutatja, hogyan irányíthatja a forgalmat egy virtuális központ egy hálózati virtuális berendezés. 

![Virtuális WAN ábrája](./media/virtual-wan-route-table/vwanroute.png)

Ebben a cikkben az alábbiakkal fog megismerkedni:

* WAN létrehozása
* Elosztó létrehozása
* Hub virtuális hálózati kapcsolatok létrehozása
* Központi útvonal létrehozása
* Útválasztási táblázat létrehozása
* Az útvonaltábla alkalmazása

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ellenőrizze, hogy teljesítette-e az alábbi feltételeket:

1. Hálózati virtuális készülék (NVA) van. Ez egy harmadik féltől származó szoftver, amely et általában az Azure Marketplace-en egy virtuális hálózatban kiépített.
2. Az NVA hálózati adapterhez privát IP-cím van rendelve. 
3. Az NVA nem telepíthető a virtuális központban. Külön virtuális hálózatban kell telepíteni. Ebben a cikkben az NVA virtuális hálózat a továbbiakban: "DMZ virtuális hálózat".
4. A "DMZ virtuális hálózat" egy vagy több virtuális hálózat csatlakozik hozzá. Ebben a cikkben ezt a virtuális hálózatot "Indirekt küllővirtuális" néven nevezik. Ezek a virtuális hálózatok virtuális hálózatok virtuális társviszony-létesítés használatával csatlakoztathatók a DMZ virtuális hálózathoz.
5. Ellenőrizze, hogy már rendelkezik-e 2 virtuális hálózatlétrejöttével. Ezeket a küllős virtuális hálózatokat fogja használni. Ebben a cikkben a virtuális hálózat küllős címterei: 10.0.2.0/24 és 10.0.3.0/24. Ha a virtuális hálózat létrehozásáról szeretne tudni, olvassa el a Virtuális hálózat létrehozása a PowerShell használatával című [témakört.](../virtual-network/quick-create-powershell.md)
6. Győződjön meg arról, hogy nincsenek virtuális hálózati átjárók a virtuális hálózatokban.

## <a name="1-sign-in"></a><a name="signin"></a>1. Bejelentkezés

Győződjön meg arról, hogy a Resource Manager PowerShell-parancsmagok legújabb verzióját telepíti. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál. Ez azért fontos, mert a parancsmagok korábbi verziói nem tartalmazzák a feladatok elvégzéséhez szükséges aktuális értékeket.

1. Nyissa meg a PowerShell-konzolemelt jogosultságokkal, és jelentkezzen be az Azure-fiókjába. Ez a parancsmag kéri a bejelentkezési hitelesítő adatokat. A bejelentkezés után letölti a fiókbeállításokat, hogy azok elérhetők legyenek az Azure PowerShell számára.

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

## <a name="2-create-resources"></a><a name="rg"></a>2. Erőforrások létrehozása

1. Hozzon létre egy erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Hozzon létre egy virtuális WAN.Create a virtual WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Hozzon létre egy virtuális hubot.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Kapcsolatok létrehozása

Hozzon létre központi virtuális hálózati kapcsolatokat az Indirect Küllővirtuális hálózatból és a DMZ virtuális hálózatról a virtuális hubra.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Hozzon létre egy virtuális hub útvonal

Ebben a cikkben a közvetett küllős virtuális hálózat címterei 10.0.2.0/24 és 10.0.3.0/24, a DMZ NVA hálózati adapter privát IP-címe pedig 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Virtuális központi útvonaltábla létrehozása

Hozzon létre egy virtuális hub útvonaltáblát, majd alkalmazza rá a létrehozott útvonalat.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. A módosítások véglegesítése

Véglegesítse a módosításokat a virtuális hubba.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
