---
title: Adatok áthelyezése a másolási tevékenység használatával |} Microsoft Docs
description: 'További tudnivalók az adat-előállító adatcsatornák adatmozgás: adatáttelepítés felhő tárolja, valamint egy helyi tárolóban és a felhőben levő tárolójának között. Használja a másolási tevékenység.'
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
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054258"
---
# <a name="move-data-by-using-copy-activity"></a>Adatok áthelyezése a másolási tevékenység használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-data-movement-activities.md)
> * [(Az aktuális verzió) 2-es verzió](../copy-activity-overview.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [másolási tevékenység során a V2](../copy-activity-overview.md).

## <a name="overview"></a>Áttekintés
Az Azure Data Factoryben, az másolási tevékenység követve másolja át a helyszíni és a felhő közötti adattárolókhoz. Miután a rendszer átmásolja az adatokat, azt kell tovább át legyenek-e és elemzése. Használhatja a másolási tevékenység közzététele átalakítását és az elemzés eredményeinek az üzleti intelligenciával és alkalmazás-fogyasztás.

![Másolási tevékenység szerepe](media/data-factory-data-movement-activities/copy-activity.png)

Másolási tevékenység működteti a biztonságos, megbízható, méretezhető, és [világszerte elérhető szolgáltatás](#global). Ez a cikk részletesen az adatátvitelt jelölik a Data Factory és a másolási tevékenység.

Első lépésként nézzük meg az adatok áttelepítési módját két felhő adattárolók között, valamint egy helyszíni adattároló és a felhő-tárolóban.

> [!NOTE]
> Általában kapcsolatos további tudnivalókért lásd: [ismertetése folyamatok és a tevékenységek](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Másolja az adatokat két felhő adattárolók között
Ha a forrás- és fogadó adattárolókhoz a felhőben, a másolási tevékenység végighalad a következő szakaszokra adatok másolása a forráskiszolgálóról a fogadó. A szolgáltatás, amely a másolási tevékenység megoldásaira épül:

1. Olvassa be az adatokat a forrás-tárolót.
2. Szerializálással/deszerializálással, tömörítés/kibontás, oszlopleképezés hajt végre, és az adattípus átalakítása. Ezek a konfigurációk a bemeneti adatkészletet, a kimeneti adatkészlet és a másolási tevékenység alapuló műveleteket végez el.
3. A cél-tárolót írja az adatokat.

A szolgáltatás automatikusan kiválasztja azt a optimális régió az adatátvitel végrehajtásához. Ebben a régióban egyike általában a legközelebbi fogadó adattárba.

![Felhő-felhőbe történő másolás](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Egy helyszíni adattároló és felhőalapú adattároló közötti adatok másolása
Egy helyszíni adattároló és felhőalapú adattároló közötti biztonságos áthelyezni az adatokat, telepítse az adatkezelési átjáró a helyi számítógépen. Az adatkezelési átjáró olyan ügynök, amely lehetővé teszi a hibrid adatmozgatást és feldolgozását. Telepítheti az ugyanazon a számítógépen, az adatok tárolásához saját magát vagy egy külön számítógépen, amely hozzáfér az adattárolóhoz.

Ebben a forgatókönyvben az adatkezelési átjáró a szerializálással/deszerializálással, tömörítés/kibontás, oszlopleképezés hajt végre, és adattípus átalakítása. Nem adatfolyam az Azure Data Factory szolgáltatáson keresztül. Ehelyett az adatkezelési átjáró közvetlenül a adatokat ír az céltár.

![A helyszíni-felhő másolása](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Lásd: [adatok áthelyezése között a helyszíni és felhőalapú adattároló](data-factory-move-data-between-onprem-and-cloud.md) bemutatása és forgatókönyv. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) az ügynök kapcsolatos részletes információkat.

Át is helyezheti az adatkezelési átjáró használatával Azure IaaS virtuális gépek (VM) tárolt adatokat a/támogatott adattárolókhoz. Ebben az esetben telepíthető az adatkezelési átjáró ugyanazon virtuális gép az adattároló magát, vagy egy külön virtuális gépre, amely hozzáfér az adattárolóhoz.

## <a name="supported-data-stores-and-formats"></a>Támogatott adatokat tárolja, és formázza
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

> [!NOTE] 
> Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, használjon egy **egyéni tevékenységet** a Data Factoryban az adatok saját logika szerinti másolásához/áthelyezéséhez. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok
Használhatja a másolási tevékenység **másolja a fájlokat-van** között két fájl alapú adattárolókhoz, kihagyhatja a [szakasz formázása](data-factory-create-datasets.md) mind a bemeneti és kimeneti adatkészlet-definíciók. Az adatokat a szerializálással/deszerializálással nélkül hatékonyan másolja.

Másolási tevékenység során is beolvassa és megadott formátumú fájlok ír: **szöveg, JSON, Avro, ORC és Parquet**, és a tömörítési kodek **GZip, Deflate, BZip2 és ZipDeflate** támogatottak. Lásd: [fájl- és tömörítési formátum támogatott](data-factory-supported-file-and-compression-formats.md) adatokkal.

Például a következő másolási tevékenység teheti meg:

* Másolja az adatokat a helyszíni SQL Server, és az Azure Data Lake Store írási ORC formátumban.
* Szöveges (CSV) formátumú fájlok másolását a helyszíni fájlrendszer, és az Avro formátum az Azure Blob írni.
* Tömörített fájlok másolását a helyszíni fájlrendszer, és kibontani majd föld Azure Data Lake Store.
* Adatok másolása GZip tömörített szöveges (CSV) formátumú az Azure Blob, és az Azure SQL Database írni.

## <a name="global"></a>Globálisan elérhető adatátvitel
Az Azure Data Factory csak az USA keleti régiója, Észak-Európa, és az USA nyugati régiója, régiók érhető el. Azonban a szolgáltatást, amely a rendszert működtet a másolási tevékenység nem áll rendelkezésre globálisan a következő régiók és földrajzi. A globálisan elérhető topológia hatékony adatmozgás, amely általában a kereszt-régió ugrások nem biztosítja. Lásd: [régiói](https://azure.microsoft.com/regions/#services) a Data Factory és az adatátvitelt jelölik a régióban rendelkezésre állását.

### <a name="copy-data-between-cloud-data-stores"></a>Adatok másolása felhő adattárolók között
Ha a forrás- és fogadó adattárolókhoz a felhőben, adat-előállító szolgáltatástelepítésben a régiót, amelyben ugyanazon a földrajzi hely, az adatokat a fogadó legközelebbi használja. A hozzárendeléseket a következő táblázatban tekintheti meg:

| Az adatok céltárolót geográfiai | A cél adattár régió | Adatátvitel használt terület |
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

Azt is megteheti, explicit módon adhatja meg a Data Factory szolgáltatásnak a Másolás megadásával végrehajtásához használt területe `executionLocation` tulajdonság a másolási tevékenység `typeProperties`. Ez a tulajdonság támogatott értékek fent felsorolt **régió használt adatmozgás** oszlop. Vegye figyelembe, hogy az adatok végighalad az adott régióban a hálózaton keresztül másolása során. Például, hogy közötti másolás Azure tárolja, koreai, megadhatja `"executionLocation": "Japan East"` a japán régió keresztül (lásd: [JSON minta](#by-using-json-scripts) hivatkozásként).

> [!NOTE]
> Ha a cél adattár régió nincs előző listában, vagy ismeretlen, alapértelmezés szerint másolási tevékenység nem sikerül egy másik régióban tett lépéseket megkerülve kivéve, ha `executionLocation` van megadva. A támogatott régióban lista kibontásra váró adott idő alatt.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Egy helyszíni adattároló és felhőalapú adattároló közötti adatok másolása
Amikor adatokat a helyszíni (vagy Azure virtuális gépek/IaaS) közötti másolással és felhőalapú tárolók, [az adatkezelési átjáró](data-factory-data-management-gateway.md) végez adatátvitelt jelölik a helyi számítógépen vagy virtuális gép. Az adatok nem keresztül folyik a szolgáltatás felhőalapú, ha nem használ a [másolási előkészített](data-factory-copy-activity-performance.md#staged-copy) funkció. Ebben az esetben adatáramlás keresztül az átmeneti Azure Blob storage a fogadó adattárba írás előtt.

## <a name="create-a-pipeline-with-copy-activity"></a>Hozzon létre egy folyamatot másolási tevékenység
A másolási tevékenység több módon is létrehozhat folyamat:

### <a name="by-using-the-copy-wizard"></a>A varázsló segítségével
A Data Factory másolása varázsló segítségével hozzon létre egy folyamatot másolási tevékenység. Ez az adatcsatorna teszi lehetővé adatok támogatott forrásokból másolása a célok *JSON írása nélkül* társított szolgáltatások, adathalmazok és adatcsatornák definícióit. Lásd: [Data Factory másolása varázsló](data-factory-copy-wizard.md) a varázsló vonatkozó további információért.  

### <a name="by-using-json-scripts"></a>JSON-parancsfájlok használatával
Az Azure-portálon, a Visual Studio vagy az Azure PowerShell Data Factory Editor segítségével hozzon létre egy folyamatot JSON definícióját (másolási tevékenység). Ezután telepítheti azt a folyamat létrehozása a Data Factory. Lásd: [oktatóanyag: másolási tevékenység során használja az Azure Data Factory-feldolgozási folyamat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) oktatóanyagért részletes utasításokat tartalmaz.    

JSON-tulajdonságok (például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek) minden típusú tevékenységek érhetők el. A rendelkezésre álló tulajdonságok a `typeProperties` szakasz a tevékenység tevékenységek minden típusának függenek.

A másolási tevékenység során a `typeProperties` szakasz források típusától függően változik, és a fogadók esetében. Kattintson a forrás/fogadó a a [támogatott források és mosdók](#supported-data-stores-and-formats) szakaszban olvashat a másolási tevékenység támogatja az adott adattároló típusú tulajdonságokhoz.

Íme egy minta JSON-definícióból:

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
A kimeneti adatkészlet definiált ütemezés határozza meg, amikor a tevékenység futtatása (például: **napi**, a frekvenciát **nap**, és intervallumhoz **1**). A tevékenység adatokat másol egy bemeneti adatkészlet (**forrás**) kimeneti adatkészlethez (**fogadó**).

A másolási tevékenység több bemeneti adatkészletet is megadhat. A tevékenység futtatása előtt győződjön meg arról a függőségek használhatók. Azonban csak az első adatkészlet adatainak másolódik a céladatkészletből. További információkért lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [másolási tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md), amely ismerteti az Azure Data Factory (másolási tevékenység) adatátvitel teljesítményét befolyásoló tényezők. Felsorolja a belső tesztelése során a megfigyelt teljesítmény és a másolási tevékenység teljesítményének optimalizálásához különböző módját tárgyalja.

## <a name="fault-tolerance"></a>Hibatűrés
Alapértelmezés szerint a másolási tevékenység során leáll másolja az adatokat, és térjen vissza a hiba nem kompatibilis adatok között a forrás és a fogadó; Ha előforduló miközben kihagyásához és a naplófájl a nem kompatibilis sorok és a csak másolatot explicit módon konfigurálhatja ezeket kompatibilis adatokba, így a másolás sikeresen befejeződött. Tekintse meg a [másolási tevékenység hibatűrést](data-factory-copy-activity-fault-tolerance.md) a további részletek.

## <a name="security-considerations"></a>Biztonsági szempontok
Tekintse meg a [biztonsági szempontok](data-factory-data-movement-security-considerations.md), amely ismerteti, hogy a biztonsági infrastruktúra, amely adatátviteli szolgáltatások az Azure Data Factory használatával az adatok védelme.

## <a name="scheduling-and-sequential-copy"></a>Ütemezési és soros másolása
Lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) adat-előállítóban ütemezés és a végrehajtási működésével kapcsolatos részletes információk. Akkor is futtathatók több másolási műveletek egymás után szekvenciális/rendezett módon. Tekintse meg a [egymás után másolja](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szakasz.

## <a name="type-conversions"></a>Típuskonverziók
Különböző adattárolókhoz natív típusa különböző rendszerek rendelkezik. Másolási tevékenység az eseményforrás-típusnak a következő kétlépéses módszert típusok gyűjtése automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típus.
2. .NET-típus konvertálása a natív a fogadó típusa.

A .NET-típus egy adattároló natív típusa rendszerről leképezése nem a megfelelő adatokat tároló cikkben. (Az adott hivatkozásra a [adattárolókhoz támogatott](#supported-data-stores) tábla). A leképezések segítségével határozza meg a megfelelő típusú a táblák létrehozása során, hogy a másolási tevékenység során hajtja végre a megfelelő átalakításra.

## <a name="next-steps"></a>További lépések
* A másolási tevékenység több, lásd: [adatok másolása az Azure Blob Storage tárolóban az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Adatok áthelyezése egy helyszíni adattároló felhőalapú adattároló kapcsolatos további tudnivalókért lásd: [tárolt adatok mozgatása a helyszíni adattárolókhoz felhőbe](data-factory-move-data-between-onprem-and-cloud.md).
