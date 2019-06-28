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
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 3ea89e9f6a6bb8a4c377c70bbe1b5540d3b74d44
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341249"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolási tevékenységek teljesítményéhez és finomhangolási útmutató
> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory használt verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)


Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást kínál. Használhatja a másolatát tíz terabájt adat minden nap gazdag számos felhőalapú és helyszíni adattárakba. A gyors adatbetöltés teljesítmény fontos győződjön meg arról, hogy a big Data típusú adatok alapvető probléma összpontosíthat: fejlett elemzési megoldásokat hozhat, és részletes elemzések lekérése az adatokból.

Az Azure biztosít a nagyvállalati adattárolás és adattárház-megoldások. A másolási tevékenység magas szinten optimalizált Adatbetöltési könnyen konfigurálható, és állítsa be a élményt nyújt. Egy másolási tevékenységgel rendelkező betöltheti az adatokat:

* Az Azure SQL Data Warehouse 1,2 GB/s.
* Az Azure Blob-tárolóból 1,0 GB/s.
* Az Azure Data Lake Store, az 1,0 GB/s.

Ez a cikk ismerteti:

* [Hivatkozás teljesítményszámokhoz](#performance-reference) támogatott forrás- és fogadó adattárak a projekt tervezéséhez nyújtanak segítséget.
* Az funkciók, amely képes javítani a másolási átviteli különböző helyzetekben, amely tartalmazza [integrációs adategységek](#data-integration-units) (DIUs), [másolási párhuzamos](#parallel-copy), és [szakaszos Másolás](#staged-copy).
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) a hangolását a teljesítmény és a főbb tényezőket, amelyek hatással lehetnek a másolási teljesítmény.

> [!NOTE]
> Ha nem ismeri a másolási tevékenység általános, tekintse meg a [másolási tevékenység áttekintése](copy-activity-overview.md) Ez a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-referencia

Hivatkozásként van listázva az alábbi táblázat számát jeleníti meg a másolási teljesítmény (Mbps) a megadott forrás, és futtatni egy másolási tevékenység fogadó párok alapján belső tesztelése. Az összehasonlítást, azt is bemutatjuk, hogyan különböző beállításait [integrációs adategységek](#data-integration-units) vagy [saját üzemeltetésű integrációs modul méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime) (több csomópont) segítségével a másolási teljesítményét.

![Teljesítmény mátrix](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> A másolási tevékenység fut egy Azure-beli integrációs modul, a minimális megengedett integrációs adategységek (korábbi nevén adatáthelyezési egységek) esetén két. Ha nincs megadva, tekintse meg az alapértelmezett integrációs adategységek van használatban a [integrációs adategységek](#data-integration-units).

**Tudnivalók:**

* Átviteli sebesség kiszámításához a rendszer az alábbi képlettel: [forrás adatsorból beolvasott adatok mérete] / [másolása tevékenység Futtatás időtartama].
* A tábla hivatkozási teljesítményszámokhoz használatával lettek mért egy [TPC-H](http://www.tpc.org/tpch/) adatkészlet egy másolási tevékenység futtatása. A fájlalapú tárolók tesztfájlok, 10 GB-nál több fájlt.
* Az Azure-beli adat-áruházaiban nagyban forrás- és fogadóadattárként is, azonos Azure-régióban.
* A helyszíni és felhő közötti hibrid másolás adattárak, mindegyik a saját üzemeltetésű integration runtime csomópontja futott olyan számítógépen, amelyen az adatokat az adattárból a következő Specification elkülönített. Egyetlen tevékenység futtatásakor a rendszer a másolási művelet csak egy részét a tesztgép CPU, memória és hálózati sávszélességet használja fel.
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
> Nagyobb átviteli sebességet érhet el, további DIUs használatával. Például 100 DIUs másolhat adatokat az Azure Blob storage-ból az Azure Data Lake Store, az 1,0 GB/s. Ez a szolgáltatás és a támogatott forgatókönyv kapcsolatos további információkért lásd: a [integrációs adategységek](#data-integration-units) szakaszban. 

## <a name="data-integration-units"></a>Adategységek-integráció

Egy adategység-integráció a (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációjával) hatékonyságát az Azure Data Factory egy egységet mérték. Adategység-integráció csak érvényes [Azure integrációs modul](concepts-integration-runtime.md#azure-integration-runtime), de nem [saját üzemeltetésű integrációs modul](concepts-integration-runtime.md#self-hosted-integration-runtime).

A minimális DIUs megjelenő új egy másolási tevékenység futtatása két. Ha nincs megadva, a következő táblázat felsorolja a különböző másolási forgatókönyvek esetén a használt alapértelmezett DIUs:

| Másolja ki a forgatókönyv | Szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | 4\. és a fájlok mérete és száma 32 között |
| Egyéb másolási forgatókönyvek esetén | 4 |

Ez az alapértelmezett felülbírálásához, adjon meg értéket a **dataIntegrationUnits** tulajdonság az alábbiak szerint. A *megengedett értékek* számára a **dataIntegrationUnits** tulajdonság legfeljebb 256. A *DIUs tényleges számát* egyenlő vagy kisebb, mint a beállított érték, a adatmintát attól függően, hogy a másolási művelet használ a futási időben. További információ a teljesítmény szintjét egy adott másolási forrásaként és fogadó további egységek konfigurálásakor kaphat,: a [teljesítményfigyelési](#performance-reference).

Láthatja, hogy a DIUs minden egyes példányra, futtassa a másolási tevékenység kimenete egy tevékenységfuttatás figyelése esetén használja. További információkért lásd: [másolása figyelését](copy-activity-overview.md#monitoring).

> [!NOTE]
> Beállításaként DIUs négy jelenleg érvényes csak akkor, ha több fájlt másolja ki az Azure Storage, Azure Data Lake Storage, az Amazon S3, Google Cloud Storage-nál nagyobb, a felhő FTP, vagy bármely más felhőalapú adattárak SFTP felhő.
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

Ne feledje, hogy Ön már alapján történik a számlázás a másolási művelet teljes időtartama. Adatok áthelyezése a teljes időtartama a számlázás DIUs időtartama összege esetében. Ha most már nyolc felhőbeli egységgel 15 percet vesz igénybe, és egy másolási feladat egy órával érvénybe két felhőbeli egységgel használják, akkor a teljes számlája összegét szinte változatlan marad.

## <a name="parallel-copy"></a>Párhuzamos másolása

Használhatja a **parallelCopies** tulajdonság, amely jelzi, amelyeket szeretne használni a másolási tevékenység párhuzamosságát. Ez a tulajdonság belül a másolási tevékenység, amely a forrásból olvashatja vagy írhatja, a fogadó adattárakba párhuzamos szálak maximális számának is felfoghatók.

Minden egyes másolási tevékenység futtatási az Azure Data Factory használatával adatokat másol a forrásadattárból adatokat tárolja, és a cél-adatok tárolására párhuzamos példányszám határozza meg. Az alapértelmezett példányszámot párhuzamos, amelyet használ a forrás- és fogadó használt függ.

| Másolja ki a forgatókönyv | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók között |A fájlok és a használt két felhőalapú adattárolók, vagy a saját üzemeltetésű integrációs modult tartalmazó számítógépen fizikai konfigurációját közötti adatokat másolja DIUs méretétől függ. |
| Adatok másolása az összes forrásadattár az Azure Table storage |4 |
| Egyéb másolási forgatókönyvek esetén |1 |

> [!TIP]
> Amikor a fájlalapú tárolók közötti adatokat másolja, alapértelmezés szerint általában lehetővé teszi a legjobb teljesítményt. Alapértelmezés szerint az automatikus határozza meg.

Szabályozhatja, hogy az adatok üzemeltető gépek terhelését tárolja, vagy másolási teljesítmény hangolására, felülbírálhatja az alapértelmezett értéket, és adjon meg egy értéket a **parallelCopies** tulajdonság. Az érték nagyobb vagy egyenlő 1 egész számnak kell lennie. Futási időben, a legjobb teljesítmény érdekében a másolási tevékenység használja, amely kisebb vagy egyenlő az érték, Ön által beállított értéket.

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

**Tudnivalók:**

* Másolt adatok közötti fájlalapú tárolók **parallelCopies** határozza meg, a párhuzamosság a fájlok szintjén. Egyetlen fájl belül darabolás történik alatt automatikusan és transzparens módon. Úgy tervezték, hogy a legjobban megfelelő adattömbök egy adott tároló adattípusra betölteni az adatok párhuzamos mérete és a merőleges használandó **parallelCopies**. Az adatátviteli szolgáltatást használja a másolási művelet futási időben párhuzamos másolatok tényleges száma nem több, mint a fájlok száma nem. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájlszintű foka.
* Ha ad meg értéket a **parallelCopies** tulajdonság, vegye fontolóra a terhelés megnövekedése a forrás és fogadó-adattárakban. A saját üzemeltetésű integrációs modulban a terhelés megnövekedése is vegye figyelembe, ha a másolási tevékenység jogosult arra, például a hibrid másolás. A terhelés megnövekedése akkor fordul elő, különösen ha rendelkezik több egyidejű futtatásainak ugyanazokat a tevékenységeket, amelyek ugyanabban az adattárban futtatásához vagy tevékenységeket. Ha azt tapasztalja, hogy az adattár vagy a saját üzemeltetésű integrációs modult a terhelés túlterhelte, csökkentheti a **parallelCopies** érték a terhelés alól.
* Másolt adatok, amelyek nem fájlalapú, amelyek a fájlalapú áruházak áruházakból származó, az adatátviteli szolgáltatás figyelmen kívül hagyja-e a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.
* A **parallelCopies** tulajdonság a merőleges **dataIntegrationUnits**. Az előbbi akkor számít az adatok integrációs összes száma közötti.

## <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

- **Szeretné betölteni az különféle adattárakból származó adatokat az SQL Data Warehouse polybase.** Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. A forrásadatok a Blob storage vagy az Azure Data Lake Store kell lennie, és annak meg kell felelnie a további feltételek. Ha egy adattár eltérő a Blob storage vagy az Azure Data Lake Store tölt be adatokat, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben az Azure Data Factory, győződjön meg arról, hogy megfelel-e a PolyBase követelményeinek szükséges adatátalakítások hajt végre. A PolyBase majd az adatok betöltése az SQL Data Warehouse-bA hatékonyan használja. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Is eltarthat egy ideig a hibrid adatmozgás végrehajtásához (azaz másol egy helyszíni adattárak egy felhőalapú adattár) a lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos Másolás segítségével tömörítse az adatokat a helyszíni, hogy az adatok áthelyezése a felhőben az átmeneti adattár kevesebb időt vesz igénybe. Ezután az átmeneti tárnak lévő adatok kibontása, előtt töltse be a célként megadott adattárba be.
- **Nyissa meg a 80-as portot és a 443-astól különböző portok a tűzfalon a vállalati informatikai házirend miatt nem szeretné.** Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben a forgatókönyvben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs modul először másolhat adatokat egy Blob Storage-példány átmeneti HTTP vagy HTTPS a 443-as porton. Majd azt is az adatok betöltése az SQL Database vagy az SQL Data Warehouse a Blob storage emeli ki. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése

Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti tárolási Blob storage (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. Az Azure Data Factory automatikusan kezeli az Ön számára a két szakaszból álló folyamatot. Az Azure Data Factory is megtisztítja az átmeneti tárolási ideiglenes adatokat, az adatáthelyezés befejeződése után.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Adatok áthelyezése az átmeneti tárolási obchodu aktiválásakor is megadhat kívánja-e a forrásadatok adatok áthelyezése előtt tömöríti az adatokat egy átmeneti tárolásához, illetve átmeneti adatok tárolására, és ezután kibontása egy ideiglenes vagy átmeneti dat származó adatok áthelyezése előtt egy tároló, a fogadó adattárba.

Jelenleg nem másolhat adatokat egy átmeneti tárnak használatával két helyszíni adattárak között.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** a másolási tevékenység a beállítás adja meg, hogy az adatokat Blob storage-ban a célként megadott adattárba történő betöltésük előtt átmenetileg tárolva. Ha **enableStaging** való `TRUE`, adja meg az alábbi táblázatban felsorolt további tulajdonságokat. Is szeretne létrehozni egy Azure Storage vagy a Storage közös hozzáférési aláírás-beli társított szolgáltatás átmeneti, ha nem rendelkezik ilyennel.

| Tulajdonság | Leírás | Alapértelmezett érték | Szükséges |
| --- | --- | --- | --- |
| enableStaging |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| linkedServiceName |Adja meg a nevét egy [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) társított szolgáltatás, amely hivatkozik a példány tárolási megoldás, amely egy átmeneti előkészítési tárolót használunk. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló közös hozzáférésű jogosultságkód nem használható. Más esetekben használhatja azt. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| elérési út |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás ideiglenes adatokat tárolni egy tárolót hoz létre. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |– |Nem |
| enableCompression |Itt adhatja meg, hogy adatok tömöríti-e, mielőtt azt a cél. Ez a beállítás átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

>[!NOTE]
> Ha szakaszos másolás használata a tömörítés engedélyezve van, az egyszerű szolgáltatás, vagy az átmeneti tárolási társított blob szolgáltatás MSI-hitelesítés nem támogatott.

Íme egy példa definíciója egy másolási tevékenység az előző táblázatban leírt tulajdonságokkal:

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

Díjkötelesek alapján két lépést: másolás időtartama, és másolja a típusát.

* Használata esetén átmeneti során a felhőbe történő másolás, amely adatokat másol egy felhőalapú adattárból egy másik felhőalapú adattárba, mindkét Azure integrációs modul által felhatalmazott szakaszában, a [összege másolás időtartama az 1 és 2. lépés] x [felhőalapú másolási Egységár] díjkötelesek.
* Használatakor átmeneti hibrid másolatot, az adatok másolása a helyszíni adattárolókból egy felhőalapú adattár, amely során egy lépésben hajtaná egy saját üzemeltetésű integrációs modul által felhatalmazott, díjkötelesek [hibrid másolás időtartama] x [hibrid másolás Egységár] + [felhőbeli másolás időtartama] [a cloud másolási Egységár] x.

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

Ezeket a lépéseket a másolási tevékenységgel az Azure Data Factory szolgáltatás teljesítményét.

1. **Megállapítja az alapértékeket.** A fejlesztési fázis során a folyamat tesztelése a másolási tevékenység elleni egy reprezentatív minta használatával. Végrehajtási részleteit és a következő teljesítményjellemzők gyűjtése [másolása figyelését](copy-activity-overview.md#monitoring).

2. **Diagnosztika és optimalizálhatja a teljesítményt.** Ha a teljesítmény megfigyelte sem felel meg az elvárásainak, azonosíthatja a szűk keresztmetszeteket. Távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében a teljesítmény optimalizálásával majd.

    Bizonyos esetekben az Azure Data Factory, a másolási tevékenység futtatásakor "Teljesítményhangolás tippek" üzenetet látja a a [másolási tevékenység lap figyelés](copy-activity-overview.md#monitor-visually), az alábbi példában látható módon. Az üzenet közli Önnel, a szűk keresztmetszetet a megadott példány Futtatás azonosított. Azt is megmutatja, hogyan lehet Mi a boost másolási átviteli sebesség módosítása. A teljesítmény-finomhangolási tippek jelenleg meg javaslatok, például:

    - Adatmásolás az Azure SQL Data Warehouse-bA a PolyBase használata
    - Ha az erőforrás adatait tároló oldalán a szűk keresztmetszetet, növelje az Azure Cosmos DB kérelemegység vagy az Azure SQL Database Dtu (adatbázis-kapacitásegységek).
    - Távolítsa el a felesleges szakaszos másolás.

    A teljesítmény-finomhangolási szabályok fog bővített fokozatosan is lehet.

    **Példa: Teljesítmény-finomhangolási tippek az Azure SQL Database-be másolni**

    Ebben a példában futtatja, a másolás során az Azure Data Factory megjegyzések a fogadó Azure SQL Database eléri a magas DTU-használata, ami lelassítja az írási műveletek. A javaslat, hogy az Azure SQL Database-szintre további Dtu-val növelheti. 

    ![Másolja a figyelés a teljesítmény-finomhangolási tippek](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Emellett az alábbiakban néhány gyakori szempontok. Teljes leírását a teljesítmény diagnosztikai van ez a cikk nem foglalkozik.

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [Adatintegrációs egységek](#data-integration-units)
     * [Szakaszos másolás](#staged-copy)
     * [Saját üzemeltetésű integrációs modul méretezhetőség](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Helyi integrációs modul](#considerations-for-self-hosted-integration-runtime)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálást és deszerializálást](#considerations-for-serialization-and-deserialization)
   * [A tömörítés](#considerations-for-compression)
   * [Oszlop-hozzárendelés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)

3. **Bontsa ki a konfigurációt a teljes adatkészletet.** Ha elégedett a eredményeit és a teljesítmény, a definíció- és ahhoz, hogy biztosítsák a teljes adatkészlet folyamat kibontásával.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul szempontjai

Ha a másolási tevékenység fut egy saját üzemeltetésű integrációs modult, vegye figyelembe a következőket:

**A telepítő**: Azt javasoljuk, hogy a gazdagép integrációs modul egy dedikált gépre használja. Lásd: [szempontok a saját üzemeltetésű integrációs modul](concepts-integration-runtime.md).

**Horizontális felskálázás**: Egy egyetlen logikai integrációs modul egy vagy több csomóponttal rendelkező egyszerre egyidejűleg több másolási tevékenység-végrehajtás szolgálhat ki. Ha hibrid adatáthelyezés nagyszámú egyidejű másolási tevékenység-végrehajtás vagy egy nagy mennyiségű adat másolása (nagy erőforrásigényű) szükség van, érdemes lehet [horizontális felskálázás a saját üzemeltetésű integrációs modul](create-self-hosted-integration-runtime.md#high-availability-and-scalability) a további erőforrások kiépítése biztosítson hatékony eszközöket másolása.

## <a name="considerations-for-the-source"></a>A forrás szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár túl nem sok egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, lásd: [megfigyelés és finomhangolás témakörök](#performance-reference) kifejezetten az adattárakban. Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

* Másolt adatok Blob storage-ból az SQL Data warehouse-ba, fontolja meg, a PolyBase a teljesítmény növelése érdekében. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Ha a HDFS adatokat másol az Azure Blob storage vagy az Azure Data Lake Store, fontolja meg, a DistCp használata a teljesítmény növelése érdekében. További információkért lásd: [használja a DistCp használatával adatait átmásolhatja a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Redshift adatokat másol az Azure SQL Data Warehouse, Azure BLob storage vagy az Azure Data Lake Store használata javasolt UNLOAD teljesítményének növelése érdekében. További információkért lásd: [használata UNLOAD adatokat másol az Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Átlagos méretét és a fájlszám**: A másolási tevékenység továbbítja egy adatfájlt egy időben. Az adatok áthelyezésének akkora a teljes átviteli sebesség alacsonyabb, ha néhány nagy méretű fájlok miatt a rendszer-indításkori fázis minden egyes fájl helyett a sok kis méretű fájlt tartalmaz. Ha lehetséges kisméretű fájlok egyesítése nagyobb fájlok nagyobb átviteli sebességet biztosítanak.
* **A fájl formátuma és tömörítés**: További részleteket a teljesítmény javítása, lásd: a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokat.

### <a name="relational-data-stores"></a>Relációs adattárak

* **Adatmintát**: A következő tábla sémáját másolási átviteli sebesség hatással van. Egy nagy méretű sor mérete, mint az azonos mennyiségű adatot másolni egy kis sor mérete jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban lekérheti kevesebb váró adatokat, amelyek kevesebb sort tartalmaznak.
* **Lekérdezés vagy tárolt eljárás**: Optimalizálja a lekérdezés vagy tárolt eljárás, adja meg, ha a másolási tevékenység forrásból történő adatlehívást, hatékonyabban logikáját.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai

### <a name="general"></a>Általános kérdések

Győződjön meg arról, hogy az alapul szolgáló adattár túl nem sok egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, lásd: [megfigyelés és finomhangolás témakörök](#performance-reference) kifejezetten az adattárakban. Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

* Ha bármely adattár adatokat másol az Azure SQL Data Warehouse, fontolja meg, a PolyBase a teljesítmény növelése érdekében. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Ha a HDFS adatokat másol az Azure Blob storage vagy az Azure Data Lake Store, fontolja meg, a DistCp használata a teljesítmény növelése érdekében. További információkért lásd: [használja a DistCp használatával adatait átmásolhatja a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Redshift adatokat másol az Azure SQL Data Warehouse, Azure Blob storage vagy az Azure Data Lake Store használata javasolt UNLOAD teljesítményének növelése érdekében. További információkért lásd: [használata UNLOAD adatokat másol az Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Másolja a viselkedés**: Ha adatokat másolja egy másik fájl alapú adatokat az adattárból, a másolási tevékenység használatával három pontot tartalmaz-e a **copyBehavior** tulajdonság. Ez a megőrzi a hierarchia, simítja egybe a hierarchia, vagy egyesíti a fájlokat. Az egybesimítás hierarchia vagy megőrzi az rendelkezik alig vagy egyáltalán nem teljesítménybeli terhelést, de a fájlok egyesítése növelése teljesítménybeli terhelést okoz.
* **A fájl formátuma és tömörítés**: További részleteket a teljesítmény javítása, lásd: a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokat.

### <a name="relational-data-stores"></a>Relációs adattárak

* **Másolja ki a viselkedést, és a teljesítmény utalás**: Eltérő módon írja az adatokat, egy SQL-fogadó. További információkat talál a [ajánlott eljárás az adatok betöltéséhez az Azure SQL Database-be](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **A minta és a batch adatméret**:
  * A következő tábla sémáját másolási átviteli sebesség hatással van. Az azonos mennyiségű adatot másolni, egy nagy méretű sor mérete ad egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti kevesebb váró adatokat.
  * A másolási tevékenység adatokat egy sorozat része kötegek szúr be. Beállíthatja a sorok számát a batch segítségével a **writeBatchSize** tulajdonság. Ha az adatok kis sorral rendelkezik, akkor lehet beállítani a **writeBatchSize** tulajdonság számára, hogy alacsonyabb batch terhelést és nagyobb átviteli sebességet a magasabb értékű. Ha az adatokat a sor mérete túl nagy, ügyeljen arra, ha növeli **writeBatchSize**. Egy magas szintű egy példány hibáját okozta. az adatbázis terhelve vezethet.

### <a name="nosql-stores"></a>NoSQL-tárolókat

* A **Table storage**:
  * **partíció**: Írja az adatokat a partíciók összefűzéses jelentősen rontja a teljesítményt. A forrásadatok rendezés a partíciókulcsot, hogy az adatokat a rendszer beszúrta hatékonyan egy partíciót egymás után. Vagy módosíthatja az adatokat írni az ugyanazon a partíción logikát.

## <a name="considerations-for-serialization-and-deserialization"></a>Szerializálást és deszerializálást szempontjai

Szerializálást és deszerializálást akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájlt. Másolási tevékenység által támogatott fájlformátumokról szóló további információkért lásd: [támogatott fájl- és tömörítési formátumok](supported-file-formats-and-compression-codecs.md).

**Másolja a viselkedés**:

* A fájlok másolása a fájlalapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkeznek azonos vagy nincs fájlformátum beállításai, az adatátviteli szolgáltatás végrehajt egy *bináris másolat* szerializálása vagy deszerializálása nélkül. Megjelenik egy a forgatókönyv, amelyben a forrás- és fogadó fájlformátum beállításai eltérnek egymáshoz képest nagyobb átviteli sebességet.
  * Amikor bemeneti és kimeneti adatkészletek mindkét szöveges formátum, és csak a kódolás típusát különböző, az adatok mozgását szolgáltatásnak csak nincs a kódolási átalakítás. Még nem csinál bármely szerializálási és deszerializálás, amely néhány bináris másolat többletterhelést teljesítményt okoz.
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkezik különböző fájlformátumokban vagy különböző konfigurációkat, például az elválasztó karakterek, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyam, átalakítása és majd szerializálni a kimeneti formátumba, jelzett. Ez a művelet egy sokkal jelentős teljesítménybeli többletterhelést képest más esetben eredményez.
* Fájlok másolása, vagy nem fájl alapú, például egy relációs áruházban, fájlalapú tároló adattárból a szerializálása vagy deszerializálása lépésre szükség. Ebben a lépésben jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A választott fájl formátumát másolási teljesítmény hatással lehetnek. Például a Avro kompakt bináris formátum, amely tárolja az adatokat metaadatok. Széles körben támogatja a Hadoop-ökoszisztéma, feldolgozása és lekérdezési rendelkezik. Az Avro drágább a szerializálást és deszerializálást, ami szövegformátum képest alacsonyabb másolási átviteli sebességet eredményez. 

Győződjön meg azok alkalmazásfüggőségeit a kiválasztott fájlformátum teljes feldolgozási folyamat során. Indítsa el:

- Milyen formában az adatokat tárolja a forrásadattárakból vagy külső rendszerekből a frissítőcsomagot.
- A storage, elemzésfeldolgozási és lekérdezése számára legjobb formátumot.
- Milyen formátumban az adatokat kell kell exportált adatpiacainak jelentéskészítő és vizualizációs eszközök számára.

Néha olyan fájlformátum, amely az optimálisnál rosszabb olvasási és írási teljesítmény előfordulhat, hogy jó választás meghatározásakor a teljes elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai

Ha a bemeneti vagy kimeneti adatkészlet egy fájlt, a másolási tevékenység a tömörítés vagy a kibontás végrehajtásához, írja az adatokat a rendeltetési állíthatja be. Ha tömörítést választja, akkor győződjön meg arról, bemeneti/kimeneti (I/O) magával és a CPU. Tömöríti a számítási erőforrásokat az extra költségek. De cserébe csökkenti a hálózati i/o- és storage. Az adatoktól függően a teljes másolási átviteli sebesség boost merülhetnek fel.

**Kodek**: Minden egyes tömörítési kodeket előnnyel jár. Például bzip2 rendelkezik a legalacsonyabb másolási teljesítmény, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert azt a feldolgozáshoz is fel. A gzip a legtöbb kiegyensúlyozott beállítás használatát, és általában a leggyakrabban használt. Válassza ki a leginkább megfelelő a végpontok közötti forgatókönyv kodek.

**Szint**: Minden egyes tömörítési kodeket két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített lehetőség a lehető leggyorsabban tömöríti az adatokat, még akkor is, ha az eredményül kapott fájl nem optimális tömörítve. Az optimális tömörített beállítás több időt tölt a tömörítést, és a egy minimális mennyiségű adatot eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb általános teljesítményt abban az esetben tesztelheti.

**Veszi figyelembe**: A nagy mennyiségű adat a helyszínen és a felhő közötti másolásához, érdemes lehet [szakaszos Másolás](#staged-copy) a tömörítéssel kompatibilis. Ideiglenes tároló használata akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások sávszélessége korlátozzák, és szeretné, a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlen formában.

## <a name="considerations-for-column-mapping"></a>Az oszlopleképezés szempontjai

Beállíthatja a **leképezésekben** tulajdonság a térkép összes vagy a bemeneti oszlopok, a kimeneti oszlopok egy része egy másolási tevékenység. Az adatáthelyezési szolgáltatás a forrásból olvassa be az adatokat, miután kell végezni az adatokon oszlop-hozzárendelés előtt írja az adatokat a fogadó. A további feldolgozás csökkenti a másolási átviteli sebességet.

Ha a forrásadattár lekérdezhető, például ha egy relációs áruházban, például az SQL Database vagy SQL Server a, vagy ha például a Table storage vagy az Azure Cosmos DB NoSQL-alapú tárolót érdemes leküldése az oszlopok szűrésének és logika átrendezésével végezze el a **lekérdezés** tulajdonság oszlop-hozzárendelés használata helyett. Ezzel a módszerrel a leképezés, amíg az adatátviteli szolgáltatás adatokat olvas be a forrásadattár, ahol a szolgáltatás sokkal hatékonyabb.

További információkat talál a [másolása tevékenység séma hozzárendelése](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Egyéb szempontok

Ha másolandó adatok mérete nagy, módosíthatja az üzleti logikát az adatok további partícióra. A másolási tevékenység minden egyes másolási tevékenység, amely futtatja a adatméret csökkentése érdekében gyakoribb futásra ütemezheti.

Legyen óvatos a számot, az adatkészletek, és másolja az Azure Data Factory csatlakozni egy időben ugyanazt az adattárat igénylő tevékenységek. Számos egyidejű másolási feladat lehet, hogy adattár szabályozás és teljesítménycsökkenésért másolási feladat belső újrapróbálkozás, és bizonyos esetekben végrehajtási hibák vezethet.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: Másolja a helyszíni SQL Serverről a Blob storage

**A forgatókönyv**: Egy folyamat adatokat másol egy helyszíni SQL server a Blob storage-ban CSV formátumban való használatra készült. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlokat kell tömörített, bzip2 formátumba.

**Teszt- és elemzési**: Az átviteli sebességet, a másolási tevékenység érték kisebb, mint 2 MB/s, amely sokkal lassabb, mint a teljesítmény a teljesítményteszt.

**Teljesítményelemzés és a hangolási**: A teljesítménnyel kapcsolatos problémák elhárításához, tekintsük át az adatok feldolgozása és áthelyezték.

- **Adatok olvasása**: Az integrációs modul megnyit egy kapcsolatot az SQL Server, és elküldi a lekérdezést. SQL-kiszolgáló válaszol, ha az adatfolyamban az integrációs modul az intraneten keresztül küld.
- **Szerializálható és az adatok tömörítése**: Az integrációs modul szerializálja a CSV formátumba az adatfolyamot, és tömöríti az adatokat, bzip2 adatfolyamba.
- **Adatok írása**: Az integrációs modul a bzip2 stream feltölti a Blob storage, az interneten keresztül.

Amint látható, az adatok feldolgozását és áthelyezett streamelési soros módon: Az SQL Server > LAN > az Integration runtime > WAN > Blob storage-ban. Az általános teljesítmény engedi át a minimális átviteli sebesség a folyamat között.

![Az adatfolyam](./media/copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhatják a teljesítménybeli szűk keresztmetszetet:

* **forrás**: Maga az SQL Server alacsony átviteli sebesség nagy terheléseket miatt van.
* **Saját üzemeltetésű integrációs modul**:
  * **HÁLÓZATI ÉBRESZTÉS**: Az Integration runtime az SQL Servert futtató gép távol található, és a egy kis sávszélességű kapcsolattal rendelkezik.
  * **Az Integration runtime**: Az Integration runtime elérte a terhelés korlátozások, a következő műveletek végrehajtásához:
    * **Szerializálási**: Lassú teljesítmény az adatfolyam CSV formátumban történő szerializálásához rendelkezik.
    * **A tömörítés**: Egy lassú tömörítési kodeket, például bzip2 választotta, ez a alapvető i7 2.8-as MBps.
  * **WAN**: A sávszélesség a vállalati hálózat és az Azure-szolgáltatások között, például a T1 1,544 kbps; = T2 6,312 KB/s =.
* **Fogadó**: A BLOB storage alacsony átviteli sebesség tartozik. Ebben a forgatókönyvben nem valószínű, mert a szolgáltatásiszint-szerződés (SLA) legalább 60 MB/s garantálja.

Ebben az esetben az adattömörítés bzip2 előfordulhat, hogy lehet lelassítja a teljes folyamat. A gzip tömörítési kodek átváltása, előfordulhat, hogy a szűk keresztmetszet megkönnyítése érdekében.

## <a name="references"></a>Referencia

Teljesítmény figyelése és hangolása hivatkozásokat a támogatott adattárak némelyikét a következők:

* Az Azure Storage, amely tartalmazza a Blob storage és Table storage: [Az Azure Storage skálázhatósági célértékét](../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya.
* Azure SQL Data Warehouse: A képesség az Adattárházegységek (Dwu) mérjük. Lásd: [kezelés számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Az Azure Cosmos DB: [Az Azure Cosmos DB teljesítményszintjeivel](../cosmos-db/performance-levels.md).
* A helyszíni SQL Server: [Figyelése és a teljesítmény hangolása](https://msdn.microsoft.com/library/ms189081.aspx).
* A helyi fájlkiszolgáló: [A fájlkiszolgálók teljesítményhangolás](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>További lépések
Az egyéb másolási tevékenység cikkekben talál:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolási tevékenység séma hozzárendelése](copy-activity-schema-and-type-mapping.md)
- [Másolja ki a tevékenység a hibatűrés](copy-activity-fault-tolerance.md)
