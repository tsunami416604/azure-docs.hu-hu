---
title: Másolja a tevékenységek teljesítményéről és finomhangolásáról szóló útmutató az Azure Data Factoryban |} A Microsoft Docs
description: További információ a másolási tevékenység használatakor az Azure Data Factory adatáthelyezési teljesítményét befolyásoló legfontosabb tényezők.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 47b9ede2d529f78b14c21f53c6cd18ed691a3df3
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445827"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolási tevékenységek teljesítményéhez és finomhangolási útmutató
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)


Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást kínál. Ez lehetővé teszi, hogy másolatot több terabájtnyi adat minden nap gazdag számos felhőalapú és a helyszíni adattárakban. Villámgyors teljesítmény Adatbetöltési fontos annak érdekében, akkor előtérbe kerülhet a core "big data" hiba: fejlett elemzési megoldásokat hozhat, és részletes elemzések lekérése az adatokból.

Az Azure biztosít nagyvállalati adattárolás és adattárház-megoldások, és a másolási tevékenység magas szinten optimalizált Adatbetöltési könnyen konfigurálható, és állítsa be a élményt biztosít. Csak egy egyetlen másolási tevékenységgel rendelkező érhet el:

* Adatok betöltése **Azure SQL Data Warehouse** , **1,2 GB/s**.
* Adatok betöltése **Azure Blob storage** , **1,0 GB/s**
* Adatok betöltése **Azure Data Lake Store** , **1,0 GB/s**

Ez a cikk ismerteti:

* [Hivatkozás teljesítményszámokhoz](#performance-reference) támogatott forrás- és fogadó adattárak segítségével tervezheti meg a projekt;
* Az funkciók, amelyek különböző helyzetekben, például a Másolás átviteli képes javítani [integrációs adategységek](#data-integration-units), [másolási párhuzamos](#parallel-copy), és [szakaszos Másolás](#staged-copy);
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) hangolását a teljesítmény- és másolási teljesítményre gyakorolt hatásáról tényezők a.

> [!NOTE]
> Ha nem ismeri a másolási tevékenység általános, lásd: [másolási tevékenység áttekintéséből](copy-activity-overview.md) Ez a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-referencia

Hivatkozásként van listázva, alábbi táblázatban látható a másolási átviteli száma **(Mbps)** a megadott forrás- és fogadó párokhoz **egy másolási tevékenység futtatása** házon belüli tesztelési alapján. Az összehasonlítást, azt is bemutatjuk, hogyan különböző beállításait [integrációs adategységek](#data-integration-units) vagy [helyi Integration Runtime méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime) (több csomópont) segítségével a másolási teljesítményét.

![Teljesítmény mátrix](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Amikor a másolási tevékenység egy Azure integrációs modul hajtja végre, a minimális megengedett integrációs adategységek (korábbi nevén adatáthelyezési egységek) két. Ha nincs megadva, tekintse meg az alapértelmezett integrációs adategységek van használatban a [integrációs adategységek](#data-integration-units).

Tudnivalók:

* Átviteli sebesség kiszámításához a rendszer az alábbi képlettel: [forrás adatsorból beolvasott adatok mérete] / [a másolási tevékenység futtatási időtartama].
* A tábla hivatkozási teljesítményszámokhoz is méri [TPC-H](http://www.tpc.org/tpch/) adatkészlet egy másolási tevékenység futtatása. A fájlalapú tárolók tesztfájlok, 10GB-nál több fájlt.
* Az Azure-beli adat-áruházaiban nagyban forrás- és fogadóadattárként is, azonos Azure-régióban.
* A helyszíni és felhő közötti hibrid másolás adattárak, minden egyes helyi Integration Runtime csomópontja futott olyan számítógépen, amelyen az adattárból az alábbi specifikáció elkülönített. Egyetlen tevékenység futtatásakor a rendszer a másolási művelet csak egy részét a tesztgép CPU, memória és hálózati sávszélességet használja fel.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 mag 2,20 GHz -es Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memory (Memória)</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Network (Hálózat)</td>
        <td>Az Internet kapcsolat: 10 GB/s; intranetes adapterének: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Nagyobb átviteli sebességet érhet el további adatok integrációs egységek (DIU) használatával. Például a 100 DIUs érheti el adatmásolásra az Azure-Blobból az Azure Data Lake Store, **1.0GBps**. Tekintse meg a [integrációs adategységek](#data-integration-units) adatait a szolgáltatás és a támogatott forgatókönyv a következő szakaszban. 

## <a name="data-integration-units"></a>Adategységek-integráció

A **adatok integrációs egység (DIU)** (korábbi nevén a Felhőbeli adatáthelyezési egység vagy DMU) egy mérték, amely a Data Factory egy egységet (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációjával) hatékonyságát. **Csak érvényes DIU [Azure integrációs modul](concepts-integration-runtime.md#azure-integration-runtime)**, de nem [helyi Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

**A minimális integrációs adategységek megjelenő új másolási tevékenység futtatási két.** Ha nincs megadva, a következő táblázat felsorolja a különböző másolási forgatókönyvek esetén a használt alapértelmezett DIUs:

| Másolja ki a forgatókönyv | Szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | 4. és a fájlok mérete és száma 32 között. |
| Egyéb másolási forgatókönyvek esetén | 4 |

Ez az alapértelmezett felülbírálásához, adjon meg értéket a **dataIntegrationUnits** tulajdonság az alábbiak szerint. A **megengedett értékek** számára a **dataIntegrationUnits** tulajdonság **legfeljebb 256**. A **DIUs tényleges számát** egyenlő vagy kisebb, mint a beállított érték, a adatmintát attól függően, hogy a másolási művelet használ a futási időben. További információ a teljesítmény szintjét egy adott másolási forrásaként és fogadó további egységek konfigurálásakor kaphat,: a [teljesítményfigyelési](#performance-reference).

Láthatja, hogy a ténylegesen felhasznált adatok integrációs egységek minden egyes példányra, futtassa a másolási tevékenység kimenetét, ha egy tevékenységet a figyelést. A részletek [másolása figyelését](copy-activity-overview.md#monitoring).

> [!NOTE]
> DIUs beállításaként **nagyobb, mint 4** csak akkor, ha jelenleg működik, **több fájlt másol a Blob storage és Data Lake Storage/Amazon S3-/ cloud FTP/felhőbeli SFTP-bármely más felhőalapú adatokat tárolja.**.
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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Adatok integrációs egységek számlázási gyakorolt hatás

Rendelkezik **fontos** ne feledje, hogy a másolási művelet teljes időtartama alapján díjkötelesek. A teljes időtartam számítjuk fel az adatok áthelyezését DIUs időtartama összege esetében. Ha most már nyolc felhőbeli egységgel 15 percet vesz igénybe, és egy másolási feladat egy órával érvénybe két felhőbeli egységgel használják, akkor a teljes számlája összegét szinte változatlan marad.

## <a name="parallel-copy"></a>Párhuzamos másolása

Használhatja a **parallelCopies** tulajdonság, amely jelzi, amelyeket szeretne használni a másolási tevékenység párhuzamosságát. Ez a tulajdonság belül a másolási tevékenység, amely a forrásból olvashatja vagy írhatja, a fogadó adattárakba párhuzamos szálak maximális számának is felfoghatók.

Minden egyes másolási tevékenység futtatási a Data Factory használatával adatokat másol a forrásadattárból adatokat tárolja, és a cél-adatok tárolására párhuzamos példányszám határozza meg. Az alapértelmezett példányszámot párhuzamos, amelyet használ a forrás- és fogadó használt típusától függ:

| Másolja ki a forgatókönyv | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók között |A használt két felhőalapú adattárolók, vagy a helyi Integration Runtime-gép, fizikai konfigurációs közötti adatokat másolja a fájlokat és adatokat integrációs egységek (DIUs) száma méretétől függ. |
| Adatok másolása az összes forrásadattár az Azure Table storage |4 |
| Egyéb másolási forgatókönyvek esetén |1 |

> [!TIP]
> Amikor a fájlalapú tárolók közötti másolást, az alapértelmezett viselkedés (automatikus során meghatározott) általában ad a legjobb teljesítményt. 

Szabályozhatja, hogy az adatok üzemeltető gépek terhelését tárolja, vagy a másolási teljesítmény hangolására, dönthet úgy, hogy felülbírálhatja az alapértelmezett értéket, és adjon meg egy értéket a **parallelCopies** tulajdonság. Az érték nagyobb vagy egyenlő 1 egész számnak kell lennie. Futási időben, a legjobb teljesítmény érdekében másolási tevékenység használja, amely kisebb vagy egyenlő az érték, Ön által beállított értéket.

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

Tudnivalók:

* Ha fájlalapú tárolók közötti adatokat másolja a **parallelCopies** határozza meg a fájlok szintjén párhuzamosságát. Egyetlen fájl belül darabolás történnek alatt, automatikusan és transzparens módon, és úgy tervezték, hogy egy adott tároló adattípusra optimális megfelelő adatrészlet mérete használata a párhuzamos és merőleges parallelCopies az adatok betöltéséhez. Az adatátviteli szolgáltatást használja a másolási művelet futási időben párhuzamos másolatok tényleges száma nem több, mint a fájlok száma nem. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájlszintű foka.
* Ha ad meg értéket a **parallelCopies** tulajdonságot használja, fontolja meg a terhelés megnövekedése az adatforrások és adatfogadók adattárak és a helyi Integration Runtime, ha a másolási tevékenység jogosult arra, például a hibrid másolás. Ez akkor fordul elő, különösen ha rendelkezik több egyidejű futtatásainak ugyanazokat a tevékenységeket, amelyek ugyanabban az adattárban futtatásához vagy tevékenységeket. Ha azt tapasztalja, hogy az adattár vagy a helyi integrációs modul a terhelés túlterhelte, csökkentheti a **parallelCopies** érték a terhelés alól.
* Másolt adatok, amelyek nem fájlalapú, amelyek a fájlalapú áruházak áruházakból származó, az adatátviteli szolgáltatás figyelmen kívül hagyja-e a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.
* **parallelCopies** a merőleges **dataIntegrationUnits**. Az előbbi akkor számít az adatok integrációs összes száma közötti.

## <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

- **Szeretné betölteni az különféle adattárakból származó adatokat az SQL Data Warehouse polybase**. Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. Azonban a forrásadatok a Blob storage vagy az Azure Data Lake Store kell lennie, és annak meg kell felelnie a további feltételek. Ha egy adattár eltérő a Blob storage vagy az Azure Data Lake Store tölt be adatokat, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben a Data Factory, győződjön meg arról, hogy megfelel-e a PolyBase követelményeinek szükséges adatátalakítások hajt végre. A PolyBase majd az adatok betöltése az SQL Data Warehouse-bA hatékonyan használja. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Is eltarthat egy ideig a hibrid adatmozgás végrehajtásához (azaz másol egy helyszíni adattárak egy felhőalapú adattár), a lassú hálózati kapcsolaton**. A teljesítmény javítása érdekében a szakaszos Másolás segítségével tömörítse az adatokat a helyszíni, hogy az adatok áthelyezése az átmeneti adattár a felhőben, majd a célként megadott adattárba való betöltés előtt az átmeneti tárnak lévő adatok kibontása kevesebb időt vesz igénybe.
- **Nyissa meg a 80-as port-astól különböző portok és a tűzfal, a 443-as porton a vállalati informatikai házirend miatt nem szeretné**. Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben a forgatókönyvben a szakaszos másolás is kihasználhatja a helyi Integration Runtime először másolhat adatokat egy Blob Storage-példány átmeneti HTTP vagy HTTPS a 443-as porton, majd adatok betöltése az SQL Database vagy az SQL Data Warehouse a Blob storage emeli ki. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése

Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti tárolási Blob storage (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Data Factory automatikusan kezeli az Ön számára a két szakaszból álló folyamatot. A Data Factory is megtisztítja az átmeneti tárolási ideiglenes adatokat, az adatáthelyezés befejeződése után.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Adatok áthelyezése az átmeneti tárolási obchodu aktiválásakor is megadhat kívánja-e az adatokat, mielőtt adatokat a forrásadattárból egy ideiglenes vagy átmeneti adattár tömörített, és ezután előtt, amely adatokat helyez át egy ideiglenes vagy átmeneti adatok kibontása a fogadó adattár tárolja.

Jelenleg nem másolhat adatokat egy átmeneti tárnak használatával két helyszíni adattárak között.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** a másolási tevékenység beállítást adja meg, hogy az adatokat Blob storage-ban a célként megadott adattárba történő betöltésük előtt átmenetileg tárolva. Ha **enableStaging** való `TRUE`, adja meg a következő táblázatban található további tulajdonságokat. Ha még nincs fiókja, is szeretne létrehozni egy Azure Storage vagy a megosztott hozzáférési aláírás-társított szolgáltatást az átmeneti tárolási.

| Tulajdonság | Leírás | Alapértelmezett érték | Szükséges |
| --- | --- | --- | --- |
| **enableStaging** |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg a nevét egy [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) társított szolgáltatás, amely hivatkozik a példány tárolási megoldás, amely egy átmeneti előkészítési tárolót használunk. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló közös hozzáférésű jogosultságkód nem használható. Más esetekben használhatja azt. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| **path** |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás ideiglenes adatokat tárolni egy tárolót hoz létre. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |– |Nem |
| **enableCompression** |Itt adhatja meg, hogy adatok tömöríti-e, mielőtt azt a cél. Ez a beállítás átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

>[!NOTE]
> Ha az átmeneti tárolási társított blob tömörítés engedélyezése, az egyszerű szolgáltatás vagy az MSI-hitelesítés a szakaszos másolás használja. a szolgáltatás nem támogatott.

Íme egy példa definíciója a másolási tevékenység az előző táblázatban leírt tulajdonságokkal:

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

### <a name="staged-copy-billing-impact"></a>Szakaszos másolás számlázási gyakorolt hatás

Két lépést alapján számítjuk fel: másolás időtartama, és másolja a típusát.

* Használata esetén átmeneti során a felhőbe történő másolás (az adatok másolása egy felhőalapú adattár egy másik felhőalapú adattárba, mindkét Azure integrációs modul által felhatalmazott szakaszokat), a számlázás a [összege másolás időtartama az 1 és 2. lépés] x [felhőalapú másolási Egységár].
* Használatakor során egy hibrid másolás (adatok másolása a helyszíni adattárolókból egy felhőalapú adattár helyi integrációs modul által felhatalmazott egy lépésben hajtaná), átmeneti díjkötelesek [hibrid másolás időtartama] x [hibrid másolás Egységár] + [felhőbeli másolás időtartama] x [felhő Másolás Egységár].

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

Javasoljuk, hogy ezen lépések a Data Factory szolgáltatás másolási tevékenységgel rendelkező teljesítményét:

1. **Megállapítja az alapértékeket**. A fejlesztési fázis során a folyamat tesztelése a másolási tevékenység használatával egy reprezentatív mintát ellen. Végrehajtási részleteit és a következő teljesítményjellemzők gyűjtése [másolása figyelését](copy-activity-overview.md#monitoring).

2. **Diagnosztika és a teljesítmény optimalizálása**. Ha a teljesítmény megfigyelte nem felel meg az elvárásainak, azonosíthatja a szűk keresztmetszeteket szeretné. Távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében a teljesítmény optimalizálásával majd. 

    Bizonyos esetekben az ADF, a másolási tevékenység végrehajtásakor közvetlenül megjelenik "**teljesítmény-finomhangolási tippek**" a a [másolási tevékenység lap figyelés](copy-activity-overview.md#monitor-visually) az alábbi példában látható módon. Az nem csak arra kéri, a szűk keresztmetszetet a megadott példány Futtatás azonosított, hanem is végigvezeti Önt a Mi a másolási átviteli sebesség növelése érdekében módosítsa. A teljesítmény-finomhangolási tippek jelenleg meg javaslatok, például a PolyBase használatával példatípust az adatok Azure SQL Data Warehouse-bA növelhető az Azure Cosmos DB-RU, vagy az Azure SQL DB DTU, ha az erőforrást, az adatok tárolása ügyféloldali a szűk keresztmetszetet, eltávolítja a felesleges előkészítése példány, stb. A teljesítmény-finomhangolási szabályok fog bővített fokozatosan is lehet.

    **Példa: példány az Azure SQL DB-be a teljesítmény-finomhangolási tippek**

    Ebben a példában másolási futtatja, az ADF figyelje meg, a fogadó Azure SQL DB eléri a magas DTU-használata, ami lelassítja az írási műveletek során ily módon a javaslat, hogy növelje az Azure SQL DB szintre négyszeres DTU. 

    ![Másolja a figyelés a teljesítmény-finomhangolási tippek](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Emellett az alábbiakban néhány gyakori szempontok. Teljes leírását a teljesítmény diagnosztikai van ez a cikk nem foglalkozik.

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [Adategységek-integráció](#data-integration-units)
     * [Szakaszos másolás](#staged-copy)
     * [Saját üzemeltetésű integrációs modul méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Saját üzemeltetésű integrációs modul](#considerations-for-self-hosted-integration-runtime)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálást és deszerializálást](#considerations-for-serialization-and-deserialization)
   * [A tömörítés](#considerations-for-compression)
   * [Oszlop-hozzárendelés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)

3. **Bontsa ki a konfigurációt, a teljes adatkészletet**. Amikor már elégedett a eredményeit és a teljesítményt, kibonthatja a definíció- és folyamatot, hogy biztosítsák a teljes adatkészletet.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul szempontjai

Ha a másolási tevékenység végrehajtása a helyi Integration Runtime, vegye figyelembe a következőket:

**A telepítő**: Azt javasoljuk, hogy használja-e egy dedikált gépre host Integration Runtime. Lásd: [szempontok a helyi Integration Runtime](concepts-integration-runtime.md).

**Horizontális felskálázás**: Egy egyetlen logikai integrációs modulnak egy vagy több csomóponttal rendelkező szolgálhat több másolási tevékenység fut egyszerre egy időben. Ha hibrid adatáthelyezés nagyszámú egyidejű másolási tevékenység-végrehajtás vagy nagy mennyiségű adat másolása (nagy erőforrásigényű) szükség van, érdemes lehet [horizontális felskálázási helyi Integration Runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) úgy, hogy a további erőforrások kiépítése biztosítson hatékony eszközöket másolása.

## <a name="considerations-for-the-source"></a>A forrás szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, lásd: [megfigyelés és finomhangolás témakörök](#performance-reference) , amelyek adott adattárak, és segítenek megérteni, hogy adatokat tárolni a teljesítményt nyújt, válaszidők csökkentése érdekében és átviteli teljesítmény.

* Ha az adatok másolása **az SQL Data Warehouse a Blob storage-ból**, érdemes lehet **PolyBase** teljesítményének növelése érdekében. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről.
* Ha az adatok másolása **Azure Blob/az Azure Data Lake Store a HDFS-ből**, érdemes lehet **DistCp** teljesítményének növelése érdekében. Lásd: [használja a DistCp használatával adatait átmásolhatja a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) részleteiről.
* Ha az adatok másolása **az Azure SQL Data Warehouse vagy az Azure BLob-vagy az Azure Data Lake Store redshiftből**, fontolja meg **UNLOAD** teljesítményének növelése érdekében. Lásd: [használata UNLOAD adatokat másol az Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) részleteiről.

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Átlagos méretét és a fájlszám**: A másolási tevékenység egy adatfájlt egy időben továbbítja. Az adatok áthelyezésének akkora a teljes átviteli sebesség alacsonyabb, ha néhány nagy méretű fájlok miatt a rendszer-indításkori fázis minden egyes fájl helyett a sok kis méretű fájlt tartalmaz. Ezért ha lehetséges, egyesítendő kisméretű fájlok nagyobb fájlok nagyobb átviteli sebességet biztosítanak.
* **A fájl formátuma és tömörítés**: További részleteket a teljesítmény javítása, lásd: a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokat.

### <a name="relational-data-stores"></a>Relációs adattárak

* **Adatmintát**: A következő tábla sémáját másolási átviteli sebesség hatással van. Egy nagy méretű sor mérete kis sor mérete, azonos mennyiségű adatot másolni egy jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban lekérheti kevesebb váró adatokat, amelyek kevesebb sort tartalmaznak.
* **Lekérdezés vagy tárolt eljárás**: Optimalizálhatja a lekérdezést, vagy adja meg, ha a másolási tevékenység forrásból történő adatlehívást, hatékonyabban tárolt eljárás logikáját.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, tekintse meg [megfigyelés és finomhangolás témakörök](#performance-reference) kifejezetten az adattárakban. Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

* Ha az adatok másolása **az SQL Data Warehouse a Blob storage-ból**, érdemes lehet **PolyBase** teljesítményének növelése érdekében. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről.
* Ha az adatok másolása **Azure Blob/az Azure Data Lake Store a HDFS-ből**, érdemes lehet **DistCp** teljesítményének növelése érdekében. Lásd: [használja a DistCp használatával adatait átmásolhatja a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) részleteiről.
* Ha az adatok másolása **az Azure SQL Data Warehouse vagy az Azure BLob-vagy az Azure Data Lake Store redshiftből**, fontolja meg **UNLOAD** teljesítményének növelése érdekében. Lásd: [használata UNLOAD adatokat másol az Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) részleteiről.

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Másolja a viselkedés**: Ha adatokat másolja egy másik fájl alapú adatokat az adattárból, a másolási tevékenység keresztül három pontot tartalmaz az **copyBehavior** tulajdonság. Ez a megőrzi a hierarchia, simítja egybe a hierarchia, vagy egyesíti a fájlokat. Az egybesimítás hierarchia vagy megőrzi az rendelkezik alig vagy egyáltalán nem teljesítménybeli terhelést, de a fájlok egyesítése növelése teljesítménybeli terhelést okoz.
* **A fájl formátuma és tömörítés**: Tekintse meg a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokban talál további részleteket a teljesítmény javítása.

### <a name="relational-data-stores"></a>Relációs adattárak

* **Másolja a viselkedés**: A Tulajdonságok függően beállított **sqlSink**, a másolási tevékenység adatokat ír a céladatbázis különböző módon.
  * Alapértelmezés szerint az adatok adatátviteli szolgáltatás által a tömeges másolási API-t az adatok beszúrása hozzáfűzése üzemmódot, amely a lehető legjobb teljesítményt biztosít.
  * Ha a fogadó tárolt eljárás konfigurálja, az adatbázis az adatokat egy sort egy kötegelt betöltés, hanem egyszerre vonatkozik. Teljesítmény jelentősen csökken. Ha az adatkészlet túl nagy, ha lehetséges, fontolja meg, hogy használatával a **preCopyScript** tulajdonság.
  * Ha konfigurálja a **preCopyScript** tulajdonság minden egyes másolási tevékenység futtatása, a szolgáltatás aktiválása a parancsfájl és majd illessze be az adatokat a tömeges másolási API-t használja. Például a teljes tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először törölje az összes rekordot a forrás az új adatok tömeges-betöltés előtt.
* **A minta és a batch adatméret**:
  * A következő tábla sémáját másolási átviteli sebesség hatással van. Az azonos mennyiségű adatot másolni, egy nagy méretű sor mérete ad egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti kevesebb váró adatokat.
  * A másolási tevékenység adatokat egy sorozat része kötegek szúr be. Beállíthatja a sorok számát a batch segítségével a **writeBatchSize** tulajdonság. Ha az adatok kis sorral rendelkezik, akkor lehet beállítani a **writeBatchSize** tulajdonság számára, hogy alacsonyabb batch terhelést és nagyobb átviteli sebességet a magasabb értékű. Ha az adatokat a sor mérete túl nagy, ügyeljen arra, ha növeli **writeBatchSize**. Egy magas szintű egy példány hibáját okozta. az adatbázis terhelve vezethet.

### <a name="nosql-stores"></a>NoSQL-tárolókat

* A **Table storage**:
  * **partíció**: Írja az adatokat a partíciók összefűzéses jelentősen rontja a teljesítményt. A forrásadatok rendezés partíciós kulccsal, hogy az adatokat a rendszer beszúrta hatékonyan egy partíciót egymás után, vagy módosítsa az adatokat írni az ugyanazon a partíción logikát.

## <a name="considerations-for-serialization-and-deserialization"></a>Szerializálást és deszerializálást szempontjai

Szerializálást és deszerializálást akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájlt. Lásd: [támogatott fájl- és tömörítési formátumok](supported-file-formats-and-compression-codecs.md) másolási tevékenység által támogatott fájlformátumokról részleteinek ismertetésével együtt.

**Másolja a viselkedés**:

* A fájlok másolása a fájlalapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkeznek azonos vagy nincs fájlformátum beállításai, az adatátviteli szolgáltatás végrehajt egy **bináris másolat** szerializálása vagy deszerializálása nélkül. Megjelenik egy a forgatókönyv, amelyben a forrás- és fogadó fájlformátum beállításai eltérnek egymáshoz képest nagyobb átviteli sebességet.
  * Amikor bemeneti és kimeneti adatkészletek mindkét szöveges formátum, és csak a kódolás típusát különböző, az adatok mozgását szolgáltatásnak csak nincs a kódolási átalakítás. Még nem csinál bármely szerializálási és deszerializálás, amely néhány bináris másolat többletterhelést teljesítményt okoz.
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkezik különböző fájlformátumokban vagy különböző konfigurációkat, például az elválasztó karakterek, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyam, átalakítása és majd szerializálni a kimeneti formátumba, jelzett. Ez a művelet egy sokkal jelentős teljesítménybeli többletterhelést képest más esetben eredményez.
* Ha másolja a fájlokat egy adattár, amely nem fájl alapú (például az olyan fájlalapú tároló relációs tárolóval), akkor a szerializálása vagy deszerializálása lépésre szükség. Ebben a lépésben jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A választott fájl formátumát másolási teljesítmény hatással lehetnek. Például a Avro kompakt bináris formátum, amely tárolja az adatokat metaadatok. Széles körben támogatja a Hadoop-ökoszisztéma, feldolgozása és lekérdezési rendelkezik. Azonban az Avro szerializálást és deszerializálást, ami szövegformátum képest alacsonyabb másolási átviteli sebességet eredményez a drágább. Győződjön meg azok alkalmazásfüggőségeit a kiválasztott fájlformátum teljes feldolgozási folyamat során. Kezdés milyen formában az adatokat tárolja a forrásadattárakból vagy külső rendszerek; kinyerése a storage, az analitikus feldolgozás és a lekérdezési; legjobb formátum és milyen formátumban az adatok exportálja az adatpiacok jelentéskészítő és vizualizációs eszközök, a. Néha olyan fájlformátum, amely az optimálisnál rosszabb olvasási és írási teljesítmény előfordulhat, hogy jó választás meghatározásakor a teljes elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai

Ha a bemeneti vagy kimeneti adatkészlet egy fájlt, írja az adatokat a rendeltetési tömörítés vagy a kibontás végrehajtani a másolási tevékenység állíthatja be. Ha tömörítést választja, akkor győződjön meg arról, bemeneti/kimeneti (I/O) magával és a CPU. Tömöríti a számítási erőforrásokat az extra költségek. De cserébe csökkenti a hálózati i/o- és storage. Az adatoktól függően a teljes másolási átviteli sebesség boost jelenhet meg.

**Kodek**: Minden egyes tömörítési kodeket előnnyel jár. Például bzip2 rendelkezik a legalacsonyabb másolási teljesítmény, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert azt a feldolgozáshoz is fel. A gzip a legtöbb kiegyensúlyozott beállítás használatát, és azt a leggyakrabban használt. Válassza ki a leginkább megfelelő a végpontok közötti forgatókönyv kodek.

**Szint**: Minden egyes tömörítési kodeket két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített lehetőség a lehető leggyorsabban tömöríti az adatokat, még akkor is, ha az eredményül kapott fájl nem optimális tömöríti. Az optimális tömörített beállítás több időt tölt a tömörítést, és a egy minimális mennyiségű adatot eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb általános teljesítményt abban az esetben tesztelheti.

**Veszi figyelembe**: A nagy mennyiségű adat a helyszínen és a felhő közötti másolásához, érdemes lehet [szakaszos Másolás](#staged-copy) a tömörítéssel kompatibilis. Ideiglenes tároló használata akkor hasznos, ha a sávszélesség a vállalati hálózat és az Azure-szolgáltatások korlátozzák, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlen formában.

## <a name="considerations-for-column-mapping"></a>Az oszlopleképezés szempontjai

Beállíthatja a **leképezésekben** tulajdonság a másolási tevékenység a térkép összes vagy a bemeneti oszlopok, a kimeneti oszlopok egy része. Az adatáthelyezési szolgáltatás a forrásból olvassa be az adatokat, miután kell végezni az adatokon oszlop-hozzárendelés előtt írja az adatokat a fogadó. A további feldolgozás csökkenti a másolási átviteli sebességet.

Ha a forrásadattár lekérdezhető, például ha egy relációs áruházban, például az SQL Database vagy SQL Server a, vagy ha például a Table storage vagy az Azure Cosmos DB NoSQL-alapú tárolót érdemes leküldése az oszlopok szűrésének és logika átrendezésével végezze el a **lekérdezés** tulajdonság oszlop-hozzárendelés használata helyett. Ezzel a módszerrel a leképezés, amíg az adatátviteli szolgáltatás adatokat olvas be a forrásadattár, ahol a szolgáltatás sokkal hatékonyabb.

További információkat talál a [másolási tevékenység séma hozzárendelése](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Egyéb szempontok

Ha másolni kívánt adatok mérete nagy, módosíthatja az üzleti logikát az adatok particionálására további és ütemezhetők a másolási tevékenység az ennél gyakoribb futtatáshoz, minden egyes másolási tevékenység futtatása az adatméret csökkentése érdekében.

Legyen óvatos az adatkészletek és a Data Factory csatlakozni egy időben ugyanazt az adattárat igénylő másolási tevékenységek száma. Számos egyidejű másolási feladat lehet, hogy adattár szabályozás és teljesítménycsökkenésért másolási feladat belső újrapróbálkozás, és bizonyos esetekben végrehajtási hibák vezethet.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: Másolja a helyszíni SQL Serverről a Blob storage

**A forgatókönyv**: Egy folyamat adatokat másol egy helyszíni SQL Server a Blob storage-ban CSV formátumban való használatra készült. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlokat kell tömörített, bzip2 formátumba.

**Teszt- és elemzési**: Az átviteli sebességet, a másolási tevékenység érték kisebb, mint 2 MB/s, amely sokkal lassabb, mint a teljesítmény a teljesítményteszt.

**Teljesítményelemzés és a hangolási**: A teljesítménnyel kapcsolatos problémák elhárításához, tekintsük át az adatok feldolgozása és áthelyezték.

1. **Adatok olvasása**: Az Integration runtime megnyit egy kapcsolatot az SQL Server, és elküldi a lekérdezést. Az SQL Server válaszol, ha az adatfolyamban integrációs modul az intraneten keresztül küld.
2. **Szerializálható és az adatok tömörítése**: Az Integration runtime szerializálja a CSV formátumba az adatfolyamot, és tömöríti az adatokat, bzip2 adatfolyamba.
3. **Adatok írása**: Az Integration runtime a bzip2 stream feltölti a Blob storage, az interneten keresztül.

Folyamatban van, ahogy látható, az adatok feldolgozását, és áthelyezte streamelési soros módon: Az SQL Server > LAN > az Integration runtime > WAN > Blob storage-ban. **Az általános teljesítmény a minimális átviteli engedi át a folyamat között**.

![Az adatfolyam](./media/copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhatják a teljesítménybeli szűk keresztmetszetet:

* **forrás**: Maga az SQL Server alacsony átviteli sebesség nagy terheléseket miatt van.
* **Saját üzemeltetésű integrációs modul**:
  * **HÁLÓZATI ÉBRESZTÉS**: Az Integration runtime az SQL Servert futtató gép távol található, és a egy kis sávszélességű kapcsolattal rendelkezik.
  * **Az Integration runtime**: Az Integration runtime elérte a terhelés korlátozások, a következő műveletek végrehajtásához:
    * **Szerializálási**: Lassú teljesítmény az adatfolyam CSV formátumban történő szerializálásához rendelkezik.
    * **A tömörítés**: Azt választotta, a lassú tömörítési kodek (például bzip2, amely alapvető i7 2.8-as MBps).
  * **WAN**: A vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség értéke alacsony (például a T1 1,544 kbps; = T2 = 6,312 KB/s).
* **Fogadó**: A BLOB storage alacsony átviteli sebesség tartozik. (Ebben a forgatókönyvben a nem valószínű, mert a hozzá tartozó SLA-garanciát legalább 60 MB/s.)

Ebben az esetben az adattömörítés bzip2 előfordulhat, hogy lehet lelassítja a teljes folyamat. A gzip tömörítési kodek átváltása, előfordulhat, hogy a szűk keresztmetszet megkönnyítése érdekében.

## <a name="reference"></a>Leírások

Teljesítmény figyelése és hangolása hivatkozásokat a támogatott adattárak némelyikét a következő:

* Az Azure Storage (beleértve a Blob storage és Table storage): [Az Azure Storage skálázhatósági célértékét](../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya
* Azure SQL Data Warehouse: A funkció mérik az adattárházegységek (Dwu); Lásd: [kezelés számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Az Azure Cosmos DB: [Az Azure Cosmos DB teljesítményszintek](../cosmos-db/performance-levels.md)
* A helyszíni SQL Server: [Figyelése és a teljesítmény hangolása](https://msdn.microsoft.com/library/ms189081.aspx)
* A helyi fájlkiszolgáló: [Teljesítmény-finomhangolási fájlkiszolgálók](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>További lépések
A másolási tevékenység egyéb cikkekben talál:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolási tevékenység séma hozzárendelése](copy-activity-schema-and-type-mapping.md)
- [Másolja ki a tevékenység a hibatűrés](copy-activity-fault-tolerance.md)
