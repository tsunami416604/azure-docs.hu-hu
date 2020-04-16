---
title: Tevékenységteljesítmény-optimalizálási funkciók másolása
description: Ismerje meg azazure Data Factory másolási tevékenységének teljesítményét optimalizáló legfontosabb funkciókat.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414188"
---
# <a name="copy-activity-performance-optimization-features"></a>Tevékenységteljesítmény-optimalizálási funkciók másolása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti a másolási tevékenység teljesítményoptimalizálási funkciókat, amelyeket az Azure Data Factoryban használhat.

## <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely egyetlen egység energiaellátását (a PROCESSZOR, a memória és a hálózati erőforrás-elosztás kombinációját) jelöli az Azure Data Factoryban. Az adatintegrációs egység csak az [Azure-integrációs futásidőre](concepts-integration-runtime.md#azure-integration-runtime)vonatkozik , de [az önkiszolgáló integrációs futásidőre](concepts-integration-runtime.md#self-hosted-integration-runtime)nem.

A másolási tevékenység futtatásának felhatalmazására engedélyezett DIUs **2 és 256 között**van. Ha nincs megadva, vagy a felhasználói felületen az "Automatikus" lehetőséget választja, a Data Factory dinamikusan alkalmazza az optimális DIU-beállítást a forrás-fogadó pár és az adatminta alapján. Az alábbi táblázat a támogatott DIU-tartományokat és az alapértelmezett viselkedést sorolja fel különböző másolási helyzetekben:

| Forgatókönyv másolása | Támogatott DIU tartomány | A szolgáltatás által meghatározott alapértelmezett azonosítók |
|:--- |:--- |---- |
| Fájltárolók között |- **Másolás egyetlen fájlból vagy fájlba:** 2-4 <br>- **Másolás több fájlból és több fájlba:** 2-256 a fájlok számától és méretétől függően <br><br>Ha például egy 4 nagy fájlt tartalmazó mappából másolja az adatokat, és úgy dönt, hogy megőrzi a hierarchiát, a maximális effektív DIU 16; ha úgy dönt, hogy egyesíti a fájlt, a maximális effektív DIU 4. |4 és 32 között, a fájlok számától és méretétől függően |
| Fájltárolóból nem fájltárolóba |- **Másolás egyetlen fájlból:** 2-4 <br/>- **Másolás több fájlból:** 2-256 a fájlok számától és méretétől függően <br/><br/>Ha például 4 nagy fájlt tartalmazó mappából másolja az adatokat, a maximális effektív DIU 16. |- **Másolás az Azure SQL Database vagy az Azure Cosmos DB könyvtárba:** 4 és 16 között, a fogadórétegtől (DDO/RUs) és a forrásfájl-mintától függően<br>- **Másolás az Azure Synapse Analytics szolgáltatásba** a PolyBase vagy copy utasítás használatával: 2<br>- Egyéb forgatókönyv: 4 |
| Nem fájltárolóból fájltárolóba |- **Másolás a partícióopciós adattárakból** (beleértve [az Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata-t](connector-teradata.md#teradata-as-source)is): 2-256, ha egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés forrásadat-partíciónként legfeljebb 4 DIUs-t használhat.<br>- **Egyéb forgatókönyvek**: 2-4 |- **Másolás REST vagy HTTP protokollból:** 1<br/>- **Másolás az Amazon Redshift-ből** a UNLOAD használatával: 2<br>- **Egyéb forgatókönyv**: 4 |
| Nem fájltárolók között |- **Másolás a partícióopciós adattárakból** (beleértve [az Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata-t](connector-teradata.md#teradata-as-source)is): 2-256, ha egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés forrásadat-partíciónként legfeljebb 4 DIUs-t használhat.<br/>- **Egyéb forgatókönyvek**: 2-4 |- **Másolás REST vagy HTTP protokollból:** 1<br>- **Egyéb forgatókönyv**: 4 |

A másolási tevékenység figyelési nézetében vagy tevékenységkimenetében megtekintheti az egyes példányfuttatásokhoz használt DIUs-ok. További információ: [A tevékenységfigyelés másolása.](copy-activity-monitoring.md) Az alapértelmezett beállítás felülbírálásához adjon `dataIntegrationUnits` meg egy értéket a tulajdonsághoz az alábbiak szerint. A másolási művelet által futásidőben használt *DIUs tényleges száma* az adatmintától függően egyenlő vagy kisebb, mint a beállított érték.

A használt **DIUs \* copy \* duration unit price/DIU-hour**díjat számítjuk fel. Lásd a jelenlegi árak [itt](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). A helyi pénznem és a külön diszkontálás előfizetéstípusonként alkalmazható.

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
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű integrációs futásidejű méretezhetőség

Ha nagyobb átviteli átatot szeretne elérni, felskálázhatja vagy kiskálázhatja a saját üzemeltetésű infravörös:

- Ha a processzor és a rendelkezésre álló memória a saját üzemeltetésű infravörös csomópont nem teljes mértékben kihasználják, de az egyidejű feladatok végrehajtása eléri a korlátot, fel kell skálázni a csomóponton futtatható egyidejű feladatok számának növelésével.  Lásd [itt](create-self-hosted-integration-runtime.md#scale-up) az utasításokat.
- Ha viszont a processzor magas a saját üzemeltetésű infravörös csomóponton, vagy kevés a rendelkezésre álló memória, hozzáadhat egy új csomópontot, amely segít a terhelés horizontális felskálázásában a több csomópont között.  Lásd [itt](create-self-hosted-integration-runtime.md#high-availability-and-scalability) az utasításokat.

A következő esetekben vegye figyelembe, hogy az egypéldányos tevékenység végrehajtása több saját üzemeltetésű infravörös csomópontot is kihasználhat:

- Adatok másolása fájlalapú tárolókból a fájlok számától és méretétől függően.
- Adatok másolása a partícióopciós adattárból (beleértve [az Oracle,](connector-oracle.md#oracle-as-source) [Netezza](connector-netezza.md#netezza-as-source), [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)és SAP Open [Hub-](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)ot), az adatpartíciók számától függően.

## <a name="parallel-copy"></a>Párhuzamos másolat

A másolási tevékenységen beállíthatja a párhuzamos másolási`parallelCopies` tevékenységet, jelezve a másolási tevékenység által használandó párhuzamosságot. Ezt a tulajdonságot a forrásból beolvasott vagy a fogadó adattárolóiba párhuzamosan írt másolási tevékenységen belüli szálak maximális számaként is felhasználhatja.

A párhuzamos másolat ortogonális [az adatintegrációs egységekhez](#data-integration-units) vagy [a saját üzemeltetésű infravörös csomópontokhoz.](#self-hosted-integration-runtime-scalability) A dius-ok vagy a saját üzemeltetésű ir-csomópontok között számít.

Minden egyes másolási tevékenység futtatása kor, alapértelmezés szerint az Azure Data Factory dinamikusan alkalmazza az optimális párhuzamos másolási beállítás alapján a forrás-fogadó pár és az adatok minta. 

> [!TIP]
> A párhuzamos másolat alapértelmezett viselkedése általában a legjobb átviteli értéket biztosítja, amelyet az ADF automatikusan határoz meg a forrás-fogadó pár, az adatminta és a DIUs-ok száma vagy a saját üzemeltetésű infravörös processzor/memória/csomópont száma alapján. Lásd: [Másolási tevékenység teljesítményének hibaelhárítása](copy-activity-performance-troubleshooting.md) a párhuzamos másolás behangolásához.

Az alábbi táblázat a párhuzamos másolási viselkedést sorolja fel:

| Forgatókönyv másolása | Párhuzamos másolási viselkedés |
| --- | --- |
| Fájltárolók között | `parallelCopies`meghatározza a **párhuzamossága a fájl szintjén**. Az egyes fájlokon belüli adattömbök automatikusan és transzparens módon történnek. Úgy tervezték, hogy egy adott adattártípushoz a legmegfelelőbb adattömbméretet használja az adatok párhuzamos betöltéséhez. <br/><br/>A párhuzamos másolatok másolási tevékenységének tényleges száma futásidőben nem több, mint a fájlok száma. Ha a másolási viselkedés **mergeFile** fájlgyűjtőbe, a másolási tevékenység nem tudja kihasználni a fájlszintű párhuzamosság előnyeit. |
| Fájltárolóból nem fájltárolóba | - Az Azure SQL Database vagy az Azure Cosmos DB adatok másolásakor az alapértelmezett párhuzamos másolás a fogadószinttől is függ (A DDO-k/RUS-ok száma).<br>- Az Azure Table-be történő adatok másolásakor az alapértelmezett párhuzamos másolat 4. |
| Nem fájltárolóból fájltárolóba | - A partícióopciós adattárból (beleértve [az Oracle,](connector-oracle.md#oracle-as-source) [Netezza](connector-netezza.md#netezza-as-source), [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)és SAP [Open Hub)](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)adatokmásolásakor az alapértelmezett párhuzamos másolat 4. A párhuzamos másolatok másolási tevékenységének tényleges száma futásidőben nem több, mint a megadott adatpartíciók száma. Ha önkiszolgáló integrációs futásidejű, és másolja az Azure Blob/ADLS Gen2, vegye figyelembe, hogy a maximális hatékony párhuzamos másolat 4 vagy 5 infravörös csomópontonként.<br>- Más esetekben a párhuzamos másolat nem lép érvénybe. Még ha meg is van adva a párhuzamosság, akkor sem alkalmazza azt. |
| Nem fájltárolók között | - Az Azure SQL Database vagy az Azure Cosmos DB adatok másolásakor az alapértelmezett párhuzamos másolás a fogadószinttől is függ (A DDO-k/RUS-ok száma).<br/>- A partícióopciós adattárból (beleértve [az Oracle,](connector-oracle.md#oracle-as-source) [Netezza](connector-netezza.md#netezza-as-source), [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)és SAP [Open Hub)](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)adatokmásolásakor az alapértelmezett párhuzamos másolat 4.<br>- Az Azure Table-be történő adatok másolásakor az alapértelmezett párhuzamos másolat 4. |

Az adattárakat tároló gépek terhelésének szabályozásához vagy a másolási teljesítmény beállításához felülbírálhatja `parallelCopies` az alapértelmezett értéket, és megadhatja a tulajdonság értékét. Az értéknek 1-nél nagyobb vagy azzal egyenlő egész számnak kell lennie. Futási időben a legjobb teljesítmény érdekében a másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő a beállított értékkel.

Amikor megad egy `parallelCopies` értéket a tulajdonsághoz, vegye figyelembe a forrás terhelésnövekedését, és vegye figyelembe az adattárakat. Is vegye figyelembe a terhelés növekedését a saját üzemeltetésű integrációs futásidejű, ha a másolási tevékenység által adhatja fel. Ez a terhelésnövekedés különösen akkor fordul elő, ha több tevékenységet vagy egyidejű futtatást végez ugyanazon tevékenységeken, amelyek ugyanazon az adattáron futnak. Ha azt veszi észre, hogy az adattár vagy a saját üzemeltetésű `parallelCopies` integrációs futásidejű túlterheltek a terhelés, csökkentse az értéket a terhelés enyhítésére.

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

## <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, dönthet úgy, hogy a Blob storage-ot ideiglenes átmeneti tárolóként használja. Az előkészítés különösen hasznos a következő esetekben:

- **Különböző adattárakból származó adatokat szeretne beadni az SQL Data Warehouse-ba a PolyBase-en keresztül.** Az SQL Data Warehouse a PolyBase-t használja nagy átviteli sebességű mechanizmusként nagy mennyiségű adat SQL Data Warehouse betöltéséhez. A forrásadatoknak a Blob storage-ban vagy az Azure Data Lake Store-ban kell lenniük, és további feltételeknek kell megfelelniük. Ha nem a Blob storage vagy az Azure Data Lake Store adattárból tölt be adatokat, aktiválhatja az adatok másolását a köztes átmeneti Blob-tárolón keresztül. Ebben az esetben az Azure Data Factory elvégzi a szükséges adatátalakításokat annak érdekében, hogy azok megfeleljenek a PolyBase követelményeinek. Ezután a PolyBase segítségével hatékonyan tölti be az adatokat az SQL Data Warehouse-ba. További információ: [A PolyBase használata adatok betöltéséhez az Azure SQL Data Warehouse-ba](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)című témakörben található.
- **Néha eltart egy ideig, amíg egy hibrid adatáthelyezés (azaz a másolás egy helyszíni adattárból egy felhőbeli adattárba) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos másolással tömörítheti az adatokat a helyszínen, így kevesebb időt vesz igénybe az adatok áthelyezése a felhőben lévő átmeneti adattárba. Ezután kibonthatja az adatokat az átmeneti tárolóban, mielőtt betöltődne a céladattárba.
- **A vállalati informatikai házirendek miatt a 80-as és a 443-as porton kívül más portokat sem szeretne megnyitni a tűzfalban.** Ha például adatokat másol egy helyszíni adattárból egy Azure SQL Database-fogadóba vagy egy Azure SQL Data Data Warehouse-fogadóba, aktiválnia kell a kimenő TCP-kommunikációt az 1433-as porton a Windows tűzfal és a vállalati tűzfal esetében is. Ebben a forgatókönyvben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs futásidejű adatok első másolása egy Blob storage átmeneti példány http-n vagy HTTPS-en keresztül a 443-as porton. Ezután betöltheti az adatokat az SQL Database vagy az SQL Data Warehouse a Blob storage átmeneti. Ebben a folyamatban nem kell engedélyeznie az 1433-as portot.

### <a name="how-staged-copy-works"></a>A szakaszos másolás működése

Az átmeneti szolgáltatás aktiválásakor először az adatokat másolja a forrás-adattárból az átmeneti Blob-tárolóba (hozza magával a sajátját). Ezután az adatok az átmeneti adattárból a fogadó adattárba lesznek másolva. Az Azure Data Factory automatikusan kezeli a kétlépcsős folyamatot. Az Azure Data Factory is törli az ideiglenes adatokat az átmeneti tárolóból az adatáthelyezés befejezése után.

![Szakaszos másolás](media/copy-activity-performance/staged-copy.png)

Ha átmeneti tároló használatával aktiválja az adatmozgást, megadhatja, hogy az adatok tömörítése a forrás-adattárból egy köztes vagy átmeneti adattárba történő áthelyezése előtt, majd a kibontott állapotba kerüljön-e, mielőtt az adatokat áthelyezi egy köztes vagy átmeneti adattárból a fogadó adattárba.

Jelenleg nem másolhat adatokat két adattár között, amelyek különböző saját üzemeltetésű írási adatokon keresztül kapcsolódnak, sem szakaszos másolással, sem anélkül. Ilyen esetben konfigurálhat két explicit módon láncolt másolási tevékenységet a forrásból az átmeneti, majd az átmeneti fogadóba történő másoláshoz.

### <a name="configuration"></a>Konfiguráció

Konfigurálja az **EnableStaging** beállítást a másolási tevékenységben annak megadásához, hogy az adatokat a Blob storage-ban kívánja-e előadni, mielőtt betölti azokat a céladattárba. Ha az **enableStaging** beállítását `TRUE`állítja be, adja meg az alábbi táblázatban felsorolt további tulajdonságokat. Azure Storage- vagy Storage-megosztott hozzáférésű, aláíráshoz kötött szolgáltatást is létre kell hoznia az előkészítéshez, ha nem rendelkezik ilyensel.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| engedélyezés |Adja meg, hogy szeretne-e adatokat másolni egy köztes átmeneti tárolón keresztül. |False (Hamis) |Nem |
| linkedServiceName |Adja meg egy [AzureStorage-kapcsolt](connector-azure-blob-storage.md#linked-service-properties) szolgáltatás nevét, amely a tároló példányára hivatkozik, amelyet ideiglenes átmeneti tárolóként használ. <br/><br/> A Storage megosztott hozzáférésű aláírással nem használható adatok sql data warehouse-ba polybase-en keresztül történő betöltéséhez. Használhatja az összes többi forgatókönyv. |N/A |Igen, ha az **enableStaging** értéke TRUE |
| path |Adja meg a blob tárolási elérési útját, amelyet a szakaszos adatokat szeretne tárolni. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adatok tárolására. <br/><br/> Csak akkor adjon meg elérési utat, ha a Storage-ot megosztott hozzáférésű aláírással használja, vagy ha ideiglenes adatokra van szüksége egy adott helyen. |N/A |Nem |
| engedélyezésTömörítés |Itt adható meg, hogy az adatokat tömöríteni kell-e, mielőtt a célhelyre másolnák őket. Ez a beállítás csökkenti az átvitt adatok mennyiségét. |False (Hamis) |Nem |

>[!NOTE]
> Ha a szakaszos másolás tágító tömörítés engedélyezve van, az egyszerű szolgáltatás vagy MSI hitelesítés átmeneti blob csatolt szolgáltatás nem támogatott.

Az alábbiakban egy másolási tevékenység mintadefinícióját ismertetik az előző táblázatban ismertetett tulajdonságokkal:

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

### <a name="staged-copy-billing-impact"></a>A példányszámlázás szakaszos számlázási hatása

A díjat két lépés alapján terheljük meg: a másolás időtartama és a másolás típusa alapján.

* Ha egy felhőalapú másolat során használja az előkészítést, amely adatokat másol egy felhőbeli adattárból egy másik felhőbeli adattárba, mindkét szakaszban, amelyet az Azure integrációs futásidő hatalma tart, akkor a [másolási időtartam összege az 1. és a 2. lépés] x [felhőalapú másolási egységár].
* Ha egy hibrid példány során használja az előkészítést, amely adatokat másol egy helyszíni adattárból egy felhőalapú adattárba, amely egy saját üzemeltetésű integrációs futásidejű által felhatalmazott szakasz, akkor a [hibrid másolás időtartama] x [hibrid másolási egységára] + [felhőalapú másolási időtartam] x [felhőalapú másolási egységára] díjat számítunk fel.

## <a name="next-steps"></a>További lépések
Lásd a többi másolási tevékenységcikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [A tevékenység teljesítményének és méretezhetőségének másolása útmutató](copy-activity-performance.md)
- [Másolási tevékenység teljesítményének – problémamegoldás](copy-activity-performance-troubleshooting.md)
- [Az Azure Data Factory segítségével áttelepítheti az adatokat az adattóból vagy az adattárházból az Azure-ba](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)