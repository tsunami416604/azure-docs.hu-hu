---
title: Szabályozhatja az Azure Virtual Network - PowerShell – klasszikus Útválasztás |} A Microsoft Docs
description: Ismerje meg, hogyan vezérelheti az útválasztást a PowerShell-lel virtuális hálózatok |} Klasszikus
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 930676a396ae316ec761ba5d03ad1a1d0fd7a425
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232566"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Vezérelheti az útválasztást és a PowerShell-lel (klasszikus) virtuális berendezések használata

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-resource-manager/resource-manager-deployment-model.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. Megtekintheti a különféle eszközök dokumentációit a cikk tetején lévő egyik lehetőség kiválasztásával. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

A minta az alábbi parancsok várható már létrehozott egy egyszerű környezetet az Azure PowerShell a fenti forgatókönyv alapján. Ha szeretné a parancsokat a jelen dokumentum megjelenített, hozzon létre a környezet látható [virtuális hálózat létrehozása (klasszikus) PowerShell-lel](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az udr-t az előtér-alhálózat létrehozása
Az útvonaltábla és útvonal szükséges a fenti forgatókönyv alapján az előtérbeli alhálózat létrehozásához kövesse az alábbi lépéseket.

1. A következő paranccsal hozzon létre egy útválasztási táblázatot az előtér-alhálózat:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Futtassa a következő parancsot az útvonaltáblában a háttérbeli alhálózat (192.168.2.0/24) felé irányuló összes forgalmat útvonal létrehozása a **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Futtassa a következő parancsot az útvonaltáblában a társítása a **előtérbeli** alhálózat:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Az udr-t, a háttérbeli alhálózat létrehozása
Az útvonaltábla és útvonal szükséges a háttér-alhálózat forgatókönyv alapján létrehozásához hajtsa végre az alábbi lépéseket:

1. A következő paranccsal hozzon létre egy útválasztási táblázatot, a háttérbeli alhálózat:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Futtassa a következő parancsot az útválasztási táblázatban az előtérbeli alhálózat (192.168.1.0/24) felé irányuló összes forgalmat útvonal létrehozása a **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Futtassa a következő parancsot az útvonaltáblában a társítása a **háttérrendszer** alhálózat:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>A virtuális Gépen FW1 IP-továbbítás engedélyezése

Ahhoz, hogy az IP-továbbítási a FW1 virtuális gépen, kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot az IP-továbbítás állapotának ellenőrzéséhez:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. IP-továbbítási az engedélyezéséhez a következő parancsot a *FW1* virtuális Géphez:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
