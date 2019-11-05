---
title: Egyéni forgalom választói az Azure VPN Gateway-kapcsolatokhoz | Microsoft Docs
description: Ismerje meg, hogyan használhatja az egyéni forgalmi választókat VPN Gateway kapcsolatokon
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512157"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>VPN Gateway kapcsolatok egyéni forgalmi választói

A VPN Gateway-kapcsolatokon egyéni forgalmi választókat is beállíthat. A forgalmi választók beállítása lehetővé teszi, hogy leszűkítse a címeket a VPN-alagutak mindkét oldaláról, amelyekről forgalmat szeretne küldeni. Az egyéni forgalom választói különösen akkor hasznosak, ha nagyméretű VNet rendelkezik, de az IPsec/IKE egyeztetéshez használni szeretné az alhálózatok egyikét.

Az egyéni forgalmi választókat új kapcsolatok létrehozásakor vagy egy meglévő kapcsolatok esetében lehet frissíteni. A `TrafficSelectorPolicies` paraméter a forgalmi választók tömbjét tartalmazza. Mindegyik forgalmi választó a helyi és a távoli címtartományok gyűjteményét CIDR formátumban tárolja.

## <a name="add-custom-traffic-selectors"></a>Egyéni forgalmi választó hozzáadása

Az alábbi példák azt mutatják be, hogyan hozhatók létre egyéni forgalmi választók a virtuális hálózati átjáróhoz a PowerShell-parancsok használatával. A virtuális hálózati átjárók kapcsolatának létrehozásával kapcsolatos segítségért lásd: [helyek közötti kapcsolat konfigurálása](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Adja meg a *trafficselectorpolicies* paraméter értékeit a $trafficselectorpolicyban. Ha ezt állítja be, figyelje meg, hogy a *New-AzTrafficSelectorPolicy* érték egy tömbben helyi és távoli címtartományt vesz igénybe.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Hozzon létre új virtuális hálózati átjáró-kapcsolatokat. Módosítsa az érték paramétereit az igényeinek megfelelően.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

A "set-AzVirtualNetworkGatewayConnection" használatával egy meglévő virtuális hálózati átjáró-kapcsolatok esetében is frissítheti az egyéni forgalmi választókat. A PowerShell-értékek esetében lásd: [virtuális hálózati átjáróval létesített kapcsolatok](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>További lépések

További információ a VPN-átjárókkal kapcsolatban: [about VPN Gateway](vpn-gateway-about-vpngateways.md).