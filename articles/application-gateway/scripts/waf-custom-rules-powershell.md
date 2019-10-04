---
title: Azure PowerShell-Példaszkript – egyéni WAF-szabályok létrehozása
description: Azure PowerShell-Példaszkript – webalkalmazás-tűzfal egyéni szabályok létrehozása
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743422"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Egyéni WAF-szabályok létrehozása az Azure PowerShell használatával

Ez a szkript létrehoz egy Application Gateway webalkalmazásokhoz használható tűzfal által használt egyéni szabályokat. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce tartalmazza a felhasználói ügynök *evilbot*.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha a helyi telepítése és használata az Azure PowerShell választja, a szkriptnek szüksége van az Azure PowerShell modul 2.1.0-ás vagy újabb.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
2. Kapcsolat létrehozása az Azure-ral, futtassa `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal eltávolítható az erőforráscsoport, az alkalmazásátjáró és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehozza az alhálózatkonfigurációt. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehozza a virtuális hálózatot az alhálózatkonfigurációk használatával. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Az alhálózatot az alkalmazásátjáróval társító konfigurációt hozza létre. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | A nyilvános IP-címet az alkalmazásátjáróhoz hozzárendelő konfigurációt hozza létre. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hozzárendeli az alkalmazásátjáró elérésére használt portot. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Háttérkészletet hoz létre az alkalmazásátjáróhoz. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurálja a háttérkészlet beállításait. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Figyelőt hoz létre. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Útválasztási szabályt hoz létre. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Az alkalmazásátjáró szintjét és kapacitását határozza meg. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Egy alkalmazásátjárót hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Automatikus skálázási konfigurációjának hoz létre az Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Egy tűzfal feltétel egyezik változót hoz létre.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Egyeztetési feltételt a egyéni szabályt hoz létre.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Az application gateway tűzfalházirendet egy új egyéni szabályt hoz létre.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Egy application gateway tűzfalszabályzatot hoz létre.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Létrehoz egy application gateway WAF konfigurációját.|

## <a name="next-steps"></a>További lépések

- WAF egyéni szabályokkal kapcsolatos további információkért lásd: [egyéni szabályok a webalkalmazási tűzfal](../custom-waf-rules-overview.md)
- Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
- Az alkalmazásátjárókkal használható további PowerShell-példaszkripteket [Az Application Gateway dokumentációjában](../powershell-samples.md) találhat.
