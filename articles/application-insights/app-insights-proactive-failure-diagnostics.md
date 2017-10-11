---
title: "Észlelési - hiba rendellenességek észlelését, és az Application Insightsban intelligens |} Microsoft Docs"
description: "Riasztást küld, azt a webalkalmazást a sikertelen kérelmek számát szokatlan változásairól, és diagnosztikai elemzés biztosít. Nincs a konfigurációra nincs szükség."
services: application-insights
documentationcenter: 
author: yorac
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: bwren
ms.openlocfilehash: e82d35459110e122ec8438b406a52df61922b0fc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="smart-detection---failure-anomalies"></a>Intelligens észlelési - hiba rendellenességek észlelését
[Az Application Insights](app-insights-overview.md) automatikusan értesíti a felhasználót közel valós idejű Ha a webalkalmazás szokatlan mértékben megnőtt a sikertelen kérelmek számát. Azt észleli, hogy egy szokatlan megnövekedhet a HTTP-kérelmek vagy sikertelenként jelentett függőségi hívások gyakorisága. A kéréseket a sikertelen kérelmek rendszerint rendelkező válaszkódot 400 vagy magasabb. Segítséget osztályozhatja és diagnosztizálhatja a problémát, a hibák és a kapcsolódó telemetriai adatok elemzését az értesítés valósul meg. Az Application Insights portáljáról további elemzés céljából mutató hivatkozásokat is vannak. A szolgáltatás nem beállításról, sem a konfigurációban kell, mert gépi tanulási algoritmusok a normál hibaaránya előre jelezni.

Ez a szolgáltatás működik, Java és az ASP.NET web Apps, a felhőben, vagy az Ön saját kiszolgálóin. Azt is működik, bármely alkalmazás, amely hoz létre a kérelem vagy függőségi telemetria - például, ha a feldolgozói szerepkör, amely behívja [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) vagy [TrackDependency()](app-insights-api-custom-events-metrics.md#trackdependency).

Beállítása után [a projekthez az Application Insights](app-insights-overview.md), és az alkalmazás egyes minimális telemetriai adatokat állít elő, ha intelligens tegye a hiba rendellenességek észlelését tart, az alkalmazás normál viselkedése további előtt be kell kapcsolni, és küldhet riasztások 24 óra.

Íme egy minta riasztás.

![A minta intelligens észleléséről szóló figyelmeztetés a fürt elemzési hiba körül megjelenítő](./media/app-insights-proactive-failure-diagnostics/013.png)

> [!NOTE]
> Alapértelmezés szerint egy rövidebb, mint ebben a példában formátum mail kap. Azonban úgy is [részletes formátuma kapcsoló](#configure-alerts).
>
>

Figyelje meg, amely jelzi, hogy:

* A hibaaránya normál alkalmazása működését képest.
* Érintett felhasználók számát – így megtudhatja, mennyire foglalkoznia.
* A hibák társított jellemző mintát. Ebben a példában van egy adott válaszkód, kérés neve (művelet) és az alkalmazás verziója. Amely azonnal megtudhatja, hol keressen a kódban. Egyéb lehetőségek egy megadott böngésző vagy az ügyfél operációs rendszer lehet.
* A kivétel naplókivonatokat és függőségi hiba (adatbázisok vagy más külső összetevő), amely a megadott hibák társítandó jelennek meg.
* Az Application Insights telemetria vonatkozó keresés közvetlen hivatkozást.

## <a name="benefits-of-smart-detection"></a>Intelligens észlelési előnyei
A szokványos [metrika riasztások](app-insights-alerts.md) jelzi, hogy probléma lehet. De intelligens észlelési elindítja a diagnosztikai megfelelőek Önnek az elemzés, amelyeket egyébként külön saját kezűleg elvégzendő számos végrehajtása. A eredményt el szépen csomagolása, segít a probléma a legfelső szintű gyors eléréséhez.

## <a name="how-it-works"></a>Működés
Intelligens észlelési figyeli a telemetriai adatokat kapott az alkalmazásból, és különösen a sikertelen sebességet. Ez a szabály megjeleníti az kérések számát, amelynek a `Successful request` tulajdonság értéke HAMIS, és amelynek hívások függőségi száma a `Successful call` tulajdonság értéke "false". A kéréseket, alapértelmezés szerint `Successful request == (resultCode < 400)` (kivéve, ha egyéni kód írt [szűrő](app-insights-api-filtering-sampling.md#filtering) , vagy a saját [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest) hívások). 

Az alkalmazás teljesítményének rendelkezik egy tipikus mintája működését. Néhány kérelmek vagy függőségi hívások esetében lesz gyakrabban hiba esetén, mint a többire; és a teljes hibaaránya lépjen a terhelés növekedése. Intelligens észlelési gépi tanulási ezek rendellenességeket kereséséhez használja.

Telemetriai adatok az Application Insightsban való tartalmazza a webalkalmazás, intelligens észlelési jelenlegi viselkedése összehasonlítja a minta az elmúlt néhány napban látható. Ha egy szokatlan mértékben megnőtt a sikertelen korábbi teljesítmény összehasonlítva, elemzés működésbe lép.

Elemzés kiváltásakor a szolgáltatás egy fürt analysis végzi, a sikertelen kérelmek, próbálkozzon egy mintát, amely jellemző a hibák azonosításához. A fenti példában az elemzés észlelte, hogy a legtöbb hibák készül-e egy adott eredménykódja, a kérelem neve, a kiszolgáló URL-címe gazdagépet és a szerepkör példánya. Ezzel szemben az elemzés észlelte, hogy az ügyfél operációs rendszer tulajdonsághoz több érték van elosztva, és ezért nem szerepel.

Ha a szolgáltatás a telemetriai adatok hívások van tagolva, a a elemző megkeresi kivételt, és az észlelt, és minden nyomkövetési napló társított ezeket a kérelmeket, például a fürt kérelmek társított függőségi hiba.

Az eredményül kapott elemzés küldött riasztás esetében, kivéve, ha nem konfigurálta.

Például a [riasztást manuálisan állítsa](app-insights-alerts.md), vizsgálja meg a riasztás állapota, és konfigurálja az Application Insights-erőforrás a riasztások panelen. De egyéb értesítések eltérően nem kell beállítása, vagy intelligens észlelés konfigurálásához. Ha azt szeretné, tiltsa le, vagy módosítsa a cél e-mail címe.

## <a name="configure-alerts"></a>Riasztások konfigurálása
Intelligens észlelési letiltása, az e-mailek címzettjeinek módosítása, a webhook létrehozása vagy engedélyezve a részletesebb figyelmeztető üzenetek.

Nyissa meg a riasztások lapon. Hiba rendellenességeket megtalálható együtt minden riasztást, manuálisan van beállítva, és láthatja, hogy jelenleg a riasztás állapota.

![A áttekintése lapon kattintson a riasztások csempén. Vagy bármely metrikák lapján kattintson a riasztások gombra.](./media/app-insights-proactive-failure-diagnostics/021.png)

Kattintson a riasztásra, konfigurálja.

![Konfiguráció](./media/app-insights-proactive-failure-diagnostics/032.png)

Figyelje meg, hogy az intelligens észlelési letilthatja, de nem törli (vagy hozzon létre egy újat).

#### <a name="detailed-alerts"></a>Részletes riasztások
Ha bejelöli a "Get részletesebb diagnosztikai" az e-mailt további diagnosztikai adatokat fogja tartalmazni. Egyes esetekben képes lesz csak az e-mailben az adatokból a probléma diagnosztizálása érdekében.

Fennáll enyhe, hogy a részletesebb riasztás tartalmazhatnak bizalmas adatokat, mert a kivétel- és nyomkövetési üzeneteket tartalmaz. Azonban ez csak történne a kód lehetővé teheti a bizalmas információk be azokat az üzeneteket.

## <a name="triaging-and-diagnosing-an-alert"></a>Triaging és figyelmeztetés diagnosztizálásával
Egy riasztás azt jelzi, hogy szokatlan mértékben megnőtt a sikertelen kérelmek aránya az észlelt. Valószínű, hogy nincs-e az alkalmazás vagy a környezet kapcsolatos problémára.

Abból a kérelmek és az érintett felhasználók számát mutatja eldöntheti, hogyan sürgős a probléma van. A fenti példában 22,5 % sikertelenségének arányát 1 % a normál értéket összehasonlítja, azt jelzi, hogy valami rossz van folyamatban. Másrészről csak 11 felhasználók is hatással volt. Amennyiben az alkalmazást, akkor tudná annak ellenőrzéséhez, hogy ez.

Sok esetben lesz gyorsan a a kérés nevét, a kivétel megadott függőségi hiba és nyomkövetési adatok a probléma diagnosztizálása érdekében.

Léteznek bizonyos más keresik. Például ebben a példában a függőségi hibaaránya megegyezik a kivétel gyakorisága (89.3 %). Ez azt sugallja, hogy a kivétel ered közvetlenül a függőségi hiba - felkínálva egy tiszta meghatározni, hogy hol keressen a kódban.

Vizsgálja meg a további, az egyes szakaszokban szereplő hivatkozásokkal azonnal egy [keresőoldalt](app-insights-diagnostic-search.md) a vonatkozó kérések, a kivételt, a függőségekkel vagy a nyomkövetési adatokat szűrve. Vagy megnyithatja a [Azure-portálon](https://portal.azure.com), keresse meg az Application Insights-erőforrást az alkalmazáshoz, és a hibák panel megnyitásához.

Ebben a példában az "Függőség hibák részleteinek megtekintése" hivatkozásra kattintva megnyílik az Application Insights search paneljét. Azt mutatja, hogy az SQL-utasítást, amely rendelkezik az alapvető ok példát: NULL érték lett megadva a kötelező mezőket, és nem felelt meg a mentés során érvényesítési műveletet.

![Diagnosztikai keresés](./media/app-insights-proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Tekintse át a legújabb riasztások

Kattintson a **intelligens észlelési** a legutóbbi riasztás eléréséhez:

![Riasztások szerint](./media/app-insights-proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Mi a különbség a...
Intelligens tegye a hiba rendellenességek észlelését más hasonló kiegészíti az Application Insights de különböző funkcióit.

* [Metrika riasztások](app-insights-alerts.md) -beállításokat, és figyelheti azokat a metrikák például CPU Foglaltság kérelem díjszabás, lapbetöltési idők vagy stb. Figyelmezteti, például, ha szeretné-e rendeljen több erőforrást használhatja őket. Intelligens tegye a hiba rendellenességek észlelését ellentétben kritikus metrikák (jelenleg csak a sikertelen kérelmek aránya), kis számos értesítése közel valós idejű módon, ha a webalkalmazás nem felelt meg a kérelmek aránya növeli a webes alkalmazás ezek normál viselkedése képest jelentősen ismerteti.

    Intelligens észlelési automatikusan beállítja az adott válasz érvényes feltételek küszöböt.

    Intelligens észlelési elindítja a diagnosztikai megfelelőek Önnek.
* [A teljesítményanomáliákat észlelése intelligens](app-insights-proactive-performance-diagnostics.md) is által használt számítógép felderítése szokatlan minták a metrikákat, az eszközintelligencia, és Ön beállításokra nincs is szükség. De intelligens tegye a hiba rendellenességek észlelését, ellentétben a teljesítményanomáliákat intelligens kimutatása célja, hogy a használati gyűjtőcső, előfordulhat, hogy rosszul szolgáltatott – például adott lap szegmensek található böngésző adott típusú. Az elemzés naponta történik, és ha eredményt, valószínűleg kevesebb sokkal sürgetőbb, mint a riasztást. Ezzel szemben az elemzést, a hiba rendellenességeket a bejövő telemetria folyamatosan történik, és értesítést fog kapni percen belül a vártnál nagyobb server hiba sebesség esetén.

## <a name="if-you-receive-a-smart-detection-alert"></a>Ha egy intelligens észleléséről szóló figyelmeztetés jelenik meg
*Miért megkapta a riasztást?*

* Szokatlan mértékben megnőtt a sikertelen kérelmek aránya az előző időszak normál alaptervhez képest észleltük. A hibák és a kapcsolódó telemetriai elemzést követően úgy tűnik, hogy probléma merül fel, hogy be kell keresnie.

*Az értesítés jelent problémát mindenképpen rendelkezik?*

* A Microsoft app megszakítása vagy teljesítménycsökkenése riasztás próbálkoznak, hanem csak is teljes mértékben tisztában a szemantikáját, és az alkalmazás vagy a felhasználók gyakorolt hatás.

*Igen guys megnézzük adataimat?*

* Nem. A szolgáltatás nem teljesen automatikus. Csak az értesítéseket kap. Az adatok [titkos](app-insights-data-retention-privacy.md).

*Ezt a riasztást fizessen elő kell?*

* Nem. Minden, hogy a küld telemetriai kérelem alkalmazásnak az intelligens észlelési riasztási szabály.

*Leiratkozhat vagy a saját munkatársak helyette küldött értesítéseket?*

* Igen, a riasztási szabályok, kattintson az intelligens észlelési szabály konfigurálását. A riasztás letiltása, vagy módosítsa az értesítés címzettjeit.

*Az e-mailt elvész. Hol található az értesítéseket a portálon?*

* A tevékenység naplózva. Az Azure nyissa meg az Application Insights-erőforrást az alkalmazáshoz, majd válassza a tevékenységi naplóit.

*A riasztások vannak ismert problémákról, és nem kívánom a fogadásukra.*

* A várakozó fájlok számát a riasztás letiltását van.

## <a name="next-steps"></a>Következő lépések
A diagnosztikai eszközök segítségével vizsgálja meg az alkalmazás a telemetriai adatok:

* [Metrika explorer](app-insights-metrics-explorer.md)
* [Keresési ablak](app-insights-diagnostic-search.md)
* [Elemzés - hatékony lekérdezési nyelv](app-insights-analytics-tour.md)

Intelligens észlelések rendszer teljesen automatikus. De lehet, hogy milyen néhány további riasztások beállítása?

* [Manuálisan konfigurált metrika riasztások](app-insights-alerts.md)
* [A webteszt rendelkezésre állása](app-insights-monitor-web-app-availability.md)
