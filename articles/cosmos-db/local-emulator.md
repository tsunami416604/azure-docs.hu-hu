---
title: Helyi fejlesztés az Azure Cosmos DB Emulator használatával | Microsoft Docs
description: Az Azure Cosmos DB Emulator használatával helyben, ingyenesen fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést.
services: cosmos-db
keywords: Azure Cosmos DB Emulator
author: David-Noble-at-work
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 368caa063ea0487923af8a29f67aa73cae7ed75e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952892"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Az Azure Cosmos DB Emulator használata helyi fejlesztéshez és teszteléshez

<table>
<tr>
  <td><strong>Bináris fájlok</strong></td>
  <td>[MSI letöltése](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker-forrás</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az Azure Cosmos DB Emulator használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

> [!NOTE]
> Jelenleg az emulátor adatkezelője teljes mértékben támogatja az SQL API-gyűjteményeket és a MongoDB-gyűjteményeket. A táblázat-, gráf- és Cassandra-tárolók jelenleg nem teljes mértékben támogatottak. 

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Az emulátor telepítése
> * Kérelmek hitelesítése
> * Az Adatkezelő használata az emulátorban
> * SSL-tanúsítványok exportálása
> * Az emulátor behívása a parancssorból
> * Az emulátor futtatása Windows rendszerhez készült Dockerben
> * Profilelemzési fájlok összegyűjtése
> * Hibaelhárítás

Ajánlott először megnézni a következő videót, amelyben Kirill Gavrylyuk az Azure Cosmos DB Emulator használatának első lépéseit ismerteti. A videó DocumentDB Emulatornak hívja az emulátort, de maga az eszköz át lett nevezve Azure Cosmos DB Emulator névre a videó készítése óta. A videóban szereplő összes információ továbbra is érvényes az Azure Cosmos DB Emulatorra. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Az emulátor működése

Az Azure Cosmos DB Emulator az Azure Cosmos DB szolgáltatás kiváló minőségű emulációját nyújtja. Az Azure Cosmos DB-vel megegyező funkciókat támogat, beleértve a JSON-dokumentumok létrehozásának és lekérdezésének támogatását, a gyűjtemények kiépítését és méretezését, valamint a tárolt eljárások és eseményindítók végrehajtását. Az Azure Cosmos DB Emulator használatával alkalmazásokat fejleszthet és tesztelhet, és globális léptékben üzembe helyezheti azokat az Azure-ba, ha csupán egyetlen konfigurációs módosítást végez az Azure Cosmos DB kapcsolati végpontján.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor megvalósítása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az Azure-infrastruktúrára támaszkodó néhány funkció, például a globális replikáció, az egy számjegyű ezredmásodperces késés az olvasás/írás műveleteknél és az aprólékosan beállítható konzisztenciaszintek nem érhetők el.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek 
Mivel az Azure Cosmos DB Emulator egy helyi fejlesztői munkaállomáson futó emulált környezetet nyújt, különbségek vannak az emulátor és a felhőben lévő Azure Cosmos DB-fiókok funkciói között:

* Jelenleg az emulátor adatkezelője csak az SQL API-gyűjteményeket és a MongoDB-gyűjteményeket támogatja teljes mértékben. A táblázat-, gráf- és Cassandra-API-k még nem teljes mértékben támogatottak.  
* Az Azure Cosmos DB Emulator csak egyetlen rögzített fiókot és jól ismert főkulcsot támogat.  Az Azure Cosmos DB Emulatorban nincs lehetőség a kulcs újbóli létrehozására.
* Az Azure Cosmos DB Emulator egy nem skálázható szolgáltatás, és nem támogatja a gyűjtemények nagy számát.
* Az Azure Cosmos DB Emulator nem szimulál különböző [Azure Cosmos DB konzisztenciaszinteket](consistency-levels.md).
* Az Azure Cosmos DB Emulator nem szimulál [többrégiós replikációt](distribute-data-globally.md).
* Az Azure Cosmos DB Emulator nem támogatja az Azure Cosmos DB szolgáltatásban elérhető szolgáltatásikvóta-felülbírálásokat (például a dokumentumméret korlátait, a megnövelt particionált gyűjteménytárolást).
* Mivel előfordulhat, hogy az Azure Cosmos DB Emulator-példány nincs frissítve az Azure Cosmos DB szolgáltatás legutóbbi módosításaival, érdemes az [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) használatával pontosan megbecsülni az alkalmazás éles teljesítménnyel (kérelemegységekkel) kapcsolatos igényeit.

## <a name="system-requirements"></a>Rendszerkövetelmények
Az Azure Cosmos DB Emulator a következő hardver- és szoftverkövetelményekkel rendelkezik:

* Szoftverkövetelmények
  * Windows Server 2012 R2, Windows Server 2016 vagy Windows 10
*   Minimális hardverkövetelmények
  * 2 GB RAM
  * 10 GB szabad merevlemez-terület

## <a name="installation"></a>Telepítés
A [Microsoft letöltőközpontból](https://aka.ms/cosmosdb-emulator) töltheti le vagy telepítheti az Azure Cosmos DB Emulatort, vagy a Windows rendszerhez készült Dockeren futtathatja az emulátort. A Windows rendszerhez készült Docker emulátor használatának útmutatásaiért lásd: [Futtatás a Dockeren](#running-on-docker). 

> [!NOTE]
> Az Azure Cosmos DB Emulator telepítéséhez, konfigurálásához és futtatásához rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

## <a name="running-on-windows"></a>Futtatás Windows rendszeren

Az Azure Cosmos DB Emulator elindításához kattintson az Indítás gombra, vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Cosmos DB Emulator** kifejezést, majd válassza ki az emulátort az alkalmazások listájából. 

![Kattintson a Start gombra, vagy nyomja le a Windows billentyűt, kezdje el beírni az **Azure Cosmos DB Emulator** kifejezést, és válassza ki az emulátort az alkalmazások listájából](./media/local-emulator/database-local-emulator-start.png)

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén. ![Azure Cosmos DB helyi emulátor értesítése a tálcán](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos DB Emulator alapértelmezés szerint a 8081-es porton figyelő helyi gépen („localhost”) fut.

Az Azure Cosmos DB Emulator alapértelmezés szerint a `C:\Program Files\Azure Cosmos DB Emulator` könyvtárba van telepítve. A parancssorból is elindíthatja és leállíthatja az emulátort. További információért tekintse meg a [parancssori eszközre vonatkozó referenciákat](#command-line).

## <a name="start-data-explorer"></a>Az Adatkezelő elindítása

Az Azure Cosmos DB Emulator indításakor az automatikusan megnyitja az Azure Cosmos DB Adatkezelőt a böngészőben. A cím a következőképpen jelenik meg: [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Ha bezárja az Adatkezelőt, és később újra meg szeretné nyitni, megnyithatja az URL-t a böngészőben, vagy elindíthatja azt a Windows tálca Azure Cosmos DB Emulator ikonjával az alább látható módon.

![Az Azure Cosmos DB helyi emulátor adatkezelőjének értesítése](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Frissítések keresése
Az Adatkezelő jelzi, ha új letölthető frissítés érhető el. 

> [!NOTE]
> Nem biztos, hogy az Azure Cosmos DB Emulator egyik verziójában létrehozott adatok elérhetők egy másik verzióban. Ha hosszú ideig kell megőriznie az adatokat, ajánlott egy Azure Cosmos DB-fiókban tárolni azokat az Azure Cosmos DB Emulator helyett. 

## <a name="authenticating-requests"></a>Kérelmek hitelesítése
A felhőbeli Azure Cosmos DB-hez hasonlóan hitelesíteni kell minden olyan kérést, amelyet az Azure Cosmos DB Emulatorhoz végez. Az Azure Cosmos DB Emulator egyetlen rögzített fiókot és jól ismert főkulcsot támogat a főkulcs hitelesítéséhez. Az Azure Cosmos DB Emulatorral csak a fiók és a kulcs hitelesítő adatok használhatók. Ezek a következők:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Az Azure Cosmos DB Emulator által támogatott főkulcs csak az emulátorral használható. Nem használhatja az éles Azure Cosmos DB-fiókot és -kulcsot az Azure Cosmos DB Emulatorral. 

> [!NOTE] 
> Ha a /Key lehetőséggel indította el az emulátort, akkor a létrehozott kulcsot használja a következő helyett: "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Az Azure Cosmos DB szolgáltatáshoz hasonlóan az Azure Cosmos DB Emulator csak az SSL-en keresztüli biztonságos kommunikációt támogatja.

## <a name="running-on-a-local-network"></a>Futtatás helyi hálózaton

Egy helyi hálózaton futtathatja az emulátort. A hálózati hozzáférés engedélyezéséhez adja meg az /AllowNetworkAccess beállítást a [parancssorban](#command-line-syntax), amelyhez a /Key=kulcs_sztring vagy a /KeyFile=fájlnév kifejezést is meg kell adnia. A /GenKeyFile=fájlnév paranccsal hozhat létre egy fájlt előre egy véletlenszerű kulccsal.  Ezután a /KeyFile=fájlnév vagy a /Key=fájl_tartalma parancsnak adhatja át azt.

A hálózati hozzáférés első engedélyezéséhez a felhasználónak le kell állítania az emulátort, és törölnie kell az emulátor adatkönyvtárát (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Fejlesztés az emulátorral
Amikor az Azure Cosmos DB Emulator fut az asztalon, bármely támogatott [Azure Cosmos DB SDK-t](sql-api-sdk-dotnet.md) vagy [Azure Cosmos DB REST API-t](/rest/api/cosmos-db/) használhatja az emulátor használatához. Az Azure Cosmos DB Emulator beépített Adatkezelőt is tartalmaz, amellyel gyűjteményeket hozhat létre az SQL és a MongoDB API-khoz, és kód írása nélkül tekintheti meg és szerkesztheti a dokumentumokat.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Ha [Azure Cosmos DB protokolltámogatást használ a MongoDB-hez](mongodb-introduction.md), használja a következő kapcsolati sztringet:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Meglévő eszközöket, például az [Azure DocumentDB Studiót](https://github.com/mingaliu/DocumentDBStudio) használhatja az Azure Cosmos DB Emulatorhoz való csatlakozáshoz. Az Azure Cosmos DB Emulator és az Azure Cosmos DB szolgáltatás között is migráltathat adatokat az [Azure Cosmos DB adatmigrálási eszközzel](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Ha a /Key lehetőséggel indította el az emulátort, akkor a létrehozott kulcsot használja a következő helyett: "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Az Azure Cosmos DB Emulator használatával alapértelmezés szerint legfeljebb 25 egypartíciós gyűjteményt vagy 1 particionált gyűjteményt hozhat létre. Az érték módosításáról további információért lásd: [A PartitionCount érték beállítása](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Az SSL-tanúsítvány exportálása

A .NET-nyelvek és a futtatókörnyezet a Windows tanúsítványtárolóval csatlakozik biztonságosan az Azure Cosmos DB helyi emulátorhoz. A többi nyelv saját módszert használ a tanúsítványok kezelésére és használatára. A Java saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) használ, míg a Python [szoftvercsatorna-burkolókat](https://docs.python.org/2/library/ssl.html).

A Windows tanúsítványtárolóval nem integrálható nyelvekkel és futtatókörnyezetekkel használni kívánt tanúsítvány beszerzéséhez a Windows tanúsítványkezelővel kell exportálnia azt. Az elindításához futtassa a certlm.msc fájlt, vagy kövesse [Az Azure Cosmos DB Emulator tanúsítványok exportálása](./local-emulator-export-ssl-certificates.md) című cikkben szereplő részletes utasításokat. Amikor fut a tanúsítványkezelő, nyissa meg a személyes tanúsítványokat az alább látható módon, és exportálja a tanúsítványt a „DocumentDBEmulatorCertificate” rövid névvel BASE-64 kódolású X.509 (.cer) fájlként.

![Azure Cosmos DB helyi emulátor SSL-tanúsítványa](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány a Java tanúsítványtárolóba importálható a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikkben lévő utasításokkal. Miután a tanúsítványt a tanúsítványtárolóba importálta, a Java és a MongoDB alkalmazások az Azure Cosmos DB Emulatorhoz csatlakozhatnak.

Amikor Python és Node.js SDK-kból csatlakozik az emulátorhoz, az SSL-ellenőrzés le van tiltva.

## <a id="command-line"></a>Parancssori eszköz referenciája
A telepítés helyén a parancssorral elindíthatja és leállíthatja az emulátort, beállításokat konfigurálhat, és más műveleteket végezhet.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

A beállítások listájának megtekintéséhez írja be a `CosmosDB.Emulator.exe /?` parancsot a parancssorba.

<table>
<tr>
  <td><strong>Beállítás</strong></td>
  <td><strong>Leírás</strong></td>
  <td><strong>Parancs</strong></td>
  <td><strong>Argumentumok</strong></td>
</tr>
<tr>
  <td>[Nincsenek argumentumok]</td>
  <td>Az alapértelmezett beállításokkal indítja el az Azure Cosmos DB Emulatort.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Súgó]</td>
  <td>Megjeleníti a támogatott parancssori argumentumok listáját.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Lekéri az Azure Cosmos DB Emulator állapotát. Az állapotot a kilépési kód jelzi: 1 = Indítás, 2 = Fut, 3 = Leállítva. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Leállítás</td>
  <td>Bezárja az Azure Cosmos DB Emulatort.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Meghatározza az adatfájlok tárolására szolgáló elérési utat. Az alapértelmezett érték a %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;adatútvonal&gt;</td>
  <td>&lt;adatútvonal&gt;: Egy hozzáférhető elérési út</td>
</tr>
<tr>
  <td>Port</td>
  <td>Az emulátorhoz használni kívánt portszámot határozza meg.  Az alapértelmezett érték: 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: Egy portszám</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Az alapértelmezett érték: 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: Egy portszám</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;közvetlen portok&gt;</td>
  <td>&lt;közvetlen portok&gt;: 4 port vesszővel elválasztott listája</td>
</tr>
<tr>
  <td>Kulcs</td>
  <td>Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;kulcs&gt;</td>
  <td>&lt;kulcs&gt;: A kulcsnak 64 bites vektor base-64 kódolásának kell lennie</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Az emulátor felhasználói felületének megjelenítése nélkül.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Az adatkezelő nem jelenik meg az indításkor.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>A particionált gyűjtemények maximális számát határozza meg. További információkért lásd: [A gyűjtemények számának módosítása](#set-partitioncount).</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partíciószám&gt;</td>
  <td>&lt;partíciószám&gt;: Az engedélyezett egypartíciós gyűjtemények maximális száma. Az alapértelmezett érték: 25. Maximálisan 250 engedélyezett.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>A particionált gyűjtemények partícióinak alapértelmezett számát határozza meg.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;alapértelmezett partíciószám&gt;</td>
  <td>&lt;alapértelmezett partíciószám&gt; Az alapértelmezett érték: 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. A /Key=&lt;kulcs_sztring&gt; vagy a /KeyFile=&lt;fájlnév&gt; parancsot is meg kell adnia a hálózati hozzáférés engedélyezéséhez.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;kulcs sztring&gt;<br><br>vagy<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;fájlnév&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Ne állítsa be a tűzfalszabályokat az /AllowNetworkAccess használatakor.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile=&lt;kulcsfájl útvonala&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konzisztencia</td>
  <td>Állítsa be a fiók alapértelmezett konzisztenciaszintjét.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;konzisztencia&gt;</td>
  <td>&lt;konzisztencia&gt;: Az értéknek a következő [konzisztenciaszintek](consistency-levels.md) egyikének kell lennie: munkamenet (Session), erős (Strong), végleges (Eventual) vagy kötött elavulás (BoundedStaleness).  Az alapértelmezett érték a munkamenet (session).</td>
</tr>
<tr>
  <td>?</td>
  <td>A súgóüzenet megjelenítése.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>A gyűjtemények számának módosítása

Alapértelmezés szerint legfeljebb 25 egypartíciós gyűjteményt vagy 1 particionált gyűjteményt hozhat létre az Azure Cosmos DB Emulator használatával. A **partíciószám** érték módosításával legfeljebb 250 egypartíciós gyűjteményt vagy 10 particionált gyűjteményt hozhat létre, illetve ezek olyan kombinációit, amelyben legfeljebb 250 egyszeres partíció szerepel (ahol egy particionált gyűjtemény = 25 egypartíciós gyűjtemény).

Ha megkísérel létrehozni egy gyűjteményt, miután elérte az aktuális partíciószámot, az emulátorban ServiceUnavailable kivétel jelenik meg a következő üzenettel.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Az Azure Cosmos DB Emulatorhoz elérhető gyűjtemények számának módosításához tegye a következőt:

1. Az összes helyi Azure Cosmos DB Emulator adat törléséhez kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson az **Adatok visszaállítása…** lehetőségre.
2. Törölje a C:\Users\felhasználónév\AppData\Local\CosmosDBEmulator mappában található összes emulátoradatot.
3. Az összes nyitott példány bezárásához kattintson a jobb gombbal a rendszertálcán lévő **Azure Cosmos DB Emulator** ikonra, majd kattintson a **Kilépés** lehetőségre. Az összes példány bezárása egy percig is eltarthat.
4. Telepítse az [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) legújabb verzióját.
5. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Például: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Az emulátor vezérlése

Az emulátor egy PowerShell modullal rendelkezik a szolgáltatás állapotának elindításához, leállításához, eltávolításához és lekéréséhez. A használatához:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

vagy tegye a `PSModules` könyvtárat a `PSModulesPath` útvonalra, és importálja a következőhöz hasonlóan:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Itt láthatja az emulátor PowerShellből való vezérlésére szolgáló parancsok összegzését:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Szintaxis

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Megjegyzések

Ezen ServiceControllerStatus értékek egyikét adja vissza: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running vagy ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Szintaxis

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>Megjegyzések

Elindítja az emulátort. Alapértelmezés szerint a parancs addig vár, amíg az emulátor készen nem áll a kérések fogadására. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor indításakor visszatérjen.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Szintaxis

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Megjegyzések

Leállítja az emulátort. Alapértelmezés szerint ez a parancs addig vár, amíg az emulátor teljesen le nem állt. A -NoWait beállítást használja, ha azt szeretné, hogy a parancsmag az emulátor leállításakor visszatérjen.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Szintaxis

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Megjegyzések

Eltávolítja az emulátort, és választhatóan eltávolítja az $env:LOCALAPPDATA\CosmosDbEmulator mappa teljes tartalmát.
A parancsmag biztosítja, hogy az emulátor le legyen állítva az eltávolítása előtt.

## <a name="running-on-docker"></a>Futtatás a Dockeren

Az Azure Cosmos DB Emulator futtatható a Windows rendszerhez készült Dockeren. Az emulátor nem működik az Oracle Linux rendszerhez készült Dockeren.

A [Windows rendszerhez készült Docker](https://www.docker.com/docker-windows) telepítése után váltson Windows-tárolókra. Ehhez kattintson a jobb gombbal az eszköztáron lévő Docker ikonra, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget.

Ezután a kedvenc rendszerhéjában a következő parancs futtatásával kérje le az emulátor rendszerképét a Docker Hubról.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Futtassa a következő parancsokat a rendszerkép elindításához.

A parancssorból:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A PowerShellből:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A válasz a következőhöz hasonlóan néz ki:

```
Starting Emulator
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
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

A PowerShellből:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Az emulátor elindítása után az interaktív rendszerhéj bezárása leállítja az emulátor tárolóját.

Az Adatkezelő megnyitásához nyissa meg a következő URL-címet a böngészőben. Az emulátor végpontja a fent látható válaszüzenetben van megadva.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Hibaelhárítás

A következő tippekkel háríthatja el az Azure Cosmos DB Emulatorral kapcsolatban tapasztalt hibákat:

- Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Az adatok visszaállításához kattintson a jobb gombbal a rendszertálcán lévő Azure Cosmos DB Emulator ikonra, majd kattintson az Adatok visszaállítása… lehetőségre. Ha ez nem segít a problémán, eltávolíthatja, majd újból telepítheti az alkalmazást. Útmutatásért lásd: [A helyi emulátor eltávolítása](#uninstall).

- Ha az Azure Cosmos DB Emulator összeomlik, gyűjtsön be összeomlási memóriaképfájlokat a c:\Users\felhasználónév\AppData\Local\CrashDumps mappából, tömörítse azokat, és küldje el azokat csatolva az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre.

- Ha összeomlásokat tapasztal a CosmosDB.StartupEntryPoint.exe fájlban, futtassa a következő parancsot egy rendszergazdai parancssorból: `lodctr /R` 

- Ha kapcsolódási problémát tapasztal, [gyűjtsön be profilelemzési fájlokat](#trace-files), majd tömörítse és küldje el azokat csatolva az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre.

- Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát.

### <a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Figyelje meg a rendszertálcán, hogy a program leállt-e, mert ez egy percig is eltarthat. Az Azure Cosmos DB Emulator felhasználói felületén lévő **Kilépés** gombra is kattinthat.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Hozza létre ismét a problémát. Ha az Adatkezelő nem működik, csak néhány másodpercig kell megnyitnia a böngészőt a hiba megtalálásához.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Keresse meg a `%ProgramFiles%\Azure Cosmos DB Emulator` mappát, és keresse meg a docdbemulator_000001.etl fájlt.
7. Küldje el az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre az .etl fájlt a reprodukálás lépéseivel együtt a hibakereséshez.

### <a id="uninstall"></a>A helyi emulátor eltávolítása

1. Az emulátor összes nyitott példányának bezárásához kattintson a jobb gombbal a rendszertálcán lévő Azure Cosmos DB Emulator ikonra, majd kattintson a Kilépés lehetőségre. Az összes példány bezárása egy percig is eltarthat.
2. A Windows keresőmezőbe írja be az **alkalmazások és szolgáltatások** kifejezést, majd kattintson az **Alkalmazások és szolgáltatások (rendszerbeállítások)** eredményre.
3. Az alkalmazások listájában görgessen az **Azure Cosmos DB Emulator** elemhez, válassza ki azt, kattintson az **Eltávolítás** lehetőségre, majd erősítse meg, és kattintson ismét az **Eltávolítás** elemre.
4. Az alkalmazás eltávolításakor nyissa meg a C:\Users\<felhasználó>\AppData\Local\CosmosDBEmulator mappát, és törölje azt. 

## <a name="change-list"></a>Lista módosítása

A verziószám ellenőrzéséhez kattintson a jobb gombbal a tálcán lévő emulátor ikonra, és kattintson az Információ menüelemre.

### <a name="1220-released-on-april-20-2018"></a>1.22.0. Kiadás dátuma: 2018. április 20.

Az emulátorszolgáltatások a Cosmos DB felhőszolgáltatásokkal való paritást szolgáló frissítése mellett továbbfejlesztett PowerShell dokumentációt és néhány különböző hibajavítást is hozzáadtunk.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6 Kiadás dátuma: 2018. március 27.

Az emulátorszolgáltatások a Cosmos DB felhőszolgáltatásokkal való paritást szolgáló frissítése mellett egy új funkciót és két hibajavítást is hozzáadtunk ehhez a kiadáshoz.

#### <a name="features"></a>Szolgáltatások

1. A Start-CosmosDbEmulator parancs mostantól indítási beállításokat is tartalmaz.

#### <a name="bug-fixes"></a>Hibajavítások

1. A Microsoft.Azure.CosmosDB.Emulator PowerShell-modul mostantól biztosítja, hogy a `ServiceControllerStatus` felsorolás be legyen töltve.

2. A Microsoft.Azure.CosmosDB.Emulator PowerShell-modul mostantól tartalmaz egy jegyzékfájlt; ez az első kiadásból kimaradt.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 Kiadás dátuma: 2018. február 14.

Ebben a kiadásban egy új szolgáltatás és két hibajavítás szerepel. Köszönet azoknak a felhasználóknak, akik segítettek a problémák megkeresésében és kijavításában.

#### <a name="bug-fixes"></a>Hibajavítások

1. Az emulátor mostantól 1 vagy 2 maggal (vagy virtuális processzorral) rendelkező számítógépekkel is működik

   A Cosmos DB feladatokat foglal le különböző szolgáltatások elvégzése érdekében. A lefoglalt feladatok száma a gazdagépen lévő magok többszöröse. Az alapértelmezett szorzó jól működik éles környezetekben, ahol nagy a magok száma. Az 1 vagy 2 maggal rendelkező gépeken azonban nincsenek lefoglalva feladatok ezen szolgáltatások elvégzéséhez ezen szorzó alkalmazásakor.

   Ennek kijavítása érdekében egy konfiguráció-felülbírálást adtunk az emulátorhoz. Mostantól az 1-es szorzót alkalmazzuk. A különböző szolgáltatások elvégzése érdekében lefoglalt feladatok száma mostantól egyenlő a gazdagépen lévő magok számával.

   Ha semmi mást nem is tettünk volna ebben a kiadásban, ezt a problémát meg kellett oldani. Úgy tapasztaljuk, hogy számos, az emulátort futtató fejlesztési/tesztelési környezet 1 vagy 2 maggal rendelkezik.

2. Az emulátorhoz mostantól nem kell telepíteni a Microsoft Visual C++ 2015 újraterjeszthető csomagot.

   Úgy találtuk, hogy a Windows (asztali és kiszolgálói kiadások) friss telepítései nem tartalmazzák ezt az újraterjeszthető csomagot. Ezért mostantól az újraterjeszthető bináris fájlokat az emulátorhoz csomagoljuk.

#### <a name="features"></a>Szolgáltatások

Számos megkérdezett ügyfelünk azt mondta: Remek lenne, ha az emulátort szkriptekkel lehetne vezérelni. Ezért a jelen kiadásban hozzáadtunk néhány szkriptképességet. Az emulátor mostantól tartalmaz egy PowerShell-modult az indításhoz, a leállításhoz, az állapot lekéréséhez és az eltávolításhoz: `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1 Kiadás dátuma: 2018. január 26.

* A MongoDB összesítőfolyamat alapértelmezés szerint engedélyezett.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Telepítette a helyi emulátort
> * Futtatta az emulátort Windows rendszerhez készült Dockerben
> * Kérelmeket hitelesített
> * Használta az Adatkezelőt az emulátorban
> * SSL-tanúsítványokat exportált
> * Behívta az emulátort a parancssorból
> * Profilelemzési fájlokat gyűjtött össze

Ebben az oktatóanyagban megtudta, hogyan használhatja a helyi emulátort ingyenes helyi fejlesztéshez. Most továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan exportálhatja az emulátor SSL-tanúsítványait. 

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB Emulator tanúsítványok exportálása](local-emulator-export-ssl-certificates.md)
