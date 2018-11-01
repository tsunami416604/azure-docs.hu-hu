---
title: Az Azure Application Insights tranzakció diagnosztikája |} A Microsoft Docs
description: Application Insights végpontok közötti tranzakció diagnosztikája
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 5b52fd41fc3e37078bbddc721c0c54af2c430b43
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419231"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Egyesített összetevőt tranzakció diagnosztikája

Az egyesített diagnosztika terén automatikusan kiszolgálóoldali telemetria utal. az összes figyelt az Application Insights-összetevő egyetlen nézetben. Nem számít, ha a különböző kialakítási kulcs több erőforrást. Application Insights az alapul szolgáló kapcsolatot észlel, és lehetővé teszi, hogy könnyedén diagnosztizálhatja az alkalmazás-összetevőt, függőséget vagy egy tranzakció lassulás vagy hibát okozó.

## <a name="what-is-a-component"></a>Mi az összetevő?

Összetevők egymástól függetlenül üzembe helyezhető az elosztott és mikroszolgáltatás-alkalmazások tartoznak. A fejlesztők és műveleti csapatok kód szintű láthatóság vagy a ezeket alkalmazás-összetevők által létrehozott telemetriát hozzáféréssel rendelkezik.

* Összetevők eltérnek "a megfigyelt" külső függőségei, például az SQL-EventHub stb., amelyek nem lehet a csapat vagy szervezet (kód vagy telemetria) eléréséhez.
* Összetevők futtassa bármely server-szerepkör-tárolót példányok száma.
* Összetevők külön Application Insights-kialakítási kulcs (akkor is, ha az előfizetések különböző) vagy egy egyetlen az Application Insights-kialakítási kulcsot a jelentéskészítés különböző szerepköröket is. Az új felület részleteket jeleníti meg termékcsalád összes tagjára, függetlenül attól, hogy azok be van állítva.

> [!NOTE]
> * **Hiányzik a kapcsolódó elem hivatkozásokat?** Az összes kapcsolódó telemetria a [felső](#cross-component-transaction-chart) és [alsó](#all-telemetry-with-this-Operation-Id) a bal oldali szakaszát. 

## <a name="transaction-diagnostics-experience"></a>Tranzakció diagnosztikája élmény
Ez a nézet részből négy fő: eredmények listája, összetevőt tranzakció diagramot, ezt a műveletet, és bármely kiválasztott telemetriai elem a bal oldali ablaktáblában kapcsolódó összes telemetriai adat idő – feladatütemezési listáját.

![Főrész](media/app-insights-transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Összetevőt tranzakció diagram

A diagram vízszintes sávokkal ütemterv és -függőségek időtartamára különböző összetevők biztosít. Gyűjtött kivételeket is vannak megjelölve az eseményeket egy idővonalon megjelenítve.

* A diagram felső sorában jelöli a belépési pont, a bejövő kéréseket az első összetevő neve, az ezt a tranzakciót. Az időtartam a tranzakció teljes ideje.
* Bármilyen külső függőségekhez intézett hívások függőségi típusát jelző ikonok rendelkező egyszerű nem összecsukható sor.
* Egyéb összetevők hívásainak összecsukható sorok. Minden sor megfelel egy adott művelethez az összetevő vyvolat.
* Alapértelmezett, a kérelem, függőséget vagy kivételt a kijelölt jelenik meg, a jobb oldalon.
* Válassza ki az összes sort, hogy a [részletei a jobb oldali](#details-of-the-selected-telemetry). 

> [!NOTE]
Az összetevőinek hívásaihoz két sor: egy sort képvisel a kimenő hívás (függőségek) a hívó összetevőtől, és a többi sor felel meg a bejövő kérelem, a hívott összetevő. A vezető ikon és a időtartama sávok különböző stílusának segít megkülönböztetni őket.

## <a name="all-telemetry-with-this-operation-id"></a>Ez a művelet az azonosító az összes telemetriai adat

Ez a szakasz a tranzakció kapcsolatos összes telemetriai adat idő sorozatát időrendi listanézet mutatja. Az egyéni eseményeket és a nyomkövetési adatok nem jelennek meg, a tranzakciós diagram is mutatja. Szűrheti a listát egy adott összetevő hívás által létrehozott telemetriát. Ebben a listában, hogy megfelelő kiválaszthatja az összes telemetriai elem [részletei a jobb oldali](#details-of-the-selected-telemetry).

![Minden telemetriai adat idő sorrendje](media/app-insights-transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>A kiválasztott telemetriai adatait

Az összecsukható ablaktábla bármely a tranzakció diagramot, vagy a listából kiválasztott elem részleteinek megtekintéséből jeleníti meg. "Az összes megjelenítése" felsorolja az összes gyűjtött szabványos attribútumát. Egyéni attribútumokat külön-külön az alábbiak a szabványos készlet. Kattintson a "..." alatt a stack nyomkövetési ablakban másolja a nyomkövetés beolvasásához. "Nyílt profiler hívásláncai" vagy "hibakeresési pillanatfelvétel megnyitása" látható kód kódszintű diagnosztika megfelelő részletei ablaktábla.

![Kivételek részletei](media/app-insights-transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Keresési eredmények

Az összecsukható panel megjeleníti az eredményeket, a szűrési feltételeknek. Kattintson a frissíteni a saját adatait a fenti 3 szakaszok minden eredményt. Próbálja ki, amelyek a legnagyobb valószínűséggel, így az összes összetevő elérhető adatok akkor is, ha a mintavétel van alkalmazva. Ezek közül bármelyik a minták keresése. Ezek láthatók, mint a "ajánlott" mintát.

![Keresési eredmények](media/app-insights-transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler és a pillanatkép-hibakereső

[Az Application Insights profiler](app-insights-profiler.md) vagy [pillanatkép-hibakereső](app-insights-snapshot-debugger.md) súgó a kód szintű diagnosztikáról, teljesítmény és meghibásodási problémákra. A felhasználói élményét láthatja a profiler hívásláncai, vagy kattintson az egyes összetevőkre egyetlen pillanatképeket.

Ha nem sikerült a Profiler működik, lépjen kapcsolatba **serviceprofilerhelp@microsoft.com**

Ha nem sikerült a pillanatkép-hibakereső működik, lépjen kapcsolatba **snapshothelp@microsoft.com**

![Profiler-integráció](media/app-insights-transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>GYIK

*Egy adott összetevőt a diagramon láthatók, és a többi csak jelennek meg a külső függőségek nélkül bármilyen részletes belül összetevőket kimenetelét.*

A lehetséges okok:

* A többi összetevő Application Insights révén utasított vannak?
* Akkor használja a legújabb stabil Application Insights SDK-t?
* Ha ezeket az összetevőket külön Application Insights-erőforrások, rendelkezik megfelelő hozzáféréssel a telemetriát?

Ha rendelkezik hozzáféréssel, és az összetevőket a legújabb Application Insights SDK-k olyan vannak felszerelve, tudassa velünk, a jobb felső visszajelzés csatornán keresztül.

*A függőségek ismétlődő sorok láthatók. Ez várható?*

Jelenleg hogy láthatók a kimenő függőségi hívás külön, a bejövő kérelem. Általában a két hívások hely csak az üzenetváltási különböző hálózati miatt folyamatban időtartam értéke azonos. A vezető ikon és a időtartama sávok különböző stílusának segít megkülönböztetni őket. Ez a bemutató, az adatok az zavaró? Küldje el visszajelzését!

*Mi a helyzet óra megdönti különböző összetevő-példányok között?*

Ütemtervek módosítva eltérései a tranzakció-diagram van. A részleteket tartalmazó ablaktáblán, vagy az Analytics használatával pontos időbélyegei látható.

*Az új felület hiányzik kapcsolódó elemek lekérdezések nagy részénél?*

Ez az elvárt működés. A kapcsolódó elemek termékcsalád összes tagjára mindegyike már elérhető a bal oldali (felső és alsó szakaszt). Az új felület rendelkezik, amely nem fedi le a bal oldalon két kapcsolódó elemek: előtt és után ezt az eseményt, és a felhasználó idővonalán öt perc alatt az összes telemetriai adat.
