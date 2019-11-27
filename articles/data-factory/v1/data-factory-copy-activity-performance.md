---
title: Másolási tevékenységek teljesítményéhez és finomhangolási útmutató
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
ms.openlocfilehash: c1cabbe3587079fa3fa5947eddbcf6cecaff3b98
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682921"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Másolási tevékenységek teljesítményéhez és finomhangolási útmutató

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-copy-activity-performance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-performance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a következő témakört: a [másolási tevékenység teljesítményének és hangolási útmutatójának Data Factory](../copy-activity-performance.md).

Az Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű Adatbetöltési megoldást kínál. Ez lehetővé teszi, hogy másolatot több terabájtnyi adat minden nap gazdag számos felhőalapú és a helyszíni adattárakban. Villámgyors teljesítmény Adatbetöltési fontos annak érdekében, akkor előtérbe kerülhet a core "big data" hiba: fejlett elemzési megoldásokat hozhat, és részletes elemzések lekérése az adatokból.

Az Azure biztosít nagyvállalati adattárolás és adattárház-megoldások, és a másolási tevékenység magas szinten optimalizált Adatbetöltési könnyen konfigurálható, és állítsa be a élményt biztosít. Csak egy egyetlen másolási tevékenységgel rendelkező érhet el:

* Adatok betöltése **Azure SQL Data Warehouse** , **1,2 GB/s**. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).
* Adatok betöltése **Azure Blob storage** , **1,0 GB/s**
* Adatok betöltése **Azure Data Lake Store** , **1,0 GB/s**

Ez a cikk ismerteti:

* [Hivatkozás teljesítményszámokhoz](#performance-reference) támogatott forrás- és fogadó adattárak segítségével tervezheti meg a projekt;
* Azok a funkciók, amelyek növelhetik a másolási sebességet különböző forgatókönyvekben, beleértve a [Felhőbeli adatáthelyezési egységeket](#cloud-data-movement-units), a [párhuzamos másolást](#parallel-copy)és a [szakaszos másolást](#staged-copy);
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) hangolását a teljesítmény- és másolási teljesítményre gyakorolt hatásáról tényezők a.

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse át a cikk olvasása a [másolási tevékenység használatával](data-factory-data-movement-activities.md) című cikket.
>

## <a name="performance-reference"></a>Teljesítmény-referencia

Az alábbi táblázatban látható a másolási átviteli sebesség (MB/s) a megadott forrás-és fogadó párok esetében a házon belüli tesztelés alapján. Az összehasonlításban azt is bemutatja, hogy a [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) és a [adatkezelés átjáró skálázhatósága](data-factory-data-management-gateway-high-availability-scalability.md) (több átjáró-csomópont) milyen különböző beállításokkal tud segíteni a másolási teljesítményben.

![Teljesítmény mátrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory 1. verziójában a felhőből a felhőbe történő másolás minimális Felhőbeli adatáthelyezési egysége kettő. Ha nincs megadva, tekintse meg a [Felhőbeli adatátviteli egységekben](#cloud-data-movement-units)használt alapértelmezett adatáthelyezési egységeket.

**Megjegyzés:**
* Átviteli sebesség kiszámításához a rendszer az alábbi képlettel: [forrás adatsorból beolvasott adatok mérete] / [a másolási tevékenység futtatási időtartama].
* A táblázatban lévő teljesítménymutatók a [TPC-H](http://www.tpc.org/tpch/) adathalmaz használatával lettek megmérve egyetlen másolási tevékenységben.
* Az Azure-beli adat-áruházaiban nagyban forrás- és fogadóadattárként is, azonos Azure-régióban.
* A helyszíni és a Felhőbeli adattárak közötti hibrid másoláshoz az egyes átjáró-csomópontok olyan gépen futnak, amely külön volt a helyszíni adattárban, az alábbi specifikációval. Ha egyetlen tevékenység fut az átjárón, a másolási művelet a teszt gép CPU-, memória-vagy hálózati sávszélességének csak egy kis részét használta fel. További információ a [adatkezelés átjáróval kapcsolatos szempontokról](#considerations-for-data-management-gateway).
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
> Nagyobb átviteli sebességet érhet el, ha több adatáthelyezési egységet (DMUs) használ, mint az alapértelmezett maximális DMUs, amely a felhőről a felhőbe másolási tevékenységek futtatásához 32. Például a 100 DMUs használatával az Azure Blobból másolhatja át az adatait Azure Data Lake Store **1.0 GB/s**-ra. A szolgáltatással és a támogatott forgatókönyvvel kapcsolatos részletekért tekintse meg a [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) című szakaszt. További DMUs igényléséhez forduljon az [Azure támogatási szolgálatához](https://azure.microsoft.com/support/) .

## <a name="parallel-copy"></a>Párhuzamos másolás
A forrástól származó adatok beolvasása vagy az adatok másolása a célhelyre **párhuzamosan, a másolási tevékenység futtatásán belül**. Ez a szolgáltatás javítja a másolási művelet átviteli sebességét, és csökkenti az adatok áthelyezéséhez szükséges időt.

Ez a beállítás eltér a tevékenység definíciójának **Egyidejűség** tulajdonságával. A **párhuzamossági** tulajdonság határozza meg, hogy az **egyidejű másolási tevékenységek** hányan futnak a különböző tevékenységi időszakokból származó adatok (1 – 2, 2 – 3, 3 – 4) és így tovább. Ez a funkció akkor hasznos, ha egy korábbi terhelést hajt végre. A párhuzamos másolási képesség **egyetlen tevékenység futtatására**vonatkozik.

Nézzük meg a minta forgatókönyvet. A következő példában a múltban több szeletet kell feldolgozni. Data Factory a másolási tevékenység (tevékenység futtatása) példányát futtatja az egyes szeletekhez:

* Az első tevékenység ablakból származó adatszelet (1 – 2) = = > tevékenység futtatása 1
* A második tevékenység ablakának adatszelete (2 – 3 AM) = = > tevékenység 2. Futtatás
* A második tevékenység ablakának adatszelete (3 és 4 közötti érték) = = > tevékenység 3. Futtatás

És így tovább.

Ebben a példában, ha a **párhuzamosság** értéke 2, a **tevékenységek 1** . és a **tevékenység futtatása 2** . adatmásolási művelet az adatátviteli teljesítmény javítása érdekében **egyszerre** két tevékenységből áll. Ha azonban több fájl van társítva a (z) 1. tevékenységhez, az adatátviteli szolgáltatás egyszerre másolja át a fájlokat a forrásról a cél egy fájlba.

### <a name="cloud-data-movement-units"></a>Felhőbeli adatáthelyezési egységek
A **Felhőbeli adatáthelyezési egység (DMU)** egy olyan mérték, amely a Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. A DMU a felhőből a felhőbe másolási műveletekre alkalmazható, nem hibrid másolatban.

**A másolási tevékenység futtatásához szükséges minimális Felhőbeli adatáthelyezési egységek kettő.** Ha nincs megadva, a következő táblázat felsorolja a különböző másolási helyzetekben használt alapértelmezett DMUs:

| Másolja ki a forgatókönyv | A szolgáltatás által meghatározott alapértelmezett DMUs |
|:--- |:--- |
| Adatok másolása a fájlalapú tárolók között | A fájlok számától és méretétől függően 4 és 16 között kell lennie. |
| Egyéb másolási forgatókönyvek esetén | 4 |

Az alapértelmezett érték felülbírálásához a **cloudDataMovementUnits** tulajdonság értékét az alábbiak szerint kell megadni. A **cloudDataMovementUnits** tulajdonság **megengedett értékei** : 2, 4, 8, 16, 32. A másolási művelet által a futási időben használt **Felhőbeli DMUs tényleges száma** a megadott értéktől függően egyenlő vagy kevesebb, mint az adatminta. További információ a teljesítmény szintjét egy adott másolási forrásaként és fogadó további egységek konfigurálásakor kaphat,: a [teljesítményfigyelési](#performance-reference).

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
> Ha nagyobb átviteli sebességre van szüksége a Felhőbeli DMUs, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/). A 8-as és újabb verzió beállítása jelenleg csak akkor működik, ha **több fájlt másol a blob Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP-ről a blob Storage/Data Lake Store/Azure SQL Database**helyre.
>

### <a name="parallelcopies"></a>parallelCopies
Használhatja a **parallelCopies** tulajdonság, amely jelzi, amelyeket szeretne használni a másolási tevékenység párhuzamosságát. Ez a tulajdonság belül a másolási tevékenység, amely a forrásból olvashatja vagy írhatja, a fogadó adattárakba párhuzamos szálak maximális számának is felfoghatók.

Minden egyes másolási tevékenység futtatási a Data Factory használatával adatokat másol a forrásadattárból adatokat tárolja, és a cél-adatok tárolására párhuzamos példányszám határozza meg. Az általa használt párhuzamos másolatok alapértelmezett száma a forrás és a fogadó használt típusától függ.

| Forrás és fogadó | Alapértelmezett párhuzamos példányszám szolgáltatás határozza meg |
| --- | --- |
| Az Adatmásolás a fájl alapú tárolók között (blob Storage; Data Lake Store; Amazon S3; helyszíni fájlrendszer; helyszíni HDFS) |1 és 32 között. A fájlok méretétől és a Felhőbeli adatáthelyezési egységek (DMUs) méretétől függ, amelyek a két Felhőbeli adattárolók közötti adatmásolásra vagy a hibrid másoláshoz használt átjáró gép fizikai konfigurációjához (az adatok egy helyszíni adattárba való másolásához) használatosak. |
| Adatok másolása **bármely forrásoldali adattárból az Azure Table Storage-** ba |4 |
| Minden más forrás-és fogadó pár |1 |

Általában az alapértelmezett viselkedésnek a legjobb átviteli sebességet kell biztosítania. Az adattárakat üzemeltető gépek terhelésének szabályozására, illetve a másolási teljesítmény finomhangolására azonban dönthet úgy, hogy felülbírálja az alapértelmezett értéket, és megadja a **parallelCopies** tulajdonság értékét. Az értéknek 1 és 32 között kell lennie (a kettőt is beleértve). Futási időben, a legjobb teljesítmény érdekében másolási tevékenység használja, amely kisebb vagy egyenlő az érték, Ön által beállított értéket.

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
* Ha megad egy értéket a **parallelCopies** tulajdonsághoz, vegye figyelembe a terhelés növekedését a forrás-és fogadó adattárakban, valamint az átjárót, ha hibrid másolat. Ez akkor fordul elő, különösen ha rendelkezik több egyidejű futtatásainak ugyanazokat a tevékenységeket, amelyek ugyanabban az adattárban futtatásához vagy tevékenységeket. Ha azt észleli, hogy az adattár vagy az átjáró túlterhelt a terheléssel, csökkentse a **parallelCopies** értékét a terhelés enyhítése érdekében.
* Másolt adatok, amelyek nem fájlalapú, amelyek a fájlalapú áruházak áruházakból származó, az adatátviteli szolgáltatás figyelmen kívül hagyja-e a **parallelCopies** tulajdonság. Akkor is, ha a párhuzamosság van megadva, ez nem érvényes ebben az esetben.

> [!NOTE]
> Hibrid másolás esetén a **parallelCopies** funkció használatához a adatkezelés Gateway 1,11-es vagy újabb verzióját kell használnia.
>
>

A két tulajdonság jobb kihasználásához és az adatátviteli sebesség növeléséhez tekintse meg a minta használati eseteit. Nem kell konfigurálnia a **parallelCopies** az alapértelmezett működés kihasználása érdekében. Ha a configure és a **parallelCopies** túl kicsi, akkor előfordulhat, hogy a Felhőbeli DMUs nem teljesen teljes mértékben kihasználva.

### <a name="billing-impact"></a>Számlázási hatás
Rendelkezik **fontos** ne feledje, hogy a másolási művelet teljes időtartama alapján díjkötelesek. Ha egy olyan másolási feladatot használ, amely egy órát egy Felhőbeli egységgel végez, és most már 15 percet vesz igénybe négy felhő egységgel, a teljes számla majdnem ugyanaz marad. Tegyük fel például, hogy négy Felhőbeli egységet használ. Az első Felhőbeli egység 10 percet, a másodikat, 10 percet, a harmadikat, 5 percet, a negyedik pedig 5 percet, egy másolási tevékenység futtatásával tölti fel. A teljes másolási (adatáthelyezési) idő után számítunk fel díjat, amely 10 + 10 + 5 + 5 = 30 perc. A **parallelCopies** használata nem befolyásolja a számlázást.

## <a name="staged-copy"></a>Szakaszos másolás
Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, választhatja egy átmeneti előkészítési tárolását a Blob storage használata. Átmeneti különösen hasznos a következő esetekben:

1. **Szeretné betölteni az különféle adattárakból származó adatokat az SQL Data Warehouse polybase**. Az SQL Data Warehouse a polybase nagy átviteli sebességű mechanizmusként nagy mennyiségű adatok betöltése az SQL Data Warehouse-bA. A forrásadatok esetében azonban a blob Storage-ban kell szerepelnie, és meg kell felelnie a további feltételeknek. Ha a blob Storage-tól eltérő adattárból tölt be adatait, az Adatmásolást átmeneti átmeneti blob Storage használatával aktiválhatja. Ebben az esetben a Data Factory, győződjön meg arról, hogy megfelel-e a PolyBase követelményeinek szükséges adatátalakítások hajt végre. Ezután használja a PolyBase adatok betöltése az SQL Data Warehouse-bA. További részletekért lásd: az [adatok Azure SQL Data Warehouseba való betöltésének alapja](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).
2. **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattár és egy felhőalapú adattár közötti másolást) lassú hálózati kapcsolaton keresztül**. A teljesítmény javítása érdekében a helyszínen tömörítheti az adatok mennyiségét, így kevesebb időt vesz igénybe, hogy az adatok áthelyezhetők legyenek a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását, mielőtt betölti azt a cél adattárba.
3. **Nyissa meg a 80-as port-astól különböző portok és a tűzfal, a 443-as porton a vállalati informatikai házirend miatt nem szeretné**. Például ha egy Azure SQL Database fogadó vagy egy Azure SQL Data Warehouse fogadó adatokat másol egy helyszíni adattárolóban, kell aktiválni a kimenő TCP-kommunikációt az 1433-as porton, a Windows tűzfal és a vállalati tűzfalon. Ebben a forgatókönyvben az átjáró előnyeit kihasználva először másolja át az adatmásolt blob Storage-alapú átmeneti példányba HTTP-n vagy HTTPS-en keresztül a 443-es porton. Ezután töltse be az adatok SQL Database vagy SQL Data Warehouse a blob Storage-előkészítésből. Ebben a folyamatban nem kell 1433-as port engedélyezése.

### <a name="how-staged-copy-works"></a>Hogyan szakaszos másolás működése
Az előkészítési funkció aktiválása után a rendszer először a forrás adattárból másolja át az adattárat az átmeneti adattárba (saját maga is). Ezután az adatok átmásolva az átmeneti adattár a fogadó adattárba. A Data Factory automatikusan kezeli az Ön számára a két szakaszból álló folyamatot. A Data Factory is megtisztítja az átmeneti tárolási ideiglenes adatokat, az adatáthelyezés befejeződése után.

A Felhőbeli másolási forgatókönyvben (a forrás-és a fogadó adattárolók a felhőben vannak), az átjáró nincs használatban. A Data Factory szolgáltatás végrehajtja a másolási műveleteket.

![Előkészített másolás: Felhőbeli forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

A hibrid másolási forgatókönyvben (a forrás a helyszínen, a fogadó pedig a felhőben van), az átjáró áthelyezi az adatait a forrás adattárból egy átmeneti adattárba. Data Factory a szolgáltatás áthelyezi az adatok az előkészítési adattárból a fogadó adattárba. A fordított folyamat a Felhőbeli adattárakból a helyszíni adattárba történő Adatmásolást is támogatja.

![Előkészített másolás: hibrid forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Adatok áthelyezése az átmeneti tárolási obchodu aktiválásakor is megadhat kívánja-e az adatokat, mielőtt adatokat a forrásadattárból egy ideiglenes vagy átmeneti adattár tömörített, és ezután előtt, amely adatokat helyez át egy ideiglenes vagy átmeneti adatok kibontása a fogadó adattár tárolja.

Jelenleg nem másolhat adatokat egy átmeneti tárnak használatával két helyszíni adattárak között. Reméljük, hogy ez a lehetőség hamarosan elérhető lesz.

### <a name="configuration"></a>Konfiguráció
Konfigurálja a **enableStaging** a másolási tevékenység beállítást adja meg, hogy az adatokat Blob storage-ban a célként megadott adattárba történő betöltésük előtt átmenetileg tárolva. Ha a **enableStaging** értéke TRUE (igaz) értékre van állítva, adja meg a következő táblázatban felsorolt további tulajdonságokat. Ha még nincs fiókja, is szeretne létrehozni egy Azure Storage vagy a megosztott hozzáférési aláírás-társított szolgáltatást az átmeneti tárolási.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| **enableStaging** |Adja meg, hogy szeretné-e az ideiglenes tároló átmeneti keresztül adatok másolása. |False (Hamis) |Nem |
| **linkedServiceName** |Adja meg egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás nevét, amely az átmeneti előkészítési tárolóként használt tároló példányára hivatkozik. <br/><br/> Adatok betöltése az SQL Data Warehouse polybase a tároló közös hozzáférésű jogosultságkód nem használható. Más esetekben használhatja azt. |N/A |Igen, mikor **enableStaging** igaz értékre van beállítva |
| **path** |Adja meg a Blob elérési útja, amelyet szeretne az előkészített adatokat tartalmaznak. Ha nem ad meg elérési utat, a szolgáltatás ideiglenes adatokat tárolni egy tárolót hoz létre. <br/><br/> Csak akkor, ha a Storage használata a közös hozzáférésű jogosultságkód vagy ideiglenes az adatokat egy adott helyen van szüksége, adjon meg egy elérési utat. |N/A |Nem |
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

### <a name="billing-impact"></a>Számlázási hatás
Két lépést alapján számítjuk fel: másolás időtartama, és másolja a típusát.

* Ha a Felhőbeli másolás során (az adatok egy Felhőbeli adattárból egy másik Felhőbeli adattárba való másolásával) való átmeneti tárolást használ, akkor az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] [a másolás időtartamának összegét kell megfizetnie.
* Ha hibrid másolás során (az adatok egy helyszíni adattárból egy Felhőbeli adattárba történő másolásával) végez előkészítést, akkor a [hibrid másolási időtartam] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések
Javasoljuk, hogy ezen lépések a Data Factory szolgáltatás másolási tevékenységgel rendelkező teljesítményét:

1. **Megállapítja az alapértékeket**. A fejlesztési fázis során a folyamat tesztelése a másolási tevékenység használatával egy reprezentatív mintát ellen. A Data Factory [szeletelő modell](data-factory-scheduling-and-execution.md) használatával korlátozhatja a munkához használt adatmennyiséget.

   A **figyelési és felügyeleti alkalmazás**használatával Gyűjtse össze a végrehajtási időt és a teljesítmény jellemzőit. A Data Factory kezdőlapján válassza a **figyelés & kezelés** lehetőséget. A fanézetben válassza ki a **kimeneti adatkészletet**. A **tevékenység Windows** listájában válassza a másolási tevékenység Futtatás lehetőséget. A **tevékenység Windows** a másolási tevékenység időtartamát és a másolt adatméretet sorolja fel. Az átviteli sebesség megjelenik a **tevékenység ablak-kezelőben**. További információ az alkalmazásról: [Azure Data Factory folyamatok figyelése és felügyelete a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md).

   ![Tevékenységfuttatás részletei](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   A cikk későbbi részében összehasonlíthatja a forgatókönyv teljesítményét és konfigurációját a tevékenységek [teljesítmény-referenciájának](#performance-reference) a tesztek alapján történő másolásához.
2. **Diagnosztika és a teljesítmény optimalizálása**. Ha a teljesítmény megfigyelte nem felel meg az elvárásainak, azonosíthatja a szűk keresztmetszeteket szeretné. Távolítsa el, vagy a szűk keresztmetszetek elkerülése érdekében a teljesítmény optimalizálásával majd. Teljesítmény diagnosztikai teljes leírását ez a cikk nem foglalkozik, de Íme néhány gyakori szempontok:

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolása](#parallel-copy)
     * [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units)
     * [Szakaszos másolás](#staged-copy)
     * [adatkezelés átjáró méretezhetősége](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Adatkezelési átjáró](#considerations-for-data-management-gateway)
   * [Forrás](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Szerializálást és deszerializálást](#considerations-for-serialization-and-deserialization)
   * [A tömörítés](#considerations-for-compression)
   * [Oszlopleképezés](#considerations-for-column-mapping)
   * [Egyéb szempontok](#other-considerations)
3. **Bontsa ki a konfigurációt, a teljes adatkészletet**. Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamat aktív időszakát, hogy az a teljes adathalmazra vonatkozzon.

## <a name="considerations-for-data-management-gateway"></a>Az adatkezelés átjáróval kapcsolatos megfontolások
**Átjáró beállítása**: javasoljuk, hogy egy dedikált gépet használjon adatkezelés átjáró üzemeltetéséhez. Lásd: [adatkezelés átjáró használatának szempontjai](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Átjáró figyelése és vertikális felskálázása**: egy vagy több átjárót tartalmazó logikai átjáró egyszerre több másolási tevékenységet is képes futni. Megtekintheti az erőforrás-felhasználás (CPU, memória, hálózat (in/out), stb.) közel valós idejű pillanatképét, valamint a Azure Portalon futó egyidejű feladatok számát, az [átjáró figyelése a portálon](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal)című témakört. Ha nagy szükség van a hibrid adatáthelyezésre, vagy nagyszámú egyidejű másolási tevékenység fut, vagy nagy mennyiségű adattal kíván másolni, vegye fontolóra az [átjáró vertikális felskálázását vagy felskálázását](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) , hogy jobban kihasználja az erőforrást, vagy több erőforrást lehessen kiépíteni másolja.

## <a name="considerations-for-the-source"></a>A forrás szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, lásd: [megfigyelés és finomhangolás témakörök](#performance-reference) , amelyek adott adattárak, és segítenek megérteni, hogy adatokat tárolni a teljesítményt nyújt, válaszidők csökkentése érdekében és átviteli teljesítmény.

Ha a blob Storage-ból SQL Data Warehouseba másol adatait, a teljesítmény növelése érdekében érdemes lehet a **Base** használata. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Blob Storage, Data Lake Store, Amazon S3, helyszíni fájlrendszerek és helyszíni HDFS tartalmazza)*

* **Átlagos méretét és a fájlszám**: másolási tevékenységgel továbbítja egy adatfájlt egy időben. Az adatok áthelyezésének akkora a teljes átviteli sebesség alacsonyabb, ha néhány nagy méretű fájlok miatt a rendszer-indításkori fázis minden egyes fájl helyett a sok kis méretű fájlt tartalmaz. Ezért ha lehetséges, egyesítendő kisméretű fájlok nagyobb fájlok nagyobb átviteli sebességet biztosítanak.
* **A fájl formátuma és tömörítés**: találhat további részleteket a teljesítmény javítása, a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszok.
* A helyszíni **fájlrendszer** esetében, amelyben **adatkezelés átjáró** szükséges, tekintse meg a [adatkezelés átjáróval kapcsolatos szempontok](#considerations-for-data-management-gateway) című szakaszt.

### <a name="relational-data-stores"></a>Relációs adattárak
*(Tartalmazza a SQL Database; SQL Data Warehouse; Amazon vöröseltolódás; SQL Server adatbázisok; és Oracle-, MySQL-, DB2-, Teradata-, Sybase-és PostgreSQL-adatbázisok stb.)*

* **Adatmintát**: A következő tábla sémáját másolási átviteli sebesség hatással van. Egy nagy méretű sor mérete kis sor mérete, azonos mennyiségű adatot másolni egy jobb teljesítményt biztosít. A hiba oka, hogy az adatbázis hatékonyabban lekérheti kevesebb váró adatokat, amelyek kevesebb sort tartalmaznak.
* **Lekérdezés vagy tárolt eljárás**: optimalizálja a lekérdezést, vagy adja meg, ha a másolási tevékenység forrásból történő adatlehívást, hatékonyabban tárolt eljárás logikáját.
* Az **adatkezelés Gateway**használatát igénylő helyszíni, például a SQL Server és az **Oracle számára a**következő témakörben talál tudnivalókat: adatkezelés Gateway című szakasz.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai
### <a name="general"></a>Általános kérdések
Győződjön meg arról, hogy az alapul szolgáló adattár nem túlterhelik az egyéb példányán, vagy a futó számítási feladatokat.

A Microsoft olyan adattárakban, tekintse meg [megfigyelés és finomhangolás témakörök](#performance-reference) kifejezetten az adattárakban. Ezek a témakörök segítségével megismerheti a data store teljesítményt nyújt és a válaszidők csökkentése érdekében, és az átviteli teljesítmény.

Ha a **blob Storage** -ból **SQL Data Warehouseba**másol Adatmásolást, érdemes lehet a teljesítmény növelésére a **Base** használatával. Lásd: [bA a PolyBase használatával az adatok betöltése az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) részleteiről. A használati esetek, olvassa [1 TB adat betöltése az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájlalapú adattárak
*(Blob Storage, Data Lake Store, Amazon S3, helyszíni fájlrendszerek és helyszíni HDFS tartalmazza)*

* **Másolja a viselkedés**: adatokat másolja egy másik fájl alapú adatokat az adattárból, ha a másolási tevékenység használatával három pontot tartalmaz az **copyBehavior** tulajdonság. Ez a megőrzi a hierarchia, simítja egybe a hierarchia, vagy egyesíti a fájlokat. Az egybesimítás hierarchia vagy megőrzi az rendelkezik alig vagy egyáltalán nem teljesítménybeli terhelést, de a fájlok egyesítése növelése teljesítménybeli terhelést okoz.
* **A fájl formátuma és tömörítés**: tekintse meg a [szerializálást és deszerializálást szempontjai](#considerations-for-serialization-and-deserialization) és [tömörítési szempontjai](#considerations-for-compression) szakaszokban talál további részleteket a teljesítmény javítása.
* **Blob Storage**: a blob Storage jelenleg csak blokk blobokat támogat az optimalizált adatátvitelhez és átviteli sebességhez.
* Adatkezelés **-** **átjáró**használatát igénylő helyszíni fájlrendszerek esetében tekintse meg a [adatkezelés átjáróval kapcsolatos szempontokat](#considerations-for-data-management-gateway) ismertető szakaszt.

### <a name="relational-data-stores"></a>Relációs adattárak
*(SQL Database, SQL Data Warehouse, SQL Server adatbázist és Oracle-adatbázisokat tartalmaz)*

* **Másolja a viselkedés**: függően a beállított tulajdonságok **sqlSink**, a másolási tevékenység adatokat ír a céladatbázis különböző módon.
  * Alapértelmezés szerint az adatok adatátviteli szolgáltatás által a tömeges másolási API-t az adatok beszúrása hozzáfűzése üzemmódot, amely a lehető legjobb teljesítményt biztosít.
  * Ha a fogadó tárolt eljárás konfigurálja, az adatbázis az adatokat egy sort egy kötegelt betöltés, hanem egyszerre vonatkozik. Teljesítmény jelentősen csökken. Ha az adathalmaz mérete nagy, akkor érdemes lehet a **sqlWriterCleanupScript** tulajdonság használatára váltani.
  * Ha a **sqlWriterCleanupScript** tulajdonságot minden egyes másolási tevékenységhez beállítja, a szolgáltatás elindítja a parancsfájlt, majd a tömeges másolási API használatával szúrja be az adatmennyiséget. Például a teljes tábla felülírja a legújabb adatokkal, is megadhat egy parancsfájlt, amely először törölje az összes rekordot a forrás az új adatok tömeges-betöltés előtt.
* **A minta és a batch adatméret**:
  * A következő tábla sémáját másolási átviteli sebesség hatással van. Az azonos mennyiségű adatot másolni, egy nagy méretű sor mérete ad egy kis sorméret jobb teljesítményt, mert az adatbázis hatékonyabban véglegesítheti kevesebb váró adatokat.
  * A másolási tevékenység adatokat egy sorozat része kötegek szúr be. Beállíthatja a sorok számát a batch segítségével a **writeBatchSize** tulajdonság. Ha az adatok kis sorral rendelkezik, akkor lehet beállítani a **writeBatchSize** tulajdonság számára, hogy alacsonyabb batch terhelést és nagyobb átviteli sebességet a magasabb értékű. Ha az adatokat a sor mérete túl nagy, ügyeljen arra, ha növeli **writeBatchSize**. Egy magas szintű egy példány hibáját okozta. az adatbázis terhelve vezethet.
* A helyszíni, SQL Server és Oracle **-alapú** , **adatkezelés átjáró**használatát igénylő adatbázisok esetében tekintse meg az [adatkezelés-átjáró szempontjai](#considerations-for-data-management-gateway) című szakaszt.

### <a name="nosql-stores"></a>NoSQL-tárolókat
*(Tartalmazza a Table Storage és a Azure Cosmos DB)*

* A **Table storage**:
  * **Partíció**: adatok írása összefűzéses partíciók jelentősen rontja a teljesítményt. A forrásadatok rendezés partíciós kulccsal, hogy az adatokat a rendszer beszúrta hatékonyan egy partíciót egymás után, vagy módosítsa az adatokat írni az ugyanazon a partíción logikát.
* **Azure Cosmos db**esetén:
  * **Köteg mérete**: a **writeBatchSize** tulajdonság állítja be a Azure Cosmos db szolgáltatásnak küldött párhuzamos kérelmek számát a dokumentumok létrehozásához. Nagyobb teljesítmény várható a **writeBatchSize** növelésekor, mert a rendszer több párhuzamos kérelmet kap Azure Cosmos db. Ha azonban a Azure Cosmos DB írásakor a szabályozást szeretné megtekinteni (a hibaüzenet "kérések aránya nagy"). A különböző tényezők a szabályozáshoz vezethetnek, beleértve a dokumentumok méretét, a dokumentumok használati feltételeit és a cél gyűjtemény indexelési házirendjét. A nagyobb másolási sebesség eléréséhez érdemes lehet egy jobb gyűjteményt használni, például: S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Szerializálást és deszerializálást szempontjai
Szerializálást és deszerializálást akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájlt. Lásd: [támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) másolási tevékenység által támogatott fájlformátumokról részleteinek ismertetésével együtt.

**Másolja a viselkedés**:

* A fájlok másolása a fájlalapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészletek esetében ugyanaz vagy nincs fájlformátum-beállítás, az adatáthelyezési szolgáltatás a szerializálás vagy a deszerializálás nélkül hajtja végre a bináris másolást. Megjelenik egy a forgatókönyv, amelyben a forrás- és fogadó fájlformátum beállításai eltérnek egymáshoz képest nagyobb átviteli sebességet.
  * Amikor bemeneti és kimeneti adatkészletek mindkét szöveges formátum, és csak a kódolás típusát különböző, az adatok mozgását szolgáltatásnak csak nincs a kódolási átalakítás. Még nem csinál bármely szerializálási és deszerializálás, amely néhány bináris másolat többletterhelést teljesítményt okoz.
  * Ha a bemeneti és kimeneti adatkészleteket is rendelkezik különböző fájlformátumokban vagy különböző konfigurációkat, például az elválasztó karakterek, az adatátviteli szolgáltatás deserializes forrásadatok adatfolyam, átalakítása és majd szerializálni a kimeneti formátumba, jelzett. Ez a művelet egy sokkal jelentős teljesítménybeli többletterhelést képest más esetben eredményez.
* Ha másolja a fájlokat egy adattár, amely nem fájl alapú (például az olyan fájlalapú tároló relációs tárolóval), akkor a szerializálása vagy deszerializálása lépésre szükség. Ebben a lépésben jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: A választott fájl formátumát hatással lehetnek a másolási teljesítmény. Például a Avro kompakt bináris formátum, amely tárolja az adatokat metaadatok. Széles körben támogatja a Hadoop-ökoszisztéma, feldolgozása és lekérdezési rendelkezik. Azonban az Avro szerializálást és deszerializálást, ami szövegformátum képest alacsonyabb másolási átviteli sebességet eredményez a drágább. Győződjön meg azok alkalmazásfüggőségeit a kiválasztott fájlformátum teljes feldolgozási folyamat során. Kezdés milyen formában az adatokat tárolja a forrásadattárakból vagy külső rendszerek; kinyerése a storage, az analitikus feldolgozás és a lekérdezési; legjobb formátum és milyen formátumban az adatok exportálja az adatpiacok jelentéskészítő és vizualizációs eszközök, a. Néha olyan fájlformátum, amely az optimálisnál rosszabb olvasási és írási teljesítmény előfordulhat, hogy jó választás meghatározásakor a teljes elemzési folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai
Ha a bemeneti vagy kimeneti adatkészlet egy fájlt, írja az adatokat a rendeltetési tömörítés vagy a kibontás végrehajtani a másolási tevékenység állíthatja be. Ha tömörítést választja, akkor győződjön meg arról, bemeneti/kimeneti (I/O) magával és a CPU. Tömöríti a számítási erőforrásokat az extra költségek. De cserébe csökkenti a hálózati i/o- és storage. Az adatoktól függően a teljes másolási átviteli sebesség boost jelenhet meg.

**Kodek**: a másolási tevékenység támogatja a gzip, a bzip2 és a deflate tömörítési típusokat. Az Azure HDInsight mindhárom típust felhasználhatja feldolgozásra. Minden tömörítési kodeknek van előnye. Például bzip2 rendelkezik a legalacsonyabb másolási teljesítmény, de a legjobb Hive lekérdezés teljesítmény bzip2 kap, mert azt a feldolgozáshoz is fel. A gzip a legtöbb kiegyensúlyozott beállítás használatát, és azt a leggyakrabban használt. Válassza ki a leginkább megfelelő a végpontok közötti forgatókönyv kodek.

**Szint**: minden egyes tömörítési kodeket két lehetőség közül választhat: leggyorsabb tömörített és optimális tömörített. A leggyorsabb tömörített lehetőség a lehető leggyorsabban tömöríti az adatokat, még akkor is, ha az eredményül kapott fájl nem optimális tömöríti. Az optimális tömörített beállítás több időt tölt a tömörítést, és a egy minimális mennyiségű adatot eredményez. Mindkét lehetőség megtekintéséhez, amely biztosítja, hogy jobb általános teljesítményt abban az esetben tesztelheti.

**Megfontolás**: Ha nagy mennyiségű adatmennyiséget szeretne másolni egy helyszíni tároló és a felhő között, fontolja meg az ideiglenes blob Storage használatát tömörítéssel. Ideiglenes tároló használata akkor hasznos, ha a sávszélesség a vállalati hálózat és az Azure-szolgáltatások korlátozzák, és azt szeretné, hogy a bemeneti adatkészlet és a kimeneti adatkészlet mindkét tömörítetlen formában. Pontosabban egyetlen másolási tevékenységet is megszakíthat két másolási tevékenységre. Az első másolási tevékenység a forrásról egy ideiglenes vagy átmeneti blobba másol tömörített formában. A második másolási tevékenység átmásolja a tömörített adatot az előkészítésből, majd kibontja, miközben a fogadóba ír.

## <a name="considerations-for-column-mapping"></a>Az oszlopleképezés szempontjai
Beállíthatja a **leképezésekben** tulajdonság a másolási tevékenység a térkép összes vagy a bemeneti oszlopok, a kimeneti oszlopok egy része. Az adatáthelyezési szolgáltatás a forrásból olvassa be az adatokat, miután kell végezni az adatokon oszlop-hozzárendelés előtt írja az adatokat a fogadó. A további feldolgozás csökkenti a másolási átviteli sebességet.

Ha a forrásadattár lekérdezhető, például ha egy relációs áruházban, például az SQL Database vagy SQL Server a, vagy ha például a Table storage vagy az Azure Cosmos DB NoSQL-alapú tárolót érdemes leküldése az oszlopok szűrésének és logika átrendezésével végezze el a **lekérdezés** tulajdonság oszlop-hozzárendelés használata helyett. Ezzel a módszerrel a leképezés, amíg az adatátviteli szolgáltatás adatokat olvas be a forrásadattár, ahol a szolgáltatás sokkal hatékonyabb.

## <a name="other-considerations"></a>Egyéb szempontok
Ha a másolni kívánt adatméret nagy, beállíthatja az üzleti logikáját, hogy tovább particionálja az adatmennyiséget a Data Factory szeletelő mechanizmusának használatával. Ezután ütemezze a másolási tevékenységet úgy, hogy gyakrabban fusson az egyes másolási tevékenységek adatméretének csökkentése érdekében.

Legyen óvatos az adathalmazok számával és a másolási tevékenységekkel, amelyeknek Data Factoryt kell megadniuk az ugyanahhoz az adattárhoz való csatlakozáshoz. Számos egyidejű másolási feladat lehet, hogy adattár szabályozás és teljesítménycsökkenésért másolási feladat belső újrapróbálkozás, és bizonyos esetekben végrehajtási hibák vezethet.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Mintaforgatókönyv: másolása a helyszíni SQL Serverről a Blob storage
**A forgatókönyv**: A folyamat adatokat másol egy helyszíni SQL Server a Blob storage-ban CSV formátumban való használatra készült. Ahhoz, hogy gyorsabban a másolási feladat, a CSV-fájlokat kell tömörített, bzip2 formátumba.

**Teszt- és elemzési**: a másolási tevékenység az átviteli sebesség legfeljebb 2 MB/s, azaz sokkal lassabb, mint a teljesítmény a teljesítményteszt.

**Teljesítményelemzés és a hangolási**: a teljesítménnyel kapcsolatos problémák elhárításához, nézzük meg az adatok feldolgozása és áthelyezték.

1. **Adatolvasás**: az átjáró megnyit egy kapcsolódást a SQL Serverhoz, és elküldi a lekérdezést. SQL Server válaszol, ha az adatfolyamot az átjárón keresztül az intraneten keresztül küldi el.
2. **Adatok szerializálása és tömörítése**: az ÁTJÁRÓ a CSV formátumba szerializálja az adatfolyamot, és tömöríti az adatok egy bzip2-adatfolyamba.
3. **Adatok írása**: az átjáró feltölti a bzip2 streamet a blob Storage-ba az interneten keresztül.

Amint láthatja, az adatfeldolgozás folyamatban van, és folyamatos átvitel alatt áll: SQL Server > LAN > átjáró > WAN > blob Storage. **Az általános teljesítmény a minimális átviteli engedi át a folyamat között**.

![Az adatfolyam](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Egy vagy több, az alábbi tényezők okozhatják a teljesítménybeli szűk keresztmetszetet:

* **Forrás**: az SQL Server magát rendelkezik alacsony átviteli sebesség miatt nagy terheléseket.
* **Adatkezelés átjáró**:
  * **LAN**: az átjáró távol van a SQL Server gépről, és alacsony sávszélességű kapcsolattal rendelkezik.
  * **Átjáró**: az átjáró elérte a terhelési korlátozásokat a következő műveletek elvégzéséhez:
    * **Szerializálási**: lassú teljesítmény az adatfolyam CSV formátumban történő szerializálásához rendelkezik.
    * **A tömörítés**: választotta, a lassú tömörítési kodek (például bzip2, amely alapvető i7 2.8-as MBps).
  * **WAN**: a vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség értéke alacsony (például a T1 1,544 kbps; = T2 = 6,312 KB/s).
* **Fogadó**: Blob storage alacsony átviteli sebesség tartozik. (Ebben a forgatókönyvben a nem valószínű, mert a hozzá tartozó SLA-garanciát legalább 60 MB/s.)

Ebben az esetben az adattömörítés bzip2 előfordulhat, hogy lehet lelassítja a teljes folyamat. A gzip tömörítési kodek átváltása, előfordulhat, hogy a szűk keresztmetszet megkönnyítése érdekében.

## <a name="sample-scenarios-use-parallel-copy"></a>Példa forgatókönyvek: párhuzamos másolás használata
**I. forgatókönyv:** Másolja az 1 000 1-MB fájlokat a helyszíni fájlrendszerből a blob Storage-ba.

**Elemzés és teljesítmény finombeállítása**: Ha egy négymagos gépre telepítette az átjárót, a Data Factory 16 párhuzamos másolatot használ a fájlok a fájlrendszerből a blob Storage-ba való párhuzamos áthelyezéséhez. Ennek a párhuzamos végrehajtásnak nagy átviteli sebességet kell eredményeznie. Explicit módon megadhatja a párhuzamos másolatok darabszámát is. Sok kis fájl másolásakor a párhuzamos másolatok jelentősen segítik az átviteli sebességet az erőforrások hatékonyabb használatával.

![1\. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-1.png)

**II. forgatókönyv**: másolja a blob Storage-ból 500 MB-nyi 20 blobot a Data Lake Store analitikaba, majd hangolja a teljesítményt.

**Elemzés és teljesítmény finomhangolása**: ebben a forgatókönyvben a Data Factory a blob Storage-ból másolt adatok átmásolása egy egypéldányos (**parallelCopies** -érték 1) és egy Felhőbeli adatáthelyezési egység használatával Data Lake Store. A megfigyelt átviteli sebesség a [teljesítmény-referenciák szakaszban](#performance-reference)leírtak szerint fog megjelenni.

![2\. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-2.png)

**III. forgatókönyv**: az egyes fájlok mérete nagyobb, mint több tucat MBS, és a teljes kötet nagy.

**Elemzés és teljesítmény**: a **parallelCopies** növelése nem eredményez jobb másolási teljesítményt az egyfelhős DMU erőforrás-korlátai miatt. Ehelyett több Felhőbeli DMUs kell megadnia ahhoz, hogy további erőforrások legyenek az adatok áthelyezéséhez. Ne határozzon meg értéket a **parallelCopies** tulajdonsághoz. Data Factory kezeli a párhuzamosságot. Ebben az esetben, ha a **cloudDataMovementUnits** 4 értékre állítja, az átviteli sebesség körülbelül négy alkalommal történik.

![3\. forgatókönyv](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referencia
Az alábbiakban a támogatott adattárakkal kapcsolatos Teljesítményfigyelés és hangolási referenciák találhatók:

* Az Azure Storage (beleértve a Blob storage és Table storage): [Azure Storage skálázhatósági célértékét](../../storage/common/storage-scalability-targets.md) és [Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../../storage/common/storage-performance-checklist.md)
* Az Azure SQL Database: Is [figyelemmel kísérni a teljesítményét](../../sql-database/sql-database-single-database-monitor.md) , és ellenőrizze az adatbázis tranzakciós egységek (DTU) százalékos aránya
* Az Azure SQL Data Warehouse: Alkalmasságát mérik az adattárházegységek (Dwu); Lásd: [kezelés számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Az Azure Cosmos DB: [az Azure Cosmos DB teljesítményszintek](../../cosmos-db/performance-levels.md)
* A helyszíni SQL Server: [figyelés és hangolás teljesítmény](https://msdn.microsoft.com/library/ms189081.aspx)
* A helyi fájlkiszolgáló: [teljesítményhangolás fájlkiszolgálók](https://msdn.microsoft.com/library/dn567661.aspx)
