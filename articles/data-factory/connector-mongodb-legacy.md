---
title: Adatok másolása a MongoDB-ból az örökölt
description: Megtudhatja, hogyan másolhatja az adatokat a Mongo DB-ből a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 31588f9657a883e9c4a5600d2d164125a5f68edf
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991943"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása a MongoDB-ból az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuális verzió](connector-mongodb.md)

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a MongoDB-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!IMPORTANT]
>Az ADF egy új MongoDB-csatlakozót ad ki, amely jobb natív MongoDB-támogatást biztosít ehhez az ODBC-alapú megvalósításhoz képest, olvassa el a [MongoDB összekötő](connector-mongodb.md) részletes cikkét. Ez az örökölt MongoDB-összekötő a visszamenőleges kompatibilitás érdekében támogatott marad, míg az új munkaterhelések esetén használja az új összekötőt.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez a MongoDB csatlakozó támogatja:

- MongoDB **verziók 2.4, 2.6, 3.0, 3.2, 3.4 és 3.6**.
- Adatok másolása **alapszintű** vagy **névtelen** hitelesítéssel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Az integrációs futásidő beépített MongoDB illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot sem a MongoDB-ból történő másoláskor.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a MongoDB-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A MongoDB csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságnak a következőre kell állítania: **MongoDb** |Igen |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |TCP-port, amelyet a MongoDB-kiszolgáló az ügyfélkapcsolatok figyelésére használ. |Nem (az alapértelmezett érték 27017) |
| adatbázisneve |Az elérni kívánt MongoDB-adatbázis neve. |Igen |
| authenticationType | A MongoDB adatbázishoz való csatlakozáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alap**szintű és **Névtelen**. |Igen |
| felhasználónév |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha egyszerű hitelesítést használ). |
| jelszó |A felhasználó jelszava. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen (ha egyszerű hitelesítést használ). |
| authForrás |A hitelesítő adatok hitelesítéséhez használni kívánt MongoDB-adatbázis neve. |Nem. Az alapfokú hitelesítéshez az alapértelmezett beállítás a rendszergazdai fiók és az databaseName tulajdonsággal megadott adatbázis használata. |
| enableSsl | Itt adható meg, hogy a kiszolgálóval létesített kapcsolatok titkosítva legyenek-e a TLS használatával. Az alapértelmezett érték a hamis.  | Nem |
| allowSelfSignedServerCert | Itt adható meg, hogy engedélyezze-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) A MongoDB adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **MongoDbCollection** | Igen |
| Lekérdezés_neve |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a MongoDB-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB mint forrás

A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **MongoDbSource** | Igen |
| lekérdezés |Az adatok olvasásához használja az egyéni SQL-92 lekérdezést. Például: válassza a * lehetőséget a MyTable táblából. |Nem (ha az adatkészletben a "collectionName" van megadva) |

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

## <a name="schema-by-data-factory"></a>Séma adatgyár szerint

Az Azure Data Factory szolgáltatás a gyűjtemény **legújabb 100 dokumentumának** használatával a MongoDB-gyűjteményséből következtet. Ha ez a 100 dokumentum nem tartalmaz teljes sémát, előfordulhat, hogy egyes oszlopok figyelmen kívül hagynak a másolási művelet során.

## <a name="data-type-mapping-for-mongodb"></a>Adattípus-hozzárendelés a MongoDB-hoz

A MongoDB-ból történő adatok másolásakor a következő leképezések kerülnek a MongoDB-adattípusokból az Azure Data Factory köztes adattípusaira. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| MongoDB adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| Bináris |Bájt[] |
| Logikai |Logikai |
| Dátum |DateTime |
| SzámDupla |Double |
| Számint |Int32 |
| Számhosszú |Int64 |
| ObjectID |Sztring |
| Sztring |Sztring |
| Uuid |Guid |
| Objektum |Újranormalizálva összeolvasztó oszlopokká, "_" elválasztóként |

> [!NOTE]
> A virtuális táblákat használó tömbök támogatásáról a [Virtuális táblákat használó összetett típusok támogatása](#support-for-complex-types-using-virtual-tables) című szakaszban olvashat.
>
> Jelenleg a következő MongoDB adattípusok nem támogatottak: DBPointer, JavaScript, Max/Min kulcs, Reguláris kifejezés, Szimbólum, Időbélyeg, Nem definiált.

## <a name="support-for-complex-types-using-virtual-tables"></a>Virtuális táblákat használó összetett típusok támogatása

Az Azure Data Factory egy beépített ODBC-illesztőprogramot használ a MongoDB-adatbázishoz való csatlakozáshoz és az adatok másolásához. Összetett típusok, például tömbök vagy különböző típusú objektumok esetén az illesztőprogram újra normalizálja az adatokat a megfelelő virtuális táblákká. Ha egy tábla ilyen oszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokat tartalmazza, mint a valós tábla, kivéve az összetett típusoszlopokat. Az alaptábla ugyanazt a nevet használja, mint az általa képviselt valódi tábla.
* Virtuális **tábla** minden egyes összetett típusoszlophoz, amely kibővíti a beágyazott adatokat. A virtuális táblák elnevezése a valódi tábla, az elválasztó "_" és a tömb vagy objektum nevével van elnevezve.

A virtuális táblák a valós táblában lévő adatokra hivatkoznak, így az illesztőprogram hozzáférhet a denormalizált adatokhoz. A MongoDB tömbök tartalmát a virtuális táblák lekérdezésével és az azokhoz való csatlakozással érheti el.

### <a name="example"></a>Példa

Például, ExampleTable itt egy MongoDB tábla, amely egy oszlop egy tömb objektumok minden cellában - Számlák, és egy oszlop egy tömb skaláris típusok - Értékelések.

| _id | Vevő neve | Számlák | Szolgáltatási szint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"kenyérpirító", ár:"456", kedvezmény:"0.2"}, {invoice_id:"124", tétel:"sütő", ár: "1235", kedvezmény: "0.2"}] |Ezüst |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", elem:"hűtőszekrény", ár: "12543", kedvezmény: "0.0"}] |Arany |[1,2] |

Az illesztőprogram több virtuális táblát hozna létre, amelyek ezt az egyetlen táblát képviselik. Az első virtuális tábla a példában látható "ExampleTable" nevű alaptábla. Az alaptábla tartalmazza az eredeti tábla összes adatát, de a tömbökből származó adatok kimaradtak, és a virtuális táblákban kivannak bontva.

| _id | Vevő neve | Szolgáltatási szint |
| --- | --- | --- |
| 1111 |ABC |Ezüst |
| 2222 |XYZ |Arany |

A következő táblák a példában az eredeti tömböket reprezentatot képviselő virtuális táblákat jelenítik meg. Ezek a táblázatok a következőket tartalmazzák:

* Hivatkozás vissza az eredeti elsődleges kulcsoszlopra, amely az eredeti tömb sorának felel meg (a _id oszlopon keresztül)
* Az adatok eredeti tömbön belüli pozíciójának jelzése
* A tömb egyes elemeinek kibontott adatai

**"ExampleTable_Invoices" táblázat:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Kenyérpirító |456 |0,2 |
| 1111 |1 |124 |Sütő |1235 |0,2 |
| 2222 |0 |135 |Hűtőszekrény |12543 |0,0 |

**"ExampleTable_Ratings" táblázat:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
