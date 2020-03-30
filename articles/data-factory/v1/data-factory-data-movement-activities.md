---
title: Adatok áthelyezése a Másolási tevékenység használatával
description: 'Ismerje meg a Data Factory-folyamatok adatmozgatását: a felhőtárolók közötti, valamint a helyszíni és a felhőalapú tárolók közötti adatáttelepítést. Másolási tevékenység használata.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281885"
---
# <a name="move-data-by-using-copy-activity"></a>Adatok áthelyezése a Másolási tevékenység használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-data-movement-activities.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-overview.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Tevékenység másolása a V2 programban című témakört.](../copy-activity-overview.md)

## <a name="overview"></a>Áttekintés
Az Azure Data Factoryban a Másolási tevékenység segítségével adatokat másolhat a helyszíni és a felhőbeli adattárak között. Az adatok másolása után tovább alakítható és elemezhető. A Tevékenység másolása segítségével is közzéteheti az üzleti intelligencia (BI) és az alkalmazások felhasználásának átalakítási és elemzési eredményeit.

![Másolási tevékenység szerepe](media/data-factory-data-movement-activities/copy-activity.png)

A Másolási tevékenységet egy biztonságos, megbízható, skálázható és [globálisan elérhető szolgáltatás](#global)hajtja. Ez a cikk a Data Factory és a Copy Activity adatmozgatásának részleteit tartalmazza.

Először nézzük meg, hogyan történik az adatok áttelepítése két felhőbeli adattárak között, valamint egy helyszíni adattár és egy felhőbeli adattár között.

> [!NOTE]
> A tevékenységekről általában a [Folyamatok és tevékenységek ismertetése](data-factory-create-pipelines.md)című témakörben olvashat.
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Adatok másolása két felhőalapú adattár között
Ha a forrás- és a fogadó adattárak a felhőben vannak, a Másolási tevékenység a következő szakaszokon megy keresztül az adatok másolásához a forrásból a fogadóba. A másolási tevékenységet működtető szolgáltatás:

1. Adatokat olvas be a forrásadattárból.
2. Szerializálást/deszerializálást, tömörítést/dekompressziót, oszlopleképezést és típuskonverziót hajt végre. Ezeket a műveleteket a bemeneti adatkészlet, a kimeneti adatkészlet és a tevékenység másolása konfigurációi alapján végzi el.
3. Adatokat ír a céladattárba.

A szolgáltatás automatikusan kiválasztja az optimális régiót az adatáthelyezés végrehajtásához. Ez a régió általában az egyik legközelebb a fogadó adattár.

![Felhőből a felhőbe irányuló másolat](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Adatok másolása a helyszíni adattár és a felhőalapú adattár között
Ha biztonságosan szeretné áthelyezni az adatokat egy helyszíni adattár és egy felhőalapú adattár között, telepítse az Adatkezelési átjárót a helyszíni gépre. Az adatkezelési átjáró egy olyan ügynök, amely lehetővé teszi a hibrid adatok mozgását és feldolgozását. Telepítheti ugyanazon a számítógépen, mint maga az adattár, vagy egy külön gépen, amely hozzáfér az adattárhoz.

Ebben az esetben az Adatkezelési átjáró végrehajtja a szerializálást/deszerializálást, a tömörítést/dekompressziót, az oszlopleképezést és a típuskonverziót. Az adatok nem folynak az Azure Data Factory szolgáltatáson keresztül. Ehelyett az Adatkezelési átjáró közvetlenül írja az adatokat a céltárolóba.

![Helyszíni– felhőalapú másolás](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Az [Adatok áthelyezése a helyszíni és a felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) a bevezetésért és a forgatókönyvért. Az ügynökkel kapcsolatos részletes információkért tekintse meg az [Adatkezelési átjáró](data-factory-data-management-gateway.md) t.

Az Azure IaaS virtuális gépeken (VM-eken) üzemeltetett, az adatokat az Adatkezelési átjáró használatával is áthelyezheti/áthelyezheti az Azure IaaS virtuális gépeken (VM-eken) üzemeltetett támogatott adattárakba. Ebben az esetben telepítheti az adatkezelési átjáró ugyanazon a virtuális gépen, mint maga az adattár, vagy egy külön virtuális gép, amely hozzáfér az adattárhoz.

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és formátumok
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

> [!NOTE] 
> Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, használjon egy **egyéni tevékenységet** a Data Factoryban az adatok saját logika szerinti másolásához/áthelyezéséhez. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok
A Tevékenység másolása segítségével **fájlokat másolhat** két fájlalapú adattár között, és a bemeneti és kimeneti adatkészlet-definíciókformátum [szakaszát](data-factory-create-datasets.md) is kihagyhatja. Az adatok másolása hatékonyan, szerializálás/deszerializálás nélkül történik.

Copy Activity is olvas, és írja a fájlokat a megadott formátumok: **Szöveg, JSON, Avro, ORC, és Parketta**, és a tömörítés codec **GZip, Deflate, BZip2, és ZipDeflate** támogatottak. Lásd: [Támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) részletekkel.

A következő másolási tevékenységeket teheti meg például:

* Adatok másolása a helyszíni SQL Server kiszolgálón, és írjon az Azure Data Lake Store orc formátumban.
* Fájlok másolása szöveges (CSV) formátumban a helyszíni fájlrendszerből, és írjon az Azure Blob avro formátumban.
* A tömörített fájlokat a helyszíni fájlrendszerből másolja, majd bontsa ki a tömörítést, majd szálljon le az Azure Data Lake Store áruházba.
* Adatok másolása GZip tömörített szöveg (CSV) formátumban az Azure Blobból, és írjon az Azure SQL Database-be.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globálisan elérhető adatmozgás
Az Azure Data Factory csak az USA nyugati régiójában, az USA keleti régiójában és Észak-Európa régióiban érhető el. A másolási tevékenységet működtető szolgáltatás azonban globálisan elérhető a következő régiókban és földrajzi területeken. A globálisan elérhető topológia biztosítja a hatékony adatmozgást, amely általában elkerüli a régiók közötti ugrásokat. A Data Factory és az Adatmozgás egy régióban való elérhetőségét lásd: [Szolgáltatások régiónként.](https://azure.microsoft.com/regions/#services)

### <a name="copy-data-between-cloud-data-stores"></a>Adatok másolása felhőalapú adattárak között
Ha a forrás- és a fogadó adattárak a felhőben vannak, a Data Factory olyan szolgáltatásközponti telepítést használ a régióban, amely a legközelebb van a fogadóhoz ugyanazon a földrajzi területen az adatok áthelyezéséhez. A hozzárendeléseket a következő táblázatban tekintheti meg:

| A céladat-tárolók földrajzi elhelyezkedése | A céladattár régiója | Az adatmozgatáshoz használt terület |
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
| Dél-Korea | Dél-Korea középső régiója | Dél-Korea középső régiója |
| &nbsp; | Dél-Korea déli régiója | Dél-Korea középső régiója |

Azt is megteheti, hogy explicit módon jelzi a Data Factory szolgáltatás `executionLocation` nak a `typeProperties`másolás végrehajtásához használandó régióját a Tevékenység másolása csoporttulajdonságának megadásával. A tulajdonság támogatott értékei a fenti **Terület az adatmozgatásoszlopban** találhatók. Vegye figyelembe, hogy az adatok a másolás során a hálózaton keresztül haladnak át a hálózaton keresztül. Például a koreai Azure-áruházak közötti `"executionLocation": "Japan East"` másoláshoz megadhatja, hogy a Japán régión keresztül haladjon át (lásd [referenciaként a JSON-mintát).](#by-using-json-scripts)

> [!NOTE]
> Ha a céladattár régiója nem szerepel az előző listában, vagy nem észlelhető, alapértelmezés `executionLocation` szerint a másolási tevékenység sikertelen lesz, ahelyett, hogy egy másik régión menne keresztül, kivéve, ha meg van adva. A támogatott régiólista idővel ki lesz bontva.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Adatok másolása a helyszíni adattár és a felhőalapú adattár között
Amikor az adatok másolása a helyszíni (vagy Az Azure virtuális gépek/IaaS) és a felhőbeli tárolók között történik, [az Adatkezelési átjáró](data-factory-data-management-gateway.md) adatáthelyezést hajt végre a helyszíni gépen vagy virtuális gépen. Az adatok nem áramlik át a szolgáltatás a felhőben, kivéve, ha a [szakaszos másolási](data-factory-copy-activity-performance.md#staged-copy) képesség. Ebben az esetben az adatok az átmeneti Azure Blob-tárolón keresztül áramlanak, mielőtt az a fogadó adattárba kerülne.

## <a name="create-a-pipeline-with-copy-activity"></a>Folyamat létrehozása másolási tevékenységgel
A másolási tevékenységgel rendelkező folyamatot többféleképpen is létrehozhatja:

### <a name="by-using-the-copy-wizard"></a>A Másolás varázsló val
Az Adatgyári másolás varázsló segít a másolási tevékenységgel rendelkező folyamat létrehozásában. Ez a folyamat lehetővé teszi a támogatott forrásokból származó adatok másolását a célhelyekre anélkül, hogy *JSON-definíciókat írna* csatolt szolgáltatásokhoz, adatkészletekhez és folyamatokhoz. A varázslóval kapcsolatos részleteket a [Data Factory Copy wizard című varázslóban](data-factory-copy-wizard.md) találja.  

### <a name="by-using-json-scripts"></a>A JSON parancsfájlok használatával
A Data Factory Editor segítségével a Visual Studio, vagy az Azure PowerShell egy JSON-definíciót hozhat létre egy folyamathoz (a Másolási tevékenység használatával). Ezután üzembe helyezheti a folyamat létrehozásához a Data Factory ban. [Lásd: Másolási tevékenység használata egy Azure Data Factory-folyamat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy oktatóanyag lépésről-lépésre.    

JSON-tulajdonságok (például név, leírás, bemeneti és kimeneti táblák és házirendek) minden típusú tevékenységhez elérhetők. A tevékenység `typeProperties` szakaszában elérhető tulajdonságok tevékenységtípusonként eltérőek lehetnek.

A Másolási `typeProperties` tevékenység esetében a szakasz a források és a fogadók típusától függően változik. Kattintson egy forrásra/fogadóra a [Támogatott források és fogadók](#supported-data-stores-and-formats) szakaszban, ha megszeretné tudni, hogy milyen típusú tulajdonságokat támogat a Másolási tevékenység az adott adattárhoz.

Itt egy minta JSON meghatározás:

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
A kimeneti adatkészletben meghatározott ütemezés határozza meg, hogy a tevékenység mikor fut (például: **napi**, **napi**gyakoriság , és **1-es**intervallum). A tevékenység adatokat másol egy bemeneti adatkészletből (**forrás**) egy kimeneti adatkészletbe (**fogadó).**

A Tevékenység másolásához több bemeneti adatkészletet is megadhat. A tevékenység futtatása előtt ellenőrzik a függőségeket. A rendszer azonban csak az első adatkészlet adatait másolja a céladatkészletbe. További információ: [Ütemezés és végrehajtás.](data-factory-scheduling-and-execution.md)  

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [Másolási tevékenység teljesítmény-és hangolási útmutatót,](data-factory-copy-activity-performance.md)amely ismerteti azokat a kulcsfontosságú tényezőket, amelyek befolyásolják az adatok mozgatásának (másolási tevékenység) teljesítményét az Azure Data Factoryban. Azt is felsorolja a megfigyelt teljesítmény a belső tesztelés során, és ismerteti a különböző módon optimalizálja a másolási tevékenység teljesítményét.

## <a name="fault-tolerance"></a>Hibatűrés
Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és hibát ad vissza, ha nem kompatibilis adatokat tapasztal a forrás és a fogadó között; miközben explicit módon beállíthatja az inkompatibilis sorok kihagyását és naplózását, és csak a kompatibilis adatok másolásához másolja a másolást. További részletekért tekintse meg a [Másolási tevékenység hibatűrését.](data-factory-copy-activity-fault-tolerance.md)

## <a name="security-considerations"></a>Biztonsági szempontok
Tekintse meg a [biztonsági szempontokat,](data-factory-data-movement-security-considerations.md)amelyek ismertetik a biztonsági infrastruktúra, amely az adatok áthelyezési szolgáltatások az Azure Data Factory-ban az adatok védelmére.

## <a name="scheduling-and-sequential-copy"></a>Ütemezés és egymást követő másolat
[Az Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) című témakörben részletes információt talál arról, hogyan működik az ütemezés és a végrehajtás a Data Factory-ban. Lehetőség van több másolási művelet futtatására egymás után egymás után szekvenciális/rendezett módon. Lásd a [Másolás című szakaszt.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Típuskonverziók
A különböző adattárak különböző natív típusú rendszerekkel rendelkeznek. A Másolási tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertáljon natív forrástípusokból .NET típussá.
2. Konvertálás .NET típusból natív fogadótípussá.

A natív típusú rendszerből az adattár .NET típusára történő leképezés a megfelelő adattárcikkben található. (Kattintson az adott hivatkozásra a Támogatott adattárolók táblában). Ezekkel a leképezésekkel meghatározhatja a megfelelő típusokat a táblák létrehozása során, hogy a Másolási tevékenység a megfelelő konverziókat hajtsa végre.

## <a name="next-steps"></a>További lépések
* Ha többet szeretne megtudni a további másolási tevékenységről, olvassa el az Adatok másolása az [Azure Blob storage-ból az Azure SQL Database-be című témakört.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
* Ha többet szeretne tudni arról, hogy miként helyezheti át az adatokat egy helyszíni adattárból egy felhőalapú adattárba, olvassa el az [Adatok áthelyezése a helyszíni adattárakból című témakört.](data-factory-move-data-between-onprem-and-cloud.md)
