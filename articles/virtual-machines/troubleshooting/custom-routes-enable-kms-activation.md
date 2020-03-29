---
title: Az Azure egyéni útvonalainak használata a KMS aktiválásának engedélyezéséhez kényszerített bújtatással | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure egyéni útvonalakat a KMS-aktiválás engedélyezéséhez kényszerített bújtatás használata esetén az Azure-ban.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920161"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>A Windows aktiválása sikertelen a kényszerített bújtatásesetén

Ez a cikk azt ismerteti, hogy miként oldható meg a KMS-aktiválási probléma, amely a kényszerített bújtatás engedélyezésekor fordulhat elő a helyek közötti VPN-kapcsolatban vagy az ExpressRoute-forgatókönyvekben.

## <a name="symptom"></a>Hibajelenség

Engedélyezi a [kényszerített bújtatás az](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) Azure virtuális hálózati alhálózatokon, hogy az összes internethez kötött forgalmat visszairányítsa a helyszíni hálózatra. Ebben a forgatókönyvben a Windows t használó Azure virtuális gépek (VM-ek) nem aktiválják a Windowst.

## <a name="cause"></a>Ok

Az Azure Windows virtuális gépek kell csatlakoznia az Azure KMS-kiszolgáló a Windows aktiválása. Az aktiválás megköveteli, hogy az aktiválási kérelem egy Azure nyilvános IP-címről származik. A kényszerített bújtatási forgatókönyvben az aktiválás sikertelen, mert az aktiválási kérelem a helyszíni hálózatról származik, nem pedig egy Azure nyilvános IP-címről.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az Azure egyéni útvonal aktiválási forgalmat az Azure KMS-kiszolgálóra.

Az Azure Global felhőjének KMS-kiszolgálójának IP-címe: 23.102.135.246. DNS-neve kms.core.windows.net. Ha más Azure-platformokat használ, például az Azure Germany-t, a megfelelő KMS-kiszolgáló IP-címét kell használnia. További információt az alábbi táblázatban talál:

|Platform| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Globális|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Amerikai Egyesült Államok kormánya által használt Azure|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Az egyéni útvonal hozzáadásához kövesse az alábbi lépéseket:

### <a name="for-resource-manager-vms"></a>Erőforrás-kezelő i virtuális gépei esetén

 

> [!NOTE] 
> Az aktiválás nyilvános IP-címeket használ, és a szabványos termékváltozat terheléselosztó konfigurációja befolyásolja. A követelmények megismeréséhez tekintse át a kimenő kapcsolatokat az [Azure-ban.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

1. Nyissa meg az Azure PowerShellt, majd [jelentkezzen be Azure-előfizetésébe.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
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
3. Nyissa meg az aktiválási problémákkal kapcsolatos virtuális gépét. Használja [PsPing,](https://docs.microsoft.com/sysinternals/downloads/psping) hogy teszteljék, ha eléri a KMS szerver:

        psping kms.core.windows.net:1688

4. Próbálja meg aktiválni a Windows rendszert, és nézze meg, hogy megoldódott-e a probléma.

### <a name="for-classic-vms"></a>Klasszikus virtuális gépekhez

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Nyissa meg az Azure PowerShellt, majd [jelentkezzen be Azure-előfizetésébe.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
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

3. Nyissa meg az aktiválási problémákkal kapcsolatos virtuális gépét. Használja [PsPing,](https://docs.microsoft.com/sysinternals/downloads/psping) hogy teszteljék, ha eléri a KMS szerver:

        psping kms.core.windows.net:1688

4. Próbálja meg aktiválni a Windows rendszert, és nézze meg, hogy megoldódott-e a probléma.

## <a name="next-steps"></a>További lépések

- [KMS-ügyfél telepítési kulcsai](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Aktiválási módszerek áttekintése és kiválasztása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
