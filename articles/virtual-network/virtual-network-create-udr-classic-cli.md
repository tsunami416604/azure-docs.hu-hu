---
title: Útválasztás vezérlése Azure-Virtual Network – CLI-klasszikus | Microsoft Docs
description: Ismerje meg, hogyan vezérelheti az útválasztást a virtuális hálózatok az Azure CLI használatával a klasszikus üzemi modellben
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058788"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Az Útválasztás és a virtuális készülékek használata (klasszikus) az Azure CLI használatával

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. [A Resource Manager-alapú üzemi modellben a virtuális berendezések útválasztását és használatát is szabályozhatja](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Az alábbi minta Azure CLI-parancsok a fenti forgatókönyv alapján már létrehozott egyszerű környezetet várnak. Ha a dokumentumban megjelenő parancsokat szeretné futtatni, hozza létre a [VNet (klasszikus) létrehozása az Azure CLI használatával](virtual-networks-create-vnet-classic-cli.md)című részében bemutatott környezetet.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
A fenti forgatókönyv alapján az előtér-alhálózathoz szükséges útválasztási táblázat és útvonal létrehozásához kövesse az alábbi lépéseket.

1. A klasszikus módra váltáshoz futtassa a következő parancsot:

    ```azurecli
    azure config mode asm
    ```

    Kimenet:

        info:    New mode is asm

2. A következő parancs futtatásával hozzon létre egy útválasztási táblázatot az előtér-alhálózathoz:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Kimenet:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Paraméterek:
   
   * **-l (vagy --location)** . Az Azure-régió, ahol az új NSG létre lesz hozva. Ebben az esetben a *westus*.
   * **-n (vagy --name)** . Az új NSG neve. Ebben az esetben a *NSG-FrontEnd*.
3. A következő parancs futtatásával hozzon létre egy útvonalat az útválasztási táblában a háttér-alhálózatra (192.168.2.0/24) irányuló összes forgalom küldéséhez a **FW1** virtuális géphez (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Kimenet:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Paraméterek:
   
   * **-r (vagy--Route-Table-Name)** . Annak az útválasztási táblának a neve, ahová az útvonalat hozzá kívánja adni. Ebben az esetben a *UDR-FrontEnd*.
   * **-a (vagy --address-prefix)** . Annak az alhálózatnak a címe, ahol a csomagokat szánják. Ebben az esetben a *192.168.2.0/24*.
   * **-t (vagy--Next-hop-Type)** . A rendszer elküldi az objektum adatforgalmának típusát. A lehetséges értékek a következők: *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*vagy *none*.
   * **-p (vagy--Next-hop-IP-cím**). A következő ugrás IP-címe. Ebben az esetben a *192.168.0.4*.
4. Futtassa a következő parancsot az előtér-alhálózattal létrehozott útválasztási táblázat hozzárendeléséhez:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Kimenet:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Paraméterek:
   
   * **-t (vagy--vnet-Name)** . Azon VNet neve, ahol az alhálózat található. A mi esetünkben *TestVNet*.
   * **-n (vagy--subnet-Name**. Annak az alhálózatnak a neve, amelyhez az útválasztási tábla hozzá lesz adva. A mi esetünkben *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>A UDR létrehozása a háttérbeli alhálózathoz
A forgatókönyv alapján a háttérbeli alhálózathoz szükséges útválasztási táblázat és útvonal létrehozásához hajtsa végre a következő lépéseket:

1. Futtassa a következő parancsot egy útválasztási táblázat létrehozásához a háttérbeli alhálózathoz:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. A következő parancs futtatásával hozzon létre egy útvonalat az útválasztási táblában az előtér-alhálózatra (192.168.1.0/24) irányuló összes forgalom küldéséhez a **FW1** virtuális géphez (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. A következő parancs futtatásával társítsa az útválasztási táblázatot a **háttér** -alhálózathoz:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

