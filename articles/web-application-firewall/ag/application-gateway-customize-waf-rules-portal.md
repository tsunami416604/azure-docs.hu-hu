---
title: Szabályok testreszabása portál használatával – Azure webalkalmazás-tűzfal
description: Ez a cikk a webalkalmazás-tűzfal szabályok testreszabásáról az Azure Portalon keresztül az Application Gateway alkalmazásban.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048377"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Webalkalmazás-tűzfal szabályainak testreszabása az Azure Portal használatával

Az Azure Application Gateway webalkalmazás-tűzfal (WAF) védelmet nyújt a webalkalmazások számára. Ezeket a védelmet az Open Web Application Security Project (OWASP) core rule set (CRS) biztosítja. Egyes szabályok hamis pozitív akta, és blokkolja a valós forgalmat. Ezért az Application Gateway lehetővé teszi a szabálycsoportok és szabályok testreszabását. Az adott szabálycsoportokról és szabályokról a [Webapplication Firewall CRS szabálycsoportok és szabályok listája című](application-gateway-crs-rulegroups-rules.md)témakörben talál további információt.

>[!NOTE]
> Ha az alkalmazásátjáró nem használja a WAF-réteget, az alkalmazásátjáró WAF-szintre való frissítésének lehetősége a jobb oldali ablaktáblában jelenik meg. 

![WAF engedélyezése][fig1]

## <a name="view-rule-groups-and-rules"></a>Szabálycsoportok és szabályok megtekintése

**Szabálycsoportok és szabályok megtekintése**
1. Tallózással keresse meg az alkalmazásátjárót, és válassza a **Webalkalmazás tűzfala**lehetőséget.  
2. Válassza ki a **WAF-házirendet.**
2. Válassza a **Felügyelt szabályok lehetőséget**.

   Ebben a nézetben a kiválasztott szabálykészlettel rendelkező összes szabálycsoport lapján egy táblázat látható. A szabály összes jelölőnégyzete be van jelölve.

## <a name="disable-rule-groups-and-rules"></a>Szabálycsoportok és szabályok letiltása

> [!IMPORTANT]
> Legyen elővigyázatos, amikor letiltja a szabálycsoportokat vagy szabályokat. Ez fokozott biztonsági kockázatnak teheti ki.

A szabályok letiltásakor egy teljes szabálycsoportot vagy adott szabályokat letilthatja egy vagy több szabálycsoport alatt. 

**Szabálycsoportok vagy adott szabályok letiltása**

   1. Keresse meg a letiltani kívánt szabályokat vagy szabálycsoportokat.
   2. Jelölje be a letiltani kívánt szabályok jelölőnégyzetét. 
   3. Jelölje ki a lap tetején lévő műveletet (engedélyezés/letiltás) a kijelölt szabályokhoz.
   2. Kattintson a **Mentés** gombra. 

![Módosítások mentése][3]

## <a name="mandatory-rules"></a>Kötelező szabályok

Az alábbi lista olyan feltételeket tartalmaz, amelyek miatt a WAF letiltja a kérelmet megelőzési módban. Észlelési módban kivételként vannak naplózva.

Ezek nem konfigurálhatók vagy tilthatók le:

* A kérelemtörzs elemzése sikertelena a kérelem blokkolása esetén, kivéve, ha a testvizsgálat ki van kapcsolva (XML, JSON, űrlapadatok)
* A kérelemtörzs (fájlok nélkül) az adatok hossza nagyobb, mint a beállított korlát
* A kérelemtörzs (a fájlokkal együtt) nagyobb, mint a korlát
* Belső hiba történt a WAF motorban

CRS 3.x specifikus:

* A bejövő anomáliapontszám túllépte a küszöbértéket

## <a name="next-steps"></a>További lépések

A letiltott szabályok konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információt az [Application Gateway diagnosztikája című témakörben talál.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
