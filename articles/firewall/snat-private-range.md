---
title: SNAT magánhálózati IP-címtartományok Azure Firewall
description: Az IP-címek magánhálózati tartományait úgy is konfigurálhatja, hogy a tűzfal ne SNAT az ezen IP-címekre irányuló forgalmat.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444458"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>SNAT magánhálózati IP-címtartományok Azure Firewall

A Azure Firewall nem SNAT, ha a célként megadott IP-cím az [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány része. 

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall a SNAT a AzureFirewallSubnet-ben lévő egyik tűzfal magánhálózati IP-címére irányítja át a forgalmat. Azonban úgy is konfigurálhatja a Azure Firewallt, hogy **ne** SNAT a nyilvános IP-címtartományt.

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT magánhálózati IP-címtartományok konfigurálása

A Azure PowerShell használatával olyan IP-címtartományt adhat meg, amelyet a tűzfal nem SNAT.

### <a name="new-firewall"></a>Új tűzfal

Új tűzfal esetén a Azure PowerShell parancs a következő:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> A IANAPrivateRanges a Azure Firewall aktuális alapértelmezett értékeire van kiterjesztve, míg a többi tartomány hozzá van adva.

További információ: [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal konfigurálásához használja a következő Azure PowerShell parancsokat:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sablonok

A következőt adhatja hozzá a `additionalProperties` szakaszhoz:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).