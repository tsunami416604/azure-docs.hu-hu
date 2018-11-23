---
title: Az Azure Service Fabric platformot szintű figyelése |} A Microsoft Docs
description: Ismerje meg a platformszintű események és naplók monitorozása és diagnosztizálása az Azure Service Fabric-fürtökkel használt.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 58bad793ba44ae91d75324257f55648cf3207cd0
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291448"
---
# <a name="monitoring-the-cluster"></a>A fürt figyelése

Fontos meghatározni,-e a hardver és a fürt van a várt módon viselkedik a fürt szintjén figyelheti. Bár a Service Fabric is fenntartható az alkalmazások futtatása közben egy hardverhiba, de továbbra is szeretné diagnosztizálhatja, hogy hiba történt az alkalmazások vagy az alapul szolgáló infrastruktúra készítését. Is célszerű figyelemmel kísérni a fürtök jobban megtervezheti a kapacitást hozzáadásával vagy eltávolításával hardver kapcsolatos döntéseket hozta a segít.

A Service Fabric több strukturált platform eseményt, mutatja [Service Fabric-események](service-fabric-diagnostics-events.md), az EventStore és a különböző naplózási csatornák-a-beépített keresztül. 

Windows, a Service Fabric-események állnak rendelkezésre a megfelelő számú egyetlen ETW-szolgáltató `logLevelKeywordFilters` használt műveleti és adatok & üzenetküldés csatornák - között válasszon Ez az a módszer azt ki kimenő Service Fabric-események szűrés külön az igény szerint.

* **Működési** magas szintű, Service Fabric és a fürtöt, egy csomópontot hamarosan, egy új alkalmazást, üzembe helyezéséhez vagy egy frissítési visszaállítási események által végrehajtott műveleteket stb. Az események teljes listáját lásd: [Itt](service-fabric-diagnostics-event-generation-operational.md).  

* **Működési – részletes**  
Rendszerállapot-jelentések és a terheléselosztási döntéseket hozhat.

A művelet csatornán többféleképpen ETW/Windows-eseménynaplók, beleértve keresztül érhetők el a [EventStore](service-fabric-diagnostics-eventstore.md) (elérhető Windows verziók 6.2 és újabb verziók, Windows fürtök esetén). Az EventStore hozzáférést biztosít (beleértve a fürt, csomópontok, alkalmazások, szolgáltatások, partíciók, replikák és tárolók entitások) / entitás alapja a fürt eseményeit, és elérhetővé teszi őket a REST API-k és a Service Fabric ügyféloldali kódtár használatával. Az EventStore használatával figyelheti a fejlesztési-tesztelési fürtökhöz, és lekérhesse az éles fürtök állapotát egy időponthoz ismeretekkel.

* **Adat & üzenetkezelés**  
Kritikus fontosságú naplók és esemény jön létre (jelenleg csak a ReverseProxy) üzenetküldést és adatelérési útjának (a reliable services-modellekhez).

* **Adat & üzenetkezelés – részletes**  
A data- és üzenetkezelés (Ez a csatorna rendelkezik egy nagyon nagy mennyiségű esemény) a fürt minden nem kritikus fontosságú naplót tartalmazó részletes csatornát.

Ezeket kívül két megadott strukturált EventSource csatornákat, valamint a jellegű támogatási célból gyűjtött naplók.

* [A Reliable Services eseményei](service-fabric-reliable-services-diagnostics.md)  
Programozási modell adott események.

* [A Reliable Actors eseményei](service-fabric-reliable-actors-diagnostics.md)  
Programozási modell adott események és teljesítményszámlálók.

* Támogatási naplók  
A rendszernaplók csak az USA által használható, ha a támogatást nyújtó Service Fabric által generált.

Ezek a különböző csatornák lefedje a javasolt szintű platform-naplózás. Kiszolgálószintű naplózás platform javítása érdekében fontolja meg jobban az állapotközpontú modellről megismeréséhez és egyéni állapotjelentések hozzáadása és hozzáadása ehhez egyéni **teljesítményszámlálók** hozhat létre egy valós idejű ismeretekkel hatását a szolgáltatások és alkalmazások a fürtön.

Ezek a naplók kihasználása érdekében azt javasoljuk, hogy engedélyezve van az Azure Portalon a fürt létrehozásakor a "diagnosztika". Diagnosztikai bekapcsolásával a fürt telepítésekor a Windows Azure Diagnostics az operatív, a Reliable Services és Reliable actors-csatornák tudomásul veszi, és tárolja az adatokat, amint azt további [az Azure-ral események összesítése Diagnosztikai](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Az Azure Service Fabric health és terhelésjelentés-készítés

A Service Fabric a saját állapotmodell, ezek a cikkek részletesen ismertetett rendelkezik:

- [Service Fabric állapotmonitorozásának bemutatása](service-fabric-health-introduction.md)
- [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)
- [A Service Fabric-állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

Szolgáltatásállapot-figyelést, kritikus fontosságú több szempontból üzemeltetéséig, különösen egy alkalmazás frissítése során. Miután a szolgáltatás minden frissítési tartomány frissítve van, a frissítési tartomány kell telnie állapot-ellenőrzések helyezi át a központi telepítés a következő frissítési tartományra. OK állapot nem érhető el, ha az üzembe helyezés, vissza lesz állítva a, hogy az alkalmazás egy ismert OK állapotban marad. Mielőtt a szolgáltatások visszavonásra hatással lehetnek egyes ügyfelek, bár a legtöbb ügyfél problémát nem tapasztalható. Ezenkívül megoldást akkor fordul elő, viszonylag gyorsan nem kell megvárnia egy humán operátort a művelet. A további állapotát ellenőrzi, hogy a kód, a több rugalmas a szolgáltatás az, hogy az üzembe helyezési problémák vannak építve.

A service health szerepet játszó másik tényező jelent a metrikák a szolgáltatásból. Metrikák fontosak a Service Fabricben, mert azokat erőforrás-használat elosztása érdekében. Metrikák is azt jelzi, hogy a helyrendszer állapotát. Például előfordulhat, hogy egy alkalmazás, amely számos szolgáltatás rendelkezik, és minden példány egy második (RPS) metrikánként kérelmek jelenti. Ha egy szolgáltatás több erőforrást, mint egy másik szolgáltatás használja, a Service Fabric szolgáltatáspéldány körül a fürt, még akkor is, erőforrás-használat karbantartása próbál helyezi át. Erőforrás-használat működésének részletesebb magyarázatáért lásd: [erőforrás-használat kezelésére, és a Service Fabric-metrikák betöltése](service-fabric-cluster-resource-manager-metrics.md).

Metrikák is segíthet információhoz juthat arról, hogy a szolgáltatás működik-e. Az idő múlásával a metrikák használatával ellenőrizze, hogy működik-e a szolgáltatás paraméterek belül. Például ha trendeket mutatják, hogy hétfő reggel ismét elindíthatja a 9-kor RPS átlagos 1000, majd beállíthat egy jelentés, amely figyelmeztet, ha az RPS 500 alatt vagy felett 1500. Minden tökéletesen finom lehet, de győződjön meg arról, hogy az ügyfelek merülnek fel a nagyszerű felhasználói élményt meg érdemes lehet. A szolgáltatás is meghatározhatja egy adott mérőszámok, amelyek jelenteni lehet állapotának ellenőrzése céljából, de a fürt resource terheléselosztás, amelyek nem befolyásolják. Ehhez állítsa nullára a metrika súlyának. Azt javasoljuk, hogy indítsa el az összes metrikát egy nulla-as súlyozással rendelkező, és nem növeli a súly, amíg nem biztos abban, hogy megértette, hogyan befolyásolja a metrikák súlyozási erőforrás-fürthöz tartozó terheléselosztási.

> [!TIP]
> Ne használjon túl sok súlyozott metrikákat. Tudni, miért szolgáltatáspéldányok vannak folyamatban áthelyezhető a terheléselosztás nehézkes lehet. Néhány metrikák hosszú úgy folytathatja!

Metrikák és az állapotjelentéseket jelöltségét ellenőrző azt jelzi, állapotát és teljesítményét az alkalmazás információit. **CPU teljesítményszámláló megtudhatja, hogyan a csomópont fel van, de nem utasítja, hogy egy adott szolgáltatás állapota kifogástalan, mivel több szolgáltatás előfordulhat, hogy fut egy csomóponton.** De mérőszámokat, például RPS elemek feldolgozását, és minden késleltetése egy adott szolgáltatás állapotát jelezheti.

## <a name="service-fabric-support-logs"></a>A Service Fabric támogatási naplók

Az Azure Service Fabric-fürttel, segítségért forduljon a Microsoft támogatási van szüksége, ha a támogatási naplók szinte mindig szükség. Ha a fürt az Azure-ban üzemel, támogatási naplók automatikusan konfigurálva és fürt létrehozásának részeként gyűjti. A naplók a fürterőforrás-csoporthoz dedikált tárfiókok vannak tárolva. A storage-fiók nem rendelkezik rögzített neve, de a fiókban található blob-tárolók és táblák kezdetű névvel rendelkező *fabric*. Önálló fürt log gyűjtemények beállításával kapcsolatos további információkért lásd: [létrehozása és kezelése egy önálló Azure Service Fabric-fürt](service-fabric-cluster-creation-for-windows-server.md) és [konfigurációs beállításai egy különálló Windows-fürt](service-fabric-cluster-manifest.md). Önálló Service Fabric-példányok a naplók helyi fájlmegosztást kell küldeni. Ön **szükséges** szeretné, hogy ezek a naplók a támogatást, de nem tartozhat használható bárki kívül a Microsoft ügyfél-támogatási csoporthoz.

## <a name="measuring-performance"></a>Teljesítmény méréséhez

Mérték teljesítmény, a fürt segít megérteni, hogyan tudja kezelni a terhelés és a meghajtó döntések körül a fürt méretezése (lásd: a fürthöz méretezés [az Azure-ban](service-fabric-cluster-scale-up-down.md), vagy [helyszíni](service-fabric-cluster-windows-server-add-remove-nodes.md)). Teljesítményadatok akkor is hasznos, a műveleteket, vagy az alkalmazások és szolgáltatások tartott, amikor a naplók elemzése a jövőben képest. 

A teljesítményszámlálók gyűjtése, ha a Service Fabricet használja, lásd: [teljesítményszámlálók a Service Fabricben](service-fabric-diagnostics-event-generation-perf.md)

Itt két egyszerű módja, amelyben állíthat be a fürthöz tartozó teljesítményadatok gyűjtése:

* **Az ügynök használatával**  
Ez az előnyben részesített módja gyűjt a gépről, teljesítmény, mivel ügynökök általában gyűjtendő lehetséges teljesítmény-mérőszámok listája, és válassza ki a metrikákat szeretné gyűjteni, vagy módosítása egy viszonylag egyszerű folyamat. Az Azure Monitor Service Fabric a Log Analytics ajánlat olvashat [Log Analytics-integráció](service-fabric-diagnostics-event-analysis-oms.md) és [beállítása a Log Analytics-ügynököket](../log-analytics/log-analytics-windows-agent.md) további információ a Log Analytics-ügynököket, amelyek egy az ilyen monitorozási ügynök, amely képes a fürt virtuális gépeihez teljesítményadatok csomópontmetrikák és üzembe helyezett tárolók.

* **Az Azure Table Storage-teljesítményszámlálók**  
Teljesítmény-mérőszámok az azonos table Storage, az eseményeket is küldhet. Ehhez a megfelelő teljesítményszámlálók felvételéhez a fürt virtuális gépekről az Azure Diagnostics-beállítások módosítása, illetve engedélyezni kívánja, hogy az érvényesítse a docker-stats, ha telepít tárolókat. Olvassa el konfigurálása [WAD teljesítményszámlálókat](service-fabric-diagnostics-event-aggregation-wad.md) a Service Fabric beállításához teljesítményszámlálót.

## <a name="next-steps"></a>További lépések

* Olvassa el a Service Fabric [Log Analytics-integráció](service-fabric-diagnostics-event-analysis-oms.md) fürt diagnosztikai adatok gyűjtéséhez és egyéni lekérdezésekkel és riasztások létrehozása
* Ismerkedés a Service Fabric beépített diagnosztikai információt biztosít, az a [EventStore](service-fabric-diagnostics-eventstore.md)
* Tárgyalja [diagnosztikai szokványos](service-fabric-diagnostics-common-scenarios.md) a Service Fabricben
