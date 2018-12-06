---
title: Frissítés a Mobile Services, Azure App Service-ben
description: A Mobile Services-alkalmazás egy App Service Mobile Apps könnyen frissítésének ismertetése
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 4fd896e3b26ae66621ba49d1bb8a5d86c0dd63ee
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963266"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>App Service-ben a .NET Azure meglévő Mobile Service frissítése
Az App Service Mobile egy új módja a Microsoft Azure mobile alkalmazásokat hozhat létre. További tudnivalókért lásd: [Mi a Mobile Apps szolgáltatás?].

Ez a témakör ismerteti az Azure Mobile Services meglévő .NET-háttérrendszer alkalmazás frissítése egy új App Service Mobile Apps. Ez a frissítés hajt végre, amíg a meglévő Mobile Services-alkalmazás továbbra is működnek.   Ha a Node.js-háttérrendszer alkalmazásokat frissíteni szeretne, olvassa el [a Node.js a Mobile Services frissítésére](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

A mobil háttérrendszer frissítésekor az Azure App Service minden App Service szolgáltatás hozzáfér, és a következők szerint számlázzuk [App Service szolgáltatás díjszabása], nem a Mobile Services díjszabása.

## <a name="migrate-vs-upgrade"></a>Frissítése és migrálása
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Javasoljuk, hogy Ön [az áttelepítés](app-service-mobile-migrating-from-mobile-services.md) előtt frissítés alatt áll. Ezzel a módszerrel is helyezze az alkalmazás mindkét verziója azonos App Service-csomagot, és további használatáért nem számítunk fel.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>A Mobile Apps .NET kiszolgálói SDK fejlesztései
Az új verzióra [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) az alábbi előnyöket nyújtja:

* Nagyobb rugalmasság a NuGet-függőségek. Az üzemeltetési környezet már nem biztosít a saját NuGet-csomagok verzióit, így alternatív kompatibilis verziója. Ugyanakkor ha vannak új kritikus bugfixes vagy biztonsági frissítéseket, a Mobile SDK-kiszolgáló vagy a függőségek, kézzel kell frissítenie a szolgáltatás.
* Nagyobb rugalmasság a mobile SDK-ban. Explicit módon szabályozhatja, hogy mely funkciók és útvonalak be vannak állítva, mint a hitelesítés, a table API-k és a leküldéses regisztrációs végpont. További tudnivalókért lásd: [a .NET kiszolgálóoldali SDK használata az Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Más típusú ASP.NET-projekt és útvonalak támogatása. Most már tárolhat MVC és a webes API vezérlő ugyanabban a projektben a mobil háttérszolgáltatásban projektként.
* Új App Service hitelesítési szolgáltatások, amelyek lehetővé teszik, hogy egy közös hitelesítési konfigurációt használja a webes és mobilalkalmazások támogatása.

## <a name="overview"></a>Alapszintű az alkalmazásfrissítés áttekintése
Sok esetben a frissítés lesz egyszerűen átvált az új Mobile Apps-kiszolgálói SDK és a kód egy új Mobile Apps-példány az alakzatot újbóli közzétételét. Vannak azonban bizonyos forgatókönyvek, melyek például speciális hitelesítési forgatókönyvek és a további konfigurációra van szükség az ütemezett feladatok. Ezek mindegyike a későbbi szakaszokban foglalkozik.

> [!TIP]
> Ezért javasoljuk, hogy Ön elolvassa és a témakör további részében teljesen tisztában a frissítés megkezdése előtt. Jegyezze fel minden funkcióját használja alábbi kiemeltük.
>
>

A Mobile Services ügyféloldali SDK-k vannak **nem** kompatibilis az új Mobile Apps-kiszolgálóval SDK-t. Annak érdekében, hogy az alkalmazás szolgáltatási folytonosság érdekében, nem jelenleg kiszolgáló közzétett ügyfelek helyhez kell közzétenni módosításokat. Ehelyett hozzunk létre egy új mobilalkalmazást, amely a duplikált szolgál. Ez az alkalmazás további pénzügyi költségek elkerüléséhez azonos App Service-csomag helyezheti.

Ezután meg kell az alkalmazás két verziója: egyet, amely változatlan marad, és továbbítja számukra közzétett alkalmazásokat a helyettesítő karakterrel, és a másik, amely ezután frissítheti és a cél egy új ügyfél kiadással. Helyezze át, és tesztelheti a kódját a tempójában, de ügyeljen rá, hogy bármely hibajavításokat tartalmaz, akkor győződjön meg arról, mindkettő érvényben. Ha úgy gondolja, hogy egy ügyfél kártevőcsaládok alkalmazások frissítése a legújabb verzióra kívánt számát, törölheti az eredeti áttelepített alkalmazás Ha, amennyit csak szeretne.

A teljes röviden ismerteti a frissítési folyamat a következőképpen történik:

1. Hozzon létre egy új Mobile Apps
2. Frissítés a projektet az új kiszolgáló SDK-k használata
3. Az ügyfélalkalmazás egy olyan új verzióját
4. (Nem kötelező) Az eredeti áttelepített példány törlése

## <a name="mobile-app-version"></a>Hozzon létre egy második alkalmazás-példányt
Az első lépése a frissítés, ha a mobilalkalmazás erőforrás, amely az alkalmazás új verzióját fogja futtatni. Ha egy meglévő mobilszolgáltatás már végrehajtotta az áttelepítést, érdemes létrehozni ebben a verzióban megegyező szolgáltatási csomagban. Nyissa meg a [Azure Portal] , és keresse meg az áttelepített alkalmazás. Jegyezze fel az App Service-csomag jelenleg is fut a.

Ezután hozza létre a második alkalmazáspéldány követve a [.NET-háttérrendszer létrehozásának utasításokat](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Amikor a rendszer kéri, válassza ki a App Service-csomag vagy a "szolgáltatási csomag" az áttelepített alkalmazás a csomag kiválasztása.

Valószínűleg érdemes ugyanabban az adatbázisban és értesítési központ használják, mint a Mobile Services. Nyissa meg ezeket az értékeket másolhatja [Azure Portal] ellenőrizheti, hogy az eredeti alkalmazást, majd kattintson **beállítások** > **Alkalmazásbeállítások**. A **kapcsolati karakterláncok**, másolatot `MS_NotificationHubConnectionString` és `MS_TableConnectionString`. Keresse meg az új hely frissítése, és illessze be őket, minden meglévő érték felülírása. Ez a folyamat bármely más Alkalmazásbeállítások ismételje meg az alkalmazás igényeinek megfelelően. Ha nem használja az áttelepített szolgáltatás, kapcsolati karakterláncok és a beállításokat alkalmazás olvashat a **konfigurálása** fülre a Mobile Services szakaszának a [klasszikus Azure portál].

Győződjön meg arról, az ASP.NET-projekt az alkalmazás egy példányát, és tegye közzé az új hely. Frissítve az új URL-címmel az ügyfélalkalmazás egy példányát használja, ellenőrizze, hogy minden a várt módon működik.

## <a name="updating-the-server-project"></a>A kiszolgálói projekt frissítése
Mobile Apps biztosít egy új [Mobil alkalmazások kiszolgálói SDK] biztosító szinte ugyanazokat a funkciókat biztosítja, mint a Mobile Services modul. Először el kell távolítania minden hivatkozás, amelyben a Mobile Services-csomagokat. A NuGet package manager keressen `WindowsAzure.MobileServices.Backend`. A legtöbb alkalmazás megjelenik itt, több csomagot, beleértve a `WindowsAzure.MobileServices.Backend.Tables` és `WindowsAzure.MobileServices.Backend.Entity`. Ebben az esetben indítsa el a függőségi fán a legalacsonyabb szintű csomag például `Entity`, és távolítsa el azt. Amikor a rendszer kéri, ne távolítsa el az összes függő csomagok. Ismételje meg ezt a folyamatot, amíg nem távolította el `WindowsAzure.MobileServices.Backend` magát.

Ezen a ponton kell egy projekt, amely már nem hivatkozik a Mobile Services SDK-k.

Ezután hozzáadja a Mobile Apps SDK hivatkozik. Ez a frissítés a legtöbb fejlesztő érdemes töltse le és telepítse a `Microsoft.Azure.Mobile.Server.Quickstart` csomag, mivel ez fogja lekérni a szükséges teljes készletében.

Több fordítási hibákat adta az SDK-k közötti különbségek eredő lesz, de ezeket egyszerűen címre, és a többi ebben a szakaszban ismertetett.

### <a name="base-configuration"></a>Alapkonfiguráció
Ezt követően WebApiConfig.cs, akkor is cserélje le:

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

a következővel:

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> Ha szeretne további információt az új .NET kiszolgálói SDK és funkciók hozzáadása/eltávolítása az alkalmazásból, olvassa el a [a .NET kiszolgálóoldali SDK használata] témakör.
>
>

Ha az alkalmazás lehetővé teszi a hitelesítési funkciók használatához, regisztrálja az OWIN közbenső szoftvert is kell. Ebben az esetben a fenti konfigurációs kódot kell áthelyezni egy új OWIN indítási osztályt.

1. Adja hozzá a NuGet-csomag `Microsoft.Owin.Host.SystemWeb` Ha azt nem már szerepel a projekthez.
2. A Visual Studióban kattintson a jobb gombbal a projekt és a select **Hozzáadás** -> **új elem**. Válassza ki **webes** -> **általános** -> **OWIN indítási osztályt**.
3. Helyezze át a fenti kód a MobileAppConfiguration `WebApiConfig.Register()` , a `Configuration()` az új indítási osztály metódusát.

Győződjön meg arról, hogy a `Configuration()` metódus végződik:

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

Nincsenek hitelesítéshez kapcsolódó további módosításokat, amelyek a teljes hitelesítés szakaszban szerepelnek.

### <a name="working-with-data"></a>Adatok használata
A Mobile Services a mobilalkalmazás neve szolgálja ki, az Entity Framework beállítások alapértelmezett séma nevével.

Annak érdekében, hogy ugyanazzal a sémával előtti, használja a következő beállítása a séma az alkalmazás a DbContext hivatkozik rá:

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

Győződjön meg arról, hogy állítsa be, ha a fenti MS_MobileServiceName. Ha az alkalmazás korábban testreszabott ez is megadhatja egy másik séma neve.

### <a name="system-properties"></a>Rendszertulajdonságok
#### <a name="naming"></a>Elnevezés
Az Azure Mobile Services-kiszolgálón SDK Rendszertulajdonságok mindig tartalmaznak dupla aláhúzásjel (`__`) tulajdonságok előtagja:

* __createdAt
* __updatedAt
* __deleted
* __version

A Mobile Services ügyféloldali SDK-k speciális logikát elemzéséhez a rendszer tulajdonságai a következő formátumban van.

Azure Mobile Apps, a rendszer tulajdonságai már nem speciális formátuma a, és rendelkezik a következő nevekkel:

* createdAt
* updatedAt
* törölve
* version

A Mobile Apps-ügyfél SDK-k az új rendszer tulajdonságok neveket használ, így a módosítások nem szükségesek az Ügyfélkód. Azonban ha közvetlenül a szolgáltatás REST-hívások majd akkor kell megváltoztatnia a lekérdezések megfelelően.

#### <a name="local-store"></a>Helyi tároló
A módosítások Rendszertulajdonságok nevei jelenti, hogy a Mobile Services-kapcsolat nélküli szinkronizálás – helyi adatbázis nem kompatibilis a Mobile Apps. Ha lehetséges kerülje az ügyfél elküldött alkalmazások a Mobile Services Mobile Apps-ig után függőben lévő módosítások a kiszolgáló frissítése. A frissített alkalmazást, majd egy új adatbázis-fájlnevet kell használnia.

Ha egy ügyfélalkalmazás frissítve van a Mobile Services Mobile Apps, bár vannak függőben lévő offline módosításokat a művelet várólistában, majd a rendszer-adatbázisok frissíteni kell az új oszlopnevek használata. Az iOS-Ez elérhető egyszerűsített áttelepítések használatával módosíthatja az oszlopok neveit. Az Android és a felügyelt .NET-ügyfelek egyéni SQL-nevezze át az oszlopokat az adatok objektum táblázatban kell írhat.

IOS-eszközökön akkor módosítani kell a Core sémát az adatok entitások megfelelően a következő. Vegye figyelembe, hogy a Tulajdonságok `createdAt`, `updatedAt` és `version` többé nem kell egy `ms_` előtagja:

| Attribútum | Típus | Megjegyzés |
| --- | --- | --- |
| id |Karakterlánc, kötelezőként megjelölt |Távoli tároló az elsődleges kulcs |
| createdAt |Dátum |(nem kötelező) vannak leképezve createdAt rendszertulajdonság |
| updatedAt |Dátum |(nem kötelező) vannak leképezve updatedAt rendszertulajdonság |
| version |Karakterlánc |(nem kötelező) annak észlelésére használnak, ütközéseket, a maps-verzióra |

#### <a name="querying-system-properties"></a>Lekérdezés tulajdonságai
Azure Mobile Services, a rendszer tulajdonságai nem kap meg alapértelmezés szerint, de csak akkor, amikor erre felkérést kapnak a lekérdezési karakterlánc használatával `__systemProperties`. Ezzel szemben az Azure Mobile Apps rendszerben a tulajdonságok akkor vannak **mindig kiválasztott** , mivel a kiszolgáló SDK hálózatiobjektum-modellt részét képezik.

Ez a változás főként hatással van a tartomány-kezelők bővítmények, például egyéni megvalósítását `MappedEntityDomainManager`. A Mobile Services szolgáltatásban, ha egy ügyfél soha nem kéri a rendszer tulajdonságokat is lehet használni egy `MappedEntityDomainManager` , amely ténylegesen nem feleltethető az összes tulajdonság. Azonban az Azure Mobile Apps, a nem leképezett tulajdonságok hibát okoz a GET-lekérdezésekben.

A legegyszerűbben úgy oldható meg, hogy a DTOs módosítása, hogy azok öröklése `ITableData` helyett `EntityData`. Adja hozzá a `[NotMapped]` attribútumot a mezőket, amelyek ki lehet hagyni.

Ha például a következőket határozza meg `TodoItem` rendszer tulajdonságok nélkül:

```csharp
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
```

Megjegyzés: Ha hibaüzenet jelenik `NotMapped`, vegyen fel egy hivatkozást a szerelvény `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
A Mobile Services néhány támogatást nyújt a CORS szerint alkalmazásburkoló az ASP.NET CORS megoldás része. Lehetővé teszik a fejlesztők további, így közvetlenül is élvezheti, el lett távolítva a burkolás réteg [ASP.NET CORS-támogatásának](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

A fő problémás területek, ha a CORS használatával, amelyek a `eTag` és `Location` fejlécek engedélyezni kell ahhoz, hogy az ügyfél SDK-k megfelelően működjön.

### <a name="push-notifications"></a>Leküldéses értesítések
Leküldés a fő cikket, amely a kiszolgáló SDK-ból hiányzik a tapasztalhatja a PushRegistrationHandler osztály. Regisztrációk kicsit másképp kezeli a Mobile Apps, és a regisztrációk tagless alapértelmezés szerint engedélyezve vannak. Címkék kezelése egyéni API-k használatával is elvégezhető. Tekintse át a [a címkék regisztrálása](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) utasítások további információt.

### <a name="scheduled-jobs"></a>Ütemezett feladatok
Ütemezett feladatok nem beépített Mobile Apps, így a .NET-háttérrendszer rendelkező meglévő feladatok külön-külön kell frissíteni kell. Az egyik lehetőség, hogy hozzon létre egy ütemezett [Webjobs-feladat] a mobilalkalmazás-kód webhelyen. Is sikerült a feladat kódot tartalmazó vezérlő beállítása, és konfigurálja a [Azure Scheduler] eléri a várt ütemezés szerint, hogy a végpont.

### <a name="miscellaneous-changes"></a>Egyéb változások
Most már rendelkeznie kell az összes ApiControllers, amelyet egy mobilügyfél fogja használni a `[MobileAppController]` attribútum. Ez már nem veszi fel alapértelmezés szerint, így a többi ApiControllers, lépjen a mobil formázót is érinti.

A `ApiServices` objektum már nem része az SDK-t. A Mobile App beállításai eléréséhez a következőket használhatja:

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

Ehhez hasonlóan naplózási most használatával valósítható meg az ASP.NET nyomkövetési standard írás:

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>Hitelesítési kapcsolatos szempontok
A Mobile Services hitelesítés összetevői rendelkezik már át lett helyezve az App Service hitelesítés/engedélyezés funkciót. Olvassa el a webhely engedélyezésével talál további információt a [hitelesítés hozzáadása a mobilalkalmazáshoz](app-service-mobile-ios-get-started-users.md) témakör.

Az egyes szolgáltatók, például az aad-ben, a Facebook és a Google kihasználhatja a meglévő regisztrációs az másolási alkalmazásból kell lennie. Egyszerűen szüksége, keresse meg az identitásszolgáltató portálján, és adjon hozzá egy új átirányítási URL-címet a regisztrációhoz. Ezután konfigurálja az App Service hitelesítés/engedélyezés az ügyfél-azonosító és titkos kulcsot.

### <a name="controller-action-authorization"></a>A művelet engedélyezési vezérlő
Minden olyan példánya a `[AuthorizeLevel(AuthorizationLevel.User)]` attribútum már módosítható a standard szintű ASP.NET használatával `[Authorize]` attribútum. Ezenkívül tartományvezérlők most névtelen alapértelmezés szerint is, mint más ASP.NET-alkalmazások.
Ha használja egy másik AuthorizeLevel beállításai, például felügyeleti vagy alkalmazást, vegye figyelembe, hogy ezek a szűnt. Ehelyett AuthorizationFilters beállítása a közös titkos kulcsot, vagy egy AAD-szolgáltatásnév biztonságosan engedélyezése a szolgáltatások közötti hívások konfigurálásához.

### <a name="getting-additional-user-information"></a>További felhasználói információk lekérése
További felhasználói információt, beleértve a hozzáférési jogkivonatok használatával a `GetAppServiceIdentityAsync()` módszer:

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

Emellett ha az alkalmazás függőségei tart a felhasználói azonosítóval, például az adatbázisban tárolja őket, fontos megjegyezni, hogy a felhasználói azonosítók között a Mobile Services és az App Service Mobile Apps különböző. A Mobile Services felhasználói Azonosítót, azonban továbbra is kaphat. Valamennyi ProviderCredentials alosztályát rendelkezik felhasználói azonosító tulajdonsággal. Ezért a folytatás előtt a példában:

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

Ha az alkalmazás végrehajtása a függőségek, a felhasználói azonosítókat, akkor fontos, hogy, használhatja ugyanazt a regisztrációt egy identitásszolgáltatóval, ha lehetséges. Felhasználói azonosítók általában hatóköre az alkalmazás regisztrációja használt, így a problémákat a feltételeknek megfelelő felhasználók az adataikhoz, létrehozhat egy új regisztrációs bemutatása.

### <a name="custom-authentication"></a>Egyéni hitelesítés
Ha az alkalmazás egy egyéni hitelesítési megoldást használ, célszerű győződjön meg arról, hogy a frissített hely rendelkezik-e a hozzáférést a rendszerhez. Az új egyéni hitelesítési az útmutatás a [.NET kiszolgálói SDK – áttekintés] integrálhatja a megoldást. Vegye figyelembe, hogy az egyéni hitelesítés összetevői egyelőre előzetes verzióként.

## <a name="updating-clients"></a>Ügyfelek frissítése
Ha már rendelkezik a egy működési Mobile Apps-háttéralkalmazást, használhatja az ügyfélalkalmazásban használ fel, amely egy új verziója. A Mobile Apps is tartalmazza az ügyfél SDK-k új verzióját, és hasonló a kiszolgáló frissítési fent, szüksége lesz a Mobile Apps-verzió telepítése előtt távolítsa el a Mobile Services SDK-k mutató összes hivatkozást.

A legfontosabb változások között az egyik célja, hogy a konstruktor már nincs szüksége egy alkalmazáskulcsot. Most már egyszerűen átadhatja a mobilalkalmazás URL-címét. A .NET-ügyfelek, például az a `MobileServiceClient` a konstruktor már:

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

Itt olvashat az új SDK-k telepítéséről és használatáról az új struktúra keresztül az alábbi hivatkozásokat:

* [iOS-verziót 3.0.0-s vagy újabb](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) 2.0.0-s verzió vagy újabb](app-service-mobile-dotnet-how-to-use-client-library.md)

Ha az alkalmazás leküldéses értesítések használatát, jegyezze fel az egyes platformokra vonatkozó konkrét regisztrációs utasításokat, néhány módosítás történt hiba is.

Ha készen áll az új ügyfél verziója, próbálja ki a frissített kiszolgálói projektet ellen. Miután ellenőrizte, hogy működik, az ügyfelek számára az alkalmazás új verziója engedje fel. Végül után az ügyfeleink éltek meg arra, hogy a frissítések fogadásához, törölheti az alkalmazást a Mobile Services verzióját. Ezen a ponton teljesen frissítését egy App Service Mobile Apps, a legújabb Mobile Apps kiszolgálóoldali SDK használatával.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[klasszikus Azure portál]: https://manage.windowsazure.com/
[Mi a Mobile Apps szolgáltatás?]: app-service-mobile-value-prop.md
[Mobil alkalmazások kiszolgálói SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Webjobs-feladat]: https://github.com/Azure/azure-webjobs-sdk/wiki
[A .NET kiszolgálóoldali SDK használata]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[App Service szolgáltatás díjszabása]: https://azure.microsoft.com/pricing/details/app-service/
[.NET kiszolgálói SDK – áttekintés]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
