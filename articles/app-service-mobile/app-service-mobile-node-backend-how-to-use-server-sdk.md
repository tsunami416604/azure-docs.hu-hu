---
title: A Node.js háttérkiszolgálós SDK-jának kezelése
description: Ismerje meg, hogyan dolgozhat a Node.js háttérkiszolgálós SDK-val az Azure App Service mobilalkalmazásokhoz.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249944"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>A Mobilalkalmazások Node.js SDK használata

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a cikk részletes információkat és példákat tartalmaz, amelyek bemutatják, hogyan dolgozhat a Node.js háttértartalékkal az Azure App Service mobilalkalmazások szolgáltatásában.

## <a name="introduction"></a><a name="Introduction"></a>Bevezetés

A Mobile Apps lehetővé teszi egy mobilra optimalizált adatelérési webes API hozzáadását egy webalkalmazáshoz. A Mobile Apps SDK ASP.NET és Node.js webalkalmazásokhoz érhető el. Az SDK a következő műveleteket biztosítja:

* Táblázatműveletek (olvasás, beszúrás, frissítés, törlés) adathozzáféréshez
* Egyéni API-műveletek

Mindkét művelet biztosítja a hitelesítést az összes identitásszolgáltató, amely az Azure App Service lehetővé teszi. Ezek a szolgáltatók közé tartoznak a közösségi identitásszolgáltatók, például a Facebook, a Twitter, a Google és a Microsoft, valamint az Azure Active Directory a vállalati identitáshoz.

Az egyes használati eseteket a [GitHub mintakönyvtárában]talál.

## <a name="supported-platforms"></a>Támogatott platformok

A Mobile Apps Node.js SDK támogatja a csomópont és újabb verziók jelenlegi LTS-kiadását. Jelenleg a legújabb LTS verzió node v4.5.0. A Csomópont más verziói működhetnek, de nem támogatottak.

A Mobile Apps Node.js SDK két adatbázis-illesztőprogramot támogat: 

* A csomópont-mssql illesztőprogram támogatja az Azure SQL Database és a helyi SQL Server-példányok.  
* Az sqlite3 illesztőprogram csak egyetlen példányban támogatja az SQLite adatbázisokat.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Egyszerű Node.js háttérablak létrehozása a parancssorból

Minden Mobile Apps Node.js háttérprogram ExpressJS alkalmazásként indul. ExpressJS a legnépszerűbb webszolgáltatás keret rendszer elérhető Node.js. Az alapvető [Express] alkalmazást az alábbiak szerint hozhatja létre:

1. Egy parancs- vagy PowerShell-ablakban hozzon létre egy könyvtárat a projekthez:

        mkdir basicapp

1. Futtassa `npm init` a csomagszerkezet inicializálásához:

        cd basicapp
        npm init

   A `npm init` parancs kérdéseket tesz fel a projekt inicializálásához. Tekintse meg a példa kimenetét:

   ![Az npm init kimenet][0]

1. Telepítse `express` a `azure-mobile-apps` könyvtárakat az npm tárházból:

        npm install --save express azure-mobile-apps

1. Hozzon létre egy app.js fájlt az alapvető mobilkiszolgáló megvalósításához:

    ```javascript
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
    ```

Ez az alkalmazás létrehoz egy mobil-optimalizált webes`/tables/TodoItem`API egyetlen végpont ( ), amely nem hitelesített hozzáférést biztosít az alapul szolgáló SQL-adattár segítségével dinamikus séma. Az ügyfélkönyvtár rövid útmutatóinak követésére alkalmas:

* [Android-ügyfél rövid útmutató]
* [Apache Cordova kliens – rövid útmutató]
* [az iOS-ügyfél rövid útmutatója]
* [Windows Áruházbeli ügyfél – rövid útmutató]
* [Xamarin.iOS kliens – rövid útmutató]
* [Xamarin.Android kliens – rövid útmutató]
* [Xamarin.Forms ügyfél rövid útmutató]

Ennek az alapszintű alkalmazásnak a kódját a [GitHub basicapp mintájában]találja.

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Node.js háttértartalék létrehozása a Visual Studio 2015 használatával

A Visual Studio 2015 bővítményt igényel a Node.js alkalmazások fejlesztéséhez az IDE-n belül. A kezdéshez telepítse a [Node.js Tools 1.1 for Visual Studio]alkalmazást. Amikor befejezte a telepítést, hozzon létre egy Express 4.x alkalmazást:

1. Nyissa meg az **Új projekt** párbeszédpanelt (az**Új** > **projekt** **fájlból).** > 
1. **Bontsa ki a Sablonok** > JavaScript-csomópont.js**JavaScript** > **.**
1. Válassza **az Egyszerű Azure Node.js Express 4 alkalmazás lehetőséget.**
1. Töltse ki a projekt nevét. Válassza **az OK gombot.**

   ![Visual Studio 2015 új projekt][1]
1. Kattintson a jobb gombbal az **npm** csomópontra, és válassza **az Új npm csomagok telepítése parancsot.**
1. Előfordulhat, hogy az első Node.js alkalmazás létrehozása után frissítenie kell az npm katalógust. Szükség esetén válassza a **Frissítés** lehetőséget.
1. Írja be **az azure-mobile-apps** a keresőmezőbe. Válassza ki az **azure-mobile-apps 2.0.0** csomagot, majd válassza **a Csomag telepítése lehetőséget.**

   ![Új npm csomagok telepítése][2]
1. Kattintson a **Bezárás** gombra.
1. Nyissa meg az app.js fájlt a Mobilalkalmazások SDK támogatásának hozzáadásához. A könyvtári `require` utasítások alján található 6.

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    A többi `app.use` utasítás után körülbelül a 27.

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Mentse a fájlt.

1. Futtassa az alkalmazást helyileg (az API-t a kiszolgáló szolgálja), `http://localhost:3000`vagy tegye közzé az Azure-ban.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Node.js háttértartalék létrehozása az Azure Portal használatával

Mobilalkalmazások háttértartalékát közvetlenül az [Azure Portalon]hozhatja létre. A [mobilalkalmazás](app-service-mobile-ios-get-started.md) létrehozása oktatóanyag követésével végrehajthatja a következő lépéseket, vagy létrehozhat egy ügyfelet és egy kiszolgálót. Az oktatóanyag ezen utasítások egyszerűsített verzióját tartalmazza, és a legjobb a koncepcióigazolási projektekhez.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Az **Első lépések** ablaktáblában, a Tábla **API létrehozása**csoportban válassza **a Node.js** elemet háttérnyelvként.
Jelölje be az **I acknowledge that that that it is overwrite all site contents (Tudomásul veszem, hogy ez felülírja a webhely összes tartalmát**, majd válassza a **TodoItem tábla létrehozása lehetőséget**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>A Node.js háttér-rövidútmutatós kódprojektjének letöltése a Git használatával

Amikor létrehoz egy Node.js Mobile Apps háttérprogramot a portál **gyorsindítási** ablaktáblájával, létrejön egy Node.js projekt, amely et telepít a webhelyre. A portálon hozzáadhat táblákat és API-kat, és szerkesztheti a node.js háttérrendszer kódfájljait. Különböző központi telepítési eszközökkel is letöltheti a háttérprojektet, így táblákat és API-kat adhat hozzá vagy módosíthat, majd újra közzéteheti a projektet. További információt az [Azure App Service telepítési útmutatójában talál.]

Az alábbi eljárás egy Git-tárház segítségével tölti le a rövid útmutató projektkódját:

1. Telepítse a Git-et, ha még nem tette meg. A Git telepítéséhez szükséges lépések operációs rendszerekenként eltérőek lehetnek. Az operációs rendszerspecifikus disztribúciókról és telepítési útmutatóról a [Git telepítése című](https://git-scm.com/book/en/Getting-Started-Installing-Git)témakörben talál.
2. Lásd: [A tárház előkészítése](../app-service/deploy-local-git.md#prepare-your-repository) a Git-tárház a háttérwebhely engedélyezéséhez. Jegyezze fel a központi telepítési felhasználónevet és a jelszót.
3. A mobilalkalmazások háttérablakában jegyezze fel a **Git klónozás URL-címét.**
4. A `git clone` parancs végrehajtása a Git klón URL-címével. Szükség esetén adja meg jelszavát, ahogy az alábbi példában is található:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Tallózással keresse`/todolist` meg a helyi könyvtárat (az előző példában), és figyelje meg, hogy a projektfájlok le lettek töltve. Keresse meg a todoitem.json fájlt a `/tables` könyvtárban. Ez a fájl határozza meg a táblában lévő engedélyeket. Keresse meg a todoitem.js fájlt is ugyanabban a könyvtárban. Ez határozza meg a CRUD művelet parancsfájlok a tábla.
6. Miután módosította a projektfájlokat, futtassa a következő parancsokat a hozzáadáshoz, véglegesítéshez, majd töltse fel a módosításokat a webhelyre:

        $ git commit -m "updated the table script"
        $ git push origin master

   Amikor új fájlokat ad a projekthez, `git add .` először futtatnia kell a parancsot.

A webhely minden alkalommal újra közzétételre kerül, amikor új véglegesítéseket ad le a webhelyre.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>A Node.js háttértartalékközzététele az Azure-ban

A Microsoft Azure számos mechanizmust biztosít a mobilalkalmazások node.js háttérrendszerének az Azure-szolgáltatás közzétételéhez. Ezek a mechanizmusok magukban foglalják a Visual Studio-ba integrált telepítési eszközöket, a parancssori eszközöket és a forrásellenőrzésen alapuló folyamatos üzembe helyezési lehetőségeket. További információt az [Azure App Service telepítési útmutatójában talál.]

Az Azure App Service konkrét tanácsokat tartalmaz a Node.js alkalmazásokhoz, amelyeket a háttérprogram közzététele előtt át kell tekintenie:

* A [csomópontverzió megadása]
* A [csomópontmodulok használata]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Kezdőlap engedélyezése az alkalmazáshoz

Számos alkalmazás webes és mobilalkalmazások kombinációja. Az ExpressJS keretrendszer segítségével kombinálhatja a két aspektusa. Néha azonban előfordulhat, hogy csak egy mobil felületet szeretne megvalósítani. Célszerű kezdőlapot biztosítani, amely biztosítja, hogy az alkalmazásszolgáltatás működőképes legyen. Megadhat saját kezdőlapot, vagy engedélyezheti az ideiglenes kezdőlapot. Ideiglenes kezdőlap engedélyezéséhez használja a következő kódot a mobilalkalmazások példányosítására:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Ha csak azt szeretné, hogy ez a lehetőség elérhető legyen a helyi fejlesztés során, hozzáadhatja ezt a beállítást az azureMobile.js fájlhoz.

## <a name="table-operations"></a><a name="TableOperations"></a>Táblaműveletek

Az azure-mobile-apps Node.js Server SDK mechanizmusokat biztosít az Azure SQL Database-ben webes API-ként tárolt adattáblák felfedésére. Öt műveletet biztosít:

| Művelet | Leírás |
| --- | --- |
| GET /tables/*táblanév* |Az összes rekord bekerülése a táblába. |
| GET /tables/*táblanév*/:id |Adott rekord bekerülése a táblába. |
| POST /tables/*táblanév* |Hozzon létre egy rekordot a táblában. |
| PATCH /tables/*táblanév*/:id |Rekord frissítése a táblában. |
| DELETE /tables/*táblanév*/:azonosító |Rekord törlése a táblából. |

Ez a webes API támogatja az [OData-t,] és kiterjeszti a táblasémát az [offline adatszinkronizálás]támogatására.

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Táblázatok definiálása dinamikus sémával

Mielőtt használhatna egy táblát, meg kell határoznia azt. A táblákat statikus séma (ahol a séma oszlopait definiálja) vagy dinamikusan definiálhat (ahol az SDK a sémát a bejövő kérelmek alapján vezérli). Ezenkívül a webes API bizonyos aspektusait javaScript-kód hozzáadásával szabályozhatja a definícióhoz.

Ajánlott eljárásként definiálni kell a `tables` címtárban lévő JavaScript-fájl `tables.import()` ban lévő táblákat, majd a módszerrel importálhatja a táblákat. Az alapalkalmazás-minta kiterjesztésével módosíthatja az app.js fájlt:

```javascript
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
```

Adja meg a táblát a ./tables/TodoItem.js mezőben:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

A táblák alapértelmezés szerint dinamikus sémát használnak. A dinamikus séma globális kikapcsolására `MS_DynamicSchema` állítsa az alkalmazásbeállítást hamisra az Azure Portalon.

A [GitHubon található todo mintában]talál egy teljes példát.

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Táblák definiálása statikus sémával

Explicit módon meghatározhatja a webes API-n keresztül elérhetővé tenni kívánt oszlopokat. Az azure-mobile-apps Node.js SDK automatikusan hozzáadja az offline adatszinkronizáláshoz szükséges további oszlopokat a megadott listához. A rövid útmutató ügyfélalkalmazások hoz egy táblát `text` két oszlop: `complete` (a karakterlánc) és (a logikai).  
A tábla a `tables` (könyvtárban található) JavaScript-definíciós táblázatdefinícióban a következőképpen határozható meg:

```javascript
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
```

Ha statikusan definiálja a táblákat, meg kell hívnia a `tables.initialize()` metódust az adatbázisséma indításkori létrehozásához. A `tables.initialize()` metódus [egy ígéretet] ad vissza, így a webszolgáltatás nem szolgál kéréseket az adatbázis inicializálása előtt.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Az SQL Server Express használata fejlesztési adattárként a helyi számítógépen

A Mobile Apps Node.js SDK három lehetőséget kínál az adatok kiszolgálására:

* A **memória-illesztőprogram** segítségével nem állandó példatárolót biztosít.
* Az **mssql** illesztőprogram segítségével sql Server Express adattárat biztosítanak a fejlesztéshez.
* Az **mssql-illesztőprogram** segítségével azure SQL Database-adattáratárolót biztosítaz éles környezetben.

A Mobile Apps Node.js SDK az [mssql Node.js csomagot] használja az SQL Server Express és az SQL Database közötti kapcsolat létrehozásához és használatához. Ehhez a csomaghoz engedélyeznie kell a TCP-kapcsolatokat az SQL Server Express példányon.

> [!TIP]
> A memória-illesztőprogram nem biztosít teljes körű tesztelési lehetőségeket. Ha helyileg szeretné tesztelni a háttérrendszert, javasoljuk az SQL Server Express adattár és az mssql illesztőprogram használatát.

1. Töltse le és telepítse a [Microsoft SQL Server 2014 Express]alkalmazást. Győződjön meg arról, hogy telepítette az SQL Server 2014 Express with Tools kiadást. Ha nem igényel kifejezetten 64 bites támogatást, a 32 bites verzió kevesebb memóriát fogyaszt futás közben.
1. Az SQL Server 2014 Configuration Manager futtatása:

   a. Bontsa ki az **SQL Server hálózati konfigurációs** csomópontját a fa menüben.

   b. Válassza **az SQLEXPRESS protokolljai**lehetőséget.

   c. Kattintson a jobb gombbal a **TCP/IP elemre,** és válassza **az Engedélyezés parancsot.** Az előugró ablakban válassza az **OK** gombot.

   d. Kattintson a jobb gombbal a **TCP/IP elemre,** és válassza a **Tulajdonságok parancsot.**

   e. Válassza az **IP-címek** lapot.

   f. Keresse meg az **IPAll** csomópontot. A **TCP-port** mezőbe írja be az **1433**értéket.

      ![Az SQL Server Express konfigurálása TCP/IP protokollhoz][3]

   g. Válassza **az OK gombot.** Az előugró ablakban válassza az **OK** gombot.

   h. Válassza az **SQL Server Services** elemet a fa menüben.

   i. Kattintson a jobb gombbal **az SQL Server (SQLEXPRESS) elemre,** és válassza **az Újraindítás parancsot.**

   j. Zárja be az SQL Server 2014 Configuration Manager alkalmazást.

1. Futtassa az SQL Server 2014 Management Studio alkalmazást, és csatlakozzon a helyi SQL Server Express-példányhoz:

   1. Kattintson a jobb gombbal a példányra az Objektumkezelőben, és válassza **a Tulajdonságok parancsot.**
   1. Válassza a **Biztonság** lapot.
   1. Győződjön meg arról, hogy az **SQL Server és a Windows hitelesítési mód** ki van jelölve.
   1. Válassza **az OK gombot.**

      ![SQL Server Express-hitelesítés konfigurálása][4]
   1. Bontsa ki a **Biztonsági** > **bejelentkezések csomópontot** az Objektumkezelőben.
   1. Kattintson a jobb gombbal **a Bejelentkezések** elemre, és válassza **az Új bejelentkezés parancsot.**
   1. Adja meg a bejelentkezési nevet. Kattintson az **SQL Server-hitelesítés** lehetőségre. Írjon be egy jelszót, majd írja be ugyanazt a jelszót a **Jelszó megerősítése mezőbe.** A jelszónak meg kell felelnie a Windows összetettségére vonatkozó követelményeknek.
   1. Válassza **az OK gombot.**

      ![Új felhasználó hozzáadása az SQL Server Express programhoz][5]
   1. Kattintson a jobb gombbal az új bejelentkezésre, és válassza **a Tulajdonságok parancsot.**
   1. Válassza a **Kiszolgálói szerepkörök** lapot.
   1. Jelölje be a **dbcreator** kiszolgálói szerepkör jelölőnégyzetét.
   1. Válassza **az OK gombot.**
   1. Zárja be az SQL Server 2015 Management Studio bezárását.

Ügyeljen arra, hogy rögzítse a kiválasztott felhasználónevet és jelszót. Előfordulhat, hogy az adatbázis követelményeitől függően további kiszolgálói szerepköröket vagy engedélyeket kell hozzárendelnie.

A Node.js alkalmazás `SQLCONNSTR_MS_TableConnectionString` beolvassa az adatbázis kapcsolati karakterláncának környezeti változóját. Ezt a változót a környezetében is beállíthatja. A PowerShell segítségével például beállíthatja ezt a környezeti változót:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Az adatbázis elérése TCP/IP-kapcsolaton keresztül. Adja meg a kapcsolat felhasználónevét és jelszavát.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>A projekt konfigurálása helyi fejlesztéshez

A Mobile Apps beolvassa az *azureMobile.js* nevű JavaScript-fájlt a helyi fájlrendszerből. Ne használja ezt a fájlt a Mobile Apps SDK éles környezetben történő konfigurálásához. Ehelyett használja **az alkalmazásbeállításokat** az [Azure Portalon.]

Az azureMobile.js fájlnak egy konfigurációs objektumot kell exportálnia. A leggyakoribb beállítások a következők:

* Adatbázis-beállítások
* Diagnosztikai naplózási beállítások
* Alternatív CORS-beállítások

Ez a példa **az azureMobile.js** fájl megvalósítja az előző adatbázis-beállításokat:

```javascript
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
```

Azt javasoljuk, hogy adja hozzá **az azureMobile.js-t** a **.gitignore** fájlhoz (vagy más forráskód-vezérlő figyelmen kívül hagyva a fájlt), hogy megakadályozza a jelszavak felhőben való tárolását. Az [Azure Portal] **alkalmazásbeállításaiban** mindig konfigurálja a termelési beállításokat.

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Alkalmazásbeállítások konfigurálása a mobilalkalmazáshoz

Az azureMobile.js fájl legtöbb beállítása egyenértékű alkalmazásbeállítással rendelkezik az [Azure Portalon.] Az alábbi lista segítségével konfigurálhatja az alkalmazást az **alkalmazásbeállításokban:**

| Alkalmazásbeállítás | azureMobile.js beállítás | Leírás | Érvényes értékek |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |név |Az alkalmazás neve |sztring |
| **MS_MobileLoggingLevel** |naplózás.szint |A naplóba küldő üzenetek minimális naplózási szintje |hiba, figyelmeztetés, info, bőbeszédű, debug, buta |
| **MS_DebugMode** |Debug |Hibakeresési mód engedélyezése vagy letiltása |igaz, hamis |
| **MS_TableSchema** |data.schema |AZ SQL-táblák alapértelmezett sémaneve |karakterlánc (alapértelmezett: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Hibakeresési mód engedélyezése vagy letiltása |igaz, hamis |
| **MS_DisableVersionHeader** |verzió (meghatározatlan) |Letiltja az X-ZUMO-Server-Version fejlécet |igaz, hamis |
| **MS_SkipVersionCheck** |skipversioncheck (skipversioncheck) |Letiltja az ügyfél API-verzióellenőrzését |igaz, hamis |

Alkalmazásbeállítás beállítása:

1. Jelentkezzen be az [Azure Portalra.]
1. Válassza **az Összes erőforrás** vagy az App **Services**lehetőséget, majd válassza ki a mobilalkalmazás nevét.
1. Alapértelmezés szerint megnyílik a **Beállítások** ablaktábla. Ha nem, válassza a **Beállítások lehetőséget.**
1. Az **ÁLTALÁNOS menüben** válassza az **Alkalmazásbeállítások lehetőséget**.
1. Görgessen az **Alkalmazásbeállítások** szakaszhoz.
1. Ha az alkalmazásbeállítás már létezik, válassza ki az alkalmazásbeállítás értékét az érték szerkesztéséhez.
   Ha az alkalmazásbeállítása nem létezik, írja be az alkalmazásbeállítást a **Kulcs** mezőbe, és az értéket az **Érték** mezőbe.
1. Kattintson a **Mentés** gombra.

A legtöbb alkalmazásbeállítás módosításához újra kell indítani a szolgáltatást.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Az SQL Database használata éles adattárként

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Az Azure SQL Database adattárként való használata megegyezik az összes Azure App Service-alkalmazástípussal. Ha még nem tette meg, kövesse az alábbi lépéseket a mobilalkalmazások háttér-tartalékházának létrehozásához:

1. Jelentkezzen be az [Azure Portalra.]
1. Az ablak bal felső részén válassza a **+ÚJ** gombot > **Web + Mobile** > **Mobile App alkalmazással,** majd adja meg a mobilalkalmazások háttérablakának nevét.
1. Az **Erőforráscsoport** mezőbe írja be ugyanazt a nevet, mint az alkalmazás.
1. Az alapértelmezett App Service-csomag van kiválasztva. Ha módosítani szeretné az App Service-csomagot:

   a. Válassza **az App Service Terv** > **+Új létrehozása lehetőséget.**

   b. Adja meg az új App Service-csomag nevét, és válassza ki a megfelelő helyet.

   c. Válassza ki a szolgáltatás megfelelő tarifacsomagát. Válassza **az Összes megtekintése** lehetőséget a további árképzési lehetőségek megtekintéséhez, például az **Ingyenes** és a **Megosztott**lehetőséget.

   d. Kattintson a **Kijelölés** gombra.

   e. Az **App Service-csomag** ablaktábláján válassza az **OK gombot.**
1. Kattintson a **Létrehozás** gombra.

A mobilalkalmazások háttér-tartalékrészének kiépítése néhány percet is igénybe vehet. A mobilalkalmazások háttértartalékának kiépítése után a portál megnyitja a Mobilalkalmazások **háttérablakának Beállítások** ablaktábláját.

Választhat, hogy egy meglévő SQL-adatbázist csatlakoztat a mobilalkalmazások háttérrendszeréhez, vagy új SQL-adatbázist hoz létre. Ebben a szakaszban egy SQL-adatbázist hozunk létre.

> [!NOTE]
> Ha már van egy adatbázis a Mobilalkalmazások háttérrendszerével azonos helyen, akkor válassza a **Meglévő adatbázis használata lehetőséget,** majd jelölje ki az adatbázist. Nem javasoljuk, hogy egy adatbázis használata egy másik helyen, mert a magasabb késések.

1. Az új mobilalkalmazások háttérben válassza a **Beállítások** > **mobilalkalmazás-adatok** > **Data** > **+Hozzáadás lehetőséget.**
1. Az **Adatkapcsolat hozzáadása** ablaktáblában válassza az SQL Database – Szükséges beállítások >  **konfigurálása****Új adatbázis létrehozása lehetőséget.** Írja be az új adatbázis nevét a **Név** mezőbe.
1. Válassza a **Kiszolgáló**lehetőséget. Az **Új kiszolgáló** ablaktáblán írjon be egy egyedi kiszolgálónevet a **Kiszolgáló neve** mezőbe, és adja meg a megfelelő kiszolgálórendszergazdai bejelentkezési nevet és jelszót. Győződjön meg arról, hogy **az Azure-szolgáltatások hozzáférés-kiszolgálóhoz engedélyezése** jelölőnégyzet be van jelölve. Válassza **az OK gombot.**

   ![Azure SQL-adatbázis létrehozása][6]
1. Az **Új adatbázis** ablaktáblán válassza az **OK gombot.**
1. Az **Adatkapcsolat hozzáadása** ablaktáblában válassza a **Kapcsolati karakterlánc**lehetőséget, és adja meg az adatbázis létrehozásakor megadott bejelentkezési és jelszóértéket. Ha meglévő adatbázist használ, adja meg az adatbázis bejelentkezési hitelesítő adatait. Válassza **az OK gombot.**
1. Az adatbázis **létrehozásához** kattintson ismét az **Adatkapcsolat hozzáadása** ablaktáblába.

<!--- END OF ALTERNATE INCLUDE -->

Az adatbázis létrehozása eltarthat néhány percig. Az **Értesítések** területen figyelheti a központi telepítés előrehaladását. Ne haladjön, amíg az adatbázis telepítése sikeres en nem történik. Az adatbázis telepítése után létrejön egy kapcsolati karakterlánc az SQL Database-példányhoz a mobilalkalmazások háttéralkalmazás-beállításaiban. Ezt az alkalmazásbeállítást az**Alkalmazásbeállítások** >  **beállítása** > **csatlakozási karakterláncokban láthatja.**

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Hitelesítés megkövetelése a táblákhoz való hozzáféréshez

Ha az App Service-hitelesítést `tables` a végponthoz szeretné használni, először konfigurálnia kell az App Service-hitelesítést az [Azure Portalon.] További információt a használni kívánt identitásszolgáltató konfigurációs útmutatójában talál:

* [Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Minden tábla rendelkezik egy hozzáférési tulajdonsággal, amely a tábla elérésének szabályozására használható. A következő minta egy statikusan definiált táblát mutat be hitelesítéssel.

```javascript
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
```

A hozzáférési tulajdonság három értéket vehet fel:

* *a névtelen* azt jelzi, hogy az ügyfélalkalmazás hitelesítés nélkül olvashatadatokat.
* *hitelesített* azt jelzi, hogy az ügyfélalkalmazásnak érvényes hitelesítési tokent kell küldenie a kéréssel.
* *letiltott* azt jelzi, hogy ez a tábla jelenleg le van tiltva.

Ha a hozzáférési tulajdonság nincs definiálva, a nem hitelesített hozzáférés engedélyezett.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Hitelesítési jogcímek használata a táblákkal
A hitelesítés beállításakor különböző jogcímeket állíthat be. Ezek a jogcímek általában `context.user` nem érhetők el az objektumon keresztül. Ezeket azonban a `context.user.getIdentity()` metódus segítségével is lekérheti. A `getIdentity()` metódus egy objektumra feloldó ígéretet ad vissza. Az objektumot a hitelesítési`facebook`módszer `google` `twitter`( `microsoftaccount`, `aad`, , , vagy ) határozza meg.

Ha például beállítja a Microsoft-fiók hitelesítését, és kéri az e-mail-címek reklamációját, az e-mail címet hozzáadhatja a rekordhoz a következő táblázatvezérlővel:

```javascript
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
```

Az elérhető jogcímek megtekintéséhez használjon webböngészőt a `/.auth/me` webhely végpontjának megtekintéséhez.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Adott táblaműveletekhez való hozzáférés letiltása

A táblázatban való megjelenés mellett a hozzáférési tulajdonság az egyes műveletek vezérlésére is használható. Négy művelet létezik:

* `read`a RESTful GET művelet az asztalon.
* `insert`a RESTful POST művelet az asztalon.
* `update`a RESTful PATCH művelet az asztalon.
* `delete`a táblázatON lévő RESTful DELETE művelet.

Előfordulhat például, hogy egy írásvédett táblát szeretne megadni:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>A táblaműveletekhez használt lekérdezés módosítása

A táblaműveletek általános követelménye az adatok korlátozott nézetének biztosítása. Megadhat például egy olyan táblát, amely a hitelesített felhasználói azonosítóval van címkézve, így csak a saját rekordjait olvashatja vagy frissítheti. Az alábbi táblázatdefiníció ezt a funkciót tartalmazza:

```javascript
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
```

A lekérdezést általában futtató műveletek rendelkeznek egy lekérdezési tulajdonsággal, amelyet `where` záradékkal módosíthat. A lekérdezéstulajdonság egy [QueryJS-objektum,] amely az OData-lekérdezés olyan rendszerré konvertálására szolgál, amelyet az adatháttér-rendszer fel tud dolgozni. Egyszerű egyenlőségi esetekhez (például az előzőhez) használhat térképet. Adott SQL-záradékokat is hozzáadhat:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Ideiglenes törlés konfigurálása táblázatban

A helyreállítható törlés valójában nem törli a rekordokat. Ehelyett úgy jelöli meg őket, mint törölték őket az adatbázisban, ha a törölt oszlopot igaz értékre állítja. A Mobile Apps SDK automatikusan eltávolítja a helyreállíthatóan törölt `IncludeDeleted()`rekordokat az eredményekközül, kivéve, ha a Mobilügyfél SDK használja. Ha egy táblázatot szeretne beállítani `softDelete` a helyreállítható törléshez, állítsa be a tulajdonságot a tábladefiníciós fájlban:

```javascript
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
```

Létre kell hoznia egy mechanizmust a rekordok törléséhez: egy ügyfélalkalmazás, egy WebJob, egy Azure-függvény vagy egy egyéni API.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Az adatbázis magvető adatai

Amikor új alkalmazást hoz létre, érdemes lehet egy táblázatot adatokkal magozni. Ezt a tábladefiníciós JavaScript-fájlban a következőképpen teheti meg:

```javascript
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
```

Az adatok kezelése csak akkor történik meg, ha a Mobile Apps SDK-t használta a tábla létrehozásához. Ha a tábla már létezik az adatbázisban, a rendszer nem ad adatokat a táblába. Ha a dinamikus séma be van kapcsolva, a séma a beültetett adatokból következik.

Azt javasoljuk, hogy explicit `tables.initialize()` módon hívja meg a metódust a tábla létrehozásához, amikor a szolgáltatás futása elindul.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Swagger támogatás engedélyezése
A Mobilalkalmazások beépített [Swagger] támogatással rendelkeznek. A Swagger-támogatás engedélyezéséhez először telepítse a swagger-ui-t függőségként:

    npm install --save swagger-ui

Ezután engedélyezheti a Swagger-támogatást a Mobilalkalmazások konstruktorában:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Valószínűleg csak a Swagger-támogatást szeretné engedélyezni a fejlesztői kiadásokban. Ezt az `NODE_ENV` alkalmazásbeállítással teheti meg:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

A `swagger` végpont a*webhely*.azurewebsites.net/swagger http:// található. A Swagger felhasználói felület a `/swagger/ui` végponton keresztül érhető el. Ha úgy dönt, hogy hitelesítést igényel a teljes alkalmazás, Swagger hibát hoz létre. A legjobb eredmény érdekében válassza a nem hitelesített kérelmek engedélyezését az Azure App `table.access` Service hitelesítési/engedélyezési beállításaiban, majd a hitelesítésvezérlést a tulajdonság használatával.

A Swagger opciót is hozzáadhatja az azureMobile.js fájlhoz, ha csak a Swagger helyi fejlesztéséhez szeretne támogatást.

## <a name="push-notifications"></a><a name="push"/>Leküldéses értesítések

A mobilalkalmazások integrálhatók az Azure Értesítési központokkal, így célzott leküldéses értesítéseket küldhet több millió eszköznek az összes főbb platformon. Az Értesítési központok használatával leküldéses értesítéseket küldhet iOS, Android és Windows rendszerű eszközökre. Ha többet szeretne megtudni az Értesítési központok segítségével, olvassa el az [Értesítési központok áttekintését.](../notification-hubs/notification-hubs-push-notification-overview.md)

### <a name="send-push-notifications"></a><a name="send-push"></a>Leküldéses értesítések küldése

A következő kód bemutatja, hogyan használhatja az `push` objektumot szórásos leküldéses értesítés küldésére a regisztrált iOS-eszközökre:

```javascript
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
```

Ha sablonleküldéses regisztrációt hoz létre az ügyféltől, ehelyett egy sablonleküldéses üzenetet küldhet az összes támogatott platformon lévő eszközöknek. A következő kód bemutatja, hogyan küldhet sablonértesítést:

```javascript
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
```

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Leküldéses értesítések küldése hitelesített felhasználónak címkék használatával
Amikor egy hitelesített felhasználó leküldéses értesítéseket regisztrál, a rendszer automatikusan hozzáad egy felhasználói azonosító címkét a regisztrációhoz. Ezzel a címkével leküldéses értesítéseket küldhet egy adott felhasználó által regisztrált összes eszközre. A következő kód megkapja a kérelmet küldő felhasználó biztonsági azonosítóját, és egy sablonleküldéses értesítést küld az adott felhasználó minden eszközregisztrációjára:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Amikor egy hitelesített ügyfél leküldéses értesítéseire regisztrál, a regisztráció megkísérlése előtt győződjön meg arról, hogy a hitelesítés befejeződött.

## <a name="custom-apis"></a><a name="CustomAPI"></a>Egyéni API-k

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Egyéni API meghatározása

A végponton keresztüli Data `/tables` Access API mellett a mobilalkalmazások egyéni API-lefedettséget is biztosíthatnak. Az egyéni API-k a tábladefiníciókhoz hasonlóan vannak definiálva, és ugyanazokat a létesítményeket érhetik el, beleértve a hitelesítést is.

Ha az App Service-hitelesítést egyéni API-val szeretné használni, először konfigurálnia kell az App Service-hitelesítést az [Azure Portalon.] További információt a használni kívánt identitásszolgáltató konfigurációs útmutatójában talál:

* [Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Az egyéni API-k meghatározása nagyjából ugyanúgy történik, mint a Táblák API:Custom API are defined in much as the way as the Tables API:

1. Hozzon `api` létre egy könyvtárat.
1. Hozzon létre egy API-definíciós JavaScript-fájlt a `api` címtárban.
1. A könyvtár importálásához `api` használja az importálási módszert.

Itt van a prototípus API-definíció alapján az alap-app minta, amit korábban használt:

```javascript
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
```

Vegyünk egy példa API-t, amely a `Date.now()` metódus dátumát adja vissza a metódus használatával. Itt van az api/date.js fájl:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Minden paraméter az egyik szabványos RESTful ige: GET, POST, PATCH vagy DELETE. A metódus egy szabványos [ExpressJS köztes szoftver] függvény, amely elküldi a szükséges kimenetet.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Hitelesítés megkövetelése az egyéni API-hoz való hozzáféréshez

A Mobile Apps SDK valósítja meg a `tables` hitelesítést ugyanúgy mind a végpont, mind az egyéni API-k. Ha hitelesítést szeretne hozzáadni az előző szakaszban kifejlesztett API-hoz, adjon hozzá egy `access` tulajdonságot:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Adott műveletekhez is megadhat hitelesítést:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

A végponthoz használt jogkivonatot kell használni a `tables` hitelesítést igénylő egyéni API-khoz.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Nagyméretű fájlfeltöltések kezelése

A Mobile Apps SDK a [testelemző köztes szoftvert](https://github.com/expressjs/body-parser) használja a beküldött tartalom elfogadásához és dekódolásához. A testelemzőt előre beállíthatja a nagyobb fájlfeltöltések elfogadásához:

```javascript
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
```

A fájl 64-es alapkódja az átvitel előtt van kódolva. Ez a kódolás növeli a tényleges feltöltés méretét (és azt a méretet, amelyet figyelembe kell venni).

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Egyéni SQL-utasítások végrehajtása

A Mobile Apps SDK lehetővé teszi a hozzáférést a teljes környezethez a kérelemobjektumon keresztül. A paraméterezett SQL-utasítások egyszerűen végrehajthatók a megadott adatszolgáltatószámára:

```javascript
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
```

## <a name="debugging"></a><a name="Debugging"></a>Hibakeresés

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Mobilalkalmazások hibakeresése, diagnosztizálása és hibaelhárítása

Az Azure App Service számos hibakeresési és hibaelhárítási technikát biztosít a Node.js alkalmazásokhoz.
A Node.js Mobile Apps háttérhibáinak elhárításához olvassa el az alábbi cikkeket:

* [Az Azure App Service figyelése]
* [Diagnosztikai naplózás engedélyezése az Azure App Service-ben]
* [Az Azure App Service hibáinak elhárítása a Visual Studióban]

A Node.js alkalmazások a diagnosztikai naplóeszközök széles köréhez férhetnek hozzá. Belsőleg a Mobile Apps Node.js SDK [winstont] használ a diagnosztikai naplózáshoz. A naplózás automatikusan engedélyezve lesz, `MS_DebugMode` ha engedélyezi a hibakeresési módot, vagy az alkalmazásbeállítást true értékre állítja az [Azure Portalon.] A létrehozott naplók megjelennek az [Azure Portal]diagnosztikai naplóiban.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android-ügyfél rövid útmutató]: app-service-mobile-android-get-started.md
[Apache Cordova kliens – rövid útmutató]: app-service-mobile-cordova-get-started.md
[az iOS-ügyfél rövid útmutatója]: app-service-mobile-ios-get-started.md
[Xamarin.iOS ügyfél – rövid útmutató]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android ügyfél rövid útmutató]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms ügyfél rövid útmutató]: app-service-mobile-xamarin-forms-get-started.md
[A Windows Áruházbeli ügyfél rövid útmutatója]: app-service-mobile-windows-store-dotnet-get-started.md
[kapcsolat nélküli adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-aad.md
[Facebook-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-facebook.md
[Google-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-google.md
[Microsoft-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-twitter.md
[Az Azure App Service telepítési útmutatója]: ../app-service/deploy-local-git.md
[Az Azure App Service figyelése]: ../app-service/web-sites-monitor.md
[Diagnosztikai naplózás engedélyezése az Azure App Service-ben]: ../app-service/troubleshoot-diagnostic-logs.md
[Az Azure App Service hibáinak elhárítása a Visual Studióban]: ../app-service/troubleshoot-dotnet-visual-studio.md
[adja meg a csomópont verzióját]: ../nodejs-specify-node-version-azure-apps.md
[Node modulok használata]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure-portál]: https://portal.azure.com/
[OData]: https://www.odata.org
[Ígéret]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp minta a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[teendő minta a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[minták könyvtára a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Eszközök 1.1 a Visual Studio számára]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js csomag]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS köztes szoftver]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
