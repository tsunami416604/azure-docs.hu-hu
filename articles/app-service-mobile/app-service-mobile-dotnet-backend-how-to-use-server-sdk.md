---
title: A .NET háttérkiszolgáló SDK-jának kezelése
description: Ismerje meg, hogyan dolgozhat a .NET háttérkiszolgáló SDK-val az Azure App Service Mobile Apps szolgáltatáshoz.
keywords: app service, azure app service, mobil app, mobil szolgáltatás, méretezés, méretezhető, app deployment, azure app deployment
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250139"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>A .NET háttérkiszolgáló SDK-jának kezelése az Azure Mobile Apps alkalmazásokhoz
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a témakör bemutatja, hogyan használhatja a .NET háttérkiszolgáló SDK-t a kulcsfontosságú Azure App Service Mobile Apps-forgatókönyvekben. Az Azure Mobile Apps SDK segítségével dolgozhat a ASP.NET alkalmazásmobil-ügyfelekkel.

> [!TIP]
> A [.NET server SDK for Azure Mobile Apps][2] nyílt forráskódú a GitHubon. A tárház tartalmazza az összes forráskódot, beleértve a teljes kiszolgáló SDK egység tesztcsomag és néhány minta projektek.
>
>

## <a name="reference-documentation"></a>Referenciadokumentáció
A kiszolgáló SDK referenciadokumentációja itt található: [Azure Mobile Apps .NET Reference][1].

## <a name="how-to-create-a-net-mobile-app-backend"></a><a name="create-app"></a>Útmutató: .NET mobile app háttérkapcsolat létrehozása
Ha új projektet indít, létrehozhat egy App Service-alkalmazást az [Azure Portalon] vagy a Visual Studióban. Futtathatja az App Service-alkalmazást helyileg, vagy közzéteheti a projektet a felhőalapú App Service mobilalkalmazásban.

Ha mobilfunkciókat ad hozzá egy meglévő projekthez, olvassa el [az SDK letöltése és inicializálása](#install-sdk) című szakaszt.

### <a name="create-a-net-backend-using-the-azure-portal"></a>.NET-háttérkapcsolat létrehozása az Azure Portal használatával
Az App Service mobilháttér-tartalékkapcsolatának létrehozásához kövesse a [rövid útmutatót,][3] vagy kövesse az alábbi lépéseket:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Az *Első lépések* panelen, a Tábla **API létrehozása**csoportban válassza a **C#** lehetőséget **háttérnyelvként.** Kattintson a **Letöltés**gombra, bontsa ki a tömörített projektfájlokat a helyi számítógépre, és nyissa meg a megoldást a Visual Studióban.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>.NET háttérprogram létrehozása a Visual Studio 2017-ben

Telepítse az Azure-számítási feladatokat a Visual Studio Installer segítségével, hogy közzétehesd az Azure Mobile Apps projektben a Visual Studióból. Az SDK telepítése után hozzon létre egy ASP.NET alkalmazást a következő lépésekkel:

1. Nyissa meg az **Új projekt** párbeszédpanelt (az**Új** >  **projekt fájlból...** > ).**Project...**
2. Bontsa ki a **Visual C# csomópontot,** és válassza a **Web**lehetőséget.
3. Válassza **ASP.NET webalkalmazás (.NET Framework) lehetőséget.**
4. Töltse ki a projekt nevét. Ezt követően kattintson az **OK** gombra.
5. Válassza ki az **Azure Mobile App** a sablonok listájából.
6. A megoldás létrehozásához kattintson az **OK** gombra.
7. Kattintson a jobb gombbal a projektre a **Megoldáskezelőben,** és válassza a **Közzététel...** parancsot, majd válassza az **App Service** parancsot közzétételi célként.
8. Kövesse a hitelesítéshez szükséges utasításokat, és válasszon egy új vagy meglévő Azure App Service közzétételre.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>.NET háttérkapcsolat létrehozása a Visual Studio 2015-tel

Telepítse az [Azure SDK-t a .NET-hez][4] (2.9.0-s vagy újabb verzió) egy Azure Mobile Apps-projekt létrehozásához a Visual Studióban. Az SDK telepítése után hozzon létre egy ASP.NET alkalmazást a következő lépésekkel:

1. Nyissa meg az **Új projekt** párbeszédpanelt (az**Új** >  **projekt fájlból...** > ).**Project...**
2. Bontsa ki **a Templates** > **Visual C#** csomópontot, és válassza a **Web**lehetőséget.
3. Válassza az **ASP.NET Web Application** (ASP.NET-es webalkalmazás) lehetőséget.
4. Töltse ki a projekt nevét. Ezt követően kattintson az **OK** gombra.
5. A *ASP.NET 4.5.2-es sablonok csoportban*válassza az **Azure Mobile App**lehetőséget. Ellenőrizze **a felhőben lévő állomást,** hogy hozzon létre egy mobil háttérszolgáltatást a felhőben, amelyhez közzéteheti ezt a projektet.
6. Kattintson az **OK** gombra.

## <a name="how-to-download-and-initialize-the-sdk"></a><a name="install-sdk"></a>Útmutató: Az SDK letöltése és inicializálása
Az SDK a [NuGet.org.] Ez a csomag tartalmazza az SDK használatának megkezdéséhez szükséges alapfunkciókat. Az SDK inicializálásához műveleteket kell végrehajtania a **HttpConfiguration** objektumon.

### <a name="install-the-sdk"></a>Az SDK telepítése
Az SDK telepítéséhez kattintson a jobb gombbal a Visual Studio kiszolgálóprojektjére, válassza **a NuGet-csomagok kezelése parancsot,** keresse meg a [Microsoft.Azure.Mobile.Server] csomagot, majd kattintson a **Telepítés parancsra.**

### <a name="initialize-the-server-project"></a><a name="server-project-setup"></a>A kiszolgálóprojekt inicializálása
A .NET háttérkiszolgáló-projektek más ASP.NET projektekhez hasonlóan inicializálódnak egy OWIN indítási osztály segítségével. Győződjön meg arról, hogy `Microsoft.Owin.Host.SystemWeb`hivatkozott a NuGet csomagra. Ha ezt az osztályt hozzá szeretné adni a Visual Studióba, kattintson a jobb gombbal a kiszolgálóprojektre, és válassza az**Új elem** **hozzáadása** >
parancsot, majd a **Web** > **General** > **OWIN startup osztályt.**  A következő attribútummal jön létre egy osztály:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Az `Configuration()` OWIN indítási osztályának metódusában használjon **httpkonfigurációs** objektumot az Azure Mobile Apps környezet konfigurálásához.
A következő példa további szolgáltatások nélkül inicializálja a kiszolgálóprojektet:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Az egyes szolgáltatások engedélyezéséhez az **ApplyTo**hívás előtt meg kell hívnia a **MobileAppConfiguration** objektum bővítménymetódusait. A következő kód például hozzáadja az alapértelmezett útvonalakat az `[MobileAppController]` összes olyan API-vezérlőhöz, amely rendelkezik az attribútummal az inicializálás során:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Az Azure Portalkiszolgáló rövid útmutatója **a UseDefaultConfiguration()**. Ez a következő beállításnak felel meg:

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

Az alkalmazott kiterjesztési módszerek a következők:

* `AddMobileAppHomeController()`az Azure Mobile Apps alapértelmezett kezdőlapját biztosítja.
* `MapApiControllers()`Egyéni API-funkciókat biztosít az `[MobileAppController]` attribútummal díszített WebAPI-vezérlők számára.
* `AddTables()`a `/tables` végpontok táblázatvezérlőkhöz való hozzárendelését biztosítja.
* `AddTablesWithEntityFramework()`a `/tables` végpontok entitáskeret-alapú vezérlők használatával történő leképezésének rövid keze.
* `AddPushNotifications()`egy egyszerű módszert biztosít az eszközök értesítési központokhoz való regisztrálására.
* `MapLegacyCrossDomainController()`szabványos CORS fejléceket biztosít a helyi fejlesztéshez.

### <a name="sdk-extensions"></a>SDK-bővítmények
A következő NuGet-alapú bővítménycsomagok különböző mobil funkciókat kínálnak, amelyeket az alkalmazás használhat. A **MobileAppConfiguration** objektum használatával engedélyezheti a bővítményeket az inicializálás során.

* [A Microsoft.Azure.Mobile.Server.Quickstart] támogatja az alapvető mobilalkalmazások telepítését. A konfigurációhoz hozzáadva a **UseDefaultConfiguration** bővítmény metódusának inicializálás során történő meghívásával. Ez a bővítmény a következő bővítményeket tartalmazza: Értesítések, Hitelesítés, Entitás, Táblák, Tartományok közötti és Otthoni csomagok. Ezt a csomagot az Azure Portalon elérhető mobilalkalmazások rövid útmutatója használja.
* [A Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) a *mobilalkalmazás* alapértelmezett, a webhely gyökéréhez létrehozott lapot valósítja meg. Adja hozzá a konfigurációhoz az **AddMobileAppHomeController** bővítmény metódusának hívásával.
* [A Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) az adatokkal való munka és az adatfolyamat beállítása osztályokat tartalmaz. Adja hozzá a konfigurációhoz az **AddTables** bővítmény metódusának hívásával.
* [A Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) lehetővé teszi, hogy az entitáskeretrendszer hozzáférjen az SQL-adatbázis adataihoz. Adja hozzá a konfigurációhoz az **AddTablesWithEntityFramework** bővítmény metódusának hívásával.
* [Microsoft.Azure.Mobile.Server.Authentication] Lehetővé teszi a hitelesítést, és beállítja a tokenek érvényesítéséhez használt OWIN köztes szoftvert. Adja hozzá a konfigurációhoz az **AddAppServiceAuthentication** és az **IAppBuilder**hívásával. **UseAppServiceAuthentication** bővítménymetódusok.
* [Microsoft.Azure.Mobile.Server.Notifications] Leküldéses értesítések engedélyezése, és meghatározza a leküldéses regisztrációs végpont. Adja hozzá a konfigurációhoz az **AddPushNotifications** bővítmény metódusának hívásával.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Létrehoz egy vezérlőt, amely a mobilalkalmazásból származó örökölt webböngészők adatait szolgálja ki. Adja hozzá a konfigurációhoz a **MapLegacyCrossDomainController** bővítmény metódusának hívásával.
* [Microsoft.Azure.Mobile.Server.Login] biztosítja az AppServiceLoginHandler.CreateToken() metódust, amely az egyéni hitelesítési forgatókönyvek során használt statikus módszer.

## <a name="how-to-publish-the-server-project"></a><a name="publish-server-project"></a>Útmutató: A kiszolgálóprojekt közzététele
Ez a szakasz bemutatja, hogyan teheti közzé a .NET háttérprojektet a Visual Studióból. A háttérprojektet [git](../app-service/deploy-local-git.md) vagy az ott elérhető bármely más módszer használatával is üzembe helyezheti.

1. A Visual Studióban építse újra a projektet a NuGet-csomagok visszaállításához.
2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson a **Közzététel parancsra.** Az első közzétételkor meg kell határoznia egy közzétételi profilt. Ha már definiált egy profilt, kijelölheti azt, és a **Közzététel gombra**kattintva.
3. Ha a rendszer megkéri, hogy válasszon ki egy közzétételi célt, kattintson a **Microsoft Azure App Service** > **Next**elemre, majd (ha szükséges) jelentkezzen be az Azure-hitelesítő adataival.
   A Visual Studio közvetlenül az Azure-ból tölti le és tárolja biztonságosan a közzétételi beállításokat.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Válassza ki az **előfizetést,** válassza **az Erőforrás típusát** a **Nézet**ből , bontsa ki a **Mobilalkalmazás**csomópontot, majd kattintson a mobilalkalmazás-háttértartalékra, majd az **OK**gombra.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Ellenőrizze a közzétételi profil adatait, és kattintson **a Közzététel gombra.**

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Ha a mobilalkalmazás-háttérkapcsolat sikeresen megjelent, megjelenik egy sikeres céloldal.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="how-to-define-a-table-controller"></a><a name="define-table-controller"></a>Útmutató: Táblavezérlő definiálása
Táblázatvezérlő definiálása az SQL-tábla mobilügyfelek számára való elérhetővé tételéhez.  A táblavezérlő konfigurálásához három lépés szükséges:

1. Adatátviteli objektum (DTO) osztály létrehozása.
2. Állítson be egy táblahivatkozást a Mobile DbContext osztályban.
3. Hozzon létre egy táblavezérlőt.

Az adatátviteli objektum (DTO) egy egyszerű C# `EntityData`objektum, amelytől öröklődik.  Példa:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

A DTO az SQL-adatbázistáblájának definiálására szolgál.  Az adatbázisbejegyzés létrehozásához `DbSet<>` vegyen fel egy tulajdonságot a használt DbContext környezetbe.  Az Azure Mobile Apps alapértelmezett projektsablonjában a `Models\MobileServiceContext.cs`DbContext neve:

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

Ha telepítve van az Azure SDK, most már létrehozhat egy sablontábla-vezérlőt az alábbiak szerint:

1. Kattintson a jobb gombbal a Vezérlők mappára, és válassza a > **Vezérlő** **hozzáadása...** lehetőséget.
2. Válassza az Azure Mobile Apps table controller (Bővítmény) **lehetőséget,** majd kattintson a **Hozzáadás gombra.**
3. A **Vezérlő hozzáadása** párbeszédpanelen:
   * A **Modell osztály** legördülő menüben válassza ki az új DTO-t.
   * A **DbContext** legördülő menüben válassza ki a Mobile Service DbContext osztályt.
   * A vezérlő név jön létre az Ön számára.
4. Kattintson a **Hozzáadás** gombra.

A rövidútmutató kiszolgáló projekt egy egyszerű **TodoItemController**példát tartalmaz.

### <a name="how-to-adjust-the-table-paging-size"></a><a name="adjust-pagesize"></a>Útmutató: A táblázat lapozási méretének beállítása
Alapértelmezés szerint az Azure Mobile Apps kérésenként 50 rekordot ad vissza.  A lapozás biztosítja, hogy az ügyfél ne kösse le túl sokáig a felhasználói felület szálát, sem a kiszolgálót, így biztosítva a jó felhasználói élményt. A táblalapozás méretének módosításához növelje a kiszolgálóoldali "engedélyezett lekérdezési méretet" és az ügyféloldali `EnableQuery` oldalméretet A kiszolgálóoldali "engedélyezett lekérdezési méret" az attribútum használatával módosul:

    [EnableQuery(PageSize = 500)]

Győződjön meg arról, hogy a PageSize azonos vagy nagyobb, mint az ügyfél által kért méret.  Az ügyféloldal méretének módosításával kapcsolatos részletekért tekintse meg az adott ügyfél HOWTO dokumentációját.

## <a name="how-to-define-a-custom-api-controller"></a>Útmutató: Egyéni API-vezérlő definiálása
Az egyéni API-vezérlő biztosítja a legalapvetőbb funkciókat a mobilalkalmazás-háttérrendszer számára egy végpont felfedésével. A [MobileAppController] attribútum használatával regisztrálhat egy mobilspecifikus API-vezérlőt. Az `MobileAppController` attribútum regisztrálja az útvonalat, beállítja a Mobilalkalmazások JSON-szerializálóját, és bekapcsolja az [ügyfélverzió-ellenőrzést.](app-service-mobile-client-and-server-versioning.md)

1. A Visual Studio programban kattintson a jobb gombbal a Vezérlők mappára, majd kattintson a**Vezérlő** **hozzáadása** > parancsra, válassza a Web API 2 **Controller&mdash;Empty** parancsot, majd kattintson a **Hozzáadás parancsra.**
2. Adja meg a vezérlő `CustomController` **nevét**, például a t, majd kattintson a **Hozzáadás**gombra.
3. Az új vezérlőosztályfájlban adja hozzá a következő utasítást:

        using Microsoft.Azure.Mobile.Server.Config;
4. Alkalmazza a **[MobileAppController]** attribútumot az API-vezérlő osztálydefiníciójára, ahogy az a következő példában is:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. A App_Start/Startup.MobileApp.cs fájlban adjon hozzá egy hívást a **MapApiControllers** bővítmény metódusához, ahogy az a következő példában is:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

A helyett a `UseDefaultConfiguration()` bővítménymetódust `MapApiControllers()`is használhatja. Minden olyan vezérlő, amely nem rendelkezik **MobileAppControllerAttribute** alkalmazott továbbra is elérhető az ügyfelek, de előfordulhat, hogy nem megfelelően használják fel az ügyfelek bármely Mobile App ügyfél SDK.

## <a name="how-to-work-with-authentication"></a>Útmutató: A hitelesítés használata
Az Azure Mobile Apps app szolgáltatás hitelesítése / engedélyezése a mobil háttérrendszer védelméhez.  Ez a szakasz a következő hitelesítéssel kapcsolatos feladatokat mutatja be a .NET háttérkiszolgáló-projektben:

* [Útmutató: Hitelesítés hozzáadása kiszolgálóprojekthez](#add-auth)
* [Útmutató: Egyéni hitelesítés használata az alkalmazáshoz](#custom-auth)
* [Útmutató: A hitelesített felhasználói adatok lekérése](#user-info)
* [Útmutató: Az adatokhoz való hozzáférés korlátozása a jogosult felhasználók számára](#authorize)

### <a name="how-to-add-authentication-to-a-server-project"></a><a name="add-auth"></a>Útmutató: Hitelesítés hozzáadása kiszolgálóprojekthez
A **mobileappkonfigurációs** objektum kiterjesztésével és az OWIN köztes szoftver konfigurálásával hitelesítést adhat a kiszolgálóprojekthez. Amikor telepíti a [Microsoft.Azure.Mobile.Server.Quickstart] csomagot, és meghívja a **UseDefaultConfiguration** bővítmény metódust, ugorjon a 3.

1. A Visual Studióban telepítse a [Microsoft.Azure.Mobile.Server.Authentication] csomagot.
2. A Startup.cs projektfájlban adja hozzá a következő kódsort a **Konfigurációs** módszer elejére:

        app.UseAppServiceAuthentication(config);

    Ez az OWIN köztes szoftver összetevő ellenőrzi a társított App Service-átjáró által kiadott jogkivonatokat.
3. Adja `[Authorize]` hozzá az attribútumot bármely hitelesítést igénylő vezérlőhöz vagy metódushoz.

Ha többet szeretne tudni arról, hogy miként hitelesítheti az ügyfeleket a mobilalkalmazások háttérrendszeréhez, olvassa el [a Hitelesítés hozzáadása az alkalmazáshoz című témakört.](app-service-mobile-ios-get-started-users.md)

### <a name="how-to-use-custom-authentication-for-your-application"></a><a name="custom-auth"></a>Útmutató: Egyéni hitelesítés használata az alkalmazáshoz
> [!IMPORTANT]
> Az egyéni hitelesítés engedélyezéséhez először engedélyeznie kell az App Service-hitelesítést anélkül, hogy az Azure Portalon szolgáltatót választana az Alkalmazásszolgáltatáshoz. Ez lehetővé teszi a WEBSITE_AUTH_SIGNING_KEY környezeti változó, ha a szolgáltatott.
> 
> 
> Ha nem kívánja használni az App Service hitelesítési/engedélyezési szolgáltatók egyikét, saját bejelentkezési rendszert valósíthat meg. Telepítse a [Microsoft.Azure.Mobile.Server.Login] csomagot a hitelesítési jogkivonat létrehozásához.  Adja meg saját kódját a felhasználói hitelesítő adatok érvényesítéséhez. Például előfordulhat, hogy egy adatbázisban ellenőrzi a sózott és kivonatolt jelszavakat. Az alábbi példában `isValidAssertion()` a (máshol meghatározott) módszer felelős ezekért az ellenőrzésekért.

Az egyéni hitelesítés elérhetővé teszi egy ApiController létrehozásával, és felfedése `register` és `login` műveletek. Az ügyfélnek egyéni felhasználói felületet kell használnia az adatok gyűjtésére a felhasználótól.  Az adatokat ezután egy szabványos HTTP POST-hívással küldik el az API-nak. Miután a kiszolgáló érvényesíti a feltételt, a rendszer egy jogkivonatot ad ki a `AppServiceLoginHandler.CreateToken()` metódus használatával.  Az ApiController **nem** `[MobileAppController]` használhatja az attribútumot.

`login` Példa:

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

Az előző példában a LoginResult és a LoginResultUser szerializálható objektumok, amelyek felfedik a szükséges tulajdonságokat. Az ügyfél várakozásai szerint a bejelentkezési válaszok az űrlap JSON-objektumaiként lesznek visszaküldve:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

A `AppServiceLoginHandler.CreateToken()` metódus egy *közönséget* és egy *kibocsátó* paramétert tartalmaz. Mindkét paraméter az alkalmazás gyökérurl-címére van beállítva a HTTPS-séma használatával. Hasonlóképpen be kell *állítania secretKey* az alkalmazás aláíró kulcsának értéke. Ne terjessze az aláíró kulcsot az ügyfélben, mert az a mentakulcsok hoz és a felhasználók megszemélyesítésére használható. Az aláíró kulcsot az App Service-ben üzemeltetve is beszerezheti a *WEBSITE\_AUTH\_SIGNING\_KEY* környezeti változóra hivatkozva. Ha helyi hibakeresési környezetben szükséges, kövesse a [Helyi hibakeresés hitelesítéssel](#local-debug) című szakasz utasításait a kulcs lekéréséhez és alkalmazásbeállításként való tárolásához.

A kibocsátott token tartalmazhat más jogcímeket és lejárati dátumot is.  A kiadott jogkivonatnak minimálisan tárgyi **(al)** jogcímet kell tartalmaznia.

A szabványos ügyfélmetódust `loginAsync()` a hitelesítési útvonal túlterhelésével támogathatja.  Ha az `client.loginAsync('custom');` ügyfél betelefonál, az `/.auth/login/custom`útvonalnak kell lennie.  Az egyéni hitelesítésvezérlő útvonalát a `MapHttpRoute()`következő vel állíthatja be:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> A `loginAsync()` megközelítés használatával biztosítja, hogy a hitelesítési jogkivonat csatlakozik a szolgáltatás minden további hívásához.
>
>

### <a name="how-to-retrieve-authenticated-user-information"></a><a name="user-info"></a>Útmutató: A hitelesített felhasználói adatok lekérése
Ha egy felhasználót az App Service hitelesít, hozzáférhet a hozzárendelt felhasználói azonosítóhoz és a .NET háttérkódban szereplő egyéb adatokhoz. A felhasználói adatok felhasználhatók a háttérrendszer engedélyezési döntéseihez. A következő kód beszerzi a kérelemhez társított felhasználói azonosítót:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

A biztonsági azonosító a szolgáltatóspecifikus felhasználói azonosítóból származik, és statikus egy adott felhasználó és bejelentkezési szolgáltató számára.  A biztonsági azonosító null értékű az érvénytelen hitelesítési jogkivonatok esetében.

Az App Service lehetővé teszi, hogy konkrét jogcímeket kérjen a bejelentkezési szolgáltatójától. Minden identitásszolgáltató további információkat adhat meg az SDK-azonosítószolgáltató használatával.  Használhatja például a Facebook Graph API-t az ismerősök adataihoz.  Megadhatja a szolgáltató panelen kért jogcímek az Azure Portalon. Egyes jogcímek további konfigurációt igényelnek az identitásszolgáltatóval.

A következő kód meghívja a **GetAppServiceIdentityAsync** bővítmény metódust a bejelentkezési hitelesítő adatok lekéréséhez, amelyek tartalmazzák a Facebook Graph API-val kapcsolatos kérelmek teljesítéséhez szükséges hozzáférési jogkivonatot:

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

Adjon hozzá egy `System.Security.Principal` using utasítást a **GetAppServiceIdentityAsync** bővítmény metódusának biztosításához.

### <a name="how-to-restrict-data-access-for-authorized-users"></a><a name="authorize"></a>Útmutató: Az adatokhoz való hozzáférés korlátozása a jogosult felhasználók számára
Az előző szakaszban bemutattuk, hogyan lehet lekérni egy hitelesített felhasználó felhasználói azonosítóját. Ezen érték alapján korlátozhatja az adatokhoz és más erőforrásokhoz való hozzáférést. Ha például egy userId oszlopot ad hozzá a táblákhoz, és a lekérdezés eredményeit a felhasználói azonosító alapján szűri, egyszerűen korlátozhatja a visszaadott adatokat csak a jogosult felhasználók számára. A következő kód csak akkor ad vissza adatsorokat, ha a sid megegyezik a TodoItem tábla UserId oszlopában szereplő értékkel:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

A `Query()` módszer `IQueryable` a LINQ által a szűrés kezeléséhez kezelhető értéket ad vissza.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Útmutató: Leküldéses értesítések hozzáadása kiszolgálóprojekthez
Push értesítések hozzáadása a kiszolgálóprojekthez a **MobileAppConfiguration** objektum kiterjesztésével és egy értesítési központ ügyfél létrehozásával.

1. A Visual Studio programban kattintson a jobb gombbal a kiszolgálóprojektre, és válassza **a NuGet csomagok kezelése**, a keresett témakört, `Microsoft.Azure.Mobile.Server.Notifications`majd kattintson a Telepítés **parancsra.**
2. Ismételje meg ezt `Microsoft.Azure.NotificationHubs` a lépést a csomag telepítéséhez, amely tartalmazza az Értesítési központok ügyfélkönyvtárát.
3. A App_Start/Startup.MobileApp.cs alkalmazásban, és az inicializálás során adjon hozzá egy hívást az **AddPushNotifications()** bővítménymetódushoz:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adja hozzá a következő kódot, amely létrehoz egy értesítési központ ügyfelet:

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

Most már használhatja az Értesítési központok ügyfél leküldéses értesítéseket a regisztrált eszközökre. További információkat az [Add push notifications to your app](app-service-mobile-ios-get-started-push.md) (Leküldéses értesítések hozzáadása az alkalmazáshoz) című témakörben találhat. Az Értesítési központokról az [Értesítési központok – áttekintés című témakörben olvashat bővebben.](../notification-hubs/notification-hubs-push-notification-overview.md)

## <a name="how-to-enable-targeted-push-using-tags"></a><a name="tags"></a>Útmutató: Célzott leküldéses leküldéses leküldéses címkék használatával
Az Értesítési központok lehetővé teszik, hogy címkék használatával célzott értesítéseket küldjön adott regisztrációknak. Több címke automatikusan jön létre:

* A telepítési azonosító egy adott eszközt azonosít.
* A hitelesített biztonsági azonosítón alapuló felhasználói azonosító egy adott felhasználót azonosít.

A telepítési azonosító a **MobileServiceClient** **installationId** tulajdonságából érhető el.  A következő példa bemutatja, hogyan lehet egy telepítési azonosító segítségével címkét hozzáadni egy adott telepítéshez az értesítési központokban:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Az ügyfél által a leküldéses értesítés regisztrációja során megadott címkéket a háttérrendszer figyelmen kívül hagyja a telepítés létrehozásakor. Ahhoz, hogy egy ügyfél címkéket adjon a telepítéshez, létre kell hoznia egy egyéni API-t, amely az előző minta használatával adja hozzá a címkéket.

Tekintse meg [az Ügyfél által hozzáadott leküldéses értesítési címkéket][5] az App Service Mobile Apps által elvégzett rövid útmutatóban egy példa.

## <a name="how-to-send-push-notifications-to-an-authenticated-user"></a><a name="push-user"></a>Útmutató: Leküldéses értesítések küldése hitelesített felhasználónak
Amikor egy hitelesített felhasználó leküldéses értesítéseket regisztrál, a rendszer automatikusan hozzáad egy felhasználói azonosító címkét a regisztrációhoz. Ezzel a címkével leküldéses értesítéseket küldhet az adott személy által regisztrált összes eszközre. A következő kód lekéri a kérelmet küldő felhasználó biztonsági azonosítóját, és egy sablonleküldéses értesítést küld az adott személy minden eszközregisztrációjára:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Amikor egy hitelesített ügyféltől származó leküldéses értesítéseket regisztrál, a regisztráció megkísérlése előtt győződjön meg arról, hogy a hitelesítés befejeződött. További információ: [Leküldéses a felhasználók nak][6] az App Service Mobile Apps befejeződött rövid útmutató minta .NET háttérrendszer.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Útmutató: A .NET Server SDK hibakeresése és hibaelhárítása
Az Azure App Service számos hibakeresési és hibaelhárítási technikát kínál ASP.NET alkalmazásokhoz:

* [Az Azure App Service figyelése](../app-service/web-sites-monitor.md)
* [Diagnosztikai naplózás engedélyezése az Azure App Service szolgáltatásban](../app-service/troubleshoot-diagnostic-logs.md)
* [Azure App Service hibáinak elhárítása a Visual Studióban](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Naplózás
Írhat az App Service diagnosztikai naplók a szabványos ASP.NET nyomkövetési írás. Mielőtt írhatna a naplókba, engedélyeznie kell a diagnosztikát a mobilalkalmazás-háttérrendszerben.

A diagnosztika engedélyezése és a naplókba való írás:

1. Kövesse az [Alkalmazásnaplózás engedélyezése (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)című részt.
2. Adja hozzá a következőket a kódfájlban lévő utasítás használatával:

        using System.Web.Http.Tracing;
3. Hozzon létre egy nyomkövetési írót, amelyet a .NET háttérrendszerből a diagnosztikai naplókba ír, az alábbiak szerint:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Tegye közzé újra a kiszolgálóprojektet, és férjen hozzá a mobilalkalmazás-háttérrendszerhez, hogy a naplózással végrehajtsa a kódelérési utat.
5. Töltse le és értékelje ki a naplókat az [Access naplófájljaiban leírtak](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)szerint.

### <a name="local-debugging-with-authentication"></a><a name="local-debug"></a>Helyi hibakeresés hitelesítéssel
Az alkalmazás helyi futtatásával tesztelheti a módosításokat, mielőtt közzétenné őket a felhőben. A legtöbb Azure Mobile Apps-háttérrendszeren nyomja le *az F5* billentyűt a Visual Studióban. A hitelesítés használata során azonban további szempontok is felmerülnek.

Rendelkeznie kell egy felhőalapú mobilalkalmazás app service hitelesítése/engedélyezése konfigurálva, és az ügyfél kell rendelkeznie a felhővégpont van megadva, mint a másodlagos bejelentkezési gazdagép. A szükséges lépésekről az ügyfélplatform dokumentációjában olvashat.

Győződjön meg arról, hogy a mobil háttérrendszer rendelkezik [a Microsoft.Azure.Mobile.Server.Authentication] telepítve. Ezután az alkalmazás OWIN indítási osztályában adja `MobileAppConfiguration` hozzá a következőket, miután alkalmazták a következőt: `HttpConfiguration`

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Az előző példában konfigurálja az *authAudience* és *authIssuer* alkalmazás beállításait a Web.config fájlban, hogy mindegyik az alkalmazás gyökér URL-címét használja a HTTPS-séma használatával. Hasonlóképpen be kell *állítania authSigningKey* az alkalmazás aláíró kulcsának értéke.
Az aláíró kulcs beszerzése:

1. Keresse meg az alkalmazást az [Azure Portalon] belül
2. Kattintson **az Eszközök**, **Kudu**, **Go**.
3. A Kudu Management webhelyen kattintson a **Környezet gombra.**
4. Keresse meg a *\_WEBSITE\_\_AUTH ALÁÍRÓ KULCS értékét.*

Használja az aláíró kulcsot az *authSigningKey* paraméterhez a helyi alkalmazás konfigurációjában.  A mobil háttérrendszer most már fel van szerelve a jogkivonatok érvényesítéséhez helyi futtatásakor, amely az ügyfél beszerzi a jogkivonatot a felhőalapú végpontról.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portál]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Hitelesítés]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Bejelentkezés]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Értesítések]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
