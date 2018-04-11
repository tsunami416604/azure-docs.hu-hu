---
title: "Hálózati biztonsági csoport – Azure PowerShell kezelése |} Microsoft Docs"
description: "Ismerje meg a PowerShell használatával a hálózati biztonsági csoportok kezelése."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-groups-using-powershell"></a>PowerShell-lel hálózati biztonsági csoportok kezelése

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Információk beolvasása
Megtekintheti a meglévő NSG-ket, szabályok lekérdezni egy meglévő NSG-t, és megtudhatja, milyen erőforrásokat egy NSG társítva.

### <a name="view-existing-nsgs"></a>Meglévő NSG-k megtekintése
Előfizetés az összes meglévő NSG-ket megtekintéséhez futtassa a `Get-AzureRmNetworkSecurityGroup` parancsmag.

A várt eredmény:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Egy adott erőforráscsoportban NSG-k listájának megtekintéséhez futtassa a `Get-AzureRmNetworkSecurityGroup` parancsmag.

Várt kimenet:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>A szabályok egy NSG listázása
Az NSG nevű szabályainak megtekintéséhez **NSG-előtérbeli**, adja meg a következő parancsot:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Várt kimenet:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> Is `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` az alapértelmezett szabályok a listát a **NSG-előtérbeli** NSG.
> 

### <a name="view-nsgs-associations"></a>Az NSG-társításának megtekintéséhez
Milyen erőforrások megtekintése a **NSG-előtérbeli** NSG társítható, futtassa a következő parancsot:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Keresse meg a **hálózati illesztők** és **alhálózatok** tulajdonságok alább látható módon:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

Az előző példában a NSG nincs társítva a hálózati adapterek (NIC); hozzá rendelve egy nevű alhálózat **előtér**.

## <a name="manage-rules"></a>Szabályok kezelése
Szabályok hozzáadása egy meglévő NSG, szerkesztheti a meglévő szabályokat, és törölje a szabályokat.

### <a name="add-a-rule"></a>Szabály hozzáadása
Hozzáadása egy szabály, amely lehetővé teszi **bejövő** forgalmának portra **443-as** bármely számítógépről történő a **NSG-előtér** NSG, kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot lekérdezéséhez a meglévő NSG-t, és tárolható egy változóban:

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Futtassa a következő parancs futtatásával adja hozzá a szabály az NSG:

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Az NSG módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    Csak a biztonsági szabályok megjelenítő várható kimenete:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Szabály módosítása
A szabály a bejövő adatforgalom engedélyezésére a fenti létrehozott módosítása a **Internet** csak, kövesse az alábbi lépéseket.

1. Futtassa a következő parancsot lekérdezéséhez a meglévő NSG-t, és tárolható egy változóban:

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Futtassa a következő parancsot az új szabály beállításait:

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Az NSG módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Csak a biztonsági szabályok megjelenítő várható kimenete:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Szabály törlése
1. Futtassa a következő parancsot lekérdezéséhez a meglévő NSG-t, és tárolható egy változóban:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. A következő parancsot a szabály eltávolítása az NSG:

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. A módosításainak mentése az NSG-t, a következő parancs futtatásával:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Várt kimenet megjelenítése csak a biztonsági szabályokat, figyelje meg a **https-szabály** már nem szerepel a listában:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Társítások kezelése
Az NSG alhálózatokra és hálózati adapterek is hozzárendelhető. Is is leválasztja az összes erőforrásból, amelyekhez társítva vannak a NSG.

### <a name="associate-an-nsg-to-a-nic"></a>Társít egy NSG egy hálózati adapter
Rendelje hozzá a a **NSG-előtérbeli** NSG a **TestNICWeb1** hálózati adapter, kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot lekérdezéséhez a meglévő NSG-t, és tárolható egy változóban:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Futtassa a következő parancsot lekérdezéséhez a meglévő hálózati adapter, és tárolható egy változóban:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Állítsa be a **hálózati biztonsági csoporthoz tartozik** tulajdonsága a **NIC** változó értékének a **NSG** változó, a következő parancs beírásával:

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. A hálózati adapter módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Várt kimenet megjelenítése csak a **hálózati biztonsági csoporthoz tartozik** tulajdonság:
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>A társítást egy NSG-t a hálózati Adapterhez
Leválasztja a **NSG-előtér** az NSG-t a **TestNICWeb1** hálózati adapter, kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot lekérdezéséhez a meglévő hálózati adapter, és tárolható egy változóban:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Állítsa be a **hálózati biztonsági csoporthoz tartozik** tulajdonsága a **NIC** változó **$null** a következő parancs futtatásával:

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. A hálózati adapter módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Várt kimenet megjelenítése csak a **hálózati biztonsági csoporthoz tartozik** tulajdonság:
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Az NSG alhálózatból származó leválasztani
Leválasztja a **NSG-előtér** az NSG-t a **előtér** alhálózati, kövesse az alábbi lépéseket:

1. A következő parancsot a meglévő VNet lekéréséhez és tárolható egy változóban:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. A következő parancsot beolvasása a **előtér** alhálózati és tárolható egy változóban:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Állítsa be a **hálózati biztonsági csoporthoz tartozik** tulajdonsága a **alhálózati** változó **$null** a következő parancs beírásával:

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Az alhálózat módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Csak a tulajdonságait megjelenítő kimeneti várt a **előtér** alhálózat. A tulajdonság nem létezik figyelmeztetés **hálózati biztonsági csoporthoz tartozik**:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Társít egy NSG alhálózathoz
Rendelje hozzá a a **NSG-előtérbeli** NSG a **FronEnd** alhálózat ebben az esetben kövesse az alábbi lépéseket:

1. A következő parancsot a meglévő VNet lekéréséhez és tárolható egy változóban:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. A következő parancsot beolvasása a **előtér** alhálózati és tárolható egy változóban:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Futtassa a következő parancsot lekérdezéséhez a meglévő NSG-t, és tárolható egy változóban:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Állítsa be a **hálózati biztonsági csoporthoz tartozik** tulajdonsága a **alhálózati** változó **$null** a következő parancs futtatásával:

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Az alhálózat módosításainak mentéséhez futtassa a következő parancsot:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Várt kimenet megjelenítése csak a **hálózati biztonsági csoporthoz tartozik** tulajdonsága a **előtér** alhálózati:
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Az NSG törlése
Az NSG csak törölheti, ha nem kapcsolódik semmilyen erőforráshoz. Ha törölni szeretne egy NSG-t, kövesse az alábbi lépéseket.

1. Az erőforrások egy NSG társított ellenőrzéséhez futtassa a `azure network nsg show` látható módon [nézet NSG-ket társítások](#View-NSGs-associations).
2. Ha az NSG egyetlen hálózati adapterrel van társítva, futtassa a `azure network nic set` látható módon [leválasztani a hálózati Adapterhez egy NSG](#Dissociate-an-NSG-from-a-NIC) az egyes hálózati adapterhez. 
3. Ha az NSG egyetlen alhálózatának sem társítva, futtassa a `azure network vnet subnet set` látható módon [leválasztani az NSG alhálózatból származó](#Dissociate-an-NSG-from-a-subnet) az egyes alhálózatokon.
4. Az NSG törléséhez a következő parancsot:

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > A `-Force` paraméter biztosítja, nem kell a törlés jóváhagyásához.
   > 

## <a name="next-steps"></a>Következő lépések
* [Naplózás engedélyezése](virtual-network-nsg-manage-log.md) az NSG-ket.

