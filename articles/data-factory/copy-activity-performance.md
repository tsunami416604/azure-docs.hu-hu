---
title: Másolja a tevékenység teljesítmény és hangolási útmutató az Azure Data Factory |} Microsoft Docs
description: További információk a másolási tevékenység használatakor az Azure Data Factory adatátvitel teljesítményét befolyásoló legfontosabb tényezők.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: c43973a7e5070676fc0f32a4c8923d57a479f884
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolja a tevékenység teljesítmény- és hangolási útmutató
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-activity-performance.md)
> * [2. verzió – Előzetes verzió](copy-activity-performance.md)


Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást nyújt. Ez lehetővé teszi terabájtos adatkészleteket több példányát minden nap felhő gazdag számos és a helyszíni adattárolókhoz. Blazing-gyors Adatbetöltési teljesítmény annak érdekében, hogy az alapvető "big data" probléma összpontosíthat kulcs: speciális elemzési megoldások kialakításához, és lekérése mélyebben elemezheti az adatokat.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [a másolási tevékenység az 1-es adat-előállítóban](v1/data-factory-copy-activity-performance.md).

Azure számos vállalati szintű adatok tárolási és adatok adatraktár megoldások, és a másolási tevékenység során magas szinten optimalizált Adatbetöltési konfigurálása, és állítson be egyszerű élményt nyújt. Csak egyetlen példány tevékenységgel érhet el:

* Az adatok betöltése **Azure SQL Data Warehouse** : **1,2 GB/s**.
* Az adatok betöltése **Azure Blob Storage tárolóban** : **1,0 GB/s**
* Az adatok betöltése **Azure Data Lake Store** : **1,0 GB/s**

Ez a cikk ismerteti:

* [Hivatkozás számok](#performance-reference) támogatott forrás és a fogadó adattárolókhoz segítségével megtervezheti a projekt;
* Funkciókat, amelyek különböző helyzetekben, például a Másolás átviteli képes javítani [adatátviteli adategységek cloud](#cloud-data-movement-units), [másolási párhuzamos](#parallel-copy), és [másolási előkészített](#staged-copy);
* [Teljesítményhangolás útmutatást](#performance-tuning-steps) hogyan javítható a teljesítmény és a Másolás teljesítményre gyakorolt hatásáról tényezők a.

> [!NOTE]
> Ha nem ismeri a másolási tevékenység során általában, lásd: [tevékenység – áttekintés](copy-activity-overview.md) a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-hivatkozás

Referenciaként alábbi táblázat a számát mutatja, másolása átviteli **MB/s mértékegységben** a megadott forrás- és fogadó párok a **egy egyetlen másolási tevékenység során futtassa a** belső fejlesztésű tesztekre alapozva. Az összehasonlításhoz, azt is bemutatja, különböző beállításait [adatátviteli adategységek cloud](#cloud-data-movement-units) vagy [Self-hosted integrációs futásidejű méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime) a fájlmásolás (több csomópont) segítségével.

![Teljesítmény mátrix](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory 2-es verzióját, a másolási tevékenység végrehajtása az Azure integrációs futásidejű, a minimális megengedett felhő adatok adatátviteli egység esetén két. Ha nincs megadva, tekintse meg az alapértelmezett adatátviteli adategységek használatban lévő [adatátviteli adategységek felhőalapú](#cloud-data-movement-units).

Vegye figyelembe a következő szempontok:

* Átviteli sebesség számítja ki a következő képlet: [forrás olvasható adatok mérete] / [a másolási tevékenység időtartama futtatása].
* A tábla a hivatkozás számok volt mérni [TPC-H](http://www.tpc.org/tpch/) adatkészlet egy egyetlen másolási tevékenység során futtassa.
* Az Azure data tárolja a forrás- és fogadó is, azonos Azure-régióban.
* A hibrid másolás között a helyszíni és felhőalapú adattároló, minden egyes Self-hosted integrációs futásidejű csomópont futott lett az adattárból az alábbi specification külön gépeken. Ha egy adott tevékenység futott, a másolási művelet felhasznált csak kis részét a tesztgép CPU, a memória vagy a hálózati sávszélesség.
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
> Nagyobb átviteli teljesítményt érhet el további adatok adatátviteli egység (DMUs) az engedélyezett maximális DMUs, amelyek egy felhő-felhőbe történő másolás tevékenységfuttatási 32 alapértelmezett használatával. Például a 100 DMUs érhet el az adatok másolását az Azure Blob az Azure Data Lake Store: **1.0GBps**. Tekintse meg a [adatátviteli adategységek Cloud](#cloud-data-movement-units) szakasz ezt a szolgáltatást és a támogatott forgatókönyv szerint. Ügyfél [az Azure támogatási](https://azure.microsoft.com/support/) további DMUs kéréséhez.

## <a name="cloud-data-movement-units"></a>A mozgás adategységek felhő

A **felhő adatok adatátviteli egység (DMU)** egy mérték, amely jelöli az (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációja) adat-előállítóban egyetlen egységben. **Csak érvényes DMU [Azure integrációs futásidejű](concepts-integration-runtime.md#azure-integration-runtime)**, de nem [Self-hosted integrációs futásidejű](concepts-integration-runtime.md#self-hosted-integration-runtime).

**A minimális felhő adatátviteli adategységek építve a másolási tevékenység során futtassa két.** Ha nincs megadva, az alábbi táblázat a különböző másolással használt alapértelmezett DMUs:

| Másolja át a forgatókönyvben | Szolgáltatás által meghatározott alapértelmezett DMUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | 4 – 32 számát és a fájlok méretétől függően. |
| Minden egyéb másolatot forgatókönyvek | 4 |

Ez az alapértelmezett felülbírálásához adjon meg értéket a **cloudDataMovementUnits** tulajdonság az alábbiak szerint. A **engedélyezett értékek** a a **cloudDataMovementUnits** tulajdonság **legfeljebb 256**. A **felhő DMUs tényleges száma** egyenlő vagy kisebb, mint a konfigurált érték, attól függően, hogy a adatmintát, hogy használja-e a másolási művelet futásidőben. Egy adott másolási forrását, és a fogadó további egységek konfigurálásakor kaphat jobb teljesítménye szintjét kapcsolatos információk: a [teljesítményfigyelési](#performance-reference).

Minden egyes példányra, futtassa a másolási tevékenység során kimenet, amikor egy figyelése, futtassa a ténylegesen használt felhő adatok adatátviteli egység tekintheti meg. További részletek a [figyelése másolása](copy-activity-overview.md#monitoring).

> [!NOTE]
> Ha további felhőalapú DMUs magasabb átviteli van szüksége, forduljon a [az Azure támogatási](https://azure.microsoft.com/support/). 8 beállítása, a fenti jelenleg működik csak akkor, ha Ön **több fájlok másolását a Blob storage/Data Lake Store/Amazon S3/felhő FTP/felhő SFTP bármely más felhőalapú adattárolókhoz**.
>

**Példa**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
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

### <a name="cloud-data-movement-units-billing-impact"></a>Felhő adatátviteli adategységek számlázási gyakorolt hatás

Rendelkezik **fontos** jegyezze meg, hogy van szó, a másolási művelet teljes ideje alapján. A teljes időtartam kell fizetni az adatmozgás időtartamának összege DMUs között. A másolási feladat használt egyórás két felhő egységek használatával, és most már nyolc felhő egységek 15 percet vesz igénybe, ha az általános számlázási szinte változatlan marad.

## <a name="parallel-copy"></a>Párhuzamos másolása

Használhatja a **parallelCopies** tulajdonság, amely jelzi a másolási tevékenység során használni kívánt párhuzamosságát. Ez a tulajdonság a másolási tevékenység során, a fogadó adattárolókhoz párhuzamosan írni vagy olvasni a forrás is szálai maximális számának tulajdonképpen.

Minden egyes futtatása másolási tevékenységhez adat-előállító száma párhuzamos adatokat másolni a forrás adatokat tárolja, és a cél adatok tárolására használandó határozza meg. Alapértelmezett száma párhuzamos, amelyet használ a forrás és a fogadó által használt típusától függ:

| Másolja át a forgatókönyvben | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók között |A fájlok és a felhő adatok adatátviteli egység (DMUs) átmásolhatja az adatok között két felhőalapú adattároló vagy a fizikai konfigurációját a Self-hosted integrációs futásidejű gép méretétől függ. |
| Adatok másolása az összes adatforrás adattár az Azure Table storage |4 |
| Minden egyéb másolatot forgatókönyvek |1 |

[!TIP]
> Amikor a fájlalapú tárolók közötti másolás, az alapértelmezett viselkedés (automatikus meghatározott) általában ad a legjobb teljesítményt. 

Az adatok üzemeltető gépek terhelését vezérlésére tárolja, vagy másolási teljesítmény hangolására meg is felülbírálhatja az alapértelmezett értéket, és adjon meg egy értéket a **parallelCopies** tulajdonság. Az érték kisebb, mint 1-nél egész számnak kell lennie. Futásidőben a legjobb teljesítmény érdekében másolási tevékenység értéket használ, amely kisebb vagy egyenlő a megadott érték.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

Vegye figyelembe a következő szempontok:

* Fájlalapú tárolók közötti adatok másolásakor a **parallelCopies** határozza meg a fájlok szintjén párhuzamosságát. Egyetlen fájlba adattömbösítő történne alá, automatikusan és transzparens módon, és úgy van kialakítva, párhuzamos és merőleges parallelCopies az adatok betöltése az ajánlott megfelelő adatrészletméretnek az egy adott forrás adattároló-típus használatával. Tényleges száma párhuzamos adatátviteli szolgáltatást használja, a másolási művelet futásidőben nem több, mint a fájlok száma. Ha a Másolás viselkedése **mergeFile**, másolási tevékenység fájlszintű párhuzamossági tudják kihasználni.
* Ha ad meg értéket a **parallelCopies** tulajdonság, fontolja meg a terhelést növelni a forrás és a fogadó adattárolókhoz, valamint Self-Hosted integrációs futásidejű, ha a másolási tevékenység felhatalmazást általa például hibrid másolására. Ez akkor fordul elő, különösen ha van több tevékenységek vagy tevékenységének ugyanazt az adattárat futtathat egyidejű futtatását. Ha azt észleli, hogy a tároló vagy Self-hosted integrációs futásidejű túlterhelik a a terhelés, csökkentse a **parallelCopies** érték a terhelés alól.
* Adatok másolása, amelyek nem fájl alapú áruházak, amelyek a fájlalapú tárolók, az adatátviteli szolgáltatás figyelmen kívül hagyja a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamos végrehajtás meg van adva, akkor nem lesz alkalmazva ebben az esetben.
* **parallelCopies** a merőleges **cloudDataMovementUnits**. A korábbi számít minden a felhő adatok adatátviteli egység között.

## <a name="staged-copy"></a>Előkészített másolása

Adatok másolása a forrás-tárolóban a fogadó adattárat, amikor előfordulhat, hogy használatát választja a Blob storage egy ideiglenes átmeneti tárolóként. Átmeneti is különösen hasznos az alábbi esetekben:

- **Betöltési különböző adattároló adatait az SQL Data Warehouse polybase kívánt**. Az SQL Data Warehouse PolyBase a nagy átviteli mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse használja. Azonban az adatok Blob-tároló vagy az Azure Data Lake Store kell lenniük, és további feltételeknek kell megfelelnie. Adatok betöltése a eltérő a Blob storage tárolóban, vagy az Azure Data Lake Store, amikor adatok másolása ideiglenes átmeneti Blob-tároló keresztül aktiválhatja. Ebben az esetben adat-előállító hajtja végre a szükséges adatátalakítást annak érdekében, hogy megfelel-e a PolyBase követelményeinek. A PolyBase majd adatok betöltése az SQL Data Warehouse hatékonyan használ. További információkért lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Néha szükséges lehet elvégezni egy hibrid adatátvitelt jelölik a (Ez azt jelenti, hogy egy helyszíni másolása adatokat tárolja egy felhőalapú adattároló) lassú hálózati kapcsolaton keresztül**. A teljesítmény javítása érdekében a előkészített másolat segítségével tömöríti az adatokat a helyszíni, hogy az adatok áthelyezése az átmeneti adattár a felhőben, majd az adatokat az átmeneti adatok céltár való betöltés előtt kibontani kevesebb időt vesz igénybe.
- **Nyissa meg a 80-as port eltérő port és a tűzfalon a 443-as porton vállalati informatikai házirendeknek miatt érdemes**. Például amikor egy helyszíni adattároló adatok másolása az Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó, akkor aktiválnia kell a Windows tűzfal és a vállalati tűzfalon 1433-as port kimenő TCP-kommunikáció. Ebben az esetben előkészített másolatot is kihasználásához először egy Blob Storage példány átmeneti HTTP vagy HTTPS a 443-as porton keresztül az adatok másolása Self-hosted integrációs futásidejű, majd az adatok betöltése az SQL Database vagy az SQL Data Warehouse a Blob storage átmeneti. Ez a folyamat az 1433-as port engedélyezéséhez nem kell.

### <a name="how-staged-copy-works"></a>Hogyan előkészített másolási működik

Az átmeneti tárolási szolgáltatás aktiválása, először az adatok esetén átmásolja a forrás-tárolót az átmeneti tárolási Blob Storage (kapcsolja a saját). Ezt követően az adatok átmásolva az átmeneti adattár fogadó adattárba. Adat-előállító automatikusan kezeli a két szakaszból álló folyamata. Adat-előállító is megtisztítja az átmeneti tárolási ideiglenes adatait, az adatmozgás befejeződése után.

![Előkészített másolása](media/copy-activity-performance/staged-copy.png)

Adatátvitel aktiválásakor egy átmeneti tároló használatával megadhatja, hogy kívánja-e az adatok áthelyezése az adatforrás adattárolóból ideiglenes vagy átmeneti adattárolóihoz előtt tömörített, és majd kibontása előtt az adatok áthelyezése egy ideiglenes, vagy az átmeneti adatok adatait a fogadó adattárolóban tárolja.

Jelenleg nem lehet másolni az adatok között egy átmeneti tárolási használatával két helyszíni adattárolókhoz.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** a másolási tevékenység beállítást adja meg, hogy az adatokat a Blob Storage tárolóban előtt töltse be a cél-tárolóban átmenetileg tárolva. Ha **enableStaging** való `TRUE`, adja meg a következő táblázatban szereplő további tulajdonságokat. Ha még nincs fiókja, szükség hozzon létre egy Azure Storage vagy a megosztott aláírás-társított szolgáltatást az átmeneti tárolási.

| Tulajdonság | Leírás | Alapértelmezett érték | Szükséges |
| --- | --- | --- | --- |
| **enableStaging** |Adja meg, hogy átmeneti tárolási ideiglenes adatot másolni. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg egy [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) társított szolgáltatás, amely az ideiglenes átmeneti tárolóként történő használó tárolási példányát. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló és a közös hozzáférésű jogosultságkód nem használható. Más esetekben használható. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| **path** |Adja meg a Blob. tárolási elérési útja, amelyet szeretne az előkészített adatok. Ha nem ad meg egy elérési utat, a szolgáltatás tárolót hoz létre ideiglenes adatok tárolására. <br/><br/> Adjon meg egy elérési utat, csak akkor, ha a közös hozzáférésű jogosultságkód tárhelyet használ, vagy ideiglenes az adatokat egy adott helyen van szüksége. |– |Nem |
| **enableCompression** |Meghatározza, hogy adatokat tömörített-e, mielőtt azt a cél felé. Ez a beállítás az átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

Íme egy minta definíciója másolási tevékenység az az előző táblázatban leírt tulajdonságokkal:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Előkészített hatás számlázási másolása

Van szó, a két lépésből áll: duration másolja, majd másolja át a típust.

* Használatakor a felhőbe történő másolás (az adatok másolása a felhő-tárolóban egy másik felhőben adattárolóhoz, mindkét szakaszból Azure integrációs Runtime aki jogosult), közben átmeneti van szó, a [másolása időtartamának összege 1 és 2. lépést] x [felhő másolási Egységár].
* Használatakor során (az adatok másolása egy helyszíni adattároló cloud data Store, egy lépésben Self-hosted integrációs futásidejű által készített) hibrid másolatát átmeneti van szó, a [hibrid másolás időtartam] x [hibrid másolás Egységár] + [cloud másolási időtartam] x [felhő Másolás Egységár].

## <a name="performance-tuning-steps"></a>Teljesítmény hangolási lépései

Javasoljuk, hogy szánjon a Data Factory szolgáltatásnak a másolási tevékenység teljesítményét az alábbi lépéseket:

1. **Meghatározásához**. A fejlesztési fázisban tesztelje a feldolgozási sor másolási tevékenység segítségével egy reprezentatív minta alapján. Végrehajtási részleteit és a következő teljesítményjellemzők gyűjtése [figyelése másolása](copy-activity-overview.md#monitoring).

2. **Diagnosztizálhatja és teljesítményének optimalizálásához**. Ha azt láthatja a teljesítmény nem felel meg az elvárásainak, akkor teljesítmény szűk keresztmetszetek azonosítása. Ezt követően optimalizálása teljesítményét, és távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében. A teljesítmény megállapítása teljes leírása, ez a cikk terjed, de az alábbiakban néhány gyakori szempontok:

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [A mozgás adategységek felhő](#cloud-data-movement-units)
     * [Előkészített másolása](#staged-copy)
     * [Önálló üzemeltetett integrációs futásidejű méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Önálló üzemeltetett integrációs futásidejű](#considerations-for-self-hosted-integration-runtime)
   * [Source](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization)
   * [Tömörítés](#considerations-for-compression)
   * [Oszlop leképezése](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)

3. **Bontsa ki a teljes adatkészletet a**. Ha elégedett a végrehajtási eredményt és a teljesítményt, bővítheti a definíció- és csővezeték fedik le a teljes adatkészletet.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Önálló üzemeltetett integrációs futásidejű szempontjai

Ha a másolási tevékenység végrehajtása egy Self-hosted integrációs futásidejű, vegye figyelembe a következőket:

**A telepítő**: azt javasoljuk, hogy a gazdagép integrációs futásidejű dedikált gép használja. Lásd: [Self-hosted integrációs futásidejű használatának szempontjai](concepts-integration-runtime.md).

**Horizontális felskálázás**: egy egyetlen logikai Self-hosted integrációs futásidejű egy vagy több csomópontot tartalmazó ki tud szolgálni másolási tevékenység több fut egyidejűleg egy időben. Ha hibrid adatátvitel nagy száma párhuzamos másolási tevékenység fut vagy nagy mennyiségű adat másolása nehéz szükség van, érdemes lehet [Self-hosted integrációs futásidejű kibővítési](create-self-hosted-integration-runtime.md#high-availability-and-scalability) úgy, hogy több erőforrás kiépítése Másolás építve.

## <a name="considerations-for-the-source"></a>A forrás szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb munkaterhelések vagy rajta.

A Microsoft adatokat tárolja, lásd: [figyelése és beállítása a témakörök](#performance-reference) kifejezetten az adatokról, és ismernie az adatok tárolásához teljesítményt nyújt, a válaszhoz szükséges idő minimalizálása és átviteli sebesség maximalizálása súgó.

* Másolt adatok **az SQL Data Warehouse-blobtárolóból**, érdemes lehet **PolyBase** teljesítmény növelése érdekében. Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről.
* Ha a Másolás **az Azure Blob vagy az Azure Data Lake Store HDFS**, érdemes lehet **ból a DistCp** teljesítmény növelése érdekében. Lásd: [adatok másolása HDFS használata ból a DistCp](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) részleteiről.
* Másolt adatok **az Azure SQL Data Warehouse vagy az Azure BLob vagy az Azure Data Lake Store Redshift**, érdemes lehet **UNLOAD** teljesítmény növelése érdekében. Lásd: [használata UNLOAD adatok másolása Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) részleteiről.

### <a name="file-based-data-stores"></a>Fájl alapú adattároló

* **Átlagos méretét és a fájlok száma**: másolási tevékenység egyszerre visz át egy adatfájlt. Az áthelyezett adatok ugyanannyi a teljes teljesítményt verziója alacsonyabb, ha néhány nagy fájlok miatt a rendszer-indításkori fázis a fájl helyett a sok kisméretű fájlt tartalmaz. Ezért ha lehetséges, kis fájlok egységgé kombinálják ahhoz, hogy nagyobb átviteli teljesítményt nagyobb fájlok.
* **Fájl formátuma és tömörítést**: további részleteket a teljesítmény javítása érdekében tekintse meg a [szempontok a szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization) és [tömörítés szempontjai](#considerations-for-compression) szakaszok.

### <a name="relational-data-stores"></a>Relációs adattároló.

* **Adatminta**: A következő tábla sémáját hatással van a Másolás átviteli sebességet. Nagy sorméret kis sorméret akkora adatok másolása egy jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban le adatokat, amelyek kevesebb sort tartalmaznak kevesebb kötegekben.
* **Lekérdezés vagy tárolt eljárás**: optimalizálja a lekérdezést, vagy adja meg, a másolási tevékenység forrásból teszi lehetővé az adatlehívást hatékonyabban tárolt eljárás logikáját.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb munkaterhelések vagy rajta.

A Microsoft adatokat tárolja, tekintse meg [figyelése és beállítása a témakörök](#performance-reference) vonatkoznak, adattároló. Ezek a témakörök azt segítenek megérteni adatok tárolási teljesítményt nyújt, és a válaszhoz szükséges idő minimalizálása és átviteli sebesség maximalizálása érdekében.

* Másolt adatok **az SQL Data Warehouse-blobtárolóból**, érdemes lehet **PolyBase** teljesítmény növelése érdekében. Lásd: [használja a PolyBase az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről.
* Ha a Másolás **az Azure Blob vagy az Azure Data Lake Store HDFS**, érdemes lehet **ból a DistCp** teljesítmény növelése érdekében. Lásd: [adatok másolása HDFS használata ból a DistCp](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) részleteiről.
* Másolt adatok **az Azure SQL Data Warehouse vagy az Azure BLob vagy az Azure Data Lake Store Redshift**, érdemes lehet **UNLOAD** teljesítmény növelése érdekében. Lásd: [használata UNLOAD adatok másolása Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) részleteiről.

### <a name="file-based-data-stores"></a>Fájl alapú adattároló

* **Másolja a viselkedés**: adatok másolása egy másik fájlalapú tároló, ha a másolási tevékenység keresztül három pontot tartalmaz a **copyBehavior** tulajdonság. Megőrzi a hierarchia, hierarchia simítja, illetve egyesíti a fájlokat. Hierarchia egybesimítását vagy megőrzi az rendelkezik kevéssé vagy egyáltalán ne teljesítményigény, de a fájlok egyesítése hatására a teljesítményigény növelése érdekében.
* **Fájl formátuma és tömörítést**: lásd: a [szempontok a szerializálás és a deszerializálás](#considerations-for-serialization-and-deserialization) és [tömörítés szempontjai](#considerations-for-compression) szakaszokban további részleteket a teljesítmény javítása érdekében.

### <a name="relational-data-stores"></a>Relációs adattároló.

* **Másolja a viselkedés**: attól függően, hogy a Tulajdonságok még állított **sqlSink**, másolási tevékenység írja az adatokat a céladatbázis különböző módon.
  * Alapértelmezés szerint az adatok adatátviteli szolgáltatás által a tömeges másolási API lehet adatokat beszúrni hozzáfűzése módját, amely a legjobb teljesítményt biztosít.
  * Konfigurálja a gyűjtő tárolt eljárást, ha az adatbázis az adatokat soronként egyidejűleg ahelyett, hogy a tömeges betöltés vonatkozik. Teljesítmény jelentősen csökken. Ha az adatkészlet túl nagy, ha lehetséges, fontolja meg, hogy használja a **preCopyScript** tulajdonság.
  * Ha konfigurálja az **preCopyScript** minden másolási tevékenységhez tulajdonság futtatásához, a szolgáltatás váltja ki a parancsprogramot, és majd illessze be az adatokat a tömeges másolási API-t használja. Például az egész tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először a forrás az új adatok tömeges-betöltés előtt az összes bejegyzés törlése.
* **Minta és kötegelt adatméret**:
  * A következő tábla sémáját hatással van a Másolás átviteli sebességet. Adatok akkora másolásához nagy sorméret lehetővé teszi egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti az adatok kevesebb kötegek.
  * Másolási tevékenység adatokat egy sorozat része kötegek beszúrása Beállíthatja a sorok számát egy kötegben használatával a **writeBatchSize** tulajdonság. Ha az adatok kis sora van, akkor megadhatja a **writeBatchSize** tulajdonság alacsonyabb kötegelt terheléssel jár és nagyobb átviteli sebességgel kihasználják a magasabb értékű. Ha az adatok sor mérete nagy, legyen óvatos növelésével **writeBatchSize**. Nagy érték egy példány hibáját okozta. az adatbázis túlterhelés vezethet.

### <a name="nosql-stores"></a>NoSQL-tárolókon

* A **Table storage**:
  * **Partíció**: írás a kihagyásos partíciók jelentősen csökkenti a teljesítményt. A forrásadatok rendezze partíciós kulcs, így az adatok bekerülnek hatékonyan egy partíciót egymás után, vagy állítsa be az adatokat írni egy olyan partíciót logikát.

## <a name="considerations-for-serialization-and-deserialization"></a>Szempontok a szerializálás és a deszerializálás

Szerializálás és a deszerializálás akkor fordulhat elő, ha a bemeneti adatkészlet vagy kimeneti adatkészlet egy fájlt. Lásd: [fájl- és tömörítési formátum támogatott](supported-file-formats-and-compression-codecs.md) a másolási tevékenység által támogatott fájlformátumok adatokkal.

**Másolja a viselkedés**:

* A fájlok másolása a fájl alapú adattárolók között:
  * Ha a bemeneti és kimeneti adatkészletek mindkét rendelkeznek azonos vagy nem fájl formátuma beállítások, az adatátviteli szolgáltatás végrehajtja a **bináris másolási** szerializálás vagy deszerializálás nélkül. A forgatókönyv, amelyben a forrás és a fogadó fájl formátuma beállítások eltérnek egymáshoz képest magasabb átviteli láthatja.
  * Ha a bemeneti és kimeneti adatkészletek mindkét szöveges formátumú, és csak a kódolás különböző típusú, az adatok mozgása szolgáltatásnak csak nincs kódolási átalakítás. Nem minden szerializálási és néhány bináris másolatot terhet képest teljesítményt deszerializálás.
  * Ha a bemeneti és kimeneti adatkészletek mindkét rendelkeznek különböző fájlformátumok vagy különböző konfigurációkat, például az elválasztó karaktert, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyamként, átalakítás és majd szerializálni, a megadott kimeneti formátumra alakítja. Ez a művelet egy sokkal jelentős teljesítménybeli, más esetekben terhet képest eredményez.
* Belőle a tárolóban, amely nem fájl alapú (például a tárolóból fájlalapú relációs áruház) fájlokat másolja, a szerializálás vagy deszerializálás lépésre szükség. Ez a lépés jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A fájlformátum választja hatással lehetnek a fájlmásolás. Például az Avro adatokkal metaadatokat tárol kompakt bináris formátumot. Széles körű támogatást nyújtanak a Hadoop rendszerben, feldolgozása és lekérdezéséhez rendelkezik. Az Avro azonban drágább a szerializálás és a deszerializálás, amely szövegformátum képest alacsonyabb másolási teljesítményt eredményez. Győződjön meg a kiválasztott fájl formátuma a feldolgozási folyamat során holistically. Útmutató milyen formában az adatokat tárolja, forrás adattárolókhoz vagy kibontani a külső rendszerek; a legjobb formátumát tárolási elemzésfeldolgozási és lekérdezése; és milyen formátumban az adatok exportálja a jelentéskészítés és a képi megjelenítés eszközök adatpiacait. Egyes esetekben az optimálisnál gyengébb fájl formátuma olvasási és írási teljesítmény lehet hasznos, amikor az átfogó analitikai folyamat érdemes.

## <a name="considerations-for-compression"></a>Tömörítés szempontjai

Ha a bemeneti vagy kimeneti adatkészlet egy olyan fájl, beállíthatja a másolási tevékenység tömörítése és kibontása végrehajtásához, írja az adatokat a cél. Ha úgy dönt, hogy a tömörítés, ellenőrizze-e egy bemeneti/kimeneti (I/O) közötti kompromisszumot és a CPU. A számítási erőforrásokat a felesleges adatok költségek tömörítése. De ismét csökkenti a hálózati i/o- és tárolási. Attól függően, hogy az adatok egy teljes másolatot átviteli sebességének program jelenhet meg.

**Kodek**: minden tömörítési kodek előnye is van. Például bzip2 rendelkezik a legalacsonyabb másolási átviteli, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert feldolgozásra felosztása. A gzip legkiegyensúlyozottabb a beállítást, és a leggyakrabban használt. Válassza ki a kodek a végpont forgatókönyvéhez leginkább illő.

**Szint**: minden tömörítési kodek két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített beállítás akkor is, ha a fájl nem optimális tömörített, minél gyorsabban tömöríti az adatokat. Az optimális tömörített beállítás több időt tölt a tömörítést, és adatok mennyisége minimális eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb összesített teljesítményt abban az esetben, ha tesztelheti.

**Veszi figyelembe**: nagy mennyiségű adatok egy helyi tárolóban és a felhő közötti másolásához érdemes [másolási előkészített](#staged-copy) engedélyezett tömörítést. Átmeneti tárolás használatával akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások a sávszélesség korlátozó tényezővé, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlenül.

## <a name="considerations-for-column-mapping"></a>Oszlopleképezés szempontjai

Beállíthatja a **columnMappings** tulajdonságot a másolási tevékenység a térkép összes vagy a kimeneti oszlop a bemeneti oszlopok egy részét. Az adatátviteli szolgáltatás a adatokat olvas a forráskiszolgálóról, miután kell oszlopleképezés végre az adatokat, mielőtt írja az adatokat a fogadó. A felesleges feldolgozási másolási átviteli csökkenti.

Ha a forrás adattár lekérdezhető, például ha például az SQL-adatbázis vagy SQL Server relációs áruházbeli, vagy ha például a Table storage vagy Azure Cosmos DB, egy NoSQL-tároló érdemes kérdez le, az oszlop szűrési és átrendezése logikát a **lekérdezés** tulajdonság oszlopleképezés használata helyett. Ezzel a módszerrel a leképezés során az adatátviteli szolgáltatás adatokat olvas a forrás-tárolót, ahol használata sokkal hatékonyabb következik be.

A további [másolási tevékenység séma-hozzárendelése](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Egyéb szempontok

Ha át kívánja másolni adatok mérete túl nagy, módosíthatja az üzleti logikát a további adatok partíció és ütemezni a másolási tevékenység gyakoribb futtatásához minden másolási tevékenységhez, futtassa a adatméret csökkentése érdekében.

Legyen óvatos adatkészletek és a másolási tevékenység egy időben ugyanazt az adattárat csatlakozni a Data Factory igénylő száma. Sok egyidejű másolási feladat lehet, hogy szabályozni a tárolóban, így csökkent teljesítményt, a másolási feladat belső, az újrapróbálkozásokat és néhány esetben végrehajtása sikertelen.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: egy a helyszíni SQL Server másolását. a Blob storage

**A forgatókönyv**: egy folyamatot egy helyi SQL Server a Blob storage CSV formátumú adatokat másolni épül. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlok lehet tömörített bzip2 formátumba.

**Vizsgálati és elemzési**: az átviteli sebessége a másolási tevékenység érték kevesebb, mint 2 MB/s, amely sokkal lassabb, mint a teljesítmény teljesítményteszt.

**Teljesítményelemzés és hangolása**: a teljesítmény a probléma elhárításához vizsgáljuk meg az adatok feldolgozása és áthelyezése módját.

1. **Olvassa el az adatok**: integrációs futásidejű megnyílik az SQL-kiszolgálóval, és elküldi a lekérdezést. SQL-kiszolgáló válaszol, az adatfolyamot küld integrációs futásidejű az intraneten keresztül.
2. **Szerializálható és az adatok tömörítése**: integrációs futásidejű rendezi sorba a CSV formátumnak adatfolyamban, és tömöríti az adatokat, bzip2 adatfolyamba.
3. **Adatírás**: integrációs futásidejű feltölti a bzip2 adatfolyam a Blob storage az interneten keresztül.

Ahogy látja, folyamatban van-e az adatok feldolgozása és adatfolyam-továbbítási soros módon áthelyezése: SQL Server > LAN > integrációs futásidejű > WAN > Blob-tároló. **Az általános teljesítményt a minimális átviteli engedi át keresztül a feldolgozási sor**.

![Adatfolyam](./media/copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhat a teljesítménybeli szűk keresztmetszetek:

* **Forrás**: az SQL Server maga rendelkezik alacsony átviteli túl nagy terhelés miatt.
* **Önállóan üzemel integrációs futásidejű**:
  * **LAN**: integrációs futásidejű messze az SQL Server-számítógépen található, és kis sávszélességű kapcsolattal rendelkezik.
  * **Integrációs futásidejű**: integrációs futásidejű elérte a következő műveletek végrehajtásához a terhelés korlátozások vonatkoznak:
    * **Szerializálási**: lassú átviteli szerializálása közben a CSV formátumnak adatfolyam rendelkezik.
    * **Tömörítés**: úgy döntött, hogy a lassú tömörítési kodek (például bzip2, amely az alapvető i7 2,8 MB/s).
  * **WAN**: a vállalati hálózat és az Azure-szolgáltatások között sávszélessége alacsony (például T1 = 1,544 kbps; T2 = 6,312 kbit/s).
* **Gyűjtése**: a Blob storage alacsony átviteli rendelkezik. (Ez a forgatókönyv nem valószínű, mert az SLA garantálja 60 MB/s legalább.)

Ebben az esetben bzip2 adattömörítés előfordulhat, hogy lehet lassítja a teljes folyamat. A gzip tömörítési kodek átváltás, előfordulhat, hogy a szűk keresztmetszetet megkönnyítése érdekében.

## <a name="reference"></a>Leírások

A teljesítmény figyelése és a támogatott adatokat tároló részénél hivatkozásainak beállítása a következő:

* Az Azure Storage (beleértve a Blob storage és a Table storage): [Azure Storage méretezhetőségi célok](../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista](../storage/common/storage-performance-checklist.md)
* Az Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya
* Az SQL Data Warehouse: Alkalmasságát mérik adattárházegységek (dwu-k); Lásd: [kezelése számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Az Azure Cosmos DB: [teljesítményszintek az Azure Cosmos-Adatbázisba](../cosmos-db/performance-levels.md)
* A helyszíni SQL Server: [figyelő és a teljesítmény hangolni?](https://msdn.microsoft.com/library/ms189081.aspx)
* A helyi fájlkiszolgáló: [teljesítményhangolás fájlkiszolgálók](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>További lépések
A másolási tevékenység cikkekben talál:

- [Tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység séma-hozzárendelése](copy-activity-schema-and-type-mapping.md)
- [Tevékenység hibatűrést másolása](copy-activity-fault-tolerance.md)
