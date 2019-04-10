---
title: .NET-webalkalmazás összeállítása az Azure Cosmos DB SQL API használatával
description: Ebben a rövid útmutatóban használhatja az Azure portal és a .NET-webalkalmazás létrehozása és kezelése az Azure Cosmos DB SQL API-fiók erőforrásokhoz.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 7ecb2269243ae96b629a20a26956e6220a2e616c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280842"
---
# <a name="quickstart-build-a-net-web-app-using-sql-api-account-in-azure-cosmos-db"></a>Gyors útmutató: Egy .NET-webalkalmazás létrehozása az Azure Cosmos DB SQL API-fiók használatával

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (előzetes verzió)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Azure Cosmos DB segítségével gyors létrehozása és lekérdezése, kulcs/érték adatbázis, dokumentum-adatbázis és gráf típusú adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket, az Azure Cosmos DB középpontjában. 

Ez a rövid útmutató azt ismerteti, hogyan hozzon létre egy Azure Cosmos DB az Azure portal használatával [SQL API](sql-api-introduction.md) fiókot, dokumentum-adatbázis és gyűjtemény létrehozása és adatok hozzáadása a gyűjteményhez. Ezután egy [SQL .NET SDK](sql-api-sdk-dotnet.md) webalkalmazást a további adatok hozzáadása a gyűjteményhez. 

Ebben a rövid útmutatóban használhatja az adatkezelőt az Azure Portalon hozhat létre az adatbázist és gyűjteményt. A .NET mintakód segítségével az adatbázist és gyűjteményt is létrehozhat. További tudnivalókért lásd: [tekintse át a .NET-kód](#review-the-net-code). 

## <a name="prerequisites"></a>Előfeltételek

A telepített Azure-fejlesztési munkafolyamat a Visual Studio 2017
- Letöltheti és használhatja a **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során. 

Egy Azure-előfizetés vagy az Azure Cosmos DB ingyenes próbaverziós fiókját
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Egy adatbázis és gyűjtemény hozzáadása 

Az Azure Portalon az adatkezelő segítségével hozzon létre egy adatbázist és gyűjteményt. 

1.  Válassza ki **adatkezelő** a bal oldali navigációs az Azure Cosmos DB-fiók oldalon, és válassza ki **új gyűjtemény**. 
    
    Szükség lehet görgessen jobbra, tekintse meg a **gyűjtemény hozzáadása** területen.
    
    ![Az Azure portal adatkezelő, gyűjtemény hozzáadása panel](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  A **Gyűjtemény hozzáadása** oldalon adja meg az új gyűjtemény beállításait.
    
    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis azonosítója**|Teendők|Adja meg *ToDoList* az új adatbázis számára. Adatbázis neve 1 és 255 karakter között kell tartalmaznia, és nem tartalmazhat `/, \\, #, ?`, vagy záró szóközt.|
    |**Katalógus azonosítója**|Elemek|Az új gyűjteménynek adja az *Elemek* nevet. A gyűjteményazonosítók ugyanazok a Karakterkorlátozások vonatkoznak, mint az adatbázis neve.|
    |**Partíciókulcs**| /kategória| A cikkben bemutatott példa */category* partíciókulcsként.|
    |**Teljesítmény**|400|Hagyja meg az átviteli sebesség 400 kérelemegység / másodperc (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    
    Ne adjon **egyedi kulcsok** ebben a példában. Egyedi kulcsok segítségével adhat hozzá egy adatintegritási réteget az adatbázis úgy, hogy egy vagy több értéket partíció kulcsonként egyediségét. További információkért lásd: [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md).
    
1.  Kattintson az **OK** gombra. 
    Az Adatkezelő megjeleníti az új adatbázist és gyűjteményt.
    
    ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

## <a name="add-data-to-your-database"></a>Adatok hozzáadása az adatbázishoz

Adja hozzá az új adatbázis az adatkezelő segítségével adatokat.

1. A **adatkezelő**, megjelenik az új adatbázis a **gyűjtemények** ablaktáblán. Bontsa ki a **ToDoList** adatbázist, majd a **elemek** gyűjtemény, jelölje be **dokumentumok**, majd válassza ki **új dokumentum**. 
   
   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adja hozzá az alábbi struktúrával jobb oldalán található a dokumentum a **dokumentumok** panelen:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Kattintson a **Mentés** gombra.
   
   ![Másolja a json-adatokat, és válassza a Mentés az Adatkezelőben az Azure Portalon](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Válassza ki **új dokumentum** újra, és hozzon létre, és mentse a dokumentumot egy másik egyedi `id`, és egyéb tulajdonságok és kívánt értékeket. A dokumentumaihoz bármilyen struktúrát, mert az Azure Cosmos DB nem ír elő semmilyen sémát, az adatokon.

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="use-the-net-web-app-to-manage-data"></a>Adatok kezelése a .NET web app használatával

Nézze meg, milyen egyszerű az Azure Cosmos DB adatokkal programozott módon dolgozni, klónozza a minta SQL API .NET webes alkalmazást a Githubról, frissítse a kapcsolati karakterláncot, és futtassa az alkalmazást, az adatok frissítéséhez. 

Is létrehozhat az adatbázist és gyűjteményt a .NET mintakód segítségével. További tudnivalókért lásd: [tekintse át a .NET-kód](#review-the-net-code).

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Első lépésben klónozza a C# [SQL API-alkalmazás](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) a Githubról. 

1. Nyissa meg egy git terminálablakot, például a Git bash eszközt, hozzon létre egy új könyvtárat nevű *git-samples*, és módosítsa azt: 
   
   ```bash
   mkdir /c/git-samples/
   cd /c/git-samples/
   ```
   
1. Futtassa a következő parancsot a minta tárház klónozásához, és hozzon létre egy példányát a mintaalkalmazást a számítógépen:
   
   ```bash
   git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
   ```

### <a name="update-the-connection-string"></a>A kapcsolati karakterlánc frissítése 

1. Keresse meg és nyissa meg a *todo.sln* fájlt a klónozott alkalmazás a Visual Studióban. 

1. A Visual Studióban **Megoldáskezelőben**, nyissa meg a *web.config* fájlt. 

1. Lépjen vissza az Azure Portalon, illessze be a kapcsolati karakterlánc adatainak másolása a *web.config*.
   
   1. Az Azure Cosmos DB-fiók bal oldali navigációs sávján válassza **kulcsok**.
      
      ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)
      
   1. A **írható és olvasható kulcsok**, másolása a **URI** értéket, a másolási gomb használatával jobb oldalán, és illessze be azt a `endpoint` kulcsban, a *web.config*. Példa: 
      
      `<add key="endpoint" value="https://mysqlapicosmosdb.documents.azure.com:443/" />`
      
   1. Másolás a **elsődleges kulcs** értékét, és illessze be azt a `authKey` kulcsban, a *web.config*. Példa:
      
      `<add key="authKey" value="19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==" />`

       
1. Ellenőrizze, hogy az adatbázis és gyűjtemény értékeit a *web.config* felel meg a korábban létrehozott nevét. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
1. Mentse a *web.config.* Az alkalmazás frissítése megtörtént, az Azure Cosmos DB közötti kommunikációhoz szükséges összes információt.

### <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a **todo** projektre a **Megoldáskezelőben**, majd válassza ki **NuGet-csomagok kezelése**. 

1. A NuGet **Tallózás** mezőjébe írja be a *DocumentDB* szöveget.

1. Az eredmények közül telepítse a **Microsoft.Azure.DocumentDB** könyvtár, ha még nem telepítette. Ez telepíti a [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) csomagot és annak összes függőségét.
   
   Ha a NuGet-Csomagkezelőt, hogy egyes csomagok hiányoznak a megoldásból üzenetet jelenít meg, válassza ki a **visszaállítása** hogy telepítse őket a belső forrásból. 

1. Válassza ki **Ctrl**+**F5** az alkalmazás futtatását a böngészőben. 

1. Válassza ki **hozzon létre új** a teendőlista alkalmazás, és hozzon létre néhány új feladatot.

   ![Teendőkezelő alkalmazás mintaadatokkal](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Léphet vissza az Adatkezelőbe az Azure Portalon megtekintheti, lekérdezheti, módosítása és az új adatok. 

## <a name="review-the-net-code"></a>Tekintse át a .NET-kód

Ez a lépés nem kötelező. Ebben a rövid útmutatóban egy adatbázis és gyűjtemény létrehozása az Azure Portalon, és mintaadatok hozzáadása a .NET-minta használatával. Azonban Ön is létrehozhat az adatbázis és a gyűjtemény a .NET-minta használatával. Tekintse át az alábbi kódrészletek, ha érdeklődne az adatbázis-erőforrások létrehozását a kódban. A kódrészletek a rendszer az összes átveszi a *DocumentDBRepository.cs* fájlt a **todo** projekt.

* Ez a kód inicializálja a `DocumentClient`: 

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Ez a kód létrehozza az új adatbázis használatával a `CreateDatabaseAsync` módszer:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Az alábbi kód használatával hozza létre az új gyűjtemény az `CreateDocumentCollectionAsync` módszer:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan hozzon létre egy Azure Cosmos DB-fiókot, hozzon létre egy adatbázist és gyűjteményt az adatkezelő segítségével és az adatok frissítése a .NET-webalkalmazás futtatása. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)

