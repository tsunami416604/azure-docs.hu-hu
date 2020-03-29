---
title: Az Azure Data Explorer integrációja az Azure Data Factoryval
description: Ebben a témakörben integrálja az Azure Data Explorert az Azure Data Factoryval a másolási, kereső- és parancstevékenységek használatához.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293623"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Az Azure Data Explorer integrálása az Azure Data Factory-val

[Az Azure Data Factory](/azure/data-factory/) (ADF) egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi a különböző adattárak integrálását és az adatokon végzett tevékenységek elvégzését. Az ADF lehetővé teszi adatvezérelt munkafolyamatok létrehozását az adatok mozgatásának és az adatok átalakításának koordinálására és automatizálására. Az Azure Data Explorer az Azure Data Factory egyik [támogatott adattára.](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Az Azure Data Factory tevékenységei az Azure Data Explorerhez

Az Azure Data Explorer felhasználói számára különböző integrációk érhetők el az Azure Data Factory szolgáltatással:

### <a name="copy-activity"></a>Másolási tevékenység
 
Az Azure Data Factory Copy tevékenység az adattárak közötti adatátvitelre szolgál. Az Azure Data Explorer forrásként támogatott, ahol az adatok at az Azure Data Explorer bármely támogatott adattárba másolja, és egy fogadót, ahol az adatok at a támogatott adattárból az Azure Data Explorerbe másolja a rendszer. További információt az [Azure Data Explorer be vagy onnan történő másolása című](/azure/data-factory/connector-azure-data-explorer)témakörben talál. részletes átjárási témakörben az [Azure Data Factory adatainak betöltése az Azure Data Explorerbe.](data-factory-load-data.md)
Az Azure Data Explorert az Azure IR (Integration Runtime) támogatja, amelyet akkor használnak, amikor az adatokat az Azure-ban másolja, és saját üzemeltetésű infravörös kapcsolatát, amelyet a helyszínen vagy hozzáférés-vezérléssel rendelkező hálózatról vagy hálózatba történő adattárolókból történő másolásakor, például egy Azure virtuális hálózaton való másolásakor használ. További információkért lásd, [hogy melyik infravörös](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> A másolási tevékenység használatakor és **egy csatolt szolgáltatás** vagy **adatkészlet**létrehozásakor válassza ki az Azure Data **Explorer (Kusto)** adattárát, és ne a régi **Kusto**adattára.  

### <a name="lookup-activity"></a>Keresési tevékenység
 
A lookup tevékenység az Azure Data Explorer lekérdezéseinek végrehajtására szolgál. A lekérdezés eredménye a lekérdezés kimeneteként lesz visszaadva, és az [ADF-kapcsolat dokumentációjában](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)leírtak szerint használható a folyamat következő tevékenységében.  
Az 5000 sorra és 2 MB-ra vonatkozó válaszméret-korláton kívül a tevékenység lekérdezési időkorlátja is 1 óra.

### <a name="command-activity"></a>Parancstevékenység

A parancstevékenység lehetővé teszi az Azure Data Explorer [vezérlőparancsainak](/azure/kusto/concepts/#control-commands)végrehajtását. A lekérdezésekkel ellentétben a vezérlőparancsok módosíthatják az adatokat vagy a metaadatokat. Néhány vezérlőparancs az Azure Data Explorer betöltésére irányul, `.ingest`például vagy `.set-or-append`) parancsokkal, vagy adatok másolása `.export`az Azure Data Explorerből külső adattárakba olyan parancsokkal, mint például a .
A parancstevékenység részletes átjárását az [Azure Data Explorer vezérlőparancsainak futtatásához az Azure Data Factory parancstevékenysége című](data-factory-command-activity.md)témakörben található.  Az adatok másolásához használt vezérlőparancs időnként gyorsabb és olcsóbb lehet, mint a Másolás tevékenység. Ha meg szeretné tudni, hogy mikor használja a Parancstevékenységet a Másolás tevékenységgel szemben, olvassa el a [Másolás és a Parancs tevékenységek közötti másolás az adatok másolásakor című témakört.](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)

### <a name="copy-in-bulk-from-a-database-template"></a>Másolás tömegesen adatbázissablonból

A [másolás tömegesen egy adatbázisból az Azure Data Explorer az Azure Data Factory sablon használatával](data-factory-template.md) egy előre definiált Azure Data Factory-folyamat. A sablon segítségével adatbázisonként vagy táblánként több folyamatot hozhat létre a gyorsabb adatmásolás érdekében. 

### <a name="mapping-data-flows"></a>Adatfolyamok leképezése 

[Az Azure Data Factory leképezési adatfolyamok](/azure/data-factory/concepts-data-flow-overview) vizuálisan tervezett adatátalakítások, amelyek lehetővé teszik az adatmérnökök számára, hogy kód írása nélkül fejlesszenek grafikus adatátalakítási logikát. Adatfolyam létrehozásához és adatok Azure Data Explorer betöltéséhez használja a következő módszert:

1. Hozza létre a [leképezési adatfolyamot.](/azure/data-factory/data-flow-create)
1. [Exportálja az adatokat az Azure Blobba.](/azure/data-factory/data-flow-sink) 
1. [Eseményrács](/azure/data-explorer/ingest-data-event-grid) vagy [ADF másolási tevékenység definiálása](/azure/data-explorer/data-factory-load-data) az adatok Azure Data Explorerbe való betöltéséhez.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Adatmásoláskor válassza a Másolás és az Azure Data Explorer parancstevékenységeit 

Ez a rész segítséget nyújt az adatmásolási igényeknek megfelelő tevékenység kiválasztásában.

Az Azure Data Explorerből vagy az Azure Data Explorerbe történő adatmásoláskor két lehetőség közül választhat az Azure Data Factory:
* Másolási tevékenység.
* Az Azure Data Explorer parancstevékenysége, amely végrehajtja az Azure Data Explorer ben adatokat továbbító vezérlőparancsok egyikét. 

### <a name="copy-data-from-azure-data-explorer"></a>Adatok másolása az Azure Data Explorerből
  
Az Azure Data Explorer ből adatokat másolhat a másolási tevékenység vagy a [`.export`](/azure/kusto/management/data-export/) parancs használatával. A `.export` parancs végrehajt egy lekérdezést, majd exportálja a lekérdezés eredményeit. 

Tekintse meg az alábbi táblázatot a `.export` másolási tevékenység és a parancs az Azure Data Explorer adatok másolása.

| | Másolási tevékenység | .export parancs |
|---|---|---|
| **Folyamat leírása** | Az ADF lekérdezést hajt végre a Kusto-n, feldolgozza az eredményt, és elküldi a céladattárnak. <br>(**ADX > ADF > fogadó adattár**) | Az ADF `.export` ellenőrző parancsot küld az Azure Data Explorernek, amely végrehajtja a parancsot, és az adatokat közvetlenül a céladattárba küldi. <br>(**ADX > fogadó adattár)** |
| **Támogatott céladat-tárolók** | A támogatott [adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) széles választéka | ADLSv2, Azure Blob, SQL-adatbázis |
| **Teljesítmény** | Központosított | <ul><li>Elosztott (alapértelmezett), adatok exportálása egyszerre több csomópontról</li><li>Gyorsabb és CogS hatékony.</li></ul> |
| **Kiszolgálói korlátok** | [A lekérdezési korlátok](/azure/kusto/concepts/querylimits) meghosszabbíthatók/letilthatók. Alapértelmezés szerint az ADF-lekérdezések a következőket tartalmazzák: <ul><li>500 000 rekord vagy 64 MB-os méretkorlát.</li><li>10 perces határidő.</li><li>`noTruncation`hamisra állítva.</li></ul> | Alapértelmezés szerint kiterjeszti vagy letiltja a lekérdezési korlátokat: <ul><li>A méretkorlátok le vannak tiltva.</li><li>A kiszolgáló időtúlidőpontja 1 órára van meghosszabbítva.</li><li>`MaxMemoryConsumptionPerIterator`és `MaxMemoryConsumptionPerQueryPerNode` max (5 GB, TotalPhysicalMemory/2) értékre vannak kiterjesztve.</li></ul>

> [!TIP]
> Ha a másolási cél a `.export` parancs által támogatott adattárolók egyike, és ha a Másolás `.export` tevékenység funkciók egyike sem áll létfontosságú az Ön igényeinek, válassza ki a parancsot.

### <a name="copying-data-to-azure-data-explorer"></a>Adatok másolása az Azure Data Explorer be

Az Azure Data Explorer bemásolási tevékenységvagy betöltési parancsok, például`.set-or-append` `.set-or-replace`a `.set` `.replace)`lekérdezésből ( ,`.ingest`, , , és [a storage( betöltése](/azure/kusto/management/data-ingestion/ingest-from-storage) ( használatával) történő másolási vagy [betöltési](/azure/kusto/management/data-ingestion/ingest-from-query) parancsokkal másolhatja az adatokat. 

Tekintse meg az alábbi táblázatot a másolási tevékenység összehasonlítását, és az Azure Data Explorer adatok másolására szolgáló betöltési parancsokat.

| | Másolási tevékenység | Betöltés lekérdezésből<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Betöltés a tárolóból <br> `.ingest` |
|---|---|---|---|
| **Folyamat leírása** | Az ADF leváltja az adatokat a forrás-adattárból, táblázatos formátumba konvertálja azokat, és elvégzi a szükséges sémaleképezési módosításokat. Az ADF ezután feltölti az adatokat az Azure-blobokba, adattömbökre osztja fel, majd letölti a blobokat, hogy betöltse őket az ADX-táblába. <br> (**Forrásadattár > ADF > Azure blobok > ADX)** | Ezek a parancsok lekérdezést `.show` vagy parancsot hajthatnak végre, és a lekérdezés eredményét táblába (**ADX > ADX)** is belehet adni. | Ez a parancs úgy bevezeti az adatokat egy táblába, hogy "lehúzza" az adatokat egy vagy több felhőalapú tárolási összetevőből. |
| **Támogatott forrásadat-tárolók** |  [különböző lehetőségek](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (a sql_request plugin használatával), Cosmos (a cosmosdb_sql_request plugin használatával) és bármely más adattár, amely HTTP- vagy Python API-kat biztosít. | Fájlrendszer, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Teljesítmény** | A betöltések várólistára kerülnek és kezelhetők, ami biztosítja a kis méretű betöltéseket, és magas rendelkezésre állást biztosít a terheléselosztás, az újrapróbálkozások és a hibakezelés biztosításával. | <ul><li>Ezeket a parancsokat nem nagy mennyiségű adat importálására tervezték.</li><li>A várt módon és olcsóbban működik. De éles forgatókönyvek, és ha a forgalmi sebesség és az adatok mérete nagy, használja a Másolás tevékenység.</li></ul> |
| **Kiszolgálói korlátok** | <ul><li>Nincs méretkorlát.</li><li>Maximális időkorlát: 1 óra egy lenyelt blob. |<ul><li>A lekérdezési rész csak méretkorláttal van eladva, amely `noTruncation=true`et a megadásával lehet kihagyni.</li><li>Maximális időkorlát: 1 óra.</li></ul> | <ul><li>Nincs méretkorlát.</li><li>Maximális időkorlát: 1 óra.</li></ul>|

> [!TIP]
> * Amikor adatokat másol az ADF-ből az `ingest from query` Azure Data Explorer a parancsokat használja.  
> * Nagy adatkészletek (>1 GB-os) használja a Másolás tevékenység.  

## <a name="required-permissions"></a>Szükséges engedélyek

Az alábbi táblázat felsorolja az Azure Data Factory-val való integráció különböző lépéseihez szükséges engedélyeket.

| Lépés | Művelet | Az engedélyek minimális szintje | Megjegyzések |
|---|---|---|---|
| **Csatolt szolgáltatás létrehozása** | Adatbázis-navigáció | *adatbázis-megjelenítő* <br>Az ADF-et használó bejelentkezett felhasználónak fel kell hatalmazni az adatbázis metaadatainak olvasására. | A felhasználó manuálisan is megadhatja az adatbázis nevét. |
| | Kapcsolat tesztelése | *adatbázis-figyelő* vagy *táblabeadás* <br>Az egyszerű szolgáltatásnak engedélyezni kell `.show` az adatbázisszintű parancsok vagy táblaszintű betöltés végrehajtását. | <ul><li>A TestConnection a fürthöz való csatlakozást ellenőrzi, nem pedig az adatbázist. Akkor is sikeres lehet, ha az adatbázis nem létezik.</li><li>A táblarendszergazdai engedélyek nem elegendőek.</li></ul>|
| **Adatkészlet létrehozása** | Táblázatnavigáció | *adatbázis-figyelő* <br>Az ADF-et használó bejelentkezett felhasználónak jogosultnak kell lennie az adatbázisszintű `.show` parancsok végrehajtására. | A felhasználó manuálisan is megadhatja a tábla nevét.|
| **Adatkészlet** vagy **másolási tevékenység** létrehozása | Adatok előnézete | *adatbázis-megjelenítő* <br>Az egyszerű szolgáltatásnak jogosultnak kell lennie az adatbázis metaadatainak olvasására. | | 
|   | Séma importálása | *adatbázis-megjelenítő* <br>Az egyszerű szolgáltatásnak jogosultnak kell lennie az adatbázis metaadatainak olvasására. | Ha az ADX egy táblázatos-táblázatos másolat forrása, az ADF automatikusan importálja a sémát, még akkor is, ha a felhasználó nem importált a sémát explicit módon. |
| **ADX mint fogadó** | Név szerint oszlopleképezés létrehozása | *adatbázis-figyelő* <br>Az egyszerű szolgáltatásnak engedélyeznie `.show` kell az adatbázisszintű parancsok végrehajtásához. | <ul><li>Minden kötelező művelet együtt fog működni *a táblabeesővel.*</li><li> Egyes választható műveletek sikertelenek lehetnek.</li></ul> |
|   | <ul><li>CSV-hozzárendelés létrehozása a táblában</li><li>A leképezés eldobása</li></ul>| *táblabetöltés* vagy *adatbázis-rendszergazda* <br>A szolgáltatásnévnek jogosultnak kell lennie a tábla módosításához. | |
|   | Adatok betöltése | *táblabetöltés* vagy *adatbázis-rendszergazda* <br>A szolgáltatásnévnek jogosultnak kell lennie a tábla módosításához. | | 
| **ADX forrásként** | Lekérdezés végrehajtása | *adatbázis-megjelenítő* <br>Az egyszerű szolgáltatásnak jogosultnak kell lennie az adatbázis metaadatainak olvasására. | |
| **Kusto parancs** | | Az egyes parancsok engedélyszintje szerint. |

## <a name="performance"></a>Teljesítmény 

Ha az Azure Data Explorer a forrás, és a lekérdezést tartalmazó lekérdezést tartalmazó kereső, másolási vagy parancstevékenységet használja, tekintse meg a teljesítményadatokra vonatkozó [gyakorlati tanácsok lekérdezési gyakorlati tanácsait](/azure/kusto/query/best-practices) és [a másolási tevékenység ADF dokumentációját.](/azure/data-factory/copy-activity-performance)
  
Ez a szakasz a másolási tevékenység használatával foglalkozik, ahol az Azure Data Explorer a fogadó. Az Azure Data Explorer-fogadó becsült átviteli sebessége 11–13 Mb/s. Az alábbi táblázat ismerteti az Azure Data Explorer-fogadó teljesítményét befolyásoló paramétereket.

| Paraméter | Megjegyzések |
|---|---|
| **Az összetevők földrajzi közelsége** | Helyezze az összes összetevőt ugyanabban a régióban:<ul><li>forrás- és fogadó adattárolók.</li><li>ADF-integrációs futásidejű.</li><li>Az ADX-fürt.</li></ul>Győződjön meg arról, hogy legalább az integrációs futásidejű ugyanabban a régióban, mint az ADX-fürt. |
| **DIUs-ok száma** | 1 virtuális gép az ADF által használt minden 4 DIUs-hoz. <br>A DIUs-ok növelése csak akkor segít, ha a forrás több fájlt tartalmazó fájlalapú tároló. Minden virtuális gép ezután egy másik fájlt dolgoz fel párhuzamosan. Ezért egyetlen nagy fájl másolása nagyobb késést eredményez, mint több kisebb fájl másolása.|
|**Az ADX-fürt mennyisége és termékváltozata** | Az ADX-csomópontok nagy száma növeli a betöltési feldolgozási időt.|
| **Párhuzamosság** | Ha nagyon nagy mennyiségű adatot szeretne másolni egy adatbázisból, particionálja az adatokat, majd használjon foreach hurkot, amely az egyes partíciókat párhuzamosan másolja, vagy használja a [Tömeges másolás adatbázisból az Azure Data Explorer sablonba](data-factory-template.md). Megjegyzés: A Másolás**tevékenységben a párhuzamosság** **foka** > nem releváns az ADX számára. |
| **Az adatfeldolgozás összetettsége** | A késés a forrásfájl formátumáttól, az oszlopleképezéstől és a tömörítéstől függően változik.|
| **Az integrációs futtató virtuális gép** | <ul><li>Az Azure-példány, ADF virtuális gépek és a gép idoszai nem módosíthatók.</li><li> Az Azure-példány on-prem, határozza meg, hogy a virtuális gép üzemelteti a saját üzemeltetésű infravörös elég erős.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tippek és gyakori buktatók

### <a name="monitor-activity-progress"></a>Tevékenységállapotának figyelése

* A tevékenység előrehaladásának figyelésekor az *Adatok írásbeli* tulajdonsága sokkal nagyobb lehet, mint az *Adatolvasás* tulajdonság, mivel az *adatok olvasása* a bináris fájlméret alapján kerül kiszámításra, míg a *beírt adatok* számítása a memórián belüli méret alapján történik, miután az adatokat deszimorizálták és kibontották.

* A tevékenység előrehaladásának figyelése során láthatja, hogy az adatok az Azure Data Explorer fogadóba írásakor. Az Azure Data Explorer tábla lekérdezésekekor láthatja, hogy az adatok nem érkeztek meg. Ennek az az oka, hogy az Azure Data Explorer be másolása két szakaszból áll. 
    * Az első szakasz beolvassa a forrásadatokat, felosztja azokat 900 MB-os adattömbökre, és feltölti az egyes adattömböket egy Azure Blobba. Az első szakaszt az ADF tevékenységelőrehaladási nézete látja. 
    * A második szakasz akkor kezdődik, amikor az összes adatot feltölti az Azure Blobs. Az Azure Data Explorer motorcsomópontjai letöltik a blobokat, és betöltik az adatokat a fogadótáblába. Az adatok ezután látható az Azure Data Explorer tábla.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>A CSV-fájlok betöltésének elmulasztása a nem megfelelő szökés miatt

Az Azure Data Explorer elvárja, hogy a CSV-fájlok igazodjanak az [RFC 4180-hoz.](https://www.ietf.org/rfc/rfc4180.txt)
Arra számít, hogy:
* A kiszökést igénylő karaktereket (például " és új sorokat) tartalmazó mezőknek **"** karakterrel kell kezdődniük és végződniük, szóköz nélkül. A *mezőn belüli* összes **"** karakter kettős **" karakterrel (** **""**) távozik. Például a _"Hello, ""World"""_ egy érvényes CSV-fájl, amelyegyetlen rekordot tartalmaz, amelynek egyetlen oszlopa vagy mezője van, amelynek tartalma _Hello, "World"_.
* A fájlban lévő összes rekordnak azonos számú oszlopmal és mezővel kell rendelkeznie.

Az Azure Data Factory lehetővé teszi a fordított perjel (escape) karakter. Ha az Azure Data Factory használatával fordított perszés karakterrel rendelkező CSV-fájlt hoz létre, a fájl Azure Data Explorerbe történő betöltése sikertelen lesz.

#### <a name="example"></a>Példa

A következő szöveges értékek: Hello, "World"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Meg kell jelennie egy megfelelő CSV fájlban a következőkszerint: "Hello, ""World"""<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
"""ABC\D""EF"<br/>

Az alapértelmezett escape karakter (fordított perjel) használatával a következő CSV nem \"fog\"működni az Azure Data Explorer rel: "Hello, World"<br/>
"ABC DEF"<br/>
"\"ABC DEF "<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Beágyazott JSON-objektumok

JSON-fájl Azure Data Explorer bemásolásakor vegye figyelembe a következőket:
* A tömbök nem támogatottak.
* Ha a JSON-struktúra objektumadat-típusokat tartalmaz, az Azure Data Factory összeolvasztja az objektum gyermekelemeit, és megpróbálja leképezni az egyes gyermekelemeket egy másik oszlophoz az Azure Data Explorer-táblázatban. Ha azt szeretné, hogy a teljes objektumelem egyetlen oszlophoz legyen hozzárendelve az Azure Data Explorerben:
    * A teljes JSON-sor egyetlen dinamikus oszlopba történő betöltése az Azure Data Explorerben.
    * Manuálisan szerkesztheti a folyamatdefiníciót az Azure Data Factory JSON-szerkesztőjével. A **leképezések**
       * Távolítsa el az egyes gyermekelemekhez létrehozott több leképezést, és adjon hozzá egyetlen leképezést, amely leképezi az objektumtípusát a táblázatoszlophoz.
       * A záró szögletes zárójel után adjunk hozzá egy vesszőt, majd:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>További tulajdonságok megadása az Azure Data Explorer be másolásakor

> [!NOTE]
> Ez a funkció jelenleg a JSON-tartalom manuális szerkesztésével érhető el. 

Adjon hozzá egy sort a másolási tevékenység "fogadó" szakaszában az alábbiak szerint:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Az érték elől való menekülés trükkös lehet. Hivatkozásként használja a következő kódrészletet:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

A nyomtatott érték:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>További lépések

* Megtudhatja, [hogyan másolhat adatokat az Azure Data Explorerbe az Azure Data Factory használatával.](data-factory-load-data.md)
* Ismerje meg, hogyan használja az [Azure Data Factory sablont az adatbázisból az Azure Data Explorerbe történő tömeges másoláshoz.](data-factory-template.md)
* Ismerje meg, hogy miként futtathatja [az Azure Data Explorer vezérlőparancsait az Azure Data Factory parancstevékenységével.](data-factory-command-activity.md)
* Ismerje meg az [Azure Data Explorer adatlekérdezési lekérdezések.](/azure/data-explorer/web-query-data)



