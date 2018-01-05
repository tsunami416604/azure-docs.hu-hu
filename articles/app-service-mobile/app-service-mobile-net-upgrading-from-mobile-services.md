---
title: "Frissítés a Mobile Services az Azure App Service"
description: "Ismerje meg a Mobile Services alkalmazás az App Service Mobile Apps könnyen frissítése"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f07b1d6037ff8ca16b673e6a1a235769355a9993
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Frissítse a meglévő .NET Azure Mobile szolgáltatást az App Service
App Service Mobile új módja a Microsoft Azure használatával mobil alkalmazásokat hozhatnak létre. További tudnivalókért lásd: [Mik azok a Mobile Apps?].

Ez a témakör ismerteti az Azure Mobile Services .NET háttérrendszer meglévő alkalmazás frissítése egy új App Service Mobile Apps. Ez a frissítés végrehajtása során a meglévő Mobile Services alkalmazás továbbra is működik.   Ha frissítenie kell a Node.js a háttéralkalmazás, tekintse meg a [a Node.js Mobile Services frissítése](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

A mobil-háttéralkalmazást frissítésekor az Azure App Service alkalmazás szolgáltatás minden funkcióját hozzáféréssel rendelkezik, és számlázása a következők szerint [App Service díjszabás], nem a Mobile Services díjszabása.

## <a name="migrate-vs-upgrade"></a>Frissítése és áttelepítése
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Javasoljuk, hogy [áttelepítés](app-service-mobile-migrating-from-mobile-services.md) áthaladás a frissítés előtt. Ezzel a módszerrel után az alkalmazás mindkét verziója helyezhető el a azonos App Service-csomag is fel Önnek további költség nélkül.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>A Mobile Apps .NET server SDK fejlesztései
Az új frissítés [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) a következő előnyöket biztosítja:

* Nagyobb rugalmasság a NuGet-függőségek. Az üzemeltetési környezetben már nem biztosítja a saját NuGet-csomagok verziói, így alternatív kompatibilis verziója is használhatja. Azonban ha vannak új kritikus bugfixes vagy biztonsági frissítéseket, hogy a Mobile Server SDK-t vagy a függőségeket, kézzel kell frissítenie a szolgáltatáshoz.
* A mobilalkalmazás SDK nagyobb rugalmasság. Explicit módon szabályozhatja a fejlesztendő funkciók és a útvonalakat, például a hitelesítés, tábla API-k és a leküldéses regisztrációs végpont. További tudnivalókért lásd: [használata a .NET-kiszolgáló SDK az Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Más típusú ASP.NET-projekt és az útvonalak támogatása. Üzemeltethető MVC és a Web API vezérlők, a mobil háttéralkalmazás-projekt ugyanabban a projektben.
* Új App Service hitelesítési szolgáltatások, amelyek lehetővé teszik, hogy egy közös hitelesítési konfigurációt használja a webes és mobilalkalmazások támogatása.

## <a name="overview"></a>Alapszintű frissítési áttekintése
Sok esetben frissítése lesz más dolga, mint az új Mobile Apps SDK kiszolgálóra, és a közzététel a kódot egy új Mobile Apps-példányt. Vannak, azonban néhány olyan forgatókönyvet, amely további konfigurálást, például a speciális hitelesítési forgatókönyvek és a szükséges ütemezett feladatokat. Ezen a későbbi szakaszokban vonatkozik.

> [!TIP]
> Javasoljuk, hogy olvassa el, és ez a témakör hátralévő része teljesen megérteni a frissítés megkezdése előtt. Jegyezze fel minden funkcióját használja alatt elnevezése.
>
>

A Mobile Services-ügyfél SDK-k vannak **nem** kompatibilis az új Mobile Apps SDK kiszolgálóval. Adja meg az alkalmazás szolgáltatási folytonosság érdekében, hogy nem módosításokat kell közzétenni, a hely jelenleg a közzétett ügyfelek szolgál. Ehelyett kell hozzon létre egy új mobil-alkalmazást, amely duplikált funkcionál. Ez az alkalmazás a járulékos költségekkel megcélzásával elkerülheti az azonos App Service-csomag helyezhet el.

Ezután meg kell az alkalmazás két verziója: a helyettesítő és egyéb, amely ezután frissítheti és egy új ügyfél kiadással cél olyanra, amelyben ugyanaz marad, és működik közzétett alkalmazást. Helyezze át, és tesztelheti a kódját a ütemben, de meg kell győződnie arról, hogy bármely hibajavításokat tartalmaz, akkor mindkét érvényben. Ha úgy véli, hogy a helyettesítő alkalmazásokat a legújabb verzióra frissítette ügyfél kívánt számát, törölheti az eredeti áttelepített alkalmazás ha felügyelni.

A teljes vázlat a frissítési folyamat a következőképpen történik:

1. Új mobileszköz-alkalmazás létrehozása
2. Frissítés a projektet az új kiszolgáló SDK-k használata
3. Az ügyfél alkalmazás új verzióját
4. (Választható) Az eredeti áttelepített példány törlése

## <a name="mobile-app-version"></a>Egy második alkalmazáspéldány létrehozása
Az első lépés frissítése, ha a mobilalkalmazás erőforrás futtatni az alkalmazás új verzióját. Ha egy meglévő mobilszolgáltatás már áttelepített, célszerű ebben a verzióban az azonos üzemeltetési terv létrehozásához. Nyissa meg a [Azure-portálon] , és keresse meg az áttelepített alkalmazás. Jegyezze fel az App Service-csomag futtatása.

Következő lépésként hozzon létre a második alkalmazáspéldány a [.NET háttérrendszer létrehozásának utasításokat](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Válassza ki a üzenet megjelenésekor a App Service-csomag vagy a "üzemeltetési terv" az áttelepített alkalmazás a csomag kiválasztása.

Valószínűleg érdemes használni a ugyanazt az adatbázist és az értesítési központ, mint a Mobile Services. Ezek az értékek másolásához megnyitása [Azure-portálon] és az eredeti alkalmazásának megnyitásával, majd kattintson a **beállítások** > **Alkalmazásbeállítások**. A **kapcsolati karakterláncok**, másolása `MS_NotificationHubConnectionString` és `MS_TableConnectionString`. Keresse meg az új hely frissítése, és illessze be őket, felülírja a meglévő értékkel. Ismételje meg ezt a folyamatot minden más alkalmazás beállításait az alkalmazás igényeinek. Ha nem használ az áttelepített szolgáltatás, a kapcsolati karakterláncokat és az alkalmazásbeállítások olvasható a **konfigurálása** a Mobile Services szakaszának lapon a [a klasszikus Azure portálon].

Másolatot készít az ASP.NET-projekt az alkalmazáshoz, és tegye közzé az új hely. Egy másolatot az ügyfél frissíti az új URL-címet használ, ellenőrizze, hogy minden megfelelően működik-e.

## <a name="updating-the-server-project"></a>A kiszolgáló projekt frissítése
Mobile Apps biztosít egy új [Mobile App SDK-t Server] nagy részét a Mobile Services futásidejű azonos funkciókat biztosító. Először el kell távolítania a Mobile Services csomagok mutató összes hivatkozást. A NuGet-csomagkezelőjét, keresse meg a `WindowsAzure.MobileServices.Backend`. Legtöbb alkalmazást megjelenik itt, több csomagot, beleértve a `WindowsAzure.MobileServices.Backend.Tables` és `WindowsAzure.MobileServices.Backend.Entity`. Ebben az esetben indítsa el a legalacsonyabb csomag függőségi fa például `Entity`, és távolítsa el. Amikor a rendszer kéri, ne távolítsa el az összes függő csomagot. Ismételje meg a folyamatot, amíg nem távolította el `WindowsAzure.MobileServices.Backend` magát.

Ezen a ponton a projekt, amely már nem hivatkozik a Mobile Services SDK-k fog.

Ezután adhat a Mobile Apps SDK hivatkozik. Ehhez a frissítéshez legtöbb fejlesztő érdemes töltse le és telepítse a `Microsoft.Azure.Mobile.Server.Quickstart` csomagot, mivel ez fogja lekérni a teljes szükséges készletében.

Az SDK-k közötti különbségek eredő számos fordítóprogram-hibákkal lesz, de ezek egyszerű címre, és a többi ebben a szakaszban ismertetett.

### <a name="base-configuration"></a>Alapkonfiguráció
Ezt követően WebApiConfig.cs, módosíthatja:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

együtt

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Ha szeretne Útmutató: további információk az új kiszolgáló .NET SDK és funkciók hozzáadása/eltávolítása az alkalmazásból, olvassa el a [használata a .NET SDK server] témakör.
>
>

Ha az alkalmazás lehetővé teszi a hitelesítési szolgáltatások használatához is szüksége lesz egy OWIN köztes regisztrálni. Ebben az esetben a fenti konfigurációs kódot kell áthelyezi egy új OWIN indítási osztályt.

1. Adja hozzá a NuGet-csomag `Microsoft.Owin.Host.SystemWeb` Ha már nincs belefoglalva a projektben.
2. A Visual Studióban, kattintson a jobb gombbal a projekt, és válassza ki **Hozzáadás** -> **új elem**. Válassza ki **webes** -> **általános** -> **OWIN indítási osztály**.
3. Helyezze el a fenti kódot a MobileAppConfiguration a `WebApiConfig.Register()` számára a `Configuration()` az új indítási osztály.

Győződjön meg arról, hogy a `Configuration()` metódus végződik:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Hitelesítéshez kapcsolódó alábbi teljes hitelesítési szakasz megakadályozására szolgáló további módosítások vannak.

### <a name="working-with-data"></a>Adatok használata
A Mobile Services szolgáltatásban az a mobilalkalmazás nevét, az alapértelmezett séma nevét az Entity Framework beállítása a szolgáltatott.

Annak érdekében, hogy rendelkezik-e az azonos sémából hivatkozott előtti, az alkalmazás DbContext beállítani a séma a következő használata:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Győződjön meg arról, hogy MS_MobileServiceName állítja be, ha a fenti teheti meg. Ha az alkalmazás korábban testreszabott ez is megadhatja egy másik sémanév.

### <a name="system-properties"></a>Rendszertulajdonságok
#### <a name="naming"></a>Elnevezés
Az Azure Mobile Services-kiszolgáló SDK, a rendszer tulajdonságai mindig tartalmaznak dupla aláhúzás (`__`) a Tulajdonságok előtag:

* __createdAt
* __updatedAt
* __deleted
* __version

A Mobile Services-ügyfél SDK-k különleges logikát elemzésekor a rendszer tulajdonságai ebben a formátumban van.

Az Azure Mobile Apps már nem a rendszer tulajdonságai egy különleges formátumú névvel rendelkeznek, és a következő:

* createdAt
* updatedAt
* törölve
* verzió:

A Mobile Apps-ügyfél SDK-k az új tulajdonságok neveket, használ, így nem végez módosítást Ügyfélkód szükséges. Azonban ha a szolgáltatás REST-hívások közvetlenül teszi majd módosítania kell a lekérdezések ennek megfelelően.

#### <a name="local-store"></a>Helyi tárolóból.
A módosítások a rendszer tulajdonságainak nevében jelenti azt, hogy egy kapcsolat nélküli szinkronizálás a Mobile Services helyi adatbázis nem kompatibilis a Mobile Apps. Ha lehetséges ne alkalmazások a Mobile Services után csak a Mobile Apps függőben lévő módosítások a kiszolgálóra küldött ügyfél frissítése. A frissített alkalmazás ezt követően egy új adatbázis fájlneve kell használni.

Ha egy ügyfélalkalmazás nem frissíti a Mobile Services Mobile Apps amíg vannak függőben lévő módosítások offline állapotban a művelet sorban, majd a rendszer adatbázis frissíteni kell az új oszlopnevek használatára. IOS rendszerű eszközökön ez elérhető egyszerűsített áttelepítések használatával módosíthatja az oszlopok neveit. Android és a felügyelt .NET-ügyfélen az adatok objektum táblák oszlopait átnevezése egyéni SQL kell írni.

IOS rendszerű eszközökön módosítania kell a Core adatsémája nem támogatott az adatok entitások megfelelően a következő. Vegye figyelembe, hogy a Tulajdonságok `createdAt`, `updatedAt` és `version` már nem rendelkezik egy `ms_` előtagja:

| Attribútum | Típus | Megjegyzés |
| --- | --- | --- |
| id |Karakterlánc, kötelezőként megjelölt |elsődleges távoli tárolóban levő kulccsal. |
| createdAt |Dátum |(választható) képeződik le createdAt rendszer tulajdonság |
| updatedAt |Dátum |(választható) képeződik le updatedAt rendszer tulajdonság |
| verzió: |Karakterlánc |(választható) annak észlelésére használnak, ütközéseket, maps verzióra |

#### <a name="querying-system-properties"></a>A Rendszertulajdonságok lekérdezése
Azure Mobile Services, a rendszer tulajdonságai nem kap alapértelmezés szerint, de csak akkor, amikor erre felkérést kapnak a lekérdezési karakterlánc használatával `__systemProperties`. Ezzel szemben az Azure Mobile Apps rendszer tulajdonságainak vannak **mindig ki** , mert a kiszolgáló SDK objektummodell részét képezik.

Ez a változás főként hatással van a tartomány-kezelő bővítményének egyéni implementációja `MappedEntityDomainManager`. A Mobile Services szolgáltatásban, ha egy ügyfél soha nem kér a rendszer tulajdonságai is lehet használni egy `MappedEntityDomainManager` , amelyek nem tényleges képezze le az összes tulajdonságait. Azonban az Azure Mobile Apps, a nem leképezett tulajdonságok hibát okoz a GET-lekérdezésekben.

A probléma megoldásához legegyszerűbb módja a a DTOs módosítása, hogy azok örökölhet `ITableData` helyett `EntityData`. Adja hozzá a `[NotMapped]` attribútumot a mezőket, amelyeknek megadni.

Például a következő meghatározása `TodoItem` rendszer tulajdonságok nélkül:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Megjegyzés: Ha hibaüzenet jelenik meg `NotMapped`, adjon hozzá egy hivatkozást a szerelvényre `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services használatával az ASP.NET a CORS-megoldás néhány CORS támogatása tartalmazza. Az alkalmazásburkoló réteg el lett távolítva, így a fejlesztők további szabályozhatja, így közvetlenül hasznosíthatja [ASP.NET CORS-támogatás](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

A fő a problémás területeket. Ha a CORS használatával, amelyek a `eTag` és `Location` fejlécek engedélyezni kell ahhoz, hogy az ügyfél SDK-k megfelelően működjön.

### <a name="push-notifications"></a>Leküldéses értesítések
Leküldéses azt tapasztalhatja hiányzik a kiszolgáló SDK fő elemet a PushRegistrationHandler osztály esetén. Regisztráció a Mobile Apps kicsit másképp kezeli, és tagless regisztrációk alapértelmezés szerint engedélyezve vannak. Címkék kezelése végezhető egyéni API-k használatával. Tekintse meg a [címkék regisztrálása](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) olvashat útmutatást.

### <a name="scheduled-jobs"></a>Ütemezett feladatok
Ütemezett feladatok nem beépített mobileszköz-alkalmazások esetén, külön-külön kell frissíteni kell a .NET-háttérrendszer található meglévő feladatokat. Egy lehetőség létrehozni egy ütemezett [webes projekt] a Mobile Apps kód helyen. Is képes állítson be egy tartományvezérlőre, amely tárolja a feladat kódot, és konfigurálja a [Azure Scheduler] érni az adott végpontra a várt ütemezés szerint.

### <a name="miscellaneous-changes"></a>Egyéb változások
Most már rendelkeznie kell az összes ApiControllers, amely egy mobil ügyfél fognak használni a `[MobileAppController]` attribútum. Ez már nem veszi fel alapértelmezés szerint így a többi ApiControllers, hogy nyissa meg a mobil is érinti.

A `ApiServices` objektum már nem része az SDK-t. Mobilalkalmazás beállítások eléréséhez a következőket használhatja:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Hasonlóképpen naplózási most teszi lehetővé az ASP.NET nyomkövetési írását normál:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Hitelesítési kapcsolatos szempontok
A Mobile Services hitelesítés összetevői most helyezte el azokat a hitelesítési/engedélyezési App Service szolgáltatást. Ez a webhely engedélyezéséhez olvasásával megismerheti a [hitelesítés hozzáadása a mobilalkalmazás](app-service-mobile-ios-get-started-users.md) témakör.

Az egyes szolgáltatók – például az aad-ben, a Facebook-on és a Google kihasználhatják a meglévő regisztrációs a másolási alkalmazásból kell lennie. Egyszerűen lépjen az identitásszolgáltató portálra, és egy új átirányítási URL-cím hozzáadása a regisztrációt. App Service hitelesítési/engedélyezési konfigurálja az ügyfél-azonosító és a titkos kulcsot.

### <a name="controller-action-authorization"></a>A tartományvezérlő műveleti engedélyezési
Minden példányát a `[AuthorizeLevel(AuthorizationLevel.User)]` attribútum most meg kell változtatni a szabványos ASP.NET használatának `[Authorize]` attribútum. Emellett tartományvezérlők, névtelen alapértelmezett, mint más ASP.NET-alkalmazások.
Ha használta az más AuthorizeLevel beállítások, például a rendszergazda vagy az alkalmazás egyik vegye figyelembe, hogy ezek elvesznek. Helyezze a közös titkokat AuthorizationFilters beállítása, vagy konfigurálása az AAD szolgáltatás egyszerű szolgáltatások közötti hívások biztonságosan engedélyezéséhez.

### <a name="getting-additional-user-information"></a>További felhasználói adatainak lekérése
További felhasználói információkat, beleértve a hozzáférési jogkivonatok használatával kaphat a `GetAppServiceIdentityAsync()` módszert:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Továbbá ha az alkalmazás függőségek tart a felhasználói azonosítót, például az adatbázisban tárolja őket, fontos megjegyezni, hogy a felhasználói azonosítók között a Mobile Services és az App Service Mobile Apps különböző. A Mobile Services felhasználói Azonosítóját, azonban továbbra is megjelenik. A UserId tulajdonság valamennyi ProviderCredentials alosztályát rendelkezik. Így a folytatás előtt példa:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Ha az alkalmazás végrehajtása függőségek, a felhasználói azonosítót, akkor fontos, hogy is használja ugyanazt a regisztrációt egy identitásszolgáltatóval Ha lehetséges. Felhasználói azonosítók általában hatóköre az alkalmazás regisztrációja használt, így vezet be, egy új regisztrációs problémák felhasználók számára az adatok megfelelő sikerült létrehozni.

### <a name="custom-authentication"></a>Egyéni hitelesítési
Ha az alkalmazás egy egyéni hitelesítési megoldás használja, érdemes győződjön meg arról, hogy a frissített hely rendelkezik-e a hozzáférést a rendszerhez. Az egyéni hitelesítési új kövesse a [.NET SDK – áttekintés] integrálható a megoldás. Vegye figyelembe, hogy az egyéni hitelesítési összetevők továbbra is a képen vannak-e.

## <a name="updating-clients"></a>Ügyfelek frissítése
Miután egy operatív mobil-háttéralkalmazást, használhat az ügyfélalkalmazást, amely használ fel az új verziója. Mobile Apps is az ügyfél SDK-k új verzióját tartalmazza, és a kiszolgáló frissítése újabb hasonlóan kell távolítson el minden hivatkozást a Mobile Services SDK-k a Mobile Apps-verzió telepítése előtt.

A verziók közötti fő módosítások egyike, hogy a konstruktor már nem szükséges egy alkalmazás-kulcsot. Most már egyszerűen adja meg a Mobile Apps URL-címét. Például a .NET-ügyfelek a `MobileServiceClient` konstruktor:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Az új SDK-k telepítéséről és használatáról az új struktúra keresztül a lenti hivatkozásokra kattintva olvashat:

* [iOS-verzióval 3.0.0 vagy újabb verzió](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) 2.0.0 verzió vagy újabb verzió](app-service-mobile-dotnet-how-to-use-client-library.md)

Ha az alkalmazás lehetővé teszi leküldéses értesítések használni, jegyezze fel a regisztráció utasítás minden egyes platformhoz, néhány módosítás történt hiba is.

Ha készen áll az új ügyfél verziója, próbálja ki a frissített kiszolgálón projekthez. Ellenőrzése, hogy működik-e, miután az ügyfél számára az alkalmazás egy új verziója is megjelenhetnek. Előfordulhat Ha az ügyfelek rendelkezésére állt-e arra, hogy ezek a frissítések kap, törölheti a Mobile Services, verziószám: az alkalmazás. Ezen a ponton teljesen frissítését egy App Service Mobile Apps a legújabb Mobile Apps-kiszolgáló SDK használatával.

<!-- URLs. -->

[Azure-portálon]: https://portal.azure.com/
[a klasszikus Azure portálon]: https://manage.windowsazure.com/
[Mik azok a Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App SDK-t Server]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[webes projekt]: https://github.com/Azure/azure-webjobs-sdk/wiki
[használata a .NET SDK server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service díjszabás]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET SDK – áttekintés]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
