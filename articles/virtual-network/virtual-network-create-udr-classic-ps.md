---
title: Útválasztás vezérlése Azure-VNet – klasszikus PowerShell
description: Megtudhatja, hogyan vezérelheti az útválasztást a virtuális hálózatok a PowerShell használatával | Klasszikus
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 4170418b4f53277a8f4306fe2835286c8ac99dee
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186356"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Az Útválasztás és a virtuális berendezések használata (klasszikus) a PowerShell használatával

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-resource-manager/resource-manager-deployment-model.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különböző eszközök dokumentációját a cikk tetején lévő egyik lehetőség kiválasztásával tekintheti meg. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Az alábbi minta Azure PowerShell parancsok a fenti forgatókönyv alapján már létrehozott egyszerű környezetet várnak. Ha a jelen dokumentumban megjelenő parancsokat szeretné futtatni, hozza létre a [VNet létrehozása (klasszikus) nevű környezetet a PowerShell használatával](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
A fenti forgatókönyv alapján az előtér-alhálózathoz szükséges útválasztási táblázat és útvonal létrehozásához kövesse az alábbi lépéseket.

1. A következő parancs futtatásával hozzon létre egy útválasztási táblázatot az előtér-alhálózathoz:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. A következő parancs futtatásával hozzon létre egy útvonalat az útválasztási táblában a háttér-alhálózatra (192.168.2.0/24) irányuló összes forgalom küldéséhez a **FW1** virtuális géphez (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Futtassa az alábbi parancsot az útválasztási táblázat az előtér- **alhálózathoz** való hozzárendeléséhez:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>A UDR létrehozása a háttérbeli alhálózathoz
Ha a forgatókönyv alapján a háttérbeli alhálózathoz szükséges útválasztási táblázatot és útvonalat szeretné létrehozni, hajtsa végre a következő lépéseket:

1. Futtassa a következő parancsot egy útválasztási táblázat létrehozásához a háttérbeli alhálózathoz:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. A következő parancs futtatásával hozzon létre egy útvonalat az útválasztási táblában az előtér-alhálózatra (192.168.1.0/24) irányuló összes forgalom küldéséhez a **FW1** virtuális géphez (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. A következő parancs futtatásával társítsa az útválasztási táblázatot a **háttér** -alhálózathoz:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>IP-továbbítás engedélyezése a FW1 virtuális gépen

Ha engedélyezni szeretné az IP-továbbítást a FW1 virtuális gépen, hajtsa végre a következő lépéseket:

1. Futtassa az alábbi parancsot az IP-továbbítás állapotának vizsgálatához:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Futtassa a következő parancsot a *FW1* virtuális gép IP-továbbításának engedélyezéséhez:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
