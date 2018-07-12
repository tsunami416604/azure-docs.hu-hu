---
title: Adatok áthelyezése másolási tevékenységgel |} A Microsoft Docs
description: 'Adatok áthelyezése a Data Factory-folyamatok ismertetése: felhőalapú adattárak között, valamint a helyszíni és felhőbeli üzlet közötti adatok migrálása. Másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6b13c70d86af195e50190083aa562811236cdd4b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299860"
---
# <a name="move-data-by-using-copy-activity"></a>Adatok áthelyezése másolási tevékenység használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-data-movement-activities.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-overview.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [másolási tevékenység a v2-ben](../copy-activity-overview.md).

## <a name="overview"></a>Áttekintés
Az Azure Data Factory segítségével a másolási tevékenység adatokat másolni a helyszíni és felhőbeli között adattárakban. Után az adatok másolását követően képes lehet további alakította át és elemzése. A másolási tevékenység használatával Adatátalakítási és elemzési eredmények az üzleti intelligenciára épülő (BI) és az alkalmazás elküldött közzététele.

![Szerepkör-másolási tevékenység](media/data-factory-data-movement-activities/copy-activity.png)

A másolási tevékenység működteti, biztonságos, megbízható, méretezhető, és [, globálisan elérhető szolgáltatás](#global). Ez a cikk részletesen a Data Factory és a másolási tevékenység az adatok áthelyezését.

Először nézzük meg, hogyan adatáttelepítés két felhőalapú adattár közötti adatáthelyezéshez, és a egy a helyszíni és felhőalapú adattárak között történik.

> [!NOTE]
> Az általános tevékenység kapcsolatos további információkért lásd: [ismertetése folyamatok és tevékenységek](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Adatmásolás két felhőalapú adattár közötti adatáthelyezéshez
Ha a forrás- és fogadó adattárak a felhőben, a másolási tevékenység végighalad a következő szakaszokban az adatokat másol a forrásadattárból a fogadó. A másolási tevékenység biztosító szolgáltatás:

1. Beolvassa az adatokat a forrásadattárból.
2. Szerializálás/deszerializálás, tömörítése és kibontása, oszlop leképezése hajt végre, és írja be az átalakítás. Ezeket a műveleteket a konfiguráció a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység alapján hajtja végre.
3. A célként megadott adattárba ír adatokat.

A szolgáltatás automatikusan kiválasztja azt az optimális régió az adatmozgás végrehajtásához. Ebben a régióban egyike általában a legközelebbi a fogadó adattárba.

![Felhő-felhőbe történő másolás](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Másolja az adatokat egy helyszíni és felhőalapú adattárak között
Biztonságos az adatok egy helyszíni és egy felhőalapú adattár közötti áthelyezése, telepítse az adatkezelési átjáró a helyszíni gépen. Az adatkezelési átjáró, az ügynök, amely lehetővé teszi az adatok hibrid áthelyezése és feldolgozása. Telepítheti ugyanarra a gépre, az adatok tárolására, saját maga, vagy egy külön számítógépen, amely hozzáfér az adattárhoz.

Ebben a forgatókönyvben az adatkezelési átjáró végzi a szerializálás/deszerializálás, tömörítése és kibontása, oszlop leképezése, és típusátalakítás. Nem adatfolyam keresztül az Azure Data Factory szolgáltatásban. Ehelyett az adatkezelési átjáró közvetlenül a adatokat ír az céltár.

![A helyi – a felhőbe irányuló másolás](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Lásd: [közötti a helyszíni és felhőalapú adattárak](data-factory-move-data-between-onprem-and-cloud.md) bemutatása és a forgatókönyv. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) az ügynök kapcsolatos részletes információkat.

Azure IaaS virtuális gépeket (VM) az adatkezelési átjáró segítségével által szolgáltatott adatokat, és a támogatott adattárak is helyezheti. Ebben az esetben telepíthető az adatkezelési átjáró ugyanazon virtuális Géphez az adatok tárolására, saját maga, vagy egy különálló virtuális gépen, amely hozzáfér az adattárhoz.

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és formátumok
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

> [!NOTE] 
> Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, használjon egy **egyéni tevékenységet** a Data Factoryban az adatok saját logika szerinti másolásához/áthelyezéséhez. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok
Használhatja a másolási tevékenység **másolja a fájlokat,-van** két fájlalapú adattárak között, akkor kihagyhatja a [szakasz formázása](data-factory-create-datasets.md) mind a bemeneti és kimeneti adatkészlet-definíciókban. Az adatokat bármely szerializálás/deszerializálás nélkül hatékonyan másolja.

Másolási tevékenység is olvas és ír a megadott formátumú: **szöveg, JSON, Avro, ORC és a parquet eszközökben**, és a tömörítési kodek **GZip, Deflate, BZip2 és ZipDeflate** támogatottak. Lásd: [támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) adatokkal.

Ha például a következő másolási tevékenységek teheti:

* Adatok másolása helyszíni SQL Server és írni az Azure Data Lake Store ORC formátumban.
* Fájlok másolása a helyszíni fájlrendszer (CSV) szöveges formátumban, és Avro formátumban az Azure-Blobba írni.
* Tömörített fájlok másolása a helyszíni fájlrendszerben és az Azure Data Lake Store föld majd kibontása a.
* A GZip tömörített szöveg (CSV) formátumú adatok másolása az Azure-Blobból, és az Azure SQL Database írási.

## <a name="global"></a>Globálisan elérhető adatáthelyezés
Az Azure Data Factory csak az USA nyugati RÉGIÓJA, USA keleti Régiójában és észak-európai régióban érhető el. Azonban a másolási tevékenység biztosító szolgáltatás érhető el globálisan az alábbi régiók és földrajzi területek számára. A globálisan elérhető topológia általában Ezzel elkerülheti a régiók közötti útválasztók ugrásainak hatékony adatáthelyezés biztosítja. Lásd: [-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) Data Factory és a egy régióban Adatáthelyezés rendelkezésre állását.

### <a name="copy-data-between-cloud-data-stores"></a>Adatok másolása a felhőalapú adattár közötti adatáthelyezéshez
Ha a forrás- és fogadó adattárak a felhőben, a Data Factory szolgáltatástelepítés azonos földrajzi helyen az adatok áthelyezése a fogadó legközelebb eső régióban használja. A hozzárendeléseket a következő táblázatban tekintheti meg:

| Földrajzi hely, a cél-adattárak | A célként megadott adattárba régiója | Adatáthelyezés használt régió |
|:--- |:--- |:--- |
| Egyesült Államok | USA keleti régiója | USA keleti régiója |
| &nbsp; | USA 2. keleti régiója | USA 2. keleti régiója |
| &nbsp; | USA középső régiója | USA középső régiója |
| &nbsp; | USA északi középső régiója | USA északi középső régiója |
| &nbsp; | USA déli középső régiója | USA déli középső régiója |
| &nbsp; | USA nyugati középső régiója | USA nyugati középső régiója |
| &nbsp; | USA nyugati régiója | USA nyugati régiója |
| &nbsp; | USA nyugati régiója, 2. | USA nyugati régiója, 2. |
| Kanada | Kelet-Kanada | Közép-Kanada |
| &nbsp; | Közép-Kanada | Közép-Kanada |
| Brazília | Dél-Brazília | Dél-Brazília |
| Európa | Észak-Európa | Észak-Európa |
| &nbsp; | Nyugat-Európa | Nyugat-Európa |
| Egyesült Királyság | Az Egyesült Királyság nyugati régiója | Az Egyesült Királyság déli régiója |
| &nbsp; | Az Egyesült Királyság déli régiója | Az Egyesült Királyság déli régiója |
| Ázsia és a Csendes-óceáni térség | Délkelet-Ázsia | Délkelet-Ázsia |
| &nbsp; | Kelet-Ázsia | Délkelet-Ázsia |
| Ausztrália | Kelet-Ausztrália | Kelet-Ausztrália |
| &nbsp; | Délkelet-Ausztrália | Délkelet-Ausztrália |
| India | Közép-India | Közép-India |
| &nbsp; | Nyugat-India | Közép-India |
| &nbsp; | Dél-India | Közép-India |
| Japán | Kelet-Japán | Kelet-Japán |
| &nbsp; | Nyugat-Japán | Kelet-Japán |
| Korea | Korea középső régiója | Korea középső régiója |
| &nbsp; | Korea déli régiója | Korea középső régiója |

Azt is megteheti, explicit módon adhatja meg a Data Factory szolgáltatás megadásával a másolás végrehajtásához használt régió `executionLocation` tulajdonság a másolási tevékenység `typeProperties`. Ez a tulajdonság támogatott értékei fent felsorolt **adatáthelyezés használt régió** oszlop. Vegye figyelembe, hogy az adatok halad végig, hogy a régióban a hálózaton keresztül másolása során. Például, hogy közötti másolás az Azure tárolja, koreai, megadhatja `"executionLocation": "Japan East"` útvonalhoz japán régióban keresztül (lásd: [példa JSON](#by-using-json-scripts) hivatkozásként van listázva).

> [!NOTE]
> Ha a célként megadott adattárba régiója nem szerepel a fenti listában, vagy nem észlelhető, alapértelmezés szerint a másolási tevékenység sikertelen lép át egy másik régióba, kivéve, ha `executionLocation` van megadva. A támogatott régióban listában kibomlik idővel.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Másolja az adatokat egy helyszíni és felhőalapú adattárak között
Amikor adatok másolásakor a helyszíni (vagy Azure virtuális gépek/IaaS) és a felhőalapú tárolók, [adatkezelési átjáró](data-factory-data-management-gateway.md) adatáthelyezés hajt végre egy helyszíni gépre vagy virtuális gépen. Az adatok nem továbbítva, a szolgáltatás a felhőben, ha nem használ a [szakaszos Másolás](data-factory-copy-activity-performance.md#staged-copy) képesség. Ebben az esetben áramlanak keresztül az adatok az átmeneti Azure Blob storage a fogadó adattár való írás előtt.

## <a name="create-a-pipeline-with-copy-activity"></a>Másolási tevékenységgel rendelkező folyamat létrehozása
Többféle módon másolási tevékenységgel rendelkező folyamatot hozhat létre:

### <a name="by-using-the-copy-wizard"></a>A másolás varázsló használatával
A Data Factory másolás varázslójával segít másolási tevékenységgel rendelkező folyamat létrehozása. Ez a folyamat lehetővé teszi, hogy az adatok másolása támogatott forrásokból a célhelyekre *JSON írása nélkül* társított szolgáltatások, adatkészletek és folyamatok definíciói. Lásd: [Data Factory másolás varázslójával](data-factory-copy-wizard.md) további információ a varázsló.  

### <a name="by-using-json-scripts"></a>JSON-parancsfájlok használatával
Az Azure portal, a Visual Studio vagy az Azure PowerShell-lel a Data Factory Editor használatával hozzon létre egy folyamatot egy JSON-definíciót (a másolási tevékenység használatával). Ezt követően telepítheti a Data Factoryban a folyamat létrehozásához. Lásd: [oktatóanyag: Azure Data Factory-folyamatot másolási tevékenységgel használata](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részletes utasításokat tartalmazó oktatóanyagért.    

Az összes típusú tevékenységet (például név, leírás, bemeneti és kimeneti táblák és szabályzatok) JSON-tulajdonságokkal érhetők el. Az elérhető tulajdonságok köre az `typeProperties` a tevékenység szakaszban tevékenységek minden típusának számától függ.

A másolási tevékenységhez a `typeProperties` szakasz források típusú függően változik, és a fogadók. Kattintson egy forrásként/fogadóként a a [forrásként és fogadóként támogatott](#supported-data-stores-and-formats) szakaszt, amely támogatja a másolási tevékenység adattár típus tulajdonságainak ismertetése.

Íme egy példa JSON-definíciót:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Az ütemezés szerint van meghatározva a kimeneti adatkészlet határozza meg, hogy a tevékenység futásakor (például: **napi**, gyakoriság **nap**, és az időintervallumot, **1**). A tevékenység adatokat másol a bemeneti adatkészletek (**forrás**), egy kimeneti adatkészletet (**fogadó**).

Másolási tevékenység egynél több bemeneti adatkészletet is megadhat. Ezek segítségével ellenőrizze a függőségeket, a tevékenység futtatása előtt. Azonban csak az első adatkészletből származó adatokat a rendeltetési adatkészlet másolódik. További információkért lásd: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md), amely ismerteti az adatáthelyezés (másolási tevékenység) az Azure Data Factoryban teljesítményét befolyásoló tényezők. Felsorolja a megfigyelt teljesítmény belső tesztelése során és a különböző módjait a másolási tevékenység teljesítményének ismerteti.

## <a name="fault-tolerance"></a>Hibatűrés
Alapértelmezés szerint a másolási tevékenység adatokat, és lépjen vissza a sikertelen másolás leáll Ha ütközik a forrás és fogadó; nem kompatibilis adatait amíg explicit módon konfigurálhatja kihagyásához és a naplófájl az inkompatibilis sorok és csak a Másolás ezeket kompatibilis adatokat, hogy a másolás sikeresen befejeződött. Tekintse meg a [másolási tevékenység a hibatűrés](data-factory-copy-activity-fault-tolerance.md) a további részleteket.

## <a name="security-considerations"></a>Biztonsági szempontok
Tekintse meg a [biztonsági szempontok](data-factory-data-movement-security-considerations.md), amely ismerteti, hogy a biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme.

## <a name="scheduling-and-sequential-copy"></a>Ütemezési és soros másolása
Lásd: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) adat-előállítóban ütemezési és végrehajtási működésével kapcsolatos részletes információk. Lehetőség több másolási műveletek futtatásához egymás után több egymást követő/rendezett módon. Tekintse meg a [egymás után másolja](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szakaszban.

## <a name="type-conversions"></a>Típuskonverziók
Különböző adattárakban különböző nativní typ rendszerek rendelkezik. Másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül:

1. Natív forrástípusok .NET típusúra konvertálni.
2. Typ .NET natív fogadó típusúra konvertálni.

A leképezés egy adattár .NET típusra nativní typ rendszer a megfelelő adatokat tároló cikkben szerepel. (A megadott hivatkozásra kattintva a [támogatott adattárak](#supported-data-stores) tábla). Ezeket a leképezéseket segítségével megfelelő típusainak meghatározása a táblák létrehozása során, úgy, hogy a másolási tevékenység végzi a megfelelő átalakítás.

## <a name="next-steps"></a>További lépések
* További információ a másolási tevékenység, lásd: [adatok másolása az Azure Blob storage-ból az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Adatok áthelyezése a helyszíni adattárolókból a felhőalapú adattárban kapcsolatos további információkért lásd: [adatok áthelyezése a helyszínről a felhőbe adattárak](data-factory-move-data-between-onprem-and-cloud.md).
