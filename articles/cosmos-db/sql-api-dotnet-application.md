---
title: 'ASP.NET MVC oktatóprogram az Azure Cosmos DB szolgáltatáshoz: webalkalmazás-fejlesztés | Microsoft Docs'
description: ASP.NET MVC oktatóprogram MVC webalkalmazás létrehozásához az Azure Cosmos DB szolgáltatással. A JSON-fájlok tárolása és az adatok elérése az Azure-webhelyeken tárolt teendőkezelő alkalmazásból történik – ASP NET MVC oktatóprogram lépésről lépésre.
keywords: asp.net mvc oktatóanyag, webalkalmazás fejlesztése, mvc-webalkalmazás, asp net mvc lépésről lépésre haladó oktatóanyag
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: mimig
ms.custom: devcenter
ms.openlocfilehash: e4f9360bfde5f3212f0bbf184c52f59b2dd43d0b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="_Toc395809351"></a>ASP.NET MVC oktatóprogram: webalkalmazás fejlesztése az Azure Cosmos DB szolgáltatással
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Ez a cikk teljes körűen bemutatja, hogyan építhet teendőkezelő alkalmazást az Azure Cosmos DB eszközzel, és ezáltal hogyan használhatja hatékonyan az Azure Cosmos DB-t a JSON-dokumentumok tárolására és lekérdezésére. A feladatok JSON-dokumentumokként lesznek tárolva az Azure Cosmos DB-ben.

![Az oktatóprogram során létrehozott teendőlista-kezelő MVC webalkalmazás képernyőfelvétele – ASP NET MVC oktatóprogram lépésről lépésre](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Ez az útmutató bemutatja, hogyan tárolja az Azure Cosmos DB szolgáltatás használatára és Azure rendszeren üzemeltetett ASP.NET MVC webalkalmazás érheti el adatait. Ha olyan oktatóprogramot keres, amely csak az Azure Cosmos DB szolgáltatással foglalkozik, az ASP.NET MVC összetevőkkel nem, akkor tekintse meg: [Azure Cosmos DB C# konzolalkalmazás felépítése](sql-api-get-started.md).

> [!TIP]
> Ez az oktatóprogram feltételezi, hogy van korábbi tapasztalata az ASP.NET MVC és az Azure webhelyek használatában. Ha nem ismeri az ASP.NET rendszert vagy az [előfeltételt jelentő eszközöket](#_Toc395637760), érdemes letöltenie a teljes mintaprojektet a [GitHubról][GitHub], és követni a mintában lévő utasításokat. Ha felépítette, ezen cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében.
> 
> 

## <a name="_Toc395637760"></a>Az adatbázis-oktatóanyag előfeltételei
A jelen cikkben lévő utasítások követése előtt rendelkeznie kell a következőkkel:

* Aktív Azure-fiók.  Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* A Microsoft Azure SDK for .NET for Visual Studio 2017, a Visual Studio telepítő keresztül érhető el.

Ez a cikk összes képernyőfelvétele használatával a Microsoft Visual Studio Community 2017 került sor. Ha a rendszer a lehetséges, hogy a képernyők és beállítások nem egyeznek tökéletesen, de ha megfelel a fenti előfeltételeknek ebben a megoldásban kell működnie egy másik verzió van konfigurálva.

## <a name="_Toc395637761"></a>1. lépés: Azure Cosmos DB-adatbázisfiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik az SQL-fiókkal az Azure Cosmos DB, vagy ha az oktatóanyag az Azure Cosmos DB Emulator használ, továbbléphet a [hozzon létre egy új ASP.NET MVC alkalmazást](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Most végigvezetjük azon, hogyan hozhat létre új ASP.NET MVC alkalmazást az alapoktól. 

## <a name="_Toc395637762"></a>2. lépés: Új ASP.NET MVC alkalmazás létrehozása

1. A Visual Studio programban, a **File** (Fájl) menüben mutasson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre. Megjelenik a **New project** (Új projekt) párbeszédpanel.

2. A **Project types** (Projekttípusok) panelen bontsa ki a **Templates** (Sablonok), **Visual C#**, **Web** elemeket, majd válassza az **ASP.NET Web Application** (ASP.NET webalkalmazás) elemet.

      ![Képernyőfelvétel a New Project (Új projekt) párbeszédpanelről, ahol az ASP.NET webalkalmazás projekttípus van kijelölve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. A **Name** (Név) szövegmezőbe írja be a projekt nevét. Ez az oktatóprogram a „todo” (teendők) nevet használja. Ha más nevet választ, akkor amikor az oktatóprogram a „todo” (teendők) névteréről beszél, akkor a megadott kódmintákat úgy kell módosítania, hogy az alkalmazás tényleges nevét használja. 
4. Kattintson a **Browse** (Böngészés) gombra azon mappa megkereséséhez, ahol létre szeretné hozni a projektet, majd kattintson az **OK** gombra.
   
      A **új ASP.NET-webalkalmazás** párbeszédpanel jelenik meg.
   
    ![Az MVC alkalmazássablon van kiemelve az új ASP.NET Webalkalmazásként való kezelése párbeszédpanel képernyőképe](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. A sablonok panelén válassza az **MVC** elemet.

6. Kattintson az **OK** gombra, és várja meg, hogy a Visual Studio kialakítsa a szerkezetet az üres ASP.NET MVC sablonban. 

          
7. Ha a Visual Studio befejezte a sablonszöveges MVC alkalmazás létrehozását, egy üres ASP.NET alkalmazást kap, amelyet helyileg futtathat.
   
    Kihagyjuk a projekt helyi futtatását, mert biztosan mindannyian láttuk az ASP.NET „Hello World” alkalmazást. Ugorjunk közvetlenül az Azure Cosmos DB ezen projekthez való hozzáadására és az alkalmazás felépítésére.

## <a name="_Toc395637767"></a>3. lépés: Azure Cosmos DB hozzáadása az MVC webalkalmazás projekthez
Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET MVC bekötések nagy részével, folytassuk az oktatóprogram valódi céljával, amely az Azure Cosmos DB MVC webalkalmazáshoz adása.

1. Az Azure Cosmos DB .NET SDK csomagolt és a NuGet-csomag terjesztése. A Visual Studióban a NuGet-csomag beszerzéséhez használja a Visual Studio NuGet-csomagkezelőjét. Ehhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
   
    ![A Megoldáskezelőben a webalkalmazás projekt helyi menüjének képernyőfelvétele, ahol a Manage NuGet Packages (NuGet-csomagok kezelése) parancs van kiemelve.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel.
2. A NuGet **Browse** (Tallózás) mezőjébe írja be az ***Azure DocumentDB*** szöveget. (A csomag neve nem frissült az Azure Cosmos-Adatbázishoz.)
   
    Az eredmények közül telepítse a **Microsoft Microsoft.Azure.DocumentDB** csomag. Ez letölti és telepíti az Azure Cosmos DB csomagot, valamint az összes függőségét, például a newtonsoft.JSON elemet. Kattintson az **OK** gombra a **Preview** (Előnézet) ablakban, majd az **I Accept** (Elfogadás) gombra a **License Acceptance** (Licenc elfogadása) ablakban a telepítés befejezéséhez.
   
    ![A NuGet-csomagok kezelése ablakban, a Microsoft Azure Cosmos DB Client Library elem van kiemelve képernyőfelvétele](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      A Csomagkezelő konzollal is telepítheti a csomagot. Ehhez a **Tools** (Eszközök) menüben kattintson a **NuGet Package Manager** (NuGet-csomagkezelő) elemre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre. A parancssorba írja be a következőt.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. A csomag telepítése után a Visual Studio megoldásnak a következőre kell hasonlítania két hozzáadott hivatkozással: Microsoft.Azure.Documents.Client és Newtonsoft.Json.
   
    ![A Megoldáskezelőben a JSON adatprojekthez adott két hivatkozás képernyőfelvétele](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>4. lépés: Az ASP.NET MVC alkalmazás beállítása
Most adjuk hozzá a modelleket, a nézeteket és a vezérlőket ehhez az MVC alkalmazáshoz:

* [Modell hozzáadása](#_Toc395637764).
* [Vezérlő hozzáadása](#_Toc395637765).
* [Nézetek hozzáadása](#_Toc395637766).

### <a name="_Toc395637764"></a>JSON adatmodell hozzáadása
Először hozzuk létre az **M-et** az MVC-ből, a modellt. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Models** (Modellek) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra.
   
      Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel.
2. Adja az új osztálynak az **Item.cs** nevet, és kattintson az **Add** (Hozzáadás) gombra. 
3. Ebben az új **Item.cs** fájlban adja hozzá a következőket az utolsó *használati utasítás* után.
   
        using Newtonsoft.Json;
4. Most cserélje le ezt a kódot 
   
        public class Item
        {
        }
   
    a következő kóddal.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Az Azure Cosmos DB összes adata átkerül a hálózaton keresztül, és JSON-fájlként lesz tárolva. Az objektumok JSON.NET általi szerializálási/deszerializálási módjának beállításához használhatja a **JsonProperty** attribútumot, ahogyan az az imént létrehozott **Item** (Elem) osztályban látható. Nem **kell** ezt csinálnia, de biztosítani szeretném, hogy a tulajdonságaim követik a JSON camelCase elnevezési konvenciókat. 
   
    Nem csak a tulajdonságnév formátumát vezérelheti, amikor a JSON-ba kerül, hanem teljesen át is nevezheti a .NET tulajdonságokat, mint ahogyan a **Description** (Leírás) tulajdonsággal tettem. 

### <a name="_Toc395637765"></a>Vezérlő hozzáadása
Ezzel megvagyunk az **M-mel**, most hozzuk létre az MVC **C-jét**, amely vezérlőosztály.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.
   
    Megjelenik az **Add Scaffold** (Szerkezet hozzáadása) párbeszédpanel.
2. Válassza az **MVC 5 Controller - Empty** (MVC 5 vezérlő - Üres) elemet, majd kattintson az **Add** (Hozzáadás) gombra.
   
    ![Az Add Scaffold (Szerkezet hozzáadása) párbeszédpanel képernyőfelvétele, ahol ki van jelölve az MVC 5 Controller - Empty (MVC 5 vezérlő - Üres) lehetőség](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Adja az **ItemController** nevet az új vezérlőnek.
   
    ![Az Add Controller (Vezérlő hozzáadása) párbeszédpanel képernyőfelvétele](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    A fájl létrehozása után a Visual Studio megoldásnak a következőre kell hasonlítania az új ItemController.cs fájllal a **Megoldáskezelőben**. A korábban létrehozott új Item.cs fájl is látható.
   
    ![A Visual Studio megoldás - Megoldáskezelő képernyőfelvétele, ahol ki van emelve az új ItemController.cs és Item.cs fájl](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Bezárhatja az ItemController.cs fájlt, később visszatérünk ahhoz. 

### <a name="_Toc395637766"></a>Nézetek hozzáadása
Most hozzuk létre az MVC **V** elemét, a nézeteket:

* [Elemindexnézet hozzáadása](#AddItemIndexView).
* [Új elemnézet hozzáadása](#AddNewIndexView).
* [Elemszerkesztési nézet hozzáadása](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Elemindexnézet hozzáadása
1. A **Megoldáskezelőben** bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal az üres **Elem** mappára, amelyet a Visual Studio az **ItemController** korábbi hozzáadásakor hozott létre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) elemre.
   
    ![A Megoldáskezelő képernyőfelvétele, amelyen a Visual Studio által létrehozott Item mappa látható, és az Add View (Nézet hozzáadása) parancsok vannak kiemelve](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Index*** nevet.
   * A **Template** (Sablon) mezőben válassza a ***List*** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
     
   ![Az Add View (Nézet hozzáadása) párbeszédpanelt megjelenítő képernyőfelvétel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Amikor ezen értékek mindegyike már be van állítva, kattintson az **Add** (Hozzáadás) gombra és várja meg, hogy a Visual Studio létrehozzon egy új sablonnézetet. Ha ezzel végzett, a rendszer megnyitja a létrehozott cshtml fájlt. Bezárhatjuk ezt a fájlt a Visual Studióban, mivel később visszatérünk hozzá.

#### <a name="AddNewIndexView"></a>Új elemnézet hozzáadása
Az **Elemindex** nézet létrehozásához hasonlóan most létrehozunk egy új nézetet új **elemek** létrehozásához.

1. A **Megoldáskezelőben** ismét kattintson a jobb gombbal az **Item** (Elem) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) gombra.
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be a ***Create*** (Létrehozás) nevet.
   * A **Template** (Sablon) mezőben válassza a ***Create*** (Létrehozás) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Kattintson a **Hozzáadás** parancsra.
   
#### <a name="_Toc395888515"></a>Elemszerkesztési nézet hozzáadása
És végül adjon hozzá egy utolsó nézetet az **elemek** szerkesztéséhez, ahogyan azt korábban is tette.

1. A **Megoldáskezelőben** ismét kattintson a jobb gombbal az **Item** (Elem) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) gombra.
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Edit*** (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az ***Edit*** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Kattintson a **Hozzáadás** parancsra.

Ha ezzel végzett, zárja be az összes cshtml dokumentumot a Visual Studióban, mivel később vissza fog térni ezekhez a nézetekhez.

## <a name="_Toc395637769"></a>5. lépés: Az Azure Cosmos DB csatlakoztatása
Most, hogy elvégeztük az MVC-vel kapcsolatos szokásos feladatokat, adjuk hozzá az Azure Cosmos DB kódját. 

Ebben a szakaszban a következők kezeléséhez adunk hozzá kódot:

* [Hiányos elemek listázása](#_Toc395637770).
* [Elemek hozzáadása](#_Toc395637771).
* [Elemek szerkesztése](#_Toc395637772).

### <a name="_Toc395637770"></a>Hiányos elemek listázása az MVC webalkalmazásban
Itt először hozzá kell adni egy osztályt, amely tartalmazza az Azure Cosmos DB-adatbázishoz való csatlakozás és a DocumentDB használatának összes logikáját. Ehhez az oktatóprogramhoz ezen logikák mindegyikét a DocumentDBRepository nevű adattárba foglaljuk. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra. Adja az új osztálynak a **DocumentDBRepository** nevet, és kattintson az **Add** (Hozzáadás) gombra.
2. Az újonnan létrehozott **DocumentDBRepository** osztályban adja a következő *használati utasításokat* a *névtér-deklaráció* fölé
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net
        
    Most cserélje le ezt a kódot 
   
        public class DocumentDBRepository
        {
        }
   
    a következő kóddal.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
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
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. A konfigurációból adunk hozzá néhány értéket, ezért nyissa meg az alkalmazás **Web.config** fájlját, és adja hozzá a következő sorokat az `<AppSettings>` szakasz alá.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Most frissítse az *endpoint* (végpont) és az *authKey* (hitelesítési kulcs) értékeit az Azure-portál Keys (Kulcsok) panelén. A Keys (Kulcsok) panel **URI-címét** használja a végpontbeállítás értékeként, és a Keys (Kulcsok) panel **PRIMARY KEY** (ELSŐDLEGES KULCS) vagy **SECONDARY KEY** (MÁSODLAGOS KULCS) értékét használja az authKey beállítás értékeként.

    Hogy vesz gondot elvégeztük a Azure Cosmos DB-tárházban, most adjuk hozzá az alkalmazás logikáját.

1. Először is meg szeretnénk tudni jeleníteni a hiányos elemeket a teendőlista alkalmazással.  Másolja és illessze be a következő kódrészletet bárhová a **DocumentDBRepository** osztályban.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Nyissa meg a korábban hozzáadott **ItemController** elemet, és adja a következő *használati utasításokat* a névtér-deklaráció fölé
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Ha a projekt neve nem „todo” (teendők), akkor frissítenie kell a „todo.Models” paranccsal, hogy tükrözze a projekt nevét.
   
    Most cserélje le ezt a kódot
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    a következő kóddal.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá a következő sort az **Application_Start** metódushoz 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

Ekkor ideális esetben hibák nélkül fel kell tudnia építenie az alkalmazást.

Ha most futtatná az alkalmazást, a **HomeController** vezérlőbe és annak **Index** nézetébe kerülne. Ez az először választott MVC sablonprojekt alapértelmezett viselkedése, de mi nem ezt szeretnénk! Módosítsuk a jelen MVC alkalmazás útválasztását ezen viselkedés megváltoztatásához.

Nyissa meg az ***App\_Start\RouteConfig.cs*** fájlt, keresse meg a „defaults:” kezdetű sort, és módosítsa úgy, hogy a következőhöz hasonlítson.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Ez most közli az ASP.NET MVC-vel, ha nem adott meg értéket az URL-címben az útválasztási viselkedés vezérléséhez, hogy a **Home** (Kezdőlap) helyett az **Item** (Elem) elemet használja vezérlőként és a felhasználói **Index** elemet nézetként.

Ha most futtatja az alkalmazást, az az **ItemController** vezérlőt hívja meg, amely az adattár osztályt hívja meg és a GetItems metódussal adja vissza az összes hiányos elemet a **Views**\\**Item**\\**Index** (Nézetek > Elem > Index) nézetben. 

Ha most felépíti és futtatja ezt a projektet, valami ilyesmit kell látnia.    

![A jelen adatbázis-oktatóprogram során létrehozott teendőlista webalkalmazás képernyőfelvétele](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Elemek hozzáadása
Tegyünk néhány elemet az adatbázisba, hogy ne csak egy üres táblát lássunk.

Adjunk néhány kódot az Azure Cosmos DBRepository és az ItemController elemhez, hogy megmaradjon a rekord az Azure Cosmos DB-adatbázisban.

1. Adja hozzá a **DocumentDBRepository** tárhoz a következő metódust:
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Ez a metódus egyszerűen vesz igénybe egy neki küldött objektumot, és továbbra is fennáll az Azure Cosmos-Adatbázisba.
2. Nyissa meg az ItemController.cs fájlt, és adja hozzá a következő kódrészletet az osztályon belül. Az ASP.NET MVC így tudja, hogy mit tegyen a **Create** (Létrehozás) művelethez. Ebben az esetben csak jelenítse meg a korábban létrehozott társított Create.cshtml nézetet.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Most több kódra van szükségünk ebben a vezérlőben, amely elfogadja a **Create** (Létrehozás) nézetből végzett elküldést.
3. Adja hozzá a következő kódblokkot az ItemController.cs osztályhoz, amely közli az ASP.NET MVC-vel, hogy mit tegyen az ezen vezérlőből származó POST űrlapművelettel.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Ez a kód a DocumentDBRepository tárat hívja be, és a CreateItemAsync metódussal őrzi meg az új teendőelemet az adatbázisban. 
   
    **Biztonsági megjegyzés**: A **ValidateAntiForgeryToken** attribútum itt segít megvédeni az alkalmazást a webhelyközi kérések hamisítása ellen. Az attribútum hozzáadásánál többről van szó, a nézeteknek is működniük kell ezzel a hamisítás elleni tokennel. A témáról további részletekért és a megfelelő megvalósításának példáiért lásd: [Webhelyközi kérések hamisításának megakadályozása][Preventing Cross-Site Request Forgery]. A [GitHubon][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.
   
    **Biztonsági megjegyzés**: A metódus paraméteren a **Bind** (Kötés) attribútummal is segítünk a túlküldéses támadások elleni védelemben. További részletekért lásd: [Alapvető CRUD műveletek az ASP.NET MVC-ben][Basic CRUD Operations in ASP.NET MVC].

Ennyi lenne az adatbázishoz új elemek hozzáadásához szükséges kód.

### <a name="_Toc395637772"></a>Elemek szerkesztése
Az egyik utolsó teendő azon funkció hozzáadása, amellyel az **elemek** szerkeszthetők az adatbázisban és megjelölhetők befejezettként. A szerkesztésre szolgáló nézet már a projekthez lett adva, így csak néhány kódot kell ismét hozzáadnunk a vezérlőhöz és a **DocumentDBRepository** osztályhoz.

1. Adja hozzá a következőt a **DocumentDBRepository** osztályhoz.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Ezen metódusok közül az első, a **GetItem** egy elemet kér le az Azure Cosmos DB-adatbázisból, amelyet visszaküld az **ItemController** vezérlőhöz, majd az **Edit** (Szerkesztés) nézethez.
   
    A most hozzáadott metódusok közül a második lecseréli a **dokumentumot** az Azure Cosmos DB-adatbázisban a **dokumentum** azon verziójával, amely az **ItemController** vezérlőből származik.
2. Adja hozzá a következőt az **ItemController** osztályhoz.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Az első metódus a Http GET kérést kezeli, amely akkor történik meg, amikor a felhasználó az **Edit** (Szerkesztés) hivatkozásra kattint az **Index** nézetből. Ez a metódus [**dokumentumot**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) kér le az Azure Cosmos DB-adatbázisból, és az **Edit** (Szerkesztés) nézetbe küldi azt.
   
    Az **Edit** (Szerkesztés) nézet ezután Http POST kérést küld az **IndexController** vezérlőnek. 
   
    A második hozzáadott metódus kezeli a frissített objektum átadását az Azure Cosmos DB-adatbázisnak, hogy megmaradjon az adatbázisban.

Ennyi, ez minden, amire szükségünk van az alkalmazás futtatásához, a hiányos **elemek** listázásához és új **elemek** hozzáadásához, valamint az **elemek** szerkesztéséhez.

## <a name="_Toc395637773"></a>6. lépés: Az alkalmazás helyileg történő futtatása
Az alkalmazás helyi gépen való teszteléséhez tegye a következőket:

1. Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő összeállításához. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:
   
    ![A jelen adatbázis-oktatóprogram során létrehozott teendőlista webalkalmazás képernyőfelvétele](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Kattintson a **Create New** (Új létrehozása) hivatkozásra, és adjon értékeket a **Name** (Név) és a **Description** (Leírás) mezőkbe. Hagyja bejelöletlenül a **Completed** (Befejezve) jelölőnégyzetet, különben az új **elem** befejezett állapotban lesz hozzáadva és nem jelenik meg a kiindulási listában.
   
    ![Képernyőfelvétel a Create (Létrehozás) nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Kattintson a **Create** (Létrehozás) gombra, és a rendszer visszairányítja az **Index** nézetre, ahol az **elem** megjelenik a listában.
   
    ![Képernyőfelvétel az Index nézetről](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Nyugodtan adjon hozzá még néhány **elemet** a teendőlistához.
    
4. Kattintson az **Edit** (Szerkesztés) gombra a lista egy **eleme** mellett, és az **Edit** (Szerkesztés) nézetbe kerül, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **Completed** (Befejezve) jelzőt. Ha bejelöli a **Complete** (Befejezve) jelzőt és a **Save** (Mentés) gombra kattint, azzal eltávolítja az **elemet** a hiányos feladatok listájából.
   
    ![Képernyőfelvétel az Index nézetről, bejelölt Completed (Befejezve) jelölőnégyzettel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Ha befejezte az alkalmazás tesztelését, nyomja meg a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének befejezéséhez. Készen áll a telepítésre!

## <a name="_Toc395637774"></a>7. lépés: Az Azure App Service-alkalmazás központi telepítése 
Most, hogy a teljes alkalmazás megfelelően működik-e az Azure Cosmos DB programot fogjuk a webalkalmazás telepítése az Azure App Service szolgáltatásban.  

1. Az alkalmazás közzétételéhez egyszerűen a jobb gombbal a projektre kell kattintania a **Megoldáskezelőben**, majd a **Publish** (Közzététel) parancsot választania.
   
    ![Képernyőfelvétel a Közzététel lehetőségről a Megoldáskezelőben](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. A a **közzététel** párbeszédpanel, kattintson a **Microsoft Azure App Service**, majd jelölje be **hozzon létre új** hozzon létre egy App Service-profilt, vagy kattintson **meglévő** egy meglévő profilt kell használnia.

    ![A Visual Studio párbeszédpanel közzététele](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Ha egy meglévő Azure App Service-profilt, adja meg az előfizetés nevét. Használja a **nézet** erőforráscsoportba vagy erőforrástípus rendezés szűréséhez, majd válassza ki az Azure App Service. 
   
    ![A Visual Studio App Service párbeszédpanelen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Új Azure App Service-profil létrehozásához kattintson a **hozzon létre új** a a **közzététel** párbeszédpanel megnyitásához. Az a **létrehozása az App Service** párbeszédpanelen adja meg a webes alkalmazás neve és a megfelelő előfizetés, az erőforráscsoport és az App Service-csomag, majd kattintson a **létrehozása**.

    ![App Service párbeszédpanelen létrehozása a Visual Studióban](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Néhány másodpercen belül a Visual Studio befejezi a webalkalmazás közzétételét, és elindít egy böngészőt, ahol láthatja az Azure-beli handiwork!



## <a name="_Toc395637775"></a>Következő lépések
Gratulálunk! Ebben az esetben a beépített az első ASP.NET MVC webalkalmazását az Azure Cosmos DB használatával, és közzétette azt Azure-bA. A teljes alkalmazás forráskódja, beleértve az oktatóprogramban nem szereplő részletezési és törlési funkciót, letölthető vagy klónozható a [GitHubról][GitHub]. Így ha továbbra is érdekli ezen funkcióknak az alkalmazáshoz adása, a kóddal ezt megteheti.

További funkciókat szeretne az alkalmazáshoz adni, tekintse át az elérhető API-kat a [Azure Cosmos .NET kódtárban](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) és nyugodtan az Azure Cosmos .NET kódtárban hozzájárulnak a [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
