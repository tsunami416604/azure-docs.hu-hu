---
title: A .NET SDK v3 Azure Cosmos DB teljesítményével kapcsolatos tippek
description: Az ügyfél-konfigurációs beállítások megismerése Azure Cosmos DB .NET v3 SDK teljesítményének javításához.
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.custom: devx-track-dotnet
ms.openlocfilehash: f8e610531eaf3e7e5dbee9c40c88683a05029303
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802990"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Az Azure Cosmos DB és a .NET teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

A Azure Cosmos DB egy gyors, rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség szintjén. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információt a [tárolók átviteli sebességének kiépítése](how-to-provision-container-throughput.md) vagy az [adatbázis-átviteli sebesség kiépítése](how-to-provision-database-throughput.md)című témakörben talál. 

Mivel Azure Cosmos DB hálózati hívásokon keresztül érhető el, az ügyféloldali optimalizálással elérheti a maximális teljesítményt az [SQL .net SDK](sql-api-sdk-dotnet-standard.md)használatakor.

Ha javítani kívánja az adatbázis teljesítményét, vegye figyelembe az alábbi részben ismertetett lehetőségeket.

## <a name="hosting-recommendations"></a>Üzemeltetési javaslatok

**A lekérdezés-igényes számítási feladatokhoz a Linux vagy a Windows 32 bites gazdagép-feldolgozás helyett a Windows 64 bites használata**

A jobb teljesítmény érdekében javasoljuk a Windows 64 bites gazdagépek feldolgozását. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. 

Linux és egyéb nem támogatott platformok esetén, ahol a ServiceInterop.dll nem érhető el, az átjáróra további hálózati hívás történik az optimalizált lekérdezés beszerzéséhez. 

Az itt felsorolt négy alkalmazás típusa alapértelmezés szerint a 32 bites gazdagép-feldolgozást használja. Ha módosítani szeretné a gazdagép feldolgozását az alkalmazás típusának 64 bites feldolgozására, tegye a következőket:

- **Végrehajtható alkalmazások esetén**: a **Projekt tulajdonságai** ablak **Build** ablaktábláján állítsa a [platform célt](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) az **x64**értékre.

- **VSTest-alapú tesztelési projektek esetén**: a Visual Studio **test** menüben válassza a **Test**teszt  >  **beállításainak**tesztelése lehetőséget, majd állítsa az **alapértelmezett processzor-architektúra** értéket az **x64**értékre.

- **Helyileg telepített ASP.NET-webalkalmazások**esetén: válassza az **eszközök**  >  **Beállítások**  >  **projektek és megoldások**  >  **webes projektek**lehetőséget, majd válassza **a webhelyekhez és projektekhez tartozó IIS Express 64 bites verziójának használata**lehetőséget.

- Az Azure-ban **üzembe helyezett ASP.NET-webalkalmazások esetében**: a Azure Portal **alkalmazás beállításai**területen válassza ki a **64 bites** platformot.

> [!NOTE] 
> Alapértelmezés szerint az új Visual Studio-projektek **bármely CPU**-ra vannak beállítva. Javasoljuk, hogy állítsa a projektet **x64** -re, hogy ne váltson **x86**-ra. Egy **CPU** -ra beállított projekt könnyen válthat **x86** -ra, ha csak x86-os függőség van hozzáadva.<br/>
> A ServiceInterop.dll fájlnak abban a mappában kell lennie, amelyre az SDK DLL-t végrehajtja. Ez csak akkor fontos, ha manuálisan másol DLL-eket, vagy egyéni Build-vagy üzembe helyezési rendszert használ.
    
**Kiszolgálóoldali Garbage-gyűjtemény bekapcsolása**

Bizonyos esetekben a Garbage-gyűjtemények gyakoriságának csökkentése is segíthet. A .NET-ben állítsa be a [gcServer](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) a következőre: `true` .

**Az ügyfél számítási felskálázása**

Ha magas átviteli sebességet használ, vagy a másodpercenként 50 000-nál nagyobb mennyiségű (RU/s) sebességű, az ügyfélalkalmazás szűk keresztmetszetet jelenthet. Ennek az az oka, hogy a gép a processzor vagy a hálózat kihasználtsága esetén is előfordulhat. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

> [!NOTE] 
> A magas CPU-használat nagyobb késést és kérelem időtúllépési kivételt okozhat.

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

**Csatlakoztatási házirend: közvetlen kapcsolási mód használata**

Az ügyfél Azure Cosmos DBhoz való kapcsolódásának módja fontos teljesítménybeli következményekkel jár, különösen a megfigyelt ügyféloldali késés miatt. Az ügyfélkapcsolati házirend konfigurálásához két kulcsfontosságú konfigurációs beállítás érhető el: a kapcsolati *mód* és a kapcsolati *protokoll*. A két elérhető kapcsolattípus a következők:

   * Közvetlen mód (alapértelmezett)

     A közvetlen mód a TCP protokollon keresztüli csatlakozást támogatja, és az alapértelmezett csatlakozási mód, ha a [Microsoft. Azure. Cosmos/. net v3 SDK-](https://github.com/Azure/azure-cosmos-dotnet-v3)t használja. A közvetlen mód jobb teljesítményt nyújt, és kevesebb hálózati ugrást igényel, mint az átjáró üzemmód.

   * Átjáró üzemmód
      
     Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. 
     
     A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az adatok beolvasása vagy írása Azure Cosmos DB. Így a közvetlen mód jobb teljesítményt nyújt, mivel kevesebb hálózati ugrás van. Azt is javasoljuk, hogy az átjáró kapcsolati üzemmódja csak korlátozott számú szoftvercsatorna-kapcsolattal rendelkező környezetekben futtatott alkalmazásokat futtasson.

     Ha az SDK-t Azure Functionsban használja, különösen a használati [tervben](../azure-functions/functions-scale.md#consumption-plan), vegye figyelembe a [kapcsolatok jelenlegi korlátait](../azure-functions/manage-connections.md). Ebben az esetben előfordulhat, hogy az átjáró mód jobb, ha más HTTP-alapú ügyfelekkel is dolgozik a Azure Functions alkalmazáson belül.
     
Ha a TCP protokollt közvetlen módban használja, az átjáró portjain kívül meg kell győződnie arról, hogy a 10000 és 20000 közötti porttartomány nyitva van, mert a Azure Cosmos DB dinamikus TCP-portokat használ. Ha közvetlen módot használ a [privát végpontokon](./how-to-configure-private-endpoints.md), a 0 és 65535 közötti TCP-portok teljes tartományának nyitva kell lennie. A portok alapértelmezés szerint a szabványos Azure-beli virtuális gép konfigurációjában nyílnak meg. Ha ezek a portok nincsenek megnyitva, és a TCP-t próbálja használni, "503 szolgáltatás nem érhető el" hibaüzenet jelenik meg. 

A következő táblázat a különböző API-k és az egyes API-k által használt szolgáltatási portok számára elérhető csatlakozási módokat mutatja be:

|Kapcsolat módja  |Támogatott protokoll  |Támogatott SDK-k  |API/szolgáltatás portja  |
|---------|---------|---------|---------|
|Átjáró  |   HTTPS    |  Minden SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tábla (443), Cassandra (10350), Graph (443) <br><br> A 10250-es port a MongoDB-példány alapértelmezett Azure Cosmos DB API-ját a Geo-replikálás nélkül, a 10255-es és a 10256-as portot pedig a Geo-replikációval rendelkező példányra képezi le.   |
|Direct    |     TCP    |  .NET SDK    | Nyilvános/szolgáltatási végpontok használata esetén: a 10000-es és 20000-os közötti portok<br><br>Privát végpontok használata esetén: a 0 és 65535 közötti portok |

Azure Cosmos DB egy egyszerű, nyitott, REST-alapú programozási modellt biztosít a HTTPS-en keresztül. Emellett hatékony TCP protokollt is biztosít, amely a kommunikációs modellben is elérhető, és a .NET Client SDK-n keresztül érhető el. A TCP protokoll Transport Layer Security (TLS) protokollt használ a kezdeti hitelesítéshez és a forgalom titkosításához. A legjobb teljesítmény érdekében a TCP protokollt használja, ha lehetséges.

Az SDK V3 esetében konfigurálja a kapcsolódási módot, amikor létrehozza a `CosmosClient` példányt a alkalmazásban `CosmosClientOptions` . Ne feledje, hogy a közvetlen mód az alapértelmezett.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Mivel a TCP csak közvetlen módban támogatott, ha átjáró üzemmódot használ, a rendszer mindig a HTTPS protokollt használja az átjáróval való kommunikációhoz.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Hozzon létre kapcsolatot a Azure Cosmos DB különböző kapcsolati módokkal és protokollokkal." border="false":::

**Rövid élettartamú portok elfogyása**

Ha a példányok nagy mennyiségű vagy magas portszámú használatot lát, először ellenőrizze, hogy az ügyfél példányai egyediek-e. Más szóval az ügyfél példányának egyedinek kell lennie az alkalmazás élettartama szempontjából.

Ha a TCP protokollon fut, az ügyfél a hosszú élettartamú kapcsolatok használatával optimalizálja a késést. Ez ellentétben áll a HTTPS protokollal, amely két perc inaktivitás után leállítja a kapcsolatokat.

Olyan helyzetekben, ahol ritka hozzáférése van, és ha az átjáró mód eléréséhez képest nagyobb számú kapcsolatra van szüksége, a következőket teheti:

* Konfigurálja a [CosmosClientOptions. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) tulajdonságot a `PrivatePortPool` (a keretrendszer 4.6.1-es és újabb verzióival, valamint a .net Core 2,0-es és újabb verziókkal). Ez a tulajdonság lehetővé teszi, hogy az SDK a különböző Azure Cosmos DB cél végpontokhoz tartozó ideiglenes portok kis készletét használja.
* Állítsa be a [CosmosClientOptions. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) tulajdonságot 10 percnél nagyobb vagy azzal egyenlő értékre. Az ajánlott értékek 20 perctől 24 óráig tartanak.

<a id="same-region"></a>

**A teljesítmény érdekében az azonos Azure-régióban lévő ügyfelek rézvezetékes végezhet**

Ha lehetséges, helyezzen olyan alkalmazásokat, amelyek a Azure Cosmos DB-adatbázissal megegyező régióban Azure Cosmos DB hívnak. Íme egy hozzávetőleges összehasonlítás: az azonos régión belüli Azure Cosmos DB meghívása 1 ezredmásodperc (MS) és 2 MS között fejeződik be, de az USA nyugati és keleti partja közötti késés meghaladja a 50 MS-ot. Ez a késés a kérelemtől függően változhat, attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure-adatközpont határán. 

A lehető legkevesebb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Hozzon létre kapcsolatot a Azure Cosmos DB különböző kapcsolati módokkal és protokollokkal." border="false":::

   <a id="increase-threads"></a>

**A szálak/feladatok számának növelésével**

Mivel a Azure Cosmos DB kezdeményez a hálózaton keresztül, előfordulhat, hogy módosítania kell a kérések párhuzamossági fokát, hogy az ügyfélalkalmazás minimális ideig várjon a kérések között. Ha például a .NET- [feladatok párhuzamos könyvtárát](https://msdn.microsoft.com//library/dd460717.aspx)használja, akkor hozzon létre több száz feladatot a Azure Cosmos db olvasására vagy írására.

**Gyorsított hálózatkezelés engedélyezése**
 
A késés és a CPU vibrálás csökkentése érdekében javasoljuk, hogy engedélyezze a gyorsított hálózatkezelést az ügyfél virtuális gépei számára. További információkért lásd: [Windows rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](../virtual-network/create-vm-accelerated-networking-powershell.md) vagy [linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-használat

**A legújabb SDK telepítése**

A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. A legújabb SDK-és felülvizsgálati eredmények megállapításához lásd: [Azure Cosmos db SDK](sql-api-sdk-dotnet-standard.md).

**Stream API-k használata**

A [.net SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) olyan stream API-kat tartalmaz, amelyek szerializálás nélkül fogadhatnak és adhatnak vissza adatforrást. 

A közepes szintű alkalmazások, amelyek nem használnak közvetlenül az SDK-ból származó válaszokat, de más alkalmazási rétegekbe továbbítják a stream API-k előnyeit. Az adatfolyam-kezelésre vonatkozó példákért tekintse meg az [elem-felügyeleti](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) mintákat.

**Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

`CosmosClient`Az egyes példányok a szálon biztonságosak, és hatékony kapcsolatok felügyeletét és gyorsítótárazását végzik, ha közvetlen módban működik. A hatékony és az SDK-ügyfél teljesítményének növelése érdekében ajánlott egyetlen példányt használni az `AppDomain` alkalmazás élettartamára.

Ha Azure Functions dolgozik, a példányoknak a meglévő [irányelveket](../azure-functions/manage-connections.md#static-clients) is követniük kell, és egyetlen példányt kell fenntartaniuk.

<a id="max-connection"></a>

**Tartalom válaszának letiltása írási műveleteken**

A nagy mennyiségű adattartalommal rendelkező munkaterhelések esetében állítsa be a kérelem lehetőséget a következőre: `EnableContentResponseOnWrite` `false` . A szolgáltatás többé nem fogja visszaadni a létrehozott vagy frissített erőforrást az SDK-nak. Általában azért, mert az alkalmazás rendelkezik a létrehozandó objektummal, nincs szükség a szolgáltatás visszaküldésére. A fejléc értékei továbbra is elérhetők, például a kérések díja. A tartalom válaszának letiltásával növelheti a teljesítményt, mivel az SDK-nak már nem kell memóriát lefoglalnia vagy szerializálnia a válasz törzsét. Emellett csökkenti a hálózati sávszélesség használatát a további teljesítmény érdekében.  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**A késleltetés helyett az átviteli sebesség optimalizálása tömeges engedélyezése**

*Tömeges* engedélyezése olyan helyzetekben, amikor a munkaterhelésnek nagy mennyiségű átviteli sebességre van szüksége, és a késés nem annyira fontos. További információ a tömeges funkció engedélyezéséről, valamint annak megismeréséhez, hogy mely forgatókönyveket kell használni a alkalmazáshoz: a [tömeges támogatás bemutatása](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**System.Net-MaxConnections növelésének engedélyezése gazdagépen az átjáró mód használatakor**

Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek, amikor átjáró üzemmódot használ. Az alapértelmezett kapcsolódási korlát az állomásnév vagy az IP-cím alapján történik. Előfordulhat, hogy magasabb értéket kell beállítania `MaxConnections` (100 – 1 000), hogy az ügyféloldali kódtár több egyidejű kapcsolatot is használhat Azure Cosmos DBhoz. A .NET SDK 1.8.0 és újabb verzióiban a [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) alapértelmezett értéke 50. Az érték módosításához [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) magasabb értéket adhat meg.

**A particionált gyűjtemények párhuzamos lekérdezésének hangolása**

Az SQL .NET SDK támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált tárolók párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy jobb lekérdezési késést és adatátviteli sebességet biztosítanak, mint a soros 

A párhuzamos lekérdezések két paramétert biztosítanak, amelyeket az igényeinek megfelelően hangolhat: 

- **MaxConcurrency**: a párhuzamosan lekérdezhető partíciók maximális számát határozza meg.

   A párhuzamos lekérdezés több partíció párhuzamos lekérdezésével működik. Az egyes partíciók adatait azonban a lekérdezésre vonatkozó sorosan kell beolvasni. `MaxConcurrency`Ha az [SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) -as verziójában a partíciók száma a legjobb, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, megadhatja a párhuzamosság mértékét magas számra. A rendszer kijelöli a minimális (partíciók számát, a felhasználó által megadott bemenetet) a párhuzamosság foka alapján.

    A párhuzamos lekérdezések a legnagyobb haszonnal járnak, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény particionálva van, és a lekérdezés által visszaadott összes adat egy része néhány partícióra koncentrál (az egyik partíció a legrosszabb ESET), akkor ezek a partíciók a lekérdezés teljesítményét szűk keresztmetszetbe helyezik.
   
- **MaxBufferedItemCount**: az előre lehívott eredmények számát szabályozza.

   A párhuzamos lekérdezés úgy lett kialakítva, hogy előzetesen beolvassa az eredményeket, miközben az ügyfél az aktuális eredményt dolgozza fel. Ez az előzetes lehívás a lekérdezés teljes késésének javítására nyújt segítséget. A `MaxBufferedItemCount` paraméter korlátozza az előre lehívott eredmények számát. Állítsa be a `MaxBufferedItemCount` visszaadott eredmények várt számát (vagy egy magasabb számot), hogy a lekérdezés a lehető legtöbbet fogadja az előzetes lekéréstől.

   Az előzetes lekérés ugyanúgy működik, függetlenül a párhuzamosság mértékétől, és egyetlen puffer van az összes partícióból származó adatokhoz.  

**Leállítási megvalósítása RetryAfter időközönként**

A teljesítmény tesztelése során növelje a terhelést, amíg a rendszer kis mennyiségű kérést nem szabályoz. Ha a kérelmek szabályozása megtörténik, az ügyfélalkalmazás a kiszolgáló által megadott újrapróbálkozási időköz szabályozásának visszavonását hajtja végre. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan megtörténjen az idő várakozása. 

További információ: [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Létezik egy mechanizmus a további diagnosztikai információk naplózására és a késéssel kapcsolatos hibák elhárítására, ahogy az az alábbi példában is látható. Naplózhatja a diagnosztikai karakterláncot olyan kérelmek esetében, amelyek nagyobb olvasási késéssel rendelkeznek. A rögzített diagnosztikai karakterlánc segít megérteni, hogy hányszor kapott egy adott kéréshez *429* hibát.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**A szálak/feladatok számának növelésével**

Lásd: [a szálak/feladatok számának növekedése](#increase-threads) a hálózatkezelés szakaszában.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
**Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

A Azure Cosmos DB indexelési házirend azt is lehetővé teszi, hogy az indexelési elérési utak (IndexingPolicy. IncludedPaths és IndexingPolicy. ExcludedPaths) használatával milyen dokumentum-elérési utakat tartalmazzon vagy zárhat ki az indexelésből. 

Csak a szükséges elérési utak indexelése javíthatja az írási teljesítményt, csökkentheti az írási műveletekhez kapcsolódó RU-díjakat, és csökkentheti az indexek tárolását olyan helyzetekben, amikor a lekérdezési mintákat előre ismerik. Ennek az az oka, hogy az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával kapcsolatosak. Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok (egy részfa) teljes szakasza az indexelésből a "*" helyettesítő karakterrel:

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

További információ: [Azure Cosmos db indexelési házirendek](index-policy.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

**Az alacsonyabb RU/s használatának mérése és finomhangolása**

Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja. Ezek a műveletek közé tartoznak az univerzális lemezes (UDF) fájlok, a tárolt eljárások és az eseményindítók által végzett, az adatbázis-gyűjteményen belüli összes művelet. 

Az egyes műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU-, IO-és memória-mennyiségtől függően változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egységét egyetlen mértékként tekintheti meg a különböző adatbázis-műveletek végrehajtásához és az alkalmazás iránti kérelmekhez szükséges erőforrásokhoz.

Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérés egységének felhasználását egység/másodperc arányban értékeljük. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

A lekérdezés bonyolultsága befolyásolja, hogy a rendszer hány kérési egységet használ a műveletekhez. A predikátumok száma, a predikátumok természete, az UDF-fájlok száma és a forrás-adatkészlet mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécét (vagy a `RequestCharge` .net SDK-ban vagy annak megfelelő tulajdonságát `ResourceResponse\<T>` ) a `FeedResponse\<T>` műveletek által felhasznált kérelmek mennyiségének méréséhez:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség (azaz 2 000 RU/s) része. Ha például az előző lekérdezés 1 000 1 – KB dokumentumokat ad vissza, a művelet díja 1 000. Tehát egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki, mielőtt az arány korlátozza a későbbi kérelmeket. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel leállítja a kérést a RequestRateTooLarge (429-es HTTP-állapotkód). Egy [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet ad vissza, amely megadja, hogy a felhasználónak mennyi ideig kell várnia, mielőtt újra megkísérli a kérést.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a maximálisan engedélyezett műveleteket, akkor előfordulhat, hogy az ügyfél által használt újrapróbálkozások száma jelenleg nem elegendő. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó CosmosException hoz az alkalmazáshoz. 

Az újrapróbálkozások alapértelmezett számának módosításához állítsa a `RetryOptions` példányt a `CosmosClientOptions` példányra. Alapértelmezés szerint a 429-as kódú CosmosException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. A rendszer akkor is ezt a hibát adja vissza, ha az újrapróbálkozások száma kevesebb, mint az újrapróbálkozások maximális száma, hogy az aktuális érték az alapértelmezett 9 vagy felhasználó által definiált érték-e.

Az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását. Előfordulhat azonban, hogy a teljesítmény-teljesítménymutatók végrehajtásakor nem a legjobb viselkedés, különösen ha a késés mérését végzi. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. 

További információt a [kérelmek egységei](request-units.md)című témakörben talál.

**Nagyobb átviteli sebesség esetén a kisebb dokumentumok tervezése**

Egy adott műveletre vonatkozó kérelem díja (azaz a kérelmek feldolgozási díja) közvetlenül a dokumentum méretével összefügg egymással. A nagyméretű dokumentumokon végzett műveletek többek között a kis dokumentumokon végzett műveletekhez szükségesek.

## <a name="next-steps"></a>További lépések
Az egyes ügyfélszámítógépeken a nagy teljesítményű forgatókönyvek Azure Cosmos DB kiértékeléséhez használt minta alkalmazással kapcsolatban lásd: [teljesítmény-és méretezési tesztek a Azure Cosmos db](performance-testing.md)használatával.

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partition-data.md)című témakört.
