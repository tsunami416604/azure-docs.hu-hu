---
title: Intelligens detektálás – rendellenes hibák, az Application Insightsban |} A Microsoft Docs
description: Riasztást küld a sikertelen kérelmek a webalkalmazás hibaarányának szokatlan módosításokat, és diagnosztikai elemzését nyújtja. Nincsenek konfigurációs van szükség.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/09/2017
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 776c923910866b3c65271a8acdc00edc6eb6df59
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723822"
---
# <a name="smart-detection---failure-anomalies"></a>Intelligens detektálás – rendellenes hibák
[Az Application Insights](app-insights-overview.md) automatikus értesítést küld közel valós időben Ha a webalkalmazás a sikertelen kérelmek arányának rendellenes növekedése. Azt észleli, hogy egy szokatlan megnövekedhet a HTTP-kérések vagy sikertelenként jelentett függőségi hívások sebessége. A kéréseket, a sikertelen kérések esetében általában a válaszkódot 400 vagy magasabb. Könnyebben osztályozhatja és diagnosztizálhatja a problémát, jellemzőit, a hibák és a kapcsolódó telemetriai adatok elemzése az értesítés megtalálható. Emellett mutató hivatkozások találhatók az Application Insights portálon további elemzés céljából. A szolgáltatás nincs beállítás és konfiguráció, nem kell, gépi tanulási algoritmusok használatával előrejelezheti a normál hibaszázalék.

Ez a funkció működik, a Java és az ASP.NET web apps, a felhőben vagy a saját kiszolgálókon. Is működik minden olyan alkalmazás, amely létrehozza a kérés és a függőségi telemetria – például ha egy feldolgozói szerepkör, amely meghívja ezt [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) vagy [TrackDependency()](app-insights-api-custom-events-metrics.md#trackdependency).

Miután beállította [a projekt Application Insights](app-insights-overview.md), és az alkalmazása a telemetria bizonyos minimális mennyiségű hoz létre, a megadott rendellenes hibák az intelligens detektálási ismerje meg az alkalmazás normál viselkedését, még mielőtt 24 óráig tart be van kapcsolva, és riasztásokat küldhet.

Íme egy példa a riasztás.

![Minta intelligens észleléséről szóló figyelmeztetés a fürt elemzési hiba körül megjelenítése](./media/app-insights-proactive-failure-diagnostics/013.png)

> [!NOTE]
> Alapértelmezés szerint, mint ebben a példában egy rövidebb formátum levelet kap. De [váltson át a részletes formátumban](#configure-alerts).
>
>

Figyelje meg, amely megadja, hogy:

* Az os Hibaarány, szemben a alkalmazás normál viselkedését.
* Hány felhasználó érintett –, hogy tudja, milyen mértékben miatt.
* A hibák társított jellemző mintát. Az ebben a példában van egy adott válaszkód, a kérés nevét (művelet) és az Alkalmazásverzió. Amely azonnal bemutatja, hol kell elkezdeni keresi a kódban. Egyéb lehetőségek lehet az adott böngésző vagy az ügyfél operációs rendszerrel.
* A kivétel, naplókivonatok és függőségi hiba (adatbázisok vagy más külső összetevők), amely a megadott hibák társítani kell jelennek meg.
* Az Application Insights telemetria a megfelelő keresések közvetlen hivatkozást.

## <a name="benefits-of-smart-detection"></a>Intelligens detektálás előnyei
Szokásos [metrikákhoz kapcsolódó riasztások](app-insights-alerts.md) jelzi, hogy probléma lehet. De intelligens detektálás elindítja a diagnosztikai munka, nagy mennyiségű, egyébként külön kellene Ön megteheti az elemzés végrehajtásához. Az eredmények eligazíthatja csomagolva, kap, annak megakadályozása, hogy gyorsan, a probléma okát.

## <a name="how-it-works"></a>Működés
Intelligens detektálás figyeli az alkalmazásból, és különösen fogadott telemetriát a hibaarányok. Ez a szabály a kérések száma számolja, amelynek a `Successful request` tulajdonság értéke FALSE (hamis), és az függőségi hívások száma, amelynek a `Successful call` tulajdonság értéke FALSE (hamis). A kéréseket, alapértelmezés szerint `Successful request == (resultCode < 400)` (kivéve, ha egyéni kódot írt [szűrő](app-insights-api-filtering-sampling.md#filtering) vagy létrehozását a saját [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest) hívások). 

Az alkalmazás teljesítményének működés jellegzetes rendelkezik. Bizonyos kérelmek vagy függőségi hívások lesz jobban a hibák, mint a többi; és a hibák összesített száma a terhelés növekedése előfordulhat, hogy lépjen. Intelligens detektálás gépi tanulási ezek előforduló rendellenességek felderítéséhez.

Telemetria az Application Insightsba származik a webalkalmazás, mivel az intelligens detektálás jelenlegi viselkedése összehasonlítja az elmúlt néhány napban látható minta. Ha a hibák arányának rendellenes növekedése összehasonlítva korábbi teljesítményét, akkor aktiválódik, elemzését.

Elemzés akkor aktiválódik, amikor a szolgáltatás egy fürt analysis végzi, a sikertelen kérelmek használatával próbál mintát értékek írhatók le a hibák azonosításához. A fenti példában az elemzés észlelte, hogy a legtöbb hiba egy adott eredménykód, a kérés nevét, a kiszolgáló URL-címe gazdagépet és a szerepkörpéldány készül. Az elemzés ezzel szemben az észlelte, hogy az ügyfél operációs rendszer tulajdonság több érték van elosztva, és ezért nem jelenik.

A szolgáltatás a hívásokat a telemetriai adatok van kialakítva, az elemző jelenik meg a kivételek és a kapcsolódó kérések a fürtben, azonosította, és minden olyan nyomkövetési naplókat, az ezen kérésekkel kapcsolatos példát függőségi hiba.

Az eredményül kapott elemzés kap, a riasztás, kivéve, ha nem konfigurálta.

Például a [riasztások a manuálisan beállított](app-insights-alerts.md), vizsgálja meg a riasztás állapotát, és konfigurálja az Application Insights-erőforrást a riasztások panelén. Azonban más riasztások eltérően nem kell beállítása, vagy intelligens észlelés konfigurálásához. Ha azt szeretné, tiltsa le, vagy módosítsa a cél e-mail-címeket.

## <a name="configure-alerts"></a>Riasztások konfigurálása
Tiltsa le az intelligens detektálás, módosítsa az e-mail címzettjeit, hozzon létre egy webhookot vagy engedélyezve a részletesebb figyelmeztető üzeneteket.

Nyissa meg a riasztások oldaláról. Rendellenes hibák részét képezi minden riasztást, manuálisan állította, és láthatja, hogy jelenleg a riasztás állapota mellett.

![Az Áttekintés oldalon kattintson a riasztások csempén. Vagy bármely metrikák lapján kattintson a riasztások gombra.](./media/app-insights-proactive-failure-diagnostics/021.png)

Kattintson a riasztás konfigurálásához.

![Konfiguráció](./media/app-insights-proactive-failure-diagnostics/032.png)

Figyelje meg, hogy az intelligens detektálás letilthatja, de nem törölhető (vagy hozzon létre egy újat).

#### <a name="detailed-alerts"></a>Részletes értesítések
Ha a "Get részletesebb diagnosztikai" lehetőséget választja az e-mailben további diagnosztikai adatokat fogja tartalmazni. Egyes esetekben lesz csak az e-mailben az adatokból a probléma diagnosztizálása érdekében.

Annak a enyhe kockázata, hogy a részletesebb riasztás tartalmazhatnak bizalmas adatokat, mert a kivétel- és nyomkövetési üzeneteket tartalmaz. Azonban ez csak történne a kód teheti lehetővé a bizalmas adatokat, azokat az üzeneteket.

## <a name="triaging-and-diagnosing-an-alert"></a>Osztályozása és diagnosztizálása a riasztást
Riasztás azt jelzi, hogy a rendszer észlelte a meghiúsult kérelmek arányának rendellenes növekedése. Valószínű, hogy nincs-e az alkalmazás vagy a környezettel kapcsolatos problémára.

A kérelmek és az érintett felhasználók számának százalékos aránya eldöntheti, hogyan sürgős a probléma van. A fenti példában hibásodik meg 22,5 % 1 % normál arány hasonlítja össze, azt jelzi, hogy rossz hiba történik. Másrészről csak 11 felhasználót érintett. Az alkalmazás azt is, ha tudná, annak ellenőrzéséhez, hogy komoly ez.

Sok esetben lesz gyorsan a a kérés nevét, a kivétel, a megadott függőségi hiba- és nyomkövetési adatok a probléma diagnosztizálása érdekében.

Vannak bizonyos a keresőmotorok. Ha például a függőségi hibák száma ebben a példában ugyanaz, mint a kivételek sebessége (89.3 %). Ez azt sugallja, hogy a kivétel ered közvetlenül a függőséghibák – így világos képet arról, hol kell elkezdeni keresi a kódban.

További vizsgálathoz a az egyes szakaszokban hivatkozásokkal közvetlenül, egy [keresőoldalán](app-insights-diagnostic-search.md) vonatkozó kérelmek, kivételek, függőségi vagy nyomkövetések szűrve. Vagy megnyithatja a [az Azure portal](https://portal.azure.com), keresse meg az Application Insights-erőforrást az alkalmazáshoz, és a hibák panel megnyitásához.

Ebben a példában az "A függőségi hibák részleteinek megtekintése" hivatkozásra kattintva megnyílik az Application Insights keresés panelen. Azt mutatja, hogy az SQL-utasítást, amely egy példa az alapvető ok: NULL érték lett megadva a kötelező mezők, és nem felelt meg az ellenőrzés során a mentési művelet.

![Diagnosztikai keresés](./media/app-insights-proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Tekintse át a legutóbbi riasztások

Kattintson a **intelligens detektálás** a legutolsó riasztás beolvasásához:

![Riasztások szerint](./media/app-insights-proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Mi a különbség...
Rendellenes hibák az intelligens detektálási más hasonló kiegészíti az Application Insights azonban különböző funkcióit.

* [Metrikákhoz kapcsolódó riasztások](app-insights-alerts.md) -beállításokat, és figyelheti a mérőszámokat, például a CPU-os foglaltságnál tart, kérelemarányok, lapbetöltési idők és így tovább széles skáláját. Figyelmezteti, például, ha szeretné-e további erőforrások hozzáadásához használhatja őket. Ezzel szemben az intelligens észlelés a rendellenes hibák ismerteti a kritikus metrikák (jelenleg csak meghiúsult kérelmek arányának), kis számos, a közel valós idejű módon, ha a webalkalmazás nem sikerült a kérelem aránya nő, a web app képest jelentősen értesítése Normál viselkedése.

    Intelligens észlelés automatikusan igazodik az uralkodó feltételekre válaszul küszöböt.

    Intelligens detektálás elindítja a diagnosztikai megfelelő az Ön számára.
* [Intelligens detektálás a rendellenes teljesítményről](app-insights-proactive-performance-diagnostics.md) is a használja gépi intelligencia a metrikákat a szokatlan minták felderítését, és Ön konfiguráció nélkül nem szükséges. De eltérően az intelligens rendellenes hibák észlelése, a rendellenes teljesítményről intelligens detektálás célja a használat gyűjtőcső, előfordulhat, hogy a program rosszul kiszolgálása az internetszolgáltatójuk – például az oldalakat a szegmensek megtalálja a böngészőben egy adott típusú. Az elemzés naponta történik, és ha minden eredmény található, valószínű, hogy sokkal kevésbé sürgős, mint a riasztást. Ezzel szemben az elemzést, a rendellenes hibák folyamatosan történik a bejövő telemetriát, és értesíteni fogjuk percen belül a vártnál nagyobb kiszolgáló hibaarányok esetén.

## <a name="if-you-receive-a-smart-detection-alert"></a>Ha egy intelligens detektálási riasztás
*Miért kapott ezt a riasztást?*

* Azt észleltük, hogy a megelőző időszak normál alapkonfigurációhoz viszonyított sikertelen kérelmek arányának rendellenes növekedése. A hibák és a kapcsolódó telemetria az elemzést úgy tűnik, hogy probléma merül fel, hogy be kell keresnie.

*Az értesítés jelenti egyértelműen fennáll a probléma?*

* Megpróbáljuk riasztás alkalmazás megszakítása vagy teljesítményromlását tapasztalja, de csak is megértette a szemantika és a az alkalmazás vagy a felhasználók gyakorolt hatást.

*Tehát fedjen fel meg, így az adataimat?*

* Nem. A szolgáltatás nem teljesen automatikus. Csak az értesítéseket kap. Az adatok [privát](app-insights-data-retention-privacy.md).

*Ez a riasztás feliratkozás kell?*

* Nem. Minden alkalmazáshoz, hogy a küld telemetriai kérelem az intelligens detektálási riasztási szabály tartozik.

*Előfizetés lemondása vagy a saját munkatársak inkább küldött értesítések?*

* Igen, a riasztási szabályok, kattintson az intelligens detektálási szabályra konfigurálásához. A riasztás letiltása, vagy módosítható a címzettek a riasztás.

*Megszakadt az e-mailt. Hol található az értesítéseket a portálon?*

* A Tevékenységnaplókban. Az Azure-ban nyissa meg az Application Insights-erőforrást az alkalmazáshoz, majd válassza a vizsgálati naplók.

*A riasztások néhány ismert problémákról, és nem kívánom a fogadásukra.*

* A várakozó fájlok számát a riasztás letiltása van.

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítséget nyújt az alkalmazásából származó telemetriai adatok vizsgálata:

* [Metrika explorer](app-insights-metrics-explorer.md)
* [A keresési ablak](app-insights-diagnostic-search.md)
* [Analytics – erőteljes lekérdezési nyelv](../log-analytics/query-language/get-started-analytics-portal.md)

Az intelligens észlelés teljesen automatikus. De esetleg szeretné néhány további riasztásokat állíthat be?

* [Manuálisan konfigurált metrikákhoz kapcsolódó riasztások](app-insights-alerts.md)
* [Rendelkezésre állási webes tesztek](app-insights-monitor-web-app-availability.md)
