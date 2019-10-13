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
ms.openlocfilehash: 0dcca2175d6ccc35a51bccb1e47f75d25cb8b11f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299185"
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
* Összesítő függvények
  * min., max., átlag, darabszám
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

* LEMEZTERÜLET létrehozása (a parancs replikációs beállításai figyelmen kívül lesznek hagyva, a rendszer a mögöttes [Azure Cosmos db replikációs modelljét](global-dist-under-the-hood.md)használja. Ha több régióra kiterjedő adatmennyiségre van szüksége, a PowerShell, a CLI vagy a portál használatával is engedélyezheti a fiók szintjén, ha további információt szeretne megtudni, [hogyan adhat hozzá vagy távolíthat el régiókat a fiókhoz](how-to-manage-database-account.md#addremove-regions-from-your-database-account) .
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* HÍR 
* BATCH – csak nem naplózott parancsok használata támogatott 
* DELETE

Az CQLV4-kompatibilis SDK-n keresztül végrehajtott összes szifiliszi művelet további információkat ad vissza a hibáról, a kért egységeket. A törlési és frissítési parancsokat az erőforrás-szabályozással kell kezelni, figyelembe véve a kiépített átviteli sebesség megfelelő használatát. 
* Ügyeljen arra, hogy a gc_grace_seconds értékének nullának kell lennie, ha meg van adva.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Konzisztencialeképezés 

Az Azure Cosmos DB Cassandra API konzisztenciaválasztási lehetőséget kínál az olvasási műveletekhez.  A konzisztencia-megfeleltetés részletes [itt [(https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Engedély- és szerepkörkezelés

A Azure Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) a kiépítés, a kulcsok elforgatása, a metrikák, valamint az írási és olvasási és a csak olvasható jelszavak/kulcsok megtekintésére, amelyek a [Azure Portal](https://portal.azure.com)keresztül szerezhetők be. A Azure Cosmos DB nem támogatja a szifilisz-tevékenységek szerepkörét. 

## <a name="keyspace-and-table-options"></a>A térköz és a tábla beállításai

A rendszer figyelmen kívül hagyja a terület neve, az osztály, a replication_factor, az adatközpont a Create Space parancsban lehetőséget. A rendszer az alapul szolgáló Azure Cosmos DB [globális eloszlását](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) használja, ha szükséges régiókat ad hozzá. Ha több régióra kiterjedő adatmennyiségre van szüksége, a PowerShell-lel, a CLI-vel vagy a portálon engedélyezheti, ha többet szeretne megtudni, tekintse meg a következő doc: https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#addremove-regions-from-your-database-account. A Durable_writes nem lehet letiltani – Cosmos DB biztosítja, hogy minden írás tartós legyen. Minden régióban Cosmos DB replikálja a 4 replikából álló REPLICASET, és ez a REPLICASET- [konfiguráció](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) nem módosítható. Az összes tábla-létrehozási beállítást figyelmen kívül hagyja a rendszer, kivéve a gc_grace_seconds, amelynek nullának kell lennie.
A térköz és a tábla extra Option-cosmosdb_provisioned_throughput rendelkezik, amelynek minimális értéke 400. A lemezterület-átviteli sebesség lehetővé teszi a több táblázat közötti adatátvitelt, és olyan forgatókönyvek esetén hasznos, amikor az összes tábla nem használja az átviteli sebességet. Az ALTER TABLE lehetővé teszi a kiépített átviteli sebesség módosítását a régiók között. HOZZon létre egy sampleks a következővel: REPLICATION = {"class": "SimpleStrategy"} és cosmosdb_provisioned_throughput = 2000;  
CREATE TABLE sampleks. T1 (user_id int elsődleges kulcs, LastName Text) és cosmosdb_provisioned_throughput = 2000; ALTER TABLE gks1. T1, cosmosdb_provisioned_throughput = 10000;

## <a name="usage-of-cassandra-retry-connection-policy"></a>Cassandra újrapróbálkozás kapcsolatok házirendjének használata

Azure Cosmos DB az erőforrás-szabályozású rendszer. Ez azt jelenti, hogy bizonyos számú műveletet végrehajthat egy adott másodpercben, amelyet a kiosztott átviteli sebesség a műveletek által felhasznált kérelmek egységei alapján korlátoz. Ha az alkalmazás túllépi ezt a korlátot, a rendszer eldönti a kivételek korlátozását. A Cosmos db Cassandra API lefordítja ezeket a kivételeket a Cassandra Native protokollon túlterhelt hibák esetén. Annak biztosítása érdekében, hogy az alkalmazás képes legyen feltartóztatni, és az újrapróbálkozási sebesség korlátozása a [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) és a [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) Helper megadásával. Ha más SDK-kat használ a Cosmos DB Cassandra API eléréséhez, hozzon létre kapcsolati házirendet a kivételek lekéréséhez. 

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával

