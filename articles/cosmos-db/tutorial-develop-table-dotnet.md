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
ms.date: 12/18/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 41d7e42f203170e4fa3b8e3a8c973e23808f941b
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: A tábla API a .NET fejlesztést

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ez az oktatóanyag ismerteti a következő feladatokat: 

> [!div class="checklist"] 
> * Azure Cosmos DB-fiók létrehozása 
> * A funkciónak az app.config fájlban 
> * Hozzon létre egy táblát az a [tábla API](table-introduction.md)
> * Entitás hozzáadása a táblához 
> * Entitásköteg beszúrása 
> * Egyetlen entitás lekérdezése 
> * Lekérdezés entitások automatikus másodlagos indexek használata 
> * Entitás cseréje 
> * Entitás törlése 
> * Tábla törlése
 
## <a name="tables-in-azure-cosmos-db"></a>Az Azure Cosmos DB táblák 

Az Azure Cosmos DB biztosít a [tábla API](table-introduction.md) séma nélküli kialakítás egy kulcs-érték tároló igénylő alkalmazásokhoz. Mindkét Azure Cosmos DB tábla API és [Azure Table storage](../storage/common/storage-introduction.md) mostantól támogatják az SDK-k és a REST API-k. Az Azure Cosmos DB használatával nagy átviteli sebességet megkövetelő táblákat hozhat létre.

Ez az oktatóanyag a fejlesztők számára, aki ismeri az Azure Table storage SDK, és a premium szolgáltatásainak használatához az Azure Cosmos DB van. Alapul [Ismerkedés az Azure Table storage használatának .NET](table-storage-how-to-use-dotnet.md) és bemutatja, hogyan további szolgáltatásokat, például a másodlagos indexek, a létesített átviteli sebesség és a többszörös homing előnyeit. Ez az oktatóanyag leírja, hogyan használható az Azure-portálon hozzon létre egy Azure Cosmos DB fiókot létrehozása és egy tábla API-alkalmazás központi telepítése. A Microsoft .NET példákból létrehozása és egy tábla törlésével és beszúrni, frissítése, törlése és tábla adatok lekérdezése is ismerteti. 

Ha az Azure Table storage jelenleg használ, a következő előnyöket Azure Cosmos DB tábla API-hoz:

- Kulcsrakész [globális terjesztési](distribute-data-globally.md) a többszörös homing és [automatikus és manuális feladatátvétel](regional-failover.md)
- Automatikus séma-független elleni tulajdonságokat ("másodlagos indexek"), és gyors lekérdezéseket indexelő támogatása 
- Támogatja az [független méretezése tárolási és átviteli](partition-data.md), tetszőleges számú régiók között
- Támogatja az [táblánként dedikált átviteli](request-units.md) , amely a kérések száma másodpercenként több millió akár több százszor is méretezhető
- Támogatja az [öt konzisztenciaszinteket](consistency-levels.md) rendelkezésre állás, a késés és az adott alkalmazástól függően konzisztencia kompromisszumot kell
- rendelkezésre állás 99,99 % belül egy régiót, és a magas rendelkezésre állás érdekében további régiókban hozzáadásának lehetősége és [iparágvezető átfogó SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) az általánosan rendelkezésre álló
- A meglévő Azure storage .NET SDK és az alkalmazáshoz nincs kódmódosításokat használata

Ez az oktatóanyag bemutatja az Azure Cosmos DB tábla API a .NET SDK használatával. Letöltheti a [Azure Storage szolgáltatás előzetes SDK](https://aka.ms/tableapinuget) a Nugetből.

Összetett Azure Table storage feladatokkal kapcsolatos további tudnivalókért lásd:

* [Bevezetés az Azure Cosmos DB tábla API](table-introduction.md)
* A tábla szolgáltatás elérhető API-kat vonatkozó referenciadokumentációt [Azure Cosmos DB tábla API .NET SDK-val](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag a fejlesztők számára, aki ismeri az Azure Table storage SDK, és a premium szolgáltatásainak használatához használja Azure Cosmos DB. Alapul [Ismerkedés az Azure Table storage használatának .NET](table-storage-how-to-use-dotnet.md) és bemutatja, hogyan további szolgáltatásokat, például a másodlagos indexek, a létesített átviteli sebesség és a többszörös homing előnyeit. A Microsoft foglalkozik az Azure-portál használatával hozzon létre egy Azure Cosmos DB fiókot és build és egy tábla alkalmazás központi telepítése. A Microsoft .NET példákból létrehozása és egy tábla törlésével és beszúrni, frissítése, törlése és tábla adatok lekérdezése is ismerteti. 

Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB fiókot az Azure portálon.  
 
> [!IMPORTANT]  
> Létre kell hoznia egy új Table API-fiókot, amely használható az általánosan elérhető Table API SDK-kkal. Az általánosan elérhető SDK-k nem támogatják az előzetes verzióban létrehozott Table API-fiókokat. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson a mappára, ahol telepíteni szeretné a mintaalkalmazást. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az [Azure Portalon](http://portal.azure.com/) kattintson a **Kapcsolati karakterlánc** elemre. 

    A képernyő jobb oldalán található másolási gombok használatával másolja a vágólapra a PRIMARY CONNECTION STRING (Elsődleges kapcsolati karakterlánc) értékét.

    ![Tekintse meg és másolja a vágólapra a CONNECTION STRING (Kapcsolati karakterlánc) értékét a Kapcsolati karakterlánc ablaktáblán](./media/create-table-dotnet/connection-string.png)

2. Nyissa meg az app.config fájlt a Visual Studióban. 

3. Állítsa vissza a 8. sorban található StorageConnectionString értéket, és tegye megjegyzésbe a 7. sorban szereplő StorageConnectionString értéket, mert ebben az oktatóanyagban nem fogjuk használni a Storage Emulatort. A 7. és a 8. sornak így kell kinéznie:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Illessze be a PRIMARY CONNECTION STRING (Elsődleges kapcsolati karakterlánc) értékét a portálról a 8. sor StorageConnectionString értékébe. Illessze be a karakterláncot az idézőjelek közé.
   
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
Az Azure Cosmos DB képességeket kínál, amelyek nem érhetők el az Azure Table storage API-ban számos támogat. 

Bizonyos funkciókat, amelyek lehetővé teszik egy szükséges kapcsolat házirend és a konzisztencia szintet adjon meg a CreateCloudTableClient új túlterhelések keresztül érhető el.

| Tábla kapcsolatbeállításai | Leírás |
| --- | --- |
| Csatlakozási mód  | Azure Cosmos-adatbázis két csatlakozási módot támogat. A `Gateway` mód, mindig kérések az Azure Cosmos DB átjárón, amely továbbítja a megfelelő adatok partíciókat. A `Direct` csatlakozási mód, az ügyfél lekéri a táblák leképezése partíciókra, és a kérések közvetlenül az adatok partíciók szemben. Ajánlott `Direct`, az alapértelmezett.  |
| Kapcsolat protokollja | Az Azure Cosmos DB támogatja két kapcsolat protokoll - `Https` és `Tcp`. `Tcp`az alapértelmezett és ajánlott, mivel az több egyszerűsített. |
| Elsődleges helyek | Előnyben részesített (többhelyű) helyek az olvasási műveletek vesszővel elválasztott listája. Minden Azure Cosmos DB fiókhoz társítható 1-30 + régiók. Minden ügyfél példány e régiók részhalmazát megadhat kis késleltetésű olvasása csatlakozási kísérleteinek kívánt sorrendjét. A régiók névvel kell ellátni használatával a [megjelenített neveket](https://msdn.microsoft.com/library/azure/gg441293.aspx), például `West US`. Lásd még: [több homing API-k](tutorial-global-distribution-table.md). |
| Konzisztenciaszint | Akkor is kompromisszumot közötti késleltetés, konzisztencia- és rendelkezésre állás öt jól meghatározott konzisztenciaszintek közötti választással: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix`, és `Eventual`. Alapértelmezett érték a `Session`. A választott konzisztenciaszint lehetővé teszi több területi beállítások jelentős teljesítménybeli különbség. Lásd: [konzisztenciaszintek](consistency-levels.md) részleteiről. |

Egyéb funkciók engedélyezéséhez a következő `appSettings` konfigurációs értékeket.

| Kulcs | Leírás |
| --- | --- |
| TableQueryMaxItemCount | Konfigurálja az egyetlen oda-vissza tábla lekérdezésenként visszaküldött elemek maximális számát. Alapértelmezett érték a `-1`, ami lehetővé teszi, hogy Azure Cosmos DB dinamikusan alapján határozhatja meg a futási időben. |
| TableQueryEnableScan | Ha a lekérdezés nem használja az index bármely szűrőt, majd futtassa ennek ellenére a vizsgálat keresztül. Alapértelmezett érték a `false`.|
| TableQueryMaxDegreeOfParallelism | A kereszt-partíció lekérdezés végrehajtási párhuzamossági fokát. `0`a nem lehívását, soros `1` van előre terjesztésekor, és a magasabb értékkel soros növekedjen párhuzamossági. Alapértelmezett érték a `-1`, ami lehetővé teszi, hogy Azure Cosmos DB dinamikusan alapján határozhatja meg a futási időben. |

Az alapértelmezett érték módosításához nyissa meg a `app.config` fájlt a Visual Studio megoldáskezelőjében. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Cserélje le `account-name` a tárfiók nevével és `account-key` a fiók hozzáférési kulccsal. 

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

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.cs` fájlt, és megkeresi, hogy ezek a sorok, a kód létrehozása a táblázat erőforrások. 

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
400
table.CreateIfNotExists(throughput: 800);
```

Nincs a táblák létrehozását egy fontos különbséggel. Cosmos. Azure-adatbázis fenntartja a teljesítményt, eltérően az Azure storage fogyasztás alapján modell az egyes tranzakciókra vonatkozóan. Az átviteli sebesség dedikált/foglalt, így Ön soha nem get szabályozva a kérelmek aránya vagy az alatt a létesített átviteli sebesség esetén.

Beállíthatja az alapértelmezett átviteli CreateIfNotExists paramétereként ot.

Egy 1 KB-os entitás olvasási van normalizált 1 RU rögzített érték a Processzor, memória és IOPS fogyasztás alapján normalizálják RU és egyéb műveletekhez. További információ [Azure Cosmos DB egység kérelem](request-units.md) és külön [kulcs-érték tárolók](key-value-store-cost.md).

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
Az Azure Table storage támogatja a kötegelt művelet API-t, amely lehetővé teszi, hogy kombinálhatja a frissítéseket, törléseket és beszúrásokat egy kötegelt műveletben.

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

Azure Cosmos-adatbázis Azure Table storage azonos lekérdezés funkciókat támogatja a tábla API-hoz. Azure Cosmos-adatbázis is támogatja a rendezést, összesítések, a földrajzi lekérdezést, a hierarchia és a számos különféle beépített funkciók. A további funkciókat nyújtanak a jövőbeli szolgáltatásfrissítés tábla API. Lásd: [Azure Cosmos adatbázis-lekérdezés](sql-api-sql-query.md) ezeket a képességeket áttekintését. 

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

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

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
