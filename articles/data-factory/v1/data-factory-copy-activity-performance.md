---
title: Másolja a tevékenység teljesítmény- és hangolási útmutató |} Microsoft Docs
description: További információk a másolási tevékenység használatakor az Azure Data Factory adatátvitel teljesítményét befolyásoló legfontosabb tényezők.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 27d74ce2cf8fdc4434c48c36dd0c0751dbbab232
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622312"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolja a tevékenység teljesítmény- és hangolási útmutató

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-copy-activity-performance.md)
> * [2. verzió – Előzetes verzió](../copy-activity-performance.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [másolása tevékenység teljesítmény és a Data Factory 2-es hangolási útmutatója](../copy-activity-performance.md).

Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást nyújt. Ez lehetővé teszi terabájtos adatkészleteket több példányát minden nap felhő gazdag számos és a helyszíni adattárolókhoz. Blazing-gyors Adatbetöltési teljesítmény annak érdekében, hogy az alapvető "big data" probléma összpontosíthat kulcs: speciális elemzési megoldások kialakításához, és lekérése mélyebben elemezheti az adatokat.

Azure számos vállalati szintű adatok tárolási és adatok adatraktár megoldások, és a másolási tevékenység során magas szinten optimalizált Adatbetöltési konfigurálása, és állítson be egyszerű élményt nyújt. Csak egyetlen példány tevékenységgel érhet el:

* Az adatok betöltése **Azure SQL Data Warehouse** : **1,2 GB/s**. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Az adatok betöltése **Azure Blob Storage tárolóban** : **1,0 GB/s**
* Az adatok betöltése **Azure Data Lake Store** : **1,0 GB/s**

Ez a cikk ismerteti:

* [Hivatkozás számok](#performance-reference) támogatott forrás és a fogadó adattárolókhoz segítségével megtervezheti a projekt;
* Funkciókat, amelyek különböző helyzetekben, például a Másolás átviteli képes javítani [adatátviteli adategységek cloud](#cloud-data-movement-units), [másolási párhuzamos](#parallel-copy), és [másolási előkészített](#staged-copy);
* [Teljesítményhangolás útmutatást](#performance-tuning-steps) hogyan javítható a teljesítmény és a Másolás teljesítményre gyakorolt hatásáról tényezők a.

> [!NOTE]
> Ha nem ismeri a másolási tevékenység során általában, lásd: [adatok áthelyezése a másolási tevékenység segítségével](data-factory-data-movement-activities.md) a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-hivatkozás

Referenciaként táblázat alatti másolási átviteli számát mutatja MB/s mértékegységben a belső tesztekre alapozva megadott forrás- és fogadó párokat. Az összehasonlításhoz, azt is bemutatja, különböző beállításait [adatátviteli adategységek cloud](#cloud-data-movement-units) vagy [az adatkezelési átjáró méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md) a fájlmásolás (több átjárócsomópontok) segítségével.

![Teljesítmény mátrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Az Azure Data Factoryben az 1-es a felhő-felhőbe történő másolás minimális felhő adatok mozgása mértékegységét két. Ha nincs megadva, tekintse meg az alapértelmezett adatátviteli adategységek használatban lévő [adatátviteli adategységek felhőalapú](#cloud-data-movement-units).

**Vegye figyelembe a következő szempontok:**
* Átviteli sebesség számítja ki a következő képlet: [forrás olvasható adatok mérete] / [a másolási tevékenység időtartama futtatása].
* A tábla a hivatkozás számok volt mérni [TPC-H](http://www.tpc.org/tpch/) adathalmaz egy egyetlen másolási tevékenység során futtassa.
* Az Azure data tárolja a forrás- és fogadó is, azonos Azure-régióban.
* A hibrid másolás között a helyszíni és felhőalapú adattároló, minden átjárócsomópont futott, de a elkülönül a helyszíni adattárolót alatt specification gépen. Ha egy adott tevékenység átjáró kiszolgálón már futott, a másolási művelet felhasznált csak kis részét a tesztgép CPU, a memória vagy a hálózati sávszélesség. A további [szempont az adatkezelési átjáró](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 processzormag 2,20 GHz -es Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memory (Memória)</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Network (Hálózat)</td>
        <td>Internetes adapter: 10 GB/s; intranetes adapterének: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Nagyobb átviteli teljesítményt érhet el, ami további adatok adatátviteli egység (DMUs) alapértelmezett maximális DMUs, amely egy felhő-felhőbe történő másolás tevékenységfuttatási 32. Például a 100 DMUs érhet el az adatok másolását az Azure Blob az Azure Data Lake Store: **1.0GBps**. Tekintse meg a [adatátviteli adategységek Cloud](#cloud-data-movement-units) szakasz ezt a szolgáltatást és a támogatott forgatókönyv szerint. Ügyfél [az Azure támogatási](https://azure.microsoft.com/support/) további DMUs kéréséhez.

## <a name="parallel-copy"></a>Párhuzamos másolása
A forrás-adatok olvasása, vagy adatok írása az a cél **belül a másolási tevékenység során futtassa párhuzamosan**. Ez a funkció növeli a teljesítményt, a másolási műveletek, és csökkenti a adatok áthelyezéséhez szükséges időt.

Ez a beállítás különbözik a **egyidejűségi** tulajdonság a tevékenységdefinícióban. A **egyidejűségi** tulajdonság határozza meg, hogy **egyidejű másolási tevékenység fut** folyamat adatok különböző tevékenység Windows (13: 00 való hajnali 2 óra, Reggel 2 3 AM, 3 Reggel 4 óra és így tovább). Ez a funkció akkor hasznos, ha egy korábbi terheléselosztási elvégezhető. A párhuzamos másolási képesség vonatkozik egy **tevékenységfuttatási egyetlen**.

Egy mintaforgatókönyv vizsgáljuk meg. A következő példában a múltban a több szeletek kell feldolgozni. Adat-előállító fut az egyes szeletek másolási tevékenység (egy tevékenység futott) példánya:

* Az adatszelet az első tevékenység ablakból (13: 00 hajnali 2 Órakor) == > tevékenység fut 1
* Az adatszelet, a második tevékenység ablakból (hajnali 2 Órakor hajnali 3 Órakor) == > tevékenység fut 2
* Az adatszelet, a második tevékenység ablakból (3 de hajnali 4 Órakor) == > tevékenység fut 3

És így tovább.

Ebben a példában amikor a **egyidejűségi** értéke 2, **tevékenység fut 1** és **tevékenység fut 2** adatokat másolni két tevékenység windows **egyidejűleg** adatok mozgása teljesítmény javítása érdekében. Azonban ha több fájl Tevékenységfuttatási 1 társul, az adatátviteli szolgáltatás fájlokat másolja a forrás egy fájlhoz egyszerre.

### <a name="cloud-data-movement-units"></a>A mozgás adategységek felhő
A **felhő adatok adatátviteli egység (DMU)** egy mérték, amely jelöli az (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációja) adat-előállítóban egyetlen egységben. DMU akkor alkalmazható, a felhő-felhőbe történő másolás műveletekhez, de nem egy hibrid másolás.

**A minimális felhő adatátviteli adategységek építve a másolási tevékenység során futtassa két.** Ha nincs megadva, az alábbi táblázat a különböző másolással használt alapértelmezett DMUs:

| Másolja át a forgatókönyvben | Szolgáltatás által meghatározott alapértelmezett DMUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | 4 – 16 számát és a fájlok méretétől függően. |
| Minden egyéb másolatot forgatókönyvek | 4 |

Ez az alapértelmezett felülbírálásához adjon meg értéket a **cloudDataMovementUnits** tulajdonság az alábbiak szerint. A **engedélyezett értékek** a a **cloudDataMovementUnits** tulajdonság, 2, 4, 8, 16 és 32. A **felhő DMUs tényleges száma** egyenlő vagy kisebb, mint a konfigurált érték, attól függően, hogy a adatmintát, hogy használja-e a másolási művelet futásidőben. Egy adott másolási forrását, és a fogadó további egységek konfigurálásakor kaphat jobb teljesítménye szintjét kapcsolatos információk: a [teljesítményfigyelési](#performance-reference).

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Ha további felhőalapú DMUs magasabb átviteli van szüksége, forduljon a [az Azure támogatási](https://azure.microsoft.com/support/). 8 beállítása, a fenti jelenleg működik csak akkor, ha Ön **több fájl másolása Blob storage vagy Data Lake Store vagy az Azure Blob storage/Data Lake Store/Amazon S3/felhő FTP/felhő SFTP SQL-adatbázis**.
>

### <a name="parallelcopies"></a>parallelCopies
Használhatja a **parallelCopies** tulajdonság, amely jelzi a másolási tevékenység során használni kívánt párhuzamosságát. Ez a tulajdonság a másolási tevékenység során, a fogadó adattárolókhoz párhuzamosan írni vagy olvasni a forrás is szálai maximális számának tulajdonképpen.

Minden egyes futtatása másolási tevékenységhez adat-előállító száma párhuzamos adatokat másolni a forrás adatokat tárolja, és a cél adatok tárolására használandó határozza meg. Alapértelmezett száma párhuzamos, amelyet használ a forrás és a fogadó által használt függ.  

| Forrás és a fogadó | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása közötti fájlalapú tárolók (Blob-tároló; Data Lake Store; Amazon S3; a helyszíni fájlrendszer; egy helyszíni HDFS) |1 és 32. A fájlok és a felhő adatok adatátviteli egység (DMUs) száma átmásolhatja az adatokat két felhőalapú adattároló, vagy az átjáró számítógépe (érdekében másolja az adatokat, vagy egy helyszíni adattárolóból) egy hibrid másolás használt fizikai konfigurációját között méretétől függ. |
| Az adatok másolása **forrás adatok tárolásához Azure Table Storage** |4 |
| Minden más forrás és a fogadó pár |1 |

Általában az alapértelmezett viselkedés biztosítani fogja a legjobb teljesítményt. Azonban az adatok üzemeltető gépek terhelését vezérlésére tárolja, vagy másolási teljesítmény hangolására meg is felülbírálhatja az alapértelmezett értéket, és adjon meg egy értéket a **parallelCopies** tulajdonság. Az érték 1 és 32 (mind a két szélsőértéket beleértve) között kell lennie. Futásidőben a legjobb teljesítmény érdekében másolási tevékenység értéket használ, amely kisebb vagy egyenlő a megadott érték.

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Vegye figyelembe a következő szempontok:

* Fájlalapú tárolók közötti adatok másolásakor a **parallelCopies** határozza meg a fájlok szintjén párhuzamosságát. Egyetlen fájlba adattömbösítő történne alá, automatikusan és transzparens módon, és úgy van kialakítva, párhuzamos és merőleges parallelCopies az adatok betöltése az ajánlott megfelelő adatrészletméretnek az egy adott forrás adattároló-típus használatával. Tényleges száma párhuzamos adatátviteli szolgáltatást használja, a másolási művelet futásidőben nem több, mint a fájlok száma. Ha a Másolás viselkedése **mergeFile**, másolási tevékenység fájlszintű párhuzamossági tudják kihasználni.
* Ha ad meg értéket a **parallelCopies** tulajdonság, fontolja meg a terhelést növelni a forrás és a fogadó adattároló, és az átjáró, ha az egy hibrid másolás. Ez akkor fordul elő, különösen ha van több tevékenységek vagy tevékenységének ugyanazt az adattárat futtathat egyidejű futtatását. Ha azt észleli, hogy az adattár vagy az átjáró túlterhelik a a terhelés, csökkentse a **parallelCopies** érték a terhelés alól.
* Adatok másolása, amelyek nem fájl alapú áruházak, amelyek a fájlalapú tárolók, az adatátviteli szolgáltatás figyelmen kívül hagyja a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamos végrehajtás meg van adva, akkor nem lesz alkalmazva ebben az esetben.

> [!NOTE]
> Használja az adatkezelési átjáró 1.11 vagy újabb verzióját kell használnia a **parallelCopies** a beállítást, ha így tesz, hibrid másolatát.
>
>

A két tulajdonság használatával eredményesebb, valamint javítható az adatok adatátviteli teljesítményt, lásd: a [használati esetek minta](#case-study-use-parallel-copy). Nem kell konfigurálni **parallelCopies** előnyeit az alapértelmezett viselkedés. Ha konfigurálja és **parallelCopies** túl kicsi, több felhőalapú DMUs nem teljes kihasználását.  

### <a name="billing-impact"></a>Számlázási gyakorolt hatás
Rendelkezik **fontos** jegyezze meg, hogy van szó, a másolási művelet teljes ideje alapján. Ha egy feladat használatával egy órával magával egy felhőalapú egység, és most négy felhő egységek 15 percet vesz igénybe, az általános számlázási szinte változatlan marad. Például használhatja négy felhő egység. Az első felhő egység fordít 10 perc, a második érték 10 perc, 5 perc, a harmadik közül, és a negyedik, 5 perc minden, a másolási tevékenység során egy futtatásához. A teljes másolása (adatátvitel) ideje, 10 + 10 + 5 + 5 = 30 perc van szó. Használatával **parallelCopies** számlázás nincs hatással.

## <a name="staged-copy"></a>Előkészített másolása
Adatok másolása a forrás-tárolóban a fogadó adattárat, amikor előfordulhat, hogy használatát választja a Blob storage egy ideiglenes átmeneti tárolóként. Átmeneti is különösen hasznos az alábbi esetekben:

1. **Betöltési különböző adattároló adatait az SQL Data Warehouse polybase kívánt**. Az SQL Data Warehouse PolyBase a nagy átviteli mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse használja. Azonban az adatok a Blob Storage tárolóban kell lennie, és további feltételeknek kell megfelelnie. Adatok betöltése a eltérő a Blob storage tárolóban, amikor adatok másolása ideiglenes átmeneti Blob-tároló keresztül aktiválhatja. Ebben az esetben adat-előállító hajtja végre a szükséges adatátalakítást annak érdekében, hogy megfelel-e a PolyBase követelményeinek. A PolyBase majd az adatok betöltése az SQL Data Warehouse használ. További részletekért lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Néha szükséges lehet elvégezni egy hibrid adatátvitelt jelölik a (Ez azt jelenti, hogy egy a helyszíni adatok közötti másolásához tároló és a felhőalapú adatokat tárol) lassú hálózati kapcsolaton keresztül**. A teljesítmény javítása érdekében tömörítheti az adatokat a helyszíni, így az adatok áthelyezése az átmeneti adattár a felhőben kevesebb időt vesz igénybe. Majd az adatokat az átmeneti kibontani, a céltár adatok betöltése előtt.
3. **Nyissa meg a 80-as port eltérő port és a tűzfalon a 443-as porton vállalati informatikai házirendeknek miatt érdemes**. Például amikor egy helyszíni adattároló adatok másolása az Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó, akkor aktiválnia kell a Windows tűzfal és a vállalati tűzfalon 1433-as port kimenő TCP-kommunikáció. Ebben a forgatókönyvben előnyeit az átjáró első adatok egy Blob storage átmeneti példányhoz HTTP vagy HTTPS a 443-as porton. Ezt követően az adatok betöltése az SQL Database vagy az SQL Data Warehouse Blob storage átmeneti. Ez a folyamat az 1433-as port engedélyezéséhez nem kell.

### <a name="how-staged-copy-works"></a>Hogyan előkészített másolási működik
Az átmeneti funkció aktiválásakor először az adatokat a rendszer átmásolja az a forrás-tárolót az átmeneti adattárolóhoz (kapcsolja a saját). Ezt követően az adatok átmásolva az átmeneti adattár fogadó adattárba. Adat-előállító automatikusan kezeli a két szakaszból álló folyamata. Adat-előállító is megtisztítja az átmeneti tárolási ideiglenes adatait, az adatmozgás befejeződése után.

A felhőbe másolásának esetéhez (a forrás- és fogadó adatok a felhőben vannak áruházak), az átjáró nem használatos. A Data Factory szolgáltatásnak a másolási műveleteket hajtja végre.

![Másolás előkészített: felhős alkalmazás esetében](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

A hibrid másolás forgatókönyvben (forrása a helyszíni és a fogadó a felhőben), az átjáró adatokat helyezi át a forrás adattárból átmeneti adattárat. Data Factory szolgáltatásnak mozgatja az adatokat az átmeneti adattárolóból fogadó adattárba. Felhőalapú adattároló történő helyszíni adattárolóihoz átmeneti tárolással végzett másolás is támogatja a fordított folyamata.

![Másolás előkészített: hibrid forgatókönyvek](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Adatátvitel aktiválásakor egy átmeneti tároló használatával megadhatja, hogy kívánja-e az adatok áthelyezése az adatforrás adattárolóból ideiglenes vagy átmeneti adattárolóihoz előtt tömörített, és majd kibontása előtt az adatok áthelyezése egy ideiglenes, vagy az átmeneti adatok adatait a fogadó adattárolóban tárolja.

Jelenleg nem lehet másolni az adatok között egy átmeneti tárolási használatával két helyszíni adattárolókhoz. Várhatóan hamarosan elérhető ezt a beállítást.

### <a name="configuration"></a>Konfiguráció
Konfigurálja a **enableStaging** a másolási tevékenység beállítást adja meg, hogy az adatokat a Blob Storage tárolóban előtt töltse be a cél-tárolóban átmenetileg tárolva. Ha **enableStaging** igaz értéke esetén a következő táblázatban szereplő további tulajdonságainak meghatározásához. Ha még nincs fiókja, szükség hozzon létre egy Azure Storage vagy a megosztott aláírás-társított szolgáltatást az átmeneti tárolási.

| Tulajdonság | Leírás | Alapértelmezett érték | Szükséges |
| --- | --- | --- | --- |
| **enableStaging** |Adja meg, hogy átmeneti tárolási ideiglenes adatot másolni. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás, amely az ideiglenes átmeneti tárolóként történő használó tárolási példányát. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló és a közös hozzáférésű jogosultságkód nem használható. Más esetekben használható. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| **path** |Adja meg a Blob. tárolási elérési útja, amelyet szeretne az előkészített adatok. Ha nem ad meg egy elérési utat, a szolgáltatás tárolót hoz létre ideiglenes adatok tárolására. <br/><br/> Adjon meg egy elérési utat, csak akkor, ha a közös hozzáférésű jogosultságkód tárhelyet használ, vagy ideiglenes az adatokat egy adott helyen van szüksége. |– |Nem |
| **enableCompression** |Meghatározza, hogy adatokat tömörített-e, mielőtt azt a cél felé. Ez a beállítás az átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

Íme egy minta definíciója másolási tevékenység az az előző táblázatban leírt tulajdonságokkal:

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Számlázási gyakorolt hatás
Van szó, a két lépésből áll: duration másolja, majd másolja át a típust.

* Használatakor a felhőbe történő másolás (az adatok másolása a felhő-tárolóban egy másik felhőben adattárolóhoz), közben átmeneti van szó, a [másolása időtartamának összege 1 és 2. lépést] x [felhő másolási Egységár].
* Használatakor során (az adatok másolása egy helyszíni adattároló egy felhőalapú adattárolóhoz) hibrid másolatát átmeneti van szó, a [hibrid másolás időtartam] x [hibrid másolás Egységár] + [cloud másolási időtartam] [felhő másolási Egységár] x.

## <a name="performance-tuning-steps"></a>Teljesítmény hangolási lépései
Javasoljuk, hogy szánjon a Data Factory szolgáltatásnak a másolási tevékenység teljesítményét az alábbi lépéseket:

1. **Meghatározásához**. A fejlesztési fázisban tesztelje a feldolgozási sor másolási tevékenység segítségével egy reprezentatív minta alapján. Használhatja a Data Factory [modell felosztás](data-factory-scheduling-and-execution.md) használata adatok korlátozásához.

   Végrehajtási idő és a teljesítményt nyújt a gyűjtése a **figyelés és a felügyeleti alkalmazás**. Válasszon **figyelő & kezelése** a Data Factory kezdőlapon. A faszerkezetes nézetben válassza ki a **kimeneti adatkészlet**. Az a **tevékenység Windows** menüben válassza ki a másolási tevékenység során futtassa. **Tevékenység Windows** sorolja fel, a másolási tevékenység időtartamát és a másolt adatok méretét. Az átviteli sebesség szerepel **tevékenység ablak Explorer**. Az alkalmazással kapcsolatos további tudnivalókért lásd: [figyelése és kezelése az Azure Data Factory adatcsatornák a figyelés és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md).

   ![Tevékenységfuttatás részletei](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   A cikk későbbi részében összehasonlíthatja a teljesítmény és a forgatókönyvhöz a másolási tevékenység konfigurációjának [teljesítményfigyelési](#performance-reference) a tesztelés.
2. **Diagnosztizálhatja és teljesítményének optimalizálásához**. Ha azt láthatja a teljesítmény nem felel meg az elvárásainak, akkor teljesítmény szűk keresztmetszetek azonosítása. Ezt követően optimalizálása teljesítményét, és távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében. A teljesítmény megállapítása teljes leírása, ez a cikk terjed, de az alábbiakban néhány gyakori szempontok:

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [A mozgás adategységek felhő](#cloud-data-movement-units)
     * [Előkészített másolása](#staged-copy)
     * [Adatok adatkezelési átjáró méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Adatkezelési átjáró](#considerations-for-data-management-gateway)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization)
   * [Tömörítés](#considerations-for-compression)
   * [Oszlop leképezése](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)
3. **Bontsa ki a teljes adatkészletet a**. Ha elégedett a végrehajtási eredményt és a teljesítményt, bővítheti a definíció- és adatcsatorna aktív időszakának fedik le a teljes adatkészletet.

## <a name="considerations-for-data-management-gateway"></a>Az adatkezelési átjáró szempontjai
**Átjáró telepítési**: javasoljuk, hogy a gazdagép az adatkezelési átjáró egy dedikált gépet használjon. Lásd: [az adatkezelési átjáró használatának szempontjai](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Átjáró felügyeleti és felfelé vagy kibővített**: egy vagy több átjáró csomópontokkal egyetlen logikai átjáró ki tud szolgálni másolási tevékenység több fut egyidejűleg egy időben. Megtekintheti a közel valós idejű pillanatképe erőforrás-használat (Processzor, memória, network(in/out), stb.), valamint a korlát az Azure portálon, illetve fut egyidejűleg futó feladatainak számát lásd átjáró gépen [figyelő átjárót a portál](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Ha hibrid adatátvitel nagy száma párhuzamos másolási tevékenység fut vagy nagy mennyiségű adat másolása nehéz szükség van, érdemes lehet [növelheti vagy horizontális felskálázás átjáró](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) úgy, hogy jobban tudják használni az erőforrás vagy kiépítése További erőforrás másolása építve. 

## <a name="considerations-for-the-source"></a>A forrás szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb munkaterhelések vagy rajta.

A Microsoft adatokat tárolja, lásd: [figyelése és beállítása a témakörök](#performance-reference) kifejezetten az adatokról, és ismernie az adatok tárolásához teljesítményt nyújt, a válaszhoz szükséges idő minimalizálása és átviteli sebesség maximalizálása súgó.

Ha az adatok másolása az Blob-tárolóból az SQL Data Warehouse-érdemes **PolyBase** teljesítmény növelése érdekében. Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájl alapú adattároló
*(A Blob storage, Data Lake Store, Amazon S3, a helyi fájlrendszer és a helyszíni HDFS tartalmazza)*

* **Átlagos méretét és a fájlok száma**: másolási tevékenység egyszerre visz át egy adatfájlt. Az áthelyezett adatok ugyanannyi a teljes teljesítményt verziója alacsonyabb, ha néhány nagy fájlok miatt a rendszer-indításkori fázis a fájl helyett a sok kisméretű fájlt tartalmaz. Ezért ha lehetséges, kis fájlok egységgé kombinálják ahhoz, hogy nagyobb átviteli teljesítményt nagyobb fájlok.
* **Fájl formátuma és tömörítést**: további részleteket a teljesítmény javítása érdekében tekintse meg a [szempontok a szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization) és [tömörítés szempontjai](#considerations-for-compression) szakaszok.
* Az a **helyszíni fájlrendszer** forgatókönyv, amelyben **az adatkezelési átjáró** van szükség esetén tekintse meg a [az adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) szakasz.

### <a name="relational-data-stores"></a>Relációs adattároló.
*(Tartalmazza az SQL-adatbázis; Az SQL Data Warehouse; Amazon Redshift; SQL Server-adatbázisok; és Oracle, MySQL, DB2, Teradata, Sybase és PostgreSQL adatbázisok stb.)*

* **Adatminta**: A következő tábla sémáját hatással van a Másolás átviteli sebességet. Nagy sorméret kis sorméret akkora adatok másolása egy jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban le adatokat, amelyek kevesebb sort tartalmaznak kevesebb kötegekben.
* **Lekérdezés vagy tárolt eljárás**: optimalizálja a lekérdezést, vagy adja meg, a másolási tevékenység forrásból teszi lehetővé az adatlehívást hatékonyabban tárolt eljárás logikáját.
* A **helyszíni relációs adatbázisok**, például az SQL Server és Oracle, amelyek használatát **az adatkezelési átjáró**, tekintse meg a [az adatkezelési átjárószempontjai](#considerations-on-data-management-gateway) szakasz.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb munkaterhelések vagy rajta.

A Microsoft adatokat tárolja, tekintse meg [figyelése és beállítása a témakörök](#performance-reference) vonatkoznak, adattároló. Ezek a témakörök azt segítenek megérteni adatok tárolási teljesítményt nyújt, és a válaszhoz szükséges idő minimalizálása és átviteli sebesség maximalizálása érdekében.

Adatok másolása **Blob-tároló** való **SQL Data Warehouse**, érdemes lehet **PolyBase** teljesítmény növelése érdekében. Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek bemutatóért lásd: [1 TB-os betöltése az Azure SQL Data Warehouse a 15 perc Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájl alapú adattároló
*(A Blob storage, Data Lake Store, Amazon S3, a helyi fájlrendszer és a helyszíni HDFS tartalmazza)*

* **Másolja a viselkedés**: adatok másolása egy másik fájlalapú tároló, ha a másolási tevékenység keresztül három pontot tartalmaz a **copyBehavior** tulajdonság. Megőrzi a hierarchia, hierarchia simítja, illetve egyesíti a fájlokat. Hierarchia egybesimítását vagy megőrzi az rendelkezik kevéssé vagy egyáltalán ne teljesítményigény, de a fájlok egyesítése hatására a teljesítményigény növelése érdekében.
* **Fájl formátuma és tömörítést**: lásd: a [szempontok a szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization) és [tömörítés szempontjai](#considerations-for-compression) szakaszokban további részleteket a teljesítmény javítása érdekében.
* **BLOB-tároló**: jelenleg Blob storage támogatja csak blokkblobokat optimalizált adatátvitel és átviteli sebességet.
* A **helyszíni fájlrendszerek** használatát igénylő forgatókönyvek **az adatkezelési átjáró**, tekintse meg a [az adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) szakasz.

### <a name="relational-data-stores"></a>Relációs adattároló.
*(Tartalmazza az SQL-adatbázis, az SQL Data Warehouse, az SQL Server-adatbázisok és Oracle-adatbázis)*

* **Másolja a viselkedés**: attól függően, hogy a Tulajdonságok még állított **sqlSink**, másolási tevékenység írja az adatokat a céladatbázis különböző módon.
  * Alapértelmezés szerint az adatok adatátviteli szolgáltatás által a tömeges másolási API lehet adatokat beszúrni hozzáfűzése módját, amely a legjobb teljesítményt biztosít.
  * Konfigurálja a gyűjtő tárolt eljárást, ha az adatbázis az adatokat soronként egyidejűleg ahelyett, hogy a tömeges betöltés vonatkozik. Teljesítmény jelentősen csökken. Ha az adatkészlet túl nagy, ha lehetséges, fontolja meg, hogy használja a **sqlWriterCleanupScript** tulajdonság.
  * Ha konfigurálja az **sqlWriterCleanupScript** minden másolási tevékenységhez tulajdonság futtatásához, a szolgáltatás váltja ki a parancsprogramot, és majd illessze be az adatokat a tömeges másolási API-t használja. Például az egész tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először a forrás az új adatok tömeges-betöltés előtt az összes bejegyzés törlése.
* **Minta és kötegelt adatméret**:
  * A következő tábla sémáját hatással van a Másolás átviteli sebességet. Adatok akkora másolásához nagy sorméret lehetővé teszi egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti az adatok kevesebb kötegek.
  * Másolási tevékenység adatokat egy sorozat része kötegek beszúrása Beállíthatja a sorok számát egy kötegben használatával a **writeBatchSize** tulajdonság. Ha az adatok kis sora van, akkor megadhatja a **writeBatchSize** tulajdonság alacsonyabb kötegelt terheléssel jár és nagyobb átviteli sebességgel kihasználják a magasabb értékű. Ha az adatok sor mérete nagy, legyen óvatos növelésével **writeBatchSize**. Nagy érték egy példány hibáját okozta. az adatbázis túlterhelés vezethet.
* A **helyszíni relációs adatbázisok** , például az SQL Server és Oracle, amelyek használatát **az adatkezelési átjáró**, tekintse meg a [az adatkezelési átjárószempontjai](#considerations-for-data-management-gateway)szakasz.

### <a name="nosql-stores"></a>NoSQL-tárolókon
*(Tartalmazza a Table storage és Azure Cosmos DB)*

* A **Table storage**:
  * **Partíció**: írás a kihagyásos partíciók jelentősen csökkenti a teljesítményt. A forrásadatok rendezze partíciós kulcs, így az adatok bekerülnek hatékonyan egy partíciót egymás után, vagy állítsa be az adatokat írni egy olyan partíciót logikát.
* A **Azure Cosmos DB**:
  * **Kötegméret**: A **writeBatchSize** tulajdonság határozza meg a párhuzamos kérelmek száma a dokumentumok létrehozásához Azure Cosmos DB szolgáltatáshoz. Jobb teljesítmény várható növelésével **writeBatchSize** , mert több párhuzamos kérések érkeznek, az Azure Cosmos-Adatbázishoz. Azonban figyelemmel a sávszélesség-szabályozás írásakor Azure Cosmos DB (hibaüzenet: "kérelmek aránya az nagy"). Számos tényező okozhat, szabályozás, dokumentum mérete, beleértve a dokumentumok, és a célgyűjtemény indexelési házirendet számát. Magasabb másolási átviteli sebesség eléréséhez érdemes lehet jobban gyűjteménye, például S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Szempontok a szerializálás és a deszerializálás
Szerializálás és a deszerializálás akkor fordulhat elő, ha a bemeneti adatkészlet vagy kimeneti adatkészlet egy fájlt. Lásd: [fájl- és tömörítési formátum támogatott](data-factory-supported-file-and-compression-formats.md) a másolási tevékenység által támogatott fájlformátumok adatokkal.

**Másolja a viselkedés**:

* A fájlok másolása a fájl alapú adattárolók között:
  * Ha a bemeneti és kimeneti adatkészletek mindkét rendelkeznek azonos vagy nem fájl formátuma beállítások, az adatátviteli szolgáltatás hajtja végre a szerializálás vagy deszerializálás nélkül bináris másolatát. A forgatókönyv, amelyben a forrás és a fogadó fájl formátuma beállítások eltérnek egymáshoz képest magasabb átviteli láthatja.
  * Ha a bemeneti és kimeneti adatkészletek mindkét szöveges formátumú, és csak a kódolás különböző típusú, az adatok mozgása szolgáltatásnak csak nincs kódolási átalakítás. Nem minden szerializálási és néhány bináris másolatot terhet képest teljesítményt deszerializálás.
  * Ha a bemeneti és kimeneti adatkészletek mindkét rendelkeznek különböző fájlformátumok vagy különböző konfigurációkat, például az elválasztó karaktert, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyamként, átalakítás és majd szerializálni, a megadott kimeneti formátumra alakítja. Ez a művelet egy sokkal jelentős teljesítménybeli, más esetekben terhet képest eredményez.
* Belőle a tárolóban, amely nem fájl alapú (például a tárolóból fájlalapú relációs áruház) fájlokat másolja, a szerializálás vagy deszerializálás lépésre szükség. Ez a lépés jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A fájlformátum választja hatással lehetnek a fájlmásolás. Például az Avro adatokkal metaadatokat tárol kompakt bináris formátumot. Széles körű támogatást nyújtanak a Hadoop rendszerben, feldolgozása és lekérdezéséhez rendelkezik. Az Avro azonban drágább a szerializálás és a deszerializálás, amely szövegformátum képest alacsonyabb másolási teljesítményt eredményez. Győződjön meg a kiválasztott fájl formátuma a feldolgozási folyamat során holistically. Útmutató milyen formában az adatokat tárolja, forrás adattárolókhoz vagy kibontani a külső rendszerek; a legjobb formátumát tárolási elemzésfeldolgozási és lekérdezése; és milyen formátumban az adatok exportálja a jelentéskészítés és a képi megjelenítés eszközök adatpiacait. Egyes esetekben az optimálisnál gyengébb fájl formátuma olvasási és írási teljesítmény lehet hasznos, amikor az átfogó analitikai folyamat érdemes.

## <a name="considerations-for-compression"></a>Tömörítés szempontjai
Ha a bemeneti vagy kimeneti adatkészlet egy olyan fájl, beállíthatja a másolási tevékenység tömörítése és kibontása végrehajtásához, írja az adatokat a cél. Ha úgy dönt, hogy a tömörítés, ellenőrizze-e egy bemeneti/kimeneti (I/O) közötti kompromisszumot és a CPU. A számítási erőforrásokat a felesleges adatok költségek tömörítése. De ismét csökkenti a hálózati i/o- és tárolási. Attól függően, hogy az adatok egy teljes másolatot átviteli sebességének program jelenhet meg.

**Kodek**: másolási tevékenység gzip, bzip2 és Deflate tömörítést típusokat támogatja. Az Azure HDInsight feldolgozásra összes háromféle is felhasználhatnak. Minden egyes tömörítési kodek előnye is van. Például bzip2 rendelkezik a legalacsonyabb másolási átviteli, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert feldolgozásra felosztása. A gzip legkiegyensúlyozottabb a beállítást, és a leggyakrabban használt. Válassza ki a kodek a végpont forgatókönyvéhez leginkább illő.

**Szint**: minden tömörítési kodek két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített beállítás akkor is, ha a fájl nem optimális tömörített, minél gyorsabban tömöríti az adatokat. Az optimális tömörített beállítás több időt tölt a tömörítést, és adatok mennyisége minimális eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb összesített teljesítményt abban az esetben, ha tesztelheti.

**Szempont**: egy nagy mennyiségű adatot egy helyi tárolóban és a felhő közötti másolásához érdemes ideiglenes blob-tároló, tömörítve történjen. Átmeneti tárolás használatával akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások a sávszélesség korlátozó tényezővé, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlenül. Pontosabban a egyetlen másolási tevékenység során is felosztása két másolási tevékenység. Az első másolási tevékenység átmásolja a forrás egy ideiglenes vagy átmeneti blob tömörített formátumban. A második másolási tevékenység másolja át a tömörített adatok átmeneti, és amíg a gyűjtő ír majd kibontja.

## <a name="considerations-for-column-mapping"></a>Oszlopleképezés szempontjai
Beállíthatja a **columnMappings** tulajdonságot a másolási tevékenység a térkép összes vagy a kimeneti oszlop a bemeneti oszlopok egy részét. Az adatátviteli szolgáltatás a adatokat olvas a forráskiszolgálóról, miután kell oszlopleképezés végre az adatokat, mielőtt írja az adatokat a fogadó. A felesleges feldolgozási másolási átviteli csökkenti.

Ha a forrás adattár lekérdezhető, például ha például az SQL-adatbázis vagy SQL Server relációs áruházbeli, vagy ha például a Table storage vagy Azure Cosmos DB, egy NoSQL-tároló érdemes kérdez le, az oszlop szűrési és átrendezése logikát a **lekérdezés** tulajdonság oszlopleképezés használata helyett. Ezzel a módszerrel a leképezés során az adatátviteli szolgáltatás adatokat olvas a forrás-tárolót, ahol használata sokkal hatékonyabb következik be.

## <a name="other-considerations"></a>Egyéb szempontok
Ha át kívánja másolni adatok mérete túl nagy, módosíthatja az adatokat a slicing mechanizmus használatával a Data Factory az üzleti logika további partícióra. Ezt követően ütemezni a másolási tevékenység gyakoribb futtatásához minden másolási tevékenységhez, futtassa a adatméret csökkentése érdekében.

Legyen óvatos adatkészletek és a Data Factory igénylő ugyanazon adattár-összekötőhöz egyszerre másolási tevékenység száma. Sok egyidejű másolási feladat lehet, hogy szabályozni a tárolóban, így csökkent teljesítményt, a másolási feladat belső, az újrapróbálkozásokat és néhány esetben végrehajtása sikertelen.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: egy a helyszíni SQL Server másolását. a Blob storage
**A forgatókönyv**: egy folyamatot egy helyi SQL Server a Blob storage CSV formátumú adatokat másolni épül. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlok lehet tömörített bzip2 formátumba.

**Vizsgálati és elemzési**: az átviteli sebessége a másolási tevékenység érték kevesebb, mint 2 MB/s, amely sokkal lassabb, mint a teljesítmény teljesítményteszt.

**Teljesítményelemzés és hangolása**: a teljesítmény a probléma elhárításához vizsgáljuk meg az adatok feldolgozása és áthelyezése módját.

1. **Olvassa el az adatok**: átjáró megnyílik az SQL-kiszolgálóval, és elküldi a lekérdezést. SQL Server az adatfolyamot küld átjáró az intraneten keresztül válaszol.
2. **Szerializálható és az adatok tömörítése**: átjáró rendezi sorba a CSV formátumnak adatfolyamban, és tömöríti az adatokat, bzip2 adatfolyamba.
3. **Adatírás**: átjáró feltölti a bzip2 adatfolyam a Blob storage az interneten keresztül.

Ahogy látja, folyamatban van-e az adatok feldolgozása és adatfolyam-továbbítási soros módon áthelyezése: SQL Server > LAN > átjáró > WAN > Blob-tároló. **Az általános teljesítményt a minimális átviteli engedi át keresztül a feldolgozási sor**.

![Adatfolyam](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhat a teljesítménybeli szűk keresztmetszetek:

* **Forrás**: az SQL Server maga rendelkezik alacsony átviteli túl nagy terhelés miatt.
* **Az adatkezelési átjáró**:
  * **LAN**: átjáró messze az SQL Server-számítógépen található, és kis sávszélességű kapcsolattal rendelkezik.
  * **Átjáró**: átjáró elérte a következő műveletek végrehajtásához a terhelés korlátozások vonatkoznak:
    * **Szerializálási**: lassú átviteli szerializálása közben a CSV formátumnak adatfolyam rendelkezik.
    * **Tömörítés**: úgy döntött, hogy a lassú tömörítési kodek (például bzip2, amely az alapvető i7 2,8 MB/s).
  * **WAN**: a vállalati hálózat és az Azure-szolgáltatások között sávszélessége alacsony (például T1 = 1,544 kbps; T2 = 6,312 kbit/s).
* **Gyűjtése**: a Blob storage alacsony átviteli rendelkezik. (Ez a forgatókönyv nem valószínű, mert az SLA garantálja 60 MB/s legalább.)

Ebben az esetben bzip2 adattömörítés előfordulhat, hogy lehet lassítja a teljes folyamat. A gzip tömörítési kodek átváltás, előfordulhat, hogy a szűk keresztmetszetet megkönnyítése érdekében.

## <a name="sample-scenarios-use-parallel-copy"></a>Lehetséges eset: párhuzamos példányát használhatja az
**A forgatókönyv I:** 1 MB-os fájlok másolása 1000 a helyszíni fájlrendszerben azon Blob Storage tárolóban.

**Elemzés és teljesítményhangolás**: példát, ha egy darab négyportos core számítógépen telepített átjárót adat-előállító segítségével 16 párhuzamos másolatok helyezze át fájlokat a fájlrendszerből a Blob storage egyidejűleg. A párhuzamos végrehajtás nagyobb teljesítményt eredményez. Emellett közvetlenül megadhatja a párhuzamos másolatok száma. Sok kisméretű fájlok másolásakor párhuzamos másolatok jelentősen segíthet az átviteli sebesség erőforrások hatékonyabb használata.

![1. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-1.png)

**A forgatókönyv II**: 20 blobok 500 MB-os blobtárolóból Data Lake Store Analytics másolja, majd ezután a teljesítmény hangolására.

**Elemzés és teljesítményhangolás**: Ebben a forgatókönyvben adat-előállító átmásolja az adatokat a Blob storage Data Lake Store single-példány használatával (**parallelCopies** állítsa 1-) és egyetlen-felhőbeli adatát adatátviteli egység. Az átviteli sebesség azt láthatja, amely közel ismerteti a [teljesítmény útmutató szakaszban](#performance-reference).   

![2. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-2.png)

**A forgatókönyv III**: egyes fájl mérete nagyobb, mint MB több tucatnyi és a teljes kötet mérete nagy.

**Elemzés és a teljesítmény bekapcsolásáról**: növelése **parallelCopies** nem egy egyetlen-felhő DMU az erőforrás-korlátozások miatt másolási jobb teljesítményt eredményez. Ehelyett adjon meg további felhőalapú DMUs az adatátvitel végrehajtásához további erőforrások eléréséhez. Ne adjon meg egy értéket a **parallelCopies** tulajdonság. Adat-előállító kezeli a párhuzamos végrehajtás meg. Ebben az esetben, ha **cloudDataMovementUnits** 4, körülbelül átviteli négy alkalommal következik be.

![3. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Leírások
Az alábbiakban a teljesítmény figyelése és hivatkozások beállítása a támogatott adatokat tároló egy részénél:

* Az Azure Storage (beleértve a Blob storage és a Table storage): [Azure Storage méretezhetőségi célok](../../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista](../../storage/common/storage-performance-checklist.md)
* Az Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya
* Az SQL Data Warehouse: Alkalmasságát mérik adattárházegységek (dwu-k); Lásd: [kezelése számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Az Azure Cosmos DB: [teljesítményszintek az Azure Cosmos-Adatbázisba](../../cosmos-db/performance-levels.md)
* A helyszíni SQL Server: [figyelő és a teljesítmény hangolni?](https://msdn.microsoft.com/library/ms189081.aspx)
* A helyi fájlkiszolgáló: [teljesítményhangolás fájlkiszolgálók](https://msdn.microsoft.com/library/dn567661.aspx)
