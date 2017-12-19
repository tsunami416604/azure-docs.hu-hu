---
title: "Szabályozhatja az Azure Virtual Network - PowerShell – klasszikus Útválasztás |} Microsoft Docs"
description: "Megtudhatja, hogyan szabályozza a PowerShell használatával Vnetek Útválasztás |} Klasszikus"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: e9564d223cb85529f1fa97bc398d35c6debcedae
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Szabályozhatja az Útválasztás és használni a virtuális készülékeket (klasszikus) PowerShell használatával

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [Sablon](virtual-network-create-udr-arm-template.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-resource-manager/resource-manager-deployment-model.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. Ez a cikk tetején egyik lehetőség kiválasztásával megtekintheti a különféle eszközök dokumentációit. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

A minta Azure PowerShell az alábbi parancsok várt már létrehozott egy egyszerű környezetben a fenti forgatókönyv alapján. Ha szeretné a parancsokat a jelen dokumentum megjelenített, hozza létre a környezet megjelenő [VNet létrehozása (klasszikus) használatával PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
Az útválasztási táblázatot és az előtér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket.

1. Futtassa a következő parancsot az előtér-alhálózat egy útválasztási táblázatot létrehozásához:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. A következő parancsot egy útvonal létrehozása az útvonaltáblában a háttér-alhálózat (192.168.2.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. A következő parancsot az útvonaltáblában a társítja a **előtér** alhálózati:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>A háttér-alhálózat UDR létrehozása
Az útvonaltábla és a háttér alhálózat forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket:

1. A következő parancsot egy útválasztási táblázatot a háttér-alhálózat létrehozásához:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. A következő parancsot egy útvonal létrehozása az útválasztási táblázatban az előtér-alhálózat (192.168.1.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. A következő parancsot az útvonaltáblában a társítja a **háttér** alhálózati:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>A virtuális Gépre FW1 IP-továbbítás engedélyezése

Ahhoz, hogy az IP-továbbítást a virtuális gép FW1, kövesse az alábbi lépéseket:

1. A következő parancsot az IP-továbbítás állapotának ellenőrzéséhez:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. A következő parancsot a továbbítási IP-címek a *FW1* VM:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
