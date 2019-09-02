---
title: A másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató a Azure Data Factoryban | Microsoft Docs
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
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 22c83b1fe53a9209fd243fe807bb76718cbdcbbd
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211693"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató
> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)

Akár nagy léptékű adatáttelepítést szeretne végezni a EDW-ből az Azure-ba, vagy a különböző forrásokból az Azure-ba történő adatgyűjtést az Azure-ba big data elemzésekhez, kritikus fontosságú az optimális teljesítmény elérése és méretezhetőség.  A Azure Data Factory egy nagy teljesítményű, rugalmas és költséghatékony mechanizmust biztosít az adatok méretekben történő betöltéséhez, így kiválóan alkalmas az olyan adatmérnökök számára, akik magas teljesítményű és skálázható adatfeldolgozási folyamatokat kívánnak kialakítani.

Ez a cikk elolvasása után fogja tudni a következő kérdések megválaszolásával:

- Milyen szintű teljesítményt és méretezhetőséget érhet el az ADF másolási tevékenység az adatáttelepítési és adatfeldolgozási forgatókönyvek esetében?

- Milyen lépéseket kell tennie az ADF másolási tevékenység teljesítményének finomhangolásához?
- Milyen ADF-optimalizálási gombokat használhatok egy adott másolási tevékenység teljesítményének optimalizálására?
- Az ADF-en kívül más tényezők is figyelembe veszik a másolási teljesítmény optimalizálásakor?

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md) a cikk elolvasása előtt.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>A teljesítmény és a méretezhetőség elérhetővé másolása ADF használatával

Az ADF olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot, ami lehetővé teszi a fejlesztők számára, hogy teljes mértékben kihasználják a hálózati sávszélességet, valamint a tárolási IOPS és sávszélességet, hogy maximalizálják az adatátviteli sebességet a környezet számára.  Ez azt jelenti, hogy az elérni kívánt átviteli sebesség a forrás és a cél közötti minimális átviteli sebesség mérésével, a célhely adattárával és a hálózati sávszélességgel mérhető.  Az alábbi táblázat kiszámítja a másolás időtartamát az adatméret és a környezet sávszélesség-korlátja alapján. 

| Adatméret/ <br/> Sávszélesség | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 perc    | 1,4 perc   | 0,3 perc   | 0,1 perc  | 0,03 perc | 0,01 perc | 0,0 perc   |
| **10 GB**                   | 27,3 perc   | 13,7 perc  | 2,7 perc   | 1,3 perc  | 0,3 perc  | 0,1 perc  | 0,03 perc  |
| **100 GB**                  | 4,6 óra    | 2,3 óra   | 0,5 óra   | 0,2 óra  | 0,05 óra | 0,02 óra | 0,0 óra   |
| **1 TB**                    | 46,6 óra   | 23,3 óra  | 4,7 óra   | 2,3 óra  | 0,5 óra  | 0,2 óra  | 0,05 óra  |
| **10 TB**                   | 19,4 nap  | 9,7 nap  | 1,9 nap  | 0,9 nap | 0,2 nap | 0,1 nap | 0,02 nap |
| **100 TB**                  | 194,2 nap | 97,1 nap | 19,4 nap | 9,7 nap | 1,9 nap | 1 nap   | 0,2 nap  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

Az ADF-másolás különböző szinteken méretezhető:

![Az ADF másolási méretezése](media/copy-activity-performance/adf-copy-scalability.png)

- Az ADF-vezérlési folyamat egyszerre több másolási tevékenységet is elindíthat, például az [egyes hurkok esetében](control-flow-for-each-activity.md).
- Egy másolási tevékenység kihasználhatja a skálázható számítási erőforrások előnyeit: Azure Integration Runtime használata esetén [akár 256 DIUs-t](#data-integration-units) is megadhat az egyes másolási tevékenységekhez kiszolgáló nélküli módon. saját üzemeltetésű Integration Runtime használatakor manuálisan is méretezheti a gépet, vagy akár több gépre is kibővíthető ([legfeljebb 4 csomópont](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), és egyetlen másolási tevékenység fogja particionálni az összes csomóponton beállított fájlt.
- Egy másolási tevékenység [párhuzamosan](#parallel-copy)több szál használatával olvas be és ír az adattárba.

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

Hajtsa végre ezeket a lépéseket a Azure Data Factory szolgáltatás teljesítményének finomhangolásához a másolási tevékenységgel.

1. **Válasszon ki egy tesztelési adatkészletet, és hozzon létre egy alaptervet.** A fejlesztési fázisban tesztelje a folyamatot a másolási tevékenységgel egy reprezentatív adatminta alapján. A kiválasztott adatkészlet a jellemző adatmintázatokat (a mappastruktúrát, a fájl mintáját, az adatsémát stb.) jelöli, és elég nagy a másolási teljesítmény kiértékeléséhez, például a másolási tevékenység befejezéséhez 10 percet vagy azt meghaladó időt vesz igénybe. A [másolási tevékenység figyelését](copy-activity-overview.md#monitoring)követő végrehajtási adatok és teljesítmény-jellemzők gyűjtése.

2. **Egy másolási tevékenység teljesítményének maximalizálása**:

   Első lépésként azt javasoljuk, hogy először egyetlen másolási tevékenységgel maximalizálja a teljesítményt.

   **Ha a másolási tevékenységet egy Azure Integration Runtime hajtja végre:**

   Az adatintegrációs [egységek (DIU)](#data-integration-units) és a [párhuzamos másolási](#parallel-copy) beállítások alapértelmezett értékeinek megkezdése.  Végezzen teljesítményteszt-futtatást, és jegyezze fel az elért teljesítményt, valamint a DIUs és a párhuzamos másoláshoz használt tényleges értékeket.  Tekintse át a [másolási tevékenység figyelését](copy-activity-overview.md#monitoring) a futtatási eredmények és a használt teljesítmény-beállítások gyűjtésének módjáról.

   Most végezzen további teljesítményteszt-futtatásokat, minden alkalommal, amikor megduplázódott a DIU beállítás értékét.  Ha úgy gondolja, hogy az alapértelmezett beállítással elért teljesítmény messze a várt érték alá esik, a DIU-beállítás drasztikusan növelhető a következő tesztelési futtatás során.

   A másolási tevékenységnek majdnem tökéletesen lineárisan kell méreteznie a DIU beállítás növelésével.  Ha a DIU-beállítás megkettőzésével nem látja az átviteli sebességet, két dolog történhet:

   - A futtatott másolási minta nem élvez további DIUs hozzáadását.  Annak ellenére, hogy nagyobb DIU értéket adott meg, a ténylegesen használt DIU változatlan marad, ezért a korábban megegyező átviteli sebességet kell megadnia.  Ebben az esetben maximalizálja az összesített átviteli sebességet úgy, hogy több, a 3. lépéssel párhuzamosan hivatkozó példányt futtat.
   - További DIUs (több lóerő) hozzáadásával, valamint az adatgyűjtési,-áthelyezési és-betöltési adatok nagyobb arányának növelésével a forrás adattár, a hálózat vagy a cél adattár elérte a szűk keresztmetszetet, és lehetséges, hogy meghaladta a sávszélességet.  Ha ez a helyzet, próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával vagy a hálózati rendszergazdával, hogy növelje a felső korlátot, vagy csökkentse a DIU beállítást, amíg a szabályozás leáll.

   **Ha a másolási tevékenységet egy saját üzemeltetésű Integration Runtime hajtja végre:**

   Azt javasoljuk, hogy az integrációs modul üzemeltetéséhez az adattárat üzemeltető kiszolgálótól külön dedikált gépet használjon.

   A [párhuzamos másolási](#parallel-copy) beállítás alapértelmezett értékeivel kezdődik, és egyetlen csomópontot használ a saját üzemeltetésű IR-hez.  Végezzen teljesítményteszt-futtatást, és jegyezze fel az elért teljesítményt.

   Ha nagyobb átviteli sebességet szeretne elérni, a saját üzemeltetésű integrációs modul vertikális felskálázásával vagy felskálázásával végezhető el:

   - Ha a saját üzemeltetésű IR-csomópont CPU-és rendelkezésre álló memóriája nincs teljesen kihasználva, de az egyidejű feladatok végrehajtása eléri a korlátot, akkor a csomóponton futtatható egyidejű feladatok számának növelésével bővítse a skálázást.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#scale-up) .
   - Ha viszont a CPU túl magas a saját üzemeltetésű IR-csomóponton, vagy kevés a rendelkezésre álló memória, akkor hozzáadhat egy új csomópontot a több csomópont terhelésének kiskálázásához.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

   A saját üzemeltetésű integrációs modul kapacitásának vertikális felskálázása vagy felskálázása után ismételje meg a teljesítményteszt futtatását, és ellenőrizze, hogy egyre nagyobb átviteli sebességre van-e szüksége.  Ha az átviteli sebesség leáll, legvalószínűbb, hogy a forrás adattár, a hálózat vagy a cél adattár elérte a szűk keresztmetszetet, és elkezdi a szabályozást. Ha ez a helyzet, próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával vagy a hálózati rendszergazdával, hogy növelje a felső korlátot, vagy térjen vissza a saját üzemeltetésű integrációs modul előző skálázási beállítására. 

3. **Az összesített átviteli sebesség maximalizálása több példány párhuzamos futtatásával:**

   Most, hogy maximalizálta egyetlen másolási tevékenység teljesítményét, ha még nem érte el a környezete – a hálózat, a forrás adattár és a cél adattároló átviteli sebesség felső korlátait –, több másolási tevékenységet is futtathat párhuzamosan az ADF használatával vezérlési folyamatok, például [az egyes hurokokhoz](control-flow-for-each-activity.md).

4. **Teljesítmény-finomhangolási tippek és optimalizálási funkciók.** Bizonyos esetekben, amikor másolási tevékenységet futtat Azure Data Factoryban, a [másolási tevékenység figyelésére](copy-activity-overview.md#monitor-visually)szolgáló "Performance tuning tippek" üzenet jelenik meg, ahogy az az alábbi példában is látható. Az üzenet közli az adott másolási futtatáshoz azonosított szűk keresztmetszetet. Azt is ismerteti, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. A teljesítmény-hangolási tippek jelenleg a következő javaslatokat nyújtják:

   - Az adatok Azure SQL Data Warehouseba való másolásakor használjon albase-t.
   - Növelje Azure Cosmos DB kérelmek egységeit vagy Azure SQL Database DTU (adatbázis átviteli egységei), amikor az adattároló oldalán lévő erőforrás a szűk keresztmetszet.
   - Távolítsa el a szükségtelenül előkészített másolatot.

   A teljesítmény-hangolási szabályok fokozatos bővítése is megtörténik.

   **Példa: Másolás Azure SQL Database teljesítmény-hangolási tippekkel**

   Ebben a példában a másolás során Azure Data Factory észleli, hogy a fogadó Azure SQL Database eléri a nagy DTU kihasználtságot, ami lelassítja az írási műveleteket. A javaslat célja, hogy növelje a Azure SQL Database szintet több DTU. 

   ![A monitoring és a Performance tuning tippek másolása](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Emellett a következő teljesítmény-optimalizálási funkciókat is érdemes figyelembe vennie:

   - [Párhuzamos másolása](#parallel-copy)
   - [Adatintegrációs egységek](#data-integration-units)
   - [Szakaszos másolás](#staged-copy)
   - [Saját üzemeltetésű Integration Runtime skálázhatósága](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Bontsa ki a konfigurációt a teljes adatkészletre.** Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamatot a teljes adatkészlet lefedéséhez.

## <a name="copy-performance-optimization-features"></a>Teljesítmény-optimalizálási funkciók másolása

Azure Data Factory a következő teljesítmény-optimalizálási funkciókat biztosítja:

- [Párhuzamos másolása](#parallel-copy)
- [Adatintegrációs egységek](#data-integration-units)
- [Szakaszos másolás](#staged-copy)

### <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely a Azure Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. Az adatintegrációs egység csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)szolgáltatásra vonatkozik, a [saját](concepts-integration-runtime.md#self-hosted-integration-runtime)üzemeltetésű integrációs modul nem.

**A rendszer \* a felhasznált DIUs-másolási időtartam \* egységnyi árát/DIU óradíjat**számítja fel. Tekintse meg az [](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)aktuális árakat. A helyi pénznem és a különálló kedvezmények előfizetési típusok esetén alkalmazhatók.

A másolási tevékenység futtatásának engedélyezése engedélyezett DIUs **2 és 256 között**van. Ha nincs megadva, vagy a felhasználói felületen az "automatikus" lehetőséget választja, Data Factory a forrás-fogadó pár és az adatminta alapján dinamikusan alkalmazza az optimális DIU beállítást. A következő táblázat felsorolja a különböző másolási helyzetekben használt alapértelmezett DIUs:

| Másolja ki a forgatókönyv | Szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | A fájlok számától és méretétől függően 4 és 32 között |
| Az Adatmásolás Azure SQL Database vagy Azure Cosmos DB |A fogadó Azure SQL Database vagy Cosmos DB szintjétől függően 4 és 16 között (DTU/RUs-k száma) |
| Az összes többi másolási forgatókönyv | 4 |

Ez az alapértelmezett felülbírálásához, adjon meg értéket a **dataIntegrationUnits** tulajdonság az alábbiak szerint. A *DIUs tényleges számát* egyenlő vagy kisebb, mint a beállított érték, a adatmintát attól függően, hogy a másolási művelet használ a futási időben.

A tevékenység futtatásának figyelése során a másolási tevékenység kimenetében minden egyes másoláshoz használt DIUs megtekintheti. További információ: másolási [tevékenység figyelése](copy-activity-overview.md#monitoring).

> [!NOTE]
> A négynél nagyobb DIUs beállítása csak akkor érvényes, ha több fájlt másol az Azure Storage-ból, Azure Data Lake Storage, az Amazon S3-ból, a Google Cloud Storage-ból, a Cloud FTP-ből vagy a Cloud SFTP-ből bármilyen más Felhőbeli adattárba.

**Példa:**

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

### <a name="parallel-copy"></a>Párhuzamos másolás

A **parallelCopies** tulajdonság használatával jelezheti a másolási tevékenység által használandó párhuzamosságot. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben lévő szálak maximális száma, amelyek beolvashatók a forrásból, vagy párhuzamosan írhatók a fogadó adattárakba.

Az egyes másolási tevékenységek futtatásához Azure Data Factory meghatározza, hogy hány párhuzamos másolatot kell használni az adatok forrás adattárból és a célhely adattárba való másolásához. Az általa használt párhuzamos másolatok alapértelmezett száma a forrás és a fogadó típusától függ.

| Másolja ki a forgatókönyv | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók között |A fájlok méretétől és a két felhőalapú adattár közötti adatmásoláshoz használt DIUs, illetve a saját üzemeltetésű integrációs modul fizikai konfigurációjától függ. |
| Másolás a (z) és a (z) (például [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)és [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) partíciós lehetőséggel rendelkező rokon adattárból|4 |
| Adatok másolása bármely forráskiszolgálóról az Azure Table Storage-ba |4 |
| Egyéb másolási forgatókönyvek esetén |1 |

> [!TIP]
> Ha a fájl alapú tárolók között másol Adatmásolást, az alapértelmezett viselkedés általában a legjobb teljesítményt biztosítja. Az alapértelmezett viselkedést a forrásfájl mintája alapján automatikusan határozza meg a rendszer.

Az adattárakat üzemeltető gépek terhelésének szabályozásához, vagy a másolási teljesítmény finomhangolásához felülbírálhatja az alapértelmezett értéket, és megadhatja a **parallelCopies** tulajdonság értékét. Az érték nagyobb vagy egyenlő 1 egész számnak kell lennie. Futásidőben a legjobb teljesítmény érdekében a másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő, mint a megadott érték.

**Megjegyzés:**

- Amikor fájlokat másol a fájl alapú tárolók között, a **parallelCopies** meghatározza a párhuzamosságot a fájl szintjén. Az egyetlen fájlon belüli adatdarabolás automatikusan és transzparens módon történik. A szolgáltatás úgy lett kialakítva, hogy egy adott forrás adattároló-típushoz a legmegfelelőbb méretet használja, hogy az adatmennyiséget párhuzamosan, a **parallelCopies**-be. Az adatátviteli szolgáltatást használja a másolási művelet futási időben párhuzamos másolatok tényleges száma nem több, mint a fájlok száma nem. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájl szintű párhuzamosságot.
- Ha olyan áruházakból másol adatokból, amelyek nem fájl alapúak (kivéve az [Oracle](connector-oracle.md#oracle-as-source), a [Teradata](connector-teradata.md#teradata-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source)és az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) -összekötőt, ha az adatparticionálás engedélyezve van) olyan tárolók számára, amelyek file-alapúak, az adatátviteli szolgáltatás figyelmen kívül hagyja a **parallelCopies** tulajdonságot. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.
- A **parallelCopies** tulajdonság a **dataIntegrationUnits**felé merőleges. Az előbbi akkor számít az adatok integrációs összes száma közötti.
- Ha megad egy értéket a **parallelCopies** tulajdonsághoz, vegye figyelembe a terhelés növekedését a forrás-és fogadó adattárakban. Vegye figyelembe a terhelés növekedését is a saját üzemeltetésű integrációs modulban, ha a másolási tevékenységre például a hibrid másolásra van felhatalmazás. Ez a terhelés növekszik, különösen akkor, ha több tevékenység vagy ugyanazon tevékenység egyidejű futtatása történik ugyanazon az adattárban. Ha azt észleli, hogy az adattár vagy a saját üzemeltetésű integrációs modul túlterhelt a terheléssel, csökkentse a **parallelCopies** értékét a terhelés enyhítése érdekében.

**Példa:**

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

### <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

- **A különböző adattárakból származó adatok betöltését a SQL Data Warehouseon keresztül kell használni.** Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. A forrásadatok csak blob Storage-ban vagy Azure Data Lake Storeban szerepelhetnek, és meg kell felelniük a további feltételeknek. Ha egy adattár eltérő a Blob storage vagy az Azure Data Lake Store tölt be adatokat, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben a Azure Data Factory végrehajtja a szükséges adatátalakításokat annak érdekében, hogy az megfeleljen a bázisterület követelményeinek. A PolyBase majd az adatok betöltése az SQL Data Warehouse-bA hatékonyan használja. További információkért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattárból a Felhőbeli adattárolóba történő másolást) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos másolással tömörítheti a helyszíni adatok, így kevesebb időt vesz igénybe az adatok áthelyezése a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását a cél adattárba való betöltés előtt.
- **A vállalati informatikai házirendek miatt nem szeretné megnyitni a 80-as és a 443-es porton kívüli portokat a tűzfalon.** Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben az esetben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs modul előnyeit, hogy először másolja az adatait egy blob Storage-alapú átmeneti példányba HTTP vagy HTTPS protokollon keresztül a 443-es porton. Ezt követően az adatok betölthetők SQL Databaseba vagy SQL Data Warehouse a blob Storage átmeneti környezetből. Ebben a folyamatban nem kell 1433-as port engedélyezése.

#### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése

Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti tárolási Blob storage (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Azure Data Factory automatikusan kezeli a kétlépcsős folyamatot. Az adatáthelyezés befejezése után a Azure Data Factory az átmeneti tárolóból is törli az ideiglenes adatok mennyiségét.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Ha egy átmeneti tároló használatával aktiválja az adatáthelyezést, megadhatja, hogy szeretné-e tömöríteni az adatok tömörítését, mielőtt a forrás adattárból egy ideiglenes vagy átmeneti adattárba helyezi az adatátvitelt, majd kibontja az adatok ideiglenes vagy átmeneti dat-ból való áthelyezése előtt. egy tároló a fogadó adattárba.

Jelenleg nem másolhat Adatmásolást két olyan adattár között, amely különböző saját üzemeltetésű IRs-kapcsolaton keresztül csatlakozik, sem a, sem a szakaszos másolat nélkül. Ilyen esetben két explicit módon láncolt másolási tevékenységet konfigurálhat a forrásról az előkészítésre való másoláshoz, majd az előkészítésből a fogadóba.

#### <a name="configuration"></a>Konfiguráció

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

#### <a name="staged-copy-billing-impact"></a>Szakaszos másolás számlázási gyakorolt hatás

A díj két lépésből áll: a másolás időtartama és a másolás típusa alapján.

* Ha a Felhőbeli másolás során használ átmeneti tárolást, amely az adatok Felhőbeli adattárból egy másik Felhőbeli adattárba való másolását végzi el, az Azure Integration Runtime által felhatalmazott mindkét szakaszban az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] értékének összegét kell megfizetnie.
* Ha a hibrid másolás során átmeneti tárolást használ, amely egy helyszíni adattárból egy Felhőbeli adattárba másol egy olyan szakaszt, amely egy saját üzemeltetésű integrációs modul által felhatalmazott, akkor a [hibrid másolási időtartam] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

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
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
