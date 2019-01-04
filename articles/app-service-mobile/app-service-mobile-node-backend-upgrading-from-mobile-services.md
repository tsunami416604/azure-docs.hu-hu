---
title: Frissítés a Mobile Services, Azure App Service – Node.js
description: A Mobile Services-alkalmazás egy App Service Mobile Apps könnyen frissítésének ismertetése
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5a74097521cc7dceea2c47f78c1dbb2f17e292aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726680"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>App Service-ben a meglévő Node.js az Azure Mobile Service frissítése
Az App Service Mobile egy új módja a Microsoft Azure mobile alkalmazásokat hozhat létre. További tudnivalókért lásd: [Mi a Mobile Apps szolgáltatás?].

Ez a cikk ismerteti az Azure Mobile Services meglévő Node.js-háttérrendszer alkalmazás frissítése egy új App Service Mobile Apps. Ez a frissítés hajt végre, amíg a meglévő Mobile Services-alkalmazás továbbra is működnek.  Ha a Node.js-háttérrendszer alkalmazásokat frissíteni szeretne, olvassa el [frissítése a .NET Mobile Services](app-service-mobile-net-upgrading-from-mobile-services.md).

A mobil háttérrendszer frissítésekor az Azure App Service minden App Service szolgáltatás hozzáfér, és a következők szerint számlázzuk [App Service szolgáltatás díjszabása], nem a Mobile Services díjszabása.

## <a name="migrate-vs-upgrade"></a>Frissítése és migrálása
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Javasoljuk, hogy Ön [az áttelepítés](app-service-mobile-migrating-from-mobile-services.md) előtt frissítés alatt áll. Ezzel a módszerrel is helyezze az alkalmazás mindkét verziója azonos App Service-csomagot, és további használatáért nem számítunk fel.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>A Mobile Apps Node.js server SDK fejlesztései
Az új verzióra [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) biztosít különböző fejlesztéseit, többek között:

* Alapján a [Express keretrendszer](https://expressjs.com/en/index.html), az új csomópont SDK egyszerűsített, és értesüljön az új csomópont verzióval módon származnak, készült. Testre szabhatja az alkalmazás viselkedése, az Express közbenső szoftvert.
* A Mobile Services SDK képest jelentős teljesítménybeli fejlesztései.
* Most már tárolhat egy webhelyet a mobil háttérszolgáltatásban; együtt Hasonlóképpen könnyebbé vált az Azure Mobile SDK-t minden olyan meglévő express.v4 az alkalmazáshoz való hozzáadásához.
* Platformok közötti és a helyi fejlesztési számára készült, a Mobile Apps SDK-t az alkalmazáskódok fejlesztésének és helyileg OSX, Windows és Linux platformokon futtatható. Mostantól könnyen használható közös csomópont fejlesztési technikákkal például a futó [galuskánál](https://mochajs.org/) teszteket az üzembe helyezés előtt.

## <a name="overview"></a>Alapszintű az alkalmazásfrissítés áttekintése
Segítheti a egy Node.js-háttérrendszer frissítése, az Azure App Service-ben biztosított kompatibilitási csomagot.  Frissítés után kap egy új helyet, amely egy új App Service-ben helyre telepíthető.

A Mobile Services ügyféloldali SDK-k vannak **nem** kompatibilis az új Mobile Apps-kiszolgálóval SDK-t. Annak érdekében, hogy az alkalmazás szolgáltatási folytonosság érdekében, nem jelenleg kiszolgáló közzétett ügyfelek helyhez kell közzétenni módosításokat. Ehelyett hozzunk létre egy új mobilalkalmazást, amely a duplikált szolgál. Ez az alkalmazás további pénzügyi költségek elkerüléséhez azonos App Service-csomag helyezheti.

Ezután meg kell az alkalmazás két verziója: a helyettesítő karakterrel, és a másik, amely ezután frissítheti és cél ügyfél új kiadását, amely változatlan marad, és továbbítja számukra közzétett alkalmazást. Helyezze át, és tesztelheti a kódját a tempójában, de ügyeljen rá, hogy bármely hibajavításokat tartalmaz, akkor győződjön meg arról, mindkettő érvényben. Ha úgy gondolja, hogy egy ügyfél kártevőcsaládok alkalmazások frissítése a legújabb verzióra kívánt számát, törölheti az eredeti áttelepített alkalmazás Ha, amennyit csak szeretne. Azt nem többletköltséggel minden pénzügyi, ha az azonos App Service-csomag, a Mobile Apps.

A teljes röviden ismerteti a frissítési folyamat a következőképpen történik:

1. Töltse le a meglévő (áttelepített) Azure mobilszolgáltatásokhoz.
2. A projekt átalakítása az Azure Mobile Apps, a kompatibilitás csomag használatával.
3. Javítsa az esetleges különbségeket (például a hitelesítési beállításokat).
4. A konvertált Azure Mobile Apps-projekt telepítése egy új App Service-ben.
5. Az ügyfélalkalmazás, amely az új Mobile Apps új verziója engedje.
6. (Nem kötelező) Az alkalmazás eredeti áttelepített mobilszolgáltatás törlése.

Törlés akkor fordulhat elő, ha nem jelenik meg a forgalmat az eredeti áttelepített mobilszolgáltatáshoz.

## <a name="install-npm-package"></a> Az előfeltételek telepítése
[Csomópont] kell telepítenie a helyi gépen.  A kompatibilitási csomagot kell telepíteni.  Csomópont telepítése után futtathatja a következő parancsot egy új cmd vagy a PowerShell-parancssorban:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Szerezze be az Azure Mobile Services-szkriptek
* Jelentkezzen be az [Azure Portal].
* Használatával **összes erőforrás** vagy **App Services**, a Mobile Services-hely található.
* A webhelyen, kattintson a **eszközök** -> **Kudu** -> **Go** a Kudu-webhely megnyitásához.
* Kattintson a **hibakeresési konzolt** -> **PowerShell** a hibakereső konzol megnyitásához.
* Navigáljon a `site/wwwroot/App_Data/config` ezután kattintson a minden könyvtár
* A letöltési ikon mellett kattintson a `scripts` könyvtár.

Ezzel letölti a parancsfájlokat a ZIP-formátumban.  Hozzon létre egy új könyvtárat a helyi gépen, és csomagolja ki a `scripts.ZIP` a könyvtárban lévő fájlt.  Ezzel létrehoz egy `scripts` könyvtár.

## <a name="scaffold-app"></a> Az új Azure Mobile Apps-háttéralkalmazás scaffold
Futtassa a következő parancsot a szkriptek könyvtárat tartalmazó könyvtárban:

```scaffold-mobile-app scripts out```

Ezzel létrehoz egy generált Azure Mobile Apps háttéralkalmazásból a a `out` könyvtár.  Nem kötelező, de érdemes ellenőrizni a `out` könyvtárban, a forráskód tárházából a választott.

## <a name="deploy-ama-app"></a> Az Azure Mobile Apps háttéralkalmazásból üzembe helyezése
A telepítés során szüksége lesz a következőket:

1. Hozzon létre egy új Mobile alkalmazást az a [Azure Portal].
2. Futtassa a `createViews.sql` a csatlakoztatott adatbázis a parancsfájlt.
3. Az adatbázis, amely kapcsolódik az új App Service a Mobilszolgáltatásban hivatkozásra.
4. Az új App Service-hivatkozás a bármely más erőforrások (például a Notification hubs használatával).
5. Telepítse az új webhely a generált kódot.

### <a name="create-a-new-mobile-app"></a>Hozzon létre egy új Mobile Apps
1. Jelentkezzen be az [Azure Portal].
2. Kattintson az **+ÚJ** > **Web + mobil** > **Mobile App** elemre, majd adjon meg egy nevet a mobilalkalmazás háttérrendszerének.
3. Az **Erőforráscsoport** beállításánál válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat (az alkalmazás nevének használatával).

    Kiválaszthat egy másik App Service-csomagot, vagy újat is létrehozhat. Az App Service-csomagokkal, valamint az új csomagok egy másik tarifacsomagban és a kívánt helyen történő létrehozásával kapcsolatos további információk: [Azure App Service plans in-depth overview](../app-service/overview-hosting-plans.md) (Az Azure App Service-csomagok részletes áttekintése).
4. Az **App Service-csomag** elemnél az alapértelmezett ([standard szintbe](https://azure.microsoft.com/pricing/details/app-service/) tartozó) csomag van kiválasztva. Kiválaszthat egy másik App Service-csomagot is, vagy [létrehozhat egy újat](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Az App Service-csomag beállításai határozzák meg a [helyét, a szolgáltatások, a költségek és a számítási erőforrások](https://azure.microsoft.com/pricing/details/app-service/) az alkalmazáshoz társított.

    A csomag kiválasztása után kattintson a **Létrehozás** gombra. Létrejön a mobilalkalmazás háttérrendszere.

### <a name="run-createviewssql"></a>CreateViews.SQL futtatása
A generált alkalmazást tartalmaz egy nevű fájlt `createViews.sql`.  Ez a szkript a céladatbázison kell végrehajtani.  A kapcsolati karakterláncot a céladatbázis szerezhető be a saját áttelepített mobilszolgáltatásból a **beállítások** lap **kapcsolati karakterláncok**.  Alkalmazás neve `MS_TableConnectionString`.

Ez a szkript az SQL Server Management Studio vagy Visual Studio futtathatja.

### <a name="link-the-database-to-your-app-service"></a>Az adatbázis csatolása az App Service
A meglévő adatbázis hivatkozásra az App Service-ben:

* Az a [Azure Portal], nyissa meg az App Service.
* Válassza ki **minden beállítás** -> **adatkapcsolatok**.
* Kattintson a **+ Hozzáadás**.
* Válassza ki a legördülő menüben **SQL-adatbázis**
* A **SQL Database**, válassza ki a meglévő adatbázist, majd kattintson a **kiválasztása**.
* A **kapcsolati karakterlánc**, adja meg az adatbázis a felhasználónevet és jelszót, majd kattintson a **OK**.
* Az a **adatkapcsolatok hozzáadása** lapon **OK**.

A felhasználónév és jelszó a kapcsolati karakterláncot a céladatbázis megtekintésével a migrált Mobilszolgáltatásban található.

### <a name="set-up-authentication"></a>Hitelesítés beállítása
Az Azure Mobile Apps lehetővé teszi konfigurálása Azure Active Directory, Facebook, Google, Microsoft, és a Twitter-hitelesítés a szolgáltatásban.  Egyéni hitelesítési kell külön-külön kell kidolgozni.  Tekintse meg a [Hitelesítés – fogalmak] dokumentációja és [hitelesítés a rövid útmutató] dokumentációjában talál további információt.  

## <a name="updating-clients"></a>Mobil ügyfelek frissítése
Ha már rendelkezik a egy működési Mobile Apps-háttéralkalmazást, használhatja az ügyfélalkalmazásban használ fel, amely egy új verziója. A Mobile Apps is tartalmazza az ügyfél SDK-k új verzióját, és hasonló a kiszolgáló frissítési fent, szüksége lesz a Mobile Apps-verzió telepítése előtt távolítsa el a Mobile Services SDK-k mutató összes hivatkozást.

A legfontosabb változások között az egyik célja, hogy a konstruktor már nincs szüksége egy alkalmazáskulcsot.
Most már egyszerűen átadhatja a mobilalkalmazás URL-címét. A .NET-ügyfelek, például az a `MobileServiceClient` a konstruktor már:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Itt olvashat az új SDK-k telepítéséről és használatáról az új struktúra keresztül az alábbi hivatkozásokat:

* [2.2 vagy újabb Android-verzió](app-service-mobile-android-how-to-use-client-library.md)
* [iOS-verziót 3.0.0-s vagy újabb](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) 2.0.0-s verzió vagy újabb](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Az Apache Cordova 2.0-s vagy újabb verzió](app-service-mobile-cordova-how-to-use-client-library.md)

Ha az alkalmazás leküldéses értesítések használatát, jegyezze fel az egyes platformokra vonatkozó konkrét regisztrációs utasításokat, néhány módosítás történt hiba is.

Ha készen áll az új ügyfél verziója, próbálja ki a frissített kiszolgálói projektet ellen. Miután ellenőrizte, hogy működik, az ügyfelek számára az alkalmazás új verziója engedje fel. Végül után az ügyfeleink éltek meg arra, hogy a frissítések fogadásához, törölheti az alkalmazást a Mobile Services verzióját. Ezen a ponton teljesen frissítését egy App Service Mobile Apps, a legújabb Mobile Apps kiszolgálóoldali SDK használatával.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Mi a Mobile Apps szolgáltatás?]: app-service-mobile-value-prop.md
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
[App Service szolgáltatás díjszabása]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Hitelesítés – fogalmak]: ../app-service/overview-authentication-authorization.md
[Hitelesítés a rövid útmutató]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
