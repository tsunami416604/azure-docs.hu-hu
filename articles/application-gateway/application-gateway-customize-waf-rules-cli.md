---
title: Webalkalmazási tűzfalszabályok az Azure Application Gateway – Azure CLI-vel testreszabása |} A Microsoft Docs
description: Ez a cikk webalkalmazási tűzfalszabályok az Application Gateway az Azure CLI-vel testreszabása ismertetése.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: c02e4edabdcb73bc14c64b42788cddc98d78498c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964121"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Webalkalmazási tűzfalszabályok az Azure CLI-n keresztül testreszabása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ezek a védelmi által az Open Web Application Security Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály által kiváltott hamis pozitív okozhat, és valódi adatforgalmat. Ebből kifolyólag az Application Gateway lehetővé teszi a csoportok és a szabályok testreszabása. Az adott csoportok és a szabályok további információkért lásd: [web application firewall CRS-szabálycsoportjainak és szabályok listája](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

A következő hitelesítésikód-példák bemutatják, hogyan-szabályok és konfigurálható megtekintéséhez.

### <a name="view-rule-groups"></a>A szabály csoportok megtekintése

Az alábbi példa bemutatja, hogyan megtekintéséhez a csoportok:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A következő kimenet az előző példából csonkolt választ:

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

### <a name="view-rules-in-a-rule-group"></a>Egy szabály csoport szabályok megtekintése

Az alábbi példa bemutatja egy megadott csoportban lévő szabályok megtekintése:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A következő kimenet az előző példából csonkolt választ:

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

A következő példa letiltja a szabályok `910018` és `910017` egy application gateway-en:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>További lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
