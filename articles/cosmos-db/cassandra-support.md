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
ms.openlocfilehash: 0a2ace3f73379cff0b9289a8cebb10cb7930348d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129081"
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
  


## <a name="cassandra-api-limits"></a>A Cassandra API korlátai

Az Azure Cosmos DB Cassandra API nem rendelkezik semmilyen korláttal a táblában tárolt adatok méretére vonatkozóan. Több száz terabájtnyi vagy petabájtnyi adat tárolható, ha biztosítva van a partíciókulcs korlátainak betartása. Hasonlóképpen, minden entitás vagy sor megfelelője nem korlátozza az oszlopok számát. Az entitás teljes mérete azonban nem haladhatja meg a 2 MB-ot. Az adat/partíciós kulcs nem haladhatja meg a 20 GB-ot az összes többi API-ban.

## <a name="tools"></a>Eszközök 

Az Azure Cosmos DB Cassandra API egy felügyelt szolgáltatási platform. A fürt kezeléséhez nincs szükség semmilyen felügyeleti többletterhelésre vagy segédprogramra, mint a Garbage Collector, Java Virtual Machine (JVM), sem csomóponti eszközre. Támogatja az olyan eszközöket, mint a cqlsh, amely bináris CQLv4-kompatibilitást használ. 

* A fiók kezeléséhez a Azure Portal adatkezelője, mérőszámai, naplózási diagnosztikája, PowerShell és CLI más támogatott mechanizmusok.

## <a name="cql-shell"></a>CQL-rendszerhéj  

A CQLSH parancssori segédprogram az Apache Cassandra 3.1.1-es verzióval rendelkezik, és néhány környezeti változó beállításával működik.

**Windows:**

Ha Windows rendszert használ, javasoljuk, hogy engedélyezze a [Linux rendszerhez készült Windows-fájlrendszert](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Ezután követheti az alábbi Linux-parancsokat.

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
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
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

* LEMEZTERÜLET létrehozása (a parancs replikációs beállításai figyelmen kívül lesznek hagyva)
* CREATE TABLE 
* INDEX létrehozása (az index nevének megadása nélkül, és a teljes befagyasztott indexek még nem támogatottak)
* SZŰRÉS ENGEDÉLYEZÉSE
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH – csak nem naplózott parancsok használata támogatott 
* DELETE

A CQL v4-kompatibilis SDK-n keresztül végrehajtott összes szifilisz-művelet további információkat ad vissza a hibákról és a kért egységekről. A TÖRLÉSi és frissítési parancsokat a kiépített átviteli sebesség leghatékonyabb kihasználásának biztosítása érdekében figyelembe kell venni az erőforrás-szabályozással.

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

Az Azure Cosmos DB Cassandra API konzisztenciaválasztási lehetőséget kínál az olvasási műveletekhez.  [Itt](consistency-levels-across-apis.md#cassandra-mapping)részletesen ismertetjük a konzisztencia-hozzárendelést.

## <a name="permission-and-role-management"></a>Engedély- és szerepkörkezelés

A Azure Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) a kiépítés, a kulcsok elforgatása, a metrikák, valamint az írási és olvasási és a csak olvasható jelszavak/kulcsok megtekintésére, amelyek a [Azure Portal](https://portal.azure.com)keresztül szerezhetők be. A Azure Cosmos DB nem támogatja a szifilisz-tevékenységek szerepkörét.

## <a name="keyspace-and-table-options"></a>A térköz és a tábla beállításai

Jelenleg a rendszer figyelmen kívül hagyja a régió neve, osztály, replication_factor és Datacenter beállításait a "Create Space" parancsban. A rendszer az alapul szolgáló Azure Cosmos DB [globális terjesztési](global-dist-under-the-hood.md) replikációs módszerét használja a régiók hozzáadásához. Ha szüksége van a régiók közötti adatmennyiségre, a PowerShell, a CLI vagy a portál használatával engedélyezheti a fiók szintjén a további tudnivalókat lásd: [régiók hozzáadása](how-to-manage-database-account.md#addremove-regions-from-your-database-account) cikk. Durable_writes nem lehet letiltani, mert Azure Cosmos DB gondoskodik az összes írás tartós állapotáról. Azure Cosmos DB minden régióban replikálja az adathalmazt, amely négy replikából áll, és ez a replikakészlet- [konfiguráció](global-dist-under-the-hood.md) nem módosítható.
 
A rendszer a tábla létrehozásakor figyelmen kívül hagyja az összes beállítást, kivéve a gc_grace_secondst, amelyet nullára kell beállítani.
A szóköz és a tábla "cosmosdb_provisioned_throughput" nevű extra lehetőséggel rendelkezik, amelynek minimális értéke 400 RU/s. A térköz átviteli sebessége lehetővé teszi a több tábla közötti adatforgalom megosztását, és olyan helyzetekben hasznos, amikor az összes tábla nem használja a kiépített átviteli sebességet. Az ALTER TABLE parancs lehetővé teszi a kiépített átviteli sebesség módosítását a régiók között. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>A Cassandra csatlakozási újrapróbálkozásra vonatkozó szabályzatának használata

A Azure Cosmos DB erőforrás-szabályozású rendszer. Ez azt jelenti, hogy egy adott másodpercben bizonyos számú műveletet végrehajthat a műveletek által felhasznált kérelmek egységei alapján. Ha egy alkalmazás túllépi ezt a korlátot egy adott másodpercben, a kérések száma korlátozott, és a rendszer a kivételeket is eldönti. A Azure Cosmos DB Cassandra API lefordítja ezeket a kivételeket a Cassandra Native protokollon túlterhelt hibák esetén. Annak biztosítása érdekében, hogy az alkalmazás képes legyen feltartóztatni és újrapróbálkozni a kérelmeket az eseti korlátozás, a [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) és a [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -bővítmények megadásával. Ha más SDK-kat használ a Azure Cosmos DB Cassandra APIhoz való hozzáféréshez, hozzon létre egy kapcsolati szabályzatot, amely újrapróbálkozik ezekkel a kivételekkel.

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával

