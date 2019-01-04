---
title: Ütemezés és végrehajtás a Data Factoryvel |} A Microsoft Docs
description: Ismerje meg az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a70c3ddb624639411dbee961b1c4d59ac1277147
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016086"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory ütemezés és végrehajtás
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [folyamat-végrehajtás és eseményindítók](../concepts-pipeline-execution-triggers.md) cikk.

Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. Ez a cikk azt feltételezi, hogy ismeri az adat-előállító alkalmazás modell fogalommal, mint például a tevékenység, a folyamatok, társított szolgáltatásokat és adatkészleteket alapjait. Az Azure Data Factory alapvető fogalmait tekintse meg a következő cikkeket:

* [Data Factory bemutatása](data-factory-introduction.md)
* [Folyamatok](data-factory-create-pipelines.md)
* [Adatkészletek](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Folyamat kezdési és befejezési idejének
Folyamat aktív csak között a **start** idő és **záró** idő. Nem hajtotta végre, a kezdési időpont előtt vagy után a Befejezés időpontja. Ha a folyamat szüneteltetve van, nem végrehajtása attól függetlenül, a kezdési és befejezési idő. A folyamat futtatásához akkor kell szüneteltethető. Ezek a beállítások (indítás, Befejezés, szünetel) található a folyamat definíciója: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

További információ: ezek a Tulajdonságok [folyamatokat hozhat létre](data-factory-create-pipelines.md) cikk. 


## <a name="specify-schedule-for-an-activity"></a>Adja meg egy tevékenység ütemezését
Már nem a végrehajtott folyamat. A folyamatban lévő teljes a környezetben, a folyamat végrehajtása tevékenység. Egy tevékenység ismétlődő ütemezés szerint megadhatja az a **scheduler** tevékenység JSON szakaszában. Például egy tevékenység, amely a következő óránként futtat ütemezhet:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Az alábbi ábrán látható, adjon meg egy ütemezést egy tevékenység létrehoz több windows átfedésmentes az a folyamat kezdési és befejezési időpontja. Az átfedésmentes ablakok rögzített méretű mozaikként, átfedés, összefüggő időintervallumok egymást. Ezek logikai az átfedésmentes ablakok egy tevékenység nevezzük **tevékenységablakok**.

![Tevékenység ütemező példa](media/data-factory-scheduling-and-execution/scheduler-example.png)

A **scheduler** egy tevékenység tulajdonság nem kötelező. Ez a tulajdonság adja meg, ha azt meg kell egyeznie a tevékenység kimeneti adatkészlet definíciójában megadott kiadása ütemben történik. Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Ezért kell létrehoznia egy kimeneti adatkészletet akkor is, ha a tevékenység nem állít elő semmilyen kimenetet. 

## <a name="specify-schedule-for-a-dataset"></a>Adja meg az ütemezés egy adatkészlethez.
A Data Factory-folyamat egy tevékenységet is igénybe vehet a nulla vagy több bemeneti **adatkészletek** , és egy vagy több kimeneti adatkészletet. Egy tevékenység kiadása ütemben történik, amikor a bemeneti adatok érhető el, vagy a kimeneti adatokat használó által előállított megadhatja a **rendelkezésre állási** szakasz az adatkészlet-definíciókban. 

**Gyakoriság** a a **rendelkezésre állási** szakasz meghatározza a időegység. A gyakoriság megengedett értékei a következők: Perc, óra, nap, heti és havi. A **időköz** tulajdonság a rendelkezésre állási szakaszban adja meg a gyakoriság egy szorzóval. Példa: Ha a időköz értéke 1, a kimeneti adatkészletek gyakoriságát napi értékre van állítva, a kimeneti adatokat naponta jön létre. Perc, a gyakoriságot adja meg, azt javasoljuk, hogy az intervallum nem lehet kisebb, mint 15-re állítsa be. 

A következő példában a bemeneti adatok érhető el Óránként, és a kimeneti adatokat a rendszer létrehoz egy adatszeletet (`"frequency": "Hour", "interval": 1`). 

**Bemeneti adatkészlet:** 

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

Jelenleg **kimeneti adatkészlet határozza az ütemezés**. Más szóval a kimeneti adatkészlet megadott ütemezés egy tevékenység futásidőben futtatásához használt. Ezért kell létrehoznia egy kimeneti adatkészletet akkor is, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. 

A következő folyamat definíciójában a **scheduler** tulajdonság segítségével adja meg a tevékenység ütemezését. Ez a tulajdonság nem kötelező. A tevékenység ütemezését jelenleg a kimeneti adatkészlet megadott ütemezés egyeznie kell.
 
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

Ebben a példában a tevékenység fut, a folyamat kezdési és befejezési idejének között óránként. A kimeneti adatokat van létrehoz egy adatszeletet háromórás Windows (8-kor – 9 kor, 9: 00 - 10-kor, és 10 óra – 11: 00). 

Felhasznált vagy futtatása tevékenység által előállított adatok minden egysége nevezzük egy **adatszelet**. Az alábbi ábrán látható egy példa egy bemeneti adatkészlet tevékenységet, és a egy kimeneti adatkészletet: 

![Rendelkezésre állási ütemező](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Az ábrán látható, a bemeneti és kimeneti adatkészlet óránkénti adatszelet. Az ábrán látható három a bemeneti szeletek, amelyek készen állnak a feldolgozásra. A 10-11 Órakor tevékenység van folyamatban, a 10-11 Órakor kimeneti szelet előállítása. 

Az időintervallum, az aktuális szelet az adatkészlet JSON társított változók használatával érhető el: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) és [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Ehhez hasonlóan az időintervallum tartozó adatszeleteket WindowStart és WindowEnd segítségével érheti el. Az ütemezés egy tevékenység meg kell egyeznie az ütemezését, a tevékenység kimeneti adatkészlete. Ezért a SliceStart és a SliceEnd értékei ugyanazok, mint WindowStart és WindowEnd értékeket jelölik. Ezeket a változókat a további információkért lásd: [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables) cikkeket.  

A tevékenység JSON különböző célokra lehessen felhasználni ezeket a változókat. Például használhatja őket, jelölje ki az adatokat a bemeneti és kimeneti adatkészleteket jelölő idősorozat-adatok (például: 8 VAGYOK a 9-kor). Ebben a példában is használt **WindowStart** és **WindowEnd** válassza ki a megfelelő adatokat egy tevékenységhez futtassa, és másolja a megfelelő blob **folderPath**. A **folderPath** szeretné, hogy egy külön mappát minden óra paraméteres van.  

Az előző példában az ütemezés a megadott bemeneti és kimeneti adatkészleteket az azonos (óránként). A tevékenység a bemeneti adatkészlet érhető el a különböző gyakorisággal, például: 15 percenként, ha a tevékenység, amely a kimeneti adatkészletet hozza létre továbbra is fut óránként egyszer, mert a kimeneti adatkészlet vezérli az tevékenység ütemezést. További információkért lásd: [modellezheti az adatkészletek eltérő gyakorisággal](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Adatkészlet rendelkezésre állás és a szabályzatok
Amint láthatta gyakoriságát és az adatkészlet-definícióban rendelkezésre állási szakaszának időköz tulajdonságok használatát. Nincsenek néhány más tulajdonságok, amelyek befolyásolják, hogy az ütemezés és a tevékenység. 

### <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása 
A következő táblázat ismerteti a használható tulajdonságok a **rendelkezésre állási** szakaszban:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet szelet éles üzemi környezetek részei.<br/><br/><b>Támogatott gyakoriság</b>: Perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja egy szorzóval gyakoriság<br/><br/>"X időköz" határozza meg, hogy milyen gyakran a szelet előállítása.<br/><br/>Ha az adatkészlet óradíjat kell szeletelt van szüksége, akkor be <b>gyakorisága</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/><b>Megjegyzés</b>: Perces gyakoriságot ad meg, ha azt javasoljuk, hogy legalább 15-re állítsa be az időköz |Igen |NA |
| stílus |Itt adhatja meg, hogy a szelet intervallum kezdő/záró lehet termelni.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha a gyakoriság értéke hónap és EndOfInterval van beállítva, a szelet előállítása a hónap utolsó napján. Ha a stílus StartOfInterval értékre van állítva, a szelet előállítása a hónap első napján.<br/><br/>Gyakoriságát napi értékre van állítva, és EndOfInterval van beállítva, ha a szelet előállítása a nap az elmúlt órában.<br/><br/>Ha a stílus beállítása EndOfInterval Frequency értéke Hour, a szelet előállítása a óra végén. Például egy szelet du. 1 – 2 PM időszakban, a rendszer óránként létrehoz egy 2-kor. |Nem |EndOfInterval |
| anchorDateTime |Az ütemező által használt adatkészlet szelet határok számítási idő abszolút pozícióját határozza meg. <br/><br/><b>Megjegyzés</b>: Ha a AnchorDateTime dátum részei, amelyek részletesebben, mint a gyakorisága, majd a rendszer figyelmen kívül hagyja a részletesebb részeket. <br/><br/>Például ha a <b>időköz</b> van <b>óránként</b> (frequency: hour és interval: 1.) és a <b>AnchorDateTime</b> tartalmaz <b>perceket és másodperceket</b>, akkor a <b>perceket és másodperceket</b> a AnchorDateTime részei a rendszer figyelmen kívül hagyja. |Nem |01/01/0001 |
| offset |Időtartam, amely szerint a kezdő és befejező az összes adatkészlet szeleteit áttért. <br/><br/><b>Megjegyzés</b>: Ha anchorDateTime és az offset is meg van adva, a kombinált shift jön létre. |Nem |NA |

### <a name="offset-example"></a>a példában eltolása
Alapértelmezetten naponta (`"frequency": "Day", "interval": 1`) a szeletek 12 AM (éjfél) (UTC) időpontban el. Ha azt szeretné, reggel 6 óra UTC idő helyett lennie a kezdési időpont, állítsa be az eltolás az alábbi kódrészletben látható módon: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime példa
A következő példában az adatkészlet 23 óránként jön létre. Az első szelet elindítja a anchorDateTime, amelynek értéke által meghatározott időben `2017-04-19T08:00:00` (UTC idő).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Példa offset/stílusa
A következő adatkészlet egy havi adatkészlet és a 3. – 8:00 órakor havonta jön létre (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Adatkészlet-házirend
Egy adatkészlet tartozhat egy meghatározott érvényesség-ellenőrzési szabályzat, amely meghatározza, hogyan egy szelet végrehajtását által létrehozott adatokat is ellenőrizni kell, mielőtt, készen áll a felhasználásra. Ezekben az esetekben a szelet befejezése után a végrehajtási, a kimeneti szelet állapota **Várakozás** -az-substatus **érvényesítési**. Miután a szelet érvényesítése után a szelet állapota **készen**. Adatszelet készült, de nem felelt meg az ellenőrzést, ha a szelet függő, alsóbb rétegbeli szeletek tevékenység-végrehajtás nem kerülnek feldolgozásra. [Folyamatok figyelése és felügyelete](data-factory-monitor-manage-pipelines.md) ismerteti a különböző állapotok adatszelet adat-előállítóban.

A **házirend** az adatkészlet-definícióban szakasz definiálja a feltételeket és a feltétellel, hogy az adatkészlet szeleteit meg kell felelniük. A következő táblázat ismerteti a használható tulajdonságok a **házirend** szakaszban:

| Házirend neve | Leírás | A alkalmazni | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Azt ellenőrzi, hogy az adatok egy **az Azure blob** megfelel a minimális méret (megabájtban). |Azure-blob |Nem |NA |
| minimumRows | Azt ellenőrzi, hogy az adatok egy **Azure SQL database** vagy egy **Azure-tábla** sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

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

Ezeket a tulajdonságokat és példákat kapcsolatos további információkért lásd: [adatkészleteket hoz létre](data-factory-create-datasets.md) cikk. 

## <a name="activity-policies"></a>Tevékenységszabályzatok
Házirendek egy tevékenység futásidejű viselkedését befolyásolják, kifejezetten egy tábla a szelet feldolgozása során. Az alábbi táblázatban a részleteket.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>A maximális érték: 10 |1 |A tevékenység párhuzamos végrehajtások száma.<br/><br/>Ez határozza meg, amely akkor fordulhat elő, a másik szeletek párhuzamos tevékenység-végrehajtások száma. Például ha egy tevékenység kell áthaladnia rengeteg rendelkezésre álló adatok, a nagyobb párhuzamosság értéke felgyorsítja az adatfeldolgozás. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza, hogy a feldolgozott adatszelet rendezése.<br/><br/>Például ha 2 szeletekre (du. 4: az egyik oka és a egy másik, 17: 00), és mindkettő végrehajtás függőben. A executionPriorityOrder NewestFirst kell állít be, ha a szelet délután 5-kor lesz elsőként feldolgozva. Hasonlóképpen ha beállította a executionPriorityORder OldestFIrst kell, majd du. 4: a szeletet dolgoz fel. |
| retry |Egész szám<br/><br/>A maximális érték 10 lehet. |0 |Mielőtt az adatfeldolgozás a szelet hiba van megjelölve. az újrapróbálkozások száma. Tevékenység-végrehajtási adatszelet rendszer legfeljebb a megadott újrapróbálkozások számát. Az újrapróbálkozás történik, a hiba után minél hamarabb. |
| timeout |Időtartam |00:00:00 |A tevékenység időkorlátja. Példa: 00:10:00 (magában foglalja a időkorlátja 10 perc)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépési érték a végtelen.<br/><br/>Ha a szelet adatok feldolgozási idő meghaladja az időtúllépés értéke, meg lett szakítva, és a rendszer megpróbálja próbálkozzon újra a feldolgozást. Az újrapróbálkozások száma attól függ, hogy az újrapróbálkozási tulajdonság. Időtúllépés történik, ha az állapot értéke időtúllépés miatt megszakadt. |
| késleltetés |Időtartam |00:00:00 |Adja meg a késleltetés, elindul a szelet feldolgozásának előtt.<br/><br/>Adatszelet tevékenység végrehajtása után a késleltetési idő legyen a várt végrehajtási időn túli elindult.<br/><br/>Példa: 00:10:00 (magában foglalja a késés 10 perc) |
| longRetry |Egész szám<br/><br/>A maximális érték: 10 |1 |A szelet végrehajtása előtt hosszú újrapróbálkozási kísérletek száma.<br/><br/>longRetry kísérletek által longRetryInterval elosztásban. Ezért ha egy újrapróbálkozási kísérletek közötti időre van szüksége, a longRetry. Ha az újrapróbálkozás és longRetry is meg van adva, egyes longRetry kísérletek magában foglalja az újrapróbálkozási kísérletek és kísérletek maximális számát. a rendszer újrapróbálkozik * longRetry.<br/><br/>Például ha a tevékenységszabályzat is van a következő beállításokat:<br/>Ismételje meg: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egyetlen szeletet végrehajtására van (Várakozás állapot) és a tevékenység-végrehajtási minden alkalommal sikertelen lesz. Kezdetben lenne 3 egymást követő végrehajtási kísérlet. Minden kísérlet után a szelet állapota lenne, próbálkozzon újra. Első 3 kísérletek esnek, miután a szelet állapota LongRetry lehet.<br/><br/>Egy óra (azaz longRetryInteval a érték) egy másik hárompéldányos készletet 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota szeretné végrehajtani, és nincs további újrapróbálkozások volna lehetséges. Ezért a teljes 6 kísérlet történt.<br/><br/>Minden olyan végrehajtása sikeres, ha a szelet állapota kész lenne, és nincs további próbálkozások nem megkísérlése.<br/><br/>a függő adatok érkeznek nem determinisztikus időpontokban vagy a teljes környezet nem flaky akkor fordul elő, hogy mely adatok feldolgozása a longRetry használni. Ezekben az esetekben újrapróbálkozások egymás után nem segíthet ezzel és a egy időszak után így időben a kívánt kimenetet eredményez.<br/><br/>Legyen körültekintő, Word: nincs beállítva a longRetry, longRetryInterval vagy nagy értékeket. Általában a magasabb értékek hasonló más rendszerből adódó problémákat. |
| longRetryInterval |Időtartam |00:00:00 |Hosszú újrapróbálkozás kísérletek közötti késleltetés |

További információkért lásd: [folyamatok](data-factory-create-pipelines.md) cikk. 

## <a name="parallel-processing-of-data-slices"></a>Párhuzamos feldolgozás adatszelet
A folyamat kezdő dátuma a múltban állíthatja be. Ha így tesz, a Data Factory automatikusan kiszámítja a (háttérrendszer kitöltéseket) az összes adatszelet a múltban, és elkezdi őket. Példa: Ha a kezdő dátuma: 2017-04-01 létrehoz egy folyamatot, és az aktuális dátum későbbi, a 2017-04-10. Ha a kiadása ütemben történik, a kimeneti adatkészlet naponta, akkor az összes szelet a 2017-04-01, 2017-04-09 feldolgozása a Data Factory kezdődik azonnal, mert a kezdő dátuma a múltban van. 2017-04-10 származó szelet nem lett feldolgozva még, mert a rendelkezésre állási szakaszban style tulajdonság értéke EndOfInterval alapértelmezés szerint. A legrégebbi szelet feldolgozása először alapértelmezett executionPriorityOrder értéke OldestFirst. A stílustulajdonság ismertetését lásd: [adatkészlet rendelkezésre](#dataset-availability) szakaszban. A executionPriorityOrder szakasz, olvassa el a [tevékenység-szabályzatok](#activity-policies) szakaszban. 

Adatszelet biztonsági töltve beállításával a párhuzamos feldolgozásra is beállíthat a **egyidejűségi** tulajdonságot a **házirend** szakasz a tevékenység JSON. Ez a tulajdonság határozza meg, amely akkor fordulhat elő, a másik szeletek párhuzamos tevékenység-végrehajtások száma. Az egyidejűség tulajdonság alapértelmezett értéke 1. Ezért egy szelet feldolgozása egyszerre alapértelmezés szerint. A maximális értéke 10. Ha egy folyamat igények haladhat végig elérhető adatokat, a nagyobb párhuzamosság értéke rengeteg felgyorsítja az adatfeldolgozás. 

## <a name="rerun-a-failed-data-slice"></a>Futtassa újra a sikertelen adatszelet
Hiba esetén egy adatszelet feldolgozásakor talál miért sikertelen volt a szelet feldolgozása az Azure portal paneljeinek vagy Monitor and Manage Appot. Lásd: [figyelése és felügyelete az Azure portal paneljeinek használatával folyamatok](data-factory-monitor-manage-pipelines.md) vagy [megfigyelési és felügyeleti alkalmazás](data-factory-monitor-manage-app.md) részleteiről.

Vegye figyelembe a következő példának, amely két tevékenységet. Hogy az Activity1 és 2. tevékenység. Activity1 Dataset1 a szelet használ fel, és a egy szeletét mutatja meg Dataset2, amely bemenetként egy szeletét mutatja meg a végső adatkészlet létrehozásához runbook az Activity2 által felhasznált eredményez.

![Sikertelen szeletek](./media/data-factory-scheduling-and-execution/failed-slice.png)

Az ábrán látható, amely három legutóbbi szeletek, kívül a 9-10-kor szeletet előállító számára Dataset2 hiba lépett fel. A Data Factory automatikusan nyomon követi a time series adatkészlet függősége. Ennek eredményeképpen nem indul el a 9-10: 00 alsóbb rétegbeli szelet tevékenységfuttatás.

Data Factory-figyelési és felügyeleti eszközök lehetővé teszik a diagnosztikai naplókat a probléma alapvető okának megkereséséhez és javítást a sikertelen szelet részletesen. A probléma kijavítása után könnyen megkezdheti a tevékenységet a előállítani a sikertelen szeletet. Futtassa újra a valamint állapotváltásra az adatszelet további információkért lásd: [figyelése és felügyelete az Azure portal paneljeinek használatával folyamatok](data-factory-monitor-manage-pipelines.md) vagy [megfigyelési és felügyeleti alkalmazás](data-factory-monitor-manage-app.md).

Után futtassa újra a 9-10-kor szelete **Dataset2**, a Data Factory a Futtatás a 9-10-kor függő szelet elindítja a végső adatkészlet.

![Futtassa újra a sikertelen szeletek](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Egy folyamathoz azonban több tevékenység is tartozhat. Ha több tevékenység van folyamatban lévő és a egy tevékenység kimenetét, nem egy másik tevékenység bemeneti, a tevékenységek párhuzamosan is futtathatók a tevékenységek bemeneti adatszeletek készen állnak.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. A tevékenységek lehetnek azonos vagy eltérő folyamatokban. A második tevékenység végrehajtása csak az elsőt végeztével sikeresen megtörtént.

Vegyük példaként a következő esetben, ha a folyamat két tevékenység rendelkezik:

1. Tevékenység a1-es, amely külső bemeneti adatkészlet D1 igényel, és létrehozza a kimeneti adatkészlet D2.
2. Tevékenység A2 D2 adatkészletből adatbevitelt igényel, és létrehozza a kimeneti adatkészlet D3.

Ebben a forgatókönyvben tevékenységek A1 és A2 azonos folyamatban vannak. Az a1-es tevékenységfuttatások érhető el a külső adatokat, és elérte az ütemezett rendelkezésre állása gyakorisága. A tevékenység a A2 fut, amikor elérhetővé válik a D2-es gép az ütemezett szeletek és ütemezett rendelkezésre állása gyakoriságát elérésekor. Ha egy D2 adatkészlet szelete, a2-es befejezéséig nem fut le, hogy a szelet elérhetővé válik.

A Diagram nézet mindkét azonos folyamat tevékenységei esetében a következő ábra jelenne meg:

![Láncolási tevékenységek a azonos folyamat](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Ahogy korábban említettük, a tevékenységek a különböző folyamatok lehet. Ilyen esetben a következő ábra a diagram nézet jelenne meg:

![Láncolási tevékenységek a két folyamatot](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Tekintse meg a [egymás után másolja](#copy-sequentially) szakaszban példaként a függelékben.

## <a name="model-datasets-with-different-frequencies"></a>Modell adatkészletek eltérő gyakorisággal
A minták a bemeneti és kimeneti adatkészleteket és a tevékenység ütemezési ablak a gyakoriságot is azonos. Egyes forgatókönyvekben van szükség, hogy állít elő kimenetet egy vagy több bemeneti gyakoriságát eltér a gyakorisággal. A Data Factory támogatja ezeket a forgatókönyveket a modellezési.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>1. példa: Jelentést készít, napi kimeneti a bemeneti adatokat, amely minden órában érhető el
Példaként vegyünk egy forgatókönyvet, amelyben megadott mérési adatokat az érzékelők érhető el minden órában az Azure Blob storage-ban. Szeretne jelentést kell készítenie naponta összesített statisztikáit, például átlag, a maximális és minimális az esetén a nap [adat-előállító hive-tevékenység](data-factory-hive-activity.md).

Itt látható, hogyan modellezhetik az ebben a forgatókönyvben a Data Factoryban:

**Bemeneti adatkészlet**

Az óránkénti bemeneti fájlok az adott napra megszakadnak a mappában. Bemeneti rendelkezésre állásra van beállítva, **óra** (frequency: Óra, időköz: 1.).

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

Egy kimeneti fájl jön létre minden nap a napi mappában. Kimeneti rendelkezésre állásának beállított **nap** (frequency: Nap és időköz: 1.).

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

**Tevékenység: a folyamat hive-tevékenység**

A hive-parancsfájl megkapja a megfelelő *DateTime* információkat használó paraméterként a **WindowStart** változó az alábbi kódrészletben látható módon. A hive-parancsfájl betöltheti az adatokat a megfelelő mappából a napot és az összesítést a kimenet létrehozásához futtassa ezt a változót használja.

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

Az alábbi ábrán a forgatókönyv egy adat-függőség szempontjából.

![Függőség](./media/data-factory-scheduling-and-execution/data-dependency.png)

A kimeneti szelet minden nap a bemeneti adatkészlet a 24 óránkénti szeletek függ. A Data Factory a függőségek foglalkozhatunk azzal, a bemeneti adatokat, amely az idő alatt, a kimeneti szelet előállított szeletek által automatikusan kiszámítja. A 24 a bemeneti szeletek bármelyike nem érhető el, ha a Data Factory megvárja, amíg a bemeneti szelet készen álljon a napi tevékenységek futtatása előtt.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>2. példa: Adja meg a függőségi kifejezések és a Data Factory-funkciók
Vegyünk például egy másik forgatókönyv. Tegyük fel, egy hive-tevékenység, amely feldolgozza a két bemeneti adatkészletet. Egy őket az új adatok naponta rendelkezik, de egyikük lekérdezi az új adatok minden héten. Tegyük fel, hogy a csatlakozzon ehhez a két bemenet közötti, és minden nap kimenetet.

Az egyszerű módszer, mely adat-előállítóban automatikusan kitalálja, hogy a jobb a bemeneti szeletek feldolgozása által a kimeneti adatok szelet időpontja időszak nem működik.

Meg kell adnia, hogy minden tevékenység-végrehajtásonként, az adat-előállító kell használnia múlt héten adatszelet heti bemeneti adatkészlete esetében. Használhatja az Azure Data Factory-függvények az alábbi kódrészletben látható módon végrehajtja ezt a viselkedést.

**Input1: Az Azure blob**

Az első bemeneti adat naponta frissíti az Azure-blobot.

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

Input2 az Azure blob hetente frissítése folyamatban.

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

**A kimenetre: Az Azure blob**

Egy kimeneti fájl jön létre minden nap a mappában az adott napra vonatkozóan. Kimeneti rendelkezésre állásának beállítása **nap** (frequency: Nap, időköz: 1.).

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

**Tevékenység: a folyamat hive-tevékenység**

A hive-tevékenység a két bemenet vesz igénybe, és létrehozza a kimeneti szelet minden nap. Minden nap kimeneti szelet a következőképpen függnek az előző hét a heti bemeneti bemeneti szelet is megadhat.

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

Lásd: [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md) függvények és rendszerváltozók, amely támogatja a Data Factory listáját.

## <a name="appendix"></a>Függelék

### <a name="example-copy-sequentially"></a>Példa: másolja egymás után
Lehetőség több másolási műveletek futtatásához egymás után több egymást követő/rendezett módon. Például előfordulhat, hogy van két másolási tevékenység (CopyActivity1 és CopyActivity2) a következő bemeneti adatokat kimeneti adatkészletek rendelkező folyamatot:   

CopyActivity1

Bemenet: Adatkészlet. Kimenet: Dataset2.

CopyActivity2

Bemenet: Dataset2.  Kimenet: Dataset3.

CopyActivity2 futna, ha a CopyActivity1 végrehajtása sikeresen befejeződött, és Dataset2 érhető el.

Itt látható a minta folyamat JSON-Fájljában:

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

Figyelje meg, hogy a példában a kimeneti adatkészlet az első másolási tevékenység (Dataset2) van megadva a második tevékenység bemeneteként. A második tevékenység fut, ezért csak akkor, amikor készen áll az első tevékenység kimeneti adatkészlete.  

A példában CopyActivity2 lehet egy másik beviteli Dataset3, például, de Dataset2 bemeneteként CopyActivity2, megadhatja, így a tevékenység nem futtatható CopyActivity1 befejezéséig. Példa:

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

Figyelje meg, hogy a példában két bemeneti adatkészletet vannak megadva a második másolási tevékenység. Több bemenetei között meg van adva, csak az első bemeneti adatkészletet adatok másolása szolgál, de egyéb adatkészletekhez függőségeket használják. CopyActivity2 lép érvénybe, csak azt követően a következő feltételek teljesülnek:

* CopyActivity1 sikeresen befejeződött, és Dataset2 érhető el. Ez az adatkészlet nem használatos Dataset4 adatok másolásakor. Csak funkcionál ütemezési függőség CopyActivity2 számára.   
* Dataset3 érhető el. Ez az adatkészlet a célhelyre másolt adatokat jelöli. 
