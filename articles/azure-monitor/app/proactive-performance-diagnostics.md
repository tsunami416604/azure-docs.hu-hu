---
title: Intelligens detektálás - teljesítményanomáliák | Microsoft dokumentumok
description: Az Application Insights intelligens elemzést végez az alkalmazás telemetriai adatairól, és figyelmeztet a lehetséges problémákra. Ennek a funkciónak nincs szüksége beállításra.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 6c5b19c7e03993ef973cd708ed7a6fe89feb01a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687696"
---
# <a name="smart-detection---performance-anomalies"></a>Intelligens észlelés – teljesítményanomáliák

[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) automatikusan elemzi a webalkalmazás teljesítményét, és figyelmezteti a lehetséges problémákra. Lehet, hogy ezt olvasod, mert megkaptad az egyik intelligens észlelési értesítésünket.

Ez a funkció nem igényel speciális beállítást, kivéve az alkalmazás konfigurálását az Application Insights hoz [(ASP.NET,](../../azure-monitor/app/asp-net.md) [Java](../../azure-monitor/app/java-get-started.md)vagy [Node.js](../../azure-monitor/app/nodejs.md), és [a weboldal kódjában).](../../azure-monitor/app/javascript.md) Akkor aktív, ha az alkalmazás elegendő telemetriai adatokat hoz létre.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Mikor kapok intelligens észlelési értesítést?

Az Application Insights észlelte, hogy az alkalmazás teljesítménye az alábbi módok egyikével csökkent:

* **Válaszidő romlása** – Az alkalmazás a korábnál lassabban válaszolt a kérésekre. A változás lehet, hogy gyors volt, például azért, mert regresszió volt a legutóbbi központi telepítésben. Vagy lehet, hogy fokozatos volt, talán egy memóriavesztés okozta. 
* **Függőségi időtartam romlása** – Az alkalmazás hívásokat kezdeményez egy REST API-t, adatbázist vagy más függőséget. A függőség lassabban reagál, mint korábban.
* **Lassú teljesítményminta** – Úgy tűnik, hogy az alkalmazás teljesítményproblémája csak bizonyos kéréseket érint. Az oldalak például lassabban töltődnek be az egyik típusú böngészőre, mint mások; vagy a kéréseket lassabban szolgálják ki egy adott kiszolgálóról. Algoritmusaink jelenleg az oldal betöltési idejét, a válaszadási időket és a függőségi válaszidőket vizsgálják.  

Intelligens észlelési igényel legalább 8 nap telemetriai egy működőképes köteten a normál teljesítmény alapkonfigurációjának megállapításához. Így, miután az alkalmazás már fut az adott időszakra, minden jelentős probléma eredményez értesítést.


## <a name="does-my-app-definitely-have-a-problem"></a>Biztos, hogy az alkalmazásomnak van problémája?

Nem, az értesítés nem jelenti azt, hogy az alkalmazásnak feltétlenül problémája van. Ezek csupán javaslatok, hogy az adott anomáliát érdemes részletesebben megvizsgálni.

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?

Az értesítések diagnosztikai információkat is tartalmaznak. Például:


![Íme egy példa a kiszolgálói válaszidő degradációjának észlelésére](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Osztályozás**. Az értesítés megmutatja, hogy hány felhasználó t vagy hány műveletet érint. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör**. A probléma minden forgalmat érint, vagy csak néhány oldalt? Ez csak bizonyos böngészőkvagy helyek? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztika**. Gyakran előfordul, hogy az értesítésben szereplő diagnosztikai információk a probléma természetét sugallják. Ha például a válaszidő lelassul, ha a kérelmek aránya magas, az azt sugallja, hogy a kiszolgáló vagy a függőségek túlterheltek. 

    Ellenkező esetben nyissa meg a Teljesítmény panelt az Application Insightsban. Itt megtalálja [a Profiler](profiler.md) adatait. Ha kivételeket hoz, megpróbálhatja a [pillanatkép-hibakeresőt](../../azure-monitor/app/snapshot-debugger.md)is.



## <a name="configure-email-notifications"></a>E-mail értesítések konfigurálása

Az intelligens észlelési értesítések alapértelmezés szerint engedélyezve vannak, és azoknak küldik, akik [figyelőolvasóval](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és [közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) hozzáféréssel rendelkeznek ahhoz az előfizetéshez, amelyben az Application Insights-erőforrás található. Ennek módosításához kattintson a **Beállítás** az e-mail értesítésben elemre, vagy nyissa meg az Intelligens észlelési beállításokat az Application Insightsban. 
  
  ![Intelligens észlelési beállítások](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Az intelligens észlelési e-mailben található **leiratkozási** hivatkozás sal leállíthatja az e-mail értesítések fogadását.

Az intelligens észlelések teljesítményanomáliáiról szóló e-mailek egy e-mailre korlátozódnak naponta application insights-erőforrásonként. Az e-mailt csak akkor küldjük el, ha legalább egy új probléma észlelhető az adott napon. Nem fog semmilyen üzenetet megismételni. 

## <a name="faq"></a>GYIK

* *Tehát a Microsoft munkatársai megnézik az adataimat?*
  * Nem. A szolgáltatás teljesen automatikus. Csak ön kapja meg az értesítéseket. Az adatok [private](../../azure-monitor/app/data-retention-privacy.md)privátak.
* *Elemzi az Application Insights által gyűjtött összes adatot?*
  * Jelenleg nem. Jelenleg elemezzük a kérelem válaszidejét, a függőségi válaszidőt és az oldal betöltési idejét. További mérőszámok elemzése a mi hátralék várja.

* Milyen típusú alkalmazás nem ez a munka?
  * Ezeket a degradációkat minden olyan alkalmazásban észleli, amely a megfelelő telemetriai adatokat hozza létre. Ha telepítette az Application Insights ot a webalkalmazásban, majd a kérelmek és a függőségek automatikusan nyomon követik. De a háttérszolgáltatásokban vagy más alkalmazásokban, ha a [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) vagy a [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)hívásait illesztette be, akkor az intelligens észlelés ugyanúgy fog működni.

* *Létrehozhatok saját anomáliaészlelési szabályokat, vagy testre szabhatom a meglévő szabályokat?*

  * Még nem, de a következőket teheti:
    * [Állítson be riasztásokat,](../../azure-monitor/app/alerts.md) amelyek megmondják, ha egy metrika átlép egy küszöbértéket.
    * [Exportálja a telemetriai adatokat](../../azure-monitor/app/export-telemetry.md) egy [adatbázisba](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) vagy [a Power BI-ba,](../../azure-monitor/app/export-power-bi.md )ahol saját maga elemezheti azt.
* *Milyen gyakran történik az elemzés?*

  * Az elemzést naponta futtatjuk az előző napi telemetriai adatokon (teljes nap UTC időzónában).
* *Tehát ez helyettesíti a [metrikariasztásokat?](../../azure-monitor/app/alerts.md)*
  * Nem.  Nem kötelezzük el magunkat minden olyan viselkedés észlelése mellett, amit abnormálisnak tart.


* *Ha nem teszek semmit az értesítésre válaszul, kapok emlékeztetőt?*
  * Nem, minden problémáról csak egyszer kap üzenetet. Ha a probléma továbbra is fennáll, akkor az intelligens észlelési hírcsatorna panelen frissül.
* *Elvesztettem az e-mailt. Hol találom az értesítéseket a portálon?*
  * Az alkalmazás Application Insights áttekintésében kattintson az **Intelligens észlelés ivarszemtile-ra.** Ott legfeljebb 90 nappal később megtalálhatja az összes értesítést.

## <a name="how-can-i-improve-performance"></a>Hogyan javíthatom a teljesítményt?
Lassú és sikertelen válaszok az egyik legnagyobb frusztrációt a webhely felhasználói, mint tudod, a saját tapasztalat. Ezért fontos, hogy foglalkozzunk a problémákkal.

### <a name="triage"></a>Osztályozás
Először is, számít ez? Ha egy oldal betöltése mindig lassú, de a webhely felhasználóinak csak 1%-ának kell megnéznie, akkor talán fontosabb dolgokra is gondolnia kell. Másrészről, ha csak 1% -ból használók nyit ez, de ez dob kivételek mind idő, amit erő lenni értékű vizsgálat.

Általános útmutatóként használja a hatásutasítást (érintett felhasználók vagy a forgalom %-a), de vegye figyelembe, hogy nem a teljes történetről van szó. Gyűjtsön más bizonyítékokat, hogy megerősítsék.

Vegye figyelembe a probléma paramétereit. Ha földrajzi lagúnafüggő, állítson be [rendelkezésre állási teszteket,](../../azure-monitor/app/monitor-web-app-availability.md) beleértve az adott régiót is: előfordulhat, hogy egyszerűen hálózati problémák vannak az adott területen.

### <a name="diagnose-slow-page-loads"></a>Lassú oldalbetöltések diagnosztizálása
Hol van a probléma? A szerver lassan válaszol, az oldal nagyon hosszú, vagy nem a böngésző, hogy nem sok munkát megjeleníteni?

Nyissa meg a Böngészők metrikus panelt. A böngészőoldal betöltési idejének szegmentált megjelenítése megmutatja, hogy merre tart az idő. 

* Ha a **küldési kérelem ideje** magas, vagy a kiszolgáló lassan válaszol, vagy a kérés egy sok adatot adó bejegyzés. Tekintse meg a [teljesítménymutatókat](../../azure-monitor/app/web-monitor-performance.md#metrics) a válaszidők vizsgálatához.
* Állítsa be a [függőségek nyomon követését,](../../azure-monitor/app/asp-net-dependencies.md) és állapítsa meg, hogy a lassúság külső szolgáltatások vagy az adatbázis miatt van-e.
* Ha **a válasz fogadása** dominál, az oldal és annak függő részei - JavaScript, CSS, képek és így tovább (de nem aszinkron módon betöltött adatok) hosszúak. Állítson be egy [rendelkezésre állási tesztet,](../../azure-monitor/app/monitor-web-app-availability.md)és győződjön meg róla, hogy beállítja a függő alkatrészek betöltésének lehetőségét. Ha eredményeket kap, nyissa meg az eredmény részleteit, és bontsa ki a különböző fájlok betöltési idejét.
* A magas **ügyfélfeldolgozási idő** azt sugallja, hogy a parancsfájlok lassan futnak. Ha az ok nem nyilvánvaló, fontolja meg néhány időzítési kód hozzáadását, és küldje el az időket a trackMetric hívásokban.

### <a name="improve-slow-pages"></a>Lassú oldalak javítása
Van egy web tele tanácsot javítása a szerver válaszok és az oldal betöltési idő, így nem próbálja meg ismételni az egészet itt. Íme néhány tipp, hogy valószínűleg már tudni, csak azért, hogy neked gondolkodás:

* Lassú betöltés a nagy fájlok miatt: Töltse be a parancsfájlokat és más részeket aszinkron módon. Használja a parancsfájl-kötegelést. Bontsa a főoldalt olyan widgetekre, amelyek külön töltik be az adataikat. Ne küldjön egyszerű régi HTML-kódot hosszú táblázatokhoz: parancsfájl segítségével kérje az adatokat JSON vagy más kompakt formátumban, majd töltse ki a táblázatot a helyén. Vannak nagy kereteket, hogy segítsen mindezt. (Ők is jár nak nagy szkriptek, természetesen.)
* Lassú kiszolgálófüggőségek: Vegye figyelembe az összetevők földrajzi helyét. Ha például az Azure-t használja, győződjön meg arról, hogy a webkiszolgáló és az adatbázis ugyanabban a régióban található. A lekérdezések a szükségesnél több információt kérnek le? Segítene a gyorsítótárazás vagy a kötegelés?
* Kapacitási problémák: Tekintse meg a kiszolgáló mutatóit a válaszidők és a kérelmek száma. Ha a válaszidők száma aránytalanul a kérelmek száma csúcsaiközött van, akkor valószínű, hogy a kiszolgálók meg vannak nyújtva.


## <a name="server-response-time-degradation"></a>Kiszolgálói válaszidő romlása

A válaszidő-lebomlási értesítés a következőket jelzi:

* A válaszidő a művelet normál válaszidejéhez képest.
* Hány felhasználó érintett.
* Átlagos válaszidő és 90 százalékos válaszidő ehhez a művelethez az észlelés napján és 7 nappal korábban. 
* A műveletkérelmek száma az észlelés napján és 7 nappal korábban.
* Korreláció a lebomlás ebben a műveletben és a kapcsolódó függőségek degradációk. 
* Linkek a probléma diagnosztizálásához.
  * Profiler nyomkövetések segítségével megtekintheti, ahol a működési idő töltött (a hivatkozás érhető el, ha Profiler nyomkövetési példákat gyűjtöttek ehhez a művelethez az észlelési időszak alatt). 
  * Teljesítményjelentések a Metrikakezelőben, ahol szeletelheti és kockázhatja az időtartományt/szűrőket ehhez a művelethez.
  * Keresse meg ezt a hívást adott hívástulajdonságok megtekintéséhez.
  * Hibajelentések – Ha a számláló > 1 ez azt jelenti, hogy a művelet ben voltak olyan hibák, amelyek hozzájárulhattak a teljesítmény csökkenéséhez.

## <a name="dependency-duration-degradation"></a>Függőség időtartamának lebontása

A modern alkalmazások egyre inkább mikroszolgáltatások tervezési megközelítését alkalmazzák, ami sok esetben a külső szolgáltatások jelentős megbízhatóságához vezet. Például ha az alkalmazás támaszkodik néhány adatplatform, vagy akkor is, ha saját robotszolgáltatás létrehozása valószínűleg továbbítja az egyes kognitív szolgáltató, hogy a botok, hogy kölcsönhatásba lépnek az emberi módon, és néhány adattár szolgáltatás bot lekérni a válaszokat.  

Példa függőséglebontási értesítésre:

![Íme egy példa a függőségi időtartam degradációjának észlelésére](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Figyelje meg, hogy azt mondja:

* A művelet normál válaszidejéhez viszonyított időtartam
* Hány felhasználóérintett
* A függőség átlagos időtartama és 90.
* Függőségi hívások száma az észlelés napján és 7 nappal az észlelés előtt
* Linkek a probléma diagnosztizálásához
  * Teljesítményjelentések a Metrikakezelőben ehhez a függőséghez
  * A hívások tulajdonságainak megtekintéséhez a függőségi hívások keresése
  * Hibajelentések – Ha > 1 száma, ez azt jelenti, hogy az észlelési időszak alatt sikertelen függőségi hívások történtek, amelyek hozzájárulhattak az időtartam lebontásához. 
  * Az Analytics megnyitása a függőség időtartamát kiszámító lekérdezésekkel és a  

## <a name="smart-detection-of-slow-performing-patterns"></a>A lassú teljesítményű minták intelligens észlelése 

Az Application Insights olyan teljesítményproblémákat talál, amelyek csak a felhasználók egy részét érinthetik, vagy bizonyos esetekben csak a felhasználókat érintik. Az oldalak betöltéséről szóló értesítés például lassabb az egyik böngészőtípuson, mint más típusú böngészőkben, vagy ha a kérelmeket lassabban kézbesítik egy adott kiszolgálóról. A tulajdonságok kombinációjával kapcsolatos problémákat is felfedezheti, például az adott operációs rendszert használó ügyfelek egy földrajzi területen történő lassú oldalbetöltését.  

Az ilyen anomáliákat nagyon nehéz észlelni csak az adatok ellenőrzésével, de gyakoribbak, mint gondolná. Gyakran csak akkor kerülnek felszínre, ha az ügyfelek panaszkodnak. Addigra már túl késő: az érintett felhasználók már átváltanak a versenytársakra!

Algoritmusaink jelenleg az oldal betöltési idejét, a kiszolgálón a válaszadási időket és a függőségi válaszidőket vizsgálják.  

Nem kell küszöbértékeket beállítania vagy szabályokat konfigurálnia. A gépi tanulási és adatbányászati algoritmusok a rendellenes minták észlelésére szolgálnak.

![Az e-mail riasztásból kattintson a hivatkozásra a diagnosztikai jelentés megnyitásához az Azure-ban](./media/proactive-performance-diagnostics/03.png)

* **Mikor** jelenik meg a probléma észlelésének időpontja.
* **Mit** írunk le:

  * A észlelt probléma;
  * Az általunk talált események jellemzői a probléma viselkedését mutatták.
* A táblázat összehasonlítja a rosszul teljesítő készletet az összes többi esemény átlagos viselkedésével.

Kattintson a hivatkozásokra a Metrikakezelő és a Keresés megnyitásához a megfelelő jelentéseken, szűrve a lassú teljesítményű készlet idejére és tulajdonságaira.

Módosítsa az időtartományt és a szűrőket a telemetriai adatok feltárásához.

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítenek az alkalmazástelemetria imitálatának vizsgálatában:

* [Profilkészítő](profiler.md) 
* [Pillanatkép-hibakereső](../../azure-monitor/app/snapshot-debugger.md)
* [Elemzés](../../azure-monitor/log-query/get-started-portal.md)
* [Analytics intelligens diagnosztika](../../azure-monitor/app/analytics.md)

Az intelligens észlelések teljesen automatikusak. De talán szeretne még néhány riasztást beállítani?

* [Manuálisan konfigurált metrikariasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md)
