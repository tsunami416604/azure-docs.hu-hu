---
title: 'Azure Cosmos DB: Fejlesztés a Table API-val .NET-keretrendszerben | Microsoft Docs'
description: Megtudhatja, hogyan fejleszthet az Azure Cosmos DB Table API-jával a .NET-keretrendszerben
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e6511b9511d2598b58fd3afee34803ceb09ac5ce
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582852"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Fejlesztés a Table API-val .NET-keretrendszerben

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ez az oktatóanyag a következő feladatokat mutatja be: 

> [!div class="checklist"] 
> * Azure Cosmos DB-fiók létrehozása 
> * Az app.config fájl funkcióinak engedélyezése 
> * Tábla létrehozása a [Table API-val](table-introduction.md)
> * Entitás hozzáadása a táblához 
> * Entitásköteg beszúrása 
> * Egyetlen entitás lekérdezése 
> * Entitások lekérdezése automatikus másodlagos indexekkel 
> * Entitás cseréje 
> * Entitás törlése 
> * Tábla törlése
 
## <a name="tables-in-azure-cosmos-db"></a>Táblák az Azure Cosmos DB-ben 

Az Azure Cosmos DB elérhetővé teszi a [Table API-t](table-introduction.md) az olyan alkalmazások számára, amelyeknek séma nélküli kulcsérték-tárolóra van szükségük. Mostantól az Azure Cosmos DB Table API és az [Azure Table Storage](../storage/common/storage-introduction.md) is ugyanazokat az SDK-kat és REST API-kat támogatja. Az Azure Cosmos DB használatával nagy átviteli sebességet megkövetelő táblákat hozhat létre.

Ez az oktatóanyag olyan fejlesztőknek szól, akik ismerik az Azure Table Storage SDK-t, és Azure Cosmos DB-vel elérhető prémium szolgáltatásokat szeretnék használni. [Az Azure Table Storage a .NET-keretrendszerrel történő használatának első lépésein](table-storage-how-to-use-dotnet.md) alapul, és bemutatja, hogyan használhatja ki az olyan további képességek előnyeit, mint például a másodlagos indexek, a kiosztott átviteli sebesség és a több kiszolgáló használata. Ez az oktatóanyag azt ismerteti, hogyan használhatja az Azure Portalt Azure Cosmos DB-fiók létrehozásához, majd hogyan állíthat össze és helyezhet üzembe egy Table API-alkalmazást. A táblák létrehozásának és törlésének, valamint a táblaadatok beszúrásának, frissítésének, törlésének és lekérdezésének .NET-példáit is ismertetjük. 

Ha jelenleg az Azure Table Storage-et használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API használatakor:

- Kulcsrakész [globális terjesztés](distribute-data-globally.md) több kiszolgálóval és [automatikus és manuális feladatátvételekkel](regional-failover.md).
- Az automatikus sémafüggetlen indexelés támogatása minden tulajdonsághoz („másodlagos indexek”) és gyors lekérdezéshez 
- [A tároló és az átviteli sebesség független méretezése](partition-data.md) bármennyi régió között
- A [táblánkénti dedikált átviteli sebesség](request-units.md) támogatása, amely másodpercenként több száztól akár több millió kérésig méretezhető
- [Öt beállítható konzisztenciaszint](consistency-levels.md) támogatása, amelyekkel az alkalmazás igényeinek megfelelően szabályozható a rendelkezésre állás, a késés és a konzisztencia
- 99,99%-os rendelkezésre állás egyetlen régióban, valamint lehetőség van további régiók felvételére a magasabb rendelkezésre állás céljából, továbbá az általános rendelkezésre állás [iparágvezető és átfogó SLA-i](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- A meglévő Azure Storage .NET SDK-t használhatja, és nincs szükség kódmódosításokra az alkalmazásban

Ez az oktatóanyag az Azure Cosmos DB Table API .NET SDK-val való használatát mutatja be. Az [Azure Cosmos DB Table API .NET SDK-t](https://aka.ms/tableapinuget) a NuGetről töltheti le.

Az összetett Azure Table Storage-feladatokkal kapcsolatos további információért lásd:

* [Alapvető ismeretek az Azure Cosmos DB Table API használatáról](table-introduction.md)
* A Table Service az elérhető API-kat részletesen ismertető referenciadokumentációjáért lásd: [Azure Cosmos DB Table API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag olyan fejlesztőknek szól, akik ismerik az Azure Table Storage SDK-t, és Azure Cosmos DB-vel elérhető prémium szolgáltatásokat szeretnék használni. [Az Azure Table Storage a .NET-keretrendszerrel történő használatának első lépésein](table-storage-how-to-use-dotnet.md) alapul, és bemutatja, hogyan használhatja ki az olyan további képességek előnyeit, mint például a másodlagos indexek, a kiosztott átviteli sebesség és a több kiszolgáló használata. Bemutatjuk, hogyan használhatja az Azure Portalt Azure Cosmos DB-fiók létrehozásához, majd hogyan állíthat össze és helyezhet üzembe egy Table-alkalmazást. A táblák létrehozásának és törlésének, valamint a táblaadatok beszúrásának, frissítésének, törlésének és lekérdezésének .NET-példáit is ismertetjük. 

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB-fiókot az Azure Portalon.  
 
> [!IMPORTANT]  
> Létre kell hoznia egy új Table API-fiókot, amely használható az általánosan elérhető Table API SDK-kkal. Az általánosan elérhető SDK-k nem támogatják az előzetes verzióban létrehozott Table API-fiókokat. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson a mappára, ahol telepíteni szeretné a mintaalkalmazást. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](http://portal.azure.com/) kattintson a **Kapcsolati sztring** elemre. 

    A képernyő jobb oldalán található másolási gombok használatával másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét.

    ![Tekintse meg és másolja a vágólapra a CONNECTION STRING (Kapcsolati sztring) értékét a Kapcsolati sztring ablaktáblán](./media/create-table-dotnet/connection-string.png)

2. Nyissa meg az app.config fájlt a Visual Studióban. 

3. Állítsa vissza a 8. sorban található StorageConnectionString értéket, és tegye megjegyzésbe a 7. sorban szereplő StorageConnectionString értéket, mert ebben az oktatóanyagban nem fogjuk használni a Storage Emulatort. A 7. és a 8. sornak így kell kinéznie:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Illessze be a PRIMARY CONNECTION STRING (Elsődleges kapcsolati sztring) értékét a portálról a 8. sor StorageConnectionString értékébe. Illessze be a sztringet az idézőjelek közé.
   
    > [!IMPORTANT]
    > Ha a végpont a documents.azure.com címet használja, akkor előzetes fiókkal rendelkezik, és létre kell hoznia egy [új Table API-fiókot](#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal. 
    >

    A 8. sornak ekkor a következőképp kell kinéznie:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Mentse az app.config fájlt.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB-képességek
Az Azure Cosmos DB számos olyan képességet támogat, amely az Azure Table Storage API-ban nem érhető el. 

Bizonyos funkciókat a CreateCloudTableClient új túlterheléseivel érhet el, amelyek lehetővé teszik a kapcsolódási szabályzat és a konzisztenciaszint meghatározását.

| Tábla kapcsolati beállításai | Leírás |
| --- | --- |
| Kapcsolat módja  | Az Azure Cosmos DB két csatlakozási módot támogat. `Gateway` módban a kérések mindig az Azure Cosmos DB-átjáróra kerülnek, amely a megfelelő adatpartíciókra továbbítja azokat. `Direct` csatlakozási módban az ügyfél lekéri a táblák partícióra történő leképezését, és a kérések közvetlenül az adatpartíciókra kerülnek. Alapértelmezés szerint a `Direct` használata javasolt.  |
| Kapcsolati protokoll | Az Azure Cosmos DB két kapcsolati protokollt támogat – ez a `Https` és a `Tcp`. A `Tcp` az alapértelmezett és ajánlott, mert kevesebb erőforrást használ. |
| Előnyben részesített helyek | Az előnyben részesített (többkiszolgálós) helyek vesszővel elválasztott listája az olvasásokhoz. Mindegyik Azure Cosmos DB-fiók 1–30+ régióval társítható. Minden ügyfélpéldány a kívánt sorrendben adhatja meg ezen régiók részhalmazát a kis késleltetésű olvasásokhoz. A régiókat a [megjelenített nevükkel](https://msdn.microsoft.com/library/azure/gg441293.aspx) kell elnevezni, például: `West US`. Lásd még a [többkiszolgálós API-k](tutorial-global-distribution-table.md) témakörét. |
| Konzisztenciaszint | Öt jól meghatározott konzisztenciaszinttel szabályozhatja a késleltetést, a konzisztenciát és a rendelkezésre állást: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` és `Eventual`. Az alapértelmezett szint a `Session`. A kiválasztott konzisztenciaszint jelentős teljesítménybeli különbséget jelent a többrégiós környezetekben. Részletekért lásd a [konzisztenciaszintek](consistency-levels.md) témakörét. |

Más funkciók is engedélyezhetők az `appSettings` következő konfigurációs értékeivel.

| Kulcs | Leírás |
| --- | --- |
| TableQueryMaxItemCount | Konfigurálhatja az egyetlen adatváltási körben táblalekérdezésenként visszaadott elemek maximális számát. Az alapértelmezett érték a `-1`, így az Azure Cosmos DB dinamikusan határozhatja meg az értéket a futtatáskor. |
| TableQueryEnableScan | Ha a lekérdezés nem tudja az indexet használni egyik szűrőhöz sem, akkor is futtatja azt egy vizsgálattal. Az alapértelmezett érték a `false`.|
| TableQueryMaxDegreeOfParallelism | A párhuzamosság foka partíciók közötti lekérdezések végrehajtásakor. A `0` sorost jelöl, előzetes beolvasás nélkül, az `1` sorost jelöl, előzetes beolvasással, a magasabb értékek pedig növelik a párhuzamossági arányt. Az alapértelmezett érték a `-1`, így az Azure Cosmos DB dinamikusan határozhatja meg az értéket a futtatáskor. |

Az alapértelmezett érték módosításához nyissa meg az `app.config` fájlt a Megoldáskezelőből a Visual Studióban. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Az `account-name` kifejezést cserélje a tárfiókja nevére, az `account-key` kifejezést pedig a hozzáférési kulcsra. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.cs` fájlt, és láthatja, hogy ezek a kódsorok táblaerőforrásokat hoznak létre. 

## <a name="create-the-table-client"></a>A táblaügyfél létrehozása
Egy `CloudTableClient` eszközt inicializál a táblafiókhoz történő csatlakozáshoz.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Az ügyfél az alkalmazás beállításaiban meghatározott `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` és `TablePreferredLocations` konfigurációs értékkel inicializálható.

## <a name="create-a-table"></a>Tábla létrehozása
Ezután létrehozhat egy táblát a `CloudTable` használatával. Az Azure Cosmos DB-ben a táblák egymástól függetlenül méretezhetők a tárolás és az átviteli teljesítmény szempontjából, és a szolgáltatás automatikusan kezeli a particionálást. Az Azure Cosmos DB a rögzített méretű és a korlátlan táblákat is támogatja. Részletekért lásd a [particionálást az Azure Cosmos DB-ben](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Fontos különbség van a táblák létrehozási módjai között. Az Azure Cosmos DB az Azure Storage fogyasztásalapú modelljeitől eltérően lefoglalja az átviteli sebességet a tranzakciókhoz. Az átviteli sebesség dedikált/foglalt, így soha nem kerül sor szabályozásra, ha a kérések sebessége a kiosztott átviteli sebességnél nem magasabb.

A CreateIfNotExists paramétereként konfigurálhatja az alapértelmezett átviteli sebességet.

Egy 1 KB-os entitás olvasása 1 RU-ként van normalizálva, és a többi művelet is rögzített RU-hoz van normalizálva a processzor-, a memória- és az IOPS-fogyasztásuk alapján. További információ az [Azure Cosmos DB-kérésegységeiről](request-units.md) és különösen a [kulcsérték-tárolókról](key-value-store-cost.md).

Ezután átvesszük az Azure Table Storage SDK használatával végzett egyszerű olvasási és írási (CRUD-) műveleteket. Ez az oktatóanyag bemutatja az Azure Cosmos DB által nyújtott előre jelezhető alacsony egyszámjegyű ezredmásodperces késéseket és gyors lekérdezéseket.

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Az Azure Table Storage entitásai a `TableEntity` osztályból származnak, és `PartitionKey` és `RowKey` tulajdonsággal kell rendelkezniük. Itt láthatja egy ügyfélentitás mintadefinícióját.

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

Az alábbi kódrészlet bemutatja, hogyan szúrható be entitás az Azure Storage SDK-val. Az Azure Cosmos DB garantáltan kis késleltetést biztosít, mérettől függetlenül és az egész világon.

Az írások az esetek 99%-ában 15 ezredmásodperc alatt, és az esetek 50%-ában körülbelül 6 ezredmásodperc alatt készülnek el az Azure Cosmos DB-fiók régiójában futó alkalmazások esetén. És ez az időtartam figyelembe veszi, hogy az írásokat az ügyfél csak akkor nyugtázza, miután szinkronban replikálva lettek, tartósan véglegesítve lettek, és minden tartalom indexelve van.


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
Az Azure Table Storage támogatja a kötegelt műveleti API-t, amellyel frissítéseket, törléseket és beszúrásokat kombinálhat ugyanabban a kötegelt műveletben.

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
A lekérdezések (GET-ek) az Azure Cosmos DB-ben az esetek 99%-ában 10 ezredmásodperc alatt, és az esetek 50%-ában körülbelül 1 ezredmásodperc alatt teljesülnek ugyanabban az Azure-régióban. Bármennyi régiót adhat a fiókhoz a kis késleltetésű olvasásokhoz, és a `TablePreferredLocations` beállításával úgy helyezheti üzembe az alkalmazásokat, hogy a helyi régiójukból olvassanak („többkiszolgálós” környezet). 

Az alábbi kódrészlettel kérdezhet le egyetlen entitást:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> A többkiszolgálós API-król további információért lásd a [több régióval végzett fejlesztés](tutorial-global-distribution-table.md) témakörét.
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Entitások lekérdezése automatikus másodlagos indexekkel
A táblák a `TableQuery` osztállyal kérdezhetők le. Az Azure Cosmos DB írásra optimalizált adatbázismotorral rendelkezik, amely automatikusan indexeli a táblában lévő összes oszlopot. Az Azure Cosmos DB-ben végzett indexelés sémafüggetlen. Ezért akkor is automatikusan van indexelve, ha különböző sémák vannak a különböző sorokban, vagy ha a séma idővel fejlődik. Mivel az Azure Cosmos DB támogatja az automatikus másodlagos indexeket, bármely tulajdonság lekérdezése használhatja az indexet és hatékonyan kiszolgálható.

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

Az Azure Cosmos DB ugyanazokat a lekérdezési funkciókat támogatja a Table API esetében, mint az Azure Table Storage. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. A további funkciók egy jövőbeli szolgáltatásfrissítésben kerülnek a Table API-ba. Az [Azure Cosmos DB lekérdezéssel](sql-api-sql-query.md) kapcsolatos szakaszában megtalálja a képességek áttekintését. 

## <a name="replace-an-entity"></a>Entitás cseréje
Ha frissíteni kíván egy entitást, kérje le a Table szolgáltatásból, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table szolgáltatásba. A következő kód egy meglévő ügyfél telefonszámát módosítja. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Ugyanígy elvégezhet `InsertOrMerge`- vagy `Merge`-műveleteket.  

## <a name="delete-an-entity"></a>Entitás törlése
A lekérdezés után egyszerűen törölheti az entitásokat az entitások frissítésénél bemutatott minta alapján. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Tábla törlése
Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. Törölhet, és azonnal újból létrehozhat egy táblát az Azure Cosmos DB használatával.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan kezdheti el az Azure Cosmos DB használatát a Table API-val, és a következőket végezte el: 

> [!div class="checklist"] 
> * Létrehozott egy Azure Cosmos DB-fiókot 
> * Funkciókat engedélyezett az app.config fájlban 
> * Létrehozott egy táblát 
> * Entitást adott a táblához 
> * Egy entitásköteget szúrt be 
> * Lekérdezett egyetlen entitást 
> * Automatikus másodlagos indexekkel kérdezett le entitásokat 
> * Lecserélt egy entitást 
> * Törölt egy entitást 
> * Törölt egy táblát  

Továbbléphet a következő oktatóanyagra, amelyben megismerheti a táblaadatok lekérdezésének módját. 

> [!div class="nextstepaction"]
> [Lekérdezés a Table API-val](tutorial-query-table.md)
