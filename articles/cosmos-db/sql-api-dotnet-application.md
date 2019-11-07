---
title: 'ASP.NET Core MVC-oktatóanyag a Azure Cosmos DBhoz: webalkalmazások fejlesztése'
description: ASP.NET Core MVC-oktatóanyag az MVC-alapú webalkalmazások Azure Cosmos DB használatával történő létrehozásához. A JSON-t és az adatok elérését egy Azure App Service-ASP NET Core MVC oktatóanyag lépésről lépésre futtatott Todo-alkalmazásból fogja tárolni.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 1cfb7718c55920d817e1f87407fa1af590e9f006
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720905"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Oktatóanyag: ASP.NET Core MVC-webalkalmazás fejlesztése a Azure Cosmos DB a .NET SDK használatával

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Ez az oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure-ban üzemeltetett ASP.NET MVC-alkalmazás adatait a Azure Cosmos DB használatával. Ebben az oktatóanyagban a .NET SDK v3-t használja. Az alábbi képen látható, hogy milyen weboldalt fog létrehozni a cikkben szereplő minta használatával:

![Képernyőkép az oktatóanyag által létrehozott teendők listája MVC-webalkalmazásról – ASP NET Core MVC oktatóanyag lépésről lépésre](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ha nincs ideje az oktatóanyag elvégzésére, letöltheti a teljes minta projektet a [githubról][GitHub].

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
>
> * Azure Cosmos-fiók létrehozása
> * ASP.NET Core MVC-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása a Azure Cosmos DBhoz
> * Létrehozási, olvasási, frissítési és törlési (szifilisz) műveletek végrehajtása az adatokon

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy már rendelkezik korábbi tapasztalattal a ASP.NET Core MVC és a Azure App Service használatával. Ha új ASP.NET Core vagy az [előfeltételként szükséges eszközök](#prerequisites), javasoljuk, hogy töltse le a teljes minta projektet a [githubról][GitHub], adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. A projekt létrehozása után áttekintheti ezt a cikket, hogy betekintést kapjon a programkódba a projekt kontextusában.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások követése előtt győződjön meg arról, hogy rendelkezik a következő erőforrásokkal:

* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

A cikkben szereplő összes képernyőkép a Microsoft Visual Studio Community 2019. Ha más verziót használ, előfordulhat, hogy a képernyők és a beállítások nem egyeznek teljesen. A megoldásnak működnie kell, ha megfelel az előfeltételeknek.

## <a name="create-an-azure-cosmos-account"></a>1. lépés: Azure Cosmos-fiók létrehozása

Kezdjük egy Azure Cosmos-fiók létrehozásával. Ha már rendelkezik Azure Cosmos DB SQL API-fiókkal, vagy ha a Azure Cosmos DB emulátort használja, ugorjon a [2. lépés: új ASP.net MVC-alkalmazás létrehozása](#create-a-new-mvc-application)elemre.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban új ASP.NET Core MVC-alkalmazást hoz létre.

## <a name="create-a-new-mvc-application"></a>2. lépés: új ASP.NET Core MVC-alkalmazás létrehozása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**lapon keresse meg és válassza ki **ASP.net Core webalkalmazást** C#. A folytatáshoz kattintson a **Tovább** gombra.

   ![Új ASP.NET Core webalkalmazás-projekt létrehozása](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Az **új projekt konfigurálása**lapon nevezze el a Project *Todo* nevet, és válassza a **Létrehozás**lehetőséget.

1. Az **új ASP.net Core Webalkalmazás létrehozása lapon**válassza a **webalkalmazás (Model-View-Controller)** lehetőséget. A folytatáshoz válassza a **Létrehozás** lehetőséget.

   A Visual Studio egy üres MVC-alkalmazást hoz létre.

1. Válassza **a hibakeresés > ** a **hibakeresés elindítása** vagy az F5 billentyűt a ASP.NET-alkalmazás helyi futtatásához.

## <a name="add-nuget-packages"></a>3. lépés: Azure Cosmos DB NuGet-csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET Core MVC Framework-kóddal, adjuk hozzá a Azure Cosmos DBhoz való kapcsolódáshoz szükséges NuGet-csomagokat.

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **NuGet csomagkezelő eszközben**keresse meg és válassza ki a **Microsoft. Azure. Cosmos**elemet. Válassza az **Install** (Telepítés) lehetőséget.

   ![NuGet-csomag telepítése](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   A Visual Studio letölti és telepíti a Azure Cosmos DB csomagot és annak függőségeit.

   A NuGet-csomag telepítéséhez a **Package Manager-konzolt** is használhatja. Ehhez válassza az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzolt**. A parancssorba írja be a következő parancsot:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>4. lépés: az ASP.NET Core MVC-alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézeteket és a vezérlőket ehhez az MVC-alkalmazáshoz.

### <a name="add-a-model"></a>Modell hozzáadása

1. **Megoldáskezelő**kattintson a jobb gombbal a **modellek** mappára, válassza a > **osztály** **hozzáadása** elemet.

1. Az **új elem hozzáadása**lapon nevezze el az új osztály *Item.cs* , és válassza a **Hozzáadás**lehetőséget.

1. Cserélje le a *Item.cs* osztály tartalmát a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

A Azure Cosmos DB JSON használatával helyezi át és tárolja az adattárolást. A `JsonProperty` attribútummal szabályozhatja, hogy a JSON hogyan szerializálja és deszerializálja az objektumokat. A `Item` osztály a `JsonProperty` attribútumot mutatja be. Ez a kód vezérli a JSON-ba kerülő tulajdonságnév formátumát. Emellett átnevezi a .NET-tulajdonságot `Completed`.

### <a name="add-views"></a>Nézetek hozzáadása

Ezután hozza létre a következő három nézetet.

* Listaelem nézetének hozzáadása
* Új elem nézet hozzáadása
* Elem szerkesztési nézetének hozzáadása

#### <a name="AddItemIndexView"></a>Listaelem nézetének hozzáadása

1. **Megoldáskezelő**kattintson a jobb gombbal a **nézetek** mappára, és válassza a > **új mappa** **hozzáadása** lehetőséget. Adja meg a mappa *tétel*nevét.

1. Kattintson a jobb gombbal az üres **elem** mappára, majd válassza a **Hozzáadás** > **nézet**lehetőséget.

1. Az **MVC-nézet hozzáadása**párbeszédpanelen adja meg a következő értékeket:

   * A **nézet neve**mezőbe írja be az *index*értéket.
   * A **sablon**területen válassza a **lista**lehetőséget.
   * A **modell osztályban**válassza az **elem (teendők) elemet. Modellek)** .
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout.cshtml*értéket.

   ![Az MVC nézet hozzáadása párbeszédpanelt ábrázoló képernyőkép](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Miután hozzáadta ezeket az értékeket, válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre.

Ha elkészült, a Visual Studio megnyitja az általa létrehozott *cshtml* -fájlt. Ezt a fájlt a Visual Studióban is lezárhatja. Később ismét vissza fogunk térni.

#### <a name="AddNewIndexView"></a>Új elem nézet hozzáadása

Hasonlóan ahhoz, ahogy létrehozott egy nézetet a listaelemek megjelenítéséhez, hozzon létre egy új nézetet az elemek létrehozásához a következő lépések segítségével:

1. **Megoldáskezelő**kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a > **nézet** **hozzáadása** lehetőséget.

1. Az **MVC-nézet hozzáadása**párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **nézet neve**mezőbe írja be a *create (létrehozás*) nevet.
   * A **sablon**lapon válassza a **Létrehozás**lehetőséget.
   * A **modell osztályban**válassza az **elem (teendők) elemet. Modellek)** .
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout.cshtml*értéket.
   * Válassza a **Hozzáadás** lehetőséget.

#### <a name="AddEditIndexView"></a>Elem szerkesztési nézetének hozzáadása

Végül pedig vegyen fel egy nézetet egy elem szerkesztéséhez a következő lépésekkel:

1. A **megoldáskezelő**kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a > **nézet** **hozzáadása** lehetőséget.

1. Az **MVC-nézet hozzáadása**párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **View name** (Nézet neve) mezőbe írja be az *Edit* (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az **Edit** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az **Item (todo.Models)** elemet.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout.cshtml*értéket.
   * Válassza a **Hozzáadás** lehetőséget.

Miután elvégezte ezeket a lépéseket, a Visual Studióban zárjunk be minden *cshtml* -dokumentumot, ahogy később visszatér a nézetekhez.

### <a name="add-a-controller"></a>Vezérlő hozzáadása

1. **Megoldáskezelő**kattintson a jobb gombbal a **vezérlők** mappára, majd válassza a > **vezérlő** **hozzáadása** elemet.

1. Az **állvány hozzáadása**területen válassza az **MVC vezérlő – üres** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

   ![MVC-vezérlő kiválasztása – üres a hozzáadási állványban](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nevezze el az új vezérlő *ItemController*.

1. Cserélje le a *ItemController.cs* tartalmát a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

A **ValidateAntiForgeryToken** attribútum itt található, amely segít megvédeni ezt az alkalmazást a helyek közötti kérelmek hamisításának elleni támadásokkal szemben. A nézeteknek ezzel a hamisítási jogkivonattal is működniük kell. További információkért és Példákért lásd: [a helyek közötti kérelmek hamisításának (CSRF) megelőzése a ASP.net MVC alkalmazásban][Preventing Cross-Site Request Forgery]. A [GitHub][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.

A metódus paraméterében a **kötési** attribútumot is használjuk a túlzott közzétételi támadásokkal szembeni védelem érdekében. További információ: [oktatóanyag: a szifilisz funkcióinak megvalósítása a Entity Framework ASP.net MVC-ben][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>5. lépés: Kapcsolódás Azure Cosmos DBhoz

Most, hogy a standard MVC-dolgok gondoskodnak a működéséről, tegyük fel, hogy hozzáadja a kódot a Azure Cosmos DBhoz való kapcsolódáshoz és a szifilisz-műveletek végrehajtásához.

### <a name="perform-crud-operations"></a>SZIFILISZ-műveletek végrehajtása az adatokon

Először egy olyan osztályt fogunk felvenni, amely tartalmazza a Azure Cosmos DBhoz való kapcsolódáshoz és használatához szükséges logikát. Ebben az oktatóanyagban beágyazjuk ezt a logikát egy `CosmosDBService` nevű osztályba, és egy `ICosmosDBService`nevű felületet. Ez a szolgáltatás a szifilisz műveleteit végzi. Emellett olyan olvasási hírcsatorna-műveleteket is végez, mint például a hiányos elemek listázása, az elemek létrehozása, szerkesztése és törlése.

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a > **új mappa** **hozzáadása** lehetőséget. Nevezze el a mappa- *szolgáltatásokat*.

1. Kattintson a jobb gombbal a **szolgáltatások** mappára, válassza a > **osztály** **hozzáadása** elemet. Nevezze el az új osztály *CosmosDBService* , és válassza a **Hozzáadás**lehetőséget.

1. Cserélje le a *CosmosDBService.cs* tartalmát a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Ismételje meg az előző két lépést, de ezúttal használja a *ICosmosDBService*nevet, és használja a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. A **ConfigureServices** -kezelőben adja hozzá a következő sort:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    Az előző lépésben szereplő kód egy `CosmosClient` kap a konstruktor részeként. ASP.NET Core folyamat után a projekt *Startup.cs* -fájlját kell megadnia. Az ebben a lépésben szereplő kód a konfiguráció alapján inicializálja az ügyfelet egy egyedi példányként, amelyet a rendszer a [függőségek befecskendezésével ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. Ugyanebben a fájlban adja hozzá a következő **InitializeCosmosClientInstanceAsync**metódust, amely beolvassa a konfigurációt, és inicializálja az ügyfelet.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Adja meg a konfigurációt a projekt *appSettings. JSON* fájljában. Nyissa meg a fájlt, és adjon hozzá egy **CosmosDb**nevű szakaszt:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Ha futtatja az alkalmazást, ASP.NET Core folyamata létrehozza a **CosmosDbService** , és egyetlen példányt tart fenn önállóként. Amikor az **ItemController** feldolgozza az ügyféloldali kérelmeket, ezt az egyetlen példányt kapja, és használhatja a szifiliszi műveletekhez.

Ha most létrehozza és futtatja ezt a projektet, most látnia kell valamit, ami így néz ki:

![Az adatbázis-oktatóanyag által létrehozott Todo List webalkalmazás képernyőképe](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>6. lépés: Az alkalmazás helyileg történő futtatása

Az alkalmazás helyi számítógépen való teszteléséhez kövesse az alábbi lépéseket:

1. Ha hibakeresési módban szeretné felépíteni az alkalmazást, válassza az F5 billentyűt a Visual Studióban. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:

   ![Az oktatóanyag által létrehozott teendők listázása webalkalmazás képernyőképe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Válassza az **új létrehozása** hivatkozást, és adja hozzá az értékeket a **név** és a **Leírás** mezőkhöz. Hagyja üresen a **Befejezve** jelölőnégyzet jelölését. Ha kijelöli, az alkalmazás kiegészített állapotban adja hozzá az új elemet. Az elem már nem jelenik meg a kezdeti listán.

1. Kattintson a **Létrehozás** gombra. Az alkalmazás visszaküldi az **index** nézetet, és az elem megjelenik a listában. Több elemet is hozzáadhat a **Tennivalók** listájához.

    ![Képernyőfelvétel az index nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Válassza a lista **elem** melletti **Szerkesztés** lehetőséget. Az alkalmazás megnyitja a **szerkesztési** nézetet, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **befejezett** jelzőt is. Ha a **kész** lehetőséget választja, és a **Mentés**gombra kattint, az alkalmazás a listában befejezettként jeleníti meg az **elemet** .

   ![Képernyőfelvétel az index nézetről a befejezett négyzet bejelölve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Ellenőrizze a Azure Cosmos DB szolgáltatásban található adatállapotot a [Cosmos Explorer](https://cosmos.azure.com) vagy a Azure Cosmos DB Emulator adatkezelő használatával.

1. Miután tesztelte az alkalmazást, válassza a CTRL + F5 billentyűkombinációt az alkalmazás hibakeresésének leállításához. Készen áll a telepítésre!

## <a name="deploy-the-application-to-azure"></a>7. lépés: az alkalmazás üzembe helyezése

Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre **megoldáskezelő** , majd válassza a **Közzététel**lehetőséget.

1. A **közzétételi cél**kiválasztása lapon válassza a **app Service**lehetőséget.

1. Meglévő App Service-profil használatához válassza a **meglévő kiválasztása**, majd a **Közzététel**lehetőséget.

1. A **app Service**válassza ki az **előfizetést**. A **View** szűrő használatával csoportosíthatja az erőforráscsoport vagy az erőforrástípus alapján.

1. Keresse meg a profilt, majd kattintson **az OK gombra**. Ezután keresse meg a szükséges Azure App Service, és kattintson **az OK gombra**.

   ![A Visual Studio App Service párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Egy másik lehetőség egy új profil létrehozása:

1. Ahogy az előző eljárásban, kattintson a jobb gombbal a projektre **megoldáskezelő** és válassza a **Közzététel**lehetőséget.
  
1. A **közzétételi cél**kiválasztása lapon válassza a **app Service**lehetőséget.

1. A **közzétételi cél**kiválasztása lapon válassza az **új létrehozása** elemet, és válassza a **Közzététel**lehetőséget.

1. A **app Service**mezőben adja meg a webalkalmazás nevét és a megfelelő előfizetést, erőforráscsoportot és üzemeltetési csomagot, majd válassza a **Létrehozás**lehetőséget.

   ![A Visual Studio App Service létrehozása párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Néhány másodpercen belül a Visual Studio közzéteszi a webalkalmazást, és elindít egy böngészőt, amelyen megtekintheti az Azure-ban futó projektet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy ASP.NET Core MVC-webalkalmazást. Az alkalmazás elérheti Azure Cosmos DB tárolt adatait. Most már folytathatja ezeket az erőforrásokat:

* [Particionálás az Azure Cosmos DB-ben](./partitioning-overview.md)
* [SQL-lekérdezések – első lépések](./how-to-sql-query.md)
* [Adatok modellezése és particionálása az Azure Cosmos DB-ben való életből vett példa használatával](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
