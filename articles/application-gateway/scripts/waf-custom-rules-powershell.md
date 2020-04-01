---
title: Azure PowerShell-parancsfájlminta – WAF-egyéni szabályok létrehozása
description: Azure PowerShell-parancsfájlminta – Webalkalmazás-tűzfal egyéni szabályainak létrehozása
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "66743422"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>WaF-egyéni szabályok létrehozása az Azure PowerShell használatával

Ez a parancsfájl egyéni szabályokat használó Application Gateway webalkalmazás-tűzfalat hoz létre. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce user-agent *evilbot-ot*tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha úgy dönt, hogy helyileg telepíti és használja az Azure PowerShellt, ehhez a parancsfájlhoz az Azure PowerShell-modul 2.1.0-s vagy újabb verziója szükséges.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
2. Ha kapcsolatot szeretne létrehozni `Connect-AzAccount`az Azure-ral, futtassa a futtassa a futtassa a futtassa a futtassa a

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
| [Új-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehozza az alhálózatkonfigurációt. |
| [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehozza a virtuális hálózatot az alhálózatkonfigurációk használatával. |
| [Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [Új-AzApplicationGatewayIP-konfiguráció](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Az alhálózatot az alkalmazásátjáróval társító konfigurációt hozza létre. |
| [Új-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | A nyilvános IP-címet az alkalmazásátjáróhoz hozzárendelő konfigurációt hozza létre. |
| [Új-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hozzárendeli az alkalmazásátjáró elérésére használt portot. |
| [Új-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Háttérkészletet hoz létre az alkalmazásátjáróhoz. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurálja a háttérkészlet beállításait. |
| [Új-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Figyelőt hoz létre. |
| [Új-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Útválasztási szabályt hoz létre. |
| [Új-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Az alkalmazásátjáró szintjét és kapacitását határozza meg. |
| [Új-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Egy alkalmazásátjárót hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Automatikus skálázási konfigurációt hoz létre az Application Gateway számára.|
|[New-AzApplicationGatewayFirewallFirewallMatchváltozó](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Létrehoz egy egyezési változót a tűzfal feltételhez.|
|[Új-AzApplicationGatewayFirewallcondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Létrehoz egyegyezési feltételt az egyéni szabályhoz.|
|[New-AzApplicationGatewayFirewallCustomrule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Új egyéni szabályt hoz létre az alkalmazásátjáró tűzfalházirendéhez.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Alkalmazásátjáró tűzfalházirendet hoz létre.|
|[New-AzApplicationGatewayWebApplicationFirewallkonfiguráció](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|WAF-konfigurációt hoz létre egy alkalmazásátjáróhoz.|

## <a name="next-steps"></a>További lépések

- A WAF egyéni szabályairól a [WebApplication Tűzfal egyéni szabályai](../custom-waf-rules-overview.md) című témakörben talál további információt.
- Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
- Az alkalmazásátjárókkal használható további PowerShell-példaszkripteket [Az Application Gateway dokumentációjában](../powershell-samples.md) találhat.
