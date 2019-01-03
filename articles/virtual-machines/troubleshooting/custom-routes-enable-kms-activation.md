---
title: Az Azure egyéni útvonalat használja a KMS-aktiválást, ha a kényszerített bújtatás engedélyezése |} A Microsoft Docs
description: Bemutatja, hogyan használható az Azure egyéni útvonalak KMS-aktiválást, ha a kényszerített bújtatás az Azure-ban engedélyezéséhez.
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
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53798091"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-aktiválás sikertelen, a kényszerített bújtatás forgatókönyvben

Ez a cikk ismerteti a KMS aktiválási problémát, ha engedélyezte az esetleg előforduló kényszerített bújtatás helyek közötti VPN-kapcsolat vagy ExpressRoute-forgatókönyvek a megoldását.

## <a name="symptom"></a>Jelenség

Engedélyezi a [kényszerített bújtatás](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az Azure az internetre irányuló összes forgalmat a virtuális hálózat alhálózataiban biztonsági a helyszíni hálózathoz. Ebben a forgatókönyvben az Azure virtuális gépeket (VM), amely a Windows Server 2012 R2 vagy újabb verzió, sikeresen az aktiválást a Windows. Azonban a Windows korábbi verzióit futtató virtuális gépek a Windows aktiválása sikertelen. 

## <a name="cause"></a>Ok

Az Azure Windows virtuális gépek az Azure KMS-kiszolgáló Windows-aktiválás kell kapcsolódni. Az aktiválás szükséges, hogy az aktiválási kérést az Azure nyilvános IP-címet kell származnia. A kényszerített bújtatás forgatókönyvben az aktiválás sikertelen lesz, mert a helyszíni hálózat helyett az Azure nyilvános IP-van az aktiválási kérelem. 

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az Azure egyéni útvonalat az útvonal aktiválási adatforgalom az Azure KMS-kiszolgáló (23.102.135.246). 

Az IP-cím 23.102.135.246 az Azure globális felhőalapú KMS-kiszolgáló IP-címét. A DNS-név kms.core.windows.net. Ha más Azure platformokon, például az Azure Germany használja, a összehangolására KMS-kiszolgáló IP-címét kell használnia. További információkért lásd az alábbi táblázatot:

|Platform| A KMS-DNS|A KMS-IP|
|------|-------|-------|
|Az Azure globális|KMS.Core.Windows.NET|23.102.135.246|
|Azure Germany|KMS.Core.cloudapi.de|51.4.143.248|
|Amerikai Egyesült Államok kormánya által használt Azure|KMS.Core.usgovcloudapi.NET|23.97.0.13|
|Azure China 21Vianet|KMS.Core.chinacloudapi.CN|42.159.7.249|


Az egyéni útvonal hozzáadásához kövesse az alábbi lépéseket:

### <a name="for-resource-manager-vms"></a>Resource Manager-beli virtuális gépek

1. Nyissa meg az Azure Powershellt, majd [jelentkezzen be az Azure-előfizetés](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Futtassa az alábbi parancsot:

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. Lépjen a virtuális gép, amelyen aktiválási hiba jelentkezik, használja a [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) azt is eléri a KMS-kiszolgáló teszteléséhez:

        psping kms.core.windows.net:1688

4. Próbálja Windows aktiválása és a probléma megoldódott.

### <a name="for-classic-vms"></a>A klasszikus virtuális gépek

1. Nyissa meg az Azure Powershellt, majd [jelentkezzen be az Azure-előfizetés](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Futtassa az alábbi parancsot:

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Lépjen a virtuális gép, amelyen aktiválási hiba jelentkezik, használja a [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) azt is eléri a KMS-kiszolgáló teszteléséhez:

        psping kms.core.windows.net:1688

4. Próbálja Windows aktiválása és a probléma megoldódott.

## <a name="next-steps"></a>További lépések

- [KMS-ügyfél telepítési kulcsai](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Tekintse meg és jelölje be az aktiválási módszerek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)