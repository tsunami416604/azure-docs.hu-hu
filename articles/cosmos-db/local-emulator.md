---
title: Helyi fejlesztés az Azure Cosmos emulátorral
description: Az Azure Cosmos Emulatort használja, akkor fejlesztheti és tesztelheti alkalmazását helyileg ingyenes, Azure-előfizetés létrehozása nélkül.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: 0adb24458f718511c7134fc3bf36dd0b03173e30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011507"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Az Azure Cosmos Emulatort használja a helyi fejlesztési és tesztelési célra

Az Azure Cosmos Emulator emulálja az Azure Cosmos DB szolgáltatás fejlesztési célokra, helyi környezetet biztosít. Az Azure Cosmos Emulatort használja, fejlesztheti és tesztelheti alkalmazását helyileg, egy Azure-előfizetés létrehozása vagy szolgáltatásért nélkül. Amikor már elégedett az alkalmazás hogyan működik az Azure Cosmos-emulátorban, átválthat az Azure Cosmos-fiók használatára a felhőben.

Az Azure Cosmos Emulator használatával is fejleszthet [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), és [tábla](local-emulator.md#table-api) API-fiókok. Azonban jelenleg az adatkezelő nézet az emulátorban teljes körűen támogatja az ügyfelek SQL API-hoz csak. 

## <a name="how-the-emulator-works"></a>Az emulátor működése

Az Azure Cosmos Emulator biztosít az Azure Cosmos DB szolgáltatás a magas színvonalú használatot élvezhet emulációs. Azure Cosmos DB, például létrehozása és adatok lekérdezése kiépítés azonos funkciókat támogatja, és a tárolók méretezését és végrehajtása tárolt eljárásokkal és eseményindítókkal. Fejlesztés és tesztelheti az alkalmazásokat az Azure Cosmos Emulatort használja, és telepítheti őket, globális méretű csak biztosításával, módosítsa a kapcsolati végpont az Azure Cosmos DB egyetlen konfigurációval.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor implementálása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az funkciók, amelyek például a globális replikáció Azure-infrastruktúra támaszkodik, egyszámjegyű ezredmásodperces késés az olvasás/írás és aprólékosan beállítható konzisztenciaszintek vásárlására nem használhatók.

Segítségével áttelepítheti az adatokat az Azure Cosmos Emulatort, és az Azure Cosmos DB szolgáltatás között a [Azure Cosmos DB adatáttelepítési eszköz](https://github.com/azure/azure-documentdb-datamigrationtool).

Most futtassa a Windows Docker-tároló az Azure Cosmos-emulátor, lásd: a [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) a docker pull-parancs és [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) az emulátor forráskódja számára.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek

Az Azure Cosmos Emulator a helyi fejlesztői munkaállomáson futó emulált környezetet biztosít, mivel vannak eltérések a funkciók között az emulátorban, és a egy Azure Cosmos-fiók a felhőben:

* Jelenleg az adatkezelő az emulátorban ügyfelek SQL API támogatja. Az adatkezelő nézet és a műveletek az Azure Cosmos DB API-k, például a MongoDB, a tábla, a Graph és a Cassandra API-k nem teljes mértékben támogatottak.
* Az Azure Cosmos-emulátor támogatja a csak egyetlen rögzített fiók és a egy jól ismert főkulcsa. Kulcs újragenerálása esetén nem lehet az Azure Cosmos-emulátorban, azonban az alapértelmezett kulcsot is módosítható a parancssori kapcsolóval.
* Az Azure Cosmos Emulator nem skálázható szolgáltatás, és nem fogja támogatni a tárolók nagyszámú.
* Az Azure Cosmos Emulator nem kínál különböző [Azure Cosmos DB konzisztenciaszintjeinek](consistency-levels.md).
* Az Azure Cosmos Emulator nem kínál [többrégiós replikáció](distribute-data-globally.md).
* Mivel az Azure Cosmos Emulator másolatának előfordulhat, hogy nem mindig naprakészen tartása a legújabb módosításokat az Azure Cosmos DB szolgáltatásban, tekintse át a [Azure Cosmos DB-kapacitás planner](https://www.documentdb.com/capacityplanner) segítségével pontosan az éles környezetben átviteli sebesség (RU) az alkalmazás igényeinek megfelelően.
* Alapértelmezés szerint az Azure Cosmos-Emulátorban, használatakor legfeljebb 25 rögzített méretű tárolók (csak a támogatott Azure Cosmos DB SDK-k használatával) vagy 5 korlátlan tárolók az Azure Cosmos Emulator használatával hozhat létre. Az érték módosításáról további információért lásd: [A PartitionCount érték beállítása](#set-partitioncount).

## <a name="system-requirements"></a>Rendszerkövetelmények

Az Azure Cosmos Emulator a következő hardver- és követelményekkel rendelkezik:

* Szoftverkövetelmények
  * Windows Server 2012 R2, Windows Server 2016 vagy Windows 10
  * 64 bites operációs rendszer
* Minimális hardverkövetelmények
  * 2 GB RAM
  * 10 GB szabad merevlemez-terület

## <a name="installation"></a>Telepítés

Töltse le, és telepítse az Azure Cosmos-emulátort a a [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) vagy az emulátor futtathatók Docker for Windows. A Windows rendszerhez készült Docker-emulátor használatára vonatkozó útmutatásért lásd: [Futtatás a Dockeren](#running-on-docker).

> [!NOTE]
> Telepítése, konfigurálása és futtatása az Azure Cosmos Emulatort, rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. Az emulátorban fog tanúsítvány létrehozása vagy hozzáadása és is beállíthat, annak érdekében, hogy a szolgáltatások; futtassa a tűzfalszabályok ezért szükség az Emulator tudják az ilyen műveletek végrehajtásához.

## <a name="running-on-windows"></a>Futtatás Windows rendszeren

Indítsa el az Azure Cosmos-emulátort, kattintson a Start gombra, vagy nyomja meg a Windows billentyűt. Kezdje el beírni **Azure Cosmos-emulátor**, és válassza ki az emulátorban alkalmazások listáját.

![Kattintson a Start gombra, vagy a Windows billentyű kezdje el begépelni ** Azure Cosmos emulátor **, majd válassza az emulátor, az alkalmazások listájából](./media/local-emulator/database-local-emulator-start.png)

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén. ![Az Azure Cosmos DB helyi emulátorával feladat sáv értesítése](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos Emulator alapértelmezés szerint futtatja a helyi gépen (a "localhost") a 8081-es porton figyel.

Az Azure Cosmos Emulator telepítve van a `C:\Program Files\Azure Cosmos DB Emulator` alapértelmezés szerint. A parancssorból is elindíthatja és leállíthatja az emulátort. További információért tekintse meg a [parancssori eszközre vonatkozó referenciákat](#command-line).

## <a name="start-data-explorer"></a>Az Adatkezelő elindítása

Amikor elindítja az Azure Cosmos Emulatort, automatikusan megnyílik az Azure Cosmos adatkezelő a böngészőben. A cím jelenik meg `https://localhost:8081/_explorer/index.html`. Az explorer bezárja és újra szeretné, ha a böngészőben nyissa meg az URL-címet, vagy indítsa el az Azure Cosmos Emulator a a Windows tálca ikonjára a lent látható módon.

![Az Azure Cosmos helyi emulátor data explorer indítója](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Frissítések keresése

Az Adatkezelő jelzi, ha új letölthető frissítés érhető el.

> [!NOTE]
> Az egyik létrehozott adatokat az Azure Cosmos Emulator verziója (lásd a %LOCALAPPDATA%\CosmosDBEmulator vagy adatok elérési útja nem kötelező beállítás) nem garantált, hogy egy másik verzió használata esetén érhető el. Ha szeretné megőrizni az adatokat a hosszú távú, ajánlott tárolja az adatokat egy Azure Cosmos-fiókban, nem pedig az Azure Cosmos-emulátoron.

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

Mint az Azure Cosmos DB a felhőben minden egyes kérés, győződjön meg az Azure Cosmos emulatorban, hitelesíteni kell. Az Azure Cosmos-emulátor támogatja egyetlen rögzített fiók és a egy jól ismert hitelesítési kulcs főkulcs hitelesítéshez. A fiók és a kulcs is engedélyezett az Azure Cosmos-Emulátorban való használatra csak hitelesítő adatait. Ezek a következők:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A fő kulcsot, az Azure Cosmos-emulátor által támogatott csak az emulátorban való használathoz szolgál. Az éles környezetben az Azure Cosmos DB-fiókot és a kulcs nem használható az Azure Cosmos emulátorral.

> [!NOTE]
> Ha az emulátorban megkezdte a /Key lehetőséggel, majd használja a létrehozott kulcs helyett `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. /Key beállítással kapcsolatos további információkért lásd: [parancssori eszköz.](#command-line-syntax)

Mint az Azure Cosmos DB-vel az Azure Cosmos-emulátor támogatja a csak biztonságos kommunikáció SSL-en keresztül.

## <a name="running-on-a-local-network"></a>Futtatás helyi hálózaton

Egy helyi hálózaton futtathatja az emulátort. Hálózati hozzáférés engedélyezéséhez adja meg a `/AllowNetworkAccess` beállítást a [parancssori](#command-line-syntax), ami szintén megköveteli, hogy megadja `/Key=key_string` vagy `/KeyFile=file_name`. Használhat `/GenKeyFile=file_name` létrehozni egy fájlt az előre egy véletlenszerű kulcsot. Akkor adhat át, hogy a `/KeyFile=file_name` vagy `/Key=contents_of_file`.

Hálózati hozzáférés engedélyezéséhez először a felhasználó kell az emulátorban leállítja, majd törölje az emulátorban adatok könyvtárat (% LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Fejlesztés az emulátorral

### <a name="sql-api"></a>SQL API

Miután az Azure Cosmos Emulatort, asztali számítógépeken fut, akkor használhatja támogatott [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) vagy a [Azure Cosmos DB – REST API](/rest/api/cosmos-db/) való kommunikációhoz az emulátorral. Az Azure Cosmos-emulátor is tartalmaz egy beépített adatkezelő, amely lehetővé teszi a Mongo DB API-hoz, és tekintse meg az SQL API-t vagy Cosmos DB-tárolók létrehozása és kód írása nélkül elemek szerkesztése.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Ha használ [Azure Cosmos DB MongoDB-hez](mongodb-introduction.md), használja a következő kapcsolati karakterláncot:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tábla API

Miután az Azure Cosmos-emulátort az asztalon, használhatja a [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) való kommunikációhoz az emulátorral. Indítsa el a emulátor parancssorból a rendszergazdaként "/ EnableTableEndpoint". Ezután futtassa a következő kódot a table API-fiókhoz való csatlakozáshoz:

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

Indítsa el az emulátor egy rendszergazdai parancssorból az "/ EnableCassandraEndpoint". Azt is megteheti azt is beállíthatja a környezeti változó `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Telepítse a Python 2.7-es](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLI/CQLSH telepítése](http://cassandra.apache.org/download/)

* Egy normál parancssori ablakban futtassa a következő parancsokat:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* A CQLSH shellben futtassa az alábbi parancsokat a Cassandra-végponthoz csatlakozik:

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

Indítsa el az emulátor egy rendszergazdai parancssorból az "/ EnableGremlinEndpoint". Azt is megteheti azt is beállíthatja a környezeti változó `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Az apache-tinkerpop-gremlin-konzol-3.3.4 telepítése](http://tinkerpop.apache.org/downloads.html)

* Az Adatkezelőben az emulátorban hozzon létre egy adatbázist a "db1" és a egy gyűjtemény "coll1"; a partíciós kulcs válassza a "/ nevezze el"

* Egy normál parancssori ablakban futtassa a következő parancsokat:

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

* A Gremlin-rendszerhéjban a következő parancsokat a Gremlin-végpont csatlakozni:

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

A Windows tanúsítványtárolóval nem integrálható nyelvekkel és futtatókörnyezetekkel használni kívánt tanúsítvány beszerzéséhez a Windows tanúsítványkezelővel kell exportálnia azt. Indítsa el a certlm.msc futtatásával, vagy lépésről lépésre kövesse a [az Azure Cosmos Emulátortanúsítványok exportálása](./local-emulator-export-ssl-certificates.md). Amikor fut a tanúsítványkezelő, nyissa meg a személyes tanúsítványokat az alább látható módon, és exportálja a tanúsítványt a „DocumentDBEmulatorCertificate” rövid névvel BASE-64 kódolású X.509 (.cer) fájlként.

![Azure Cosmos DB helyi emulátor SSL-tanúsítványa](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány a Java tanúsítványtárolóba importálható a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikkben lévő utasításokkal. A tanúsítványt a tanúsítványtárolóba importálása után és az Azure Cosmos DB SQL API a mongodb-hez készült ügyfelek tud csatlakozni az Azure Cosmos Emulator lesz.

Amikor Python és Node.js SDK-kból csatlakozik az emulátorhoz, az SSL-ellenőrzés le van tiltva.

## <a id="command-line"></a>Parancssori eszköz referenciája
A telepítési helyről segítségével a parancssorból indítása és leállítása az emulátorban, beállításokat adhat meg és egyéb műveleteket.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

A beállítások listájának megtekintéséhez írja be a `CosmosDB.Emulator.exe /?` parancsot a parancssorba.

|**Beállítás** | **Leírás** | **Parancs**| **Argumentumok**|
|---|---|---|---|
|[Nincsenek argumentumok] | Az Azure Cosmos Emulator az alapértelmezett beállításokkal indul. |CosmosDB.Emulator.exe| |
|[Súgó] |Megjeleníti a támogatott parancssori argumentumok listáját.|CosmosDB.Emulator.exe /? | |
| GetStatus |Az Azure Cosmos Emulator állapotát olvassa be. Az állapot jelzi a kilépési kód: 1-től kezdődően 2 = futó, 3 = = leállt. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet. | CosmosDB.Emulator.exe /GetStatus| |
| Leállítás| Az Azure Cosmos Emulator leáll.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Meghatározza az adatfájlok tárolására szolgáló elérési utat. Alapértelmezett érték: % LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<adatútvonal\> | \<DataPath\>: Egy elérhető elérési útja |
|Port | Az emulátorhoz használni kívánt portszámot határozza meg. Alapértelmezett értéke 8081-es. |CosmosDB.Emulator.exe /Port=\<port\> | \<Port\>: Egyetlen port száma |
| MongoPort | A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Alapértelmezett érték: 10255. |CosmosDB.Emulator.exe /MongoPort = \<mongoport\>|\<mongoport\>: Egyetlen port száma|
| CassandraPort | A Cassandra-végpontjához használandó portszám meghatározására. Alapértelmezett érték: 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Egyetlen port száma |
| ComputePort | A megadott a port számát, a számítási Interop-átjáró szolgáltatás használatára. Az átjáró HTTP-végpont mintavételi port ComputePort + 79 számítása. Ezért ComputePort és ComputePort + 79 nyílt és elérhetőnek kell lenniük. Az alapértelmezett értékek a következők 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Egyetlen port száma |
| EnableCassandraEndpoint | Enables Cassandra API | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Lehetővé teszi, hogy a Gremlin API | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | A Gremlin-végpont használni kívánt portszámot. Alapértelmezett érték: 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<Port\>: Egyetlen port száma |
|TablePort | Az Azure Table-végpont esetében használni kívánt portszámot. Alapértelmezett érték: 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<Port\>: Egyetlen port száma|
| KeyFile | Engedélyezési kulcsot a megadott fájl olvasásakor. Hozzon létre egy keyfile /GenKeyFile lehetőség használatával | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<Fájlnév\>: A fájl elérési útja |
| ResetDataPath | Rekurzív módon eltávolítja a megadott elérési út összes fájlt. Ha nem ad meg elérési utat, alapértelmezés szerint az %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=<path>] | \<Elérési út\>: Fájl elérési útja  |
| StartTraces  |  A kezdő hibakeresési nyomkövetési naplók gyűjtésére. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Állítsa le a hibakeresési nyomkövetési naplók gyűjtésére. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Lehetővé teszi, hogy az Azure Table API | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Alapértelmezés szerint az Emulátorban az önaláírt SSL-tanúsítvány újragenerálása, ha a tanúsítvány SAN nem tartalmazza az emulátor állomás tartománynév, helyi IPv4 cím, "localhost" és "127.0.0.1". Ezzel a beállítással az emulátorban sikertelen lesz a indításakor helyette. Majd használjon a /GenCert beállítást hozhat létre, és a egy új önaláírt SSL-tanúsítvány telepítése. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Hozzon létre, és a egy új önaláírt SSL-tanúsítvány telepítése. igény szerint, beleértve az Emulátorban eléréséhez a hálózaton keresztül további DNS-nevek vesszővel tagolt listája. | CosmosDB.Emulator.exe /GenCert [ \<további dns-neveket vesszővel elválasztott listája\>] | |
| DirectPorts |A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<közvetlen portok\> | \<directports\>: 4 portok vesszővel tagolt listája |
| Kulcs |Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie. | CosmosDB.Emulator.exe /Key:\<kulcs\> | \<kulcs\>: A base-64 kódolását egy 64 bájt méretű vektor kulcsnak kell lennie|
| EnableRateLimiting | Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Az emulátor felhasználói felületének megjelenítése nélkül. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Az adatkezelő nem jelenik meg az indításkor. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | A particionált tárolók maximális számát határozza meg. Lásd: [tárolók számának módosítása](#set-partitioncount) további információt. | CosmosDB.Emulator.exe /PartitionCount=\<partíciószám\> | \<partitioncount\>: Az engedélyezett egypartíciós tárolók maximális számát. Az alapértelmezett érték 25. Maximálisan 250 engedélyezett.|
| DefaultPartitionCount| A particionált tároló partíciók alapértelmezett számát adja meg. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<alapértelmezett partíciószám\> | \<defaultpartitioncount\> alapértelmezett érték: 25.|
| AllowNetworkAccess | Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. A /Key=\<kulcs_sztring\> vagy a /KeyFile=\<fájlnév\> parancsot is meg kell adnia a hálózati hozzáférés engedélyezéséhez. | CosmosDB.Emulator.exe AllowNetworkAccess /Key =\<key_string\> vagy CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile =\<fájlnév\>| |
| NoFirewall | Ne módosítsa a tűzfalszabályok /AllowNetworkAccess beállítás használatakor. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható. | CosmosDB.Emulator.exe /GenKeyFile =\<kulcsfájl elérési útja\> | |
| Konzisztencia | Állítsa be a fiók alapértelmezett konzisztenciaszintjét. | CosmosDB.Emulator.exe /Consistency=\<konzisztencia\> | \<Konzisztencia\>: Érték a következők egyikének kell lennie [konzisztenciaszintek](consistency-levels.md): Munkamenet, erős, végleges, vagy BoundedStaleness. Az alapértelmezett érték a munkamenet (session). |
| ? | A súgóüzenet megjelenítése.| | |

## <a id="set-partitioncount"></a>Módosítsa a tárolók száma

Alapértelmezés szerint legfeljebb 25 rögzített méretű tárolók (csak a támogatott Azure Cosmos DB SDK-k használatával) vagy 5 korlátlan tárolók az Azure Cosmos Emulator használatával hozhat létre. Módosításával a **PartitionCount** érték, hozhat létre legfeljebb 250 rögzített méretű tárolók vagy 50 korlátlan tárolók vagy tetszőleges kombinációjának a két, legfeljebb 250 rögzített méretű tárolók (ahol egy korlátlan számú tárolót = 5 rögzített méretű tárolók). Azonban nem ajánlott az emulátor beállításához, több mint 200 rögzített méretű tárolók futtatásához. Miatt a terhelést a lemez i/o-műveletek hozzáadása, amely eredményez időtúllépések előre nem látható a végpont API-k használata esetén.


Ha a tároló létrehozása után az aktuális partíciók száma túl lett lépve próbál a emulátor ServiceUnavailable kivételt, a következő üzenetet okoz.

"Sajnáljuk, hogy nagy kereslet ebben a régióban jelenleg tapasztal, és jelenleg a kérés nem teljesíthető. A Microsoft folyamatosan több és több kapacitás online állapotba és és, és javasoljuk, hogy próbálja meg újra.
Várjuk e-mailek askcosmosdb@microsoft.com bármikor és bármilyen okból. Tevékenységazonosító: 12345678-1234-1234-1234-123456789abc"

Elérhető az Azure Cosmos-emulátorban tárolók száma módosításához futtassa az alábbi lépéseket:

1. Kattintson a jobb gombbal az összes helyi Azure Cosmos-emulátor adatok törlése a **Azure Cosmos DB Emulator** ikonja a tálcán, és végül pedig kattintson **adatok alaphelyzetbe állítása...** .
2. Ez a mappa összes emulátor adatot törölni `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Az összes nyitott példány bezárásához kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson a **Kilépés** lehetőségre. Az összes példány bezárása egy percig is eltarthat.
4. Telepítse a legújabb verzióját a [Azure Cosmos-emulátor](https://aka.ms/cosmosdb-emulator).
5. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Például: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Az emulátor vezérlése

Az emulator együttműködik a PowerShell-modul indítása, leállítása, távolítsa el, és a szolgáltatás állapotának lekéréséhez. A PowerShell-modult használja a következő parancsmag futtatásával:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

vagy helyezze a `PSModules` könyvtárába a `PSModulesPath` , majd azok importálása az alábbi parancsban látható módon:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Itt láthatja az emulátor PowerShellből való vezérlésére szolgáló parancsok összegzését:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Megjegyzések**

Ezek ServiceControllerStatus érték egyikét adja vissza: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running vagy ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Megjegyzések**

Elindítja az emulátort. Alapértelmezés szerint a parancs addig vár, amíg az emulátor készen nem áll a kérések fogadására. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor indításakor visszatérjen.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Megjegyzések**

Leállítja az emulátort. Alapértelmezés szerint ez a parancs addig vár, amíg az emulátor teljesen le nem állt. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor leállításakor visszatérjen.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Megjegyzések**

Eltávolítja az emulátort, és választhatóan eltávolítja az $env:LOCALAPPDATA\CosmosDbEmulator mappa teljes tartalmát.
A parancsmag biztosítja, hogy az emulátor le legyen állítva az eltávolítása előtt.

## <a name="running-on-docker"></a>Futtatás a Dockeren

Az Azure Cosmos Emulator futtatni a Docker for Windows. Az emulátor nem működik az Oracle Linux rendszerhez készült Dockeren.

A [Windows rendszerhez készült Docker](https://www.docker.com/docker-windows) telepítése után váltson Windows-tárolókra. Ehhez kattintson a jobb gombbal az eszköztáron lévő Docker ikonra, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget.

Ezután kérje le az emulátor rendszerképét a Docker Hubról. Ehhez futtassa a következő parancsot azon a felületen, amelyet használni szokott.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Futtassa a következő parancsokat a rendszerkép elindításához.

A parancssorból:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

A PowerShellből:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

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


## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi tippek segítségével az Azure Cosmos emulátorral tapasztal problémák hibaelhárításához:

- Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Visszaállíthatja az adatokat, kattintson a jobb gombbal a tálcán a Azure Cosmos-emulátor ikonjára, és kattintson az adatok alaphelyzetbe állítása... Ha, amely nem oldja meg a hibákat, távolíthatja el az emulátort, és minden régebbi verzióját az emulátorban Ha található, "C:\Program files\Azure Cosmos DB Emulator" könyvtár eltávolítása és újratelepítése az emulátorban. Útmutatásért lásd: [A helyi emulátor eltávolítása](#uninstall).

- Ha az Azure Cosmos Emulator összeomlik, memóriaképek gyűjtése (% LOCALAPPDATA%\CrashDumps) mappát, tömörítéssel és csatolja őket egy e-mailek [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Ha összeomlik a `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, ez lehet a hiba, a teljesítményszámlálók sérült állapotban vannak. Általában a következő parancsot egy rendszergazdai jogú parancssorban futtatja megoldja a problémát:

  ```cmd
  lodctr /R
   ```

- Ha kapcsolódási problémát tapasztal, [gyűjtsön be profilelemzési fájlokat](#trace-files), majd tömörítse és küldje el azokat csatolva az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre.

- Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát. Azt is megteheti indítsa el az emulátort /DisableRIO, amelyek rendszeres Winsock átvált az emulátor hálózati kommunikáció. 

- Amennyiben az emulátor futtatása közben a számítógép alvó állapotba lép vagy frissül az operációs rendszere, a **Szolgáltatás jelenleg nem érhető el** üzenet jelenik meg. Az emulator adatok, kattintson a jobb gombbal a, és válassza a windows értesítési területen megjelenő ikonra visszaállítása **adatok alaphelyzetbe állítása**.

### <a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Figyelje meg a rendszertálcán, hogy a program leállt-e, mert ez egy percig is eltarthat. Akkor is egyszerűen kattintson **kilépési** az Azure Cosmos Emulator felhasználói felületén.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Hozza létre ismét a problémát. Ha az Adatkezelő nem működik, csak néhány másodpercig kell megnyitnia a böngészőt a hiba megtalálásához.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Keresse meg a `%ProgramFiles%\Azure Cosmos DB Emulator` mappát, és keresse meg a docdbemulator_000001.etl fájlt.
7. Küldje el az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre az .etl fájlt a reprodukálás lépéseivel együtt a hibakereséshez.

### <a id="uninstall"></a>A helyi emulátor eltávolítása

1. Kattintson a jobb gombbal a tálcán a Azure Cosmos-emulátor ikonjára, majd a kilépési lépjen ki a helyi emulátor minden megnyitott példánya. Az összes példány bezárása egy percig is eltarthat.
2. A Windows keresőmezőbe írja be az **alkalmazások és szolgáltatások** kifejezést, majd kattintson az **Alkalmazások és szolgáltatások (rendszerbeállítások)** eredményre.
3. Az alkalmazások listájában görgessen az **Azure Cosmos DB Emulator** elemhez, válassza ki azt, kattintson az **Eltávolítás** lehetőségre, majd erősítse meg, és kattintson ismét az **Eltávolítás** elemre.
4. Az alkalmazás eltávolításakor lépjen a `%LOCALAPPDATA%\CosmosDBEmulator` mappához, és törölje ki.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban azt ismertettük, hogyan használhatja a helyi emulátort ingyenes helyi fejlesztési feladatokhoz. Most továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan exportálhatja az emulátor SSL-tanúsítványait.

> [!div class="nextstepaction"]
> [Az Azure Cosmos emulátortanúsítványok exportálása](local-emulator-export-ssl-certificates.md)
