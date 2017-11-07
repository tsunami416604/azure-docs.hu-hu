---
title: "Azure Cosmos DB: A tábla API a .NET fejlesztést |} Microsoft Docs"
description: "Ismerje meg, hogyan fejleszthet Azure Cosmos DB tábla API-t a .NET használatával"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/03/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: a4145f70af429274c3c908d3dedef63c5f973bf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: A tábla API a .NET fejlesztést

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ez az oktatóanyag ismerteti a következő feladatokat: 

> [!div class="checklist"] 
> * Azure Cosmos DB-fiók létrehozása 
> * A funkciónak az app.config fájlban 
> * Hozzon létre egy táblát az a [tábla API](table-introduction.md) (előzetes verzió)
> * Entitás hozzáadása a táblához 
> * Entitásköteg beszúrása 
> * Egyetlen entitás lekérdezése 
> * Lekérdezés entitások automatikus másodlagos indexek használata 
> * Entitás cseréje 
> * Entitás törlése 
> * Tábla törlése
 
## <a name="tables-in-azure-cosmos-db"></a>Az Azure Cosmos DB táblák 

Az Azure Cosmos DB biztosít a [tábla API](table-introduction.md) (előzetes verzió) egy kulcs-érték igénylő alkalmazásokat séma nélküli kialakítás tárolja, és magas througput követelményekkel rendelkezik. [Az Azure Table storage](../storage/common/storage-introduction.md) SDK-k és a REST API-k segítségével az Azure Cosmos Adatbázisba táblák használata.   

Ez az oktatóanyag a fejlesztők számára, aki ismeri az Azure Table storage SDK, és a premium szolgáltatásainak használatához az Azure Cosmos DB van. Alapul [Ismerkedés az Azure Table storage használatának .NET](table-storage-how-to-use-dotnet.md) és bemutatja, hogyan további szolgáltatásokat, például a másodlagos indexek, a létesített átviteli sebesség és a többszörös homing előnyeit. Ez az oktatóanyag leírja, hogyan használható az Azure-portálon hozzon létre egy Azure Cosmos DB fiókot létrehozása és egy tábla API-alkalmazás központi telepítése. A Microsoft .NET példákból létrehozása és egy tábla törlésével és beszúrni, frissítése, törlése és tábla adatok lekérdezése is ismerteti. 

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs telepítve a Visual Studio 2017, töltse le és használja a **szabad** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB fiókot az Azure portálon.  

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást.

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a mintatárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban.

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **kulcsok** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A másolási gombok fogjuk a képernyő jobb oldalán másolja a kapcsolati karakterláncot a következő lépésben az app.config fájlba kerülnek.

2. Nyissa meg az app.config fájlt a Visual Studióban. 

3. Az URI értéket másol a portálról (a Másolás gombra kattintva), és teszi a fiók-kulcsnak az értéke az App.config fájlban. Használja a korábban létrehozott fióknevet az App.config fájlban a fiók nevét.
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> Az alkalmazás használatához az Azure Table storage, a kapcsolati karakterlánc módosítani szeretné `app.config file`. Azure Storage elsődleges kulcsként használja tábla fióknevet és kulcsot a fiók nevét. <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>Hozza létre, és az alkalmazás központi telepítése
1. A Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, majd kattintson a **NuGet-csomagok kezelése** elemre. 

2. A NuGet **Tallózás** mezőjébe írja be a ***WindowsAzure.Storage-PremiumTable*** kifejezést. Ellenőrizze **előzetes verzióját tartalmazzák**.

3. Az eredmények közül telepítse a **windowsazure.Storage kifejezésre-PremiumTable** , és válassza a preview build `0.0.1-preview`. Ez a művelet telepíti az Azure Table storage csomag és az összes függősége.

4. Az alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt. 

Mostantól vissza a adatkezelő és tekintse meg a lekérdezés, módosíthatja, és a tábla adatokkal dolgozni. 

> [!NOTE]
> Az alkalmazás használatához az Azure Cosmos DB emulátorral, egyszerűen módosítani szeretné a kapcsolati karakterlánc `app.config file`. Használja a Emulator érték alá. <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB-képességek
Az Azure Cosmos DB tábla API számos olyan képességeket kínál, amelyek nem érhetők el az Azure Table storage-ban támogat. Az új funkciók engedélyezéséhez a következő `appSettings` konfigurációs értékeket. Új aláírásokat vagy túlterhelés nem érhetőek el a tábla API-t, amely nem található az az Azure Storage szolgáltatás SDK. Ez lehetővé teszi, hogy csatlakozzon az Azure Table storage mind az Azure Cosmos DB táblák, és az egyéb Azure Storage services hasonlóan működik. 


| Kulcs | Leírás |
| --- | --- |
| TableConnectionMode  | Azure Cosmos-adatbázis két csatlakozási módot támogat. A `Gateway` mód, mindig kérések az Azure Cosmos DB átjárón, amely továbbítja a megfelelő adatok partíciókat. A `Direct` csatlakozási mód, az ügyfél lekéri a táblák leképezése partíciókra, és a kérések közvetlenül az adatok partíciók szemben. Ajánlott `Direct`, az alapértelmezett.  |
| TableConnectionProtocol | Az Azure Cosmos DB támogatja két kapcsolat protokoll - `Https` és `Tcp`. `Tcp`az alapértelmezett és ajánlott, mivel az több egyszerűsített. |
| TablePreferredLocations | Előnyben részesített (többhelyű) helyek az olvasási műveletek vesszővel elválasztott listája. Minden Azure Cosmos DB fiókhoz társítható 1-30 + régiók. Minden ügyfél példány e régiók részhalmazát megadhat kis késleltetésű olvasása csatlakozási kísérleteinek kívánt sorrendjét. A régiók névvel kell ellátni használatával a [megjelenített neveket](https://msdn.microsoft.com/library/azure/gg441293.aspx), például `West US`. Lásd még: [több homing API-k](tutorial-global-distribution-table.md).
| TableConsistencyLevel | Akkor is kompromisszumot közötti késleltetés, konzisztencia- és rendelkezésre állás öt jól meghatározott konzisztenciaszintek közötti választással: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix`, és `Eventual`. Alapértelmezett érték a `Session`. A választott konzisztenciaszint lehetővé teszi több területi beállítások jelentős teljesítménybeli különbség. Lásd: [konzisztenciaszintek](consistency-levels.md) részleteiről. |
| TableThroughput | A következő táblázatban a kérelemegység (RU) másodpercenként kifejezett fenntartott átviteli sebességet. Egyetlen tábla RU/mp 100-as egység millióit képes támogatni. Lásd: [egységek kérelem](request-units.md). Alapértelmezett érték`400` |
| TableIndexingPolicy | Egységes és automatikus másodlagos indexelése lévő táblák oszlopok | Az indexelési házirendet specifikációjának megfelelő JSON-karakterláncban. Lásd: [indexelő házirend](indexing-policies.md) tekintheti meg, hogyan módosíthatja az egyes oszlopok belefoglalási/kizárási indexelési házirendet. | Automatikus indexeléshez levő összes tulajdonság (kivonatoló karakterláncok), és a számok tartománya |
| TableQueryMaxItemCount | Konfigurálja az egyetlen oda-vissza tábla lekérdezésenként visszaküldött elemek maximális számát. Alapértelmezett érték a `-1`, ami lehetővé teszi, hogy Azure Cosmos DB dinamikusan alapján határozhatja meg a futási időben. |
| TableQueryEnableScan | Ha a lekérdezés nem használja az index bármely szűrőt, majd futtassa ennek ellenére a vizsgálat keresztül. Alapértelmezett érték a `false`.|
| TableQueryMaxDegreeOfParallelism | A kereszt-partíció lekérdezés végrehajtási párhuzamossági fokát. `0`a nem lehívását, soros `1` van előre terjesztésekor, és a magasabb értékkel soros növekedjen párhuzamossági. Alapértelmezett érték a `-1`, ami lehetővé teszi, hogy Azure Cosmos DB dinamikusan alapján határozhatja meg a futási időben. |

Az alapértelmezett érték módosításához nyissa meg a `app.config` fájlt a Visual Studio megoldáskezelőjében. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Cserélje le `account-name` a tárfiók nevével és `account-key` a fiók hozzáférési kulccsal. 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.cs` fájlt, és található, az alábbi kódsorokat hozzon létre a tábla erőforrásokat. 

## <a name="create-the-table-client"></a>A tábla ügyfél létrehozása
Ön inicializálni egy `CloudTableClient` kapcsolódni a tábla fiókjához.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Ez az ügyfél inicializálása használatával a `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel`, és `TablePreferredLocations` konfigurációs értékei, ha az alkalmazás beállításaiban megadott.
    
## <a name="create-a-table"></a>Tábla létrehozása
Ezután létrehozhat egy tábla használatával `CloudTable`. Azure Cosmos DB táblák egymástól függetlenül is méretezhető tárolási és átviteli sebesség szempontjából, és particionálás kezeli automatikusan a szolgáltatás. Azure Cosmos-adatbázis a rögzített méretű és korlátlan táblák is támogatja. Lásd: [Azure Cosmos DB a particionálás](partition-data.md) részleteiről. 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

Nincs a táblák létrehozását egy fontos különbséggel. Cosmos. Azure-adatbázis fenntartja a teljesítményt, eltérően az Azure storage fogyasztás alapján modell az egyes tranzakciókra vonatkozóan. A foglalási modellnek két nagy előnye van:

* Az átviteli sebesség dedikált/foglalt, így Ön soha nem get szabályozva a kérelmek aránya vagy az alatt a létesített átviteli sebesség esetén
* A foglalási modell több [költséghatékony a teljesítmény-gyakori feladatok](key-value-store-cost.md)

Konfigurálja a beállítást úgy állíthatja be, az alapértelmezett átviteli `TableThroughput` RU (kérelemegység) másodpercenként tekintetében. 

Egy 1 KB-os entitás olvasási van normalizált 1 RU rögzített érték a Processzor, memória és IOPS fogyasztás alapján normalizálják RU és egyéb műveletekhez. További információ [Azure Cosmos DB egység kérelem](request-units.md).

> [!NOTE]
> A Table storage SDK jelenleg nem támogatja a módosítása átviteli, amíg az átviteli sebesség azonnal bármikor az Azure portálon vagy az Azure parancssori felület használatával módosíthatja.

A következő azt végezze el az egyszerű olvasható, és írási (CRUD) típusú műveletek az Azure Table storage SDK használatával. Ez az oktatóanyag azt mutatja be, előre jelezhető alacsony egyjegyű ezredmásodperces késések és Azure Cosmos DB által nyújtott gyors lekérdezéseket.

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Az Azure Table storage entitások terjessze ki a a `TableEntity` osztályhoz, és rendelkeznie kell `PartitionKey` és `RowKey` tulajdonságok. Íme egy minta definíciója egy ügyfélentitást.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Az alábbi kódrészletben láthatja az Azure storage SDK rendelkező entitás beszúrása. Azure Cosmos DB végzi a kis késleltetésű bármilyen léptékben garantált keresztül történik.

Írási műveletek befejezése < 15 ms p99 és ~ 6 ms: p50 ugyanabban a régióban, az Azure Cosmos DB fiókként futó alkalmazások. És ennek az időtartamnak a tényt, hogy írási műveletek is vonatkozik, az ügyfél csak azokat a rendszer szinkron módon replikálja, tartósan véglegesítve lett, és minden tartalom indexelt után tartozó fiókok.

A tábla API-t a Azure Cosmos DB jelenleg előzetes verzióban érhető. Általánosan rendelkezésre álló p99 késés garanciák SLA-k, például a más Azure Cosmos DB API-k által támogatott. 

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Az Azure Table storage támogatja a kötegelt művelet API-t, amely lehetővé teszi, hogy kombinálhatja a frissítéseket, törléseket és beszúrásokat az egyetlen kötegművelettel. Azure Cosmos-adatbázis nem lehet a korlátozások némelyike a kötegelt API-t az Azure Table storage. Például egy kötegelt belül többszöri beolvasás végezheti el, több írási műveleteket ad ki ugyanaz az entitás egy kötegelt belül végezheti el, és kötegenként 100 műveletek korlátozva van. 

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
(Lekérdezi) lekérdezi a teljes Azure Cosmos DB < 10 ms p99 és ~ 1 p50 azonos Azure-régióban, ms. Annyi régiók hozzáadnia a fiókhoz a kis késleltetésű olvasása, és telepítsen alkalmazásokat úgy, hogy a helyi régió ("többhelyű") olvasni `TablePreferredLocations`. 

Az alábbi kódrészletben használatával egyetlen entitás le:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> További tudnivalók a többhelyű API-k [fejlesztés több régióba az](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Lekérdezés entitások automatikus másodlagos indexek használata
Táblázatok használatával lehet lekérdezni a `TableQuery` osztály. Azure Cosmos-adatbázis egy adatbázis írási optimalizált motor, amely automatikusan elvégzi a belül a táblázat összes oszlopa van. Az Azure Cosmos Adatbázisba indexelő független sémát. Ezért még akkor is, ha a séma sorok különböznek, vagy a séma fejlődésének adott idő alatt, akkor automatikusan indexelt. Mivel Azure Cosmos DB támogatja az automatikus másodlagos indexek, lekérdezések egyik tulajdonságnak sem használhatja az index, és hatékonyan kell kézbesíteni.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

A képen Azure Cosmos DB Azure Table storage azonos lekérdezés funkciókat támogatja a tábla API-hoz. Azure Cosmos-adatbázis is támogatja a rendezést, összesítések, a földrajzi lekérdezést, a hierarchia és a számos különféle beépített funkciók. A további funkciókat nyújtanak a jövőbeli szolgáltatásfrissítés tábla API. Lásd: [Azure Cosmos adatbázis-lekérdezés](documentdb-sql-query.md) ezeket a képességeket áttekintését. 

## <a name="replace-an-entity"></a>Entitás cseréje
Ha frissíteni kíván egy entitást, kérje le a Table szolgáltatásból, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table szolgáltatásba. A következő kód egy meglévő ügyfél telefonszámát módosítja. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Hasonló módon végezheti el `InsertOrMerge` vagy `Merge` műveletek.  

## <a name="delete-an-entity"></a>Entitás törlése
A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Tábla törlése
Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. Töröl, és hozza létre újból az azonnali Azure Cosmos DB tartalmazó tábla.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon.   

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra. 

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag azt a kezelt Ismerkedés az Azure Cosmos DB használatával tábla API-val, és ezt a következő: 

> [!div class="checklist"] 
> * Egy Azure Cosmos DB-fiók létrehozása 
> * Engedélyezett funkciókat az app.config fájlban 
> * Táblázat létrehozása 
> * Egy entitás hozzá 
> * Szúrja be egy teljes entitásköteget 
> * Egyetlen entitás lekérése 
> * Automatikus másodlagos indexek használatával lekérdezett entitásokat 
> * Entitás cseréje 
> * Egy entitás törlése 
> * Tábla törlése  

Ezután folytassa a következő oktatóanyag és tudhat meg többet a tábla adatainak lekérdezésekor. 

> [!div class="nextstepaction"]
> [A tábla API lekérdezés](tutorial-query-table.md)
