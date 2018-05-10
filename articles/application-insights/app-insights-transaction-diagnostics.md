---
title: Az Azure Application Insights tranzakció diagnosztika |} Microsoft Docs
description: Application Insights-végpontok közötti tranzakció diagnosztika
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: mbullwin;sdash
ms.openlocfilehash: 7a4e4f74c02358fc117e0a66977ee3f0aef5b1dd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Egyesített kereszt-összetevő tranzakció diagnosztika

*Ez a felület jelenleg előzetes verzióban érhetők, és lecseréli a meglévő diagnosztikai paneleken kiszolgálóoldali kérelmeket, függőségeit és kivételeket.*

Az előzetes egy új, egyesített diagnosztika kezelőfelülettel automatikusan hibához kiszolgálóoldali telemetriai az összes figyelt Application Insights összetevő egyetlen nézetben. Nem számít, ha külön instrumentation kulccsal rendelkező több erőforrást. Az Application Insights az alapul szolgáló kapcsolatot észlel, és lehetővé teszi a könnyen diagnosztizálhatja az alkalmazás-összetevő, a függőségekkel vagy az egy tranzakció lassulást vagy sikertelenségét okozó kivétel.

## <a name="what-is-a-component"></a>Mi az, hogy egy összetevő?

Összetevők az elosztott/mikroszolgáltatások alkalmazás egymástól függetlenül telepíthető részét képezik. A fejlesztők és a műveletek csapat kód szintű láthatóságot, vagy próbáljon elérni ezeket alkalmazás-összetevők által létrehozott telemetriai rendelkezik.

* Összetevők eltérnek "megfigyelt" külső függőségei, például az SQL, EventHub stb., amelyek a csoport/szervezeti nem lehet hozzáférni a (kód vagy telemetria).
* Összetevők tetszőleges server/szerepkör/tároló példányát futtatni.
* Összetevők külön Application Insights instrumentation kulcsok (akkor is, ha előfizetések különböző) vagy egy Application Insights instrumentation kulcs jelentéskészítés külön szerepköröket is. Az új felület részletes adatainak megjelenítése termékcsalád összes tagjára, függetlenül attól, hogy azok be van állítva.

> [!NOTE]
> * **Hiányzik a kapcsolódó elem hivatkozások?** Minden kapcsolódó kiszolgálóoldali kérelem, függőség és kivételekkel kapcsolatos telemetriai adatok van a [felső](#cross-component-transaction-chart) és [alsó](#all-telemetry-related-to-the-selected-component-operation) bal oldalán szakaszait. 
> * A [felső](#cross-component-transaction-chart) szakasz termékcsalád összes tagjára ad eredményül a tranzakciót. A legjobb eredmények érdekében győződjön meg arról, az összes összetevő és a legújabb stabil Application Insights SDK vannak tagolva. Ha másik Application Insights-erőforrások, ellenőrizze, hogy a telemetriai adatok megtekintéséhez szükséges engedélyekkel.
> * A [alsó](#all-telemetry-related-to-the-selected-component-operation) az bal oldalán látható szakasza **összes** a kérelem kapcsolódó telemetriai adatokat, beleértve a nyomkövetési adatokat és eseményeket a kiválasztott összetevőhöz.

## <a name="enable-transaction-diagnostics-experience"></a>Tranzakció diagnosztika élmény engedélyezése
Engedélyezze "egyesített részletek: E2E tranzakció diagnosztika" a a [az előzetes verziójú funkciók listáját](app-insights-previews.md)

![Minta engedélyezése](media/app-insights-e2eTxn-diagnostics/previews.png)

Ebben az előzetes verzióban érhető el jelenleg a kiszolgálóoldali kérelmeket, függőségeket és kivételeket. Érheti el az új felület a **keresési eredmények**, **teljesítmény**, vagy **hiba** osztályozhatja a feladatait. Az előzetes a felváltja a megfelelő klasszikus részletei panelen.

![Teljesítmény – minták](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Tranzakció diagnosztika élmény
Ez a nézet három részből kulcs: kereszt-összetevő tranzakció diagram, egy adott összetevő művelet, és bármely kiválasztott telemetriai elem a bal oldali ablaktáblában az összes telemetriai adat idő szekvenciális listáját.

![Legfontosabb elemei](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Kereszt-összetevő tranzakció diagram

Ez a diagram vízszintes sávokkal ütemterv biztosít kérések és a függőségek időtartama összetevői között. Az idősor szintén inaktívra összegyűjtött kivételek.

* Ez a diagram felső sora jelenti. a belépési pont a bejövő kérelem első összetevőnek hívása a következő tranzakcióban. Az időtartam a tranzakció elvégzéséhez szükséges teljes idő.
* Bármely külső függőségek hívásainak rendelkező függőségi típusát jelző ikonok egyszerű nem összecsukható sor.
* Egyéb összetevők hívásainak összecsukható sorok. Minden egyes sorára felel meg az összetevő a meghívott adott művelethez.
* Alapértelmezés szerint a kérelmet, a függőségekkel vagy a kivétellel, hogy kezdetben kiválasztott jelenik meg a diagramot.
* Válassza ki az összes sort, hogy a [részleteit a jobb oldali](#details-of-the-selected-telemetry). 

> [!NOTE]
Egyéb összetevők hívásainak két sort rendelkezik: egy sor jelenti a kimenő hívás (függőség) a hívó összetevő, és a többi sor felel meg a bejövő kérelem, a hívott összetevő. A kezdő Ikonos, mind a duration sávok különböző stílusbeállításokat segítségével különböztetheti.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>A kijelölt összetevő-művelethez kapcsolódó összes telemetriai adat

A kereszt-összetevő tranzakció diagram kijelölt minden sor egy adott összetevő a meghívott művelet kapcsolódik. A kiválasztott összetevő művelet címe alján megjelenik. Nyissa meg az ebben a szakaszban tekintheti meg, hogy adott művelethez kapcsolódó összes telemetriai adat strukturálatlan idő sorozata. Megfelelő megjelenítéséhez a listában szereplő bármely telemetriai elem kiválaszthatja [részleteit a jobb oldali](#details-of-the-selected-telemetry).

![Idő sorozatát összes telemetriai adat](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>A kijelölt telemetriai adatait

Ezen az ablaktáblán látható a bal oldali vagy a két szakasz a kiválasztott elemek részletei. "Minden látszik" felsorolja az összes összegyűjtött szabványos attribútumait. Egyéni attribútumokat külön-külön az alábbiak a szabványos készletét. Kattintson a "Megnyitás szolgáltatásprofil-elemzői adat" vagy "Megnyitás hibakeresési snapshot" kód szintű diagnosztikai megfelelő részletek ablaktáblán.

![Kivételek részletei](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profilkészítő és a pillanatfelvételek hibakereső

[Application Insights Profilkészítő](app-insights-profiler.md) vagy [pillanatkép hibakereső](app-insights-snapshot-debugger.md) súgó a kód szintű diagnosztika a teljesítmény és meghibásodási problémákra. Ez a felület a szolgáltatásprofil-elemzői adat megjelenik, vagy kattintson a egyetlen összetevők közül bármelyik pillanatképeinek.

Ha nem sikerült a Profilkészítő működik-e, lépjen kapcsolatba **serviceprofilerhelp@microsoft.com**

Ha nem sikerült a pillanatkép-hibakereső működik-e, lépjen kapcsolatba **snapshothelp@microsoft.com**

![Integráció a hibakereső](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>GYIK

*A diagramon látható adott összetevőt, és a többi csak jelennek meg a külső függőségekkel anélkül, hogy mi történt az összetevők belül minden részletét.*

Lehetséges okok:

* A többi összetevő az Application insights szolgáltatással vannak tagolva?
* Akkor használja a legújabb stabil Application Insights SDK?
* Ha ezeket az összetevőket külön Application Insights-erőforrások, rendelkezik megfelelő hozzáféréssel a telemetriai adatokat?

Ha Ön rendelkezik hozzáféréssel, és az összetevők és a legújabb Application Insights SDK-k vannak tagolva, ossza meg velünk a jobb felső visszajelzés csatornán keresztül.

*Csak kell külső függőségeit. Kell I érdeklik ebben az előzetes verzióban?*

Igen. Az új felület a szolgáltatás minden kapcsolódó kiszolgálóoldali telemetriai adatokat egyetlen nézetben. A régebbi részletei panel cseréli a forgatókönyvben a jövőben, így próbálja ki és küldje el visszajelzését. Néz ki egy adott összetevőt tranzakció itt található:

![Egyetlen összetevőt élmény](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*A függőségek ismétlődő sorok láthatók. Ez várható?*

Ilyenkor azt láthatók a kimenő függőségi hívás a bejövő kérelem elkülönül. Általában a két hívások hely csak a duration érték miatt a hálózati különböző üzenetváltási azonos. A kezdő Ikonos, mind a duration sávok különböző stílusbeállításokat segítségével különböztetheti. Az adatok a bemutató egyértelmű? Küldje el visszajelzését!

*Mi a helyzet óra között különböző összetevő példányok megdönti?*

Ütemtervek módosítva óra dönt a tranzakció diagram van. A pontos időbélyegeket a részleteket tartalmazó ablaktáblán vagy Analytics segítségével tekintheti meg.

*Az új felület hiányzik kapcsolódó elemek lekérdezések nagy részénél?*

Ez az elvárt működés. A kapcsolódó elem termékcsalád összes tagjára már rendelkezésre állnak a bal oldalon (felső és alsó szakaszok). Az új felület rendelkezik, amely nem fedi le a bal oldalon két kapcsolódó elemek: 5 perc előtt és után ezt az eseményt a felhasználó ütemterv összes telemetriai adat.

*Miért az új felület nincs telepítve a szolgáltatás, amely a régebbi paneleket az I loved?*

Küldjön visszajelzést! Szeretnénk cím számára fontos szempont, mielőtt a szolgáltatás általánosan elérhető, hogy mely a régebbi nézetek elavulttá válik. A lépést Ha vissza kíván térni a régebbi paneleket az előzetes letilthatja.

## <a name="known-issues"></a>Ismert problémák

* A hiba-mintákat, az alkalmazás-hozzárendelés csatolja a régebbi részletei panelen.
* A keresési eredmények autocluster alapú feltárása a régebbi részletei panel kapcsolódik.
* Az új felület a munkalem-integrációt nem érhető el.
