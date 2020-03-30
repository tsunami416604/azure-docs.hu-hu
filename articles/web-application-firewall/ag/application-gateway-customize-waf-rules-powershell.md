---
title: Szabályok testreszabása a PowerShell használatával
titleSuffix: Azure Web Application Firewall
description: Ez a cikk a Web Application Firewall-szabályok testreszabásáról nyújt tájékoztatást az Application Gateway powershell-ben.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048513"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Webalkalmazás-tűzfal szabályainak testreszabása a PowerShell használatával

Az Azure Application Gateway webalkalmazás-tűzfal (WAF) védelmet nyújt a webalkalmazások számára. Ezeket a védelmet az Open Web Application Security Project (OWASP) core rule set (CRS) biztosítja. Egyes szabályok hamis pozitív akta, és blokkolja a valós forgalmat. Ezért az Application Gateway lehetővé teszi a szabálycsoportok és szabályok testreszabását. Az adott szabálycsoportokról és szabályokról a [Webapplication Firewall CRS szabálycsoportok és szabályok listája](application-gateway-crs-rulegroups-rules.md)című témakörben talál további információt.

## <a name="view-rule-groups-and-rules"></a>Szabálycsoportok és szabályok megtekintése

Az alábbi kódpéldák bemutatják, hogyan tekintheti meg a WAF-kompatibilis alkalmazásátjárón konfigurálható szabályokat és szabálycsoportokat.

### <a name="view-rule-groups"></a>Szabálycsoportok megtekintése

A következő példa bemutatja, hogyan tekintheti meg a szabálycsoportokat:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

A következő kimenet az előző példa csonkolt válasza:

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

A következő példa `911011` letiltja a szabályokat és `911012` az alkalmazásátjárót:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Kötelező szabályok

Az alábbi lista olyan feltételeket tartalmaz, amelyek miatt a WAF letiltja a kérelmet megelőzési módban (észlelési módban kivételként naplózzák őket). Ezek nem konfigurálhatók vagy tilthatók le:

* A kérelemtörzs elemzése sikertelena a kérelem blokkolása esetén, kivéve, ha a testvizsgálat ki van kapcsolva (XML, JSON, űrlapadatok)
* A kérelemtörzs (fájlok nélkül) az adatok hossza nagyobb, mint a beállított korlát
* A kérelemtörzs (a fájlokkal együtt) nagyobb, mint a korlát
* Belső hiba történt a WAF motorban

CRS 3.x specifikus:

* A bejövő anomáliapontszám túllépte a küszöbértéket

## <a name="next-steps"></a>További lépések

A letiltott szabályok konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információt az [Application Gateway Diagnosztika című témakörben talál.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
