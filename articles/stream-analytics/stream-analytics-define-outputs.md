---
title: A Azure Stream Analytics kimenetének megismerése
description: Ez a cikk a Azure Stream Analyticsban elérhető adatkimeneti beállításokat ismerteti, beleértve az elemzési eredmények Power BIét is.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475229"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>A Azure Stream Analytics kimenetének megismerése

Ez a cikk egy Azure Stream Analytics feladatokhoz elérhető kimenetek típusait ismerteti. A kimenetek lehetővé teszik a Stream Analytics feladatok eredményének tárolását és mentését. A kimeneti adatokat felhasználva további üzleti elemzéseket és adattárház-adatokat is végezhet.

Amikor megtervezi a Stream Analytics lekérdezést, a [INTO záradék](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)használatával tekintse meg a kimenet nevét. A lekérdezésben több INTO záradékot is használhat feladatokhoz, vagy több kimeneti adatfolyam-feladatokhoz (ha szüksége van rájuk).

Stream Analytics feladatok kimenetének létrehozásához, szerkesztéséhez és teszteléséhez használhatja a [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), a [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), a [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), a [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)és a [Visual Studio alkalmazást](stream-analytics-quick-create-vs.md).

Egyes kimeneti típusok támogatják a [particionálást](#partitioning). A [kimeneti köteg mérete](#output-batch-size) eltérő az átviteli sebesség optimalizálása érdekében.


## <a name="azure-data-lake-storage-gen-1"></a>1. generációs Azure Data Lake Storage

A Stream Analytics támogatja a [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). A Azure Data Lake Storage egy nagyvállalati szintű, nagy kapacitású adattár big data analitikus számítási feladatokhoz. A Data Lake Storage használatával bármilyen méretű, típusú és feldolgozási sebességű adatot tárolhat az operatív és a felderítő elemzésekhez. Stream Analytics jogosultsággal kell rendelkeznie a Data Lake Storage eléréséhez.

Stream Analytics Azure Data Lake Storage kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

A következő táblázat felsorolja a tulajdonságok nevét és a hozzájuk tartozó leírásokat a Data Lake Storage 1. generációs kimenetének konfigurálásához.   

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének Data Lake Storeba való irányítására szolgál. |
| Előfizetés | Az Azure Data Lake Storage-fiókot tartalmazó előfizetés. |
| Fióknév | Annak a Data Lake Store-fióknak a neve, ahová a kimenetet küldi. Az előfizetésében elérhető Data Lake Store fiókok legördülő listája jelenik meg. |
| Elérésiút-előtag mintája | A fájlnak a megadott Data Lake Store fiókban való írásához használt elérési út. Megadhatja a (z) {date} és a {Time} változók egy vagy több példányát:<br /><ul><li>1. példa: Mappa1/naplók/{Date}/{Time}</li><li>2. példa: Mappa1/naplók/{Date}</li></ul><br />A létrehozott mappa struktúrájának időbélyegzője az UTC és a helyi idő szerint történik.<br /><br />Ha a fájl elérési útjának mintája nem tartalmaz záró perjelet (/), a fájl elérési útjának utolsó mintázata fájlnév-előtagként lesz kezelve. <br /><br />Az új fájlok a következő esetekben jönnek létre:<ul><li>Változás a kimeneti sémában</li><li>A feladatok külső vagy belső újraindítása</li></ul> |
| Dátumformátum | Választható. Ha a Date tokent az előtag elérési útján használja, kiválaszthatja a fájlok rendszerezésének dátumát. Példa: éééé/hh/nn |
|Időformátum | Választható. Ha az időtokent az előtag elérési útja használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak.|
| Encoding | Ha CSV-vagy JSON-formátumot használ, meg kell adni egy kódolást. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.|
| Elválasztó | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-adattárolás szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv.|
| Formátum | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva**lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON.  A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. Ezt a tömböt csak akkor zárja be a rendszer, ha a feladatot leállítja vagy Stream Analytics áthelyezte a következő időszakra. Általánosságban elmondható, hogy a rendszer inkább a sortöréses JSON-t használja, mert nem igényel semmilyen speciális kezelést, amíg a kimeneti fájl továbbra is írásra kerül.|
| Hitelesítési módszer | A [felügyelt identitás](stream-analytics-managed-identities-adls.md) vagy felhasználói jogkivonat használatával engedélyezheti a Data Lake Storage-fiók elérését. A hozzáférés engedélyezése után visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladathoz tartozó Data Lake Storage kimenet törlésével vagy a Stream Analyticsi feladatok törlésével. |

## <a name="sql-database"></a>SQL Database

A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a természettel rokon adatokhoz, illetve olyan alkalmazásokhoz is használhatja, amelyek a kapcsolódó adatbázisban tárolt tartalomtól függenek. Stream Analytics feladatok írását egy meglévő táblába SQL Database. A tábla sémájának pontosan egyeznie kell a feladatok kimenetében szereplő mezőkkel és típusokkal. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kimenetként is megadható kimenetként a SQL Database output (kimenet) lehetőség használatával. Ha többet szeretne megtudni az írási sebesség javításának módjairól, tekintse meg a [Azure SQL Database as stream Analytics a kimenettel](stream-analytics-sql-output-perf.md) című cikket.

[Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) kimenetként is használható. [Azure SQL Database felügyelt példányban konfigurálnia kell a nyilvános végpontot](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , majd manuálisan kell konfigurálnia a következő beállításokat a Azure stream Analyticsban. A SQL Servert futtató Azure-beli virtuális gépeket az alábbi beállítások manuális konfigurálásával is támogatja.

A következő táblázat felsorolja a tulajdonságok nevét és leírását SQL Database kimenet létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid név, amely a lekérdezésekben a lekérdezés kimenetének az adatbázisba való irányításához használatos. |
| Adatbázis | Annak az adatbázisnak a neve, ahová a kimenetet küldi. |
| Kiszolgálónév | A SQL Database-kiszolgáló neve. Azure SQL Database felügyelt példány esetében az 3342-es portot kell megadni. Például: *sampleserver. public. database. Windows. net, 3342* |
| Felhasználónév | Az adatbázishoz írási hozzáféréssel rendelkező Felhasználónév. A Stream Analytics csak az SQL-hitelesítést támogatja. |
| Jelszó | Az adatbázishoz való kapcsolódáshoz használt jelszó. |
| Tábla | A tábla neve, ahol a kimenet meg van írva. A tábla neve megkülönbözteti a kis-és nagybetűket. A táblázat sémájának pontosan meg kell egyeznie a feladatok által létrehozott mezők és típusok számával. |
|Partíciós séma öröklése| Az előző lekérdezési lépés particionálási sémájának öröklésére szolgáló lehetőség, amely lehetővé teszi, hogy teljesen párhuzamos topológiát engedélyezzen több íróival a táblához. További információ: [Azure stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Kötegek maximális száma| Az összes tömeges beszúrási tranzakcióval ellátott rekordok számának ajánlott felső korlátja.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>BLOB Storage és Azure Data Lake Gen2

A Data Lake Storage Gen2 lehetővé teszi az Azure Storage számára, hogy a vállalati adattavakat az Azure-ban hozza létre. Több petabájt-szolgáltatás megkezdése és kiszolgálása mellett a Data Lake Storage Gen2 segítségével könnyedén kezelhet nagy mennyiségű adatot. Data Lake Storage Gen2 alapvető része egy hierarchikus névtér hozzáadása a blob Storage-hoz.

Az Azure Blob Storage költséghatékony és méretezhető megoldást kínál nagy mennyiségű strukturálatlan adat tárolására a felhőben. A blob Storage és annak használatának bemutatása: Blobok [feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

A következő táblázat felsorolja a tulajdonságok nevét, valamint a Blobok vagy ADLS Gen2 kimenetek létrehozásához szükséges leírásokat.

| Tulajdonság neve       | Leírás                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Kimeneti alias        | Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a blob-tárolóba történő irányítására szolgál. |
| Tárfiók     | Annak a Storage-fióknak a neve, ahová a kimenetet küldi.               |
| Storage-fiók kulcsa | A Storage-fiókhoz társított titkos kulcs.                              |
| Storage-tároló   | Az Azure Blob serviceban tárolt Blobok logikai csoportosítása. Amikor feltölt egy blobot a Blob serviceba, meg kell adnia egy tárolót a blobhoz. |
| Elérésiút-minta | Választható. A fájl elérési útjának mintája, amely a Blobok megadott tárolón belüli írásához használatos. <br /><br /> Az elérési út mintájában dönthet úgy, hogy a dátum és idő változók egy vagy több példányát használja a Blobok írásának megadásához: <br /> {Date}, {Time} <br /><br />Egyéni blob-particionálással megadhat egy egyéni {Field} nevet az esemény adataiból a Blobok particionálásához. A mező neve alfanumerikus, és tartalmazhat szóközt, kötőjelet és aláhúzást. Az egyéni mezőkre vonatkozó korlátozások a következők: <ul><li>A mezőnevek nem megkülönböztetik a kis-és nagybetűket. A szolgáltatás például nem tud különbséget tenni az "ID" oszlop és az "id" oszlop között.</li><li>Beágyazott mezők használata nem engedélyezett. Ehelyett használjon egy aliast a feladatokhoz a lekérdezésben a mező kisimításához.</li><li>A kifejezések nem használhatók mező neveként.</li></ul> <br />Ez a szolgáltatás lehetővé teszi az egyéni Dátum-/időformátumok megadott beállításainak használatát az elérési úton. Az egyéni dátum-és időformátumokat egyszerre kell megadni, a (z) {datetime:\<megadási>} kulcsszóval elkerítve. A megadott> engedélyezett \<bemenetei: ÉÉÉÉ, hh, M, DD, d, hh, H, mm, m, SS vagy s. A (z)\<{datetime: megadási>} kulcsszó többször is használható az elérési úton az egyéni dátum-/időkonfigurációk létrehozásához. <br /><br />Példák: <ul><li>1. példa: cluster1/naplók/{Date}/{Time}</li><li>2. példa: cluster1/naplók/{Date}</li><li>3. példa: cluster1/{client_id}/{Date}/{Time}</li><li>4. példa: cluster1/{datetime: SS}/{myField}, ahol a lekérdezés: válassza az adatok. myField lehetőséget myField a bemenettől;</li><li>5. példa: cluster1/év = {datetime: ÉÉÉÉ}/hónap = {datetime: PP}/nap = {datetime: DD}</ul><br />A létrehozott mappa struktúrájának időbélyegzője az UTC és a helyi idő szerint történik.<br /><br />A fájlok elnevezése a következő konvenciót használja: <br /><br />{Path előtag-minta}/schemaHashcode_Guid_Number. bővítmény<br /><br />Példa kimeneti fájlokra:<ul><li>Myoutput/20170901/00/45434_gguid_1. csv</li>  <li>Myoutput/20170901/01/45434_gguid_1. csv</li></ul> <br />További információ erről a szolgáltatásról: [Azure stream Analytics egyéni blob kimeneti particionálás](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Dátumformátum | Választható. Ha a Date tokent az előtag elérési útján használja, kiválaszthatja a fájlok rendszerezésének dátumát. Példa: éééé/hh/nn |
| Időformátum | Választható. Ha az időtokent az előtag elérési útja használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | A kimeneti adatokat a szerializálási formátum. A JSON, a CSV, a Avro és a Parquet támogatott. |
|Minimális sorok (csak parketta esetén)|A kötegek minimális sorainak száma. A parketta esetében minden köteg létrehoz egy új fájlt. Az aktuális alapértelmezett érték 2 000 sor, és az engedélyezett maximális érték 10 000 sor.|
|Maximális idő (csak Parkett)|A maximális várakozási idő tételenként. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. Az aktuális alapértelmezett érték 1 perc, az engedélyezett maximum pedig 2 óra. Ha a blob kimenetének elérésiút-mintázati gyakorisága van, a várakozási idő nem lehet nagyobb, mint a partíció időtartománya.|
| Encoding    | Ha CSV-vagy JSON-formátumot használ, meg kell adni egy kódolást. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó   | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-adattárolás szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum      | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva**lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. Ezt a tömböt csak akkor zárja be a rendszer, ha a feladatot leállítja vagy Stream Analytics áthelyezte a következő időszakra. Általánosságban elmondható, hogy a rendszer inkább a sortöréses JSON-t használja, mert nem igényel semmilyen speciális kezelést, amíg a kimeneti fájl továbbra is írásra kerül. |

Ha a blob Storage-ot kimenetként használja, a következő esetekben létrejön egy új fájl a blobban:

* Ha a fájl túllépi az engedélyezett blokkok maximális számát (jelenleg 50 000). Elérheti a maximálisan megengedett számú blokkot anélkül, hogy elérte a Blobok maximálisan megengedett méretét. Ha például a kimeneti sebesség magas, több bájt/blokk látható, és a fájlméret nagyobb. Ha a kimeneti sebesség alacsony, minden blokk kevesebb adattal rendelkezik, és a fájlméret kisebb.
* Ha a kimenetben séma változik, és a kimeneti formátumhoz rögzített séma szükséges (CSV és Avro).
* Ha egy feladat újra lett indítva, vagy külsőleg egy felhasználó leállítja és elindítja, vagy belsőleg a rendszerkarbantartáshoz vagy a hiba helyreállításához.
* Ha a lekérdezés teljesen particionálva van, és minden kimeneti partícióhoz létrejön egy új fájl.
* Ha a felhasználó töröl egy fájlt vagy egy tárolót a Storage-fiókból.
* Ha a kimenet időkorlátja az elérési út előtagja, és a rendszer egy új blobot használ, amikor a lekérdezés a következő órára kerül.
* Ha a kimenetet egy egyéni mező particionálja, és egy partíciós kulcsban létrehoz egy új blobot, ha az nem létezik.
* Ha a kimenetet egy olyan egyéni mező particionálja, amelyben a partíciós kulcs kardinális mérete meghaladja az 8 000-ot, és a partíciós kulcs egy új blobot hoz létre.

## <a name="event-hubs"></a>Event Hubs

Az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatás egy nagymértékben méretezhető közzétételi és előfizetési esemény betöltése. Másodpercenként akár több millió eseményt is gyűjthet. Az Event hub egyik használata kimenetként, amikor egy Stream Analytics-feladatok kimenete lesz egy másik folyamatos átviteli feladathoz tartozó bemenet. További információ az üzenetek maximális méretéről és a Batch méretének optimalizálásáról: [kimeneti köteg mérete](#output-batch-size) szakasz.

Az Event hubokból kimenetként kell konfigurálnia az adatfolyamokat.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Egy rövid név, amelyet a lekérdezések a lekérdezés kimenetének az Event hub-ba történő irányításához használnak. |
| Eseményközpont-névtér | Az üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor létrehozott egy Event hub-névteret is. |
| Event Hubs neve | Az Event hub kimenetének neve. |
| Event hub-házirend neve | A megosztott elérési házirend, amelyet az Event hub **configure (Konfigurálás** ) lapján lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| Event hub-házirend kulcsa | Az Event hub-névtérhez való hozzáférés hitelesítéséhez használt megosztott elérési kulcs. |
| Partíciós kulcs oszlopa | Választható. Az Event hub kimenetének partíciós kulcsát tartalmazó oszlop. |
| Eseményszerializációs formátum | A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak. |
| Encoding | A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-formátumú adatszerializáláshoz. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva**lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva.  |
| Tulajdonságok oszlopai | Választható. Vesszővel tagolt oszlopokat kell csatolni a kimenő üzenet felhasználói tulajdonságaihoz a hasznos adatok helyett. A szolgáltatással kapcsolatos további információkért lásd: [Egyéni metaadatok tulajdonságai a kimenethez](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

A Stream Analytics feladatokhoz [Power bi](https://powerbi.microsoft.com/) kimenetként használhatja az elemzési eredmények gazdag vizualizációs élményének biztosításához. Ezt a funkciót használhatja az operatív irányítópultokhoz, a jelentéskészítéshez és a metrikus alapú jelentéskészítéshez.

Stream Analytics Power BI kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

A következő táblázat felsorolja a tulajdonságok nevét és leírásait a Power BI kimenetének konfigurálásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Adjon meg egy rövid nevet, amelyet a lekérdezésekben használ a lekérdezés kimenetének a Power BI kimenetre való irányításához. |
| Csoport munkaterülete |Ha az adatmegosztást más Power BI felhasználókkal szeretné engedélyezni, válassza ki a csoportokat a Power BI-fiókban, vagy válassza **a saját munkaterület** lehetőséget, ha nem szeretne írni egy csoportba. Egy meglévő csoport frissítéséhez a Power BI hitelesítés megújítása szükséges. |
| Adatkészlet neve |Adja meg azt az adatkészlet-nevet, amelyet a Power BI kimenetének használni szeretne. |
| Tábla neve |Adja meg a tábla nevét a Power BI kimenetének adatkészletében. Jelenleg Power BI Stream Analytics feladatokból származó kimenetnek csak egy táblája lehet egy adatkészletben. |
| Kapcsolat engedélyezése | A kimeneti beállítások konfigurálásához engedélyeznie kell a Power BI. Miután engedélyezte ezt a kimenetet a Power BI-irányítópulthoz, visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladatok kimenetének törlésével vagy a Stream Analytics feladatok törlésével. | 

A Power BI kimenetének és irányítópultjának konfigurálásához lásd: [Azure stream Analytics és Power bi](stream-analytics-power-bi-dashboard.md) oktatóanyag.

> [!NOTE]
> Ne explicit módon hozza létre az adatkészletet és a táblát a Power BI irányítópulton. Az adatkészlet és a tábla automatikusan fel lesz töltve a feladatok elindításakor, és a feladattal Power BIba kerül a bepumpálás. Ha a feladatütemezés nem hoz létre eredményeket, az adatkészlet és a tábla nem jön létre. Ha Power BI már van olyan adatkészlet és tábla, amelynek a neve megegyezik az ebben a Stream Analytics feladatban megadott névvel, a rendszer felülírja a meglévő adatokat.
>

### <a name="create-a-schema"></a>Séma létrehozása
Azure Stream Analytics létrehoz egy Power BI adatkészletet és egy tábla sémát a felhasználó számára, ha még nem léteznek. Minden más esetben a tábla új értékekkel frissül. Jelenleg csak egy tábla létezhet egy adatkészleten belül. 

A Power BI az első, az első kimenő (FIFO) adatmegőrzési szabályt használja. Az adatok egy táblában lesznek gyűjtve, amíg 200 000 sort nem üt.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Adattípus átalakítása Stream Analyticsból Power BI
Azure Stream Analytics az adatmodellt dinamikusan frissíti futásidőben, ha a kimeneti séma megváltozik. Az oszlop neve megváltozik, az oszlop típusa megváltozik, az oszlopok hozzáadása vagy eltávolítása pedig nyomon követhető.

Ez a táblázat a [stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) adattípusok adattípusra való átalakítását ismerteti Power bi [Entity EDM-típusokra](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), ha Power bi adatkészlet és tábla nem létezik.

Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar (max.) | Sztring
dátum/idő | Datetime
lebegőpontos | Double
Tömb rögzítése | Karakterlánc típusa, konstans érték: "IRecord" vagy "IArray"

### <a name="update-the-schema"></a>A séma frissítése
Stream Analytics kikövetkezteti az adatmodell-sémát a kimenetben szereplő első események alapján. Később, ha szükséges, az adatmodell-séma úgy frissül, hogy befogadja a bejövő eseményeket, amelyek esetleg nem férnek hozzá az eredeti sémához.

Kerülje a `SELECT *` lekérdezésben, hogy megakadályozza a dinamikus séma frissítését a sorok között. A lehetséges teljesítmény-kihatások mellett előfordulhat, hogy az eredményekhez szükséges idő bizonytalan. Válassza ki azokat a pontos mezőket, amelyeket meg kell jeleníteni a Power BI irányítópulton. Emellett az adatértékeknek meg kell felelniük a választott adattípusnak.


Előző/aktuális | Int64 | Sztring | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sztring | Sztring | Double
Double | Double | Sztring | Sztring | Double
Sztring | Sztring | Sztring | Sztring | Sztring 
Datetime | Sztring | Sztring |  Datetime | Sztring

## <a name="table-storage"></a>Table Storage

Az [Azure Table Storage](../storage/common/storage-introduction.md) kiválóan elérhető, nagy mértékben méretezhető tárhelyet kínál, így az alkalmazások automatikusan méretezhetők a felhasználói igények kielégítése érdekében. A Table Storage a Microsoft NoSQL kulcs-és attribútum-tárolója, amelyet a séma kevesebb korlátozásával rendelkező strukturált adatkészletekhez használhat. Az Azure Table Storage az adatok megőrzését és hatékony lekérését is képes tárolni.

A következő táblázat felsorolja a tulajdonságok nevét és a táblázat kimenetének létrehozásához szükséges leírásokat.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid név, amelyet a lekérdezések a lekérdezés kimenetének a táblázatos tárolóba való irányításához használnak. |
| Tárfiók |Annak a Storage-fióknak a neve, ahová a kimenetet küldi. |
| Storage-fiók kulcsa |A Storage-fiókhoz társított hozzáférési kulcs. |
| Tábla neve |A tábla neve. A tábla akkor jön létre, ha nem létezik. |
| Partíciókulcs |A partíciós kulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs a partíció egyedi azonosítója egy olyan táblán belül, amely az entitás elsődleges kulcsának első részét képezi. Ez egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. |
| Sor kulcsa |A sor kulcsot tartalmazó kimeneti oszlop neve. A sor kulcsa a partíción belüli entitás egyedi azonosítója. Az entitás elsődleges kulcsának második részét képezi. A sor kulcsa egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. |
| Köteg mérete |A Batch-művelet rekordjainak száma. Az alapértelmezett érték (100) elegendő a legtöbb feladathoz. A beállítás módosításával kapcsolatos további információkért tekintse meg a [tábla batch műveletének specifikációját](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="service-bus-queues"></a>Service Bus-üzenetsorok

[Service Bus várólisták](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) egy vagy több versengő fogyasztó számára biztosítanak egy FIFO-üzenet kézbesítését. Az üzeneteket általában a fogadók a várólistához való felvételének időbeli sorrendjében fogadják és dolgozzák fel. Minden üzenet fogadása és feldolgozása csak egy üzenet felhasználója által történik.

A [1,2-es kompatibilitási szinten](stream-analytics-compatibility-level.md)Azure stream Analytics a [speciális üzenetsor-kezelési protokoll (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) üzenetkezelő protokollt használja a Service Bus várólistákba és témakörökbe való íráshoz. A AMQP lehetővé teszi többplatformos hibrid alkalmazások készítését egy nyílt szabványú protokoll használatával.

A következő táblázat felsorolja a tulajdonságok nevét és a várólista kimenetének létrehozásához szükséges leírásokat.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a Service Bus üzenetsor felé történő irányítására szolgál. |
| Service Bus névtér |Az üzenetküldési entitások készletének tárolója. |
| Üzenetsor neve |Az Service Bus üzenetsor neve. |
| Üzenetsor-házirend neve |Amikor létrehoz egy várólistát, közös hozzáférési szabályzatokat is létrehozhat a várólista **konfigurálása** lapon. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| Üzenetsor-házirend kulcsa |A Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. |
| Eseményszerializációs formátum |A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak. |
| Encoding |A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó |Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-formátumú adatszerializáláshoz. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum |Csak JSON-típus esetén alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva**lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. |
| Tulajdonságok oszlopai | Választható. Vesszővel tagolt oszlopokat kell csatolni a kimenő üzenet felhasználói tulajdonságaihoz a hasznos adatok helyett. A szolgáltatással kapcsolatos további információkért lásd: [Egyéni metaadatok tulajdonságai a kimenethez](#custom-metadata-properties-for-output). |
| Rendszertulajdonság-oszlopok | Választható. A Rendszertulajdonságok és a hozzájuk tartozó oszlopnevek neve, amelyeket az adattartalom helyett a kimenő üzenethez kell csatolni. További információ erről a szolgáltatásról: [Service Bus üzenetsor rendszertulajdonságai és témakör kimenetei](#system-properties-for-service-bus-queue-and-topic-outputs)  |

A partíciók száma a [Service Bus SKU és a méret alapján](../service-bus-messaging/service-bus-partitioning.md)történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke.

## <a name="service-bus-topics"></a>Service Bus-üzenettémák
Service Bus Queues egy-az-egyhez kommunikációs módszert biztosít a küldő és a fogadó között. A [Service Bus témakörök](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy a többhöz kommunikációs formát biztosítanak.

A következő táblázat a tulajdonságok nevét és leírásait sorolja fel Service Bus témakör kimenetének létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a Service Bus témakörbe való irányítására szolgál. |
| Service Bus névtér |Az üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor létrehozott egy Service Bus névteret is. |
| Témakör neve |A témakörök az Event hubokhoz és a várólistákhoz hasonló üzenetkezelési entitások. Úgy tervezték, hogy az eszközökről és szolgáltatásokból származó esemény-adatfolyamokat gyűjtsön. Egy témakör létrehozásakor egy adott nevet is kap. A témakörbe küldött üzenetek nem érhetők el, kivéve, ha előfizetést hoznak létre, ezért gondoskodjon arról, hogy egy vagy több előfizetés legyen a témakörben. |
| Témakör-házirend neve |Service Bus témakör létrehozásakor a témakör **configure (Konfigurálás** ) lapján is létrehozhat megosztott hozzáférési házirendeket. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| Témakör-házirend kulcsa |A Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. |
| Eseményszerializációs formátum |A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak. |
| Encoding |Ha CSV-vagy JSON-formátumot használ, meg kell adni egy kódolást. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó |Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-formátumú adatszerializáláshoz. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Tulajdonságok oszlopai | Választható. Vesszővel tagolt oszlopokat kell csatolni a kimenő üzenet felhasználói tulajdonságaihoz a hasznos adatok helyett. A szolgáltatással kapcsolatos további információkért lásd: [Egyéni metaadatok tulajdonságai a kimenethez](#custom-metadata-properties-for-output). |
| Rendszertulajdonság-oszlopok | Választható. A Rendszertulajdonságok és a hozzájuk tartozó oszlopnevek neve, amelyeket az adattartalom helyett a kimenő üzenethez kell csatolni. További információ erről a szolgáltatásról: [Service Bus üzenetsor rendszertulajdonságai és témakör kimenetei](#system-properties-for-service-bus-queue-and-topic-outputs) |

A partíciók száma a [Service Bus SKU és a méret alapján](../service-bus-messaging/service-bus-partitioning.md)történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
A [Azure Cosmos db](https://azure.microsoft.com/services/documentdb/) egy globálisan elosztott adatbázis-szolgáltatás, amely korlátlanul rugalmas méretezhetőséget biztosít a világ minden részén, gazdag lekérdezéssel és automatikus indexeléssel a séma-agnosztikus adatmodellek esetében. Ha többet szeretne megtudni a Stream Analytics-tárolók Azure Cosmos DBéről, tekintse meg a [Stream Analytics Azure Cosmos db kimenetként](stream-analytics-documentdb-output.md) című cikket.

Stream Analytics Azure Cosmos DB kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

> [!Note]
> Jelenleg a Azure Stream Analytics csak az SQL API használatával támogatja a Azure Cosmos DBhoz való kapcsolódást.
> Más Azure Cosmos DB API-k még nem támogatottak. Ha Azure Stream Analytics a más API-kkal létrehozott Azure Cosmos DB-fiókokra mutat, előfordulhat, hogy az adathalmazok nem lesznek megfelelően tárolva.

Az alábbi táblázat a Azure Cosmos DB kimenet létrehozásának tulajdonságait ismerteti.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Az Stream Analytics-lekérdezésben ezt a kimenetet meghívó alias. |
| Sink (Fogadó) | Azure Cosmos DB. |
| Importálási beállítás | Válassza ki **Cosmos db az előfizetésből** , vagy adja meg a **Cosmos db beállításokat manuálisan**.
| Fiókazonosító | A Azure Cosmos DB fiók neve vagy végpontjának URI azonosítója. |
| Fiókkulcs | A Azure Cosmos DB-fiókhoz tartozó megosztott elérési kulcs. |
| Adatbázis | A Azure Cosmos DB adatbázis neve. |
| Tárolónév | A használandó tároló neve, amely a Cosmos DBban léteznie kell. Példa:  <br /><ul><li> _MyContainer_: A "MyContainer" nevű tárolónak léteznie kell.</li>|
| Dokumentum azonosítója |Választható. Annak az elsődleges kulcsnak a megadásához használt mező neve, amelybe az INSERT vagy a Update művelet alapul.

## <a name="azure-functions"></a>Azure Functions
A Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerint futtathat programkódokat anélkül, hogy explicit módon kellene kiépíteni vagy kezelni az infrastruktúrát. Lehetővé teszi az Azure-ban vagy a partneri szolgáltatásokban bekövetkező események által aktivált kódok megvalósítását. Azure Functions az eseményindítók megválaszolásának lehetősége a Azure Stream Analytics természetes kimenetét teszi lehetővé. Ez a kimeneti adapter lehetővé teszi, hogy a felhasználók a Azure Functionshoz csatlakozzanak Stream Analyticshoz, és egy parancsfájlt vagy programkódot futtassanak a különböző eseményekre adott válaszként.

Stream Analytics Azure Functions kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

A Azure Stream Analytics HTTP-eseményindítók használatával hívja meg Azure Functions. A Azure Functions kimeneti adapter a következő konfigurálható tulajdonságokkal érhető el:

| Tulajdonság neve | Leírás |
| --- | --- |
| Függvényalkalmazás |A Azure Functions alkalmazás neve. |
| Függvény |A függvény neve a Azure Functions alkalmazásban. |
| Kulcs |Ha egy másik előfizetésből származó Azure-függvényt szeretne használni, ezt megteheti a függvény eléréséhez szükséges kulcs megadásával. |
| Köteg maximális mérete |Olyan tulajdonság, amely lehetővé teszi az Azure-függvénynek elküldhető kimeneti kötegek maximális méretének beállítását. A bemeneti egység bájtban van. Alapértelmezés szerint ez az érték 262 144 bájt (256 KB). |
| Kötegek maximális száma  |Olyan tulajdonság, amely lehetővé teszi, hogy megadhatja az egyes kötegekben a Azure Functionsba küldendő események maximális számát. Az alapértelmezett érték 100. |

A Azure Stream Analytics a sikeresen feldolgozott kötegekhez tartozó functions alkalmazásból 200 HTTP-állapotot vár.

Ha a Azure Stream Analytics egy Azure-függvénytől egy 413-es ("http-kérelem entitás túl nagy") kivételt kap, akkor csökkenti a Azure Functions számára küldött kötegek méretét. Az Azure-függvény kódjában használja ezt a kivételt annak biztosításához, hogy Azure Stream Analytics ne küldjön túlméretes kötegeket. Győződjön meg arról is, hogy a függvényben használt kötegek maximális száma és mérete konzisztens a Stream Analytics portálon megadott értékekkel.

> [!NOTE]
> A tesztelési kapcsolat során a Stream Analytics egy üres köteget küld Azure Functionsnak, hogy tesztelje, hogy a két működik-e a kapcsolat. Győződjön meg arról, hogy a functions alkalmazás az üres batch-kérelmeket kezeli, hogy ellenőrizze a kapcsolatok ellenőrzésének menetét.

Abban az esetben is, ha egy időablakban nincs esemény kirakodás, a rendszer nem generál kimenetet. Ennek eredményeképpen a **computeResult** függvény nem lett meghívva. Ez a viselkedés konzisztens a beépített ablakos összesítő függvényekkel.

## <a name="custom-metadata-properties-for-output"></a>A kimenet egyéni metaadat-tulajdonságai 

A lekérdezési oszlopokat felhasználói tulajdonságokként csatolhatja a kimenő üzenetekhez. Ezek az oszlopok nem kerülnek bele a hasznos adatokba. A tulajdonságok a kimeneti üzenet szótárának formájában jelennek meg. A *kulcs* az oszlopnév és az *érték* az oszlop értéke a tulajdonságok szótárban. A rekord és a tömb kivételével minden Stream Analytics adattípus támogatott.  

Támogatott kimenetek: 
* Service Bus-üzenetsor 
* Service Bus-témakör 
* Eseményközpont 

A következő példában a két mezőt `DeviceId` és `DeviceStatus` a metaadatokat vesszük fel. 
* Lekérdezés`select *, DeviceId, DeviceStatus from iotHubInput`
* Kimeneti konfiguráció:`DeviceId,DeviceStatus`

![Tulajdonságok oszlopai](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

A következő képernyőfelvételen a EventHub megtekintett kimeneti üzenetek tulajdonságai láthatók [Service Bus Explorerben](https://github.com/paolosalvatori/ServiceBusExplorer).

![Egyéni esemény tulajdonságai](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Service Bus üzenetsor és a témakör kimenetének rendszertulajdonságai 
A lekérdezési oszlopokat [rendszertulajdonságként](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) csatolhatja a kimenő Service Bus-üzenetsor vagy-témakör üzeneteihez. Ezek az oszlopok nem lépnek be a hasznos adatokba, hanem a megfelelő BrokeredMessage [rendszertulajdonságot](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) a lekérdezési oszlop értékeivel töltik fel.
Ezek a Rendszertulajdonságok támogatottak – `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Ezeknek az oszlopoknak a karakterlánc-értékeit a rendszer a megfelelő rendszertulajdonság-érték típusként értelmezi, és az elemzési hibák adathibákként lesznek kezelve.
Ez a mező JSON-objektum formátumként van megadva. A formátum részletei a következők:
* Kapcsos zárójelek között {}.
* Kulcs/érték párokba írva.
* A kulcsoknak és az értékeknek sztringeknek kell lenniük.
* A kulcs a rendszer tulajdonságának neve és értéke a lekérdezési oszlop neve.
* A kulcsok és az értékek kettősponttal vannak elválasztva.
* A kulcs/érték párok vesszővel vannak elválasztva.

Ez a tulajdonság használatát mutatja be –

* Lekérdezés`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Rendszertulajdonság oszlopai:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Ezzel beállítja a `MessageId` Service Bus üzenetsor üzeneteinek `column1`értékeit és a PartitionKey `column2`értékeit.

## <a name="partitioning"></a>Particionálás

A következő táblázat összefoglalja a partíciók támogatását és a kimeneti írók számát az egyes kimeneti típusokhoz:

| Kimenet típusa | Particionálás támogatása | Partíciókulcs  | Kimeneti írók száma |
| --- | --- | --- | --- |
| Azure Data Lake Store | Igen | Használja a {Date} és az {Time} tokent az elérési út előtagjának mintájában. Válassza ki a dátumformátum (például éééé/hh/nn, nn/hh/éééé vagy hh-nn-éééé). A HH az időformátumot használja. | A [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)esetében a bemeneti particionálást követi. |
| Azure SQL Database | Igen, engedélyezni kell. | A lekérdezésben a PARTITION BY záradék alapján. | Ha engedélyezve van a particionálási lehetőség, a a [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)esetében a bemeneti particionálást követi. Ha többet szeretne megtudni az adatok Azure SQL Databaseba való betöltésével kapcsolatos jobb írási teljesítmény eléréséről, tekintse meg [Azure stream Analytics kimenetét Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Igen | Használja a {Date} és az {Time} jogkivonatot az esemény mezőiből az elérésiút-mintában. Válassza ki a dátumformátum (például éééé/hh/nn, nn/hh/éééé vagy hh-nn-éééé). A HH az időformátumot használja. A blob kimenete egyetlen egyéni Event attribútummal ({mezőnév} vagy {datetime:\<megadási>}) particionálható. | A [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)esetében a bemeneti particionálást követi. |
| Azure Event Hubs | Igen | Igen | A partíció-igazítástól függően változhat.<br /> Ha az Event hub kimenetéhez tartozó partíciós kulcs egyenlően van igazítva a felsőbb rétegbeli (előző) lekérdezési lépéssel, az írók száma megegyezik az Event hub kimenetében található partíciók számával. Az egyes írók a [EventHubSender osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) használatával küldik el az eseményeket az adott partícióra. <br /> Ha az Event hub kimenetének partíciós kulcsa nincs igazítva a felsőbb rétegbeli (előző) lekérdezési lépéssel, az írók száma megegyezik az előző lépésben lévő partíciók számával. Mindegyik író a [SendBatchAsync osztályt](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) használja a **EventHubClient** -ben, hogy eseményeket küldjön az összes kimeneti partícióra. |
| Power BI | Nem | None | Nem alkalmazható. |
| Azure Table Storage | Igen | Bármely kimeneti oszlop.  | A [teljesen párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure Service Bus-témakör | Igen | Automatikusan kiválasztva. A partíciók száma a [Service Bus SKU és a méret](../service-bus-messaging/service-bus-partitioning.md)alapján történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke.| Ugyanaz, mint a kimeneti témakörben lévő partíciók száma.  |
| Azure Service Bus-üzenetsor | Igen | Automatikusan kiválasztva. A partíciók száma a [Service Bus SKU és a méret](../service-bus-messaging/service-bus-partitioning.md)alapján történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke.| Ugyanaz, mint a partíciók száma a kimeneti várólistában. |
| Azure Cosmos DB | Igen | A lekérdezésben a PARTITION BY záradék alapján. | A [teljesen párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure Functions | Igen | A lekérdezésben a PARTITION BY záradék alapján. | A [teljesen párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |

A kimeneti írók száma a lekérdezésben (lásd: `INTO <partition count>` [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) záradék használatával is vezérelhető, ami hasznos lehet a kívánt feladatok topológiájának megvalósításában. Ha a kimeneti adapter nincs particionálva, az egyik bemeneti partícióban lévő adatok hiánya késlelteti a késői érkezés időtartamát. Ilyen esetekben a kimenet egyetlen író számára lesz egyesítve, ami szűk keresztmetszetet eredményezhet a folyamat során. Ha többet szeretne megtudni a késői beérkezési szabályzatról, tekintse meg az [Azure stream Analytics az események sorrendjét](stream-analytics-out-of-order-and-late-events.md)ismertető szakaszt

## <a name="output-batch-size"></a>Kimeneti köteg mérete
A Azure Stream Analytics változó méretű kötegeket használ az események feldolgozásához és a kimenetekhez való íráshoz. Általában a Stream Analytics motor nem ír egyszerre egy üzenetet, és batchs-t használ a hatékonysághoz. Ha a bejövő és a kimenő események aránya is magas, Stream Analytics nagyobb kötegeket használ. Ha a kimenő forgalom aránya alacsony, kisebb kötegeket használ a késés alacsonyra állításához.

A következő táblázat a kimenetek kötegelt feldolgozásával kapcsolatos szempontokat ismerteti:

| Kimenet típusa |    Üzenetek maximális mérete | Batch méretének optimalizálása |
| :--- | :--- | :--- |
| Azure Data Lake Store | Lásd: [Data Lake Storage korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Írási művelet esetén akár 4 MB-ot is használhat. |
| Azure SQL Database | Konfigurálható a kötegek maximális száma alapján. 10 000 a maximális és a 100-os minimális sor egy tömeges beszúrási alapértelmezés szerint.<br />Lásd: [Azure SQL-korlátok](../sql-database/sql-database-resource-limits.md). |  A kötegek kezdetben tömegesen vannak beszúrva a kötegek maximális számával. A Batch az SQL-újrapróbálkozást lehetővé tevő hibák alapján fél (a minimális kötegek száma) között oszlik meg. |
| Azure Blob Storage | Lásd: [Azure Storage-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | A Blobok maximális mérete 4 MB.<br />A Blobok maximális száma 50 000. |
| Azure Event Hubs    | 256 KB vagy 1 MB/üzenet. <br />Lásd: [Event Hubs korlátok](../event-hubs/event-hubs-quotas.md). |    Ha a bemeneti/kimeneti particionálás nincs igazítva, az egyes események külön vannak csomagolva, `EventData` és egy kötegben lesznek elküldve az üzenetek maximális méretére. Ez akkor is előfordul, ha [Egyéni metaadatokat](#custom-metadata-properties-for-output) használunk. <br /><br />  Ha a bemeneti/kimeneti particionálás be van igazítva, több esemény is egyetlen `EventData` példányba van csomagolva, az üzenetek maximális méretével és a küldéssel.    |
| Power BI | Lásd: [Power bi REST API-korlátok](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Lásd: [Azure Storage-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Az alapértelmezett érték a 100 entitások száma egyetlen tranzakcióban. Igény szerint kisebb értékre is konfigurálhatja. |
| Azure Service Bus-üzenetsor    | 256 KB/üzenet a standard szinthez, 1 MB a prémium szintű csomaghoz.<br /> Lásd: [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md). | Egyetlen esemény használata üzenetben. |
| Azure Service Bus-témakör | 256 KB/üzenet a standard szinthez, 1 MB a prémium szintű csomaghoz.<br /> Lásd: [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md). | Egyetlen esemény használata üzenetben. |
| Azure Cosmos DB    | Lásd: [Azure Cosmos db korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | A köteg mérete és az írási gyakoriság a Azure Cosmos DB válaszok alapján dinamikusan módosul. <br /> A Stream Analytics nem rendelkezik előre meghatározott korlátozásokkal. |
| Azure Functions    | | Az alapértelmezett köteg mérete 262 144 bájt (256 KB). <br /> Az események alapértelmezett száma/batch értéke 100. <br /> A köteg mérete konfigurálható, és a Stream Analytics [kimeneti beállításokban](#azure-functions)növelheti vagy csökkenthető.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> 
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
