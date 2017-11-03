---
title: "Webes alkalmazás tűzfalszabályok Azure Application Gateway - Azure CLI 2.0 testreszabása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogy miként webes alkalmazás tűzfalszabályokat az Azure CLI 2.0 Alkalmazásátjáró testreszabásához."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 3051f71f269e409b76e6a19fdcd2feae2a04b8fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Webes alkalmazás tűzfalszabályok keresztül az Azure CLI 2.0 testreszabása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Az Azure Application Gateway webalkalmazási tűzfal (WAF) webalkalmazások védelmet biztosít. A védelem által a nyitott webes alkalmazás biztonsági Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály okozhat a vakriasztások és valós adatforgalmat. Emiatt Application Gateway lehetővé teszi a csoportok és a szabályok testreszabásához. Az adott szabály csoportokról és a szabályok további információkért lásd: [webes alkalmazás tűzfal CRS csoportok és a szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

Az alábbi kód példák bemutatják, hogyan a szabályok és a csoportok, amelyek konfigurálhatók.

### <a name="view-rule-groups"></a>A szabály csoportok megtekintése

A következő példa bemutatja, hogyan megtekintéséhez a csoportok:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A következő az előző példából csonkolt válasz kimenete:

```
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

### <a name="view-rules-in-a-rule-group"></a>A szabály csoport szabályok megtekintése

A következő példa bemutatja, hogyan szeretné megtekinteni a megadott szabály szabályok:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A következő az előző példából csonkolt válasz kimenete:

```
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

A következő példa letiltja a szabályok `910018` és `910017` az Alkalmazásátjáró:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF naplók megtekintéséhez. További információkért lásd: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
