---
title: "Adatok másolása az Azure Data Factory használatával MongoDB |} Microsoft Docs"
description: "Útmutató: adatok másolása Mongo DB támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jingwang
ms.openlocfilehash: b12477b855dad28976989e694888667fd0cb6b20
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával MongoDB
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-on-premises-mongodb-connector.md)
> * [2. verzió – Előzetes verzió](connector-mongodb.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni a MongoDB-adatbázist. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [MongoDB-összekötőt a V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz másolhatja MongoDB-adatbázist. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a MongoDB összekötő támogatja:

- MongoDB **2.4, 2.6, 3.0 és 3.2**.
- Adatok másolása **alapvető** vagy **névtelen** hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása a MongoDB-adatbázist, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikk további részleteket. Integrációs futásidejű biztosít egy beépített MongoDB-illesztőprogramot, ezért nem, manuálisan kell telepítenie minden olyan illesztőprogram MongoDB-adatok másolásakor kell.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások megadhatók a MongoDB-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

MongoDB kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **mongodb-Protokolltámogatással** |Igen |
| kiszolgáló |Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét a mongodb-Protokolltámogatással. |Igen |
| port |A MongoDB-kiszolgálóhoz a kapcsolatok figyelésére használt TCP portot. |Nem (alapértelmezett érték 27017) |
| DatabaseName |A MongoDB-adatbázist, amely az elérni kívánt nevét. |Igen |
| authenticationType | A MongoDB-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Két érték engedélyezett: **alapvető**, és **névtelen**. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (Ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen (Ha alapszintű hitelesítést használ). |
| authSource |A MongoDB-adatbázist, amely a hitelesítő adatok kereséséhez használni kívánt nevét. |Nem. Az egyszerű hitelesítés alapértelmezés szerint a rendszer a rendszergazdai fiókot és a databaseName tulajdonsággal megadott adatbázis. |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a MongoDB dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása a MongoDB, az adatkészlet típus tulajdonságának beállítása **MongoDbCollection**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **MongoDbCollection** | Igen |
| CollectionName |A MongoDB-adatbázist a gyűjtemény nevét. |Igen |

**Példa**

```json
{
     "name":  "MongoDbDataset",
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

```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MongoDB forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB forrásaként

Adatok másolása a MongoDB, állítsa be a forrás típusa a másolási tevékenység **MongoDbSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **MongoDbSource** | Igen |
| lekérdezés |Az egyéni SQL-92 lekérdezés segítségével adatokat olvasni. Például: Válasszon * from tábla. |Nem (Ha a "collectionName" adatkészlet paraméter van megadva) |

**Példa**

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
> Ha meg az SQL-lekérdezést, vegye figyelembe a dátum és idő formátumban. Például:`SELECT * FROM Account WHERE LastModifiedDate >= {{ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')}'}} AND LastModifiedDate < {{ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}'}}`

## <a name="schema-by-data-factory"></a>Adat-előállító sémája

Az Azure Data Factory szolgáltatásnak kikövetkezteti a MongoDB-gyűjteményből séma használatával a **legújabb 100 dokumentumok** a gyűjteményben. A 100 dokumentumok teljes séma nem tartalmaznak, ha azokat az oszlopokat is figyelmen kívül hagyja a másolási művelet során.

## <a name="data-type-mapping-for-mongodb"></a>Adattípus-leképezés mongodb-protokolltámogatással

Amikor adatokat másol a MongoDB, a következő megfeleltetéseket szolgálnak a MongoDB adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| MongoDB-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| Bináris |Byte] |
| Logikai |Logikai |
| Dátum |DateTime |
| NumberDouble |Dupla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Karakterlánc |
| Karakterlánc |Karakterlánc |
| UUID |GUID |
| Objektum |Renormalized történő egybesimítására "_" beágyazott elválasztójelként oszlopok |

> [!NOTE]
> Virtuális táblák használata tömbök-támogatással kapcsolatos további tudnivalókért tekintse meg [támogatja az összetett típusok virtuális táblák használata](#support-for-complex-types-using-virtual-tables) szakasz.
>
> Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, Max percenkénti kulcs, reguláris kifejezés, szimbólum, Timestamp, meghatározatlan.

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális táblák használata összetett típusok támogatása

Az Azure Data Factory beépített ODBC-illesztőprogram használatával csatlakozhat, és másolja az adatokat a MongoDB-adatbázisból. A dokumentumok között különböző típusú tömb, vagy objektumok például összetett típusok az illesztőprogram újra normalizálja adatok megfelelő virtuális táblákba. Pontosabban Ha a tábla tartalmaz ilyen oszlopok, az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely tartalmazza a tényleges táblából, az összetett típusú oszlopok ugyanazokat az adatokat. Az alaptábla ugyanazt a nevet használja, amely valós táblázatként.
* A **virtuális tábla** minden összetett típusú oszlophoz, amely kiterjeszti a beágyazott adatok. A virtuális táblák neve a valódi tábla, "_" elválasztó és a nevét, a tömb vagy objektum nevével.

Virtuális táblák tekintse meg az adatok a valós táblázatban, az illesztőprogram, a nem normalizált adatok eléréséhez. A MongoDB-tömbök tartalmának lekérdezése, és a virtuális tábla érheti el.

### <a name="example"></a>Példa

Itt ExampleTable például minden cellában – számlákon objektumokból álló tömb egy oszlopot és egy oszlop formájú skaláris típusoknál – minősítések tömbje MongoDB tábla lesz.

| _id | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", cikk: "toaster", az ár: "456" kedvezményes: "0,2"}, {invoice_id: "124" elem: "helyezzük", az ár: "1235" kedvezményes: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", cikk: "kombinált hűtőszekrények", az ár: "12543" kedvezménnyel: "0,0"}] |Arany |[1,2] |

Az illesztőprogram az egyetlen tábla képviselő virtuális táblákat hoz létre. Az első virtuális táblát kell az alaptábla nevű "ExampleTable", a példában látható módon. Az alaptábla az eredeti tábla összes adatot tartalmaz, de az adatokat a tömbök kimaradt, és a virtuális táblázatokban ki van bontva.

| _id | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Arany |

Az alábbi táblázatok bemutatják a virtuális táblákat, amelyek megfelelnek a példában az eredeti tömbök. Ezek a táblázatok tartalmazzák a következő:

* Vissza az eredeti elsődleges kulcsként megadott oszlop (keresztül a _id. oszlop) eredeti tömb sorának megfelelő mutató hivatkozás
* Utalhat, hogy az adatok az eredeti tömbön belüli pozíciója
* A kibontott adatok belül a tömb egyes elemei

**"ExampleTable_Invoices". tábla:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Elem | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |a toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |kombinált hűtőszekrények |12543 |0.0 |

**"ExampleTable_Ratings". tábla:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).