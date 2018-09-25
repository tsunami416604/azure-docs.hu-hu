---
title: Webalkalmazási tűzfalszabályok az Azure Application Gateway – az Azure portál testreszabása |} A Microsoft Docs
description: Ez a cikk információt nyújt az webalkalmazási tűzfalszabályok az Application Gateway az Azure Portallal testreszabásához.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: victorh
ms.openlocfilehash: 30df26dc3a9697d3435779f91c32b2d99a747b88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990467"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Webalkalmazási tűzfalszabályok az Azure Portalon keresztül testreszabása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Az Azure Application Gateway webalkalmazási tűzfala (WAF) védelmet kínál a webes alkalmazásokhoz. Ezek a védelmi által az Open Web Application Security Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály által kiváltott hamis pozitív okozhat, és valódi adatforgalmat. Ebből kifolyólag az Application Gateway lehetővé teszi a csoportok és a szabályok testreszabása. Az adott csoportok és a szabályok további információkért lásd: [web application firewall CRS-szabálycsoportjainak és szabályok listája](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Ha az application gateway nem használja a WAF-csomag, az application gateway WAF-szintre frissítheti a jobb oldali ablaktáblán jelenik meg. 

![WAF engedélyezése][fig1]

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

**Csoportok és szabályok megtekintése**
   1. Keresse meg az application gateway, és válassza ki **webalkalmazási tűzfal**.  
   2. Válassza ki **speciális szabálykonfiguráció**.  
   Ez a nézet a kiválasztott szabálykészlet megadott összes szabály csoport oldalán táblázatát jeleníti meg. A szabály jelölőnégyzetekből ki van jelölve.

![Letiltott szabályok konfigurálása][1]

## <a name="search-for-rules-to-disable"></a>Letiltja a szabályok keresése

A **webalkalmazás tűzfalbeállításai** panel lehetővé teszi, hogy egy szöveges keresés révén a szabályok szűrése. Az eredmény csak a csoportok és a szabályokat, amelyek tartalmazzák a keresett szöveget jeleníti meg.

![Szabályok keresése][2]

## <a name="disable-rule-groups-and-rules"></a>Disable-szabálycsoportjainak és szabályok

Ha az Ön letiltja a szabályok, letilthatja egy teljes csoport vagy az adott szabályok alapján egy vagy több szabály csoport. 

**Csoportok vagy adott szabályok letiltása**

   1. Keresse meg a szabályokat vagy letiltani kívánt csoportok.
   2. Törölje a szabályokat, amelyek le kívánja tiltani a jelölőnégyzeteit. 
   2. Kattintson a **Mentés** gombra. 

![Változtatások mentése][3]

## <a name="next-steps"></a>További lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF-naplók megtekintéséhez. További információkért lásd: [Application Gateway-diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
