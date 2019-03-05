---
title: A meglévő Azure Application Gateway HTTP-fejlécek újraírása
description: Ez a cikk információt nyújt a meglévő Azure Application Gateway HTTP-fejlécek újraírási Azure PowerShell-lel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: f3bda2cf680fa1ad17b5cbb3b9ba5911c78a1e4e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316506"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>A meglévő Application gateway HTTP-fejlécek újraírása

Azure PowerShell-lel való konfigurálásához használható [HTTP-kérelmek és válaszfejlécek újraírási szabályok](rewrite-http-headers.md) meglévő [automatikus skálázást és zónaredundáns application gateway Termékváltozat](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Meglévő application gateway konfigurációjának beolvasása
> * Adja meg a http-fejléc újraírási szabálykonfiguráció
> * Frissítse az application gateway http-fejlécek újraírását fenti konfigurációja

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz az Azure PowerShellt helyileg kell futtatnia. Rendelkeznie kell Az 1.0.0-s verziójának modul, vagy újabb verziója van telepítve. Futtatás `Import-Module Az` , majd`Get-Module Az` a verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Adja meg a http-fejléc újraírási szabálykonfiguráció**

Adja meg a szükséges a http-fejlécek újraírási új objektumok:

- **RequestHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a kérés üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
- **ResponseHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a válasz üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
- **ActionSet**: Ez az objektum tartalmaz a kérelmek és válaszfejlécek, a fentiekben ismertetett konfigurációit.
- **RewriteRule**: Ez az objektum tartalmazza az összes a *actionSets* fent megadott.
- **RewriteRuleSet**– Ez az objektum tartalmazza az összes a *rewriteRules* és a egy kérés útválasztási szabály – alap- vagy -alapú csatlakoztatni kell.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>A meglévő application Gateway konfigurációs beolvasása

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>A meglévő kérelem-útválasztási szabály konfigurációjának beolvasása

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Frissítse az application gateway http-fejlécek újraírását konfigurációja

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Egy átírási szabály törlése

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)
