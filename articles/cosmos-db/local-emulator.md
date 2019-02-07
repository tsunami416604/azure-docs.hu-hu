---
title: Az Azure Cosmos DB Emulator a helyi fejlesztés
description: Az Azure Cosmos DB Emulator használatával helyben, ingyenesen fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: cbdc57489eb7ebd50e3ce7e2b4e0e4081aef8e27
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770384"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Az Azure Cosmos DB Emulator használata helyi fejlesztéshez és teszteléshez

|**Bináris fájlok**|[MSI letöltése](https://aka.ms/cosmosdb-emulator)|| **Docker**|[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)|| **Docker-forrás** | [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker)|

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az Azure Cosmos DB Emulator használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

Jelenleg az Adatkezelőben az emulátorban csak teljes körűen támogatja az ügyfelek az SQL API és az Azure Cosmos DB API a mongodb-hez. A tábla, a Graph és a Cassandra API-k az ügyfelek teljes körűen nem támogatottak.

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

## <a name="how-the-emulator-works"></a>Az emulátor működése

Az Azure Cosmos DB Emulator az Azure Cosmos DB szolgáltatás kiváló minőségű emulációját nyújtja. Az Azure Cosmos DB-vel megegyező funkciókat támogat, beleértve a JSON-dokumentumok létrehozásának és lekérdezésének támogatását, a gyűjtemények kiépítését és méretezését, valamint a tárolt eljárások és eseményindítók végrehajtását. Az Azure Cosmos DB Emulator használatával alkalmazásokat fejleszthet és tesztelhet, és globális léptékben üzembe helyezheti azokat az Azure-ban, ha csupán egyetlen konfigurációs módosítást végez az Azure Cosmos DB kapcsolati végpontján.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor implementálása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az Azure-infrastruktúrára támaszkodó néhány funkció, például a globális replikáció, az egy számjegyű ezredmásodperces késés az olvasás/írás műveleteknél és az aprólékosan beállítható konzisztenciaszintek nem érhetők el.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek
Mivel az Azure Cosmos DB Emulator egy helyi fejlesztői munkaállomáson futó emulált környezetet nyújt, különbségek vannak az emulátor és a felhőben lévő Azure Cosmos DB-fiókok funkciói között:

* Jelenleg az adatkezelőt az emulátorban támogatja az ügyfelek az SQL API és az Azure Cosmos DB API a mongodb-hez. A tábla, a Graph és a Cassandra API-k ügyfelek még nem támogatott.
* Az Azure Cosmos DB Emulator csak egyetlen rögzített fiókot és jól ismert főkulcsot támogat. Az Azure Cosmos DB Emulatorban nincs lehetőség a kulcs újbóli létrehozására.
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
A [Microsoft letöltőközpontból](https://aka.ms/cosmosdb-emulator) töltheti le vagy telepítheti az Azure Cosmos DB Emulatort, vagy a Windows rendszerhez készült Dockeren futtathatja az emulátort. A Windows rendszerhez készült Docker-emulátor használatára vonatkozó útmutatásért lásd: [Futtatás a Dockeren](#running-on-docker).

> [!NOTE]
> Az Azure Cosmos DB Emulator telepítéséhez, konfigurálásához és futtatásához rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

## <a name="running-on-windows"></a>Futtatás Windows rendszeren

Az Azure Cosmos DB Emulator elindításához kattintson az Indítás gombra, vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Cosmos DB Emulator** kifejezést, majd válassza ki az emulátort az alkalmazások listájából.

![Kattintson a Start gombra, vagy nyomja le a Windows billentyűt, kezdje el beírni az **Azure Cosmos DB Emulator** kifejezést, és válassza ki az emulátort az alkalmazások listájából](./media/local-emulator/database-local-emulator-start.png)

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén. ![Azure Cosmos DB helyi emulátor értesítése a tálcán](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos DB Emulator alapértelmezés szerint a 8081-es porton figyelő helyi gépen („localhost”) fut.

Az Azure Cosmos DB Emulator alapértelmezés szerint a `C:\Program Files\Azure Cosmos DB Emulator` könyvtárba van telepítve. A parancssorból is elindíthatja és leállíthatja az emulátort. További információért tekintse meg a [parancssori eszközre vonatkozó referenciákat](#command-line).

## <a name="start-data-explorer"></a>Az Adatkezelő elindítása

Az Azure Cosmos DB Emulator az indításakor automatikusan megnyitja az Azure Cosmos DB Adatkezelőt a böngészőben. A cím a következőképpen jelenik meg: [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Ha bezárja az Adatkezelőt, és később újra meg szeretné nyitni, megnyithatja az URL-t a böngészőben, vagy elindíthatja azt a Windows tálca Azure Cosmos DB Emulator ikonjával az alább látható módon.

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

Egy helyi hálózaton futtathatja az emulátort. A hálózati hozzáférés engedélyezéséhez adja meg az /AllowNetworkAccess beállítást a [parancssorban](#command-line-syntax), amelyhez a /Key=kulcs_sztring vagy a /KeyFile=fájlnév kifejezést is meg kell adnia. A /GenKeyFile=fájlnév paranccsal hozhat létre egy fájlt előre egy véletlenszerű kulccsal. Ezután a /KeyFile=fájlnév vagy a /Key=fájl_tartalma parancsnak adhatja át azt.

A hálózati hozzáférés első engedélyezéséhez a felhasználónak le kell állítania az emulátort, és törölnie kell az emulátor adatkönyvtárát (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Fejlesztés az emulátorral
Amikor az Azure Cosmos DB Emulator fut az asztalon, bármelyik támogatott [Azure Cosmos DB SDK-t](sql-api-sdk-dotnet.md) vagy [Azure Cosmos DB REST API-t](/rest/api/cosmos-db/) használhatja az emulátor használatához. Az Azure Cosmos DB Emulatort is tartalmaz egy beépített adatkezelő, amely lehetővé teszi a Mongo DB API-hoz, és tekintse meg az SQL API-t vagy Cosmos DB gyűjtemények létrehozása, és szerkeszthetik a dokumentumokat a kód írása nélkül.

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"),
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Ha használ [Azure Cosmos DB átviteli protokoll támogatása a mongodb-hez](mongodb-introduction.md), használja a következő kapcsolati karakterláncot:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Meglévő eszközöket, például az [Azure DocumentDB Studiót](https://github.com/mingaliu/DocumentDBStudio) használhatja az Azure Cosmos DB Emulatorhoz való csatlakozáshoz. Az Azure Cosmos DB Emulator és az Azure Cosmos DB szolgáltatás között is migráltathat adatokat az [Azure Cosmos DB adatmigrálási eszközzel](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE]
> Ha a /Key lehetőséggel indította el az emulátort, akkor a létrehozott kulcsot használja a következő helyett: "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Az Azure Cosmos DB Emulator használatával alapértelmezés szerint legfeljebb 25 egypartíciós gyűjteményt vagy 1 particionált gyűjteményt hozhat létre. Az érték módosításáról további információért lásd: [A PartitionCount érték beállítása](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Az SSL-tanúsítvány exportálása

A .NET-nyelvek és a futtatókörnyezet a Windows tanúsítványtárolóval csatlakozik biztonságosan az Azure Cosmos DB helyi emulátorhoz. A többi nyelv saját módszert használ a tanúsítványok kezelésére és használatára. A Java saját [tanúsítványtárolót](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) használ, míg a Python [szoftvercsatorna-burkolókat](https://docs.python.org/2/library/ssl.html).

A Windows tanúsítványtárolóval nem integrálható nyelvekkel és futtatókörnyezetekkel használni kívánt tanúsítvány beszerzéséhez a Windows tanúsítványkezelővel kell exportálnia azt. Az elindításához futtassa a certlm.msc fájlt, vagy kövesse [Az Azure Cosmos DB Emulator tanúsítványok exportálása](./local-emulator-export-ssl-certificates.md) című cikkben szereplő részletes utasításokat. Amikor fut a tanúsítványkezelő, nyissa meg a személyes tanúsítványokat az alább látható módon, és exportálja a tanúsítványt a „DocumentDBEmulatorCertificate” rövid névvel BASE-64 kódolású X.509 (.cer) fájlként.

![Azure Cosmos DB helyi emulátor SSL-tanúsítványa](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány a Java tanúsítványtárolóba importálható a [Tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójához](https://docs.microsoft.com/azure/java-add-certificate-ca-store) című cikkben lévő utasításokkal. A tanúsítványt a tanúsítványtárolóba importálása után és az Azure Cosmos DB SQL API a mongodb-hez készült ügyfelek tud csatlakozni az Azure Cosmos DB Emulatort lesz.

Amikor Python és Node.js SDK-kból csatlakozik az emulátorhoz, az SSL-ellenőrzés le van tiltva.

## <a id="command-line"></a>Parancssori eszköz referenciája
A telepítés helyén a parancssorral elindíthatja és leállíthatja az emulátort, beállításokat konfigurálhat, és más műveleteket végezhet.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

A beállítások listájának megtekintéséhez írja be a `CosmosDB.Emulator.exe /?` parancsot a parancssorba.

|**Beállítás** | **Leírás** | **Parancs**| **Argumentumok**|
|---|---|---|---|
|[Nincsenek argumentumok] | Az alapértelmezett beállításokkal indítja el az Azure Cosmos DB Emulatort. |CosmosDB.Emulator.exe| |
|[Súgó] |Megjeleníti a támogatott parancssori argumentumok listáját.|CosmosDB.Emulator.exe /? | |
| GetStatus |Lekéri az Azure Cosmos DB Emulator állapotát. Az állapot jelzi a kilépési kód: 1-től kezdődően 2 = futó, 3 = = leállt. A negatív kilépési kód azt jelzi, hogy hiba történt. Nem jön létre más kimenet. | CosmosDB.Emulator.exe /GetStatus| |
| Leállítás| Bezárja az Azure Cosmos DB Emulatort.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Meghatározza az adatfájlok tárolására szolgáló elérési utat. Az alapértelmezett érték a %LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<adatútvonal\> | \<DataPath\>: Egy elérhető elérési útja |
|Port | Az emulátorhoz használni kívánt portszámot határozza meg. Az alapértelmezett érték: 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<Port\>: Egyetlen port száma |
| MongoPort | A MongoDB kompatibilitási API-hoz használni kívánt portszámot határozza meg. Az alapértelmezett érték: 10255. |CosmosDB.Emulator.exe /MongoPort = \<mongoport\>|\<mongoport\>: Egyetlen port száma|
| DirectPorts |A közvetlen kapcsolódáshoz használni kívánt portokat határozza meg. Az alapértelmezett értékek: 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<közvetlen portok\> | \<directports\>: 4 portok vesszővel tagolt listája |
| Kulcs |Az emulátor engedélyezési kulcsa. A kulcsnak 64 bites vektor base-64 kódolásának kell lennie. | CosmosDB.Emulator.exe /Key:\<kulcs\> | \<kulcs\>: A base-64 kódolását egy 64 bájt méretű vektor kulcsnak kell lennie|
| EnableRateLimiting | Megadja, hogy a kérelmek sebességét korlátozó viselkedés engedélyezve van. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Megadja, hogy a kérelmek sebességét korlátozó viselkedés le van tiltva. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Az emulátor felhasználói felületének megjelenítése nélkül. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Az adatkezelő nem jelenik meg az indításkor. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | A particionált gyűjtemények maximális számát határozza meg. További információkért lásd: [A gyűjtemények számának módosítása](#set-partitioncount). | CosmosDB.Emulator.exe /PartitionCount=\<partíciószám\> | \<partitioncount\>: Maximális száma a megengedett egypartíciós gyűjteményeket. Az alapértelmezett érték: 25. Maximálisan 250 engedélyezett.|
| DefaultPartitionCount| A particionált gyűjtemények partícióinak alapértelmezett számát határozza meg. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<alapértelmezett partíciószám\> | \<alapértelmezett partíciószám\> Az alapértelmezett érték: 25.|
| AllowNetworkAccess | Hozzáférést nyújt az emulátorhoz egy hálózaton keresztül. A /Key=\<kulcs_sztring\> vagy a /KeyFile=\<fájlnév\> parancsot is meg kell adnia a hálózati hozzáférés engedélyezéséhez. | CosmosDB.Emulator.exe AllowNetworkAccess /Key =\<key_string\> vagy CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile =\<fájlnév\>| |
| NoFirewall | Ne állítsa be a tűzfalszabályokat az /AllowNetworkAccess használatakor. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Új engedélyezési kulcsot készít, és a megadott fájlba menti azt. A létrehozott kulcs a /Key vagy a /KeyFile lehetőséggel használható. | CosmosDB.Emulator.exe /GenKeyFile =\<kulcsfájl elérési útja\> | |
| Konzisztencia | Állítsa be a fiók alapértelmezett konzisztenciaszintjét. | CosmosDB.Emulator.exe /Consistency=\<konzisztencia\> | \<Konzisztencia\>: Érték a következők egyikének kell lennie [konzisztenciaszintek](consistency-levels.md): Munkamenet, erős, végleges, vagy BoundedStaleness. Az alapértelmezett érték a munkamenet (session). |
| ? | A súgóüzenet megjelenítése.| | |

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
5. Indítsa el az emulátort a PartitionCount jelzővel egy <= 250 érték beállításával. Például: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Az emulátor vezérlése

Az emulátor egy PowerShell-modullal rendelkezik a szolgáltatás elindításához, leállításához, eltávolításához és állapotának lekéréséhez. A használatához:

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

Ezek ServiceControllerStatus érték egyikét adja vissza: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running vagy ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Szintaxis

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

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

Ezután kérje le az emulátor rendszerképét a Docker Hubról. Ehhez futtassa a következő parancsot azon a felületen, amelyet használni szokott.

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
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

A PowerShellből:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Az emulátor elindítása után az interaktív felület bezárása leállítja az emulátor tárolóját.

Az Adatkezelő megnyitásához nyissa meg a következő URL-címet a böngészőben. Az emulátor végpontja a fent látható válaszüzenetben van megadva.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Hibaelhárítás

A következő tippekkel háríthatja el az Azure Cosmos DB Emulatorral kapcsolatban tapasztalt hibákat:

- Ha az emulátor új verzióját telepítette, és hibákat tapasztal, állítsa vissza az adatokat. Az adatok visszaállításához kattintson a jobb gombbal a rendszertálcán lévő Azure Cosmos DB Emulator ikonra, majd kattintson az Adatok visszaállítása… lehetőségre. Ha ez nem segít a problémán, eltávolíthatja, majd újból telepítheti az alkalmazást. Útmutatásért lásd: [A helyi emulátor eltávolítása](#uninstall).

- Ha az Azure Cosmos DB Emulator összeomlik, gyűjtsön be összeomlási memóriaképfájlokat a c:\Users\felhasználónév\AppData\Local\CrashDumps mappából, tömörítse azokat, és küldje el csatolva az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre.

- Ha összeomlásokat tapasztal a CosmosDB.StartupEntryPoint.exe fájlban, futtassa a következő parancsot egy rendszergazdai parancssorból: `lodctr /R`

- Ha kapcsolódási problémát tapasztal, [gyűjtsön be profilelemzési fájlokat](#trace-files), majd tömörítse és küldje el azokat csatolva az [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) címre.

- Ha **A szolgáltatás nem érhető el** üzenetet kap, előfordulhat, hogy az emulátor nem tudja elindítani a hálózati vermet. Ellenőrizze, hogy telepítve van-e a Pulse Secure ügyfél vagy a Juniper Networks ügyfél, mert ezek hálózatszűrő illesztőprogramjai okozhatják a problémát. A külső gyártótól származó hálózatszűrő illesztőprogramok eltávolítása általában kijavítja a problémát.

- Amennyiben az emulátor futtatása közben a számítógép alvó állapotba lép vagy frissül az operációs rendszere, a **Szolgáltatás jelenleg nem érhető el** üzenet jelenik meg. Az emulátor alaphelyzetbe állításához kattintson a jobb gombbal a Windows értesítési tálcáján megjelenő ikonra, és válassza az **Adatok visszaállítása** lehetőséget.

### <a id="trace-files"></a>Profilelemzési fájlok gyűjtése

Hibakeresési nyomok begyűjtéséhez futtassa a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Figyelje meg a rendszertálcán, hogy a program leállt-e, mert ez egy percig is eltarthat. Azt is megteheti, hogy rákattint az Azure Cosmos DB Emulator felhasználói felületén lévő **Kilépés** gombra.
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
4. Az alkalmazás eltávolításakor lépjen a `C:\Users\<user>\AppData\Local\CosmosDBEmulator` mappához, és törölje ki.

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

Ebben az oktatóanyagban azt ismertettük, hogyan használhatja a helyi emulátort ingyenes helyi fejlesztési feladatokhoz. Most továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan exportálhatja az emulátor SSL-tanúsítványait.

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB Emulator tanúsítványok exportálása](local-emulator-export-ssl-certificates.md)
