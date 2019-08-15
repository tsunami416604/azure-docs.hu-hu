---
title: Adatok másolása a MongoDB a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatmásolási tevékenységet a Mongo DB-ből a Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 77d0f632c763651004efa46edf027719040f4760
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967488"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása a MongoDB a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuális verzió](connector-mongodb.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok MongoDB-adatbázisból történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

>[!IMPORTANT]
>Az ADF kibocsát egy új MongoDB-összekötőt, amely jobb natív MongoDB-támogatást biztosít az ehhez az ODBC-alapú implementációhoz képest, a részletekért lásd a [MongoDB](connector-mongodb.md) -összekötőt ismertető cikket. Ez az örökölt MongoDB-összekötő továbbra is támogatott a visszafelé compability, míg az új munkaterhelések esetében az új összekötőt kell használnia.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB-adatbázisból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez a MongoDB-összekötő a következőket támogatja:

- MongoDB- **verziók 2,4, 2,6, 3,0, 3,2, 3,4 és 3,6**.
- Adatok másolása az alapszintű vagy a **Névtelen** hitelesítés használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A Integration Runtime egy beépített MongoDB-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie az illesztőprogramokat az adatok MongoDB történő másolásakor.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az MongoDB-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A Type tulajdonságot a következőre kell beállítani: **MongoDb** |Igen |
| server |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |A MongoDB-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem (az alapértelmezett érték 27017) |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| authenticationType | A MongoDB-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők:Alapszintű és **Névtelen**. |Igen |
| username |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha alapszintű hitelesítést használ). |
| password |A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen (ha alapszintű hitelesítést használ). |
| authSource |Annak a MongoDB-adatbázisnak a neve, amelyet a hitelesítés hitelesítő adatainak ellenőrzéséhez használni kíván. |Nem. Az alapszintű hitelesítéshez az alapértelmezett érték a databaseName tulajdonsággal megadott rendszergazdai fiók és adatbázis használata. |
| enableSsl | Itt adhatja meg, e-kiszolgálóhoz a rendszer SSL használatával titkosítja. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy, hogy a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). A MongoDB adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MongoDbCollection** | Igen |
| collectionName |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

**Példa:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MongoDB forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB forrásként

A következő tulajdonságok támogatottak a másolási tevékenység **source** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MongoDbSource** | Igen |
| query |Az egyéni SQL-92 lekérdezés használatával olvashatja el az adatolvasást. Például: select * from MyTable. |Nem (ha meg van adva a "collectionName" az adatkészletben) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Az SQL-lekérdezés megadásakor figyeljen a DateTime formátumra. Például: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` vagy a paraméter használata`SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Adat-előállítók által séma

Azure Data Factory a szolgáltatás egy MongoDB-gyűjtemény sémáját a gyűjteményben a **legújabb 100-dokumentumok** használatával. Ha ezek a 100-dokumentumok nem tartalmaznak teljes sémát, egyes oszlopok figyelmen kívül hagyhatók a másolási művelet során.

## <a name="data-type-mapping-for-mongodb"></a>Adattípusok leképezése MongoDB

Az adatok MongoDB-ből való másolása során a rendszer a következő leképezéseket használja a MongoDB adattípusok között az ideiglenes adattípusok Azure Data Factory. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| MongoDB adattípusa | Data factory közbenső adattípus |
|:--- |:--- |
| Binary |Byte[] |
| Logikai |Logikai |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sztring |
| String |Sztring |
| UUID |Guid |
| Object |Normalizálva a "_" oszlopokkal beágyazott elválasztóként |

> [!NOTE]
> Ha többet szeretne megtudni a virtuális táblákat használó tömbök támogatásáról, tekintse meg az [összetett típusok támogatása virtuális táblák használatával](#support-for-complex-types-using-virtual-tables) című szakaszt.
>
> Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, max/min kulcs, reguláris kifejezés, szimbólum, időbélyeg, nincs meghatározva.

## <a name="support-for-complex-types-using-virtual-tables"></a>Összetett típusok támogatása virtuális táblák használatával

A Azure Data Factory egy beépített ODBC-illesztővel csatlakozik a MongoDB-adatbázishoz, és másolja azokat. Az olyan összetett típusok esetében, mint például a tömbök vagy különböző típusú objektumok a dokumentumokban, az illesztőprogram újranormalizálja az adatmennyiséget a megfelelő virtuális táblákba. Pontosabban, ha egy tábla ilyen oszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokkal rendelkezik, mint a valós tábla, kivéve a komplex típusú oszlopokat. Az alaptábla ugyanazt a nevet használja, mint az azt jelképező valódi tábla.
* Az egyes komplex típusú oszlopokhoz tartozó **virtuális táblázat** , amely kibővíti a beágyazott adattípusokat. A virtuális táblák neve a valódi tábla, a "_" elválasztója és a tömb vagy objektum neve alapján történik.

A virtuális táblák a valós táblázatba tartozó, az illesztőprogramnak a denormalizált információhoz való hozzáférésének engedélyezésére vonatkoznak. A MongoDB-tömbök tartalmát a virtuális táblák lekérdezésével és a hozzájuk való csatlakozással érheti el.

### <a name="example"></a>Példa

Például a ExampleTable itt egy olyan MongoDB-táblázat, amely egyetlen oszloppal rendelkezik, és az egyes cellákban található objektumok tömbje, valamint egy skaláris típusú tömbvel rendelkező oszlop – értékelések.

| _id | Ügyfél neve | Számlák | Szolgáltatásszint | Értékelés |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", elem: "kenyérpirító", Ár: "456", kedvezmény: "0.2"}, {invoice_id: "124", elem: "sütő", Ár: "1235", kedvezmény: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", elem: "hűtőszekrény", Ár: "12543", kedvezmény: "0,0"}] |Arany |[1,2] |

Az illesztőprogram több virtuális táblát fog előállítani, hogy ezt az egyetlen táblát képviseljék. Az első virtuális tábla a "ExampleTable" nevű alaptábla, amely a példában látható. Az alaptábla az eredeti tábla összes adatát tartalmazza, de a tömbökből származó adatok ki lettek hagyva, és ki lettek bontva a virtuális táblákban.

| _id | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Arany |

A következő táblázatok a példában szereplő eredeti tömböket képviselő virtuális táblákat mutatják be. Ezek a táblák a következőket tartalmazzák:

* Az eredeti tömb soraihoz tartozó eredeti elsődleges kulcs oszlopra való hivatkozás (a _id oszlopon keresztül)
* Az eredeti tömbben lévő adatok pozíciójának jelzése
* A tömbben lévő egyes elemek kibontott adathalmaza

**Tábla "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | ár | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |kenyérpirító |456 |0.2 |
| 1111 |1 |124 |sütő |1235 |0.2 |
| 2222 |0 |135 |hűtőszekrény |12543 |0.0 |

**Tábla "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
