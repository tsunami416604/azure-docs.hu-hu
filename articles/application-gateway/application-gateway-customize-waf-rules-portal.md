---
title: "Webes alkalmazás tűzfalszabályok Azure Application Gateway - Azure-portál testreszabása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogy miként webes alkalmazás tűzfalszabályokat az alkalmazás átjáró és az Azure portál testreszabásához."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 406e491aa54806b3534ef0f500d6aea03434e2c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Webes alkalmazás tűzfalszabályokat az Azure portálon keresztül testreszabása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Az Azure Application Gateway webalkalmazási tűzfal (WAF) webalkalmazások védelmet biztosít. A védelem által a nyitott webes alkalmazás biztonsági Project (OWASP) Core szabály beállítása (CRS) vannak megadva. Néhány szabály okozhat a vakriasztások és valós adatforgalmat. Emiatt Application Gateway lehetővé teszi a csoportok és a szabályok testreszabásához. Az adott szabály csoportokról és a szabályok további információkért lásd: [webes alkalmazás tűzfal CRS csoportok és a szabályok listája](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Az Alkalmazásátjáró nem használja a WAF rétegben, ha az Alkalmazásátjáró WAF csomagra frissítési lehetőség a jobb oldali ablaktáblán jelenik meg. 

![WAF engedélyezése][fig1]

## <a name="view-rule-groups-and-rules"></a>A szabály csoportok megtekintése és szabályok

**Csoportok és a szabályok megtekintése**
   1. Keresse meg az Alkalmazásátjáró, és válassza ki **webalkalmazási tűzfal**.  
   2. Válassza ki **speciális konfiguráció**.  
   Ez a nézet a lapon megadott a kijelölt szabálykészletet az összes szabály csoportot táblázatát jeleníti meg. A szabály jelölőnégyzetekből ki van jelölve.

![Letiltott szabályok konfigurálása][1]

## <a name="search-for-rules-to-disable"></a>Tiltsa le a szabályok keresése

A **webes alkalmazás tűzfalbeállítások** panel lehetővé teszi a szabályok egy szöveges keresés keresztül szűrése. Az eredmény csak a csoportok és a szabályt, amely tartalmazza a keresett szöveget jeleníti meg.

![Szabályok keresése][2]

## <a name="disable-rule-groups-and-rules"></a>Tiltsa le a csoportok és szabályok

Ha az még szabályok letiltása, letilthatja egy teljes csoport vagy a egy vagy több szabály csoportok meghatározott szabályokat. 

**Csoportok és a speciális szabályok letiltása**

   1. A szabályok vagy letiltani kívánt csoportok kereséséhez.
   2. Törölje a jelölőnégyzet a letiltani kívánt. 
   2. Kattintson a **Mentés** gombra. 

![Módosítások mentése][3]

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a letiltott szabályok, megismerheti a WAF naplók megtekintéséhez. További információkért lásd: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
