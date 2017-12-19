---
title: "Hozzon létre virtuális hálózatot - Azure PowerShell |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre virtuális hálózatot PowerShell használatával."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7072ddf51570d46578111e2e392e3cbea53f2aa
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-virtual-network-using-powershell"></a>Hozzon létre egy virtuális hálózatot PowerShell használatával

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.
 
Ez a cikk azt ismerteti, hogyan hozhat létre egy Vnetet keresztül a Resource Manager üzembe helyezési modellel PowerShell használatával. Resource Manager-alapú VNetet létrehozhat egyéb eszközökkel is, illetve létrehozhat VNetet a klasszikus üzemi modellben is, ha az alábbi listából egy másik lehetőséget választ:

> [!div class="op_single_selector"]
> * [Portál](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [Parancssori felület](virtual-networks-create-vnet-arm-cli.md)
> * [Sablon](virtual-networks-create-vnet-arm-template-click.md)
> * [Portál (klasszikus)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (klasszikus)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [Parancssori felület (klasszikus)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

PowerShell-lel virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. Telepítse és konfigurálja az Azure PowerShell, a lépések a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk.

2. Szükség esetén hozzon létre egy új erőforráscsoportot a lent látható módon. Ebben a forgatókönyvben nevű erőforráscsoport létrehozása *TestRG*. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Várt kimenet:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. Hozzon létre egy új virtuális hálózat nevű *TestVNet*:

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    Várt kimenet:

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. A virtuális hálózat objektumot tárolható egy változóban:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > 3. és 4 lépést kombinálhatja futtatásával `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus`.
   > 

5. Adjon hozzá egy alhálózatot az új VNet változóhoz:

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    Várt kimenet:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. A fenti 5. lépést ismételje meg minden létrehozni kívánt alhálózat esetében. A következő parancs létrehozza a *háttér* alhálózati az esethez:

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. Ugyan létrehoz alhálózatokat, azok jelenleg csak a VNet lekéréséhez a 4. lépésben használt helyi változóban léteznek. Menti a módosításokat az Azure-ba, a következő parancsot:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    Várt kimenet:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztathat:

- A virtuális gép (VM) virtuális hálózathoz olvasásával a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md) cikk. Ha nem szeretne egy VNetet és alhálózatot létrehozni a cikkben ismertetett lépések szerint, használhat meglévő VNetet és alhálózatot, amelyekhez kapcsolódhat.
- virtuális hálózatot más virtuális hálózatokhoz – olvassa el a [virtuális hálózatok csatlakoztatását](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) ismertető cikket.
- virtuális hálózatot helyszíni hálózathoz helyek közti virtuális magánhálózat (VPN) vagy ExpressRoute-kapcsolatcsoport használatával. Az elsajátításához olvassa el a [Virtuális hálózat csatlakoztatása helyszíni hálózathoz helyek közti VPN használatával](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) és a [Virtuális hálózat csatlakoztatása ExpressRoute-kapcsolatcsoporthoz](../expressroute/expressroute-howto-linkvnet-arm.md) eljárásokat ismertető cikkeket.
