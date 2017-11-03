---
title: "Riasztások beállítása a Azure Application insights szolgáltatással |} Microsoft Docs"
description: "Értesítés válaszidejű, kivételek, és egyéb teljesítmény vagy a webalkalmazás használatának változásairól."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: b0e4828b2cb6bbcb4329381e77db3b187d659706
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="set-alerts-in-application-insights"></a>Az Application Insightsban riasztások beállítása
[Az Azure Application Insights] [ start] is figyelmeztet a webalkalmazás a teljesítmény vagy a használati metrikák változásairól. 

Az Application Insights figyeli az élő app egy [számos platformon] [ platforms] segítséget nyújtanak a teljesítménnyel kapcsolatos problémák diagnosztizálásához és használati módokba.

Riasztások három fő típusba sorolhatók:

* **Metrika riasztások** jelzi, hogy ha egy metrika áthalad bizonyos időszakokra – például a válaszidőt, a kivételek száma, a CPU-használat vagy a lapmegtekintések egy küszöbértéket. 
* [**Webalkalmazás-tesztek** ] [ availability] mondja el, ha a webhely nem érhető el az interneten, vagy válaszol lassan. [További][availability].
* [**Proaktív diagnosztika** ](app-insights-proactive-diagnostics.md) értesítjük szokatlan teljesítmény minták automatikusan megtörténik.

Azt a cikkben metrika riasztások összpontosítanak.

## <a name="set-a-metric-alert"></a>A metrika-riasztások beállítása
Nyissa meg a riasztási szabályok panelt, és aztán a Hozzáadás gombra. 

![A riasztási szabályok paneljén válassza riasztási hozzáadása. Állítsa be az alkalmazás mérni, adja meg a riasztás nevét, és válassza ki a metrika erőforrás.](./media/app-insights-alerts/01-set-metric.png)

* Állítsa be az erőforrást, mielőtt a többi tulajdonság. **Válassza ki a "(összetevők)" erőforrást** Ha a teljesítmény vagy a használati metrikák állíthatók be riasztások.
* A neve, amelyet átadhat a riasztás az erőforráscsoport (ne csak az alkalmazás) belül egyedinek kell lennie.
* Ügyeljen arra, ügyeljen az egységeket, amelyekben azonosítóját, írja be a küszöbértéket.
* Ha bejelöli a jelölőnégyzetet "... E-mail tulajdonosok", riasztások által küldött e-mailek mindenki, aki hozzáfér az erőforráscsoport. Bontsa ki a személyek készletét, vegye fel őket a [erőforráscsoportba vagy előfizetésbe](app-insights-resources-roles-access-control.md) (az erőforrás nem).
* "További e-mailek" megadása esetén a rendszer küld figyelmeztetést személyek vagy csoportok (-e, be van jelölve a "e-mail-tulajdonosok..." mezőben). 
* Állítsa be a [webhook cím](../monitoring-and-diagnostics/insights-webhooks-alerts.md) Ha meg van adva egy webes alkalmazás, amely válaszol a riasztásokat. Azt nevezzük, mind a riasztás aktiválásakor, és ha meg van oldva. (De vegye figyelembe, hogy jelenleg lekérdezési paraméterek nem továbbítja a rendszer webhook tulajdonságként.)
* Bármikor letilthatja, vagy engedélyezze a figyelmeztetés: a gombok a panel tetején.

*A riasztási hozzáadása gomb nem látható.* 

* Egy szervezeti fiók használatával? Riasztások állíthatja be, ha tulajdonosa vagy közreműködője az alkalmazás erőforráshoz való hozzáférést. Tekintse meg a hozzáférés-vezérlés panelen. [További tudnivalók a hozzáférés-vezérlés][roles].

> [!NOTE]
> A riasztások panelen láthatja, hogy már van egy riasztási beállítva: [proaktív diagnosztika](app-insights-proactive-failure-diagnostics.md). Az automatikus riasztás egy adott metrika, hibaarányú figyeli. Ha úgy dönt, hogy tiltsa le a proaktív riasztás, nem kell saját riasztás beállítása sikertelen. 
> 
> 

## <a name="see-your-alerts"></a>A riasztások megtekintéséhez
Ha egy riasztás módosításokat állam e-mailt kap aktív és inaktív között. 

A riasztási szabályok panelen látható minden egyes riasztás aktuális állapota.

Nincs a riasztásokat a legutóbbi tevékenységét legördülő:

![Riasztások legördülő](./media/app-insights-alerts/010-alert-drop.png)

Állapot változásainak az előzményeit a műveletnaplóban van:

![Áttekintés paneljén kattintson a beállítások naplókat](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>A riasztások működése
* Riasztás három állapota van: "Soha nem aktivált", "Aktív" és "Feloldva." Aktivált azt jelenti, hogy a megadott feltétel volt igaz értéke esetén, ha a legutóbbi értékelésének.
* Értesítés jön létre, amikor egy riasztás állapota. (Ha a riasztási feltétel már nem igaz, a riasztás létrehozásakor, nem kaphat értesítést mindaddig, amíg a feltétel hamis kerül.)
* Minden értesítési e-mailt állít elő, ha be van jelölve az e-mailek mezőbe, vagy a megadott e-mail címet. Is megtalálhatja az értesítések legördülő listából.
* Riasztás minden alkalommal, amikor egy metrika megérkeznek, de más módon nem értékeli.
* A kiértékelés összesíti a metrika az előző adott időszakban, és összehasonlítja azt a küszöbértéket, az új állapot meghatározásához.
* Az időtartamot, ameddig úgy dönt, amelyben a időköze metrikák összesítése. Nincs hatással, milyen gyakran legyen kiértékelve a riasztás: metrikák érkezési gyakoriságát függ.
* Ha nincs adat érkezik egy adott metrika egy kis ideig, résnek rendelkezik különböző hatások riasztási kiértékelési és a diagramok metrika explorer. A metrika Intézőben adatot nem látható több mint a mintavételi időközben a diagram, ha a diagram jeleníti meg a 0 érték. De azonos metrika alapuló riasztást nem lehet reevaluated, és a riasztás állapota változatlan marad. 
  
    Végül érkező adatokat, amikor a diagram ugrik vissza egy nullától eltérő értékre. A riasztás kiértékeli a megadott időszakra vonatkozó rendelkezésre álló adatok alapján. Ha az új adatpont az időtartamon belül csak egy, az összesítés alapul, csak az, hogy az adatpontok.
* Riasztást vibrálását gyakran közötti riasztási és kifogástalan állapotok, még akkor is, ha hosszú ideig állít be. Ez akkor fordulhat elő, ha a metrika értékét körül a küszöbérték felett áll. A küszöbérték nem kapott van: riasztás átmenet értéke megegyezik az átmenet kifogástalanra, történik.

## <a name="what-are-good-alerts-to-set"></a>Mik azok a helyes riasztások beállítása
Ez az alkalmazás függ. Kezdésként érdemes nem szeretné beállítani a túl sok metrikákat. Szánjon némi időt, megnézi a metrika diagramok követve betekintést nyerhet abba a viselkedését leíró általában az alkalmazás futtatása közben. Ez az eljárás segítséget a teljesítmény javítása érdekében. Jelzi, ha a metrikák nyissa meg a normál területen kívül, majd állítsa be riasztások. 

Népszerű riasztások a következők:

* [Böngésző metrikák][client], különösen a böngésző **lapon a betöltési idők**, webes alkalmazásokhoz megfelelőek. Ha az oldala sok parancsfájlok, kell keresnie az **böngésző kivételek**. A metrikák és a riasztások eléréséhez meg kell beállítania [weblap figyelési][client].
* **Kiszolgáló válaszideje** webalkalmazások server oldalán. Riasztások beállítása, valamint nyomon követheti a Ez a metrika megjelenítéséhez, ha azt művelettől aránytalanul sok díjszabás: variation utalhat, hogy az alkalmazás fut-e elegendő erőforrással. 
* **Kivételek** – megtekintheti őket, meg kell nyitnia egy [további telepítési](app-insights-asp-net-exceptions.md).

Ne feledje, hogy [proaktív hiba sebesség diagnosztika](app-insights-proactive-failure-diagnostics.md) automatikusan figyelése, amellyel az alkalmazás válaszol-e a hibát kódok rendelkező kérelmek száma másodpercenként. 

## <a name="automation"></a>Automatizálás
* [PowerShell használatával automatizálhatja a riasztások beállítása](app-insights-powershell-alerts.md)
* [Webhook segítségével automatizálhatja a riasztásokra való reagálásról](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Lásd még:
* [A webteszt rendelkezésre állása](app-insights-monitor-web-app-availability.md)
* [Riasztások beállítása automatizálásához](app-insights-powershell-alerts.md)
* [Proaktív diagnosztika](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

