---
title: "Hozzon létre virtuális hálózatot - Azure CLI 2.0 |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy virtuális hálózatot az Azure CLI 2.0 használatával."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7d7b3543f488aedff1ea2c68a2b497e0ca744af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli-20"></a>Hozzon létre egy virtuális hálózatot az Azure CLI 2.0 használatával

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Azure CLI 1.0](virtual-networks-create-vnet-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez
- [Az Azure CLI 2.0](#create-a-virtual-network) -erőforrás felügyeleti telepítési modell (Ez a cikk) a következő generációs CLI "
 
    Resource Manager-alapú VNetet létrehozhat egyéb eszközökkel is, illetve létrehozhat VNetet a klasszikus üzemi modellben is, ha az alábbi listából egy másik lehetőséget választ:

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

Az Azure CLI 2.0 virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. Telepítse és konfigurálja a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/#login).

2. Hozzon létre egy erőforráscsoportot a virtuális hálózat használatával a [az csoport létrehozása](/cli/azure/group#create) parancsot a `--name` és `--location` argumentumai:

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. Egy VNet és alhálózat létrehozása:

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    Várt kimenet:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    Használt paraméterek:

    - `--name TestVNet`: Létrehozni kívánt VNet neve.
    - `--resource-group TestRG`: # Az erőforráscsoport neve, amely meghatározza az erőforrás. 
    - `--location centralus`: Az a hely be, amelyre telepíteni szeretné.
    - `--address-prefix 192.168.0.0/16`: A címelőtagot és blokkot.  
    - `--subnet-name FrontEnd`: Az az alhálózat neve.
    - `--subnet-prefix 192.168.1.0/24`: A címelőtagot és blokkot.

    A következő parancs használható alapvető információinak felsorolásához kérdezhetők le a virtuális hálózat használatával a [lekérdezésszűrő](/cli/azure/query-az-cli2):

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    A következő kimenetet állít elő:

        Where      Name      Group

        centralus  TestVNet  TestRG

4. Hozzon létre egy alhálózatot:

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    Várt kimenet:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Használt paraméterek:

    - `--address-prefix 192.168.2.0/24`: Alhálózat CIDR-blokkja.
    - `--name BackEnd`: Az új alhálózat neve.
    - `--resource-group TestRG`: Az erőforráscsoportot.
    - `--vnet-name TestVNet`: A tulajdonos VNet neve.

5. Az új VNet tulajdonságainak lekérdezése:

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    Várt kimenet:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. Az alhálózatok tulajdonságainak lekérdezése:

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Várt kimenet:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztathat:

- A virtuális gép (VM) virtuális hálózathoz olvasásával a [hozzon létre egy Linux virtuális Gépet](../virtual-machines/linux/quick-create-cli.md) cikk. Ha nem szeretne egy VNetet és alhálózatot létrehozni a cikkben ismertetett lépések szerint, használhat meglévő VNetet és alhálózatot, amelyekhez kapcsolódhat.
- virtuális hálózatot más virtuális hálózatokhoz – olvassa el a [virtuális hálózatok csatlakoztatását](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) ismertető cikket.
- virtuális hálózatot helyszíni hálózathoz helyek közti virtuális magánhálózat (VPN) vagy ExpressRoute-kapcsolatcsoport használatával. Megtudhatja, hogyan elolvasni a [egy Vnetet csatlakozik egy helyszíni hálózat, a telephelyek közötti VPN használatával](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) és [egy virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).