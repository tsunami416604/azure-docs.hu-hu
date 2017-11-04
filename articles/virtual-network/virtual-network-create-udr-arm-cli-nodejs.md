---
title: "Útválasztási és a virtuális készülékek használata az Azure CLI 1.0 szabályozása |} Microsoft Docs"
description: "Megtudhatja, hogyan ellenőrzésére Útválasztás és a virtuális készülékek használata az Azure CLI 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2017
ms.author: jdial
ms.openlocfilehash: 5f21bc7a4fcd9507ea9d6b2b752a2328a7b834f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-10"></a>Hozzon létre felhasználói útvonalakat (UDR) az Azure CLI 1.0 használatával

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [Sablon](virtual-network-create-udr-arm-template.md)
> * [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
> * [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

Hozzon létre egyéni Útválasztás és a virtuális készülékek, az Azure parancssori felület használatával.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók 

A következő CLI-verziók egyikével elvégezheti a feladatot: 

- [Az Azure CLI 1.0](#Create-the-UDR-for-the-front-end-subnet) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](virtual-network-create-udr-arm-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli. 


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Az alábbi minta Azure parancssori felület parancsait már a fenti forgatókönyv alapján létre egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először összeállítása a tesztkörnyezetben üzembe helyezésével [sablon](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), kattintson a **az Azure telepítéséhez**, cserélje le az alapértelmezett paraméterértékek, ha szükséges, és kövesse az utasításokat a portálon.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Az előtér-alhálózat UDR létrehozása
Az útválasztási táblázatot és az előtér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket.

1. Futtassa a következő parancsot az előtér-alhálózat egy útválasztási táblázatot létrehozásához:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Kimenet:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Paraméterek:
   
   * **-g (vagy --resource-group)**. Ahol a UDR létrejön az erőforráscsoport neve. A mi esetünkben *TestRG*.
   * **-l (vagy --location)**. Azure-régió, ahol az új UDR létrejön. A mi esetünkben *westus*.
   * **-n (vagy --name)**. Az új UDR nevét. A mi esetünkben *UDR-előtérbeli*.
2. A következő parancsot egy útvonal létrehozása az útvonaltáblában a háttér-alhálózat (192.168.2.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Kimenet:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Paraméterek:
   
   * **-r (vagy--útvonal-részes-táblanév)**. Az útvonaltábla, ahova a rendszer hozzáadja az útvonal neve. A mi esetünkben *UDR-előtérbeli*.
   * **-a (vagy --address-prefix)**. Az alhálózat, ahol a csomagok irányuló forgalomnál címelőtagot. A mi esetünkben *192.168.2.0/24*.
   * **-y (vagy--tovább-hop-type)**. Típusú objektum forgalom kapnak. A lehetséges értékek: *VirtualAppliance*, *pedig*, *VNETLocal*, *Internet*, vagy *nincs*.
   * **-p (vagy--next-hop-ip-cím**). Következő ugrás IP-címet. A mi esetünkben *192.168.0.4*.
3. A következő parancsot az előbb létrehozott útvonaltábla hozzárendelni a **előtér** alhálózati:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Kimenet:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Paraméterek:
   
   * **-e (vagy--vnet-name)**. A VNet neve, ahol az alhálózatban. A mi esetünkben *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>A háttér-alhálózat UDR létrehozása
Az útvonaltábla és a háttér-alhálózat, a fenti forgatókönyv alapján szükséges útvonal létrehozásához kövesse az alábbi lépéseket:

1. A következő parancsot egy útválasztási táblázatot a háttér-alhálózat létrehozásához:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. A következő parancsot egy útvonal létrehozása az útválasztási táblázatban az előtér-alhálózat (192.168.1.0/24) irányuló összes forgalom küldése a a **FW1** virtuális gép (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. A következő parancsot az útvonaltáblában a társítja a **háttér** alhálózati:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>IP-továbbítás a FW1 engedélyezése
A hálózati adapter által használt IP-továbbítás engedélyezése **FW1**, kövesse az alábbi lépéseket:

1. Futtassa a parancsot, amely a következő, és figyelje meg a következő **engedélyezése IP-továbbítás**. Beállításaként pedig *hamis*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Kimenet:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. A következő paranccsal IP-továbbítás engedélyezése:

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Kimenet:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Paraméterek:
   
   * **-f (vagy--enable-ip-továbbítás)**. *Igaz* vagy *hamis*.

