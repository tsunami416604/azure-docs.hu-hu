---
title: Az Azure Application Gateway HTTP-fejlécek újraírása
description: Ez a cikk információt nyújt az Azure Application Gateway a HTTP-fejlécek újraírási Azure PowerShell-lel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548319"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Újraírási HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure PowerShell-lel

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure PowerShell használatával egy [Application Gateway v2 szintű Termékváltozatot](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) újraírási a HTTP-fejléceket, a kérelmek és válaszok.

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Ehhez az oktatóanyaghoz az Azure PowerShellt helyileg kell futtatnia. Rendelkeznie kell Az 1.0.0-s verziójának modul, vagy újabb verziója van telepítve. Futtatás `Import-Module Az` , majd`Get-Module Az` a verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.
- Szüksége lesz egy Application Gateway v2 a v1-Termékváltozat nem támogatott Termékváltozat, mivel a fejléc újraírási képessége. Ha nem rendelkezik a v2 szintű Termékváltozatot, hozzon létre egy [Application Gateway v2 szintű Termékváltozatot](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) megkezdése előtt.

## <a name="what-is-required-to-rewrite-a-header"></a>Mi szükséges egy fejléc újraírása

HTTP-fejléc újraírási konfigurálásához meg kell:

1. A szükséges a http-fejlécek újraírási új objektumokat hozhat létre:

   - **RequestHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a kérés üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.

   - **ResponseHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a válasz üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.

   - **ActionSet**: Ez az objektum tartalmaz a kérelmek és válaszfejlécek, a fentiekben ismertetett konfigurációit.

   - **A feltétel**: Egy opcionális konfigurációs. újraírási feltétel kerül, ha a program a HTTP (S)-kérelmek és válaszok tartalmát értékeli. A újraírási feltétel társított újraírási művelet végrehajtásához a döntés-e a HTTP (S) kérelem vagy válasz párosítva a újraírási feltétel alapul. 

     Ha egynél több feltételek társított egy műveletet, majd a művelet lesz végrehajtva csak akkor, ha a feltételek mindegyike teljesül, azaz, egy logikai és művelet történik.

   - **RewriteRule**: több újraírási művelet – átírás feltétel kombinációk tartalmazza.

   - **RuleSequence**: Ez a választható konfiguráció. Ez segít meghatározni a sorrendet, amelyben a különböző újraírási szabályok végrehajtásra kerülhetnek. Ez akkor hasznos, ha több újraírási szabályok újraírási készlet. A szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály lekérdezi először hajtott végre. Ha megadja a ugyanaz a szabály során, az két újraírási szabályok a végrehajtás sorrendje nem determinisztikus lesz.

     Ha a RuleSequence nem ad meg explicit módon, 100, az alapértelmezett érték lesz beállítva.

   - **RewriteRuleSet**: Ez az objektum több újraírási szabályok, amely hozzá lesz rendelve egy kérelem-útválasztási szabályt tartalmaz.

2. Csatlakoztassa a rewriteRuleSet útválasztási szabályt kell adnia. Ennek az oka az átfogalmazás konfigurációs van csatolva a forrás figyelőt az útválasztási szabályt. Egy alapszintű útválasztási szabályt használatakor a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használata esetén a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ezért csak vonatkozik egy helyet a megadott elérési út területéhez.

Több http-fejléc újraírási csoportok hozhat létre, és minden egyes újraírási set több kérésfigyelőt is alkalmazható. Azonban csak az egyik értéke egy adott hallgató újraírási is alkalmazhat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Adja meg a http-fejléc újraírási szabálykonfiguráció**

Ebben a példában az átirányítási URL-cím lesz módosítjuk, amikor a location fejlécet tartalmaz egy hivatkozást az "azurewebsites.net" újraírásával a http-válasz location fejlécébe. Ehhez hozzáadjuk kiértékelését, hogy a válasz location fejlécébe azurewebsites.net tartalmazza a minta használatával feltétel `(https?):\/\/.*azurewebsites\.net(.*)$`. Ezzel `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként. Ez a művelet felülírja *azurewebsites.net* a *contoso.com* location fejlécébe.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>A meglévő application Gateway konfigurációs beolvasása

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>A meglévő kérelem-útválasztási szabály konfigurációjának beolvasása

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Frissítse az application gateway http-fejlécek újraírását konfigurációja

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

További információ a konfiguráció szükséges elvégeznie néhány gyakoribb felhasználási kapcsolatban lásd: [közös fejlécének újraírási forgatókönyvek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).