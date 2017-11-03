---
title: "Az Azure CLI 1.0 virtuális hálózat létrehozása |} Microsoft Docs"
description: "Útmutató az Azure CLI 1.0 virtuális hálózat létrehozása |} Erőforrás-kezelő."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Hozzon létre egy virtuális hálózatot az Azure parancssori felület használatával

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.
- [Az Azure CLI 1.0](#create-a-virtual-network) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az Azure parancssori felület használatával virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. Telepítse és konfigurálja az Azure parancssori felület a lépések a [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md) cikk.

2. Egy VNet és alhálózat létrehozása:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Várt kimenet:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Használt paraméterek:

   * **--vnet**. A létrehozni kívánt VNet neve. A mi esetünkben *TestVNet*
   * **-e (vagy--címtartomány)**. Virtuális hálózat címterület. A mi esetünkben *192.168.0.0*
   * **-i (vagy - cidr)**. Hálózati maszk CIDR formátumban. A mi esetünkben *16*.
   * **-n (vagy--alhálózatneve**). Az első alhálózat neve. A mi esetünkben *FrontEnd*.
   * **-p (vagy--ip-alhálózat-start)**. Kezdő IP-cím alhálózati vagy alhálózati címtartományt. A mi esetünkben *192.168.1.0*.
   * **-r (vagy--alhálózat-cidr)**. Hálózati maszk alhálózat CIDR formátumban. A mi esetünkben *24*.
   * **-l (vagy --location)**. Azure-régió, ahol a VNet létrejön. A mi esetünkben *USA középső RÉGIÓJA*.

3. Hozzon létre egy alhálózatot:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Várt kimenet:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Használt paraméterek:

   * **-t (vagy--vnet-name**. A VNet neve, ahol létrejön az alhálózat. A mi esetünkben *TestVNet*.
   * **-n (vagy --name)**. Az új alhálózat neve. A mi esetünkben *háttér*.
   * **-a (vagy --address-prefix)**. Az alhálózat CIDR-blokkja. Négy esetünkben *192.168.2.0/24*.
   
4. Az új vnet tulajdonságainak megtekintéséhez:

    ```azurecli
    azure network vnet show
    ```
   
    Várt kimenet:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztathat:

- A virtuális gép (VM) virtuális hálózathoz olvasásával a [hozzon létre egy Linux virtuális Gépet](../virtual-machines/linux/quick-create-cli.md) cikk. Ha nem szeretne egy VNetet és alhálózatot létrehozni a cikkben ismertetett lépések szerint, használhat meglévő VNetet és alhálózatot, amelyekhez kapcsolódhat.
- virtuális hálózatot más virtuális hálózatokhoz – olvassa el a [virtuális hálózatok csatlakoztatását](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) ismertető cikket.
- virtuális hálózatot helyszíni hálózathoz helyek közti virtuális magánhálózat (VPN) vagy ExpressRoute-kapcsolatcsoport használatával. Megtudhatja, hogyan elolvasni a [egy Vnetet csatlakozik egy helyszíni hálózat, a telephelyek közötti VPN használatával](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) és [egy virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).