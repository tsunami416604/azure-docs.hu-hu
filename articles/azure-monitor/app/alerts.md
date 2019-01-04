---
title: Riasztásokat állíthat be az Azure Application Insights |} A Microsoft Docs
description: Lassú válasz a következő alkalommal, kivételek, és más teljesítményére és használati módosításokat a webalkalmazásban értesítést kaphat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 0f16f61e99e059c8cf21ec4ffa85eef0a04702bc
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972992"
---
# <a name="set-alerts-in-application-insights"></a>Riasztásokat állíthat be az Application insights szolgáltatásban
[Az Azure Application Insights] [ start] riasztja Önt, a webalkalmazásban a teljesítmény vagy a használati metrikák változásai. 

Az Application Insights monitorozza az élő alkalmazás a egy [sokféle Platform] [ platforms] teljesítménybeli problémák diagnosztizálása és a használati mintákról.

A riasztások három fő típusba sorolhatók:

* **Metrikákhoz kapcsolódó riasztások** mondja el, ha egy metrika átlépi egy bizonyos időn – például a válaszidőt, a kivételek számát, a CPU-használat vagy a lapmegtekintések egy küszöbértéket. 
* [**Webes teszteket** ] [ availability] mondja el, ha a hely nem érhető el az interneten, vagy válaszra képes lassan. [További][availability].
* [**Proaktív diagnosztika** ](../../application-insights/app-insights-proactive-diagnostics.md) konfigurálása automatikusan megtörténik a szokatlan teljesítmény minták kaphat értesítést.

Ebben a cikkben metrikákhoz kapcsolódó riasztások koncentrálunk.

## <a name="set-a-metric-alert"></a>Metrikariasztás beállítása
A riasztási szabályok panel megnyitásához, és használja a Hozzáadás gombra. 

![A riasztási szabályok paneljén válassza a riasztás hozzáadása. Állítsa be az alkalmazás és mérhető, adja meg a riasztás nevét, és válassza a metrika az erőforrásnak.](./media/alerts/01-set-metric.png)

* Állítsa be az erőforrást, mielőtt a többi tulajdonságot. **Válassza ki a "(összetevők)" erőforrást** Ha azt szeretné, a teljesítmény vagy a használati metrikákhoz riasztásokat állíthat be.
* A neve, amely engedélyezi a riasztást, az erőforráscsoportot (nem csak az alkalmazás) belül egyedinek kell lennie.
* Ügyeljen arra, hogy vegye figyelembe az egységeket, amelyben kéri, hogy adja meg a küszöbértéket.
* Ha bejelöli a jelölőnégyzetet "E-mail küldése a tulajdonosoknak...", riasztások által küldött e-mailek mindenki számára, aki hozzáfér az ebben az erőforráscsoportban. Bontsa ki a csoport, akik, adja hozzá őket a [erőforráscsoportba vagy előfizetésbe](../../application-insights/app-insights-resources-roles-access-control.md) (nem az erőforrás).
* Ha megadja a "További e-mailek", riasztást kap az adott személyek vagy csoportok (függetlenül attól, be van jelölve az "e-mail-tulajdonosok..." mezőben). 
* Állítsa be a [webhook cím](../../azure-monitor/platform/alerts-webhooks.md) Ha meg van adva egy webalkalmazást, amely válaszol a riasztásokra. A riasztás aktiválásakor mind a megoldott nevezzük. (De vegye figyelembe, hogy jelenleg lekérdezési paraméterek nem továbbítódnak tulajdonságokként webhook.)
* Letilthatja a riasztás engedélyezése vagy: a gombok a panel tetején.

*A riasztás hozzáadása gomb nem látható.* 

* Szervezeti fiók használatával? Beállíthat riasztásokat, ha rendelkezik tulajdonosi vagy közreműködői hozzáférést az alkalmazás-erőforrást. Vessen egy pillantást az Access Control panelen. [További tudnivalók a hozzáférés-vezérlés][roles].

> [!NOTE]
> A riasztások panelen láthatja, hogy már van egy riasztási csoport: [Proaktív diagnosztika](../../application-insights/app-insights-proactive-failure-diagnostics.md). Az automatikus riasztás figyeli egy adott mérőszám, kérelmek hibaaránya. Kivéve, ha úgy dönt, hogy a proaktív riasztás letiltása, nem kell beállítani a saját riasztás kérelmek hibaaránya. 
> 
> 

## <a name="see-your-alerts"></a>A riasztások megtekintéséhez
Közötti inaktív és az aktív kap egy e-mailt, ha egy riasztás módosítások állapotba. 

Minden riasztás aktuális állapota akkor jelenik meg a riasztási szabályok paneljén.

Nincs friss tevékenység a riasztások összegzését legördülő:

![Riasztások kombinált](./media/alerts/010-alert-drop.png)

Állapot változásainak az előzményeit a tevékenységnaplóban van:

![Az Áttekintés panelen kattintson a beállítások, a vizsgálati naplók](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>A riasztások működése
* Riasztás három állapota van: "Soha nem aktiválódik", "Aktiválva" és "Megoldva." Legutóbbi értékelésének volt igaz, aktivált azt jelenti, hogy a megadott feltétel.
* Értesítés jön létre, ha egy riasztás állapota. (Ha a riasztási feltétel, a riasztás létrehozásakor már volt igaz, nem kaphat értesítést mindaddig, amíg a feltétel hamis kerül.)
* Minden értesítés e-mailt állít elő, ha be van jelölve az e-mailek mezőbe, vagy a megadott e-mail-címeket. Az értesítések legördülő listája is megjeleníthető.
* Riasztás minden alkalommal, amikor egy metrika megérkezik, de más módon nem értékeli ki.
* A kiértékelés a metrika összefoglalja az előző időszakban, és összehasonlítja azt a küszöbértéket, az új állapot meghatározásához.
* Az időszak, Ön által választott Megadja azt az időtartamot, amelyen a metrikák vannak összesítve. Ez nincs hatással, milyen gyakran legyen kiértékelve a riasztást: attól függ, hogy a metrikák megérkezését gyakoriságát.
* Ha nincs adat érkezik egy adott metrika egy kis ideig, a térköz rendelkezik riasztás értékelése és a metrika explorer a diagramok különböző hatásokkal. A metrika Explorerben a látható adatok nem hosszabb, mint a mintavételi időközben a diagramot, ha a diagram mutatja a 0 érték. De ugyanazt a metrika alapján riasztás nem újraértékeli, és a riasztási állapot változatlan marad. 
  
    Végül adatok érkeznek, amikor a diagram ugrik vissza egy nullától eltérő értékre. A riasztást kiértékeli a megadott időszakra vonatkozóan rendelkezésre álló adatok alapján. Ha az új adatok pont az egyetlen, az időtartamon belül, az összesítés alapul, csak az, hogy az adatpont.
* Riasztás is elhalványul gyakori riasztási és kifogástalan állapota, között akkor is, ha hosszabb ideig állít be. Ez akkor fordulhat elő, ha a metrika értéke a küszöbérték körül mutat. Nem kapott szerepel a küszöbértéket: áttérés riasztás történik, ha megegyezik az átállás kifogástalanra.

## <a name="what-are-good-alerts-to-set"></a>Mik azok a megfelelő riasztások beállítása
Ez az alkalmazás függ. Első lépésként érdemes nem túl sok metrikák be. Némi időt a mérőszám-diagramok megnézzük, miközben az alkalmazás fut, betekintést nyerhet a viselkedik általában. Ez az eljárás segítségével keresse meg a teljesítmény javítására. Ezután állíthat be riasztásokat mondja el, ha a metrikák nyissa meg a normál zónán kívüli. 

Népszerű riasztások a következők:

* [Böngésző metrikák][client], különösen a böngésző **lapbetöltési idők lapon**, jó webes alkalmazásokhoz. Ha a lapon számos parancsfájlok, kell keresnie az **böngészőkivételek**. Ezek a metrikák és riasztások eléréséhez be kell [weblap figyelési][client].
* **Kiszolgáló válaszideje** webes alkalmazás kiszolgálói oldalára. Riasztások beállítása, valamint ez a mérőszám, ha azt művelettől aránytalanul magas kérelemarányok megtekintéséhez kövesse figyelemmel: variation jelezheti, hogy az alkalmazás fut-e elegendő erőforrással. 
* **Kiszolgálókivételek** – őket, hogy rendelkezik egy elvégzése [további telepítési](../../azure-monitor/app/asp-net-exceptions.md).

Ne feledje, hogy [proaktív hibadiagnosztika arány](../../application-insights/app-insights-proactive-failure-diagnostics.md) automatikusan figyelni a sebesség, amellyel az alkalmazás válasza, hiba kód-kérelmekre. 

## <a name="automation"></a>Automation
* [Riasztások beállítása automatizálása a PowerShell használatával](../../application-insights/app-insights-powershell-alerts.md)
* [Webhookok használatával automatizálhatja a riasztásokra való reagálásról](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Lásd még
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
* [Riasztások beállítása automatizálása](../../application-insights/app-insights-powershell-alerts.md)
* [Proaktív diagnosztika](../../application-insights/app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../application-insights/app-insights-platforms.md
[roles]: ../../application-insights/app-insights-resources-roles-access-control.md
[start]: ../../application-insights/app-insights-overview.md

