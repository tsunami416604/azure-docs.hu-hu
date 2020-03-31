---
title: Ütemezés és végrehajtás adatsamunkral
description: Ismerje meg az Azure Data Factory alkalmazásmodell ütemezési és végrehajtási szempontjait.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281066"
---
# <a name="data-factory-scheduling-and-execution"></a>Adatgyár ütemezése és végrehajtása
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [folyamat végrehajtása és az eseményindítók](../concepts-pipeline-execution-triggers.md) cikket.

Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. Ez a cikk feltételezi, hogy a Data Factory alkalmazásmodell-fogalmak alapjait ismeri, beleértve a tevékenységet, a folyamatokat, a csatolt szolgáltatásokat és az adatkészleteket. Az Azure Data Factory alapfogalmait az alábbi cikkekben láthatja:

* [A Data Factory bemutatása](data-factory-introduction.md)
* [Folyamatok](data-factory-create-pipelines.md)
* [Adathalmazok](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>A csővezeték kezdő és záró időpontja
Egy folyamat csak a **kezdési** és **befejezési** időpont között aktív. A végrehajtás nem történik meg a kezdési időpont előtt vagy a befejezési időpont után. Ha a folyamat szünetel, a folyamat nem kerül végrehajtásra, függetlenül annak kezdési és befejezési időpontjától. A folyamat futtatásához nem szabad szüneteltetni. Ezeket a beállításokat (kezdés, befejezés, szüneteltetve) találja a folyamatdefinícióban: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Ezek a tulajdonságok további információt a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben talál. 


## <a name="specify-schedule-for-an-activity"></a>Tevékenység ütemezésének megadása
Nem a folyamat kerül végrehajtásra. A folyamat tevékenységeit a folyamat általános környezetében hajtják végre. A JSON tevékenység **ütemező** szakaszának használatával ismétlődő ütemezést adhat meg egy tevékenységhez. Ütemezheti például, hogy egy tevékenység óránként fusson a következők szerint:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Ahogy az a következő ábrán látható, egy tevékenység ütemezésének megadása egy sor bukdácsoló ablakot hoz létre a folyamat kezdési és befejezési időpontjaival. Az árnyékoló ablakok rögzített méretű, egymást nem átfedő, összefüggő időintervallumok sorozatai. Ezeket a logikai bukdácsoló ablakokat **tevékenységi ablakoknak**nevezzük.

![Példa a tevékenységütemezőre](media/data-factory-scheduling-and-execution/scheduler-example.png)

Egy tevékenység **ütemező** tulajdonsága nem kötelező. Ha megadja ezt a tulajdonságot, meg kell egyeznie a tevékenység kimeneti adatkészletének meghatározásában megadott lépésszámnak. Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Ezért akkor is létre kell hoznia egy kimeneti adatkészletet, ha a tevékenység nem hoz létre kimenetet. 

## <a name="specify-schedule-for-a-dataset"></a>Adatkészlet ütemezése megadása
A Data Factory-folyamat egy tevékenység nulla vagy több bemeneti **adatkészletek,** és egy vagy több kimeneti adatkészletek. Egy tevékenységhez megadhatja azt a lépésszám, amelyen a bemeneti adatok rendelkezésre állnak, vagy a kimeneti adatok az adatkészlet-definíciók **rendelkezésre állási** szakaszának használatával keletkeznek. 

A **rendelkezésre állási** szakasz **gyakorisága** határozza meg az időegységet. A gyakoriság engedélyezett értékei a következők: Perc, Óra, Nap, Hét és Hónap. A rendelkezésre állási **szakaszintervallum** tulajdonsága a gyakoriság szorzóját határozza meg. Például: ha a gyakoriság a Nap, az intervallum pedig 1-re van állítva egy kimeneti adatkészletnél, a kimeneti adatok naponta keletkeznek. Ha a gyakoriságot percként adja meg, azt javasoljuk, hogy az időközt legalább 15-re állítsa. 

A következő példában a bemeneti adatok óránként állnak rendelkezésre, és a kimeneti adatok óránként ( ).`"frequency": "Hour", "interval": 1` 

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

Jelenleg a **kimeneti adatkészlet hajtja az ütemezést.** Más szóval a kimeneti adatkészlethez megadott ütemezés t a rendszer futásidőben futtatja a tevékenységet. Ezért akkor is létre kell hoznia egy kimeneti adatkészletet, ha a tevékenység nem hoz létre kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. 

A következő folyamatdefinícióban az **ütemező** tulajdonság a tevékenység ütemezésének megadására szolgál. Ez a tulajdonság nem kötelező. Jelenleg a tevékenység ütemezésének meg kell egyeznie a kimeneti adatkészlethez megadott ütemezésnek.
 
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

Ebben a példában a tevékenység óránként fut a folyamat kezdési és befejezési időpontjai között. A kimeneti adatok óránként készülnek három órás ablakokhoz (8:00-9:00, 9:00 - 10:00 és 10:00- 11:00). 

A tevékenységfuttatássorán felhasznált vagy előállított adatok minden egyes egységét **adatszeletnek nevezzük.** Az alábbi ábra egy egy bemeneti adatkészletet és egy kimeneti adatkészletet rendelkező tevékenységet mutat be: 

![Rendelkezésre állásütemező](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Az ábra a bemeneti és kimeneti adatkészlet óránkénti adatszeleteit mutatja be. Az ábrán három feldolgozásra kész bemeneti szelet látható. A 10-11 AM tevékenység folyamatban van, így a 10-11 AM kimeneti szelet. 

A JSON adatkészlet aktuális szeletéhez társított időintervallumot a következő változók segítségével érheti el: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) és [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Hasonlóképpen a WindowStart és a WindowEnd segítségével is elérheti a tevékenységablakhoz társított időintervallumot. Egy tevékenység ütemezésének meg kell egyeznie a tevékenység kimeneti adatkészletének ütemezésével. Ezért a SliceStart és SliceEnd értékek megegyeznek a WindowStart és a WindowEnd értékekkel. Ezekről a változókról a [Data Factory függvények és a rendszerváltozók cikkeiben](data-factory-functions-variables.md#data-factory-system-variables) talál további információt.  

Ezeket a változókat a tevékenység JSON különböző célokra használhatja. Ezek segítségével például kiválaszthatja az idősorozat-adatokat (például 8:00 és 9:00 között) képviselő bemeneti és kimeneti adatkészletek adatait. Ez a példa a **WindowStart** és **a WindowEnd** segítségével is kiválasztja a megfelelő adatokat egy tevékenység futtatásához, és másolja egy blobba a megfelelő **folderPath mappával.** A **folderPath** paraméterezve van, hogy minden órában külön mappát kap.  

Az előző példában a bemeneti és kimeneti adatkészletek megadott ütemezése megegyezik (óránként). Ha a bemeneti adatkészlet a tevékenység érhető el egy másik gyakorisággal, mondjuk 15 percenként, a tevékenység, amely ezt a kimeneti adatkészletet, továbbra is óránként egyszer fut, mivel a kimeneti adatkészlet, ami hajtja a tevékenység ütemezését. További információ: [Különböző frekvenciájú adatkészletek modellezése.](#model-datasets-with-different-frequencies)

## <a name="dataset-availability-and-policies"></a>Az adatkészlet ek elérhetősége és házirendjei
Az adatkészlet-definíció rendelkezésre állási szakaszában a gyakorisági és időközeg-tulajdonságok használatát látta. Van néhány más tulajdonságok, amelyek befolyásolják a tevékenység ütemezése és végrehajtása. 

### <a name="dataset-availability"></a>Az adatkészlet elérhetősége 
Az alábbi táblázat a **rendelkezésre állási** szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészletszelet előállításának időegységét.<br/><br/><b>Támogatott gyakoriság:</b>Perc, Óra, Nap, Hét, Hónap |Igen |NA |
| interval |A frekvencia szorzóját adja meg<br/><br/>A "Gyakoriság x intervallum" határozza meg, hogy milyen gyakran keletkezik a szelet.<br/><br/>Ha az adatkészletet óránként kell szeletelni, akkor a <b>Gyakoriság értékét</b> <b>óra</b>értékre, az <b>intervallumot</b> pedig <b>1-re</b>kell állítani.<br/><br/><b>Megjegyzés:</b>Ha a Gyakoriság szót adja meg percként, javasoljuk, hogy az időközt legalább 15 |Igen |NA |
| stílus |Itt adható meg, hogy a szeletet az intervallum elején/végén kell-e előállítani.<ul><li>StartOfInterval (Kezdési intervallum)</li><li>EndOfInterval (Időköz vége)</li></ul><br/><br/>Ha a Gyakoriság beállítása Hónap, és a stílus beállítása EndOfInterval, a szelet a hónap utolsó napján keletkezik. Ha a stílus startofinterval értékre van állítva, a szelet a hónap első napján jön létre.<br/><br/>Ha a Gyakoriság érték Nap, a stílus pedig EndOfInterval értékre van állítva, akkor a szelet a nap utolsó órájában keletkezik.<br/><br/>Ha a Gyakoriság érték Óra, a stílus pedig EndOfInterval értékre van állítva, akkor a szelet az óra végén keletkezik. Például egy 1:00–2:00-as időszakra szóló szelet esetében a szelet 14:00 órakor keletkezik. |Nem |EndOfInterval (Időköz vége) |
| anchorDateTime |Az adathalmazszelet-határok kiszámításához az ütemező által használt abszolút időbeosztást határozza meg. <br/><br/><b>Megjegyzés:</b>Ha az AnchorDateTime dátumrészei részletesebbek, mint a gyakoriság, akkor a részletesebb részeket figyelmen kívül hagyja. <br/><br/>Ha például az <b>időköz</b> <b>óránkénti</b> (gyakoriság: óra és időköz: 1), és az <b>AnchorDateTime</b> <b>perceket és másodperceket</b>tartalmaz, akkor az AnchorDateTime <b>perc és másodperc</b> részei figyelmen kívül lesznek hagyva. |Nem |01/01/0001 |
| offset |Az az időtartam, amely alatt az összes adatkészletszelet kezdő és záróeleme eltolódik. <br/><br/><b>Megjegyzés:</b>Ha mind a anchorDateTime, mind az eltolás meg van adva, az eredmény a kombinált eltolódás. |Nem |NA |

### <a name="offset-example"></a>példa eltolásra
Alapértelmezés szerint a`"frequency": "Day", "interval": 1`napi ( ) szeletek utadás kor (éjfélkor) kezdődnek. Ha azt szeretné, hogy a kezdési idő 6:00-kor legyen UTC idő szerint, állítsa be az eltolást a következő kódrészletben látható módon: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime példa
A következő példában az adatkészlet 23 óránként egyszer keletkezik. Az első szelet az anchorDateTime által megadott időpontban `2017-04-19T08:00:00` kezdődik, amely (UTC idő) értékre van állítva.

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>eltolás/stílus Példa
A következő adatkészlet egy havi adatkészlet, amely minden hónap 3-án, 8:00 órakor ():`3.08:00:00`

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Adatkészlet-házirend
Egy adatkészlet rendelkezhet egy érvényesítési házirend definiálva, amely meghatározza, hogy a szelet végrehajtása által létrehozott adatok érvényesíthetők, mielőtt készen áll a használatra. Ilyen esetekben a szelet végrehajtása után a kimeneti szelet állapota **Megerősítés**alállapottal **Várakozás** állapotra változik. A szeletek ellenőrzése után a szelet állapota Kész állapotra **változik.** Ha egy adatszelet készült, de nem felelt meg az ellenőrzésen, a tevékenység nem dolgozza fel az ezen a szelettől függő alsóbb rétegeket. [Figyelheti és kezelheti](data-factory-monitor-manage-pipelines.md) a folyamatok at a Data Factory adatszeletek különböző állapotait.

Az adatkészlet-definíció **házirendszakasza** határozza meg azokat a feltételeket vagy feltételt, amelyeknek az adatkészletszeleteknek meg kell felelniük. Az alábbi táblázat a **házirendszakaszban** használható tulajdonságokat ismerteti:

| Házirend neve | Leírás | Alkalmazott: | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ellenőrzi, hogy az **Azure blobban** lévő adatok megfelelnek-e a minimális méretkövetelményeknek (megabájtban). |Azure-blob |Nem |NA |
| minimumSorok | Ellenőrzi, hogy az **Azure SQL-adatbázisban** vagy egy **Azure-táblában** lévő adatok tartalmazzák-e a sorok minimális számát. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

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

**minimumSorok**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Ezekről a tulajdonságokról és példákról az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat bővebben. 

## <a name="activity-policies"></a>Tevékenység-szabályzatok
A házirendek hatással vannak egy tevékenység futásidejű viselkedésére, különösen a táblázat szeletének feldolgozásakor. Az alábbi táblázat a részleteket tartalmazza.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Konkurencia |Egész szám <br/><br/>Maximális érték: 10 |1 |A tevékenység egyidejű végrehajtásai száma.<br/><br/>Ez határozza meg a párhuzamos tevékenység végrehajtások száma, amelyek különböző szeleteken fordulhat elő. Ha például egy tevékenységnek nagy mennyiségű rendelkezésre álló adatot kell átnéznie, a nagyobb egyidejűségi érték felgyorsítja az adatfeldolgozást. |
| executionPriorityOrder |LegújabbElső<br/><br/>LegrégebbiElső |LegrégebbiElső |A feldolgozás alatt álló adatszeletek sorrendjét határozza meg.<br/><br/>Ha például 2 szelete van (az egyik 16:00-kor, a másik 17:00-kor történik), és mindkettő végrehajtás rakoncát; Ha a executionPriorityOrder-t NewestFirst-re állítja be, a szelet feldolgozása 17:00 órakor lesz. Hasonlóképpen, ha a executionPriorityORder-t legrégebbiFIrst-re állítja be, akkor a szelet 16:00-kor kerül feldolgozásra. |
| retry |Egész szám<br/><br/>A maximális érték 10 lehet |0 |A szelet adatfeldolgozása előtt újrapróbálkozások száma sikertelenként. Az adatszelet tevékenységének végrehajtása a megadott újrapróbálkozások számáig újra próbálkozik. Az újrapróbálkozás a hiba után a lehető leghamarabb megtörténik. |
| timeout |időtartam |00:00:00 |A tevékenység időmeghosszabbítása. Példa: 00:10:00 (időout 10 mins)<br/><br/>Ha egy érték nincs megadva, vagy 0, az időtúlérték végtelen.<br/><br/>Ha egy szelet adatfeldolgozási ideje meghaladja az időtúllépési értéket, a rendszer megszakítja, és a rendszer megpróbálja újramegpróbálni a feldolgozást. Az újrapróbálkozások száma az újrapróbálkozási tulajdonságtól függ. Időeltoridő-elállás esetén az állapot Beállítása TimedOut. |
| Késleltetés |időtartam |00:00:00 |Adja meg a szelet adatfeldolgozásának elindulása előtti késleltetést.<br/><br/>Az adatszelet tevékenységének végrehajtása a késleltetés után kezdődik a várt végrehajtási idő után.<br/><br/>Példa: 00:10:00 (10 mins késéssel) |
| hosszúRetry |Egész szám<br/><br/>Maximális érték: 10 |1 |A szelet végrehajtása előtt a hosszú újrapróbálkozások száma.<br/><br/>A longRetry kísérleteket a longRetryInterval határozza meg. Tehát, ha meg kell adnia egy időt az újrapróbálkozások között, használja a longRetry.So if you need to specify a time between retry attempts, use longRetry. Ha mind az újrapróbálkozás, mind a longRetry meg van adva, minden longRetry kísérlet újrapróbálkozási kísérleteket tartalmaz, és a kísérletek maximális száma újra * longRetry.<br/><br/>Ha például a következő beállításokkal rendelkezünk a tevékenységi szabályzatban:<br/>Újrapróbálkozás: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egy szeletet kell végrehajtani (az állapot várakozás), és a tevékenység végrehajtása minden alkalommal sikertelen lesz. Kezdetben 3 egymást követő végrehajtási kísérlet lenne. Minden kísérlet után a szelet állapota újra lesz. Miután az első 3 kísérlet véget ért, a szelet állapota longretry lesz.<br/><br/>Egy óra elteltével (azaz longRetryInteval értéke), nem lenne egy másik készlet 3 egymást követő végrehajtási kísérletek. Ezt követően a szelet állapota sikertelen lesz, és nem kísérelt meg több újrapróbálkozást. Ezért összességében 6 kísérlet történt.<br/><br/>Ha bármelyik végrehajtás sikeres, a szelet állapota kész lesz, és nem kísérelmeg több újrapróbálkozást.<br/><br/>A longRetry olyan helyzetekben alkalmazható, amikor a függő adatok nem determinisztikus időpontban érkeznek, vagy az általános környezet pelyhes, amely alatt az adatfeldolgozás történik. Ilyen esetekben az újrapróbálkozások egymás után nem biztos, hogy segít, és ezt időintervallum után eredményezi a kívánt kimenetet.<br/><br/>Figyelmeztető szó: ne állítson be magas értékeket a longRetry vagy longRetryInterval értékéhez. A magasabb értékek általában más rendszerszintű problémákat is jelentenek. |
| longRetryInterval között |időtartam |00:00:00 |A hosszú újrapróbálkozások közötti késleltetés |

További információ: [Pipelines](data-factory-create-pipelines.md) article. 

## <a name="parallel-processing-of-data-slices"></a>Adatszeletek párhuzamos feldolgozása
Beállíthatja a folyamat kezdési dátumát a múltban. Ha így tesz, a Data Factory automatikusan kiszámítja (visszatölti) az összes adatszeletet a múltban, és megkezdi azok feldolgozását. Például: ha létrehoz egy folyamatot a 2017-04-01 kezdési dátummal, és az aktuális dátum 2017-04-10. Ha a kimeneti adatkészlet üteme naponta, majd a Data Factory megkezdi az összes szelet feldolgozását 2017-04-01-től 2017-04-09-ig, mert a kezdési dátum a múltban van. A szelet 2017-04-10 még nem dolgozták fel még, mert a stílus tulajdonság értéke a rendelkezésre állási szakaszban EndOfInterval alapértelmezés szerint. A legrégebbi szelet et először a program dolgozza fel, mivel a executionPriorityOrder alapértelmezett értéke A LegrégebbiElő. A stílustulajdonság leírását az [adatkészlet rendelkezésre állása](#dataset-availability) című szakaszban található. A executionPriorityOrder szakasz leírását a [tevékenységházirendek](#activity-policies) szakaszban található. 

Beállíthatja, hogy a visszatöltött adatszeletek párhuzamosan legyenek feldolgozva, ha a JSON tevékenység **házirendszakaszában** beállítja az **egyidejűségi** tulajdonságot. Ez a tulajdonság határozza meg a párhuzamos tevékenység végrehajtások száma, amelyek különböző szeleteken fordulhat elő. Az egyidejűségi tulajdonság alapértelmezett értéke 1. Ezért alapértelmezés szerint egy szelet feldolgozása történik. A maximális érték 10. Ha egy folyamatnak nagy mennyiségű rendelkezésre álló adaton kell átesnie, a nagyobb egyidejűségi érték felgyorsítja az adatfeldolgozást. 

## <a name="rerun-a-failed-data-slice"></a>Sikertelen adatszelet újbóli futtatása
Ha hiba történik egy adatszelet feldolgozása közben, megtudhatja, hogy miért nem sikerült egy szelet feldolgozása az Azure Portal blades vagy a Monitor and Manage App használatával. A részletekért tekintse meg [a folyamatok figyelése és kezelése az Azure Portal blades](data-factory-monitor-manage-pipelines.md) vagy [a Figyelés i és felügyeleti alkalmazás](data-factory-monitor-manage-app.md) használatával.

Vegye figyelembe a következő példát, amely két tevékenységet mutat. Tevékenység1 és 2. Az Activity1 a Dataset1 egy szeletét használja fel, és létrehoz egy dataset2 szeletet, amelyet az Activity2 a végső adatkészlet egy szeletének létrehozásához használ fel.

![Sikertelen szelet](./media/data-factory-scheduling-and-execution/failed-slice.png)

Az ábra azt mutatja, hogy három legutóbbi szeletből hiba történt a Dataset2 9-10 AM szelet ének előállításában. A Data Factory automatikusan nyomon követi az idősorozat-adatkészlet függőségét. Ennek eredményeképpen nem indítja el a tevékenység futását a 9-10 am alsó bb réteghez.

A Data Factory figyelési és felügyeleti eszközei lehetővé teszik a sikertelen szelet diagnosztikai naplóinak részletezését, hogy könnyen megtalálhassa a probléma alapvető okait, és javítsa ki. Miután kijavította a problémát, könnyedén elindíthatja a tevékenység futtatását a sikertelen szelet létrehozásához. Az adatszeletek állapotátmenetek újrafuttatásáról és megértéséről a [folyamatok figyelése és kezelése az Azure Portal blades vagy](data-factory-monitor-manage-pipelines.md) [a Figyelés és kezelés alkalmazás](data-factory-monitor-manage-app.md)használatával című témakörben talál további információt.

A **Dataset2**9-10 AM szelet újrafuttatása után a Data Factory elindítja a 9-10 AM függő szelet futását a végső adatkészleten.

![Sikertelen szelet ismétlése](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Egy folyamathoz azonban több tevékenység is tartozhat. Ha egy folyamatban több tevékenység is található, és egy tevékenység kimenete nem egy másik tevékenység bemenete, a tevékenységek párhuzamosan futhatnak, ha a tevékenységek bemeneti adatszeletei készen állnak.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. A tevékenységek lehetnek ugyanabban a folyamatban, vagy különböző folyamatokban. A második tevékenység csak akkor hajtódik végre, ha az első sikeresen befejeződik.

Vegyük például a következő esetet, amikor egy csővezeték nek két tevékenysége van:

1. A1 tevékenység, amely külső bemeneti adatkészletet igényel D1, és létrehozza a D2 kimeneti adatkészletet.
2. A2 tevékenység, amely a D2 adatkészletből történő bevitelt igényel, és a D3 kimeneti adatkészletet hozza létre.

Ebben a forgatókönyvben az A1 és A2 tevékenységek ugyanabban a folyamatban vannak. Az A1 tevékenység akkor fut, amikor a külső adatok rendelkezésre állnak, és az ütemezett rendelkezésre állási gyakoriság elérése. Az A2 tevékenység akkor fut, amikor a D2 ütemezett szeletei elérhetővé válnak, és elérik az ütemezett rendelkezésre állási gyakoriságot. Ha hiba történik a D2 adatkészlet egyik szeletében, az A2 nem fut az adott szeleten, amíg az elérhetővé nem válik.

A Diagram nézet, amelyben mindkét tevékenység ugyanabban a folyamatban van, a következő diagramhoz hasonlóan nézne ki:

![Láncolási tevékenységek ugyanabban a csővezetékben](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Amint azt korábban említettük, a tevékenységek különböző csővezetékekben lehetnek. Ilyen esetben a diagramnézet a következő diagramhoz hasonlóan nézne ki:

![Két csővezeték láncolása](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Lásd a másolás egymás után szakaszban a függelékben egy példát.

## <a name="model-datasets-with-different-frequencies"></a>Különböző frekvenciájú modelladatkészletek
A mintákban a bemeneti és kimeneti adatkészletek gyakorisága és a tevékenységütemezési ablak megegyezik. Egyes forgatókönyvek megkövetelik a kimenet et egy vagy több bemenet frekvenciájától eltérő frekvencián. A Data Factory támogatja ezeket a forgatókönyveket.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>1. minta: Óránként elérhető bemeneti adatok napi kimeneti jelentésének elkészítése
Fontolja meg egy olyan forgatókönyv, amelyben az Azure Blob storage-ban óránként elérhető érzékelők ből származó bemeneti mérési adatokat. Napi összesítő jelentést szeretne készíteni olyan statisztikákkal, mint az átlagos, a maximális és a minimum a nap hoz a [Data Factory struktúra tevékenységével.](data-factory-hive-activity.md)

Itt van, hogyan modellezheti ezt a forgatókönyvet a Data Factory:

**Bemeneti adatkészlet**

Az óránkénti bemeneti fájlok az adott nap mappájába kerülnek. A bemenet irendelkezésre állás a **Óra** (gyakoriság: Óra, intervallum: 1) értékben van beállítva.

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

Minden nap egy kimeneti fájl jön létre a napi mappában. A kimenet elérhetősége a **Nap** (gyakoriság: Nap és intervallum: 1) beállítással van beállítva.

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

**Tevékenység: kaptártevékenység egy folyamatban**

A struktúraparancsfájl a megfelelő *DateTime-információkat* kapja meg paraméterekként, amelyek a **WindowStart** változót használják a következő kódrészletben látható módon. A struktúraparancsfájl ezt a változót használja az adatok betöltéséhez a megfelelő mappából a naphoz, és futtassa az összesítést a kimenet létrehozásához.

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

Az alábbi ábra a forgatókönyvet adatfüggőségi szempontból mutatja be.

![Adatfüggőség](./media/data-factory-scheduling-and-execution/data-dependency.png)

A kimeneti szelet minden nap függ 24 óránkénti szeletek egy bemeneti adatkészletből. A Data Factory automatikusan kiszámítja ezeket a függőségeket a bemeneti adatszeletek kiszámításával, amelyek ugyanabban az időszakban esnek, mint a kimeneti szelet et. Ha a 24 bemeneti szelet bármelyike nem érhető el, a Data Factory megvárja, amíg a bemeneti szelet készen áll a napi tevékenység futtatása előtt.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>2. minta: Függőség megadása kifejezésekkel és Adatfeldolgozó függvényekkel
Vegyünk egy másik forgatókönyvet. Tegyük fel, hogy rendelkezik egy hivatás-tevékenység, amely feldolgozza a két bemeneti adatkészletek. Az egyik naponta új adatokat tartalmaz, de az egyik minden héten új adatokat kap. Tegyük fel, hogy a két bemeneten keresztül szeretne csatlakozni, és minden nap kimenetet szeretne létrehozni.

Az egyszerű megközelítés, amelyben a Data Factory automatikusan kitalálja a megfelelő bemeneti szeleteket a feldolgozáshoz a kimeneti adatszelet időszakához való igazításával, nem működik.

Meg kell adnia, hogy minden tevékenység futtatásakor a Data Factory kell használnia a múlt heti adatszelet a heti bemeneti adatkészlet. Az Azure Data Factory-függvények használata a következő kódrészletben látható módon a viselkedés megvalósításához.

**Bevitel1: Azure blob**

Az első bemenet az Azure blob naponta frissül.

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

**Bevitel2: Azure blob**

Az Input2 az Azure blob hetente frissülő.

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

**Kimenet: Azure blob**

Egy kimeneti fájl jön létre minden nap a mappában a nap. A kimenet elérhetősége **nap** (gyakoriság: Nap, intervallum: 1).

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

**Tevékenység: kaptártevékenység egy folyamatban**

A hive tevékenység veszi a két bemenet, és létrehoz egy kimeneti szelet minden nap. Megadhatja, hogy minden nap kimeneti szeletfügg az előző heti bemeneti szelet heti bemeneti az alábbiak szerint.

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

A [Data Factory által](data-factory-functions-variables.md) támogatott függvények és rendszerváltozók listáját a Data Factory által támogatott függvények és rendszerváltozók listája tartalmazza.

## <a name="appendix"></a>Függelék

### <a name="example-copy-sequentially"></a>Példa: másolás egymás után
Lehetőség van több másolási művelet futtatására egymás után egymás után szekvenciális/rendezett módon. Előfordulhat például, hogy két másolási tevékenység van egy folyamatban (CopyActivity1 és CopyActivity2) a következő bemeneti adatkimeneti adatkészletekkel:   

CopyActivity1

Bemenet: Adatkészlet. Kimenet: Adatkészlet2.

CopyActivity2

Bemenet: Dataset2.  Kimenet: Adatkészlet3.

A CopyActivity2 csak akkor fut, ha a CopyActivity1 sikeresen lefutott, és a Dataset2 elérhető.

Itt van a Minta csővezeték JSON:

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

Figyelje meg, hogy a példában az első másolási tevékenység (Dataset2) kimeneti adatkészlete a második tevékenység bemeneteként van megadva. Ezért a második tevékenység csak akkor fut, ha az első tevékenység kimeneti adatkészlete készen áll.  

A példában a CopyActivity2 egy másik bemenettel is rendelkezhet, például a Dataset3, de a Dataset2 értéket adja meg a CopyActivity2 bemenetként, így a tevékenység nem fut, amíg a CopyActivity1 be nem fejeződik. Példa:

CopyActivity1

Bemenet: Adatkészlet1. Kimenet: Adatkészlet2.

CopyActivity2

Bemenetek: Dataset3, Dataset2. Kimenet: Adatkészlet4.

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

Figyelje meg, hogy a példában két bemeneti adatkészlet van megadva a második másolási tevékenységhez. Ha több bemenet van megadva, csak az első bemeneti adatkészletet használja a rendszer az adatok másolásához, de más adatkészleteket függőségként használ. A CopyActivity2 csak a következő feltételek teljesülése után indulna el:

* A CopyActivity1 sikeresen befejeződött, és a Dataset2 elérhető. Ez az adatkészlet nem használatos az adatok Dataset4 rendszerbe történő másolásakor. Csak a CopyActivity2 ütemezési függőségeként működik.   
* Adatkészlet3 elérhető. Ez az adatkészlet a célhelyre másolt adatokat jelöli. 
