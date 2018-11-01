---
title: Intelligens detektálás – teljesítménnyel kapcsolatos anomáliák |} A Microsoft Docs
description: Application Insights hajt végre az alkalmazás telemetriai adatainak az intelligens elemzésének, és figyelmezteti a potenciális problémákról. Ez a funkció a telepítés nem kell.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: b0256e899d47e3b6f8141218c11e1a7a62ca8e1a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419724"
---
# <a name="smart-detection---performance-anomalies"></a>Intelligens detektálás – teljesítménnyel kapcsolatos anomáliák

[Az Application Insights](app-insights-overview.md) automatikusan elemzi a webalkalmazás teljesítményét, és figyelmezteti azokról a potenciális problémákról. Akkor lehet, hogy lehet olvassa, mert az intelligens detektálási értesítést kapott.

Ez a funkció nem speciális beállítás, az alkalmazás konfigurálása az Application Insights nem szükséges (a [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), vagy [Node.js](app-insights-nodejs.md), majd a [weblap kód](app-insights-javascript.md)). Ez akkor aktív, ha az alkalmazása elég telemetriát hoz létre.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Ha szeretne egy intelligens detektálási értesítést kaphat?

Az Application Insights azt észlelte, hogy az alkalmazás teljesítményének segít a következő módszerek egyikével:

* **Válaszidő romlása** – az alkalmazás elindult, mint amennyi lassabban válaszol a kérelmekre. A módosítás lehet, hogy törölték, például gyors, mert hiba történt a legutóbbi telepítés regresszió. Vagy megváltoztak, fokozatos, talán memóriavesztés által okozott. 
* **A függőségi időtartam teljesítménycsökkenése** – az alkalmazás egy REST API-t, adatbázis vagy más függőségi hívást hajt végre. A függőség lassabban, mint amennyi válaszol.
* **Lassú teljesítmény mintája** – az alkalmazás úgy tűnik, hogy rendelkezik egy teljesítményprobléma, amely csak bizonyos kérelmek van hatással. Például lapok töltődnek lassabban egyfajta, mint a többi; böngészővel vagy a kérések kiszolgált lassabban egy adott kiszolgálóról. Jelenleg az algoritmusok lapbetöltési idők, a kérelem válaszidők és a válaszidők függőségi meg.  

Intelligens detektálás legalább 8 nap, egy közös üzemeltetése kötet telemetriai igényel a normál teljesítmény az alapértékeket. Tehát után az alkalmazás adott ideig futott, bármely jelentős probléma eredményez értesítést.


## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképp rendelkezik a probléma?

Nem, egy értesítés nem jelenti azt, hogy az alkalmazás mindenképp van probléma. Egyszerűen egy javaslatot vonatkozó további szorosan nézze érdemes.

## <a name="how-do-i-fix-it"></a>Hogyan javíthatom?

Az értesítések közé tartoznak a diagnosztikai adatokat. Például:


![Íme egy példa kiszolgálói válaszidő romlása észlelés](media/app-insights-proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Osztályozási**. Az értesítés megtudhatja, hány felhasználóra vagy hány műveletek érintettek. Ez segíthet a probléma prioritást rendelhet.
2. **Hatókör**. A probléma érintő minden forgalmat, vagy csak egyes oldalak? Ez korlátozódik bizonyos böngészők vagy helyek? Ez az információ szerezhető az értesítést.
3. **Diagnosztika**. Gyakran előfordul a diagnosztikai adatokat értesítési javaslatot tesz a probléma jellegétől. Például ha válaszidő lelassul, ha a kérések aránya túl magas, amely ajánl fel a kiszolgáló vagy a függőségek túlterhelt. 

    Ellenkező esetben a teljesítmény panel megnyitása az Application Insightsban. Itt megtalálja [Profiler](app-insights-profiler.md) adatokat. Ha a kivételek, Ön is kipróbálhatja a [pillanatkép-hibakereső](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>E-mail értesítések konfigurálása

Az intelligens észlelés értesítések alapértelmezés szerint engedélyezve van, és azok, akik küldött [tulajdonosoknak, közreműködőknek és olvasóknak a hozzáférést az Application Insights-erőforrást](app-insights-resources-roles-access-control.md). Ez módosításához kattintson a **konfigurálása** az e-mail-értesítés vagy az Application Insights intelligens detektálási beállítások megnyitása. 
  
  ![Intelligens detektálási beállítások](media/app-insights-proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Használhatja a **leiratkozás** hivatkozás az intelligens detektálási e-mailben az e-mail-értesítéseket kapni.

Az intelligens észlelés teljesítményanomáliákat kapcsolatos e-mailek korlátozva, egy e-mailben naponta egy Application Insights-erőforrást. Az e-mailt küld, csak akkor, ha van legalább egy új problémát észlelt az adott napon. Nem jelenik meg semmilyen üzenet ismétlődik. 

## <a name="faq"></a>GYIK

* *Tehát Microsoft személyzete tekintse meg az adataimat?*
  * Nem. A szolgáltatás nem teljesen automatikus. Csak az értesítéseket kap. Az adatok [privát](app-insights-data-retention-privacy.md).
* *Application Insights által gyűjtött összes adat elemzése?*
  * Nem, jelenleg. Jelenleg a válaszidő, függőségi válaszidő és oldalbetöltési idő kérelem elemezzük. További metrikák elemzése még megvalósítás Reméljük be van kapcsolva.

* Milyen típusú alkalmazás ez működik?
  * Ezen romlását bármely olyan alkalmazásban, amely létrehozza a megfelelő telemetriai adatokat észlelt. Ha telepítette az Application Insights a webalkalmazásban, majd és -függőségek automatikusan nyomon követi. Azonban a Háttérszolgáltatásokhoz vagy más alkalmazásokat, ha hívásainak beszúrt [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) vagy [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), majd az intelligens detektálás ugyanúgy fog működni.

* *Hozzon létre saját anomáliadetektálási észlelési szabályok vagy testre szabhatja a meglévő szabályokat?*

  * Még nincs de:
    * [Riasztásokat állíthat be](app-insights-alerts.md) , mondja el, ha egy metrika átlépi a küszöbértéket.
    * [Telemetria exportálása](app-insights-export-telemetry.md) , egy [adatbázis](app-insights-code-sample-export-sql-stream-analytics.md) vagy [Power bi-bA](app-insights-export-power-bi.md), ahol elemezheti, saját magának.
* *Milyen gyakran történik az elemzés?*

  * Mi az elemzés futtatása naponta az előző nap (teljes napja UTC időzónában) telemetriai adatok.
* *A csere együtt növekszik [metrikákhoz kapcsolódó riasztások](app-insights-alerts.md)?*
  * Nem.  Nem tudjuk véglegesítse minden, érdemes lehet rendellenes viselkedésének észlelésekor.


* *Ha nem tesz semmit, válaszként egy értesítés, fog kapok emlékeztető?*
  * Nem, akkor szóló üzenetet kap egyes problémák csak egyszer. Ha a probléma nem szűnik meg, a hírcsatorna-panelen az intelligens detektálási frissülnek.
* *Megszakadt az e-mailt. Hol található az értesítéseket a portálon?*
  * Az Application Insights áttekintése az alkalmazást, kattintson a **intelligens detektálás** csempére. Akkor fog futni, minden értesítés talált mentése biztonsági 90 nap.

## <a name="how-can-i-improve-performance"></a>Hogyan javíthatja a teljesítményt?
Sikertelen és a lassú válaszai közé tartoznak a legnagyobb frustrations webhely számára, mint már tudja, a saját környezetből. Ezért fontos problémák megoldása érdekében.

### <a name="triage"></a>Osztályozási
Első lépésként jelentősége? Ha egy lap mindig betöltődni, de a webhely felhasználóinak csak 1 %-át minden eddiginél kell nézni, talán rendelkezik állításoknak több fontos dolgot. Másrészről Ha csak 1 %-a felhasználó megnyithatja, de minden alkalommal jelez kivételt, amely lehet érdemes megvizsgálni.

Általános útmutató a hatás utasítás (érintett felhasználók vagy %-a forgalmat) használata, de vegye figyelembe, hogy nincs-e a teljes képet. Gyűjtse össze az egyéb igazolást megerősítéséhez.

Fontolja meg a probléma a paramétereket. Ha földrajzi böngészőfüggő, állítsa be [rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md) többek között az adott régióban: egyszerűen van hálózati probléma az adott területre.

### <a name="diagnose-slow-page-loads"></a>Lassú lapbetöltés diagnosztizálása
Hol található a probléma? Lassan válaszol-e a kiszolgáló, az oldal nagyon hosszú, vagy nem a böngészőben kell elvégezni, a megjelenítéséhez számos tennivalónk?

A böngészők metrika panel megnyitásához. A böngésző oldal betöltési idő mutatja az idő hol fog szegmentált megjelenítését. 

* Ha **küldése kérelem időtartama** van magas, vagy a kiszolgáló válaszol lassan, vagy a kérelem egy bejegyzést a nagy mennyiségű adatot. Tekintse meg a [teljesítmény-mérőszámok](app-insights-web-monitor-performance.md#metrics) válaszidők vizsgálatára.
* Állítsa be a [függőségi nyomkövetés](app-insights-asp-net-dependencies.md) , hogy a lassúsága miatt külső szolgáltatásokkal vagy az adatbázis.
* Ha **válasz fogadása** domináns, az oldal és a függő részek – JavaScript, CSS, és így tovább (de aszinkron módon betöltött adatok) lemezképek hosszúak. Állítsa be egy [rendelkezésre állási teszt](app-insights-monitor-web-app-availability.md), és ügyeljen arra, hogy a függő részek betölteni a beállítást. Amikor eredmények beszerzése, nyissa meg az eredményt a részletek, és bontsa ki a betöltési időt, a különböző fájlok megtekintéséhez.
* Magas **ügyfél feldolgozási ideje** lassan futnak a parancsfájlok javasol. Ha az OK nem egyértelmű, érdemes lehet hozzáadni egy időzítési kódrészletet, és trackMetric hívások küldése az időpontokat.

### <a name="improve-slow-pages"></a>Lassú lapok javítása
Nincs tanácsadás javítása a kiszolgáló válasza és lapbetöltési idők, így azt nem próbál ismételje meg az összes itt a teljes webes. Íme néhány tipp, amely valószínűleg már ismert tudnivalókat, csak az első felhőmegoldásokat:

* Lassú betöltése big Data típusú fájlok miatt: a parancsfájlok és más aszinkron módon tölthető be. Használja a parancsfájl a kötegelés. A főoldalon felosztása widgetet, külön-külön betölteni az adatokat. Ne küldjön egyszerű régi HTML-táblázatok hosszú: parancsfájl használata az adatok kérés JSON vagy más kompakt formátumban, majd töltse ki a tábla helyen. Számos nagyszerű keretrendszerek mindez segítséget. (Is járnak, big Data típusú parancsfájlok természetesen.)
* Kiszolgálóoldali függőségek lassú: fontolja meg a földrajzi helyeket az összetevők. Például az Azure használata esetén ellenőrizze, hogy a webalkalmazás-kiszolgáló és az adatbázis-e ugyanabban a régióban. Tegye lekérdezések lekéréséhez szükséges több információt? Gyorsítótárazás vagy súgó kötegelés lenne?
* A kapacitás problémák: tekintse meg a kiszolgáló metrikáinak, válaszidők és a kérelmek számát. A kérések számát csúcsok aránytalanul kiugró válaszidők, valószínű, hogy a kiszolgálók úgy módosítja a program.


## <a name="server-response-time-degradation"></a>Kiszolgálói válaszidő romlása

A válasz ideje teljesítménycsökkenés értesítés jelzi, hogy:

* A válaszidő, ehhez a művelethez a szokásos válaszidő képest.
* Hány felhasználó érintett.
* Átlagos válaszidő, és ezt a műveletet a nap az észlelési és előtt 7 nappal 90 PERCENTILIS válaszideje. 
* Ez az az észlelési és 7 nap előtt művelet kérelmek száma.
* Ez a művelet csökkenése és a kapcsolódó függőségek romlását korrelációját. 
* Hivatkozások segítenek diagnosztizálni a problémát.
  * Profiler-nyomkövetések megjelenítéséhez, ahol a művelet időt vesz igénybe (a hivatkozás áll rendelkezésre, ha ehhez a művelethez az észlelési időszak során összegyűjtött Profiler nyomkövetést példák). 
  * Metrikaböngésző, ahol Ön is részletekbe menően vizsgálhatja az tartomány/Időszűrők ehhez a művelethez a jelentések teljesítményét.
  * Keresse meg a hívás meghatározott hívás tulajdonságainak megtekintése.
  * A jelentések hiba – Ha a count > 1 Ez azt jelenti, hogy hiba történt a művelet, amely a teljesítménycsökkenés lehet, hogy hozzájárult a.

## <a name="dependency-duration-degradation"></a>A függőségi időtartam teljesítménycsökkenése

A modern alkalmazásnak a mikroszolgáltatásokhoz tervezési megközelítés, ami számos esetben nehéz megbízhatóság a külső szolgáltatások egyre több fogad el. Például ha az alkalmazás támaszkodik bizonyos adatplatform vagy még akkor is, ha létre saját bot lesz valószínűleg továbbítási néhány a cognitive services-szolgáltató engedélyezése interaktív módon a robotok és néhány data store szolgáltatás felhasználóról lekérni a válaszok robot m.  

A példában a függőségi teljesítménycsökkenés értesítés:

![Íme egy példa a függőségi időtartam teljesítménycsökkenése észlelés](media/app-insights-proactive-performance-diagnostics/dependency_duration_degradation.png)

Figyelje meg, amely megadja, hogy:

* A szokásos válaszidő ehhez a művelethez képest időtartama
* Hány felhasználó érintett
* Átlagos és az észlelési és előtt 7 nappal a Pro tuto závislost 90 PERCENTILIS időtartama
* Az észlelési és előtt 7 nappal a hívások függőségi száma
* Hivatkozások segítenek a probléma diagnosztizálása
  * Teljesítményjelentések készítésére a Metrikaböngésző Pro tuto závislost
  * Keresse meg a függőségi hívás hívások tulajdonságainak megtekintése
  * Hiba jelentések – Ha a count > 1 Ez azt jelenti, amely volt sikertelen függőségi időtartam teljesítménycsökkenése előfordulhat, hogy hozzájárult az észlelési időszakban. 
  * Nyissa meg a elemzési lekérdezéseket, amelyek alapján számítja ki a függőségi időtartam és a száma  

## <a name="smart-detection-of-slow-performing-patterns"></a>Intelligens detektálás lassú végrehajtása minták 

Az Application Insights megkeresése, amelyek csak érintik bizonyos része a felhasználók számára, vagy csak az egyes esetekben felhasználókat érintő teljesítménybeli problémák. Például oldalak terhelési értesítési egyfajta böngésző, mint a böngészők, más típusú lassabb lesz-e, vagy ha kérelmek egy adott kiszolgálóról lassabban fájlnévkiterjesztései. Azt is felfedezhetik tulajdonságok kombinációja kapcsolódó problémák, például a lassú oldal megfelelően töltődik be egy földrajzi területen adott operációs rendszert használó ügyfelek számára.  

Az alábbiakhoz hasonló rendellenességek észlelése az adatok vizsgálatával csak nagyon nehéz, de gyakoribbak, mint gondolná. Gyakran azok csak surface, amikor az ügyfelek panaszok. Adott időpontig, s késő: az érintett felhasználók már vált át a versenytársak!

Jelenleg az algoritmusok lapbetöltési idők, a kérelem válaszidejének a kiszolgálón és a válaszidők függőségi meg.  

Nem kell semmilyen küszöbérték beállítása, vagy a szabályok konfigurálása. Machine learning és az adatbányászati algoritmusokat rendellenes minták észlelésére szolgálnak.

![Az e-mail riasztást kattintson a hivatkozásra kattintva nyissa meg a diagnosztikai jelentést az Azure-ban](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Amikor** mutat be a rendszer észlelte a problémát idő.
* **Milyen** ismerteti:

  * A problémát észlelt;
  * Találtunk eseménycsoportot jellemzőit jelenik meg a probléma viselkedését.
* A táblázat összehasonlítja a gyengén teljesítő beállítása minden más esemény átlagos működése.

Kattintson a hivatkozásokra kattintva nyissa meg a Metrikaböngésző, és keresse a megfelelő jelentésekben, idő-és a lassú végrehajtása-készlet tulajdonságainak szűrve.

Időtartomány és szűrőket, Fedezze fel a telemetriai adatok módosításához.

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítséget nyújt az alkalmazásából származó telemetriai adatok vizsgálata:

* [Profilkészítő](app-insights-profiler.md) 
* [Pillanatkép-hibakereső](app-insights-snapshot-debugger.md)
* [Elemzés](app-insights-analytics-tour.md)
* [Intelligens diagnosztika Analytics](app-insights-analytics-diagnostics.md)

Az intelligens észlelés teljesen automatikus. De esetleg szeretné néhány további riasztásokat állíthat be?

* [Manuálisan konfigurált metrikákhoz kapcsolódó riasztások](app-insights-alerts.md)
* [Rendelkezésre állási webes tesztek](app-insights-monitor-web-app-availability.md)
