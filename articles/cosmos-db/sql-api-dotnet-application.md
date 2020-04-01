---
title: ASP.NET Core MVC webalkalmazás oktatóanyaga az Azure Cosmos DB használatával
description: ASP.NET Core MVC oktatóanyag ot, amely az Azure Cosmos DB használatával hoz létre egy MVC webalkalmazást. A JSON-t tárolja, és lépésről lépésre hozzáférhet az Azure App Service - ASP NET Core MVC oktatóanyag - üzemeltetett todo alkalmazásból származó adatokhoz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274075"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Oktatóanyag: A .NET SDK használatával ASP.NET Core MVC webalkalmazás fejlesztése az Azure Cosmos DB használatával

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Cosmos DB-t az Azure-ban üzemeltetett ASP.NET MVC-alkalmazás adatainak tárolására és elérésére. Ebben az oktatóanyagban a .NET SDK V3 programot használja. Az alábbi képen látható a cikkben szereplő minta segítségével létrehozott weblap:

![Képernyőkép az oktatóanyag által létrehozott MVC webalkalmazás todo listájáról - ASP NET Core MVC oktatóanyag lépésről lépésre](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ha nincs ideje az oktatóanyag befejezésére, letöltheti a teljes mintaprojektet a [GitHubról.][GitHub]

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
>
> * Azure Cosmos-fiók létrehozása
> * ASP.NET Core MVC alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB-hez
> * Létrehozási, olvasási, frissítési és törlési (CRUD) műveletek végrehajtása az adatokon

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy előzetes tapasztalattal rendelkezik ASP.NET Core MVC és az Azure App Service használatával. Ha még nem ASP.NET Core vagy az [előfeltételként szolgáló eszközök,](#prerequisites)javasoljuk, hogy töltse le a teljes mintaprojektet a [GitHubról,][GitHub]adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. Miután megépítette a projektet, áttekintheti ezt a cikket, hogy betekintést nyerjen a kódba a projekt kontextusában.

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek

A cikkben található utasítások bekövetése előtt győződjön meg arról, hogy rendelkezik a következő erőforrásokkal:

* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

A cikkben szereplő összes képernyőkép a Microsoft Visual Studio Community 2019-ből származik. Ha más verziót használ, előfordulhat, hogy a képernyők és a beállítások nem egyeznek meg teljesen. A megoldásnak működnie kell, ha megfelel az előfeltételeknek.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>1. lépés: Azure Cosmos-fiók létrehozása

Kezdjük egy Azure Cosmos-fiók létrehozásával. Ha már rendelkezik egy Azure Cosmos DB SQL API-fiókkal, vagy ha az Azure Cosmos DB-emulátort használja, ugorjon a [ASP.NET 2.](#create-a-new-mvc-application)

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban hozzon létre egy új ASP.NET Core MVC alkalmazás.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>2. lépés: Hozzon létre egy új ASP.NET Core MVC alkalmazás

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban keresse meg és jelölje ASP.NET Core Web Application for C#(C# **)** lehetőséget. A folytatáshoz kattintson a **Tovább** gombra.

   ![Új ASP.NET Core webalkalmazás-projekt létrehozása](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Az **Új projekt konfigurálása**csoportban nevezze el a projekt *teendőit,* és válassza a **Létrehozás lehetőséget.**

1. Az **Új ASP.NET Core WebApplication** **(Webapplication) (Modell-nézet-vezérlő)** lehetőséget válassza. A folytatáshoz válassza a **Létrehozás** gombot.

   A Visual Studio egy üres MVC-alkalmazást hoz létre.

1. Válassza **a Hibakeresés** > **indítása hibakeresés** vagy az F5 lehetőséget a ASP.NET alkalmazás helyi futtatásához.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>3. lépés: Azure Cosmos DB NuGet csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges core MVC keretkód ASP.NET nagy részével, adjuk hozzá az Azure Cosmos DB-hez való csatlakozáshoz szükséges NuGet-csomagokat.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**keresse meg és válassza a **Microsoft.Azure.Cosmos lehetőséget.** Válassza az **Install** (Telepítés) lehetőséget.

   ![NuGet csomag telepítése](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   A Visual Studio letölti és telepíti az Azure Cosmos DB csomagot és annak függőségeit.

   A Package **Manager konzol** segítségével is telepítheti a NuGet csomagot. Ehhez válassza az **Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt.** A parancssorba írja be a következő parancsot:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>4. lépés: A ASP.NET Core MVC alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézeteket és a vezérlőket ehhez az MVC alkalmazáshoz.

### <a name="add-a-model"></a><a name="add-a-model"></a>Modell hozzáadása

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Modellek** mappára, és válassza az**Osztály** **hozzáadása parancsot.** > 

1. Az **Új elem hozzáadása csoportban**nevezze el az új *osztályt Item.cs,* és válassza a **Hozzáadás**lehetőséget.

1. Cserélje le *Item.cs* osztály tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Az Azure Cosmos DB a JSON segítségével adatok áthelyezése és tárolása. Az attribútum `JsonProperty` segítségével szabályozhatja, hogy a JSON hogyan szerializálja és deszerializálja az objektumokat. Az `Item` osztály bemutatja az `JsonProperty` attribútumot. Ez a kód határozza meg a JSON-ba kerülő tulajdonságnév formátumát. Átnevezi a .NET `Completed`tulajdonságot is .

### <a name="add-views"></a><a name="add-views"></a>Nézetek hozzáadása

Ezután hozzuk létre a következő három nézetet.

* Listaelem nézet hozzáadása
* Új elemnézet hozzáadása
* Szerkesztési elemnézet hozzáadása

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Listaelem nézet hozzáadása

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Nézetek** mappára, és válassza az Új mappa **hozzáadása** > **parancsot.** Nevezze el a *mappát Elem*.

1. Kattintson a jobb gombbal az üres **Elem** mappára, majd válassza **a Nézet hozzáadása parancsot.** > **View**

1. Az **MVC nézet hozzáadása csoportban**adja meg a következő értékeket:

   * A **Nézet nevében**írja be az *Index*értéket.
   * A **Sablon listában**válassza a Lista **lehetőséget.**
   * A **Modellosztályban**válassza **az Elem (teendő) lehetőséget. modellek)**.
   * Válassza **az Elrendezéslap használata** lehetőséget, és írja be a *~/Views/Shared/_Layout.cshtml parancsot.*

   ![Az MVC nézet hozzáadása párbeszédpanelképernyő-képe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Miután hozzáadja ezeket az értékeket, válassza **a Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio hozzon létre egy új sablonnézetet.

Miután elkészült, a Visual Studio megnyitja az általa létrehozott *cshtml* fájlt. Ezt a fájlt bezárhatja a Visual Studióban. Majd később visszatérünk rá.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Új elemnézet hozzáadása

A listaelemek nézetének létrehozásához hasonlóan hozzon létre egy új nézetet, amely nek köszönhetően elemeket hozhat létre az alábbi lépésekkel:

1. A **Megoldáskezelőben**kattintson ismét a jobb gombbal az **Elem** mappára, és válassza **a Nézet hozzáadása parancsot.** > **View**

1. Az **MVC nézet hozzáadása nézetben**hajtsa végre a következő módosításokat:

   * A **Nézet nevében**írja be a *Create (Létrehozás) értéket.*
   * A **Sablon**ban válassza a **Létrehozás**lehetőséget.
   * A **Modellosztályban**válassza **az Elem (teendő) lehetőséget. modellek)**.
   * Válassza **az Elrendezéslap használata** lehetőséget, és írja be a *~/Views/Shared/_Layout.cshtml parancsot.*
   * Válassza a **Hozzáadás** lehetőséget.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Szerkesztési elemnézet hozzáadása

Végül pedig adjon hozzá egy nézetet egy elem szerkesztéséhez a következő lépésekkel:

1. A **Megoldáskezelőben**kattintson ismét a jobb gombbal az **Elem** mappára, és válassza **a Nézet hozzáadása parancsot.** > **View**

1. Az **MVC nézet hozzáadása nézetben**hajtsa végre a következő módosításokat:

   * A **View name** (Nézet neve) mezőbe írja be az *Edit* (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az **Edit** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az **Item (todo.Models)** elemet.
   * Válassza **az Elrendezéslap használata** lehetőséget, és írja be a *~/Views/Shared/_Layout.cshtml parancsot.*
   * Válassza a **Hozzáadás** lehetőséget.

Miután elvégezte ezeket a lépéseket, zárja be az összes *cshtml* dokumentumot a Visual Studióban, amikor később visszatér ezekhez a nézetekhez.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Szolgáltatások deklarizálása és inicializálása

Először is hozzáadunk egy osztályt, amely tartalmazza az Azure Cosmos DB-hez való csatlakozás és azok használatához való csatlakozás logikáját. Ebben az oktatóanyagban ezt a logikát egy `CosmosDBService` osztálynak nevezett `ICosmosDBService`osztályba és egy felületbe ágyazjuk be. Ez a szolgáltatás a CRUD műveleteket végzi. Emellett olvasási hírcsatorna-műveleteket is végez, például hiányos elemeket sorol fel, létrehozza, szerkeszti és lemondja az elemeket.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza az Új mappa **hozzáadása** > **parancsot.** Nevezze el a *Szolgáltatás mappát.*

1. Kattintson a jobb gombbal a **Szolgáltatások** mappára, és válassza az Osztály **hozzáadása parancsot.** > **Class** Nevezze el az új *cosmosDBService osztályt,* és válassza **a Hozzáadás lehetőséget.**

1. Cserélje le *CosmosDBService.cs* tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Kattintson a jobb gombbal a **Szolgáltatások** mappára, és válassza az Osztály **hozzáadása parancsot.** > **Class** Nevezze el az új *ICosmosDBService osztályt,* és válassza **a Hozzáadás lehetőséget.**

1. Adja hozzá a következő kódot az *ICosmosDBService* osztályhoz:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Nyissa meg a *Startup.cs* fájlt `ConfigureServices` a megoldásban, és cserélje le a módszert a következőkre:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    Ebben a lépésben a kód inicializálja az ügyfelet a konfiguráció alapján, mint egypéldányos példányt, amelyet [a ASP.NET Core függőségi injektálásával](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)kell injektálni.

1. Ugyanebben a fájlban adja hozzá a következő módszert **InitializeCosmosClientInstanceAsync**, amely beolvassa a konfigurációt, és inicializálja az ügyfelet.

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Adja meg a konfigurációt a projekt *appsettings.json* fájljában a következő kódrészletben látható módon:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Vezérlő hozzáadása

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **Vezérlők** mappára, és válassza a**Vezérlő** **hozzáadása parancsot.** > 

1. Az **Állvány hozzáadása csoportban**válassza az **MVC-vezérlő - Üres** lehetőséget, és válassza a **Hozzáadás**lehetőséget.

   ![MVC-vezérlő kiválasztása – üres állványzat hozzáadása](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nevezze el az új *vezérlőItemController*.

1. Cserélje le *ItemController.cs* tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

A **ValidateAntiForgeryToken** attribútum itt használatos, hogy megvédje ezt az alkalmazást a helyek közötti kérelem hamisítási támadások ellen. Az Ön nézeteinek ezzel az anti-hamisítási tokennel is működniük kell. További információt és példákat a [webhelyközi kérelmek hamisításának (CSRF) támadásainak megakadályozása ASP.NET MVC alkalmazásban című témakörben talál.][Preventing Cross-Site Request Forgery] A [GitHub][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.

A metódus **paraméteren** lévő Bind attribútumot is használjuk a túlzott közzétételi támadások elleni védelemérdekében. További információ: [A CRUD-funkciók megvalósítása az Entitáskeretrendszerrel ASP.NET MVC-ben.][Basic CRUD Operations in ASP.NET MVC]

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>5. lépés: Az alkalmazás helyi futtatása

Az alkalmazás helyi számítógépen történő teszteléséhez kövesse az alábbi lépéseket:

1. Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő létrehozásához. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:

   ![Képernyőkép az oktatóanyag által létrehozott teendőlista-webalkalmazásról](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Ha az alkalmazás inkább a kezdőlapra `/Item` nyílik meg, fűzz hozzá az URL-címhez.

1. Jelölje ki az **Új létrehozása** hivatkozást, és adjon értékeket a **Név** és a **Leírás** mezőkhöz. Hagyja bejelölve a **Befejezett** jelölőnégyzetet. Ha kiválasztja, az alkalmazás az új elemet befejezett állapotban adja hozzá. Az elem már nem jelenik meg az eredeti listán.

1. Kattintson a **Létrehozás** gombra. Az alkalmazás visszaküldi Önt az **Index** nézetbe, és az elem megjelenik a listában. A **teendők** listájához hozzáadhat még néhány elemet.

    ![Képernyőkép a Tárgymutató nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Válassza a **Szerkesztés lehetőséget** a lista **egy eleme** mellett. Az alkalmazás megnyitja a **Szerkesztés** nézetet, ahol frissítheti az objektum bármely tulajdonságát, beleértve a Befejezett jelzőt **is.** Ha a **Befejezett** lehetőséget választja, és a Mentés lehetőséget **választja,** az alkalmazás az **elemet** a listában befejezettként jeleníti meg.

   ![Képernyőkép a Tárgymutató nézetről, bejelölve a Befejezett jelölőnégyzettel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Ellenőrizze az adatok állapotát az Azure Cosmos DB szolgáltatásban a [Cosmos Explorer](https://cosmos.azure.com) vagy az Azure Cosmos DB-emulátor adatkezelője segítségével.

1. Miután tesztelte az alkalmazást, válassza a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének leállításához. Készen áll a telepítésre!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>6. lépés: Az alkalmazás telepítése

Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben,** és válassza **a Közzététel parancsot.**

1. A **Közzétételi cél kiválasztása**csoportban válassza az App **Service**lehetőséget.

1. Meglévő App Service-profil használatához válassza **a Létező kijelölése**lehetőséget, majd a **Közzététel**lehetőséget.

1. Az **App Service alkalmazásban**válasszon egy **előfizetést.** A **Nézet** szűrővel erőforráscsoport vagy erőforrástípus szerint rendezhet.

1. Keresse meg a profilját, majd kattintson **az OK gombra.** Ezután keresse meg a szükséges Azure App Service szolgáltatást, és válassza **az OK gombot.**

   ![A Visual Studio App Service párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Egy másik lehetőség egy új profil létrehozása:

1. Az előző eljáráshoz megfelelően kattintson a jobb gombbal a projektre a **Megoldáskezelőben,** és válassza **a Közzététel parancsot.**
  
1. A **Közzétételi cél kiválasztása**csoportban válassza az App **Service**lehetőséget.

1. A **Közzétételi cél kiválasztása**csoportban válassza az Új **létrehozása** lehetőséget, és válassza **a Közzététel**lehetőséget.

1. Az **App Service alkalmazásban**adja meg a Web App nevét és a megfelelő előfizetést, erőforráscsoportot és üzemeltetési csomagot, majd válassza a **Létrehozás lehetőséget.**

   ![A Visual Studio App Service létrehozása párbeszédpanelje](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Néhány másodperc en belül a Visual Studio közzéteszi a webalkalmazást, és elindít egy böngészőt, ahol láthatja, hogy a projekt fut az Azure-ban!

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre ASP.NET Core MVC webalkalmazást. Az alkalmazás hozzáférhet az Azure Cosmos DB-ben tárolt adatokhoz. Most már folytathatja az alábbi erőforrásokat:

* [Particionálás az Azure Cosmos DB-ben](./partitioning-overview.md)
* [Az SQL-lekérdezések első lépései](./how-to-sql-query.md)
* [Adatok modellezése és particionálása az Azure Cosmos DB-ben való életből vett példa használatával](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
