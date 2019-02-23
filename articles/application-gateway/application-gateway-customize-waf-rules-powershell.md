---
title: Testre szabhatja a webalkalmazási tűzfalszabályok az Azure Application Gatewayjel – PowerShell
description: Ez a cikk webalkalmazási tűzfalszabályok az Application Gatewayen PowerShell-lel testreszabása ismertetése.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: 1e1638d69915f16b9f30acc5b6a0265b25c2c561
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728879"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Webalkalmazási tűzfalszabályok Powershellen keresztül testreszabása

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ezek a védelmi által az Open Web Application Security Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály által kiváltott hamis pozitív okozhat, és valódi adatforgalmat. Ebből kifolyólag az Application Gateway lehetővé teszi a csoportok és a szabályok testreszabása. Az adott csoportok és a szabályok további információkért lásd: [webes alkalmazás tűzfal CRS-szabálycsoportjainak és szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

A következő hitelesítésikód-példák bemutatják, hogyan-szabályok és konfigurálható a WAF-kompatibilis application gatewayen megtekintéséhez.

### <a name="view-rule-groups"></a>A szabály csoportok megtekintése

Az alábbi példa bemutatja, hogyan szabály csoportok megtekintése:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

A következő kimenet az előző példából csonkolt választ:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Szabályok letiltása

A következő példa letiltja a szabályok `911011` és `911012` egy application gateway-en:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>A kötelező szabályok

Az alábbi lista tartalmazza a WAF blokkolása a megelőzés üzemmód (a kivételeket bejelentkeztek észlelési mód) a kérelmet eredményező feltételeket. Ezek nem lehet beállítva vagy le van tiltva:

* Nem sikerült elemezni a kérelem törzsében eredményez blokkolja, a kérelem, kivéve, ha a szervezet ellenőrzési ki van kapcsolva (XML, JSON, az űrlap adatait)
* Kérelem törzse (rendelkező fájlokat) a következő adattípus adathossza mérete nagyobb, mint a beállított korlát
* A kérelem törzsében (beleértve a fájlok) nagyobb, mint a határérték
* Belső hiba történt a WAF-vezérlő

CRS 3.x specifikus:

* Bejövő anomáliadetektálás pontszámot túllépte a küszöbértéket

## <a name="next-steps"></a>További lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
