---
title: Az Azure Cosmos DB Cassandra API-ja által támogatott Apache Cassandra-funkciók és -parancsok
description: További tudnivalók az Apache Cassandra-funkciók támogatásáról az Azure Cosmos DB Cassandra API-ban
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: a68d290bc21a8cdc36d5c85f770752ad67a4e91e
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330757"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API-ja által támogatott Apache Cassandra-funkciók 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB Cassandra API-val a Cassandra Query Language (CQL) 4-es verziójú [vezetékes protokollal](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) kompatibilis, nyílt forráskódú Cassandra ügyfél [illesztőprogramjaival](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) kommunikálhat. 

Az Azure Cosmos DB Cassandra API használatával kihasználhatja az Apache Cassandra API-k előnyeit, valamint az Azure Cosmos DB által biztosított vállalati képességeket. A vállalati képességek magukban foglalják a [globális terjesztést](distribute-data-globally.md), az [automatikus horizontális felskálázás particionálását](partition-data.md), rendelkezésre állási és késési garanciát, az inaktív adatok titkosítását, a biztonsági mentéseket és még sok más.

## <a name="cassandra-protocol"></a>Cassandra protokoll 

Az Azure Cosmos DB Cassandra API a CQL **4-es verziójával** kompatibilis. A támogatott CQL-parancsok, -eszközök, -korlátozások és -kivételeket alább láthatók. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia az Azure Cosmos DB Cassandra API-hoz.

## <a name="cassandra-driver"></a>Cassandra-illesztőprogram

Az Azure Cosmos DB Cassandra API a következő Cassandra-illesztőprogramverziókat támogatja:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL-adattípusok 

Az Azure Cosmos DB Cassandra API a következő CQL-adattípusokat támogatja:

* ascii  
* bigint  
* blob  
* logikai  
* számláló  
* dátum  
* tizedes tört  
* double  
* lebegőpontos  
* kimerevítve  
* inet  
* int  
* lista  
* halmaz  
* smallint  
* szöveg  
* time  
* időbélyeg  
* timeuuid  
* tinyint  
* rekord  
* uuid  
* varchar  
* varint  
* rekordok  
* udts  
* térkép  

## <a name="cql-functions"></a>CQL-függvények

Az Azure Cosmos DB Cassandra API a következő CQL-függvényeket támogatja:

* Jogkivonat  
* Blob-konverziós függvények 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID és timeuuid függvények 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>A Cassandra lekérdezési nyelv korlátai

Az Azure Cosmos DB Cassandra API nem rendelkezik semmilyen korláttal a táblában tárolt adatok méretére vonatkozóan. Több száz terabájtnyi vagy petabájtnyi adat tárolható, ha biztosítva van a partíciókulcs korlátainak betartása. Hasonló módon az entitások vagy az egyenértékű sorok szintén nem rendelkeznek semmilyen korláttal az oszlopok számát illetően, azonban az entitás teljes mérete nem lehet nagyobb 2 MB-nál.

## <a name="tools"></a>Eszközök 

Az Azure Cosmos DB Cassandra API egy felügyelt szolgáltatási platform. A fürt kezeléséhez nincs szükség semmilyen felügyeleti többletterhelésre vagy segédprogramra, mint a Garbage Collector, Java Virtual Machine (JVM), sem csomóponti eszközre. Támogatja az olyan eszközöket, mint a cqlsh, amely bináris CQLv4-kompatibilitást használ. 

* A fiók kezelésének további támogatott eszközei az Azure Portal adatkezelője, metrikái, naplódiagnosztikája, a PowerShell és a cli.

## <a name="cql-shell"></a>CQL-rendszerhéj  

CQLSH parancssori segédprogram tartalmazza az Apache Cassandra 3.1.1 verzióját, és a következő engedélyezett környezeti változókkal azonnal használható:

Az alábbi parancsok futtatása előtt [vegyen fel egy Baltimore-gyökértanúsítványt a cacerts tárolóba](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>CQL-parancsok

Az Azure Cosmos DB a következő adatbázisparancsokat támogatja a Cassandra API-fiókok esetében.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH – csak nem naplózott parancsok használata támogatott 
* DELETE

A CQLV4-kompatibilis SDK használatával végrehajtott összes crud művelet további információval szolgál a hibáról, a felhasznált kérelemegységekről és tevékenységazonosítóról. A kiépített erőforrások túlhasználatának elkerüléséhez a törlési és frissítési parancsokat az erőforrások irányítását figyelembe véve kell kezelni. 
* Ügyeljen arra, hogy a gc_grace_seconds értékének nullának kell lennie, ha meg van adva.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Konzisztencialeképezés 

Az Azure Cosmos DB Cassandra API konzisztenciaválasztási lehetőséget kínál az olvasási műveletekhez. A fiók konzisztenciájától függetlenül az összes írási művelet végrehajtása mindig az írási műveletekre vonatkozó SLA szerint történik.

## <a name="permission-and-role-management"></a>Engedély- és szerepkörkezelés

Az Azure Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), illetve az írható-olvasható és a csak olvasható jelszavakat/kulcsokat, amelyeket az [Azure Portalon](https://portal.azure.com) lehet beszerezni. Az Azure Cosmos DB az adatsík-tevékenységek esetében még nem támogatja a felhasználókat és a szerepköröket. 

## <a name="planned-support"></a>Tervezett támogatás 
* Az időbélyeg és a TTL együttes használata  
* A régió nevét a create keyspace parancsban a rendszer figyelmen kívül hagyja – Az adatok elosztása az alapul szolgáló Cosmos DB platformon valósul meg, és a portálon vagy a fiókhoz tartozó PowerShellen keresztül történik a közzététel. 





## <a name="next-steps"></a>További lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával

