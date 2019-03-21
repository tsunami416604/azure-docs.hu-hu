---
title: Oktatóanyag fejleszthet ASP.NET MVC webalkalmazás az Azure Cosmos DB .NET-keretrendszerrel előzetes SDK-t.
description: Az oktatóanyag azt ismerteti, hogy egy ASP .NET MVC-webalkalmazás létrehozása Azure Cosmos DB használatával. Fogja tárolni, és JSON-adatok elérése az Azure-ban tárolt teendőkezelő alkalmazásból.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: bf1da7e8a1041b15076ebda6eeac9b0a75c567c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857164"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Oktatóanyag: Az Azure Cosmos DB az ASP.NET MVC webalkalmazás fejlesztése .NET SDK-előzetes verzió használatával 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET-előzetes verzió](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Ez az oktatóanyag bemutatja, hogyan Azure Cosmos DB használatával tárolhatja és érheti el adatait üzemeltetett ASP.NET MVC alkalmazásnak az Azure-ban. Ebben az oktatóanyagban használja a .NET SDK V3, amely jelenleg előzetes verzióban érhető el. Az alábbi képen látható a weblap, amelyen ez a cikk a minta használatával fog létrehozni:
 
![Az MVC-webalkalmazás oktatóanyag – ASP NET MVC oktatóprogram lépésről lépésre során létrehozott teendőlista képernyőképe](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Ha nincs ideje az oktatóanyag elvégzéséhez, letöltheti a teljes mintaprojektet a [GitHub][GitHub]. 

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> * Egy Azure Cosmos-fiók létrehozása
> * ASP.NET MVC alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB 
> * Az adatok a CRUD-műveletek végrehajtása

> [!TIP]
> Ez az oktatóprogram feltételezi, hogy van korábbi tapasztalata az ASP.NET MVC és az Azure webhelyek használatában. Ha még nem ismeri az ASP.NET rendszert vagy a [előfeltételt jelentő eszközöket](#prerequisites), azt javasoljuk, hogy töltse le a teljes mintaprojektet a [GitHub][GitHub], adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. Miután a projekt buildjének elkészítéséhez, ez a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek 

A jelen cikkben lévő utasítások követése előtt győződjön meg arról, hogy rendelkezik az alábbi erőforrásokkal:

* **Aktív Azure-fiók:** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK for .NET a Visual Studio 2017-hez – a Visual Studio telepítőjén keresztül érhető el.

Ez a cikk a képernyőképek csak a Microsoft Visual Studio Community 2017 használatával került sor. Ha a rendszer egy másik verziója van konfigurálva, lehetséges, hogy a képernyők és beállítások előfordulhat, hogy nem egyeznek tökéletesen, de ha megfelel a fenti előfeltételeknek ebben a megoldásban használható.

## <a name="create-an-azure-cosmos-account"></a>1. lépés: Egy Azure Cosmos-fiók létrehozása

Először hozzon létre egy Azure Cosmos-fiók. Ha már rendelkezik egy Azure Cosmos DB SQL API-fiókot, vagy ha az Azure Cosmos DB emulatort használja ehhez az oktatóanyaghoz, továbbléphet a [hozzon létre egy új ASP.NET MVC alkalmazás](#create-a-new-mvc-application) szakaszban.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban hozzon létre egy új ASP.NET MVC-alkalmazás. 

## <a name="create-a-new-mvc-application"></a>2. lépés: Új ASP.NET MVC alkalmazás létrehozása

1. A Visual Studióban az a **fájl** menüben válassza a **új**, majd válassza ki **projekt**. Megjelenik a **New project** (Új projekt) párbeszédpanel.

2. Az a **új projekt** ablakában bontsa ki a **telepített** sablonokat, **Visual C#** , **webes**, majd válassza ki **ASP. NET webes alkalmazás**. 

   ![Hozzon létre új ASP.NET webalkalmazás projekthez](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. A **Name** (Név) szövegmezőbe írja be a projekt nevét. Ez az oktatóprogram a „todo” (teendők) nevet használja. Ha más nevet, majd ez az oktatóanyag ismerteti a teendőlista-névteret, ahol szükség esetén módosítsa a megadott kódmintákat úgy, hogy használja az alkalmazást. 

4. Válassza ki **Tallózás** lépjen abba a mappába, ahol szeretné a projekt létrehozásához, és válassza a **.NET-keretrendszer 4.6.1-es** vagy újabb verziója. Kattintson az **OK** gombra. 

5. Megjelenik a **Új ASP.NET-webalkalmazás** párbeszédpanel. A sablonok panelén válassza az **MVC** elemet.

6. Válassza ki **OK** és hagyhatja, hogy a Visual Studio összecsomagolja az üres ASP.NET MVC sablonban körül. 

7. Miután a Visual Studio a bolierplate MVC alkalmazás létrehozása befejeződött, van egy üres ASP.NET alkalmazást, amelyet helyileg futtathat.

## <a name="add-nuget-packages"></a>3. lépés: Az Azure Cosmos DB NuGet-csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET MVC framework-kód a legtöbb, adjuk hozzá a NuGet-csomagok Azure Cosmos DB-hez való kapcsolódáshoz szükséges.

1. Az Azure Cosmos DB .NET SDK NuGet-csomagként van csomagolva és elosztva. A NuGet-csomagot a Visual Studióban, amelyet a NuGet-Csomagkezelőt a Visual Studióban kattintson a jobb gombbal a projektre a **Megoldáskezelőben** majd **NuGet-csomagok kezelése**.
   
   ![Képernyőfelvétel a NuGet-csomagok kezelése kiemelve a Megoldáskezelőben a webalkalmazás projekt helyi közül.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel. A nuget **Tallózás** mezőbe írja be **Microsoft.Azure.Cosmos**. Az eredmények közül telepítse a **Microsoft.Azure.Cosmos** 3.0.0.1-preview verzió. Ez letölti és telepíti az Azure Cosmos DB csomagot és annak függőségeit, például a newtonsoft.JSON elemet. Válassza ki **OK** a a **előzetes** ablakot, és **elfogadom** a a **licencfeltételek elfogadását** ablakban a telepítés befejezéséhez.
   
   A Package Manager konzol segítségével azt is megteheti, telepítse a NuGet-csomagot. Az ehhez a **eszközök** menüjében válassza **NuGet-Csomagkezelő**, majd válassza ki **Package Manager Console**. A parancssorba írja be a következő parancsot:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. A csomag telepítése után a Visual Studio-megoldás a két új könyvtár mutató hivatkozásokat a Microsoft.Azure.Cosmos.Client, és a newtonsoft.JSON elemet kell tartalmaznia.
  
## <a name="set-up-the-mvc-application"></a>4. lépés: Az ASP.NET MVC alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézetek és a tartományvezérlők ehhez az MVC alkalmazáshoz:

* [Modell hozzáadása](#add-a-model).
* [Vezérlő hozzáadása](#add-a-controller).
* [Nézetek hozzáadása](#add-views).

### <a name="add-a-model"></a> Modell hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **modellek** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel.

1. Adja az új osztálynak **TodoItem.cs** válassza **Hozzáadás**. 

1. Ezután cserélje le a kód "Todoitem" osztályban az alábbi kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Az Azure Cosmos DB-ben tárolt adatokat a hálózaton keresztül továbbított és JSON-fájlként tárolja. A módon az objektumok JSON.NET általi szerializálni vagy deszerializálni szabályozásához használhatja a **JsonProperty** attribútumot, ahogyan az a **TodoItem** létrehozott osztályt. Csak nem is szabályozhatja, hogy a goes JSON formátumba, átnevezheti a .NET tulajdonságokat, ahogyan azt tette a tulajdonságnév formátumát a **leírás** tulajdonság. 

### <a name="add-a-controller"></a>Vezérlő hozzáadása

1. Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **tartományvezérlők** mappáját, válassza ki **Hozzáadás**, majd válassza ki **vezérlő**. Megjelenik az **Add Scaffold** (Szerkezet hozzáadása) párbeszédpanel.

1. Válassza ki **MVC 5 vezérlő - üres** válassza **Hozzáadás**.

   ![A Scaffold hozzáadása párbeszédpanelen az MVC 5 vezérlő - üres opció kiemelésével – képernyőfelvétel](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nevezze el az új vezérlőnek ** ItemController, és a kódot, az adott fájlban cserélje le a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   A **ValidateAntiForgeryToken** attribútum segítségével itt segít megvédeni az alkalmazást a webhelyközi kérések hamisítása ellen. Csak ez az attribútum hozzáadásánál hozzá további, a nézetek, valamint a hamisításgátló jogkivonat együttműködve. A tulajdonos, és a példák a megvalósításának módjáról további információkért lásd: [megakadályozza, hogy Webhelyközi kérések hamisításának megakadályozása][Preventing Cross-Site Request Forgery]. A [GitHubon][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.
   
   Is használhatja a **kötési** attribútum a metódus paraméteren túlküldéses támadások elleni védelem érdekében. További részletekért tekintse meg [alapvető CRUD műveletek az ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Nézetek hozzáadása

Következő lépésként hozzon létre az alábbi három nézet: 

* [Lista elemnézet hozzáadása](#AddItemIndexView).
* [Új elemnézet hozzáadása](#AddNewIndexView).
* [Nézet szerkesztése elem hozzáadása](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Lista elemnézet hozzáadása

1. A **Megoldáskezelőben**, bontsa ki a **nézetek** mappát, kattintson a jobb gombbal az üres **elem** mappát, a Visual Studio hozzáadásakor az Ön számára létrehozott a  **ItemController** korábban, kattintson a **Hozzáadás**, és kattintson a **nézet**.
   
   ![Képernyőkép a Megoldáskezelőben, amely a Visual Studio használatával a nézet hozzáadása a parancsok vannak kiemelve létrehozott Item mappa](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. Az a **nézet hozzáadása** párbeszédpanel mezőben módosítsa a következő értékeket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Index*** nevet.
   * A **Template** (Sablon) mezőben válassza a ***List*** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
     
   ![Képernyőfelvétel a nézet hozzáadása párbeszédpanel:](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

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

Itt először is, adjon hozzá egy osztályt, amely tartalmazza a logika csatlakozhat, és az Azure Cosmos dB-ben. Ebben az oktatóanyagban azt fogjuk magába foglalja a logikai egy TodoItemService.cs nevű osztályba. Ez a kód beolvassa az Azure Cosmos DB végpont értékek űrlap a konfigurációs fájlt, és elvégzi a CRUD-műveletek, például a hiányos elemek listázása, létrehozására, szerkesztésére és az elemek törlése. 

1. A **Megoldáskezelőben**, hozzon létre egy új mappát a projekt neve **szolgáltatások**.

1. Kattintson a jobb gombbal a **szolgáltatások** mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**. Az új osztály neve **TodoItemService** válassza **Hozzáadás**.

1. Adja hozzá a következő kódot a **TodoItemService** osztályt, és a kódot, az adott fájlban cserélje le a következő kódot:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Az előző kód olvassa be a kapcsolati karakterlánc-értékeket a konfigurációs fájlból. A kapcsolati karakterlánc-értékek az Azure Cosmos-fiók frissítéséhez nyissa meg a **Web.config** fájlt a projektben, és adja hozzá a következő sorokat a a `<AppSettings>` szakaszban:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Frissítse a végpont és a primarykey értékeket lekért értékekkel a **kulcsok** panel az Azure Portalon. Használja a **URI** értéket a végpont beállítást, és használja a kulcsok panelről való a **elsődleges kulcs**, vagy **másodlagos kulcs** kulcsok paneljéről, a kulcsok beállítása. Ez gondoskodik csatlakoztatása az Azure Cosmos DB most az alkalmazáshoz, most adja hozzá az alkalmazás logikáját.

1. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá a következő sort az **Application_Start** metódushoz 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   A megoldás ezen a ponton a projekt hibák nélkül képesnek kell lennie. Ha futtatja az alkalmazást, a **HomeController** és a **Index** nézetébe kerülne kell megnyitni. Ez a kezdetekor választottuk MVC sablonprojekt alapértelmezett viselkedése. Módosítsuk a jelen MVC alkalmazás útválasztását ezen viselkedés megváltoztatásához.

1. Nyissa meg ***alkalmazás\_Start\RouteConfig.cs*** , és keresse meg a kezdetű sort "alapértelmezett értéke:", és frissítse az alábbi kódra:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

   Ez a kód most közli az ASP.NET MVC, ha nem adott meg egy értéket az URL-címben az útválasztási viselkedés vezérlő helyett **kezdőlap**, használ **elem** , a vezérlő és **Index**nézetként.

Most futtatja az alkalmazást, ha meghívja-e be a **ItemController** , amely a GetItems olyan módszereket hív meg, amelyeket a következő szakaszban TodoItemService osztályhoz. 

Ha most felépíti és futtatja ezt a projektet, valami ilyesmit kell látnia.    

![A teendőlista webalkalmazás az adatbázis-oktatóprogram során létrehozott képernyőképe](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>6. lépés: Az alkalmazás helyi futtatása

A helyi gépen lévő alkalmazás teszteléséhez használja a következő lépéseket:

1. Az alkalmazás hibakeresési módban, a Visual studióban nyomja le az F5. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:
   
   ![A teendőlista webalkalmazás az adatbázis-oktatóprogram során létrehozott képernyőképe](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Kattintson a **Create New** (Új létrehozása) hivatkozásra, és adjon értékeket a **Name** (Név) és a **Description** (Leírás) mezőkbe. Hagyja a **befejezve** jelölőnégyzet nincs kiválasztva ellenkező esetben az új elem egy befejezett állapotba kerül, és nem jelenik meg a kiindulási lista.
   
3. Kattintson a **létrehozás** és vissza a rendszer átirányítja a **Index** nézet és az elem megjelenik a listában. Néhány további elemek is hozzáadhat a teendőlistához.

    ![Képernyőfelvétel az Index nézetről](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Kattintson az **Edit** (Szerkesztés) gombra a lista egy **eleme** mellett, és az **Edit** (Szerkesztés) nézetbe kerül, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **Completed** (Befejezve) jelzőt. Ha bejelöli a **Complete** (Befejezve) jelzőt és a **Save** (Mentés) gombra kattint, azzal eltávolítja az **elemet** a hiányos feladatok listájából.
   
   ![Képernyőfelvétel az Index nézetről, bejelölt Completed (Befejezve) be van jelölve](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Ha befejezte az alkalmazás tesztelését, nyomja meg a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének befejezéséhez. Készen áll a telepítésre!

## <a name="deploy-the-application-to-azure"></a>7. lépés: Az alkalmazás központi telepítése 
Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben** válassza **közzététel**.
   
2. A a **közzététel** párbeszédpanelen jelölje ki **Microsoft Azure App Service**, majd válassza **hozzon létre új** hozzon létre egy App Service-profilt, vagy válasszon **kiválasztása Meglévő** egy meglévő profil.

3. Ha rendelkezik egy meglévő Azure App Service-profilt, válassza ki a **előfizetés** a legördülő listából. Használja a **nézet** szűrő erőforráscsoport vagy erőforrás típusa szerinti rendezéshez. Ezután keresse meg a szükséges Azure App Service, és válassza ki **OK**. 
   
   ![A Visual Studio App Service párbeszédpanelje](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Új Azure App Service-profil létrehozásához kattintson az **Új létrehozása** lehetőségre a **Közzététel** párbeszédpanelen. Az a **létrehozása App Service** párbeszédpanelen adja meg a webalkalmazás neve és a megfelelő előfizetést, erőforráscsoportot és App Service-csomagot, majd válassza ki **létrehozás**.

   ![A Visual Studio App Service létrehozása párbeszédpanelje](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Néhány másodpercen belül a Visual Studio tesz közzé a webalkalmazást, és elindít egy böngészőt, ahol láthatja a projekt az Azure-ban futó!

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban bemutattuk, hogyan hozhat létre egy ASP.NET MVC-webalkalmazást, amely érhessék el az Azure Cosmos DB-ben tárolt adatokat. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB SQL API-fiókban tárolt adatok elérését a Java-alkalmazás létrehozása]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
