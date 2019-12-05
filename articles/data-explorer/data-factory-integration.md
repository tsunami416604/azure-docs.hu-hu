---
title: Az Azure Adatkezelő integrációja Azure Data Factory
description: Ebben a témakörben az Azure Adatkezelő és az Azure Data Factory integrálásával a másolási, keresési és parancs-tevékenységeket használhatja
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 51683e529f832e06efbe8eb71466f3b27d95fcb1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819144"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Az Azure Adatkezelő integrálása Azure Data Factory

A [Azure Data Factory](/azure/data-factory/) (ADF) egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi a különböző adattárak integrálását és az adatokon végzett tevékenységek végrehajtását. Az ADF lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatáthelyezés és az adatátalakítás előkészítéséhez és automatizálásához. Az Azure Adatkezelő a Azure Data Factory [által támogatott adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) egyike. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory tevékenységek az Azure Adatkezelő

Az Azure Adatkezelő-felhasználók számára Azure Data Factory különböző integrációk érhetők el:

### <a name="copy-activity"></a>Másolási tevékenység
 
Az adattárak közötti adatátvitelhez Azure Data Factory másolási tevékenység használható. Az Azure Adatkezelő forrásaként támogatott, ahol az adatok az Azure Adatkezelőból a támogatott adattárba és egy fogadóba másolódnak át, ahol az adatok a bármely támogatott adattárból az Azure Adatkezelőba másolódnak. További információ: [adatok másolása az Azure-ba vagy az Azure Adatkezelő Azure Data Factory használatával](/azure/data-factory/connector-azure-data-explorer). a részletes útmutatóért lásd: [adatok betöltése Azure Data Factoryból az Azure Adatkezelőba](data-factory-load-data.md).
Az Azure Adatkezelőt a Azure IR (Integration Runtime) támogatja, amelyet az Azure-ban tárolt adatok és a saját üzemeltetésű integrációs modul használ, amely akkor használatos, amikor az adatok a helyszíni vagy egy hozzáférés-vezérlési szolgáltatással (például egy Azure-Virtual Network) történő adattárakba másolódnak. További információ: a [használandó IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> A másolási tevékenység és a **társított szolgáltatás** vagy **adatkészlet**létrehozásakor válassza az adattár **Azure adatkezelő (Kusto)** elemet, és ne a régi adattár- **Kusto**.  

### <a name="lookup-activity"></a>Keresési tevékenység
 
A keresési tevékenység az Azure Adatkezelő lekérdezések futtatására szolgál. A lekérdezés eredményét a rendszer a keresési tevékenység kimenete adja vissza, és a folyamat következő tevékenységében használhatja az [ADF-keresési dokumentációban](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)leírtaknak megfelelően.  
Az 5 000-es és a 2 MB-os válasz méretén kívül a tevékenységhez a lekérdezés időtúllépési korlátja is 1 óra.

### <a name="command-activity"></a>Parancs tevékenység

A parancs tevékenysége lehetővé teszi az Azure Adatkezelő- [vezérlési parancsok](/azure/kusto/concepts/#control-commands)végrehajtását. A lekérdezésekkel ellentétben a vezérlési parancsok módosíthatják az adatokat vagy a metaadatokat. Egyes vezérlési parancsok célja, hogy az adatok betöltését az Azure Adatkezelőba, parancsok, például `.ingest`vagy `.set-or-append`használatával, vagy az adatok másolása az Adatkezelő Azure-ból a külső adattárakba, például a `.export`parancsokkal.
A parancs tevékenység részletes ismertetését lásd: [Azure Data Factory Command tevékenység használata az Azure adatkezelő-vezérlési parancsok futtatásához](data-factory-command-activity.md).  Az adatmásolásra szolgáló vezérlési parancs használatával időnként a másolási tevékenységnél gyorsabb és olcsóbb lehetőség is lehet. Annak megállapításához, hogy mikor kell használni a parancs tevékenységét és a másolási tevékenységet, tekintse meg az [adatok másolása során a másolás és a parancs tevékenységek közötti választást](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Tömeges másolás adatbázis-sablonból

Az [adatbázisból az Azure-ba adatkezelő a Azure Data Factory sablon használatával](data-factory-template.md) egy előre definiált Azure Data Factory folyamat. A sablon használatával több folyamat hozható létre adatbázisban vagy táblában a gyorsabb adatmásoláshoz. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Válassza ki a másolás és az Azure Adatkezelő parancs tevékenységeit az Adatmásolás során 

Ez a szakasz segít az adatmásolási igényeknek megfelelő tevékenység kiválasztásában.

Az adatok Azure Adatkezelőba való másolásakor a Azure Data Factory két lehetőség közül választhat:
* Másolási tevékenység.
* Az Azure Adatkezelő parancs tevékenysége, amely az Azure Adatkezelőban lévő adatok átvitelére szolgáló vezérlési parancsok egyikét hajtja végre. 

### <a name="copy-data-from-azure-data-explorer"></a>Adatok másolása az Azure Adatkezelőból
  
Az Azure Adatkezelőról a másolási tevékenység vagy a [`.export`](/azure/kusto/management/data-export/) parancs használatával másolhat adatok. A `.export` parancs végrehajt egy lekérdezést, majd exportálja a lekérdezés eredményeit. 

A másolási tevékenység és a `.export` az Adatkezelő Azure-ból történő adatmásolási parancs összehasonlításához tekintse meg a következő táblázatot.

| | Másolási tevékenység | . export parancs |
|---|---|---|
| **Folyamat leírása** | Az ADF végrehajt egy lekérdezést a Kusto, feldolgozza az eredményt, és elküldi azt a célként megadott adattárba. <br>(**ADX > ADF > fogadó adattár**) | Az ADF `.export` vezérlési parancsot küld az Azure Adatkezelőnak, amely végrehajtja a parancsot, és közvetlenül a célként megadott adattárba küldi az adatokat. <br>(**ADX > fogadó adattár**) |
| **Támogatott adattárolók** | A [támogatott adattárak](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) széles választéka | ADLSv2, Azure Blob, SQL Database |
| **Teljesítmény** | Központosított | <ul><li>Elosztott (alapértelmezett), több csomópont adatainak exportálása egyidejűleg</li><li>Gyorsabb és az ELÁBÉ hatékony.</li></ul> |
| **Kiszolgáló korlátai** | A [lekérdezési korlátok](/azure/kusto/concepts/querylimits) bővíthetők vagy letilthatók. Alapértelmezés szerint az ADF-lekérdezések a következőket tartalmazzák: <ul><li>500 000 rekord vagy 64 MB méretű.</li><li>10 perces időkorlát.</li><li>a `noTruncation` false (hamis) értékre van állítva.</li></ul> | Alapértelmezés szerint a lekérdezési korlátok kiterjeszthetők vagy Letiltva: <ul><li>A méretkorlát le van tiltva.</li><li>A kiszolgáló időtúllépése 1 órára van kiterjesztve.</li><li>a `MaxMemoryConsumptionPerIterator` és a `MaxMemoryConsumptionPerQueryPerNode` a Max (5 GB, TotalPhysicalMemory/2) értékre van kiterjesztve.</li></ul>

> [!TIP]
> Ha a másolási célhely az `.export` parancs által támogatott adattárak egyike, és ha a másolási tevékenység egyik funkciója sem elengedhetetlen az igényeinek kielégítéséhez, válassza a `.export` parancsot.

### <a name="copying-data-to-azure-data-explorer"></a>Adatok másolása az Azure Adatkezelőba

Az adatok másolása az Azure Adatkezelő a másolási tevékenység vagy a betöltési parancsok használatával, például a [lekérdezésből](/azure/kusto/management/data-ingestion/ingest-from-query) való betöltéssel (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`és [a tárolóból](/azure/kusto/management/data-ingestion/ingest-from-storage) való betöltéssel (`.ingest`). 

A másolási tevékenység és az adatok Azure Adatkezelőba másolására szolgáló betöltési parancsok összehasonlítását a következő táblázat tartalmazza.

| | Másolási tevékenység | Betöltés a lekérdezésből<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Betöltés a tárolóból <br> `.ingest` |
|---|---|---|---|
| **Folyamat leírása** | Az ADF beolvassa az adatokat a forrás adattárból, táblázatos formátumba konvertálja, és a szükséges séma-hozzárendelési módosításokat végzi. Az ADF ezután feltölti az adatot az Azure-blobokra, felosztja őket, majd letölti a blobokat a ADX táblába való betöltéshez. <br> (**Forrás adattár > ADF > Azure-blobok > ADX**) | Ezek a parancsok futtathatnak egy lekérdezést vagy `.show` parancsot, és betöltik a lekérdezés eredményét egy táblába (**ADX > ADX**). | Ez a parancs az adatok egy táblába való betöltését végzi el egy vagy több felhőalapú tárolási összetevőből származó adatok húzásával. |
| **Támogatott forrás adattárak** |  [különböző lehetőségek](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (a sql_request beépülő modullal), Cosmos (a cosmosdb_sql_request beépülő modullal) és bármely más, HTTP-vagy Python API-t biztosító adattár. | Filesystem, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Teljesítmény** | A betöltések várólistára kerülnek és kezelhetők, ami biztosítja a kis méretű betöltést, és biztosítja a magas rendelkezésre állást a terheléselosztás, az újrapróbálkozások és a hibakezelés biztosításával. | <ul><li>Ezek a parancsok nem a nagy mennyiségű adatimportáláshoz lettek tervezve.</li><li>A vártnál és olcsóbban működik. Az éles környezetek esetében azonban, ha a forgalmi díjak és az adatméretek nagy méretűek, használja a másolási tevékenységet.</li></ul> |
| **Kiszolgáló korlátai** | <ul><li>Nincs méretkorlát.</li><li>Maximális időkorlát: 1 óra/betöltött blob. |<ul><li>A lekérdezési részben csak egy méretkorlát lehet, amely `noTruncation=true`megadásával kihagyható.</li><li>Maximális időtúllépési korlát: 1 óra.</li></ul> | <ul><li>Nincs méretkorlát.</li><li>Maximális időtúllépési korlát: 1 óra.</li></ul>|

> [!TIP]
> * Ha az ADF-ből az Azure-ba másol adatok másolása Adatkezelő használja a `ingest from query` parancsokat.  
> * Nagyméretű adathalmazok esetén (> 1GB) használja a másolási tevékenységet.  

## <a name="required-permissions"></a>Szükséges engedélyek

A következő táblázat felsorolja a Azure Data Factory integrációjának különböző lépéseihez szükséges engedélyeket.

| Lépés: | Művelet | Engedélyek minimális szintje | Megjegyzések |
|---|---|---|---|
| **Társított szolgáltatás létrehozása** | Adatbázis-navigáció | *adatbázis megjelenítője* <br>Az ADF használatával bejelentkezett felhasználó számára engedélyezni kell az adatbázis metaadatainak olvasását. | A felhasználó manuálisan is megadhatja az adatbázis nevét. |
| | Kapcsolat tesztelése | *Adatbázis-figyelő* vagy- *tábla lenyelése* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell az adatbázis-szint `.show` a parancsok vagy a tábla szintjének betöltését. | <ul><li>A TestConnection ellenőrzi a kapcsolódást a fürthöz, és nem az adatbázishoz. Ez akkor is sikeres lehet, ha az adatbázis nem létezik.</li><li>A tábla-rendszergazdai engedélyek nem elegendőek.</li></ul>|
| **Adatkészlet létrehozása** | Táblázatos Navigálás | *Adatbázis-figyelő* <br>Az ADF használatával bejelentkezett felhasználónak engedélyezni kell az adatbázis-szintű `.show` parancsok futtatását. | A felhasználó manuálisan is megadhatja a táblanév nevét.|
| **Adatkészlet** vagy **másolási tevékenység** létrehozása | Előzetes verzió | *adatbázis megjelenítője* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell az adatbázis-metaadatok olvasását. | | 
|   | Séma importálása | *adatbázis megjelenítője* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell az adatbázis-metaadatok olvasását. | Ha a ADX egy táblázatos táblázatos másolás forrása, akkor az ADF automatikusan importálja a sémát, még akkor is, ha a felhasználó nem importál explicit módon sémát. |
| **ADX fogadóként** | Név szerinti oszlop leképezésének létrehozása | *Adatbázis-figyelő* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell az adatbázis-szintű `.show` parancsok futtatását. | <ul><li>Az összes kötelező művelet működni fog a *Table*betöltéssel.</li><li> Néhány választható művelet sikertelen lehet.</li></ul> |
|   | <ul><li>CSV-megfeleltetés létrehozása a táblában</li><li>A leképezés eldobása</li></ul>| *Table* betöltés vagy *adatbázis-rendszergazda* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell a tábla módosítását. | |
|   | Adatok kigyűjtése | *Table* betöltés vagy *adatbázis-rendszergazda* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell a tábla módosítását. | | 
| **ADX forrásként** | Lekérdezés végrehajtása | *adatbázis megjelenítője* <br>Az egyszerű szolgáltatásnév számára engedélyezni kell az adatbázis-metaadatok olvasását. | |
| **Kusto parancs** | | Az egyes parancsok engedélyei szintjének megfelelően. |

## <a name="performance"></a>Teljesítmény 

Ha az Azure Adatkezelő a forrás, és használja a keresési, másolási vagy parancsi tevékenységet, amely egy lekérdezést tartalmaz, a következő témakörben talál információt: a Teljesítményadatok és az ADF dokumentációjának [lekérdezése](/azure/kusto/query/best-practices) a [másolási tevékenységhez](/azure/data-factory/copy-activity-performance).
  
Ez a szakasz a másolási tevékenység használatát tárgyalja, ahol az Azure Adatkezelő a fogadó. Az Azure Adatkezelő-fogadó becsült átviteli sebessége 11-13 MBps. Az alábbi táblázat az Azure Adatkezelő fogadó teljesítményét befolyásoló paramétereket ismerteti.

| Paraméter | Megjegyzések |
|---|---|
| **Összetevők földrajzi közelsége** | Helyezze el az összes összetevőt ugyanabban a régióban:<ul><li>forrás-és fogadó adattárak.</li><li>ADF Integration Runtime.</li><li>A ADX-fürt.</li></ul>Győződjön meg arról, hogy legalább az integrációs modul ugyanabban a régióban található, mint a ADX-fürt. |
| **DIUs száma** | 1 virtuális gép az ADF által használt minden 4 DIUs. <br>A DIUs növelése csak akkor segít, ha a forrás egy fájl alapú, több fájllal rendelkező tároló. Az egyes virtuális gépek ezután párhuzamosan dolgozzák fel egy másik fájlt. Ezért egyetlen nagyméretű fájl másolása nagyobb késéssel jár, mint több kisebb fájl másolása.|
|**A ADX-fürt mennyisége és SKU-je** | A ADX-csomópontok nagy száma növeli a betöltés feldolgozásának idejét.|
| **Párhuzamosság** | Egy adatbázisból nagyon nagy mennyiségű adatok másolásához particionálja az adatait, majd használjon egy ForEach hurkot, amely párhuzamosan másolja az egyes partíciókat, vagy használja a [tömeges másolást az adatbázisból az Azure adatkezelő sablonba](data-factory-template.md). Megjegyzés: a **beállítások** > a másolási tevékenység **párhuzamossági foka** nem vonatkozik a ADX. |
| **Adatfeldolgozás bonyolultsága** | A késés a forrásfájl formátuma, az oszlopok leképezése és a tömörítés alapján változhat.|
| **Az integrációs modult futtató virtuális gép** | <ul><li>Az Azure Copy esetében az ADF-alapú virtuális gépek és a gép SKU-i nem módosíthatók.</li><li> Az Azure-beli másoláshoz ellenőrizze, hogy a saját üzemeltetésű integrációs modult üzemeltető virtuális gép elég erős-e.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tippek és gyakori buktatók

### <a name="monitor-activity-progress"></a>Tevékenységek előrehaladásának figyelése

* A tevékenység előrehaladásának figyelése során az *adatírást* megadó tulajdonság sokkal nagyobb lehet az *adatolvasási* tulajdonságnál, mert a rendszer a bináris fájl mérete alapján számítja ki az *adatolvasást* , míg az *írás* a memóriában tárolt méret szerint történik, az adatmennyiség deszerializálása és kibontása után.

* A tevékenység előrehaladásának monitorozásakor láthatja, hogy az Azure Adatkezelő fogadóba írja az adott információt. Az Azure Adatkezelő tábla lekérdezésekor láthatja, hogy az adatvesztés nem érkezett meg. Ennek az az oka, hogy az Azure Adatkezelőba való másoláskor két fázis van. 
    * Az első szakasz beolvassa a forrásadatokat, feldarabolja azt 900 MB-nyi darabokra, és feltölti az egyes adattömböket egy Azure-Blobba. Az első szakaszt az ADF tevékenység előrehaladási nézete látja. 
    * A második szakasz akkor kezdődik, amikor az összes adattal feltölti az Azure-blobokat. Az Azure Adatkezelő Engine-csomópontok letöltik a blobokat, és betöltik az adatot a fogadó táblába. Az Azure-Adatkezelő táblázatban láthatók az adatai.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Nem sikerült bevenni a CSV-fájlokat a nem megfelelő Escape miatt

Az Azure Adatkezelő CSV-fájlokat vár az [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)-hez való igazításhoz.
A következőket várja:
* Azok a mezők, amelyek Escape-karaktert (például "és új vonalakat") tartalmazó karaktereket tartalmaznak **, egy** karakterrel kell kezdődnie és végződnie, szóköz nélkül. *A mezőben* **lévő összes karakter** megmenekül egy dupla **"** karakter (" **"** ) használatával. Például a _"Hello", "World"_ "" egy érvényes CSV-fájl, amelynek egyetlen rekordja egyetlen oszlop vagy mező a _"World"_ tartalommal.
* A fájl összes rekordjának azonos számú oszlopot és mezőt kell tartalmaznia.

Azure Data Factory lehetővé teszi a fordított perjel (Escape) karakter használatát. Ha Azure Data Factory használatával létrehoz egy fordított perjel karaktert tartalmazó CSV-fájlt, a fájl betöltése az Azure-ba Adatkezelő sikertelen lesz.

#### <a name="example"></a>Példa

A következő szöveges értékek: Hello, "World"<br/>
ABC-DEF<br/>
"ABC\D" EF<br/>
"ABC DEF<br/>

A következőnek kell megjelennie egy megfelelő CSV-fájlban: "Hello", "World" ""<br/>
"ABC DEF"<br/>
"" "ABC DEF"<br/>
"" "ABC\D" "EF"<br/>

Az alapértelmezett escape-karakter (fordított perjel) használatával a következő CSV nem fog működni az Azure Adatkezelő: "Hello, \"World\""<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Beágyazott JSON-objektumok

JSON-fájl Azure Adatkezelőba való másolása során vegye figyelembe a következőket:
* A tömbök nem támogatottak.
* Ha a JSON-struktúra objektum adattípusokat tartalmaz, a Azure Data Factory lelapul az objektum alárendelt elemeit, és az egyes alárendelt elemeket az Azure Adatkezelő-tábla egy másik oszlopához próbálja meg leképezni. Ha azt szeretné, hogy a teljes objektum elem le legyen képezve egyetlen oszlopra az Azure Adatkezelőban:
    * A teljes JSON-sor betöltése egyetlen dinamikus oszlopba az Azure Adatkezelőban.
    * Manuálisan szerkessze a folyamat definícióját Azure Data Factory JSON-szerkesztő használatával. A **leképezésekben**
       * Távolítsa el az egyes alárendelt elemekhez létrehozott több leképezést, és adjon hozzá egyetlen leképezést, amely leképezi az objektum típusát a Table (tábla) oszlophoz.
       * A záró szögletes zárójel után adjon hozzá egy vesszőt, majd a következőt:<br/>
       `"mapComplexValuesToString": true` kérdésre adott válaszban foglalt lépéseket.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>AdditionalProperties megadása az Azure-ba való másoláskor Adatkezelő

> [!NOTE]
> Ez a funkció jelenleg a JSON-adattartalom manuális szerkesztésével érhető el. 

Vegyen fel egyetlen sort a másolási tevékenység "fogadó" szakaszában a következőképpen:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Az érték Escape-értéke trükkös lehet. A következő kódrészletet használja hivatkozásként:

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

A kinyomtatott érték:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [másolhat az Azure Adatkezelőba Azure Data Factory használatával](data-factory-load-data.md).
* További információ az [adatbázisból az adatkezelő Azure-ba való tömeges másoláshoz Azure Data Factory sablon](data-factory-template.md)használatával.
* Ismerje meg, hogyan használható [Azure Data Factory parancs tevékenység az Azure adatkezelő-vezérlési parancsok futtatásához](data-factory-command-activity.md).
* Ismerje meg az Azure Adatkezelő Adatlekérdezési [lekérdezéseit](/azure/data-explorer/web-query-data) .



