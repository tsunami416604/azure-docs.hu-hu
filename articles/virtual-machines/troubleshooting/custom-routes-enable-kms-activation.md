---
title: A KMS-Aktiválás kényszerített bújtatással való engedélyezése az egyéni Azure-útvonalak használatával | Microsoft Docs
description: Bemutatja, hogyan használhatók az Azure egyéni útvonalak a KMS-Aktiválás engedélyezéséhez a kényszerített bújtatás használatakor az Azure-ban.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 2877fae66584ec24fb6e62b20d66ded36157b824
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990355"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>A Windows aktiválása sikertelen a kényszerített bújtatási forgatókönyvben

Ez a cikk azt ismerteti, hogyan oldható meg a KMS-aktiválási probléma, amely akkor fordulhat elő, ha engedélyezi a kényszerített bújtatást a helyek közötti VPN-kapcsolaton vagy ExpressRoute forgatókönyvekben.

## <a name="symptom"></a>Jelenség

A [kényszerített bújtatást](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az Azure-beli virtuális hálózati alhálózatokon engedélyezheti, hogy az összes internetes forgalmat visszairányítsa a helyszíni hálózatra. Ebben a forgatókönyvben a Windows Server 2012 R2-t (vagy a Windows újabb verzióit) futtató Azure-beli virtuális gépek (VM-EK) sikeresen aktiválni tudják a Windowst. A Windows korábbi verzióját futtató virtuális gépek azonban nem tudják aktiválni a Windowst.

## <a name="cause"></a>Ok

Az Azure Windows rendszerű virtuális gépeknek csatlakozniuk kell az Azure KMS-kiszolgálóhoz a Windows aktiválásához. Az aktiváláshoz az aktiválási kérelemnek egy Azure nyilvános IP-címről kell származnia. A kényszerített bújtatási forgatókönyvben az aktiválás sikertelen, mert az aktiválási kérelem a helyszíni hálózatról származik, és nem egy Azure nyilvános IP-címről.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az Azure egyéni útvonalát az aktiválási forgalom Azure KMS-kiszolgálóra történő átirányításához.

Az Azure globális felhőhöz tartozó KMS-kiszolgáló IP-címe 23.102.135.246. Its DNS name is kms.core.windows.net. Ha más Azure-platformokat, például az Azure Germany-t használja, akkor a megfelelő KMS-kiszolgáló IP-címét kell használnia. További információkért tekintse meg a következő táblázatot:

|Platform| KMS DNS|KMS IP|
|------|-------|-------|
|Azure globális|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Amerikai Egyesült Államok kormánya által használt Azure|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Az egyéni útvonal hozzáadásához kövesse az alábbi lépéseket:

### <a name="for-resource-manager-vms"></a>Resource Manager-alapú virtuális gépek esetén

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

> [!NOTE] 
> Az aktiválás nyilvános IP-címeket használ, és egy szabványos SKU Load Balancer konfiguráció fogja érinteni. A követelmények megismeréséhez körültekintően tekintse át a [kimenő kapcsolatokat az Azure-ban](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) .

1. Nyissa meg Azure PowerShell, majd jelentkezzen be az [Azure-](https://docs.microsoft.com/powershell/azure/authenticate-azureps)előfizetésbe.
2. Futtassa az alábbi parancsot:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Lépjen az aktiválási problémákkal rendelkező virtuális gépre. A [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) használatával ellenőrizze, hogy a KMS-kiszolgáló elérheti-e a következőt:

        psping kms.core.windows.net:1688

4. Próbálja meg aktiválni a Windowst, és ellenőrizze, hogy megoldódott-e a probléma.

### <a name="for-classic-vms"></a>Klasszikus virtuális gépek esetén

1. Nyissa meg Azure PowerShell, majd jelentkezzen be az [Azure-](https://docs.microsoft.com/powershell/azure/authenticate-azureps)előfizetésbe.
2. Futtassa az alábbi parancsot:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Lépjen az aktiválási problémákkal rendelkező virtuális gépre. A [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) használatával ellenőrizze, hogy a KMS-kiszolgáló elérheti-e a következőt:

        psping kms.core.windows.net:1688

4. Próbálja meg aktiválni a Windowst, és ellenőrizze, hogy megoldódott-e a probléma.

## <a name="next-steps"></a>További lépések

- [KMS-ügyfél telepítési kulcsai](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Aktiválási módszerek áttekintése és kiválasztása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
