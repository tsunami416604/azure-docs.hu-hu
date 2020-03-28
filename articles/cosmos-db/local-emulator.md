---
title: Helyi fejlesztés az Azure Cosmos emulátorral
description: Az Azure Cosmos emulátor használatával az alkalmazást helyileg, ingyenesen fejlesztheti és tesztelheti anélkül, hogy Azure-előfizetést hozna létre.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 287933de6403d680c5aa5b6c78df49abe5f2ac56
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238509"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Az Azure Cosmos emulátor használata helyi fejlesztéshez és teszteléshez

Az Azure Cosmos emulátor egy helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást fejlesztési célokra emulálja. Az Azure Cosmos emulátor használatával fejlesztheti és tesztelheti az alkalmazást helyileg, anélkül, hogy létrehozna egy Azure-előfizetést, vagy bármilyen költséggel járna. Ha elégedett azzal, ahogyan az alkalmazás működik az Azure Cosmos-emulátorban, átválthat egy Azure Cosmos-fiók használatára a felhőben.

Az Azure Cosmos emulátor [sql,](local-emulator.md#sql-api) [Cassandra,](local-emulator.md#cassandra-api) [MongoDB,](local-emulator.md#azure-cosmos-dbs-api-for-mongodb) [Gremlin](local-emulator.md#gremlin-api)és [Table](local-emulator.md#table-api) API-fiókok használatával fejleszthet. Azonban ebben az időben a Data Explorer nézet az emulátor teljes mértékben támogatja az ügyfelek csak az SQL API-t. 

## <a name="how-the-emulator-works"></a>Az emulátor működése

Az Azure Cosmos emulátor az Azure Cosmos DB szolgáltatás kiváló minőségű emulációját biztosítja. Támogatja az Azure Cosmos DB azonos funkciókat, beleértve az adatok létrehozásának és lekérdezésének támogatását, a tárolók kiépítését és méretezését, valamint a tárolt eljárások és eseményindítók végrehajtásához. Alkalmazásokat fejleszthet és tesztelhet az Azure Cosmos emulátorhasználatával, és globális szinten üzembe helyezheti őket az Azure Cosmos DB kapcsolatvégpontjában.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor implementálása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az Azure-infrastruktúrára támaszkodó funkciók, például a globális replikáció, az olvasási/írási késés egyszámjegyű késése és a hangolható konzisztenciaszintek nem alkalmazhatók.

Az Azure Cosmos DB [adatáttelepítési eszközhasználatával](https://github.com/azure/azure-documentdb-datamigrationtool)áttelepítheti az adatokat az Azure Cosmos DB adatáttelepítési eszközével.

Futtathatja az Azure Cosmos-emulátort a Windows Docker-tárolón, tekintse meg a [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) a docker lekéréses parancs és a [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) a `Dockerfile` és további információkért.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek

Mivel az Azure Cosmos-emulátor a helyi fejlesztői munkaállomáson futó emulált környezetet biztosít, az emulátor és a felhőben lévő Azure Cosmos-fiók funkciói között bizonyos különbségek vannak:

* Jelenleg data explorer az emulátor támogatja az ügyfelek az SQL API-t. Az Azure Cosmos DB API-k, például a MongoDB, a Table, graph és a Cassandra API-k Data Explorer nézete és műveletei nem teljes mértékben támogatottak.
* Az Azure Cosmos emulátor csak egyetlen rögzített fiókot és egy jól ismert főkulcsot támogat. A kulcs-regenerálás nem lehetséges az Azure Cosmos emulátorában, de az alapértelmezett kulcs a parancssori kapcsolóval módosítható.
* Az Azure Cosmos emulátor nem skálázható szolgáltatás, és nem támogatja a nagy számú tárolók.
* Az Azure Cosmos emulátor nem kínál különböző [Azure Cosmos DB konzisztenciaszinteket.](consistency-levels.md)
* Az Azure Cosmos emulátor nem kínál [többrégiós replikációt.](distribute-data-globally.md)
* Az Azure Cosmos-emulátor példánya nem mindig lehet naprakész az Azure Cosmos DB szolgáltatás legutóbbi változásaival, tekintse meg az [Azure Cosmos DB kapacitástervezőjét,](https://www.documentdb.com/capacityplanner) hogy pontosan megbecsülje az alkalmazás éles átviteli (RUs) igényeit.
* Az Azure Cosmos emulátor használata esetén alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre (csak az Azure Cosmos DB SDK-k használatával támogatott), vagy 5 korlátlan tárolót az Azure Cosmos emulátor használatával. Az érték módosításáról további információért lásd: [A PartitionCount érték beállítása](#set-partitioncount).

## <a name="system-requirements"></a>Rendszerkövetelmények

Az Azure Cosmos emulátor a következő hardver- és szoftverkövetelményekkel rendelkezik:

* Szoftverkövetelmények
  * Windows Server 2012 R2, Windows Server 2016 vagy Windows 10
  * 64 bites operációs rendszer esetén:
* Minimális hardverkövetelmények
  * 2 GB RAM
  * 10 GB szabad merevlemez-terület

## <a name="installation"></a>Telepítés

Letöltheti és telepítheti az Azure Cosmos emulátort a [Microsoft letöltőközpontból,](https://aka.ms/cosmosdb-emulator) vagy futtathatja az emulátort a Docker for Windows rendszeren. A Windows rendszerhez készült Docker-emulátor használatára vonatkozó útmutatásért lásd: [Futtatás a Dockeren](#running-on-docker).

> [!NOTE]
> Az Azure Cosmos emulátor telepítéséhez, konfigurálásához és futtatásához rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. Az emulátor létrehoz/hozzáad egy tanúsítványt, és a szolgáltatások futtatásához beállítja a tűzfalszabályokat is; ezért szükséges, hogy az emulátor képes legyen végrehajtani az ilyen műveleteket.

## <a name="running-on-windows"></a>Futtatás Windows rendszeren

Az Azure Cosmos emulátor elindításához válassza a Start gombot, vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Cosmos emulátort,** és válassza ki az emulátort az alkalmazások listájából.

![Válassza a Start gombot, vagy nyomja le a Windows billentyűt, kezdje el beírni a **Azure Cosmos emulátor**, és válassza ki az emulátort az alkalmazások listájából](./media/local-emulator/database-local-emulator-start.png)

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén. ![Az Azure Cosmos DB helyi emulátorfeladat-értesítése](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos emulátor alapértelmezés szerint fut a helyi gépen ("localhost") figyelése a 8081-es porton.

Az Azure Cosmos emulátor `C:\Program Files\Azure Cosmos DB Emulator` alapértelmezés szerint telepítve van. A parancssorból is elindíthatja és leállíthatja az emulátort. További információért tekintse meg a [parancssori eszközre vonatkozó referenciákat](#command-line).

## <a name="start-data-explorer"></a>Az Adatkezelő elindítása

Amikor az Azure Cosmos emulátor elindul, automatikusan megnyitja az Azure Cosmos Data Explorer böngészőben. A cím a következőképpen jelenik meg: `https://localhost:8081/_explorer/index.html`. Ha bezárja az intézőt, és később újra meg szeretné nyitni, megnyithatja az URL-címet a böngészőjében, vagy elindíthatja az Azure Cosmos emulátorból a Windows tálca ikonján az alábbiak szerint.

![Az Azure Cosmos helyi emulátor adatkezelőjének indítója](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Frissítések keresése

Az Adatkezelő jelzi, ha új letölthető frissítés érhető el.

> [!NOTE]
> Az Azure Cosmos emulator egy verziójában létrehozott adatok (lásd: %LOCALAPPDATA%\CosmosDBEmulator vagy az adatelérési út választható beállításait) nem garantáltan elérhetők egy másik verzió használata esetén. Ha hosszú távon meg kell persistniaz adatokat, javasoljuk, hogy az adatokat egy Azure Cosmos-fiókban tárolja, nem pedig az Azure Cosmos-emulátorban.

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

Az Azure Cosmos DB-hez a felhőben, az Azure Cosmos emulátorával szemben tett minden kérést hitelesíteni kell. Az Azure Cosmos emulátor egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a főkulcs-hitelesítéshez. Ez a fiók és a kulcs az egyetlen engedélyezett hitelesítő adatok az Azure Cosmos emulátor használata. Ezek a következők:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Az Azure Cosmos emulátor által támogatott főkulcs csak az emulátorral használható. Az éles Azure Cosmos DB-fiók és kulcs nem használható az Azure Cosmos emulátorral.

> [!NOTE]
> Ha az emulátort a /Key kapcsolóval indította el, `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`akkor a létrehozott kulcs helyett használja a. A /Key kapcsolóról a [Parancssori eszköz hivatkozása](#command-line) című témakörben talál további információt.

Az Azure Cosmos DB-hez ugyanúgy, mint az Azure Cosmos emulátor, csak biztonságos kommunikációt támogat Az SSL-en keresztül.

## <a name="running-on-a-local-network"></a>Futtatás helyi hálózaton

Egy helyi hálózaton futtathatja az emulátort. A hálózati hozzáférés engedélyezéséhez adja meg `/AllowNetworkAccess` a [parancssori](#command-line-syntax)kapcsolót, amelyhez meg kell adnia vagy `/Key=key_string` `/KeyFile=file_name`a lehetőséget is. Segítségével `/GenKeyFile=file_name` létrehozhat egy fájlt egy véletlenszerű kulccsal előre. Akkor adja át, `/KeyFile=file_name` `/Key=contents_of_file`hogy vagy .

A hálózati hozzáférés első alkalommal való engedélyezéséhez a felhasználónak le kell állítania az emulátort, és törölnie kell az emulátor adatkönyvtárát (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Fejlesztés az emulátorral

### <a name="sql-api"></a>SQL API

Miután az Azure Cosmos emulátor fut az asztalon, használhatja a támogatott [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) vagy az [Azure Cosmos DB REST API-t](/rest/api/cosmos-db/) az emulátor. Az Azure Cosmos emulátor is tartalmaz egy beépített Adatkezelő, amely lehetővé teszi, hogy tárolókat hozzon létre az SQL API-hoz vagy a Cosmos DB A Mongo DB API-hoz, és megtekintheti és szerkesztheti az elemeket kód írása nélkül.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Miután az Azure Cosmos emulátor fut az asztalon, használhatja az [Azure Cosmos DB API-mongoDB](mongodb-introduction.md) az emulátor. Indítsa el az emulátort a parancssorból rendszergazdaként a "/EnableMongoDbEndpoint" paranccsal. Ezután a következő kapcsolati karakterlánc segítségével csatlakozzon a MongoDB API-fiókhoz:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tábla API

Miután az Azure Cosmos emulátor fut az asztalon, használhatja az [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) az emulátor. Indítsa el az emulátort a parancssorból rendszergazdaként a "/EnableTableEndpoint" paranccsal. Ezután futtassa a következő kódot a tábla API-fiókjához való csatlakozáshoz:

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

Indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableCassandraEndpoint" paranccsal. Másik lehetőségként beállíthatja a `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`környezeti változót is.

* [A Python 2.7 telepítése](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLI/CQLSH telepítése](https://cassandra.apache.org/download/)

* Futtassa a következő parancsokat egy normál parancssorablakban:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* A CQLSH rendszerhéjban futtassa a következő parancsokat a Cassandra-végponthoz való csatlakozáshoz:

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

Indítsa el az emulátort egy rendszergazdai parancssorból a "/EnableGremlinEndpoint" paranccsal. Másik lehetőségként beállíthatja a környezeti változót is`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Telepítse az apache-tinkerpop-gremlin-console-3.3.4 konzolt.](https://archive.apache.org/dist/tinkerpop/3.3.4)

* Az emulátor Data Explorer hozzon létre egy adatbázist "db1" és a gyűjtemény "coll1"; a partíciókulcshoz válassza a "/name" lehetőséget.

* Futtassa a következő parancsokat egy normál parancssorablakban:

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

* A Gremlin rendszerhéjban futtassa a következő parancsokat a Gremlin végponthoz való csatlakozáshoz:

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

A Windows tanúsítványtárolóval nem integrálható nyelvekkel és futtatókörnyezetekkel használni kívánt tanúsítvány beszerzéséhez a Windows tanúsítványkezelővel kell exportálnia azt. A certlm.msc futtatásával vagy az [Azure Cosmos emulátortanúsítványok exportálása](./local-emulator-export-ssl-certificates.md)című lépéslépéssel indíthatja el. Amikor fut a tanúsítványkezelő, nyissa meg a személyes tanúsítványokat az alább látható módon, és exportálja a tanúsítványt a „DocumentDBEmulatorCertificate” rövid névvel BASE-64 kódolású X.509 (.cer) fájlként.

![Azure Cosmos DB helyi emulátor SSL-tanúsítványa](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány a Java tanúsítványtárolóba importálható a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikkben lévő utasításokkal. Miután a tanúsítvány importálása a tanúsítványtárolóba, az SQL és az Azure Cosmos DB API-mongoDB-hoz tartozó ügyfelek csatlakozhatnak az Azure Cosmos emulátorhoz.

Amikor Python és Node.js SDK-kból csatlakozik az emulátorhoz, az SSL-ellenőrzés le van tiltva.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Parancssori eszköz referenciája
A telepítés helyéről a parancssorsegítségével indíthatja el és állíthatja le az emulátort, konfigurálhatja a beállításokat, és egyéb műveleteket hajthat végre.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

A beállítások listájának megtekintéséhez írja be a `Microsoft.Azure.Cosmos.Emulator.exe /?` parancsot a parancssorba.

|**Beállítás** | **Leírás** | **Parancs**| **Argumentumok**|
|---|---|---|---|
|[Nincsenek argumentumok] | Elindítja az Azure Cosmos emulátor alapértelmezett beállításokkal. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Súgó] |Megjeleníti a támogatott parancssori argumentumok listáját.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Az Azure Cosmos-emulátor állapotát. Az állapotot a kilépési kód jelzi: 1 = Indítás, 2 = Fut, 3 = Leállítva. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Leállítás| Leállítja az Azure Cosmos emulátor.| Microsoft.Azure.Cosmos.Emulator.exe /Leállítás | |
|DataPath | Meghatározza az adatfájlok tárolására szolgáló elérési utat. Az alapértelmezett érték a következő: %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<adatelérési út\> | \<adatútvonal\>: Egy hozzáférhető elérési út |
|Port | Az emulátorhoz használni kívánt portszámot határozza meg. Az alapértelmezett érték 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: Egy portszám |
| Számítási port | Megadta a Compute Interop Gateway szolgáltatáshoz használandó portszámot. Az átjáró HTTP-végpontmintavételi portja ComputePort + 79 számítása. Ezért a ComputePort és a ComputePort + 79 nyitottnak és elérhetőnek kell lennie. Az alapértelmezett érték 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<számítási port\> | \<computeport\>: Egyportos szám |
| EnableMongoDbEndpoint=3.2 | A MongoDB API 3.2 engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | A MongoDB API 3.6 engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Az alapértelmezett érték 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: Egy portszám|
| EnableCassandraEndpoint | Cassandra API engedélyezése | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort között | Megadja a Cassandra végponthoz használandó portszámot. Az alapértelmezett érték 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Egyport száma |
| EnableGremlinEndpoint | Engedélyezi a Gremlin API-t | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort között | A Gremlin végponthoz használandó portszám. Az alapértelmezett érték 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: Egy portszám |
|TableEndpoint engedélyezése | Engedélyezi az Azure Table API-t | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|Táblaport | Az Azure-táblavégponthoz használandó portszám. Az alapértelmezett érték 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: Egy portszám|
| Keyfile | Olvassa be az engedélyezési kulcsot a megadott fájlból. Kulcsfájl létrehozása a /GenKeyFile kapcsolóval | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: A fájl elérési útja |
| ResetDataPath | Rekurzív módon eltávolítja a megadott elérési út összes fájlját. Ha nem ad meg elérési utat, alapértelmezés szerint %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<elérési út> | \<elérési\>út : Fájl elérési útja  |
| StartTraces  |  Kezdje el gyűjteni a hibakeresési nyomkövetési naplókat a LOGMAN használatával. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | A LOGMAN segítségével leállíthatja a hibakeresési nyomkövetési naplók gyűjtését. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Kezdje el gyűjteni a hibakeresési nyomkövetési naplókat a Windows Teljesítményrögzítő eszközzel. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | A Hibakeresési nyomkövetési naplók gyűjtésének leállítása a Windows Teljesítményrögzítő eszközzel. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Alapértelmezés szerint az Emulátor újragenerálja az önaláírt SSL-tanúsítványát, ha a tanúsítvány SAN-ja nem tartalmazza az Emulátor állomás tartománynevét, helyi IPv4-címét, "localhost" és "127.0.0.1" azonosítóját. Ezzel a beállítással az emulátor nem fog sikerülni indításkor. Ezután a /GenCert kapcsolóval hozzon létre és telepítsen egy új, önaláírt SSL-tanúsítványt. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert között | Új, önaláírt SSL-tanúsítvány létrehozása és telepítése. opcionálisan, beleértve a további DNS-nevek vesszővel tagolt listáját az Emulátor hálózaton keresztültörténő eléréséhez. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-nevek\> |\<dns-nevek:\>Választható vesszővel tagolt listája további dns nevek  |
| DirectPorts |A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<közvetlen portok\>: 4 port vesszővel elválasztott listája |
| Kulcs |Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie. | Microsoft.Azure.Cosmos.Emulator.exe /Kulcs:\<kulcs\> | \<kulcs\>: A kulcsnak 64 bites vektor base-64 kódolásának kell lennie|
| EnableRateLimiting | Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Az emulátor felhasználói felületének megjelenítése nélkül. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Az adatkezelő nem jelenik meg az indításkor. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | A particionált tárolók maximális számát adja meg. További információt [a Tárolók számának módosítása](#set-partitioncount) című témakörben talál. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partíciószám\> | \<partitioncount\>: Az engedélyezett egypartíciós tárolók maximális száma. Az alapértelmezett érték 25. Maximálisan 250 engedélyezett.|
| DefaultPartitionCount| A particionált tároló partícióinak alapértelmezett számát adja meg. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<alapértelmezett partidenszám\> | \<defaultpartitioncount\> Az alapértelmezett érték 25.|
| AllowNetworkAccess | Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. A /Key=\<kulcs_sztring\> vagy a /KeyFile=\<fájlnév\> parancsot is meg kell adnia a hálózati hozzáférés engedélyezéséhez. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> vagy Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Az /AllowNetworkAccess beállítás használata esetén ne módosítsa a tűzfalszabályokat. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<elérési út a kulcsfájlhoz\> | |
| Konzisztencia | Állítsa be a fiók alapértelmezett konzisztenciaszintjét. | Microsoft.Azure.Cosmos.Emulator.exe /Konzisztencia=\<konzisztencia\> | \<konzisztencia\>: Az értéknek a következő [konzisztenciaszintek](consistency-levels.md) egyikének kell lennie: munkamenet (Session), erős (Strong), végleges (Eventual) vagy kötött elavulás (BoundedStaleness). Az alapértelmezett érték a munkamenet (session). |
| ? | A súgóüzenet megjelenítése.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>A tárolók számának módosítása

Alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre (csak az Azure Cosmos DB SDK-k használatával támogatott), vagy 5 korlátlan tárolót az Azure Cosmos emulátor használatával. A **PartitionCount** érték módosításával legfeljebb 250 rögzített méretű tárolót vagy 50 korlátlan tárolót hozhat létre, vagy a kettő bármely kombinációját, amely nem haladja meg a 250 rögzített méretű tárolót (ahol egy korlátlan tároló = 5 rögzített méretű tároló). Azonban nem ajánlott beállítani az emulátort több mint 200 rögzített méretű tárolóval való futtatásra. A lemez I/o-műveleteihez hozzáadott többletterhelés miatt, amely a végponti API-k használatakor előre nem látható időtúlterhelést eredményez.

Ha az aktuális partíciószám túllépése után megpróbál létrehozni egy tárolót, az emulátor egy ServiceUnavailable kivételt okoz a következő üzenettel.

"Sajnáljuk, jelenleg nagy a kereslet ebben a régióban, és jelenleg nem tudjuk teljesíteni a kérését. Folyamatosan dolgozunk azon, hogy egyre több és több kapacitást hozzunk létre az interneten, és arra bátorítjuk Önt, hogy próbálja meg újra.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Az Azure Cosmos emulátorban elérhető tárolók számának módosításához futtassa az alábbi lépéseket:

1. Törölje az összes helyi Azure Cosmos-emulátor-adatot úgy, hogy a jobb gombbal az **Azure Cosmos DB emulátor** ikonjára kattint a tálcán, majd az **Adatok visszaállítása parancsra**kattint.
2. A mappában `%LOCALAPPDATA%\CosmosDBEmulator`lévő összes emulátoradat törlése.
3. Az összes nyitott példány bezárásához kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson a **Kilépés** lehetőségre. Az összes példány bezárása egy percig is eltarthat.
4. Telepítse az Azure [Cosmos emulátor](https://aka.ms/cosmosdb-emulator)legújabb verzióját.
5. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Például: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Az emulátor vezérlése

Az emulátor egy PowerShell-modullal rendelkezik a szolgáltatás indításához, leállításához, eltávolításához és lekéréséhez. Futtassa a következő parancsmast a PowerShell-modul használatához:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

vagy helyezze `PSModules` a `PSModulesPath` könyvtárat a helyére, és importálja a következő parancsban látható módon:

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

Az Azure Cosmos-emulátor futtatható a Windows Docker-en. Az emulátor nem működik az Oracle Linux rendszerhez készült Dockeren.

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
> Ha a docker-futtatási parancs futtatásakor portütközési hiba jelenik meg (a megadott port már használatban van), a portszámok módosításával egyéni portot adhat át. A "-p 8081:8081" (-p 8081:8081) például "-p 443:8081" lesz.

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

Ha egy .NET ügyfélalkalmazás linuxos docker-tárolón fut, és az Azure Cosmos emulátort egy gazdagépen futtatja, kövesse az alábbi Linux-szakaszt a tanúsítvány linuxos docker-tárolóba való importálásához.

## <a name="running-on-mac-or-linux"></a>Futás Mac-en vagy Linuxon<a id="mac"></a>

Jelenleg a Cosmos emulátor csak windowson futtatható. A Mac vagy Linux rendszert futtató felhasználók futtathatják az emulátort egy olyan Windows virtuális gépen, amely hipervizort, például Parallels vagy VirtualBox rendszert üzemeltet. Az alábbiakban az ezt lehetővé tevő lépéseket olvashatja.

A Windows virtuális gépen belül futtassa az alábbi parancsot, és jegyezze fel az IPv4-címet.

```cmd
ipconfig.exe
```

Az alkalmazáson belül módosítania kell a végpontként használt URI-t `ipconfig.exe` a `localhost`helyett visszaadott IPv4-cím használatához.

A következő lépés, a Windows virtuális gép, indítsa el a Cosmos emulátor a parancssorból a következő beállításokat.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Végül importálnunk kell az Emulátor Hitelesítésszolgáltató tanúsítványát a Linux vagy Mac környezetbe.

### <a name="linux"></a>Linux

Ha Linuxon dolgozik, az OpenSSL .NET reléket továbbít az érvényesítéshez:

1. [Exportálja a tanúsítványt PFX formátumban](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (a PFX a személyes kulcs exportálásakor érhető el). 

1. Másolja a PFX fájlt a Linux környezetbe.

1. A PFX-fájl konvertálása CRT-fájllá

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Másolja a CRT-fájlt a Linux-disztribúcióban egyéni tanúsítványokat tartalmazó mappába. Gyakran a Debian disztribúciókon `/usr/local/share/ca-certificates/`található.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Frissítse a hitelesítésszolgáltatói tanúsítványokat, amelyek frissítik a `/etc/ssl/certs/` mappát.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Ha Mac en dolgozik, kövesse az alábbi lépéseket:

1. [Exportálja a tanúsítványt PFX formátumban](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (a PFX a személyes kulcs exportálásakor érhető el).

1. Másolja a PFX-fájlt a Mac környezetébe.

1. Nyissa meg a *Kulcskarika Access* alkalmazást, és importálja a PFX-fájlt.

1. Nyissa meg a Tanúsítványok listáját, és `localhost`azonosítsa a nevet.

1. Nyissa meg az adott elem helyi menüjét, válassza az *Elem betöltése* lehetőséget, és a *Megbízható* > *csoportban Válassza a Megbízható módszert a Tanúsítvány használatakor* beállításnál a *Mindig megbízható*lehetőséget. 

   ![Nyissa meg az adott elem helyi menüjét, válassza az Elem betöltése lehetőséget, és a Megbízhatóság – Ha ezt a tanúsítványt használja, válassza a Mindig megbízható lehetőséget.](./media/local-emulator/mac-trust-certificate.png)

A következő lépések után a környezet megbízik az emulátor által használt tanúsítványban, amikor csatlakozik a. `/AllowNetworkAccess`

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi tippek segítségével elháríthatja az Azure Cosmos emulátorral kapcsolatos problémákat:

- Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Az adatok alaphelyzetbe állításához kattintson a jobb gombbal az Azure Cosmos emulátor ikonjára a tálcán, majd kattintson az Adatok visszaállítása parancsra.... Ha ez nem javítja ki a hibákat, eltávolíthatja az emulátort és az emulátor bármely régebbi verzióját, ha megtalálható, távolítsa el a "C:\Program files\Azure Cosmos DB Emulator" könyvtárat, és telepítse újra az emulátort. Útmutatásért lásd: [A helyi emulátor eltávolítása](#uninstall).

- Ha az Azure Cosmos emulátor összeomlik, gyűjtse össze a memóriaképfájlokat a(z) '%LOCALAPPDATA%\CrashDumps' mappából, tömörítse őket, és nyisson meg egy támogatási jegyet az [Azure Portalról.](https://portal.azure.com)

- Ha összeomlást tapasztal, ez olyan tünet lehet, amelynek következtében `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`a teljesítményszámlálók sérült állapotban vannak. A következő parancs rendszergazdai parancssorból történő futtatása általában megoldja a problémát:

  ```cmd
  lodctr /R
   ```

- Ha kapcsolódási problémát tapasztal, gyűjtse össze a [nyomkövetési fájlokat,](#trace-files)tömörítse őket, és nyisson meg egy támogatási jegyet az [Azure Portalon.](https://portal.azure.com)

- Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát. Másik lehetőségként indítsa el az emulátort a /DisableRIO kapcsolóval, amely az emulátor hálózati kommunikációját rendszeres Winsock-ra kapcsolja. 

- Amennyiben az emulátor futtatása közben a számítógép alvó állapotba lép vagy frissül az operációs rendszere, a **Szolgáltatás jelenleg nem érhető el** üzenet jelenik meg. Állítsa alaphelyzetbe az emulátor adatait úgy, hogy a jobb gombbal a Windows értesítési tálcáján megjelenő ikonra kattint, és válassza az **Adatok visszaállítása parancsot**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Figyelje meg a rendszertálcán, hogy a program leállt-e, mert ez egy percig is eltarthat. Az Azure Cosmos emulátor felhasználói felületén kattintson a **Kilépés** gombra is.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Hozza létre ismét a problémát. Ha az Adatkezelő nem működik, csak néhány másodpercig kell megnyitnia a böngészőt a hiba megtalálásához.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Keresse meg a `%ProgramFiles%\Azure Cosmos DB Emulator` mappát, és keresse meg a docdbemulator_000001.etl fájlt.
8. Nyisson meg egy támogatási jegyet az [Azure Portalon,](https://portal.azure.com) és tartalmazza az .etl fájlt a repro lépésekkel együtt.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>A helyi emulátor eltávolítása

1. Lépjen ki a helyi emulátor összes nyitott példányából úgy, hogy a jobb gombbal az Azure Cosmos emulátor ikonjára kattint a tálcán, majd a Kilépés parancsra kattint. Az összes példány bezárása egy percig is eltarthat.
2. A Windows keresőmezőbe írja be az **alkalmazások és szolgáltatások** kifejezést, majd kattintson az **Alkalmazások és szolgáltatások (rendszerbeállítások)** eredményre.
3. Az alkalmazások listájában görgessen az **Azure Cosmos DB Emulator** elemhez, válassza ki azt, kattintson az **Eltávolítás** lehetőségre, majd erősítse meg, és kattintson ismét az **Eltávolítás** elemre.
4. Az alkalmazás eltávolításakor lépjen a `%LOCALAPPDATA%\CosmosDBEmulator` mappához, és törölje ki.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban azt ismertettük, hogyan használhatja a helyi emulátort ingyenes helyi fejlesztési feladatokhoz. Most továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan exportálhatja az emulátor SSL-tanúsítványait.

> [!div class="nextstepaction"]
> [Az Azure Cosmos emulátortanúsítványainak exportálása](local-emulator-export-ssl-certificates.md)
