---
title: Az Azure Cosmos DB Cassandra API-ja által támogatott Apache Cassandra-funkciók
description: További tudnivalók az Apache Cassandra-funkciók támogatásáról az Azure Cosmos DB Cassandra API-ban
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 223544f7ceddce6bc2071d561da1cff1c0d4b53b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420159"
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
* Aggregátumfüggvények
  * min, max, átlag, darabszám
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
  


## <a name="cassandra-api-limits"></a>A Cassandra API korlátai

Az Azure Cosmos DB Cassandra API nem rendelkezik semmilyen korláttal a táblában tárolt adatok méretére vonatkozóan. Több száz terabájtnyi vagy petabájtnyi adat tárolható, ha biztosítva van a partíciókulcs korlátainak betartása. Hasonlóképpen, minden entitás vagy sormegfelelő nem korlátozza az oszlopok számát. Az entitás teljes mérete azonban nem haladhatja meg a 2 MB-ot. A partíciókulcsonkénti adatok nem haladhatják meg a 20 GB-ot, mint az összes többi API-ban.

## <a name="tools"></a>Eszközök 

Az Azure Cosmos DB Cassandra API egy felügyelt szolgáltatási platform. A fürt kezeléséhez nincs szükség semmilyen felügyeleti többletterhelésre vagy segédprogramra, mint a Garbage Collector, Java Virtual Machine (JVM), sem csomóponti eszközre. Támogatja az olyan eszközöket, mint a cqlsh, amely bináris CQLv4-kompatibilitást használ. 

* Az Azure Portal adatkezelője, metrikái, naplódiagnosztikája, PowerShell és CLI más támogatott mechanizmusok a fiók kezeléséhez.

## <a name="cql-shell"></a>CQL-rendszerhéj  

A CQLSH parancssori segédprogram apache cassandra 3.1.1-es típussal érkezik, és néhány környezeti változó beállításával kivan a programból.

**Windows:**

Windows használata esetén javasoljuk, hogy engedélyezze a [Windows fájlrendszert Linuxra](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Ezután kövesse az alábbi linux parancsokat.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL-parancsok

Az Azure Cosmos DB a következő adatbázisparancsokat támogatja a Cassandra API-fiókok esetében.

* KEYSPACE LÉTREHOZÁSA (A parancs replikációs beállításait a program figyelmen kívül hagyja)
* CREATE TABLE 
* CREATE INDEX (indexnév megadása nélkül, és a teljes fagyasztott indexek még nem támogatottak)
* SZŰRÉS ENGEDÉLYEZÉSE
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH – csak nem naplózott parancsok használata támogatott 
* DELETE

Minden CRUD-művelet, amely egy CQL v4-kompatibilis SDK-n keresztül hajtható végre, további információkat ad vissza a hiba- és kérési egységekről. A DELETE és UPDATE parancsokat az erőforrás-szabályozás figyelembe véve kell kezelni, a kiosztott átviteli rendszer leghatékonyabb kihasználásának biztosítása érdekében.

* Ügyeljen arra, hogy a gc_grace_seconds értékének nullának kell lennie, ha meg van adva.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Konzisztencialeképezés 

Az Azure Cosmos DB Cassandra API konzisztenciaválasztási lehetőséget kínál az olvasási műveletekhez.  A konzisztencia-leképezésitt [található.](consistency-levels-across-apis.md#cassandra-mapping)

## <a name="permission-and-role-management"></a>Engedély- és szerepkörkezelés

Az Azure Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) az [Azure Portalon](https://portal.azure.com)keresztül beszerezhető, a kulcsok üzembe állításához, a metrikák megtekintéséhez, valamint az írási és írási és írásvédett jelszavakhoz/kulcsokhoz. Az Azure Cosmos DB nem támogatja a CRUD-tevékenységek szerepkörök.

## <a name="keyspace-and-table-options"></a>A kulcstér és a táblázat beállításai

A "Kulcstér létrehozása" parancsban a régiónév, osztály, replication_factor és adatközpont beállításait a program jelenleg figyelmen kívül hagyja. A rendszer az alapul szolgáló Azure Cosmos DB [globális terjesztési](global-dist-under-the-hood.md) replikációs módszerét használja a régiók hozzáadásához. Ha szüksége van az adatok régiók közötti jelenlétére, engedélyezheti azt a powershell, a CLI vagy a portál fiókszintjén, ha többet szeretne megtudni, olvassa el a [régiók hozzáadása](how-to-manage-database-account.md#addremove-regions-from-your-database-account) cikket. Durable_writes nem tiltható le, mert az Azure Cosmos DB biztosítja, hogy minden írási tartós legyen. Az Azure Cosmos DB minden régióban replikálja az adatokat a replikakészlet, amely négy replikák, és ez a replika készlet [konfiguráció](global-dist-under-the-hood.md) nem módosítható.
 
A tábla létrehozásakor a rendszer figyelmen kívül hagyja az összes beállítást, kivéve a gc_grace_seconds, amelyet nullára kell állítani.
A Keyspace és a tábla egy "cosmosdb_provisioned_throughput" nevű extra beállítással rendelkezik, amelynek minimális értéke 400 RU/s. A Keyspace átviteli valószínűleg lehetővé teszi az átviteli szint megosztását több tábla között, és olyan esetekben hasznos, amikor az összes tábla nem a kiosztott átviteli feszültséget használ. Az Alter Table parancs lehetővé teszi a kiosztott átviteli hang módosítása a régiók között. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>A Cassandra csatlakozási újrapróbálkozásra vonatkozó szabályzatának használata

Az Azure Cosmos DB egy erőforrás által szabályozott rendszer. Ez azt jelenti, hogy egy adott másodpercben bizonyos számú műveletet ellehet végezni a műveletek által felhasznált kérelemegységek alapján. Ha egy alkalmazás egy adott másodpercben meghaladja ezt a korlátot, a kérelmek sebességkorlátozottak, és kivételek jelennek meg. A Cassandra API az Azure Cosmos DB fordítja ezeket a kivételeket a túlterhelt hibák a Cassandra natív protokoll. Annak érdekében, hogy az alkalmazás képes elfogni, és újra kérelmek esetén sebességkorlátozás, a [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) és a [Java-bővítmények](https://github.com/Azure/azure-cosmos-cassandra-extensions) vannak biztosítva. Ha más SDK-k használatával érhető el cassandra API az Azure Cosmos DB, hozzon létre egy kapcsolati szabályzatot, hogy újra ezeket a kivételeket.

## <a name="next-steps"></a>További lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával

