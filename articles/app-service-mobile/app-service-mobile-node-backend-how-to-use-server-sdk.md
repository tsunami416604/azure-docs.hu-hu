---
title: Hogyan használható a Node.js háttér-Server SDK-val a Mobile Apps |} Microsoft Docs
description: Megtudhatja, hogyan használható a Node.js háttér-Server SDK az Azure App Service Mobile Apps a.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 335186deccaa82b9a8d262d62dd8ce5d620446b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>A Mobile Apps Node.js SDK használatával
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a cikk részletes információkat tartalmazza, és az Azure App Service Mobile Apps szolgáltatásának a háttér használata a Node.js példák.

## <a name="Introduction"></a>Bevezetés
Mobile Apps lehetővé teszi a mobil optimalizált adatelérési Web API hozzáadása egy webalkalmazást. A Mobile Apps SDK-t az ASP.NET és a Node.js webalkalmazás valósul meg. Az SDK tartalmazza a következő műveleteket:

* Tábla műveletek (olvasni, beszúrása, frissítése és törlése) az adatok elérése
* Egyéni API-műveletek

Mindkét műveleti hitelesítéshez biztosít Azure App Service lehetővé teszi, hogy minden identitás-szolgáltatóktól. Ezek a szolgáltatók közé tartozik a közösségi Identitásszolgáltatók például a Facebook, Twitter, Google, és a Microsoft, valamint Azure Active Directory vállalati identitás.

Minták találhat meg minden esetben használja a [minták directory a Githubon].

## <a name="supported-platforms"></a>Támogatott platformok
A Mobile Apps Node.js SDK támogatja a jelenlegi LTS verzió, csomópont és újabb verziók. A legújabb LTS jelenleg csomópont v4.5.0. Egyéb verziói csomópont működik, de nem támogatottak.

A Mobile Apps Node.js SDK két adatbázis-illesztőprogram támogatja: 

* A csomópont-mssql illesztőprogram támogatja az Azure SQL Database és a helyi SQL Server-példányokat.  
* A sqlite3 illesztőprogram csak egyetlen példányán SQLite adatbázisokat támogatja.

### <a name="howto-cmdline-basicapp"></a>Hozzon létre egy alapszintű Node.js háttér a parancssor használatával
Minden Mobile Apps Node.js háttérrendszer ExpressJS alkalmazásként indul. ExpressJS a legnépszerűbb web service keretrendszer Node.js érhető el. Létrehozhat egy alapszintű [Express] alkalmazás az alábbiak szerint:

1. Egy parancs vagy a PowerShell-ablakot hozzon létre egy könyvtárat a projekthez:

        mkdir basicapp
2. Futtatás `npm init` a csomag struktúra inicializálása:

        cd basicapp
        npm init

   A `npm init` parancs kéri a projekt inicializálása kérdésekre. Tekintse meg a példa az alábbiakat:

   ![A npm init kimenet][0]
3. Telepítse a `express` és `azure-mobile-apps` összetevőtárházat npm könyvtárak:

        npm install --save express azure-mobile-apps
4. Hozza létre az app.js fájlban az alapvető mobil server megvalósításához:

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table.
        mobile.tables.add('TodoItem');

        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);

Ez az alkalmazás egy végpontot hoz létre egy mobile optimalizált webes API-t (`/tables/TodoItem`), amely egy alapul szolgáló SQL-adattároló nem hitelesített hozzáférést biztosít a dinamikus séma használatával. Alkalmas az ügyfél szalagtár quickstarts a következő:

* [Android ügyfél gyors üzembe helyezés]
* [Apache Cordova-ügyfél gyors üzembe helyezés]
* [iOS ügyfél gyors üzembe helyezés]
* [A Windows Store ügyfél gyors üzembe helyezés]
* [Xamarin.iOS ügyfél gyors üzembe helyezés]
* [Xamarin.Android ügyfél gyors üzembe helyezés]
* [Xamarin.Forms-ügyfél gyors üzembe helyezés]

Ezen alapszintű alkalmazás találja a kódot a [basicapp mintát a Githubon].

### <a name="howto-vs2015-basicapp"></a>A Node.js háttérből létrehozása a Visual Studio 2015 használatával
Visual Studio 2015-öt bővítménye belül az IDE Node.js-alkalmazások fejlesztéséhez szükséges. Indítsa el, telepítse a [Node.js eszközök 1.1 a Visual Studio]. A telepítés befejezése után hozzon létre egy Express 4.x alkalmazást:

1. Nyissa meg a **új projekt** párbeszédpanelen (a **fájl** > **új** > **projekt**).
2. Bontsa ki a **sablonok** > **JavaScript** > **Node.js**.
3. Válassza ki **alapszintű Azure Node.js Express 4 alkalmazás**.
4. Töltse ki a projekt nevét. Kattintson az **OK** gombra.

   ![A Visual Studio 2015-öt új projekt][1]
5. Kattintson a jobb gombbal a **npm** csomópont, és válassza **telepítése új npm csomagok**.
6. Szükség lehet frissíteni az npm-katalógus az első Node.js-alkalmazás létrehozása után. Válassza ki **frissítése** szükség esetén.
7. Adja meg **azure-mobilalkalmazások** be a keresőmezőbe. Válassza ki a **azure-mobileszköz-alkalmazások 2.0.0** csomagot, majd válassza ki **csomagtelepítés**.

   ![Új npm-csomagok][2]
8. Válassza ki **Bezárás**.
9. Nyissa meg az app.js fájl vehető támogatásához a Mobile Apps SDK-t. At sor 6 at a szalagtár alsó `require` utasítások, adja hozzá a következő kódot:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

   A másik után körülbelül sor 27 at `app.use` utasítások, adja hozzá a következő kódot:

        app.use('/users', users);

        // Mobile Apps initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

   Mentse a fájlt.
10. Vagy az alkalmazás helyileg történő futtatása (az API-t a kiszolgált http://localhost:3000) vagy közzététele az Azure-bA.

### <a name="create-node-backend-portal"></a>A Node.js háttérből létrehozása az Azure-portál használatával
A Mobile Apps háttérből közvetlenül hozhat létre a a [Azure-portálon]. Kövesse az alábbi lépéseket, vagy hozzon létre egy ügyfél és kiszolgáló együtt a [mobilalkalmazás létrehozása](app-service-mobile-ios-get-started.md) oktatóanyag. Az oktatóanyag ezek az utasítások egyszerűsített verzióját tartalmazza, és a koncepció igazolása projektek.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vissza a **Ismerkedés** ablaktáblán, a **tábla API létrehozása**, válassza a **Node.js** háttér-nyelv.
Jelölje be a **tudomásul veszem, hogy ezzel felülírja a webhely teljes tartalmát**, majd válassza ki **TodoItem tábla létrehozása**.

### <a name="download-quickstart"></a>Töltse le a Node.js háttér-gyors üzembe helyezés kódú projektben a Git használatával
Amikor a Node.js Mobile Apps háttérből hoz létre a portál használatával **gyors üzembe helyezési** ablaktáblán egy Node.js-projektet, létrehozása és telepítése a helyen. A portálon adja hozzá a táblák és API-kat, és a Node.js háttérrendszer tartozó kódfájlok szerkesztése. Különböző központi telepítési eszközök segítségével is, hogy vegye fel vagy módosítsa a táblák és API-k, majd tegye közzé a projekt, töltse le a háttér-projektet. További információkért lásd: a [Azure App Service telepítési útmutató]. 

Az alábbi eljárás egy Git-tárház használatával töltse le a gyors üzembe helyezési projekt kódot:

1. Ha még nem tette, telepítse a Git esetében. A Git telepítéséhez szükséges lépések eltérőek, operációs rendszerek között. Operációsrendszer-specifikus disztribúcióiról, valamint a telepítési útmutatót lásd: [telepítése Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Lásd: [készítse elő a tárház](../app-service/app-service-deploy-local-git.md#prepare-your-repository) ahhoz, hogy a háttér-webhely a Git-tárházat. Jegyezze fel a központi telepítés felhasználónevet és jelszót.
3. A panelen a a Mobile Apps háttér, jegyezze fel a **Git-klón URL-cím** beállítást.
4. Hajtsa végre a `git clone` parancsot a Git-klón URL-cím használatával. Írja be a jelszót, ha szükséges, az alábbi példában látható módon:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Keresse meg a helyi címtárszolgáltatásban (`/todolist` az előző példában), és figyelje meg, hogy project fájlok le vannak töltve. Az todoitem.json fájlban keresse meg a `/tables` könyvtár. Ez a fájl engedélyeit a tábla határozza meg. A todoitem.js fájl is ugyanabban a könyvtárban található. Meghatározza a CRUD művelet parancsfájlok a következő táblázatban.
6. Project fájlok, a következő parancsokat hozzáadása, módosítása után véglegesíthető, majd töltse fel az a módosításokat a helyhez:

        $ git commit -m "updated the table script"
        $ git push origin master

   Amikor új fájlok hozzáadása a projekthez, először futtassa a `git add .` parancsot.

A hely minden alkalommal, amikor egy új készletét véglegesíti a rendszer előkészítésre továbbít a webhely ismételt közzététele.

### <a name="howto-publish-to-azure"></a>A Node.js háttér közzététele az Azure-bA
A Microsoft Azure biztosít a Mobile Apps Node.js-közzététel számos mechanizmust háttér az Azure szolgáltatásban. Ezek a mechanizmusok központi telepítési eszközöket a Visual Studio integrált, a parancssori eszközök és a folyamatos üzembe helyezés beállítások verziókezelő alapján. További információkért lásd: a [Azure App Service telepítési útmutató].

Az Azure App Service rendelkezik konkrét útmutatásért tekintse át Node.js-alkalmazások közzététele a háttér előtt:

* Hogyan [a csomópont verzió megadása]
* Hogyan [csomópont modulok használata]

### <a name="howto-enable-homepage"></a>Az alkalmazás kezdőlapját engedélyezése
Számos alkalmazás webes és mobilalkalmazások. A ExpressJS keretrendszer segítségével kombinálhatja a két értékkorlátozást. Egyes esetekben azonban érdemes csak a mobil felület megvalósításához. Hasznos egy kezdőlapját győződjön meg arról, hogy az alkalmazás szolgáltatás működik-e és fut. Adja meg a saját kezdőlapján, vagy egy ideiglenes kezdőlap engedélyezése. Ahhoz, hogy egy ideiglenes kezdőlapját, az alábbi kód segítségével hozható létre a Mobile Apps:

    var mobile = azureMobileApps({ homePage: true });

Ha csak ez a beállítás érhető el helyi fejlesztésekor, ezt a beállítást a azureMobile.js fájl is hozzáadhat.

## <a name="TableOperations"></a>Tábla műveletek
Az azure-mobilalkalmazások Node.js Server SDK teszi közzé az Azure SQL Database, a webes API tárolt adattáblák mechanizmust biztosít. Öt műveleteket tartalmazza:

| Művelet | Leírás |
| --- | --- |
| GET /tables/*táblanév* |Az összes rekord beolvasása a táblában. |
| GET /tables/*tablename*/:id |Első adott rekord a táblázatban. |
| POST /tables/*táblanév* |Hozzon létre egy rekordot a táblában. |
| JAVÍTÁS /tables/*tablename*/:id |Módosítani egy rekordot a táblában. |
| TÖRLÉS /tables/*tablename*/:id |A tábla rekord törlése. |

A webes API támogatja [OData] és bővíti a következő tábla sémáját támogatásához [offline adatszinkronizálás].

### <a name="howto-dynamicschema"></a>Adja meg a táblák dinamikus séma használatával
Egy tábla használata előtt meg kell adni. Meghatározhatja a táblák egy statikus séma segítségével (Itt adhatja meg az oszlopok a sémában) vagy dinamikusan (ha az SDK szabályozza a séma, a bejövő kérések alapján). A webes API-t adott aspektusainak továbbá JavaScript-kód hozzáadása a definíció szabályozhatja.

Ajánlott eljárásként, JavaScript fájlban minden tábla érdemes definiálni a `tables` könyvtár, majd használja a `tables.import()` importálhatja a táblákat metódust. Az alkalmazáson belüli basic minta kiterjesztése esetén lenne módosíthatja az app.js fájlban:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed.
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined.
    mobile.tables.initialize().then(function () {
        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);
    });

Adja meg a táblázatban szereplő. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here.

    module.exports = table;

Táblák alapértelmezés szerint dinamikus sémát használja. A dinamikus séma globálisan kikapcsolásához állítsa be a `MS_DynamicSchema` Alkalmazásbeállítás FALSE értékre az Azure portálon.

A teljes példáját megtalálhatja a [todo mintát a Githubon].

### <a name="howto-staticschema"></a>Adja meg a táblák statikus séma használatával
Explicit módon határozhatja meg a webes API-n keresztül teszi közzé az oszlopokat. Az azure-mobilalkalmazások Node.js SDK automatikusan hozzáadja a listában, hogy az offline adatszinkronizálás szükséges további oszlopokat. Például a gyors üzembe helyezés ügyfélalkalmazások szükséges két oszlopokkal rendelkező táblát: `text` (karakterlánc) és `complete` (logikai érték).  
A tábla adható meg a tábla definícióját JavaScript-fájlt (található, a `tables` könyvtár) az alábbiak szerint:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    module.exports = table;

Ha statikusan határozza meg a táblák, is hívja meg a `tables.initialize()` az adatbázis-séma létrehozása indításkor metódust. A `tables.initialize()` metódus értéket ad vissza egy [ígéret] , hogy a webszolgáltatás nem teljesíti a kérelmeket az adatbázis inicializálása előtt.

### <a name="howto-sqlexpress-setup"></a>SQL Server Expresst használ a helyi gépen fejlesztési adattárként
A Mobile Apps Node.js SDK-t, de az adatok a kezdő verzióról három lehetőséget kínál:

* Használja a **memória** egy nem állandó példa tárolót biztosítanak az illesztőprogramot.
* Használja a **mssql** adjon meg egy SQL Server Express adattár fejlesztési az illesztőprogramot.
* Használja a **mssql** adjon meg egy Azure SQL Database adattároló üzemi az illesztőprogramot.

A Mobile Apps Node.js SDK-t használ a [mssql Node.js csomag] létrehozásához és az SQL Server Express és SQL-adatbázis kapcsolat használatára. A csomag igényli-e, hogy az SQL Server Express-példány TCP-kapcsolatok engedélyezése.

> [!TIP]
> A memória illesztőprogram nem biztosít tesztelési létesítményekben teljes készletét. Ha helyileg a háttér vizsgálni kívánt, egy SQL Server Express-tárolót és az mssql illesztőprogram használatát javasoljuk.
>
>

1. Töltse le és telepítse [Microsoft SQL Server 2014 Express]. Győződjön meg arról, hogy telepítse az SQL Server 2014 Express eszközök kiadásával. Ha 64 bites támogatás kifejezetten szüksége a 32 bites kevesebb memóriát igényel, futtatásakor.
2. Futtassa az SQL Server 2014 Konfigurációkezelő:

   a. Bontsa ki a **SQL Server hálózati konfigurációja** a fa menü csomópontja.

   b. Válassza ki **SQLEXPRESS protokollok**.

   c. Kattintson a jobb gombbal **TCP/IP** válassza **engedélyezése**. Válassza ki **OK** az előugró párbeszédpanelen.

   d. Kattintson a jobb gombbal **TCP/IP** válassza **tulajdonságok**.

   e. Válassza ki a **IP-címek** fülre.

   f. Keresés a **IPAll** csomópont. Az a **TCP-Port** mezőbe írja be **1433**.

      ![Az SQL Server Express TCP/IP konfigurálása][3]

   g. Kattintson az **OK** gombra. Válassza ki **OK** az előugró párbeszédpanelen.

   h. Válassza ki **SQL Server Services** a fa menüben.

   i. Kattintson a jobb gombbal **SQL Server (SQLEXPRESS)** válassza **indítsa újra a**.

   j. Zárja be az SQL Server 2014 Configuration Manager.
3. Futtassa az SQL Server 2014 Management Studio eszközt, és csatlakozni a helyi SQL Server Express példány:

   1. Kattintson a jobb gombbal az Object Explorerben a példányát, és válassza ki **tulajdonságok**.
   2. Válassza ki a **biztonsági** lap.
   3. Győződjön meg arról, hogy **SQL Server és a Windows-hitelesítés üzemmód** van kiválasztva.
   4. Kattintson az **OK** gombra.

      ![SQL Server Express-hitelesítés konfigurálása][4]
   5. Bontsa ki a **biztonsági** > **bejelentkezések** az Object Explorerben.
   6. Kattintson a jobb gombbal **bejelentkezések** válassza **új bejelentkezés**.
   7. Adja meg a bejelentkezési nevet. Kattintson az **SQL Server-hitelesítés** lehetőségre. Adjon meg egy jelszót, és írja be ugyanezt a jelszót **jelszó megerősítése**. A jelszónak meg kell felelnie a Windows bonyolultsági követelményeknek.
   8. Kattintson az **OK** gombra.

      ![Új felhasználó hozzáadása SQL Server Express][5]
   9. Kattintson a jobb gombbal az új bejelentkezési adatait, és válassza ki **tulajdonságok**.
   10. Válassza ki a **kiszolgálói szerepkörök** lap.
   11. Jelölje be a jelölőnégyzetet a **dbcreator** kiszolgálói szerepkört.
   12. Kattintson az **OK** gombra.
   13. Zárja be az SQL Server 2015 Management Studio eszközt.

Ne felejtse el a felhasználónevet és jelszót, verziószámával. Szükség lehet további kiszolgálói szerepkörök vagy engedélyek, attól függően, hogy az adatbázis-követelmények hozzárendelni.

A Node.js-alkalmazás olvasása a `SQLCONNSTR_MS_TableConnectionString` környezeti változó a kapcsolati karakterlánc ehhez az adatbázishoz. Ez a változó beállíthatja a környezetében. Például a PowerShell segítségével a környezeti változót:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP-kapcsolaton keresztül érik el az adatbázist. Adjon meg egy felhasználónevet és jelszót a kapcsolathoz.

### <a name="howto-config-localdev"></a>A helyi fejlesztési projekt konfigurálása
Mobile Apps beolvassa a JavaScript-fájl neve *azureMobile.js* a helyi fájlrendszer. A Mobile Apps SDK konfigurálásához éles környezetben ne használja ezt a fájlt. Ehelyett használjon **Alkalmazásbeállítások** a a [Azure-portálon]. 

A azureMobile.js fájl exportálja a konfigurációs objektum. A leggyakrabban használt beállítások a következők:

* Adatbázis-beállítások
* Diagnosztikai naplózás beállításait
* Alternatív CORS-beállítások

Ez a példa azureMobile.js fájl valósítja meg az előző adatbázis-beállításai:

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

Azt javasoljuk, hogy hozzá azureMobile.js a .gitignore fájlt (vagy más verziókövetési figyelmen kívül hagyja a fájlt) a felhőben tárolt jelszavak megelőzése érdekében. Mindig konfigurálni az éles beállításait **Alkalmazásbeállítások** belül a [Azure-portálon].

### <a name="howto-appsettings"></a>A mobilalkalmazás-beállításainak konfigurálása
A azureMobile.js fájlban a legtöbb beállítás rendelkezik egyenértékű Alkalmazásbeállítás a [Azure-portálon]. Az alábbi lista segítségével állítsa be alkalmazását a **Alkalmazásbeállítások**:

| Alkalmazás beállítása | azureMobile.js setting | Leírás | Érvényes értékek |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |név |Az alkalmazás nevét |karakterlánc |
| **MS_MobileLoggingLevel** |Logging.level |Naplózandó üzenetek minimális naplózási szint |Hiba, figyelmeztetés, információ, részletes, hibakeresési silly |
| **MS_DebugMode** |hibakeresés |Engedélyezheti vagy letilthatja a hibakeresési mód |IGAZ, hamis |
| **MS_TableSchema** |data.schema |SQL-táblák alapértelmezett séma neve |karakterlánc (alapértelmezett: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Engedélyezheti vagy letilthatja a hibakeresési mód |IGAZ, hamis |
| **MS_DisableVersionHeader** |verzió (a nem definiált beállítása) |Az X-ZUMO-kiszolgáló-Version fejlécnek letiltása |IGAZ, hamis |
| **MS_SkipVersionCheck** |skipversioncheck |Letiltja az ügyfél API-verziójának ellenőrzése |IGAZ, hamis |

Egy Alkalmazásbeállítás beállítása:

1. Jelentkezzen be az [Azure-portálon].
2. Válassza ki **összes erőforrás** vagy **alkalmazásszolgáltatások**, majd válassza a mobilalkalmazás nevét.
3. A **beállítások** ablaktábla alapértelmezés szerint megnyílik. Ha nem, és válassza az **beállítások**.
4. Az a **általános** menü **Alkalmazásbeállítások**.
5. Görgessen a **Alkalmazásbeállítások** szakasz.
6. Ha az alkalmazás, beállítás már létezik, válassza ki az Alkalmazásbeállítás értéke pedig az az érték szerkesztéséhez.
   Ha az alkalmazás nem létezik, adja meg az Alkalmazásbeállítás a a **kulcs** bezárásához és az értéket a **érték** mezőbe.
8. Kattintson a **Mentés** gombra.

A legtöbb alkalmazás beállításainak módosítása a szolgáltatás újraindítását igényli.

### <a name="howto-use-sqlazure"></a>Az SQL Database, a termelési adatok tárolásához
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Az Azure SQL Database adattárként használata azonos Azure App Service-alkalmazás összes típusa. Ha Ön rendelkezik nem tette, kövesse az alábbi lépéseket a Mobile Apps háttérből létrehozásához:

1. Jelentkezzen be az [Azure-portálon].
2. Az ablak bal felső sarokban válassza ki a **+ új** gomb > **Web + mobil** > **mobilalkalmazás**, majd meg egy nevet a Mobile Apps háttér és.
3. Az a **erőforráscsoport** mezőbe írja be a néven az alkalmazáshoz.
4. Az alapértelmezett App Service-csomag van kiválasztva. Ha azt szeretné, az App Service-csomag módosítása:

   a. Válassza ki **App Service-csomag** > **+ új**. 
   
   b. Adja meg az új App Service-csomag nevét, és válasszon egy megfelelő helyet. 
   
   c. Válasszon egy megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **összes** több, mint az beállítások árképzési nézetre **szabad** és **megosztott**. 
   
   d. Kattintson a **válasszon** gombra. 
   
   e. Vissza a **App Service-csomag** ablaktáblán válassza előbb **OK**.
5. Kattintson a **Létrehozás** gombra. 

A Mobile Apps-kiépítés háttér is igénybe vehet néhány percet. Miután a Mobile Apps biztonsági end ki van építve, a portál megnyitja a **beállítások** a Mobile Apps háttér ablaktábla.

Ha szeretné, vagy egy meglévő SQL-adatbázis csatlakoztatása a Mobile Apps háttér, vagy hozzon létre egy új SQL-adatbázis. Ebben a szakaszban a Microsoft SQL-adatbázis létrehozása.

> [!NOTE]
> Ha Ön már rendelkezik adatbázissal ugyanazon a helyen, a Mobile Apps háttér, ehelyett válassza **meglévő adatbázis használata** , és válassza ki, hogy az adatbázis. Egy másik helyen található adatbázis használata magasabb késések miatt nem ajánlott.
>
>

1. Válassza ki az új Mobile Apps háttér, **beállítások** > **mobilalkalmazás** > **adatok** > **+ Hozzáadás**.
2. Az a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **SQL adatbázis - kötelező beállítások konfigurálása** > **hozzon létre egy új adatbázist**. Adja meg az új adatbázis nevét a **neve** mezőbe.
3. Válassza ki **Server**. A a **új kiszolgáló** panelen adjon meg egy egyedi kiszolgálónevet a a **kiszolgálónév** mezőbe, majd adjon meg egy megfelelő kiszolgálót, rendszergazda felhasználónevet és jelszót. Győződjön meg arról, hogy **azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** van kiválasztva. Kattintson az **OK** gombra.

   ![Azure SQL-adatbázis létrehozása][6]
4. Az a **új adatbázis** ablaktáblán válassza előbb **OK**.
5. Vissza a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **kapcsolati karakterlánc**, és adja meg a bejelentkezési és az adatbázis létrehozásakor megadott jelszót. Ha egy meglévő adatbázist használ, a bejelentkezési hitelesítő adatok megadása, hogy az adatbázis. Kattintson az **OK** gombra.
6. Vissza a **adatkapcsolat hozzáadása** ablaktáblán ebben az esetben válassza **OK** létrehozni az adatbázist.

<!--- END OF ALTERNATE INCLUDE -->

Az adatbázis létrehozása néhány percet is igénybe vehet. Használja a **értesítések** kell figyelni a telepítés előrehaladását. Nem folytatódni mindaddig, amíg az adatbázis sikeresen telepítve lett. Az adatbázis telepítése után egy kapcsolati karakterláncot a Mobile Apps háttér-Alkalmazásbeállítások az SQL-adatbázispéldány jön létre. Láthatja, hogy ez az Alkalmazásbeállítás **beállítások** > **Alkalmazásbeállítások** > **kapcsolati karakterláncok**.

### <a name="howto-tables-auth"></a>Hitelesítés megkövetelése a hozzáféréshez táblákhoz
Ha azt szeretné, az App Service hitelesítés használatára a `tables` végpont, konfigurálnia kell az App Service hitelesítési a [Azure-portálon] első. További információkért lásd: az identitásszolgáltató használni kívánt konfigurációs Útmutató:

* [Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Minden tábla tulajdonsága hozzáférés táblájához való hozzáférés vezérlésére használható. Az alábbi minta-hitelesítés használatához a statikusan megadott táblát tartalmazza.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

A hozzáférés tulajdonság a következő három érték egyikét veheti:

* *Névtelen* azt jelzi, hogy az ügyfélalkalmazás nélkül hitelesítési adatokat olvasni.
* *hitelesített* azt jelzi, hogy az ügyfélalkalmazás egy érvényes hitelesítési jogkivonatot a kérelmet kell küldenie.
* *letiltott* azt jelzi, hogy ez a táblázat jelenleg le van tiltva.

Ha a hozzáférési tulajdonság nincs definiálva, nem hitelesített elérését.

### <a name="howto-tables-getidentity"></a>A táblák hitelesítési jogcímek használata
Beállíthat különböző hitelesítési beállításakor igényelt jogcímeket. Ezeket a jogcímeket amelyek általában telepítéseinél nem használhatók a `context.user` objektum. Azonban helyreállíthatók használatával a `context.user.getIdentity()` metódust. A `getIdentity()` metódus, amely egy objektum ígéret ad vissza. Az objektum kulccsal van ellátva, a hitelesítési módszerrel (`facebook`, `google`, `twitter`, `microsoftaccount`, vagy `aad`).

Például ha beállította a Microsoft-fiók hitelesítése és e-mail cím jogcím kérelem, adhat hozzá az e-mail cím a rekord, a következő táblázat a tartományvezérlő:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition.
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

    // Configure specific code when the client does a request.
    // READ: only return records that belong to the authenticated user.
    table.read(queryContextForEmail);

    // CREATE: add or overwrite the userId based on the authenticated user.
    table.insert(addEmailToContext);

    // UPDATE: only allow updating of records that belong to the authenticated user.
    table.update(queryContextForEmail);

    // DELETE: only allow deletion of records that belong to the authenticated user.
    table.delete(queryContextForEmail);

    module.exports = table;

Milyen jogcímeket érhetők el, használja a webböngésző megtekintéséhez a `/.auth/me` végpont a webhely.

### <a name="howto-tables-disabled"></a>Bizonyos táblájához műveletek való hozzáférés letiltása
Mellett a táblázatban szereplő, az access tulajdonság segítségével szabályozhatja az egyes műveletek. Nincsenek négy műveletet:

* `read` az a RESTful BEOLVASNI művelet a táblán.
* `insert` az a RESTful POST művelet a táblán.
* `update` az a RESTful javítás művelet a táblán.
* `delete` a tábla a RESTful DELETE művelet van.

Például egy olyan írásvédett nem hitelesített táblázat segítséget:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-only table. Only allow READ operations.
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>A lekérdezés tábla műveletek használt beállítása
Kötelező megadni a tábla műveletek arra, hogy az adatok korlátozott nézete. Például megadhatja, hogy csak olvasható vagy a saját rekordok frissítése a hitelesített felhasználói azonosítóval címkézett tábla. A következő tábla definícióját tartalmazza ezt a funkciót:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table.
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table.
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved.
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user.
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Műveletek általában a lekérdezés futtatása rendelkezik egy lekérdezés tulajdonságot, amelynek segítségével módosíthatja egy `where` záradékban. A lekérdezés tulajdonság egy [QueryJS] átalakítás OData-lekérésen valamit, hogy az adatok háttér használt objektum tud feldolgozni. Olyan egyszerű egyenlőség esetekben (például az előző) a térkép is használhatja. SQL záradékokat is hozzáadhat:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Egy tábla egy helyreállítható törlésre konfigurálása
Egy helyreállítható törlésre ténylegesen nem törli a rekordok. Ehelyett azt jelöli meg azokat a törölt oszlop true értékre állításával az adatbázis törlése. A Mobile Apps SDK automatikusan eltávolítja helyreállíthatóan törölt rekordok eredményeket, kivéve, ha a Mobile ügyfél SDK-t használ `IncludeDeleted()`. Egy tábla egy helyreállítható törlésre vonatkozó konfigurálásához állítsa a `softDelete` tulajdonság a tábla szolgáltatásdefiníciós fájlban:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Turn on soft delete.
    table.softDelete = true;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Ki kell alakítania egy olyan mechanizmus bejegyzések törlése: egy ügyfélalkalmazást, webjobs-feladat, egy Azure függvény vagy egy egyéni API-t.

### <a name="howto-tables-seeding"></a>Rendezze az adatokat tartalmazó adatbázis
Ha egy új alkalmazást hoz létre, érdemes rendezi az adatokat tartalmazó táblát. Ehhez a tábla definícióját JavaScript-fájlon belüli az alábbiak szerint:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Az adatok összehangolása történik, csak akkor, ha már használta a Mobile Apps SDK-t a tábla létrehozásához. Ha a tábla már létezik az adatbázisban, a rendszer nem adatok nézetmodellbe, a táblába. A dinamikus séma be van kapcsolva, ha a séma táblanévhez a kiemelt adatokból.

Azt javasoljuk, hogy explicit módon meghívja a `tables.initialize()` metódus fut a szolgáltatás indulásakor a tábla létrehozásához.

### <a name="Swagger"></a>A Swagger-támogatás engedélyezése
Mobile Apps mellékelt beépített [Swagger] támogatja. Swagger támogatását engedélyezi, először telepítenie kell a swagger-ui a függőség beállításához:

    npm install --save swagger-ui

Engedélyezheti a Swagger-támogatás a Mobile Apps-konstruktor majd:

    var mobile = azureMobileApps({ swagger: true });

Ön valószínűleg csak szeretné fejlesztési kiadásai Swagger-támogatás engedélyezése. Ehhez a a `NODE_ENV` Alkalmazásbeállítás:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

A `swagger` végpont itt található: http://*yoursite*.azurewebsites.net/swagger. A Swagger felhasználói felületén keresztül érheti el a `/swagger/ui` végpont. Ha a teljes alkalmazás közötti hitelesítés szükséges, a Swagger hibát hoz létre. A legjobb eredmények elérése érdekében válassza ki, hogy lehetővé tegyék a nem hitelesített kérelmek az Azure App Service hitelesítési/engedélyezési beállításaihoz, és majd hitelesítés használatával szabályozhatja a `table.access` tulajdonság.

Azt is megteheti a Swagger-beállítást a azureMobile.js fájl Ha kizárólag Swagger támogatási fejlesztési helyileg.

## <a name="a-namepushpush-notifications"></a><a name="push">Leküldéses értesítések
Mobile Apps integrálja az Azure Notification Hubs, célzott leküldéses értesítéseket küldhet eszközök millióira minden fő platformon. A Notification Hubs használatával leküldéses értesítéseket küldhet az iOS, Android és Windows eszközökhöz. A Notification hubs használatával elvégezhető az összes kapcsolatos további tudnivalókért tekintse meg a [Notification Hubs – áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Leküldéses értesítések küldése
A következő kód bemutatja, hogyan használható a `push` objektum egy leküldéses értesítést küldeni a regisztrált iOS-eszközök:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured.
    if (context.push) {
        // Send a push notification by using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Az ügyfél egy sablon leküldéses regisztrációs létrehozásával is inkább egy sablon leküldéses üzenetet küld eszközök az összes támogatott platformon. A következő kód bemutatja, hogyan sablon értesítést küldhet:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured.
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Leküldéses értesítések küldéséhez egy hitelesített felhasználó számára címkék használatával
Amikor a hitelesített felhasználó regisztrálja a leküldéses értesítések, a felhasználói azonosító címke automatikusan hozzáadódik a regisztráció. Ezt a címkét használatával küldhet leküldéses értesítéseket egy adott felhasználó által regisztrált összes eszközt. A következő kód jogosultságot kap a felhasználó, aki a kérést, és egy sablon leküldéses értesítést küld minden eszköz regisztrálása az adott felhasználó biztonsági azonosítója:

    // Only do the push if configured.
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

A leküldéses értesítések egy hitelesített ügyfél most regisztrálhatja, győződjön meg arról, hogy a hitelesítési befejeződött a regisztráció előtt.

## <a name="CustomAPI"></a> Egyéni API-k
### <a name="howto-customapi-basic"></a>Adja meg egy egyéni API
A Data Access API keresztül mellett a `/tables` a végponthoz, a Mobile Apps is biztosít egyéni API. Egyéni API-k a definíciói hasonló módon határozza meg, és hozzáférhet ugyanazokat létesítményekben, például a hitelesítés.

Ha azt szeretné, egy egyéni API App Service hitelesítés használatára, konfigurálnia kell a App Service hitelesítés a [Azure-portálon] első. További információkért lásd: az identitásszolgáltató használni kívánt konfigurációs Útmutató:

* [Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Egyéni API-k meghatározott sokkal ugyanúgy, mint a táblák API:

1. Hozzon létre egy `api` directory.
2. Az API definition JavaScript fájl létrehozása a `api` könyvtár.
3. Az importálás módszer használatával importálja a `api` könyvtár.

A típusnak a basic alkalmazáson belüli minta alapján, amely korábban használtuk az API-definíció itt található:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Vegyünk egy példát API, amely segítségével a kiszolgáló dátumot adja vissza a `Date.now()` metódust. A api/date.js fájl itt található:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Egyes paraméterek egyike a szabványos RESTful HTTP-parancsokat: GET, POST, javítása vagy törlése. A metódus egy szabványos [ExpressJS köztes] függvény, amelyet a kimeneti küld.

### <a name="howto-customapi-auth"></a>Hitelesítés egy egyéni API eléréséhez szükséges
A Mobile Apps SDK valósít meg hitelesítési ugyanúgy mind a `tables` végpont és egyéni API-kat. Hitelesítés hozzáadása az előző szakaszban létrehozott API-hoz, vegye fel az `access` tulajdonság:

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

A használt ugyanezt a tokent a `tables` végpont az egyéni API-k, hogy a hitelesítést kell használnia.

### <a name="howto-customapi-auth"></a>Nagy fájlfeltöltések leíró
A Mobile Apps SDK-t használ a [törzs-elemző köztes](https://github.com/expressjs/body-parser) elfogadásához és dekódolási törzs tartalma a jelentkezést. Megadhatja, hogy a szervezet-elemző nagyobb fájlfeltöltések fogadására:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling.
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);

A fájl nem base-64 kódolású továbbítás előtt. Ez a kódolás növeli a tényleges feltöltés (és figyelembe kell venni a megadott méret) méretét.

### <a name="howto-customapi-sql"></a>Egyéni SQL-utasítások végrehajtása
A Mobile Apps SDK lehetővé teszi a hozzáférést a teljes környezet a request objektumon keresztül. Könnyen végre lehet hajtani ezeket az adott szolgáltató paraméteres SQL-utasításokat:

    var api = {
        get: function (request, response, next) {
            // Check for parameters. If not there, pass on to a later API call.
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query. Anything that the mssql
            // driver can handle is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query. The context for Mobile Apps is available through
            // request.azureMobile. The data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Hibakeresés, könnyen táblák és egyszerű API-k
### <a name="howto-diagnostic-logs"></a>Hibakeresési, diagnosztizálása és elhárítása Mobile Apps
Az Azure App Service számos Hibakeresés és hibaelhárítási módszerekről a Node.js-alkalmazások biztosít.
Ismerkedés a Node.js Mobile Apps háttér elhárításához, olvassa el a következő cikkeket:

* [Az Azure App Service figyelése]
* [Az Azure App Service diagnosztikai naplózás engedélyezése]
* [A Visual Studio Azure App Service hibaelhárítása]

NODE.js-alkalmazások hozzáférhetnek a diagnosztikai naplófájl eszközök széles skáláját. Belső, a Mobile Apps Node.js SDK-t használ [Winston] diagnosztikai naplózás. Naplózás automatikusan engedélyezve, ha engedélyezi a hibakeresési mód vagy állítsa a `MS_DebugMode` Alkalmazásbeállítás igaz értékű a [Azure-portálon]. A diagnosztikai naplókat a létrehozott naplók jelennek meg a [Azure-portálon].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Az Azure-portálon a könnyen táblázatok használata
Egyszerű táblázatok használatával létrehozása és használata táblákat közvetlenül a portálon. A dataset CSV formátumban feltöltheti könnyen táblákat. Vegye figyelembe, hogy nem használhatja a tulajdonságnevek (a fürt megosztott kötetei szolgáltatás dataset), amely ütközik a Mobile Apps háttér tulajdonság nevét. A rendszer tulajdonság-nevek a következők:
* createdAt
* updatedAt
* törölve
* verzió:

App Service-szerkesztő használatával tábla műveletek akár szerkesztheti is. Ha bejelöli **könnyen táblák** a háttér-webhely beállításai hozzáadása, módosítása és törlése egy tábla. Megtekintheti a tábla adatai is.

![A könnyen táblázatok használata](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Az alábbi parancsokat a táblázat parancssávon érhetők el:

* **Engedélyek módosítása**: módosítási olvasási engedéllyel, beszúrni, frissíteni és törlési műveletek a táblában.
 Is, hogy engedélyezze a névtelen hozzáférést, a hitelesítés szükséges, vagy tiltsa le a műveletet az elérésére.
* **Parancsfájl szerkesztése**: A parancsfájl a következő táblázatban a alkalmazás-szerkesztőben van megnyitva.
* **Séma kezelése**: felvétele vagy oszlopok törlése vagy a tábla index módosítása.
* **Törölje a jelet tábla**: csonkolja a meglévő tábla összes adatsorok törlésével, de a séma változatlanul.
* **Sorok törlése**: egyedi sornyi adatot törli.
* **A folyamatos átviteli naplók megtekintése**: a hely a folyamatos átviteli napló szolgáltatásához.

### <a name="work-easy-apis"></a>Egyszerű API-k használata az Azure-portálon
Egyszerű API-k segítségével létrehozása és használata egyéni API-k közvetlenül a portálon. API-parancsfájlok App Service-szerkesztő használatával módosíthatja.

Ha bejelöli **egyszerű API-k** a háttér-webhely beállításai hozzáadása, módosítása vagy egy egyéni API-végpont törlése.

![Egyszerű API-khoz](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

A portálon módosítsa a HTTP-művelethez engedélyeket, az API App Service szerkesztő parancsfájl szerkesztése vagy a folyamatos átviteli naplók megtekintése.

### <a name="online-editor"></a>App Service szerkesztő kód szerkesztése
Az Azure portál használatával módosíthatja a Node.js háttér-parancsfájlok App Service szerkesztő anélkül, hogy a projekt letöltése a helyi számítógépen. Az online szerkesztő parancsfájlok szerkesztése:

1. Ablaktáblán a Mobile Apps háttér, válasszon **összes beállítás** > vagy **könnyen táblák** vagy **egyszerű API-k**. Egy tábla vagy API-t, majd válassza ki és **parancsfájl szerkesztése**. A parancsfájl az alkalmazás-szerkesztő megnyitása.

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
[Apache Cordova ügyfél gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[iOS ügyfél gyors üzembe helyezés]: app-service-mobile-ios-get-started.md
[Xamarin.iOS ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-ügyfél gyors üzembe helyezés]: app-service-mobile-xamarin-forms-get-started.md
[A Windows Store ügyfél gyors üzembe helyezés]: app-service-mobile-windows-store-dotnet-get-started.md
[offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[Azure Active Directory-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Google-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Microsoft-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter-hitelesítés konfigurálása]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service telepítési útmutató]: ../app-service/app-service-deploy-local-git.md
[Az Azure App Service figyelése]: ../app-service/web-sites-monitor.md
[Az Azure App Service diagnosztikai naplózás engedélyezése]: ../app-service/web-sites-enable-diagnostic-log.md
[A Visual Studio Azure App Service hibaelhárítása]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[a csomópont verzió megadása]: ../nodejs-specify-node-version-azure-apps.md
[csomópont modulok használata]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure-portálon]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
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
