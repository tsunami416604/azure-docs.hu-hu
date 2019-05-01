---
title: Az Azure Application Gateway HTTP-fejlécek újraírása
description: Ez a cikk információt nyújt az Azure PowerShell-lel az Azure Application Gateway HTTP-fejlécek újraírási
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947191"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Újraírási HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure PowerShell-lel

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure PowerShell használatával egy [Application Gateway v2 szintű Termékváltozatot](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) példány a HTTP-fejléceket, a kérelmek és válaszok újraírási.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előzetes teendők

- Azure PowerShell, a jelen cikkben ismertetett lépések végrehajtásához helyileg futtatni szeretné. Emellett Az 1.0.0-s verziójának modul rendelkeznie kell, vagy újabb verziója szükséges. Futtatás `Import-Module Az` , majd `Get-Module Az` , amelyen telepítve van a verzió meghatározásához. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.
- Szüksége lesz egy Application Gateway v2 SKU példány. A fejlécek újraírását a v1-termékváltozat nem támogatott. Ha nem rendelkezik a v2 szintű Termékváltozatot, hozzon létre egy [Application Gateway v2 szintű Termékváltozatot](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) példány megkezdése előtt.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

HTTP-fejléc újraírási konfigurálásához hajtsa végre az alábbi lépéseket kell.

1. A HTTP-fejléc újraírási szükséges objektumok létrehozása:

   - **RequestHeaderConfiguration**: A kérés üzenetfejlécének mezői újraírási kívánt és az új értéket a fejlécek meghatározására szolgál.

   - **ResponseHeaderConfiguration**: A válasz üzenetfejlécének mezői újraírási kívánt és az új értéket a fejlécek meghatározására szolgál.

   - **ActionSet**: Tartalmazza a korábban megadott kérelmek és válaszfejlécek konfigurációit.

   - **A feltétel**: Választható konfiguráció. Újraírási feltételek kiértékelése a HTTP (S)-kérelmek és válaszok tartalmát. Az újraírási művelet fordul elő, ha a HTTP (S) kérelem vagy válasz megegyezik a újraírási feltétel.

     Ha egynél több feltétel társítása egy műveletet, a művelet csak akkor, ha a feltételek mindegyike teljesül következik be. Más szóval a művelet egy logikai és műveletet.

   - **RewriteRule**: Több újraírási műveletet tartalmaz / újraírási feltétel kombinációi.

   - **RuleSequence**: Választható konfiguráció, amely segít meghatározni a sorrendet, amely újraírási szabályokkal hajtható végre. Ez a konfiguráció akkor hasznos, ha több újraírási szabályok újraírási készlet rendelkezik. Egy szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály első fut. Ha két újraírási szabályok szabály feladatütemezési ugyanazt az értéket rendel, akkor a végrehajtás sorrendje nem determinisztikus.

     Ha a RuleSequence nem ad meg explicit módon, 100, az alapértelmezett érték van beállítva.

   - **RewriteRuleSet**: Több újraírási szabályt, amely hozzá lesz rendelve egy kérelem-útválasztási szabályt tartalmaz.

2. A RewriteRuleSet csatolása egy útválasztási szabályt. Az újraírási konfigurációs van csatolva, a forrás figyelőt az útválasztási szabályt. Ha egy alapszintű útválasztási szabályt használ, a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használatakor a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ebben az esetben csak vonatkozik egy helyet a megadott elérési út területéhez.

Hozzon létre több HTTP-fejléc újraírási csoportokat, és minden egyes újraírási több kérésfigyelőt beállítása a alkalmazni. Azonban csak az egyik értéke egy adott hallgató újraírási alkalmazhat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Adja meg a HTTP-fejléc újraírási szabálykonfiguráció

Ebben a példában egy átirányítási URL-cím, amikor a location fejlécet azurewebsites.net hivatkozást tartalmaz a HTTP-válasz location fejlécébe újraírásával módosítjuk. Ehhez hozzáadjuk a válasz location fejlécébe azurewebsites.net tartalmaz-e kiértékelendő feltétel. A mintát fogjuk használni `(https?):\/\/.*azurewebsites\.net(.*)$`. Fogjuk használni, és `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként. Ezt az értéket lecseréli *azurewebsites.net* a *contoso.com* location fejlécébe.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Az application gateway konfigurációjának beolvasása

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>A kérelem-útválasztási szabály konfigurációjának beolvasása

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Frissítse az application gateway HTTP-fejlécek újraírását konfigurációja

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Egy átírási szabály törlése

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>További lépések

Néhány gyakori alkalmazási helyzetek beállításával kapcsolatos további információkért lásd: [közös fejlécének újraírási forgatókönyvek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).