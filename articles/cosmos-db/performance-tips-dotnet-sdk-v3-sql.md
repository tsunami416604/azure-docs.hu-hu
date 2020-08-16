---
title: A .NET SDK v3 Azure Cosmos DB teljesítményével kapcsolatos tippek
description: Az ügyfél-konfigurációs beállítások megismerése Azure Cosmos DB .NET v3 SDK teljesítményének javításához.
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.openlocfilehash: 90b4ffb273fc314a7c92971490fb09b6f0c131ee
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258343"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Az Azure Cosmos DB és a .NET teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információkért lásd: [a tárolók teljesítményének kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis átviteli sebességének kiépítése](how-to-provision-database-throughput.md). Mivel azonban a Azure Cosmos DB hálózati hívásokon keresztül érhetők el, ügyféloldali optimalizálások érhetők el, amelyekkel elérheti a maximális teljesítményt az [SQL .net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)használatakor.

Tehát ha javítani kívánja az adatbázis teljesítményét, vegye figyelembe a következő lehetőségeket:

## <a name="hosting-recommendations"></a>Üzemeltetési javaslatok

**A lekérdezés-igényes számítási feladatokhoz a Linux vagy a Windows 32 bites gazdagép-feldolgozás helyett a Windows 64 bites használata**

A jobb teljesítmény érdekében javasoljuk a Windows 64 bites gazdagépek feldolgozását. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. Linux és egyéb nem támogatott platformok esetén, ahol a ServiceInterop.dll nem érhető el, az átjáróra további hálózati hívás történik az optimalizált lekérdezés beszerzéséhez. A következő típusú alkalmazások alapértelmezés szerint 32 bites gazdagép-feldolgozást használnak. Ha módosítani szeretné a gazdagép feldolgozását 64 bites feldolgozásra, kövesse az alábbi lépéseket az alkalmazás típusa alapján:

- A végrehajtható alkalmazások esetében úgy módosíthatja a gazdagépek feldolgozását, hogy a [platform célját](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) **x64**  értékre állítja a **Projekt tulajdonságai** ablakban a **Build** lapon.

- A VSTest-alapú tesztelési projektek esetében **Test**  >  **Test Settings**  >  a Visual Studio **test** menüben válassza a tesztelési beállítások**alapértelmezett processzor-architektúra x64-ként** lehetőséget.

- Helyileg telepített ASP.NET-webalkalmazások esetében a gazdagépek feldolgozását úgy módosíthatja, hogy kiválasztja **a IIS Express 64 bites verzióját a webhelyekhez és projektekhez** a **Tools**  >  **Options**  >  **Projects és a Solutions**  >  **webes projektjei**alatt.

- Az Azure-on üzembe helyezett webalkalmazások esetében a **ASP.net a** **64 bites** platform kiválasztásával módosíthatja a Azure Portal.

> [!NOTE] 
> Alapértelmezés szerint az új Visual Studio-projektek **bármely CPU**-ra vannak beállítva. Javasoljuk, hogy állítsa a projektet **x64** -re, hogy ne váltson **x86**-ra. **Bármely CPU** -ra beállított projekt könnyen válthat **x86** -ra, ha csak x86-os függőség van hozzáadva.<br/>
> ServiceInterop.dllnak abban a mappában kell lennie, amelyre az SDK DLL-t végrehajtja. Ez csak akkor fontos, ha manuálisan másol DLL-eket, vagy egyéni Build-vagy üzembe helyezési rendszert használ.
    
**Kiszolgálóoldali Garbage-gyűjtemény bekapcsolása (GC)**

Bizonyos esetekben a Garbage-gyűjtemények gyakoriságának csökkentése is segíthet. A .NET-ben állítsa be a [gcServer](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) a következőre: `true` .

**Az ügyfél számítási felskálázása**

Ha magas átviteli sebességű (több mint 50 000 RU/s) tesztelést végez, az ügyfélalkalmazás a processzor vagy a hálózat kihasználtságának korlátozásával járó szűk keresztmetszet miatt válhat. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

> [!NOTE] 
> A magas CPU-használat nagyobb késést és kérelem időtúllépési kivételt okozhat.

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

**Csatlakoztatási házirend: közvetlen kapcsolási mód használata**

Az ügyfél Azure Cosmos DBhoz való kapcsolódásának módja fontos teljesítménybeli következményekkel jár, különösen a megfigyelt ügyféloldali késés miatt. Az ügyfélkapcsolati házirend konfigurálásához két fő konfigurációs beállítás érhető el: a kapcsolati *mód* és a kapcsolati *protokoll*.  A két elérhető mód a következők:

   * Közvetlen mód (alapértelmezett)

     A közvetlen mód a TCP protokollon keresztüli csatlakozást támogatja, és az alapértelmezett csatlakozási mód, ha a [Microsoft. Azure. Cosmos/. net v3 SDK-](https://github.com/Azure/azure-cosmos-dotnet-v3)t használja. Ez jobb teljesítményt nyújt, és kevesebb hálózati ugrást igényel, mint az átjáró üzemmód.

   * Átjáró üzemmód
      
     Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az adatok beolvasása vagy írása Azure Cosmos DB. Így a közvetlen mód jobb teljesítményt nyújt, mivel kevesebb hálózati ugrás van. Azt is javasoljuk, hogy az átjáró kapcsolati üzemmódja csak korlátozott számú szoftvercsatorna-kapcsolattal rendelkező környezetekben futtatott alkalmazásokat futtasson.

     Ha az SDK-t Azure Functionsban használja, különösen a használati [tervben](../azure-functions/functions-scale.md#consumption-plan), vegye figyelembe a [kapcsolatok jelenlegi korlátait](../azure-functions/manage-connections.md). Ebben az esetben előfordulhat, hogy az átjáró mód jobb, ha más HTTP-alapú ügyfelekkel is dolgozik a Azure Functions alkalmazáson belül.
     
Ha a TCP-t közvetlen módban használja, az átjáró portjain kívül meg kell győződnie arról, hogy a 10000 és a 20000 közötti porttartomány meg van nyitva, mert Azure Cosmos DB dinamikus TCP-portokat használ. Ha közvetlen módot használ [privát végpontokon](./how-to-configure-private-endpoints.md), a TCP-portok teljes tartományát (0 és 65535 között) nyitva kell lennie. A portok alapértelmezés szerint a szabványos Azure-beli virtuális gép konfigurációjában nyílnak meg. Ha ezek a portok nincsenek megnyitva, és a TCP-t próbálja használni, a 503-es szolgáltatás nem érhető el hibaüzenetet kap. Az alábbi táblázat a különböző API-k és az egyes API-k által használt szolgáltatási portok kapcsolati módjait mutatja be:

|Kapcsolat módja  |Támogatott protokoll  |Támogatott SDK-k  |API/szolgáltatás portja  |
|---------|---------|---------|---------|
|Átjáró  |   HTTPS    |  Minden SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tábla (443), Cassandra (10350), Graph (443) <br> Az 10250-es port a MongoDB-példány alapértelmezett Azure Cosmos DB API-ját képezi le a Geo-replikáció nélkül. Míg a 10255-es és a 10256-es port a Geo-replikációval rendelkező példányhoz rendelhető hozzá.   |
|Direct    |     TCP    |  .NET SDK    | Nyilvános/szolgáltatási végpontok használata esetén: a 10000-as port a 20000-tartományon keresztül<br>Privát végpontok használata esetén: a 0 és 65535 közötti portok |

Azure Cosmos DB egy egyszerű, nyitott, REST-alapú programozási modellt biztosít a HTTPS-en keresztül. Emellett hatékony TCP protokollt is biztosít, amely a kommunikációs modellben is elérhető, és a .NET Client SDK-n keresztül érhető el. A TCP protokoll TLS protokollt használ a kezdeti hitelesítéshez és a forgalom titkosításához. A legjobb teljesítmény érdekében a TCP protokollt használja, ha lehetséges.

Az SDK V3 esetében a példány létrehozásakor konfigurálja a kapcsolódási módot a `CosmosClient` alkalmazásban `CosmosClientOptions` . Ne feledje, hogy a közvetlen mód az alapértelmezett.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Mivel a TCP csak közvetlen módban támogatott, ha átjáró üzemmódot használ, a rendszer mindig a HTTPS protokollt használja az átjáróval való kommunikációhoz.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="A Azure Cosmos DB-kapcsolatok házirendje" border="false":::

**Rövid élettartamú portok elfogyása**

Ha a példányok nagy mennyiségű vagy magas portszámú használatot lát, először ellenőrizze, hogy az ügyfél példányai egyediek-e. Más szóval az ügyfél példányának egyedinek kell lennie az alkalmazás élettartama szempontjából.

Ha a TCP protokollon fut, az ügyfél a hosszú élettartamú kapcsolatok és a HTTPS protokoll használata esetén is optimalizálja a késést, amely 2 perc inaktivitás után leállítja a kapcsolatokat.

Olyan helyzetekben, ahol ritka hozzáférése van, és ha az átjáró mód eléréséhez képest nagyobb számú kapcsolatra van szüksége, a következőket teheti:

* Konfigurálja a [CosmosClientOptions. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) tulajdonságot a `PrivatePortPool` következőre: (a keretrendszer verziószáma>= 4.6.1 és a .net Core verziója >= 2,0): Ez a tulajdonság lehetővé teszi, hogy az SDK a különböző Azure Cosmos db végpontokhoz tartozó ideiglenes portok kis készletét használja.
* A [CosmosClientOptions. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) tulajdonság konfigurálásának 10 percnél nagyobbnak vagy azzal egyenlőnek kell lennie. Az ajánlott értékek 20 perc és 24 óra között vannak.

<a id="same-region"></a>

**A teljesítmény érdekében az azonos Azure-régióban lévő ügyfelek rézvezetékes végezhet**

Ha lehetséges, helyezzen olyan alkalmazásokat, amelyek a Azure Cosmos DB-adatbázissal megegyező régióban Azure Cosmos DB hívnak. Íme egy hozzávetőleges összehasonlítás: az azonos régión belüli Azure Cosmos DB meghívása 1 MS és 2 MS között fejeződik be, de az USA nyugati és keleti partja közötti késés meghaladja a 50 MS-ot. Ez a késés a kérelemtől függően változhat, attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure-adatközpont határán. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="A Azure Cosmos DB-kapcsolatok házirendje" border="false":::

   <a id="increase-threads"></a>

**A szálak/feladatok számának növelésével**

Mivel a Azure Cosmos DB kezdeményez a hálózaton keresztül, előfordulhat, hogy módosítania kell a kérések párhuzamossági fokát, hogy az ügyfélalkalmazás minimális ideig várjon a kérések között. Ha például a .NET- [feladatok párhuzamos könyvtárát](https://msdn.microsoft.com//library/dd460717.aspx)használja, akkor hozzon létre több száz feladatot a Azure Cosmos db olvasására vagy írására.

**Gyorsított hálózatkezelés engedélyezése**
 
 A késés és a CPU vibrálás csökkentése érdekében javasoljuk, hogy engedélyezze a gyorsított hálózatkezelést az ügyfél virtuális gépei között. Lásd: [Windows rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](../virtual-network/create-vm-accelerated-networking-powershell.md) vagy [linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-használat
**A legújabb SDK telepítése**

A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.

**Stream API-k használata**

A [.net SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) olyan stream API-kat tartalmaz, amelyek szerializálás nélkül fogadhatnak és adhatnak vissza adatforrást. 

A közepes szintű alkalmazások, amelyek nem használnak közvetlenül az SDK-ból származó válaszokat, de más alkalmazási rétegekbe továbbítják a stream API-k előnyeit. Tekintse át a stream-kezelés példáit az [elemek kezelése](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) című témakörben.

**Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

`CosmosClient`Az egyes példányok a Thread-Safe és a hatékony kapcsolatok kezelése és a címek gyorsítótárazása közvetlen üzemmódban való működés esetén. A hatékony és az SDK-ügyfél teljesítményének növelése érdekében ajánlott egyetlen példányt használni az `AppDomain` alkalmazás élettartamára.

Azure Functions használatakor a példányoknak a meglévő [irányelveket](../azure-functions/manage-connections.md#static-clients) is követniük kell, és egyetlen példányt kell fenntartaniuk.

<a id="max-connection"></a>

**Tartalom válaszának letiltása írási műveleteken**

A nagy mennyiségű létrehozási adattartalommal rendelkező munkaterhelések esetén a EnableContentResponseOnWrite-kérés beállítása false (hamis) lehet. A szolgáltatás többé nem fogja visszaadni a létrehozott vagy frissített erőforrást az SDK-nak. Általában az alkalmazás hozza létre az objektumot, így nincs szükség a szolgáltatás visszaküldésére. A fejléc értékei továbbra is elérhetők, mint a kérelmek díja. Ez javíthatja a teljesítményt, mert az SDK-nak többé nem kell memóriát lefoglalnia vagy szerializálnia a válasz törzsét. Ez csökkenti a hálózati sávszélesség-használatot is, hogy további segítséget nyújtson a teljesítményhez.  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

A **késleltetés helyett az átviteli sebesség optimalizálása tömeges engedélyezése** Tömeges engedélyezése olyan helyzetekben, amikor a munkaterhelésnek nagy mennyiségű átviteli sebességre van szüksége, és a késés nem annyira fontos. A szolgáltatás engedélyezésével és az általa használt forgatókönyvekkel kapcsolatos további információkért tekintse meg a [tömeges bevezetést](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk) .

**System.Net-MaxConnections növelésének engedélyezése gazdagépen az átjáró mód használatakor**

Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek, amikor átjáró üzemmódot használ. A rendszer az alapértelmezett kapcsolódási korlátot az állomásnév vagy az IP-cím alapján végzi. Előfordulhat, hogy `MaxConnections` magasabb értékre kell állítani (100 – 1 000), hogy az ügyféloldali kódtár több egyidejű kapcsolatot is használhat Azure Cosmos DBhoz. A .NET SDK 1.8.0 és újabb verzióiban a [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) alapértelmezett értéke 50. Az érték módosításához beállíthatja a [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) értéket magasabb értékre.

**A particionált gyűjtemények párhuzamos lekérdezésének hangolása**

Az SQL .NET SDK támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált tárolók párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy jobb lekérdezési késést és adatátviteli sebességet biztosítanak, mint a soros A párhuzamos lekérdezések két paramétert biztosítanak, amelyeket az igényeinek megfelelően hangolhat: 
- `MaxConcurrency` a párhuzamosan lekérdezhető partíciók maximális számát szabályozza. 
- `MaxBufferedItemCount` az előre lehívott eredmények számát szabályozza.

***Az egyidejűség finomhangolási foka***

A párhuzamos lekérdezés több partíció párhuzamos lekérdezésével működik. Az egyes partíciók adatait azonban a lekérdezésre vonatkozó sorosan kell beolvasni. `MaxConcurrency`Ha az [SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) -as verziójában a partíciók száma a legjobb, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, megadhatja a párhuzamosság mértékét magas számra. A rendszer kijelöli a minimális (partíciók számát, a felhasználó által megadott bemenetet) a párhuzamosság foka alapján.

A párhuzamos lekérdezések a legnagyobb haszonnal járnak, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény particionálva van, és a lekérdezés által visszaadott összes adat egy része néhány partícióra koncentrál (az egyik partíció a legrosszabb ESET), akkor ezek a partíciók a lekérdezés teljesítményét szűk keresztmetszetbe helyezik.

***MaxBufferedItemCount finomhangolása***
    
A párhuzamos lekérdezés úgy lett kialakítva, hogy előzetesen beolvassa az eredményeket, miközben az ügyfél az aktuális eredményt dolgozza fel. Ez az előzetes lehívás a lekérdezés teljes késésének javítására nyújt segítséget. A `MaxBufferedItemCount` paraméter korlátozza az előre lehívott eredmények számát. Állítsa be a `MaxBufferedItemCount` visszaadott eredmények várt számát (vagy egy magasabb számot), hogy a lekérdezés a lehető legtöbbet fogadja az előzetes lekéréstől.

Az előzetes lekérés ugyanúgy működik, függetlenül a párhuzamosság mértékétől, és egyetlen puffer van az összes partícióból származó adatokhoz.  

**Leállítási megvalósítása RetryAfter időközönként**

A teljesítmény tesztelése során növelje a terhelést, amíg a rendszer kis mennyiségű kérést nem szabályoz. Ha a kérelmek szabályozása megtörténik, az ügyfélalkalmazás a kiszolgáló által megadott újrapróbálkozási időközre vonatkozó biztonsági mentést hajt végre a szabályozásban. A leállítási tiszteletben tartásával biztosíthatja, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia. 

További információ: [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Létezik egy mechanizmus a további diagnosztikai információk naplózására és a késéssel kapcsolatos hibák elhárítására, ahogy az az alábbi példában is látható. Naplózhatja a diagnosztikai karakterláncot olyan kérésekhez, amelyek nagyobb olvasási késéssel rendelkeznek. A rögzített diagnosztikai karakterlánc segít megérteni, hogy hányszor kapott 429 hibát egy adott kérelemnél.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**A szálak/feladatok számának növelésével**

Lásd: [a szálak/feladatok számának növekedése](#increase-threads) a hálózatkezelés szakaszában.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
**Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

A Azure Cosmos DB indexelési házirend azt is lehetővé teszi, hogy az indexelési elérési utak (IndexingPolicy. IncludedPaths és IndexingPolicy. ExcludedPaths) használatával milyen dokumentum-elérési utakat tartalmazzon vagy zárhat ki az indexelésből. Csak a szükséges elérési utak indexelése javíthatja az írási teljesítményt, csökkentheti az írási műveletekhez tartozó RU-díjat, és csökkentheti az indexek tárolását olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték. Ennek az az oka, hogy az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával kapcsolatosak. Ez a kód például azt mutatja be, hogyan zárható ki a dokumentumok (egy részfa) teljes szakasza az indexelésből a "*" helyettesítő karakterrel:

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

További információ: [Azure Cosmos db indexelési házirendek](index-policy.md).

## <a name="throughput"></a>Átviteli sebesség
<a id="measure-rus"></a>

**Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja. Ezek a műveletek a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, az adatbázis-gyűjteményen belüli összes műveletre vonatkozó viszonyítási és hierarchikus lekérdezéseket tartalmazzák. Az egyes műveletekhez kapcsolódó díjak a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

A lekérdezés bonyolultsága befolyásolja, hogy a rendszer hány kérési egységet használ a műveletekhez. A predikátumok száma, a predikátumok természete, a UDF száma és a forrás-adatkészlet mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

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

Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség (azaz 2 000 RUs/s) része. Ha például az előző lekérdezés 1 000 1 – KB dokumentumokat ad vissza, a művelet díja 1 000. Tehát egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló a RequestRateTooLarge (429-as HTTP-állapotkód) végére fogja megelőző jelleggel a kérést. Egy [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet ad vissza, amely megadja, hogy a felhasználónak mennyi ideig kell várnia, mielőtt újra megkísérli a kérést.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a maximálisan engedélyezett műveleteket, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó CosmosException hoz az alkalmazáshoz. 

Az újrapróbálkozások alapértelmezett számának módosításához állítsa a `RetryOptions` példányt a `CosmosClientOptions` példányra. Alapértelmezés szerint a 429-as kódú CosmosException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez a hiba akkor is igaz, ha az újrapróbálkozások száma nem éri el az újrapróbálkozások maximális számát, az aktuális érték pedig az alapértelmezett 9 vagy felhasználó által definiált érték.

Az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását. Előfordulhat azonban, hogy a teljesítmény-teljesítménymutatók végrehajtásakor nem a legjobb viselkedés, különösen ha a késés mérését végzi. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.

**Nagyobb átviteli sebesség esetén a kisebb dokumentumok tervezése**

Egy adott műveletre vonatkozó kérelem díja (azaz a kérelmek feldolgozási díja) közvetlenül a dokumentum méretével összefügg. A nagyméretű dokumentumokon végzett műveletek többek között a kis dokumentumokon végzett műveletekhez szükségesek.

## <a name="next-steps"></a>További lépések
Az egyes ügyfélszámítógépeken a nagy teljesítményű forgatókönyvek Azure Cosmos DB kiértékeléséhez használt minta alkalmazással kapcsolatban lásd: [teljesítmény-és méretezési tesztek a Azure Cosmos db](performance-testing.md)használatával.

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partition-data.md)című témakört.
