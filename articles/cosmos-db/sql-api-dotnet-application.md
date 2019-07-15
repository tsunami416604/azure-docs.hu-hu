---
title: 'Az ASP.NET Core MVC-oktatóanyag az Azure Cosmos DB: Webalkalmazás-fejlesztés'
description: Az ASP.NET Core MVC oktatóprogram az Azure Cosmos DB MVC webalkalmazás létrehozásához. Fog JSON tárolhatja és érheti el adatait az Azure App Service – ASP NET Core MVC oktatóprogram lépésről lépésre tárolt teendőkezelő alkalmazást.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985871"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Oktatóanyag: Az Azure Cosmos DB az ASP.NET Core MVC webalkalmazás fejlesztése .NET SDK-val 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Ez az oktatóanyag bemutatja, hogyan Azure Cosmos DB használatával tárolhatja és érheti el adatait üzemeltetett ASP.NET MVC alkalmazásnak az Azure-ban. Ebben az oktatóanyagban használja a .NET SDK V3. Az alábbi képen látható a weblap, amelyen ez a cikk a minta használatával fog létrehozni:
 
![Az MVC-webalkalmazás oktatóanyag – ASP NET Core MVC oktatóprogram lépésről lépésre során létrehozott teendőlista képernyőképe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ha nincs ideje az oktatóanyag elvégzéséhez, letöltheti a teljes mintaprojektet a [GitHub][GitHub].

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Egy Azure Cosmos-fiók létrehozása
> * ASP.NET Core MVC-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB 
> * Az adatok a CRUD-műveletek végrehajtása

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy van korábbi tapasztalata az ASP.NET Core MVC és az Azure App Service használatával. Ha még nem ismeri az ASP.NET Core vagy a [előfeltételt jelentő eszközöket](#prerequisites), azt javasoljuk, hogy töltse le a teljes mintaprojektet a [GitHub][GitHub], adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. Miután a projekt buildjének elkészítéséhez, ez a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek 

A jelen cikkben lévő utasítások követése előtt győződjön meg arról, hogy rendelkezik az alábbi erőforrásokkal:

* **Aktív Azure-fiók:** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Ez a cikk a képernyőképek csak a Microsoft Visual Studio Community 2019 használatával került sor. Ha a rendszer egy másik verziója van konfigurálva, lehetséges, hogy a képernyők és beállítások előfordulhat, hogy nem egyeznek tökéletesen, de ha megfelel a fenti előfeltételeknek ebben a megoldásban használható.

## <a name="create-an-azure-cosmos-account"></a>1. lépés: Egy Azure Cosmos-fiók létrehozása

Először hozzon létre egy Azure Cosmos-fiók. Ha már rendelkezik egy Azure Cosmos DB SQL API-fiókot, vagy ha az Azure Cosmos DB emulatort használja ehhez az oktatóanyaghoz, továbbléphet a [hozzon létre egy új ASP.NET MVC alkalmazás](#create-a-new-mvc-application) szakaszban.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban hozzon létre egy új ASP.NET Core MVC-alkalmazás. 

## <a name="create-a-new-mvc-application"></a>2. lépés: Új ASP.NET Core MVC alkalmazás létrehozása

1. A Visual Studióban az a **fájl** menüben válassza a **új**, majd válassza ki **projekt**. Megjelenik a **New project** (Új projekt) párbeszédpanel.

2. Az a **új projekt** ablakban a **sablonok keresése** beviteli mezőben keressen a "Webes", és válassza a **ASP.NET Core-webalkalmazás**. 

   ![Hozzon létre új ASP.NET Core web projektet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. A **Name** (Név) szövegmezőbe írja be a projekt nevét. Ez az oktatóprogram a „todo” (teendők) nevet használja. Ha ezt a nevet más nevet, majd ez az oktatóanyag ismerteti a teendőlista-névteret, ahol szükség esetén módosítsa a megadott kódmintákat úgy, hogy használja az alkalmazást. 

4. Válassza ki **Tallózás** , lépjen abba a mappába, ahol szeretné a projekt létrehozásához. Kattintson a **Létrehozás** gombra. 

5. A **hozzon létre egy új ASP.NET Core-webalkalmazás** párbeszédpanel jelenik meg. A sablonok listájában válassza ki a **webalkalmazás (Model-View-Controller)** .

6. Válassza ki **létrehozás** és hagyhatja, hogy a Visual Studio összecsomagolja az üres ASP.NET Core MVC sablonban körül. 

7. Miután a Visual Studio a bolierplate MVC alkalmazás létrehozása befejeződött, van egy üres ASP.NET alkalmazást, amelyet helyileg futtathat.

## <a name="add-nuget-packages"></a>3. lépés: Az Azure Cosmos DB NuGet-csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET Core MVC framework-kód a legtöbb, adjuk hozzá a NuGet-csomagok Azure Cosmos DB-hez való kapcsolódáshoz szükséges.

1. Az Azure Cosmos DB .NET SDK NuGet-csomagként van csomagolva és elosztva. A NuGet-csomagot a Visual Studióban, amelyet a NuGet-Csomagkezelőt a Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben** majd **NuGet-csomagok kezelése**.
   
   ![Képernyőfelvétel a NuGet-csomagok kezelése kiemelve a Megoldáskezelőben a webalkalmazás projekt helyi közül.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel. A nuget **Tallózás** mezőbe írja be **Microsoft.Azure.Cosmos**. Az eredmények közül telepítse a **Microsoft.Azure.Cosmos** csomagot. Ez letölti és telepíti az Azure Cosmos DB csomagot és annak függőségeit. Válassza ki **elfogadom** a a **licencfeltételek elfogadását** ablakban a telepítés befejezéséhez.
   
   A Package Manager konzol segítségével azt is megteheti, telepítse a NuGet-csomagot. Az ehhez a **eszközök** menüjében válassza **NuGet-Csomagkezelő**, majd válassza ki **Package Manager Console**. A parancssorba írja be a következő parancsot:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. A csomag telepítése után a Visual Studio-projektek Microsoft.Azure.Cosmos, a kódtár – referencia tartalmaznia kell.
  
## <a name="set-up-the-mvc-application"></a>4. lépés: Az ASP.NET Core MVC alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézetek és a tartományvezérlők ehhez az MVC alkalmazáshoz:

* [Modell hozzáadása](#add-a-model).
* [Vezérlő hozzáadása](#add-a-controller).
* [Nézetek hozzáadása](#add-views).

### <a name="add-a-model"></a> Modell hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **modellek** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel.

1. Adja az új osztálynak **Item.cs** válassza **Hozzáadás**. 

1. Ezután cserélje le a kód "Item.cs" osztályban az alábbi kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Az Azure Cosmos DB-ben tárolt adatokat a hálózaton keresztül továbbított és JSON-fájlként tárolja. A módon az objektumok JSON.NET általi szerializálni vagy deszerializálni szabályozásához használhatja a **JsonProperty** attribútumot, ahogyan az a **elem** létrehozott osztályt. Csak nem is szabályozhatja, hogy a goes JSON formátumba, átnevezheti a .NET tulajdonságokat, ahogyan azt tette a tulajdonságnév formátumát a **befejezve** tulajdonság. 

### <a name="add-a-controller"></a>Vezérlő hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **tartományvezérlők** mappáját, válassza ki **Hozzáadás**, majd válassza ki **vezérlő**. Megjelenik az **Add Scaffold** (Szerkezet hozzáadása) párbeszédpanel.

1. Válassza ki **MVC-vezérlő - üres** válassza **Hozzáadás**.

   ![A Scaffold hozzáadása párbeszédpanelen az MVC-vezérlő - üres opció kiemelésével – képernyőfelvétel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nevezze el az új vezérlőnek **ItemController**, és a kódot, az adott fájlban cserélje le a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   A **ValidateAntiForgeryToken** attribútum segítségével itt segít megvédeni az alkalmazást a webhelyközi kérések hamisítása ellen. Csak ez az attribútum hozzáadásánál hozzá további, a nézetek, valamint a hamisításgátló jogkivonat együttműködve. A tulajdonos, és a példák a megvalósításának módjáról további információkért lásd: [megakadályozza, hogy Webhelyközi kérések hamisításának megakadályozása][Preventing Cross-Site Request Forgery] . The source code provided on [GitHub][GitHub] szerepel a teljes megvalósítás.

   Is használhatja a **kötési** attribútum a metódus paraméteren túlküldéses támadások elleni védelem érdekében. További részletekért tekintse meg [alapvető CRUD műveletek az ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Nézetek hozzáadása

Következő lépésként hozzon létre az alábbi három nézet: 

* [Lista elemnézet hozzáadása](#AddItemIndexView).
* [Új elemnézet hozzáadása](#AddNewIndexView).
* [Nézet szerkesztése elem hozzáadása](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Lista elemnézet hozzáadása

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal az üres **elem** mappát, a Visual Studio hozzáadásakor az Ön számára létrehozott a  **ItemController** korábban, kattintson a **Hozzáadás**, és kattintson a **nézet**.
   
   ![Képernyőkép a Megoldáskezelőben, amely a Visual Studio használatával a nézet hozzáadása a parancsok vannak kiemelve létrehozott Item mappa](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Az a **nézet hozzáadása** párbeszédpanel mezőben módosítsa a következő értékeket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Index*** nevet.
   * A **Template** (Sablon) mezőben válassza a ***List*** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
     
   ![Képernyőfelvétel a nézet hozzáadása párbeszédpanel:](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Miután hozzáadta ezeket az értékeket, válassza ki **Hozzáadás** és hagyhatja, hogy a Visual Studióban hozzon létre egy új sablonnézetet. Ha végzett, megnyílik a létrehozott cshtml fájlt. Bezárhatja ezt a fájlt a Visual Studióban, akkor fogja azt később visszatérhet.

#### <a name="AddNewIndexView"></a>Új elemnézet hozzáadása

Hasonló hogyan hozott létre, listaelemek nézetet is konfigurációelemek létrehozása a következő lépések segítségével új nézet létrehozása:

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **elem** mappáját, válassza ki **Hozzáadás**, majd válassza ki **nézet**.

1. Az a **nézet hozzáadása** párbeszédpanel mezőben módosítsa a következő értékeket:
   
   * A **View name** (Nézet neve) mezőbe írja be a ***Create*** (Létrehozás) nevet.
   * A **Template** (Sablon) mezőben válassza a ***Create*** (Létrehozás) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Válassza a **Hozzáadás** lehetőséget.
   
#### <a name="AddEditIndexView"></a>Egy elem szerkesztési nézet hozzáadása

És végül adja hozzá egy nézetet, hogy módosítson egy elemet az alábbi lépéseket követve:

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **elem** mappáját, válassza ki **Hozzáadás**, majd válassza ki **nézet**.

1. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Edit*** (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az ***Edit*** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Válassza a **Hozzáadás** lehetőséget.

Ha ezzel végzett, zárja be az összes cshtml dokumentumot a Visual Studio, ezek a nézetek később vissza.

## <a name="connect-to-cosmosdb"></a>5. lépés: Csatlakozás az Azure Cosmos DB-hez 

Most, hogy a standard szintű MVC elvégeztük a, most be hozzá a kódot, amellyel csatlakozhat az Azure Cosmos DB és a CRUD-műveletek végrehajtása. 

### <a name="perform-crud-operations"></a> Az adatok a CRUD-műveletek végrehajtása

Itt először is, adjon hozzá egy osztályt, amely tartalmazza a logika csatlakozhat, és az Azure Cosmos dB-ben. Ebben az oktatóanyagban azt fogja magába foglalja a vonatkozó logika egy nevű osztályba `CosmosDBService` , és felület nevű `ICosmosDBService`. Ez a szolgáltatás elvégzi a CRUD-MŰVELETEKKEL, és olvassa el a hírcsatorna műveletek, például a hiányos elemek listázása, létrehozására, szerkesztésére és az elemek törlése. 

1. A **Megoldáskezelőben**, hozzon létre egy új mappát a projekt neve **szolgáltatások**.

1. Kattintson a jobb gombbal a **szolgáltatások** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Az új osztály neve **CosmosDBService** válassza **Hozzáadás**.

1. Adja hozzá a következő kódot a **CosmosDBService** osztályt, és a kódot, az adott fájlban cserélje le a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Ismételje meg a 2-3, de ez esetben egy osztály nevű **ICosmosDBService**, és adja hozzá a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Az előző kód kap egy `CosmosClient` konstruktor részeként. A következő ASP.NET Core-folyamat, nyissa meg a projekt kell **Startup.cs** és keresztül beszúrásra példányként konfigurációja alapján az ügyfél inicializálása [függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Az a **ConfigureServices** -kezelő azt határozza meg:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. Ugyanebben a fájlban lévő meghatározzuk a segédmetódus **InitializeCosmosClientInstanceAsync**, amely beolvassa a konfigurációt, és az ügyfél inicializálása.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. A konfigurációs van definiálva a projekt **appsettings.json** fájlt. Nyissa meg és adja hozzá a nevű szakaszt **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Most futtatja az alkalmazást, ha az ASP.NET Core folyamat létrehozza nekünk **CosmosDbService** és kezelése egyetlen példányra egypéldányosnak; Ha **ItemController** ügyféloldali, kérelmek feldolgozásához használt, az Egypéldányos fogadja és CRUD-műveletek végrehajtásához használhatja azt.

Ha hozhat létre, és most futtatni a projekthez, most kell megjelennie valamit a következőhöz hasonló:

![A teendőlista webalkalmazás az adatbázis-oktatóprogram során létrehozott képernyőképe](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>6. lépés: Az alkalmazás helyi futtatása

A helyi gépen lévő alkalmazás teszteléséhez használja a következő lépéseket:

1. Az alkalmazás hibakeresési módban, a Visual studióban nyomja le az F5. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:
   
   ![A teendőlista webalkalmazás oktatóanyag során létrehozott képernyőképe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kattintson a **Create New** (Új létrehozása) hivatkozásra, és adjon értékeket a **Name** (Név) és a **Description** (Leírás) mezőkbe. Hagyja a **befejezve** jelölőnégyzet nincs kiválasztva ellenkező esetben az új elem egy befejezett állapotba kerül, és nem jelenik meg a kiindulási lista.
   
3. Kattintson a **létrehozás** és vissza a rendszer átirányítja a **Index** nézet és az elem megjelenik a listában. Néhány további elemek is hozzáadhat a teendőlistához.

    ![Képernyőfelvétel az Index nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kattintson az **Edit** (Szerkesztés) gombra a lista egy **eleme** mellett, és az **Edit** (Szerkesztés) nézetbe kerül, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **Completed** (Befejezve) jelzőt. Ha Ön a **Complete** jelzőt, és kattintson a **mentése**, a **elem** jelenik meg a listában befejezettként.
   
   ![Képernyőfelvétel az Index nézetről, bejelölt Completed (Befejezve) be van jelölve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Az Azure Cosmos DB szolgáltatás használata az adatok állapotának ellenőrzéséhez bármikor [Cosmos Explorer](https://cosmos.azure.com) vagy az Azure Cosmos DB Emulatort adatkezelő.

6. Ha befejezte az alkalmazás tesztelését, nyomja meg a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének befejezéséhez. Készen áll a telepítésre!

## <a name="deploy-the-application-to-azure"></a>7. lépés: Az alkalmazás központi telepítése 
Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **közzététel**.
   
2. Az a **közzététel** párbeszédpanelen jelölje ki **App Service-ben**, majd válassza **hozzon létre új** hozzon létre egy App Service-profilt, vagy válasszon **meglévő**egy meglévő profil.

3. Ha rendelkezik egy meglévő Azure App Service-profilt, válassza ki a **előfizetés** a legördülő listából. Használja a **nézet** szűrő erőforráscsoport vagy erőforrás típusa szerinti rendezéshez. Ezután keresse meg a szükséges Azure App Service, és válassza ki **OK**.
   
   ![A Visual Studio App Service párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Új Azure App Service-profil létrehozásához kattintson az **Új létrehozása** lehetőségre a **Közzététel** párbeszédpanelen. Az a **létrehozása App Service** párbeszédpanelen adja meg a webalkalmazás neve és a megfelelő előfizetést, erőforráscsoportot és App Service-csomagot, majd válassza ki **létrehozás**.

   ![A Visual Studio App Service létrehozása párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Néhány másodpercen belül a Visual Studio tesz közzé a webalkalmazást, és elindít egy böngészőt, ahol láthatja a projekt az Azure-ban futó!

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban bemutattuk, hogyan hozhat létre egy ASP.NET Core MVC-webalkalmazás Azure Cosmos DB-ben tárolt adatok eléréséhez. Továbbléphet a következő cikkre:

* [További tudnivalók az Azure Cosmos DB az adatok particionálása](./partitioning-overview.md)
* [Ismerje meg, az Azure Cosmos DB bonyolultabb lekérdezéseket kapcsolatban](./how-to-sql-query.md)
* [Hogyan modellezheti az adatokat a speciálisabb forgatókönyvek esetében](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
