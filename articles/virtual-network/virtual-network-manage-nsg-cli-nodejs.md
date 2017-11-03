---
title: "Hálózati biztonsági csoport – Azure CLI 1.0 kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a hálózati biztonsági csoportok használata az Azure parancssori felület (CLI) 1.0-s."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e53c3ff2ffbef95d6b72ca6afb3b4de377f0389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-groups-using-the-azure-cli-10"></a>Hálózati biztonsági csoportok használata az Azure CLI 1.0 kezelése

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók 

A következő CLI-verziók egyikével elvégezheti a feladatot: 

- [Azure CLI 1.0](#View-existing-NSGs) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez 
- [Az Azure CLI 2.0](virtual-network-manage-nsg-arm-cli.md) -erőforrás felügyeleti telepítési modell (Ez a cikk) a következő generációs parancssori felület

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>Információk beolvasása
Megtekintheti a meglévő NSG-ket, szabályok lekérdezni egy meglévő NSG-t, és megtudhatja, milyen erőforrásokat egy NSG társítva.

### <a name="view-existing-nsgs"></a>Meglévő NSG-k megtekintése
Egy adott erőforráscsoportban NSG-k listájának megtekintéséhez futtassa a `azure network nsg list` parancsot a lent látható módon.

```azurecli
azure network nsg list --resource-group RG-NSG
```

Várt kimenet:

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK

### <a name="list-all-rules-for-an-nsg"></a>A szabályok egy NSG listázása
Az NSG nevű szabályainak megtekintéséhez **NSG-előtérbeli**- ben futtassa a `azure network nsg show` parancsot a lent látható módon. 

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd
```

Várt kimenet:

    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

> [!NOTE]
> Is `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` szabálygyűjtemény listát a **NSG-előtérbeli** NSG.
>

### <a name="view-nsg-associations"></a>NSG-társítások megtekintése

Milyen erőforrások megtekintése a **NSG-előtérbeli** NSG, futtassa az associate a `azure network nsg show` parancsot a lent látható módon. Láthatja, hogy az egyetlen különbség a használatát a **--json** paraméter.

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json
```

Keresse meg a **hálózati illesztők** és **alhálózatok** tulajdonságok alább látható módon:

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

A fenti példában az NSG nincs társítva a hálózati adapterek (NIC), és hozzá rendelve egy nevű alhálózat **előtér**.

## <a name="manage-rules"></a>Szabályok kezelése
Szabályok hozzáadása egy meglévő NSG, szerkesztheti a meglévő szabályokat, és törölje a szabályokat.

### <a name="add-a-rule"></a>Szabály hozzáadása
Hozzáadása egy szabály, amely lehetővé teszi **bejövő** forgalmának portra **443-as** bármely számítógépről történő a **NSG-előtérbeli** NSG-t, írja be a következő parancsot:

```azurecli
azure network nsg rule create --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --description "Allow access to port 443 for HTTPS" \
    --protocol Tcp \
    --source-address-prefix * \
    --source-port-range * \
    --destination-address-prefix * \
    --destination-port-range 443 \
    --access Allow \
    --priority 102 \
    --direction Inbound
```

Várt kimenet:

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>Szabály módosítása
A szabály a bejövő adatforgalom engedélyezésére a fenti létrehozott módosítása a **Internet** csak, futtassa a következő parancsot:

```azurecli
azure network nsg rule set --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --source-address-prefix Internet
```

Várt kimenet:

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>Szabály törlése
A fentiekben létrehozott szabály törléséhez a következő parancsot:

```azurecli
azure network nsg rule delete --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --quiet
```

> [!NOTE]
> A `--quiet` paraméter biztosítja, nem kell a törlés jóváhagyásához.
>

Várt kimenet:

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>Társítások kezelése
Az NSG alhálózatokra és hálózati adapterek is hozzárendelhető. Is is leválasztja az összes erőforrásból, amelyekhez társítva vannak a NSG.

### <a name="associate-an-nsg-to-a-nic"></a>Társít egy NSG egy hálózati adapter
Rendelje hozzá a a **NSG-előtérbeli** NSG a **TestNICWeb1** hálózati, a következő parancsot:

```azurecli
azure network nic set --resource-group RG-NSG \
    --name TestNICWeb1 \
    --network-security-group-name NSG-FrontEnd
```

Várt kimenet:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>A társítást egy NSG-t a hálózati Adapterhez

Leválasztja a **NSG-előtérbeli** az NSG-t a **TestNICWeb1** hálózati, a következő parancsot:

```azurecli
azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""
```

> [!NOTE]
> Figyelje meg a "" (üres) értéket a `network-security-group-id` paraméter. Ez hogyan távolítsa el az NSG való társítás. Nem azonos az a `network-security-group-name` paraméter.
> 

A várt eredmény:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>Az NSG alhálózatból származó leválasztani
Leválasztja a **NSG-előtérbeli** az NSG-t a **előtér** alhálózat, a következő parancsot:

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-id ""
```

Várt kimenet:

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>Társít egy NSG alhálózathoz
Rendelje hozzá a a **NSG-előtérbeli** NSG a **FronEnd** alhálózati ismét, futtassa a következő parancsot:

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-name NSG-FronEnd
```

> [!NOTE]
> A fenti parancs csak akkor működik, mert a **NSG-előtérbeli** NSG-t, a virtuális hálózat ugyanabban az erőforráscsoportban van **TestVNet**. Ha az NSG-t egy másik erőforráscsoportban, kell használnia a `--network-security-group-id` paraméter, és adja meg a teljes azonosítót az NSG. Az azonosító futtassa le `azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json` , és keres a **azonosító** tulajdonság. 
> 

Várt kimenet:

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>Az NSG törlése
Az NSG csak törölheti, ha nem kapcsolódik semmilyen erőforráshoz. Ha törölni szeretne egy NSG-t, kövesse az alábbi lépéseket.

1. Az erőforrások egy NSG társított ellenőrzéséhez futtassa a `azure network nsg show` látható módon [nézet NSG-ket társítások](#View-NSGs-associations).
2. Ha az NSG egyetlen hálózati adapterrel van társítva, futtassa a `azure network nic set` látható módon [leválasztani a hálózati Adapterhez egy NSG](#Dissociate-an-NSG-from-a-NIC) az egyes hálózati adapterhez. 
3. Ha az NSG egyetlen alhálózatának sem társítva, futtassa a `azure network vnet subnet set` látható módon [leválasztani az NSG alhálózatból származó](#Dissociate-an-NSG-from-a-subnet) az egyes alhálózatokon.
4. Az NSG törléséhez a következő parancsot:

    ```azurecli
    azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet
    ```

    Várt kimenet:

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>Következő lépések
* [Naplózás engedélyezése](virtual-network-nsg-manage-log.md) az NSG-ket.

