---
title: "A Mobile Apps használata a .NET-háttérrendszer server SDK |} Microsoft Docs"
description: "Megtudhatja, hogyan használható a .NET-háttérrendszer server SDK az Azure App Service Mobile Apps a."
keywords: "az App service, a azure app service, a mobilalkalmazás, a mobilszolgáltatást, a méretezési, méretezhető, központi telepítését, az azure app alkalmazástelepítés"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a1a29d87864bff8cb2ecda70d8a0a7833c70d481
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Az Azure Mobile Appshoz készült .NET háttérkiszolgáló-SDK használata
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a témakör bemutatja, hogyan használhatja a .NET háttérkiszolgáló SDK az Azure App Service Mobile Apps főbb forgatókönyvek. Az Azure Mobile Apps SDK segítséget nyújt az ASP.NET-alkalmazás a mobil ügyfelek dolgozni.

> [!TIP]
> A [.NET server az Azure Mobile Apps SDK] [ 2] nyílt forráskódú a Githubon. A tárház tartalmazza az összes többek között a teljes kiszolgáló SDK egység tesztcsomag és néhány mintaprojektjeit.
>
>

## <a name="reference-documentation"></a>Segédanyagok
A kiszolgáló SDK dokumentációját a következő helyen található: [Azure Mobile Apps .NET hivatkozás][1].

## <a name="create-app"></a>Hogyan: .NET Mobile Apps-háttéralkalmazás létrehozása
Ha a számítógépet egy új projektet, létrehozhat egy App Service alkalmazás segítségével a [Azure-portálon] vagy a Visual Studio. Az App Service alkalmazás helyileg történő futtatása, vagy a projekt közzététele a felhőalapú App Service mobilalkalmazáshoz.

Ha egy meglévő projektjébe hozzáadni mobil funkciókkal, tekintse meg a [töltse le és inicializálja az SDK-](#install-sdk) szakasz.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Az Azure portál használatával a .NET-háttéralkalmazás létrehozása
Az App Service mobil-háttéralkalmazás létrehozása, vagy hajtsa végre a [gyors üzembe helyezési útmutató] [ 3] vagy kövesse az alábbi lépéseket:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vissza a *Ismerkedés* panelen, a **tábla API létrehozása**, válassza a **C#** , a **háttéralkalmazás-nyelv**. Kattintson a **letöltése**, bontsa ki a tömörített projektfájlokat a helyi számítógépen, és nyissa meg a megoldást a Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 és a Visual Studio 2015-öt használó .NET-háttéralkalmazás létrehozása
Telepítse a [Azure SDK for .NET] [ 4] (2.9.0 verzió vagy újabb) az Azure Mobile Apps-projekt létrehozása a Visual Studióban. Az SDK telepítése után az alábbi lépéseket követve ASP.NET-alkalmazás létrehozása:

1. Nyissa meg a **új projekt** párbeszédpanelen (a *fájl* > **új** > **projekt …** ).
2. Bontsa ki a **sablonok** > **Visual C#**, és válassza ki **webes**.
3. Válassza ki **ASP.NET webalkalmazás**.
4. Töltse ki a projekt nevét. Ezután kattintson az **OK** gombra.
5. A *ASP.NET 4.5.2 sablont*, jelölje be **Azure Mobile Apps**. Ellenőrizze **a felhőben lévő gazdagéphez** a mobil-háttéralkalmazás létrehozása, amelyre a projekt közzéteheti a felhőben.
6. Kattintson az **OK** gombra.

## <a name="install-sdk"></a>Hogyan: Töltse le és inicializálja az SDK
Az SDK nem érhető el a [NuGet.org]. Ez a csomag tartalmazza az SDK használatának megkezdéséhez szükséges alapvető funkciót. Inicializálja az SDK, elvégezheti a kapcsolódó műveleteket kell a **HttpConfiguration** objektum.

### <a name="install-the-sdk"></a>Az SDK telepítése
Az SDK telepítéséhez kattintson a jobb gombbal a kiszolgáló projektre a Visual Studio, válassza ki a **NuGet-csomagok kezelése**, keresse meg a [Microsoft.Azure.Mobile.Server] csomagot, majd kattintson az **telepítése** .

### <a name="server-project-setup"></a>A projekt inicializálása
Egy .NET-háttérrendszer kiszolgálóprojektet inicializálva van más ASP.NET projektek hasonló OWIN indítási osztály-ot. Győződjön meg arról, hogy rendelkezik-e hivatkozott a NuGet-csomag `Microsoft.Owin.Host.SystemWeb`. Ez az osztály a Visual Studio hozzáadásához kattintson a jobb gombbal a kiszolgáló projektre, és válassza ki **Hozzáadás** >
**új elem**, majd **webes**  >  ** Általános** > **OWIN indítási osztály**.  Egy osztály hoz létre a következő attribútumot:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Az a `Configuration()` az OWIN indítási osztályt, használja a metódus egy **HttpConfiguration** objektum az Azure Mobile Apps-környezet konfigurálása.
Az alábbi példa inicializálja a projekt nem hozzáadott szolgáltatásokkal:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Egyes funkciók engedélyezéséhez meg kell hívnia kiterjesztésmetódusok a **MobileAppConfiguration** hívása előtt objektum **ApplyTo**. Például a következő kódot hozzáadja az alapértelmezett útvonalakat a attribútummal rendelkező összes API-vezérlő `[MobileAppController]` inicializálása közben:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

A kiszolgáló gyors üzembe helyezés az Azure portál hívást **UseDefaultConfiguration()**. Ez egyenértékű a következő telepítési:

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

A bővítmény használt módszerek a következők:

* `AddMobileAppHomeController()`az alapértelmezett Azure Mobile Apps kezdőlap biztosít.
* `MapApiControllers()`WebAPI tartományvezérlők attribútummal rendelkező egyéni API képességeket nyújt a `[MobileAppController]` attribútum.
* `AddTables()`egy táblázatot a biztosít a `/tables` tábla tartományvezérlők végpontok.
* `AddTablesWithEntityFramework()`egy rövid az aktuális leképezés van a `/tables` használó Entity Framework végpontok alapú tartományvezérlők.
* `AddPushNotifications()`eszközök regisztrálása a Notification Hubs egy egyszerű módszert kínál.
* `MapLegacyCrossDomainController()`standard CORS fejlécek biztosít helyi fejlesztési.

### <a name="sdk-extensions"></a>SDK-bővítmények
A következő NuGet-alapú bővítmény csomagok az alkalmazás által használható különböző mobil funkciókat biztosítanak. Bővítmények inicializálásakor használatával engedélyezheti a **MobileAppConfiguration** objektum.

* [Microsoft.Azure.Mobile.Server.Quickstart] a Mobile Apps alapbeállítások támogatja. Meghívásával része a konfigurációnak a **UseDefaultConfiguration** kiterjesztésmetódus inicializálása során. Ezt a bővítményt tartalmazza a következő kiterjesztések: értesítések, a hitelesítés, az entitás, a táblák, a tartományok közötti és otthoni csomagok. Ez a csomag használja a Mobile Apps gyors üzembe helyezés az Azure portálon.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) valósítja meg az alapértelmezett *a mobilalkalmazás megfelelően működik, és lap* a webhely gyökeréhez. Meghívásával hozzáadni a konfigurációhoz a **AddMobileAppHomeController** kiterjesztésmetódus.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) adatok osztályokat tartalmazza, és a készlet létrehozása az adatok feldolgozási sor. Meghívásával hozzáadni a konfigurációhoz a **AddTables** kiterjesztésmetódus.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) lehetővé teszi, hogy az entitás-keretrendszer access adatokat az SQL-adatbázisban. Meghívásával hozzáadni a konfigurációhoz a **AddTablesWithEntityFramework** kiterjesztésmetódus.
* [Microsoft.Azure.Mobile.Server.Authentication] engedélyezi a hitelesítést és a készlet létrehozása az OWIN köztes jogkivonatainak érvényesítéséhez használt. Meghívásával hozzáadni a konfigurációhoz a **AddAppServiceAuthentication** és **IAppBuilder**. **UseAppServiceAuthentication** kiterjesztésmetódusok.
* [Microsoft.Azure.Mobile.Server.Notifications] lehetővé teszi a leküldéses értesítések és egy leküldéses regisztrációs végpontot határozza meg. Meghívásával hozzáadni a konfigurációhoz a **AddPushNotifications** kiterjesztésmetódus.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) egy tartományvezérlőre, amely az adatok a mobileszköz-alkalmazás is szolgál az örökölt webböngészők hoz létre. Meghívásával hozzáadni a konfigurációhoz a **MapLegacyCrossDomainController** kiterjesztésmetódus.
* [Microsoft.Azure.Mobile.Server.Login] a AppServiceLoginHandler.CreateToken() metódust biztosít, amelyet egy egyéni hitelesítési forgatókönyvek során használt statikus metódus.

## <a name="publish-server-project"></a>Útmutató: a kiszolgáló-projekt közzététele
Ez a szakasz bemutatja, hogyan tehet közzé a Visual Studio .NET-háttérprojekt. A háttérrendszer projekt használatával is telepítheti [Git](../app-service/app-service-deploy-local-git.md) vagy bármely egyéb módszerek elérhető e.

1. A Visual Studio NuGet-csomagok visszaállítására a projekt újraépítése.
2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **közzététel**. Beállíthatja, először kell a közzétételi profil meghatározásához. Ha már rendelkezik egy definiált profil, válassza ki azt, és kattintson a **közzététel**.
3. A közzétételi célként válassza ki, kattintson **Microsoft Azure App Service** > **következő**, majd (ha szükséges) jelentkezzen be Azure hitelesítő adatait.
   A Visual Studio letölti és tárolja biztonságos helyen a közzétételi beállítások közvetlenül az Azure-ból.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Válassza ki a **előfizetés**, jelölje be **erőforrástípus** a **nézet**, bontsa ki a **mobilalkalmazás**, és kattintson a mobil-háttéralkalmazást, majd **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ellenőrizze a közzétételi profil adatait, és kattintson a **közzététel**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Ha a mobilalkalmazás háttérrendszerének közzététele sikeresen befejeződött, megjelenik a sikert jelző kezdőlapja.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Útmutató: egy tábla vezérlő megadása
Adja meg egy tábla vezérlő teszi közzé a mobil ügyfelek SQL tábla.  Egy tábla Controller konfigurálása három lépésből áll:

1. Hozzon létre egy adatok átvitele objektum (DTO) osztályt.
2. Adja meg a Mobile DbContext osztályt a táblahivatkozás.
3. Hozzon létre egy tábla tartományvezérlőre.

Egy adatok átvitele objektum (DTO) mező egy egyszerű C#-objektum, amely örökli `EntityData`.  Példa:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

A DTO a tábla az SQL-adatbázis azonosítására szolgál.  Az adatbázis-bejegyzés létrehozása, vegye fel a `DbSet<>` használ DbContext tulajdonságot.  Az Azure Mobile Apps alapértelmezett projektsablon a DbContext neve `Models\MobileServiceContext.cs`:

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

Ha az Azure SDK telepítve van, most létrehozhat egy sablont table vezérlő az alábbiak szerint:

1. Kattintson a jobb gombbal a tartományvezérlők mappára, és válassza ki **Hozzáadás** > **vezérlő...** .
2. Válassza ki a **Azure Mobile Apps Table vezérlő** lehetőséget, majd kattintson az **Hozzáadás**.
3. Az a **adja hozzá a tartományvezérlő** párbeszédpanel:
   * Az a **Model class** legördülő menüben válassza ki az új DTO.
   * Az a **DbContext** legördülő menüben válassza ki a Mobile Service DbContext osztályt.
   * A tartományvezérlő neve akkor jön létre.
4. Kattintson a **Hozzáadás** parancsra.

A gyors üzembe helyezés kiszolgálóprojektet egy egyszerű példa tartalmaz **TodoItemController**.

### <a name="adjust-pagesize"></a>Útmutató: a tábla a lapozófájl méretének módosítása
Alapértelmezés szerint az Azure Mobile Apps kérelmenként 50 bejegyzést ad vissza.  Lapozófájl biztosítja, hogy az ügyfél nem nagy terhelést jelent a felhasználói felület szálán, sem a kiszolgáló túl sokáig a jó felhasználói élményt biztosítva. Módosítsa a táblázat a lapozófájl méretét, növelje a kiszolgálóoldali "engedélyezett a lekérdezés mérete", és az ügyféloldali oldalméret a kiszolgálóoldali "engedélyezett a lekérdezés mérete" úgy kell beállítani, használja a `EnableQuery` attribútum:

    [EnableQuery(PageSize = 500)]

Győződjön meg arról a pageSize együttes értéke azonos vagy nagyobb, mint az ügyfél által kért méret.  Tekintse meg az adott ügyfél útmutató dokumentációjában talál részletes információt az ügyfél Oldalméret módosítása.

## <a name="how-to-define-a-custom-api-controller"></a>Hogyan: Adja meg egy egyéni API-vezérlő
Az egyéni API-vezérlőben a Mobile Apps-háttéralkalmazás a legalapvetőbb lehetőségeket kínál a jelentkezik, mintha a végpont. Egy mobileszköz-specifikus API-vezérlőben, a [MobileAppController] attribútum használatával regisztrálhatja. A `MobileAppController` attribútum regisztrálja az útvonal, a Mobile Apps JSON-szerializáló beállítja és bekapcsolja a [ügyfél verzióellenőrzés](app-service-mobile-client-and-server-versioning.md).

1. A Visual Studióban, kattintson a jobb gombbal a tartományvezérlők mappát, majd kattintson **Hozzáadás** > **vezérlő**, jelölje be **Web API 2-es vezérlőhöz&mdash;üres** és Kattintson a **Hozzáadás**.
2. Adjon meg egy **tartományvezérlő neve**, például `CustomController`, és kattintson a **Hozzáadás**.
3. Az új tartományvezérlő osztály fájlban adja hozzá a következő using utasítást:

        using Microsoft.Azure.Mobile.Server.Config;
4. Alkalmazza a **[MobileAppController]** attribútum segítségével az API vezérlő osztálydefiníciót, az alábbi példában látható módon:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup.MobileApp.cs fájlban adja hozzá a következőt hívja a **MapApiControllers** kiterjesztésmetódus, az alábbi példában látható módon:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Használhatja a `UseDefaultConfiguration()` kiterjesztésmetódus helyett `MapApiControllers()`. Minden futtassa, amelynek nincs **MobileAppControllerAttribute** alkalmazza az ügyfelek által továbbra is elérhető, de előfordulhat, hogy nem megfelelően védelmekor ügyfelek bármely Mobile Apps-ügyfél SDK használatával.

## <a name="how-to-work-with-authentication"></a>Útmutató: hitelesítés
Az Azure Mobile Apps használja az App Service hitelesítési / engedélyezési biztonságossá tételéhez a mobil-háttéralkalmazást.  Ez a szakasz bemutatja, hogyan hajthat végre a következő hitelesítési kapcsolatos feladatokat a .NET server háttérprojekt:

* [Útmutató: hitelesítés hozzáadása egy kiszolgálóprojektet](#add-auth)
* [Útmutató: az alkalmazás egyéni hitelesítés használata](#custom-auth)
* [Hogyan: lekérése hitelesített felhasználói adatok](#user-info)
* [Útmutató: a jogosult felhasználók adatokhoz való hozzáférést](#authorize)

### <a name="add-auth"></a>Útmutató: hitelesítés hozzáadása egy kiszolgálóprojektet
Kiterjesztésével server projektjéhez hitelesítési is hozzáadhat a **MobileAppConfiguration** objektum és OWIN köztes konfigurálása. Amikor telepíti a [Microsoft.Azure.Mobile.Server.Quickstart] csomag- és hívás a **UseDefaultConfiguration** kiterjesztésmetódus, ugorjon a 3. lépés.

1. A Visual Studio telepíti a [Microsoft.Azure.Mobile.Server.Authentication] csomag.
2. A Startup.cs projekt fájlban adja hozzá a következő kódsort elején a **konfigurációs** módszert:

        app.UseAppServiceAuthentication(config);

    Az OWIN köztes összetevő ellenőrzi a társított App Service-átjáró által kiállított jogkivonatokat.
3. Adja hozzá a `[Authorize]` bármely tartományvezérlő vagy a metódust, amelyhez hitelesítés szükséges.

Hitelesíti az ügyfeleket a Mobile Apps-háttéralkalmazásának kapcsolatos további tudnivalókért lásd: [hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Útmutató: az alkalmazás egyéni hitelesítés használata
> [!IMPORTANT]
> Ahhoz, hogy az egyéni hitelesítési, először engedélyeznie kell App Service hitelesítés kiválasztása az App Service egy szolgáltatót az Azure portálon nélkül. Ezzel a lépéssel engedélyezi a WEBSITE_AUTH_SIGNING_KEY környezeti változó, ha azt.
> 
> 
Ha nem szeretné az App Service hitelesítési/engedélyezési szolgáltatók egyikét kell használnia, a saját bejelentkezési rendszer valósíthat meg. Telepítse a [Microsoft.Azure.Mobile.Server.Login] csomag elősegítve ezzel a hitelesítési jogkivonatok létrehozásához.  Adja meg a saját kódot a felhasználó hitelesítő adatainak ellenőrzése. Például ellenőrizze sózott és kivonatolt jelszavakat adatbázisban ellen. Az alábbi példában a `isValidAssertion()` (határozni) metódus felelős az ellenőrzést.

Az egyéni hitelesítési fel van fedve egy ApiController létrehozásával, és az ilyen `register` és `login` műveletek. Az ügyfél által használandó egyéni felhasználói Felületet a Információgyűjtés a felhasználótól.  Az információkat majd szabványos HTTP POST hívja az API számára. Ha a kiszolgáló ellenőrzi a helyességi feltétel, jogkivonat használatával kiadott a `AppServiceLoginHandler.CreateToken()` metódust.  A ApiController **nem kell** használja a `[MobileAppController]` attribútum.

Példa `login` művelet:

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

Az előző példában a LoginResult és LoginResultUser kötelező tulajdonságok feltáró szerializálható objektumok. Az ügyfél vár a válaszokat, vissza kell adni az űrlap JSON-objektumként:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

A `AppServiceLoginHandler.CreateToken()` metódust tartalmaz egy *célközönség* és egy *kibocsátó* paraméter. Mindkét paraméter beállítása az URL-CÍMÉT az alkalmazás gyökereként, a HTTPS protokollt használ. Hasonló módon állítsa be *secretKey* kell lennie az értéket az alkalmazás csomagazonosítóját aláírási kulcs. Az aláírási kulcs ügyfélprogram ne ossza el a kulcsok Mentaízű és megszemélyesíthet felhasználókat is használható. Ezt úgy szerezheti be az aláírási kulcs közben Vezérlőpultjának az App Service-ben üzemeltetett a *webhely\_AUTH\_aláírás\_kulcs* környezeti változó. Ha szükséges a helyi hibakeresési környezetben, kövesse az utasításokat a a [hitelesítéssel helyi hibakeresés](#local-debug) lekérni a kulcsot, és alkalmazás-beállítás szerint tárolja a szakaszban található.

A kibocsátott jogkivonat más jogcímeket és lejárati dátummal is.  A minimális követelmény, a kibocsátott jogkivonat tartalmaznia kell a tulajdonos (**sub**) jogcímek.

A szabványos ügyfél támogathatja `loginAsync()` metódus által a hitelesítési útvonal terhelve.  Ha az ügyfél meghívja a `client.loginAsync('custom');` a bejelentkezéshez, az útvonal lehet `/.auth/login/custom`.  Beállíthatja, hogy az egyéni vezérlő használatával útvonal `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Használja a `loginAsync()` megközelítés biztosítja, hogy a hitelesítési jogkivonat csatolva van-e a szolgáltatás minden ezt követő hívásakor.
>
>

### <a name="user-info"></a>Hogyan: lekérése hitelesített felhasználói adatok
Amikor a felhasználó hitelesítése az App Service, a hozzárendelt felhasználói Azonosítót és egyéb információkat hozzáférhet a .NET-háttérrendszer kódban. A felhasználói adatok használható hitelesítési döntések meghozatala során a háttérben. A következő kódot a felhasználói Azonosítóját a kérelemhez társított szerzi be:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A SID származó szolgáltatói felhasználói Azonosítóját, és egy adott felhasználó és a bejelentkezés-szolgáltató statikus.  A SID értéke érvénytelen a hitelesítési tokenek null.

App Service lehetővé teszi a bejelentkezési szolgáltató által adott jogcímek igénylésére. Minden egyes identitásszolgáltató az identitásszolgáltató SDK használatával további információk megadására.  Például használhatja a Facebook Graph API-t ismerősök információt.  A szolgáltató panel az Azure portálon is megadhat igényelt jogcímeket. Egyes jogcímek a identitásszolgáltatóval további beállításokra van szükség.

A következő kódot a hívások a **GetAppServiceIdentityAsync** kiterjesztésmetódus a bejelentkezési hitelesítő adatokat, többek között a hozzáférési jogkivonatának beszerzéséhez szükséges kéréseket a meghatározott a Facebook Graph API:

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

Adja hozzá egy utasítást `System.Security.Principal` biztosításához a **GetAppServiceIdentityAsync** kiterjesztésmetódus.

### <a name="authorize"></a>Útmutató: a jogosult felhasználók adatokhoz való hozzáférést
Az előző szakaszban azt bemutatta, hogyan lehet lekérni a hitelesített felhasználók felhasználói Azonosítóját. Adatokhoz és más erőforrások, ez az érték alapján korlátozhatja a hozzáférést. Például userId oszlop hozzáadásával táblák és a lekérdezés eredményeit a felhasználói azonosító szűrés módja a egyszerű visszaadott adatok csak a jogosult felhasználókra korlátozzák. A következő kódot adja vissza adatok csak akkor, ha a biztonsági azonosítója megegyezik-e a TodoItem tábla UserId oszlopban:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

A `Query()` metódus értéket ad vissza egy `IQueryable` , kezelni a szűrés LINQ kezelhetők.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Hogyan: adhat leküldéses értesítések küldéséhez egy kiszolgálóprojektet
Leküldéses értesítések hozzáadása a projekt kiterjesztésével a **MobileAppConfiguration** objektum és a Notification Hubs ügyfelet hoz létre.

1. A Visual Studióban, kattintson a jobb gombbal a projekt, és kattintson a **NuGet-csomagok kezelése**, keressen `Microsoft.Azure.Mobile.Server.Notifications`, majd kattintson a **telepítése**.
2. Telepítse ezt a lépést ismételje meg a `Microsoft.Azure.NotificationHubs` csomag, amely tartalmazza a Notification Hubs ügyféloldali kódtárára.
3. A App_Start/Startup.MobileApp.cs és adjon hozzá egy a **AddPushNotifications()** kiterjesztésmetódus inicializálása közben:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Az alábbi kódot, amely létrehozza a Notification Hubs-ügyfél hozzáadása:

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

A Notification Hubs-ügyfél leküldéses értesítések küldésére regisztrált eszközöket használhatja. További információkért lásd: [leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-push.md). Notification hubs használatával kapcsolatos további információkért lásd: [Notification Hubs – áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Hogyan: engedélyezése megcélzott ügyfélleküldéses címkék használatával
A Notification Hubs lehetővé teszi célzott értesítések küldése adott regisztrációk címkék használatával. Több címkék jönnek létre automatikusan:

* A telepítési azonosító azonosítja az adott eszköz.
* A felhasználói azonosítóját, a hitelesített SID azonosítója alapján azonosítja az egy adott felhasználó.

A telepítési azonosító érhetők el a **végrehajtott** tulajdonságát a **MobileServiceClient**.  A következő példa bemutatja, hogyan használja a telepítési Azonosítót a Notification Hubs egy adott telepítési egy címke hozzáadása:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Leküldéses értesítés regisztrálása során az ügyfél által megadott címkéket figyelmen kívül hagyja a kiszolgáló létrehozása a telepítés során. Ahhoz, hogy egy ügyfél címkék hozzáadása a telepítés, létre kell hoznia egy egyéni API-t, amely az előző minta használatával címkék.

Lásd: [ügyfél által hozzáadott leküldéses értesítések címkék] [ 5] például egy App Service Mobile Apps befejezett gyors üzembe helyezési minta.

## <a name="push-user"></a>Hogyan: leküldéses értesítések küldése a hitelesített felhasználó
Amikor a hitelesített felhasználó regisztrálja a leküldéses értesítések, a felhasználói azonosító címke automatikusan hozzáadódik a regisztráció. Ezt a címkét használatával leküldéses értesítéseket küldhet az adott személy által regisztrált összes eszközre. Az alábbi kód lekérdezi a kérelmező felhasználó biztonsági azonosítója és minden eszközregisztráció személyre sablon leküldéses értesítést küld:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Amikor regisztrál egy hitelesített ügyfél leküldéses értesítésekhez, győződjön meg arról, hogy a hitelesítési teljes regisztrációs megkísérlése előtt. További információkért lásd: [felhasználók leküldése] [ 6] .NET-háttérrendszer az App Service Mobile Apps befejezett gyors üzembe helyezési minta.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Hogyan: hibakeresését és a .NET Server SDK hibáinak elhárítása
Az Azure App Service számos Hibakeresés és hibaelhárítási módszerekről az ASP.NET-alkalmazások biztosítja:

* [Az Azure App Service figyelése](../app-service/web-sites-monitor.md)
* [Az Azure App Service diagnosztikai naplózás engedélyezése](../app-service/web-sites-enable-diagnostic-log.md)
* [A Visual Studio egy Azure App Service hibaelhárítása](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Naplózás
App Service diagnosztikai naplók írni az ASP.NET nyomkövetési írását normál használatával. Ahhoz, hogy a naplókba, engedélyeznie kell a Mobile Apps-háttéralkalmazás diagnosztika.

Diagnosztika engedélyezése, és a naplófájlok írásához:

1. Kövesse a [diagnosztika engedélyezésével](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Adja hozzá a következő using utasítást a kód fájlban:

        using System.Web.Http.Tracing;
3. Hozzon létre egy nyomkövetési író írni a .NET-háttérrendszer a diagnosztikai naplók, az alábbiak szerint:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. A projekt közzé, és hozzáférni a Mobile Apps-háttéralkalmazás a kód útvonalat a naplózási végrehajtásához.
5. Töltse le és kiértékelheti a naplókat, a [hogyan: naplók letöltéséhez](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Helyi hitelesítéssel hibakeresés
Az alkalmazás helyileg tesztelje a módosításokat a felhő közzététel előtt is futtathatja. Nyomja le az Azure Mobile Apps-háttérkiszolgálókon legtöbb, *F5* a Visual Studio során. Van azonban néhány további szempontok hitelesítése során.

Rendelkeznie kell egy felhőalapú mobilalkalmazást az App Service hitelesítési/engedélyezési konfigurálva, és az ügyfél a megadott másodlagos bejelentkezési állomásaként felhő végpontot kell rendelkeznie. A szükséges lépéseit ügyfélplatform dokumentációjában olvashatók.

Győződjön meg arról, hogy rendelkezik-e a mobil-háttéralkalmazást [Microsoft.Azure.Mobile.Server.Authentication] telepítve. Ezt követően az alkalmazás OWIN indítási osztályt, adja hozzá a következő után `MobileAppConfiguration` telepítve van a `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

A fenti példában, konfigurálnia kell a *authAudience* és *authIssuer* Alkalmazásbeállítások belül a Web.config fájl az egyes URL-CÍMÉT az alkalmazás gyökereként használja a HTTPS sémát kell. Hasonló módon állítsa be *authSigningKey* kell lennie az értéket az alkalmazás csomagazonosítóját aláírási kulcs.
Az aláírási kulcs beszerzése:

1. Keresse meg az alkalmazás belül a [Azure-portálon]
2. Kattintson a **eszközök**, **Kudu**, **Ugrás**.
3. A Kudu felügyeleti hely, kattintson a **környezet**.
4. Keresse meg az értéket a *webhely\_AUTH\_aláírás\_kulcs*.

Az aláírási kulcs használata a *authSigningKey* paraméter a helyi alkalmazások Config.  A mobil-háttéralkalmazást most már rendelkezik a érvényesíthet jogkivonatokat, a helyi futtatás során, amelyek az ügyfél a felhőalapú végpontról a jogkivonat szerzik be.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portálon]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
