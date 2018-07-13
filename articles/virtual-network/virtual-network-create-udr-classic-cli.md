---
title: Szabályozhatja az Azure Virtual Network - CLI - klasszikus Útválasztás |} A Microsoft Docs
description: Ismerje meg, hogyan vezérelheti az útválasztást a virtuális hálózatok a klasszikus üzemi modellben az Azure CLI használatával
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 0b6c8da03c4a67aadb38280ba958a9b0feb88d1f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38678578"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Vezérelheti az útválasztást és a virtuális készülékek (klasszikus) használatával az Azure parancssori felület használata

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [vezérelheti az útválasztást és a virtuális készülékek használata a Resource Manager-alapú üzemi modellben](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

A minta Azure CLI-parancsokat az alábbi számíthat egy egyszerű környezetet állítunk már létrehozta a fenti forgatókönyv alapján. Ha szeretné a parancsokat a jelen dokumentum megjelenített, hozzon létre a környezet látható [virtuális hálózat létrehozása (klasszikus) az Azure CLI-vel](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az udr-t az előtér-alhálózat létrehozása
Az útvonaltábla és útvonal szükséges a fenti forgatókönyv alapján az előtérbeli alhálózat létrehozásához kövesse az alábbi lépéseket.

1. Futtassa a következő parancsot a klasszikus módra váltani:

    ```azurecli
    azure config mode asm
    ```

    Kimenet:

        info:    New mode is asm

2. A következő paranccsal hozzon létre egy útválasztási táblázatot az előtér-alhálózat:

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
   
   * **-l (vagy --location)**. Az Azure régióban, ahol létrejön az új NSG-t. A mi esetünkben *westus*.
   * **-n (vagy --name)**. Az új NSG neve. A mi esetünkben *NSG-FrontEnd*.
3. Futtassa a következő parancsot az útvonaltáblában a háttérbeli alhálózat (192.168.2.0/24) felé irányuló összes forgalmat útvonal létrehozása a **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Kimenet:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Paraméterek:
   
   * **-r (vagy--route-table-neve)**. Az útvonaltábla, ahova a rendszer hozzáadja az útvonal neve. A mi esetünkben *UDR-FrontEnd*.
   * **-a (vagy --address-prefix)**. Az alhálózat, amelyben a csomagok felé irányuló címelőtag. A mi esetünkben *192.168.2.0/24*.
   * **-t (vagy--next-hop-type)**. Objektum forgalom típusának megfelelő küld. Lehetséges értékek a következők *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, vagy *None*.
   * **-p (vagy--next-hop-ip-address**). Következő ugrás IP-címét. A mi esetünkben *192.168.0.4*.
4. A következő parancsot a létrehozott útválasztási táblázat hozzárendelése a **előtérbeli** alhálózat:

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
   
   * **-t (vagy--vnet-name)**. A VNet neve, ahol az alhálózaton-e. A mi esetünkben *TestVNet*.
   * **-n (vagy--subnet-name**. Az útválasztási táblázat hozzáadódik az alhálózat neve. A mi esetünkben *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Az udr-t, a háttérbeli alhálózat létrehozása
Az útvonaltábla és útvonal szükséges a háttérbeli alhálózat forgatókönyv alapján létrehozásához hajtsa végre az alábbi lépéseket:

1. A következő paranccsal hozzon létre egy útválasztási táblázatot, a háttérbeli alhálózat:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Futtassa a következő parancsot az útválasztási táblázatban az előtérbeli alhálózat (192.168.1.0/24) felé irányuló összes forgalmat útvonal létrehozása a **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Futtassa a következő parancsot az útvonaltáblában a társítása a **háttérrendszer** alhálózat:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

