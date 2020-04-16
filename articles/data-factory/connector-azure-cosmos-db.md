---
title: Adatok másolása és átalakítása az Azure Cosmos DB -ben (SQL API)
description: Megtudhatja, hogyan másolhat adatokat az Azure Cosmos DB -ba (SQL API), és hogyan alakíthatja át az adatokat az Azure Cosmos DB (SQL API) rendszerben a Data Factory használatával.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415385"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure Cosmos DB (SQL API) szolgáltatásban az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használhatja az Azure Data Factory másolási tevékenységét, amellyel adatokat másolhat az Azure Cosmos DB-be (SQL API) és onnan ki, és hogyan használhatja az Adatfolyamot az adatok átalakítására az Azure Cosmos DB-ben (SQL API). Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.



>[!NOTE]
>Ez az összekötő csak a Cosmos DB SQL API-t támogatja. A MongoDB API-t az [Azure Cosmos DB MongoDB API-jának összekötője című](connector-azure-cosmos-db-mongodb-api.md)dokumentumban tájékformálják. Más API-típusok most már nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A másolási tevékenység, ez az Azure Cosmos DB (SQL API) összekötő támogatja:

- Adatok másolása az Azure Cosmos DB SQL API-ból és az Azure Cosmos DB [SQL API-ba.](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)
- Írjon az Azure Cosmos **DB-nek beszúrásként** vagy **upsert**ként.
- JSON-dokumentumok importálása és exportálása adott állapotban, illetve adatok másolása táblázatos adatkészletből vagy táblázatos adatkészletbe. Ilyen például egy SQL-adatbázis és egy CSV-fájl. A Dokumentumok JSON-fájlokba vagy más Azure Cosmos DB-gyűjteménybe vagy más Azure Cosmos DB-gyűjteményből történő másolásához olvassa el [a JSON-dokumentumok importálása és exportálása.](#import-and-export-json-documents)

A Data Factory integrálja az [Azure Cosmos DB tömeges végrehajtó könyvtárát,](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) hogy a legjobb teljesítményt nyújtsa az Azure Cosmos DB-be való íráskor.

> [!TIP]
> Az [adatáttelepítési videó](https://youtu.be/5-SRNiC_qOU) végigvezeti az Azure Blob storage-ból az Azure Cosmos DB-be történő adatok másolásának lépéseit. A videó azt is ismerteti, teljesítmény-hangolási szempontok az adatok azure Cosmos DB általában betöltése.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Cosmos DB (SQL API) specifikus Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi tulajdonságok támogatottak az Azure Cosmos DB (SQL API) csatolt szolgáltatás:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **CosmosDb (Típus tulajdonság)** tulajdonságnak CosmosDb -re kell állítania. | Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure Cosmos DB adatbázishoz való csatlakozáshoz szükséges adatokat.<br />**Megjegyzés:** Meg kell adnia az adatbázis adatait a kapcsolati karakterláncban, ahogy az a következő példákban látható. <br/> Fiókkulcsot is helyezhet el az Azure `accountKey` Key Vaultban, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure-integrációs runtime lesz használva. |Nem |

**Példa**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiókkulcs tárolása az Azure Key Vaultban**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md)

Az alábbi tulajdonságok támogatottak az Azure Cosmos DB (SQL API) adatkészletben: 

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **CosmosDbSqlApiCollection (CosmosDbSqlApiCollection)** tulajdonságra kell állítani. |Igen |
| Lekérdezés_neve |Az Azure Cosmos DB-dokumentumgyűjtemény neve. |Igen |

Ha a "DocumentDbCollection" típusú adatkészletet használja, továbbra is támogatott, a másolási és a kereshető tevékenység visszamenőleges kompatibilitása esetén, az adatfolyam nem támogatott. Javasoljuk, hogy az új modellt használja a jövőben.

**Példa**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz az Azure Cosmos DB (SQL API) forrás- és fogadótámogatása által nyújtott tulajdonságok listáját tartalmazza. A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md)

### <a name="azure-cosmos-db-sql-api-as-source"></a>Az Azure Cosmos DB (SQL API) forrásként

Ha adatokat szeretne másolni az Azure Cosmos DB-ből (SQL API), állítsa be a **forrástípust** a **DocumentDbCollectionSource**másolása mezőben. 

A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságát** **CosmosDbSqlApiSource (CosmosDbSqlApiSource)** típusú tulajdonságra kell állítani. |Igen |
| lekérdezés |Adja meg az Azure Cosmos DB-lekérdezést az adatok olvasásához.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, a rendszer végrehajtja ezt az SQL utasítást:`select <columns defined in structure> from mycollection` |
| előnyben részesített régiók | A Cosmos DB-ből származó adatok beolvasásakor az előnyben részesített régiók listája. | Nem |
| oldalMéret | A lekérdezés eredményének oldalankénti dokumentumszáma. Az alapértelmezett érték a "-1", ami azt jelenti, hogy a szolgáltatási oldali dinamikus oldalméretet 1000-ig használja. | Nem |

Ha a "DocumentDbCollectionSource" típusú forrást használja, az továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy használja az új modell tágítja a cosmos DB-ből származó adatok másolása gazdagabb lehetőségeket.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Amikor adatokat másol a Cosmos DB-ből, hacsak nem szeretné [a JSON-dokumentumokat úgy exportálni, ahogy van,](#import-and-export-json-documents)az ajánlott eljárás a másolási tevékenység leképezésének megadása. A Data Factory tiszteletben tartja a tevékenységben megadott leképezést – ha egy sor nem tartalmaz értéket egy oszlophoz, akkor null érték lesz megadva az oszlop értékéhez. Ha nem ad meg leképezést, a Data Factory az adatok első sorának használatával következtet a sémából. Ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányozni fog a tevékenységművelet eredményében.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Az Azure Cosmos DB (SQL API) fogadóként

Az Adatok másolásához az Azure Cosmos DB (SQL API) szolgáltatásba állítsa a **fogadó** típusát a **DocumentDbCollectionSink**másolása mezőben. 

A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Copy Activity sink **típustulajdonságának** **CosmosDbSqlApiSink (CosmosDbSqlApiSink)** típusú tulajdonságát kell állítani. |Igen |
| writeBehavior (írási viselkedés) |Az Azure Cosmos DB-re való adatok írása. Megengedett értékek: **insert** és **upsert**.<br/><br/>A **upsert** viselkedése az, hogy lecseréli a dokumentumot, ha már létezik azonos azonosítóval rendelkező dokumentum; ellenkező esetben szúrja be a dokumentumot.<br /><br />**Megjegyzés:** A Data Factory automatikusan létrehoz egy azonosítót egy dokumentumhoz, ha az azonosító nincs megadva sem az eredeti dokumentumban, sem oszlopleképezésben. Ez azt jelenti, hogy biztosítania kell, hogy a **upsert** a várt módon működjön, a dokumentum azonosítóval rendelkezik. |Nem<br />(az alapértelmezett beállítás **beszúrása**) |
| writeBatchSize | A Data Factory az [Azure Cosmos DB tömeges végrehajtó könyvtárát](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) használja az Azure Cosmos DB-nek való adatíráshoz. A **writeBatchSize** tulajdonság határozza meg az ADF által a tárnak biztosított dokumentumok méretét. Megpróbálhatja növelni a **writeBatchSize** értékét a teljesítmény javítása és az érték csökkentése érdekében, ha a dokumentum mérete nagy - lásd alább a tippeket. |Nem<br />(az alapértelmezett **érték 10 000**) |
| disableMetricsCollection | A Data Factory olyan metrikákat gyűjt, mint például a Cosmos DB rus-ok a másolási teljesítmény optimalizálásához és a javaslatokhoz. Ha ez a viselkedés aggasztja, adja meg `true` a kikapcsolására. | Nem (az `false`alapértelmezett ) |

>[!TIP]
>A JSON-dokumentumok importjának a következőképpen történő importálásához tekintse meg [a JSON-dokumentumok importálása vagy exportálása](#import-and-export-json-documents) című szakaszt; táblázatos alakú adatokból másoláshoz olvassa el az [Áttelepítés relációs adatbázisból a Cosmos DB-ba](#migrate-from-relational-database-to-cosmos-db)című dokumentumban.

>[!TIP]
>Cosmos DB korlátozza az egyetlen kérelem méretét 2 MB-ra. A képlet a Kérelem mérete = Egy dokumentum mérete * Írási kötegméret. Ha **a "Kérelem mérete túl nagy"** hibaüzenet jelenik meg, csökkentse a ** `writeBatchSize` ** copy sink konfigurációjának értékét.

Ha a "DocumentDbCollectionSink" típusú forrást használja, az továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy használja az új modell tágítja a cosmos DB-ből származó adatok másolása gazdagabb lehetőségeket.

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Sémaleképezés

Ha adatokat szeretne másolni az Azure Cosmos DB-ből táblázatos fogadóba vagy fordítottba, olvassa el [a séma-leképezést.](copy-activity-schema-and-type-mapping.md#schema-mapping)

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Adatok átalakítása során az adatfolyam, olvashatja és írhatja a gyűjtemények a Cosmos DB. További információ: a [forrásátalakítása](data-flow-source.md) és a [fogadó átalakítása](data-flow-sink.md) az adatfolyamatok leképezésében.

### <a name="source-transformation"></a>Forrás átalakítása

Az Azure Cosmos DB-re vonatkozó beállítások a forrásátalakítás **Forrásbeállítások** lapján érhetők el. 

**Rendszeroszlopok belefoglalása:** Ha ```id```igaz, ```_ts```a , és más rendszeroszlopok is szerepelni fognak a CosmosDB adatfolyam-metaadataiban. A gyűjtemények frissítésekor fontos, hogy ezt is tartalmazza, hogy megragadhassa a meglévő sorazonosítót.

**Oldalméret:** A lekérdezés eredményének oldalankénti dokumentumszáma. Az alapértelmezett érték a "-1", amely a szolgáltatás dinamikus oldalát használja 1000-ig.

**Átviteli átmenő:** Állítson be egy opcionális értéket a CosmosDB-gyűjteményre alkalmazni kívánt felhasználói jogkezelők számára az olvasási művelet során az adatfolyam minden egyes végrehajtásához. A minimum 400.

**Előnyben részesített régiók:** Válassza ki a folyamat előnyben részesített olvasási régióit.

#### <a name="json-settings"></a>JSON-beállítások

**Egyetlen dokumentum:** Akkor válassza ezt a lehetőséget, ha az ADF a teljes fájlt egyetlen JSON-dokumentumként szeretné kezelni.

**Nem jegyzett oszlopnevek:** Akkor válassza ezt a lehetőséget, ha a JSON-ban nincsenek idézve oszlopnevek.

**Megjegyzések:** Akkor használja ezt a beállítást, ha a JSON-dokumentumokban megjegyzések vannak az adatokhoz.

**Egyetlen idézett:** Ezt akkor kell kiválasztani, ha a dokumentum oszlopait és értékeit idézőjelbe teszi a dokumentum.

**A fordított perjel megszökött:** Ha fordított perjellel kerüli el a JSON karaktereit, válassza ezt a lehetőséget.

### <a name="sink-transformation"></a>Mosogató átalakítása

Az Azure Cosmos DB-re vonatkozó beállítások a fogadó **átalakításának Beállítások** lapján érhetők el.

**Frissítési módszer:** Azt határozza meg, hogy milyen műveletek engedélyezettek az adatbázis célállomásán. Az alapértelmezett beállítás az, hogy csak a beszúrásokat engedélyezi. A sorok frissítéséhez, upsert-jéhez vagy törléséhez egy alter-row átalakításra van szükség a műveletek sorainak címkézéséhez. A frissítések, upserts és törlések, egy kulcs oszlop vagy oszlopok kell beállítani, hogy melyik sort kell módosítani.

**Beszedési művelet:** Azt határozza meg, hogy a célgyűjteményt újra létre kell-e hozni az írás előtt.
* Nincs: A gyűjtemény nem történik meg.
* Újra létrehozás: A gyűjtemény eldobva lesz, és újra létre

**Kötegméret**: Azt szabályozza, hogy hány sor kerül írásra az egyes gyűjtőkben. A nagyobb kötegméretek javítják a tömörítést és a memóriaoptimalizálást, de az adatok gyorsítótárazásakor kiszorítják a memóriából kitett kivételeket.

**Partíciókulcs:** Adjon meg egy karakterláncot, amely a gyűjtemény partíciókulcsát jelöli. Például: ```/movies/title```

**Átviteli átmenő:** Állítson be egy opcionális értéket a CosmosDB-gyűjteményre alkalmazni kívánt felhasználói jogkezelők számához az adatfolyam minden egyes végrehajtásához. A minimum 400.

**Átviteli költségvetés írása:** Egy egész szám, amely a tömeges betöltési spark-feladathoz lefoglalni kívánt rus-ok számát jelöli. Ez a szám kivan a gyűjteményhez rendelt teljes átviteli mennyiségből.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="import-and-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Az Azure Cosmos DB (SQL API) összekötő segítségével egyszerűen:

* Dokumentumok másolása két Azure Cosmos DB-gyűjtemény között, ahogy van.
* Importálja a különböző forrásokból származó JSON-dokumentumokat az Azure Cosmos DB-ba, többek között az Azure Blob storage-ból, az Azure Data Lake Store-ból és az Azure Data Factory által támogatott egyéb fájlalapú áruházakból.
* JSON-dokumentumokat exportálhat egy Azure Cosmos DB-gyűjteményből különböző fájlalapú tárolókba.

Séma-agnosztikus másolat elérése:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás jsonfájlokba vagy a Cosmos DB gyűjteménybe lehetőséget.**
* Ha tevékenységszerzői tevékenységet használ, válassza a JSON formátumot a megfelelő fájltárolóval a forráshoz vagy a fogadóhoz.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Áttelepítés relációs adatbázisból cosmos DB-re

Amikor egy relációs adatbázisból, például az SQL Server az Azure Cosmos DB- re migrál, a másolási tevékenység könnyen leképezheti a táblázatos adatokat a forrásból a JSON-dokumentumok összeolvasztására a Cosmos DB-ben. Bizonyos esetekben előfordulhat, hogy szeretné újratervezni az adatmodellt, hogy optimalizálja azt a NoSQL-használati esetekben az [Azure Cosmos DB data modellezése](../cosmos-db/modeling-data.md)szerint, például az adatok denormalizálásához az összes kapcsolódó alelem beágyazásával egy JSON-dokumentumban. Ebben az esetben tekintse meg [ezt a cikket](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) egy útmutatót, hogyan érheti el az Azure Data Factory másolási tevékenység használatával.

## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
