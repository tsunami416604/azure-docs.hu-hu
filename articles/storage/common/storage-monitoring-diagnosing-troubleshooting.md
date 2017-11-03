---
title: "Figyelése, diagnosztizálása és elhárítása az Azure Storage |} Microsoft Docs"
description: "Használni a szolgáltatásokat, például a tárolási analitika, az ügyféloldali naplózás és más külső eszközök azonosításához, diagnosztizálása és Azure tárolással kapcsolatos problémák elhárításához."
services: storage
documentationcenter: 
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: 1a9c9354b665294778886441cc6d7f02adb1163f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Áttekintés
Diagnosztizálása és elhárítása egy üzemeltetett felhőalapú környezetek elosztott alkalmazásban lévő lehet bonyolultabb, mint a hagyományos környezetekben. Alkalmazások PaaS vagy infrastruktúra-szolgáltatási infrastruktúra, a helyszíni, egy mobileszközön, vagy ezek valamilyen kombinációjára is telepíthető. Általában az alkalmazás hálózati forgalmat is haladnak át a nyilvános és titkos és az alkalmazás használhat több tárolási technológiák, például a Microsoft Azure Storage-táblákat, BLOB, üzenetsorok vagy fájlok mellett egyéb adatokat tárolja, például a relációs- és dokumentumszolgáltatások adatbázisok.

Az ilyen alkalmazásokat kezeléséhez sikeresen kell proaktív módon figyelheti azokat, és segít megérteni, hogyan diagnosztizálásához és hibaelhárításához őket, és a függő technológiák minden szempontját. Azure Storage szolgáltatás felhasználóit inkább folyamatosan figyelni a váratlan viselkedés (például a lassabb, mint a szokásos válaszidő) módosításai az alkalmazás által tárolási szolgáltatásokat, és használatát naplózási részletes adatok gyűjtéséhez és részletesen probléma elemzéséhez. A diagnosztika figyelés és naplózás beszerezte nyújt segítséget az alkalmazás észlelt a probléma okának megállapításához. Ezután hibaelhárítást, és határozza meg a megfelelő lépéseket, amelyekkel megoldásáról. Az Azure Storage alapszintű Azure-szolgáltatás, és az ügyfelek központi telepítése az Azure infrastruktúra-megoldások többsége fontos részét képezi. Az Azure Storage kínálja egyszerűbbé teheti a figyelése, diagnosztizálása és a felhőalapú alkalmazásokban tárolási problémák elhárításához.

> [!NOTE]
> Az Azure-fájlok nem támogatja a jelenleg naplózását.
> 

Gyakorlati végpont Azure Storage-alkalmazások hibáinak elhárításához, váltásról [-végpontok hibaelhárítása az Azure Storage Metrics és a naplózás, az AzCopy és a Message Analyzer segítségével](../storage-e2e-troubleshooting.md).

* [Bevezetés]
  * [Hogyan szerveződik, ez az útmutató]
* [a tárolás szolgáltatás figyelése]
  * [Figyelési szolgáltatásának állapota]
  * [Kapacitásának figyelése]
  * [Rendelkezésre állás figyelése]
  * [A teljesítmény figyelése]
* [tárolási problémák diagnosztizálása]
  * [Szolgáltatás ügynökállapottal kapcsolatos hibákkal]
  * [Teljesítménnyel kapcsolatos problémák]
  * [Hibák diagnosztizálása]
  * [Tárolási emulátor problémák]
  * [Tárolási naplózási eszközök]
  * [Hálózati naplózási eszközök használatával]
* [végpont nyomkövetés]
  * [Naplóadatok válaszoknak az összekapcsolása]
  * [Ügyfélkérelem-azonosító]
  * [Server Kérelemazonosító]
  * [Időbélyeg helyi időre]
* [hibaelhárítási útmutatás]
  * [metrika AverageE2ELatency magas és alacsony AverageServerLatency]
  * [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
  * [A mérőszámok magas AverageServerLatency értéket mutatnak]
  * [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]
  * [PercentThrottlingError metrika növelését]
  * [PercentTimeoutError metrika növelését]
  * [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]
  * [Az ügyfél HTTP 403 (tiltott) üzeneteket fogad]
  * [Az ügyfél HTTP 404-es (nem található) üzeneteket fogad]
  * [Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]
  * [metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére állapotú tranzakció]
  * [Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]
  * [Váratlan vesznek el virtuális gépeken, amelyek csatlakoztatott virtuális merevlemezek nagy számú tapasztal]
  * [A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]
  * [.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]
  * [Van egy másik probléma storage szolgáltatással]
  * [A Windows Azure fájlok problémák elhárítása](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [A Linux és Azure fájlok problémák elhárítása](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [mellékletek]
  * [1 függelék: HTTP és HTTPS-forgalom rögzítése a Fiddler használatával]
  * [2 függelék: Wireshark használó rögzítheti a hálózati forgalom]
  * [függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom]
  * [4. függelék: Segítségével Excel metrikák megtekintése és az adatok naplózása]
  * [5. függelék: A Visual Studio Team Services az Application insights szolgáltatással figyelését.]

## <a name="introduction"></a>Bevezetés
Ez az útmutató bemutatja, hogyan használja a szolgáltatások, például az Azure Storage Analytics, a bejelentkezés az Azure Storage ügyféloldali kódtárat, és más külső eszközök azonosításához, diagnosztizálása és elhárítása az Azure Storage ügyféloldali kapcsolatos hiba lépett fel.

![][1]

Ez az útmutató elsősorban az online szolgáltatások, Azure Storage szolgáltatásainak és az informatikai szakemberek felelős, ilyen online szolgáltatások kezelésére szolgáló használó fejlesztők által olvasandó készült. Ez az útmutató a céljai a következők:

* Segíti a állapotának és teljesítményének az Azure Storage-fiókokat.
* Biztosítja, hogy a szükséges eljárások és eszközök segítségével eldöntheti, hogy ha egy hiba vagy probléma merült fel a kérelmet az Azure Storage vonatkozik.
* Biztosítja, hogy végrehajthatóként útmutatással Azure tárolással kapcsolatos problémák megoldása érdekében.

### <a name="how-this-guide-is-organized"></a>Hogyan szerveződik, ez az útmutató
A szakasz "[a tárolás szolgáltatás figyelése]" állapotának és az Azure Storage szolgáltatások, Azure Storage Analytics Metrics (Storage Metrics) használatával teljesítményének figyelése.

A szakasz "[tárolási problémák diagnosztizálása]" Azure Storage Analytics-naplózás használata (tárhely-naplózás) eseményadatokat ismerteti. Emellett bemutatja, hogyan lehet a klienskódtárak segítségével egyikében létesítményekben az például a Storage ügyféloldali kódtára a .NET vagy az Azure SDK for Java ügyféloldali naplózás engedélyezéséhez.

A szakasz "[végpont nyomkövetés]" ismerteti, hogyan hozhatók a különböző naplófájlokat és metrikai adatok tárolt adatokhoz.

A szakasz "[hibaelhárítási útmutatás]" nyújt hibaelhárítási útmutatót, néhány tárolással kapcsolatos gyakoribb kapcsolatos probléma, akkor léphetnek fel.

A "[mellékletek]" egyéb eszközzel, Wireshark vagy netmon eszközzel elemzése hálózati csomagadatok, a Fiddler a HTTP/HTTPS-üzenetek, elemzése és a Microsoft Message Analyzert használatával történik az adatok naplózása információkat tartalmaznak.

## <a name="monitoring-your-storage-service"></a>A storage szolgáltatás figyelése
Ha ismeri a Windows Teljesítményfigyelő, tulajdonképpen Storage Metrics, hogy a Windows Teljesítményfigyelő-számlálókból egy Azure Storage megfelelője. Storage Metrics található széles választékát metrikák (számlálók a Windows Teljesítményfigyelő terminológia) például a szolgáltatás rendelkezésre állása, a szolgáltatási kérelmek teljes száma, vagy a szolgáltatáshoz tartozó sikeres kérelmek aránya. Az elérhető mérőszámok teljes listáját lásd: [Storage Analytics metrikák táblaséma](http://msdn.microsoft.com/library/azure/hh343264.aspx). Megadhatja, hogy kívánja-e a tárolás szolgáltatás begyűjtése és összesítése metrikák minden órában vagy percben. Engedélyezheti őket, és figyelheti a storage-fiókok kapcsolatos további információkért lásd: [storage mérőszámainak engedélyezése és megtekintése a metrikai adatok](http://go.microsoft.com/fwlink/?LinkId=510865).

Választhat, hogy mely óránkénti meg szeretne jeleníteni a metrikák a [Azure-portálon](https://portal.azure.com) és értesíthetők a rendszergazdák e-mailben, amikor egy óránkénti mérőszám eléri az adott szabályok konfigurálása. További információkért lásd: [riasztási értesítéseket kapni](/azure/monitoring-and-diagnostics/monitoring-overview-alerts.md). 

A tároló szolgáltatás segítségével a lehető legkedvezőbb metrikákat gyűjtő, de nem rögzíthet minden tárolási műveletet.

Az Azure portálon például a rendelkezésre állási, kérelmek teljes száma és a tárfiókon átlagos várakozási számok metrikák tekintheti meg. Egy értesítési szabályt is beállítva a rendszergazda riasztást küld, ha rendelkezésre állási bizonyos szintű alá süllyed. Megtekinteni az adatokat, hogy meg lehessen vizsgálni egy lehetséges terület a table szolgáltatás sikeres arány nem érik el a 100 %-os (további információkért tekintse meg a szakasz "[metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére állapotú tranzakció]").

Az Azure-alkalmazások, azok kifogástalan és teljesítő által várt folyamatosan figyelje:

* Néhány alkalmazást, amely lehetővé teszi a baseline metrikáját létrehozó összehasonlíthatja az aktuális adatok, és az Azure storage és az alkalmazás viselkedését jelentős módosításai azonosítása. Az alapkonfiguráció mérőszámok értékeit, sok esetben adott alkalmazást, és ki kell alakítania őket, amikor az alkalmazás teljesítménytesztelési.
* Percenkénti metrikákat rögzítése, és használja azokat aktívan figyelésére váratlan hibák és rendellenességek észlelését, például a teljesítményt a hiba száma, vagy kérje meg díjszabás.
* Óránkénti metrikák rögzítése segítségével figyelheti, mint átlagos értékek átlagos hiba száma és igényléséhez díjszabás.
* Diagnosztikai eszközök használatával később a szakaszban bemutatott lehetséges problémákat vizsgálja "[tárolási problémák diagnosztizálása]."

Az alábbi ábrán a diagramok bemutatják, hogyan átlagolási óránkénti metrikáihoz előforduló elrejthetők igényeiben jelentkező tevékenységben. Az óránkénti metrikák jelennek meg kérelmek, állandó száma a metrikák azt mutatják történnek valóban ingadozásokat perc során.

![][3]

Ez a szakasz többi ismerteti, milyen, célszerű figyelemmel kísérni metrikák és okát.

### <a name="monitoring-service-health"></a>Figyelési szolgáltatásának állapota
Használhatja a [Azure-portálon](https://portal.azure.com) a tároló szolgáltatás (és más Azure-szolgáltatások) állapotának megtekintéséhez a világ minden Azure régióban. Ez lehetővé teszi, hogy azonnal láthatja, ha a vezérlőn kívül problémát érinti a társzolgáltatás használhatja az alkalmazás a régióban.

A [Azure-portálon](https://portal.azure.com) is biztosít a különböző Azure-szolgáltatások érintő incidensek értesítések.
Megjegyzés: Ez az információ korábban rendelkezésre állt, jelszavat és az előzmények, a a [Azure szolgáltatás irányítópultját](http://status.azure.com).

Amíg a [Azure-portálon](https://portal.azure.com) egészségügyi adatokat gyűjt a (belső kibővített figyelés), az Azure adatközpontjaiban belül is érdemes lehet egy kívül a módszert, amelyet rendszeres időközönként érni az Azure által üzemeltetett webalkalmazás több helyről szintetikus tranzakciók létrehozására bevezetése. A szolgáltatás által kínált [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) és az Application Insights for Visual Studio Team Services példák a külső hátránya. A Visual Studio Team Services Application insights szolgáltatással kapcsolatos további információkért lásd: a függelék: "[5 függelék: figyelés az Application Insights for Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Kapacitásának figyelése
Storage mérőszámainak csak tárolja a teljesítmény-mérőszámait a blob szolgáltatás, mert a blobok általában fiók tárolt adatok legnagyobb részét (a írásának időpontjában, nincs lehetőség Storage Metrics a figyelheti a táblák és a várólisták). Ezeket az adatokat a megtalálhatja a **$MetricsCapacityBlob** tábla, ha engedélyezte a Blob szolgáltatás figyelését. Storage mérőszámainak rögzíti az adatokat naponta egyszer, és használhatja a értékének a **RowKey** annak meghatározásához, hogy a sor tartalmaz-e olyan entitás, amely kapcsolódik a felhasználói adatok (érték **adatok**) vagy analitikai adatok (érték **analytics**). Minden tárolt entitás tárolókapacitást használt információkat tartalmaz (**kapacitás** bájtban mért) és a tárolók száma (**ContainerCount**) és a blobok (**ObjectCount**) a tárfiókot használja. További információ a kapacitási mérőszámokat tárolja a **$MetricsCapacityBlob** táblázatban, lásd: [Storage Analytics metrikák táblaséma](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Ezeket az értékeket, hogy a tárfiók kapacitás határain hamarosan eléri a korai figyelmeztetéseket, célszerű figyelemmel kísérni. Az Azure-portálon adhat hozzá a riasztási szabályok, amelyek értesítést küldenek, ha az összesített tárolóhely-használat meghaladja, vagy a megadott küszöbérték alá esik.
> 
> 

A bináris objektumok, például különféle tárolási objektumok méretének becslése talál segítséget a következő blogbejegyzésben található [az Azure Storage számlázási – sávszélesség, a tranzakciók és a kapacitás](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Rendelkezésre állás figyelése
Figyelje a tárolási szolgáltatások rendelkezésre állását a tárfiókban lévő által az érték a **rendelkezésre állási** az óránként vagy percenkénti metrikákat táblázatok oszlopát – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A **rendelkezésre állási** oszlop egy százalékos értéket tartalmaz, amely a szolgáltatás vagy az API-művelet a sor által képviselt rendelkezésre állását mutatja (a **RowKey** jeleníti meg, ha a sor tartalmaz-e a szolgáltatás egészére vagy egy adott API-művelet metrikák).

Bármely érték, amely kisebb, mint 100 %-os azt jelzi, hogy bizonyos tárolási kérelmeket nem működnek. Láthatja, hogy miért ezek nem működnek a metrikák adatok, amelyek különböző hiba típusú kérések számát, mint a többi oszlop megvizsgálásával **ServerTimeoutError**. Lásd: várható **rendelkezésre állási** ősszel ideiglenesen 100 % okokból közben a szolgáltatás átmeneti server időtúllépések alatt áthelyezése partíciók jobb terheléselosztásához kérelem; az ügyfélalkalmazás az újrapróbálkozási logika kezelnie kell az ilyen időszakos feltételek. A cikk [Storage Analytics naplózott műveletekkel és az állapotüzenetek](http://msdn.microsoft.com/library/azure/hh343260.aspx) Storage Metrics tartalmaz a tranzakció-típusok listája a **rendelkezésre állási** számítási.

A a [Azure-portálon](https://portal.azure.com), amelyek értesítést küldenek, ha a riasztási szabályok is hozzáadhat **rendelkezésre állási** a szolgáltatás a megadott küszöbérték alá esik.

A "[hibaelhárítási útmutatás]" című szakaszában talál néhány gyakori tárolási szolgáltatás problémákat elérhetőségének ismerteti.

### <a name="monitoring-performance"></a>A teljesítmény figyelése
A tárolási szolgáltatások teljesítményének figyeléséhez az óránkénti és percenkénti metrikákat tábla a következő mérőszámokat is használhat.

* Az értékek a **AverageE2ELatency** és **AverageServerLatency** oszlopaiban végrehajtásához szükséges átlagos idő a társzolgáltatás vagy API művelet típusa tart kérelmek feldolgozásához. **AverageE2ELatency** azt méri, végpontok közötti késés, amely tartalmazza az olvasni a kérelmet, és a kérelem feldolgozásához szükséges idő mellett küldés szükséges idő (ezért tartalmazza a hálózati késés, ha a kérelem eléri a társzolgáltatás); **AverageServerLatency** biztosítása csak a feldolgozási idő, és ezért nem tartalmazza az összes hálózati késés kommunikál az ügyféllel kapcsolatos. Című témakör "[metrika AverageE2ELatency magas és alacsony AverageServerLatency]" az útmutató információt a miért lehet jelentős különbség a két érték között.
* Az értékek a **TotalIngress** és **TotalEgress** oszlopok megjelenítése a teljes adatmennyiség bájtban érkező, és ezzel kívül a társzolgáltatás vagy a megadott API-művelet típusra.
* Az értékek a **TotalRequests** oszlop megjelenítése a társzolgáltatás API művelet fogadó kérelmek teljes száma. **TotalRequests** , amely megkapja a társzolgáltatás kérelmek teljes száma.

Általában fogja figyelni a ezek bármelyike váratlan változásokat, azt jelzi, hogy rendelkezik-e a hibát, amely vizsgálatot igényel.

Az a [Azure-portálon](https://portal.azure.com), amelyek értesítést küldenek, ha bármelyik ezt a szolgáltatást a metrikák alá esnek, vagy meghaladnia a küszöbértéket, melyet a riasztási szabályok is hozzáadhat.

A "[hibaelhárítási útmutatás]" című szakaszában talál néhány gyakori tárolási szolgáltatás problémákat a teljesítménnyel kapcsolatos ismerteti.

## <a name="diagnosing-storage-issues"></a>Tárolási problémák diagnosztizálása
Számos módon, hogy előfordulhat, hogy tudomást hiba vagy probléma a alkalmazás is van, ezek közé tartoznak:

* Súlyos hiba, amely az alkalmazás összeomlik vagy nem működik.
* Az eredeti értékeket az előző szakaszban leírtak szerint figyeli a metrikák a jelentős változtatások "[a tárolás szolgáltatás figyelése]."
* A felhasználók az alkalmazás, amely egy adott művelet nem fejeződött be, várt módon jelentések vagy, hogy néhány szolgáltatás nem működik.
* Hibák keletkeztek az alkalmazáson belül, amely jelennek meg a naplófájlokban vagy néhány egyéb értesítési metódussal.

Általában az Azure storage szolgáltatások kapcsolatos problémák egyikébe négy kategóriába sorolhatók:

* Az alkalmazásnak a teljesítménybeli problémát, vagy a felhasználók által jelentett, vagy a metrikák változása látható.
* A probléma az Azure Storage-infrastruktúra egy vagy több régióban van.
* Az alkalmazás hibás, vagy a felhasználók által jelentett, vagy a figyelheti hiba száma metrikák egyik növekedése által feltárt léptek fel.
* Fejlesztési és tesztelési során előfordulhat, hogy használni a helyi storage emulator; a storage emulator használata kapcsolódik néhány problémák merülhetnek fel.

Az alábbi szakaszok felsorolják a követendő lépések diagnosztizálásához és hibaelhárításához minden ilyen négy kapcsolatos hibákat. A szakasz "[hibaelhárítási útmutatás]" Ez az útmutató későbbi részletgazdagabb a gyakori problémákat tapasztalhat.

### <a name="service-health-issues"></a>Szolgáltatás ügynökállapottal kapcsolatos hibákkal
Szolgáltatás állapotával kapcsolatos problémák jellemzően a vezérlőn kívül. A [Azure-portálon](https://portal.azure.com) Azure szolgáltatásokkal, beleértve a tárolási szolgáltatások folyamatos problémákkal kapcsolatos információkat biztosít. Választotta írásvédett Georedundáns tárolás a tárfiók létrehozása után, ha az elsődleges helyen nem érhető el az adatok esetén az alkalmazás sikerült váltson ideiglenesen az írásvédett másolatát a másodlagos helyen. Ehhez az szükséges, az alkalmazás kell váltani az elsődleges és másodlagos tárolóhelyek használata között, és lehet csak olvasható adatok a csökkentett módban való működésre. Az Azure Storage ügyfélkódtáraival engedélyezi annak meghatározását, amely is olvasható másodlagos tárterületre abban az esetben, ha egy elsődleges tárolási való olvasás sikertelen újrapróbálkozási házirendje. Az alkalmazás is ismernie kell, hogy idővel konzisztenssé a másodlagos hely adatai. További információkért lásd a következő blogbejegyzésben [Azure tárolási redundancia lehetőségek és az írásvédett Georedundáns redundáns tárolás](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Teljesítménnyel kapcsolatos problémák
Egy alkalmazás teljesítményének megítélése szubjektív lehet, főképp a felhasználó szemszögéből. Ezért fontos, hogy rendelkezzen olyan alapmértékekkel, amelyek segíthetnek a teljesítménnyel kapcsolatos problémák azonosításában. Számos tényező befolyásolhatja egy Azure storage szolgáltatás alkalmazás ügyfelek szemszögéből teljesítményére. Ezek a tényezők a társzolgáltatás, az ügyfél vagy a hálózati infrastruktúra; előfordulhat, hogy működik. Ezért fontos egy olyan stratégia, azonosítsa a felhasználót a teljesítménycsökkenés oka forrása.

Miután azonosította a teljesítménycsökkenés oka a metrikák a oka valószínűleg helyét, majd használhatja a naplófájlok diagnosztizálásához és a probléma további hibaelhárításához kapcsolatos részletes információk.

A szakasz "[hibaelhárítási útmutatás]" kapcsolatos néhány általános teljesítmény kapcsolatban, hogy ez az útmutató későbbi biztosít találkozhat.

### <a name="diagnosing-errors"></a>Hibák diagnosztizálása
Az alkalmazás felhasználók is értesíti az ügyfélalkalmazás által jelentett hibákat. Storage mérőszámainak rögzíti számát a tárolási szolgáltatások különböző típusok is, mint **NetworkError**, **ClientTimeoutError**, vagy **AuthorizationError**. Storage Metrics csak számát is különböző típusok rögzíti, miközben szerezhet be további részletes információkat az egyes kérelmek kiszolgálóoldali, az ügyféloldali és hálózati naplók megvizsgálásával. A tároló szolgáltatás által visszaadott HTTP-állapotkód rendszerében általában arra utal, hogy a kérelem sikertelenségének okát.

> [!NOTE]
> Ne feledje, hogy Ön látnia néhány átmeneti hiba: például átmeneti hálózati körülmények miatti hibák, vagy az alkalmazáshibák.
> 
> 

A következő erőforrások hasznosak tárolással kapcsolatos állapotának és kódjainak:

* [Közös REST API hibakódok](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [BLOB szolgáltatás hibakódok](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Várólista hibakódjai](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Tábla hibakódjai](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Fájl hibakódjai](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Tárolási emulátor problémák
Az Azure SDK magában foglalja a storage emulatort, egy fejlesztő munkaállomás futtathatja. Az emulátor szimulálja az Azure storage szolgáltatásainak viselkedését a legtöbb, és hasznos során fejlesztési és tesztelési, így lehetővé teszi a Azure-előfizetés és Azure storage-fiók nélkül az Azure storage-szolgáltatásokat használó alkalmazások futtatásához.

A "[hibaelhárítási útmutatás]" című szakaszában talál néhány gyakori problémát észlelt a storage emulator használatával ismerteti.

### <a name="storage-logging-tools"></a>Tárolási naplózási eszközök
Tároló-naplózás biztosítja a kiszolgálóoldali naplózása tárolási kérelmeket az Azure-tárfiókot. Kiszolgálóoldali naplózás engedélyezése és az adatok elérését az napló kapcsolatos további információkért lásd: [tárolási naplózás engedélyezése és használata naplóadatok](http://go.microsoft.com/fwlink/?LinkId=510867).

A Storage ügyféloldali kódtára a .NET ügyféloldali napló adatokat gyűjthet, amelyek az alkalmazás által végrehajtott tárolási műveletek vonatkozik teszi lehetővé. További információkért lásd: [ügyféloldali naplózás a .NET a Storage ügyféloldali kódtára](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> Bizonyos esetekben (például a SAS-hitelesítési hibák) a felhasználó, amelynek nincs kérelem adatai az található a kiszolgálóoldali tárolási naplófájljai hiba előfordulhat, hogy jelentést. A naplózási képességeket a Storage ügyféloldali kódtár segítségével vizsgálja meg, ha a probléma oka az, az ügyfélen, vagy vizsgálja meg a hálózati Hálózatfigyelő eszközök segítségével.
> 
> 

### <a name="using-network-logging-tools"></a>Hálózati naplózási eszközök használatával
Részletes információkat szolgáltatnak az adatok ügyfélen és kiszolgálón cseréjét, és a mögöttes hálózati feltételek mellett az ügyfél és kiszolgáló közötti forgalmat is rögzítheti. Hasznos hálózati naplózási eszközök a következők:

* [Fiddler](http://www.telerik.com/fiddler) egy szabad webalkalmazás-proxy, amely lehetővé teszi, hogy ellenőrizze a fejlécek és a HTTP és HTTPS kérés- és üzenetek adattartalom hibakeresés. További információkért lásd: [1. függelékében: Fiddler használatával a HTTP és HTTPS-forgalom rögzítésére](#appendix-1).
* [Microsoft Hálózatfigyelő (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) és [Wireshark](http://www.wireshark.org/) vannak szabad hálózati protokoll lekérdezések, amelyek lehetővé teszik a hálózati protokollok széles köre csomag részletes információk megtekintéséhez. Wireshark kapcsolatos további információkért lásd: "[2. függelékben: Wireshark használó rögzítheti a hálózati forgalom](#appendix-2)".
* Microsoft Message Analyzert egy olyan eszköz, a Microsoft írja felül a Netmon, és hogy a hálózati csomag adatrögzítés mellett, megtekintése és elemzése a naplóadatok rögzítése más eszközök segítségével. További információkért lásd: "[függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom](#appendix-3)".
* Ha azt szeretné, a hálózati kapcsolat ellenőrzéséhez ellenőrizze, hogy az ügyfélszámítógép csatlakozhat az Azure storage szolgáltatást a hálózaton keresztül, nem ehhez a szabványos **ping** eszköz az ügyfélen. Azonban használhatja a [ **tcping** eszköz](http://www.elifulkerson.com/projects/tcping.php) kapcsolat ellenőrzése.

Sok esetben a naplóadatait tárolási naplózás és a Storage ügyféloldali kódtár elegendőek a probléma diagnosztizálásához, de bizonyos helyzetekben szükség lehet a további tájékoztatáshoz, amely a hálózati naplózási eszközök nyújt. Például a HTTP és HTTPS üzenetek megtekintése a Fiddler segítségével lehetővé teszi fejlécének és adattartalmának bontása, és a tárolási szolgáltatások, amelyek lehetővé teszik, hogy vizsgálja meg, hogyan ügyfélalkalmazás újrapróbálja tárolási műveletek onnan küldött adatok megtekintéséhez. Protokoll elemzőkkel Wireshark például a csomag szintjén teszi TCP-adatok, amelyek lehetővé teszik, hogy elveszett csomagok és a problémák elhárítása engedélyezése fog működni. Az Üzenetelemző HTTP és a TCP rétegek is működnek.

## <a name="end-to-end-tracing"></a>Végpont nyomkövetés
Végpont nyomkövetési naplófájlok számos használatát egy olyan hasznos eljárás kapcsolatos lehetséges problémákat vizsgálja. Is használhatja a dátum/idő adatokat a metrikák adatokból utalhat, hogy hol keressen a részletes információkat, amelyek segítenek a probléma megoldásához a rendszernapló fájljaiban.

### <a name="correlating-log-data"></a>Naplóadatok válaszoknak az összekapcsolása
Az ügyfélalkalmazásokból napló megtekintésekor hálózati követi, és nagyon fontos tudni összefüggéseket a kiszolgálóoldali tárolási naplózási keresztül a különböző naplófájlokat kéri. A napló-fájlok közé tartoznak a különböző mezők, melyek hasznosak lehetnek a korrelációs azonosítóként számos. Az ügyfél-azonosító a különböző naplókat bejegyzések összefüggéseket leghasznosabb mezőt. Egyes esetekben azonban hasznos lehet a kiszolgáló Kérelemazonosító vagy a időbélyegeket használja. A következő szakaszokban ezekről a beállításokról további részleteket.

### <a name="client-request-id"></a>Ügyfélkérelem-azonosító
A Storage ügyféloldali kódtár automatikusan hoz létre minden egyes kérés egyedi ügyfélkérelem-azonosító.

* Az ügyféloldali naplóban, amely a Storage ügyféloldali kódtár hoz létre, az ügyfél-azonosító szerepel a **ügyfélkérelem-azonosító** mezőjét, minden naplóbejegyzés kapcsolódó a kérést.
* A Fiddler rögzített például egy hálózati nyomkövetést, az ügyfél-Azonosítót is elérhetővé válik a kérelemüzenetek, mint a **x-ms-client-request-id** HTTP-fejléc értéke.
* A kiszolgálóoldali tárolási naplózás naplóban az ügyfélkérelem-azonosító az ügyfél kérelem azonosító oszlopban jelenik meg.

> [!NOTE]
> A többszörös kéréseket megoszthatók az azonos ügyfél-Azonosítót, mivel az ügyfelet hozzá lehet rendelni ezt az értéket (bár a Storage ügyféloldali kódtár automatikusan hozzárendeli az új érték) lehetőség. Esetén az ügyfél újrapróbálkozások bármilyen kísérlet megosztani az ügyfél kérelem megegyezik. Egy kötegben, az ügyfél által küldött, ha a kötegelt az egyetlen ügyfél kérelem azonosítóval rendelkezik.
> 
> 

### <a name="server-request-id"></a>Kiszolgáló azonosítója
A tároló szolgáltatás automatikusan létrehozza a kérelem kiszolgálóazonosítók.

* A kiszolgáló Kérelemazonosító jelenik meg a kiszolgálóoldali tárolási naplózás napló a **Kérelemazonosító fejléc** oszlop.
* A Fiddler rögzített például egy hálózati nyomkövetést, a kiszolgáló Kérelemazonosító válaszüzenetek, megjelenik a **az x-ms-request-id** HTTP-fejléc értéke.
* Az ügyféloldali naplóban, amely a Storage ügyféloldali kódtár hoz létre, a kiszolgáló Kérelemazonosító megjelenik a **művelet szöveg** a naplóbejegyzést, a kiszolgáló válaszára részleteit megjelenítő oszlopában.

> [!NOTE]
> A tároló szolgáltatás mindig egyedi kiszolgálói kérelem Azonosítót rendel minden kérést kap, így az ügyfél minden újrapróbálkozások és minden műveletet a kötegben szereplő egy egyedi kiszolgálói kérelem.
> 
> 

Ha a Storage ügyféloldali kódtár jelez a **StorageException** az ügyfél a **RequestInformation** tulajdonsága tartalmazza egy **RequestResult** objektum, amely tartalmazza egy **ServiceRequestID** tulajdonság. Emellett egy **RequestResult** objektum egy **OperationContext** példány.

Az alábbi példakód bemutatja, hogyan kell beállítani egy egyéni **ClientRequestId** csatolásával érték egy **OperationContext** a kérést a társzolgáltatás objektum. Azt is bemutatja, hogyan lehet lekérni a **ServerRequestId** értéket a válaszüzenetben.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Időbélyeg helyi időre
Időbélyeg helyi időre segítségével keresse meg a kapcsolódó naplóbejegyzések, de legyen óvatos, ha bármely óraeltérés között az ügyfél és a kiszolgálóra, lehetséges, hogy létezik. Plusz vagy mínusz 15 percenként megfelelő kiszolgálóoldali bejegyzések az ügyfélen időbélyeg alapján kell keresni. Ne feledje, hogy a blobok metrikák tartalmazó blob metaadatai az időtartományt a metrikáihoz tárolja a blob; Ez akkor hasznos, ha sok metrikák blobok ugyanazon perc vagy óra.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató
Ez a szakasz segítséget az elemzés céljából, és az Azure storage szolgáltatásainak használata során felmerülő néhány gyakori problémák elhárítása az alkalmazás. Az alábbi lista segítségével keresse meg az adott problémára vonatkozó információt.

**Hibaelhárítási döntési fája**

---
A probléma kapcsolódik a teljesítmény, a tárolási szolgáltatások közül az egyik?

* [metrika AverageE2ELatency magas és alacsony AverageServerLatency]
* [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
* [A mérőszámok magas AverageServerLatency értéket mutatnak]
* [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]

---
A probléma kapcsolódik a tárolási szolgáltatások közül az egyik rendelkezésre állásának?

* [PercentThrottlingError metrika növelését]
* [PercentTimeoutError metrika növelését]
* [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]

---
 Az ügyfélalkalmazás fogadja-e egy HTTP-válaszok aránya (például a 404-es) 4XX egy társzolgáltatás?

* [Az ügyfél HTTP 403 (tiltott) üzeneteket fogad]
* [Az ügyfél HTTP 404-es (nem található) üzeneteket fogad]
* [Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]

---
[metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére állapotú tranzakció]

---
[Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]

---
[Váratlan vesznek el virtuális gépeken, amelyek csatlakoztatott virtuális merevlemezek nagy számú tapasztal]

---
[A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]

---
[.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]

---
[Van egy másik probléma storage szolgáltatással]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrikák AverageE2ELatency magas és alacsony AverageServerLatency megjelenítése
Az alábbi ábrán látható a [Azure-portálon](https://portal.azure.com) eszköz figyelési azt szemlélteti, ahol a **AverageE2ELatency** jelentősen nagyobb, mint a **AverageServerLatency**.

![][4]

Vegye figyelembe, hogy a társzolgáltatás csak kiszámítja a metrika **AverageE2ELatency** a sikeres kéréseket, és eltérően **AverageServerLatency**, az ügyfél az adatok küldésére és fogadására a nyugtázás a tárolószolgáltatásból idejét tartalmazza. Ezért közötti különbségek **AverageE2ELatency** és **AverageServerLatency** miatt lassú válaszolni az ügyfélalkalmazás vagy a hálózaton feltételek miatt.

> [!NOTE]
> Megtekintheti továbbá **E2ELatency** és **ServerLatency** egyéni tárolószolgáltatások művelethez a tárolási naplózási adatok naplózása.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Ügyfél teljesítménnyel kapcsolatos problémák vizsgálatában
Az ügyfél lassan válaszol a lehetséges okok közé tartoznak, használjon korlátozott mennyiségű rendelkezésre álló kapcsolatok vagy -szál, vagy éppen kevés az erőforrás, például a Processzor, a memória vagy a hálózati sávszélesség. Előfordulhat, hogy a probléma megoldásához, hatékonyabb (például a használatával a társzolgáltatás aszinkron hívások) ügyfél kódjának a módosítása, vagy a nagyobb virtuális gépek (a több maggal és memória) használatával.

A tábla- és várólista szolgáltatások esetén a Nagle algoritmus is eredményezheti, hogy magas **AverageE2ELatency** képest történő **AverageServerLatency**: további információk: a feladás egy vagy több [Nagle tartozó algoritmus nem rövid kis kérelmek felé](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Letilthatja a kódban Nagle algoritmus használatával a **ServicePointManager** osztályt a **System.NET névtérbeli** névtér. Akkor tegye ezt meg semmilyen hívásokat a táblázathoz, vagy nyissa meg a várólista-szolgáltatás az alkalmazásban, mert ez nem befolyásolja a már kapcsolatok előtt. Az alábbi példa származik a **Application_Start** a feldolgozói szerepkör metódust.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Ellenőrizni kell az ügyféloldali naplójában hány kérelmeket küld az ügyfélalkalmazást, és ellenőrizze a általános .NET-hez kapcsolódó szűk keresztmetszetek a ügyfélprogram, pl. a CPU, a .NET szemétgyűjtés, a hálózat kihasználtságának vagy a memória. Kiindulási pontként .NET ügyfélalkalmazások hibaelhárításhoz, lásd: [profilkészítési, hibakeresés és nyomkövetés](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Hálózati késési problémák kivizsgálása
A hálózat által okozott magas végpontok közötti késés általában átmeneti feltételek miatt. Mindkét ideiglenes és állandó hálózati problémák, például az eldobott csomagok használatával megvizsgálhatja például Wireshark vagy a Microsoft Message Analyzert eszközök segítségével.

Hálózati probléma elhárításához Wireshark használatával kapcsolatos további információkért lásd: "[2 függelék: Wireshark használó rögzítheti a hálózati forgalom]."

Microsoft Message Analyzert hálózati probléma elhárításához használatával kapcsolatos további információkért lásd: "[függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrika alacsony AverageE2ELatency és alacsony AverageServerLatency, de az ügyfél nagy késleltetésű tapasztal
Ebben a forgatókönyvben az a legvalószínűbb ok az a tárolási kérelmeket közel jár a társzolgáltatás késleltetése. Ki kell vizsgálni, hogy miért az ügyfél kéréseit nem létesített keresztül, a blob szolgáltatás.

Késlelteti a kérelmek küldését az ügyfél egyik lehetséges oka az, hogy vannak-e rendelkezésre álló kapcsolatok vagy szálak korlátozott számú.

Emellett ellenőrizze, hogy az ügyfél több újrapróbálkozások hajt végre, és vizsgálja meg az oka, hogy ez a helyzet. Annak megállapításához, hogy az ügyfél több újrapróbálkozások hajt végre, a következőket teheti:

* Ellenőrizze a tárolási analitika eseménynaplóit. Ha több próbálkozás van folyamatban, látni fogja a több műveletet a azonos ügyfélkérelem-azonosító, de különböző kiszolgálói kérelem azonosítók.
* Tanulmányozza az ügyfél. A részletes naplózás tájékoztatja arról, hogy újbóli kísérlet történt.
* A kód hibakereséséhez, és ellenőrizze a tulajdonságait a **OperationContext** a kérelemhez társított objektumot. Ha a rendszer megpróbálja újból végrehajtani a műveletet a rendelkezik, a **RequestResults** tulajdonság több egyedi kiszolgálói kérelem azonosítókat tartalmazza. Ellenőrizheti az egyes kérelmek kezdő és befejező időpontja. További információkért lásd: a kódminta szakaszában [Server Kérelemazonosító].

Ha az ügyfél nincs probléma, ki kell vizsgálni a lehetséges hálózati problémák, például a csomagveszteség. Eszközök, például Wireshark vagy a Microsoft Message Analyzer segítségével vizsgálja meg a hálózati problémák.

Hálózati probléma elhárításához Wireshark használatával kapcsolatos további információkért lásd: "[2 függelék: Wireshark használó rögzítheti a hálózati forgalom]."

Microsoft Message Analyzert hálózati probléma elhárításához használatával kapcsolatos további információkért lásd: "[függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metrika magas AverageServerLatency
Ha nagy **AverageServerLatency** blob letöltési kérelmek, érdemes használni a tárolási naplózási naplókat, ha van-e az azonos blob (vagy bináris objektumok) ismétlődő kérelmek. A blob vonatkozó kérések feltöltésére, ki kell vizsgálni, milyen blokk méretét az ügyfél által használt (például blokkolja a kisebb, mint 64 KB méretű eredményezhet terhek kivéve, ha az olvasási műveletek is a kisebb, mint 64K adattömbök), és ha több ügyfél blokkok feltölteni a párhuzamos azonos blobba. Azt is ellenőrizze igényeiben jelentkező kéréseket eredményező meghaladó számú perc metrikáját a egy második méretezhetőségi célok: is láthatja, hogy "[PercentTimeoutError metrika növelését]."

Ha magas **AverageServerLatency** blob letölthető kérelmek ismételt hiba van a kérelmek az azonos blob vagy blobok, akkor érdemes gyorsítótárazása a blobok, Azure Cache vagy az Azure Content Delivery Network (CDN) használatával. A feltöltési kérés kapcsolódik növelheti az átviteli sebesség a nagyobb blokkméret használatával. A lekérdezéseket akkor is valósíthat meg ügyféloldali gyorsítótárazás az ügyfeleken, hogy a lekérdezés ugyanezen műveletek végrehajtásához, és ha az adatok nem módosulnak.

Magas **AverageServerLatency** értékek rosszul megtervezett táblát vagy lekérdezést, hogy a Keresés eredménye, vagy hajtsa végre a víruskereső append/illesztenie mintát tünete is lehet. Lásd: "[PercentThrottlingError metrika növelését]" További információ.

> [!NOTE]
> Egy átfogó ellenőrzőlista teljesítmény ellenőrzőlistát itt találja: [Microsoft Azure tárolási teljesítmény és méretezhetőség ellenőrzőlista](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>A várólista üzenetkézbesítést váratlan késést tapasztal
Ha a kérelmet ad hozzá egy üzenetet, amely egy várólista és az idő olvasása az üzenetsorból való elérhetővé válik késleltetés tapasztalja, majd kell venni a probléma diagnosztizálása érdekében az alábbi lépéseket:

* Ellenőrizze, hogy az alkalmazás sikeresen hozzáadásával az üzenetek várólistára. Ellenőrizze, hogy az alkalmazás nem újra megpróbálja a **AddMessage** metódus többször előtt sikeresen lezajlottak. A Storage ügyféloldali kódtár naplók bármely, a tárolási műveletek miatti ismételt próbálkozás jeleníti meg.
* Ellenőrizze, nincs óra eltérésére között a feldolgozói szerepkör, amely az üzenet a várólistában van, és a feldolgozói szerepkör, amely kiolvassa az üzenetet az üzenetsorból, amely megkönnyíti a jelenik meg, mintha késik a feldolgozása.
* Ellenőrizze, hogy ha a feldolgozói szerepkör, amely olvassa be az üzenetek a várólista nem működik. Ha a várólista-ügyfél a **GetMessage** metódust, de nem tudja válaszolni nyugtázást, az üzenet nem látható, amíg a várólista marad a **invisibilityTimeout** időszakának lejártáig. Ezen a ponton az üzenet feldolgozásához újra elérhetővé válik.
* Ellenőrizze, hogy ha a várólista hossza nő adott idő alatt. Ez akkor fordulhat elő, ha nem rendelkezik elegendő munkavállalók minden más munkavállalók helyez várakozási üzenetek feldolgozásához használható. Azt is ellenőrizze a mérni kívánt láthatja Ha törlési kéréseket sikertelen és a dequeue üzeneteken, ami azt jelezheti ismétlődő sikertelen kísérlet történt az üzenet törlése.
* Vizsgálja meg a tárolási naplózás keresse meg a várólista-műveleteket, amelyek rendelkeznek a vártnál nagyobb **E2ELatency** és **ServerLatency** értékek idő szokásosnál hosszabb időszakra.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>PercentThrottlingError metrika növelését
Sávszélesség-szabályozási hiba történik, amikor a túllépi a méretezhetőségi célok tároló szolgáltatás. A tároló szolgáltatás ezt a végzi annak érdekében, hogy nincs egyetlen ügyfél vagy a bérlői csökkenti a többi szolgáltatás használhatja. További információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md) méretezhetőségi célok storage-fiókok és a storage-fiókok partíciókról teljesítménycéloknak.

Ha a **PercentThrottlingError** metrika növelését megjeleníteni, amelyek sávszélesség-szabályozási hiba miatt nem kérelem azon százaléka, ki kell vizsgálni helyzetek egyikében:

* [PercentThrottlingError átmeneti növekedése]
* [Állandó növekedése PercentThrottlingError hiba]

Megnövelheti a **PercentThrottlingError** gyakran kerül sor, megnövelheti a storage-kérelmek számát egy időben, vagy ha kezdetben betölteni az alkalmazás tesztelése. Ez lehet, hogy is manifest magát az ügyfél "503-as kiszolgáló foglalt" vagy "500 művelet időtúllépése" HTTP állapotüzenete tárolási műveletek.

#### <a name="transient-increase-in-PercentThrottlingError"></a>PercentThrottlingError átmeneti növekedése
Ha Ön értékének igényeiben jelentkező **PercentThrottlingError** , amely az alkalmazás a nagy aktivitású időszakok egybe, meg kell valósítania az ügyfél egy exponenciális (lineáris) vissza az újbóli próbálkozások stratégiával ki: Ezzel csökkenthető a partíció azonnali terhelése, és az alkalmazás számára a forgalom igényeiben jelentkező simítja segítségével. A Storage ügyféloldali kódtár segítségével újrapróbálkozási-házirendek implementálásához kapcsolatos további információkért lásd: [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Is találkozhat értékének igényeiben jelentkező **PercentThrottlingError** , amely nem esik egybe az alkalmazás a nagy aktivitású időszakokkal: Itt a legvalószínűbb ok az a partíciók terheléselosztás javítása áthelyezését tároló szolgáltatást.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Állandó növekedése PercentThrottlingError hiba
Ha Ön folyamatosan magas értéket **PercentThrottlingError** a végleges növelését a tranzakció-köteteket, vagy amikor a kezdeti betöltés hajtja végre a következő teszteli, az alkalmazásra, majd fel kell mérnie, hogy az alkalmazás adattárolási partíciókat használja, és e közeledik egy tárfiók a méretezhetőségi célok. Például ha Ön szabályozás hibákat a várólista (amely számít egyetlen partícióra), majd érdemes további várólisták segítségével a tranzakciók elosztva több partíciót. Ha Ön szabályozás hibák táblán, fontolja meg egy másik particionálási sémát a tranzakciók elosztva több partíciót széles körének partíciókulcs-értékek használatával szeretné. Ennek a problémának egy közös ok: a prepend hozzáfűzése elleni mintát ahol dátumát jelöli ki a partíciós kulcs, és majd egy adott napon összes adatot ír egy partíciót: terhelés, ennek eredményeként a írási szűk keresztmetszetek. Fontolja meg egy másik particionálási tervezési vagy mérlegelje, hogy a blob storage használatával jobb megoldás lehet kell. Kell ellenőrizze, hogy a sávszélesség-szabályozás miatt a forgalom igényeiben jelentkező végbemegy, és vizsgálja meg a minta kérelem simítás módjait.

Ha a tranzakciók szét több partíciót, továbbra is kell a méretezhetőségének korlátai, a tárolási fiókjában tudomást. Például ha tíz várólisták a legfeljebb 2000 1KB üzenetek másodpercenkénti feldolgozási, akkor lesz teljes legfeljebb 20 000 üzenetek / másodperc, a tárfiók. Ha kell feldolgozni másodpercenként több mint 20 000 entitások, érdemes lehet több tárfiókot használni. Meg kell is figyelembe kell vennie, hogy méretét a kérelmek és entitások akkor van hatása, ha a társzolgáltatás az ügyfelek azelőtt gyorsítja fel a: Ha nagyobb kérelmek és entitások, akkor előfordulhat, hogy lehet halmozódni hamarabb.

Nem elég hatékony Lekérdezéstervezés is okozhat, hogy elérte a méretezhetőségi korlátok, a táblázat partíciókat. Például, amely egy partíció csak kiválaszt egy százalék entitások, de, amely megvizsgálja egy partíció összes entitásának szűrő egy lekérdezést kell minden egyes entitás eléréséhez. Minden entitás, olvassa el az adott partíció; tranzakciók száma is beleszámít. ezért könnyen képes elérni a méretezhetőségi célok.

> [!NOTE]
> A teljesítmény tesztelés egyetlen lekérdezés nem elég hatékony tervet, az alkalmazás kódproblémájáról van.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>PercentTimeoutError metrika növelését
A metrika növekedése **PercentTimeoutError** a tárolási szolgáltatások egyike. Egy időben az ügyfél fogad az "500 művelet időtúllépése" HTTP-állapot üzenetek nagy mennyiségű tárolási műveletek.

> [!NOTE]
> Ideiglenesen a tárolási szolgáltatásként időtúllépést betöltése kiegyensúlyozza kérelmeket egy új kiszolgálóra helyezi át a partíció jelenhet meg.
> 
> 

A **PercentTimeoutError** mérőszáma egyszerűsítése érdekében a következő metrikákat: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, és **SASServerTimeoutError**.

A kiszolgáló időkorlátja a kiszolgálón hiba okozza. Az ügyfél-időtúllépések fordulhat elő, mert a kiszolgálón egy művelet túllépte az; az ügyfél által megadott például egy ügyfél a Storage ügyféloldali kódtár segítségével állíthatja be művelet időtúllépés használatával a **ServerTimeout** tulajdonsága a **QueueRequestOptions** osztály.

Kiszolgáló időkorlátja a tároló szolgáltatás, amely további vizsgálatot igényel problémát jelez. Metrikák is használhatja, ha hogy elérte-e a szolgáltatás a méretezhetőségének korlátai, és bármely igényeiben jelentkező a forgalom okozta probléma azonosításához. Ha a probléma időszakos, lehet terheléselosztás miatt a szolgáltatás tevékenység. Ha a probléma állandó, és nem okozza az alkalmazás elérte-e a szolgáltatás a méretezhetőségének korlátai, a problémának a megoldásához kell előléptetése. Az ügyfél-időtúllépések döntse el, ha az időtúllépés értéke egy megfelelő értéket, az ügyfél és vagy az időkorlát értékének megadása a ügyfél módosítása, vagy vizsgálja meg, hogyan javíthatja a teljesítményét a műveletek a tárolás szolgáltatás például a táblázat lekérdezések optimalizálása, vagy az üzenetek méretének csökkentését.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>PercentNetworkError metrika növelését
A metrika növekedése **PercentNetworkError** a tárolási szolgáltatások egyike. A **PercentNetworkError** mérőszáma egyszerűsítése érdekében a következő metrikákat: **NetworkError**, **AnonymousNetworkError**, és **SASNetworkError**. Ezek akkor, ha a tároló szolgáltatás észleli a hálózati hiba, ha az ügyfél egy tárolási kérést.

Ez a hiba leggyakoribb oka egy ügyfél leválasztása időtúllépés járjon le a storage szolgáltatásban. Az ügyfél tudni, miért és mikor az ügyfél bontja a kapcsolatot a társzolgáltatás ki kell vizsgálni a kódot. Vizsgálja meg az ügyfél hálózati problémák Wireshark, Microsoft Message Analyzert vagy Tcping is használja. Ezek az eszközök ismertetett a [mellékletek].

### <a name="the-client-is-receiving-403-messages"></a>Az ügyfél HTTP 403 (tiltott) üzeneteket fogad
Ha az ügyfélalkalmazást szűrész HTTP 403 (tiltott) hibák, a legvalószínűbb oka, hogy az ügyfél használ egy lejárt közös hozzáférésű Jogosultságkód (SAS) tárolási kérelmet küld a (bár egyéb lehetséges okok a következők: óra eltérésére, érvénytelen kulcsokat, és üres fejlécekkel együtt). Ha egy lejárt SAS-kulcsot az okot, nem látják azokat a bejegyzéseket a kiszolgálóoldali tárolási naplózás naplóadatokat. Az alábbi táblázat megmutatja, az ügyféloldali naplóból állítja elő a Storage ügyféloldali kódtár, amely bemutatja, hogy ez a probléma lépett fel:

| Forrás | A részletességi | A részletességi | Ügyfélkérelem-azonosító | A művelet szöveg |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |A hely elsődleges hely módban PrimaryOnly művelet elindítása. |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |Indítása szinkron kérelem https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;sig OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % = 3D&amp;api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |Válaszra való várakozás közben. |
| Microsoft.WindowsAzure.Storage |Figyelmeztetés |2 |85d077ab-... |Kivétel keletkezett a válaszra való várakozás közben: A távoli kiszolgáló hibát adott vissza: (403-as) tiltott... |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |A válasz érkezett. Állapotkód = 403-as, a kérelem azonosítója = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =. |
| Microsoft.WindowsAzure.Storage |Figyelmeztetés |2 |85d077ab-... |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (403-as) tiltott... |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |Annak ellenőrzése, ha a műveletet meg kell ismételni. Ismétlések száma = 0, HTTP-állapotkód = 403-as, kivétel = a távoli kiszolgáló hibát adott vissza: (403-as) tiltott... |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-... |A következő helyre van beállítva elsődleges, a hely mód alapján. |
| Microsoft.WindowsAzure.Storage |Hiba |1 |85d077ab-... |Újrapróbálkozási házirend nem engedélyezi az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (403-as) tiltott. |

Ebben az esetben kell vizsgálni, miért a SAS-jogkivonat lejár az ügyfélszámítógép elküldi a jogkivonatot a kiszolgálóra:

* Általában a kezdő időpont, amikor létrehoz egy SAS-t egy ügyfélen szeretné azonnal nincs beállítva. Ha a gazdagép a SAS használatával az aktuális idő és a tárolás szolgáltatás létrehozásakor, akkor lehetséges, hogy a társzolgáltatás fogadására, amely még nem érvényes SAS közötti kis óra különbségek vannak.
* SAS-kód nem egy nagyon rövid lejárati időt kell beállítani. Ebben az esetben kis óra különbségei az SA-kat és a tárolás szolgáltatás létrehozása a gazdagép látszólag lejár a vártnál korábban SAS vezethet.
* Az SAS-kulcsot a verzió paramétere does (például **sv = 2015-04-05**) felel meg a Storage ügyféloldali kódtár használata verziójának? Azt javasoljuk, hogy mindig a legújabb verzióját használja a [a Storage ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/).
* A tárelérési kulcsok újragenerálása, ha ez bármely létező SAS-tokenje érvénytelenné tehetik. Erre akkor lehet problémát, ha az ügyfél gyorsítótár alkalmazások hosszú lejárati idővel rendelkező SAS-tokenje.

A Storage ügyféloldali kódtár SAS-tokenje létrehozásához használ, majd esetén könnyen összeállíthat egy érvényes tokent. Azonban ha a Storage REST API-t használ, és kézzel jogkivonatokat hoz létre, a biztonsági Társítások figyelmesen olvassa el a témakör [egy közös hozzáférésű Jogosultságkód hozzáférést delegálása](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Az ügyfél HTTP 404-es (nem található) üzeneteket fogad
Ha az ügyfélalkalmazás egy HTTP 404-es (nem található) üzenetet kap, a kiszolgálóról, ez azt jelenti, hogy az objektum az ügyfél próbált használni (például egy entitás, tábla, blob, tároló vagy várólista) nem létezik a tároló szolgáltatást. Számos, ennek lehetséges okai például:

* [Az ügyfél vagy egy másik folyamat a korábban az objektum törölve]
* [Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba]
* [Ügyféloldali JavaScript-kód nincs engedélye az objektumhoz való hozzáférést]
* [Hálózati hiba]

#### <a name="client-previously-deleted-the-object"></a>Az ügyfél vagy egy másik folyamat a korábban az objektum törölve
A forgatókönyvek, ahol az ügyfél megpróbálja olvasása, frissítése vagy törölni az adatokat egy társzolgáltatás esetében általában könnyen azonosítható a kiszolgálóoldali naplók egy korábbi művelet, amely a szóban forgó objektum törlése a tárolószolgáltatásból. Nagyon gyakran a naplózási adatokat jeleníti meg, hogy egy másik felhasználó vagy folyamat az objektum törölve. A kiszolgálóoldali tárolási naplózás naplóban művelet-típusa és a kért objektum-kulcs típusú oszlopok megjelenítése objektum törlésekor az ügyfelet.

A forgatókönyvben, ha egy ügyfél próbál egy olyan objektum beszúrására akkor előfordulhat, hogy azonnal nem válik egyértelművé, ezért az eredmény (nem található) HTTP 404-es választ fényében, hogy az ügyfél egy új objektumot hoz létre. Azonban ha az ügyfél hoz létre egy blobot, képesnek kell lennie a blob-tároló, ha az ügyfél hoz létre a várólista található képesnek kell lennie egy üzenetet, és az ügyfél hozzáadásával egy sort kell lennie a táblázatban találja.

Az ügyféloldali napló a Storage ügyféloldali kódtára a segítségével megismerheti a részletes ismertetése amikor az ügyfél elküldi a társzolgáltatás bizonyos kérésekre.

A következő ügyféloldali napló a Storage ügyféloldali kódtár által generált szemlélteti a problémát, ha az ügyfél akkor hoz létre a BLOB nem található a tárolóban. Ez a napló tartalmazza a tárolási műveletet az alábbiak közül:

| Kérelem azonosítója | Művelet |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metódus a blob-tároló törlése. Vegye figyelembe, hogy ez a művelet tartalmaz egy **HEAD** kérelmet a tároló meglétének ellenőrzése. |
| e2d06d78... |**CreateIfNotExists** módszer a blob-tároló létrehozása. Vegye figyelembe, hogy ez a művelet tartalmaz egy **HEAD** kérésére, amely ellenőrzi, hogy létezik-e a tárolóban. A **HEAD** 404-es üzenetet adja vissza, de továbbra is. |
| de8b1c3c-... |**UploadFromStream** metódus létrehozása a blob. A **PUT** kérelem a 404-es üzenettel sikertelen |

Naplóbejegyzéseket:

| Kérelem azonosítója | A művelet szöveg |
| --- | --- |
| 07b26a5d-... |Kezdő https://domemaildist.blob.core.windows.net/azuremmblobcontainer szinkron kérelmet. |
| 07b26a5d-... |StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 2014. jún 03. 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Válaszra való várakozás közben. |
| 07b26a5d-... |A válasz érkezett. Állapotkód = 200 kérés azonosítója = eeead849... Content-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Válaszfejlécek sikerült feldolgozni, folytatás a többi részének a műveletet. |
| 07b26a5d-... |Választörzs letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| 07b26a5d-... |Kezdő https://domemaildist.blob.core.windows.net/azuremmblobcontainer szinkron kérelmet. |
| 07b26a5d-... |StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 2014. jún 03. 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Válaszra való várakozás közben. |
| 07b26a5d-... |A válasz érkezett. Állapotkód = 202, kérelem azonosítója = 6ab2a4cf-..., Content-MD5 = ETag =. |
| 07b26a5d-... |Válaszfejlécek sikerült feldolgozni, folytatás a többi részének a műveletet. |
| 07b26a5d-... |Választörzs letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Kezdő https://domemaildist.blob.core.windows.net/azuremmblobcontainer aszinkron kérelem.</td> |
| e2d06d78-... |StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 2014. jún 03. 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Válaszra való várakozás közben. |
| de8b1c3c-... |Kezdő https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt szinkron kérelmet. |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-Type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE, 2014. jún 03. 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Felkészülés a kérelem adatokat írni. |
| e2d06d78-... |Kivétel keletkezett a válaszra való várakozás közben: A távoli kiszolgáló hibát adott vissza: (404) nem található. |
| e2d06d78-... |A válasz érkezett. Állapotkód: 404-es, a kérelem azonosítója = = 353ae3bc-..., Content-MD5 = ETag =. |
| e2d06d78-... |Válaszfejlécek sikerült feldolgozni, folytatás a többi részének a műveletet. |
| e2d06d78-... |Választörzs letöltése. |
| e2d06d78-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Kezdő https://domemaildist.blob.core.windows.net/azuremmblobcontainer aszinkron kérelem. |
| e2d06d78-... |StringToSign = PUT... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, 2014. jún 03. 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Válaszra való várakozás közben. |
| de8b1c3c-... |Kérelem az adatok írásakor. |
| de8b1c3c-... |Válaszra való várakozás közben. |
| e2d06d78-... |Kivétel keletkezett a válaszra való várakozás közben: A távoli kiszolgáló hibát adott vissza: (409) ütközés... |
| e2d06d78-... |A válasz érkezett. Állapotkód = 409, a kérelem azonosítója = c27da20e-..., Content-MD5 = ETag =. |
| e2d06d78-... |Csomagletöltési hiba adott válasz törzse. |
| de8b1c3c-... |Kivétel keletkezett a válaszra való várakozás közben: A távoli kiszolgáló hibát adott vissza: (404) nem található. |
| de8b1c3c-... |A válasz érkezett. Állapotkód: 404-es, a kérelem azonosítója = = 0eaeab3e-..., Content-MD5 = ETag =. |
| de8b1c3c-... |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (404) nem található. |
| de8b1c3c-... |Újrapróbálkozási házirend nem engedélyezi az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (404) nem található. |
| e2d06d78-... |Újrapróbálkozási házirend nem engedélyezi az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (409) ütközés... |

Ebben a példában a napló jeleníti meg, hogy az ügyfél van kihagyásos érkező kéréseket a **CreateIfNotExists** érkező kéréseket (... kérelem azonosítója e2d06d78) metódust a **UploadFromStream** metódus (de8b1c3c-...); Ez akkor történik meg, mert az ügyfélalkalmazás aszinkron módon hívja a ezeket a módszereket. Az ügyfél számára, győződjön meg arról, hogy létrehozza a tároló előtt minden adatok feltöltése a blob a tárolóban lévő aszinkron kódot kell módosítani. Ideális esetben a tárolók előre kell létrehoznia.

#### <a name="SAS-authorization-issue"></a>Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba
Ha az ügyfélalkalmazás megpróbálja, amely nem tartalmazza a szükséges engedélyekkel a művelet SAS-kulcsot használ, a tároló szolgáltatás (nem található) HTTP 404 üzenetben visszaadja az ügyfélnek. Egy időben, látni fogja emellett nullától eltérő értéket **SASAuthorizationError** a metrikák a.

Az alábbi táblázat a naplózás tárolási naplófájlból kiszolgálóoldali napló mintaüzenet:

| Név | Érték |
| --- | --- |
| Kérelem kezdési ideje | 2014-05-30T06:17:48.4473697Z |
| Művelet típusa     | GetBlobProperties            |
| Szolgáltatáskérés állapota     | SASAuthorizationError        |
| HTTP-állapotkód:   | 404                          |
| Hitelesítés típusa| SAS                          |
| Szolgáltatás típusa       | Blob                         |
| Kérelem URL-címe        | https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ? sv 2014-02-14 = & sr = c & si = mypolicy & sig = XXXXX&;api-version = 2014-02-14 |
| Kérelemfejlécet azonosítója  | a1f348d5-8032-4912-93EF-b393e5252a3b |
| Ügyfélkérelem-azonosító  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Ki kell vizsgálni, hogy miért az ügyfélalkalmazás megpróbálja végrehajtania egy műveletet nem kapta meg engedélyeket.

#### <a name="JavaScript-code-does-not-have-permission"></a>Ügyféloldali JavaScript-kód nincs engedélye az objektumhoz való hozzáférést
Ha a JavaScript-ügyfelet kell használnia, és a társzolgáltatás HTTP 404-es üzeneteket ad eredményül, keressen a böngészőben a következő JavaScript-hibák:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Az Internet Explorer az F12 fejlesztői eszközök segítségével nyomon követni az ügyféloldali JavaScript problémák elhárításakor a böngésző és a tárolási szolgáltatás között cserélt üzeneteket.
> 
> 

Ezek a hibák fordulhat elő, mert a webböngésző valósítja meg a [ugyanazt](http://www.w3.org/Security/wiki/Same_Origin_Policy) biztonsági korlátozása, hogy a webes hívja az API-t egy másik tartományban a tartományból a lap származik.

A JavaScript probléma Cross Origin Resource Sharing (CORS) is konfigurálhat a ügyfelet társzolgáltatás. További információkért lásd: [Azure Storage szolgáltatásainak Cross-Origin Resource Sharing (CORS) támogatása](http://msdn.microsoft.com/library/azure/dn535601.aspx).

A következő példakód bemutatja, hogyan konfigurálja a blob szolgáltatást, hogy a blob storage szolgáltatásban blob eléréséhez a Contoso tartományban futó JavaScript engedélyezése:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Hálózati hiba
Bizonyos körülmények között elveszett hálózati csomagokat a HTTP 404-es üzenetek vissza az ügyfélnek társzolgáltatás vezethet. Például, ha az ügyfélalkalmazást entitás törlése a table szolgáltatásból megjelenik az ügyfél egy tárolási kivétel jelentés throw egy "HTTP 404-es (nem található)" a table szolgáltatásból a hibaállapot-üzeneteket. Ha a tábla a table storage szolgáltatásban vizsgálja meg, megjelenik az, hogy a szolgáltatás minden volt törölni egy entitást, kért.

A kivétel részletes adatai az ügyfél tartalmazza a kéréshez a table szolgáltatás által hozzárendelt kérelemazonosító (7e84f12d...): Ez az információ segítségével a keresőkifejezéssel kereshet a kiszolgálóoldali tárolási naplók a kérelem részletes adatait a **kérelemfejlécet-azonosító** oszlop a naplófájlban. A metrikák hibák például ez történik, és kereshet a naplófájlok a metrikák rögzített Ez a hiba ideje alapján azonosítására is használhatja. A naplóbejegyzés jeleníti meg, hogy a törlése nem sikerült egy "HTTP (404) ügyfél más hiba" állapotüzenetet. Az azonos naplóbejegyzés is tartalmaz az ügyfél által generált kérelemazonosító a **ügyfélkérelem-azonosító** oszlop (813ea74f...).

A kiszolgálóoldali napló is azonos egy másik belépési **ügyfélkérelem-azonosító** értékét (813ea74f...) a sikeres művelet ugyanaz az entitás, valamint az ugyanazon ügyféltől törlése. A sikeres törlési művelete rövid idő került sor, a sikertelen kérelem törlése előtt.

Ebben a forgatókönyvben ennek legvalószínűbb oka az, hogy az ügyfél az entitás delete kérelmet küldött a table szolgáltatás, amely sikeres volt, de nem kapott nyugtázást a kiszolgáló (lehet, hogy oka egy átmeneti hálózati probléma). Az ügyfél ezután automatikusan újra megpróbálja a műveletet (azonos **ügyfélkérelem-azonosító**), és ily módon tett újrapróbálkozás sikertelen volt, mert az entitás már törlődött.

Ha a probléma gyakran előfordul, ki kell vizsgálni, miért az ügyfél nem képes a table szolgáltatásból nyugtázásának érkezésére. Ha a probléma időszakos, azt kell feldolgozzák-e a "HTTP (404) nem található" hiba és -e jelentkezni, az ügyfél, azonban az ügyfél továbbra is.

### <a name="the-client-is-receiving-409-messages"></a>Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad
Az alábbi táblázatban láthatók a kiszolgálóoldali keresse meg a két Ügyfélműveletek kivonatát: **DeleteIfExists** után azonnal az **CreateIfNotExists** blob-tároló azonos név használata. Vegye figyelembe, hogy minden ügyfél művelet eredménye két kérelmet a rendszer kiszolgálóra küldi, először egy **GetContainerProperties** kérelem ellenőrizze, hogy a tároló létezik-e, majd a **DeleteContainer** vagy **CreateContainer** kérelmet.

| időbélyeg | Művelet | eredménye | Tárolónév | Ügyfélkérelem-azonosító |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Az ügyfélalkalmazás kódja törli, és azonnal újra létrehozza a blobtárolót az azonos név használata: a **CreateIfNotExists** (ügyfél kérelem azonosítója bc881924-...) metódus idővel a HTTP 409 (Ütközés) hibaüzenettel meghiúsul. Ha törli az ügyfél a blobtárolók, táblák, vagy olyan várólisták esetében egy rövid időszak neve előtt ismét elérhetővé válik.

Az ügyfélalkalmazás kell használni egyedi tároló neve, ha a törlése és ismételt létrehozása minta közös új tárolók létrehozza.

### <a name="metrics-show-low-percent-success"></a>Metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére a tranzakció állapota
A **PercentSuccess** metrika százalékos arányát sikerrel járt-e a HTTP-állapotkód alapján műveleteket rögzíti. A 2XX állapotkódok műveletek száma sikeres, mivel az állapotkódnak 3XX, 4XX és 5XX tartományok műveletek szerint sikertelen és alacsonyabb bájtjai számítanak. a **PercentSucess** Átjárómetrika értékeként. A tranzakció állapota a rögzített ezeket a műveleteket a kiszolgálóoldali tárolási naplófájlokban **ClientOtherErrors**.

Fontos megjegyezni, hogy ezek a műveletek sikeresen befejeződött, és ezért nem befolyásolják a más mutatókat, például a rendelkezésre állási. Néhány művelet, hogy sikeresen végrehajtható legyen, de a sikertelen HTTP-állapotkódok eredményezhet, amely többek között:

* **ResourceNotFound** (nem található 404-es), például a GET kérelem blob, amely nem létezik.
* **ResouceAlreadyExists** (ütközés 409), például egy **CreateIfNotExist** műveletet, ha az erőforrás már létezik.
* **ConditionNotMet** (nem módosított 304), például az egy feltételes művelet, például amikor egy ügyfél küld egy **ETag** érték és a HTTP **If-None-Match** fejléc kéréséhez lemezkép csak akkor, ha az utolsó művelet óta frissült.

Közös REST API hiba megtekintéséhez, amely a tárolási szolgáltatások ad vissza, az oldalon található [közös REST API hibakódok](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat
Hirtelen jelenik meg, ha a tárfiókban lévő kapacitás használati váratlan változásai használatával megvizsgálhatja a okok miatt a rendelkezésre állási metrikák; első megtekintésével például sikertelen törlési kérelmek vezethet, előfordulhat, hogy rendelkezik várhatóan kell szabadítson fel helyet az alkalmazás-specifikus karbantartási műveleteket nem fog működni, (például a várt módon használt blob-tároló mennyiségének olyan növekedése számának növekedése mert lejárt a SAS-tokenje szabadítson fel helyet használja).

### <a name="you-are-experiencing-unexpected-reboots"></a>Váratlan újraindítások Azure virtuális gépek, amelyek csatlakoztatott virtuális merevlemezek nagy számú tapasztal
Ha egy Azure virtuális gép (VM) ugyanabban a tárfiókban lévő csatlakoztatott virtuális merevlemezek nagy mennyiségű, meghaladhatja a a méretezhetőségi célok, az egyes tárfiók a virtuális gép leállását okozza. Jelölje be a tárfiók percenkénti metrikákat (**TotalRequests**/**TotalIngress**/**TotalEgress**) a teljesítményt, amelyek mérete meghaladja a méretezhetőségi célok tárfiókok esetén. Című témakör "[PercentThrottlingError metrika növelését]" támogatásért, ha sávszélesség-szabályozás történt a tárfiók számára.

Általában minden egyes bemeneti vagy kimeneti művelet a virtuális gép virtuális merevlemez az eszköz **első oldal** vagy **Put lap** az alapul szolgáló oldalakra vonatkozó blob műveleteket. Ezért a környezetnek a becsült IOPS segítségével is rendelkezik egyetlen tárfiókokban alapján az adott viselkedést az alkalmazás hány virtuális merevlemezek finomítsa. Legfeljebb 40 lemezek rendelkező egyetlen tárfiókokban nem ajánlott. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md) az aktuális méretezhetőségi célok storage-fiókok részleteit, különösen tárfióknak a típusa tartozó összes kérelem sebesség és a teljes sávszélesség használata.
Ha a tárfiók túllépte a vonatkozó méretezhetőségi célok, helyezze a virtuális merevlemezek több különböző tárfiókokban a tevékenység minden egyes számlára csökkentése érdekében.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával
Általában a storage emulatorral a fejlesztés során, és ellenőrizze, hogy elkerülése érdekében szükség az Azure storage-fiók. A gyakori problémákat, amik akkor léphetnek fel, a storage emulator használata esetén a következők:

* [A storage emulator az "X" a szolgáltatás nem működik]
* [Hiba történt "a HTTP-fejlécek egyikéhez értéke nem megfelelő formátumú" a storage emulator használata esetén]
* [A storage emulator futtatása rendszergazdai jogosultságokra van szüksége]

#### <a name="feature-X-is-not-working"></a>A storage emulator az "X" a szolgáltatás nem működik
A storage emulator nem támogatja a Funkciók, például a szolgáltatás az Azure storage szolgáltatások. További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral).

Ezeket a szolgáltatásokat, és a storage emulator nem támogatja használja az Azure storage szolgáltatás a felhőben.

#### <a name="error-HTTP-header-not-correct-format"></a>Hiba történt "a HTTP-fejlécek egyikéhez értéke nem megfelelő formátumú" a storage emulator használata esetén
Teszteli az alkalmazás, amely használja, mint a Storage ügyféloldali kódtára a helyi storage emulator és metódus hívások elleni **CreateIfNotExists** sikertelen, és a "a HTTP-fejlécek egyikéhez értéke nem a megfelelő hibaüzenet formátumban." Ez azt jelzi, hogy a storage emulator használata verziója nem támogatja a storage ügyféloldali kódtár használata verzióját. A Storage ügyféloldali kódtára a fejlécet ad **x-ms-version** teszi minden kérelemre. Ha a storage emulator nem ismeri fel az értéket a **x-ms-version** fejléc, hogy elutasítja a kérelmet.

A tárolási Szalagtári ügyfél naplók segítségével értékének láthatja a **x-ms-version fejlécnek** akkor küld. Azt is láthatja, értékét a **x-ms-version fejlécnek** Fiddler a kérelmek nyomon követésére, az ügyfélalkalmazás használatakor.

Ebben a forgatókönyvben rendszerint azért fordul elő, ha telepít, és a Storage ügyféloldali kódtár legújabb verzióját használja a storage emulator frissítése nélkül. Meg kell a storage emulator legújabb verziójának telepítéséhez, vagy használjon felhőalapú tárolás helyett az emulátor fejlesztési és tesztelési.

#### <a name="storage-emulator-requires-administrative-privileges"></a>A storage emulator futtatása rendszergazdai jogosultságokra van szüksége
Kéri a rendszergazdai hitelesítő adatok a storage emulator futtatásakor. Ez csak akkor történik, az első alkalommal a storage emulator vannak inicializálása közben. Miután a storage emulator inicializálta, nem kell újra futtatásához rendszergazdai jogosultságokkal.

További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral). Vegye figyelembe, hogy a Visual Studio, amely rendszergazdai jogosultságokkal is szüksége lesz a storage emulator is lehet inicializálni.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.
A storage emulator telepítése a helyi számítógépen próbálja telepíteni az SDK-t, sikertelen. A telepítési naplófájl tartalmazza az alábbi üzenetek egyike:

* CAQuietExec: Hiba: nem érhető el az SQL-példány
* CAQuietExec: Hiba: nem hozható létre adatbázis

Az OK: a meglévő LocalDB telepítési kapcsolatos problémát. Alapértelmezés szerint a storage Emulator LocalDB megőrizni az adatokat, amikor azt szimulálja az Azure storage szolgáltatások. A következő parancsok futtatásával egy parancssori ablakban az SDK telepítése előtt alaphelyzetbe állíthatja a LocalDB példányához.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

A **törlése** parancs eltávolítja a régi adatbázisfájlok a storage emulator az előző telepítések.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Van egy másik probléma storage szolgáltatással
Ha az előző hibaelhárítási szakaszok nem adja meg a problémát tapasztal storage szolgáltatással, el kell fogadnia a következő módon diagnosztizálásának és a hiba elhárításához.

* Ellenőrizze a metrikákat, ha bármi is módosul az a várt viszonyítási viselkedése. A metrikák a esetleg meg tudja határozni, hogy a probléma átmeneti és végleges, és mely tárolási műveletek érinti a problémát.
* A metrikák információk segítségével keresse meg a kiszolgálóoldali naplóadatokat előforduló hibákat további részletes információt nyújt segítséget. Ezek az információk segítenek elhárításához és hárítsa el a problémát.
* Ha a kiszolgálóoldali naplókban lévő információk nem elegendő a probléma elhárításához sikeresen, a Storage ügyféloldali kódtár ügyféloldali naplók segítségével vizsgálja meg az ügyfélalkalmazást és a Fiddler, Wireshark vagy Microsoft Message Analyzert, vizsgálja meg a hálózat működését.

A Fiddler használatával kapcsolatos további információkért lásd: "[1 függelék: HTTP és HTTPS-forgalom rögzítése a Fiddler használatával]."

Wireshark használatával kapcsolatos további információkért lásd: "[2 függelék: Wireshark használó rögzítheti a hálózati forgalom]."

Microsoft Message Analyzert használatával kapcsolatos további információkért lásd: "[függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom]."

## <a name="appendices"></a>Mellékletek
A mellékletek azt tapasztalhatja hasznos diagnosztizálása és elhárítása az Azure Storage (és az egyéb szolgáltatásokkal) eszközöket írja le. Ezek az eszközök nem része az Azure Storage és harmadik féltől származó termékekre. Így az eszközök ismertetett ezen mellékletek nem tartoznak egyetlen támogatási szerződést, előfordulhat, hogy a Microsoft Azure vagy az Azure Storage, és ezért a kiértékelési folyamat részeként meg kell vizsgálni a licencelési és támogatási lehetőségekről a szolgáltató ezen eszközök.

### <a name="appendix-1"></a>1. függelék: A HTTP és HTTPS-forgalom rögzítésére Fiddler segítségével
[Fiddler](http://www.telerik.com/fiddler) az eszköz a HTTP és HTTPS-forgalmat az ügyfélalkalmazást és a használ az Azure storage szolgáltatás közötti elemzéséhez.

> [!NOTE]
> Fiddler tudja dekódolni a HTTPS-forgalom; olvassa el a Fiddler dokumentáció konfigurációkat annak megértése, hogyan teszi ezt, és a biztonsági vonatkozó következmények ismertetése.
> 
> 

Ebben a függelékben egy rövid útmutató arról, hogyan konfigurálhatja a helyi számítógépen, amelyen telepítette a Fiddler és az Azure storage szolgáltatások közötti forgalom rögzítése a Fiddler.

Miután Fiddler indítása, a helyi számítógépen a HTTP és HTTPS-forgalom rögzítése kezdi. Az alábbiakban néhány hasznos parancsokat szabályozásához a Fiddler:

* Állítsa le és indítsa el a forgalom rögzítése. A főmenü Ugrás **fájl** , majd **forgalom rögzítése** váltáshoz rögzítése a be- és kikapcsolható.
* Rögzített forgalmi adatok mentése. A főmenü Ugrás **fájl**, kattintson a **mentése**, és kattintson a **minden munkamenet**: Ez lehetővé teszi, hogy a forgalom munkamenet archív fájlba mentése. Töltse be újra a később elemzéshez munkamenet-archívum létrehozása, vagy küldje el, ha a Microsoft támogatási kérelmet.

A Fiddler rögzíti a forgalom mennyiségének korlátozásához használhatja a szűrők, amelyet megadtak a **szűrők** fülre. Az alábbi képernyőfelvételen látható egy szűrő, amely csak a küldött forgalmat rögzíti a **contosoemaildist.table.core.windows.net** tárolási végpont:

![][5]

### <a name="appendix-2"></a>2. függelékben: Wireshark használó rögzítheti a hálózati forgalom
[Wireshark](http://www.wireshark.org/) van egy hálózati protokollelemző eszköz, amely lehetővé teszi a hálózati protokollok széles köre csomag részletes információk megtekintése.

Az alábbi eljárás bemutatja, hogyan részletes csomagok információit a helyi számítógépről érkező forgalom rögzítésére telepítési Wireshark a table szolgáltatásba az Azure-tárfiókot.

1. Indítsa el a Wireshark a helyi számítógépen.
2. Az a **Start** területen válassza ki a helyi hálózati adapter vagy az internethez csatlakozó felületek.
3. Kattintson a **beállítások rögzítése**.
4. Adja hozzá egy szűrőt, amely a **rögzítési szűrő** szövegmező. Például **contosoemaildist.table.core.windows.net gazdagép** Wireshark csak a table szolgáltatási végpont a által küldött és csomagok rögzítéséhez konfigurálja a **contosoemaildist** tárfiók. Tekintse meg a [rögzítése szűrők teljes listájának](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Kattintson a **Start**. Wireshark most rögzíti összes csomagokat küldjön vagy a table szolgáltatási végpont a használata az ügyfélalkalmazást a helyi számítógépen.
6. Miután végzett, a fő menüben kattintson a **rögzítése** , majd **leállítása**.
7. A rögzített adatok Wireshark rögzítése fájlba mentése, a főmenü kattintson **fájl** , majd **mentése**.

WireShark ki olyan hibákat, amelyek szerepelnek a **packetlist** ablak. Is használhatja a **szakértő Info** ablakban (kattintson **elemzés**, majd **szakértő Info**) a hibák és figyelmeztetések összegzésének megtekintése.

![][7]

Másik lehetőségként a TCP-adatok megtekintéséhez, mivel az alkalmazási rétegre látja a TCP-adatok csomagot jobb gombbal, majd válassza **hajtsa végre a TCP-folyam**. Ez különösen fontos, ha a biztonsági másolat nélkül rögzítési szűrő rögzített. További információkért lásd: [következő TCP-adatfolyamok](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Wireshark használatával kapcsolatos további információkért lásd: a [Wireshark felhasználók útmutató](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom
Microsoft Message Analyzert használja a HTTP és HTTPS-forgalom rögzítése a Fiddler hasonló módon, és a hálózati forgalom rögzítése a Wireshark hasonló módon.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Egy webes nyomkövetési munkamenet használ a Microsoft Message Analyzert konfigurálása
Egy webes nyomkövetési munkamenet a Microsoft Message Analyzert, futtassa a Microsoft Message Analyzert alkalmazást használ HTTP és HTTPS-forgalmat, majd a konfigurálása a **fájl** menüben kattintson **rögzítési/nyomkövetési**. Válassza ki a rendelkezésre álló nyomkövetési forgatókönyvek listáját, **webproxy**. Ezt a a **nyomkövetési forgatókönyv konfigurációjának** panelen, a a **HostnameFilter** szövegmező, a tárolási végpontok neveket adhat hozzá (ezekkel a nevekkel megjeleníthetők a [Azure-portálon](https://portal.azure.com)). Például, ha az Azure storage-fiók neve **contosodata**, adja hozzá a következőt a **HostnameFilter** szövegmező:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> A szóköz karakter választja el a gazdagép neve.
> 
> 

Amikor készen áll a nyomkövetési adatok gyűjtése indításához kattintson a **Start With** gombra.

További információt a Microsoft Message Analyzert **webproxy** nyomkövetési című [Microsoft-PEF-WebProxy szolgáltató](http://technet.microsoft.com/library/jj674814.aspx).

A beépített **webproxy** nyomkövetési Microsoft Message Analyzert a Fiddler alapul; ügyféloldali HTTPS-forgalom rögzítése és nem titkosított HTTPS üzenetek megjelenítése. A **webproxy** works nyomkövetése minden HTTP és HTTPS-forgalom, amely azt hozzáférést ad a nem titkosított üzeneteket helyi proxy konfigurálása.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Microsoft Message Analyzert használata a hálózati problémák diagnosztizálása
A Microsoft Message Analyzert használata mellett **webproxy** nyomkövetés rögzítése a HTTP/HTTPs-forgalmat az ügyfélalkalmazást és a tárolási szolgáltatás között részleteit, használhatja a beépített **helyi kapcsolat réteg** nyomkövetési rögzítheti a hálózati csomagok információit. Ez lehetővé teszi, hogy hasonló, amely a Wireshark rögzítése és diagnosztizálhatják az adatok rögzítéséhez hálózati problémák, mint eldobott csomagok.

Az alábbi képernyőfelvételen szereplő példán látható **helyi kapcsolat réteg** egy nyomkövetési **tájékoztató** állapotüzenetek a **DiagnosisTypes** oszlop. A megfelelő ikonra kattintva a **DiagnosisTypes** oszlop az üzenet részleteit jeleníti meg. Ebben a példában a kiszolgáló üzenet #305 újraküldött, mert nem kapott nyugtázást az ügyfél:

![][9]

Amikor a nyomkövetési munkamenet hoz létre a Microsoft Message Analyzert, úgy, hogy csökkenti a nyomkövetés a zaj is megadhat. Az a **rögzítése / nyomkövetési** Itt adhatja meg a nyomkövetési lapon kattintson a a **konfigurálása** melletti hivatkozásra **Microsoft-Windows-NDIS-PacketCapture**. Az alábbi képernyőfelvételen látható a konfigurációkat, amelyek a TCP-forgalom három adattárolási szolgáltatások IP-címek szűrése:

![][10]

A Microsoft Message Analyzer helyi kapcsolat réteg nyomkövetési kapcsolatos további információkért lásd: [Microsoft-PEF-NDIS-PacketCapture szolgáltató](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>4. függelék: Segítségével Excel metrikák megtekintése és az adatok naplózása
Sok eszközei lehetővé teszik a Storage Metrics adatok letöltése az Azure table storage segítségével egyszerűen az adatok betöltése az Excel megtekintése és elemzése tagolt formátumú. Az Azure blob storage tárolási naplózási adatok már Excelbe betöltése tagolt formátumú. Azonban akkor adja hozzá a megfelelő oszlopának fejlécére kattintva rendezhető, a következő információ alapján [Storage Analytics naplóformátumban](http://msdn.microsoft.com/library/azure/hh343259.aspx) és [Storage Analytics metrikák táblaséma](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Az a tárolási naplózási adatok importálása Excelbe, miután a blob storage-ból tölthetik le:

* Az a **adatok** menüben kattintson a **a szöveg**.
* Keresse meg a naplófájlt megtekintheti, és kattintson a kívánt **importálási**.
* Az 1. lépésében a **varázslóban**, jelölje be **tagolt**.

Az 1. lépésében a **varázslóban**, jelölje be **pontosvessző** csak elválasztóként idézőjel, válassza a **Szöveghatároló**. Kattintson a **Befejezés** , és válassza ki az adatok helyét a munkafüzetet.

### <a name="appendix-5"></a>5. függelék: A Visual Studio Team Services az Application insights szolgáltatással figyelését.
Az Application Insights szolgáltatás Visual Studio Team Services, a teljesítmény és rendelkezésre állásának figyelésére szolgáló részeként is használja. Ez az eszköz a következőket teheti:

* Ellenőrizze, hogy a webes szolgáltatás megfelelően üzemel és rugalmas. Hogy az alkalmazás egy webhely vagy egy eszköz-alkalmazást, amely egy webszolgáltatás-bővítmény használ, azt a világ különböző helyekről néhány percenként az URL-cím tesztelése, és jelzi, ha probléma van.
* Gyorsan diagnosztizálhatja a webszolgáltatás kivételek, illetve bármely problémák. Annak megállapítása, ha Processzor- vagy egyéb erőforrások vannak nyújtja, a kivételek az híváslánc megjelenik le, és könnyen megkereshetjük a naplókivonatokat. Ha az alkalmazás teljesítmény elfogadható korlátok alá csökken, elküldhetjük egy e-mailt. .NET- és a Java webes szolgáltatásokat is figyelhet.

További információt a [Mi az Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Bevezetés]: #introduction
[Hogyan szerveződik, ez az útmutató]: #how-this-guide-is-organized

[a tárolás szolgáltatás figyelése]: #monitoring-your-storage-service
[Figyelési szolgáltatásának állapota]: #monitoring-service-health
[Kapacitásának figyelése]: #monitoring-capacity
[Rendelkezésre állás figyelése]: #monitoring-availability
[A teljesítmény figyelése]: #monitoring-performance

[tárolási problémák diagnosztizálása]: #diagnosing-storage-issues
[Szolgáltatás ügynökállapottal kapcsolatos hibákkal]: #service-health-issues
[Teljesítménnyel kapcsolatos problémák]: #performance-issues
[Hibák diagnosztizálása]: #diagnosing-errors
[Tárolási emulátor problémák]: #storage-emulator-issues
[Tárolási naplózási eszközök]: #storage-logging-tools
[Hálózati naplózási eszközök használatával]: #using-network-logging-tools

[végpont nyomkövetés]: #end-to-end-tracing
[Naplóadatok válaszoknak az összekapcsolása]: #correlating-log-data
[Ügyfélkérelem-azonosító]: #client-request-id
[Server Kérelemazonosító]: #server-request-id
[Időbélyeg helyi időre]: #timestamps

[hibaelhárítási útmutatás]: #troubleshooting-guidance
[metrika AverageE2ELatency magas és alacsony AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok magas AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageServerLatency
[Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]: #you-are-experiencing-unexpected-delays-in-message-delivery

[PercentThrottlingError metrika növelését]: #metrics-show-an-increase-in-PercentThrottlingError
[PercentThrottlingError átmeneti növekedése]: #transient-increase-in-PercentThrottlingError
[Állandó növekedése PercentThrottlingError hiba]: #permanent-increase-in-PercentThrottlingError
[PercentTimeoutError metrika növelését]: #metrics-show-an-increase-in-PercentTimeoutError
[A mérőszámok emelkedő PercentNetworkError értéket mutatnak]: #metrics-show-an-increase-in-PercentNetworkError

[Az ügyfél HTTP 403 (tiltott) üzeneteket fogad]: #the-client-is-receiving-403-messages
[Az ügyfél HTTP 404-es (nem található) üzeneteket fogad]: #the-client-is-receiving-404-messages
[Az ügyfél vagy egy másik folyamat a korábban az objektum törölve]: #client-previously-deleted-the-object
[Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba]: #SAS-authorization-issue
[Ügyféloldali JavaScript-kód nincs engedélye az objektumhoz való hozzáférést]: #JavaScript-code-does-not-have-permission
[Hálózati hiba]: #network-failure
[Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]: #the-client-is-receiving-409-messages

[metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére állapotú tranzakció]: #metrics-show-low-percent-success
[Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]: #capacity-metrics-show-an-unexpected-increase
[Váratlan vesznek el virtuális gépeken, amelyek csatlakoztatott virtuális merevlemezek nagy számú tapasztal]: #you-are-experiencing-unexpected-reboots
[A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]: #your-issue-arises-from-using-the-storage-emulator
[A storage emulator az "X" a szolgáltatás nem működik]: #feature-X-is-not-working
[Hiba történt "a HTTP-fejlécek egyikéhez értéke nem megfelelő formátumú" a storage emulator használata esetén]: #error-HTTP-header-not-correct-format
[A storage emulator futtatása rendszergazdai jogosultságokra van szüksége]: #storage-emulator-requires-administrative-privileges
[.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Van egy másik probléma storage szolgáltatással]: #you-have-a-different-issue-with-a-storage-service

[mellékletek]: #appendices
[1 függelék: HTTP és HTTPS-forgalom rögzítése a Fiddler használatával]: #appendix-1
[2 függelék: Wireshark használó rögzítheti a hálózati forgalom]: #appendix-2
[függelék: 3: Microsoft Message Analyzert használó rögzítheti a hálózati forgalom]: #appendix-3
[4. függelék: Segítségével Excel metrikák megtekintése és az adatok naplózása]: #appendix-4
[5. függelék: A Visual Studio Team Services az Application insights szolgáltatással figyelését.]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
