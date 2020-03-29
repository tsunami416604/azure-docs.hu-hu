---
title: HTTP-fejlécek újraírása az Azure Application Gateway alkalmazásban
description: Ez a cikk aHTTP-fejlécek Azure PowerShell használatával történő újraírásáról nyújt tájékoztatást az Azure Application Gateway szolgáltatásban
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947191"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Http-kérelem- és válaszfejlécek újraírása az Azure Application Gateway alkalmazással – Azure PowerShell

Ez a cikk ismerteti, hogyan azure PowerShell használatával konfigurálja az [Application Gateway v2 Termékváltozat-példány](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) a HTTP-fejlécek a kérelmek és válaszok.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

- Az Azure PowerShell helyi futtatásához a cikkben leírt lépések végrehajtásához. Az Az modul 1.0.0-s vagy újabb verziójának telepítése is szükséges. Futtassa, `Import-Module Az` majd `Get-Module Az` határozza meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.
- Rendelkeznie kell egy Application Gateway v2 Termékváltozat-példány. A fejlécek újraírása nem támogatott a v1 termékváltozatban. Ha nem rendelkezik a v2 termékváltozat, hozzon létre egy [Application Gateway v2 termékváltozat,](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) mielőtt elkezdené.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

A HTTP-fejléc újraírásának konfigurálásához végre kell hajtsa végre ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **RequestHeaderConfiguration**: Az újraírni kívánt kérelemfejléc-mezők és a fejlécek új értékének megadására szolgál.

   - **ResponseHeaderConfiguration**: Az újraírni kívánt válaszfejléc-mezők és a fejlécek új értékének megadására szolgál.

   - **ActionSet**: A korábban megadott kérés- és válaszfejlécek konfigurációit tartalmazza.

   - **Feltétel**: Választható konfiguráció. Az újraírási feltételek kiértékelik a HTTP(S) kérések és válaszok tartalmát. Az újraírási művelet akkor történik meg, ha a HTTP(S) kérés vagy válasz megfelel az újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor történik meg, ha az összes feltétel teljesül. Más szóval a művelet logikai ÉS művelet.

   - **RewriteRule**: Több újraírási műveletet / újraírásfeltétel-kombinációt tartalmaz.

   - **RuleSequence**: Egy választható konfiguráció, amely segít meghatározni a szabályok újraírási sorrendjét. Ez a konfiguráció akkor hasznos, ha egy újraírási készletben több újraírási szabály is szerepel. Először egy alacsonyabb szabálysorozat-értékkel rendelkező újraírási szabály fut. Ha ugyanazt a szabálysorozat-értéket két újraírási szabályhoz rendeli, a végrehajtási sorrend nem determinisztikus.

     Ha nem adja meg explicit módon a RuleSequence értéket, a rendszer 100-as alapértelmezett értéket állít be.

   - **RewriteRuleSet**: Több újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatlakoztassa az ÚjrawriteRuleSet-et egy útválasztási szabályhoz. Az újraírási konfiguráció az útválasztási szabályon keresztül csatlakozik a forrásfigyelőhöz. Ha alapútválasztási szabályt használ, a fejléc újraírási konfigurációja egy forrásfigyelőhöz van társítva, és globális fejléc-újraírás. Útalapú útválasztási szabály használata esetén a fejléc újraírási konfigurációja az URL-elérési út térképén lesz definiálva. Ebben az esetben csak a webhely adott elérési útterületén érvényes.

Több HTTP-fejléc újraírási készletet hozhat létre, és minden újraírási készletet több figyelőre alkalmazhat. De csak egy újraírási készletet alkalmazhat egy adott figyelőhöz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>A HTTP-fejléc újraírási szabálykonfigurációjának megadása

Ebben a példában módosítjuk az átirányítási URL-címet úgy, hogy átírjuk a helyfejlécet a HTTP-válaszban, ha a helyfejléc azurewebsites.net hivatkozik. Ehhez adjunk hozzá egy feltételt, amely kiértékeli, hogy a válaszban szereplő helyfejléc tartalmaz-e azurewebsites.net. A mintát `(https?):\/\/.*azurewebsites\.net(.*)$`fogjuk használni. És fejlécként `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` használjuk. Ez az érték lecseréli *azurewebsites.net* a helyfejlécben *lévő contoso.com.*

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Az alkalmazásátjáró konfigurációjának beolvasása

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>A kérelem-útválasztási szabály konfigurációjának beolvasása

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Az alkalmazásátjáró frissítése a HTTP-fejlécek újraírására szolgáló konfigurációval

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Újraírási szabály törlése

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni agyakori használati esetek beállításáról, olvassa el a [gyakori fejléc-újraírási forgatókönyvek .](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)