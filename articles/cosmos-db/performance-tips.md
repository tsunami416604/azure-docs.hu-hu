---
title: Azure Cosmos DB teljesítményre vonatkozó tippek a .NET-hez
description: Az ügyfél-konfigurációs beállítások megismerése az Azure Cosmos Database teljesítményének növeléséhez
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: eec5ab6cdf4afd63db2e77046bb19436e600ece6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720996"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>A Azure Cosmos DB és a .NET teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információkért lásd: [a tárolók teljesítményének kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis átviteli sebességének kiépítése](how-to-provision-database-throughput.md). Mivel azonban a Azure Cosmos DB hálózati hívásokkal érhetők el, ügyféloldali optimalizálási lehetőségek érhetők el, amelyekkel elérheti a maximális teljesítményt az [SQL .net SDK](sql-api-sdk-dotnet-standard.md)használatakor.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? Vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózatkezelés
<a id="direct-connection"></a>

1. **Csatlakoztatási házirend: közvetlen kapcsolási mód használata**

    Az ügyfél Azure Cosmos DB-hez való csatlakozásának módja jelentősen befolyásolja a teljesítményt, főként a megfigyelt ügyféloldali késés szempontjából. Az ügyfélkapcsolati házirend konfigurálásához két fő konfigurációs beállítás érhető el – a kapcsolati *mód* és a kapcsolati *protokoll*.  A két elérhető mód a következők:

   * Átjáró üzemmód
      
     Az átjáró üzemmód minden SDK platformon támogatott, és a [Microsoft. Azure. DOCUMENTDB SDK](sql-api-sdk-dotnet.md)számára beállított alapértelmezett érték. Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró mód a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az összes adat beolvasása vagy írása Azure Cosmos DB. Emiatt a közvetlen mód jobb teljesítményt nyújt kevesebb hálózati ugrás miatt. Az átjáró kapcsolódási módja akkor is ajánlott, ha korlátozott számú szoftvercsatorna-kapcsolattal rendelkező környezetekben futtat alkalmazásokat. 

     Ha az SDK-t Azure Functions, különösen a használati [tervben](../azure-functions/functions-scale.md#consumption-plan)használja, vegye figyelembe a [kapcsolatok jelenlegi korlátait](../azure-functions/manage-connections.md). Ebben az esetben előfordulhat, hogy az átjáró mód akkor ajánlott, ha más HTTP-alapú ügyfelekkel is dolgozik a Azure Functions alkalmazáson belül.

   * Közvetlen mód

     A közvetlen mód a TCP protokollon keresztüli csatlakozást támogatja, és az alapértelmezett csatlakozási mód, ha a [Microsoft. Azure. Cosmos/.net v3 SDK-](sql-api-sdk-dotnet-standard.md)t használja.

     Az átjáró mód használata esetén a Cosmos DB a 443-es portot és a 10250-es, 10255-as és 10256-es portokat használja a MongoDB API-k Azure Cosmos DB használatakor. Az 10250-es port az alapértelmezett MongoDB-példányra van leképezve, a Geo-replikálás és a 10255/10256-portok nélkül, a MongoDB-példányhoz a Geo-replikálási funkcióval. Ha a TCP-t közvetlen módban használja, az átjáró portjain kívül meg kell győződnie arról, hogy a 10000 és a 20000 közötti porttartomány meg van nyitva, mert Azure Cosmos DB dinamikus TCP-portokat használ. Ha ezek a portok nincsenek megnyitva, és a TCP-t próbálja használni, a 503-es szolgáltatás nem érhető el hibaüzenetet kap. Az alábbi táblázat a különböző API-k és a szolgáltatási portok felhasználói számára elérhető csatlakozási módokat mutatja be az egyes API-kra vonatkozóan:

     |Csatlakoztatási mód  |Támogatott protokoll  |Támogatott SDK-k  |API/szolgáltatás portja  |
     |---------|---------|---------|---------|
     |Átjáró  |   HTTPS    |  Minden SDK    |   SQL (443), Mongo (10250, 10255, 10256), tábla (443), Cassandra (10350), Graph (443)    |
     |Direct    |     TCP    |  .NET SDK    | 10 000 000 tartományon belüli portok |

     Azure Cosmos DB egy egyszerű és nyitott, REST-alapú programozási modellt biztosít a HTTPS-en keresztül. Emellett hatékony TCP protokollt is biztosít, amely a kommunikációs modellben is elérhető, és a .NET Client SDK-n keresztül érhető el. A TCP protokoll SSL protokollt használ a kezdeti hitelesítéshez és a forgalom titkosításához. A legjobb teljesítmény érdekében a TCP protokollt használja, ha lehetséges.

     Az SDK V3 esetében a kapcsolati mód a CosmosClient-példány létrehozásakor van konfigurálva, a CosmosClientOptions részeként ne feledje, hogy a közvetlen mód az alapértelmezett.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     A Microsoft. Azure. DocumentDB SDK esetében a kapcsolati mód a DocumentClient-példánynak a ConnectionPolicy paraméterrel való létrehozásakor van konfigurálva. Ha a közvetlen mód van használatban, a protokoll a ConnectionPolicy paraméteren belül is megadható.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Mivel a TCP csak közvetlen módban támogatott, az átjáró mód használata esetén a rendszer mindig a HTTPS protokollt használja az átjáróval való kommunikációhoz, és a protokoll értékét a ConnectionPolicy figyelmen kívül hagyja.

     ![A Azure Cosmos DB-kapcsolatok házirendjének ábrája](./media/performance-tips/connection-policy.png)

2. **OpenAsync meghívása az első kérés indítási késésének elkerülése érdekében**

    Alapértelmezés szerint az első kérelem nagyobb késéssel jár, mert be kell olvasnia a címek útválasztási tábláját. Ha az [SDK v2](sql-api-sdk-dotnet.md)-t használja, hogy elkerülje az indítási késést az első kérelemnél, a következő módon hívja meg a OpenAsync () függvényt az inicializálás során.

        await client.OpenAsync();

    > [!NOTE] 
    > A OpenAsync metódus a fiókban lévő összes tárolóhoz tartozó címek útválasztási táblázatának beolvasására vonatkozó kérelmeket hoz létre. Az olyan fiókok esetében, amelyek sok tárolóval rendelkeznek, de az alkalmazásuk hozzájuk fér hozzájuk egy részhalmazához, felesleges forgalmat eredményezne, ami lelassítja az inicializálást. Így előfordulhat, hogy a OpenAsync metódus használata nem hasznos ebben a forgatókönyvben, mert lelassítja az alkalmazás indítását.

   <a id="same-region"></a>
3. **Rézvezetékes végezhet-ügyfelek ugyanabban az Azure-régióban a teljesítmény érdekében**

    Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint az Azure Cosmos Database. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés > 50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

    ![a Azure Cosmos DB-kapcsolatok házirendjének illusztrációja](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Szálak/feladatok számának növekedése**

    Mivel a hálózatra irányuló Azure Cosmos DB hívások a hálózaton keresztül történnek, lehetséges, hogy a kérések párhuzamosságának mértékét módosítani kell, hogy az ügyfélalkalmazás nagyon kevés időt töltsön a kérések között. Ha például a-t használja. A NET [feladatának párhuzamos könyvtára](https://msdn.microsoft.com//library/dd460717.aspx), amely a Azure Cosmos DBba olvasott vagy írt feladatok (100s) sorrendjében hozható létre.

5. **Gyorsított hálózatkezelés engedélyezése**

   A késés és a CPU vibrálás csökkentése érdekében javasoljuk, hogy az ügyfél virtuális gépei gyorsított hálózatkezelést engedélyezzenek. A gyorsított hálózatkezelés lehetővé tételéhez tekintse meg a [Windows rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozását](../virtual-network/create-vm-accelerated-networking-powershell.md) , vagy [hozzon létre egy linuxos virtuális gépet a gyorsított](../virtual-network/create-vm-accelerated-networking-cli.md) hálózatkezelési cikkekkel.


## <a name="sdk-usage"></a>SDK-használat
1. **A legújabb SDK telepítése**

    A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](sql-api-sdk-dotnet-standard.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.

2. **Stream API-k használata**

    A [.net SDK v3](sql-api-sdk-dotnet-standard.md) olyan stream API-kat tartalmaz, amelyek szerializálás nélkül fogadhatnak és adhatnak vissza adatforrást. 

    Azok a középső szintű alkalmazások, amelyek nem használják közvetlenül az SDK válaszait, de más alkalmazási rétegekbe továbbítják őket, a stream API-k előnyeit élvezheti. A streamek kezelésével kapcsolatos példákért tekintse meg az [elemek kezelési](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) mintáit.

3. **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

    Az egyes DocumentClient-és CosmosClient-példányok a szálon biztonságosak, és hatékonyan kezelik a kapcsolatok felügyeletét és a címek gyorsítótárazását közvetlen módban való működés közben. Annak érdekében, hogy az SDK-ügyfél hatékony legyen a kapcsolatok kezeléséhez és jobb teljesítményhez, a alkalmazástartomány egyetlen példányát ajánlott használni az alkalmazás élettartama szempontjából.

   <a id="max-connection"></a>
4. **System.Net-MaxConnections növelésének engedélyezése gazdagépen az átjáró mód használatakor**

    Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek az átjáró mód használatakor, és a rendszer az alapértelmezett kapcsolati korlátot adja meg egy állomásnév vagy IP-cím alapján. Előfordulhat, hogy a MaxConnections magasabb értékre (100-1000) kell állítania, hogy az ügyféloldali kódtár több egyidejű kapcsolatot is felhasználjon Azure Cosmos DBhoz. A .NET SDK 1.8.0-ben és a fentiekben a [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) alapértelmezett értéke 50, és az érték módosításához beállíthatja a [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) értéket magasabb értékre.   
5. **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

     Az SQL .NET SDK 1.9.0 és újabb verziója támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik A párhuzamos lekérdezések két olyan paramétert biztosítanak, amelyeket a felhasználók az egyéni igényeknek megfelelően módosíthatnak (a) Maxanalyticsunits: a partíciók maximális számának szabályozása érdekében párhuzamosan lekérdezhető, és (b) MaxBufferedItemCount: a (z) számának szabályozása előre lehívott eredmények.

    (a) a párhuzamossági ***fok\:*** párhuzamos lekérdezés működik párhuzamosan több partíció lekérdezésével. Az egyes partíciók adatait azonban a lekérdezésre vonatkozó sorosan kell beolvasni. Ha az SDK [v2](sql-api-sdk-dotnet.md) -ben `MaxConcurrency` vagy az [SDK v3](sql-api-sdk-dotnet-standard.md) -ban lévő `MaxDegreeOfParallelism`t a partíciók számának értékre állítja, akkor a legtöbb elvégezhető lekérdezés maximális eséllyel érhető el, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, megadhatja a párhuzamosság mértékét magas értékre, és a rendszer a minimális (partíciók száma, felhasználó által megadott bemenet) szintet választja a párhuzamosság fokaként.

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

    (b) a ***MaxBufferedItemCount finomhangolása\:*** párhuzamos lekérdezés az eredmények előzetes beolvasására szolgál, miközben az ügyfél dolgozza fel az aktuális eredményt. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. Az MaxBufferedItemCount az a paraméter, amellyel korlátozható az előre lehívott eredmények száma. A MaxBufferedItemCount beállítása a visszaadott eredmények várt számához (vagy egy nagyobb szám) lehetővé teszi, hogy a lekérdezés a lehető legtöbb előnyt fogadja az előzetes lekéréstől.

    Az előzetes lekérés ugyanúgy működik, mint a párhuzamosság foka, és egyetlen puffer van az összes partícióból származó adatokhoz.  
6. **Kiszolgálóoldali GC bekapcsolása**

    Bizonyos esetekben a Garbage-gyűjtemények gyakoriságának csökkentése segíthet. A .NET-ben állítsa a [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) igaz értékre.
7. **Leállítási megvalósítása RetryAfter időközönként**

    A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha a szabályozás meg van adva, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköz szabályozása érdekében. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia. Az újrapróbálkozási szabályzat támogatása a 1.8.0 és az SQL [.net](sql-api-sdk-dotnet.md) és a [Java](sql-api-sdk-java.md), valamint a [Node. js](sql-api-sdk-node.md) és a [Python](sql-api-sdk-python.md), valamint a [.net Core](sql-api-sdk-dotnet-core.md) SDK-k összes támogatott verziójának 1.9.0-verziójában található. További információ: [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    A .NET SDK 1,19-es és újabb verzióiban létezik egy mechanizmus a további diagnosztikai információk naplózására és a késéssel kapcsolatos hibák elhárítására az alábbi példában látható módon. Naplózhatja a diagnosztikai karakterláncot olyan kérésekhez, amelyek nagyobb olvasási késéssel rendelkeznek. A rögzített diagnosztikai sztring segít megérteni, hogy az adott kérelem 429s hányszor észlelt.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
8. **Az ügyfél felskálázása – munkaterhelés**

    Ha nagy átviteli sebességű > (50000 RU/s) szintű tesztelést végez, akkor előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a PROCESSZORon vagy a hálózat kihasználtságán túl. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.
9. **Az alacsonyabb olvasási késéshez tartozó URI-k gyorsítótárazása**

    Ha lehetséges, a legjobb olvasási teljesítmény érdekében gyorsítótárazza a dokumentumok URI-azonosítóit. Az erőforrás létrehozásakor meg kell határoznia a ResourceId gyorsítótárazásához szükséges logikát. A ResourceId-alapú keresések gyorsabbak, mint a neveken alapuló keresések, ezért az értékek gyorsítótárazása javítja a teljesítményt. 

   <a id="tune-page-size"></a>
10. **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

   Ha a dokumentumok tömeges olvasását az olvasási hírcsatorna funkció (például ReadDocumentFeedAsync) vagy egy SQL-lekérdezés kiadása során hajtja végre, akkor az eredmények szegmentált módon lesznek visszaadva, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

   Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör alakú utak számát, az [x-MS-Max-Item-](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) Request fejléc használatával növelheti az oldal méretét akár 1000-ra. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API csak 10 eredményt ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

   > [!NOTE] 
   > A maxItemCount tulajdonságot nem szabad kizárólag tördelési célra használni. Ez a legfontosabb használat a lekérdezések teljesítményének javítására azáltal, hogy csökkenti az egyetlen oldalon visszaadott elemek maximális számát.  

   Az oldalméret az elérhető Azure Cosmos DB SDK-k használatával is beállítható. A FeedOptions [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) tulajdonsága lehetővé teszi, hogy beállítsa a számbavételi műveletben visszaadott elemek maximális számát. Ha a `maxItemCount` értéke-1, az SDK automatikusan megkeresi a legoptimálisabb értéket a dokumentum méretétől függően. Példa:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Egy lekérdezés végrehajtásakor az eredményül kapott adatküldés egy TCP-csomagon belül történik. Ha a `maxItemCount`túl alacsony értéket ad meg, a TCP-csomagban lévő adatküldéshez szükséges utak száma magas, ami hatással van a teljesítményre. Tehát ha nem biztos benne, hogy melyik értéket kell beállítani a `maxItemCount` tulajdonsághoz, akkor a legjobb, ha az-1 értékre állítja, és lehetővé teszi, hogy az SDK kiválassza az alapértelmezett értéket. 

11. **Szálak/feladatok számának növekedése**

    Lásd: a [szálak/feladatok számának növekedése](#increase-threads) a hálózatkezelés szakaszban.

12. **64 bites gazdagép-feldolgozás használata**

    Az SQL SDK 32 bites gazdagép-folyamattal működik, ha az SQL .NET SDK 1.11.4 vagy újabb verzióját használja. Ha azonban több partíciós lekérdezést használ, a jobb teljesítmény érdekében ajánlott a 64 bites gazdagép-feldolgozás. A következő típusú alkalmazások alapértelmezettként 32 bites gazdagép-folyamattal rendelkeznek, így a 64 bitesre való váltáshoz kövesse az alábbi lépéseket az alkalmazás típusától függően:

    - A végrehajtható alkalmazások esetében ez az **előnyben részesített 32-bites** lehetőség törlésével végezhető el a **Projekt tulajdonságai** ablakban a **Build** lapon.

    - A VSTest-alapú tesztelési projektek esetében ehhez válassza a **tesztelés**->**tesztelési beállítások**->**alapértelmezett processzor-architektúra x64-ként**lehetőséget a **Visual Studio test** menüpontban.

    - Helyileg telepített ASP.NET-webalkalmazások esetén ezt a **webhelyekhez és projektekhez készült IIS Express 64 bites verziójának használata**a következő **eszközök**->**lehetőségek**->**projektek és megoldások**->**webes projektek**menüpontban végezheti el.

    - Az Azure-on üzembe helyezett webalkalmazások esetében ez a **Platform 64 bitesként** való kiválasztásával végezhető el **a Azure Portal** ASP.net.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
1. **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    A Cosmos DB indexelési házirendje azt is lehetővé teszi, hogy az indexelési elérési utak (IndexingPolicy. IncludedPaths és IndexingPolicy. ExcludedPaths) használatával megadhatja, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva.  Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok (egy részfa) teljes szakasza az indexelésből a "*" helyettesítő karakter használatával.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    További információ: [Azure Cosmos db indexelési házirendek](index-policy.md).

## <a name="throughput"></a>Adatforgalom
<a id="measure-rus"></a>

1. **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

    A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, és az adatbázis-gyűjteményen belüli összes műveletet. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

    Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt. 

    A lekérdezés bonyolultsága befolyásolja, hogy hány kérelem-egységet használ a művelet. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok készletének mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

    Az összes művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet (vagy a ResourceResponse\<t > vagy FeedResponse\<t > a .net SDK-ban) a műveletek által felhasznált kérelmek mennyiségének méréséhez.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség (azaz 2000 RUs/másodperc) része. Ha például az előző lekérdezés 1000 1KB ad vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó DocumentClientException hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható úgy, hogy beállítja a RetryOptions a ConnectionPolicy-példányon. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során.  Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.
3. **Kisebb dokumentumok tervezése magasabb átviteli sebesség esetén**

    Egy adott műveletre vonatkozó kérések (például a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak.

## <a name="next-steps"></a>Következő lépések
Az egyes ügyfélszámítógépeken a nagy teljesítményű forgatókönyvek Azure Cosmos DB kiértékeléséhez használt minta alkalmazás esetében tekintse meg a [teljesítmény-és méretezési tesztet a Azure Cosmos DBával](performance-testing.md).

Ha többet szeretne megtudni a méretezési és a nagy teljesítménybeli alkalmazások tervezéséről, olvassa el [a particionálás és skálázás a Azure Cosmos DBban](partition-data.md)című témakört.
