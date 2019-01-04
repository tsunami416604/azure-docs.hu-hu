---
title: Adatok másolása az Azure Data Factory használatával MongoDB |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Mongo DB támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 228c5bf701b48a046b743db046587f530b223aec
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812066"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával MongoDB
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuális verzió](connector-mongodb.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy MongoDB-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

>[!IMPORTANT]
>ADF-kiadás új MongoDB-összekötőt hozhat létre, így jobb natív MongoDB támogatja, az ODBC-alapú megvalósítás összehasonlítása, tekintse meg [MongoDB-összekötővel](connector-mongodb.md) cikkben talál. Az örökölt MongoDB-összekötővel, támogatott adatért-van az előző verziókkal való kompatibilitási, az új munkaterheléshez, használja az új összekötőt.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok bármely támogatott fogadó adattárba másolhatja a MongoDB-adatbázist. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a MongoDB-összekötővel támogat:

- MongoDB **2.4, 2.6-os, 3.0, 3.2, 3.4-es és 3.6-os**.
- Másolja az adatokat az **alapszintű** vagy **névtelen** hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

Adatokat másol egy MongoDB-adatbázis, amely nem érhető el nyilvánosan, akkor be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) további részleteket a cikkben. Az integrációs modul egy beépített MongoDB-illesztőprogram biztosít, így nem kell manuálisan bármely illesztőprogram telepítéséhez, ha az adatok másolása a mongodb-hez.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a tulajdonságok, amelyek meghatározzák az adott Data Factory-entitások MongoDB-összekötővel kapcsolatos adatokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Mongodb-hez társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **Mongodb-hez** |Igen |
| kiszolgáló |IP-cím vagy a gazdagép neve a MongoDB-kiszolgáló. |Igen |
| port |A MongoDB-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem (alapértelmezés szerint a 27017) |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| authenticationType | A MongoDB-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Engedélyezett értékek a következők: **Alapszintű**, és **névtelen**. |Igen |
| felhasználónév |Felhasználói fiók MongoDB eléréséhez. |Igen (alapszintű hitelesítés használata esetén). |
| jelszó |A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen (alapszintű hitelesítés használata esetén). |
| authSource |A MongoDB-adatbázis, amely a hitelesítéshez a hitelesítő adatok ellenőrzésére használni kívánt nevét. |Nem. Az alapszintű hitelesítés alapértelmezés szerint a rendszer a rendszergazdai fiókkal és -databaseName tulajdonsággal megadott adatbázis használata. |
| enableSsl | Itt adhatja meg, e-kiszolgálóhoz a rendszer SSL használatával titkosítja. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy, hogy a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). MongoDB-adatkészlet a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **MongoDbCollection** | Igen |
| collectionName |MongoDB-adatbázisban szereplő gyűjtemény neve. |Igen |

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
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MongoDB-forráshoz által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>Mongodb-hez mint forrás

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **MongoDbSource** | Igen |
| lekérdezés |Az egyéni SQL-92 lekérdezés segítségével olvassa el az adatokat. Például: válassza ki * from tábla. |Nem (Ha a "collectionName" adatkészlet paraméter van megadva) |

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
> Amikor adja meg az SQL-lekérdezést, figyeljen oda arra, hogy a dátum és idő formátumban. Például: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` vagy paraméter használata `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Adat-előállítók által séma

Az Azure Data Factory szolgáltatás kikövetkezteti a sémát a MongoDB-gyűjtemény használatával a **legújabb 100 dokumentumok** a gyűjteményben. Ha 100 dokumentumok tartalmazza a teljes séma, néhány oszlop figyelmen kívül hagyhatja a másolási művelet során.

## <a name="data-type-mapping-for-mongodb"></a>Adattípus-leképezés a mongodb-hez

Az adatok másolása a mongodb-hez, amikor a következő hozzárendeléseket használhatók a MongoDB-adattípusok Azure Data Factory közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| MongoDB adatok típusa | Data factory közbenső adattípus |
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
| Objektum |Renormalized be simítja egybe az oszlopok, "_" beágyazott elválasztóként |

> [!NOTE]
> Virtuális táblák használata tömbök támogatása kapcsolatos további információkért tekintse meg [virtuális tábla használatával összetett típusok támogatása](#support-for-complex-types-using-virtual-tables) szakaszban.
>
> Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, maximális és minimális key, reguláris kifejezés, szimbólum, Timestamp, nincs megadva.

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális tábla használatával összetett típusok támogatása

Az Azure Data Factory kapcsolódni, és adatokat másol a MongoDB-adatbázis egy beépített ODBC-illesztőt használja. A dokumentumok között különböző típusú tömbök vagy objektumok például komplex típusok esetében az illesztőprogram újra normalizálja adatok megfelelő virtuális táblákba. Pontosabban a tábla tartalmaz ilyen oszlopokhoz, ha az illesztőprogram állít elő, a következő virtuális táblák:

* A **alaptábla**, amely ugyanazokat az adatokat, mint a valódi tábla kivételével a komplex típusú oszlopokat tartalmaz. Az alaptábla ugyanazt a nevet használja, mint a valódi tábla, amely azt jelöli.
* A **virtuális tábla** minden komplex típusú oszlophoz, amely kibővíti a beágyazott adatok. A virtuális táblák elnevezése a valódi tábla, "_" elválasztó és a tömb vagy objektum nevét, a név használatával.

Tekintse meg az adatok denormalizált adatokat az illesztőprogram, a valós táblázatban virtuális táblákat. A tartalom a MongoDB-tömbök elérheti, lekérdezés és a virtuális táblázatok összekapcsolása.

### <a name="example"></a>Példa

Például ExampleTable itt egy MongoDB-tábla, amely rendelkezik egy oszlop minden cella – a számlákon, az objektumok egy tömbjét és a egy oszlopot a skaláris típusok – minősítési tömbjét.

| _azonosítója | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123"-elem: "a toaster", ár: "456" kedvezményt: "0.2-es"}, {invoice_id: "124"-elem: "helyezzük", ár: "1235", kedvezmény: "0.2-es"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", cikk: "fridge", ár: "12543" kedvezményt: "0.0"}] |Arany |[1,2] |

Az illesztőprogram hoz létre több virtuális táblákat, amelyek ebben a táblázatban szerepelnek. Az első virtuális tábla az alaptábla nevű, "ExampleTable", a példában látható. Az alaptábla tartalmaz az eredeti tábla összes adatot, de a rendszer kihagyta a tömbök származó adatokat, és ki van bontva, a virtuális táblák.

| _azonosítója | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Arany |

Az alábbi táblázatok bemutatják a virtuális táblákat, amelyek a példában az eredeti tömböket. Ezek a táblázatok a következőket tartalmazzák:

* Egy hivatkozást a sor az eredeti tömb (keresztül a _azonosítója. oszlop) megfelelő eredeti elsődleges kulcs oszlopa
* Arra utalhat, hogy az adatok az eredeti tömbön belüli pozíciója
* A kibontott adatok belül a tömb egyes elemei

**"ExampleTable_Invoices". tábla:**

| _azonosítója | ExampleTable_Invoices_dim1_idx | invoice_id | Elem | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |a toaster |456 |0.2 |
| 1111 |1 |124 |Helyezzük |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

**"ExampleTable_Ratings". tábla:**

| _azonosítója | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
