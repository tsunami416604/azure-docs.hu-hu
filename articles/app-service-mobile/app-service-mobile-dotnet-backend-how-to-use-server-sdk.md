---
title: A .NET háttérkiszolgáló-SDK használata a Mobile Apps-alkalmazáshoz |} A Microsoft Docs
description: Megtudhatja, hogyan használható a .NET háttérkiszolgáló-SDK az Azure App Service Mobile Apps a.
keywords: App Service-ben, az azure app Service-ben, mobilalkalmazás, mobilszolgáltatások, méretezhető, méretezhető, alkalmazás üzembe helyezés, azure alkalmazástelepítés
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
ms.openlocfilehash: bbba7898329af9d9bca9d35883e3cb4097ca3de4
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968612"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Az Azure Mobile Appshoz készült .NET háttérkiszolgáló-SDK használata
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a témakör bemutatja, hogyan használható a .NET háttérkiszolgáló-SDK az Azure App Service Mobile Apps alkalmazási helyzeteket. Az Azure Mobile Apps SDK segítségével dolgozhat a mobilügyfelek az ASP.NET-alkalmazás.

> [!TIP]
> A [.NET server SDK for Azure Mobile Apps] [ 2] nyílt forráskódú, a Githubon. A tárház minden forráskódját, beleértve a teljes kiszolgáló SDK egység és az egyes mintaprojektekkel tartalmazza.
>
>

## <a name="reference-documentation"></a>Segédanyagok
Itt található az a kiszolgáló SDK dokumentációja: [Azure Mobile Apps .NET-referencia][1].

## <a name="create-app"></a>Útmutató: .NET Mobile Apps-háttéralkalmazás létrehozása
Ha a számítógépet egy új projekt, egy App Service-alkalmazás használatával hozhat létre a [Azure Portal] vagy a Visual Studio. Az App Service-alkalmazás helyi futtatásához, vagy a projekt közzététele a felhőalapú alkalmazásszolgáltatás mobilalkalmazáshoz.

Ha mobileszközös lehetőségek hozzáadni egy meglévő projekt, tekintse meg a [töltse le és inicializálja az SDK-t](#install-sdk) szakaszban.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Hozzon létre egy .NET-háttérrendszer az Azure portal használatával
Hozzon létre egy App Service-ben mobil háttérszolgáltatás használatára, vagy kövesse a [a rövid útmutató] [ 3] vagy kövesse az alábbi lépéseket:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Térjen vissza a *Ismerkedés* panel alatt **tábla API létrehozása**, válassza a **C#** , a **háttérrendszer nyelveként**. Kattintson a **letöltése**, bontsa ki a tömörített projektfájlokat a helyi számítógépen, és nyissa meg a megoldást a Visual Studióban.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Hozzon létre egy .NET-háttérrendszer a Visual Studio 2017 használatával

Telepítse az Azure-beli számítási keresztül a Visual Studióból az Azure Mobile Apps-projekt közzététele a Visual Studio telepítőjében. Miután telepítette az SDK-t, hozzon létre egy ASP.NET-alkalmazás az alábbi lépéseket követve:

1. Nyissa meg a **új projekt** párbeszédpanelen (a **fájl** > **új** > **projekt...** ).
2. Bontsa ki a **Visual C#** válassza **webes**.
3. Válassza ki **ASP.NET-es webalkalmazás (.NET-keretrendszer)**.
4. Adja meg a projekt nevét. Ezután kattintson az **OK** gombra.
5. Válassza ki **Azure Mobile Apps** sablont a listából.
6. Kattintson a **OK** a megoldás létrehozásához.
7. Kattintson a jobb gombbal a projektre a a **Megoldáskezelőben** válassza **Publish...** , majd válassza a **App Service-ben** közzétételi célként.
8. Kövesse az utasításokat hitelesítéséhez és a egy új vagy meglévő Azure App Service szolgáltatásban való közzétételéhez válassza.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Hozzon létre egy Visual Studio 2015 használata .NET-háttérrendszer

Telepítse a [Azure SDK for .NET] [ 4] (2.9.0-s verzió vagy újabb verzió) az Azure Mobile Apps-projekt létrehozása a Visual Studióban. Miután telepítette az SDK-t, hozzon létre egy ASP.NET-alkalmazás az alábbi lépéseket követve:

1. Nyissa meg a **új projekt** párbeszédpanelen (a **fájl** > **új** > **projekt...** ).
2. Bontsa ki a **sablonok** > **Visual C#**, és válassza ki **webes**.
3. Válassza az **ASP.NET Web Application** (ASP.NET-es webalkalmazás) lehetőséget.
4. Adja meg a projekt nevét. Ezután kattintson az **OK** gombra.
5. A *ASP.NET 4.5.2 sablonok*válassza **Azure Mobile Apps**. Ellenőrizze **üzemeltetés a felhőben** mobilháttérmodul létrehozása, amelyre a projekt közzéteheti a felhőben.
6. Kattintson az **OK** gombra.

## <a name="install-sdk"></a>Hogyan: Töltse le és inicializálja az SDK-t
Az SDK-val érhető el az [NuGet.org]. A csomag tartalmazza az SDK használatának megkezdéséhez szükséges alapvető funkciót biztosítja. Inicializálja az SDK-t, műveleteket hajthat végre kell a **HttpConfiguration** objektum.

### <a name="install-the-sdk"></a>Az SDK telepítése
Telepítse az SDK-t, kattintson a jobb gombbal a Visual Studióban, válassza ki a kiszolgálói projekt **NuGet-csomagok kezelése**, keresse meg a [Microsoft.Azure.Mobile.Server] csomagot, majd kattintson a **telepítése** .

### <a name="server-project-setup"></a> A kiszolgálói projekt inicializálása
Egy .NET-háttérrendszer kiszolgálóprojektet inicializálva van más ASP.NET-projektek hasonló azzal egy OWIN indítási osztályt. Győződjön meg arról, hogy rendelkezik-e hivatkozott a NuGet-csomag `Microsoft.Owin.Host.SystemWeb`. Ez az osztály hozzáadása a Visual Studióban, kattintson a jobb gombbal a kiszolgálói projektet, és válassza ki **Hozzáadás** >
**új elem**, majd **webes**  >  ** Általános** > **OWIN indítási osztályt**.  Egy osztály hozza létre a következő attribútumot:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Az a `Configuration()` az OWIN indítási osztályt, a használat módja egy **HttpConfiguration** objektum konfigurálása az Azure Mobile Apps-környezetben.
Az alábbi példa inicializálja a kiszolgálói projektet, nem hozzáadott funkciókat:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Egyes funkciók engedélyezéséhez meg kell hívni a bővítő metódusokat a **MobileAppConfiguration** hívása előtt objektum **ApplyTo**. Például az alábbi kód hozzáadja az alapértelmezett útvonalakat a attribútummal rendelkező összes API-vezérlő `[MobileAppController]` inicializálása során:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

A server gyors üzembe helyezése az Azure portal-hívások **UseDefaultConfiguration()**. Ez egyenértékű a következő beállítás:

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

* `AddMobileAppHomeController()` az alapértelmezett Azure Mobile Apps kezdőlap biztosít.
* `MapApiControllers()` egyéni API-funkciókat biztosít a WebAPI-tartományvezérlőket az kitüntetett a `[MobileAppController]` attribútum.
* `AddTables()` tartalmazza a `/tables` tábla tartományvezérlők végpontok.
* `AddTablesWithEntityFramework()` a leképezés egy röviden kézből a `/tables` Entity Framework használatával végpontok alapú tartományvezérlők.
* `AddPushNotifications()` eszközök regisztrálása a Notification Hubs egy egyszerű módszert kínál.
* `MapLegacyCrossDomainController()` Standard szintű CORS fejléceket biztosít a helyi fejlesztési.

### <a name="sdk-extensions"></a>SDK-bővítmények
A következő NuGet-alapú bővítmény csomagok adja meg az alkalmazás által használható különféle mobil szolgáltatások. Bővítmények az inicializálás során használatával engedélyezheti a **MobileAppConfiguration** objektum.

* [Microsoft.Azure.Mobile.Server.Quickstart] támogatja a Mobile Apps alapszintű beállítás. Meghívásával része a konfigurációnak a **UseDefaultConfiguration** metódust az inicializálás során. Ez a bővítmény tartalmazza a következő kiterjesztések: értesítések, hitelesítés, entitás, táblák, a tartományok közötti és a kezdőlap csomagok. A Mobile Apps – első lépések az Azure Portalon ezt a csomagot használja.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) valósítja meg az alapértelmezett *ezt a mobilalkalmazást működik-lap* webhely legfelső szintű. Adja hozzá a konfigurációhoz meghívásával a **AddMobileAppHomeController** metódust.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) adatkezelési osztályokat tartalmazza, és a csoportok mentése adatfolyamat. Adja hozzá a konfigurációhoz meghívásával a **AddTables** metódust.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) lehetővé teszi, hogy az Entity Framework adatok elérését az SQL Database-ben. Adja hozzá a konfigurációhoz meghívásával a **AddTablesWithEntityFramework** metódust.
* [Microsoft.Azure.Mobile.Server.Authentication] lehetővé teszi a hitelesítést és a csoportok felfelé az OWIN közbenső szoftver jogkivonatok érvényesítésére használni. Adja hozzá a konfigurációhoz meghívásával a **AddAppServiceAuthentication** és **IAppBuilder**. **UseAppServiceAuthentication** bővítő metódusokat.
* [Microsoft.Azure.Mobile.Server.Notifications] lehetővé teszi, hogy leküldéses értesítéseket és a egy leküldéses regisztrációs végpont meghatározása. Adja hozzá a konfigurációhoz meghívásával a **AddPushNotifications** metódust.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) hoz létre egy vezérlő, amely régebbi webböngészők, a Mobile App szolgáltat adatokat. Adja hozzá a konfigurációhoz meghívásával a **MapLegacyCrossDomainController** metódust.
* [Microsoft.Azure.Mobile.Server.Login] biztosít a AppServiceLoginHandler.CreateToken() módszer, amely egy egyéni hitelesítési forgatókönyvek során használt statická metoda.

## <a name="publish-server-project"></a>Útmutató: a kiszolgálói projekt közzététele
Ez a szakasz bemutatja, hogyan teheti közzé a .NET-háttérrendszer projekt a Visual Studióból. Ezenkívül telepítheti a háttér-projektet az [Git](../app-service/app-service-deploy-local-git.md) vagy bármely más módszerek elérhető itt.

1. A Visual Studióban építse újra a projektet a NuGet-csomagok visszaállítására.
2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **közzététel**. Először tesz közzé, amelyet akkor kell meghatároznia egy közzétételi profilt. Ha már rendelkezik egy meghatározott profil, válassza ki, és kattintson a **közzététel**.
3. Ha egy közzétételi célként válassza kéri, kattintson a **Microsoft Azure App Service** > **tovább**, majd (ha szükséges) jelentkezzen be Azure hitelesítő adatait.
   A Visual Studio letölti és biztonságosan tárolja a közzétételi beállítások közvetlenül az Azure-ból.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Válassza ki a **előfizetés**, jelölje be **erőforrástípus** a **nézet**, bontsa ki a **mobilalkalmazás**, és kattintson a Mobile Apps-háttéralkalmazást, majd kattintson **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ellenőrizze a közzétételi profil adatait, és kattintson a **közzététel**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Ha a Mobile Apps-háttéralkalmazást közzététele sikeresen befejeződött, megjelenik egy kezdőlapja jelzi a sikert.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Útmutató: egy definiálása
Adjon meg egy Table Controller elérhetővé a mobilügyfelek SQL-táblájába.  Egy Table Controller konfigurálása három lépésből áll:

1. Hozzon létre egy Data Transfer objektumot (dto-t) osztályt.
2. Adja meg a táblahivatkozást a Mobile DbContext osztályt.
3. Hozzon létre egy táblavezérlőnek.

A Data Transfer objektumot (dto-t) egy olyan egyszerű C# objektum, amely örökli az `EntityData`.  Példa:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

A dto-t a tábla az SQL Database meghatározására szolgál.  Az adatbázis-bejegyzést létrehozásához adja hozzá a `DbSet<>` tulajdonságot használ a DbContext.  Az Azure Mobile Apps alapértelmezett projektsablonnal a DbContext neve `Models\MobileServiceContext.cs`:

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

Ha az Azure SDK-t, a következő most már létrehozhat egy sablont kontroler tabulek:

1. Kattintson a jobb gombbal a tartományvezérlők mappához, és válassza ki **Hozzáadás** > **vezérlő...** .
2. Válassza ki a **Azure Mobile Apps Table Controller** lehetőséget, majd kattintson a **Hozzáadás**.
3. Az a **vezérlő hozzáadása** párbeszédpanel:
   * Az a **Model class** legördülő menüben válassza ki az új dto-t.
   * Az a **DbContext** legördülő menüben válassza ki a Mobile Service DbContext osztályt.
   * A tartományvezérlő neve jön létre az Ön számára.
4. Kattintson a **Hozzáadás** parancsra.

A rövid útmutató kiszolgálói projektet tartalmaz egy egyszerű példa **TodoItemController**.

### <a name="adjust-pagesize"></a>Útmutató: table lapozófájl méretének beállítása
Alapértelmezés szerint az Azure Mobile Apps-alkalmazások / kérelem 50 rekordjait adja vissza.  Lapozófájl biztosítja, hogy az ügyfél nem lefoglalják a felhasználói felület szála, sem a kiszolgáló túl sokáig várakozott egy jó felhasználói élményt biztosítva. A táblázat lapozófájl méretének módosításához növelni a kiszolgáló oldalán "engedélyezett a lekérdezések mérete", és az ügyféloldali oldal méretezése a kiszolgálóoldali "engedélyezett a lekérdezések mérete" úgy kell beállítani, használja a `EnableQuery` attribútum:

    [EnableQuery(PageSize = 500)]

Győződjön meg, hogy a PageSize azonos vagy nagyobb, mint az ügyfél által kért mérete.  Tekintse meg az adott ügyfél HOWTO dokumentációjában az ügyfél oldalméret módosítására.

## <a name="how-to-define-a-custom-api-controller"></a>Útmutató: egyéni API-vezérlő definiálása
Az egyéni API-vezérlő biztosítja, hogy a Mobile Apps-háttéralkalmazást a legalapvetőbb funkciókat elérhetővé tenni a végpontokat. Egy mobileszköz-specifikus API-vezérlő a [MobileAppController] attribútum használatával lehet regisztrálni. A `MobileAppController` attribútum regisztrálja az útvonalat, állítja be a Mobile Apps JSON-szerializáló, és kapcsolja be a [ügyfél verziója ellenőrzése](app-service-mobile-client-and-server-versioning.md).

1. A Visual Studióban kattintson a jobb gombbal a tartományvezérlők mappát, majd kattintson **Hozzáadás** > **vezérlő**válassza **Web API 2 vezérlő&mdash;üres** és Kattintson a **Hozzáadás**.
2. Adjon meg egy **vezérlőnév**, például `CustomController`, és kattintson a **Hozzáadás**.
3. Az új tartományvezérlő osztály fájlban adja hozzá a következő using utasítást:

        using Microsoft.Azure.Mobile.Server.Config;
4. Alkalmazza a **[MobileAppController]** attribútumot az API vezérlő az osztálykiterjesztések definíciója az alábbi példában látható módon:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup.MobileApp.cs fájlban adja hozzá a hívást a **MapApiControllers** metódust, az alábbi példában látható módon:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Is használhatja a `UseDefaultConfiguration()` bővítmény metódusa `MapApiControllers()`. Minden vezérlő, amely nem rendelkezik **MobileAppControllerAttribute** alkalmazza az ügyfelek által továbbra is elérhető, de, előfordulhat, hogy nem megfelelően használni az ügyfelek bármilyen mobilalkalmazás-ügyféloldali SDK-val.

## <a name="how-to-work-with-authentication"></a>Útmutató: hitelesítés használata
Az Azure Mobile Apps használja az App Service-hitelesítés / engedélyezés a mobil háttérmodul védelme az.  Ez a szakasz bemutatja, hogyan hajtsa végre a következő hitelesítési feladatok a .NET-háttérrendszer kiszolgálói projektet:

* [Útmutató: hitelesítés hozzáadása a kiszolgálói projekt](#add-auth)
* [Útmutató: az alkalmazás egyéni hitelesítés használata](#custom-auth)
* [Hogyan: lekérése hitelesített felhasználói adatok](#user-info)
* [Útmutató: az engedélyezett felhasználók adatokhoz való hozzáférést](#authorize)

### <a name="add-auth"></a>Útmutató: hitelesítés hozzáadása a kiszolgálói projekt
Hozzáadhat hitelesítési a kiszolgálói projektet által bővítése a **MobileAppConfiguration** objektum és az OWIN közbenső szoftver konfigurálása. Amikor telepíti a [Microsoft.Azure.Mobile.Server.Quickstart] csomagot, és hívja a **UseDefaultConfiguration** metódust, továbbléphet a 3. lépés.

1. A Visual Studio telepítése a [Microsoft.Azure.Mobile.Server.Authentication] csomagot.
2. Projekt a Startup.cs fájlban adja hozzá a következő kódsort a elején a **konfigurációs** módszer:

        app.UseAppServiceAuthentication(config);

    Az OWIN közbenső szoftveres összetevők érvényesíti a társított App Service-átjáró által kiállított jogkivonatokban.
3. Adja hozzá a `[Authorize]` bármely tartományvezérlő vagy a metódus, amelyhez hitelesítés szükséges.

Hogyan hitelesítheti az ügyfelek számára, hogy a Mobile Apps-háttéralkalmazást kapcsolatos további információkért lásd: [hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Útmutató: az alkalmazás egyéni hitelesítés használata
> [!IMPORTANT]
> Annak érdekében, hogy egyéni hitelesítés engedélyezéséhez először engedélyeznie kell App Service-hitelesítés anélkül, hogy egy szolgáltató az App Service az Azure Portalon. Ez lehetővé teszi a WEBSITE_AUTH_SIGNING_KEY környezeti változót, ha ezek üzemelnek.
> 
> 
Ha nem szeretne használni az App Service hitelesítés/engedélyezés egyik, a saját bejelentkezési rendszer valósíthat meg. Telepítse a [Microsoft.Azure.Mobile.Server.Login] csomag, amelyek segítik a hitelesítési jogkivonat létrehozása.  Adja meg a saját kódját felhasználó hitelesítő adatainak ellenőrzése. Például megkereshet egy adatbázisban sózott és kivonatolt jelszavak ellen. Az alábbi példában a `isValidAssertion()` (máshol megadott) módszer felelős ezeket az ellenőrzéseket.

Az egyéni hitelesítési tesz elérhetővé egy ApiController létrehozása és adatokhoz hozzáférést biztosító `register` és `login` műveleteket. Az ügyfélnek, az adatokat gyűjt a felhasználó egy egyéni felhasználói használnia kell.  Az információkat ezután az egy normál HTTP POST-hívás az API-hoz. Miután a kiszolgáló ellenőrzi a helyességi feltétel, a jogkivonat használatával kiadott a `AppServiceLoginHandler.CreateToken()` metódust.  A ApiController **nem** használja a `[MobileAppController]` attribútum.

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

Az előző példában LoginResult LoginResultUser jsou kötelező tulajdonságok feltáró szerializálható objektumok. Az ügyfél vár a válaszokat az űrlap JSON-objektumként kell visszaadni:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

A `AppServiceLoginHandler.CreateToken()` metódus egy *célközönség* és a egy *kibocsátó* paraméter. Mindkét paraméter beállítása az URL-címe az alkalmazás gyökérkönyvtáraként használatával a HTTPS-sémát. Hasonlóképpen kell beállítania *secretKey* kell az alkalmazás értékét a aláíró kulcs. Ne ossza el az ügyfélprogram aláírókulcsot Mentaízű kulcsok és a felhasználók megszemélyesítésére is használható. Az aláírókulcs közben hivatkozva App Service-ben üzemeltetett szerezheti be a *webhely\_AUTH\_aláírás\_kulcs* környezeti változót. Egy helyi hibakeresési környezetben szükség esetén kövesse a a [helyi hibakeresés hitelesítéssel](#local-debug) szakasz lekérni a kulcsot, és a egy alkalmazás beállításként tárolja.

A kiállított jogkivonat is tartalmazhat más jogcímet, és a egy lejárati dátumot.  A kiállított jogkivonat mezőgyűjteményre tartalmaznia kell egy tulajdonos (**sub**) jogcím.

A standard szintű ügyfél támogathatja `loginAsync()` metódus által a hitelesítés útvonal terhelve.  Ha az ügyfél meghívja a `client.loginAsync('custom');` való bejelentkezéshez, az útvonal lehet `/.auth/login/custom`.  Beállíthatja, hogy az egyéni hitelesítési vezérlő használatával útvonala `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Használatával a `loginAsync()` megközelítés biztosítja, hogy a hitelesítési jogkivonat csatolva van a szolgáltatás minden ezt követő meghívásához.
>
>

### <a name="user-info"></a>Hogyan: lekérése hitelesített felhasználói adatok
Ha a felhasználók hitelesítése az App Service, érheti el a hozzárendelt felhasználói Azonosítót és egyéb információkat a a .NET-háttéralkalmazás kódjához. A felhasználói adatokat a hitelesítési döntések meghozatala során a háttérben is használható. A következő kódot a felhasználói azonosító, a kéréshez társított kéri le:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A SID származik a szolgáltatóhoz tartozó felhasználói azonosító és a egy adott felhasználó és a bejelentkezés-szolgáltató statikus.  A biztonsági azonosító értéke null a következőnél érvénytelen hitelesítési token.

App Service-ben is lehetővé teszi a bejelentkezés-szolgáltató adott jogcímek kérhet. Minden egyes identitásszolgáltató SDK identitásszolgáltatóval további információk megadására.  Használhatja például a Facebook Graph API barátok információt.  A szolgáltató panel az Azure Portalon is megadhat igényelt jogcímeket. Egyes jogcímek az identitásszolgáltatónál történő további konfiguráció szükséges.

A következő kód meghívja a **GetAppServiceIdentityAsync** metódust a bejelentkezési hitelesítő adatokat, többek között a hozzáférési jogkivonat beszerzése az szükséges, hogy a Facebook Graph API kérelmeket:

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

Adjon hozzá egy utasítást a `System.Security.Principal` biztosít a **GetAppServiceIdentityAsync** metódust.

### <a name="authorize"></a>Útmutató: az engedélyezett felhasználók adatokhoz való hozzáférést
Az előző szakaszban megmutattuk, hogyan kérhető le egy hitelesített felhasználó felhasználói azonosítója. Az adatok és más erőforrások, ez az érték alapján korlátozhatja a hozzáférést. Például táblák felhasználói azonosító oszlop hozzáadásával és szűrése a lekérdezés eredményeit a felhasználói azonosító egy egyszerű módja visszaadott adatok csak a jogosult felhasználókra korlátozzák. A következő kódot az adatok sorát adja vissza, csak akkor, ha a biztonsági AZONOSÍTÓT a TodoItem tábla UserId oszlopára értéke megegyezik:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

A `Query()` metódus adja vissza egy `IQueryable` LINQ szűrés kezelésére, amelyek kezelhetők.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Hogyan: Adja hozzá a leküldéses értesítések küldéséhez egy kiszolgálói projekt
Leküldéses értesítések hozzáadása a kiszolgálói projektet által bővítése a **MobileAppConfiguration** objektum és a egy a Notification Hubs-ügyfelének létrehozása.

1. A Visual Studióban kattintson a jobb gombbal a kiszolgálói projektet, és kattintson a **NuGet-csomagok kezelése**, keressen `Microsoft.Azure.Mobile.Server.Notifications`, majd kattintson a **telepítése**.
2. Telepítse ezt a lépést ismételje meg a `Microsoft.Azure.NotificationHubs` csomagot, amely tartalmazza a Notification Hubs ügyféloldali kódtárral.
3. A App_Start/Startup.MobileApp.cs és adja hozzá a hívást a **AddPushNotifications()** metódust az inicializálás során:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adja hozzá a következő kódot, amely létrehozza a Notification Hubs-ügyfél:

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

Most már használhatja a Notification Hubs-ügyfél leküldéses értesítések küldéséhez regisztrált eszközökre. További információkért lásd: [leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-ios-get-started-push.md). A Notification hubs szolgáltatással kapcsolatos további információkért lásd: [Notification Hubs – áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Hogyan: Enable célzott leküldés címkék használatával
A Notification Hubs lehetővé teszi célzott értesítések küldése adott regisztrációk címkék használatával. Több címkét automatikusan jönnek létre:

* A telepítési azonosító egy adott eszköz azonosítja.
* A felhasználói azonosítót, a hitelesített SID alapján azonosítja az egy adott felhasználó.

A telepítési azonosító elérhetők a **installationId** tulajdonsága a **MobileServiceClient**.  Az alábbi példa bemutatja, hogyan használható a telepítési Azonosítót egy adott telepítés a Notification Hubs egy címke hozzáadása:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Az ügyfél által a leküldéses értesítésre való regisztráció során megadott címkéket figyelmen kívül hagyják a háttérrendszer létrehozása a telepítés során. Ahhoz, hogy egy ügyfél címkék hozzáadása a telepítés, létre kell hoznia egy egyéni API-t, amely hozzáadja a címkék az előző minta használatával.

Lásd: [ügyfél által hozzáadott leküldéses értesítések címkék] [ 5] a App Service Mobile Apps befejezett rövid mintában példa.

## <a name="push-user"></a>Hogyan: leküldéses értesítések küldése hitelesített felhasználók
Ha egy hitelesített felhasználó regisztrálja a leküldéses értesítésekhez, felhasználói azonosító címkét automatikusan hozzáadódik a regisztráció. Ez a címke használatával leküldéses értesítéseket küldhet az adott személy által regisztrált összes eszközre. A következő kód lekéri a kérelmező felhasználó biztonsági azonosítója, és a sablon leküldéses értesítést küld minden eszközregisztráció személyre:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Egy hitelesített ügyfél leküldéses értesítésekhez történő regisztráláskor győződjön meg arról, hogy a hitelesítés befejezése előtt regisztrációs. További információkért lásd: [felhasználók leküldése] [ 6] a App Service Mobile Apps befejezett rövid mintában a .NET-háttérrendszert.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Hogyan: Hibakeresés és a .NET Server SDK hibaelhárítása
Az Azure App Service biztosítja több hibakeresési és hibaelhárítási módszerekről az ASP.NET-alkalmazások:

* [Figyelés az Azure App Service platformon](../app-service/web-sites-monitor.md)
* [Az Azure App Service-ben a diagnosztikai naplózás engedélyezése](../app-service/web-sites-enable-diagnostic-log.md)
* [A Visual Studio Azure App Service hibaelhárítása](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Naplózás
Standard írását az ASP.NET nyomkövetési használatával írhat a diagnosztikai naplók App Service-ben. Ahhoz, hogy a naplókba, engedélyeznie kell diagnosztika a Mobile Apps-háttéralkalmazását.

Diagnosztika engedélyezése, és írja a naplókba:

1. Kövesse a [diagnosztika engedélyezése](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Adja hozzá a következő using utasítást a kód fájlban:

        using System.Web.Http.Tracing;
3. Hozzon létre egy nyomkövetési writer használatával írhat a .NET-háttérrendszer a diagnosztikai naplók a következő:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Tegye közzé újra a kiszolgálói projektet, és hajtsa végre a kódelérési út a naplózással, a Mobile Apps-háttéralkalmazás eléréséhez.
5. Töltse le és kiértékelheti a naplók leírtak szerint [hogyan: naplók letöltéséhez](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Helyi hibakeresés hitelesítéssel
Az alkalmazás helyileg tesztelheti a módosításokat a felhőbe való közzététel előtt is futtathatja. A legtöbb Azure Mobile Apps-háttérrendszerek esetében nyomja le az ENTER *F5* során a Visual Studióban. Vannak azonban néhány további szempontot-hitelesítés használata esetén.

Rendelkeznie kell egy felhőalapú mobilalkalmazást az App Service hitelesítés/engedélyezés konfigurált, és az ügyfél a felhőbeli végpont a másodlagos bejelentkezési gazdagépként megadott kell rendelkeznie. A szükséges konkrét eljárás ügyfélplatform dokumentációjában talál.

Győződjön meg arról, hogy rendelkezik-e a mobil háttérszolgáltatásban [Microsoft.Azure.Mobile.Server.Authentication] telepítve. Ezt követően az alkalmazás OWIN indítási osztályt, adja hozzá a következő után `MobileAppConfiguration` lett alkalmazva a `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Az előző példában, konfigurálnia kell a *authAudience* és *authIssuer* Alkalmazásbeállítások belül a Web.config fájlt az egyes URL-címe az alkalmazás gyökérkönyvtáraként használatával a HTTPS-sémát kell. Hasonlóképpen kell beállítania *authSigningKey* kell az alkalmazás értékét a aláíró kulcs.
Az aláíró kulcs beszerzése:

1. Keresse meg az alkalmazás belül a [Azure Portal]
2. Kattintson a **eszközök**, **Kudu**, **Go**.
3. A Kudu felügyeleti hely, kattintson a **környezet**.
4. Keresse meg az értéket a *webhely\_AUTH\_aláírás\_kulcs*.

Az aláíró kulcs használata a *authSigningKey* paraméter a helyi alkalmazások Config.  A mobil háttérszolgáltatásban most már rendelkezik a jogkivonatok helyileg fut, amikor az ügyfél a felhő alapú végpontról kéri le a jogkivonatot, amely ellenőrzése.

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
