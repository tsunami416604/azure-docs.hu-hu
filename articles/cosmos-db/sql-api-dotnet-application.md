---
title: 'ASP.NET Core MVC-oktatóanyag a Azure Cosmos DBhoz: Webalkalmazás-fejlesztés'
description: ASP.NET Core MVC-oktatóanyag az MVC-alapú webalkalmazások Azure Cosmos DB használatával történő létrehozásához. A JSON-t és az adatok elérését egy Azure App Service-ASP NET Core MVC oktatóanyag lépésről lépésre futtatott Todo-alkalmazásból fogja tárolni.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 9824e1468604763834e37abe94290d68d81077ab
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020123"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Oktatóanyag: ASP.NET Core MVC-webalkalmazás fejlesztése a Azure Cosmos DB a .NET SDK használatával 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Ez az oktatóanyag bemutatja, hogyan Azure Cosmos DB használatával tárolhatja és érheti el adatait üzemeltetett ASP.NET MVC alkalmazásnak az Azure-ban. Ebben az oktatóanyagban a .NET SDK v3-t használja. Az alábbi képen látható a weblap, amelyen ez a cikk a minta használatával fog létrehozni:
 
![Képernyőkép az oktatóanyag által létrehozott teendők listája MVC-webalkalmazásról – ASP NET Core MVC oktatóanyag lépésről lépésre](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ha nincs ideje az oktatóanyag elvégzésére, letöltheti a teljes minta projektet a Githubról [][GitHub].

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Egy Azure Cosmos-fiók létrehozása
> * ASP.NET Core MVC-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB 
> * Az adatok a CRUD-műveletek végrehajtása

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy már rendelkezik korábbi tapasztalattal a ASP.NET Core MVC és a Azure App Service használatával. Ha új ASP.NET Core vagy az előfeltételként [](#prerequisites)szükséges eszközökhöz, javasoljuk, hogy töltse le a teljes minta projektet [][GitHub]a githubról, adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. Miután a projekt buildjének elkészítéséhez, ez a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek 

A jelen cikkben lévő utasítások követése előtt győződjön meg arról, hogy rendelkezik az alábbi erőforrásokkal:

* **Aktív Azure-fiók:** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

A cikkben szereplő összes képernyőkép a Microsoft Visual Studio Community 2019 használatával készült. Ha a rendszer egy másik verziója van konfigurálva, lehetséges, hogy a képernyők és beállítások előfordulhat, hogy nem egyeznek tökéletesen, de ha megfelel a fenti előfeltételeknek ebben a megoldásban használható.

## <a name="create-an-azure-cosmos-account"></a>1. lépés: Azure Cosmos-fiók létrehozása

Először hozzon létre egy Azure Cosmos-fiók. Ha már rendelkezik egy Azure Cosmos DB SQL API-fiókot, vagy ha az Azure Cosmos DB emulatort használja ehhez az oktatóanyaghoz, továbbléphet a [hozzon létre egy új ASP.NET MVC alkalmazás](#create-a-new-mvc-application) szakaszban.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban új ASP.NET Core MVC-alkalmazást hoz létre. 

## <a name="create-a-new-mvc-application"></a>2. lépés: Új ASP.NET Core MVC-alkalmazás létrehozása

1. A Visual Studióban az a **fájl** menüben válassza a **új**, majd válassza ki **projekt**. Megjelenik a **New project** (Új projekt) párbeszédpanel.

2. Az **új projekt** ablakban a **sablonok keresése** beviteli mezővel keresheti meg a "web" kifejezést, majd válassza ki **ASP.net Core**webalkalmazást. 

   ![Új ASP.NET Core webalkalmazás-projekt létrehozása](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. A **Name** (Név) szövegmezőbe írja be a projekt nevét. Ez az oktatóprogram a „todo” (teendők) nevet használja. Ha úgy dönt, hogy nem ezt a nevet használja, akkor bárhol, amíg az oktatóanyag a ToDo-névtérre mutat, állítsa be a megadott kód mintákat úgy, hogy az alkalmazás nevét is használja. 

4. Kattintson a **Tallózás** gombra, és keresse meg azt a mappát, ahol létre szeretné hozni a projektet. Kattintson a **Létrehozás** gombra. 

5. Megjelenik az **új ASP.net Core Webalkalmazás létrehozása** párbeszédpanel. A sablonok listában válassza a **webalkalmazás (Model-View-Controller)** elemet.

6. Válassza a **Létrehozás** lehetőséget, és hagyja, hogy a Visual Studio az üres ASP.net Core MVC-sablon körüli állványzatot hajtsa végre. 

7. Miután a Visual Studio a bolierplate MVC alkalmazás létrehozása befejeződött, van egy üres ASP.NET alkalmazást, amelyet helyileg futtathat.

## <a name="add-nuget-packages"></a>3. lépés: Azure Cosmos DB NuGet-csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET Core MVC Framework-kóddal, adjuk hozzá a Azure Cosmos DBhoz való kapcsolódáshoz szükséges NuGet-csomagokat.

1. Az Azure Cosmos DB .NET SDK NuGet-csomagként van csomagolva és elosztva. A NuGet-csomagot a Visual Studióban, amelyet a NuGet-Csomagkezelőt a Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben** majd **NuGet-csomagok kezelése**.
   
   ![Képernyőkép a Megoldáskezelő webalkalmazás-projektre vonatkozó, a jobb gombbal kattintva elérhető beállításokról, a NuGet-csomagok kezelése kiemelve.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel. A nuget **Tallózás** mezőbe írja be **Microsoft.Azure.Cosmos**. Az eredmények közül telepítse a **Microsoft. Azure. Cosmos** csomagot. Letölti és telepíti a Azure Cosmos DB csomagot és annak függőségeit. A telepítés befejezéséhez válassza az Elfogadom lehetőséget a **licenc** elfogadását kérő ablakban.
   
   A Package Manager konzol segítségével azt is megteheti, telepítse a NuGet-csomagot. Az ehhez a **eszközök** menüjében válassza **NuGet-Csomagkezelő**, majd válassza ki **Package Manager Console**. A parancssorba írja be a következő parancsot:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. A csomag telepítése után a Visual Studio-projektnek tartalmaznia kell a Microsoft. Azure. Cosmos függvénytár-referenciáját.
  
## <a name="set-up-the-mvc-application"></a>4. lépés: Az ASP.NET Core MVC-alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézetek és a tartományvezérlők ehhez az MVC alkalmazáshoz:

* [Modell hozzáadása](#add-a-model).
* [Vezérlő hozzáadása](#add-a-controller).
* [Nézetek hozzáadása](#add-views).

### <a name="add-a-model"></a> Modell hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **modellek** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel.

1. Nevezze el az új osztály **Item.cs** , és válassza a **Hozzáadás**lehetőséget. 

1. Ezután cserélje le a "Item.cs" osztályban található kódot a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Az Azure Cosmos DB-ben tárolt adatokat a hálózaton keresztül továbbított és JSON-fájlként tárolja. Az objektumok JSON.NET általi szerializált/deszerializált módjának szabályozásához használhatja a **JsonProperty** attribútumot, ahogyan azt a létrehozott **Item** osztályban is mutatja. Nem csak a JSON-ba kerülő tulajdonságnév formátumát szabályozhatja, a .NET-tulajdonságokat ugyanúgy is átnevezheti, mint a **befejezett** tulajdonsággal. 

### <a name="add-views"></a>Nézetek hozzáadása

Következő lépésként hozzon létre az alábbi három nézet: 

* [Lista elemnézet hozzáadása](#AddItemIndexView).
* [Új elemnézet hozzáadása](#AddNewIndexView).
* [Nézet szerkesztése elem hozzáadása](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Lista elemnézet hozzáadása

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal az üres **elem** mappát, a Visual Studio hozzáadásakor az Ön számára létrehozott a  **ItemController** korábban, kattintson a **Hozzáadás**, és kattintson a **nézet**.
   
   ![Képernyőkép a Megoldáskezelő a Visual Studio által a nézet hozzáadása parancsokkal létrehozott mappa mappájának megjelenítéséhez](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Az a **nézet hozzáadása** párbeszédpanel mezőben módosítsa a következő értékeket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Index*** nevet.
   * A **Template** (Sablon) mezőben válassza a ***List*** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
     
   ![A nézet hozzáadása párbeszédpanelt megjelenítő képernyőkép](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

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

### <a name="add-a-controller"></a>Vezérlő hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **tartományvezérlők** mappáját, válassza ki **Hozzáadás**, majd válassza ki **vezérlő**. Megjelenik az **Add Scaffold** (Szerkezet hozzáadása) párbeszédpanel.

1. Válassza az **MVC-vezérlő – üres** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

   ![Képernyőfelvétel a állvány hozzáadása párbeszédpanelről az MVC-vezérlő – üres beállítás kiemelve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nevezze el az új vezérlőt, **ItemController**, és cserélje le a fájl kódját a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   A **ValidateAntiForgeryToken** attribútum segítségével itt segít megvédeni az alkalmazást a webhelyközi kérések hamisítása ellen. Csak ez az attribútum hozzáadásánál hozzá további, a nézetek, valamint a hamisításgátló jogkivonat együttműködve. A témával kapcsolatos további Példákért lásd: a [helyek közötti kérelmek hamisításának megakadályozása][Preventing Cross-Site Request Forgery]. A [GitHub][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.

   Is használhatja a **kötési** attribútum a metódus paraméteren túlküldéses támadások elleni védelem érdekében. További részletekért lásd: alapszintű [szifilisz-műveletek a ASP.net MVC-ben][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>5. lépés: Csatlakozás az Azure Cosmos DB-hez 

Most, hogy a standard szintű MVC elvégeztük a, most be hozzá a kódot, amellyel csatlakozhat az Azure Cosmos DB és a CRUD-műveletek végrehajtása. 

### <a name="perform-crud-operations"></a> Az adatok a CRUD-műveletek végrehajtása

Itt először is, adjon hozzá egy osztályt, amely tartalmazza a logika csatlakozhat, és az Azure Cosmos dB-ben. Ebben az oktatóanyagban ezt a logikát egy nevű `CosmosDBService` osztályba és egy nevű `ICosmosDBService`illesztőfelületbe ágyazjuk be. Ez a szolgáltatás végrehajtja a szifilisz és az olvasási hírcsatorna műveleteit, például a hiányos elemek listázását, az elemek létrehozását, szerkesztését és törlését. 

1. A **Megoldáskezelőben**, hozzon létre egy új mappát a projekt neve **szolgáltatások**.

1. Kattintson a jobb gombbal a **szolgáltatások** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Nevezze el az új osztály **CosmosDBService** , és válassza a **Hozzáadás**lehetőséget.

1. Adja hozzá a következő kódot a **CosmosDBService** osztályhoz, és cserélje le a fájlban található kódot a következő kódra:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Ismételje meg a 2-3 lépést, de ezúttal egy **ICosmosDBService**nevű osztályhoz, és adja hozzá a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Az előző kód `CosmosClient` a konstruktor részét képezi. A ASP.NET Core folyamat után a projekt **Startup.cs** kell megadnia, és az ügyfelet a konfiguráció alapján kell inicializálnia egy egyedi példányként a [függőségi befecskendezéssel](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). A **ConfigureServices** -kezelőben a következőket adjuk meg:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. Ugyanebben a fájlban definiáljuk a **InitializeCosmosClientInstanceAsync**segítő módszert, amely beolvassa a konfigurációt és inicializálja az ügyfelet.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. A konfiguráció a projekt **appSettings. JSON** fájljában van meghatározva. Nyissa meg, és adjon hozzá egy **CosmosDb**nevű szakaszt:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Most, ha futtatja az alkalmazást, ASP.NET Core folyamata létrehozza a **CosmosDbService** , és egyetlen példányt tart fenn egyetlen példányként; Ha a **ItemController** az ügyféloldali kérelmek feldolgozására szolgál, a rendszer ezt az egyetlen példányt fogja használni, és a használatával szifilisz-műveleteket hajthat végre.

Ha most létrehozza és futtatja ezt a projektet, most látnia kell valamit, ami így néz ki:

![Az adatbázis-oktatóanyag által létrehozott Todo List webalkalmazás képernyőképe](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>6. lépés: Az alkalmazás helyi futtatása

A helyi gépen lévő alkalmazás teszteléséhez használja a következő lépéseket:

1. Az alkalmazás hibakeresési módban, a Visual studióban nyomja le az F5. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:
   
   ![Az oktatóanyag által létrehozott teendők listázása webalkalmazás képernyőképe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kattintson a **Create New** (Új létrehozása) hivatkozásra, és adjon értékeket a **Name** (Név) és a **Description** (Leírás) mezőkbe. Hagyja a **befejezve** jelölőnégyzet nincs kiválasztva ellenkező esetben az új elem egy befejezett állapotba kerül, és nem jelenik meg a kiindulási lista.
   
3. Kattintson a **létrehozás** és vissza a rendszer átirányítja a **Index** nézet és az elem megjelenik a listában. Néhány további elemek is hozzáadhat a teendőlistához.

    ![Képernyőfelvétel az index nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kattintson az **Edit** (Szerkesztés) gombra a lista egy **eleme** mellett, és az **Edit** (Szerkesztés) nézetbe kerül, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **Completed** (Befejezve) jelzőt. Ha megjelöli a **teljes** jelzőt, és a **Mentés**gombra kattint, az **elem** a listában befejezettként jelenik meg.
   
   ![Képernyőfelvétel az index nézetről a befejezett négyzet bejelölve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. A [Cosmos Explorer](https://cosmos.azure.com) vagy a Azure Cosmos DB Emulator adatkezelő használatával bármikor ellenőrizheti, hogy a Azure Cosmos db szolgáltatásban lévő adatai állapota megtörténjen-e.

6. Ha befejezte az alkalmazás tesztelését, nyomja meg a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének befejezéséhez. Készen áll a telepítésre!

## <a name="deploy-the-application-to-azure"></a>7. lépés: Az alkalmazás központi telepítése 
Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **közzététel**.
   
2. A **Közzététel** párbeszédpanelen válassza a **app Service**lehetőséget, majd válassza az **új létrehozása** lehetőséget egy app Service-profil létrehozásához, vagy a meglévő profil használatához válassza a **létező lehetőséget** .

3. Ha rendelkezik egy meglévő Azure App Service-profilt, válassza ki a **előfizetés** a legördülő listából. Használja a **nézet** szűrő erőforráscsoport vagy erőforrás típusa szerinti rendezéshez. Ezután keresse meg a szükséges Azure App Service, és válassza ki **OK**.
   
   ![A Visual Studio App Service párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Új Azure App Service-profil létrehozásához kattintson az **Új létrehozása** lehetőségre a **Közzététel** párbeszédpanelen. Az a **létrehozása App Service** párbeszédpanelen adja meg a webalkalmazás neve és a megfelelő előfizetést, erőforráscsoportot és App Service-csomagot, majd válassza ki **létrehozás**.

   ![A Visual Studio App Service létrehozása párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Néhány másodpercen belül a Visual Studio közzéteszi a webalkalmazást, és elindít egy böngészőt, amelyen megtekintheti az Azure-ban futó projektet.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre olyan ASP.NET Core MVC webalkalmazást, amely hozzáférhet a Azure Cosmos DBban tárolt adatszolgáltatásokhoz. Továbbléphet a következő cikkre:

* [Tudnivalók az adatparticionálásról Azure Cosmos DB](./partitioning-overview.md)
* [További információ a Azure Cosmos DB haladó lekérdezésekről](./how-to-sql-query.md)
* [Ismerje meg, hogyan modellezheti az adatait egy fejlettebb forgatókönyvben](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
