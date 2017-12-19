---
title: "Szabályozhatja az Azure Virtual Network - CLI - klasszikus Útválasztás |} Microsoft Docs"
description: "Megtudhatja, hogyan szabályozhatja az Azure parancssori felület használatával a klasszikus üzembe helyezési modellel Vnetek Útválasztás"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 8fcb98723e7e872c932908e3456dc8680deb0901
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Szabályozhatja az Útválasztás és használni a virtuális készülékeket (klasszikus) az Azure parancssori felület használatával

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [Sablon](virtual-network-create-udr-arm-template.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [szabályozhatja az Útválasztás és a virtuális készülékek használata a Resource Manager üzembe helyezési modellel](virtual-network-create-udr-arm-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Az alábbi minta Azure parancssori felület parancsait már a fenti forgatókönyv alapján létre egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, hozza létre a környezet megjelenő [hozzon létre egy virtuális hálózat (klasszikus) az Azure parancssori felület használatával](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
Az útválasztási táblázatot és az előtér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket.

1. Futtassa a következő parancsot a klasszikus mód:

    ```azurecli
    azure config mode asm
    ```

    Kimenet:

        info:    New mode is asm

2. Futtassa a következő parancsot az előtér-alhálózat egy útválasztási táblázatot létrehozásához:

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
   
   * **-l (vagy --location)**. Azure-régió, ahol létrejön az új NSG. A mi esetünkben *westus*.
   * **-n (vagy --name)**. Az új NSG neve. A mi esetünkben *NSG-előtérbeli*.
3. A következő parancsot egy útvonal létrehozása az útvonaltáblában a háttér-alhálózat (192.168.2.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Kimenet:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Paraméterek:
   
   * **-r (vagy--útvonal-részes-táblanév)**. Az útvonaltábla, ahova a rendszer hozzáadja az útvonal neve. A mi esetünkben *UDR-előtérbeli*.
   * **-a (vagy --address-prefix)**. Az alhálózat, ahol a csomagok irányuló forgalomnál címelőtagot. A mi esetünkben *192.168.2.0/24*.
   * **-t (vagy--tovább-hop-type)**. Típusú objektum forgalom kapnak. A lehetséges értékek: *VirtualAppliance*, *pedig*, *VNETLocal*, *Internet*, vagy *nincs*.
   * **-p (vagy--next-hop-ip-cím**). Következő ugrás IP-címet. A mi esetünkben *192.168.0.4*.
4. Rendelje hozzá a létrehozott útválasztási táblázatot a következő parancsot a **előtér** alhálózati:

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
   
   * **-t (vagy--vnet-name)**. A VNet neve, ahol az alhálózatban. A mi esetünkben *TestVNet*.
   * **-n (vagy--alhálózatneve**. Az útvonaltábla nem kerülnek be az alhálózat neve. A mi esetünkben *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>A háttér-alhálózat UDR létrehozása
Az útvonaltábla és a háttér-alhálózat alapján a forgatókönyvhöz szükséges útvonal létrehozásához kövesse az alábbi lépéseket:

1. A következő parancsot egy útválasztási táblázatot a háttér-alhálózat létrehozásához:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. A következő parancsot egy útvonal létrehozása az útválasztási táblázatban az előtér-alhálózat (192.168.1.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. A következő parancsot az útvonaltáblában a társítja a **háttér** alhálózati:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

