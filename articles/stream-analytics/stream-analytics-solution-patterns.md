---
title: Az Azure Stream Analytics megoldás minták
description: További információk a megoldásminta az Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761978"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Az Azure Stream Analytics megoldás minták

Számos más-szolgáltatásokhoz hasonlóan az Azure Stream Analytics legjobb szolgál más szolgáltatásokkal nagyobb teljes körű megoldást hozhat létre. Ez a cikk ismerteti az Azure Stream Analytics-megoldások egyszerű és a különféle architekturális mintái. Ezek a minták olyan összetett megoldások a hozhat létre. Az ebben a cikkben leírt minták számos esetben használható. Példák a forgatókönyvre jellemző minták érhetők el a [Azure-megoldások architektúrái](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Hozzon létre egy Stream Analytics-feladatot egy valós idejű irányítópult

Az Azure Stream Analytics a könnyű használata akkor is gyorsan üzembe valós idejű irányítópultok és riasztások. Egy egyszerű megoldást a jól alkalmazkodik az Event Hubs vagy IoT hubot, és [-csatornák a streamelési adatkészlet a Power BI-irányítópult](/power-bi/service-real-time-streaming). További információkért tekintse meg a részletes oktatóanyag [a Stream Analytics-szel telefonhívás-adatok elemzése és megjelenítése a Power BI-irányítópult eredményt](stream-analytics-manage-job.md).

![ASA Power BI dashboard](media/stream-analytics-solution-patterns/pbidashboard.png)

Ez a megoldás csak pár perc alatt az Azure Portalról építhetők fel. Nincs a nincs kiterjedt kódolásra vesz részt, és az SQL-nyelv segítségével az üzleti logika express.

A valós idejű irányítópult megoldás minta kínál a legkisebb késés az eseményforrás egy böngészőben a Power BI-irányítópulton. Az Azure Stream Analytics egy csak az Azure-szolgáltatás ezt a beépített funkciót.

## <a name="use-sql-for-dashboard"></a>Irányítópult SQL használata

A Power BI-irányítópultot kínál a kis késleltetésű, de nem használható teljes értékű Power BI-jelentések előállításához. Jelentéskészítési egyik első kimeneti adatait egy SQL Database-adatbázishoz. A Power BI SQL-összekötővel SQL lekérdezési majd használata a legfrissebb adatokat.

![ASA SQL-irányítópult](media/stream-analytics-solution-patterns/sqldashboard.png)

Az SQL database több rugalmasságot tesz lehetővé késések rovására. Ez a megoldás akkor optimális nagyobb, mint egy második késési követelményekkel rendelkező feladatok. Ezzel a módszerrel maximalizálja a Power BI további szelet segédprogramot, és az adatok menően jelentésekhez. Más irányítópult megoldásokkal, például a Tableau, a rugalmasságot is kaphat.

Az SQL nem nagy átviteli sebességű adattár, és az Azure Stream Analytics egy SQL Database maximális átviteli sebesség MB/s-24. A eseményforrások a megoldás a magasabb adatok előállításához, ha szeretné használni feldolgozási logikáját a Stream Analytics SQL a kimenő forgalom csökkentése érdekében. A mintát technikák, például szűrhet, az ablakos összesítéseket, az időalapú illesztéseket megfelelő, és elemzési függvények használhatók. A kimeneti arány SQL tovább optimalizálható leírt technikákkal [az Azure SQL Database az Azure Stream Analytics-kimenetet](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Építhet be valós idejű betekintést az alkalmazás esemény üzenetkezeléssel

A második legnépszerűbb Stream Analytics használata valós idejű riasztásokat generálni. Megoldás ebben a mintában az üzleti logikát a Stream Analytics észleléséhez használható [historikus és térbeli mintázatok](stream-analytics-geospatial-functions.md) vagy [rendellenességek](stream-analytics-machine-learning-anomaly-detection.md), majd a termék riasztási jelek. Ellentétben a Stream Analytics, a Power BI használja elsődleges végpont irányítópult megoldást, azonban több köztes adatfogadókba használható. Ezek fogadóként közé tartozik az Event Hubs, a Service Bus és az Azure Functions. Mint az alkalmazás builder kell döntse el, melyik adatfogadó leginkább a forgatókönyvnek.

Alsóbb rétegbeli esemény fogyasztói logikai riasztásokat generálni a már meglévő üzleti munkafolyamatot a kell végrehajtani. Mivel az Azure Functions egyéni logikát valósíthat meg, a Funkciók, a leggyorsabb módot kínálni arra is elvégezheti ezt az integrációt. -Üzembehelyezési útmutató az Azure-függvény használatával tekintheti meg a Stream Analytics-feladat kimenetét [Azure Functions futtatása Azure Stream Analytics-feladatokból](stream-analytics-with-azure-functions.md). Az Azure Functions támogatja a különböző típusú, beleértve a szöveges és az e-mail értesítések is. Logikai alkalmazás ilyen az integráció, az Event Hubs a Stream Analytics és a logikai alkalmazás között is használhatók.

![ASA esemény üzenetkezelési alkalmazás](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event hubs is –, másrészt a legrugalmasabb integrációs pontot biztosít. Az Event hubs-Eseményközpontok eseményeinek számos olyan szolgáltatásokhoz, mint az Azure Data Explorer és a Time Series Insight használhatnak fel. Szolgáltatások közvetlenül az Event Hubs fogadó a csatlakoztathatók az Azure Stream Analytics a megoldás végrehajtásához. Az Event Hubs egyben a legmagasabb szintű teljesítmény üzenetközvetítő ilyen integrációs forgatókönyvek az Azure-ban elérhető.

## <a name="dynamic-applications-and-websites"></a>A dinamikus alkalmazások és webhelyek

Például az irányítópult egyéni valós idejű megjelenítéseket is létrehozhat, illetve térkép-Vizualizáció, az Azure Stream Analytics és az Azure SignalR Service segítségével. SignalR használatával, a webes ügyfelek frissíthetők, és valós idejű dinamikus tartalom megjelenítése.

![ASA dynamic app](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Építhet be valós idejű elemzéseket kaphat az alkalmazás adattárak keresztül

A legtöbb web services és a webalkalmazások még ma kérés/válasz mintát használja a megjelenítési réteg kiszolgálására. A kérelem/válasz a minta akkor egyszerűen hozhat létre, és a egy állapot nélküli előtér- és skálázható tárolók, például a Cosmos DB használatával válaszidő alacsony egyszerűen skálázhatók.

A nagy adatmennyiség CRUD-alapú rendszerekben gyakran a teljesítmény szűk hoz létre. A [event sourcing minta megoldás](/azure/architecture/patterns/event-sourcing) a teljesítmény szűk szolgál. A historikus mintákat és összefüggéseket: is nehéz és hatékony kinyeréséhez egy hagyományos adatokat az adattárból. A modern nagy mennyiségű adatvezérelt alkalmazások gyakran fogad el egy adatfolyam-alapú architektúrát. Az Azure Stream Analytics, a számítási motor a mozgásban lévő adatok egy fonalgyártójának az architektúrában.

![ASA-események forráskezelése alkalmazás](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Ez a megoldás mintában események feldolgozása és adattárak összesíti az Azure Stream Analytics által. Az alkalmazási rétegre adattárak a hagyományos kérés/válasz minta használatával kommunikál. Stream Analytics képes-e feldolgozni az események nagy száma miatt a valós idejű, az alkalmazás akkor jól skálázható fel az adatokat tároló réteg tömeges nélkül. A data store réteg lényegében a materializált nézet a rendszerben. [Az Azure Cosmos DB az Azure Stream Analytics-kimenetet](stream-analytics-documentdb-output.md) ismerteti a Cosmos DB egy Stream Analytics kimenetként használatáról.

A feldolgozó logika esetén összetett, hogy valódi alkalmazások van szükség, bizonyos részei a logikai frissítése egymástól függetlenül, több Stream Analytics-feladatok összeállítható az Event Hubs a közvetítő eseményszervező együtt.

![ASA összetett események forráskezelése alkalmazás](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Ez a minta javítja a rugalmasság és a kezelhetőség, a rendszer. Annak ellenére, hogy a Stream Analytics garantáltan a pontosan egyszeri feldolgozását, van azonban egy kis arra, hogy a duplikált események előfordulhat, hogy megnyitja a közvetítő az Event Hubs. Fontos a alsóbb rétegbeli Stream Analytics-feladat a deduplikációs eseményeket logikai kulcsokkal lookback ablakban. Eseménykézbesítés további információkért lásd: [esemény kézbesítési garanciával könnyítik](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) hivatkozást.

## <a name="use-reference-data-for-application-customization"></a>Referenciaadatok használata a kérelem testreszabáshoz

Az Azure Stream Analytics a referenciaadatok funkció kifejezetten készült végfelhasználói testreszabási például riasztási küszöbérték, szabályok, feldolgozása és [geokerítések](geospatial-scenarios.md). Az alkalmazási rétegre paraméter módosítások elfogadásához és a egy SQL-adatbázisban tárolja őket. A Stream Analytics-feladat rendszeres időközönként lekérdezi a változásokat az adatbázisból, és a testreszabási paraméterek a referencia-adatok csatlakozzon keresztül elérhető lesz. A kérelem testreszabáshoz referenciaadatok használatának további információkért lásd: [SQL referenciaadatok](sql-reference-data.md) és [referencia-adatok illesztési](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ez a minta is használható egy szabálymotorral megvalósításához ahol definiálva vannak a küszöbértékeket a szabályok a referenciaadatok. A szabályok további információkért lásd: [feldolgozni a konfigurálható küszöbérték-alapú szabályok az Azure Stream Analyticsben](stream-analytics-threshold-based-rules.md).

![Referencia-adatok alkalmazás ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adja hozzá a Machine Learning, a valós idejű elemzéseket

Az Azure Stream Analytics beépített [Anomáliadetektálás modell](stream-analytics-machine-learning-anomaly-detection.md) kényelmesen be a Machine Learning a valós idejű alkalmazását. A Machine Learning szélesebb tartományban van szüksége, lásd: [Azure Stream Analytics integrálható az Azure Machine Learning pontozási szolgáltatás](stream-analytics-machine-learning-integration-tutorial.md).

Haladó felhasználók, akik online tanítási és pontozási folyamatokat építhet be ugyanazt a Stream Analytics csővezetéket, lásd: hogyan tegye, hogy a jelen példa [lineáris regressziós](stream-analytics-high-frequency-trading.md).

![ASA Machine Learning-alkalmazás](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Közel valós idejű az adatraktározás terén

Egy másik egyik valós idejű data warehousing, más néven a streamelési data warehouse-bA. Az Event Hubs és az IoT Hub az alkalmazásból érkező események mellett [Azure Stream Analytics az IoT Edge-en futó](stream-analytics-edge.md) Adattisztítás, a adatcsökkentés, és az adattár és a továbbítás igények kielégítésére is használható. Az IoT Edge-en futó Stream Analytics szabályosan kezelhetik a sávszélesség-korlátozás és a kapcsolódási problémák a rendszerben. Az SQL kimeneti adapter is használható az SQL Data Warehouse; kimeneti azonban a maximális átviteli sebesség legfeljebb 10 MB/s.

![Adattárház-ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Egyik módja az egyes késés vonatkozóan kompromisszumot jelent a teljesítmény növelése érdekében, hogy az események archiválására Azure Blob storage-ba, majd [importálja azokat az SQL Data Warehouse a polybase-zel](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Meg kell manuálisan összefűzheti a kimeneti blob storage-bA a Stream Analytics és a bemeneti blob storage-ból az SQL Data Warehouse által [-tárhelyek időbélyegző szerint az adatok archiválása](stream-analytics-custom-path-patterns-blob-storage-output.md) és rendszeres időközönként importálása.

Ez a használati minta az Azure Stream Analytics egy közel valós idejű ETL-motort használják. Újonnan beérkező események is folyamatosan átalakíthatók és tárolhatók az alsóbb rétegbeli analytics szolgáltatás használata során.

![Az Adattárházak ASA lebonyolító](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Elemzés a valós idejű adatok archiválása

A legtöbb az adatelemzési és analitikai tevékenységeket továbbra is kapcsolat nélküli fordulhat elő. Adatok Azure Data Lake Store Gen2 kimeneti és a parquet eszközökben kimeneti formátumok archiválhatók az Azure Stream Analytics által. Ezt a funkciót eltávolítja az adatokat közvetlenül az Azure Data Lake Analytics, az Azure Databricks és az Azure HDInsight-hírcsatornára fennakadások nélkül használható. Az Azure Stream Analytics használatban van ebben a megoldásban közel valós idejű ETL-motor. Áttekintheti a Data Lake az archivált adatok különböző számítási motorok használatával.

![A kapcsolat nélküli ASA-elemzés](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Referenciaadatok használata Adatbővítés

Kibővített adatok esetében gyakran ETL motorok követelmény. Az Azure Stream Analytics támogatja a kibővített adatok [referenciaadatok](stream-analytics-use-reference-data.md) az SQL database és az Azure Blob storage. Az adatok Azure Data Lake és az SQL Data Warehouse üzenetsorokra Adatbővítés teheti meg.

![A kapcsolat nélküli elemzések ASA Adatbővítés](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Elemezheti az archivált adatok üzembe helyezése

Ha a kapcsolat nélküli analitikai mintázat kombinálva a közel valós idejű alkalmazás minta, létrehozhat egy visszacsatolási hurokba kerülnek. A visszajelzési ciklus lehetővé teszi, hogy az alkalmazás automatikusan módosítsa a változó adatok. Ez a visszajelzési ciklus egyszerűen vonatkozó riasztási küszöbérték megváltoztatása vagy összetetteket, mint a Machine Learning-modellek átképezési lehet. Az azonos megoldásarchitektúrában mindkét ASA-feladatok futtatása a felhőben és az IoT Edge-ben is alkalmazható.

![ASA insights operationalization](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA-feladatok figyelése

Azure Stream Analytics-feladat futtatható 24/7 bejövő események folyamatos, valós idejű feldolgozásához. A hasznos üzemidő garancia elengedhetetlen az alkalmazás általános állapotát. Az iparág által kínált csak streaming analytics szolgáltatás pedig a Stream Analytics egy [99,9 %-os rendelkezésre állást garantálunk](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), előfordulhat, hogy továbbra is díjat kell bizonyos szintű leállás. Az évek során a Stream Analytics metrikákat, naplókat és a feladatok állapotát tükröző feladatállapotok vezetett. Ezek mindegyike végzetesnek Azure Monitor szolgáltatásban, és az OMS-nek további exportálhatók. További információkért lásd: [feladat megismerheti a Stream Analytics-figyelés és lekérdezések figyelése](stream-analytics-monitoring.md).

![ASA figyelése](media/stream-analytics-solution-patterns/monitoring.png)

Két dolgot kulcsok figyelése:

- [Feladat állapota nem sikerült](job-states.md)

    Mindenekelőtt győződjön meg arról, hogy a feladat fut. szeretné. A feladat futó állapotba kerül, nélkül nincs új metrikákat vagy naplókat akkor jönnek létre. Feladatok különböző okok miatt, beleértve a SU kihasználtsági magas szintű sikertelen állapotba módosíthatja (azaz a kevés erőforrás).

- [Vízjel késleltetés metrikák](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ez a metrika tükrözi milyen mögött a feldolgozási folyamat valós idő (másodperc) szerepel. A késleltetés némelyike tudható be a rejlő feldolgozási logikáját. Ennek eredményeképpen a növekvő tendenciát mutatnak figyelési fontos sokkal több, mint a figyelést az abszolút értékét. Az egyenletes állapotú késleltetés beavatkozást igényel az alkalmazások kialakítását, amelyet nem a figyelést és riasztásokat tesz elérhetővé.

Hiba esetén a Tevékenységnaplók és [diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md) a helyeken hibákat keres a kezdéshez.

## <a name="build-resilient-and-mission-critical-applications"></a>Rugalmas build és a működés szempontjából kritikus fontosságú alkalmazások

Függetlenül az Azure Stream Analytics SLA-garanciára, és hogyan gondos futtatja a teljes körű alkalmazást, valamilyen okból kimaradás lép fordulhat elő. Ha az alkalmazás kritikus fontosságú, meg kell valamilyen okból kimaradás lép fel készülnöm annak érdekében, hogy szabályosan helyreállítani.

Riasztási alkalmazások, a legfontosabb, hogy észlelje, a következő riasztás. Dönthet úgy, hogy indítsa újra a feladatot az aktuális időt állít helyre, korábbi riasztások figyelmen kívül hagyja. A feladat kezdési idő szemantikát kimeneti először, nem az első bemeneti időpont szerint is. A bemeneti visszamenőleges visszatekeri megfelelő mennyi idő biztosítsák az első kimeneti, a megadott időpontban befejeződött, és helyes-e. Nem olvashatók be a részleges összesítések és riasztások aktiválása váratlanul eredményeként.

Dönthet úgy is, kimeneti kezdő dátuma a múltban bizonyos mennyiségű. Az Event Hubs és az IoT Hub adatmegőrzési házirendek, hogy a feldolgozást az elmúlt egy ésszerű adatmennyiség tartsa. Az egyensúlyt a rendszer milyen gyors az aktuális időpontnál bepótolják, és létrehozhat új riasztások időben megjelenjenek. Adatok elveszíti az értékét gyorsan idővel, ezért fontos, hogy gyorsan feltárkózni az aktuális idő. Gyorsan feltárkózni két módja van:

- További erőforrások (SU) kiépítése, amikor elfogja-e.
- Indítsa újra az aktuális időponthoz képest.

A jelenlegi újraindítása az idő az egyszerű ehhez, azzal a kompromisszummal jár megőrzésének költségével eseményáramlási kimaradást feldolgozása során. Ezzel a módszerrel újraindítása előfordulhat, hogy OK forgatókönyvek, riasztások, de irányítópult forgatókönyvekhez problémás lehet és nem-alapszintű archiválás és az adatraktározási forgatókönyvekben.

További erőforrások kiépítése felgyorsíthatja a folyamatot, de a hatást, hogy a feldolgozási sebesség megnő összetett.

- Az ellenőrzés, hogy a feladat nem méretezhető, több SUS-t. Nem minden lekérdezéseket is méretezhető. Győződjön meg arról, hogy a lekérdezés kell [párhuzamosíthatók](stream-analytics-parallelization.md).

- Ellenőrizze, hogy nincsenek elegendő partíciót a felsőbb szintű Event Hubs vagy IoT hubot, hogy további átviteli egységek átviteli (egységek) a bemeneti átviteli méretezését is hozzáadhat. Ne feledje, hogy minden Event Hubs átviteli egység lefoglalja meg egy 2 MB/s kimeneti mellett.

- Ellenőrizze, hogy elegendő erőforrás a kimeneti fogadóinak (azaz a SQL Database, Cosmos DB) kiépítése, így azok nem korlátozhatja a kimenetben, amely időnként is megnő, hogy a rendszer zárolja.

A legfontosabb, hogy a feldolgozási sebesség módosítás várhatóan, ezeket a forgatókönyveket, mielőtt éles környezetben tesztelése és készen áll a feldolgozás sikertelen helyreállítás ideje alatt megfelelően skálázhatja.

A szélsőséges esetben, hogy a bejövő események minden késnek, [lehetséges minden, a rendszer elveti a késleltetett események](stream-analytics-time-handling.md) alkalmazott a feladat egy késői beérkező ablakot. Az események az eldobása úgy tűnhet, hogy lehet egy frusztráló működés elején; azonban considering a Stream Analytics egy valós idejű motor, akkor vár bejövő eseményeket, hogy a valós idő közelében kell. Ezen korlátozást megsértő események eldobni rendelkezik.

### <a name="backfilling-process"></a>Backfilling folyamat

Szerencsére az előző archiválási adatmintát ezek későn érkező események feldolgozása szabályosan használható. Az lényege, hogy az archiválási feladat bejövő dolgozza fel a érkezési ideje, és archiválja az eseményeket a megfelelő időben gyűjtőhöz, az Azure Blob- vagy Azure Data Lake Store az esemény időpontját. Nem számít, hogy hogyan késői esemény érkezik, a rendszer soha nem lehet elvetni. A megfelelő időben gyűjtőhöz mindig megnyitja azt. A helyreállítás során az archivált eseményeket és visszatöltési az eredményeket, és a tároló választott kockapartíció újbóli lehetőség.

![ASA visszatöltési](media/stream-analytics-solution-patterns/backfill.png)

A visszatöltési folyamat rendelkezik egy offline állapotban van a rendszer, amely valószínűleg rendelkezik, mint az Azure Stream Analytics különböző programozási modell feldolgozása kötegelt végezni. Ez azt jelenti, hogy újra a teljes feldolgozási logika.

Backfilling továbbra is fontos, hogy legalább átmenetileg üzembe helyezése több erőforrást a kimeneti fogadók, mint stabil állapotban kell feldolgozása magasabb átviteli sebesség kezelésére.

|Alkalmazási helyzetek  |Indítsa újra most csak a  |Indítsa újra az utolsó Leállítás időpontja |Indítsa újra a now + visszatöltési archivált események|
|---------|---------|---------|---------|
|**Dashboarding**   |Hozza létre a térköz    |A rövid kimaradás OK    |A hosszú szolgáltatáskimaradás használata |
|**Riasztások kezelése**   |Elfogadható |A rövid kimaradás OK    |Nem szükséges |
|**Események forráskezelése alkalmazás** |Elfogadható |A rövid kimaradás OK    |A hosszú szolgáltatáskimaradás használata |
|**Adatraktározás**   |Adatvesztés  |Elfogadható |Nem szükséges |
|**A kapcsolat nélküli analytics**  |Adatvesztés  |Elfogadható |Nem szükséges|

## <a name="putting-it-all-together"></a>Végső összeállítás

Nem áll az imagine, hogy a fent említett megoldásminta is egyesíthet egy összetett, végpontok közötti rendszerben rögzített. A kombinált rendszer irányítópultokat, riasztások, események forráskezelése alkalmazás, adattárház- és offline elemzési képességeket tartalmazhatnak.

A kulcs a tervezési algyűjteményeinek összefüggő minták, a rendszer minden egyes alrendszer építhetők fel, így teszteltük, frissítve, és a helyreállításra egymástól függetlenül.

## <a name="next-steps"></a>További lépések

Az Azure Stream Analytics megoldás minták számos most láthatta. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md).
* [Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md).
