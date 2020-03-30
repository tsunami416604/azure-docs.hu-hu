---
title: Azure Application Insights tranzakciódiagnosztika | Microsoft dokumentumok
description: Az Application Insights végpontok között végeztranzakció-diagnosztikája
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671137"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Egységes, több összetevőből álló tranzakciódiagnosztika

Az egyesített diagnosztikai élmény automatikusan korrelálja a kiszolgálóoldali telemetriai adatokat az összes Application Insights figyelt összetevők egyetlen nézetben. Nem számít, ha több erőforrás külön instrumentation kulcsokat. Az Application Insights észleli az alapul szolgáló kapcsolatot, és lehetővé teszi, hogy könnyen diagnosztizálja az alkalmazás-összetevő, függőség vagy kivétel, amely a tranzakció lassulását vagy hibáját okozta.

## <a name="what-is-a-component"></a>Mi az összetevő?

Az összetevők egymástól függetlenül telepíthető részei az elosztott/mikroszolgáltatási alkalmazásnak. A fejlesztők és a műveleti csapatok kódszintű láthatósággal vagy hozzáféréssel rendelkeznek az alkalmazás-összetevők által létrehozott telemetriai adatokhoz.

* Az összetevők eltérnek a "megfigyelt" külső függőségektől, például az SQL-től, az EventHub-tól stb., amelyekhez a csapat/szervezet nem férhet hozzá (kód vagy telemetria).
* Az összetevők tetszőleges számú kiszolgáló-/szerepkör-/tárolópéldányon futnak.
* Az összetevők lehetnek különálló Application Insights instrumentation kulcsok (akkor is, ha az előfizetések eltérőek) vagy különböző szerepkörök jelentési egyetlen Application Insights instrumentation kulcs. Az új felület az összes összetevő részleteit jeleníti meg, függetlenül attól, hogy hogyan lettek beállítva.

> [!NOTE]
> * **Hiányzik a kapcsolódó elemhivatkozás?** Az összes kapcsolódó telemetriai adatok a bal oldali [felső](#cross-component-transaction-chart) és [alsó](#all-telemetry-with-this-operation-id) szakaszok. 

## <a name="transaction-diagnostics-experience"></a>Tranzakciódiagnosztika i tapasztalata
Ebben a nézetben négy fő részből áll: eredménylista, egy több komponensből álló tranzakciós diagram, a művelethez kapcsolódó összes telemetriai elem idősorrend-listája, valamint a bal oldalon kijelölt telemetriai elemek részletei ablaktáblája.

![Kulcsfontosságú alkatrészek](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Több komponensből álló tranzakciódiagram

Ez a diagram vízszintes sávokat tartalmazó ütemtervet biztosít az összetevők közötti kérelmek és függőségek időtartamára. A begyűjtött kivételek az idővonalon is meg vannak jelölve.

* A diagram felső sora a belépési pontot, a tranzakcióban szereplő első összetevőbe érkező bejövő kérelmet jelöli. Az időtartam a tranzakció befejezéséhez szükséges teljes idő.
* A külső függőségek hívásai egyszerű, nem összecsukható sorok, a függőségtípusát jelző ikonokkal.
* A többi összetevő hívása összecsukható sor. Minden sor egy adott műveletnek felel meg, amelyet az összetevő hív meg.
* Alapértelmezés szerint a kiválasztott kérelem, függőség vagy kivétel a jobb oldalon jelenik meg.
* Jelölje ki bármelyik sort, hogy a [részleteket a jobb oldalon](#details-of-the-selected-telemetry)láthassa. 

> [!NOTE]
> A többi összetevő hívásai két sort tartalmaznak: az egyik sor a hívó összetevő kimenő hívását (függőségét), a másik pedig a hívott összetevő bejövő kérésének felel meg. Az időtartamsávok vezető ikonja és egyedi stílusa segít megkülönböztetni őket.

## <a name="all-telemetry-with-this-operation-id"></a>Az összes telemetria ezzel a műveletazonosítóval

Ez a szakasz a tranzakcióhoz kapcsolódó összes telemetriai adatok idősorozatában jeleníti meg a síklista nézetét. Azt is megjeleníti az egyéni eseményeket, és a nyomkövetések, amelyek nem jelennek meg a tranzakciós diagramon. Ezt a listát szűrheti egy adott összetevő/hívás által létrehozott telemetriai adatokra. A lista bármely telemetriai elemét kiválaszthatja a megfelelő részletek megtekintéséhez [a jobb oldalon.](#details-of-the-selected-telemetry)

![Az összes telemetria iszsorozata](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>A kiválasztott telemetria részletei

Ez az összecsukható ablaktábla a tranzakciódiagram bármely kijelölt elemének vagy a listának a részleteit jeleníti meg. A "Show all" az összegyűjtött összes szabványos attribútumot felsorolja. Az egyéni attribútumok külön felsorolva a szabványos készlet alatt találhatók. Kattintson a "..." a veremkövetési ablak alatt, hogy lemásolhassa a nyomkövetést. A "Profilkezelő-nyomkövetések megnyitása" vagy a "Hibakeresési pillanatkép megnyitása" parancs a megfelelő részletes ablaktáblákban jeleníti meg a kódszint-diagnosztikát.

![Kivétel részletei](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Keresési eredmények

Ez az összecsukható ablaktábla a szűrési feltételeknek megfelelő többi eredményt jeleníti meg. Kattintson bármelyik eredményre, hogy frissítse a megfelelő részleteket a 3 fent felsorolt szakaszban. Igyekszünk olyan mintákat találni, amelyek a legnagyobb valószínűséggel rendelkeznek az összes összetevőadataival, még akkor is, ha a mintavétel bármelyikben érvényben van. Ezek "javasolt" mintákként jelennek meg.

![Keresési eredmények](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profilozó és pillanatkép-hibakereső

[Az Application Insights profilozó](../../azure-monitor/app/profiler.md) vagy [pillanatkép-hibakereső](snapshot-debugger.md) segít a teljesítmény és a hiba problémák kódszintű diagnosztikájában. Ezzel a tapasztalattal egyetlen kattintással láthatja a profilkészítőnyomkövetéseket vagy pillanatképeket bármely összetevőből.

Ha nem tudta a Profiler működését, forduljon **a\@serviceprofilerhelp microsoft.com**

Ha nem tudta a Snapshot Debugger működését, lépjen kapcsolatba **a snapshothelp\@microsoft.com**

![Profilozó integrációja](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>GYIK

*Egyetlen összetevőt látok a diagramon, és a többiek csak külső függőségként jelennek meg, anélkül, hogy részleteznék, mi történt ezekben az összetevőkben.*

Lehetséges okok:

* A többi összetevő az Application Insights-szal van instrumentálva?
* A legújabb stabil Application Insights SDK-t használják?
* Ha ezek az összetevők különálló Application Insights-erőforrások, rendelkezik a telemetriai adatokhoz való szükséges hozzáféréssel?

Ha rendelkezik hozzáféréssel, és az összetevők a legújabb Application Insights SDK-kkal vannak elvégezve, tudassa velünk a jobb felső visszacsatolási csatornán keresztül.

*Ismétlődő sorok jelennek meg a függőségeknél Ez várható?*

Jelenleg a kimenő függőségi hívást a bejövő kérelemtől elkülönítve jelenítjük meg. A két hívás általában azonosnak tűnik, és csak az időtartam értéke különbözik a hálózati oda-vissza út miatt. Az időtartamsávok vezető ikonja és egyedi stílusa segít megkülönböztetni őket. Az adatok bemutatása zavaros? Adja meg nekünk visszajelzését!

*Mi a helyzet az óra ferde különböző összetevő példányok?*

Az ütemtervek a tranzakciódiagramon az óradöntéshez igazítva. A pontos időbélyegeket a részletek ablaktáblában vagy az Analytics segítségével láthatja.

*Miért hiányzik az új felület a kapcsolódó elemek legtöbb lekérdezéséből?*

Ez az elvárt működés. Az összes kapcsolódó elem az összes összetevőn már elérhető a bal oldalon (a felső és az alsó részek között). Az új felület két kapcsolódó elemet tartalmaz, amelyeket a bal oldal nem fed le: az esemény előtt és után öt perccel és a felhasználói idővonallal kapcsolatos összes telemetriai adat.
