---
title: "Útválasztási és a virtuális készülékek használata az Azure CLI 2.0 szabályozása |} Microsoft Docs"
description: "Megtudhatja, hogyan szabályozhatja az Azure CLI 2.0 használatával útválasztási és a virtuális készülékek."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Hozzon létre felhasználói útvonalakat (UDR) az Azure CLI 2.0 használatával

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [Sablon](virtual-network-create-udr-arm-template.md)
> * [PowerShell (Classic deployment)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Classic deployment)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók 

A következő CLI-verziók egyikével elvégezheti a feladatot: 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez 
- [Az Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) -erőforrás felügyeleti telepítési modell (Ez a cikk) a következő generációs parancssori felület

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Az alábbi minta Azure parancssori felület parancsait már a fenti forgatókönyv alapján létre egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először összeállítása a tesztkörnyezetben üzembe helyezésével [sablon](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), kattintson a **az Azure telepítéséhez**, cserélje le az alapértelmezett paraméterértékek, ha szükséges, és kövesse az utasításokat a portálon.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
Az útválasztási táblázatot és az előtér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket.

1. Hozzon létre egy útválasztási táblázatot előtér-alhálózat a [az hálózati útvonal-táblázat létrehozása](/cli/azure/network/route-table#create) parancs:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Kimenet:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Egy olyan útvonalat, amelyet az összes adatforgalmat küld a háttér-alhálózathoz (192.168.2.0/24) hozhat létre a **FW1** (192.168.0.4) virtuális gép használata a [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route#create) parancs:

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Kimenet:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Paraméterek:

    * **--útvonal-részes-táblanév**. Az útvonaltábla, ahova a rendszer hozzáadja az útvonal neve. A mi esetünkben *UDR-előtérbeli*.
    * **--address-prefix**. Az alhálózat, ahol a csomagok irányuló forgalomnál címelőtagot. A mi esetünkben *192.168.2.0/24*.
    * **--következő ugrás-típusú**. Típusú objektum forgalom kapnak. A lehetséges értékek: *VirtualAppliance*, *pedig*, *VNETLocal*, *Internet*, vagy *nincs*.
    * **--next-hop-ip-cím**. Következő ugrás IP-címet. A mi esetünkben *192.168.0.4*.

3. Futtassa a [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#update) parancs az előbb létrehozott útvonaltábla hozzárendelni a **előtér** alhálózati:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Kimenet:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Paraméterek:
    
    * **--vnet-name**. A VNet neve, ahol az alhálózatban. A mi esetünkben *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>A háttér-alhálózat UDR létrehozása

Az útvonaltábla és a háttér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket:

1. A következő parancsot egy útválasztási táblázatot a háttér-alhálózat létrehozásához:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. A következő parancsot egy útvonal létrehozása az útválasztási táblázatban az előtér-alhálózat (192.168.1.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. A következő parancsot az útvonaltáblában a társítja a **háttér** alhálózati:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>IP-továbbítás a FW1 engedélyezése

A hálózati adapter által használt IP-továbbítás engedélyezése **FW1**, kövesse az alábbi lépéseket:

1. Futtassa a [az hálózati nic megjelenítése](/cli/azure/network/nic#show) JMESPATH szűrő megjelenítése az aktuális parancsot **enable-ip-továbbítás** értékének **engedélyezése IP-továbbítás**. Beállításaként pedig *hamis*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Kimenet:

        false

2. A következő paranccsal IP-továbbítás engedélyezése:

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    Vizsgálja meg a folyamatos átviteli a konzol kimeneti, vagy az adott csak ellenőrzése hosszadalmas **enableIpForwarding** érték:

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Kimenet:

        true

    Paraméterek:

    **--ip-továbbítás**: *igaz* vagy *hamis*.

