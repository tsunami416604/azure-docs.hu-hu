---
title: "Ütemezés és a Data Factory végrehajtási |} Microsoft Docs"
description: "Ismerje meg az Azure Data Factory alkalmazásmodell ütemezés és a végrehajtási aspektusait."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d1cd846418b0e65b978971526af7b918bd03a7a1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory ütemezés és a végrehajtás
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [csővezeték-végrehajtási és eseményindítók](../concepts-pipeline-execution-triggers.md) cikk.

Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. Ez a cikk feltételezi, hogy tudomásul veszi a Data Factory alkalmazás modell fogalmakat, beleértve a tevékenység, a folyamatok, a társított szolgáltatások és a adatkészletek alapjait. Azure Data Factory alapvető fogalmait tekintse meg a következő cikkeket:

* [Adat-előállító bemutatása](data-factory-introduction.md)
* [Folyamatok](data-factory-create-pipelines.md)
* [Adatkészletek](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Feldolgozási folyamat kezdő és befejező időpontja
Egy folyamat aktív csak között a **start** idő és **end** idő. Nem hajtotta végre, a kezdő időpont elé vagy a befejező időpont után. Ha a feldolgozási sor fel van függesztve, nem végre függetlenül a kezdő és záró idő. A futtatásához egy folyamatot azt kell nem lehet szüneteltetni. Ezek a beállítások (kezdő, célból szünetel) található a feldolgozási sor definíciója: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

További információ: ezek a Tulajdonságok [hozzon létre adatcsatornák](data-factory-create-pipelines.md) cikk. 


## <a name="specify-schedule-for-an-activity"></a>Egy tevékenység ütemezése
A folyamat, amely végrehajtja a rendszer nincs. A tevékenységek, amelyek a feldolgozási sor általános összefüggésben folyamatban. Segítségével egy tevékenység ismétlődő ütemezés szerint megadhatja a **Feladatütemező** tevékenység JSON szakasza. Például ütemezheti az óránkénti futásra következő tevékenységet:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Az alábbi ábrán is látható, mert annak ütemezését, hogy egy tevékenység létrehoz több windows átfedésmentes a folyamat elindítása és befejezési időpontja. Átfedésmentes windows rendszer rögzített méretű mozaikként, átfedés nélkül, összefüggő időközök sorozata. A logikai átfedésmentes egy tevékenység hívjuk **tevékenység windows**.

![Tevékenység Feladatütemező – példa](media/data-factory-scheduling-and-execution/scheduler-example.png)

A **Feladatütemező** tevékenység tulajdonsága nem kötelező. Ha megadja ezt a tulajdonságot, akkor meg kell egyeznie a ütemben történik meg a kimeneti adatkészlet a tevékenység-definíció adható meg. Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Ezért egy kimeneti adatkészlet kell létrehoznia, még akkor is, ha a tevékenység nem ad kimenetet. 

## <a name="specify-schedule-for-a-dataset"></a>Adja meg a DataSet adatkészlet ütemezését
A Data Factory-folyamat egy tevékenységének is igénybe vehet a nulla vagy több bemeneti **adatkészletek** , majd előállítanak egy vagy több kimeneti adatkészletek. A tevékenység, a ütemben történik, amelyen a bemeneti adatok érhető el, vagy a kimeneti adatok hozzák használó adhat meg a **rendelkezésre állási** szakasz az adatkészlet-definíciókban. 

**Gyakoriság** a a **rendelkezésre állási** szakasz megadja időegységét. A gyakoriság engedélyezett értékek a következők: perc, óra, nap, hét és hónap. A **időköz** rendelkezésre állással kapcsolatos szakaszának a tulajdonság határozza meg a gyakoriság egy szorzóval. Példa: Ha a gyakoriság napra van beállítva, és időköz egy kimeneti adatkészlet 1 értékre van állítva, a kimeneti adatok naponta jön létre. Ha gyakoriságát percben, azt javasoljuk, hogy beállította az intervallum nem lehet kisebb, mint 15. 

A következő példában a bemeneti érhetők el adatok óránkénti és a kimeneti adatok rendszer óránként készít adatszeletet (`"frequency": "Hour", "interval": 1`). 

**Bemeneti adatkészletet:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Kimeneti adatkészlet**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Jelenleg **kimeneti adatkészlet meghajtók az ütemezés**. Más szóval a megadott kimeneti adatkészlet ütemezése futásidőben tevékenység futtatásához használt. Ezért egy kimeneti adatkészlet kell létrehoznia, még akkor is, ha a tevékenység nem ad kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. 

A következő adatcsatorna-definícióban a **Feladatütemező** tulajdonság használatával adja meg a tevékenység ütemezését. Ez a tulajdonság nem kötelező. Az ütemezés a tevékenység jelenleg, meg kell egyeznie a megadott kimeneti adatkészlet ütemezése.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Ebben a példában a tevékenység fut, a kezdő és befejező időpontja a feldolgozási folyamat között óránkénti. A kimeneti adatok óránkénti állítanak elő háromórás windows (8 de - de, Reggel 9-10 Reggel 9, és 10 de - de). 

Felhasznált vagy egy futtatása tevékenység által létrehozott adatok tárolóegységekhez nevezik egy **adatszelet**. Az alábbi ábrán látható egy példa egy bemeneti adatkészlet a tevékenység, és egy kimeneti adatkészletet: 

![Rendelkezésre állási Feladatütemező](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Az ábrán látható a bemeneti és kimeneti adatkészlet óránkénti adatszeletek. Az ábrán látható, amely készen áll a feldolgozás három bemeneti szeletek. A 10-11 AM tevékenység van folyamatban, a 10-11 AM kimeneti szeletet előállító. 

A alatt az időtartam alatt a jelenlegi szelet az adatkészlet JSON társított változók használatával végezheti el: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) és [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Hasonló módon érheti el a WindowStart és WindowEnd segítségével egy tevékenység ablakban társított időtartam alatt. Az ütemezés tevékenység meg kell egyeznie a kimeneti adatkészlet a tevékenység ütemezését. Ezért a SliceStart és a SliceEnd értékek megegyeznek WindowStart és WindowEnd értékek kulcsattribútumokkal. Ezek a változók további információkért lásd: [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables) cikkeket.  

Ezek a változók többféle célra használhatja a a tevékenység JSON-NÁ. Például használhatja őket adatok kiválasztásához a bemeneti és kimeneti adatkészletek adatsorozat időadatok képviselő (például: 8 óra a Reggel 9). Ez a példa **WindowStart** és **WindowEnd** jelölje be a megfelelő adatokat egy tevékenységhez futtatni, és másolja a megfelelő blob **folderPath**. A **folderPath** külön mappába kaphatnak minden órában a paraméteres van.  

A fenti példában az ütemezés a megadott bemeneti és kimeneti adatkészletek azonos (óránként). Ha a tevékenység bemeneti adatkészlet érhető el egy másik gyakorisággal mondja ki 15 percenként, a tevékenység, amely létrehozza a kimeneti adatkészletet továbbra is fut óránként, a kimeneti adatkészlet működik a tevékenység ütemezés szerint. További információkért lásd: [eltérő gyakorisággal adatkészletek modell](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Adatkészlet rendelkezésre állását és házirendek
Amint láthatta gyakoriságát és a rendelkezésre állással kapcsolatos szakaszának adatkészlet-definícióban időköz tulajdonságok használatát. Néhány más tulajdonságok, amelyek hatással vannak az ütemezés és tevékenység végrehajtási vannak. 

### <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása 
A következő táblázat ismerteti a használható tulajdonságok a **rendelkezésre állási** szakasz:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja a dataset szelet üzemi időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja egy szorzóval gyakoriság<br/><br/>"X időköz" határozza meg, milyen gyakran a szelet jön létre.<br/><br/>Ha módosítania kell a adatkészlet kell szeletelhetők óránként, beállíthatja <b>gyakorisága</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/><b>Megjegyzés:</b>: perces gyakoriságot ad meg, ha azt javasoljuk, hogy beállította az intervallum nem lehet kisebb, mint 15 |Igen |NA |
| stílus |Meghatározza, hogy a szelet akkor a rendszer az időköz kezdő/végén.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha gyakoriságának beállítása hónapot és stílus EndOfInterval beállítása, a szelet hónap utolsó napján elő. Ha a stílus StartOfInterval van megadva, a szelet hónap első napján elő.<br/><br/>Ha nap gyakoriságának beállítása és stílus EndOfInterval beállítása, a szelet elő az elmúlt órában a nap.<br/><br/>Ha gyakoriságának beállítása óra és stílus EndOfInterval beállítása, a szelet elő az órát végén. Például egy adatszelethez du. 1 – 2 óra időszakban, a szelet hozzák 2 du. |Nem |EndOfInterval |
| anchorDateTime |Határozza meg az idő az ütemező által használt adatkészlet szelet határok számítási abszolút pozíciója. <br/><br/><b>Megjegyzés:</b>: Ha a AnchorDateTime részekből dátum, amelyek részletesebben, mint a gyakorisága, akkor a részletesebb részek figyelmen kívül lesznek hagyva. <br/><br/>Például ha a <b>időköz</b> van <b>óránkénti</b> (gyakoriság: óra és időköz: 1) és a <b>AnchorDateTime</b> tartalmazza <b>percet és másodpercet</b>, akkor a <b>percet és másodpercet</b> a AnchorDateTime részei a rendszer figyelmen kívül hagyja. |Nem |01/01/0001 |
| Az offset |TimeSpan érték, amely a kezdő és a záró összes adatkészlet szeletek vette. <br/><br/><b>Megjegyzés:</b>: Ha anchorDateTime és az offset is meg van adva, az eredmény a kombinált shift-e. |Nem |NA |

### <a name="offset-example"></a>az eltolási – példa
Alapértelmezés szerint naponta (`"frequency": "Day", "interval": 1`) szeletek kezdjék 12 óra UTC idő szerint (éjfél). Ha azt szeretné, hogy a kezdési idő reggel 6 óra UTC idő helyette, állítsa be az eltolás látható módon a következő kódrészletet: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime – példa
A következő példában a dataset 23 óránként jön létre. A anchorDateTime, melynek értéke által meghatározott időpontban elindítja az első szelet `2017-04-19T08:00:00` (UTC idő).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Az offset/stílus – példa
A következő adatkészlet egy havi adatkészlet és a 3. minden hónap 8:00 órakor hozzák (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>A DataSet házirend
Egy adatkészlet tartozhat egy meghatározott érvényesség-ellenőrzési házirend, amely meghatározza, hogyan olyan szelet végrehajtással által létrehozott adatokat is ellenőrizni kell, mielőtt azt készen áll a felhasználásra. Ilyen esetben a szelet végrehajtás befejezését követően a kimeneti szelet állapota **Várakozás** rendelkező, a részállapot **érvényesítési**. A szeletek ellenőrzését követően a szelet állapota **készen**. Ha egy adatszelet készült, de nem felelt meg az érvényesítési, alsóbb rétegbeli szeletek a szelet függő tevékenység fut nincsenek feldolgozva. [Megfigyelés és kezelés folyamatok](data-factory-monitor-manage-pipelines.md) ismerteti a különböző állapotok adat-előállítóban adatszeletek.

A **házirend** az adatkészlet-definícióban szakaszban határozza meg, a feltételek vagy a feltétellel, hogy a dataset szeletek teljesítenie kell. A következő táblázat ismerteti a használható tulajdonságok a **házirend** szakasz:

| Házirend neve | Leírás | Vonatkozik. | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Azt ellenőrzi, hogy az adatokat egy **Azure blob** megfelel a minimális méretét (megabájtban). |Azure-blob |Nem |NA |
| minimumRows | Azt ellenőrzi, hogy az adatokat egy **Azure SQL adatbázis** vagy egy **Azure-tábla** a sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

#### <a name="examples"></a>Példák
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Ezek a tulajdonságok és példák kapcsolatos további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. 

## <a name="activity-policies"></a>A tevékenység-szabályzatok
A házirendek milyen hatással a futtatási viselkedés tevékenység, kifejezetten a szelet egy tábla feldolgozásakor. A következő táblázat a részletesen.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűségi |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység egyidejű végrehajtások száma.<br/><br/>Meghatározza, hogy a párhuzamos tevékenység végrehajtások, amely akkor fordulhat elő, a másik szeletek számát. Például ha egy tevékenység kell végighaladnia rendelkezésre álló adatok, nagyobb feldolgozási értéke számos felgyorsítja az adatok feldolgozása. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy feldolgozott adatszeletek sorrendje.<br/><br/>Például ha 2 szeletek (du. 4: egy azonban és délután 5 óra egy másik tulajdonságnak), és mindkét végrehajtási függőben van. Ha a executionPriorityOrder NewestFirst kell, a szelet, délután 5 óra lesz elsőként feldolgozva. Hasonlóképpen ha OldestFIrst kell executionPriorityORder, majd a szelet du. 4: dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 is lehet. |0 |Az adatok feldolgozása a szeletre vonatkozó hiba van megjelölve, mielőtt újrapróbálkozások száma. Egy adatszelet tevékenység végrehajtása a rendszer ismét megkísérli legfeljebb a megadott újrapróbálkozások maximális számát. Az újra gombra a lehető leghamarabb a meghibásodás után történik. |
| timeout |A TimeSpan |00:00:00 |A tevékenység időkorlátja. . Példa: 00:10:00 (azt jelenti, időtúllépés 10 perc)<br/><br/>Ha az érték nincs megadva vagy 0, az időtúllépési érték végtelen.<br/><br/>A szelet adatok feldolgozási ideje meghaladja a időtúllépési értéket, ha azt megszakadt, és a rendszer megkísérli újra feldolgozását. Az újrapróbálkozások száma attól függ, hogy az újra gombra tulajdonság. Időtúllépés történik, ha a beállítás időtúllépésbe került. |
| Késleltetés |A TimeSpan |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Egy adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási ideje elmúlt elindult.<br/><br/>. Példa: 00:10:00 (magában foglalja a késleltetést a 10 perc) |
| hosszú újrapróbálkozás |Egész szám<br/><br/>A maximális érték: 10 |1 |Sikertelen volt a szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>hosszú újrapróbálkozás kísérletek által longRetryInterval távolságban helyezkednek el. Ha meg kell adnia egy újrapróbálkozási kísérletek között eltelt idő, így hosszú újrapróbálkozás használja. Ha mind az újra gombra, és a hosszú újrapróbálkozás meg van adva, minden hosszú újrapróbálkozás kísérlet tartalmazza újrapróbálkozások és kísérletek maximális számát. Próbálkozzon újra * hosszú újrapróbálkozás.<br/><br/>Ha például a tevékenység-házirend van a következő beállításokat:<br/>Próbálkozzon újra: 3<br/>hosszú újrapróbálkozás: 2. régiója<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, nincs végrehajtandó csak egy szelet (állapot vár) és a tevékenység végrehajtása meghiúsul minden alkalommal. Eredetileg nem lenne 3 egymást követő végrehajtási kísérletek. A szelet állapota minden kísérlet után újra lehet. Miután először 3 kísérletet keresztül történik, a szelet állapota hosszú újrapróbálkozás lehet.<br/><br/>Egy óra (Ez azt jelenti, hogy longRetryInteval tartozó érték) nem lenne a 3 egymást követő végrehajtási kísérletek egy másik készlet. Ezt követően a szelet állapota akkor sikertelen, és nincs további újrapróbálkozások volna kísérli meg a. Ezért a teljes 6 történt kísérlet.<br/><br/>Ha bármely végrehajtása sikeres, a szelet állapota Kész és nincs további újrapróbálkozások próbált vannak.<br/><br/>hosszú újrapróbálkozás függő adatok nem determinisztikus időpontokban érkeznek vagy flaky akkor következik be, mely az adatfeldolgozás alatt a környezetben használható. Ilyen esetekben újrapróbálkozások egymás után nem segíthet ezzel, és ezzel egy időszak után időt a kívánt kimeneti eredményez.<br/><br/>Járjon el a Word: nincs beállítva hosszú újrapróbálkozás vagy longRetryInterval magas értékeit. Általában a magasabb értékkel rendszeres problémákkal utalnak. |
| longRetryInterval |A TimeSpan |00:00:00 |Hosszú újrapróbálkozások közötti késleltetés |

További információkért lásd: [folyamatok](data-factory-create-pipelines.md) cikk. 

## <a name="parallel-processing-of-data-slices"></a>Párhuzamos feldolgozás adatszeletek
A feldolgozási sor kezdő dátuma a múltban állíthatja be. Ha így tesz, a Data Factory automatikusan kiszámítja a (hátsó kitöltés) összes adatszeletek a múltban, és elkezdi őket. Példa: Ha kezdő dátum 2017-04-01 hoz létre egy folyamatot, és az aktuális dátum 2017-04-10. Ha a ütemben történik a kimeneti adatkészlet naponta, majd a Data Factory indítása összes a szeletet 2017-04-01-től 2017-04-09 feldolgozása azonnal, mert a kezdő dátuma a múltban van. A szelet 2017-04-10-nem lett feldolgozva még, mert a rendelkezésre állással kapcsolatos szakaszának style tulajdonságának értéke EndOfInterval alapértelmezés szerint. A legrégebbi szelet feldolgozása először alapértelmezett executionPriorityOrder értéke OldestFirst. A style tulajdonságának ismertetését lásd: [adatkészlet rendelkezésre állási](#dataset-availability) szakasz. A executionPriorityOrder szakasz, olvassa el a [tevékenység szabályzatai](#activity-policies) szakasz. 

Konfigurálhat biztonsági kitöltött adatszeletek beállításával párhuzamos feldolgozásra a **egyidejűségi** tulajdonságot a **házirend** szakasz a tevékenység JSON. Ez a tulajdonság, amely akkor fordulhat elő, a másik szeletek párhuzamos tevékenység végrehajtások számát határozza meg. A feldolgozási tulajdonság alapértelmezett értéke 1. Ezért egy szelet feldolgozása egyszerre alapértelmezés szerint. A maximális értéke 10. Amikor egy folyamatot kell halad át a rendelkezésre álló adatok, nagyobb feldolgozási értéke számos felgyorsítja az adatok feldolgozása. 

## <a name="rerun-a-failed-data-slice"></a>Futtassa újra a sikertelen adatszelet
Adatok szelet feldolgozása során hiba lép fel, ha azt megtudhatja, miért nem sikerült a szelet feldolgozása az Azure portál paneleken vagy a figyelő és App kezelése. Lásd: [figyelése és kezelése az Azure portál paneleken használatával folyamatok](data-factory-monitor-manage-pipelines.md) vagy [figyelés és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md) részleteiről.

Vegye figyelembe a következő példának, amely két tevékenység jeleníti meg. Activity1 és 2 tevékenység. Activity1 Dataset1 szelet használ fel, és Dataset2, amely runbook az Activity2 végső DataSet szelet létrehozásához által felhasznált bemeneti adatokként a szelet eredményez.

![Hibás szeletet](./media/data-factory-scheduling-and-execution/failed-slice.png)

Az ábra azt mutatja, hogy kívül három legutóbbi szeletek, a 9-10 de szeletet előállító a Dataset2 hiba történt. Adat-előállító automatikusan nyomon követi a függőséget az az idő adatsorozat adatkészletet. Emiatt nem indul el a Reggel 9-10 alárendelt szeletre vonatkozó tevékenységfuttatási.

Adatok gyári figyelése és a felügyeleti eszközök lehetővé teszik a diagnosztikai naplók könnyen található okozza a problémát, majd hárítsa el a sikertelen szelet elemezze. A probléma kijavítása után könnyen megkezdheti a tevékenységfuttatási a hibás szeletet létrehozásához. Futtassa újra a, és állapotváltozási adat áramlik az adatok megismeréséhez kapcsolatos további információkért lásd: [figyelése és kezelése az Azure portál paneleken használatával folyamatok](data-factory-monitor-manage-pipelines.md) vagy [figyelés és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md).

Után futtassa újra a a 9-10 de újrapróbálása **Dataset2**, adat-előállító elindítja a futtatáskor a 9-10 de függő szeletre vonatkozó végső adatkészlet.

![Futtassa újra a sikertelen szelet](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Egy folyamathoz azonban több tevékenység is tartozhat. Ha több tevékenység rendelkezik egy folyamatot, és a tevékenység kimenete nem bemenet egy másik tevékenység, a tevékenységek párhuzamos futtathatja, ha készen áll a tevékenységekhez tartozó bemeneti adatszeletek.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. A tevékenységek azonos vagy különböző folyamatok lehet. A második tevékenység hajtja végre, csak ha az első címtárra futtatása sikeresen befejeződött.

Vegyük példaként a következő eset, ha a folyamat két tevékenység rendelkezik:

1. Külső bemeneti adatkészlet D1, és az előállított kimeneti adatkészlet D2 igénylő tevékenységek A1.
2. A kimeneti adatkészlet D3 tevékenység A2 D2 adatkészletből beavatkozást igényel, és hozza létre.

Ebben a forgatókönyvben tevékenységek A1 és A2 azonos szerepelnek. A tevékenység a A1 fut, amikor a külső adatokat érhető el, és elérte az ütemezett rendelkezésre állása gyakoriságát. A tevékenység a A2 fut, amikor a D2 ütemezett szeletek rendelkezésre állására, és elérte az ütemezett rendelkezésre állása gyakoriságát. Ha nincs hiba fordult elő egy adatkészlet D2 szeletek, A2 nem fut a, hogy a szelet amíg elérhetővé válik.

Az alábbi ábra a Diagram nézet azonos mindkét tevékenységeinek jelenne meg:

![Láncolás az azonos adatcsatorna tevékenységeinek](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Amint azt korábban említettük, a tevékenységek a különböző folyamatok lehet. Ilyen esetben a diagram nézetben az alábbi ábra jelenne meg:

![Két kimenetátirányítási titkosításblokkoló tevékenységek](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Tekintse meg a [egymás után másolja](#copy-sequentially) szakasz egy példát a függelékben.

## <a name="model-datasets-with-different-frequencies"></a>Eltérő gyakorisággal modell adatkészletek
A minta bemeneti és kimeneti adatkészletek és a tevékenység ütemezési ablak a gyakoriságot azonos volt. Bizonyos esetekben van szükség, hogy egy vagy több bemeneti gyakoriságát eltérő gyakorisággal eredménye. Adat-előállító támogatja a modellezési forgatókönyvekben.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>1. példa: A bemeneti adatok óránként napi kimeneti jelentést készít.
Fontolja meg egy olyan forgatókönyvet, amelyben van megadott mérési adatokat az érzékelők érhető el az Azure Blob storage óránként. A statisztikákról, például átlag, maximális és minimális napi összesített jelentést készít a napon szeretné [adat-előállító hive tevékenység](data-factory-hive-activity.md).

Hogyan modellezhető ebben a forgatókönyvben a Data Factory itt található:

**Bemeneti adatkészlet**

Az óránkénti bemeneti fájlok dobja a mappában található a megadott napon. Bemeneti rendelkezésre állásra van beállítva **óra** (gyakoriság: óra, időköz: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kimeneti adatkészlet**

Egy kimeneti fájl a napi mappában jön létre naponta. Kimeneti rendelkezésre állását nem állítják **nap** (gyakoriság: nap és időköz: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Tevékenység: struktúra egy feldolgozási soros tevékenység**

A hive-parancsfájl megkapja a megfelelő *DateTime* paramétereket, amelyek adatként a **WindowStart** változó, ahogy az az alábbi kódrészletet. A hive-parancsfájl Ez a változó segítségével az adatok betöltése a megfelelő mappából a nap, és futtassa az összesítés a kimenet előállításához.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Az alábbi ábrán a forgatókönyv a-függőség szempontjából.

![Függőség](./media/data-factory-scheduling-and-execution/data-dependency.png)

A kimeneti szelet esetén minden nap 24 óránként szeletek a egy bemeneti adatkészlet függ. Adat-előállító ezeket a függőségeket automatikusan kiszámítja a bemeneti adatok böngészésére idő alatt, akkor a rendszer a kimeneti szelet eső szeleteket. A 24 bemeneti szeletek bármelyike nem érhető el, ha a Data Factory vár a bemeneti szelet készen álljon a napi tevékenységfuttatási megkezdése előtt.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>2. példa: Adja meg a függőségi kifejezések és a Data Factory-funkciók
Mérlegeljük, egy másik helyzet. Tegyük fel, a hive tevékenység, amely feldolgozza a két bemeneti adatkészletek. Egyik új adatokat naponta, de egyikük kap új adatokat minden héten. Tegyük a csatlakozzon a két bemenet közötti és előállít egy kimeneti minden nap.

Az egyszerű módszer, mely adat-előállítóban automatikusan adatokat, a jobb oldali bemeneti szeletek feldolgozását igazítani a kimeneti adatok szelet időszak nem működik.

Meg kell adnia, hogy minden tevékenység futtatásához, a Data Factory érdemes használni a múlt héten adatszelet heti bemeneti adatkészlet. Segítségével szeretne Azure Data Factory látható módon a következő kódrészletet a viselkedés.

**Input1: Az Azure blob**

Az első bemeneti érték naponta frissíti az Azure-blobot.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Az Azure blob**

Input2 az Azure-blobot hetente frissítése folyamatban.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Kimenete: Az Azure blob**

Egy kimeneti fájl napjára létrejön a mappa minden nap. Kimeneti rendelkezésre állását értéke **nap** (gyakoriság: nap, időköz: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Tevékenység: struktúra egy feldolgozási soros tevékenység**

A hive időt vesz igénybe a két bemenet és egy kimeneti szeletet előállító minden nap. Minden nap kimeneti szelet az előző hét bemeneti szelet heti bemeneti függnek az alábbiak szerint adhatja meg.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Lásd: [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md) funkciók és a Data Factory támogató rendszerváltozók listáját.

## <a name="appendix"></a>Függelék:

### <a name="example-copy-sequentially"></a>Példa: egymás után másolása
Akkor is futtathatók több másolási műveletek egymás után szekvenciális/rendezett módon. Például lehetséges, hogy két másolási tevékenység egy folyamat (CopyActivity1 és CopyActivity2) a következő bemeneti adatok kimeneti adatkészletekkel:   

CopyActivity1

Bemenet: Dataset. Kimenet: Dataset2.

CopyActivity2

Bemenet: Dataset2.  Kimenet: Dataset3.

CopyActivity2 fog futni, ha a CopyActivity1 végrehajtása sikeresen befejeződött, és Dataset2 érhető el.

Ez a minta adatcsatorna JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Figyelje meg, hogy a példában a kimeneti adatkészlet első másolási tevékenység (Dataset2) van megadva a második tevékenység bemeneti adatként. Ezért a második tevékenység fut, csak akkor, amikor készen áll a kimeneti adatkészlet első tevékenységtől származnak.  

A példában CopyActivity2 egy másik bemeneti Dataset3, például rendelkezhet, de megadott Dataset2 CopyActivity2, bemenetként, a tevékenység nem futtatja a CopyActivity1 befejezéséig. Példa:

CopyActivity1

Bemenet: Dataset1. Kimenet: Dataset2.

CopyActivity2

Bemenetek: Dataset3, Dataset2. Kimenet: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Figyelje meg, hogy a példában két bemeneti adatkészletek vannak megadva a második másolási tevékenységhez. Ha több bemeneti adatok meg vannak adva, csak az első bemeneti adatkészletet szolgál az adatok másolásának, de más adatkészletek függőségek használatosak. CopyActivity2 szeretné elindítani, csak azt követően a következő feltételek teljesülnek:

* CopyActivity1 sikeresen befejeződött, és Dataset2 érhető el. Adat másolása az Dataset4 ezt az adathalmazt nem használja. Csak működik ütemezési függőségei a CopyActivity2.   
* Dataset3 érhető el. Ehhez az adatkészlethez az adatokat, amelyek a célra másolódik jelöli. 