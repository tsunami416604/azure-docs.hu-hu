---
title: Helyi fejlesztés az Azure Cosmos Emulatorral
description: Az Azure Cosmos Emulator használatával ingyenesen, Azure-előfizetés létrehozása nélkül fejlesztheti és tesztelheti alkalmazását.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: ea4abada259c929f387b1477c127824ac6269319
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939171"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Az Azure Cosmos Emulator használata helyi fejlesztéshez és teszteléshez

Az Azure Cosmos Emulator egy helyi környezetet biztosít, amely a Azure Cosmos DB szolgáltatás fejlesztésére szolgál. Az Azure Cosmos Emulator használatával helyileg fejlesztheti és tesztelheti alkalmazását anélkül, hogy Azure-előfizetést hozna létre, vagy bármilyen költséget kellene fizetnie. Ha meggyőződött arról, hogy az alkalmazás hogyan működik az Azure Cosmos Emulatorban, átválthat egy Azure Cosmos-fiók használatára a felhőben.

Az Azure Cosmos Emulator használatával az [SQL](local-emulator.md#sql-api), a [Cassandra](local-emulator.md#cassandra-api), a [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), a [Gremlin](local-emulator.md#gremlin-api)és a [Table](local-emulator.md#table-api) API-fiókokat is kifejlesztheti. Ebben az időszakban azonban az emulátor Adatkezelő nézete teljes mértékben támogatja az ügyfeleket csak az SQL API-hoz. 

## <a name="how-the-emulator-works"></a>Az emulátor működése

Az Azure Cosmos Emulator a Azure Cosmos DB szolgáltatás kiváló minőségű emulációját biztosítja. Támogatja az Azure Cosmos DB azonos funkcionalitását, beleértve az adatlétrehozási és-lekérdezési támogatást, a tárolók kiosztását és méretezését, valamint a tárolt eljárások és triggerek végrehajtását. Az Azure Cosmos Emulator használatával fejlesztheti és tesztelheti az alkalmazásokat, és globális skálázással üzembe helyezheti őket az Azure-ban azáltal, hogy csak egyetlen konfigurációs módosítást végez a Azure Cosmos DB kapcsolódási végpontján.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor implementálása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az Azure-infrastruktúrára, például a globális replikálásra támaszkodó funkciók, az olvasási/írási műveletek egyszámjegyű ezredmásodperces késése és a hangolt konzisztencia-szintek nem alkalmazhatók.

Az Azure Cosmos Emulator és a Azure Cosmos DB szolgáltatás között az [Azure Cosmos db adatáttelepítési eszköz](https://github.com/azure/azure-documentdb-datamigrationtool)használatával telepítheti át az adatátvitelt.

Az Azure Cosmos Emulatort a Windows Docker-tárolón futtathatja, ha [a Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) -lekérési parancshoz és a [githubhoz](https://github.com/Azure/azure-cosmos-db-emulator-docker) is a `Dockerfile` és további információkat szeretne látni.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek

Mivel az Azure Cosmos Emulator a helyi fejlesztői munkaállomáson futó, emulált környezetet biztosít, a felhőben az emulátor és egy Azure Cosmos-fiók közötti funkcionalitás számos különbséggel rendelkezik:

* Az emulátorban jelenleg Adatkezelő támogatja az SQL API-ügyfeleket. Azure Cosmos DB API-k, például a MongoDB, a Table, a Graph és a Cassandra API-k Adatkezelő nézete és műveletei nem teljes mértékben támogatottak.
* Az Azure Cosmos Emulator csak egyetlen rögzített fiókot és egy jól ismert főkulcsot támogat. A kulcs újragenerálása nem lehetséges az Azure Cosmos Emulatorban, de az alapértelmezett kulcs a parancssori kapcsoló használatával módosítható.
* Az Azure Cosmos Emulator nem méretezhető szolgáltatás, és nem támogatja nagy mennyiségű tároló használatát.
* Az Azure Cosmos Emulator nem biztosít különböző [Azure Cosmos db konzisztencia-szintet](consistency-levels.md).
* Az Azure Cosmos Emulator nem nyújt [többrégiós replikálást](distribute-data-globally.md).
* Mivel előfordulhat, hogy az Azure Cosmos Emulator másolata nem mindig naprakész a Azure Cosmos DB szolgáltatás legutóbbi változásaival kapcsolatban, a [Azure Cosmos db Capacity plannerre](https://www.documentdb.com/capacityplanner) kell hivatkoznia, hogy pontosan megbecsülje az alkalmazás üzemi teljesítményre (RUs) vonatkozó szükségleteit.
* Ha az Azure Cosmos Emulatort használja, alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre (csak Azure Cosmos DB SDK-k használatával támogatott), vagy 5 korlátlan tárolót az Azure Cosmos Emulator használatával. Az érték módosításáról további információért lásd: [A PartitionCount érték beállítása](#set-partitioncount).

## <a name="system-requirements"></a>Rendszerkövetelmények

Az Azure Cosmos Emulator az alábbi hardver-és szoftver-követelményekkel rendelkezik:

* Szoftverkövetelmények
  * Windows Server 2012 R2, Windows Server 2016 vagy Windows 10
  * 64 bites operációs rendszer
* Minimális hardverkövetelmények
  * 2 GB RAM
  * 10 GB szabad merevlemez-terület

## <a name="installation"></a>Telepítés

Letöltheti és telepítheti az Azure Cosmos Emulatort a [Microsoft letöltőközpontból](https://aka.ms/cosmosdb-emulator) , vagy futtathatja a Windows rendszerhez készült Docker-emulátort is. A Windows rendszerhez készült Docker-emulátor használatára vonatkozó útmutatásért lásd: [Futtatás a Dockeren](#running-on-docker).

> [!NOTE]
> Az Azure Cosmos Emulator telepítéséhez, konfigurálásához és futtatásához rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. Az emulátor létrehoz/hozzáad egy tanúsítványt, és beállítja a tűzfalszabályok beállításait a szolgáltatásainak futtatásához; ezért ahhoz szükséges, hogy az emulátor végre tudja hajtani ezeket a műveleteket.

## <a name="running-on-windows"></a>Futtatás Windows rendszeren

Az Azure Cosmos Emulator elindításához kattintson a Start gombra, vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Cosmos emulatort**, és válassza ki az emulátort az alkalmazások listájából.

![Kattintson a Start gombra, vagy nyomja le a Windows billentyűt, kezdje beírni * * az Azure Cosmos Emulator * * parancsot, és válassza ki az emulátort az alkalmazások listájából.](./media/local-emulator/database-local-emulator-start.png)

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén. ![A helyi emulátor Azure Cosmos DB értesítése](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos Emulator alapértelmezés szerint a helyi gépen ("localhost") fut a 8081-es porton.

Alapértelmezés szerint az Azure Cosmos Emulator `C:\Program Files\Azure Cosmos DB Emulator`ra van telepítve. A parancssorból is elindíthatja és leállíthatja az emulátort. További információért tekintse meg a [parancssori eszközre vonatkozó referenciákat](#command-line).

## <a name="start-data-explorer"></a>Az Adatkezelő elindítása

Amikor az Azure Cosmos Emulator elindul, automatikusan megnyitja az Azure Cosmos Adatkezelőt a böngészőben. A címben `https://localhost:8081/_explorer/index.html`jelenik meg. Ha bezárta a Explorert, és később újra meg szeretné nyitni, megnyithatja az URL-címet a böngészőben, vagy elindíthatja az Azure Cosmos Emulatorban a Windows tálca ikonjában az alábbi ábrán látható módon.

![Azure Cosmos helyi emulátor adatkezelő-indító](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Frissítések keresése

Az Adatkezelő jelzi, ha új letölthető frissítés érhető el.

> [!NOTE]
> Az Azure Cosmos Emulator egyik verziójában létrehozott adatmennyiség (lásd:%LOCALAPPDATA%\CosmosDBEmulator vagy az adatelérési út választható beállításai) nem garantált, hogy más verzió használata esetén nem érhető el. Ha hosszú távon is meg kell őriznie az adatait, ajánlott az Azure Cosmos-fiókban tárolni az adatait, nem pedig az Azure Cosmos Emulatorban.

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

Ahogy a felhőben Azure Cosmos DB is, a rendszer minden, az Azure Cosmos Emulatoron végzett kérelmet hitelesíteni kell. Az Azure Cosmos Emulator egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a főkulcsos hitelesítéshez. Ez a fiók és a kulcs csak az Azure Cosmos Emulator használatával engedélyezett hitelesítő adatok. Ezek a következők:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Az Azure Cosmos Emulator által támogatott főkulcs kizárólag az emulátorhoz használható. Az Azure Cosmos Emulator használatával nem használhatja éles Azure Cosmos DB fiókját és kulcsát.

> [!NOTE]
> Ha elindította az emulátort a/Key kapcsolóval, akkor `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`helyett a generált kulcsot használja. A/Key beállítással kapcsolatos további információkért lásd a [parancssori eszköz dokumentációját.](#command-line)

A Azure Cosmos DBhoz hasonlóan az Azure Cosmos Emulator csak SSL-kapcsolaton keresztül támogatja a biztonságos kommunikációt.

## <a name="running-on-a-local-network"></a>Futtatás helyi hálózaton

Egy helyi hálózaton futtathatja az emulátort. A hálózati hozzáférés engedélyezéséhez a [parancssorban](#command-line-syntax)válassza a `/AllowNetworkAccess` lehetőséget, amelyhez `/Key=key_string` vagy `/KeyFile=file_name`megadása is szükséges. A `/GenKeyFile=file_name` használatával létrehozhat egy véletlenszerű kulccsal rendelkező fájlt. Ezt követően átadhatja `/KeyFile=file_name` vagy `/Key=contents_of_file`.

Ha az első alkalommal szeretné engedélyezni a hálózati hozzáférést, a felhasználónak le kell állítania az emulátort, és törölnie kell az emulátor adatkönyvtárát (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Fejlesztés az emulátorral

### <a name="sql-api"></a>SQL API

Miután futtatta az Azure Cosmos Emulatort az asztalon, bármilyen támogatott [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -t vagy a [Azure Cosmos db Rest APIt](/rest/api/cosmos-db/) használhatja az emulátorral való kommunikációhoz. Az Azure Cosmos Emulator olyan beépített Adatkezelő is tartalmaz, amely lehetővé teszi a tárolók létrehozását az SQL API Cosmos DB-hoz vagy a Mongo adatbázis-API-hoz, valamint a kódok írása nélkül megtekintheti és szerkesztheti az elemeket.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Miután futtatta az Azure Cosmos Emulatort az asztalon, használhatja a [Azure Cosmos db API](mongodb-introduction.md) -ját a MongoDB, hogy együttműködjön az emulátorral. Indítsa el az emulátort a parancssorból rendszergazdaként a "/EnableMongoDbEndpoint" parancs futtatásával. Ezután használja a következő kapcsolati karakterláncot a MongoDB API-fiókhoz való kapcsolódáshoz:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tábla API

Miután futtatta az Azure Cosmos Emulatort az asztalon, a [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) -val használhatja az emulátort. Indítsa el az emulátort a parancssorból rendszergazdaként a "/EnableTableEndpoint" parancs futtatásával. Ezután futtassa a következő kódot a Table API-fiókhoz való kapcsolódáshoz:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

Indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableCassandraEndpoint" paranccsal. Másik lehetőségként megadhatja a környezeti változót is `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [A Python 2,7 telepítése](https://www.python.org/downloads/release/python-2716/)

* [A Cassandra CLI/CQLSH telepítése](https://cassandra.apache.org/download/)

* Futtassa a következő parancsokat egy normál parancssori ablakban:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* A CQLSH-rendszerhéjban futtassa a következő parancsokat a Cassandra-végponthoz való kapcsolódáshoz:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin API

Indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableGremlinEndpoint" paranccsal. Másik lehetőségként beállíthatja a környezeti változót is `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* Az [Apache-tinkerpop-Gremlin-Console-3.3.4 telepítése](https://archive.apache.org/dist/tinkerpop/3.3.4).

* Az emulátor Adatkezelő hozzon létre egy "db1" adatbázist és egy "coll1" gyűjteményt; a partíciós kulcshoz válassza a "/Name" elemet.

* Futtassa a következő parancsokat egy normál parancssori ablakban:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* A Gremlin-rendszerhéjban futtassa a következő parancsokat az Gremlin-végponthoz való kapcsolódáshoz:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Az SSL-tanúsítvány exportálása

A .NET-nyelvek és a futtatókörnyezet a Windows tanúsítványtárolóval csatlakozik biztonságosan az Azure Cosmos DB helyi emulátorhoz. A többi nyelv saját módszert használ a tanúsítványok kezelésére és használatára. A Java saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) használ, míg a Python [szoftvercsatorna-burkolókat](https://docs.python.org/2/library/ssl.html).

A Windows tanúsítványtárolóval nem integrálható nyelvekkel és futtatókörnyezetekkel használni kívánt tanúsítvány beszerzéséhez a Windows tanúsítványkezelővel kell exportálnia azt. Indítsa el a certlm. msc futtatásával, vagy kövesse az [Azure Cosmos Emulator-tanúsítványok exportálása](./local-emulator-export-ssl-certificates.md)című lépésről lépésre vonatkozó utasításokat. Amikor fut a tanúsítványkezelő, nyissa meg a személyes tanúsítványokat az alább látható módon, és exportálja a tanúsítványt a „DocumentDBEmulatorCertificate” rövid névvel BASE-64 kódolású X.509 (.cer) fájlként.

![Azure Cosmos DB helyi emulátor SSL-tanúsítványa](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány a Java tanúsítványtárolóba importálható a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikkben lévő utasításokkal. Miután importálta a tanúsítványt a tanúsítványtárolóba, az SQL-hez és a Azure Cosmos DB API-MongoDB tartozó ügyfelek képesek lesznek csatlakozni az Azure Cosmos Emulatorhoz.

Amikor Python és Node.js SDK-kból csatlakozik az emulátorhoz, az SSL-ellenőrzés le van tiltva.

## <a id="command-line"></a>Parancssori eszköz referenciája
A telepítési helyről a parancssor segítségével elindíthatja és leállíthatja az emulátort, konfigurálhatja a beállításokat, és egyéb műveleteket is végrehajthat.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

A beállítások listájának megtekintéséhez írja be a `Microsoft.Azure.Cosmos.Emulator.exe /?` parancsot a parancssorba.

|**Beállítás** | **Leírás** | **Parancs**| **Argumentumok**|
|---|---|---|---|
|[Nincsenek argumentumok] | Az alapértelmezett beállításokkal elindítja az Azure Cosmos Emulatort. |Microsoft. Azure. Cosmos. Emulator. exe| |
|[Súgó] |Megjeleníti a támogatott parancssori argumentumok listáját.|Microsoft. Azure. Cosmos. Emulator. exe/? | |
| GetStatus |Lekéri az Azure Cosmos Emulator állapotát. Az állapotot a kilépési kód jelzi: 1 = Indítás, 2 = Fut, 3 = Leállítva. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet. | Microsoft. Azure. Cosmos. Emulator. exe/GetStatus| |
| Leállítás| Leállítja az Azure Cosmos Emulatort.| Microsoft. Azure. Cosmos. Emulator. exe/shutdown parancsot | |
|DataPath | Meghatározza az adatfájlok tárolására szolgáló elérési utat. Az alapértelmezett érték a%LocalAppdata%\CosmosDBEmulator. | Microsoft. Azure. Cosmos. Emulator. exe/DataPath =\<DataPath\> | \<adatútvonal\>: Egy hozzáférhető elérési út |
|Port | Az emulátorhoz használni kívánt portszámot határozza meg. Az alapértelmezett érték a 8081. |Microsoft. Azure. Cosmos. Emulator. exe/port =\<port\> | \<port\>: Egy portszám |
| ComputePort | A számítási együttműködési átjáró szolgáltatáshoz használandó portszám megadása. Az átjáró HTTP-végpontjának mintavételi portja a következőképpen számítja ki: ComputePort + 79. Ezért a ComputePort és a ComputePort + 79 nyitva kell lennie és elérhetőnek kell lennie. Az alapértelmezett érték a 8900. | Microsoft. Azure. Cosmos. Emulator. exe/ComputePort =\<ComputePort\> | \<computeport\>: egyetlen portszám |
| EnableMongoDbEndpoint = 3.2 | A MongoDB API 3,2-es verziójának engedélyezése | Microsoft. Azure. Cosmos. Emulator. exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | A MongoDB API 3,6-es verziójának engedélyezése | Microsoft. Azure. Cosmos. Emulator. exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Az alapértelmezett érték a 10255. |Microsoft. Azure. Cosmos. Emulator. exe/MongoPort =\<MongoPort\>|\<mongoport\>: Egy portszám|
| EnableCassandraEndpoint | Engedélyezi a Cassandra API | Microsoft. Azure. Cosmos. Emulator. exe/EnableCassandraEndpoint | |
| CassandraPort | Megadja a Cassandra-végponthoz használandó portszámot. Az alapértelmezett érték a 10350. | Microsoft. Azure. Cosmos. Emulator. exe/CassandraPort =\<CassandraPort\> | \<cassandraport\>: egyetlen portszám |
| EnableGremlinEndpoint | Gremlin API engedélyezése | Microsoft. Azure. Cosmos. Emulator. exe/EnableGremlinEndpoint | |
| GremlinPort | A Gremlin-végponthoz használandó portszám. Az alapértelmezett érték a 8901. | Microsoft. Azure. Cosmos. Emulator. exe/GremlinPort =\<port\> | \<port\>: Egy portszám |
|EnableTableEndpoint | Az Azure Table API engedélyezése | Microsoft. Azure. Cosmos. Emulator. exe/EnableTableEndpoint | |
|TablePort | Az Azure Table végponthoz használandó portszám Az alapértelmezett érték a 8902. | Microsoft. Azure. Cosmos. Emulator. exe/TablePort =\<port\> | \<port\>: Egy portszám|
| KeyFile | Az engedélyezési kulcs olvasása a megadott fájlból. A/GenKeyFile kapcsoló használata a keyfile létrehozásához | Microsoft. Azure. Cosmos. Emulator. exe/KeyFile =\<file_name\> | \<file_name\>: a fájl elérési útja |
| ResetDataPath | A megadott elérési úton lévő összes fájl rekurzív eltávolítása. Ha nem ad meg elérési utat, az alapértelmezett érték a%LOCALAPPDATA%\CosmosDbEmulator | Microsoft. Azure. Cosmos. Emulator. exe/ResetDataPath =\<útvonal > | \<Path\>: fájl elérési útja  |
| StartTraces  |  Hibakeresési nyomkövetési naplók gyűjtésének megkezdése a LOGMAN használatával. | Microsoft. Azure. Cosmos. Emulator. exe/StartTraces | |
| StopTraces     | A hibakeresési nyomkövetési naplók gyűjtésének leállítása a LOGMAN használatával. | Microsoft. Azure. Cosmos. Emulator. exe/StopTraces  | |
| StartWprTraces  |  A hibakeresési nyomkövetési naplók gyűjtésének megkezdése a Windows Teljesítményfigyelő eszközzel. | Microsoft. Azure. Cosmos. Emulator. exe/StartWprTraces | |
| StopWprTraces     | A hibakeresési nyomkövetési naplók gyűjtésének leállítása a Windows Teljesítményfigyelő eszközzel. | Microsoft. Azure. Cosmos. Emulator. exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Alapértelmezés szerint az emulátor újragenerálta az önaláírt SSL-tanúsítványt, ha a tanúsítvány SAN nem tartalmazza az emulátor tartományának nevét, a helyi IPv4-címeket, a "localhost" és a "127.0.0.1" értéket. Ha ezt a lehetőséget választja, az emulátor indításkor sikertelen lesz. Ezután a/GenCert lehetőség használatával hozzon létre és telepítsen egy új önaláírt SSL-tanúsítványt. | Microsoft. Azure. Cosmos. Emulator. exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Új önaláírt SSL-tanúsítvány létrehozása és telepítése. szükség esetén a további DNS-nevek vesszővel tagolt listáját is megadhatja az emulátor hálózaton keresztüli eléréséhez. | Microsoft. Azure. Cosmos. Emulator. exe/GenCert =\<DNS-nevek\> |\<DNS-nevek\>: a további DNS-nevek választható vesszővel tagolt listája  |
| DirectPorts |A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254. | Microsoft. Azure. Cosmos. Emulator. exe/DirectPorts:\<DirectPorts\> | \<közvetlen portok\>: 4 port vesszővel elválasztott listája |
| Jelmagyarázat |Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie. | Microsoft. Azure. Cosmos. Emulator. exe/Key:\<kulcs\> | \<kulcs\>: A kulcsnak 64 bites vektor base-64 kódolásának kell lennie|
| EnableRateLimiting | Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van. |Microsoft. Azure. Cosmos. Emulator. exe/EnableRateLimiting | |
| DisableRateLimiting |Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva. |Microsoft. Azure. Cosmos. Emulator. exe/DisableRateLimiting | |
| NoUI | Az emulátor felhasználói felületének megjelenítése nélkül. | Microsoft. Azure. Cosmos. Emulator. exe/NoUI | |
| NoExplorer | Az adatkezelő nem jelenik meg az indításkor. |Microsoft. Azure. Cosmos. Emulator. exe/NoExplorer | | 
| PartitionCount | A particionált tárolók maximális számát határozza meg. További információt [a tárolók számának módosítása](#set-partitioncount) című témakörben talál. | Microsoft. Azure. Cosmos. Emulator. exe/PartitionCount =\<PartitionCount\> | \<partitioncount\>: engedélyezett egypartíciós tárolók maximális száma. Az alapértelmezett érték 25. Maximálisan 250 engedélyezett.|
| DefaultPartitionCount| Meghatározza egy particionált tároló partícióinak alapértelmezett számát. | Microsoft. Azure. Cosmos. Emulator. exe/DefaultPartitionCount =\<DefaultPartitionCount\> | \<defaultpartitioncount\> alapértelmezett értéke 25.|
| AllowNetworkAccess | Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. A /Key=\<kulcs_sztring\> vagy a /KeyFile=\<fájlnév\> parancsot is meg kell adnia a hálózati hozzáférés engedélyezéséhez. | Microsoft. Azure. Cosmos. Emulator. exe/AllowNetworkAccess/Key =\<key_string\> vagy a Microsoft. Azure. Cosmos. Emulator. exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | Ne módosítsa a tűzfalszabályok használatát, ha a/AllowNetworkAccess beállítás van használatban. |Microsoft. Azure. Cosmos. Emulator. exe/NoFirewall | |
| GenKeyFile | Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható. | Microsoft. Azure. Cosmos. Emulator. exe/GenKeyFile =\<a kulcsfájl elérési útja\> | |
| Konzisztencia | Állítsa be a fiók alapértelmezett konzisztenciaszintjét. | Microsoft. Azure. Cosmos. Emulator. exe/Consistency =\<konzisztencia\> | \<konzisztencia\>: Az értéknek a következő [konzisztenciaszintek](consistency-levels.md) egyikének kell lennie: munkamenet (Session), erős (Strong), végleges (Eventual) vagy kötött elavulás (BoundedStaleness). Az alapértelmezett érték a munkamenet (session). |
| ? | A súgóüzenet megjelenítése.| | |

## <a id="set-partitioncount"></a>A tárolók számának módosítása

Alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre (csak Azure Cosmos DB SDK-k használata esetén támogatott), vagy 5 korlátlan tárolót az Azure Cosmos Emulator használatával. A **PartitionCount** érték módosításával akár 250 rögzített méretű tárolót vagy 50 korlátlan tárolót hozhat létre, vagy a kettő bármely kombinációját, amely nem haladja meg a 250 rögzített méretű tárolókat (ahol egy korlátlan tároló = 5 rögzített méretű tároló). Az emulátor azonban nem ajánlott úgy beállítani, hogy több mint 200 rögzített méretű tárolóval fusson. A lemez i/o-műveleteihez hozzáadott terhelés miatt a végponti API-k használatakor előre nem látható időtúllépéseket eredményezhet.

Ha az aktuális partíciók számának túllépése után megpróbál létrehozni egy tárolót, az emulátor egy ServiceUnavailable-kivételt hoz létre a következő üzenettel.

"Sajnos jelenleg nagy az igényünk ebben a régióban, és most nem lehet teljesíteni a kérést. Folyamatosan dolgozunk, hogy a kapacitás online állapotba kerüljön, és javasoljuk, hogy próbálkozzon újra.
Tevékenységazonosító: 12345678-1234-1234-1234-123456789abc "

Az Azure Cosmos Emulatorban elérhető tárolók számának módosításához futtassa a következő lépéseket:

1. Törölje az összes helyi Azure Cosmos Emulator-adatfájlt úgy, hogy a tálcán a jobb gombbal a **Azure Cosmos db Emulator** ikonra kattint, majd az **adatbeállítások visszaállítása..** . lehetőségre kattint.
2. Törölje a mappában található összes Emulator-adat`%LOCALAPPDATA%\CosmosDBEmulator`.
3. Az összes nyitott példány bezárásához kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson a **Kilépés** lehetőségre. Az összes példány bezárása egy percig is eltarthat.
4. Telepítse az [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator)legújabb verzióját.
5. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Például: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Az emulátor vezérlése

Az emulátor egy PowerShell-modullal elindíthatja, leállíthatja, eltávolíthatja és lekérheti a szolgáltatás állapotát. Futtassa a következő parancsmagot a PowerShell-modul használatához:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

vagy helyezze a `PSModules` könyvtárat a `PSModulesPath`, és importálja a következő parancsban látható módon:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Itt láthatja az emulátor PowerShellből való vezérlésére szolgáló parancsok összegzését:

### `Get-CosmosDbEmulatorStatus`

**Szintaxis**

`Get-CosmosDbEmulatorStatus`

**Megjegyzések**

Ezen ServiceControllerStatus értékek egyikét adja vissza: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running vagy ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Szintaxis**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Megjegyzések**

Elindítja az emulátort. Alapértelmezés szerint a parancs addig vár, amíg az emulátor készen nem áll a kérések fogadására. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor indításakor visszatérjen.

### `Stop-CosmosDbEmulator`

**Szintaxis**

 `Stop-CosmosDbEmulator [-NoWait]`

**Megjegyzések**

Leállítja az emulátort. Alapértelmezés szerint ez a parancs addig vár, amíg az emulátor teljesen le nem állt. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor leállításakor visszatérjen.

### `Uninstall-CosmosDbEmulator`

**Szintaxis**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Megjegyzések**

Eltávolítja az emulátort, és választhatóan eltávolítja az $env:LOCALAPPDATA\CosmosDbEmulator mappa teljes tartalmát.
A parancsmag biztosítja, hogy az emulátor le legyen állítva az eltávolítása előtt.

## <a name="running-on-docker"></a>Futtatás a Dockeren

Az Azure Cosmos Emulator a Windows Docker szolgáltatásban is futtatható. Az emulátor nem működik az Oracle Linux rendszerhez készült Dockeren.

A [Windows rendszerhez készült Docker](https://www.docker.com/docker-windows) telepítése után váltson Windows-tárolókra. Ehhez kattintson a jobb gombbal az eszköztáron lévő Docker ikonra, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget.

Ezután kérje le az emulátor rendszerképét a Docker Hubról. Ehhez futtassa a következő parancsot azon a felületen, amelyet használni szokott.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Futtassa a következő parancsokat a rendszerkép elindításához.

A parancssorból:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Ha egy port ütközési hibát észlel (a megadott port már használatban van) a Docker Run parancs futtatásakor, a portszámok módosításával átadhat egyéni portot. Megváltoztathatja például a "-p 8081:8081" kifejezést a "-p 443:8081" értékre.

A PowerShellből:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

A válasz a következőhöz hasonlóan néz ki:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Most az ügyfél válaszában lévő végponttal és főkulccsal importálja az SSL-tanúsítványt a gazdagépre. Az SSL-tanúsítvány importálásához tegye a következőket egy rendszergazdai parancssorban:

A parancssorból:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

A PowerShellből:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Az emulátor elindítása után az interaktív felület bezárása leállítja az emulátor tárolóját.

Az Adatkezelő megnyitásához nyissa meg a következő URL-címet a böngészőben. Az emulátor végpontja a fent látható válaszüzenetben van megadva.

    https://<emulator endpoint provided in response>/_explorer/index.html

Ha a .NET-ügyfélalkalmazás Linux Docker-tárolón fut, és ha egy gazdagépen futtatja az Azure Cosmos emulatort, kövesse az alábbi, Linux rendszerű szakaszt a tanúsítvány Linux Docker-tárolóba való importálásához.

## Futtatás Mac vagy Linux rendszeren<a id="mac"></a>

Jelenleg a Cosmos emulatort csak Windows rendszeren lehet futtatni. A Mac vagy Linux operációs rendszert futtató felhasználók futtathatják az emulátort egy Windows rendszerű virtuális gépen, amely olyan Hypervisort üzemeltet, mint a Parallels vagy a VirtualBox. A következő lépésekkel engedélyezheti ezt.

A Windows rendszerű virtuális gépen futtassa az alábbi parancsot, és jegyezze fel az IPv4-címeket.

```cmd
ipconfig.exe
```

Az alkalmazásban módosítania kell a végpontként használt URI-t, hogy a `localhost`helyett a `ipconfig.exe` által visszaadott IPv4-címet használja.

A következő lépés a Windows rendszerű virtuális gépen belül a következő lehetőségek használatával indítsa el a Cosmos emulatort a parancssorból.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Végezetül importálnia kell az emulátor HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a Linux vagy a Mac környezetbe.

### <a name="linux"></a>Linux

Ha Linux rendszeren dolgozik, a .NET-továbbítókat az OpenSSL-ben végezheti el az ellenőrzés elvégzéséhez:

1. [Exportálja a tanúsítványt pfx formátumban](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (a pfx elérhető a titkos kulcs exportálásának kiválasztásakor). 

1. Másolja a PFX-fájlt a linuxos környezetbe.

1. PFX-fájl konvertálása CRT-fájlba

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Másolja a CRT-fájlt arra a mappára, amely a Linux-disztribúcióban egyéni tanúsítványokat tartalmaz. A Debian-disztribúciók általában `/usr/local/share/ca-certificates/`találhatóak.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Frissítse a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat, amelyek frissítik a `/etc/ssl/certs/` mappát.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Ha Mac gépen dolgozik, kövesse az alábbi lépéseket:

1. [Exportálja a tanúsítványt pfx formátumban](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (a pfx elérhető a titkos kulcs exportálásának kiválasztásakor).

1. Másolja a PFX-fájlt a Mac-környezetbe.

1. Nyissa meg a *kulcstartó-hozzáférési* alkalmazást, és importálja a pfx-fájlt.

1. Nyissa meg a tanúsítványok listáját, és azonosítsa az `localhost`nevű nevet.

1. Nyissa meg az adott elemhez tartozó helyi menüt, válassza az *elem beolvasása* és a *megbízhatóság* > a *tanúsítvány használatakor* lehetőséget, válassza a *mindig megbízható*lehetőséget. 

   ![Nyissa meg az adott elemhez tartozó helyi menüt, válassza az elem beolvasása és a megbízhatóság alatt – ezen tanúsítvány használata esetén válassza a mindig megbízható lehetőséget.](./media/local-emulator/mac-trust-certificate.png)

Az alábbi lépések elvégzése után a környezet megbízik az emulátor által használt tanúsítványban, amikor az az IP-címhez csatlakozik `/AllowNetworkAccess`.

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi tippek segítséget nyújtanak az Azure Cosmos Emulatorral kapcsolatban felmerülő problémák elhárításához:

- Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Az adatai alaphelyzetbe állíthatók. ehhez kattintson a jobb gombbal az Azure Cosmos Emulator ikonra a tálcán, majd kattintson az adatbeállítások visszaállítása... elemre. Ha ez nem oldja meg a hibákat, távolítsa el az emulátort és az emulátor bármely régebbi verzióját, ha megtalálható, távolítsa el a "C:\Program files\Azure Cosmos DB Emulator" könyvtárat, és telepítse újra az emulátort. Útmutatásért lásd: [A helyi emulátor eltávolítása](#uninstall).

- Ha az Azure Cosmos Emulator összeomlik, Gyűjtse össze a memóriaképeket a "%LOCALAPPDATA%\CrashDumps" mappából, tömörítse őket, és nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com).

- Ha a `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`összeomlik, lehet, hogy ez egy tünet, amelyben a teljesítményszámlálók sérült állapotban vannak. A következő parancs általában a rendszergazdai parancssorból való futtatása javítja a problémát:

  ```cmd
  lodctr /R
   ```

- Ha kapcsolódási problémába ütközik, [Gyűjtse össze a nyomkövetési fájlokat](#trace-files), tömörítse őket, és nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com).

- Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát. Azt is megteheti, hogy elindítja az emulátort a/DisableRIO, amely átváltja az emulátor hálózati kommunikációját a normál Winsock-ra. 

- Amennyiben az emulátor futtatása közben a számítógép alvó állapotba lép vagy frissül az operációs rendszere, a **Szolgáltatás jelenleg nem érhető el** üzenet jelenik meg. Állítsa alaphelyzetbe az emulátort, és kattintson a jobb gombbal a Windows értesítési tálcán megjelenő ikonra, és válassza az **adatvisszaállítás**lehetőséget.

### <a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown` kérdésre adott válaszban foglalt lépéseket. Figyelje meg a rendszertálcán, hogy a program leállt-e, mert ez egy percig is eltarthat. Egyszerűen kattintson a **Kilépés** lehetőségre az Azure Cosmos Emulator felhasználói felületén is.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Hozza létre ismét a problémát. Ha az Adatkezelő nem működik, csak néhány másodpercig kell megnyitnia a böngészőt a hiba megtalálásához.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Keresse meg a `%ProgramFiles%\Azure Cosmos DB Emulator` mappát, és keresse meg a docdbemulator_000001.etl fájlt.
8. Nyisson meg egy támogatási jegyet a [Azure Portalban](https://portal.azure.com) , és foglalja bele az. etl fájlt a Reprodukálási lépésekkel együtt.

### <a id="uninstall"></a>A helyi emulátor eltávolítása

1. Lépjen ki a helyi emulátor összes nyitott példányáról. ehhez kattintson a jobb gombbal az Azure Cosmos Emulator ikonra a tálcán, majd kattintson a Kilépés elemre. Az összes példány bezárása egy percig is eltarthat.
2. A Windows keresőmezőbe írja be az **alkalmazások és szolgáltatások** kifejezést, majd kattintson az **Alkalmazások és szolgáltatások (rendszerbeállítások)** eredményre.
3. Az alkalmazások listájában görgessen az **Azure Cosmos DB Emulator** elemhez, válassza ki azt, kattintson az **Eltávolítás** lehetőségre, majd erősítse meg, és kattintson ismét az **Eltávolítás** elemre.
4. Az alkalmazás eltávolításakor lépjen a `%LOCALAPPDATA%\CosmosDBEmulator` mappához, és törölje ki.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban azt ismertettük, hogyan használhatja a helyi emulátort ingyenes helyi fejlesztési feladatokhoz. Most továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan exportálhatja az emulátor SSL-tanúsítványait.

> [!div class="nextstepaction"]
> [Az Azure Cosmos Emulator-tanúsítványok exportálása](local-emulator-export-ssl-certificates.md)
