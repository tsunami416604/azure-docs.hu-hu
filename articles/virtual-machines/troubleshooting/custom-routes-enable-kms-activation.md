---
title: Az Azure egyéni útvonalat használja a KMS-aktiválást, ha a kényszerített bújtatás engedélyezése |} A Microsoft Docs
description: KMS-aktiválás engedélyezéséhez, ha a kényszerített bújtatás az Azure-ban használata az Azure egyéni útvonalak használatát szemlélteti.
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
ms.openlocfilehash: b121996530ea0618fc757f1ae12dfafde10ed7bb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979377"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-aktiválás sikertelen, a kényszerített bújtatás forgatókönyvben

Ez a cikk ismerteti a KMS aktiválási problémát, hogy Ön is szembesülhet, ha engedélyezi a kényszerített bújtatás helyek közötti VPN-kapcsolat vagy ExpressRoute-forgatókönyvek a megoldását.

## <a name="symptom"></a>Jelenség

Engedélyezi a [kényszerített bújtatás](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az Azure-ban az internetre irányuló összes forgalmat a virtuális hálózat alhálózataiban biztonsági a helyszíni hálózathoz. Ebben a forgatókönyvben az Azure virtuális gépeket (VM), amely a Windows Server 2012 R2 (vagy a Windows újabb verziói), sikeresen az aktiválást Windows. Azonban a Windows korábbi verzióit futtató virtuális gépek Windows aktiválása sikertelen.

## <a name="cause"></a>Ok

Az Azure Windows virtuális gépek az Azure KMS-kiszolgálóhoz Windows-aktiválás csatlakoznia kell. Az aktiválás szükséges, hogy az aktiválási kérelem származnak-e az Azure nyilvános IP-cím. A kényszerített bújtatás forgatókönyvben az aktiválás sikertelen, mert az aktiválási kérelem származik, a helyszíni hálózat az Azure nyilvános IP-cím helyett.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az Azure egyéni útvonalat az útvonal aktiválási adatforgalom Azure KMS-kiszolgálón.

Az Azure globális felhőalapú a KMS-kiszolgáló IP-cím 23.102.135.246 áll. Its DNS name is kms.core.windows.net. Ha például az Azure Germany más az Azure platformon használja, a megfelelő KMS-kiszolgáló IP-címét kell használnia. További információkért lásd az alábbi táblázatot:

|Platform| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Amerikai Egyesült Államok kormánya által használt Azure|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Az egyéni útvonal hozzáadásához kövesse az alábbi lépéseket:

### <a name="for-resource-manager-vms"></a>Resource Manager-beli virtuális gépek

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. Nyissa meg az Azure Powershellt, majd [jelentkezzen be az Azure-előfizetés](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Futtassa az alábbi parancsot:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable
    ```
3. Nyissa meg a virtuális gép, amely rendelkezik az aktiválással kapcsolatos problémák. Használat [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) azt is eléri a KMS-kiszolgáló teszteléséhez:

        psping kms.core.windows.net:1688

4. Próbálja meg a Windows aktiválása és a probléma megoldódott.

### <a name="for-classic-vms"></a>A klasszikus virtuális gépek

1. Nyissa meg az Azure Powershellt, majd [jelentkezzen be az Azure-előfizetés](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Nyissa meg a virtuális gép, amely rendelkezik az aktiválással kapcsolatos problémák. Használat [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) azt is eléri a KMS-kiszolgáló teszteléséhez:

        psping kms.core.windows.net:1688

4. Próbálja meg a Windows aktiválása és a probléma megoldódott.

## <a name="next-steps"></a>További lépések

- [KMS-ügyfél telepítési kulcsai](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Tekintse meg és jelölje be az aktiválási módszerek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
