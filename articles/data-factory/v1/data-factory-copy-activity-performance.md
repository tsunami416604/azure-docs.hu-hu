---
title: Útmutató a másolási tevékenységek teljesítményéhez és finomhangolásához
description: Ismerje meg azokat a kulcsfontosságú tényezőket, amelyek befolyásolják az adatok mozgását az Azure Data Factoryban a Másolási tevékenység használatakor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527384"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Útmutató a másolási tevékenységek teljesítményéhez és finomhangolásához

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-copy-activity-performance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-performance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Tevékenység teljesítményének másolása és hangolási útmutatója a Data Factory számára című témakört.](../copy-activity-performance.md)

Az Azure Data Factory Copy Activity első osztályú biztonságos, megbízható és nagy teljesítményű adatbetöltési megoldást biztosít. Lehetővé teszi, hogy naponta több tíz terabájtnyi adatot másoljon a felhőbeli és helyszíni adattárak széles választékába. A villámgyors adatbetöltési teljesítmény kulcsfontosságú annak biztosításához, hogy az alapvető "big data" problémára összpontosíthasson: fejlett elemzési megoldásokat hozhat ki, és részletes betekintést nyerhet az összes adatból.

Az Azure nagyvállalati szintű adattárolási és adattárház-megoldásokat kínál, a Másolási tevékenység pedig rendkívül optimalizált adatbetöltési élményt kínál, amely könnyen konfigurálható és beállítható. Egyetlen másolási tevékenységgel a következőket érheti el:

* Adatok betöltése az **Azure SQL Data Warehouse-ba** **1,2 GBps sebességgel.** A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.
* Adatok betöltése az Azure Blob **1,0 GBps-es** **tárhelyén**
* Adatok betöltése az **Azure Data Lake Store áruházba** **1,0 GBps-en**

Ez a cikk a következőket ismerteti:

* A támogatott forrás- és fogadóadattárak [teljesítményhivatkozási számai](#performance-reference) a projekt megtervezéséhez;
* Olyan funkciók, amelyek növelhetik a másolási átviteli sebességet a különböző forgatókönyvekben, beleértve a [felhőalapú adatmozgatási egységeket](#cloud-data-movement-units), [a párhuzamos másolást](#parallel-copy)és a [szakaszos másolást](#staged-copy);
* [Teljesítményhangolási útmutató](#performance-tuning-steps) a teljesítmény és a másolási teljesítményt befolyásoló kulcsfontosságú tényezők beállításához.

> [!NOTE]
> Ha általában nem ismeri a Másolási tevékenységet, olvassa el az [Adatok áthelyezése a Másolási tevékenység használatával című](data-factory-data-movement-activities.md) témakört a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítményreferencia

Referenciaként az alábbi táblázat az adott forrás- és fogadópárok másolási átviteli sebességének számát mutatja az adott forrás- és fogadópárhoz a házon belüli tesztelés alapján. Összehasonlításképpen azt is bemutatja, hogy a [felhőbeli adatmozgatási egységek](#cloud-data-movement-units) vagy [az adatkezelési átjáró méretezhetősége](data-factory-data-management-gateway-high-availability-scalability.md) (több átjárócsomópont) különböző beállításai hogyan segíthetnek a másolási teljesítményben.

![Teljesítménymátrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Az Azure Data Factory 1-es verziójában a felhőből a felhőbe irányuló másolás minimális felhőalapú adatmozgatási egységei kettő. Ha nincs megadva, tekintse meg a [felhőalapú adatmozgatási egységekben használt alapértelmezett adatmozgatási egységeket.](#cloud-data-movement-units)

**Megjegyzésre mutat:**
* Az átviteli forgalmat a következő képlet telje: [a forrásból beolvasott adatok mérete]/[A tevékenység futásának időtartama másolása].
* A táblázatban szereplő teljesítményhivatkozási számokat [tpc-h](http://www.tpc.org/tpch/) adatkészlet használatával mértük egyetlen másolási tevékenységfuttatássorán.
* Az Azure-adattárakban a forrás és a fogadó ugyanabban az Azure-régióban található.
* A helyszíni és a felhőbeli adattárak közötti hibrid másolás esetén minden átjárócsomópont egy olyan gépen futott, amely elkülönült a helyszíni adattártól az alábbi specifikációval. Amikor egyetlen tevékenység futott az átjárón, a másolási művelet a tesztgép processzorának, memóriájának vagy hálózati sávszélességének csak egy kis részét használta fel. További információ [az Adatkezelési átjáró ról.](#considerations-for-data-management-gateway)
    <table>
    <tr>
        <td>CPU</td>
        <td>32 mag 2,20 GHz-es Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memory (Memória)</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Network (Hálózat)</td>
        <td>Internet interfész: 10 Gbps; intranetes interfész: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Nagyobb átviteli sebességet érhet el, ha több adatmozgatási egységet (DMU)-t kihasznál, mint az alapértelmezett maximális DMU-k, amely 32 a felhőből a felhőbe irányuló másolási tevékenység futtatásához. A 100 DMU-val például **1,0 Mb/s sebességgel**másolhat adatokat az Azure Blobból az Azure Data Lake Store áruházba. A [felhőbeli adatmozgatási egységek](#cloud-data-movement-units) című szakaszban a funkcióval és a támogatott forgatókönyvvel kapcsolatos részleteket. Lépjen kapcsolatba az [Azure-támogatással,](https://azure.microsoft.com/support/) ha további DMU-kat szeretne kérni.

## <a name="parallel-copy"></a>Párhuzamos másolat
Az adatokat a forrásból olvashatja, vagy az adatokat párhuzamosan is átírhatja a célhelyre **a Másolási tevékenység futtatásán belül.** Ez a funkció növeli a másolási művelet átviteli mertékét, és csökkenti az adatok áthelyezéséhez szükséges időt.

Ez a beállítás eltér a tevékenységdefinícióban szereplő **egyidejűségi** tulajdonságtól. Az **egyidejűségi** tulajdonság határozza meg az **egyidejű másolási tevékenység futtatásainak** számát a különböző tevékenységi ablakokból (1:00 és 2:00, 2:00 és 3:00, 3:00 és 4:00 között és így tovább) történő feldolgozáshoz. Ez a funkció akkor hasznos, ha egy korábbi terhelés végrehajtása. A párhuzamos másolási funkció **egyetlen tevékenységfuttatásra**vonatkozik.

Nézzünk egy minta forgatókönyvet. A következő példában több múltbeli szeletet kell feldolgozni. A Data Factory minden szelethez futtatja a Másolási tevékenység (tevékenységfuttatás) egy példányát:

* Az adatszelet az első tevékenységablakból (1:00-2:00) ==> Tevékenység futtatása 1
* Az adatszelet a második tevékenységablakból (2:00-tól 3:m-ig) ==> Tevékenység futtatása 2
* Az adatszelet a második tevékenységablakból (3:00-tól 4:m-ig) ==> Tevékenység futtatása 3

És így tovább.

Ebben a példában, ha az **egyidejűségi** érték 2, **tevékenység futtatása 1** és **a tevékenység fut 2** másolási adatokkét két tevékenység ablak egyidejűleg az adatmozgási teljesítmény javítása **érdekében.** Ha azonban több fájl van társítva az 1-es tevékenység futtatáshoz, az adatmozgatási szolgáltatás egyszerre egy fájlból másolja a fájlokat a forrásból a célhelyre.

### <a name="cloud-data-movement-units"></a>Felhőalapú adatmozgatási egységek
A **felhőalapú adatmozgatási egység (DMU)** olyan mérték, amely a Data Factory egyetlen egységének (a PROCESSZOR, a memória és a hálózati erőforrás-lefoglalás kombinációját) képviseli. A DMU a felhőből a felhőbe irányuló másolási műveletekhez alkalmazható, de hibrid példányban nem.

**A minimális felhőadat-mozgatási egységek, amelyek képessé teszik a másolási tevékenység futtatását, kettő.** Ha nincs megadva, a következő táblázat a különböző másolási esetekben használt alapértelmezett DMU-kat sorolja fel:

| Forgatókönyv másolása | A szolgáltatás által meghatározott alapértelmezett DMU-k |
|:--- |:--- |
| Adatok másolása fájlalapú tárolók között | 4 és 16 között, a fájlok számától és méretétől függően. |
| Minden más másolási forgatókönyv | 4 |

Az alapértelmezett felülíráshoz adja meg a **cloudDataMovementUnits** tulajdonság értékét az alábbiak szerint. A **cloudDataMovementUnits** tulajdonság **megengedett értékei:** 2, 4, 8, 16, 32. A **felhőalapú DMU-k tényleges száma,** amelyet a másolási művelet futásidőben használ, egyenlő vagy kisebb, mint a beállított érték, az adatmintától függően. A teljesítménynövekedés szintjéről, amelyet akkor kaphat, amikor több egységet konfigurál egy adott másolási forráshoz és fogadóhoz, olvassa el a [teljesítményre vonatkozó hivatkozást.](#performance-reference)

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
> Ha több felhőalapú DMU-ra van szüksége a nagyobb átviteli igény érdekében, forduljon az [Azure ügyfélszolgálatához.](https://azure.microsoft.com/support/) A 8-as és újabb beállítás jelenleg csak akkor működik, ha **több fájlt másol a Blob Storage/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP szolgáltatásból a Blob storage/Data Lake Store/Azure SQL Database könyvtárba.**
>

### <a name="parallelcopies"></a>párhuzamos másolatok
A **parallelCopies** tulajdonsággal jelezheti a másolási tevékenység által használandó párhuzamosságot. Ezt a tulajdonságot a másolási tevékenységen belül a forrásból olvasható szálak maximális számaként használhatja, vagy párhuzamosan írhat a fogadó adattárolóiba.

A Data Factory minden egyes másolási tevékenység futtatásakor meghatározza a forrásadattárból és a céladattárból az adatok másolásához használt párhuzamos másolatok számát. Az általa használt párhuzamos másolatok alapértelmezett száma a használt forrás és fogadó típusától függ.

| Forrás és mosogató | A szolgáltatás által meghatározott alapértelmezett párhuzamos másolási számláló |
| --- | --- |
| Adatok másolása fájlalapú tárolók között (Blob storage; Data Lake Áruház; Amazon S3; helyszíni fájlrendszer; helyszíni HDFS) |1 és 32 között. A fájlok méretétől és a két felhőbeli adattárak közötti adatok másolásához használt felhőalapú adatmozgató egységek (DMU-k) számától, vagy a hibrid másolathoz használt átjárógép fizikai konfigurációjától függ (adatok másolása a helyszíni adattárba vagy onnan). |
| Adatok másolása **bármely forrásadattárból az Azure Table tárolóba** |4 |
| Minden más forrás- és mosogatópár |1 |

Általában az alapértelmezett viselkedés a legjobb átviteli értéket adja. Az adattárakat tároló gépek terhelésének szabályozásához vagy a másolási teljesítmény beállításához azonban felülbírálhatja az alapértelmezett értéket, és megadhatja a **parallelCopies** tulajdonság értékét. Az értéknek 1 és 32 között kell lennie (mindkettő bezárólag). Futási időben a legjobb teljesítmény érdekében a Másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő a beállított értékkel.

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
Megjegyzésre mutat:

* Amikor adatokat másol a fájlalapú tárolók között, a **párhuzamosmásolatok** fájlszinten határozzák meg a párhuzamosságokat. A darabolás egyetlen fájlbelül automatikusan és átláthatóan történik, és úgy tervezték, hogy a legmegfelelőbb adattömbméretet használja egy adott forrásadattár típusához az adatok párhuzamos és ortogonális párhuzamos másolatokra történő betöltéséhez. Az adatmozgatási szolgáltatás futásidőben történő másolási művelethez használt párhuzamos másolatainak tényleges száma nem több, mint a fájlok száma. Ha a másolási viselkedés **mergeFile**, copy activity nem tudja kihasználni a fájlszintű párhuzamosság.
* Ha megad egy értéket a **parallelCopies** tulajdonsághoz, vegye figyelembe a forrás- és fogadóadattárak terhelésnövekedését, valamint az átjárót, ha hibrid másolatról van szó. Ez különösen akkor fordul elő, ha több tevékenységet vagy egyidejű futtatást végez ugyanazon tevékenységeken, amelyek ugyanazon az adattáron futnak. Ha azt veszi észre, hogy az adattár vagy az átjáró túlterheltek a terhelés, csökkentse a **parallelCopies** értéket a terhelés csökkentése érdekében.
* Ha nem fájlalapú tárolókból másol adatokat fájlalapú tárolókba, az adatmozgatási szolgáltatás figyelmen kívül hagyja a **parallelCopies tulajdonságot.** Még akkor is, ha a párhuzamosság meg van adva, ebben az esetben nincs alkalmazva.

> [!NOTE]
> Hibrid példány esetén az Adatkezelési átjáró 1.11-es vagy újabb verzióját kell használnia a **parallelCopies** szolgáltatás használatához.
>
>

A két tulajdonság jobb használatához és az adatátviteli átviteli sebesség növeléséhez tekintse meg a használati esetek mintaadatait. Az alapértelmezett viselkedés kihasználásához nem kell **konfigurálnia a párhuzamosmásolatokat.** Ha konfigurálja, és **a parallelCopies** túl kicsi, előfordulhat, hogy több felhőalapú DMU-k nem teljes mértékben kihasználják.

### <a name="billing-impact"></a>Számlázási hatás
Fontos megjegyezni, hogy a másolási művelet teljes ideje alapján kell fizetnie. **important** Ha egy másolási feladat egy órát vett igénybe egy felhőegységgel, és most 15 percet vesz igénybe négy felhőegységgel, a teljes számla majdnem ugyanaz marad. Négy felhőegységet például használ. Az első felhőegység 10 percet, a második 10 percet, a harmadikat 5 percet, a negyediket pedig 5 percet tölt egy másolási tevékenység futással. A teljes másolási (adatmozgási) időt 10 + 10 + 5 + 5 = 30 perc értékében számítjuk fel. **A párhuzamos másolatok** használata nincs hatással a számlázásra.

## <a name="staged-copy"></a>Szakaszos másolás
Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, dönthet úgy, hogy a Blob storage-ot ideiglenes átmeneti tárolóként használja. Az előkészítés különösen hasznos a következő esetekben:

1. **Különböző adattárakból származó adatokat szeretne beadni az SQL Data Warehouse-ba a PolyBase segítségével.** Az SQL Data Warehouse a PolyBase-t használja nagy átviteli sebességű mechanizmusként nagy mennyiségű adat SQL Data Warehouse betöltéséhez. A forrásadatoknak azonban a Blob storage-ban kell lenniük, és további feltételeknek kell megfelelniük. Ha nem a Blob-tárolóból tölt be adatokat, aktiválhatja az adatok másolását a köztes átmeneti Blob-tárolón keresztül. Ebben az esetben a Data Factory elvégzi a szükséges adatátalakításokat annak érdekében, hogy azok megfeleljenek a PolyBase követelményeinek. Ezután a PolyBase segítségével tölti be az adatokat az SQL Data Warehouse-ba. További részletekért olvassa el az [Adatok Azure SQL Data Warehouse betöltéséhez](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)a PolyBase használata című témakört. A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.
2. **Néha eltart egy ideig, amíg egy hibrid adatáthelyezés (azaz a másolás között egy helyszíni adattár és egy felhőbeli adattár) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében tömörítheti az adatokat a helyszínen, így kevesebb időt vesz igénybe az adatok áthelyezése a felhőben lévő átmeneti adattárba. Ezután kibonthatja az adatokat az átmeneti tárolóban, mielőtt betöltené őket a céladattárba.
3. **A vállalati informatikai házirendek miatt a 80-as és a 443-as porton**kívül más portokat sem szeretne megnyitni a tűzfalban. Ha például adatokat másol egy helyszíni adattárból egy Azure SQL Database-fogadóba vagy egy Azure SQL Data Data Warehouse-fogadóba, aktiválnia kell a kimenő TCP-kommunikációt az 1433-as porton a Windows tűzfal és a vállalati tűzfal esetében is. Ebben a forgatókönyvben használja ki az átjáró t, amely első alkalommal másolja az adatokat egy Blob storage átmeneti példányhttp-n vagy HTTPS-en keresztül a 443-as porton. Ezután töltse be az adatokat az SQL Database vagy az SQL Data Warehouse a Blob storage átmeneti. Ebben a folyamatban nem kell engedélyeznie az 1433-as portot.

### <a name="how-staged-copy-works"></a>A szakaszos másolás működése
Az átmeneti szolgáltatás aktiválásakor először az adatokat másolja a forrás-adattárból az átmeneti adattárba (hozza magával a sajátját). Ezután az adatok az átmeneti adattárból a fogadó adattárba lesznek másolva. A Data Factory automatikusan kezeli a kétlépcsős folyamatot. A Data Factory az átmeneti tárolóból is törli az ideiglenes adatokat az adatáthelyezés befejezése után.

A felhőbeli másolási forgatókönyvben (mind a forrás-, mind a fogadó adattárak a felhőben vannak), az átjáró nem használatos. A Data Factory szolgáltatás végrehajtja a másolási műveleteket.

![Szakaszos másolás: Felhőbeli forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

A hibrid másolási forgatókönyvben (a forrás a helyszínen van, és a fogadó a felhőben van), az átjáró adatokat helyezi át a forrás-adattárból egy átmeneti adattárba. A Data Factory szolgáltatás áthelyezi az adatokat az átmeneti adattárból a fogadó adattárába. A fordított folyamat támogatja az adatok másolása egy felhőbeli adattárból egy helyszíni adattárba átmeneti keresztül is támogatja a fordított folyamat.

![Szakaszos másolás: Hibrid forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ha átmeneti tároló használatával aktiválja az adatmozgást, megadhatja, hogy az adatok tömörítése a forrás-adattárból egy köztes vagy átmeneti adattárba történő áthelyezése előtt legyen-e, majd a kibontást, mielőtt adatokat helyezne át egy köztes vagy átmeneti adattárból. tárolóba a fogadó adattárba.

Jelenleg nem másolhatja az adatokat két helyszíni adattár között egy átmeneti tároló használatával. Arra számítunk, hogy ez a lehetőség hamarosan elérhető lesz.

### <a name="configuration"></a>Konfiguráció
Állítsa be az **EnableStaging** beállítást a Másolási tevékenységben annak megadásához, hogy az adatokat a Blob storage-ban kívánja-e előadni, mielőtt betölti azokat a céladattárba. Ha **az enableStaging** értéket TRUE értékre állítja, adja meg a következő táblázatban felsorolt további tulajdonságokat. Ha nem rendelkezik ilyen, akkor is létre kell hoznia egy Azure Storage vagy Storage megosztott hozzáférésű aláírás-kapcsolt szolgáltatás átmeneti.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| **engedélyezés** |Adja meg, hogy szeretne-e adatokat másolni egy köztes átmeneti tárolón keresztül. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) kapcsolt szolgáltatás nevét, amely a tároló példányára hivatkozik, amelyet ideiglenes átmeneti tárolóként használ. <br/><br/> A Storage megosztott hozzáférésű aláírással nem használható adatok SQL Data Warehouse-ba való betöltéséhez a PolyBase-en keresztül. Használhatja az összes többi forgatókönyv. |N/A |Igen, ha az **enableStaging** értéke TRUE |
| **Elérési út** |Adja meg a blob tárolási elérési útját, amelyet a szakaszos adatokat szeretne tárolni. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adatok tárolására. <br/><br/> Csak akkor adjon meg elérési utat, ha a Storage-ot megosztott hozzáférésű aláírással használja, vagy ha ideiglenes adatokra van szüksége egy adott helyen. |N/A |Nem |
| **engedélyezésTömörítés** |Itt adható meg, hogy az adatokat tömöríteni kell-e, mielőtt a célhelyre másolnák őket. Ez a beállítás csökkenti az átvitt adatok mennyiségét. |False (Hamis) |Nem |

Az alábbiakban a Másolási tevékenység mintadefiníciója szerepel az előző táblázatban ismertetett tulajdonságokkal:

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

### <a name="billing-impact"></a>Számlázási hatás
A díjat két lépés alapján terheli: a másolás időtartama és a másolás típusa.

* Ha egy felhőbeli másolás során használja az előkészítést (adatok másolása egy felhőbeli adattárból egy másik felhőbeli adattárba), akkor az 1.
* Ha hibrid másolat során használja az előkészítést (adatok másolása egy helyszíni adattárból egy felhőbeli adattárba), a [hibrid másolás időtartama] x [hibrid másolási egységára] + [felhőalapú másolás időtartama] x [felhőalapú másolási egységára] díjat számítunk fel.

## <a name="performance-tuning-steps"></a>Teljesítményhangolási lépések
Javasoljuk, hogy tegye meg az alábbi lépéseket a Data Factory szolgáltatás teljesítményének másolási tevékenységgel történő finomhangolásához:

1. **Alapterv létrehozása**. A fejlesztési fázisban tesztelje a folyamatot a Másolási tevékenység használatával egy reprezentatív adatminta alapján. A Data Factory [szeletelési modell](data-factory-scheduling-and-execution.md) segítségével korlátozhatja a használt adatok mennyiségét.

   Gyűjtse össze a végrehajtási időt és a teljesítmény jellemzőket a **Monitoring and Management alkalmazás segítségével.** Válassza **a & kezelés figyelése** lehetőséget a Data Factory kezdőlapján. A fa nézetben válassza ki a **kimeneti adatkészletet**. A **Tevékenység Windows** listájában válassza a Tevékenység másolása futtatást. **A Tevékenység Windows** felsorolja a Másolási tevékenység időtartamát és a másolt adatok méretét. Az átviteli képesség a **Tevékenységablak-kezelőben**jelenik meg. Ha többet szeretne megtudni az alkalmazásról, olvassa el az [Azure Data Factory-folyamatok figyelése és kezelése a Figyelési és Felügyeleti alkalmazás használatával című témakört.](data-factory-monitor-manage-app.md)

   ![Tevékenységfuttatás részletei](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   A cikk későbbi részében összehasonlíthatja a forgatókönyv teljesítményét és konfigurációját a Másolási tevékenység [teljesítményreferenciájával](#performance-reference) a tesztjeinkből.
2. **A teljesítmény diagnosztizálása és optimalizálása.** Ha a megfigyelt teljesítmény nem felel meg az elvárásainak, azonosítania kell a teljesítménybeli szűk keresztmetszeteket. Ezután optimalizálja a teljesítményt a szűk keresztmetszetek eltávolításához vagy csökkentéséhez. A teljesítménydiagnosztika teljes leírása túlmutat a jelen cikk hatókörén, de itt van néhány gyakori szempont:

   * Teljesítmény jellemzők:
     * [Párhuzamos másolat](#parallel-copy)
     * [Felhőalapú adatmozgatási egységek](#cloud-data-movement-units)
     * [Szakaszos másolás](#staged-copy)
     * [Adatkezelési átjáró méretezhetősége](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Adatkezelési átjáró](#considerations-for-data-management-gateway)
   * [Forrás](#considerations-for-the-source)
   * [Sink (Fogadó)](#considerations-for-the-sink)
   * [Szerializálás és deszerializálás](#considerations-for-serialization-and-deserialization)
   * [Tömörítés](#considerations-for-compression)
   * [Oszlopleképezés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)
3. **Bontsa ki a konfigurációt a teljes adatkészletre.** Ha elégedett a végrehajtási eredményekkel és a teljesítménnyel, kibonthatja a definíciót és a folyamat aktív időszakát a teljes adatkészlet lefedésére.

## <a name="considerations-for-data-management-gateway"></a>Az adatkezelési átjáróval kapcsolatos szempontok
**Átjáró beállítása**: Azt javasoljuk, hogy egy dedikált gépet használjon az adatkezelési átjáró üzemeltetéséhez. Az [Adatkezelési átjáró használatának szempontjai.](data-factory-data-management-gateway.md#considerations-for-using-gateway)

**Átjárófigyelés és felskálázás/-ki:** Egy vagy több átjárócsomóval rendelkező logikai átjáró egyidejűleg több másolási tevékenységfuttatást is kiszolgálhat. Megtekintheti az erőforrás-kihasználtság (CPU, memória, hálózat(be/ki) közel valós idejű pillanatképét egy átjárógépen, valamint az Azure Portalon futó egyidejű feladatok számát, lásd: [Átjáró figyelése a portálon.](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal) Ha nagy szükség van a hibrid adatáthelyezésre, vagy nagy számú egyidejű másolási tevékenység fut, vagy nagy mennyiségű adatot másolni, fontolja meg, hogy [horizontális felskálázása vagy horizontális felskálázása átjáró,](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) hogy jobban kihasználja az erőforrást, vagy több erőforrást, hogy felhatalmazza a másolást.

## <a name="considerations-for-the-source"></a>A forrással kapcsolatos szempontok
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterheltek más számítási feladatok, amelyek futnak rajta vagy ellene.

A Microsoft-adattárakról az adattárakra vonatkozó [figyelési és hangolási témaköröket](#performance-reference) olvashatja, amelyek segítenek az adattár teljesítményjellemzőinek megértésében, a válaszidők minimalizálásában és az átviteli sebesség maximalizálásában.

Ha adatokat másol a Blob storage-ból az SQL Data Warehouse-ba, fontolja meg a **PolyBase** használatát a teljesítmény növelése érdekében. A részletekért olvassa el [a PolyBase használata az adatok azure SQL Data Warehouse betöltéséhez](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) című témakört. A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Tartalmazza a Blob-tárolót, a Data Lake Store-t, az Amazon S3-at, a helyszíni fájlrendszereket és a helyszíni HDFS-t)*

* **Átlagos fájlméret és fájlszám**: A másolási tevékenység egyszerre egy fájlt továbbít. Azonos mennyiségű adatot kell áthelyezni, a teljes átviteli alacsonyabb, ha az adatok sok kis fájlokat, nem pedig néhány nagy fájlok miatt a bootstrap fázis minden fájlt. Ezért, ha lehetséges, egyesítse a kis fájlokat nagyobb fájlokba, hogy nagyobb átviteli átalakulást nyerjen.
* **Fájlformátum és tömörítés:** A teljesítmény javításának további módjaiért tekintse meg a [szerializálásés deszerializálás szempontjait,](#considerations-for-serialization-and-deserialization) [valamint a tömörítési](#considerations-for-compression) szakaszok szempontjaicímű témakört.
* A **helyszíni fájlrendszer-forgatókönyv,** amelyben **adatkezelési átjáró** szükséges, lásd: [Az adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) című szakaszban.

### <a name="relational-data-stores"></a>Relációs adattárak
*(Tartalmazza az SQL Database-t; SQL adattárház; Amazon Redshift; SQL Server adatbázisok; és Oracle, MySQL, DB2, Teradata, Sybase és PostgreSQL adatbázisok, stb.)*

* **Adatminta**: A táblaséma befolyásolja a másolási átviteli forgalmat. A nagy sorméret jobb teljesítményt nyújt, mint a kis sorméret, ha azonos mennyiségű adatot másol. Ennek az az oka, hogy az adatbázis hatékonyabban tudja beolvasni a kevesebb sort tartalmazó adatkötegek et.
* **Lekérdezés vagy tárolt eljárás:** Az adatok hatékonyabb lekéréséhez optimalizálja a Tevékenység másolása forrásban megadott lekérdezés vagy tárolt eljárás logikáját.
* A helyszíni relációs adatbázisok , például az SQL Server és az Oracle, amelyek az **adatkezelési átjáró**használatát **igénylik,** az Adatkezelési átjáró szempontjai című szakaszban találhatók.

## <a name="considerations-for-the-sink"></a>A mosogatóval kapcsolatos szempontok
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterheltek más számítási feladatok, amelyek futnak rajta vagy ellene.

A Microsoft-adattárak esetében tekintse meg az adattárakra vonatkozó [figyelési és hangolási](#performance-reference) témaköröket. Ezek a témakörök segítenek megérteni az adattár teljesítményjellemzőit, valamint azt, hogy miként minimalizálhatja a válaszidőket és maximalizálhatja az átviteli teljesítményt.

Ha adatokat másol a **Blob storage-ból** az **SQL Data Warehouse-ba,** fontolja meg a **PolyBase** használatát a teljesítmény növelése érdekében. A részletekért olvassa el [a PolyBase használata az adatok azure SQL Data Warehouse betöltéséhez](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) című témakört. A használati eset forgatókönyve: [1 TB betöltése az Azure SQL Data Warehouse-ba 15 percen belül az Azure Data Factory segítségével](data-factory-load-sql-data-warehouse.md)című témakörben található.

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Tartalmazza a Blob-tárolót, a Data Lake Store-t, az Amazon S3-at, a helyszíni fájlrendszereket és a helyszíni HDFS-t)*

* **Másolási viselkedés:** Ha adatokat másol egy másik fájlalapú adattárból, a Másolási tevékenység három lehetőségközül választhat a **copyBehavior** tulajdonságon keresztül. Megőrzi a hierarchiát, összeolvasztja a hierarchiát vagy egyesíti a fájlokat. A hierarchia megőrzése vagy összeolvasztása kevés vagy egyáltalán nem rendelkezik teljesítményterheléssel, de a fájlok egyesítése növeli a teljesítménytöbbletet.
* **Fájlformátum és tömörítés**: A teljesítmény javításának további [módjairól a szerializálással és deszerializálással](#considerations-for-serialization-and-deserialization) [kapcsolatos szempontok, valamint a tömörítési](#considerations-for-compression) szempontok című témakörben található.
* **Blob storage:** Jelenleg a Blob storage csak blokkblobokat támogat az optimalizált adatátvitelhez és átviteli kapacitáshoz.
* A **helyszíni fájlrendszeri** forgatókönyvek, amelyek az **adatkezelési átjáró**használatát igénylik, tekintse meg az Adatkezelési átjáró szempontjai című [szakaszban.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relációs adattárak
*(Sql Database, SQL Data Warehouse, SQL Server és Oracle adatbázisokat tartalmaz)*

* **Másolási viselkedés:** Az **sqlSink**beállításának tulajdonságaitól függően a Copy Activity különböző módokon írja az adatokat a céladatbázisba.
  * Alapértelmezés szerint az adatmozgatási szolgáltatás a Tömeges másolás API-t használja az adatok hozzáfűző módban való beszúrásához, amely a legjobb teljesítményt nyújtja.
  * Ha egy tárolt eljárást állít be a fogadóban, az adatbázis az adatokat soronként alkalmazza, nem pedig tömeges terhelésként. A teljesítmény jelentősen csökken. Ha az adatkészlet nagy, ha alkalmazható, fontolja meg az **sqlWriterCleanupScript** tulajdonság használatára való váltást.
  * Ha minden egyes copy activity futtatáshoz konfigurálja az **sqlWriterCleanupScript** tulajdonságot, a szolgáltatás elindítja a parancsfájlt, majd a Tömeges másolás API-t használja az adatok beszúrásához. Ha például a teljes táblát felül szeretné írni a legfrissebb adatokkal, megadhat egy parancsfájlt, amely először törli az összes rekordot, mielőtt tömegesen betöltené az új adatokat a forrásból.
* **Adatminta és kötegméret:**
  * A táblaséma hatással van a másolási átviteli. Azonos mennyiségű adat másolásához a nagy sorméret jobb teljesítményt nyújt, mint egy kis sorméret, mivel az adatbázis hatékonyabban tud kevesebb adatköteget véglegesítani.
  * A Másolási tevékenység kötegek sorozatába illeszti be az adatokat. A kötegsorainak számát a **writeBatchSize** tulajdonság segítségével állíthatja be. Ha az adatok kis sorokat tartalmaznak, beállíthatja a **writeBatchSize** tulajdonságot magasabb értékkel, hogy az alacsonyabb kötegterhelés és a nagyobb átviteli terhelés előnyeit élvezhesse. Ha az adatok sormérete nagy, legyen óvatos, amikor növeli **a writeBatchSize parancsot.** A magas érték az adatbázis túlterhelése miatt idomás-meghibásodáshoz vezethet.
* A **helyszíni relációs adatbázisok,** például az SQL Server és az Oracle, amelyek az **adatkezelési átjáró**használatát igénylik, az [Adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) című szakaszban találhatók.

### <a name="nosql-stores"></a>NoSQL üzletek
*(Tartalmazza a table storage-ot és az Azure Cosmos DB-t)*

* **Táblázat tárolására:**
  * **Partíció**: Adatok írása az áthatárolt partíciókra jelentősen csökkenti a teljesítményt. Rendezze a forrásadatokat partíciókulcs szerint úgy, hogy az adatok hatékonyan bekerüljenek az egyik partícióba a másik után, vagy állítsa be a logikát úgy, hogy az adatokat egyetlen partícióra írja.
* **Az Azure Cosmos DB**esetében:
  * **Kötegméret:** A **writeBatchSize** tulajdonság beállítja az Azure Cosmos DB szolgáltatáshoz a dokumentumok létrehozásához szükséges párhuzamos kérelmek számát. A **writeBatchSize** növelése esetén jobb teljesítményre számíthat, mivel az Azure Cosmos DB további párhuzamos kérelmeket küld. Azonban figyelje a szabályozást, amikor az Azure Cosmos DB-be ír (a hibaüzenet "A kérelmek aránya nagy"). Különböző tényezők okozhatnak szabályozást, beleértve a dokumentum méretét, a dokumentumokban lévő kifejezések számát és a célgyűjtemény indexelési házirendjét. A nagyobb másolási átviteli érték eléréséhez fontolja meg egy jobb gyűjtemény, például az S3 használatát.

## <a name="considerations-for-serialization-and-deserialization"></a>A szerializálás és a deszerializálás szempontjai
Szerializálás és deszerializálás akkor fordulhat elő, ha a bemeneti adatkészlet vagy kimeneti adatkészlet egy fájl. Lásd: [Támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) a támogatott fájlformátumok másolási tevékenység alapján című témakörben.

**Másolási viselkedés**:

* Fájlok másolása fájlalapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészletek azonos vagy nincs fájlformátum-beállítással rendelkeznek, az adatmozgatási szolgáltatás szerializálás vagy deszerializálás nélkül hajt végre bináris másolatot. Nagyobb átviteli áteresztőmert láthat, mint a forgatókönyv, amelyben a forrás- és fogadó fájlformátum-beállításai eltérnek egymástól.
  * Ha a bemeneti és kimeneti adatkészletek szöveges formátumúak, és csak a kódolási típus különbözik, az adatmozgatási szolgáltatás csak kódolást végez. Nem végez szerializálást és deszerializálást, ami némi teljesítményterhelést okoz a bináris másolathoz képest.
  * Ha a bemeneti és kimeneti adatkészletek különböző fájlformátumokkal vagy konfigurációkkal rendelkeznek, például határolójelekkel, az adatmozgatási szolgáltatás deszerializálja a forrásadatokat a forrásadatok streameléséhez, átalakításához, majd szerializálásához a megadott kimeneti formátumba. Ez a művelet más forgatókönyvekhez képest sokkal jelentősebb teljesítményterhelést eredményez.
* Ha nem fájlalapú (például fájlalapú tárolóból relációs tárolóba) lévő adattárba másol fájlokat, a szerializálási vagy deszerializálási lépés szükséges. Ez a lépés jelentős teljesítményterhelést eredményez.

**Fájlformátum**: A választott fájlformátum befolyásolhatja a másolás idotartamát. Az Avro például egy kompakt bináris formátum, amely adatokkal tárolja a metaadatokat. Széles körű támogatást élvez a Hadoop ökoszisztémában a feldolgozáshoz és a lekérdezéshez. Az Avro azonban drágább a szerializálás és a deszerializálás esetében, ami alacsonyabb másolási átviteli áteresztőmert eredményez a szöveges formátumhoz képest. Válassza ki a fájlformátumot a feldolgozási folyamat során holisztikusan. Kezdje azzal, hogy milyen formában tárolja az adatokat, forrásadat-tárolókat, vagy kinyerhető külső rendszerekből; a legjobb tárolási, analitikus feldolgozási és lekérdezési formátum; és milyen formátumban kell az adatokat adattárba exportálni a jelentéskészítő és vizualizációs eszközök számára. Előfordulhat, hogy az olvasási és írási teljesítményhez optimálistól elmaradó fájlformátum jó választás lehet, ha figyelembe veszi az általános elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítéssel kapcsolatos szempontok
Ha a bemeneti vagy kimeneti adatkészlet fájl, beállíthatja, hogy a Másolási tevékenység tömörítést vagy kicsomagolást hajtson végre, miközben adatokat ír a célhelyre. Ha a tömörítést választja, kompromisszumot köt a bemeneti/kimeneti (I/O) és a CPU között. Az adatok tömörítése többletköltséget jelent a számítási erőforrásokban. De cserébe csökkenti a hálózati I/O-t és a tárolást. Az adatoktól függően előfordulhat, hogy a teljes másolási átviteli átmenő.

**Codec**: Copy Activity támogatja a gzip, bzip2 és Deflate tömörítési típusokat. Az Azure HDInsight mindhárom típust felhasználhatja a feldolgozáshoz. Minden tömörítési kodeknek vannak előnyei. Például bzip2 rendelkezik a legalacsonyabb másolási átviteli teljesítmény, de a legjobb Hive lekérdezési teljesítmény bzip2, mert feloszthatja a feldolgozásra. Gzip a legkiegyensúlyozottabb lehetőség, és használják a leggyakrabban. Válassza ki azt a kodeket, amely a legjobban megfelel a végpontok között forgatókönyvnek.

**Szint**: Minden tömörítési kodekhez két lehetőség közül választhat: a leggyorsabb tömörített és optimálisan tömörített. A leggyorsabb tömörített beállítás a lehető leggyorsabban tömöríti az adatokat, még akkor is, ha az eredményül kapott fájl nem optimálisan tömörített. Az optimálisan tömörített beállítás több időt tölt a tömörítéssel, és minimális mennyiségű adatot eredményez. Mindkét lehetőséget tesztelheti, hogy lássa, melyik biztosít jobb általános teljesítményt az Ön esetében.

**A megfontolás:** Nagy mennyiségű adat másolásához a helyszíni tároló és a felhő között, fontolja meg a köztes blob storage tömörítéssel. Az átmeneti tárolás használata akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások sávszélessége a korlátozó tényező, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet tömörítetlen formában legyen. Pontosabban, egyetlen másolási tevékenységet két másolási tevékenységre bonthat. Az első másolási tevékenység a forrásból egy köztes vagy átmeneti blobba tömörített formában másolatot készít. A második másolási tevékenység másolja a tömörített adatokat az előkészítésből, majd kibontja, miközben a fogadóba ír.

## <a name="considerations-for-column-mapping"></a>Az oszlopok leképezésével kapcsolatos szempontok
Beállíthatja, hogy a **ColumnMappings** tulajdonság a Másolási tevékenységben a bemeneti oszlopok teljes vagy egy részhalmazát a kimeneti oszlopokhoz rendelje. Miután az adatmozgatási szolgáltatás beolvassa az adatokat a forrásból, oszlopleképezést kell végrehajtania az adatokon, mielőtt az adatokat a fogadóba írna. Ez az extra feldolgozás csökkenti a másolási átviteli.

Ha a forrás-adattár lekérdezhető, például ha egy relációs tároló, például az SQL Database vagy az SQL Server, vagy ha ez egy NoSQL-tároló, például table storage vagy az Azure Cosmos DB, fontolja meg az oszlopszűrés és a logika átrendelése a **lekérdezési** tulajdonság helyett oszlop leképezés. Ily módon a vetület akkor következik be, amikor az adatmozgatási szolgáltatás beolvassa az adatokat a forrás-adattárból, ahol az sokkal hatékonyabb.

## <a name="other-considerations"></a>Egyéb szempontok
Ha a másolni kívánt adatok mérete nagy, módosíthatja az üzleti logikát az adatok további particionálásához a Data Factory szeletelési mechanizmusával. Ezután ütemezze úgy, hogy a Másolási tevékenység gyakrabban fusson, hogy csökkentse az egyes másolási tevékenységek adatméretét.

Legyen óvatos az adatkészletek számát és másolási tevékenységek igénylő Data Factory összekötő ugyanabba az adattárba ugyanabban az időben. Sok egyidejű másolási feladatok lehet, hogy szabályozegy adattáraszot, és vezethet csökkent teljesítmény, másolás feladat belső újrapróbálkozások, és bizonyos esetekben végrehajtási hibák.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: Másolás egy helyszíni SQL Server ről a Blob storage-ba
**Eset:** Egy folyamat úgy épül fel, hogy adatokat másoljon egy helyszíni SQL Server-ből a Blob storage-ba CSV formátumban. A másolási feladat gyorsabbá adására a CSV-fájlokat bzip2 formátumba kell tömöríteni.

**Tesztelés és elemzés:** A másolási tevékenység átviteli sebessége kevesebb, mint 2 Mb/s, ami sokkal lassabb, mint a teljesítmény-referenciaérték.

**Teljesítményelemzés és -finomhangolás:** A teljesítményprobléma elhárításához nézzük meg, hogyan történik az adatok feldolgozása és áthelyezése.

1. **Adatok olvasása**: Az átjáró kapcsolatot nyit meg az SQL Server kiszolgálóval, és elküldi a lekérdezést. Az SQL Server úgy válaszol, hogy az adatfolyamot az intraneten keresztül elküldi az átjárónak.
2. **Adatok szerializálása és tömörítése**: Az átjáró szerializálja az adatfolyamot CSV formátumba, és bzip2 adatfolyamba tömöríti az adatokat.
3. **Adatok írása**: Az átjáró az interneten keresztül feltölti a bzip2 adatfolyamot a Blob-tárolóba.

Mint látható, az adatok feldolgozása és áthelyezése a folyamatos átvitel ű, egymást követő módon: AZ SQL Server > a LAN > a GATEWAY > a WAN > Blob storage. **Az általános teljesítményt a folyamaton keresztüli minimális átviteli teljesítmény határozza meg.**

![Adatfolyam](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Az alábbi tényezők közül egy vagy több okozhatja a teljesítmény szűk keresztmetszetét:

* **Forrás**: Maga az SQL Server nagy terhelés miatt alacsony átviteli teljesítményű.
* **Adatkezelési átjáró:**
  * **Lan**: Az átjáró az SQL Server-géptől távol helyezkedik el, és alacsony sávszélességű kapcsolattal rendelkezik.
  * **Átjáró**: Az átjáró elérte a terhelési korlátozásokat a következő műveletek végrehajtásához:
    * **Szerializálás**: Az adatfolyam CSV formátumba történő szerializálása lassú átviteli hatávált.
    * **Tömörítés**: Lassú tömörítési kodeket választott (például bzip2, amely 2,8 MBps Core i7-es sel).
  * **WAN**: A vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség alacsony (például T1 = 1544 kbps; T2 = 6312 kbps).
* **Fogadó:** Blob storage alacsony átviteli. (Ez a forgatókönyv nem valószínű, mert az SLA legalább 60 Mb/s-ot garantál.)

Ebben az esetben a bzip2 adattömörítés lelassíthatja a teljes folyamatot. A gzip tömörítési kodekre való váltás enyhítheti ezt a szűk keresztmetszetet.

## <a name="sample-scenarios-use-parallel-copy"></a>Mintaforgatókönyvek: Párhuzamos másolás használata
**I. forgatókönyv:** 1000 1 MB-os fájlok másolása a helyszíni fájlrendszerből a Blob storage-ba.

**Elemzés és teljesítményhangolás:** Ha például négymagos gépen telepített átjárót, a Data Factory 16 párhuzamos másolatot használ a fájlok fájlrendszerből a Blob storage-ba való egyidejű áthelyezéséhez. Ez a párhuzamos végrehajtás nagy átviteli feszültséget eredményez. Azt is explicit módon megadhatja a párhuzamos másolatok száma. Sok kis fájl másolásakor a párhuzamos másolatok jelentősen megkönnyítik az átviteli műveleteket az erőforrások hatékonyabb használatával.

![1. példa](./media/data-factory-copy-activity-performance/scenario-1.png)

**II. forgatókönyv:** 20 blobok 500 MB-os mindegyik blobok a Blob storage data lake store analytics, majd a teljesítmény finomhangolása.

**Elemzés és teljesítményhangolás:** Ebben a forgatókönyvben a Data Factory egypéldányos (1-re beállított**parallelCopies)** és egyfelhős adatmozgatási egységek használatával másolja az adatokat a Blob storage-ból a Data Lake Store-ba. A megfigyelt átviteli teljesítmény közel lesz a [teljesítményreferencia-szakaszban leírtakhoz.](#performance-reference)

![2. példa](./media/data-factory-copy-activity-performance/scenario-2.png)

**III. forgatókönyv:** Az egyes fájlméretek több tucat MB-nél nagyobbak, és a teljes mennyiség nagy.

**Elemzés és teljesítményesztergálás:** **A párhuzamos másolatok** növelése nem eredményez jobb másolási teljesítményt az egyfelhős DMU erőforrás-korlátai miatt. Ehelyett több felhőalapú DMU-t kell megadnia, hogy több erőforrást kapjon az adatáthelyezés végrehajtásához. Ne adjon meg értéket a **parallelCopies** tulajdonsághoz. A Data Factory kezeli a párhuzamosságokat. Ebben az esetben, ha a **cloudDataMovementUnits** 4-re állítva körülbelül négyszer, körülbelül négyszeres.

![3. példa](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referencia
Az alábbiakban a támogatott adattárak teljesítményfigyelési és hangolási hivatkozásait olvashatja:

* Azure Blob storage: [Méretezhetőségi és teljesítménycélok blob storage](../../storage/blobs/scalability-targets.md) és [teljesítmény- és méretezhetőségi ellenőrzőlista blob storage.](../../storage/blobs/storage-performance-checklist.md)
* Azure Table storage: [Méretezhetőségi és teljesítménycélok táblatárolás](../../storage/tables/scalability-targets.md) és [teljesítmény- és méretezhetőségi ellenőrzőlista table storage.](../../storage/tables/storage-performance-checklist.md)
* Azure SQL Database: [Figyelheti a teljesítményt,](../../sql-database/sql-database-single-database-monitor.md) és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos
* Azure SQL Data Warehouse: A képességét adattárház egységekben (DWUs) mérik; lásd: [Számítási teljesítmény kezelése az Azure SQL Data Warehouse-ban (áttekintés)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Teljesítményszintek az Azure Cosmos DB-ben](../../cosmos-db/performance-levels.md)
* Helyszíni SQL Server: [A teljesítmény figyelése és hangolása](https://msdn.microsoft.com/library/ms189081.aspx)
* Helyszíni fájlkiszolgáló: [Fájlkiszolgálók teljesítményének növelése](https://msdn.microsoft.com/library/dn567661.aspx)
