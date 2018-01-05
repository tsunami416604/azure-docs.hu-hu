---
title: "A Mobile Apps használata a Node.js háttérkiszolgáló SDK |} Microsoft Docs"
description: "Megtudhatja, hogyan használható a Node.js háttérkiszolgáló SDK az Azure App Service Mobile Apps a."
services: app-service\mobile
documentationcenter: 
author: elamalani
manager: elamalani
editor: 
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 336da28bea7de313bced97e447fc6b7b1fb1390d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Az Azure Mobile Apps Node.js SDK használatával
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a cikk ismerteti a részletes útmutatást és példákat bemutatja, hogyan használható az Azure App Service Mobile Apps Node.js-háttéralkalmazáshoz.

## <a name="Introduction"></a>Bevezetés
Az Azure App Service Mobile Apps lehetővé teszi a mobil optimalizált adatelérési Web API hozzáadása egy webalkalmazást.  Az ASP.NET és a Node.js webes alkalmazásokhoz az Azure App Service Mobile Apps SDK valósul meg.  Az SDK tartalmazza a következő műveleteket:

* Az adatelérési tábla műveletek (olvasás, Insert, Update, Delete)
* Egyéni API-műveletek

Mindkét műveletek Azure App Service, beleértve a közösségi Identitásszolgáltatók például a Facebookhoz, Twitter, Google és a Microsoft, valamint Azure Active Directory vállalati identitás által engedélyezett összes Identitásszolgáltatók biztosít a hitelesítéshez.

Minták találhat meg minden esetben használja a [minták directory a Githubon].

## <a name="supported-platforms"></a>A támogatott platformok
Az Azure Mobile Apps csomópont SDK támogatja a jelenlegi LTS verzió, csomópont és újabb verziók.  Frissítésétől írást, a legújabb LTS verzió a csomópont v4.5.0.  A csomópont más verzióiban előfordulhat, hogy működik, de nem támogatottak.

Az Azure Mobile Apps csomópont SDK két adatbázis-illesztőprogram támogatja, mert a csomópont-mssql illesztőprogram támogatja az SQL Azure és a helyi SQL Server-példányokat.  A sqlite3 illesztőprogram csak egyetlen példányán SQLite adatbázisokat támogatja.

### <a name="howto-cmdline-basicapp"></a>Útmutató: a parancssor használatával alapszintű Node.js-háttéralkalmazás létrehozása
Minden Azure App Service Mobile App Node.js-háttéralkalmazáshoz ExpressJS alkalmazásként indul.  ExpressJS a legnépszerűbb web service keretrendszer Node.js érhető el.  Létrehozhat egy alapszintű [Express] alkalmazás az alábbiak szerint:

1. Egy parancs vagy a PowerShell-ablakot hozzon létre egy könyvtárat a projekthez.

        mkdir basicapp
2. A csomag struktúra inicializálása npm init futtassa.

        cd basicapp
        npm init

    Az npm init parancs kéri a projekt inicializálása kérdésekre.  Tekintse meg a példa az alábbiakat:

    ![A npm init kimenet][0]
3. Telepítse a sürgős és az azure-mobilalkalmazások könyvtárak összetevőtárházat npm.

        npm install --save express azure-mobile-apps
4. A kiszolgáló alapszintű mobil végrehajtásához app.js fájl létrehozása.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Ez az alkalmazás egy végpontot hoz létre egy mobile optimalizált WebAPI (`/tables/TodoItem`), amely egy dinamikus sémával alapul szolgáló SQL adattároló nem hitelesített hozzáférést biztosít.  Alkalmas az ügyfél szalagtár gyors üzembe helyezések a következő:

* [Android ügyfél gyors üzembe helyezés]
* [Apache Cordova-ügyfél gyors üzembe helyezés]
* [iOS ügyfél gyors üzembe helyezés]
* [A Windows Store ügyfél gyors üzembe helyezés]
* [Xamarin.iOS ügyfél gyors üzembe helyezés]
* [Xamarin.Android ügyfél gyors üzembe helyezés]
* [Xamarin.Forms-ügyfél gyors üzembe helyezés]

Ezen alapszintű alkalmazás találja a kódot a [basicapp mintát a Githubon].

### <a name="howto-vs2015-basicapp"></a>Útmutató: a Visual Studio 2015 csomópont-háttéralkalmazás létrehozása
Visual Studio 2015-öt bővítménye belül az IDE Node.js-alkalmazások fejlesztéséhez szükséges.  Indítsa el, telepítse a [Node.js eszközök 1.1 a Visual Studio].  A Node.js Tools for Visual Studio telepítése után hozzon létre egy Express 4.x alkalmazást:

1. Nyissa meg a **új projekt** párbeszédpanelen (a **fájl** > **új** > **projekt …** ).
2. Bontsa ki a **sablonok** > **JavaScript** > **Node.js**.
3. Válassza ki a **alapszintű Azure Node.js Express 4 alkalmazás**.
4. Töltse ki a projekt nevét.  Kattintson az *OK* gombra.

    ![Visual Studio 2015-öt új projekt][1]
5. Kattintson a jobb gombbal a **npm** csomópont, és válassza **telepítése új npm csomagok...** .
6. Szükség lehet az npm-katalógus az első Node.js-alkalmazás létrehozása a frissítése.  Kattintson a **frissítése** szükség esetén.
7. Adja meg *azure-mobilalkalmazások* be a keresőmezőbe.  Kattintson a **azure-mobileszköz-alkalmazások 2.0.0** csomagot, majd kattintson az **csomagtelepítés**.

    ![Új npm-csomagok][2]
8. Kattintson a **Bezárás** gombra.
9. Nyissa meg a *app.js* fájl az Azure Mobile Apps SDK támogatásához.  Sor 6 at alsó részén a könyvtárban található utasítások szükséges, adja hozzá a következő kódot:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Körülbelül sor 27 után az egyéb app.use utasítások adja hozzá a következő kódot:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Mentse a fájlt.
10. Futtassa az alkalmazást helyileg (az API-t kiszolgált a http://localhost: 3000), vagy közzététele az Azure-bA.

### <a name="create-node-backend-portal"></a>Útmutató: az Azure portál használata Node.js-háttéralkalmazás létrehozása
A Mobile Apps háttér közvetlenül hozhat létre a [Azure-portálon]. Kövesse az alábbi lépéseket, vagy hozzon létre egy ügyfél és kiszolgáló együtt a [mobilalkalmazás létrehozása](app-service-mobile-ios-get-started.md) oktatóanyag. Az oktatóanyag tartalmazza ezeket az utasításokat egy egyszerűsített verziója és a projektek a koncepció igazolása.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vissza a *Ismerkedés* panelen, a **tábla API létrehozása**, válassza a **Node.js** , a **háttéralkalmazás-nyelv**.
Jelölje be a "**tudomásul veszem, hogy ezzel a művelettel felülírja az összes hely tartalmának.**", majd kattintson a **TodoItem tábla létrehozása**.

### <a name="download-quickstart"></a>Hogyan: Töltse le a Node.js háttérrendszer gyors üzembe helyezés kód projekt Git használatával
Ha Node.js Mobile Apps-háttéralkalmazás létrehozása a portál használatával **gyors üzembe helyezési** panelen, egy Node.js-projektet, létrehozása és telepítése a helyen. Adja hozzá a táblák és API-kat, és a Node.js-háttéralkalmazáshoz a portálon tartozó kódfájlok szerkesztése. Különböző központi telepítési eszközök segítségével is letölti a háttéralkalmazás-projekt hozzáadása vagy módosítása a táblák és API-k, majd a projekt közzé. További információkért lásd: a [Azure App Service telepítési útmutató]. az alábbi eljárás egy Git-tárház használatával töltse le a gyors üzembe helyezési projekt kódját.

1. Ha még nem tette, telepítse a Git esetében. A Git telepítéséhez szükséges lépések eltérőek, operációs rendszerek között. Lásd: [telepítése Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) az operációsrendszer-specifikus disztribúcióiról, valamint a telepítési útmutatót.
2. Kövesse a [engedélyezése az App Service alkalmazás tárház](../app-service/app-service-deploy-local-git.md#Step3) ahhoz, hogy a háttér-helyhez, és jegyezze fel a központi telepítés felhasználónevet és jelszót a Git-tárházba.
3. A mobilalkalmazás háttérrendszerének panelen, jegyezze fel a **Git-klón URL-cím** beállítást.
4. Hajtsa végre a `git clone` parancsot a Git használatával klón URL-CÍMÉT, írja be a jelszót, ha szükséges, az alábbi példában látható módon:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Keresse meg a helyi könyvtárba, amely az előző példában /todolist, és figyelje meg, hogy project fájlok le vannak töltve. Keresse meg a `todoitem.json` fájlt a `/tables` könyvtár.  Ez a fájl engedélyeit a tábla határozza meg.  Is megtalálhatja a `todoitem.js` fájl ugyanabban a könyvtárban, amely meghatározza, hogy CRUD művelet a következő táblázatban a parancsfájlok.
6. Project fájlok módosult, miután hajtható végre a következő parancsok futtatásával adja hozzá, véglegesítése, majd töltse fel a módosításokat a helyhez:

        $ git commit -m "updated the table script"
        $ git push origin master

    Amikor új fájlok hozzáadása a projekthez, először kell végrehajtani a `git add .` parancsot.

A hely minden alkalommal, amikor egy új készletét véglegesíti a rendszer előkészítésre továbbít a webhely ismételt közzététele.

### <a name="howto-publish-to-azure"></a>Útmutató: a Node.js-háttéralkalmazáshoz közzététele az Azure-bA
A Microsoft Azure az Azure szolgáltatásban az Azure App Service Mobile Apps Node.js háttérrendszer közzétételéhez számos mechanizmust biztosít.  Ezek közé tartozik a központi telepítési eszközöket a Visual Studio integrált, parancssori eszközök és verziókövetési alapuló folyamatos üzembe helyezés beállítások használatával.  A témakörrel kapcsolatos további információkért lásd: a [Azure App Service telepítési útmutató].

Az Azure App Service a Node.js-alkalmazás üzembe helyezése előtt tekintse át a konkrét útmutatásért rendelkezik:

* Hogyan [a csomópont verzió megadása]
* Hogyan [csomópont modulok használata]

### <a name="howto-enable-homepage"></a>Útmutató: az alkalmazás kezdőlapját engedélyezése
Számos alkalmazás webes és mobilalkalmazások és ExpressJS keretében teszi lehetővé a két értékkorlátozás kombinálhatók.  Egyes esetekben azonban Kezdésként csak valósítja meg a mobil illesztőfelületet.  Ez akkor hasznos, annak érdekében, az app service egy kezdőlapja megfelelően működik, és így.  Adja meg a saját kezdőlapján, vagy egy ideiglenes kezdőlap engedélyezése.  Ahhoz, hogy egy ideiglenes kezdőlapját, használja a következő példányának létrehozása az Azure Mobile Apps:

    var mobile = azureMobileApps({ homePage: true });

Ha csak ez a beállítás érhető el helyi fejlesztésekor, ezzel a beállítással adhat hozzá a `azureMobile.js` fájlt.

## <a name="TableOperations"></a>Tábla műveletek
Az azure-mobilalkalmazások Node.js Server SDK teszi közzé az Azure SQL Database, a WebAPI tárolt adattáblák mechanizmust biztosít.  Öt műveleti rendelkezésre állnak.

| Művelet | Leírás |
| --- | --- |
| GET /tables/*táblanév* |A tábla összes rekord beolvasása |
| GET /tables/*tablename*/:id |Egy adott rekord a táblázatban beolvasása |
| POST /tables/*táblanév* |A tábla rekord létrehozása |
| JAVÍTÁS /tables/*tablename*/:id |Módosítani egy rekordot a tábla |
| TÖRLÉS /tables/*tablename*/:id |A tábla rekord törlése |

Támogatja a WebAPI [OData] és bővíti a következő tábla sémáját támogatásához [offline adatszinkronizálás].

### <a name="howto-dynamicschema"></a>Hogyan: Adja meg a táblák egy dinamikus séma használata
Egy tábla használt, meg kell határozni.  Táblák (ahol a fejlesztői definiálja a séma oszlopaira) statikus séma vagy dinamikusan adható meg (ha az SDK szabályozza a séma, a bejövő kérések alapján). Emellett a fejlesztői szabályozhatja a WebAPI bizonyos aspektusainak Javascript-kód hozzáadása a definíciót.

Ajánlott eljárásként akkor kell mindegyik tábla a táblák könyvtárban Javascript-fájlt ad meg, majd a tables.import() metódus használatával importálhatja a táblákat.  Az alkalmazáson belüli basic kiterjesztése esetén az app.js fájl ki kell igazítani:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Adja meg a táblázatban szereplő. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Táblák alapértelmezés szerint dinamikus séma használja.  Dinamikus séma globálisan kikapcsolásához állítsa be az Alkalmazásbeállítás **MS_DynamicSchema** false értékre az Azure portálon.

A teljes példáját megtalálhatja a [todo mintát a Githubon].

### <a name="howto-staticschema"></a>Hogyan: Adja meg a táblák egy statikus séma használata
Explicit módon határozhatja meg a WebAPI keresztül teszi közzé az oszlopokat.  Az azure-mobilalkalmazások Node.js SDK automatikusan hozzáadja a listához, Ön által biztosított offline adatszinkronizálás szükséges további oszlopokat.  Például a gyors üzembe helyezés ügyfélalkalmazások szükséges két oszlopokkal rendelkező táblát: text (szöveg) (logikai) hajtsa végre.  
A tábla a tábla definícióját JavaScript-fájlt (a táblák könyvtárban található) az alábbiak szerint lehet meghatározni:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Ha statikusan határozza meg a táblák, majd kell is a tables.initialize() metódust hívja az adatbázisséma indításkor létrehozásához.  A tables.initialize() metódus visszaadja a [ígéret] , hogy a webszolgáltatás nem teljesíti a kérelmeket az adatbázis inicializálása előtt.

### <a name="howto-sqlexpress-setup"></a>Hogyan: használja az SQL Express fejlesztési adattárként a helyi számítógépen
Az Azure Mobile Apps a AzureMobile alkalmazások csomópont SDK kiszolgáló kívül a mezőbe a három lehetőséget kínál: SDK kiszolgáló kívül a mezőbe a három lehetőséget kínál:

* Használja a **memória** nem állandó példa áruházbeli-illesztőprogrammal
* Használja a **mssql** adja meg az SQL Express adattárat fejlesztési-illesztőprogram
* Használja a **mssql** adjon meg egy Azure SQL Database adattároló üzemi-illesztőprogram

Az Azure Mobile Apps Node.js SDK-t használ a [mssql Node.js csomag] létrehozásához és az SQL Express és SQL-adatbázis kapcsolat használatára.  A csomag igényli-e, hogy az SQL Express-példány TCP-kapcsolatok engedélyezése.

> [!TIP]
> A memória illesztőprogram nem biztosít tesztelési létesítményekben teljes készletét.  Ha a háttéralkalmazást helyileg tesztelni kívánt, SQL Express adattárat és az mssql illesztőprogram használatát javasoljuk.
>
>

1. Töltse le és telepítse [Microsoft SQL Server 2014 Express].  Győződjön meg arról, telepíti az SQL Server 2014 Express eszközök kiadásával.  Ha 64 bites támogatás kifejezetten szüksége a 32 bites kevesebb memóriát igényel, futtatásakor.
2. Futtassa az SQL Server 2014 Configuration Manager.

   1. Bontsa ki a **SQL Server hálózati konfigurációja** csomópontot a bal oldali fában menüben.
   2. Kattintson a **SQLEXPRESS protokollok**.
   3. Kattintson a jobb gombbal **TCP/IP** válassza **engedélyezése**.  Kattintson a **OK** az előugró párbeszédpanelen.
   4. Kattintson a jobb gombbal **TCP/IP** válassza **tulajdonságok**.
   5. Kattintson a **IP-címek** fülre.
   6. Keresés a **IPAll** csomópont.  Az a **TCP-Port** mezőbe írja be **1433**.

          ![Configure SQL Express for TCP/IP][3]
   7. Kattintson az **OK** gombra.  Kattintson a **OK** az előugró párbeszédpanelen.
   8. Kattintson a **SQL Server Services** a bal oldali fában menüben.
   9. Kattintson a jobb gombbal **SQL Server (SQLEXPRESS)** válassza **újraindítása**
   10. Zárja be az SQL Server 2014 Configuration Manager.
3. Futtassa az SQL Server 2014 Management Studio, és csatlakozzon a helyi SQL Express-példány

   1. Kattintson a jobb gombbal az Object Explorer-példány, és válassza ki **tulajdonságai**
   2. Válassza ki a **biztonsági** lap.
   3. Győződjön meg arról a **SQL Server és a Windows-hitelesítés üzemmód** van kijelölve
   4. Kattintson az **OK** gombra

          ![Configure SQL Express Authentication][4]
   5. Bontsa ki a **biztonsági** > **bejelentkezések** az Object Explorer
   6. Kattintson a jobb gombbal **bejelentkezések** válassza **új bejelentkezés...**
   7. Adja meg a bejelentkezési nevet.  Kattintson az **SQL Server-hitelesítés** lehetőségre.  Adjon meg egy jelszót, majd írja be ugyanezt a jelszót **jelszó megerősítése**.  A jelszónak meg kell felelnie a Windows bonyolultsági követelményeknek.
   8. Kattintson az **OK** gombra

          ![Add a new user to SQL Express][5]
   9. Kattintson a jobb gombbal az új bejelentkezési adatait, és válassza ki **tulajdonságai**
   10. Válassza ki a **kiszolgálói szerepkörök** lap
   11. Mellett jelölje be a jelölőnégyzetet a **dbcreator** kiszolgálói szerepkör
   12. Kattintson az **OK** gombra
   13. Zárja be az SQL Server 2015 Management Studio

Győződjön meg arról, a felhasználónév és a megadott jelszó rögzítéséhez.  Szükség lehet további kiszolgálói szerepkörök vagy engedélyek attól függően, hogy az adott adatbázis követelményeit.

A Node.js-alkalmazás olvasása a **SQLCONNSTR_MS_TableConnectionString** környezeti változó a kapcsolati karakterlánc ehhez az adatbázishoz.  A változó beállított a környezetben.  Például a PowerShell segítségével a környezeti változót:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP-kapcsolaton keresztül érik el az adatbázist, és adjon meg egy felhasználónevet és jelszót a kapcsolat.

### <a name="howto-config-localdev"></a>Útmutató: a helyi fejlesztési projekt konfigurálása
Az Azure Mobile Apps beolvassa a JavaScript-fájl neve *azureMobile.js* helyi objektumot.  Ne használja ezt a fájlt az Azure Mobile Apps SDK konfigurálásához éles környezetben, – használja az alkalmazásbeállítások belül a [Azure-portálon] helyette.  A *azureMobile.js* fájlba exportálja a konfigurációs objektum.  A leggyakrabban használt beállítások a következők:

* Adatbázis-beállítások
* Diagnosztikai naplózás beállításait
* Alternatív CORS-beállítások

Példa *azureMobile.js* az előző adatbázis-beállítások megvalósításához fájl a következő:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Javasoljuk, hogy adja hozzá *azureMobile.js* számára a *.gitignore* fájlt (vagy más verziókövetési figyelmen kívül hagyja a fájlt) a felhőben tárolt jelszavak megelőzése érdekében.  Mindig adja meg az üzemi beállításait az alkalmazás beállításairól a [Azure-portálon].

### <a name="howto-appsettings"></a>Útmutató: A mobilalkalmazás-beállításainak konfigurálása
A legtöbb beállítását a *azureMobile.js* fájl rendelkezik egyenértékű Alkalmazásbeállítás a [Azure-portálon].  Az alábbi lista segítségével állítsa be alkalmazását az alkalmazás beállításai:

| Alkalmazás beállítása | *azureMobile.js* beállítás | Leírás | Érvényes értékek |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |név |Az alkalmazás nevét |karakterlánc |
| **MS_MobileLoggingLevel** |Logging.level |Naplózandó üzenetek minimális naplózási szint |Hiba, figyelmeztetés, információ, részletes, hibakeresési silly |
| **MS_DebugMode** |hibakeresés |Engedélyezés vagy letiltás hibakeresési mód |IGAZ, hamis |
| **MS_TableSchema** |Data.Schema |SQL-táblák alapértelmezett séma neve |karakterlánc (alapértelmezett: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Engedélyezés vagy letiltás hibakeresési mód |IGAZ, hamis |
| **MS_DisableVersionHeader** |verzió (a nem definiált beállítása) |Az X-ZUMO-kiszolgáló-Version fejlécnek letiltása |IGAZ, hamis |
| **MS_SkipVersionCheck** |skipversioncheck |Letiltja az ügyfél API-verziójának ellenőrzése |IGAZ, hamis |

Egy Alkalmazásbeállítás beállítása:

1. Jelentkezzen be az [Azure-portálon].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások** kattintson a mobilalkalmazás nevére.
3. Alapértelmezés szerint a beállítások panel nyílik meg. Ha nem, kattintson a gombra **beállítások**.
4. Kattintson a **Alkalmazásbeállítások** az általános menüjében.
5. Görgessen az alkalmazásbeállítások szakaszhoz.
6. Ha az alkalmazás, beállítás már létezik, kattintson az Alkalmazásbeállítás értéke pedig az az érték szerkesztéséhez.
7. Ha az alkalmazás nem létezik, adja meg az Alkalmazásbeállítás a kulcs és az érték mezőben megadott érték.
8. Ha befejeződött, kattintson a **mentése**.

A legtöbb alkalmazás beállításainak módosítása a szolgáltatás újraindítását igényli.

### <a name="howto-use-sqlazure"></a>Útmutató: az üzemi adattároló SQL-adatbázis használata
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Az Azure SQL Database adattárként használata azonos Azure App Service-alkalmazás összes típusa. Ha még nem tette meg, kövesse az alábbi lépéseket a mobilalkalmazás háttérrendszerének létrehozásához.

1. Jelentkezzen be az [Azure-portálon].
2. A felső bal oldali ablak, kattintson a **+ új** gomb > **Web + mobil** > **mobilalkalmazás**, majd adjon meg egy nevet a mobilalkalmazás háttérrendszerének.
3. Az a **erőforráscsoport** mezőbe írja be a néven az alkalmazáshoz.
4. Az alapértelmezett App Service-csomag van kiválasztva.  Ha úgy szeretné módosítani az App Service-csomag, akkor megteheti az App Service-csomag kattintva > **+ új**.  Adja meg az új App Service-csomag nevét, és válasszon egy megfelelő helyet.  Kattintson a tarifacsomag, és válasszon egy megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **összes** több, mint az beállítások árképzési nézetre **szabad** és **megosztott**.  Miután kiválasztotta a tarifacsomagot, kattintson a **válasszon** gombra.  Vissza a **App Service-csomag** panelen kattintson a **OK**.
5. Kattintson a **Create** (Létrehozás) gombra. A Mobile Apps-háttéralkalmazás kiépítés néhány percet is igénybe vehet.  Miután a Mobile Apps-háttéralkalmazás ki van építve, a portál megnyitja a **beállítások** panel a mobilalkalmazás háttérrendszerének.

A Mobile Apps-háttéralkalmazás létrehozása után ha szeretné, vagy egy meglévő SQL-adatbázis csatlakoztatása a Mobile Apps-háttéralkalmazás, vagy hozzon létre egy új SQL-adatbázis.  Ebben a szakaszban a Microsoft SQL-adatbázis létrehozása.

> [!NOTE]
> Ha már rendelkezik adatbázissal ugyanazon a helyen, a mobil-háttéralkalmazást, választhatja **meglévő adatbázis használata** , és válassza ki, hogy az adatbázis. Egy másik helyen található adatbázis használata nem ajánlott miatt magasabb késések fordulnak elő.
>
>

1. Kattintson az új Mobile Apps-háttéralkalmazás **beállítások** > **mobilalkalmazás** > **adatok** > **+ Hozzáadás**.
2. Az a **adatkapcsolat hozzáadása** panelen kattintson a **SQL adatbázis - kötelező beállítások konfigurálása** > **hozzon létre egy új adatbázist**.  Adja meg az új adatbázis nevét a **neve** mező.
3. Kattintson a **Server**.  Az a **új kiszolgáló** panelen adjon meg egy egyedi kiszolgálónevet a a **kiszolgálónév** mezőben, majd adjon meg egy megfelelő **kiszolgáló-rendszergazdai bejelentkezés** és **jelszó**.  Győződjön meg arról **azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** be van jelölve.  Kattintson az **OK** gombra.

    ![Egy Azure SQL-adatbázis létrehozása][6]
4. Az a **új adatbázis** panelen kattintson a **OK**.
5. Vissza a **adatkapcsolat hozzáadása** panelen válassza **kapcsolati karakterlánc**, adja meg a bejelentkezési és az adatbázis létrehozásakor megadott jelszót.  Ha egy meglévő adatbázist használ, a bejelentkezési hitelesítő adatok megadása, hogy az adatbázis.  Ha a megadott, kattintson **OK**.
6. Vissza a **adatkapcsolat hozzáadása** újra, kattintson a panel **OK** létrehozni az adatbázist.

<!--- END OF ALTERNATE INCLUDE -->

Az adatbázis létrehozása néhány percet is igénybe vehet.  Használja a **értesítések** kell figyelni a telepítés előrehaladását.  Nem folytatódni mindaddig, amíg az adatbázis sikeresen telepítve lett.  Miután sikeresen telepítve központilag, egy kapcsolati karakterláncot a mobil-háttéralkalmazást Alkalmazásbeállítások az SQL-adatbázispéldány jön létre.  A app beállítás látható a **beállítások** > **Alkalmazásbeállítások** > **kapcsolati karakterláncok**.

### <a name="howto-tables-auth"></a>Útmutató: hitelesítés megkövetelése a hozzáféréshez táblákhoz
Ha szeretne az App Service-hitelesítés használatára a táblák végpont, konfigurálnia kell a App Service hitelesítés a [Azure-portálon] első.  Az Azure App Service hitelesítés konfigurálásával kapcsolatos további részletekért tekintse át a konfigurációs útmutató szeretne használni az identitásszolgáltató számára:

* [Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [A Microsoft Authentication konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Minden tábla tulajdonsága hozzáférés táblájához való hozzáférés vezérlésére használható.  Az alábbi minta-hitelesítés használatához a statikusan megadott táblát tartalmazza.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

A hozzáférés tulajdonságot is igénybe vehet, értékek egyike

* *Névtelen* azt jelzi, hogy az ügyfélalkalmazás hitelesítés nélkül-adatok olvasása
* *hitelesített* azt jelzi, hogy az ügyfélalkalmazás egy érvényes hitelesítési jogkivonatot a kérelmet kell küldenie
* *letiltott* azt jelzi, hogy ez a táblázat jelenleg le van tiltva

Ha a hozzáférési tulajdonság nincs definiálva, nem hitelesített elérését.

### <a name="howto-tables-getidentity"></a>Útmutató: hitelesítés jogcímek a táblák használata
Beállíthat különböző hitelesítési beállításakor igényelt jogcímeket.  Ezeket a jogcímeket amelyek általában telepítéseinél nem használhatók a `context.user` objektum.  Azonban azokat lekérheti használatával a `context.user.getIdentity()` metódust.  A `getIdentity()` metódus, amely egy objektum ígéret ad vissza.  Az objektum van kulccsal (facebook, google, twitter, microsoftaccount vagy aad-ben) hitelesítési módszerrel.

Például ha korábban beállított Microsoft Account hitelesítése és a kérés e-mail cím jogcím, adhat hozzá az e-mail cím a rekord, a következő táblázat a tartományvezérlő:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Milyen jogcímeket érhetők el, használja a webböngésző megtekintéséhez a `/.auth/me` végpont a webhely.

### <a name="howto-tables-disabled"></a>Útmutató: bizonyos táblájához műveletek való hozzáférés letiltása
Mellett a táblázatban szereplő, az access tulajdonság segítségével szabályozhatja az egyes műveletek.  Nincsenek négy műveletet:

* *olvassa el* a RESTful BEOLVASNI művelet a táblán
* *Helyezze be* a RESTful POST művelet a táblázat
* *frissítés* a RESTful javítás művelet a táblán
* *Törlés* a RESTful DELETE művelet a táblán van

Például Kezdésként érdemes lehet egy olyan írásvédett nem hitelesített táblázat:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Hogyan: módosítsa a táblázat műveletek használt lekérdezés
Kötelező megadni a tábla műveletek arra, hogy az adatok korlátozott nézete.  Például előfordulhat, hogy adjon meg egy táblát, amely a hitelesített felhasználói azonosítóval van megjelölve, hogy csak olvasható vagy a saját rekordok frissítése.  A következő tábla definícióját tartalmazza ezt a funkciót:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Műveletek általában a lekérdezés végrehajtása rendelkezik egy lekérdezés tulajdonságot, amelynek módosíthatja, a where záradékban. A lekérdezés tulajdonság egy [QueryJS] egy OData-lekérdezés konvertálása valamilyen, amely képes az adatok háttérbeli használt objektum.  Egyszerű egyenlőség esetekben (például az előző) térképre használhatók. SQL záradékokat is hozzáadhat:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Útmutató: egy helyreállítható törlésre konfigurálása
Helyreállítható törlésre ténylegesen nem törli a rekordok.  Ehelyett azt jelöli meg azokat a törölt oszlop true értékre állításával az adatbázis törlése.  Az Azure Mobile Apps SDK automatikusan eltávolítja helyreállíthatóan törölt rekordok eredmények, kivéve, ha a mobileszköz ügyfél SDK IncludeDeleted() használja.  Egy helyreállítható törlésre tábla konfigurálásához állítsa a `softDelete` tulajdonság a tábla szolgáltatásdefiníciós fájlban:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Létre kell hoznia egy olyan mechanizmus kiürítése rekordok - vagy a ügyfélalkalmazás keresztül webjobs-feladat, Azure-függvény, vagy egy egyéni API-n keresztül.

### <a name="howto-tables-seeding"></a>Útmutató: az adatbázis adatokkal magtípusú leképezést
Amikor egy új alkalmazást hoz létre, érdemes rendezi az adatokat tartalmazó táblát.  Ezt megteheti a tábla definícióját JavaScript-fájlon belüli az alábbiak szerint:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Az adatok összehangolása csak végre, ha az Azure Mobile Apps SDK hozta létre a tábla.  Ha a tábla már létezik az adatbázisban, a program a táblába beszúrta nincsenek adatok.  Ha dinamikus séma be van kapcsolva, majd a séma van következtetni a kiemelt adatokat.

Azt javasoljuk, hogy explicit módon meghívja a `tables.initialize()` metódus fut a szolgáltatás indulásakor a tábla létrehozásához.

### <a name="Swagger"></a>Útmutató: a Swagger-támogatás engedélyezése
Az Azure App Service Mobile Apps mellékelt beépített [Swagger] támogatja.  A Swagger-támogatásának engedélyezése, először telepítenie kell a swagger felhasználói felület a függőség beállításához:

    npm install --save swagger-ui

A telepítést követően engedélyezheti a Swagger-támogatás az Azure Mobile Apps-konstruktor:

    var mobile = azureMobileApps({ swagger: true });

Ön valószínűleg csak szeretné fejlesztési kiadásai Swagger-támogatás engedélyezése.  Ehhez felügyelniük a `NODE_ENV` Alkalmazásbeállítás:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

A swagger-végpont nem található: http://*yoursite*.azurewebsites.net/swagger.  A Swagger felhasználói felületén keresztül érheti el a `/swagger/ui` végpont.  Ha a teljes alkalmazás közötti hitelesítés szükséges, a Swagger hibát hoz létre.  A legjobb eredmények elérése érdekében válassza ki, hogy lehetővé tegyék a nem hitelesített kérelmek használatával az Azure App Service Authentication / engedélyezési beállításait, majd vezérlőt használatával a `table.access` tulajdonság.

A Swagger beállítással is hozzáadhat a `azureMobile.js` fájlt, ha azt szeretné csak Swagger támogatási helyileg fejlesztése során.

## <a name="a-namepushpush-notifications"></a><a name="push">Leküldéses értesítések
Mobile Apps integrálja az Azure Notification Hubs ahhoz, hogy célzott leküldéses értesítések küldésére eszközök millióira minden fő platformon. A Notification Hubs használatával leküldéses értesítéseket küldhet az iOS, Android és Windows rendszerű eszközökhöz. A Notification hubs használatával elvégezhető az összes kapcsolatos további információkért lásd: [Notification Hubs – áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Hogyan: leküldéses értesítések küldése
A következő kód bemutatja, hogyan küldhet leküldéses objektum leküldéses értesítések a regisztrált iOS-eszközök:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Az ügyfél egy sablon leküldéses regisztrációs létrehozásával is inkább egy sablon leküldéses üzenetet küld eszközök az összes támogatott platformon. A következő kód bemutatja, hogyan sablon értesítést küldhet:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Útmutató: egy hitelesített felhasználó számára címkék használatával leküldéses értesítések küldése
Amikor a hitelesített felhasználó regisztrálja a leküldéses értesítések, a felhasználói azonosító címke automatikusan hozzáadódik a regisztráció. Ezt a címkét használatával küldhet leküldéses értesítéseket egy adott felhasználó által regisztrált összes eszközt. Az alábbi kód lekérdezi a kérelmező felhasználó biztonsági azonosítója és minden eszköz regisztrálása az adott felhasználó sablon leküldéses értesítést küld:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Amikor regisztrál egy hitelesített ügyfél leküldéses értesítésekhez, győződjön meg arról, hogy a hitelesítési teljes regisztrációs megkísérlése előtt.

## <a name="CustomAPI"></a>Egyéni API-k
### <a name="howto-customapi-basic"></a>Hogyan: Adja meg egy egyéni API
Az adatok hozzáférésének API a /tables végpont, mellett Azure Mobile Apps is biztosít egyéni API.  Egyéni API-k a definíciói hasonló módon határozza meg, és hozzáférhet ugyanazokat létesítményekben, például a hitelesítés.

Ha szeretné használni az App Service hitelesítés egy egyéni API-t, konfigurálnia kell a App Service hitelesítés a [Azure-portálon] első.  Az Azure App Service hitelesítés konfigurálásával kapcsolatos további részletekért tekintse át a konfigurációs útmutató szeretne használni az identitásszolgáltató számára:

* [Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [A Microsoft Authentication konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Egyéni API-k sokkal ugyanúgy, mint a táblák API vannak definiálva.

1. Hozzon létre egy **api** könyvtár
2. Az API definition JavaScript fájl létrehozása a **api** könyvtár.
3. Az importálás módszer használatával importálja a **api** könyvtár.

Ez a korábbi használtuk az alkalmazáson belüli basic minta alapján prototípus api-definíció.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Vegyünk egy példát a kiszolgálóra dátum történő visszaadó API a *Date.now()* metódust.  A api/date.js fájl itt található:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Egyes paraméterek egyike a szabványos RESTful HTTP-parancsokat - GET, POST, javítását vagy törlése.  A metódus egy szabványos [ExpressJS köztes] függvény, amelyet a kimeneti küld.

### <a name="howto-customapi-auth"></a>Útmutató: hitelesítés egy egyéni API eléréséhez szükséges
Az Azure Mobile Apps SDK hitelesítés a táblák végpont és az egyéni API-k ugyanúgy valósítja meg.  Hitelesítés hozzáadása az előző szakaszban létrehozott API-hoz, vegye fel egy **hozzáférés** tulajdonság:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

A konkrét műveletek is hitelesítési adhatja meg:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Ugyanezt a tokent a táblák végpont használt hitelesítést igénylő, egyéni API-kat kell használni.

### <a name="howto-customapi-auth"></a>Hogyan: nagy fájlfeltöltéseket kezelése
Az Azure Mobile Apps SDK-t használ a [törzs-elemző köztes](https://github.com/expressjs/body-parser) elfogadásához és dekódolási törzs tartalma a jelentkezést.  Törzs-elemző nagyobb fájlfeltöltések fogadásához előre konfigurálhatja:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

A fájl nem base-64 kódolású továbbítás előtt.  Ez növeli a tényleges feltöltés méretét (és így mérete figyelembe kell venni).

### <a name="howto-customapi-sql"></a>Útmutató: egyéni SQL-utasítások végrehajtása
Az Azure Mobile Apps SDK lehetővé teszi a hozzáférést a teljes környezet a request objektumon keresztül lehetővé téve a paraméteres SQL utasítást, hogy az adott szolgáltató egyszerűen hajtható végre:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Hibakeresés, könnyen táblák és egyszerű API-k
### <a name="howto-diagnostic-logs"></a>Hogyan: hibakeresési, diagnosztizálása és elhárítása az Azure Mobile apps
Az Azure App Service számos Hibakeresés és hibaelhárítási módszerekről a Node.js-alkalmazások biztosít.
Ismerkedés a Node.js mobil-háttéralkalmazás elhárításához a következő cikkekben talál:

* [Az Azure App Service figyelése]
* [Az Azure App Service diagnosztikai naplózás engedélyezése]
* [A Visual Studio egy Azure App Service hibaelhárítása]

NODE.js-alkalmazások hozzáférhetnek a diagnosztikai naplófájl eszközök széles skáláját.  Belsőleg, az Azure Mobile Apps Node.js SDK használ [Winston] diagnosztikai naplózás.  Automatikusan naplózását hibakeresési mód engedélyezésével, illetve a **MS_DebugMode** Alkalmazásbeállítás igaz értékű a [Azure-portálon]. A diagnosztikai naplók létrehozott naplók jelennek meg a [Azure-portálon].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Útmutató: egyszerű táblák használata az Azure-portálon
Egyszerű táblák a portálon lehetővé teszik, hogy létrehozása és használata táblákat közvetlenül a portálon. A dataset CSV formátumban feltöltheti könnyen táblákat. Vegye figyelembe, hogy nem használhatja a Tulajdonságok nevek (a fürt megosztott kötetei szolgáltatás dataset) ütköző Azure Mobile Apps-háttéralkalmazás tulajdonságok nevét. A rendszer tulajdonságok nevek a következők:
* createdAt
* updatedAt
* törölve
* verzió:

Az App Service-szerkesztővel tábla műveletek akár szerkesztheti is. Amikor rákattint **könnyen táblák** a háttér-webhely beállításai hozzáadása, módosítása és törlése egy tábla. Megtekintheti a tábla adatai is.

![A könnyen táblázatok használata](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Az alábbi parancsokat a táblázat parancssávon érhetők el:

* **Engedélyek módosítása** - módosítási olvasási engedéllyel, beszúrása, frissítése és törlési műveletek a táblában.
  Is, hogy engedélyezze a névtelen hozzáférést, a hitelesítés szükséges, vagy tiltsa le a műveletet az elérésére.
* **Parancsfájl szerkesztése** -a parancsfájl a következő táblázatban a App Service-szerkesztőben van megnyitva.
* **Séma kezelése** - hozzáadása vagy oszlopok törlése vagy a tábla index módosítása.
* **Törölje a jelet tábla** -csonkolja a meglévő tábla kell az összes adat sorok törléséhez, de a séma hagyja változatlanul.
* **Sorok törlése** -egyedi sornyi adatot törli.
* **A folyamatos átviteli naplók megtekintése** -csatlakoztatja a helyhez a folyamatos átviteli naplózási szolgáltatás.

### <a name="work-easy-apis"></a>Útmutató: egyszerű API-k használata az Azure-portálon
A portál egyszerű API-k lehetővé teszik, hogy létrehozása és használata egyéni API-k közvetlenül a portálon. Az App Service-szerkesztővel API parancsfájlok szerkesztheti.

Amikor rákattint **egyszerű API-k** a háttér-webhely beállításai hozzáadása, módosítása és egy egyéni API-végpont törlése.

![Egyszerű API-khoz](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

A portál egy adott HTTP-művelet hozzáférési engedélyeinek módosítása, az API-parancsfájlt a App Service-szerkesztőben szerkesztheti vagy a folyamatos átviteli naplók megtekintése.

### <a name="online-editor"></a>Útmutató: a App Service-szerkesztőben kód szerkesztése
Az Azure portál segítségével anélkül, hogy a projekt letöltése a helyi számítógépen a Node.js háttérrendszer parancsprogramok a App Service-szerkesztőben szerkesztheti. Az online szerkesztő parancsfájlok szerkesztése:

1. A mobilalkalmazás-háttérrendszer paneljén kattintson **összes beállítás** > vagy **könnyen táblák** vagy **egyszerű API-k**, egy táblázatot vagy API-t, majd **parancsfájl szerkesztése**. A parancsfájl a App Service-szerkesztőben van megnyitva.

    ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. A módosításokat a kódfájl online-szerkesztőben. Változások automatikusan mentése közben.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android ügyfél gyors üzembe helyezés]: app-service-mobile-android-get-started.md
[Apache Cordova-ügyfél gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[iOS ügyfél gyors üzembe helyezés]: app-service-mobile-ios-get-started.md
[Xamarin.iOS ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-forms-get-started.md
[A Windows Store ügyfél gyors üzembe helyezés]: app-service-mobile-windows-store-dotnet-get-started.md
[offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[Azure Active Directory-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Google-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[A Microsoft Authentication konfigurálása]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service telepítési útmutató]: ../app-service/app-service-deploy-local-git.md
[Az Azure App Service figyelése]: ../app-service/web-sites-monitor.md
[Az Azure App Service diagnosztikai naplózás engedélyezése]: ../app-service/web-sites-enable-diagnostic-log.md
[A Visual Studio egy Azure App Service hibaelhárítása]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[a csomópont verzió megadása]: ../nodejs-specify-node-version-azure-apps.md
[csomópont modulok használata]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure-portálon]: https://portal.azure.com/
[OData]: http://www.odata.org
[ígéret]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp mintát a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo mintát a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[minták directory a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js eszközök 1.1 a Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js csomag]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS köztes]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
