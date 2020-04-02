---
title: Az Azure Cosmos DB teljesítménytippjei a .NET használatához
description: Ismerje meg az ügyfél konfigurációs lehetőségeket az Azure Cosmos DB teljesítményének javítása érdekében.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546078"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Az Azure Cosmos DB és a .NET teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen skálázható a garantált késéssel és átviteli kapcsolattal. Nem kell jelentős architektúra-módosításokat végrehajtania, vagy összetett kódot írnia az adatbázis azure Cosmos DB-vel való méretezéséhez. Fel- és leskálázás a könnyű, mint egyetlen API-hívás. További információ: [a tárolóátviteli-ék kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis-átviteli átbocsátás kiépítése.](how-to-provision-database-throughput.md) Mivel azonban az Azure Cosmos DB hálózati hívásokon keresztül érhető el, ügyféloldali optimalizálások érhetők el az [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)használatakor a csúcsteljesítmény elérése érdekében.

Ha tehát javítani szeretné az adatbázis teljesítményét, vegye figyelembe az alábbi lehetőségeket:

## <a name="hosting-recommendations"></a>Hosting ajánlások

**A lekérdezésigényes munkaterhelések esetén linuxos vagy Windows 32 bites gazdagép-feldolgozás helyett windows os 64 bites**

A jobb teljesítmény érdekében a Windows 64 bites gazdagép-feldolgozást javasoljuk. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. Linux és más nem támogatott platformok, ahol ServiceInterop.dll nem érhető el, egy további hálózati hívás történik az átjáróhoz az optimalizált lekérdezés lekérni. A következő típusú alkalmazások alapértelmezés szerint 32 bites gazdagép-feldolgozást használnak. Ha az állomás feldolgozását 64 bites feldolgozásra szeretné módosítani, kövesse az alábbi lépéseket az alkalmazás típusától függően:

- Végrehajtható alkalmazások esetén módosíthatja az állomás feldolgozását, ha a [platformcélt](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) **x64-re** állítja a **Projekt tulajdonságai** ablakban, a **Build** lapon.

- VsTest-alapú tesztprojektek esetén a Visual Studio **Test menüBen** a **Test** > Settings Default Processor Architecture **(Tesztbeállítások** > **alapértelmezett processzorarchitektúrájának x64- ként)** lehetőségkiválasztásával módosíthatja az állomás feldolgozását.

- A helyileg telepített ASP.NET webalkalmazások esetében módosíthatja a gazdagép feldolgozását, ha **az IIS Express 64 bites verziójának használata webhelyekhez és projektekhez** lehetőséget választja az **Eszközök** > **beállításai** > **projektek és megoldások** > **webes projektek**csoportban.

- Az Azure-ban telepített ASP.NET webalkalmazások esetében módosíthatja a gazdagép feldolgozását, ha kiválasztja a **64 bites** platformot az **Azure Portalalkalmazás-beállításokban.**

> [!NOTE] 
> Alapértelmezés szerint az új Visual Studio-projektek **beállítása Bármely CPU**. Javasoljuk, hogy állítsa a projektet **x64-re,** hogy ne váltson **x86-ra.** A Bármely **CPU-ra** beállított projekt könnyen átválthat **x86-ra,** ha csak x86-függőséget ad hozzá.<br/>
> A ServiceInterop.dll fájlnak abban a mappában kell lennie, amelyből az SDK DLL végrehajtása folyamatban van. Ez csak akkor lehet probléma, ha manuálisan másolja a DL-eket, vagy egyéni buildelési/központi telepítési rendszerekkel rendelkezik.
    
**Kiszolgálóoldali szemétgyűjtés bekapcsolása (GC)**

A szemétgyűjtés gyakoriságának csökkentése bizonyos esetekben segíthet. A .NET rendszerben állítsa `true`a [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) rendszert .

**Az ügyfél-munkaterhelés horizontális felskálázása**

Ha nagy átviteli sebességű (több mint 50 000 RU/s) sebességgel tesztel, az ügyfélalkalmazás szűk keresztmetszetté válhat a processzor vagy a hálózati kihasználtság miatt. Ha eléri ezt a pontot, továbbra is leküldéses az Azure Cosmos DB-fiók további horizontális felskálázása az ügyfélalkalmazások több kiszolgálók között.

> [!NOTE] 
> A magas processzorhasználat fokozott késést okozhat, és időkérési kivételeket kérhet.

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

**Csatlakozási házirend: Közvetlen kapcsolati mód használata**

Hogyan csatlakozik egy ügyfél az Azure Cosmos DB-hez fontos teljesítménybeli következményekkel jár, különösen a megfigyelt ügyféloldali késés. Az ügyfélkapcsolati házirend konfigurálásához két kulcskonfigurációs beállítás áll rendelkezésre: a *kapcsolatmód* és a *kapcsolatprotokoll*.  A két rendelkezésre álló mód a következő:

   * Átjáró mód
      
     Az átjárómód minden SDK-platformon támogatott, és a [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md)konfigurált alapértelmezett beállítása. Ha az alkalmazás fut egy vállalati hálózaton szigorú tűzfalkorlátozások, átjáró mód a legjobb választás, mert a szabványos HTTPS-port ot és egyetlen végpontot használ. A teljesítmény kompromisszumot, azonban az átjáró mód magában foglalja egy további hálózati ugrás minden alkalommal, amikor az adatok olvasása vagy írása az Azure Cosmos DB. Így a közvetlen mód jobb teljesítményt nyújt, mivel kevesebb a hálózati ugrás. Javasoljuk az átjárókapcsolati módot is, ha korlátozott számú szoftvercsatorna-kapcsolattal rendelkező környezetben futtat alkalmazásokat.

     Ha az SDK-t használja az Azure Functions ben, különösen a [felhasználási tervben,](../azure-functions/functions-scale.md#consumption-plan)vegye figyelembe a kapcsolatok ra vonatkozó jelenlegi [korlátokat.](../azure-functions/manage-connections.md) Ebben az esetben az átjáró mód jobb lehet, ha az Azure Functions alkalmazáson belül más HTTP-alapú ügyfelekkel is dolgozik.

   * Közvetlen mód

     A Direct mód támogatja a TCP protokollon keresztüli kapcsolatot, és a [Microsoft.Azure.Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md)használata esetén ez az alapértelmezett csatlakozási mód.

Átjáró módban az Azure Cosmos DB a 443-as portot használja, és az 10250-es, 10255-ös és 10256-os portokat használja, ha a MongoDB-hoz készült Azure Cosmos DB API-t használja. Az 10250-es port egy alapértelmezett MongoDB-példányra rendel i georeplikáció nélkül. Az 10255-ös és az 10256-os portok leképezik a georeplikációval rendelkező MongoDB-példányt.
     
Ha a TCP-t közvetlen módban használja, az átjáróportok mellett biztosítania kell, hogy az 10000 és 20000 közötti porttartomány meg legyen nyitva, mivel az Azure Cosmos DB dinamikus TCP-portokat használ. Ha ezek a portok nincsenek megnyitva, és megpróbálja használni a TCP-t, 503-as szolgáltatás nem érhető el hibaüzenet jelenik meg. Ez a táblázat a különböző API-khoz elérhető kapcsolódási módokat és az egyes API-khoz használt szolgáltatásportokat mutatja be:

|Kapcsolat mód  |Támogatott protokoll  |Támogatott SDK-k  |API/szolgáltatás portja  |
|---------|---------|---------|---------|
|Átjáró  |   HTTPS    |  Minden SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Táblázat (443), Cassandra (10350), Graph (443)    |
|Direct    |     TCP    |  .NET SDK    | Az 10000 és 20000 közötti tartományportjai |

Az Azure Cosmos DB egy egyszerű, nyílt RESTful programozási modellhttps-en keresztül kínál. Emellett hatékony TCP protokollt is kínál, amely kommunikációs modelljében is RESTful, és a .NET ügyfél SDK-n keresztül érhető el. A TCP protokoll tls-t használ a kezdeti hitelesítéshez és a forgalom titkosításához. A legjobb teljesítmény érdekében ha lehetséges, használja a TCP protokollt.

Az SDK V3 esetében a példány létrehozásakor konfigurálja a csatlakozási módot a `CosmosClient` alkalmazásban. `CosmosClientOptions` Ne feledje, hogy a közvetlen mód az alapértelmezett.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

A Microsoft.Azure.DocumentDB SDK esetében a beállítási mód `DocumentClient` a példány `ConnectionPolicy` létrehozása során a paraméter használatával. Ha közvetlen módot használ, a `Protocol` `ConnectionPolicy` paraméter használatával is beállítható.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Mivel a TCP csak közvetlen módban támogatott, átjáró módban a HTTPS protokoll mindig az `Protocol` átjáróval való kommunikációra szolgál, és a rendszer figyelmen kívül hagyja a benne lévő `ConnectionPolicy` értéket.

![Az Azure Cosmos DB kapcsolati szabályzata](./media/performance-tips/connection-policy.png)

**OpenAsync hívása az indítási késés elkerülése érdekében az első kérésre**

Alapértelmezés szerint az első kérelem nagyobb késést, mert be kell olvasnia a cím-útválasztási tábla. Ha [az SDK V2-t](sql-api-sdk-dotnet.md)használja, az inicializálás során egyszer hívja meg `OpenAsync()` az indítási késést az első kérésre:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`kéréseket hoz létre a fiók összes tárolójának cím-útválasztási táblájának beszerzésére. Olyan fiókok esetében, amelyek sok tárolót tartalmaznak, `OpenAsync` de amelyek alkalmazása egy részhalmazukhoz fér hozzá, szükségtelen mennyiségű forgalmat hozna létre, ami lelassítaná az inicializálást. Így `OpenAsync` a használata nem lehet hasznos ebben a forgatókönyvben, mert lelassítja az alkalmazás indításakor.

   <a id="same-region"></a>
**A teljesítmény érdekében helyezze el az ügyfeleket ugyanabban az Azure-régióban**

Ha lehetséges, helyezzen el minden olyan alkalmazást, amely az Azure Cosmos DB-t hívja meg ugyanabban a régióban, mint az Azure Cosmos DB adatbázisát. Íme egy hozzávetőleges összehasonlítás: az Azure Cosmos DB-hez való hívások ugyanabban a régióban 1 és 2 ms között befejeződnek, de az USA nyugati és keleti partja közötti késés több mint 50 ms. Ez a késés kérésről kérelemre változhat, attól függően, hogy a kérelem milyen útvonalon halad át az ügyfélről az Azure adatközpont határára. A lehető legalacsonyabb késést kaphatja meg, ha biztosítja, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB-végpont. Az elérhető régiók listáját az [Azure-régiók ban.](https://azure.microsoft.com/regions/#services)

![Az Azure Cosmos](./media/performance-tips/same-region.png) DB kapcsolati szabályzata<a id="increase-threads"></a>

**Szálak/feladatok számának növelése**

Mivel az Azure Cosmos DB-hívások a hálózaton keresztül történnek, előfordulhat, hogy módosítania kell a kérelmek párhuzamosságának mértékét, hogy az ügyfélalkalmazás minimális várakozási időt töltsön a kérelmek között. Ha például a . [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx)

**Gyorsított hálózatkezelés engedélyezése**
 
 A késés és a CPU-vibrálás csökkentése érdekében azt javasoljuk, hogy engedélyezze a gyorsított hálózatkezelést az ügyfél virtuális gépeken. Lásd: [Windows virtuális gép létrehozása gyorsított hálózatkezeléssel,](../virtual-network/create-vm-accelerated-networking-powershell.md) vagy [Linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel.](../virtual-network/create-vm-accelerated-networking-cli.md)

## <a name="sdk-usage"></a>SDK-használat
**A legújabb SDK telepítése**

Az Azure Cosmos DB SDK-k folyamatosan fejlesztik, hogy a legjobb teljesítményt. Tekintse meg az [Azure Cosmos DB SDK-lapok](sql-api-sdk-dotnet-standard.md) a legújabb SDK meghatározásához és tekintse át a fejlesztéseket.

**Adatfolyam-API-k használata**

[A .NET SDK V3](sql-api-sdk-dotnet-standard.md) olyan adatfolyam-API-kat tartalmaz, amelyek szerializálás nélkül is képesek fogadni és visszaadni az adatokat. 

Középső rétegbeli alkalmazások, amelyek nem használnak fel válaszokat közvetlenül az SDK-ból, de továbbítja azokat más alkalmazásszintekre is élvezhetik a stream API-k előnyeit. Tekintse meg a [cikk-kezelési](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) minták példákat az adatfolyam-kezelés.

**Egyetlen Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama alatt**

Mindegyik `DocumentClient` `CosmosClient` és példány szálbiztos, és hatékony kapcsolatkezelést és címgyorsítótárazást hajt végre, ha közvetlen módban működik. A hatékony kapcsolatkezelés és az SDK-ügyfél teljesítményének érdekében `AppDomain` azt javasoljuk, hogy az alkalmazás élettartama alatt egyetlen példányt használjon.

   <a id="max-connection"></a>

**A System.Net MaxConnections állomásonkénti számának növelése átjárómódban**

Az Azure Cosmos DB-kérelmek HTTPS/REST protokollon keresztül történnek, ha átjáró módot használ. Állomásnévvagy IP-cím szerint az alapértelmezett kapcsolati korlát vonatkozik rájuk. Előfordulhat, hogy `MaxConnections` magasabb értékre (100-1000) kell beállítania, hogy az ügyfélkódtár több egyidejű kapcsolatot használhasson az Azure Cosmos DB-vel. A .NET SDK 1.8.0-s és újabb verzióiban a [ServicePointManager alapértelmezett](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) értéke 50. Az érték módosításához a [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) értéket magasabb értékre állíthatja.

**Particionált lekérdezések finomhangolása particionált gyűjtemények esetén**

Az SQL .NET SDK 1.9.0 és újabb verziói támogatják a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemény párhuzamos lekérdezését. További információ: Az SDK-kkal való munkához kapcsolódó [kódminták.](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) A párhuzamos lekérdezések célja, hogy jobb lekérdezési késést és átviteli, mint a soros megfelelője. A párhuzamos lekérdezések két paramétert biztosítanak, amelyeket a követelményeknek megfelelően finomíthat: 
- `MaxDegreeOfParallelism`szabályozza a párhuzamosan lekérdezhető partíciók maximális számát. 
- `MaxBufferedItemCount`szabályozza az előre lekért eredmények számát.

***A párhuzamosság hangolási foka***

A párhuzamos lekérdezés több partíció párhuzamos lekérdezésével működik. De az egyes partíciókból származó adatok lehívása a lekérdezés tekintetében sorozatosan történik. Az `MaxDegreeOfParallelism` [SDK V2](sql-api-sdk-dotnet.md) vagy `MaxConcurrency` az [SDK V3](sql-api-sdk-dotnet-standard.md) partíciók számának beállítása a legjobb eséllyel éri el a legeredményesebb lekérdezést, feltéve, hogy minden más rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, beállíthatja a párhuzamosság mértékét magas számra. A rendszer a minimális (partíciók száma, felhasználó által megadott bemenet) a párhuzamosság fokát választja.

Vegye figyelembe, hogy a párhuzamos lekérdezések a legnagyobb hasznot, ha az adatok egyenletesen oszlik el az összes partíciót a lekérdezés tekintetében. Ha a particionált gyűjtemény particionált, így a lekérdezés által visszaadott adatok mindegyike vagy többsége néhány partícióra koncentrálódik (egy partíció a legrosszabb eset), ezek a partíciók szűk keresztmetszetet jelentenek a lekérdezés teljesítményében.

***MaxBufferedItemCount hangolása***
    
A párhuzamos lekérdezés úgy van kialakítva, hogy az eredmények et előre lehívása közben az aktuális eredményköteget az ügyfél dolgozza fel. Ez az előzetes lekérés segít a lekérdezés általános késésének javításában. A `MaxBufferedItemCount` paraméter korlátozza az előre lekért eredmények számát. Állítsa `MaxBufferedItemCount` be a várt számú eredményt (vagy egy nagyobb számot), hogy a lekérdezés megkapja a maximális hasznot az előlekérésből.

Az előzetes lekérés ugyanúgy működik, függetlenül a párhuzamosság mértékétől, és az összes partícióadataihoz egyetlen puffer van.  

**A visszamaradás megvalósítása az újrapróbálkozások nál**

A teljesítménytesztelés során növelni e kell tennie a terhelést, amíg egy kis igénykérelmek szabályozása. Ha a kérelmek et szabályozza, az ügyfélalkalmazásnak vissza kell állnia a szabályszabályrendszerről a kiszolgáló által megadott újrapróbálkozási időközhöz. A visszalépés tiszteletben tartása biztosítja, hogy minimális időt töltsön az újrapróbálkozások között. 

Az újrapróbálkozási házirend ek támogatása a következő SDK-kban található:
- A [.NET SDK for SQL](sql-api-sdk-dotnet.md) és a Java [SDK for SQL](sql-api-sdk-java.md) 1.8.0-s és újabb verziója
- A [Node.js SDK sql-hez](sql-api-sdk-node.md) és a Python [SDK sql-hez 1.9.0-s](sql-api-sdk-python.md) és újabb verziója
- A [.NET Core](sql-api-sdk-dotnet-core.md) SDK-k összes támogatott verziója 

További információ: [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
A . A magasabb olvasási késéssel rendelkező kérelmek diagnosztikai karakterláncát naplózhatja. A rögzített diagnosztikai karakterlánc segít megérteni, hogy hányszor kapott 429 hibát egy adott kérelemhez.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Dokumentum URI-k gyorsítótárazása az alacsonyabb olvasási késleltetéshez**

A dokumentum URI-inak gyorsítótárazása, amikor csak lehetséges, a legjobb olvasási teljesítmény érdekében. Az erőforrás-azonosító létrehozásakor meg kell határoznia a logikát az erőforrás-azonosító gyorsítótárazásához. Az erőforrás-azonosítókon alapuló keresgések gyorsabbak, mint a névalapú keresgések, így ezek az értékek gyorsítótárazása javítja a teljesítményt.

   <a id="tune-page-size"></a>
**A jobb teljesítmény érdekében a lekérdezések/etetők olvasása oldalméretének finomhangolása**

Ha olvasási hírcsatorna-funkcióval (például `ReadDocumentFeedAsync`) vagy SQL-lekérdezés kibocsátásakor tömegesen olvassa el a dokumentumokat, az eredmények szegmentált módon kerülnek visszaadásra, ha az eredményhalmaz túl nagy. Alapértelmezés szerint az eredmények 100 elemből vagy 1 MB-os adattömbökből adják vissza az eredményeket, attól függően, hogy melyik korlát ot érinti először.

Az összes vonatkozó eredmény lekéréséhez szükséges hálózati átutazások számának csökkentése érdekében az [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) használatával akár 1000 fejlécet is kérhet. Ha csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az alkalmazás API-ja egyszerre csak 10 eredményt ad vissza, az oldalméretet is csökkentheti 10-re az olvasások és lekérdezések során felhasznált átviteli érték csökkentése érdekében.

> [!NOTE] 
> A `maxItemCount` szálláshelyet nem szabad csak tördelésre használni. Fő célja a lekérdezések teljesítményének javítása az egy oldalon visszaadott elemek maximális számának csökkentésével.  

Az oldalméret a rendelkezésre álló Azure Cosmos DB SDK-k használatával is beállítható. A [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) `FeedOptions` tulajdonság lehetővé teszi a felsorolási műveletben visszaadandó elemek maximális számának beállítását. Ha `maxItemCount` a beállítás -1, az SDK automatikusan megkeresi az optimális értéket, a dokumentum méretétől függően. Példa:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
A lekérdezés végrehajtásakor az eredményül kapott adatok at tcp-csomagon belül küldik el. Ha túl alacsony értéket `maxItemCount`ad meg a számára, a TCP-csomagon belüli adatok küldéséhez szükséges utak száma magas, ami befolyásolja a teljesítményt. Tehát, ha nem biztos benne, hogy `maxItemCount` milyen értéket kell beállítani a tulajdonsághoz, akkor a legjobb, ha -1 értékre állítja, és hagyja, hogy az SDK válassza ki az alapértelmezett értéket.

**Szálak/feladatok számának növelése**

Lásd: [Szálak/feladatok számának növelése](#increase-threads) a cikk Hálózatolás szakaszában.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
**Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

Az Azure Cosmos DB indexelési szabályzat azt is lehetővé teszi, hogy adja meg, hogy mely dokumentum elérési utak at kell felvenni, vagy kizárni az indexelési útvonalak (IndexingPolicy.IncludedPaths és IndexingPolicy.ExcludedPaths). Az indexelési útvonalak javíthatják az írási teljesítményt, és csökkenthetik az indextárolót olyan esetekben, amelyekben a lekérdezési minták előre ismertek. Ennek az az oka, hogy az indexelési költségek közvetlenül az indexelt egyedi elérési utak számával korrelálódnak. Ez a kód például azt mutatja be, hogyan lehet kizárni a dokumentumok egy teljes szakaszát (részfát) az indexelésből a "*" helyettesítő karakter használatával:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

További információ: [Azure Cosmos DB indexelési szabályzatok.](index-policy.md)

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

**Alacsonyabb kérelemegységek/másodperchasználat mérése és hangolása**

Az Azure Cosmos DB adatbázis-műveletek gazdag készletét kínálja. Ezek a műveletek magukban foglalják az UDF-fájlokkal való relációs és hierarchikus lekérdezéseket, a tárolt eljárásokat és az eseményindítókat, amelyek az adatbázis-gyűjteményben lévő dokumentumokon működnek. Az egyes műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges PROCESSZORtól, I/O-tól és memóriától függenek. A hardvererőforrások átgondolása és kezelése helyett a kérelemegység (RU) egyetlen mértékként is elgondolkodhat a különböző adatbázis-műveletek végrehajtásához és az alkalmazáskérelmek kiszolgálásához szükséges erőforrásokhoz.

Átviteli átviteli van kiépítve az egyes [tárolókhoz](request-units.md) beállított kérelemegységek száma alapján. A kérelem egységfelhasználása másodpercenkénti díjként lesz kiértékelve. Alkalmazások, amelyek meghaladják a kiosztott kérelem egység aránya a tároló korlátozott, amíg a sebesség alá csökken a kiosztott szint a tároló. Ha az alkalmazás magasabb átviteli szintet igényel, növelheti az átviteli kapacitást további kérelemegységek kiépítésével.

A lekérdezés összetettsége befolyásolja, hogy hány kérelemegységet használ fel egy művelethez. A predikátumok száma, a predikátumok jellege, az UDF-ek száma és a forrásadatkészlet mérete mind befolyásolják a lekérdezési műveletek költségét.

Bármely művelet (létrehozás, frissítés vagy törlés) többletterhelésének méréséhez vizsgálja meg az `RequestCharge` [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet (vagy a .NET SDK-ban `ResourceResponse\<T>` vagy a .NET SDK-ban található `FeedResponse\<T>` egyenértékű tulajdonságot) a műveletek által felhasznált kérelemegységek számának mérésére:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

A fejlécben visszaadott kérelemdíj a kiosztott átviteli érték töredéke (azaz 2000 kérelem/ másodperc). Ha például az előző lekérdezés 1000 1 KB-os dokumentumot ad vissza, a művelet költsége 1000. Így egy másodpercen belül a kiszolgáló csak két ilyen kérést tart tiszteletben, mielőtt a sebességkorlátozás későbbi kérések. További információt az [Egységek kérése](request-units.md) és a [Kérelemegység kalkulátor című témakörben talál.](https://www.documentdb.com/capacityplanner)
<a id="429"></a>

**A sebességkorlátozás/kérelem sebességének lehívása túl nagy**

Ha egy ügyfél megpróbálja túllépni a fenntartott átviteli egy fiókot, nincs teljesítménycsökkenés a kiszolgálón, és nem használja az átviteli kapacitás t a fenntartott szinten túl. A kiszolgáló a requestet megelőzően a RequestRateTooLarge (HTTP 429-es állapotkód) segítségével befejezi. Egy [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet ad vissza, amely azt az időt jelzi ezredmásodpercben, amíg a felhasználónak várnia kell a kérés újbóli megkísérlése előtt.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Az SDK-k implicit módon elkapják ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási fejlécet, és újrapróbálkoznak a kéréssel. Ha csak akkor éri el fiókját, hogy több ügyfél egyidejűleg fér hozzá, a következő újrapróbálkozás sikeres lesz.

Ha egynél több ügyfél összesítve működik következetesen meghaladja a kérelem aránya, az alapértelmezett újrapróbálkozások száma jelenleg beállított 9 belsőleg az ügyfél nem elegendő. Ebben az esetben az ügyfél egy 429-es állapotkódú DocumentClientException-et dob az alkalmazásnak. 

Az alapértelmezett újrapróbálkozások számát a `RetryOptions` `ConnectionPolicy` példányon való beállítással módosíthatja. Alapértelmezés szerint a DocumentClientException állapotkód 429-es állapotkóddal 30 másodperces összesített várakozási idő után kerül vissza, ha a kérelem továbbra is a kérelemsebesség felett működik. Ez a hiba akkor is visszaadja, ha az aktuális újrapróbálkozások száma kisebb, mint a maximális újrapróbálkozások száma, függetlenül attól, hogy az aktuális érték az alapértelmezett 9 vagy a felhasználó által definiált érték.

Az automatikus újrapróbálkozási viselkedés a legtöbb alkalmazás rugalmasságának és használhatóságának javítását javítja. De lehet, hogy nem a legjobb viselkedés, ha csinálsz teljesítmény-referenciaértékek, különösen akkor, ha a késés mérése. Az ügyfél által megfigyelt késés kiugrik, ha a kísérlet eléri a kiszolgáló szabályozási, és az ügyfél SDK-t némán újrapróbálkozik. A teljesítménykísérletek során a késés kiugrásának elkerülése érdekében mérje meg az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelemdíj alatt működnek. További információ: [Request Units](request-units.md).

**A nagyobb átamenő kedés hez kisebb dokumentumokhoz tervezve**

A kérelem díja (azaz a kérelem-feldolgozási költség) egy adott művelet közvetlenül a dokumentum méretének. A nagy dokumentumokon végzett műveletek többe kerülnek, mint a kis dokumentumokon végzett műveletek.

## <a name="next-steps"></a>További lépések
Az Azure Cosmos DB-t néhány ügyfélgépen végzett nagy teljesítményű forgatókönyvek kiértékeléséhez használt mintaalkalmazásról a [Teljesítmény és a méretezési tesztelés az Azure Cosmos DB-vel](performance-testing.md)című témakörben található.

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű tervezéséről, olvassa el [a Particionálás és méretezés az Azure Cosmos DB-ben című témakört.](partition-data.md)
