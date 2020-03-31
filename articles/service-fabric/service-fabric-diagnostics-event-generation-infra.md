---
title: Azure Service Fabric platformszintű figyelés
description: Ismerje meg az Azure Service Fabric-fürtök figyeléséhez és diagnosztizálásához használt platformszintű eseményeket és naplókat.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376936"
---
# <a name="monitoring-the-cluster"></a>A fürt figyelése

Fontos, hogy a fürt szintjén figyelje, hogy a hardver és a fürt a várt módon viselkedjen-e. Bár a Service Fabric hardverhiba esetén is futtathatja az alkalmazásokat, de továbbra is diagnosztizálnia kell, hogy hiba történik-e egy alkalmazásban vagy az alapul szolgáló infrastruktúrában. Emellett figyelnie kell a fürtöket, hogy jobban megtervezhesse a kapacitást, és segítve a hardver hozzáadásával vagy eltávolításával kapcsolatos döntéseket.

A Service Fabric számos strukturált platformeseményt tesz elérhetővé, a [Service Fabric-eseményekaz](service-fabric-diagnostics-events.md)EventStore-on és a különböző naplócsatornákon keresztül. 

Windows rendszeren a Service Fabric-események egyetlen ETW-szolgáltatótól `logLevelKeywordFilters` érhetők el, amely az operatív és az adatkezelési & üzenetek csatornái közötti választáshoz szükséges, releváns készlettel – ez az a mód, ahogyan elkülönítjük a kimenő Service Fabric-eseményeket, amelyekre szükség szerint szűrni kell.

* **Működési** A Service Fabric és a fürt által végrehajtott magas szintű műveletek, beleértve a közelgő csomópont, egy új alkalmazás üzembe helyezése vagy a frissítés visszaállítása stb. Az események teljes listáját [itt](service-fabric-diagnostics-event-generation-operational.md)olvashatja.  

* **Operatív - részletes**  
Állapotjelentések és terheléselosztási döntések.

A műveleti csatorna számos módon elérhető, beleértve az ETW/Windows eseménynaplókat, az [EventStore-t](service-fabric-diagnostics-eventstore.md) (a Windows 6.2-es és újabb verzióiban érhető el a Windows-fürtökesetében). Az EventStore hozzáférést biztosít a fürt eseményeihez entitásonként (entitások, beleértve a fürtöt, csomópontokat, alkalmazásokat, szolgáltatásokat, partíciókat, replikákat és tárolókat), és elérhetővé teszi őket a REST API-kon és a Service Fabric ügyfélkódlibrary-n keresztül. Az EventStore segítségével figyelheti a fejlesztői/tesztelési fürtöket, és időpontban megismerheti az éles fürtök állapotát.

* **Adatküldési & üzenetek**  
Kritikus naplók és az üzenetküldés (jelenleg csak a ReverseProxy) és az adatelérési út (megbízható szolgáltatási modellek) által generált események.

* **Data & Messaging - részletes**  
Részletes csatorna, amely tartalmazza az összes nem kritikus naplók az adatok és az üzenetek a fürtben (ez a csatorna nagyon nagy mennyiségű események).

Ezeken kívül két strukturált EventSource csatorna, valamint a támogatási célokra gyűjtött naplók is rendelkezésre állnak.

* [A Reliable Services eseményei](service-fabric-reliable-services-diagnostics.md)  
Programozási modell-specifikus események.

* [A Reliable Actors eseményei](service-fabric-reliable-actors-diagnostics.md)  
Programozási modell specifikus események és teljesítményszámlálók.

* Támogatási naplók  
A Service Fabric által létrehozott rendszernaplókat csak akkor használjuk, ha támogatást nyújtunk.

Ezek a különböző csatornák lefedik az ajánlott platformszintű naplózás nagy részét. A platformszintű naplózás javítása érdekében fontolja meg az állapotmodell jobb megértésébe és egyéni állapotjelentések hozzáadásába való befektetést, valamint egyéni **teljesítményszámlálók hozzáadását** a szolgáltatások és alkalmazások fürtre gyakorolt hatásának valós idejű megértéséhez.

Annak érdekében, hogy kihasználják ezeket a naplókat, erősen ajánlott, hogy hagyja a "Diagnosztika" engedélyezve a fürt létrehozása során az Azure Portalon. A diagnosztika bekapcsolásával a fürt üzembe helyezésekor a Windows Azure Diagnostics képes nyugtázni a működési, megbízható szolgáltatások és megbízható szereplők csatornákat, és az adatokat az [Azure Diagnostics összesített eseményeiben](service-fabric-diagnostics-event-aggregation-wad.md)tovább magyarázva tárolja.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Az Azure Service Fabric állapot- és terhelésjelentése

A Service Fabric saját egészségügyi modellel rendelkezik, amelyet részletesen ismertetett az alábbi cikkekben:

- [A Service Fabric állapotmonitorozásának bemutatása](service-fabric-health-introduction.md)
- [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)
- [A Service Fabric állapotjelentésének megtekintése](service-fabric-view-entities-aggregated-health.md)

Az állapotfigyelés kritikus fontosságú a szolgáltatás működtetésének több aspektusa szempontjából, különösen az alkalmazás frissítése során. A szolgáltatás minden frissítési tartományának frissítése után a frissítési tartománynak át kell mennie az állapotellenőrzéseken, mielőtt a központi telepítés a következő frissítési tartományba kerül. Ha az OK állapot nem érhető el, a központi telepítés visszalesz állítva, így az alkalmazás ismert OK állapotban marad. Bár egyes ügyfeleket érinthet a szolgáltatások visszagörgetése, a legtöbb ügyfél nem tapasztal problémát. A megoldás viszonylag gyorsan történik anélkül, hogy meg kellene várnia egy emberi operátor cselekvését. Minél több állapotellenőrzést tartalmaz a kódba, annál rugalmasabb a szolgáltatás a telepítési problémák.

A szolgáltatás állapotának egy másik szempontja a metrikák jelentése a szolgáltatásból. Metrikák fontos a Service Fabric, mert erőforrás-használat kiegyenlítésére használják. A mérőszámok a rendszer állapotának mutatójaként is lehetnek. Például előfordulhat, hogy egy alkalmazás, amely számos szolgáltatás, és minden példány jelentések egy kérelmek másodpercenkénti (RPS) metrika. Ha egy szolgáltatás több erőforrást használ, mint egy másik szolgáltatás, a Service Fabric áthelyezi a szolgáltatáspéldányokat a fürt körül, hogy megpróbálja fenntartani a egyenletes erőforrás-kihasználtságot. Az erőforrás-kihasználtság működésének részletesebb magyarázatát az [Erőforrás-felhasználás és -betöltés kezelése a Service Fabric ben metrikákkal című témakörben ismerteti.](service-fabric-cluster-resource-manager-metrics.md)

A metrikák is segíthetnek betekintést nyújtani a szolgáltatás teljesítményébe. Idővel metrikák segítségével ellenőrizheti, hogy a szolgáltatás a várt paramétereken belül működik-e. Ha például a trendek azt mutatják, hogy hétfő reggel 9 órakor az átlagos RPS 1000, akkor beállíthat egy állapotjelentést, amely figyelmezteti, ha az RPS 500 vagy 1500 felett van. Minden lehet, hogy tökéletesen rendben van, de érdemes megnézni, hogy megbizonyosodjon arról, hogy az ügyfelek, amelyek egy nagy élmény. A szolgáltatás meghatározhatja a metrikák, amelyek állapot-ellenőrzési célokra jelenthető, de ez nem befolyásolja a fürt erőforrás-egyensúly. Ehhez állítsa a metrika vastagságát nullára. Azt javasoljuk, hogy az összes metrikát nullás súllyal kezdje el, és ne növelje a súlyt, amíg nem biztos abban, hogy tisztában van azzal, hogy a metrikák súlyozása hogyan befolyásolja a fürt erőforrás-kiegyensúlyozását.

> [!TIP]
> Ne használjon túl sok súlyozott mérőszámot. Nehéz lehet megérteni, hogy miért szolgáltatáspéldányok kerülnek áthelyezésre a kiegyensúlyozás. Néhány mutató sokat tehet!

Minden olyan információ, amely jelezheti az alkalmazás állapotát és teljesítményét, a mérőszámok és az állapotjelentések jelöltje. **A PROCESSZOR teljesítményszámlálója meg tudja mondani, hogyan használja a csomópontot, de nem mondja meg, hogy egy adott szolgáltatás kifogástalan állapotú-e, mert több szolgáltatás is előfordulhat, hogy egyetlen csomóponton fut.** De metrikák, például RPS, feldolgozott elemek, és a kérelem késés minden jelezheti az adott szolgáltatás állapotát.

## <a name="service-fabric-support-logs"></a>Service Fabric támogatási naplók

Ha az Azure Service Fabric-fürtdel kapcsolatban segítségért kell kapcsolatba lépnie a Microsoft támogatási szolgálatával, szinte mindig szükség van támogatási naplókra. Ha a fürt az Azure-ban van üzemeltetve, a támogatási naplók automatikusan konfigurálva vannak, és a fürt létrehozásának részeként gyűjtik össze. A naplók egy dedikált tárfiókban tárolódnak a fürt erőforráscsoportjában. A tárfiók nem rendelkezik rögzített névvel, de a fiókban blobtárolók és a *hálóval*kezdődő nevekkel rendelkező táblák jelennek meg. Az önálló fürt naplógyűjteményének beállításáról az [Önálló Azure Service Fabric-fürt létrehozása és](service-fabric-cluster-creation-for-windows-server.md) [konfigurációs beállításainak](service-fabric-cluster-manifest.md)létrehozása és kezelése című témakörben olvashat. Önálló Service Fabric-példányok esetén a naplókat egy helyi fájlmegosztásra kell küldeni. Ezeket a naplókat támogatási célokra **kell** megadnia, de azokat nem a Microsoft ügyfélszolgálati csapatán kívül bárki nek kell használhatónak tekintenie.

## <a name="measuring-performance"></a>Mérési teljesítmény

A fürt teljesítményének mérése segít megérteni, hogyan képes kezelni a terhelést, és hogyan tudja a fürt méretezésével kapcsolatos döntéseket (további információk a fürt [Azure-beli](service-fabric-cluster-scale-up-down.md)vagy [helyszíni méretezéséről).](service-fabric-cluster-windows-server-add-remove-nodes.md) A teljesítményadatok akkor is hasznosak, ha összehasonlítjuk az Ön vagy az alkalmazások és szolgáltatások által a jövőbeli naplók elemzésekor végrehajtott műveletekkel. 

A Service Fabric használata során összegyűjtő teljesítményszámlálók listáját a [Teljesítményszámlálók a Service Fabric-ben című témakörben található.](service-fabric-diagnostics-event-generation-perf.md)

Az alábbiakban két gyakori módon állíthatja be a fürt teljesítményadatok gyűjtését:

* **Ügynök használata**  
Ez az előnyben részesített módja a teljesítmény gyűjtése egy gépről, mivel az ügynökök általában rendelkezik egy listát a lehetséges teljesítmény mérőszámok, amelyek gyűjthetők, és ez egy viszonylag egyszerű folyamat, hogy válassza ki a metrikákat szeretne gyűjteni vagy módosítani. Az Azure Monitor szolgáltatásfigyelő naplók at Service Fabric [Azure Monitor naplók integráció](service-fabric-diagnostics-event-analysis-oms.md) és beállítása a Log [Analytics-ügynök,](../log-analytics/log-analytics-windows-agent.md) hogy többet tudjon meg a Log Analytics-ügynök, amely egy ilyen figyelési ügynök, amely képes felvenni a fürt virtuális gépek és üzembe helyezett tárolók teljesítményadatait.

* **Teljesítményszámlálók az Azure Table Storage-hoz**  
Teljesítménymutatókat is küldhet ugyanabba a táblatárolóba, mint az események. Ehhez módosítani kell az Azure Diagnostics konfigurációját, hogy a fürtben lévő virtuális gépekről vegye fel a megfelelő teljesítményszámlálókat, és lehetővé kell tennie, hogy a docker-statisztikákat vegye fel, ha tárolókat telepít. A teljesítményszámlálók konfigurálása a SERVICE Fabric [ben a WAD-ban](service-fabric-diagnostics-event-aggregation-wad.md) teljesítményszámláló-gyűjtemény beállításához.

## <a name="next-steps"></a>További lépések

* A Service Fabric [Azure Monitor naplóinak integrációja](service-fabric-diagnostics-event-analysis-oms.md) a fürtdiagnosztika összegyűjtéséhez, valamint egyéni lekérdezések és riasztások létrehozásához
* Ismerje meg a Service Fabric beépített diagnosztikai élményét, az [EventStore-t](service-fabric-diagnostics-eventstore.md)
* Néhány [gyakori diagnosztikai forgatókönyv végigjárása](service-fabric-diagnostics-common-scenarios.md) a Service Fabricben
