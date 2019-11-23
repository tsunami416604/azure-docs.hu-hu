---
title: A .NET backend Server SDK használata a Mobile Appshoz | Microsoft Docs
description: Megtudhatja, hogyan dolgozhat a Azure App Service Mobile Apps .NET-háttérbeli kiszolgálói SDK-val.
keywords: App Service, az Azure app Service, a Mobile App, a Mobile Service, a Scale, a skálázható, az alkalmazások telepítése, az Azure-alkalmazások üzembe helyezése
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5372385bd4de0f5592a7fff3e4a78fbb5e1648dc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388781"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Az Azure Mobile Appshoz készült .NET háttérkiszolgáló-SDK használata
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Ebből a témakörből megtudhatja, hogyan használhatja a .NET backend Server SDK-t a Azure App Service Mobile Apps forgatókönyvekben. Az Azure Mobile Apps SDK segítségével a ASP.NET-alkalmazásból származó mobil ügyfelekkel dolgozhat.

> [!TIP]
> Az Azure-hoz készült [.NET Server SDK Mobile apps][2] nyílt forráskódú a githubon. A tárház tartalmazza az összes forráskódot, beleértve a teljes Server SDK Unit test Suite és néhány minta projektet is.
>
>

## <a name="reference-documentation"></a>Segédanyagok
A Server SDK dokumentációja itt található: [Azure Mobile apps .net Reference][1].

## <a name="create-app"></a>Útmutató: .NET-alapú mobil alkalmazások háttérbeli létrehozása
Új projekt indításához létrehozhat egy App Service alkalmazást a [Azure Portal] vagy a Visual Studio használatával. A App Service alkalmazást helyileg is futtathatja, vagy közzéteheti a projektet a felhőalapú App Service Mobile-alkalmazásban.

Ha meglévő projekthez ad hozzá mobil képességeket, tekintse meg az [SDK letöltése és inicializálása](#install-sdk) című szakaszt.

### <a name="create-a-net-backend-using-the-azure-portal"></a>.NET-háttérrendszer létrehozása a Azure Portal használatával
App Service Mobile háttérrendszer létrehozásához kövesse a gyors [útmutató oktatóanyagot][3] , vagy kövesse az alábbi lépéseket:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vissza az *első lépések* panelen, a **Table API létrehozása**területen válassza a **C#** **háttér nyelv**lehetőséget. Kattintson a **Letöltés**gombra, bontsa ki a tömörített projektfájlok a helyi számítógépre, és nyissa meg a megoldást a Visual Studióban.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>.NET-háttérrendszer létrehozása a Visual Studio 2017 használatával

Telepítse az Azure-beli számítási feladatot a Visual Studio telepítőjének használatával, és tegye közzé az Azure Mobile Apps-projektet a Visual studióból. Miután telepítette az SDK-t, hozzon létre egy ASP.NET-alkalmazást a következő lépések végrehajtásával:

1. Nyissa meg az **új projekt** párbeszédpanelt ( **fájlból** > **új** > **projektből...** ).
2. Bontsa ki a **vizualizáció C#**  elemet, és válassza a **web**lehetőséget.
3. Válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet.
4. Adja meg a projekt nevét. Ezután kattintson az **OK** gombra.
5. Válassza ki az **Azure Mobile App** elemet a sablonok listájában.
6. A megoldás létrehozásához kattintson **az OK** gombra.
7. Kattintson a jobb gombbal a projektre a **megoldáskezelő** , majd válassza a **Közzététel...** lehetőséget, majd válassza ki a **app Service** közzétételi célként.
8. Kövesse az utasításokat a hitelesítéshez, és válasszon ki egy új vagy meglévő Azure App Service a közzétételhez.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>.NET-háttérrendszer létrehozása a Visual Studio 2015 használatával

Telepítse a [.net-hez készült Azure SDK][4] -t (2.9.0 vagy újabb verzió) egy Azure Mobile apps-projekt létrehozásához a Visual Studióban. Miután telepítette az SDK-t, hozzon létre egy ASP.NET-alkalmazást a következő lépések végrehajtásával:

1. Nyissa meg az **új projekt** párbeszédpanelt ( **fájlból** > **új** > **projektből...** ).
2. Bontsa ki a **sablonok** > a **vizualizáció C#** elemet, és válassza a **web**lehetőséget.
3. Válassza az **ASP.NET Web Application** (ASP.NET-es webalkalmazás) lehetőséget.
4. Adja meg a projekt nevét. Ezután kattintson az **OK** gombra.
5. Az *ASP.net 4.5.2-sablonok*területen válassza az **Azure Mobile App**elemet. A **felhőben** keresse meg a gazdagépet, és hozzon létre egy mobil hátteret a felhőben, amelyen közzéteheti a projektet.
6. Kattintson az **OK** gombra.

## <a name="install-sdk"></a>Útmutató: az SDK letöltése és inicializálása
Az SDK a [NuGet.org]címen érhető el. Ez a csomag tartalmazza az SDK használatának megkezdéséhez szükséges alapszintű funkciókat. Az SDK inicializálásához műveleteket kell végrehajtania a **HttpConfiguration** objektumon.

### <a name="install-the-sdk"></a>Az SDK telepítése
Az SDK telepítéséhez kattintson a jobb gombbal a kiszolgálói projektre a Visual Studióban, válassza a **NuGet-csomagok kezelése**lehetőséget, keresse meg a [Microsoft.Azure.Mobile.Server] csomagot, majd kattintson a **telepítés**gombra.

### <a name="server-project-setup"></a>A kiszolgálói projekt inicializálása
A .NET háttér-kiszolgálói projekt a többi ASP.NET-projekthez hasonlóan inicializálva van, a OWIN indítási osztályával együtt. Győződjön meg arról, hogy a NuGet-csomagra hivatkozik `Microsoft.Owin.Host.SystemWeb`. Ha ezt az osztályt a Visual Studióban szeretné felvenni, kattintson a jobb gombbal a kiszolgálói projektre, és válassza a **hozzáadás** >
**új elem**, majd a **webes** > **általános** > **OWIN indítási osztály**elemet.  Egy osztály jön létre a következő attribútummal:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

A OWIN indítási osztályának `Configuration()` metódusában használjon egy **HttpConfiguration** objektumot az Azure Mobile apps-környezet konfigurálásához.
A következő példa a kiszolgálói projektet a hozzáadott funkciók nélkül inicializálja:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Az egyes funkciók engedélyezéséhez a **ApplyTo**meghívása előtt meg kell hívnia a bővítmények metódusait a **MobileAppConfiguration** objektumon. A következő kód például hozzáadja az alapértelmezett útvonalakat az összes olyan API-vezérlőhöz, amelynek az attribútuma `[MobileAppController]` az inicializálás során:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

A kiszolgáló rövid útmutatója a Azure Portal meghívja a **UseDefaultConfiguration ()** metódust. Ez egyenértékű a következő beállítással:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

A használt bővítményi módszerek a következők:

* `AddMobileAppHomeController()` az alapértelmezett Azure Mobile Apps kezdőlapot biztosítja.
* `MapApiControllers()` egyéni API-képességeket biztosít a `[MobileAppController]` attribútummal díszített WebAPI-vezérlőkhöz.
* a `AddTables()` a `/tables` végpontok táblázatos vezérlőkhöz való leképezését teszi lehetővé.
* a `AddTablesWithEntityFramework()` a `/tables` végpontok Entity Framework alapú vezérlőkkel való leképezése.
* a `AddPushNotifications()` egyszerű módszert kínál az eszközök Notification Hubshoz való regisztrálására.
* `MapLegacyCrossDomainController()` a helyi fejlesztéshez szabványos CORS-fejléceket biztosít.

### <a name="sdk-extensions"></a>SDK-bővítmények
A következő NuGet-alapú kiterjesztési csomagok különböző mobil funkciókat biztosítanak, amelyeket az alkalmazás használhat. A bővítményeket az inicializálás során a **MobileAppConfiguration** objektum használatával engedélyezheti.

* A [Microsoft.Azure.Mobile.Server.Quickstart] támogatja az alapszintű Mobile apps telepítőt. A konfigurációhoz való hozzáadásához hívja meg a **UseDefaultConfiguration** bővítmény metódust az inicializálás során. Ez a bővítmény a következő bővítményeket tartalmazza: értesítések, hitelesítés, entitás, táblák, tartományok közötti és otthoni csomagok. Ezt a csomagot a Azure Portal elérhető Mobile Apps rövid útmutatója használja.
* A [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementálja az alapértelmezett *Ez a Mobile-alkalmazás* a webhely gyökeréhez. Adja hozzá a konfigurációhoz a **AddMobileAppHomeController** -bővítmény metódus meghívásával.
* A [Microsoft. Azure. Mobile. Server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) az adatkezelési osztályokat és az adatfolyamatok beállítását is tartalmazza. Adja hozzá a konfigurációhoz a **AddTables** -bővítmény metódus meghívásával.
* A [Microsoft. Azure. Mobile. Server. Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) lehetővé teszi, hogy a Entity Framework hozzáférjenek a SQL Database lévő adatszolgáltatásokhoz. Adja hozzá a konfigurációhoz a **AddTablesWithEntityFramework** -bővítmény metódus meghívásával.
* A [Microsoft.Azure.Mobile.Server.Authentication] engedélyezi a hitelesítést és beállítja a jogkivonatok érvényesítéséhez használt OWIN middleware-t. Adja hozzá a konfigurációhoz a **AddAppServiceAuthentication** és a **IAppBuilder**meghívásával. **UseAppServiceAuthentication** -bővítmény módszerei.
* A [Microsoft.Azure.Mobile.Server.Notifications] lehetővé teszi a leküldéses értesítések küldését, és leküldéses regisztrációs végpontot definiál. Adja hozzá a konfigurációhoz a **AddPushNotifications** -bővítmény metódus meghívásával.
* A [Microsoft. Azure. Mobile. Server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) egy vezérlőt hoz létre, amely az adatoknak a mobil alkalmazásból származó, régi webböngészőkbe való kiszolgálása érdekében. Adja hozzá a konfigurációhoz a **MapLegacyCrossDomainController** -bővítmény metódus meghívásával.
* A [Microsoft.Azure.Mobile.Server.Login] a AppServiceLoginHandler. okta () metódust biztosítja, amely az egyéni hitelesítési forgatókönyvek során használt statikus metódus.

## <a name="publish-server-project"></a>Útmutató: a kiszolgálói projekt közzététele
Ez a szakasz bemutatja, hogyan teheti közzé a .NET-háttér-projektet a Visual studióból. A háttér-projektet a [git](../app-service/deploy-local-git.md) használatával vagy az ott elérhető egyéb módszerekkel is üzembe helyezheti.

1. A Visual Studióban építse újra a projektet a NuGet-csomagok visszaállításához.
2. Megoldáskezelő kattintson a jobb gombbal a projektre, majd a **Közzététel**elemre. Amikor először tesz közzé, meg kell adnia egy közzétételi profilt. Ha már van definiálva profil, kiválaszthatja, majd kattintson a **Közzététel**elemre.
3. Ha a rendszer a közzétételi cél kiválasztására kéri, kattintson **Microsoft Azure App Service** > **tovább**gombra, majd (ha szükséges) az Azure-beli hitelesítő adataival jelentkezzen be.
   A Visual Studio letölti és biztonságosan tárolja a közzétételi beállításokat közvetlenül az Azure-ból.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Válassza ki az **előfizetést**, válassza ki az **erőforrás típusát** a **nézetből**, bontsa ki a **Mobile App**elemet, majd kattintson a Mobile apps-háttérre, majd **az OK**gombra

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ellenőrizze a profil közzétételének adatait, majd kattintson a **Közzététel**elemre.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Ha a Mobile apps-háttér sikeresen közzé lett téve, megjelenik egy, a sikert jelző Kezdőlap.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Útmutató: tábla-vezérlő definiálása
Definiáljon egy tábla-vezérlőt, amely egy SQL-táblázatot tesz elérhetővé a mobil ügyfelek számára.  A táblázatos vezérlő konfigurálásához három lépés szükséges:

1. Hozzon létre egy Adatátvitel Object (DTO) osztályt.
2. Konfigurálja a táblázat hivatkozását a Mobile DbContext osztályban.
3. Hozzon létre egy tábla-vezérlőt.

A Adatátvitel objektum (DTO) egy egyszerű C# objektum, amely a `EntityData`tól örököl.  Például:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

A DTO az SQL-adatbázison belüli tábla definiálására szolgál.  Az adatbázis-bejegyzés létrehozásához vegyen fel egy `DbSet<>` tulajdonságot a használni kívánt DbContext.  Az Azure Mobile Apps alapértelmezett Project-sablonjában a DbContext neve `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Ha telepítette az Azure SDK-t, mostantól a következőképpen hozhat létre egy sablon tábla-vezérlőt:

1. Kattintson a jobb gombbal a vezérlők mappára, és válassza a > vezérlő **hozzáadása** **..** . lehetőséget.
2. Válassza ki az **Azure Mobile apps tábla-vezérlő** lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. A **vezérlő hozzáadása** párbeszédpanelen:
   * A **modell osztály** legördülő menüben válassza ki az új DTO.
   * A **DbContext** legördülő menüben válassza ki a Mobile Service DbContext osztályt.
   * A rendszer létrehozza a vezérlő nevét.
4. Kattintson az **Hozzáadás** parancsra.

A gyors üzembe helyezési kiszolgáló projekt egy példát tartalmaz egy egyszerű **TodoItemController**.

### <a name="adjust-pagesize"></a>Útmutató: a tábla lapozófájl-méretének módosítása
Alapértelmezés szerint az Azure Mobile Apps kérelemként 50 rekordot ad vissza.  A lapozás biztosítja, hogy az ügyfél ne kösse össze a felhasználói felületi szálat és a kiszolgálót túl hosszú ideig, így biztosítva a megfelelő felhasználói élményt. A tábla lapozófájljának méretének módosításához növelje a kiszolgálóoldali "megengedett lekérdezési méret" és az ügyféloldali oldal méretét, a "megengedett lekérdezési méret" beállítás a `EnableQuery` attribútummal van beállítva:

    [EnableQuery(PageSize = 500)]

Győződjön meg arról, hogy a PageSize megegyezik az ügyfél által kért mérettel, vagy annál nagyobb.  Az ügyfél oldal méretének módosításával kapcsolatban tekintse meg az adott ügyfél-HOWTO dokumentációját.

## <a name="how-to-define-a-custom-api-controller"></a>Útmutató: egyéni API-vezérlő definiálása
Az egyéni API-vezérlő a legalapvetőbb funkciókat biztosítja a Mobile apps-háttér számára a végpontok kimutatása révén. A [MobileAppController] attribútum használatával regisztrálhat egy Mobile-specifikus API-vezérlőt. A `MobileAppController` attribútum regisztrálja az útvonalat, beállítja a Mobile apps JSON-szerializáló, és bekapcsolja az [ügyfél verziójának ellenőrzését](app-service-mobile-client-and-server-versioning.md).

1. A Visual Studióban kattintson a jobb gombbal a vezérlők mappára, majd kattintson a > **vezérlő** **hozzáadása** lehetőségre, válassza a **webes API 2 vezérlő&mdash;üres** lehetőséget, majd kattintson a **Hozzáadás**gombra.
2. Adja meg a **vezérlő nevét**, például `CustomController`, majd kattintson a **Hozzáadás**gombra.
3. Az új vezérlő osztály fájljában adja hozzá a következő using utasítást:

        using Microsoft.Azure.Mobile.Server.Config;
4. Alkalmazza a **[MobileAppController]** attribútumot az API-vezérlő osztályának meghatározására, az alábbi példában látható módon:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup.MobileApp.cs-fájlban vegyen fel egy hívást a **MapApiControllers** Extension metódusba, ahogy az alábbi példában is látható:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

`MapApiControllers()`helyett a `UseDefaultConfiguration()` Extension metódust is használhatja. Az ügyfelek továbbra is hozzáférhetnek a **MobileAppControllerAttribute** -t nem használó vezérlőkhöz, de előfordulhat, hogy az ügyfelek nem tudják megfelelően használni az összes Mobile App Client SDK-t.

## <a name="how-to-work-with-authentication"></a>Útmutató: a hitelesítés használata
Az Azure Mobile Apps App Service hitelesítés/engedélyezés használatával gondoskodik a mobil háttérrendszer védelméről.  Ez a szakasz bemutatja, hogyan végezheti el a következő hitelesítéssel kapcsolatos feladatokat a .NET háttér-kiszolgálói projektben:

* [Útmutató: hitelesítés hozzáadása egy kiszolgálói projekthez](#add-auth)
* [Útmutató: egyéni hitelesítés használata az alkalmazáshoz](#custom-auth)
* [Útmutató: a hitelesített felhasználói adatok beolvasása](#user-info)
* [Útmutató: az adathozzáférés korlátozása a jogosult felhasználók számára](#authorize)

### <a name="add-auth"></a>Útmutató: hitelesítés hozzáadása egy kiszolgálói projekthez
A kiszolgálói projekthez a **MobileAppConfiguration** objektum kibővítésével és a OWIN middleware konfigurálásával adhat hozzá hitelesítést. A [Microsoft.Azure.Mobile.Server.Quickstart] gyorsindítási csomag telepítésekor és a **UseDefaultConfiguration** -bővítmény metódusának meghívásakor ugorjon a 3. lépésre.

1. A Visual Studióban telepítse a [Microsoft.Azure.Mobile.Server.Authentication] csomagot.
2. A Startup.cs projekt fájljában adja hozzá a következő kódrészletet a **konfigurációs** módszer elején:

        app.UseAppServiceAuthentication(config);

    Ez a OWIN köztes összetevő ellenőrzi a társított App Service átjáró által kiállított jogkivonatokat.
3. Adja hozzá a `[Authorize]` attribútumot bármely olyan vezérlőhöz vagy metódushoz, amelyhez hitelesítés szükséges.

Ha szeretné megtudni, hogyan hitelesítheti az ügyfeleket a Mobile Apps háttérrel, tekintse meg [a hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-users.md)című témakört.

### <a name="custom-auth"></a>Útmutató: egyéni hitelesítés használata az alkalmazáshoz
> [!IMPORTANT]
> Az egyéni hitelesítés engedélyezéséhez először engedélyeznie kell App Service hitelesítést anélkül, hogy kijelöli a App Servicehoz tartozó szolgáltatót a Azure Portal. Ez engedélyezi a WEBSITE_AUTH_SIGNING_KEY környezeti változót a gazdagépen.
> 
> 
> Ha nem kívánja használni a App Service hitelesítés/engedélyezés szolgáltatók egyikét, saját bejelentkezési rendszerét is megvalósíthatja. Telepítse a [Microsoft.Azure.Mobile.Server.Login] csomagot, hogy segítséget nyújtson a hitelesítési jogkivonat létrehozásához.  Adja meg saját kódját a felhasználói hitelesítő adatok érvényesítéséhez. Előfordulhat például, hogy egy adatbázisban megtekinti a sózott és a kivonatos jelszavakat. Az alábbi példában a `isValidAssertion()` metódus (máshol definiálva) felelős az ellenőrzésekért.

Az egyéni hitelesítés ApiController létrehozásával és a `register` és `login` műveletek elvégzésével van kitéve. Az ügyfélnek egyéni felhasználói felületet kell használnia az adatoknak a felhasználótól való összegyűjtéséhez.  Ezt követően a rendszer a szabványos HTTP POST hívással küldi el az adatokat az API-nak. Ha a kiszolgáló érvényesíti az állítást, a rendszer egy jogkivonatot állít ki a `AppServiceLoginHandler.CreateToken()` metódus használatával.  A ApiController **nem szabad** a `[MobileAppController]` attribútumot használni.

Példa `login` műveletre:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

Az előző példában a LoginResult és a LoginResultUser szerializálható objektumok, amelyek a szükséges tulajdonságokat teszik közzé. Az ügyfél az űrlap JSON-objektumaiként visszaadott bejelentkezési válaszokat vár:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

A `AppServiceLoginHandler.CreateToken()` metódus egy *célközönséget* és egy *kiállítói* paramétert tartalmaz. Mindkét paraméter az alkalmazás gyökérkönyvtárának URL-címére van beállítva a HTTPS-séma használatával. Hasonlóképpen be kell állítania az *secretKey* értékét az alkalmazás aláíró kulcsának értékeként. Ne terjesszen az aláíró kulcsot az ügyfélen, mivel a kulcsok megverésére és a felhasználók megszemélyesítésére használható. Az aláíró kulcsot a App Service futtatva, a *webhelyre hivatkozva\_hitelesítési\_aláírás\_kulcs* környezeti változó. Ha helyi hibakeresési környezetben van szükség, kövesse a [helyi hibakeresés a hitelesítéssel](#local-debug) című szakaszt a kulcs lekéréséhez és az alkalmazás-beállításként való tárolásához.

A kiállított jogkivonat tartalmazhat más jogcímeket és lejárati dátumot is.  A kiállított jogkivonatnak minimálisan tartalmaznia kell egy tárgy (**Sub**) jogcímet.

A hitelesítési útvonal túlterhelésével támogathatja a szabványos ügyfél-`loginAsync()` módszert.  Ha az ügyfél meghívja a `client.loginAsync('custom');`t a bejelentkezéshez, az útvonalnak `/.auth/login/custom`nak kell lennie.  Az egyéni hitelesítési vezérlő útvonalát a `MapHttpRoute()`használatával adhatja meg:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> A `loginAsync()` módszer használatával biztosíthatja, hogy a hitelesítési jogkivonat a szolgáltatás minden további hívásához csatolva legyen.
>
>

### <a name="user-info"></a>Útmutató: a hitelesített felhasználói adatok beolvasása
Ha App Service hitelesíti a felhasználót, hozzáférhet a hozzárendelt felhasználói AZONOSÍTÓhoz és egyéb információkhoz a .NET-háttér kódjában. A felhasználói adatok a háttérbeli engedélyezési döntések meghozatalához is használhatók. A következő kód beolvassa a kérelemhez társított felhasználói azonosítót:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A SID a szolgáltató-specifikus felhasználói AZONOSÍTÓból származik, és az adott felhasználó és bejelentkezési szolgáltató számára statikus.  A SID null értékű az érvénytelen hitelesítési tokenek esetében.

App Service is lehetővé teszi, hogy konkrét jogcímeket Kérjen a bejelentkezési szolgáltatójától. Az egyes identitás-szolgáltatók több információt is biztosíthatnak az Identity Provider SDK használatával.  Használhatja például a barátokra vonatkozó Facebook-Graph API.  Megadhat olyan jogcímeket, amelyeket a rendszer a Azure Portal szolgáltató paneljén kér. Egyes jogcímek esetében további konfigurációra van szükség az identitás-szolgáltatónál.

A következő kód meghívja a **GetAppServiceIdentityAsync** -bővítmény metódust a bejelentkezési hitelesítő adatok beszerzéséhez, amely tartalmazza a Facebook Graph APIhoz való kérések elvégzéséhez szükséges hozzáférési jogkivonatot:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Vegyen fel egy using utasítást a `System.Security.Principal` számára a **GetAppServiceIdentityAsync** -bővítmény módszerének megadásához.

### <a name="authorize"></a>Útmutató: az adathozzáférés korlátozása a jogosult felhasználók számára
Az előző szakaszban bemutatjuk, hogyan lehet beolvasni egy hitelesített felhasználó felhasználói AZONOSÍTÓját. Ezen érték alapján korlátozhatja az adatelérést és az egyéb erőforrásokat. Ha például egy userId oszlopot ad hozzá a táblákhoz, és a lekérdezési eredményeket a felhasználói azonosító alapján szűri, a visszaadott adatmennyiséget csak a jogosultsággal rendelkező felhasználókra korlátozhatja. A következő kód csak akkor adja vissza az adatsorokat, ha a SID megegyezik a TodoItem tábla UserId oszlopának értékével:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

A `Query()` metódus egy olyan `IQueryable` ad vissza, amelyet a LINQ a szűrés kezeléséhez kezelhet.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Útmutató: leküldéses értesítések hozzáadása egy kiszolgálói projekthez
Leküldéses értesítések hozzáadása a kiszolgálói projekthez a **MobileAppConfiguration** objektum kibővítésével és egy Notification Hubs-ügyfél létrehozásával.

1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektre, majd kattintson a **NuGet-csomagok kezelése**elemre, keresse meg `Microsoft.Azure.Mobile.Server.Notifications`, majd kattintson a **telepítés**gombra.
2. Ismételje meg ezt a lépést a `Microsoft.Azure.NotificationHubs` csomag telepítéséhez, amely tartalmazza a Notification Hubs ügyféloldali függvénytárat.
3. App_Start/Startup.MobileApp.cs, és adjon hozzá egy hívást a **AddPushNotifications ()** Extension metódushoz az inicializálás során:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adja hozzá a következő kódot, amely létrehoz egy Notification Hubs ügyfelet:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Mostantól a Notification Hubs ügyfél használatával leküldéses értesítéseket küldhet a regisztrált eszközökre. További információ: [leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-push.md). További információ a Notification Hubsről: [Notification Hubs Overview (áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md)).

## <a name="tags"></a>Útmutató: a célként megadott leküldés engedélyezése címkék használatával
Notification Hubs lehetővé teszi a célzott értesítések küldését adott regisztrációra címkék használatával. Több címke is automatikusan létrejön:

* A telepítési azonosító egy adott eszközt azonosít.
* A hitelesített SID alapján megadott felhasználói azonosító egy adott felhasználót azonosít.

A telepítési azonosító a **MobileServiceClient** **telepítésazonosító** tulajdonságának használatával érhető el.  Az alábbi példa azt szemlélteti, hogyan használható a telepítési azonosító egy címke adott telepítéshez való hozzáadásához Notification Hubsban:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

A háttérben a leküldéses értesítési regisztráció során az ügyfél által megadott címkéket a rendszer a telepítés létrehozásakor figyelmen kívül hagyja. Annak engedélyezéséhez, hogy az ügyfél címkéket adjon a telepítéshez, létre kell hoznia egy egyéni API-t, amely az előző minta alapján címkéket ad hozzá.

Példaként tekintse meg az [ügyfél által hozzáadott leküldéses értesítési címkéket][5] a app Service Mobile apps befejezett rövid útmutatóban.

## <a name="push-user"></a>Útmutató: leküldéses értesítések küldése hitelesített felhasználóknak
Ha egy hitelesített felhasználó regisztrálja a leküldéses értesítéseket, a rendszer automatikusan hozzáadja a felhasználói azonosító címkét a regisztrációhoz. A címke használatával leküldéses értesítéseket küldhet az adott személy által regisztrált összes eszközre. A következő kód lekéri a kérést küldő felhasználó biztonsági azonosítóját, és leküldéses értesítést küld minden eszköz regisztrálására az adott személy számára:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ha egy hitelesített ügyfélről próbál leküldéses értesítéseket regisztrálni, győződjön meg arról, hogy a hitelesítés befejeződött a regisztráció megkísérlése előtt. További információ: [leküldés a felhasználóknak][6] a app Service Mobile apps befejezett gyors üzembe helyezési minta a .net-háttérrendszer számára.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Útmutató: a .NET Server SDK hibakeresése és hibaelhárítása
A Azure App Service számos hibakeresési és hibaelhárítási eljárást biztosít a ASP.NET-alkalmazásokhoz:

* [Azure App Service figyelése](../app-service/web-sites-monitor.md)
* [Diagnosztikai naplózás engedélyezése Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Azure App Service hibakeresése a Visual Studióban](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Naplózás
A diagnosztikai naplókat a szabványos ASP.NET-nyomkövetési írás használatával írhatja App Service. A naplókba való íráshoz engedélyeznie kell a diagnosztika alkalmazást a Mobile apps-háttérben.

A diagnosztika engedélyezéséhez és a naplókba való íráshoz:

1. Kövesse az [alkalmazások naplózásának engedélyezése (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)című témakör lépéseit.
2. Adja hozzá a következő using utasítást a Code fájlban:

        using System.Web.Http.Tracing;
3. Hozzon létre egy nyomkövetési írót a .NET-háttérből a diagnosztikai naplókba való íráshoz, a következőképpen:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Tegye közzé újra a kiszolgálói projektet, és nyissa meg a Mobile apps-hátteret, és futtassa a kód elérési útját a naplózással.
5. Töltse le és értékelje ki a naplókat a [hozzáférési naplófájlok](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)című témakörben leírtak szerint.

### <a name="local-debug"></a>Helyi hibakeresés hitelesítéssel
Az alkalmazást helyileg is futtathatja a módosítások teszteléséhez, mielőtt közzéteszi őket a felhőben. A legtöbb Azure Mobile Apps-háttér esetében nyomja le az *F5* billentyűt a Visual Studióban. A hitelesítés használatakor azonban további szempontokat is figyelembe kell venni.

App Service hitelesítéssel/hitelesítéssel konfigurált felhőalapú alkalmazásnak kell lennie, és az ügyfélnek rendelkeznie kell a másodlagos bejelentkezési gazdagépként megadott Felhőbeli végponttal. A szükséges lépésekért tekintse meg az ügyfél platformjának dokumentációját.

Győződjön meg arról, hogy a mobil backend a [Microsoft.Azure.Mobile.Server.Authentication] telepítve van. Ezután az alkalmazás OWIN-indítási osztályában adja hozzá a következőt, miután alkalmazta `MobileAppConfiguration` a `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Az előző példában a web. config fájl *authAudience* és *authIssuer* kell konfigurálnia az alkalmazás gyökérkönyvtárának URL-címére a https-séma használatával. Hasonlóképpen be kell állítania az *authSigningKey* értékét az alkalmazás aláíró kulcsának értékeként.
Az aláíró kulcs beszerzése:

1. Navigáljon az alkalmazáshoz a [Azure Portal] belül
2. Kattintson az **eszközök**, **kudu**, **tovább**elemre.
3. A kudu-kezelés helyen kattintson a **környezet**elemre.
4. Itt megtalálhatja a *webhely\_AUTH\_aláírás\_kulcs*értékét.

Használja az *authSigningKey* paraméterhez tartozó aláíró kulcsot a helyi alkalmazás konfigurációjában.  A mobil háttérrendszer most már készen áll a jogkivonatok érvényesítésére a helyi futtatásakor, amelyet az ügyfél a felhőalapú végponttól szerez be a tokenhez.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
