---
title: Másolási tevékenységek teljesítményéhez és finomhangolási útmutató |} A Microsoft Docs
description: További információ a másolási tevékenység használatakor az Azure Data Factory adatáthelyezési teljesítményét befolyásoló legfontosabb tényezők.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ec8c58e4ced0d8df958e242b9c1671aeed8c2ee6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812089"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolási tevékenységek teljesítményéhez és finomhangolási útmutató

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-copy-activity-performance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-performance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [másolási tevékenységek teljesítményéhez és finomhangolási útmutató a Data Factory](../copy-activity-performance.md).

Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást kínál. Ez lehetővé teszi, hogy másolatot több terabájtnyi adat minden nap gazdag számos felhőalapú és a helyszíni adattárakban. Villámgyors teljesítmény Adatbetöltési fontos annak érdekében, akkor előtérbe kerülhet a core "big data" hiba: fejlett elemzési megoldásokat hozhat, és részletes elemzések lekérése az adatokból.

Az Azure biztosít nagyvállalati adattárolás és adattárház-megoldások, és a másolási tevékenység magas szinten optimalizált Adatbetöltési könnyen konfigurálható, és állítsa be a élményt biztosít. Csak egy egyetlen másolási tevékenységgel rendelkező érhet el:

* Adatok betöltése **Azure SQL Data Warehouse** , **1,2 GB/s**. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).
* Adatok betöltése **Azure Blob storage** , **1,0 GB/s**
* Adatok betöltése **Azure Data Lake Store** , **1,0 GB/s**

Ez a cikk ismerteti:

* [Hivatkozás teljesítményszámokhoz](#performance-reference) támogatott forrás- és fogadó adattárak segítségével tervezheti meg a projekt;
* Az funkciók, amelyek különböző helyzetekben, például a Másolás átviteli képes javítani [felhőbeli adatáthelyezési egységek](#cloud-data-movement-units), [másolási párhuzamos](#parallel-copy), és [szakaszos Másolás](#staged-copy);
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) hangolását a teljesítmény- és másolási teljesítményre gyakorolt hatásáról tényezők a.

> [!NOTE]
> Ha nem ismeri a másolási tevékenység általános, lásd: [adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md) Ez a cikk elolvasása előtt.
>

## <a name="performance-reference"></a>Teljesítmény-referencia

Hivatkozásként van listázva táblával számát jeleníti meg a másolási átviteli sebesség (Mbps) a belső fejlesztésű tesztelés alapján adott forrás- és fogadó párokhoz. Az összehasonlítást, azt is bemutatjuk, hogyan különböző beállításait [felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) vagy [adatkezelési átjáró méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md) (több átjárócsomópontok) segítségével a másolási teljesítményét.

![Teljesítmény mátrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Az Azure Data Factory 1. verzió a minimális felhőbeli adatáthelyezési egységek a felhő-felhőbe történő másolás két. Ha nincs megadva, tekintse meg a használt alapértelmezett adatáthelyezési egységek [felhőbeli adatáthelyezési egységek](#cloud-data-movement-units).

**Tudnivalók:**
* Átviteli sebesség kiszámításához a rendszer az alábbi képlettel: [forrás adatsorból beolvasott adatok mérete] / [a másolási tevékenység futtatási időtartama].
* A tábla hivatkozási teljesítményszámokhoz is méri [TPC-H](http://www.tpc.org/tpch/) adatkészlet egy másolási tevékenység futtatása.
* Az Azure-beli adat-áruházaiban nagyban forrás- és fogadóadattárként is, azonos Azure-régióban.
* A helyszíni és felhő közötti hibrid másolás adattárak, minden egyes átjárócsomópont futott olyan számítógépen, amelyen a helyszíni adattárakból származó az alábbi specifikáció elkülönített. Ha egyetlen tevékenységgel átjáró kiszolgálón már futott, a másolási művelet csak egy részét a tesztgép CPU, memória és hálózati sávszélességet használja fel. További információkat talál a [figyelembe veszi a Data Management Gateway](#considerations-for-data-management-gateway).
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
> Nagyobb átviteli sebességet érhet el, további adatáthelyezési egységek kihasználásával (DMUs), mint az alapértelmezett maximális DMUs, amely egy felhő-a-felhőbe másolási tevékenység futtatása esetén 32. Például a 100 DMUs érheti el adatmásolásra az Azure-Blobból az Azure Data Lake Store, **1.0GBps**. Tekintse meg a [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) adatait a szolgáltatás és a támogatott forgatókönyv a következő szakaszban. Kapcsolattartó [az Azure-támogatás](https://azure.microsoft.com/support/) további DMUs kéréséhez.

## <a name="parallel-copy"></a>Párhuzamos másolása
Adatok olvasása a forrásból, vagy adatokat írni a cél **belül egy másolási tevékenység futtatása párhuzamosan**. Ez a funkció javítja a másolási műveletek teljesítményét, és csökkenti az adatok áthelyezéséhez szükséges időt.

Ez a beállítás különbözik a **egyidejűségi** tulajdonság a tevékenység meghatározásában. A **egyidejűségi** tulajdonság határozza meg, hány **egyidejű másolási tevékenység futtatásakor** adatok feldolgozásához a másik tevékenység windows (1 AM, 2 AM, 2 AM 3 óra, 3 AM-4 AM és így tovább). Ez a funkció akkor hasznos, ha egy korábbi betöltés végrehajtása. A párhuzamos másolási képesség vonatkozik egy **tevékenységfuttatás egyetlen**.

Lássunk erre egy példa forgatókönyv. A következő példában az elmúlt több szeletet kell feldolgozni. A Data Factory az egyes szeletekhez a másolási tevékenység (tevékenység Futtatás) példányát futtatja:

* Az első tevékenység (1 AM, 2 AM) ablak adatszeletre == > tevékenységfuttatás 1
* A második tevékenység (2 AM a 3-kor) ablakban az adatszelet == > tevékenységfuttatás 2
* A második tevékenység (3 AM, 4 AM) ablakban az adatszelet == > tevékenységfuttatás 3

És így tovább.

Ebben a példában során a **egyidejűségi** értéke 2, **tevékenységfuttatás 1** és **tevékenységfuttatás 2** adatokat másol a két tevékenység windows **egyidejűleg** adatáthelyezési teljesítményét javítása érdekében. Azonban, ha több fájlt társítva a tevékenység-végrehajtásonként 1, az adatátviteli szolgáltatás fájlokat másolja a forrásból a célfájl egy egyszerre.

### <a name="cloud-data-movement-units"></a>Felhőbeli adatáthelyezési egységek
A **a felhőbeli adatáthelyezési egység (DMU)** egy mérték, amely a Data Factory egy egységet (a Processzor, memória és a hálózatierőforrás-lefoglalás kombinációjával) hatékonyságát. DMU akkor alkalmazható, a felhő-a-felhőbe másolási műveleteket, de nem egy hibrid másolás.

**A minimális felhőbeli adatáthelyezési egységek a lehetővé teheti a másolási tevékenység futtatási két.** Ha nincs megadva, a következő táblázat felsorolja a különböző másolási forgatókönyvek esetén a használt alapértelmezett DMUs:

| Másolja ki a forgatókönyv | Szolgáltatás által meghatározott alapértelmezett DMUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | 4 és 16 száma és a fájlok méretétől függően. |
| Egyéb másolási forgatókönyvek esetén | 4 |

Ez az alapértelmezett felülbírálásához, adjon meg értéket a **cloudDataMovementUnits** tulajdonság az alábbiak szerint. A **megengedett értékek** számára a **cloudDataMovementUnits** tulajdonság 2, 4, 8, 16 és 32. A **felhőalapú DMUs tényleges számát** egyenlő vagy kisebb, mint a beállított érték, a adatmintát attól függően, hogy a másolási művelet használ a futási időben. További információ a teljesítmény szintjét egy adott másolási forrásaként és fogadó további egységek konfigurálásakor kaphat,: a [teljesítményfigyelési](#performance-reference).

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
> Ha további felhőalapú DMUs egy nagyobb átviteli sebességet, forduljon [az Azure-támogatás](https://azure.microsoft.com/support/). Beállítás 8 és újabb a jelenleg csak akkor, ha működik, **több fájlt másol a Blob storage/Data Lake Store/Amazon S3/felhőbeli FTP-/ cloud SFTP a Blob storage-vagy Data Lake Store és az Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Használhatja a **parallelCopies** tulajdonság, amely jelzi, amelyeket szeretne használni a másolási tevékenység párhuzamosságát. Ez a tulajdonság belül a másolási tevékenység, amely a forrásból olvashatja vagy írhatja, a fogadó adattárakba párhuzamos szálak maximális számának is felfoghatók.

Minden egyes másolási tevékenység futtatási a Data Factory használatával adatokat másol a forrásadattárból adatokat tárolja, és a cél-adatok tárolására párhuzamos példányszám határozza meg. Az alapértelmezett példányszámot párhuzamos, amelyet használ a forrás- és fogadó használt függ.

| Forrás és fogadó | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Adatok másolása a fájlalapú tárolók (Blob storage; között Data Lake Store; Az Amazon S3; a helyi fájlrendszer; egy a helyszíni HDFS) |1 és 32 között. A használt két felhőalapú adattárolók, vagy az átjárót tartalmazó számítógépen (az adatok másolása, vagy a helyszíni adattárolókból) egy hibrid másolás használt fizikai konfigurációját közötti adatokat másolja a fájlokat és a felhőbeli adatáthelyezési egységek (DMUs) száma méretétől függ. |
| Az adatok másolása **forrás adatok tárolása az Azure Table storage** |4 |
| Minden más adatforrások és adatfogadók pár |1 |

Általában az alapértelmezett viselkedést adjon meg a legjobb teljesítményt. Azonban szabályozhatja, hogy az adatok üzemeltető gépek terhelését tárolja, vagy a másolási teljesítmény hangolására, dönthet úgy, hogy felülbírálhatja az alapértelmezett értéket, és adjon meg egy értéket a **parallelCopies** tulajdonság. Az érték 1 és 32 (mindkét határokat is beleértve) között kell lennie. Futási időben, a legjobb teljesítmény érdekében másolási tevékenység használja, amely kisebb vagy egyenlő az érték, Ön által beállított értéket.

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
Tudnivalók:

* Ha fájlalapú tárolók közötti adatokat másolja a **parallelCopies** határozza meg a fájlok szintjén párhuzamosságát. Egyetlen fájl belül darabolás történnek alatt, automatikusan és transzparens módon, és úgy tervezték, hogy egy adott tároló adattípusra optimális megfelelő adatrészlet mérete használata a párhuzamos és merőleges parallelCopies az adatok betöltéséhez. Az adatátviteli szolgáltatást használja a másolási művelet futási időben párhuzamos másolatok tényleges száma nem több, mint a fájlok száma nem. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájlszintű foka.
* Ha ad meg értéket a **parallelCopies** tulajdonságot használja, fontolja meg a terhelés megnövekedése az adatforrások és adatfogadók adattárak és -átjáróra, ha egy hibrid másolás. Ez akkor fordul elő, különösen ha rendelkezik több egyidejű futtatásainak ugyanazokat a tevékenységeket, amelyek ugyanabban az adattárban futtatásához vagy tevékenységeket. Ha azt tapasztalja, hogy az adattár vagy a Gateway a terhelés túlterhelte, csökkentheti a **parallelCopies** érték a terhelés alól.
* Másolt adatok, amelyek nem fájlalapú, amelyek a fájlalapú áruházak áruházakból származó, az adatátviteli szolgáltatás figyelmen kívül hagyja-e a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.

> [!NOTE]
> Használja az adatkezelési átjáró 1.11 vagy újabb verzióját kell használnia a **parallelCopies** funkciót, ha így tesz, a hibrid másolás.
>
>

A két tulajdonság jobb használatához, és az adatok mozgását átviteli sebesség növelése érdekében, tekintse meg a mintát, használati esetek. Nem szükséges konfigurálni **parallelCopies** kihasználásához az alapértelmezett viselkedést. Ha konfigurálja és **parallelCopies** túl kicsi, több felhő DMUs nem teljes kihasználását.

### <a name="billing-impact"></a>A számlázás gyakorolt hatás
Rendelkezik **fontos** ne feledje, hogy a másolási művelet teljes időtartama alapján díjkötelesek. Ha most négy felhőbeli egységgel 15 percet vesz igénybe, és egy másolási feladat egy órával tennie egy felhőbeli egységgel használják, akkor a teljes számlája összegét szinte változatlan marad. Ha például négy felhőbeli egység használhatja. Az első felhőbeli egység szintén 10-et, a második érték 10 perc, a harmadik egyet, 5 perc, és a negyedik, 5 perc, a egy másolási tevékenység az összes futtatásához. A Másolás (adatáthelyezés) teljes időpontot, amely 10 + 10 + 5 + 5 = 30 perc díjkötelesek. Használatával **parallelCopies** nem befolyásolják a számlázást.

## <a name="staged-copy"></a>Szakaszos másolás
Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

1. **Szeretné betölteni az különféle adattárakból származó adatokat az SQL Data Warehouse polybase**. Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. Azonban a forrásadatok a Blob storage-ban kell lennie, és annak meg kell felelnie a további feltételek. Adatok betöltésekor eltérő a Blob storage-adatokat az adattárból, az adatok másolását az átmeneti előkészítési Blob storage-n keresztül aktiválhatja. Ebben az esetben a Data Factory, győződjön meg arról, hogy megfelel-e a PolyBase követelményeinek szükséges adatátalakítások hajt végre. Ezután használja a PolyBase adatok betöltése az SQL Data Warehouse-bA. További részletekért lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).
2. **Is eltarthat egy ideig a hibrid adatmozgás végrehajtásához (azt jelenti, egy a helyszíni adatok másolása a felhőbeli adatok tárolására és tárolhat), a lassú hálózati kapcsolaton**. A teljesítmény javítása érdekében az adatokat a helyszíni képes tömöríteni, hogy az adatok áthelyezése a felhőben az átmeneti adattár kevesebb időt vesz igénybe. Ezután az átmeneti tárnak lévő adatok kibontása, ahhoz a célként megadott adattárba történő betöltéséhez.
3. **Nyissa meg a 80-as port-astól különböző portok és a tűzfal, a 443-as porton a vállalati informatikai házirend miatt nem szeretné**. Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben a forgatókönyvben kihasználhatja az átjáró első az adatok másolása Blob storage átmeneti példányra HTTP vagy HTTPS 443-as porton. Ezt követően az adatok betöltése az SQL Database vagy az SQL Data Warehouse Blob storage emeli ki. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése
Ha aktiválja az átmeneti tárolási szolgáltatás, először az adatokat másolja át a forrásadattár az átmeneti adattár (használata a saját). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Data Factory automatikusan kezeli az Ön számára a két szakaszból álló folyamatot. A Data Factory is megtisztítja az átmeneti tárolási ideiglenes adatokat, az adatáthelyezés befejeződése után.

A felhőbe másolásának esetéhez (a forrás- és fogadó adatokat tárolja a felhőben vannak) az átjáró nincs használatban. A Data Factory szolgáltatás hajtja végre a másolási műveletek.

![Szakaszos másolás: Felhő használata](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

A hibrid másolás forgatókönyvben (forrás helyszíni fogadó pedig a felhőben), az átjáró helyez át adatokat a forrásadattárból egy átmeneti adattár. A Data Factory szolgáltatás helyez át adatokat az átmeneti adattárból a fogadó adattárba. Az adatok másolása egy felhőalapú adattár átmeneti tárolással a helyszíni adatok tárolóhoz is támogatott, a fordított folyamat.

![Szakaszos másolás: Hibrid forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Adatok áthelyezése az átmeneti tárolási obchodu aktiválásakor is megadhat kívánja-e az adatokat, mielőtt adatokat a forrásadattárból egy ideiglenes vagy átmeneti adattár tömörített, és ezután előtt, amely adatokat helyez át egy ideiglenes vagy átmeneti adatok kibontása a fogadó adattár tárolja.

Jelenleg nem másolhat adatokat egy átmeneti tárnak használatával két helyszíni adattárak között. Terveink szerint már ezt a beállítást, hamarosan elérhető lesz.

### <a name="configuration"></a>Konfiguráció
Konfigurálja a **enableStaging** a másolási tevékenység beállítást adja meg, hogy az adatokat Blob storage-ban a célként megadott adattárba történő betöltésük előtt átmenetileg tárolva. Ha **enableStaging** , igaz értéke esetén adja meg a következő táblázatban található további tulajdonságokat. Ha még nincs fiókja, is szeretne létrehozni egy Azure Storage vagy a megosztott hozzáférési aláírás-társított szolgáltatást az átmeneti tárolási.

| Tulajdonság | Leírás | Alapértelmezett érték | Szükséges |
| --- | --- | --- | --- |
| **enableStaging** |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg a nevét egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás, amely hivatkozik a példány tárolási megoldás, amely egy átmeneti előkészítési tárolót használunk. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló közös hozzáférésű jogosultságkód nem használható. Más esetekben használhatja azt. |– |Igen, mikor **enableStaging** igaz értékre van beállítva |
| **path** |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás ideiglenes adatokat tárolni egy tárolót hoz létre. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |– |Nem |
| **enableCompression** |Itt adhatja meg, hogy adatok tömöríti-e, mielőtt azt a cél. Ez a beállítás átvitt adatok mennyiségét csökkenti. |False (Hamis) |Nem |

Íme egy példa definíciója a másolási tevékenység az előző táblázatban leírt tulajdonságokkal:

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

### <a name="billing-impact"></a>A számlázás gyakorolt hatás
Két lépést alapján számítjuk fel: másolás időtartama, és másolja a típusát.

* Használata esetén átmeneti során a felhőbe történő másolás (az adatok másolása egy felhőalapú adattár egy másik felhőalapú adattárba), a számlázás a [összege másolás időtartama az 1 és 2. lépés] x [felhőalapú másolási Egységár].
* Használatakor során egy hibrid másolás (adatok másolása a helyszíni adattárolókból egy felhőalapú adattár), átmeneti díjkötelesek [hibrid másolás időtartama] x [hibrid másolás Egységár] + [felhőbeli másolás időtartama] [felhőalapú másolási Egységár] x.

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések
Javasoljuk, hogy ezen lépések a Data Factory szolgáltatás másolási tevékenységgel rendelkező teljesítményét:

1. **Megállapítja az alapértékeket**. A fejlesztési fázis során a folyamat tesztelése a másolási tevékenység használatával egy reprezentatív mintát ellen. Használhatja a Data Factory [felosztási modelljét](data-factory-scheduling-and-execution.md) dolgozik adatok korlátozása érdekében.

   Végrehajtási idő és a teljesítményjellemzők gyűjtése használatával a **megfigyelési és felügyeleti alkalmazás**. Válasszon **Monitor & Manage** a Data Factory kezdőlapján. A faszerkezetes nézetben válassza ki a **kimeneti adatkészlet**. Az a **tevékenység Windows** menüben válassza ki a másolási tevékenység futtatási. **Tevékenység Windows** felsorolja a másolási tevékenység időtartama és a másolt adatok mérete. Az átviteli sebességet szerepel-e **Activity Window Explorer**. Az alkalmazással kapcsolatos további tudnivalókért lásd: [figyelése és kezelése az Azure Data Factory-folyamatok a megfigyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md).

   ![Tevékenységfuttatás részletei](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   A cikk későbbi részében össze lehessen hasonlítani a teljesítmény és a konfiguráció a másolási tevékenység, megoldásait [teljesítményfigyelési](#performance-reference) a tesztelés.
2. **Diagnosztika és a teljesítmény optimalizálása**. Ha a teljesítmény megfigyelte nem felel meg az elvárásainak, azonosíthatja a szűk keresztmetszeteket szeretné. Távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében a teljesítmény optimalizálásával majd. Teljesítmény diagnosztikai teljes leírását ez a cikk nem foglalkozik, de Íme néhány gyakori szempontok:

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units)
     * [Szakaszos másolás](#staged-copy)
     * [Data Management Gateway méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Adatkezelési átjáró](#considerations-for-data-management-gateway)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálást és deszerializálást](#considerations-for-serialization-and-deserialization)
   * [A tömörítés](#considerations-for-compression)
   * [Oszlop-hozzárendelés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)
3. **Bontsa ki a konfigurációt, a teljes adatkészletet**. Ha elégedett a eredményeit és a teljesítmény, kibonthatja a definíció- és adatcsatorna aktív időszakának ahhoz, hogy biztosítsák a teljes adatkészletet.

## <a name="considerations-for-data-management-gateway"></a>Az adatkezelési átjáró szempontjai
**Átjáró telepítőjének**: Azt javasoljuk, hogy a gazdagép az adatkezelési átjáró egy dedikált gépre használja. Lásd: [adatkezelési átjáró használatának szempontjai](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Átjáró figyelési és felfelé vagy kibővített**: Egy vagy több átjáró csomópont egyetlen logikai átjáró több másolási tevékenység-végrehajtás szolgálhat egyidejűleg egy időben. Megtekintheti a közel valós idejű pillanatképét erőforrás-használat (CPU, memória, network(in/out), stb.) egy átjárót tartalmazó számítógépre, valamint tekintse meg a korlátot, az Azure Portalon, és futó párhuzamos feladatok száma [figyelő átjárót a portálon](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Ha hibrid adatáthelyezés nagyszámú egyidejű másolási tevékenység-végrehajtás vagy nagy mennyiségű adat másolása (nagy erőforrásigényű) szükség van, érdemes lehet [vertikális vagy horizontális felskálázás átjáró](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) üzembe helyezésére szolgáló vagy úgy, hogy jobban kihasználhassák az erőforrás További erőforrás megjelenő új példányt.

## <a name="considerations-for-the-source"></a>A forrás szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, lásd: [megfigyelés és finomhangolás témakörök](#performance-reference) , amelyek adott adattárak, és segítenek megérteni, hogy adatokat tárolni a teljesítményt nyújt, válaszidők csökkentése érdekében és átviteli teljesítmény.

Ha a Blob storage adatokat másol az SQL Data Warehouse, fontolja meg **PolyBase** teljesítményének növelése érdekében. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Tartalmazza a Blob storage, Data Lake Store, Amazon S3, a helyi fájlrendszer és a helyszíni HDFS)*

* **Átlagos méretét és a fájlszám**: A másolási tevékenység egy adatfájlt egy időben továbbítja. Az adatok áthelyezésének akkora a teljes átviteli sebesség alacsonyabb, ha néhány nagy méretű fájlok miatt a rendszer-indításkori fázis minden egyes fájl helyett a sok kis méretű fájlt tartalmaz. Ezért ha lehetséges, egyesítendő kisméretű fájlok nagyobb fájlok nagyobb átviteli sebességet biztosítanak.
* **A fájl formátuma és tömörítés**: További részleteket a teljesítmény javítása, lásd: a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokat.
* Az a **a helyi fájlrendszer** forgatókönyv, amelyben **adatkezelési átjáró** van szükséges, tekintse meg a [adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) szakaszban.

### <a name="relational-data-stores"></a>Relációs adattárak
*(Tartalmazza az SQL-adatbázis; Az SQL Data Warehouse; Amazon Redshift; Az SQL Server-adatbázisok; és az Oracle, MySQL, DB2, Teradata, Sybase és PostgreSQL-adatbázisok stb.)*

* **Adatmintát**: A következő tábla sémáját másolási átviteli sebesség hatással van. Egy nagy méretű sor mérete kis sor mérete, azonos mennyiségű adatot másolni egy jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban lekérheti kevesebb váró adatokat, amelyek kevesebb sort tartalmaznak.
* **Lekérdezés vagy tárolt eljárás**: Optimalizálhatja a lekérdezést, vagy adja meg, ha a másolási tevékenység forrásból történő adatlehívást, hatékonyabban tárolt eljárás logikáját.
* A **helyszíni relációs adatbázisok**, mint például az SQL Server és az Oracle, a használható **adatkezelési átjáró**, adatkezelési átjáró szakasz szempontjait lásd:.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, tekintse meg [megfigyelés és finomhangolás témakörök](#performance-reference) kifejezetten az adattárakban. Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

Az adatok másolása **a Blob storage-** való **SQL Data Warehouse**, fontolja meg **PolyBase** teljesítményének növelése érdekében. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Tartalmazza a Blob storage, Data Lake Store, Amazon S3, a helyi fájlrendszer és a helyszíni HDFS)*

* **Másolja a viselkedés**: Ha adatokat másolja egy másik fájl alapú adatokat az adattárból, a másolási tevékenység keresztül három pontot tartalmaz az **copyBehavior** tulajdonság. Ez a megőrzi a hierarchia, simítja egybe a hierarchia, vagy egyesíti a fájlokat. Az egybesimítás hierarchia vagy megőrzi az rendelkezik alig vagy egyáltalán nem teljesítménybeli terhelést, de a fájlok egyesítése növelése teljesítménybeli terhelést okoz.
* **A fájl formátuma és tömörítés**: Tekintse meg a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokban talál további részleteket a teljesítmény javítása.
* **A BLOB storage-**: A Blob storage jelenleg csak blokkblobokat támogat optimalizált az adatátviteli és átviteli sebesség.
* A **fájlrendszerekhez a helyi** használatát igénylő forgatókönyvek **adatkezelési átjáró**, tekintse meg a [adatkezelési átjáró szempontjai](#considerations-for-data-management-gateway) szakaszban.

### <a name="relational-data-stores"></a>Relációs adattárak
*(Az SQL Database, az SQL Data Warehouse, SQL Server-adatbázisok és Oracle-adatbázisokat tartalmazza)*

* **Másolja a viselkedés**: A Tulajdonságok függően beállított **sqlSink**, a másolási tevékenység adatokat ír a céladatbázis különböző módon.
  * Alapértelmezés szerint az adatok adatátviteli szolgáltatás által a tömeges másolási API-t az adatok beszúrása hozzáfűzése üzemmódot, amely a lehető legjobb teljesítményt biztosít.
  * Ha a fogadó tárolt eljárás konfigurálja, az adatbázis az adatokat egy sort egy kötegelt betöltés, hanem egyszerre vonatkozik. Teljesítmény jelentősen csökken. Ha az adatkészlet túl nagy, ha lehetséges, fontolja meg, hogy használatával a **sqlWriterCleanupScript** tulajdonság.
  * Ha konfigurálja a **sqlWriterCleanupScript** tulajdonság minden egyes másolási tevékenység futtatása, a szolgáltatás aktiválása a parancsfájl és majd illessze be az adatokat a tömeges másolási API-t használja. Például a teljes tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először törölje az összes rekordot a forrás az új adatok tömeges-betöltés előtt.
* **A minta és a batch adatméret**:
  * A következő tábla sémáját másolási átviteli sebesség hatással van. Az azonos mennyiségű adatot másolni, egy nagy méretű sor mérete ad egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti kevesebb váró adatokat.
  * A másolási tevékenység adatokat egy sorozat része kötegek szúr be. Beállíthatja a sorok számát a batch segítségével a **writeBatchSize** tulajdonság. Ha az adatok kis sorral rendelkezik, akkor lehet beállítani a **writeBatchSize** tulajdonság számára, hogy alacsonyabb batch terhelést és nagyobb átviteli sebességet a magasabb értékű. Ha az adatokat a sor mérete túl nagy, ügyeljen arra, ha növeli **writeBatchSize**. Egy magas szintű egy példány hibáját okozta. az adatbázis terhelve vezethet.
* A **helyszíni relációs adatbázisok** , például az SQL Server és az Oracle, a használható **adatkezelési átjáró**, tekintse meg a [adatkezelési átjárószempontjai](#considerations-for-data-management-gateway)szakaszban.

### <a name="nosql-stores"></a>NoSQL-tárolókat
*(Tartalmazza a Table storage és az Azure Cosmos DB)*

* A **Table storage**:
  * **partíció**: Írja az adatokat a partíciók összefűzéses jelentősen rontja a teljesítményt. A forrásadatok rendezés partíciós kulccsal, hogy az adatokat a rendszer beszúrta hatékonyan egy partíciót egymás után, vagy módosítsa az adatokat írni az ugyanazon a partíción logikát.
* A **az Azure Cosmos DB**:
  * **Kötegméret**: A **writeBatchSize** vlastnost nastavuje vlastnost párhuzamos kérelmek száma az Azure Cosmos DB szolgáltatásba dokumentumok létrehozásához. Jobb teljesítmény várható növelése **writeBatchSize** mivel több párhuzamos kérések érkeznek, az Azure Cosmos DB-hez. Azonban tekintse meg a szabályozás, az Azure Cosmos DB (a hibaüzenet: "túl sok kérelmet adott") írásakor. Számos tényező okozhatja, szabályozás, beleértve a dokumentumok méretétől, a szám a feltételek a dokumentumok és a célgyűjtemény indexelési házirendet. Másolási átviteli teljesítménye elérése érdekében fontolja meg egy jobb, például S3 szintű gyűjtemény.

## <a name="considerations-for-serialization-and-deserialization"></a>Szerializálást és deszerializálást szempontjai
Szerializálást és deszerializálást akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájlt. Lásd: [támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) másolási tevékenység által támogatott fájlformátumokról részleteinek ismertetésével együtt.

**Másolja a viselkedés**:

* A fájlok másolása a fájlalapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkeznek azonos vagy nincs fájlformátum beállításai, az adatátviteli szolgáltatás bináris másolat szerializálása vagy deszerializálása nélkül hajtja végre. Megjelenik egy a forgatókönyv, amelyben a forrás- és fogadó fájlformátum beállításai eltérnek egymáshoz képest nagyobb átviteli sebességet.
  * Amikor bemeneti és kimeneti adatkészletek mindkét szöveges formátum, és csak a kódolás típusát különböző, az adatok mozgását szolgáltatásnak csak nincs a kódolási átalakítás. Még nem csinál bármely szerializálási és deszerializálás, amely néhány bináris másolat többletterhelést teljesítményt okoz.
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkezik különböző fájlformátumokban vagy különböző konfigurációkat, például az elválasztó karakterek, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyam, átalakítása és majd szerializálni a kimeneti formátumba, jelzett. Ez a művelet egy sokkal jelentős teljesítménybeli többletterhelést képest más esetben eredményez.
* Ha másolja a fájlokat egy adattár, amely nem fájl alapú (például az olyan fájlalapú tároló relációs tárolóval), akkor a szerializálása vagy deszerializálása lépésre szükség. Ebben a lépésben jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A választott fájl formátumát másolási teljesítmény hatással lehetnek. Például a Avro kompakt bináris formátum, amely tárolja az adatokat metaadatok. Széles körben támogatja a Hadoop-ökoszisztéma, feldolgozása és lekérdezési rendelkezik. Azonban az Avro szerializálást és deszerializálást, ami szövegformátum képest alacsonyabb másolási átviteli sebességet eredményez a drágább. Győződjön meg azok alkalmazásfüggőségeit a kiválasztott fájlformátum teljes feldolgozási folyamat során. Kezdés milyen formában az adatokat tárolja a forrásadattárakból vagy külső rendszerek; kinyerése a storage, az analitikus feldolgozás és a lekérdezési; legjobb formátum és milyen formátumban az adatok exportálja az adatpiacok jelentéskészítő és vizualizációs eszközök, a. Néha olyan fájlformátum, amely az optimálisnál rosszabb olvasási és írási teljesítmény előfordulhat, hogy jó választás meghatározásakor a teljes elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai
Ha a bemeneti vagy kimeneti adatkészlet egy fájlt, írja az adatokat a rendeltetési tömörítés vagy a kibontás végrehajtani a másolási tevékenység állíthatja be. Ha tömörítést választja, akkor győződjön meg arról, bemeneti/kimeneti (I/O) magával és a CPU. Tömöríti a számítási erőforrásokat az extra költségek. De cserébe csökkenti a hálózati i/o- és storage. Az adatoktól függően a teljes másolási átviteli sebesség boost jelenhet meg.

**Kodek**: Másolási tevékenység a gzip, bzip2 és a Deflate tömörítést típusokat támogatja. Az Azure HDInsight minden három típusú feldolgozási használhatnak fel. Minden egyes tömörítési kodeket előnnyel jár. Például bzip2 rendelkezik a legalacsonyabb másolási teljesítmény, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert azt a feldolgozáshoz is fel. A gzip a legtöbb kiegyensúlyozott beállítás használatát, és azt a leggyakrabban használt. Válassza ki a leginkább megfelelő a végpontok közötti forgatókönyv kodek.

**Szint**: Minden egyes tömörítési kodeket két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített lehetőség a lehető leggyorsabban tömöríti az adatokat, még akkor is, ha az eredményül kapott fájl nem optimális tömöríti. Az optimális tömörített beállítás több időt tölt a tömörítést, és a egy minimális mennyiségű adatot eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb általános teljesítményt abban az esetben tesztelheti.

**Veszi figyelembe**: Egy nagy mennyiségű adat a helyszínen és a felhő közötti másolásához, fontolja meg átmeneti blob storage-bA a tömörítéssel. Ideiglenes tároló használata akkor hasznos, ha a sávszélesség a vállalati hálózat és az Azure-szolgáltatások korlátozzák, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlen formában. Pontosabban egy másolási tevékenységgel is felosztása két másolási tevékenységeket. Az első másolási tevékenység átmásolja a forrás tömörített formátumban ideiglenes vagy átmeneti blobba. A második másolási tevékenységgel másolja át a tömörített adatok átmeneti, és amíg a gyűjtő ír majd kibontja.

## <a name="considerations-for-column-mapping"></a>Az oszlopleképezés szempontjai
Beállíthatja a **leképezésekben** tulajdonság a másolási tevékenység a térkép összes vagy a bemeneti oszlopok, a kimeneti oszlopok egy része. Az adatáthelyezési szolgáltatás a forrásból olvassa be az adatokat, miután kell végezni az adatokon oszlop-hozzárendelés előtt írja az adatokat a fogadó. A további feldolgozás csökkenti a másolási átviteli sebességet.

Ha a forrásadattár lekérdezhető, például ha egy relációs áruházban, például az SQL Database vagy SQL Server a, vagy ha például a Table storage vagy az Azure Cosmos DB NoSQL-alapú tárolót érdemes leküldése az oszlopok szűrésének és logika átrendezésével végezze el a **lekérdezés** tulajdonság oszlop-hozzárendelés használata helyett. Ezzel a módszerrel a leképezés, amíg az adatátviteli szolgáltatás adatokat olvas be a forrásadattár, ahol a szolgáltatás sokkal hatékonyabb.

## <a name="other-considerations"></a>Egyéb szempontok
Ha másolandó adatok mérete nagy, módosíthatja a slicing mechanizmus használatával a Data Factoryban az adatok az üzleti logikára további partícióra. Ezt követően ütemezése ennél gyakoribb futtatáshoz, minden egyes másolási tevékenység futtatása az adatméret csökkentése érdekében a másolási tevékenység.

Legyen óvatos az adatkészletek és a Data Factory igénylő összekötőnek ugyanabban az adattárban való egyszerre másolási tevékenységek száma. Számos egyidejű másolási feladat lehet, hogy adattár szabályozás és teljesítménycsökkenésért másolási feladat belső újrapróbálkozás, és bizonyos esetekben végrehajtási hibák vezethet.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: Másolja a helyszíni SQL Serverről a Blob storage
**A forgatókönyv**: Egy folyamat adatokat másol egy helyszíni SQL Server a Blob storage-ban CSV formátumban való használatra készült. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlokat kell tömörített, bzip2 formátumba.

**Teszt- és elemzési**: Az átviteli sebességet, a másolási tevékenység érték kisebb, mint 2 MB/s, amely sokkal lassabb, mint a teljesítmény a teljesítményteszt.

**Teljesítményelemzés és a hangolási**: A teljesítménnyel kapcsolatos problémák elhárításához, tekintsük át az adatok feldolgozása és áthelyezték.

1. **Adatok olvasása**: Átjáró megnyit egy kapcsolatot az SQL Server, és elküldi a lekérdezést. Az SQL Server, ha az intraneten keresztül küld az adatfolyam átjáró válaszol.
2. **Szerializálható és az adatok tömörítése**: Átjáró szerializálja a CSV formátumba az adatfolyamot, és tömöríti az adatokat, bzip2 adatfolyamba.
3. **Adatok írása**: Átjáró a bzip2 stream feltölti a Blob storage, az interneten keresztül.

Folyamatban van, ahogy látható, az adatok feldolgozását, és áthelyezte streamelési soros módon: Az SQL Server > LAN > átjáró > WAN > Blob storage-ban. **Az általános teljesítmény a minimális átviteli engedi át a folyamat között**.

![Az adatfolyam](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhatják a teljesítménybeli szűk keresztmetszetet:

* **forrás**: Maga az SQL Server alacsony átviteli sebesség nagy terheléseket miatt van.
* **Az adatkezelési átjáró**:
  * **HÁLÓZATI ÉBRESZTÉS**: Átjáró az SQL Servert futtató gép távol található, és a egy kis sávszélességű kapcsolattal rendelkezik.
  * **Átjáró**: Átjáró elérte a terhelés korlátatait, a következő műveletek végrehajtásához:
    * **Szerializálási**: Lassú teljesítmény az adatfolyam CSV formátumban történő szerializálásához rendelkezik.
    * **A tömörítés**: Azt választotta, a lassú tömörítési kodek (például bzip2, amely alapvető i7 2.8-as MBps).
  * **WAN**: A vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség értéke alacsony (például a T1 1,544 kbps; = T2 = 6,312 KB/s).
* **Fogadó**: A BLOB storage alacsony átviteli sebesség tartozik. (Ebben a forgatókönyvben a nem valószínű, mert a hozzá tartozó SLA-garanciát legalább 60 MB/s.)

Ebben az esetben az adattömörítés bzip2 előfordulhat, hogy lehet lelassítja a teljes folyamat. A gzip tömörítési kodek átváltása, előfordulhat, hogy a szűk keresztmetszet megkönnyítése érdekében.

## <a name="sample-scenarios-use-parallel-copy"></a>Mintaforgatókönyvek: Párhuzamos példány használatával
**A forgatókönyv I:** 1000 1 MB-os fájlok másolása a helyszíni fájlrendszerhez a Blob storage.

**Elemzés és a teljesítmény-finomhangolási**: Például, ha telepítette az átjárót egy négyportos core gépen Data Factory használatával 16 párhuzamos másolja át a fájlrendszerből a Blob storage egyidejűleg. A párhuzamos végrehajtás kell eredményeznie, nagy átviteli sebességet. Emellett explicit módon megadhatja a párhuzamos másolatok száma. Párhuzamos másolatok sok kis méretű fájlt másol, jelentősen segítenek átviteli erőforrások hatékonyabban használatával.

![1. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-1.png)

**A forgatókönyv II**: 500 MB-os 20 blobok másolása Blob storage-ból a Data Lake Store Analytics, és ezután a teljesítmény hangolására.

**Elemzés és a teljesítmény-finomhangolási**: Ebben a forgatókönyvben a Data Factory a adatokat másol a Blob storage Data Lake Store egy másolási használatával (**parallelCopies** állítsa 1-re) és a egy a felhőbe irányuló adatáthelyezési egységek. Az átviteli sebességet, figyelje meg, amely közel leírását a [teljesítmény referenciaszakasz](#performance-reference).

![2. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-2.png)

**A forgatókönyv III**: Egyedi fájl mérete nagyobb, több tucat MB-nál, és a teljes kötet mérete nagy.

**Elemzés és a teljesítmény bekapcsolása folyamatban**: Növelje a **parallelCopies** egy egyetlen a felhőbe DMU az erőforrás-korlátozások miatt nem eredményez jobb másolási teljesítmény érdekében. Ehelyett adjon meg további felhőalapú DMUs az adatmozgás végrehajtásához további erőforrásokat lekérni. Ne adjon meg egy értéket a **parallelCopies** tulajdonság. A Data Factory, a párhuzamosság kezeli. Ebben az esetben, ha **cloudDataMovementUnits** kell 4, egy átviteli sebessége körülbelül négy alkalommal fordul elő.

![3. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Leírások
Teljesítmény figyelése és hangolása hivatkozásokat a támogatott adattárak némelyikét a következők:

* Az Azure Storage (beleértve a Blob storage és Table storage): [Az Azure Storage skálázhatósági célértékét](../../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya
* Azure SQL Data Warehouse: A funkció mérik az adattárházegységek (Dwu); Lásd: [kezelés számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Az Azure Cosmos DB: [Az Azure Cosmos DB teljesítményszintek](../../cosmos-db/performance-levels.md)
* A helyszíni SQL Server: [Figyelése és a teljesítmény hangolása](https://msdn.microsoft.com/library/ms189081.aspx)
* A helyi fájlkiszolgáló: [Teljesítmény-finomhangolási fájlkiszolgálók](https://msdn.microsoft.com/library/dn567661.aspx)
