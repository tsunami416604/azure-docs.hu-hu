---
title: Az Azure Application insights szolgáltatásban felhasználói, munkamenet és esemény elemzése |} A Microsoft docs
description: A felhasználók a webalkalmazás demográfiai elemzése.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7d378c2f72035c3584e1f5cd3c1f0fb9a5d5c2ed
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119867"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Felhasználók, munkamenetek és események elemzése az Application insights szolgáltatásban

Ismerje meg, amikor a felhasználók használják a webalkalmazásokat, milyen lapok, leginkább érdeklő, ahol a felhasználók is található, és milyen böngészőket és operációs rendszerek használata. Üzleti és a használati adatokat elemezhet [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Application Insights felhasználók képernyőképe](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Bevezetés

Ha még nem látható a felhasználók, munkamenetek és események paneleket az Application Insights portálon lévő adatok [megtudhatja, hogyan kezdheti el a használati eszközökkel](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A felhasználók, munkamenetek és események Szegmentálás eszköz

Három a használati paneleket a webes alkalmazás három perspektívából szeletelésére és feldarabolására használnak a telemetriai használni ugyanazzal a módszerrel. Szűréssel és az adatok felosztása, insights különböző lapjait és szolgáltatásait relatív használatát ismertető részletes elemzést.

* **Felhasználó-eszköz**: Hányan használják az alkalmazást és annak szolgáltatásait.  Felhasználók számítanak névtelen azonosítók tárolva a böngésző cookie-k használatával. A különböző böngészők vagy a gépek használata egyetlen személy szerint egynél több felhasználó beleszámítanak.
* **Munkamenetek**: Felhasználói tevékenység, hogy hány munkamenetben vette az egyes oldalak és az alkalmazás funkcióit. A munkamenet akkor számít, felhasználói inaktivitás fél óra múlva, vagy 24 órányi folyamatos, becsült használat után.
* **Események eszközének**: Milyen gyakran egyes oldalak és az alkalmazás funkcióit használja. Egy oldal nézet külön tranzakciónak minősül Ha egy böngészőben lap betöltésekor az alkalmazásból, amennyiben rendelkezik [kialakítva, hogy](../../azure-monitor/app/javascript.md). 

    Egyéni esemény több előfordulása hiba történik az alkalmazás, gyakran egy felhasználói beavatkozás, például egy gombra való kattintással vagy néhány tevékenység befejezése után a jelöli. Az alkalmazás szúr be kódot [egyéni események létrehozása](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Az egyes felhasználókat kérdezi le

Ismerje meg a különböző felhasználói csoportokhoz a felhasználók eszköz tetején a lekérdezési beállítások módosításával:

* Megjelenítés: Válassza ki a elemzéséhez felhasználói kohorsz a következő.
* Használja ki: Válassza ki az egyéni események és Lapmegtekintések.
* Során: Válasszon időtartományt.
* Szerint: Válassza ki a gyűjtőbe az adatokat, vagy egy bizonyos idő, vagy egy másik tulajdonságot, mint például a böngészőben vagy az városa módját.
* Felosztási szempont: Válassza ki egy tulajdonságot amely split vagy szegmens az adatokat. 
* Szűrők hozzáadása: A lekérdezést, hogy bizonyos felhasználók, munkamenetek és események azok tulajdonságait, például a böngészőben vagy az városa alapján korlátozza. 
 
## <a name="saving-and-sharing-reports"></a>És jelentések megosztása 
A Mindenki más az Application Insights-erőforrást a megosztott jelentések szakasz hozzáféréssel rendelkező felhasználók jelentéseket, vagy a saját csak a saját jelentések szakaszban Ön, vagy megosztott mentheti.

A felhasználók, munkamenetek és események jelentés; mutató hivatkozás megosztása Kattintson a **megosztás** az eszköztáron, majd másolja a hivatkozást.

A felhasználók, munkamenetek és események jelentésben; másolatának megosztása Kattintson a **megosztás** az eszköztáron, majd kattintson a **Word ikon** az adatokat egy Word-dokumentum létrehozásához. Vagy kattintson a **Word ikon** a fő diagram felett.

## <a name="meet-your-users"></a>A felhasználók megismerése

A **felel meg a felhasználók** szakasz információkat jelenít meg az aktuális lekérdezés egyező nagyjából öt mintafelhasználók. Figyelembe véve, és felfedezése az egyének mellett összesítések, a viselkedés is vonatkozó adatokat a felhasználók miként ténylegesen használják az alkalmazást.

## <a name="next-steps"></a>További lépések

- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](usage-retention.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Adja hozzá a felhasználói környezet](usage-send-user-context.md)