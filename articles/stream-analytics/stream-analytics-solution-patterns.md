---
title: Az Azure Stream Analytics megoldásmintái
description: Ismerje meg az Azure Stream Analytics gyakori megoldásmintáit, például az irányítópultokat, az eseményüzenet-küldést, az adattárakat, a referenciaadatok bővítését és a figyelést.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535782"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Az Azure Stream Analytics megoldásmintái

Az Azure számos más szolgáltatásához hasonlóan a Stream Analytics is a legjobb, ha más szolgáltatásokkal együtt egy nagyobb, végpontok között lévő megoldást hoz létre. Ez a cikk egyszerű Azure Stream Analytics-megoldásokat és különböző architekturális mintákat ismerteti. Ezekre a mintákra építhet összetettebb megoldások kifejlesztéséhez. Az ebben a cikkben leírt minták at a legkülönbözőbb esetekben is használható. Forgatókönyv-specifikus minták példák érhetők el az [Azure-megoldásarchitektúrák.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Stream Analytics-feladat létrehozása a valós idejű irányítópult-élmény hez

Az Azure Stream Analytics segítségével gyorsan felállhat a valós idejű irányítópultokra és riasztásokra. Egy egyszerű megoldás eseményeket fog beaz Event Hubs vagy Az IoT Hub, és [egy streamelési adatkészlettel táplálja a Power BI irányítópultját.](/power-bi/service-real-time-streaming) További információt a Telefonhívás adatainak elemzése a Stream Analytics szolgáltatással című részletes [oktatóanyagban talál, és az eredmények megjelenítése a Power BI irányítópultján.](stream-analytics-manage-job.md)

![AZ ASA Power BI irányítópultja](media/stream-analytics-solution-patterns/pbidashboard.png)

Ez a megoldás néhány perc alatt létrehozható az Azure Portalról. Nincs kiterjedt kódolás szó, és az SQL nyelvet használják az üzleti logika kifejezésére.

Ez a megoldásminta a legalacsonyabb késést kínálja az eseményforrástól a Böngészőben lévő Power BI irányítópultjáig. Az Azure Stream Analytics az egyetlen Azure-szolgáltatás, amely rendelkezik ezzel a beépített képességgel.

## <a name="use-sql-for-dashboard"></a>Az SQL használata irányítópulthoz

A Power BI irányítópultja alacsony késleltetést kínál, de nem használható teljes értékű Power BI-jelentések készítésére. A közös jelentéskészítési minta az adatok kimeneti egy SQL-adatbázis először. Ezután a Power BI SQL-összekötője segítségével kérdezze le az SQL-t a legfrissebb adatokhoz.

![ASA SQL irányítópult](media/stream-analytics-solution-patterns/sqldashboard.png)

Az SQL-adatbázis használata nagyobb rugalmasságot biztosít, de egy kicsit nagyobb késés rovására. Ez a megoldás optimális az egy másodpercnél nagyobb késési igényű feladatokhoz. Ezzel a módszerrel maximalizálhatja a Power BI képességeit, hogy tovább szeletelhesse és felkockázhassa a jelentések adatait, és még sok más megjelenítési lehetőséget. Más irányítópult-megoldások, például a Tableau használatával is rugalmasan használ.

Az SQL nem nagy átviteli sebességű adattár. Az Azure Stream Analytics SQL-adatbázisának maximális átviteli sebessége jelenleg körülbelül 24 MB/s. Ha a megoldásban lévő eseményforrások nagyobb arányban hoznak létre adatokat, a Stream Analytics feldolgozási logikáját kell használnia a kimeneti sebesség SQL-re való csökkentéséhez. Olyan technikák, mint a szűrés, az ablakos aggregátumok, a temporális illesztésekkel való megfeleltetés, valamint az analitikus függvények használhatók. Az SQL kimeneti sebesség tovább optimalizálható az [Azure Stream Analytics Azure SQL Database kimenetében](stream-analytics-sql-output-perf.md)ismertetett technikákkal.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Valós idejű elemzéseket építhet be az alkalmazásba az eseményüzenetekkel

A Stream Analytics második legnépszerűbb használata a valós idejű riasztások létrehozása. Ebben a megoldásmintában a Stream Analytics üzleti logikája használható [a temporális és térbeli minták](stream-analytics-geospatial-functions.md) vagy [anomáliák](stream-analytics-machine-learning-anomaly-detection.md)észlelésére, majd riasztási jeleket hozhat létre. Azonban ellentétben az irányítópult-megoldással, ahol a Stream Analytics a Power BI-t használja előnyben részesített végpontként, számos köztes adatgyűjtő használható. Ezek a fogadók közé tartozik az Event Hubs, a Service Bus és az Azure Functions. Önnek, mint az alkalmazásszerkesztőnek el kell döntenie, hogy melyik adatfogadó működik a legjobban a forgatókönyvhöz.

A meglévő üzleti munkafolyamatban a riasztások létrehozásához végre kell hajtani az alsóbb rétegbeli eseményfogyasztói logikát. Mivel egyéni logikát valósíthat meg az Azure Functionsben, az Azure Functions a leggyorsabb módja ennek az integrációnak a végrehajtására. Az Azure-függvény streamanalytics-feladat kimenetként való használatának oktatóanyaga az [Azure Stream Analytics-feladatokból származó Azure-függvények futtatása című részben](stream-analytics-with-azure-functions.md)található. Az Azure Functions különböző típusú értesítéseket is támogat, például szöveget és e-mailt. Logikai alkalmazás is használható az ilyen integráció, a Stream Analytics és a Logic App event hubok.

![ASA eseményüzenet-kezelő alkalmazás](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Az Event Hubs ezzel szemben a legrugalmasabb integrációs pontot kínálja. Számos más szolgáltatás, például az Azure Data Explorer és a Time Series Insights is felhasználhatja az eseményeket event hubs. A szolgáltatások közvetlenül csatlakoztathatók az Event Hubs-fogadóhoz az Azure Stream Analytics-ből a megoldás befejezéséhez. Az Event Hubs egyben a legmagasabb átviteli üzenetküldési közvetítő is elérhető az Azure-ban az ilyen integrációs forgatókönyvekhez.

## <a name="dynamic-applications-and-websites"></a>Dinamikus alkalmazások és webhelyek

Az Azure Stream Analytics és az Azure SignalR Service használatával egyéni valós idejű vizualizációkat hozhat létre, például irányítópultot vagy térképmegjelenítést. A SignalR használatával a webes ügyfelek frissíthetők és valós időben jeleníthetők meg dinamikus tartalmakban.

![ASA dinamikus alkalmazás](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Valós idejű elemzési adatok beépítése az alkalmazásba az adattárakon keresztül

A legtöbb webes szolgáltatások és webes alkalmazások ma egy kérelem / válasz mintát szolgálja a bemutató réteg. A kérelem/válasz minta egyszerűen létrehozható, és könnyen méretezhető alacsony válaszidő egy állapotnélküli előtér és skálázható tárolja, például a Cosmos DB.

A nagy adatmennyiség gyakran teljesítményszűk keresztmetszeteket hoz létre a CRUD-alapú rendszerben. Az [eseménybeszerzési megoldás minta](/azure/architecture/patterns/event-sourcing) a teljesítmény szűk keresztmetszetek kezelésére szolgál. Időbeli minták és elemzések is nehéz és nem hatékony kinyerni egy hagyományos adattárból. A modern, nagy mennyiségű adatvezérelt alkalmazások gyakran fogadnak el adatfolyam-alapú architektúrát. Az Azure Stream Analytics, mint a mozgásban lévő adatok számítási motorja, az adott architektúra kapocsa.

![ASA eseménybeszervező alkalmazás](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Ebben a megoldási mintában az Eseményeket az Azure Stream Analytics dolgozza fel és összesíti adattárolókba. Az alkalmazásréteg a hagyományos kérés/válasz minta használatával kommunikál az adattárakkal. Mivel a Stream Analytics képes nagyszámú eseményt valós időben feldolgozni, az alkalmazás nagymértékben méretezhető anélkül, hogy tömegesen fel kellene nagyítania az adattárréteget. Az adattárréteg lényegében egy materializált nézet a rendszerben. [Az Azure Stream Analytics-kimenet az Azure Cosmos DB-hez](stream-analytics-documentdb-output.md) azt ismerteti, hogy a Cosmos DB hogyan használatos streamanalytics-kimenetként.

A valós alkalmazások, ahol a feldolgozási logika összetett, és szükség van a logika bizonyos részeinek független frissítésére, több Stream Analytics-feladatok is állnak együtt Event Hubs, mint a közvetítő esemény közvetítő.

![ASA komplex eseménybeszervező alkalmazás](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Ez a minta javítja a rendszer rugalmasságát és kezelhetőségét. Annak ellenére azonban, hogy a Stream Analytics pontosan a feldolgozás után garantálja, kis esély van arra, hogy ismétlődő események jelenhetnek meg a közvetítő eseményközpontokban. Fontos, hogy az alsóbb rétegbeli Stream Analytics-feladat a logikai kulcsok használatával egy visszatekintő ablakban az események et. Az eseménykézbesítésről további információt az [Eseménykézbesítési garanciák hivatkozása](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) című témakörben talál.

## <a name="use-reference-data-for-application-customization"></a>Referenciaadatok használata az alkalmazások testreszabásához

Az Azure Stream Analytics referenciaadatok szolgáltatás kifejezetten a végfelhasználói testreszabáshoz, például a riasztási küszöbértékhez, a feldolgozási szabályokhoz és [a geokerítésekhez](geospatial-scenarios.md)készült. Az alkalmazásréteg elfogadja a paramétermódosításokat, és sql-adatbázisban tárolja azokat. A Stream Analytics-feladat rendszeres időközönként lekérdezi az adatbázis változásait, és a testreszabási paramétereket referenciaadatok-illesztésen keresztül elérhetővé teszi. A referenciaadatok alkalmazás-testreszabáshoz való használatáról az [SQL referenciaadatok](sql-reference-data.md) és [a referenciaadatok illesztése című témakörben](/stream-analytics-query/reference-data-join-azure-stream-analytics)talál további információt.

Ez a minta egy szabálymotor megvalósítására is használható, ahol a szabályok küszöbértékei referenciaadatokból vannak meghatározva. A szabályokról további információt az [Azure Stream Analytics konfigurálható küszöbérték-alapú szabályainak feldolgozása című témakörben talál.](stream-analytics-threshold-based-rules.md)

![ASA referenciaadat-alkalmazás](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Gépi tanulás hozzáadása valós idejű elemzéseihez

Az Azure Stream Analytics beépített [anomáliadetektálási modellje](stream-analytics-machine-learning-anomaly-detection.md) kényelmes enitsza, hogy bemutassa a Machine Learninget a valós idejű alkalmazásnak. A Machine Learning-igények szélesebb körét az [Azure Stream Analytics integrálja az Azure Machine Learning pontozási szolgáltatásával.](stream-analytics-machine-learning-integration-tutorial.md)

Azon tapasztalt felhasználók számára, akik az online oktatást és pontozást ugyanabba a Stream Analytics-folyamatba szeretnék beépíteni, tekintse meg ezt a példát arra, hogyan kell ezt [lineáris regresszióval](stream-analytics-high-frequency-trading.md)megtenni.

![ASA Machine Learning alkalmazás](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Közel valós idejű adattárház

Egy másik gyakori minta a valós idejű adattárház, más néven streamelési adattárház. Az Event Hubs-hoz és az IoT Hubhoz az alkalmazásból érkező események mellett az [IoT Edge-en futó Azure Stream Analytics](stream-analytics-edge.md) az adatok tisztítására, az adatcsökkentésre, valamint az adattárolásra és továbbítási igényekre is használható. Az IoT Edge-en futó Stream Analytics kecsesen képes kezelni a sávszélesség korlátozását és a kapcsolattal kapcsolatos problémákat a rendszerben. Az SQL kimeneti adapter az SQL Data Warehouse-ba történő kimenetre használható; a maximális átviteli sebesség azonban 10 MB/s-ra korlátozódik.

![ASA adattárház](media/stream-analytics-solution-patterns/datawarehousing.png)

Az átviteli teljesítmény néhány késéses kompromisszumoval történő javításának egyik módja az események archiválása az Azure Blob storage-ba, majd importálja őket az [SQL Data Warehouse-ba a Polybase segítségével.](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md) Manuálisan kell összefűznie a Stream Analytics kimenetét a blobstorage blobtárolóhoz és a blobstorage-ból az SQL Data Warehouse-ba történő bevitelhez [az adatok időbélyeggel történő archiválásával](stream-analytics-custom-path-patterns-blob-storage-output.md) és rendszeres importálásával.

Ebben a használati mintában az Azure Stream Analytics közel valós idejű ETL-motorként használatos. Az újonnan érkező események et folyamatosan átalakítja és tárolja az alsóbb rétegbeli elemzési szolgáltatás felhasználása.

![ASA nagy átviteli sebességű adattárház](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Valós idejű adatok archiválása elemzéshez

A legtöbb adatelemzési és elemzési tevékenység továbbra is offline módban történik. Az adatokarchiválhatók az Azure Stream Analytics által az Azure Data Lake Store Gen2 kimeneti és parkettakimeneti formátumokon keresztül. Ez a funkció megszünteti az adatok közvetlen betáplálási zökkenőmentes betáplálása az Azure Data Lake Analytics, az Azure Databricks és az Azure HDInsight. Az Azure Stream Analytics közel valós idejű ETL-motorként használatos ebben a megoldásban. A Data Lake archivált adatait különböző számítási motorokkal fedezheti fel.

![ASA offline analitika](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Referenciaadatok használata a dúsításhoz

Az adatok dúsítása gyakran az ETL-motorok követelménye. Az Azure Stream Analytics támogatja az adatok bővítését az SQL-adatbázisból és az Azure Blob storageból származó [referenciaadatokkal.](stream-analytics-use-reference-data.md) Az adatok bővítésére az Azure Data Lake és az SQL Data Warehouse adatlandolása is elvégezhető.

![ASA offline elemzés adatbővítéssel](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Archivált adatokból származó elemzések működőképessé tétele

Ha az offline elemzési mintát a közel valós idejű alkalmazásmintával kombinálja, létrehozhat egy visszacsatolási hurkot. A visszacsatolási hurok lehetővé teszi, hogy az alkalmazás automatikusan állítsa be az adatok mintáinak módosítását. Ez a visszacsatolási ciklus lehet olyan egyszerű, mint a riasztási küszöbérték módosítása, vagy olyan összetett, mint a Machine Learning-modellek átképzése. Ugyanaz a megoldásarchitektúra alkalmazható a felhőben és az IoT Edge-en futó ASA-feladatokra is.

![ASA insights operationalization](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Az ASA-feladatok figyelése

Az Azure Stream Analytics-feladat a hét minden nap 24 órájában futtatható a bejövő események valós idejű folyamatos feldolgozásához. Az általános alkalmazás egészsége szempontjából alapvető fontosságú az állásidő-garancia. Bár a Stream Analytics az egyetlen olyan streamelési elemzési szolgáltatás az iparágban, amely [99,9%-os rendelkezésre állási garanciát](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)kínál, előfordulhat, hogy bizonyos szintű leállási idő merül fel. Az évek során a Stream Analytics mérőszámokat, naplókat és feladatállapotokat vezetett be, hogy tükrözzék a feladatok állapotát. Mindegyik az Azure Monitor szolgáltatáson keresztül érhető el, és tovább exportálható az OMS-be. További információt a [Stream Analytics-feladatfigyelés és a lekérdezések figyelésének ismertetése](stream-analytics-monitoring.md)című témakörben talál.

![ASA monitoring](media/stream-analytics-solution-patterns/monitoring.png)

Két fontos dolog van, amelyet figyelni kell:

- [Feladat sikertelen állapota](job-states.md)

    Az első és legfontosabb, meg kell győződnie arról, hogy a feladat fut. A feladat futtatása nélkül nem jön létre új metrikák vagy naplók. A feladatok különböző okok miatt változhatnak sikertelen állapotra, például magas SU-kihasználtsági szinttel (azaz elfogynak az erőforrások).

- [Vízjel késleltetési mutatói](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ez a metrika azt tükrözi, hogy milyen messze van a feldolgozási folyamat a falióra idő (másodperc). A késleltetés egy része a benne rejlő feldolgozási logikához van rendelve. Ennek eredményeképpen a növekvő tendencia nyomon követése sokkal fontosabb, mint az abszolút érték nyomon követése. Az állandósult állapot késleltetését az alkalmazás tervének kell kezelnie, nem pedig figyeléssel vagy figyelmeztetésekkel.

Hiba esetén a tevékenységnaplók és [a diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md) a legjobb helyek a hibák keresésének megkezdéséhez.

## <a name="build-resilient-and-mission-critical-applications"></a>Rugalmas és kritikus fontosságú alkalmazások létrehozása

Függetlenül attól, hogy az Azure Stream Analytics SLA garancia és milyen óvatos futtatni a end-to-end alkalmazás, kimaradások történnek. Ha az alkalmazás kritikus fontosságú, fel kell készülnie a kimaradások annak érdekében, hogy visszaszerezze kecsesen.

A riasztási alkalmazások, a legfontosabb dolog az, hogy észleli a következő riasztást. Dönthet úgy, hogy a korábbi riasztásokat figyelmen kívül hagyva a helyreállítás időpontjában az aktuális időponttól újraindítja a feladatot. A feladat kezdési idő szemantikája az első kimeneti idő, nem az első bemeneti idő. A bemenet visszatekerése megfelelő idővel garantálja, hogy az első kimenet a megadott időpontban befejeződött és helyes legyen. Ennek következtében nem kap részleges összesítéseket és eseményindító riasztásokat.

Azt is választhatja, hogy a kimenetet bizonyos időből a múltban indítsa el. Az Event Hubs és az IoT Hub adatmegőrzési szabályzatai is ésszerű mennyiségű adatot tartanak a múltból történő feldolgozás hoz. A kompromisszum az, hogy milyen gyorsan lehet felzárkózni az aktuális idő, és elkezd generálni időben új riasztásokat. Az adatok az idő múlásával gyorsan elveszítik értékvesztést, ezért fontos, hogy gyorsan felzárkózzon az aktuális időhöz. Kétféleképpen lehet gyorsan felzárkózni:

- További források (SU) kiépítése a felzárkózás során.
- Indítsa újra az aktuális időből.

Az aktuálisról való újraindítás az idő egyszerű, a feldolgozás során egy rés elhagyásának kompromisszumával. Az ilyen módszer újraindítása lehet, hogy rendben van a riasztások forgatókönyvek, de problémás lehet az irányítópult-forgatókönyvek, és nem induló archiválási és adattárház forgatókönyvek.

Több erőforrás kiépítése felgyorsíthatja a folyamatot, de a feldolgozási sebesség megugrásának hatása összetett.

- Tesztelje, hogy a feladat nagyobb számú SUs-ra méretezhető-e. Nem minden lekérdezés méretezhető. Meg kell győződnie arról, hogy a lekérdezés [párhuzamos.](stream-analytics-parallelization.md)

- Győződjön meg arról, hogy elegendő partíció van a felsőbb szintű Eseményközpontokban vagy az IoT Hubban, amelyek további átviteli egységek (Átviteli egységek) hozzáadásával skálázhatók a bemeneti átviteli érték. Ne feledje, hogy minden Event Hubtu 2 MB/s kimeneti sebességgel kimaxol.

- Győződjön meg arról, hogy elegendő erőforrást a kimeneti fogadók (azaz SQL Database, Cosmos DB), így nem szabályozza a túlfeszültség a kimenet, ami néha a rendszer zárolását okozhat.

A legfontosabb dolog az, hogy előre a feldolgozási sebesség változása, tesztelje ezeket a forgatókönyveket, mielőtt éles környezetbe lépne, és készen áll a feldolgozás megfelelő méretezésére a hiba-helyreállítási idő alatt.

Abban az extrém esetben, amikor a bejövő események mind késnek, [lehetséges, hogy az összes késleltetett esemény elesik,](stream-analytics-time-handling.md) ha késői érkezési ablakot alkalmazott a munkájához. A csepegés az események úgy tűnhet, hogy egy titokzatos viselkedés az elején; azonban, figyelembe véve a Stream Analytics egy valós idejű feldolgozó motor, arra számít, hogy a bejövő események közel vannak a falióra időhöz. El kell dobnia azokat az eseményeket, amelyek megsértik ezeket a korlátokat.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda architektúrák vagy backfill folyamat

Szerencsére az előző adatarchiválási minta használható a késői események kecses feldolgozásához. Az ötlet az, hogy az archiválási feladat feldolgozza a bejövő események et az érkezési időben, és az eseményeket a megfelelő időgyűjtőbe archiválja az Azure Blobban vagy az Azure Data Lake Store-ban az esemény időpontjával. Nem számít, milyen későn érkezik egy esemény, soha nem fogják dobni. Mindig a megfelelő idővödörben fog landolni. A helyreállítás során lehetőség van az archivált események újrafeldolgozására és az eredmények háttérfeltöltésére a választott tárolóba. Ez hasonló ahhoz, ahogyan a lambda mintákat megvalósítják.

![ASA háttöltés](media/stream-analytics-solution-patterns/backfill.png)

A háttérkitöltési folyamatot egy offline kötegelt feldolgozási rendszerrel kell elvégezni, amely valószínűleg más programozási modellel rendelkezik, mint az Azure Stream Analytics. Ez azt jelenti, hogy újra végre kell hajtania a teljes feldolgozási logikát.

A feltöltéshez még mindig fontos, hogy legalább ideiglenesen több erőforrást a kimeneti fogadók kezelésére nagyobb átviteli teljesítmény, mint az állandósult állapot feldolgozási igényeket.

|Forgatókönyvek  |Újraindítás mostantól csak  |Újraindítás az utolsó leállított időpontból |Indítsa újra mostantól + háttérkitöltés archivált eseményekkel|
|---------|---------|---------|---------|
|**Irányítópult**   |Héközt hoz létre    |OK a rövid leálláshoz    |Hosszú kimaradásesetén használható |
|**Riasztások kezelése**   |Elfogadható |OK a rövid leálláshoz    |Nem szükséges |
|**Eseménybeszervező alkalmazás** |Elfogadható |OK a rövid leálláshoz    |Hosszú kimaradásesetén használható |
|**Adattárház**   |Adatvesztés  |Elfogadható |Nem szükséges |
|**Offline elemzés**  |Adatvesztés  |Elfogadható |Nem szükséges|

## <a name="putting-it-all-together"></a>Végső összeállítás

Nem nehéz elképzelni, hogy a fent említett összes megoldásminta kombinálható egy összetett végpontok között. A kombinált rendszer tartalmazhat irányítópultokat, riasztást, eseménybeszerzési alkalmazást, adatraktározást és offline elemzési képességeket.

A legfontosabb, hogy a rendszert komptoktoképes mintákban tervezze meg, így minden alrendszer önállóan építhető, tesztelhető, frissíthető és helyreállítható.

## <a name="next-steps"></a>További lépések

Most már látta a különböző megoldásminták az Azure Stream Analytics használatával. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Hozzon létre egy Stream Analytics-feladatot az Azure PowerShell használatával.](stream-analytics-quick-create-powershell.md)
* [Stream Analytics-feladat létrehozása a Visual Studio használatával.](stream-analytics-quick-create-vs.md)
