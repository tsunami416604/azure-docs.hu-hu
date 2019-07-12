---
title: Webalkalmazási tűzfalszabályok az Azure Application Gateway – az Azure portál testreszabása
description: Ez a cikk információt nyújt az webalkalmazási tűzfalszabályok az Application Gateway az Azure Portallal testreszabásához.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: f4af52907ab2e950636dea0874b49500f3a6b587
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613442"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Webalkalmazási tűzfalszabályok az Azure Portalon keresztül testreszabása

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

A **webalkalmazás tűzfalbeállításai** lap lehetővé teszi, hogy egy szöveges keresés révén a szabályok szűrése. Az eredmény csak a csoportok és a szabályokat, amelyek tartalmazzák a keresett szöveget jeleníti meg.

![Szabályok keresése][2]

## <a name="disable-rule-groups-and-rules"></a>Disable-szabálycsoportjainak és szabályok

> [!IMPORTANT]
> Körültekintően járjon el, ha letiltja a szabályt a csoportokat vagy szabályokat. Ez lehet elérhetővé tenni, az fokozott biztonsági kockázatokat.

Ha éppen letiltja a szabályok, egy teljes csoport vagy az adott szabályok alapján egy vagy több szabály csoport is letilthatja. 

**Csoportok vagy adott szabályok letiltása**

   1. Keresse meg a szabályokat vagy letiltani kívánt csoportok.
   2. Törölje a szabályokat, amelyek le kívánja tiltani a jelölőnégyzeteit. 
   2. Kattintson a **Mentés** gombra. 

![Módosítások mentése][3]

## <a name="mandatory-rules"></a>A kötelező szabályok

Az alábbi lista tartalmazza a WAF megelőzés üzemmódban a kérelem blokkolása eredményező feltételeket. Az észlelési mód akkor jelentkezett, kivételeket.

Ezek nem lehet beállítva vagy le van tiltva:

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
