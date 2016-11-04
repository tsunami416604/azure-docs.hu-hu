---
title: 'ASP.NET MVC oktatóprogram a DocumentDB szolgáltatáshoz: webalkalmazás-fejlesztés | Microsoft Docs'
description: ASP.NET MVC oktatóprogram MVC webalkalmazás létrehozásához a DocumentDB szolgáltatással. A JSON-fájlok tárolása és az adatok elérése az Azure-webhelyeken tárolt teendőkezelő alkalmazásból történik – ASP NET MVC oktatóprogram lépésről lépésre.
keywords: asp.net mvc oktatóanyag, webalkalmazás fejlesztése, mvc-webalkalmazás, asp net mvc lépésről lépésre haladó oktatóanyag
services: documentdb
documentationcenter: .net
author: syamkmsft
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: syamk

---
# <a name="_Toc395809351"></a>ASP.NET MVC oktatóprogram: webalkalmazás fejlesztése a DocumentDB szolgáltatással
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md) 
> 
> 

Ez a cikk teljes körűen bemutatja, hogyan építhet teendőkezelő alkalmazást az Azure DocumentDB eszközzel, és ezáltal hogyan teheti hatékonnyá a JSON-dokumentumok tárolását és lekérdezését. A feladatok JSON-dokumentumokként lesznek tárolva az Azure DocumentDB-ben.

![Az oktatóprogram során létrehozott teendőlista-kezelő MVC webalkalmazás képernyőfelvétele – ASP NET MVC oktatóprogram lépésről lépésre](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

Ez az útmutató bemutatja, hogyan használhatja az Azure által biztosított DocumentDB szolgáltatást az Azure rendszeren üzemeltetett ASP.NET MVC webalkalmazásról származó adatok eléréséhez. Ha olyan oktatóprogramot keres, amely csak a DocumentDB szolgáltatással foglalkozik, az ASP.NET MVC összetevőkkel nem, akkor tekintse meg: [DocumentDB C# konzolalkalmazás felépítése](documentdb-get-started.md).

> [!TIP]
> Ez az oktatóprogram feltételezi, hogy van korábbi tapasztalata az ASP.NET MVC és az Azure webhelyek használatában. Ha nem ismeri az ASP.NET rendszert vagy az [előfeltételt jelentő eszközöket](#_Toc395637760), érdemes letöltenie a teljes mintaprojektet a [GitHub][GitHub] és követni a mintában lévő utasításokat. Ha felépítette, ezen cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében.
> 
> 

## <a name="_Toc395637760"></a>Az adatbázis-oktatóanyag előfeltételei
A jelen cikkben lévő utasítások követése előtt rendelkeznie kell a következőkkel:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio 2015](http://www.visualstudio.com/) vagy Visual Studio 2013 Update 4 vagy újabb verzió. Ha Visual Studio 2013-at használ, telepítenie kell a [Microsoft.Net.Compilers NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Net.Compilers/) a C# 6.0 támogatásához. 
* Azure SDK for .NET 2.5.1-es vagy újabb verzió, amely a [Microsoft Webplatform-telepítőn][] keresztül érhető el.

A jelen cikk összes képernyőfelvétele az Update 4-es verzióval ellátott Visual Studio 2013 programmal és az Azure SDK for .NET 2.5.1-es verzióval készült. Ha a rendszere más verziókkal van konfigurálva, akkor előfordulhat, hogy a képernyők és beállítások nem egyeznek tökéletesen, de ha megfelel a fenti előfeltételeknek, ennek a megoldásnak működnie kell.

## <a name="_Toc395637761"></a>1. lépés: DocumentDB-adatbázisfiók létrehozása
Először hozzon létre egy DocumentDB-fiókot. Ha már rendelkezik fiókkal, továbbléphet az [Új ASP.NET MVC alkalmazás létrehozása](#_Toc395637762) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
Most végigvezetjük azon, hogyan hozhat létre új ASP.NET MVC alkalmazást az alapoktól. 

## <a name="_Toc395637762"></a>2. lépés: Új ASP.NET MVC alkalmazás létrehozása
Most, hogy már rendelkezik fiókkal, hozzuk létre az új ASP.NET projektet.

1. A Visual Studio programban, a **File** (Fájl) menüben mutasson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre.
   
    Megjelenik a **New project** (Új projekt) párbeszédpanel.
2. A **Project types** (Projekttípusok) panelen bontsa ki a **Templates** (Sablonok), **Visual C#**, **Web** elemeket, majd válassza az **ASP.NET Web Application** (ASP.NET webalkalmazás) elemet.
   
    ![Képernyőfelvétel a New Project (Új projekt) párbeszédpanelről, ahol az ASP.NET webalkalmazás projekttípus van kijelölve](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)
3. A **Name** (Név) szövegmezőbe írja be a projekt nevét. Ez az oktatóprogram a „todo” (teendők) nevet használja. Ha más nevet választ, akkor amikor az oktatóprogram a „todo” (teendők) névteréről beszél, akkor a megadott kódmintákat úgy kell módosítania, hogy az alkalmazás tényleges nevét használja. 
4. Kattintson a **Browse** (Böngészés) gombra azon mappa megkereséséhez, ahol létre szeretné hozni a projektet, majd kattintson az **OK** gombra.
   
    Megjelenik a **New ASP.NET Project** (Új ASP.NET-projekt) párbeszédpanel.
   
    ![Képernyőfelvétel a New ASP.NET Project (Új ASP.NET projekt) párbeszédpanelről, ahol az MVC alkalmazássablon van kiemelve, és be van jelölve a Host in the cloud (Üzemeltetés a felhőben) jelölőnégyzet](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)
5. A sablonok panelén válassza az **MVC** elemet.
6. Ha az Azure rendszeren szeretné üzemeltetni az alkalmazást, jelölje be a bal alsó részen lévő **Host in the cloud** (Üzemeltetés a felhőben) lehetőséget. Kiválasztottuk, hogy a felhőben üzemeltessük és egy Azure-webhelyről futtassuk az alkalmazást. Ezen lehetőség kiválasztásával egy Azure-webhelyet kap, és sokkal könnyebb lesz a dolga, amikor üzembe kell helyeznie a végleges, működő alkalmazást. Ha ezt máshol szeretné üzemeltetni vagy nem szeretné előre konfigurálni az Azure rendszert, akkor egyszerűen törölje a **Host in the Cloud** (Üzemeltetés a felhőben) lehetőséget.
7. Kattintson az **OK** gombra, és várja meg, hogy a Visual Studio kialakítsa a szerkezetet az üres ASP.NET MVC sablonban. 
8. Ha úgy döntött, hogy ezt a felhőben üzemelteti, legalább egy további képernyőt lát, amely megkéri, hogy jelentkezzen be az Azure-fiókjába és adjon meg néhány értéket az új webhelyhez. Adja meg az összes további értéket, és folytassa. 
   
    Itt nem választottam „Adatbázis-kiszolgálót”, mert itt nem Azure SQL adatbázis-kiszolgálót használunk, hanem később hozunk létre új Azure DocumentDB-fiókot az Azure-portálon.
   
    Az **App Service-csomagok** és az **Erőforráscsoportok** kiválasztásáról további információért lásd: [Az Azure App Service díjcsomagjainak részletes áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
    ![A Configure Microsoft Azure Website (Microsoft Azure webhely konfigurálása) párbeszédpanel képernyőfelvétele](./media/documentdb-dotnet-application/image11_1.png)
9. Ha a Visual Studio befejezte a sablonszöveges MVC alkalmazás létrehozását, egy üres ASP.NET alkalmazást kap, amelyet helyileg futtathat.
   
    Kihagyjuk a projekt helyi futtatását, mert biztosan mindannyian láttuk az ASP.NET „Hello World” alkalmazást. Ugorjunk közvetlenül a DocumentDB ezen projekthez való hozzáadására és az alkalmazás felépítésére.

## <a name="_Toc395637767"></a>3. lépés: DocumentDB hozzáadása az MVC webalkalmazás projekthez
Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET MVC bekötések nagy részével, folytassuk az oktatóprogram valódi céljával, amely az Azure DocumentDB MVC webalkalmazáshoz adása.

1. A DocumentDB .NET SDK NuGet-csomagként van csomagolva és elosztva. A Visual Studióban a NuGet-csomag beszerzéséhez használja a Visual Studio NuGet-csomagkezelőjét. Ehhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
   
    ![A Megoldáskezelőben a webalkalmazás projekt helyi menüjének képernyőfelvétele, ahol a Manage NuGet Packages (NuGet-csomagok kezelése) parancs van kiemelve.](./media/documentdb-dotnet-application/image21.png)
   
    Megjelenik a **Manage NuGet Packages** (NuGet-csomagok kezelése) párbeszédpanel.
2. A NuGet **Browse** (Tallózás) mezőjébe írja be az ***Azure DocumentDB*** szöveget.
   
    Az eredmények közül telepítse a **Microsoft Azure DocumentDB Client Library** csomagot. Ez letölti és telepíti a DocumentDB-csomagot, valamint az összes függőségét, például a Newtonsoft.Json elemet. Kattintson az **OK** gombra a **Preview** (Előnézet) ablakban, majd az **I Accept** (Elfogadás) gombra a **License Acceptance** (Licenc elfogadása) ablakban a telepítés befejezéséhez.
   
    ![A Manage NuGet Packages (NuGet-csomagok kezelése) ablak képernyőfelvétele, ahol a Microsoft Azure DocumentDB Client Library elem van kiemelve](./media/documentdb-dotnet-application/nuget.png)
   
    A Csomagkezelő konzollal is telepítheti a csomagot. Ehhez a **Tools** (Eszközök) menüben kattintson a **NuGet Package Manager** (NuGet-csomagkezelő) elemre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre. A parancssorba írja be a következőt.
   
        Install-Package Microsoft.Azure.DocumentDB
3. A csomag telepítése után a Visual Studio megoldásnak a következőre kell hasonlítania két hozzáadott hivatkozással: Microsoft.Azure.Documents.Client és Newtonsoft.Json.
   
    ![A Megoldáskezelőben a JSON adatprojekthez adott két hivatkozás képernyőfelvétele](./media/documentdb-dotnet-application/image22.png)

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
   
    A DocumentDB összes adata átkerül a hálózaton keresztül, és JSON-fájlként lesz tárolva. Az objektumok JSON.NET általi szerializálási/deszerializálási módjának beállításához használhatja a **JsonProperty** attribútumot, ahogyan az az imént létrehozott **Item** (Elem) osztályban látható. Nem **kell** ezt csinálnia, de biztosítani szeretném, hogy a tulajdonságaim követik a JSON camelCase elnevezési konvenciókat. 
   
    Nem csak a tulajdonságnév formátumát vezérelheti, amikor a JSON-ba kerül, hanem teljesen át is nevezheti a .NET tulajdonságokat, mint ahogyan a **Description** (Leírás) tulajdonsággal tettem. 

### <a name="_Toc395637765"></a>Vezérlő hozzáadása
Ezzel megvagyunk az **M-mel**, most hozzuk létre az MVC **C-jét**, amely vezérlőosztály.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.
   
    Megjelenik az **Add Scaffold** (Szerkezet hozzáadása) párbeszédpanel.
2. Válassza az **MVC 5 Controller - Empty** (MVC 5 vezérlő - Üres) elemet, majd kattintson az **Add** (Hozzáadás) gombra.
   
    ![Az Add Scaffold (Szerkezet hozzáadása) párbeszédpanel képernyőfelvétele, ahol ki van jelölve az MVC 5 Controller - Empty (MVC 5 vezérlő - Üres) lehetőség](./media/documentdb-dotnet-application/image14.png)
3. Adja az **ItemController** nevet az új vezérlőnek.
   
    ![Az Add Controller (Vezérlő hozzáadása) párbeszédpanel képernyőfelvétele](./media/documentdb-dotnet-application/image15.png)
   
    A fájl létrehozása után a Visual Studio megoldásnak a következőre kell hasonlítania az új ItemController.cs fájllal a **Megoldáskezelőben**. A korábban létrehozott új Item.cs fájl is látható.
   
    ![A Visual Studio megoldás - Megoldáskezelő képernyőfelvétele, ahol ki van emelve az új ItemController.cs és Item.cs fájl](./media/documentdb-dotnet-application/image16.png)
   
    Bezárhatja az ItemController.cs fájlt, később visszatérünk ahhoz. 

### <a name="_Toc395637766"></a>Nézetek hozzáadása
Most hozzuk létre az MVC **V** elemét, a nézeteket:

* [Elemindexnézet hozzáadása](#AddItemIndexView).
* [Új elemnézet hozzáadása](#AddNewIndexView).
* [Elemszerkesztési nézet hozzáadása](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Elemindexnézet hozzáadása
1. A **Megoldáskezelőben** bontsa ki a **Nézetek** mappát, kattintson a jobb gombbal az üres **Elem** mappára, amelyet a Visual Studio az **ItemController** korábbi hozzáadásakor hozott létre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) elemre.
   
    ![A Megoldáskezelő képernyőfelvétele, amelyen a Visual Studio által létrehozott Item mappa látható, és az Add View (Nézet hozzáadása) parancsok vannak kiemelve](./media/documentdb-dotnet-application/image17.png)
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Index*** nevet.
   * A **Template** (Sablon) mezőben válassza a ***List*** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * Hagyja üresen a **Data context class** (Adatkörnyezet osztálya) mezőt. 
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
     
     ![Az Add View (Nézet hozzáadása) párbeszédpanelt megjelenítő képernyőfelvétel](./media/documentdb-dotnet-application/image18.png)
3. Amikor ezen értékek mindegyike már be van állítva, kattintson az **Add** (Hozzáadás) gombra és várja meg, hogy a Visual Studio létrehozzon egy új sablonnézetet. Ha ezzel végzett, a rendszer megnyitja a létrehozott cshtml fájlt. Bezárhatjuk ezt a fájlt a Visual Studióban, mivel később visszatérünk hozzá.

#### <a name="AddNewIndexView"></a>Új elemnézet hozzáadása
Az **Elemindex** nézet létrehozásához hasonlóan most létrehozunk egy új nézetet új **elemek** létrehozásához.

1. A **Megoldáskezelőben** ismét kattintson a jobb gombbal az **Item** (Elem) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) gombra.
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be a ***Create*** (Létrehozás) nevet.
   * A **Template** (Sablon) mezőben válassza a ***Create*** (Létrehozás) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * Hagyja üresen a **Data context class** (Adatkörnyezet osztálya) mezőt.
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Kattintson az **Add** (Hozzáadás) parancsra.

#### <a name="_Toc395888515"></a>Elemszerkesztési nézet hozzáadása
És végül adjon hozzá egy utolsó nézetet az **elemek** szerkesztéséhez, ahogyan azt korábban is tette.

1. A **Megoldáskezelőben** ismét kattintson a jobb gombbal az **Item** (Elem) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **View** (Nézet) gombra.
2. Az **Add View** (Nézet hozzáadása) párbeszédpanelen tegye a következőket:
   
   * A **View name** (Nézet neve) mezőbe írja be az ***Edit*** (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az ***Edit*** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az ***Item (todo.Models)*** elemet.
   * Hagyja üresen a **Data context class** (Adatkörnyezet osztálya) mezőt. 
   * A layout page (elrendezéslap) mezőbe írja be a ***~/Views/Shared/_Layout.cshtml*** szöveget.
   * Kattintson az **Add** (Hozzáadás) parancsra.

Ha ezzel végzett, zárja be az összes cshtml dokumentumot a Visual Studióban, mivel később vissza fog térni ezekhez a nézetekhez.

## <a name="_Toc395637769"></a>5. lépés: A DocumentDB csatlakoztatása
Most, hogy elvégeztük az MVC-vel kapcsolatos szokásos feladatokat, adjuk hozzá a DocumentDB kódját. 

Ebben a szakaszban a következők kezeléséhez adunk hozzá kódot:

* [Hiányos elemek listázása](#_Toc395637770).
* [Elemek hozzáadása](#_Toc395637771).
* [Elemek szerkesztése](#_Toc395637772).

### <a name="_Toc395637770"></a>Hiányos elemek listázása az MVC webalkalmazásban
Itt először hozzá kell adni egy osztályt, amely tartalmazza a DocumentDB adatbázishoz való csatlakozás és a DocumentDB használatának összes logikáját. Ehhez az oktatóprogramhoz ezen logikák mindegyikét a DocumentDBRepository nevű adattárba foglaljuk. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra. Adja az új osztálynak a **DocumentDBRepository** nevet, és kattintson az **Add** (Hozzáadás) gombra.
2. Az újonnan létrehozott **DocumentDBRepository** osztályban adja a következő *használati utasításokat* a *névtér-deklaráció* fölé
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
   
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
   
   > [!TIP]
   > Új DocumentCollection létrehozásakor megadhat egy választható OfferType típusú RequestOptions paramétert, amellyel megadhatja az új gyűjtemény teljesítményszintjét. Ha ez a paraméter nem kerül át, a rendszer az alapértelmezett ajánlattípust használja. A DocumentDB ajánlattípusokról további információ: [DocumentDB teljesítményszintek](documentdb-performance-levels.md)
   > 
   > 
3. A konfigurációból adunk hozzá néhány értéket, ezért nyissa meg az alkalmazás **Web.config** fájlját, és adja hozzá a következő sorokat az `<AppSettings>` szakasz alá.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Most frissítse az *endpoint* (végpont) és az *authKey* (hitelesítési kulcs) értékeit az Azure-portál Keys (Kulcsok) panelén. A Keys (Kulcsok) panel **URI-címét** használja a végpontbeállítás értékeként, és a Keys (Kulcsok) panel **PRIMARY KEY** (ELSŐDLEGES KULCS) vagy **SECONDARY KEY** (MÁSODLAGOS KULCS) értékét használja az authKey beállítás értékeként.

    Ezzel elvégeztük a DocumentDB-adattár csatlakoztatását, most adjuk hozzá az alkalmazás logikáját.

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

![A jelen adatbázis-oktatóprogram során létrehozott teendőlista webalkalmazás képernyőfelvétele](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Elemek hozzáadása
Tegyünk néhány elemet az adatbázisba, hogy ne csak egy üres táblát lássunk.

Adjunk néhány kódot a DocumentDBRepository és az ItemController elemhez, hogy megmaradjon a rekord a DocumentDB adatbázisban.

1. Adja hozzá a **DocumentDBRepository** tárhoz a következő metódust:
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Ez a metódus egyszerűen vesz egy neki küldött objektumot, és megőrzi azt a DocumentDB adatbázisban.
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
   
    **Biztonsági megjegyzés**: A **ValidateAntiForgeryToken** attribútum itt segít megvédeni az alkalmazást a webhelyközi kérések hamisítása ellen. Az attribútum hozzáadásánál többről van szó, a nézeteknek is működniük kell ezzel a hamisítás elleni tokennel. A témáról további részletekért és a megfelelő megvalósításának példáiért lásd: [Webhelyközi kérések hamisításának megakadályozása][Webhelyközi kérések hamisításának megakadályozása]. A [GitHub][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.
   
    **Biztonsági megjegyzés**: A metódus paraméteren a **Bind** (Kötés) attribútummal is segítünk a túlküldéses támadások elleni védelemben. További részletekért lásd: [Alapvető CRUD műveletek az ASP.NET MVC-ben][].

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
   
    Ezen metódusok közül az első, a **GetItem** egy elemet kér le a DocumentDB adatbázisból, amelyet visszaküld az **ItemController** vezérlőhöz, majd az **Edit** (Szerkesztés) nézethez.
   
    A most hozzáadott metódusok közül a második lecseréli a **dokumentumot** a DocumentDB adatbázisban a **dokumentum** azon verziójával, amely az **ItemController** vezérlőből származik.
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
   
    Az első metódus a Http GET kérést kezeli, amely akkor történik meg, amikor a felhasználó az **Edit** (Szerkesztés) hivatkozásra kattint az **Index** nézetből. Ez a metódus [**dokumentumot**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) kér le a DocumentDB adatbázisból és az **Edit** (Szerkesztés) nézetbe küldi azt.
   
    Az **Edit** (Szerkesztés) nézet ezután Http POST kérést küld az **IndexController** vezérlőnek. 
   
    A második hozzáadott metódus kezeli a frissített objektum átadását a DocumentDB adatbázisnak, hogy megmaradjon az adatbázisban.

Ennyi, ez minden, amire szükségünk van az alkalmazás futtatásához, a hiányos **elemek** listázásához és új **elemek** hozzáadásához, valamint az **elemek** szerkesztéséhez.

## <a name="_Toc395637773"></a>6. lépés: Az alkalmazás helyileg történő futtatása
Az alkalmazás helyi gépen való teszteléséhez tegye a következőket:

1. Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő összeállításához. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:
   
    ![A jelen adatbázis-oktatóprogram során létrehozott teendőlista webalkalmazás képernyőfelvétele](./media/documentdb-dotnet-application/image24.png)
   
    Ha Visual Studio 2013-at használ, és a „Nem lehet várakozni a „catch” záradék törzsében.” hibaüzenetet kapja, telepítenie kell a [Microsoft.Net.Compilers NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Net.Compilers/). Emellett össze is hasonlíthatja a kódját a [GitHub][GitHub] lévő mintaprojekttel. 
2. Kattintson a **Create New** (Új létrehozása) hivatkozásra, és adjon értékeket a **Name** (Név) és a **Description** (Leírás) mezőkbe. Hagyja bejelöletlenül a **Completed** (Befejezve) jelölőnégyzetet, különben az új **elem** befejezett állapotban lesz hozzáadva és nem jelenik meg a kiindulási listában.
   
    ![Képernyőfelvétel a Create (Létrehozás) nézetről](./media/documentdb-dotnet-application/image25.png)
3. Kattintson a **Create** (Létrehozás) gombra, és a rendszer visszairányítja az **Index** nézetre, ahol az **elem** megjelenik a listában.
   
    ![Képernyőfelvétel az Index nézetről](./media/documentdb-dotnet-application/image26.png)
   
    Nyugodtan adjon hozzá még néhány **elemet** a teendőlistához.
4. Kattintson az **Edit** (Szerkesztés) gombra a lista egy **eleme** mellett, és az **Edit** (Szerkesztés) nézetbe kerül, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **Completed** (Befejezve) jelzőt. Ha bejelöli a **Complete** (Befejezve) jelzőt és a **Save** (Mentés) gombra kattint, azzal eltávolítja az **elemet** a hiányos feladatok listájából.
   
    ![Képernyőfelvétel az Index nézetről, bejelölt Completed (Befejezve) jelölőnégyzettel](./media/documentdb-dotnet-application/image27.png)
5. Ha befejezte az alkalmazás tesztelését, nyomja meg a Ctrl+F5 billentyűkombinációt az alkalmazás hibakeresésének befejezéséhez. Készen áll a telepítésre!

## <a name="_Toc395637774"></a>7. lépés: Az alkalmazás üzembe helyezése az Azure Websites-ban
Most, hogy a teljes alkalmazás megfelelően működik a DocumentDB adatbázissal, Azure-webhelyekre fogjuk telepíteni ezt a webalkalmazást. Ha bejelölte a **Host in the cloud** (Üzemeltetés a felhőben) lehetőséget az üres ASP.NET MVC projekt létrehozásakor, akkor a Visual Studio jelentősen megkönnyíti ezt, és a legtöbb munkát elvégzi. 

1. Az alkalmazás közzétételéhez egyszerűen a jobb gombbal a projektre kell kattintania a **Megoldáskezelőben**, majd a **Publish** (Közzététel) parancsot választania.
   
    ![Képernyőfelvétel a Közzététel lehetőségről a Megoldáskezelőben](./media/documentdb-dotnet-application/image28.png)
2. Már mindennek konfigurálva kell lennie a hitelesítő adatai szerint; valójában a webhely már létrejött az Azure rendszerben az itt látható **Destination URL** (Cél URL) helyen, csak a **Publish** (Közzététel) gombra kell kattintania.
   
    ![A Visual Studio Publish Web (Webes közzététel) párbeszédpanelének képernyőfelvétele – ASP NET MVC oktatóprogram lépésről lépésre](./media/documentdb-dotnet-application/image29.png)

Néhány másodpercen belül a Visual Studio befejezi a webalkalmazás közzétételét, és elindít egy böngészőt, ahol láthatja az Azure rendszeren futó munkáját!

## <a name="_Toc395637775"></a>Következő lépések
Gratulálunk! Megépítette az első ASP.NET MVC webalkalmazását az Azure DocumentDB eszközzel és közzétette azt Azure-webhelyekre. A teljes alkalmazás forráskódja, beleértve az oktatóprogramban nem szereplő részletezési és törlési funkciót, letölthető vagy klónozható a [GitHub][GitHub]. Így ha továbbra is érdekli ezen funkcióknak az alkalmazáshoz adása, a kóddal ezt megteheti.

Ha további funkciókat szeretne az alkalmazáshoz adni, tekintse át a [DocumentDB .NET kódtárban](https://msdn.microsoft.com/library/azure/dn948556.aspx) lévő API-kat, és nyugodtan járuljon hozzá a DocumentDB .NET kódtárhoz a [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Webplatform-telepítő]: http://www.microsoft.com/web/downloads/platform.aspx
[Webhelyközi kérések hamisításának megakadályozása]: http://go.microsoft.com/fwlink/?LinkID=517254
[Alapszintű CRUD műveletek az ASP.NET MVC-ben]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app



<!--HONumber=Oct16_HO3-->


