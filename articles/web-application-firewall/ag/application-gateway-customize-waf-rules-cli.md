---
title: Szabályok testreszabása a CLI használatával – Azure webalkalmazás-tűzfal
description: Ez a cikk awebapplication firewall szabályok testreszabásáról az Application Gateway az Azure CLI testreszabásáról.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048536"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Webalkalmazás-tűzfal szabályainak testreszabása az Azure CLI használatával

Az Azure Application Gateway webalkalmazás-tűzfal (WAF) védelmet nyújt a webalkalmazások számára. Ezeket a védelmet az Open Web Application Security Project (OWASP) core rule set (CRS) biztosítja. Egyes szabályok hamis pozitív akta, és blokkolja a valós forgalmat. Ezért az Application Gateway lehetővé teszi a szabálycsoportok és szabályok testreszabását. Az adott szabálycsoportokról és szabályokról a [Webapplication Firewall CRS szabálycsoportok és szabályok listája című](application-gateway-crs-rulegroups-rules.md)témakörben talál további információt.

## <a name="view-rule-groups-and-rules"></a>Szabálycsoportok és szabályok megtekintése

Az alábbi kódpéldák bemutatják, hogyan tekintheti meg a konfigurálható szabályokat és szabálycsoportokat.

### <a name="view-rule-groups"></a>Szabálycsoportok megtekintése

A következő példa bemutatja, hogyan tekintheti meg a szabálycsoportokat:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A következő kimenet az előző példa csonkolt válasza:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Szabályok megtekintése szabálycsoportban

A következő példa bemutatja, hogyan tekintheti meg a szabályokat egy adott szabálycsoportban:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A következő kimenet az előző példa csonkolt válasza:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Szabályok letiltása

A következő példa `910018` letiltja a szabályokat és `910017` az alkalmazásátjárót:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

A letiltott szabályok konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információt az [Application Gateway diagnosztikája című témakörben talál.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
