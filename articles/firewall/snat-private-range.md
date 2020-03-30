---
title: Az Azure Firewall SNAT privát IP-címtartományai
description: Az IP-cím magántartományait beállíthatja úgy, hogy a tűzfal ne forgalmazzon SNAT-forgalmat ezekre az IP-címekre.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064808"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Az Azure Firewall SNAT privát IP-címtartományai

Az Azure Firewall nem snat hálózati szabályokkal, ha a cél IP-cím egy privát IP-címtartományban [iANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Az alkalmazásszabályok alkalmazása mindig [transzparens proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) használatával lesz alkalmazva, függetlenül a cél IP-címétől.

Ha a szervezet nyilvános IP-címtartományt használ a magánhálózatokhoz, az Azure Firewall SNAT-ok forgalmat bonyolítanak le az AzureFirewallSubnet tűzfal egyik privát IP-címére. Az Azure Firewall azonban konfigurálható úgy, hogy **ne** snat-t a nyilvános IP-címtartományba.

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT privát IP-címtartományok konfigurálása

Az Azure PowerShell segítségével megadhatja az IP-címtartományt, amelyet a tűzfal nem snat.You can use Azure PowerShell to specify a IP address range that the firewall won't SNAT.

### <a name="new-firewall"></a>Új tűzfal

Új tűzfal esetén az Azure PowerShell parancs a következő:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges ki van bontva az Azure Tűzfal jelenlegi alapértelmezett, míg a többi tartományok hozzá.

További információ: [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal konfigurálásához használja a következő Azure PowerShell-parancsokat:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sablonok

A következőt veheti `additionalProperties` fel a szakaszba:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [telepítheti és konfigurálhatja az Azure-tűzfalat.](tutorial-firewall-deploy-portal.md)