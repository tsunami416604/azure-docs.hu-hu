---
title: "Az Azure App Service - Node.js mobilszolgáltatások frissítés"
description: "Ismerje meg a Mobile Services alkalmazás az App Service Mobile Apps könnyen frissítése"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: yochayk
editor: 
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 9a641bc667d07d1b674d2864c1f29151d527f46a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Frissítse a meglévő Node.js Azure Mobile szolgáltatást az App Service
App Service Mobile új módja a Microsoft Azure használatával mobil alkalmazásokat hozhatnak létre. További tudnivalókért lásd: [Mik azok a Mobile Apps?].

A cikkből megtudhatja, hogyan frissítheti az Azure Mobile Services meglévő Node.js háttérrendszer alkalmazás egy új App Service Mobile Apps. Ez a frissítés végrehajtása során a meglévő Mobile Services alkalmazás továbbra is működik.  Ha frissítenie kell a Node.js a háttéralkalmazás, tekintse meg a [frissítése a .NET Mobile Services](app-service-mobile-net-upgrading-from-mobile-services.md).

A mobil-háttéralkalmazást frissítésekor az Azure App Service alkalmazás szolgáltatás minden funkcióját hozzáféréssel rendelkezik, és számlázása a következők szerint [App Service díjszabás], nem a Mobile Services díjszabása.

## <a name="migrate-vs-upgrade"></a>Frissítése és áttelepítése
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Javasoljuk, hogy [áttelepítés](app-service-mobile-migrating-from-mobile-services.md) áthaladás a frissítés előtt. Ezzel a módszerrel után az alkalmazás mindkét verziója helyezhető el a azonos App Service-csomag is fel Önnek további költség nélkül.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>A Mobile Apps Node.js server SDK fejlesztései
Az új frissítés [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) biztosít számos fejlesztést, beleértve:

* Alapján a [keretrendszer Express](http://expressjs.com/en/index.html), az új csomópont SDK passzív és tervezett lépnek, új csomópont verziókkal tartani. Testre szabhatja az Express közbenső szoftvert az alkalmazás működését.
* A Mobile Services SDK képest jelentős teljesítményjavulást eredményezhet.
* Üzemeltethető webhely és a mobil-háttéralkalmazást; Hasonlóképpen akkor egyszerűen az Azure Mobile SDK minden meglévő express.v4 az alkalmazáshoz való hozzáadásához.
* Platformok közötti és a helyi fejlesztési készült, a Mobile Apps SDK lehetnek fejlesztett és helyileg Windows, Linux vagy OSX platformokon futtatható. Mostantól könnyen használható közös csomópont fejlesztői módszerek például futó [galuskánál](https://mochajs.org/) tesztek telepítését megelőzően.

## <a name="overview"></a>Alapszintű frissítési áttekintése
Támogatás a Node.js-háttéralkalmazáshoz frissítése, Azure App Service nyújtott kompatibilitási csomagot.  Frissítés után hogy egy új helyet, amely egy új App Service helyre telepíthető.

A Mobile Services-ügyfél SDK-k vannak **nem** kompatibilis az új Mobile Apps SDK kiszolgálóval. Adja meg az alkalmazás szolgáltatási folytonosság érdekében, hogy nem módosításokat kell közzétenni, a hely jelenleg a közzétett ügyfelek szolgál. Ehelyett kell hozzon létre egy új mobil-alkalmazást, amely duplikált funkcionál. Ez az alkalmazás a járulékos költségekkel megcélzásával elkerülheti az azonos App Service-csomag helyezhet el.

Ezután meg kell az alkalmazás két verziója: a helyettesítő és egyéb, amely ezután frissítheti és egy új ügyfél kiadással cél ugyanaz marad, és betölti közzétett alkalmazást. Helyezze át, és tesztelheti a kódját a ütemben, de meg kell győződnie arról, hogy bármely hibajavításokat tartalmaz, akkor mindkét érvényben. Ha úgy véli, hogy a helyettesítő alkalmazásokat a legújabb verzióra frissítette ügyfél kívánt számát, törölheti az eredeti áttelepített alkalmazás ha felügyelni. Azt nem költségnövekedéssel bármely pénzügyi, ha a ugyanazon, mint a Mobile Apps App Service-csomag tárolva.

A teljes vázlat a frissítési folyamat a következőképpen történik:

1. Töltse le a meglévő (áttelepített) Azure Mobile szolgáltatást.
2. A projekt átalakítása a kompatibilitási csomag használata Azure Mobile Apps.
3. Javítsa ki az összes különbséget (például a hitelesítési beállításait).
4. A konvertált Azure Mobile Apps-projekt telepítése egy új App Service.
5. A kiadási az ügyfélalkalmazást, amely az új mobilalkalmazás használ egy új verziója.
6. (Választható) Az eredeti áttelepített mobilszolgáltatás-alkalmazás törlése.

Törlés akkor fordulhat elő, ha nem lát minden forgalom az eredeti áttelepített mobil szolgáltatáson.

## <a name="install-npm-package"></a>Az előfeltételek telepítése
[Csomópont] a helyi számítógépre kell telepíteni.  A kompatibilitási csomagot kell telepíteni.  Csomópont telepítése után a következő parancsot futtathatja egy új cmd vagy a PowerShell-parancssorba:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Az Azure Mobile Services parancsfájlok beszerzése
* Jelentkezzen be az [Azure Portalra].
* Használatával **összes erőforrás** vagy **alkalmazásszolgáltatások**, a Mobile Services webhely található.
* A webhelyen, kattintson a **eszközök** -> **Kudu** -> **lépjen** a Kudu webhely megnyitásához.
* Kattintson a **Debug konzol** -> **PowerShell** a hibakereső konzol megnyitásához.
* Navigáljon a `site/wwwroot/App_Data/config` kattintva pedig minden könyvtár
* A letöltési ikonra kattintson a Tovább gombra a `scripts` könyvtár.

A ZIP-formátum parancsfájlok letöltése.  Hozzon létre egy új könyvtárat a helyi számítógépen, és csomagolja ki a `scripts.ZIP` fájl a könyvtárban található.  Ezzel létrehoz egy `scripts` directory.

## <a name="scaffold-app"></a>Ezek az új Azure Mobile Apps-háttéralkalmazás
A következő parancsot a parancsfájlok könyvtárat tartalmazó könyvtár:

```scaffold-mobile-app scripts out```

Ezzel létrehoz egy generált Azure Mobile Apps-háttéralkalmazás a a `out` könyvtár.  Bár nem kötelező megadni, akkor célszerű ellenőrizni a `out` könyvtárhoz, az Ön által választott forráskódraktárban be.

## <a name="deploy-ama-app"></a>Azure Mobile Apps-háttéralkalmazásának telepítése
A telepítés során szüksége lesz a következőkre:

1. Hozzon létre egy új Mobile alkalmazás a [Azure Portalra].
2. Futtassa a `createViews.sql` a csatlakoztatott adatbázis a parancsfájlt.
3. Csatolja az adatbázist, amely csatolva van a mobileszköz az új App Service szolgáltatást.
4. Csatolja az új App Service bármely más erőforrások (például a Notification hubs használatával).
5. A generált kód telepítése az új helyre.

### <a name="create-a-new-mobile-app"></a>Új mobileszköz-alkalmazás létrehozása
1. Jelentkezzen be az [Azure Portalra].
2. Kattintson az **+ÚJ** > **Web + mobil** > **Mobile App** elemre, majd adjon meg egy nevet a mobilalkalmazás háttérrendszerének.
3. Az **Erőforráscsoport** beállításánál válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat (az alkalmazás nevének használatával).

    Kiválaszthat egy másik App Service-csomagot, vagy újat is létrehozhat. További információk az App Service szolgáltatások tervek létrehozása egy új csomagot egy másik tarifacsomagban réteg és a kívánt helyen látható [Azure App Service-csomagok részletes áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Az **App Service-csomag** elemnél az alapértelmezett ([standard szintbe](https://azure.microsoft.com/pricing/details/app-service/) tartozó) csomag van kiválasztva. Igény szerint kiválaszthatja egy másik csomagot, vagy [hozzon létre egy újat](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Az App Service-csomag beállításai határozzák meg a [helyét, a szolgáltatások, a költség és a számítási erőforrásokat](https://azure.microsoft.com/pricing/details/app-service/) az alkalmazáshoz társított.

    A csomag kiválasztása után kattintson a **Létrehozás** gombra. Létrejön a mobilalkalmazás háttérrendszere.

### <a name="run-createviewssql"></a>CreateViews.SQL futtatása
A generált alkalmazást tartalmaz egy nevű fájlt `createViews.sql`.  Ezt a parancsfájlt a cél adatbázisban kell végrehajtani.  A kapcsolati karakterláncot a céladatbázis az áttelepített mobilszolgáltatáshoz lehet lekérni a **beállítások** lapon az **kapcsolati karakterláncok**.  A neve `MS_TableConnectionString`.

Ez a parancsfájl az SQL Server Management Studio vagy Visual Studio belül is futtathatja.

### <a name="link-the-database-to-your-app-service"></a>Az adatbázis csatolása az App Service
A meglévő adatbázis az App Service mutató hivatkozás létrehozása:

* Az a [Azure Portalra], nyissa meg az App Service.
* Válassza ki **összes beállítás** -> **adatkapcsolatok**.
* Kattintson a **+ Hozzáadás**.
* Válassza ki a legördülő **SQL-adatbázis**
* A **SQL-adatbázis**, válassza ki a meglévő adatbázist, majd kattintson a **válasszon**.
* A **kapcsolati karakterlánc**, adja meg a felhasználónevet és jelszót az adatbázist, majd kattintson a **OK**.
* Az a **adja hozzá az adatokhoz való kapcsolódást** lapján kattintson a **OK**.

A felhasználónév és jelszó található a kapcsolati karakterláncot a céladatbázis az áttelepített Mobile Service megtekintésével.

### <a name="set-up-authentication"></a>Hitelesítés beállítása
Az Azure Mobile Apps lehetővé teszi konfigurálása az Azure Active Directory, a Facebook, a Google, a Microsoft, és a Twitter hitelesítési a szolgáltatáson belül.  Egyéni hitelesítési kell külön-külön kell létrehoznia.  Tekintse meg a [hitelesítési fogalmakkal] dokumentáció és [hitelesítés gyorsindító] dokumentációjában talál további információt.  

## <a name="updating-clients"></a>Mobil ügyfelek frissítése
Miután egy operatív mobil-háttéralkalmazást, használhat az ügyfélalkalmazást, amely használ fel az új verziója. Mobile Apps is az ügyfél SDK-k új verzióját tartalmazza, és a kiszolgáló frissítése újabb hasonlóan kell távolítson el minden hivatkozást a Mobile Services SDK-k a Mobile Apps-verzió telepítése előtt.

A verziók közötti fő módosítások egyike, hogy a konstruktor már nem szükséges egy alkalmazás-kulcsot.
Most már egyszerűen adja meg a Mobile Apps URL-címét. Például a .NET-ügyfelek a `MobileServiceClient` konstruktor:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Az új SDK-k telepítéséről és használatáról az új struktúra keresztül a lenti hivatkozásokra kattintva olvashat:

* [2.2 vagy újabb Android verziója](app-service-mobile-android-how-to-use-client-library.md)
* [iOS-verzióval 3.0.0 vagy újabb verzió](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) 2.0.0 verzió vagy újabb verzió](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova 2.0 vagy újabb verzió](app-service-mobile-cordova-how-to-use-client-library.md)

Ha az alkalmazás lehetővé teszi leküldéses értesítések használni, jegyezze fel a regisztráció utasítás minden egyes platformhoz, néhány módosítás történt hiba is.

Ha készen áll az új ügyfél verziója, próbálja ki a frissített kiszolgálón projekthez. Ellenőrzése, hogy működik-e, miután az ügyfél számára az alkalmazás egy új verziója is megjelenhetnek. Előfordulhat Ha az ügyfelek rendelkezésére állt-e arra, hogy ezek a frissítések kap, törölheti a Mobile Services, verziószám: az alkalmazás. Ezen a ponton teljesen frissítését egy App Service Mobile Apps a legújabb Mobile Apps-kiszolgáló SDK használatával.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Mik azok a Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service díjszabás]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[hitelesítési fogalmakkal]: ../app-service/app-service-authentication-overview.md
[hitelesítés gyorsindító]: app-service-mobile-auth.md

[Azure Portalra]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
