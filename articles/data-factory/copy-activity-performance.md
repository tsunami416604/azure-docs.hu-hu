---
title: Másolási tevékenység teljesítményének és finomhangolásának útmutatója Azure Data Factoryban | Microsoft Docs
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére Azure Data Factory a másolási tevékenység használatakor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967363"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolási tevékenység teljesítményének és finomhangolásának útmutatója
> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)


A Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű betöltési megoldást biztosít. A használatával naponta több tízezer adat másolását végezheti el a felhőben és a helyszíni adattárakban. A gyors betöltési teljesítmény kulcsfontosságú annak biztosítása érdekében, hogy a központi Big Data problémára koncentráljon: fejlett analitikai megoldások létrehozása és az összes adat részletes elemzésének megkezdése.

Az Azure nagyvállalati szintű adattárolási és adattárház-megoldásokat kínál. A másolási tevékenység egy könnyen konfigurálható és beállítható betöltési élményt nyújt. Egyetlen másolási tevékenységgel az alábbiakat töltheti be:

* Azure SQL Data Warehouse 1,2 GB/s.
* Azure Blob Storage 1,0 GB/s-nál.
* Azure Data Lake Store 1,0 GB/s.

Ez a cikk ismerteti:

* A támogatott forrás-és fogadó adattárak [teljesítmény-hivatkozási számai](#performance-reference) , amelyek segítségével megtervezheti a projektet.
* Azok a funkciók, amelyek növelhetik a másolási sebességet különböző forgatókönyvekben, beleértve az adatintegrációs [egységeket](#data-integration-units) (DIUs), a [párhuzamos másolást](#parallel-copy)és a [Lépcsőzetes másolást](#staged-copy).
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) a teljesítmény finomhangolásához és a másolási teljesítményt befolyásoló fő tényezőkhöz.

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md) a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-referencia

A következő táblázat a másolási átviteli sebesség (MB/s) értékeit mutatja a megadott forrás-és fogadó párok esetében egy másolási tevékenységben a házon belüli tesztelés alapján. Az összehasonlításban azt is bemutatjuk, hogy az [adatintegrációs egységek](#data-integration-units) és a saját üzemeltetésű integrációs modul [skálázhatósága](concepts-integration-runtime.md#self-hosted-integration-runtime) (több csomópont) milyen különböző beállításai segíthetnek a másolási teljesítményben.

![Teljesítmény mátrix](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Ha a másolási tevékenység egy Azure Integration Runtime-on fut, a minimálisan engedélyezett adatintegrációs egységek (korábbi nevén adatáthelyezési egységek) kettő. Ha nincs megadva, tekintse meg az adatintegrációs egységekben [](#data-integration-units)használt alapértelmezett adatintegrációs egységeket.

**Megjegyzés:**

* Az átviteli sebesség kiszámítása a következő képlettel történik: [a forrásból beolvasott adatok mérete és a másolási tevékenység futási időtartama].
* A táblázatban lévő teljesítménymutató-számokat egy másolási tevékenységben lévő [TPC-H](http://www.tpc.org/tpch/) adatkészlet használatával mérjük. A fájl alapú tárolók fájljainak tesztelése több, 10 GB méretű fájl.
* Az Azure-beli adat-áruházaiban nagyban forrás- és fogadóadattárként is, azonos Azure-régióban.
* A helyszíni és a Felhőbeli adattárolók közötti hibrid másoláshoz a saját üzemeltetésű integrációs modul minden csomópontja olyan gépen futott, amely az adattártól elkülönített, a következő specifikációval. Egyetlen tevékenység futtatásakor a rendszer a másolási művelet csak egy részét a tesztgép CPU, memória és hálózati sávszélességet használja fel.
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
        <td>Internetes adapter: 10 GB/s; intranetes csatoló: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Nagyobb átviteli sebességet érhet el további DIUs használatával. Például az 100 DIUs-mel az Azure Blob Storage-ból másolhatja az adatait az Azure Data Lake Storeba 1,0 GB/s-ra. További információ erről a szolgáltatásról és a támogatott forgatókönyvről: adatintegrációs [egységek](#data-integration-units) szakasz. 

## <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely a Azure Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. Az adatintegrációs egység csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)szolgáltatásra vonatkozik, a [saját](concepts-integration-runtime.md#self-hosted-integration-runtime)üzemeltetésű integrációs modul nem.

A másolási tevékenység futtatásának minimális DIUs kettő. Ha nincs megadva, a következő táblázat felsorolja a különböző másolási forgatókönyvek esetén a használt alapértelmezett DIUs:

| Másolja ki a forgatókönyv | Szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | A fájlok számától és méretétől függően 4 és 32 között |
| Az Adatmásolás Azure SQL Database vagy Azure Cosmos DB |A fogadó Azure SQL Database vagy Cosmos DB szintjétől függően 4 és 16 között (DTU/RUs-k száma) |
| Az összes többi másolási forgatókönyv | 4 |

Ez az alapértelmezett felülbírálásához, adjon meg értéket a **dataIntegrationUnits** tulajdonság az alábbiak szerint. A **dataIntegrationUnits** tulajdonság *megengedett értékeinek* értéke 256. A *DIUs tényleges számát* egyenlő vagy kisebb, mint a beállított érték, a adatmintát attól függően, hogy a másolási művelet használ a futási időben. További információ a teljesítmény szintjét egy adott másolási forrásaként és fogadó további egységek konfigurálásakor kaphat,: a [teljesítményfigyelési](#performance-reference).

A tevékenység futtatásának figyelése során a másolási tevékenység kimenetében minden egyes másoláshoz használt DIUs megtekintheti. További információ: másolási [tevékenység figyelése](copy-activity-overview.md#monitoring).

> [!NOTE]
> A négynél nagyobb DIUs beállítása csak akkor érvényes, ha több fájlt másol az Azure Storage-ból, Azure Data Lake Storage, az Amazon S3-ból, a Google Cloud Storage-ból, a Cloud FTP-ből vagy a Cloud SFTP-ből bármilyen más Felhőbeli adattárba.
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

Ne feledje, hogy a másolási művelet teljes ideje alapján kell fizetnie. Az adatáthelyezéshez használt teljes időtartam a DIUs közötti időtartam összege. Ha most már nyolc felhőbeli egységgel 15 percet vesz igénybe, és egy másolási feladat egy órával érvénybe két felhőbeli egységgel használják, akkor a teljes számlája összegét szinte változatlan marad.

## <a name="parallel-copy"></a>Párhuzamos másolás

A **parallelCopies** tulajdonság használatával jelezheti a másolási tevékenység által használandó párhuzamosságot. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben lévő szálak maximális száma, amelyek beolvashatók a forrásból, vagy párhuzamosan írhatók a fogadó adattárakba.

Az egyes másolási tevékenységek futtatásához Azure Data Factory meghatározza, hogy hány párhuzamos másolatot kell használni az adatok forrás adattárból és a célhely adattárba való másolásához. Az általa használt párhuzamos másolatok alapértelmezett száma a forrás és a fogadó típusától függ.

| Másolja ki a forgatókönyv | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók között |A fájlok méretétől és a két felhőalapú adattár közötti adatmásoláshoz használt DIUs, illetve a saját üzemeltetésű integrációs modul fizikai konfigurációjától függ. |
| Adatok másolása bármely forráskiszolgálóról az Azure Table Storage-ba |4 |
| Egyéb másolási forgatókönyvek esetén |1 |

> [!TIP]
> Ha a fájl alapú tárolók között másol Adatmásolást, az alapértelmezett viselkedés általában a legjobb teljesítményt biztosítja. Az alapértelmezett viselkedést a forrásfájl mintája alapján automatikusan határozza meg a rendszer.

Az adattárakat üzemeltető gépek terhelésének szabályozásához, vagy a másolási teljesítmény finomhangolásához felülbírálhatja az alapértelmezett értéket, és megadhatja a **parallelCopies** tulajdonság értékét. Az érték nagyobb vagy egyenlő 1 egész számnak kell lennie. Futásidőben a legjobb teljesítmény érdekében a másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő, mint a megadott érték.

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

**Megjegyzés:**

* Amikor fájlokat másol a fájl alapú tárolók között, a **parallelCopies** meghatározza a párhuzamosságot a fájl szintjén. Az egyetlen fájlon belüli adatdarabolás automatikusan és transzparens módon történik. A szolgáltatás úgy lett kialakítva, hogy egy adott forrás adattároló-típushoz a legmegfelelőbb méretet használja, hogy az adatmennyiséget párhuzamosan, a **parallelCopies**-be. Az adatátviteli szolgáltatást használja a másolási művelet futási időben párhuzamos másolatok tényleges száma nem több, mint a fájlok száma nem. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájl szintű párhuzamosságot.
* Ha olyan áruházakból másol adatokból, amelyek nem fájl alapúak (kivéve az [Oracle](connector-oracle.md#oracle-as-source), a [Teradata](connector-teradata.md#teradata-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source)és az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) -összekötőt, ha az adatparticionálás engedélyezve van) olyan tárolók számára, amelyek file-alapúak, az adatátviteli szolgáltatás figyelmen kívül hagyja a **parallelCopies** tulajdonságot. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.
* A **parallelCopies** tulajdonság a **dataIntegrationUnits**felé merőleges. Az előbbi akkor számít az adatok integrációs összes száma közötti.
* Ha megad egy értéket a **parallelCopies** tulajdonsághoz, vegye figyelembe a terhelés növekedését a forrás-és fogadó adattárakban. Vegye figyelembe a terhelés növekedését is a saját üzemeltetésű integrációs modulban, ha a másolási tevékenységre például a hibrid másolásra van felhatalmazás. Ez a terhelés növekszik, különösen akkor, ha több tevékenység vagy ugyanazon tevékenység egyidejű futtatása történik ugyanazon az adattárban. Ha azt észleli, hogy az adattár vagy a saját üzemeltetésű integrációs modul túlterhelt a terheléssel, csökkentse a **parallelCopies** értékét a terhelés enyhítése érdekében.

## <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

- **A különböző adattárakból származó adatok betöltését a SQL Data Warehouseon keresztül kell használni.** Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. A forrásadatok csak blob Storage-ban vagy Azure Data Lake Storeban szerepelhetnek, és meg kell felelniük a további feltételeknek. Ha egy adattár eltérő a Blob storage vagy az Azure Data Lake Store tölt be adatokat, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben a Azure Data Factory végrehajtja a szükséges adatátalakításokat annak érdekében, hogy az megfeleljen a bázisterület követelményeinek. A PolyBase majd az adatok betöltése az SQL Data Warehouse-bA hatékonyan használja. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattárból a Felhőbeli adattárolóba történő másolást) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos másolással tömörítheti a helyszíni adatok, így kevesebb időt vesz igénybe az adatok áthelyezése a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását a cél adattárba való betöltés előtt.
- **A vállalati informatikai házirendek miatt nem szeretné megnyitni a 80-as és a 443-es porton kívüli portokat a tűzfalon.** Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben az esetben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs modul előnyeit, hogy először másolja az adatait egy blob Storage-alapú átmeneti példányba HTTP vagy HTTPS protokollon keresztül a 443-es porton. Ezt követően az adatok betölthetők SQL Databaseba vagy SQL Data Warehouse a blob Storage átmeneti környezetből. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése

Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti tárolási Blob storage (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Azure Data Factory automatikusan kezeli a kétlépcsős folyamatot. Az adatáthelyezés befejezése után a Azure Data Factory az átmeneti tárolóból is törli az ideiglenes adatok mennyiségét.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Ha egy átmeneti tároló használatával aktiválja az adatáthelyezést, megadhatja, hogy szeretné-e tömöríteni az adatok tömörítését, mielőtt a forrás adattárból egy ideiglenes vagy átmeneti adattárba helyezi az adatátvitelt, majd kibontja az adatok ideiglenes vagy átmeneti dat-ból való áthelyezése előtt. egy tároló a fogadó adattárba.

Jelenleg nem másolhat Adatmásolást két olyan adattár között, amely különböző saját üzemeltetésű IRs-kapcsolaton keresztül csatlakozik, sem a, sem a szakaszos másolat nélkül. Ilyen esetben két explicit módon láncolt másolási tevékenységet konfigurálhat a forrásról az előkészítésre való másoláshoz, majd az előkészítésből a fogadóba.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** beállítást a másolási tevékenységben annak megadásához, hogy a blob Storage-ban kívánja-e az adatelőkészítést, mielőtt betölti azt egy célhely-adattárba. A **enableStaging** `TRUE`beállításakor adja meg az alábbi táblázatban felsorolt további tulajdonságokat. Ha még nem rendelkezik ilyennel, létre kell hoznia egy Azure Storage vagy Storage közös hozzáférésű aláírással társított szolgáltatást az átmeneti tároláshoz.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| enableStaging |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| linkedServiceName |Adja meg a nevét egy [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) társított szolgáltatás, amely hivatkozik a példány tárolási megoldás, amely egy átmeneti előkészítési tárolót használunk. <br/><br/> Nem használhat megosztott hozzáférési aláírással rendelkező tárolót az adatok SQL Data Warehouseba való betöltéséhez a Base használatával. Más esetekben használhatja azt. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| path |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adattároláshoz. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |– |Nem |
| enableCompression |Megadja, hogy a rendszer a célhelyre való másolás előtt tömöríti-e az adatfájlokat. Ez a beállítás átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

>[!NOTE]
> Ha a szakaszos másolást engedélyezte a tömörítést, az átmeneti blobhoz társított szolgáltatás egyszerű vagy MSI-hitelesítése nem támogatott.

A másolási tevékenység mintájának definíciója az előző táblázatban ismertetett tulajdonságokkal rendelkezik:

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

A díj két lépésből áll: a másolás időtartama és a másolás típusa alapján.

* Ha a Felhőbeli másolás során használ átmeneti tárolást, amely az adatok Felhőbeli adattárból egy másik Felhőbeli adattárba való másolását végzi el, az Azure Integration Runtime által felhatalmazott mindkét szakaszban az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] értékének összegét kell megfizetnie.
* Ha a hibrid másolás során átmeneti tárolást használ, amely egy helyszíni adattárból egy Felhőbeli adattárba másol egy olyan szakaszt, amely egy saját üzemeltetésű integrációs modul által felhatalmazott, akkor a [hibrid másolási időtartam] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

Hajtsa végre ezeket a lépéseket a Azure Data Factory szolgáltatás teljesítményének finomhangolásához a másolási tevékenységgel.

1. **Hozzon létre egy alaptervet.** A fejlesztési fázisban tesztelje a folyamatot a másolási tevékenységgel egy reprezentatív adatminta alapján. A [másolási tevékenység figyelését](copy-activity-overview.md#monitoring)követő végrehajtási adatok és teljesítmény-jellemzők gyűjtése.

2. **A teljesítmény diagnosztizálása és optimalizálása.** Ha a megfigyelt teljesítmény nem felel meg az elvárásainak, akkor azonosítsa a teljesítménnyel kapcsolatos szűk keresztmetszeteket. Távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében a teljesítmény optimalizálásával majd.

    Bizonyos esetekben, amikor másolási tevékenységet futtat Azure Data Factoryban, a [másolási tevékenység figyelése oldalon](copy-activity-overview.md#monitor-visually)a "Performance tuning tippek" üzenet jelenik meg, ahogy az az alábbi példában is látható. Az üzenet közli az adott másolási futtatáshoz azonosított szűk keresztmetszetet. Azt is ismerteti, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. A teljesítmény-hangolási tippek jelenleg a következő javaslatokat nyújtják:

    - Az adatok Azure SQL Data Warehouseba való másolásakor használjon albase-t.
    - Növelje Azure Cosmos DB kérelmek egységeit vagy Azure SQL Database DTU (adatbázis átviteli egységei), amikor az adattároló oldalán lévő erőforrás a szűk keresztmetszet.
    - Távolítsa el a szükségtelenül előkészített másolatot.

    A teljesítmény-hangolási szabályok fokozatos bővítése is megtörténik.

    **Példa: Másolás Azure SQL Database teljesítmény-hangolási tippekkel**

    Ebben a példában a másolás során Azure Data Factory észleli, hogy a fogadó Azure SQL Database eléri a nagy DTU kihasználtságot, ami lelassítja az írási műveleteket. A javaslat célja, hogy növelje a Azure SQL Database szintet több DTU. 

    ![A monitoring és a Performance tuning tippek másolása](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Emellett az alábbiakban néhány gyakori szempontot is figyelembe kell venni. A teljesítmény-diagnosztika teljes leírása meghaladja a jelen cikk hatókörét.

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [Adatintegrációs egységek](#data-integration-units)
     * [Szakaszos másolás](#staged-copy)
     * [Saját üzemeltetésű Integration Runtime skálázhatósága](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Helyi integrációs modul](#considerations-for-self-hosted-integration-runtime)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálást és deszerializálást](#considerations-for-serialization-and-deserialization)
   * [A tömörítés](#considerations-for-compression)
   * [Oszlop-hozzárendelés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)

3. **Bontsa ki a konfigurációt a teljes adatkészletre.** Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamatot a teljes adatkészlet lefedéséhez.

## <a name="considerations-for-self-hosted-integration-runtime"></a>A saját üzemeltetésű integrációs modul szempontjai

Ha a másolási tevékenység egy saját üzemeltetésű integrációs modulon fut, vegye figyelembe a következőket:

**Telepítés**: Javasoljuk, hogy egy dedikált gépet használjon az integrációs modul üzemeltetéséhez. Lásd: a saját üzemeltetésű integrációs modul [használatának szempontjai](concepts-integration-runtime.md).

Vertikális felskálázás: Egyetlen logikai saját üzemeltetésű integrációs modul egy vagy több csomóponttal egyidejűleg egyszerre több másolási tevékenységet is képes futni. Ha a hibrid adatáthelyezéshez nagy mennyiségű párhuzamos másolási tevékenység vagy nagy mennyiségű adatmásolási művelet szükséges, érdemes [a saját](create-self-hosted-integration-runtime.md#high-availability-and-scalability) üzemeltetésű integrációs modult kibővíteni, hogy több erőforrást lehessen kiépíteni a másoláshoz.

## <a name="considerations-for-the-source"></a>A forrás szempontjai

### <a name="general"></a>Általános

Ügyeljen arra, hogy az alapul szolgáló adattár ne legyen túlterhelve más, vagy azon kívül futó munkaterhelések között.

A Microsoft-adattárakat lásd: az adattárakra jellemző [témakörök figyelése és finomhangolása](#performance-reference) . Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

* Ha a blob Storage-ból SQL Data Warehouseba másol adatait, a teljesítmény növelése érdekében érdemes lehet a Base használata. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Ha a HDFS-ből az Azure Blob Storage-ba vagy a Azure Data Lake Storeba másol adatait, érdemes lehet a DistCp-t használnia a teljesítmény növelése érdekében. További információ: a [DistCp használata adatok másolásához a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Ha a vöröseltolódásról Azure SQL Data Warehousere, Azure BLob Storage-ba vagy Azure Data Lake Store-ra másol adatokból, a teljesítmény növelése érdekében vegye figyelembe a kitöltést. További információ: az [Eltávolítás használata az adatok az Amazon vöröseltolódásból való másolásához](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Az átlagos fájlméret és a fájlok száma**: A másolási tevékenység egyszerre egy fájlt továbbít. Az adatok áthelyezésének akkora a teljes átviteli sebesség alacsonyabb, ha néhány nagy méretű fájlok miatt a rendszer-indításkori fázis minden egyes fájl helyett a sok kis méretű fájlt tartalmaz. Ha lehetséges, a kisebb fájlok nagyobb méretű fájlokba való összevonásával nagyobb átviteli sebességet nyerhet.
* **Fájlformátum és tömörítés**: A teljesítmény javítása érdekében tekintse meg a [szerializálási és](#considerations-for-serialization-and-deserialization) deszerializálási szempontokat és a [tömörítési szempontokat](#considerations-for-compression) ismertető szakaszt.

### <a name="relational-data-stores"></a>Relációs adattárak

* **Adatminta**: A következő tábla sémáját másolási átviteli sebesség hatással van. A nagyméretű sorok mérete jobb teljesítményt nyújt, mint a kisebb sorok mérete, hogy ugyanazokat az adatmennyiségeket másolja. A hiba oka, hogy az adatbázis hatékonyabban lekérheti kevesebb váró adatokat, amelyek kevesebb sort tartalmaznak.
* **Lekérdezési vagy tárolt eljárás**: Optimalizálja a másolási tevékenység forrásában megadott lekérdezés vagy tárolt eljárás logikáját, hogy az adatgyűjtést hatékonyabban lehessen beolvasni.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai

### <a name="general"></a>Általános

Ügyeljen arra, hogy az alapul szolgáló adattár ne legyen túlterhelve más, vagy azon kívül futó munkaterhelések között.

A Microsoft-adattárakat lásd: az adattárakra jellemző [témakörök figyelése és finomhangolása](#performance-reference) . Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

* Ha bármely adattárból másol adatokból Azure SQL Data Warehouseba, érdemes lehet a teljesítmény növelésére használni a Base-t. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Ha a HDFS-ből az Azure Blob Storage-ba vagy a Azure Data Lake Storeba másol adatait, érdemes lehet a DistCp-t használnia a teljesítmény növelése érdekében. További információ: a [DistCp használata adatok másolásához a HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Ha a vöröseltolódásról Azure SQL Data Warehousere, Azure Blob Storage-ba vagy Azure Data Lake Store-ra másol adatokból, a teljesítmény növelése érdekében vegye figyelembe a kitöltést. További információ: az [Eltávolítás használata az adatok az Amazon vöröseltolódásból való másolásához](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak

* **Másolja a viselkedés**: Ha más fájl-alapú adattárból másol adatokból, a másolási tevékenység három lehetőséggel rendelkezik a **copyBehavior** tulajdonságon keresztül. Ez a megőrzi a hierarchia, simítja egybe a hierarchia, vagy egyesíti a fájlokat. Az egybesimítás hierarchia vagy megőrzi az rendelkezik alig vagy egyáltalán nem teljesítménybeli terhelést, de a fájlok egyesítése növelése teljesítménybeli terhelést okoz.
* **Fájlformátum és tömörítés**: A teljesítmény javítása érdekében tekintse meg a [szerializálási és](#considerations-for-serialization-and-deserialization) deszerializálási szempontokat és a [tömörítési szempontokat](#considerations-for-compression) ismertető szakaszt.

### <a name="relational-data-stores"></a>Relációs adattárak

* **A másolás viselkedése és a teljesítményre gyakorolt hatás**: Az adatbevitel többféleképpen is elvégezhető egy SQL-fogadóba. További információ az [adatok Azure SQL Databaseba való betöltésével kapcsolatos ajánlott eljárásokról](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **A minta és a batch adatméret**:
  * A következő tábla sémáját másolási átviteli sebesség hatással van. Az azonos mennyiségű adatot másolni, egy nagy méretű sor mérete ad egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti kevesebb váró adatokat.
  * A másolási tevékenység kötegek sorozatában szúr be adatmennyiséget. Beállíthatja a sorok számát a batch segítségével a **writeBatchSize** tulajdonság. Ha az adatok kis sorral rendelkezik, akkor lehet beállítani a **writeBatchSize** tulajdonság számára, hogy alacsonyabb batch terhelést és nagyobb átviteli sebességet a magasabb értékű. Ha az adatokat a sor mérete túl nagy, ügyeljen arra, ha növeli **writeBatchSize**. Egy magas szintű egy példány hibáját okozta. az adatbázis terhelve vezethet.

### <a name="nosql-stores"></a>NoSQL-tárolókat

* A **Table storage**:
  * **Partíció**: Az átadott partíciók közötti adatírás jelentősen csökkenti a teljesítményt. Rendezze a forrásadatokat a partíciós kulcs szerint, hogy a rendszer hatékonyan beszúrja az adatait egy partícióba egy másik után. Vagy beállíthatja úgy a logikát, hogy az adatlemezeket egyetlen partícióba írja.

## <a name="considerations-for-serialization-and-deserialization"></a>Szerializálást és deszerializálást szempontjai

A szerializálás és a deszerializálás akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájl. A másolási tevékenység által támogatott fájlformátumokról további információt a [támogatott fájl-és Tömörítési formátumok](supported-file-formats-and-compression-codecs.md)című témakörben talál.

**Másolja a viselkedés**:

* A fájlok másolása a fájlalapú adattárak között:
  * Ha a bemeneti és a kimeneti adatkészletek azonos vagy nem fájlformátum-beállításokkal rendelkeznek, az adatáthelyezési szolgáltatás a szerializálás vagy a deszerializálás nélkül hajtja végre a *bináris másolást* . Megjelenik egy a forgatókönyv, amelyben a forrás- és fogadó fájlformátum beállításai eltérnek egymáshoz képest nagyobb átviteli sebességet.
  * Ha a bemeneti és a kimeneti adatkészletek egyaránt szöveges formátumúak, és csak a kódolás típusa különbözik, akkor az adatátviteli szolgáltatás csak kódolási konverziót végez. Még nem csinál bármely szerializálási és deszerializálás, amely néhány bináris másolat többletterhelést teljesítményt okoz.
  * Ha a bemeneti és a kimeneti adatkészletek különböző fájlformátumokkal vagy különböző konfigurációkkal rendelkeznek, mint például a határolójelek, az adatátviteli szolgáltatás deszerializálja a forrásadatokat adatfolyamként, átalakítja, majd szerializálja a megadott kimeneti formátumba. Ez a művelet egy sokkal jelentős teljesítménybeli többletterhelést képest más esetben eredményez.
* Ha olyan adattárból másol fájlokat, amely nem fájl alapú, például egy fájl alapú tárolóból egy kapcsolódó tárolóba, a szerializálás vagy a deszerializálás lépésre van szükség. Ebben a lépésben jelentős teljesítménybeli terhelést eredményez.

**Fájl formátuma**: A választott fájlformátum hatással lehet a másolási teljesítményre. Például a Avro kompakt bináris formátum, amely tárolja az adatokat metaadatok. Széles körben támogatja a Hadoop-ökoszisztéma, feldolgozása és lekérdezési rendelkezik. A Avro drágább a szerializálás és a deszerializálás során, ami alacsonyabb másolási sebességet eredményez a szöveges formátumhoz képest. 

Győződjön meg azok alkalmazásfüggőségeit a kiválasztott fájlformátum teljes feldolgozási folyamat során. Kezdés:

- Az adatok tárolási formája, a forrás-és a külső rendszerekből származó adatok kinyerése.
- A tárolás, az analitikus feldolgozás és a lekérdezés legjobb formátuma.
- Milyen formátumban kell exportálni az adatközpontokat a jelentéskészítési és vizualizációs eszközök számára.

Néha olyan fájlformátum, amely az optimálisnál rosszabb olvasási és írási teljesítmény előfordulhat, hogy jó választás meghatározásakor a teljes elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai

Ha a bemeneti vagy kimeneti adatkészlet egy fájl, beállíthatja, hogy a másolási tevékenység tömörítést vagy kibontást végezzen, miközben adatokat ír a célhelyre. Ha tömörítést választja, akkor győződjön meg arról, bemeneti/kimeneti (I/O) magával és a CPU. Tömöríti a számítási erőforrásokat az extra költségek. De cserébe csökkenti a hálózati i/o- és storage. Az adatoktól függően megjelenhet a teljes másolási átviteli sebesség.

**Kodek**: Minden tömörítési kodeknek van előnye. Például bzip2 rendelkezik a legalacsonyabb másolási teljesítmény, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert azt a feldolgozáshoz is fel. A gzip a leginkább kiegyensúlyozott megoldás, és a leggyakrabban használt. Válassza ki a leginkább megfelelő a végpontok közötti forgatókönyv kodek.

**Szint**: Az egyes tömörítési kodekekhez két lehetőség közül választhat: a leggyorsabb tömörített és az optimális módon tömörített. A leggyorsabb tömörített beállítás a lehető leggyorsabban tömöríti az adatgyűjtést, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan. Az optimális tömörített beállítás több időt tölt a tömörítést, és a egy minimális mennyiségű adatot eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb általános teljesítményt abban az esetben tesztelheti.

**A megfontolás**: Ha nagy mennyiségű adatmennyiséget szeretne másolni egy helyszíni tároló és a felhő között, érdemes lehet [szakaszos másolatot](#staged-copy) használni a tömörítés engedélyezésével. Az átmeneti tárterület használata akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások sávszélessége a korlátozási tényező, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet is legyen tömörítetlen formában.

## <a name="considerations-for-column-mapping"></a>Az oszlopleképezés szempontjai

A másolási tevékenységek **columnMappings** tulajdonságát beállíthatja úgy, hogy a bemeneti oszlopok összes vagy egy részhalmazát hozzárendelje a kimeneti oszlopokhoz. Az adatáthelyezési szolgáltatás a forrásból olvassa be az adatokat, miután kell végezni az adatokon oszlop-hozzárendelés előtt írja az adatokat a fogadó. A további feldolgozás csökkenti a másolási átviteli sebességet.

Ha a forrásadattár lekérdezhető, például ha egy relációs áruházban, például az SQL Database vagy SQL Server a, vagy ha például a Table storage vagy az Azure Cosmos DB NoSQL-alapú tárolót érdemes leküldése az oszlopok szűrésének és logika átrendezésével végezze el a **lekérdezés** tulajdonság oszlop-hozzárendelés használata helyett. Így a kivetítés akkor történik meg, amikor az adatátviteli szolgáltatás beolvassa az adatokat a forrás adattárból, ahol sokkal hatékonyabbá válik.

További információ a [másolási tevékenység sémájának leképezéséről](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Egyéb szempontok

Ha a másolni kívánt adatméret nagy méretű, az üzleti logikát beállíthatja úgy, hogy tovább particionálja az adatmennyiséget. A másolási tevékenységet gyakrabban is futtathatja, hogy csökkentse az egyes futó másolási tevékenységek adatméretét.

Legyen óvatos az adathalmazok és a másolási tevékenységek számával kapcsolatban, amelyek megkövetelik, hogy a Azure Data Factory egy időben kapcsolódjanak ugyanahhoz az adattárhoz. Számos egyidejű másolási feladat lehet, hogy adattár szabályozás és teljesítménycsökkenésért másolási feladat belső újrapróbálkozás, és bizonyos esetekben végrehajtási hibák vezethet.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Példa a forgatókönyvre: Másolás helyszíni SQL Serverről blob Storage-tárolóba

**Forgatókönyv**: A folyamat úgy van felépítve, hogy CSV formátumban másolja a helyszíni SQL Serverről a blob Storage-ba. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlokat kell tömörített, bzip2 formátumba.

**Tesztelés és elemzés**: A másolási tevékenység átviteli sebessége kevesebb, mint 2 MBps, ami sokkal lassabb, mint a teljesítménnyel kapcsolatos teljesítményteszt.

**Teljesítmény elemzése és finomhangolása**: A teljesítménnyel kapcsolatos probléma megoldásához tekintse át az adatfeldolgozás és-áthelyezés módját.

- **Olvasási információk**: Az Integration Runtime megnyit egy kapcsolódást a SQL Serverhoz, és elküldi a lekérdezést. SQL Server válaszol, ha az adatfolyamot az intraneten keresztül az integrációs modulba küldi.
- **Adatok szerializálása és tömörítése**: Az Integration Runtime az adatfolyamot CSV formátumba szerializálja, és az adatok tömörítését egy bzip2-adatfolyamba tömöríti.
- **Írási érték**: Az Integration Runtime feltölti a bzip2 streamet a blob Storage-ba az interneten keresztül.

Ahogy láthatja, az adatfeldolgozás és a továbbítás egymás utáni sorrendben történik: SQL Server > LAN > Integration Runtime > WAN > blob Storage. A teljes teljesítmény a folyamaton belüli minimális átviteli sebességtől.

![Az adatfolyam](./media/copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhatják a teljesítménybeli szűk keresztmetszetet:

* **Forrás**: SQL Server maga a nagy terhelés miatt alacsony átviteli sebességgel rendelkezik.
* **Saját**üzemeltetésű integrációs modul:
  * **HELYI HÁLÓZAT**: Az Integration Runtime a SQL Server gépről távol található, és alacsony sávszélességű kapcsolattal rendelkezik.
  * **Integration Runtime**: Az Integration Runtime elérte a terhelési korlátozásokat a következő műveletek végrehajtásához:
    * **Szerializálás**: Az adatfolyam CSV formátumba való szerializálásának lassú átviteli sebessége van.
    * **Tömörítés**: Úgy döntött, hogy lassú tömörítési kodeket (például bzip2, 2,8 MBps, Core i7) választ.
  * **WAN**: A vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség alacsony, például T1 = 1 544 kbps; T2 = 6 312 kbps.
* Fogadó: A blob Storage-ban alacsony átviteli sebesség van. Ez a forgatókönyv nem valószínű, mert a szolgáltatói szerződés (SLA) legalább 60 MBps-t garantál.

Ebben az esetben az adattömörítés bzip2 előfordulhat, hogy lehet lelassítja a teljes folyamat. A gzip tömörítési kodek átváltása, előfordulhat, hogy a szűk keresztmetszet megkönnyítése érdekében.

## <a name="references"></a>Referencia

Az alábbiakban a támogatott adattárakkal kapcsolatos Teljesítményfigyelés és hangolási referenciák találhatók:

* Azure Storage, amely tartalmazza a blob Storage-t és a Table Storage-t: Az [Azure Storage skálázhatósági céljai](../storage/common/storage-scalability-targets.md) és az [Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Nyomon követheti [a teljesítményt](../sql-database/sql-database-single-database-monitor.md) , és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos arányát.
* Azure SQL Data Warehouse: Az adatraktár-egységekben (DWU) mérhető a funkció. Lásd: [a számítási teljesítmény kezelése Azure SQL Data Warehouseban (áttekintés)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Az Azure Cosmos DB: [Azure Cosmos db teljesítménybeli szintjei](../cosmos-db/performance-levels.md).
* Helyszíni SQL Server: [A teljesítmény figyelése és finomhangolása](https://msdn.microsoft.com/library/ms189081.aspx).
* Helyszíni fájlkiszolgáló: [A fájlkiszolgálók teljesítményének finomhangolása](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>További lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolási tevékenység sémájának leképezése](copy-activity-schema-and-type-mapping.md)
- [Másolja ki a tevékenység a hibatűrés](copy-activity-fault-tolerance.md)
