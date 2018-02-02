---
title: "Helyileg Azure Cosmos DB emulátorral kidolgozása |} Microsoft Docs"
description: "Az Azure Cosmos DB Emulator használatával, fejlesztése és tesztelése az alkalmazás helyileg szabad, Azure-előfizetés létrehozása nélkül."
services: cosmos-db
documentationcenter: 
keywords: "Az Azure Cosmos DB emulátor"
author: David-Noble-at-work
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: danoble
ms.openlocfilehash: daaa628fae3e495a0c9c7a3c74e643caa56fb18b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Az Azure Cosmos DB Emulator használja a helyi fejlesztéshez és teszteléshez

<table>
<tr>
  <td><strong>Bináris fájlok</strong></td>
  <td>[MSI-fájl letöltése](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker központ](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker-forrás</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
Az Azure Cosmos DB Emulator emulálja a Azure Cosmos DB szolgáltatás fejlesztési célokra szolgál egy helyi környezet biztosít. Az Azure Cosmos DB Emulator használatával, létrehozhatja és helyileg, az alkalmazás tesztelése egy Azure-előfizetés létrehozása, és ezzel járó költségeket nélkül. Ha elégedett az alkalmazás az Azure Cosmos DB emulátorban alakulását, átválthat a felhőben Azure Cosmos DB fiókkal.

> [!NOTE]
> Jelenleg az adatkezelő az emulátorban csak teljes mértékben támogatja az SQL API-gyűjtemények és a MongoDB-gyűjtemény. Tábla Graph és Cassandra tárolók jelenleg nem teljes mértékben támogatottak. 

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az emulátor telepítése
> * Kérések hitelesítése
> * Az adatkezelő használatával az emulátorban
> * SSL-tanúsítványok exportálása
> * Az emulátor hívja a parancssorból
> * Az emulátor futó Windows Docker
> * Nyomkövetési fájlok összegyűjtése
> * Hibaelhárítás

Azt javasoljuk, hogy Kezdésként tekintse meg az alábbi videót, amely Kirill Gavrylyuk mutatja Ismerkedés az Azure Cosmos DB emulátorral. Vegye figyelembe, hogy a videó az emulátor, mint ahol a DocumentDB-emulátor hivatkozik, de maga az eszköz lett az Azure Cosmos DB Emulator átnevezi a videó taping óta. A videó utalások az Azure Cosmos DB Emulator pontosak. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Az emulátor működése
Az Azure Cosmos DB Emulator egy valósághű emuláció a Azure Cosmos DB szolgáltatást biztosít. Azure Cosmos DB, például létrehozása és a JSON-dokumentumok lekérdezését kiépítés azonos funkciókat támogatja, és gyűjtemények skálázás, és végrehajtása tárolt eljárásokként és eseményindítókként. Fejlesztés és tesztelje az alkalmazásokat az Azure Cosmos DB Emulator használatával, és telepítheti őket az Azure-ba, globális méretű azáltal, hogy csak a módosítsa a csatlakozási végpont az Azure Cosmos DB egyetlen konfigurációja.

Létrehoztunk egy valósághű helyi emuláció a tényleges Azure Cosmos DB szolgáltatást, amíg az Azure Cosmos DB Emulator végrehajtásának eltér attól, hogy a szolgáltatás. Például a Azure Cosmos DB Emulator szabványos operációs rendszer összetevők például a helyi fájlrendszerben; adatmegőrzési, és a HTTPS protokollhoz tartozó, a hálózati kapcsolatot. Ez azt jelenti, hogy bizonyos funkciók, amely az Azure infrastruktúra például globális replikációs, egyjegyű ezredmásodperces késési olvasása/írása, és beállítható konzisztenciaszinteket nem érhetők el az Azure Cosmos DB Emulator támaszkodik.

## <a name="differences-between-the-emulator-and-the-service"></a>Az emulátor és a szolgáltatás közötti különbségek 
Az Azure Cosmos DB Emulator egy helyi fejlesztői munkaállomáson fut emulált környezetet biztosít, mivel számos bizonyos funkciók és közötti különbségek az emulátor Azure Cosmos DB fiók a felhőben.

* Az Azure Cosmos DB Emulator csak egyetlen rögzített fiók és a jól ismert főkulcs támogatja.  Kulcs újragenerálása nincs lehetőség az Azure Cosmos DB-emulátoron.
* Az Azure Cosmos DB Emulator nem egy méretezhető szolgáltatás, és nem fogja támogatni a nagy mennyiségű gyűjteményhez.
* Az Azure Cosmos DB Emulator nem szimulálása különböző [Azure Cosmos DB konzisztenciaszintek](consistency-levels.md).
* Az Azure Cosmos DB Emulator nem szimulálása [több területi replikációs](distribute-data-globally.md).
* Az Azure Cosmos DB Emulator nem támogatja a szolgáltatás kvóta felülbírálások elérhető az Azure Cosmos DB szolgáltatásban (a dokumentum méretkorlátait, nagyobb particionált gyűjtemény tároló).
* Kérjük, mert az Azure Cosmos DB Emulator példányának esetleg nem naprakészek legyenek a legutóbbi változtatásokat az Azure Cosmos DB szolgáltatással, [Azure Cosmos DB kapacitás planner](https://www.documentdb.com/capacityplanner) pontosan a termelési átviteli sebesség (RUs) igényeit becsléséhez a az alkalmazás.

## <a name="system-requirements"></a>Rendszerkövetelmények
Az Azure Cosmos DB Emulator a következő hardver- és követelményekkel rendelkezik:

* Szoftverkövetelmények
  * Windows Server 2012 R2, Windows Server 2016 vagy Windows 10
*   Minimális hardverkövetelmények
  * 2 GB RAM
  * 10 GB szabad lemezterület

## <a name="installation"></a>Telepítés
Töltse le, és telepítse az Azure-Cosmos DB az emulátort a [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) vagy az emulátor Docker for Windows futtathatja. A Docker for Windows emulátorral, lásd: [Docker futó](#running-on-docker). 

> [!NOTE]
> Telepítését, konfigurálását és az Azure Cosmos DB Emulator futtassa, hogy a számítógépen rendszergazdai jogosultsággal kell rendelkeznie.

## <a name="running-on-windows"></a>A Windows rendszeren futó

Indítsa el az Azure Cosmos DB emulátort, kattintson a Start gombra, vagy nyomja meg a Windows billentyűt. Írja be a szöveget **Azure Cosmos DB emulátor**, válassza ki az emulátor az alkalmazások listáját. 

![Kattintson a Start gombra, vagy nyomja meg a Windows billentyűt, írja be a szöveget ** Azure Cosmos DB emulátor **, és válassza ki az emulátor alkalmazások listája](./media/local-emulator/database-local-emulator-start.png)

Amikor az emulátor fut, a Windows tálca értesítési területén egy ikon látható. ![Az Azure Cosmos DB helyi emulátor értesítési](./media/local-emulator/database-local-emulator-taskbar.png)

Az Azure Cosmos DB Emulator alapértelmezés szerint a helyi számítógépen ("localhost") porton 8081 fut.

Az Azure Cosmos DB Emulator alapértelmezés szerint telepítve van a `C:\Program Files\Azure Cosmos DB Emulator` könyvtár. Is elindíthatja, és állítsa le az emulátor parancsot a parancssorból. Lásd: [parancssori eszköz](#command-line) további információt.

## <a name="start-data-explorer"></a>Adatok Explorer elindítása

Az Azure Cosmos DB emulátor indításakor automatikusan megnyitja az Azure Cosmos DB adatkezelő a böngészőben. A cím jelenik meg [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Zárja be a Explorert, és szeretné nyissa meg újra később, ha a böngészőben nyissa meg az URL-címet, vagy indítsa el a Windows tálcaikon Azure Cosmos DB emulátorától alább látható módon.

![Az Azure Cosmos DB helyi emulátor data explorer indítója](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Frissítések keresése
Adatkezelő azt jelzi, ha van egy új frissítés elérhető letöltésre. 

> [!NOTE]
> Az Azure Cosmos DB Emulator egy verziójában létrehozott adatokat elérhetőnek kell lennie egy másik verzió használata esetén nem garantált. Ha szeretné megőrizni az adatokat a hosszú távú, ajánlott tárolja ezeket az adatokat egy Cosmos-DB Azure-fiók, nem pedig az Azure Cosmos DB-emulátoron. 

## <a name="authenticating-requests"></a>Kérések hitelesítése
Ahogy az Azure Cosmos DB a felhőben az Azure Cosmos DB emulatorban végrehajtott kérelmek hitelesíteni kell. Az Azure Cosmos DB Emulator főkulcs hitelesítéshez ugyanazt a rögzített fiókot és egy jól ismert hitelesítési kulcs támogatja. A fiók és a kulcs az Azure Cosmos DB Emulator való használatra engedélyezett csak hitelesítő adatait. Ezek a következők:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Az Azure Cosmos DB emulátor által támogatott főkulcs csak az emulátorban való használatra készült. Az éles Azure Cosmos DB fiókot és kulcsot az Azure Cosmos DB Emulator nem használható. 

> [!NOTE] 
> Ha a /Key beállítással használatba vette az emulátor, ahelyett, hogy a létrehozott kulcs használja "C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw =="

Emellett közvetlenül az Azure Cosmos DB szolgáltatást az Azure Cosmos DB Emulator csak biztonságos kommunikációhoz SSL-en keresztüli támogatja.

## <a name="running-on-a-local-network"></a>A helyi hálózaton fut

Az emulátor egy helyi hálózaton is futtathatja. Hálózati hozzáférés engedélyezéséhez adja meg a /AllowNetworkAccess beállítás jelenleg a [parancssori](#command-line-syntax), ami megköveteli azt is meg kell adnia /Key = key_string vagy /KeyFile = fájlnév. Használhatja a /GenKeyFile = fájlnév hozott létre a fájlt az előzetes megfizetése esetén egy véletlenszerű kulcsot.  Ezt követően, hogy a /KeyFile = fájlnév vagy /Key átadhatók = contents_of_file.

A hálózati hozzáférés engedélyezéséhez először a felhasználó az emulátor leállítási kell, és törölje az emulátor adatkönyvtára (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>A emulátorral fejlesztése
Miután az Azure Cosmos DB-emulátort az asztalon, használata támogatott [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) vagy a [Azure Cosmos DB REST API](/rest/api/documentdb/) az emulátor kommunikál. Az Azure Cosmos DB Emulator egy beépített adatkezelő, amely lehetővé teszi az SQL- és MongoDB API-kat, és tekintse meg a gyűjtemények dokumentumok létrehozásához és szerkesztéséhez programozás nélkül is.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Ha használ [Azure Cosmos DB protokoll támogatása mongodb](mongodb-introduction.md), használja a következő kapcsolati karakterlánc:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Használhatja például a meglévő eszközöket [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) az Azure Cosmos DB Emulator való kapcsolódáshoz. Is áttelepítheti az adatokat az Azure Cosmos DB Emulator és az Azure Cosmos DB szolgáltatás használata között a [Azure Cosmos DB adatáttelepítési eszköz](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Ha a /Key beállítással használatba vette az emulátor, ahelyett, hogy a létrehozott kulcs használja "C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw =="

Alapértelmezés szerint az Azure Cosmos DB emulátor használva létrehozhat akár 25 az egypartíciós gyűjtemények vagy 1 particionált gyűjtemény. Ez az érték módosításával kapcsolatos további információkért lásd: [PartitionCount értékre állítja](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Az SSL-tanúsítvány exportálása

.NET-nyelveket és futásidejű Windows tanúsítványtárolóinak használatára való biztonságos kapcsolódás a helyi Azure Cosmos DB-emulátor. Nyelvek kezelése és tanúsítványok segítségével a saját metódus rendelkezik. Java használ a saját [tanúsítványtároló](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) mivel Python használ [burkolók szoftvercsatorna](https://docs.python.org/2/library/ssl.html).

Ahhoz, hogy az beszerzése a nyelvet és a futtatókörnyezetek, amely integrálható a Windows tanúsítványtároló használandó tanúsítványt kell exportálni kell a Windows tanúsítványkezelő használata. Indítsa el a certlm.msc futtatásával, vagy részletes utasításait [Azure Cosmos DB emulátor tanúsítványok exportálása](./local-emulator-export-ssl-certificates.md). Miután a Tanúsítványkezelő fut, nyissa meg a személyes tanúsítványok a lent látható módon, és exportálása "DocumentDBEmulatorCertificate" rövid nevű tanúsítványra, egy BASE-64 kódolású X.509 (.cer) fájlba.

![Az Azure Cosmos DB helyi emulátor SSL-tanúsítvány](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Az X.509 tanúsítvány található útmutatást követve importálható Java tanúsítványtároló [tanúsítvány hozzáadása a Java hitelesítésszolgáltató tanúsítványtárolójában](https://docs.microsoft.com/azure/java-add-certificate-ca-store). A tanúsítványt a tanúsítványtárolóba importálása után Java és a MongoDB alkalmazások fog tudni csatlakozni az Azure Cosmos DB Emulator.

Amikor az emulátor csatlakozik a Python és a Node.js SDK-k, SSL-ellenőrzést le van tiltva.

## <a id="command-line"></a>Parancssori eszköz
A telepítési helyről használhatja a parancssori indítsa el és állítsa le az emulátor, beállításokat és egyéb műveleteket végezhet.

### <a name="command-line-syntax"></a>Parancssori szintaxis

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Beállítások listájának megtekintéséhez írja be a `CosmosDB.Emulator.exe /?` parancsot a parancssorba.

<table>
<tr>
  <td><strong>A beállítás</strong></td>
  <td><strong>Leírás</strong></td>
  <td><strong>A parancs</strong></td>
  <td><strong>Argumentumok</strong></td>
</tr>
<tr>
  <td>[Nincs argumentumok]</td>
  <td>Az Azure Cosmos DB Emulator alapértelmezett beállításokkal indul.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Súgó]</td>
  <td>Támogatott parancssori argumentumok listájának megjelenítése.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Leállítás</td>
  <td>Az Azure Cosmos DB Emulator leáll.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Meghatározza az adatforrás adatfájljainak tárolására használt elérési utat. Alapértelmezett érték a % LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;DataPath&gt;: egy elérhető elérési útja</td>
</tr>
<tr>
  <td>Port</td>
  <td>Adja meg a portszámot az emulátor használandó.  Alapértelmezés szerint 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: egy portszám</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Adja meg a MongoDB kompatibilitási API használandó portszámot. Alapértelmezés szerint 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: egy portszám</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Adja meg a portokat a közvetlen kapcsolatot használ. Alapértelmezett 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: 4 portok vesszővel tagolt listája</td>
</tr>
<tr>
  <td>Kulcs</td>
  <td>Az emulátor hitelesítési kulcs. Kulcsnak base 64 kódolás egy 64 bájtos vektor kell lennie.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;kulcs&gt;</td>
  <td>&lt;kulcs&gt;: base 64 kódolás egy 64 bájtos vektor kulcsnak kell lennie</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Megadja, hogy korlátozza az viselkedés kérelmek aránya engedélyezve van.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Meghatározza, hogy korlátozza az viselkedés kérelmek aránya le van tiltva.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Ne jelenjen meg az emulátor felhasználói felületét.</td>
  <td>CosmosDB.Emulator.exe/noui</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Ne jelenjen meg indításkor dokumentumkezelő.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>A particionált gyűjtemények maximális számát határozza meg. Lásd: [gyűjtemények számának módosítása](#set-partitioncount) további információt.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: maximális száma az egypartíciós gyűjtemények engedélyezett. Alapértelmezett érték 25. Megengedett legnagyobb érték 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>A particionált gyűjtemény partíciók alapértelmezett számát adja meg.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; alapértelmezett érték 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Lehetővé teszi a hozzáférést az emulátor egy hálózaton keresztül. Is át kell /Key =&lt;key_string&gt; vagy /KeyFile =&lt;Fájlnév&gt; a hálózati hozzáférés engedélyezéséhez.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>vagy<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Ne állítsa be úgy a tűzfalszabályok /AllowNetworkAccess használata esetén.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Hozzon létre egy új hitelesítési kulcsot, és a megadott fájlba történő mentését. A létrehozott kulcs /Key vagy /KeyFile kapcsolókkal használható.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile =&lt;kulcs fájljának elérési útja&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konzisztencia</td>
  <td>A fiók az alapértelmezett konzisztencia szintjének beállítása.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;konzisztencia&gt;: értéknek kell lennie a következő [konzisztenciaszintek](consistency-levels.md): munkamenet, erős, Eventual vagy BoundedStaleness.  Az alapértelmezett érték: a munkamenet.</td>
</tr>
<tr>
  <td>?</td>
  <td>A Súgó üzenet megjelenítése.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Gyűjtemények számának módosítása

Alapértelmezés szerint legfeljebb 25 az egypartíciós gyűjtemények, vagy az Azure Cosmos DB Emulator használatával 1 particionált gyűjtemény hozhat létre. Módosítja a **PartitionCount** érték, létrehozhat legfeljebb 250 az egypartíciós gyűjtemények vagy 10 particionált gyűjtemények, illetve a legfeljebb 250 egyetlen partíciók két bármilyen kombinációját (ahol 1 particionált gyűjtemény = 25 az egypartíciós gyűjtemény).

Gyűjtemény létrehozása után az aktuális partíciók száma túl lett lépve kísérli meg, ha az emulátorban ServiceUnavailable kivételt, a következő üzenetet okoz.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

A rendelkezésre álló gyűjtemények száma az Azure Cosmos DB Emulator módosításához tegye a következőket:

1. Törli az összes helyi Azure Cosmos DB emulátor adatokat kattintson a jobb gombbal a **Azure Cosmos DB emulátor** ikonra a tálcán, majd kattintson az **adatok alaphelyzetbe állítása...** .
2. Ez a mappa C:\Users\user_name\AppData\Local\CosmosDBEmulator összes emulátor adatok törlése.
3. Minden nyitott példányokat kilépéshez kattintson a jobb gombbal a **Azure Cosmos DB emulátor** ikonra a tálcán, majd kattintson az **kilépési**. Az összes példányhoz való kilépéshez egy percet is igénybe vehet.
4. Telepítse a legújabb verzióját a [Azure Cosmos DB emulátor](https://aka.ms/cosmosdb-emulator).
5. Indítsa el az emulátor PartitionCount jelzővel úgy, hogy egy érték < = 250. Például: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="running-on-docker"></a>A Docker fut

Az Azure Cosmos DB Emulator Docker a Windows rendszeren futtatható. Az emulátor nem működnek a Docker az Oracle Linux.

Ha már [Docker for Windows](https://www.docker.com/docker-windows) telepítve, kattintson a jobb gombbal az eszköztár a Docker ikonjára, majd válassza a Windows-tárolók váltani **Windows tárolók váltani**.

A következő lekérés az emulátor rendszerképe a Docker Hub a kedvenc rendszerhéj a következő parancs futtatásával.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
A lemezkép elindításához futtassa a következő parancsokat.

A parancssorból:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A Powershellből:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A válasz a következőhöz hasonlít:

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

Most a végpont és a válaszban szereplő található főkulcs használata az ügyfél és az SSL-tanúsítvány importálása a gazdagépen. Az SSL-tanúsítvány importálásához hajtsa végre a következő parancsot egy rendszergazdai parancssort:

```
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Leáll az interaktív rendszerhéj bezárja az emulátor követően elindult az emulátor tároló.

Nyissa meg az adatkezelő navigáljon a böngészőben a következő URL-címet. Az emulátor végpont a fenti válaszüzenetben valósul meg.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Hibaelhárítás

A következő tippek segítségével megoldhatja a problémákat tapasztal, az Azure Cosmos DB emulátorral:

- Ha az emulátor új verzióját, és hibákat tapasztal, győződjön meg arról, alaphelyzetbe állítja az adatokat. Visszaállíthatja az adatokat, kattintson a jobb gombbal a tálcán a Azure Cosmos DB emulátor ikonjára, majd adatok alaphelyzetbe állítása... Ha nem, javítsa a hibákat, távolítsa el, és telepítse újra az alkalmazást. Lásd: [távolítsa el a helyi emulátor](#uninstall) utasításokat.

- Ha az Azure Cosmos DB emulátor összeomlik, memóriaképek összegyűjtése c:\Users\user_name\AppData\Local\CrashDumps mappából, tömörítéssel, és csatolja azokat egy e-mailek [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Ha összeomlik CosmosDB.StartupEntryPoint.exe, futtassa a következő parancsot egy rendszergazdai parancssorból:`lodctr /R` 

- Ha egy hálózati probléma, [nyomkövetési fájlok összegyűjtése](#trace-files), tömörítéssel, és csatolja azokat egy e-mailek [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Ha megjelenik egy **szolgáltatás nem érhető el** üzenet, a hálózati verem inicializálni az emulátor valószínűleg hibás. Ellenőrizze, hogy van-e a Pulse secure ügyfél vagy Juniper hálózatok telepített, mert a hálózati fájlrendszerszűrő-illesztőprogramok a problémát okozhatja. Harmadik féltől származó hálózati fájlrendszerszűrő-illesztőprogramok eltávolítása általában megoldja a problémát.

### <a id="trace-files"></a>Nyomkövetési fájlok gyűjtése

Hibakeresési nyomkövetési adatokat gyűjteni a következő parancsokat egy rendszergazdai parancssorból:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. A tálcán, győződjön meg arról, hogy a program a figyelés leállt, egy percig is eltarthat. Csak is kattinthat **kilépési** a Azure Cosmos DB emulator felhasználói felületén.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. A probléma reprodukálásához szükséges. Data Explorer nem működik, ha csak szeretné várja meg a böngészőben nyissa meg a hiba tényleges néhány másodpercig.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navigáljon a `%ProgramFiles%\Azure Cosmos DB Emulator` docdbemulator_000001.etl fájlt.
7. Az .etl fájl együtt Reprodukálja a szükséges további lépéseket küldési [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) a hibakereséshez.

### <a id="uninstall"></a>Távolítsa el a helyi emulátor

1. Minden nyitott példányokat a helyi emulátor bezárásához kattintson a jobb gombbal a tálcán a Azure Cosmos DB emulátor ikonjára, majd a kilépési. Az összes példányhoz való kilépéshez egy percet is igénybe vehet.
2. A Windows keresési mezőbe, írja be a **alkalmazások és szolgáltatások** , majd kattintson a a **alkalmazások és szolgáltatások (rendszerbeállítások)** eredménye.
3. Az alkalmazások listájának, görgessen **Azure Cosmos DB emulátor**, válassza ki azt, kattintson a **Eltávolítás**, majd erősítse meg, és kattintson a **Eltávolítás** újra.
4. Amikor a rendszer eltávolítja az alkalmazást, navigáljon a C:\Users\<felhasználó > \AppData\Local\CosmosDBEmulator, törölje a mappát. 

## <a name="change-list"></a>A változások listája

A verziószám ellenőrizheti a jobb gombbal a tálcán helyi emulátor ikonjára kattintva, majd kattintson a menüpont kapcsolatban.

### <a name="120-released-on-january-26-2018"></a>1.20 2018 január 26 kiadása

* A MongoDB-összesítési feldolgozási folyamat alapértelmezés szerint engedélyezett.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * A helyi emulátor telepítve
> * VÉL a Docker Windows emulátor
> * Hitelesített kéréseknél
> * Az adatkezelő használja az emulátorban
> * Exportált SSL-tanúsítványok
> * Az emulátor hívása a parancssorból
> * Összegyűjtött nyomkövetési fájlokat

Ebben az oktatóanyagban már megismerte a helyi emulátor ingyenes helyi fejlesztési célra. Most már továbbléphet a következő oktatóanyag, és megtudhatja, hogyan emulátor SSL-tanúsítványok exportálása. 

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB emulátor tanúsítványok exportálása](local-emulator-export-ssl-certificates.md)
