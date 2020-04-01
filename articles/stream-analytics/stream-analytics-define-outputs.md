---
title: Az Azure Stream Analytics kimeneteinek megismerése
description: Ez a cikk az Azure Stream Analytics szolgáltatásban elérhető adatkimeneti lehetőségeket ismerteti, beleértve a Power BI-t az elemzési eredményekhez.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475229"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Az Azure Stream Analytics kimeneteinek megismerése

Ez a cikk az Azure Stream Analytics-feladathoz elérhető kimenetek típusait ismerteti. A kimenetek lehetővé teszik a Stream Analytics-feladat eredményeinek tárolását és mentését. A kimeneti adatok használatával további üzleti elemzéseket és adattárház-adatokat tehet.

A Stream Analytics-lekérdezés tervezésekor az INTO záradékkal hivatkozzon a kimenet [nevére.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) A lekérdezésben több INTO-záradékot is használhat feladatonként, vagy több kimenetet streamelési feladatonként (ha szükség van rájuk).

A Stream Analytics-feladatkimenetek létrehozásához, szerkesztéséhez és teszteléséhez használhatja az [Azure Portalt,](stream-analytics-quick-create-portal.md#configure-job-output)az [Azure PowerShellt](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [a .NET API-t](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), a [REST API-t](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)és a [Visual Studio-t.](stream-analytics-quick-create-vs.md)

Egyes kimeneti típusok támogatják [a particionálást.](#partitioning) [A kimeneti kötegek mérete](#output-batch-size) az átviteli teljesítmény optimalizálása érdekében változhat.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

A Stream Analytics támogatja az [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)szolgáltatást. Az Azure Data Lake Storage egy nagyvállalati szintű, nagy méretű tárház big data-elemzési számítási feladatokhoz. A Data Lake Storage segítségével bármilyen méretű, típusú és betöltési sebességű adatokat tárolhat a működési és feltáró elemzésekhez. A Stream Analytics-nek engedélyeznie kell a Data Lake Storage elérését.

A Stream Analytics Azure Data Lake Storage-kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régiókban.

Az alábbi táblázat a tulajdonságneveket és azok leírását sorolja fel a Data Lake Storage Gen 1 kimenet konfigurálásához.   

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A lekérdezéskimenet data lake store-ba történő irányításához használt rövid név. |
| Előfizetés | Az Azure Data Lake Storage-fiókot tartalmazó előfizetés. |
| Fióknév | Annak a Data Lake Áruház-fióknak a neve, amelybe a kimenetet küldi. Megjelenik az előfizetésében elérhető Data Lake Áruház-fiókok legördülő listája. |
| Elérési út előtagmintáza | A fájlok nak a megadott Data Lake Store-fiókon belüli írásához használt fájlelérési út. A(z) {date} és {time} változók egy vagy több példányát megadhatja:<br /><ul><li>1. példa: folder1/logs/{date}/{time}</li><li>2. példa: folder1/logs/{date}</li></ul><br />A létrehozott mappastruktúra időbélyegzője az UTC-t követi, nem pedig a helyi időt.<br /><br />Ha a fájl elérési útmintája nem tartalmaz záró perjelet (/), a fájl elérési útjának utolsó mintáját a program fájlnévelőtagként kezeli. <br /><br />Az ilyen körülmények között új fájlok jönnek létre:<ul><li>A kimeneti séma módosítása</li><li>A feladat külső vagy belső újraindítása</li></ul> |
| Dátum formátuma | Választható. Ha a dátumtokent használja az előtag elérési útján, kiválaszthatja azt a dátumformátumot, amelyben a fájlok rendszerezve vannak. Példa: YYYY/MM/DD |
|Időformátum | Választható. Ha az időjogkivonatot az előtag elérési útján használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | A kimeneti adatok szerializálási formátuma. JSON, CSV és Avro támogatottak.|
| Encoding | CSV vagy JSON formátum használata esetén meg kell adni egy kódolást. Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum.|
| Elválasztó | Csak CSV-szerializálásesetén alkalmazható. A Stream Analytics számos közös határolót támogat a CSV-adatok szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, tabulátor és függőleges sáv.|
| Formátum | Csak JSON-szerializálás esetén alkalmazható. **A sorelválasztott** azt adja meg, hogy a kimenet formázása úgy történik, hogy minden JSON-objektumot új sor választ el egymástól. Ha a **Sor elválasztva lehetőséget választja,** a JSON objektumot olvas egyszerre. A teljes tartalom önmagában nem lenne érvényes JSON.  **A tömb** azt adja meg, hogy a kimenet JSON-objektumok tömbjeként van formázva. Ez a tömb csak akkor van bezárva, ha a feladat leáll, vagy a Stream Analytics áttért a következő időablakra. Általában célszerű vonal-elválasztott JSON-t használni, mert nem igényel speciális kezelést, amíg a kimeneti fájl még mindig írásban van.|
| Hitelesítési módszer | Engedélyezheti a data lake storage-fiókjához való hozzáférést [felügyelt identitás](stream-analytics-managed-identities-adls.md) vagy felhasználói jogkivonat használatával. Miután engedélyezte a hozzáférést, visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a Data Lake Storage kimenetének törlésével a feladathoz, vagy a Stream Analytics-feladat törlésével. |

## <a name="sql-database"></a>SQL Database

Az [Azure SQL Database-t](https://azure.microsoft.com/services/sql-database/) kimenetként használhatja a relációs jellegű adatokhoz vagy a relációs adatbázisban tárolt tartalomtól függő alkalmazásokhoz. A Stream Analytics-feladatok az SQL Database egy meglévő táblába írása. A táblasémának pontosan meg kell egyeznie a mezőkkel és azok típusával a feladat kimenetében. Az Azure [SQL Data Warehouse-t](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kimenetként is megadhatja az SQL Database kimeneti beállításon keresztül. Az írási átviteli teljesítmény javításának módjairól tekintse meg a [Stream Analytics az Azure SQL Database kimeneti cikkként.](stream-analytics-sql-output-perf.md)

Az Azure [SQL Database felügyelt példánykimeneti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) használatával is használható. Konfigurálnia kell [a nyilvános végpontot az Azure SQL Database felügyelt példányában,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) majd manuálisan kell konfigurálnia a következő beállításokat az Azure Stream Analytics-ben. Az SQL Servert futtató, adatbázist tartalmazó virtuális gépet az alábbi beállítások manuális konfigurálásával is támogatja.

Az alábbi táblázat felsorolja az SQL Database kimenet létrehozásának tulajdonságneveit és leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenetének az adatbázisba történő irányításához használt rövid név. |
| Adatbázis | Annak az adatbázisnak a neve, amelybe a kimenetet küldi. |
| Kiszolgálónév | Az SQL Database kiszolgáló neve. Az Azure SQL Database felügyelt példánya esetén meg kell adnia a 3342-es portot. Például *sampleserver.public.database.windows.net,3342* |
| Felhasználónév | Az adatbázishoz írási hozzáféréssel rendelkező felhasználónév. A Stream Analytics csak az SQL-hitelesítést támogatja. |
| Jelszó | Az adatbázishoz való csatlakozáshoz. |
| Tábla | Az a táblanév, ahol a kimenet íródik. A tábla neve nem imitot. A tábla sémájának pontosan meg kell egyeznie a mezők számával és típusával, amelyet a feladat kimenete generál. |
|Partícióséma öröklése| Az előző lekérdezési lépés particionálási sémájának öröklési lehetősége, amely lehetővé teszi a teljes párhuzamos topológiát több íróval a táblához. További információ: [Azure Stream Analytics-kimenet az Azure SQL Database számára.](stream-analytics-sql-output-perf.md)|
|Kötegek maximális száma| Az egyes tömeges beszúrási tranzakciókkal küldött rekordok számának ajánlott felső határa.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob storage és Az Azure Data Lake Gen2

A Data Lake Storage Gen2 az Azure Storage-ot az Azure-beli nagyvállalati adattavak létrehozásának alapjának teszi lehetővé. A Data Lake Storage Gen2 a kezdetektől fogva több petabájtnyi információt szolgál ki, miközben több száz gigabitátviteli kapacitást tart fenn, így könnyedén kezelheti a hatalmas mennyiségű adatot. A Data Lake Storage Gen2 alapvető része egy hierarchikus névtér hozzáadása a Blob storage-hoz.

Az Azure Blob storage költséghatékony és méretezhető megoldást kínál nagy mennyiségű strukturálatlan adat felhőben való tárolására. A Blob storage és annak használatának [bemutatása: Blobok feltöltése, letöltése és listablobok az Azure Portalon.](../storage/blobs/storage-quickstart-blobs-portal.md)

Az alábbi táblázat felsorolja a tulajdonságneveket és azok leírását egy blob vagy Az ADLS Gen2 kimenet létrehozásához.

| Tulajdonság neve       | Leírás                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Kimeneti alias        | A lekérdezések lekérdezések a lekérdezés kimenetét erre a blob storage-ra egy rövid név. |
| Tárfiók     | Annak a tárfióknak a neve, amelyet a kimenet küldésekor küld.               |
| Tárfiók kulcsa | A tárfiókhoz társított titkos kulcs.                              |
| Tárolótároló   | Az Azure Blob szolgáltatásban tárolt blobok logikai csoportosítása. Amikor feltölt egy blobot a Blob szolgáltatásba, meg kell adnia egy tárolót az adott blobhoz. |
| Elérésiút-minta | Választható. A fájl elérési út minta, amely a blobok írásához a megadott tárolóban. <br /><br /> Az elérési út mintájában a dátum- és időváltozók egy vagy több példányát használhatja a blobok írási gyakoriságának megadásához: <br /> {date}, {time} <br /><br />Egyéni blob particionálás segítségével adhat meg egy egyéni {field} nevet az eseményadatokból a partícióblobok. A mező neve alfanumerikus, és szóközöket, kötőjeleket és aláhúzásjeleket tartalmazhat. Az egyéni mezőkre vonatkozó korlátozások a következők: <ul><li>A mezőnevek nem érzékenyek a kis- és nagybetűkre. A szolgáltatás például nem tud különbséget tenni az "ID" és az "id" oszlop között.</li><li>A beágyazott mezők nem engedélyezettek. Ehelyett a feladatlekérdezésben használjon egy aliast a mező "simításához".</li><li>A kifejezések nem használhatók mezőnévként.</li></ul> <br />Ez a szolgáltatás lehetővé teszi az egyéni dátum-/időformátum-megváltó konfigurációk használatát az elérési úton. Az egyéni dátum- és időformátumokat egyenként kell megadni, a ({datetime:\<specifier>} kulcsszónak a kíséretében. A \<> megadott bemenetek: éééé, MM, M, dd, d, HH, H, mm, m, ss vagy s. A {datetime:\<specifier>} kulcsszó többször is használható az egyéni dátum-/időkonfigurációk létrehozásának útvonalán. <br /><br />Példák: <ul><li>1. példa: fürt1/logs/{date}/{time}</li><li>2. példa: fürt1/logs/{date}</li><li>3. példa: fürt1/{client_id}/{date}/{time}</li><li>4. példa: cluster1/{datetime:ss}/{myField} ahol a lekérdezés: SELECT data.myField AS myField FROM Input;</li><li>5. példa: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />A létrehozott mappastruktúra időbélyegzője az UTC-t követi, nem pedig a helyi időt.<br /><br />A fájlelnevezés a következő konvenciót használja: <br /><br />{Path előtag minta}/schemaHashcode_Guid_Number.extension<br /><br />Példa kimeneti fájlokra:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />A funkcióról további információt az [Azure Stream Analytics egyéni blobkimeneti particionálás című](stream-analytics-custom-path-patterns-blob-storage-output.md)témakörben talál. |
| Dátum formátuma | Választható. Ha a dátumtokent használja az előtag elérési útján, kiválaszthatja azt a dátumformátumot, amelyben a fájlok rendszerezve vannak. Példa: YYYY/MM/DD |
| Időformátum | Választható. Ha az időjogkivonatot az előtag elérési útján használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | Szerializálási formátum a kimeneti adatokhoz. JSON, CSV, Avro és Parketta támogatottak. |
|Minimális sorok (csak parketta)|A kötegenkénti minimális sorok száma. A Parketta esetében minden köteg új fájlt hoz létre. Az aktuális alapértelmezett érték 2000 sor, a megengedett maximális érték pedig 10 000 sor.|
|Maximális idő (csak parketta)|Kötegenkénti maximális várakozási idő. Ezt követően a köteg akkor is bekerül a kimenetbe, ha a minimális sorokra vonatkozó követelmény nem teljesül. Az aktuális alapértelmezett érték 1 perc, a megengedett maximális érték pedig 2 óra. Ha a blob kimenet elérési út minta gyakorisága, a várakozási idő nem lehet magasabb, mint a partíció időtartománya.|
| Encoding    | CSV vagy JSON formátum használata esetén meg kell adni egy kódolást. Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó   | Csak CSV-szerializálásesetén alkalmazható. A Stream Analytics számos közös határolót támogat a CSV-adatok szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, tabulátor és függőleges sáv. |
| Formátum      | Csak JSON-szerializálás esetén alkalmazható. **A sorelválasztott** azt adja meg, hogy a kimenet formázása úgy történik, hogy minden JSON-objektumot új sor választ el egymástól. Ha a **Sor elválasztva lehetőséget választja,** a JSON objektumot olvas egyszerre. A teljes tartalom önmagában nem lenne érvényes JSON. **A tömb** azt adja meg, hogy a kimenet JSON-objektumok tömbjeként van formázva. Ez a tömb csak akkor van bezárva, ha a feladat leáll, vagy a Stream Analytics áttért a következő időablakra. Általában célszerű vonal-elválasztott JSON-t használni, mert nem igényel speciális kezelést, amíg a kimeneti fájl még mindig írásban van. |

Blob storage kimenetként való használatakor a következő esetekben jön létre egy új fájl a blobban:

* Ha a fájl meghaladja az engedélyezett blokkok maximális számát (jelenleg 50 000). Előfordulhat, hogy a maximálisan engedélyezett számú blokkot anélkül éri el, hogy elérné a maximálisan engedélyezett blobméretet. Ha például a kimeneti sebesség magas, blokkonként több bájt látható, és a fájlméret nagyobb. Ha a kimeneti sebesség alacsony, minden blokk kevesebb adatot, és a fájl mérete kisebb.
* Ha van egy séma változás a kimenetben, és a kimeneti formátum rögzített sémát igényel (CSV és Avro).
* Ha egy feladatot újraindít, vagy külsőleg a felhasználó leállítja és elindítja, vagy belsőleg a rendszer karbantartása vagy a hiba helyreállítása.
* Ha a lekérdezés teljesen particionált, és minden kimeneti partícióhoz új fájl jön létre.
* Ha a felhasználó töröl egy fájlt vagy a tárfiók tárolóját.
* Ha a kimenet az elérési út előtag minta használatával particionált idő, és egy új blob ot használ, amikor a lekérdezés a következő órára kerül.
* Ha a kimenetet egy egyéni mező particionálja, és partíciókulcsonként új blob jön létre, ha nem létezik.
* Ha a kimenetparticionált egy egyéni mező, ahol a partíciókulcs számossága meghaladja a 8000, és egy új blob jön létre partíciókulcsonként.

## <a name="event-hubs"></a>Event Hubs

Az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatás egy jól méretezhető közzététel-előfizetés eseménybetöltés. Másodpercenként több millió eseményt gyűjthet össze. Az eseményközpont egyik használata kimenetként az, amikor a Stream Analytics-feladat kimenete egy másik streamelési feladat bemenete lesz. Az üzenetek maximális méretéről és a kötegméret optimalizálásáról a [kimeneti kötegméret](#output-batch-size) szakaszban talál tájékoztatást.

Az eseményközpontokból származó adatfolyamok kimenetként való konfigurálásához néhány paraméterre van szükség.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A lekérdezéskimenet erre az eseményközpontra történő irányításához használt rövid név. |
| Eseményközpont-névtér | Üzenetkezelési entitások tárolója. Új eseményközpont létrehozásakor létrehozott egy eseményközpont-névteret is. |
| Event Hubs neve | Az eseményközpont kimenetének neve. |
| Az eseményközpont házirendjének neve | A megosztott hozzáférési szabályzat, amelyet az eseményközpont **Konfigurálás** lapján hozhat létre. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. |
| Eseményközpont házirendkulcsa | Az eseményközpont névteréhez való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs. |
| Partíciókulcs oszlopa | Választható. Az eseményközpont kimenetének partíciókulcsát tartalmazó oszlop. |
| Eseményszerializációs formátum | A kimeneti adatok szerializálási formátuma. JSON, CSV és Avro támogatottak. |
| Encoding | A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó | Csak CSV-szerializálásesetén alkalmazható. A Stream Analytics számos közös határolómezőt támogat az adatok CSV formátumban való szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, tabulátor és függőleges sáv. |
| Formátum | Csak JSON-szerializálás esetén alkalmazható. **A sorelválasztott** azt adja meg, hogy a kimenet formázása úgy történik, hogy minden JSON-objektumot új sor választ el egymástól. Ha a **Sor elválasztva lehetőséget választja,** a JSON objektumot olvas egyszerre. A teljes tartalom önmagában nem lenne érvényes JSON. **A tömb** azt adja meg, hogy a kimenet JSON-objektumok tömbjeként van formázva.  |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok, amelyeket a tartalom helyett a kimenő üzenet felhasználói tulajdonságaiként kell csatolni. A szolgáltatásról további információt a [kimenet egyéni metaadat-tulajdonságai című](#custom-metadata-properties-for-output)szakasz tartalmaz. |

## <a name="power-bi"></a>Power BI

A [Power BI-t](https://powerbi.microsoft.com/) a Stream Analytics-feladat kimeneteként is használhatja, hogy az elemzési eredmények gazdag vizualizációs élményét biztosítsa. Ezt a funkciót használható működési irányítópultokhoz, jelentéskészítéshez és metrikaalapú jelentésekhez.

A Stream Analytics Power BI-kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régiókban.

Az alábbi táblázat a power BI-kimenet konfigurálásához tartalmazza a tulajdonságneveket és azok leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Adjon meg egy rövid nevet, amely a lekérdezésekben a lekérdezés kimenetének erre a Power BI-kimenetre való irányítására szolgál. |
| Munkaterület csoportosítása |Ha engedélyezni szeretné az adatok megosztását más Power BI-felhasználókkal, kiválaszthat csoportokat a Power BI-fiókján belül, vagy választhatja **a Saját munkaterület** lehetőséget, ha nem szeretne írni egy csoportnak. Egy meglévő csoport frissítéséhez meg kell újítani a Power BI-hitelesítést. |
| Adatkészlet neve |Adja meg azt az adatkészletnevet, amelyet a Power BI-kimenetnek használnia kell. |
| Tábla neve |Adja meg a tábla nevét a Power BI-kimenet adatkészlete alatt. Jelenleg a Stream Analytics-feladatok Power BI-kimenete iményegy adatkészletben csak egy táblával rendelkezhet. |
| Kapcsolat engedélyezése | A kimeneti beállítások konfigurálásához engedélyeznie kell a Power BI-val. Miután engedélyezte ezt a kimeneti hozzáférést a Power BI irányítópultjához, visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladat kimenetének törlésével vagy a Stream Analytics-feladat törlésével. | 

A Power BI-kimenet és irányítópult konfigurálásának forgatókönyvét az [Azure Stream Analytics és](stream-analytics-power-bi-dashboard.md) a Power BI oktatóanyaga ismerteti.

> [!NOTE]
> Ne hozza létre kifejezetten az adatkészletet és a táblát a Power BI irányítópultján. Az adatkészlet és a tábla automatikusan kitöltődik, amikor a feladat elindul, és a feladat elkezdi szivattyúzni a kimenetet a Power BI-ba. Ha a feladatlekérdezés nem hoz létre eredményt, az adatkészlet és a tábla nem jön létre. Ha a Power BI-nak már volt olyan adatkészlete és táblája, amelynek neve megegyezik az ebben a Stream Analytics-feladatban megadott névvel, a meglévő adatok felülíródnak.
>

### <a name="create-a-schema"></a>Séma létrehozása
Az Azure Stream Analytics létrehoz egy Power BI-adatkészletet és táblasémát a felhasználó számára, ha azok még nem léteznek. Minden más esetben a tábla új értékekkel frissül. Jelenleg csak egy tábla létezhet egy adatkészleten belül. 

A Power BI az első be- és első kikapcsolási (FIFO) adatmegőrzési szabályzatot használja. Az adatok at gyűjtünk egy táblázatban, amíg el nem éri a 200 000 sort.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Adattípus konvertálása A Stream Analytics szolgáltatásból a Power BI-ba
Az Azure Stream Analytics futásidőben dinamikusan frissíti az adatmodellt, ha a kimeneti séma megváltozik. Az oszlopnév, az oszloptípus-módosítások, valamint az oszlopok hozzáadása vagy eltávolítása mind nyomon követhető.

Ez a táblázat a [Stream Analytics adattípusaiból](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) Power BI entity data [model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)típusúadat-típusokba történő adattípusokat ismerteti, ha nem létezik Power BI-adatkészlet és tábla.

Forrás: Stream Analytics | A Power BI-ra
-----|-----
bigint | Int64
nvarchar(max) | Sztring
dátum/idő | Datetime
lebegőpontos | Double
Rekordtömb | Karakterlánc típusa, "IRecord" vagy "IArray" állandó érték

### <a name="update-the-schema"></a>A séma frissítése
A Stream Analytics a kimenetben lévő események első készlete alapján következtet az adatmodell sémájából. Később, ha szükséges, az adatmodell séma frissül, hogy a bejövő események, amelyek esetleg nem fér nek be az eredeti séma.

Kerülje `SELECT *` a lekérdezést, hogy megakadályozza a dinamikus séma frissítését a sorok között. A mellett, hogy a lehetséges teljesítmény következményei, ez azt eredményezheti, bizonytalanság az idő, hogy az eredmények. Válassza ki azokat a mezőket, amelyeket meg kell jelennie a Power BI irányítópultján. Ezenkívül az adatértékeknek meg kell felelniük a kiválasztott adattípusnak.


Előző/aktuális | Int64 | Sztring | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sztring | Sztring | Double
Double | Double | Sztring | Sztring | Double
Sztring | Sztring | Sztring | Sztring | Sztring 
Datetime | Sztring | Sztring |  Datetime | Sztring

## <a name="table-storage"></a>Table Storage

[Az Azure Table storage](../storage/common/storage-introduction.md) magas rendelkezésre állású, nagymértékben méretezhető tárhelyet kínál, így egy alkalmazás automatikusan skálázható a felhasználói igényeknek megfelelően. A táblatárolás a Microsoft NoSQL kulcs-/attribútumtárolója, amelyet a séma kevesebb korlátozásával rendelkező strukturált adatokhoz használhat. Az Azure Table storage adatok tárolására használható az adatmegőrzés és a hatékony lekérés érdekében.

Az alábbi táblázat a tulajdonságneveket és azok leírását tartalmazza a táblakimenet létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezéskimenet táblatárolóba való irányításához használt rövid név. |
| Tárfiók |Annak a tárfióknak a neve, amelyet a kimenet küldésekor küld. |
| Tárfiók kulcsa |A tárfiókhoz társított hozzáférési kulcs. |
| Tábla neve |A tábla neve. A tábla létrejön, ha nem létezik. |
| Partíciókulcs |A partíciókulcsot tartalmazó kimeneti oszlop neve. A partíciókulcs egy olyan tábla partíciójának egyedi azonosítója, amely az entitás elsődleges kulcsának első részét képezi. Ez egy karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. |
| Sorkulcs |A sorkulcsot tartalmazó kimeneti oszlop neve. A sorkulcs egy partíción belüli entitás egyedi azonosítója. Az entitás elsődleges kulcsának második részét képezi. A sorkulcs olyan karakterlánc-érték, amelynek mérete legfeljebb 1 KB lehet. |
| Köteg mérete |A kötegelt művelet rekordjainak száma. Az alapértelmezett (100) elegendő a legtöbb feladathoz. A beállítás módosításáról további részleteket a [Táblakötegelés specifikációja](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation) tartalmaz. |

## <a name="service-bus-queues"></a>Service Bus-üzenetsorok

[A Service Bus-várólisták](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) FIFO-üzenetkézbesítést kínálnak egy vagy több versenytárs fogyasztónak. Az üzeneteket általában abban a temporális sorrendben fogadják és dolgozzák fel a fogadók, amelyben a várólistához adták őket. Minden üzenetet csak egy üzenetfogyasztó fogad és dolgoz fel.

Az [1.2-es kompatibilitási szinten](stream-analytics-compatibility-level.md)az Azure Stream Analytics [advanced message queueing protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) üzenetkezelési protokollt használ a Service Bus-várólistákba és témakörökbe való íráshoz. Az AMQP lehetővé teszi, hogy platformfüggetlen, hibrid alkalmazásokat hozzon létre egy nyílt szabványú protokoll használatával.

Az alábbi táblázat felsorolja a tulajdonságneveket és azok leírását a várólista-kimenet létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezéskimenet erre a Service Bus-várólistára irányítandó lekérdezésekben használt rövid név. |
| Service Bus névtere |Üzenetkezelési entitások tárolója. |
| Üzenetsor neve |A Service Bus-várólista neve. |
| Várólista házirendjének neve |Várólista létrehozásakor a várólista **Konfigurálás** lapján is létrehozhat megosztott hozzáférési házirendeket. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. |
| Várólista-házirendkulcs |A szolgáltatásbusz névteréhez való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs. |
| Eseményszerializációs formátum |A kimeneti adatok szerializálási formátuma. JSON, CSV és Avro támogatottak. |
| Encoding |A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó |Csak CSV-szerializálásesetén alkalmazható. A Stream Analytics számos közös határolómezőt támogat az adatok CSV formátumban való szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, tabulátor és függőleges sáv. |
| Formátum |Csak a JSON-típusra alkalmazható. **A sorelválasztott** azt adja meg, hogy a kimenet formázása úgy történik, hogy minden JSON-objektumot új sor választ el egymástól. Ha a **Sor elválasztva lehetőséget választja,** a JSON objektumot olvas egyszerre. A teljes tartalom önmagában nem lenne érvényes JSON. **A tömb** azt adja meg, hogy a kimenet JSON-objektumok tömbjeként van formázva. |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok, amelyeket a tartalom helyett a kimenő üzenet felhasználói tulajdonságaiként kell csatolni. A szolgáltatásról további információt a [kimenet egyéni metaadat-tulajdonságai című](#custom-metadata-properties-for-output)szakasz tartalmaz. |
| Rendszertulajdonság oszlopok | Választható. A rendszertulajdonságok és a megfelelő oszlopnevek kulcsértékpárjai, amelyeket a tartalom helyett a kimenő üzenethez kell csatolni. A szolgáltatásról további információ a [Service Bus-várólista és a Témakör kimenetek Rendszertulajdonságai című szakaszban található.](#system-properties-for-service-bus-queue-and-topic-outputs)  |

A partíciók száma [a Service Bus termékváltozatán és méretétől függ.](../service-bus-messaging/service-bus-partitioning.md) A partíciókulcs az egyes partíciók egyedi egész értéke.

## <a name="service-bus-topics"></a>Service Bus-üzenettémák
A Service Bus-várólisták egy-az-egyhez kommunikációs módszert biztosítanak a feladótól a fogadóig. [A Service Bus témakörei](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy-a-többhöz kommunikációs formát biztosítanak.

Az alábbi táblázat felsorolja a szolgáltatásneveket és azok leírását a Service Bus-témakör kimenetének létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezéskimenet erre a Service Bus-témakörre irányítandó rövid név. |
| Service Bus névtere |Üzenetkezelési entitások tárolója. Új eseményközpont létrehozásakor létrehozott egy Service Bus-névteret is. |
| Témakör neve |A témakörök az eseményközpontokhoz és a várólistákhoz hasonlóan üzenetkezelő entitások. Úgy tervezték őket, hogy eseményfolyamokat gyűjtsenek az eszközökről és szolgáltatásokból. Amikor egy témakör jön létre, akkor is adott nevet. A témakörbe küldött üzenetek csak akkor érhetők el, ha létrejön egy előfizetés, ezért győződjön meg arról, hogy egy vagy több előfizetés van a témakör alatt. |
| Témakörházirend neve |Service Bus-témakör létrehozásakor a témakör **Konfigurálás** lapján is létrehozhat megosztott hozzáférési házirendeket. Minden megosztott hozzáférési házirendrendelkezik névvel, a beállított engedélyekkel és hozzáférési kulcsokkal. |
| Témaházirend-kulcs |A szolgáltatásbusz névteréhez való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs. |
| Eseményszerializációs formátum |A kimeneti adatok szerializálási formátuma. JSON, CSV és Avro támogatottak. |
| Encoding |CSV vagy JSON formátum használata esetén meg kell adni egy kódolást. Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó |Csak CSV-szerializálásesetén alkalmazható. A Stream Analytics számos közös határolómezőt támogat az adatok CSV formátumban való szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, tabulátor és függőleges sáv. |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok, amelyeket a tartalom helyett a kimenő üzenet felhasználói tulajdonságaiként kell csatolni. A szolgáltatásról további információt a [kimenet egyéni metaadat-tulajdonságai című](#custom-metadata-properties-for-output)szakasz tartalmaz. |
| Rendszertulajdonság oszlopok | Választható. A rendszertulajdonságok és a megfelelő oszlopnevek kulcsértékpárjai, amelyeket a tartalom helyett a kimenő üzenethez kell csatolni. A szolgáltatásról további információ a [Service Bus-várólista és a Témakör kimenetek Rendszertulajdonságai című szakaszban található.](#system-properties-for-service-bus-queue-and-topic-outputs) |

A partíciók száma [a Service Bus termékváltozatán és méretétől függ.](../service-bus-messaging/service-bus-partitioning.md) A partíciókulcs az egyes partíciók egyedi egész értéke.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) egy globálisan elosztott adatbázis-szolgáltatás, amely korlátlan rugalmas skálázást kínál világszerte, gazdag lekérdezést és automatikus indexelést a séma-agnosztikus adatmodelleken keresztül. Ha többet szeretne megtudni az Azure Cosmos DB tároló beállításait a Stream Analytics, tekintse meg a [Stream Analytics az Azure Cosmos DB kimeneti](stream-analytics-documentdb-output.md) cikkben.

A Stream Analytics Azure Cosmos DB kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régiókban.

> [!Note]
> Ebben az időben az Azure Stream Analytics csak az SQL API használatával támogatja az Azure Cosmos DB-vel való kapcsolatot.
> Más Azure Cosmos DB API-k még nem támogatottak. Ha az Azure Stream Analytics az Azure Cosmos DB-fiókok más API-kkal létrehozott, előfordulhat, hogy az adatok nem megfelelően tárolják.

Az alábbi táblázat ismerteti az Azure Cosmos DB-kimenet létrehozásához használható tulajdonságokat.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Egy alias, amely ezt a kimenetet a Stream Analytics-lekérdezésben hivatkozik. |
| Sink (Fogadó) | Azure Cosmos DB. |
| Importálási beállítás | Válassza **a Cosmos DB kiválasztása az előfizetésből** vagy **adja meg manuálisan a Cosmos DB beállításait.**
| Fiókazonosító | Az Azure Cosmos DB-fiók neve vagy végpontURI-ja. |
| Fiókkulcs | Az Azure Cosmos DB-fiók megosztott hozzáférési kulcsa. |
| Adatbázis | Az Azure Cosmos DB adatbázis neve. |
| Tárolónév | A használni használandó tárolónév, amelynek léteznie kell a Cosmos DB-ben. Példa:  <br /><ul><li> _MyContainer_: A "MyContainer" nevű tárolónak léteznie kell.</li>|
| Dokumentum azonosítója |Választható. A mező neve a kimeneti eseményekben, amely a beszúrási vagy frissítési műveletek alapjául szolgáló elsődleges kulcs megadására szolgál.

## <a name="azure-functions"></a>Azure Functions
Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amely segítségével futtathatja a kódot igény szerint anélkül, hogy explicit módon kiépítése vagy kezelése infrastruktúra. Lehetővé teszi az Azure-ban vagy a partnerszolgáltatásokban bekövetkező események által kiváltott kód implementálására. Az Azure Functions azon képessége, hogy reagáljon az eseményindítókra, természetes kimenetet biztosít az Azure Stream Analytics számára. Ez a kimeneti adapter lehetővé teszi a felhasználók számára, hogy a Stream Analytics-et az Azure Functionshez csatlakoztassák, és különböző eseményekre válaszul parancsfájlt vagy kóddarabot futtassanak.

A Stream Analytics Azure Functions kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régiókban.

Az Azure Stream Analytics http-eseményindítókon keresztül hívja meg az Azure Functions szolgáltatást. Az Azure Functions kimeneti adapter a következő konfigurálható tulajdonságokkal érhető el:

| Tulajdonság neve | Leírás |
| --- | --- |
| Függvényalkalmazás |Az Azure Functions alkalmazás neve. |
| Függvény |A függvény neve az Azure Functions alkalmazásban. |
| Kulcs |Ha egy másik előfizetésből származó Azure-függvényt szeretne használni, ezt megteheti a funkció eléréséhez szükséges kulcs megadásával. |
| Maximális kötegméret |Egy tulajdonság, amely lehetővé teszi, hogy állítsa be a maximális méretet minden kimeneti köteg, amely az Azure-függvény. A bemeneti egység bájtban van. Ez az érték alapértelmezés szerint 262 144 bájt (256 KB). |
| Kötegek maximális száma  |Egy tulajdonság, amely lehetővé teszi, hogy adja meg a maximális események száma minden kötegben, amely az Azure Functions küldött. Az alapértelmezett érték 100. |

Az Azure Stream Analytics 200-as HTTP-állapotot vár a Functions alkalmazástól a sikeresen feldolgozott kötegekhez.

Amikor az Azure Stream Analytics kap egy 413-as ("http request entity too large") kivételt egy Azure-függvénytől, csökkenti az Azure Functionsnek küldött kötegek méretét. Az Azure-függvénykódban használja ezt a kivételt, és győződjön meg arról, hogy az Azure Stream Analytics nem küld túlméretes kötegeket. Győződjön meg arról is, hogy a függvényben használt maximális kötegszám és méretértékek konzisztensek a Stream Analytics portálon megadott értékekkel.

> [!NOTE]
> A tesztkapcsolat során a Stream Analytics egy üres köteget küld az Azure Functionsnek, hogy tesztelje, működik-e a kapcsolat a kettő között. Győződjön meg arról, hogy a Functions alkalmazás kezeli az üres kötegkérelmeket, hogy megbizonyosodjon arról, hogy a tesztkapcsolat sikeres.

Is, egy olyan helyzetben, ahol nincs esemény leszállás egy időablakban, nem jön létre kimenet. Ennek eredményeképpen a computeResult függvény nem hív.Ennek eredményeképpen a **computeResult** függvény nem hívják. Ez a viselkedés összhangban van a beépített ablakos összesítő függvényekkel.

## <a name="custom-metadata-properties-for-output"></a>Egyéni metaadat-tulajdonságok a kimenethez 

A kimenő üzenetekhez lekérdezésoszlopokat is csatolhat felhasználói tulajdonságokként. Ezek az oszlopok nem kerülnek a hasznos teherbe. A tulajdonságok a kimeneti üzenetszótár formájában vannak jelen. *Kulcs:* az oszlop név, *az érték* pedig a tulajdonságszótár oszlopértéke. A Record és a Array kivételével minden Stream Analytics-adattípus támogatott.  

Támogatott kimenetek: 
* Service Bus-üzenetsor 
* Service Bus-témakör 
* Eseményközpont 

A következő példában hozzáadjuk `DeviceId` a `DeviceStatus` két mezőt és a metaadatokat. 
* Lekérdezés:`select *, DeviceId, DeviceStatus from iotHubInput`
* Kimeneti konfiguráció:`DeviceId,DeviceStatus`

![Tulajdonságoszlopok](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Az alábbi képernyőképen az EventHub szolgáltatásbusz-kezelőn keresztül ellenőrzött kimeneti üzenetek tulajdonságai [láthatók.](https://github.com/paolosalvatori/ServiceBusExplorer)

![Esemény egyéni tulajdonságai](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>A Service Bus-várólista és a témakör kimenetének rendszertulajdonságai 
A lekérdezési oszlopokat [rendszertulajdonságokként](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) csatolhatja a kimenő szolgáltatásbusz várólistához vagy a Topic üzenetekhez. Ezek az oszlopok nem kerülnek a hasznos adatba, hanem a megfelelő BrokeredMessage [rendszertulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) a lekérdezésoszlop értékeivel van feltöltve.
Ezek a rendszertulajdonságok `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`támogatottak - .
Ezekaz oszlopok karakterláncértékei megfelelő rendszertulajdonság-értéktípusként vannak elemezve, és az esetleges elemzési hibák adathibaként lesznek kezelve.
Ez a mező JSON-objektumformátumként van megadva. Részletek erről a formátumról a következők -
* Körülvéve kapcsos {}zárójelek .
* Kulcs/érték párban írva.
* A kulcsoknak és értékeknek karakterláncnak kell lenniük.
* Kulcs a rendszertulajdonság neve, az érték pedig a lekérdezésoszlop neve.
* A kulcsokat és az értékeket kettőspont választja el egymástól.
* Minden kulcs/érték párt vessző választ el egymástól.

Ez bemutatja, hogyan kell használni ezt a tulajdonságot -

* Lekérdezés:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Rendszertulajdonság-oszlopok:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Ez beállítja a `MessageId` szolgáltatásbusz várólistáján lévő üzenetsorokat 's értékekkel, `column1`és a PartitionKey 's értékekkel `column2`van beállítva.

## <a name="partitioning"></a>Particionálás

Az alábbi táblázat összefoglalja a partíció támogatását és az egyes kimeneti típusok kimeneti íróinak számát:

| Kimenet típusa | Particionálás támogatása | Partíciókulcs  | Kimenetírók száma |
| --- | --- | --- | --- |
| Azure Data Lake Store | Igen | {date} és {time} tokenek használata az elérési út előtagmintájában. Válassza ki a dátumformátumot, például YYYY/HH/ND, DD/HH/YYYY vagy MM-DD-YYYY. Hh az időformátumhoz használatos. | A [teljes mértékben párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure SQL Database | Igen, engedélyezni kell. | A lekérdezéspartition by záradéka alapján. | Ha a Particionálás öröklése beállítás engedélyezve van, a [teljes mértékben párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. Ha többet szeretne megtudni arról, hogy miként érhető el jobb írási átviteli teljesítmény az Azure SQL Database-be való betöltésekor, olvassa el az [Azure Stream Analytics kimenetét az Azure SQL Database-be című témakörben.](stream-analytics-sql-output-perf.md) |
| Azure Blob Storage | Igen | {date} és {time} tokenek használata az eseménymezőkből az elérési út mintájában. Válassza ki a dátumformátumot, például YYYY/HH/ND, DD/HH/YYYY vagy MM-DD-YYYY. Hh az időformátumhoz használatos. A blobkimenet et egyetlen egyéni eseményattribútum {fieldname} vagy {datetime:\<>} particionálhatja. | A [teljes mértékben párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure Event Hubs | Igen | Igen | A partíció igazításától függően változik.<br /> Ha az eseményközpont kimenetének partíciókulcsa egyaránt igazodik a felsőbb szintre (előző) lekérdezési lépéshez, az írók száma megegyezik az eseményközpont kimenetében lévő partíciók számával. Minden író az [EventHubSender osztályt](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) használja az adott partícióra való események küldéséhez. <br /> Ha az eseményközpont kimenetének partíciókulcsa nincs összhangban a felsőbb szintre (előző) lekérdezési lépéssel, az írók száma megegyezik az előző lépésben lévő partíciók számával. Minden író az **EventHubClient** [SendBatchAsync osztályát](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) használja az összes kimeneti partícióra való küldéshez. |
| Power BI | Nem | None | Nem alkalmazható. |
| Azure Table Storage | Igen | Bármely kimeneti oszlop.  | A [teljes mértékben párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure Service Bus-témakör | Igen | Automatikusan kiválasztva. A partíciók száma a [Service Bus termékváltozatán és méretétől](../service-bus-messaging/service-bus-partitioning.md)függ. A partíciókulcs az egyes partíciók egyedi egész értéke.| Megegyezik a kimeneti témakörben lévő partíciók számával.  |
| Azure Service Bus-üzenetsor | Igen | Automatikusan kiválasztva. A partíciók száma a [Service Bus termékváltozatán és méretétől](../service-bus-messaging/service-bus-partitioning.md)függ. A partíciókulcs az egyes partíciók egyedi egész értéke.| Megegyezik a kimeneti várólistában lévő partíciók számával. |
| Azure Cosmos DB | Igen | A lekérdezéspartition by záradéka alapján. | A [teljes mértékben párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |
| Azure Functions | Igen | A lekérdezéspartition by záradéka alapján. | A [teljes mértékben párhuzamos lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. |

A kimeneti írók száma is `INTO <partition count>` vezérelhető a lekérdezésben (lásd [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)záradék használatával, ami hasznos lehet a kívánt feladat topológia elérésében. Ha a kimeneti adapter nincs particionálva, az adatok hiánya egy bemeneti partíción a késői érkezési idő ig késlelteti. Ilyen esetekben a kimenet egyetlen íróvá egyesül, ami szűk keresztmetszeteket okozhat a folyamatban. Ha többet szeretne megtudni a késői érkezési szabályzatról, olvassa el az [Azure Stream Analytics eseményrendelési szempontjait.](stream-analytics-out-of-order-and-late-events.md)

## <a name="output-batch-size"></a>Kimeneti köteg mérete
Az Azure Stream Analytics változó méretű kötegeket használ az események feldolgozásához és a kimenetek írásához. Általában a Stream Analytics-motor nem ír egyszerre egy üzenetet, és kötegeket használ a hatékonyság érdekében. Ha a bejövő és a kimenő események aránya is magas, a Stream Analytics nagyobb kötegeket használ. Ha a kimenő forgalom aránya alacsony, kisebb kötegeket használ a késés alacsony anamtiójának fenntartásához.

Az alábbi táblázat a kimeneti kötegelés néhány szempontját ismerteti:

| Kimenet típusa |    Üzenet maximális mérete | Kötegméret optimalizálása |
| :--- | :--- | :--- |
| Azure Data Lake Store | Lásd: [Data Lake Storage korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Írási műveletenként legfeljebb 4 MB-ot használhat. |
| Azure SQL Database | A maximális kötegszám használatával konfigurálható. Alapértelmezés szerint legfeljebb 10 000 maximális és 100 minimális sor egyetlen ömlesztett lapkánként.<br />Lásd: [Azure SQL-korlátok](../sql-database/sql-database-resource-limits.md). |  Kezdetben minden köteg tömegesen bevan szúrva, maximális kötegszámmal. A köteg ek felére oszlik (a minimális kötegszámig) az SQL újrapróbálható hibái alapján. |
| Azure Blob Storage | Lásd: [Azure Storage-korlátozások.](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) | A blobblokk maximális mérete 4 MB.<br />A blob bock maximális száma 50 000. |
| Azure Event Hubs    | 256 KB vagy 1 MB/üzenet. <br />Lásd: [Eseményközpontok korlátai.](../event-hubs/event-hubs-quotas.md) |    Ha a bemeneti/kimeneti particionálás nincs összehangolva, minden esemény külön-külön `EventData` van csomagolva, és a maximális üzenetméretig kötegben kerül elküldésre. Ez akkor is előfordul, ha [egyéni metaadat-tulajdonságokat](#custom-metadata-properties-for-output) használ. <br /><br />  A bemeneti/kimeneti particionálás igazításakor több `EventData` esemény egyetlen példányba van csomagolva, a maximális üzenetméretig, és elküldésre kerül.    |
| Power BI | Lásd: [Power BI Rest API-korlátozások](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Lásd: [Azure Storage-korlátozások.](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) | Az alapértelmezett érték 100 entitás egy tranzakciónként. Beállíthatja, hogy egy kisebb értéket, ha szükséges. |
| Azure Service Bus-üzenetsor    | 256 KB/üzenet a standard szinthez, 1 MB a prémium szinthez.<br /> Lásd: [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md). | Üzenetenként egyetlen eseményt használjon. |
| Azure Service Bus-témakör | 256 KB/üzenet a standard szinthez, 1 MB a prémium szinthez.<br /> Lásd: [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md). | Üzenetenként egyetlen eseményt használjon. |
| Azure Cosmos DB    | Tekintse meg [az Azure Cosmos DB-kor .](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits) | A kötegek mérete és az írási gyakoriság dinamikusan módosul az Azure Cosmos DB válaszai alapján. <br /> A Stream Analytics nem korlátoz előre meghatározott korlátozásokat. |
| Azure Functions    | | Az alapértelmezett kötegméret 262 144 bájt (256 KB). <br /> Az alapértelmezett eseményszám kötegenként 100. <br /> A kötegméret konfigurálható, és növelhető vagy csökkenthető a Stream Analytics [kimeneti beállításaiban.](#azure-functions)

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
