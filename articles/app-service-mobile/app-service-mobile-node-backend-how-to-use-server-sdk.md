---
title: A Node. js háttérrendszer-kiszolgáló SDK használata a Mobile Appshoz | Microsoft Docs
description: Megtudhatja, hogyan dolgozhat a Azure App Service Mobile Apps Node. js-alapú háttér-kiszolgálói SDK-val.
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
ms.openlocfilehash: 74a522f8761c2eeaf329c90ae35aef0f44c40254
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027206"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Az Mobile Apps Node. js SDK használata

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

Ez a cikk részletes információkat és példákat tartalmaz, amelyek bemutatják, hogyan használható a Node. js háttérrendszer a Azure App Service Mobile Apps szolgáltatásában.

## <a name="Introduction"></a>Bevezetés

A Mobile Apps lehetővé teszi a Mobile-optimalizált adatelérési webes API-k egy webalkalmazáshoz való hozzáadását. A Mobile Apps SDK a ASP.NET és a Node. js webalkalmazásokhoz van megadva. Az SDK a következő műveleteket biztosítja:

* Táblázatos műveletek (olvasási, beszúrási, frissítési, törlési) adatokhoz való hozzáféréshez
* Egyéni API-műveletek

Mindkét művelet biztosítja a hitelesítést a Azure App Service összes olyan identitás-szolgáltatónál, amely lehetővé teszi. Ezek a szolgáltatók olyan közösségi identitás-szolgáltatókat foglalnak magukban, mint a Facebook, a Twitter, a Google és a Microsoft, valamint Azure Active Directory a vállalati identitáshoz.

Az egyes használati esetekhez mintákat a [Példák könyvtára a GitHubon]talál.

## <a name="supported-platforms"></a>Támogatott platformok

A Mobile Apps Node. js SDK támogatja a Node és újabb verziók jelenlegi LTS-kiadását. Jelenleg a legújabb LTS-verzió a Node v 4.5.0. A csomópont más verziói is működhetnek, de nem támogatottak.

A Mobile Apps Node. js SDK két adatbázis-illesztőprogramot támogat: 

* A Node-MSSQL illesztőprogram támogatja a Azure SQL Database és a helyi SQL Server példányokat.  
* A sqlite3-illesztőprogram csak egyetlen példányon támogatja az SQLite-adatbázisokat.

### <a name="howto-cmdline-basicapp"></a>Alapszintű Node. js-háttér létrehozása a parancssor használatával

Minden Mobile Apps Node. js-háttér ExpressJS-alkalmazásként indul el. A ExpressJS a Node. js-hez elérhető legnépszerűbb webszolgáltatás-keretrendszer. Alapszintű [Express] alkalmazást a következőképpen hozhat létre:

1. A parancs vagy a PowerShell ablakban hozzon létre egy könyvtárat a projekthez:

        mkdir basicapp

1. Futtassa a `npm init` parancsot a csomag szerkezetének inicializálásához:

        cd basicapp
        npm init

   A `npm init` parancs kérdéseket kér fel a projekt inicializálásához. Lásd a következő példában szereplő kimenetet:

   ![A NPM init kimenete][0]

1. Telepítse a `express` és a `azure-mobile-apps` kódtárat a NPM adattárból:

        npm install --save express azure-mobile-apps

1. Hozzon létre egy app. js fájlt az alapszintű mobil kiszolgáló megvalósításához:

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

Ez az alkalmazás egy olyan mobil optimalizált webes API-t hoz létre egyetlen végponttal (`/tables/TodoItem`), amely nem hitelesített hozzáférést biztosít egy mögöttes SQL-adattárhoz egy dinamikus séma használatával. Ez az ügyféloldali kódtár gyors üzembe helyezéséhez használható:

* [Android-ügyfél rövid útmutatója]
* [Apache Cordova-ügyfél gyors üzembe helyezés]
* [iOS-ügyfél rövid útmutatója]
* [Windows áruházbeli ügyfél rövid útmutatója]
* [Xamarin. iOS-ügyfél rövid útmutatója]
* [Xamarin.Android Client quickstart]
* [Xamarin. Forms-ügyfél rövid útmutatója]

Az alapszintű alkalmazás kódját a [basicapp-minta a GitHubon]találja.

### <a name="howto-vs2015-basicapp"></a>Node. js-háttér létrehozása a Visual Studio 2015 használatával

A Visual Studio 2015 használatához bővítmény szükséges a Node. js-alkalmazások fejlesztéséhez az IDE-n belül. Az indításhoz telepítse a [Node. js-eszközök a Visual studióhoz 1,1]. A telepítés befejezése után hozzon létre egy Express 4. x alkalmazást:

1. Nyissa meg az **új projekt** párbeszédpanelt (a következő **fájlból**:  > **új** > **projekt**).
1. Bontsa ki a **sablonok** > **JavaScript** > **Node. js**elemet.
1. Válassza az **alapszintű Azure Node. js Express 4 alkalmazást**.
1. Adja meg a projekt nevét. Kattintson az **OK** gombra.

   ![Visual Studio 2015 új projekt][1]
1. Kattintson a jobb gombbal a **NPM** csomópontra, és válassza az **új NPM-csomagok telepítése**lehetőséget.
1. Előfordulhat, hogy az első Node. js-alkalmazás létrehozása után frissítenie kell az NPM-katalógust. Szükség esetén válassza a **frissítés** lehetőséget.
1. A keresőmezőbe írja be az **Azure-Mobile-apps** kifejezést. Válassza ki az **Azure-Mobile-apps 2.0.0** csomagot, majd válassza a **csomag telepítése**lehetőséget.

   ![Új NPM-csomagok telepítése][2]
1. Válassza a **Bezárás**lehetőséget.
1. A Mobile Apps SDK támogatásának hozzáadásához nyissa meg az app. js fájlt. Adja hozzá a következő kódot a könyvtár alján található 6. sorban `require` utasításhoz:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    A többi `app.use` utasítás után körülbelül 27. sorban adja hozzá a következő kódot:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Mentse a fájlt.

1. Futtassa az alkalmazást helyileg (az API-t `http://localhost:3000`), vagy tegye közzé az Azure-ban.

### <a name="create-node-backend-portal"></a>Node. js-háttér létrehozása a Azure Portal használatával

A [Azure Portal]közvetlenül is létrehozhat egy Mobile apps. Az alábbi lépéseket végrehajthatja, vagy létrehozhat egy ügyfelet és egy kiszolgálót a [Mobile App létrehozása](app-service-mobile-ios-get-started.md) oktatóanyag követésével. Az oktatóanyag az utasítások egyszerűsített verzióját tartalmazza, és a legjobb megoldás a megvalósíthatósági projektekhez.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vissza az **első lépések** ablaktáblán a **Table API létrehozása**területen válassza a **Node. js** lehetőséget a háttérrendszer nyelvének megfelelően.
Jelölje be az Elfogadom jelölőnégyzetet, **hogy ez felülírja az összes webhely tartalmát**, majd válassza a **TodoItem tábla létrehozása**lehetőséget.

### <a name="download-quickstart"></a>Töltse le a Node. js háttérbeli gyors üzembe helyezési kód projektjét a git használatával

Amikor létrehoz egy Node. js-Mobile Apps a háttérben a portál **gyors üzembe** helyezési paneljén, a rendszer létrehoz egy Node. js-projektet, és telepíti őket a webhelyre. A portálon táblákat és API-kat vehet fel, és szerkesztheti a Node. js-háttér kódjának fájljait. A háttér-projekt letöltéséhez különböző üzembe helyezési eszközöket is használhat, így táblákat és API-kat adhat hozzá vagy módosíthat, majd újból közzéteheti a projektet. További információ: [Azure app Service telepítési útmutató].

Az alábbi eljárás egy git-tárházat használ a gyors üzembe helyezési projekt kódjának letöltéséhez:

1. Ha még nem tette meg, telepítse a gitt. A git telepítéséhez szükséges lépések az operációs rendszerek között változnak. Operációs rendszerre vonatkozó disztribúciók és telepítési útmutatás: a [git telepítése](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. A háttérbeli hely git-tárházának engedélyezéséhez lásd: [a tárház előkészítése](../app-service/deploy-local-git.md#prepare-your-repository) . Jegyezze fel a telepítési felhasználónevet és a jelszót.
3. A Mobile Apps háttér ablaktábláján jegyezze fel a **git-klón URL-címének** beállítását.
4. A git Clone URL használatával hajtsa végre a `git clone` parancsot. Szükség esetén adja meg a jelszót, ahogy az az alábbi példában is látható:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Tallózással keresse meg a helyi könyvtárat (`/todolist` az előző példában), és figyelje meg, hogy a projektfájlok letöltése megtörtént. Keresse meg a todoitem. JSON fájlt a `/tables` könyvtárban. Ez a fájl határozza meg a tábla engedélyeit. Keresse meg a todoitem. js fájlt is ugyanabban a könyvtárban. Meghatározza a tábla szifilisz-üzemeltetési parancsfájljait.
6. A projektfájlok módosítása után futtassa a következő parancsokat a hely módosításainak hozzáadásához, végrehajtásához, majd feltöltéséhez:

        $ git commit -m "updated the table script"
        $ git push origin master

   Amikor új fájlokat ad hozzá a projekthez, először futtatnia kell a `git add .` parancsot.

A rendszer minden alkalommal újra közzéteszi a helyet, amikor új véglegesíti a-készletet.

### <a name="howto-publish-to-azure"></a>A Node. js-háttér közzététele az Azure-ban

A Microsoft Azure számos mechanizmust biztosít a Mobile Apps Node. js-re való visszamenőleges közzétételéhez az Azure-szolgáltatásban. Ezek a mechanizmusok magukban foglalják a Visual studióba, a parancssori eszközökbe és a folyamatos üzembe helyezési lehetőségekkel integrált üzembe helyezési eszközöket a verziókövetés alapján. További információ: [Azure app Service telepítési útmutató].

A Azure App Service a háttérrendszer közzététele előtt áttekinthető Node. js-alkalmazásokra vonatkozó tanácsokat tartalmaz:

* [A csomópont verziószámának meghatározása]
* A [Csomópont-modulok használata]

### <a name="howto-enable-homepage"></a>Kezdőlap engedélyezése az alkalmazáshoz

Számos alkalmazás a webes és a mobil alkalmazások kombinációja. A ExpressJS keretrendszert használhatja a két aspektus összevonásához. Időnként előfordulhat azonban, hogy csak mobil felületet szeretne megvalósítani. Érdemes megadnia a kezdőlapot az App Service működésének biztosításához. Megadhatja saját kezdőlapját, vagy engedélyezheti az ideiglenes kezdőlapot. Egy ideiglenes Kezdőlap engedélyezéséhez használja a következő kódot a Mobile Apps létrehozásához:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Ha ezt a lehetőséget csak helyi fejlesztés esetén szeretné használni, ezt a beállítást a azureMobile. js fájlba is hozzáadhatja.

## <a name="TableOperations"></a>Tábla műveletei

Az Azure-Mobile-apps Node. js Server SDK olyan mechanizmusokat biztosít, amelyek lehetővé teszik a Azure SQL Databaseban tárolt adattáblák webes API-ként való továbbítását. Öt műveletet biztosít:

| Művelet | Leírás |
| --- | --- |
| /Tables/-*Táblanév* beolvasása |A tábla összes rekordjának beolvasása. |
| /Tables/*Táblanév*/: azonosító beolvasása |Egy adott rekord beolvasása a táblában. |
| /Tables/*TÁBLANÉV* közzététele |Hozzon létre egy rekordot a táblában. |
| PATCH/Tables/*Táblanév*/: azonosító |Rekord frissítése a táblában. |
| /Tables/*Táblanév*/: azonosító törlése |Rekord törlése a táblában. |

Ez a webes API támogatja a [OData] , és kibővíti a tábla sémáját az [Offline adatszinkronizálás]támogatásához.

### <a name="howto-dynamicschema"></a>Táblázatok definiálása dinamikus séma használatával

A tábla használata előtt meg kell határoznia azt. A táblákat statikus sémával is definiálhatja (ahol meghatározhatja a sémában lévő oszlopokat), vagy dinamikusan (ahol az SDK a bejövő kérések alapján vezérli a sémát). Emellett a webes API adott szempontjait a JavaScript-kód a definícióhoz való hozzáadásával is szabályozhatja.

Ajánlott eljárásként minden táblázatot egy JavaScript-fájlban kell megadnia a `tables` könyvtárban, majd az `tables.import()` metódus használatával importálnia kell a táblákat. Az alap-alkalmazás minta kiterjesztése az app. js fájl módosítása:

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

Adja meg a táblát a./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

A táblák alapértelmezés szerint dinamikus sémát használnak. A dinamikus séma globális kikapcsolásához állítsa a `MS_DynamicSchema` alkalmazás beállítást hamis értékre a Azure Portal.

A következő példában talál egy teljes példát a [teendői minta a GitHubon]közül.

### <a name="howto-staticschema"></a>Táblák definiálása statikus sémák használatával

Explicit módon megadhatja a webes API-n keresztül elérhetővé tenni kívánt oszlopokat. Az Azure-Mobile-apps Node. js SDK automatikusan hozzáadja az offline adatszinkronizáláshoz szükséges további oszlopokat az Ön által megadott listához. A gyors üzembe helyezési ügyfélalkalmazások például két oszlopból álló táblázatot igényelnek: `text` (a karakterlánc) és a `complete` (logikai).  
A tábla definiálható a Table definition JavaScript-fájljában (a `tables` könyvtárban található) a következőképpen:

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

Ha statikusan adja meg a táblákat, a `tables.initialize()` metódust is meg kell hívnia az adatbázis-séma indításkor történő létrehozásához. A `tables.initialize()` metódus egy [Ígéret] ad vissza, hogy a webszolgáltatás ne kézbesítse a kérelmeket az adatbázis inicializálása előtt.

### <a name="howto-sqlexpress-setup"></a>SQL Server Express használata fejlesztési adattárként a helyi gépen

A Mobile Apps Node. js SDK három lehetőséget biztosít az adatszolgáltatások kiszolgálása érdekében:

* A **memória** -illesztőprogram használatával nem állandó példaként használható tárolót biztosíthat.
* Az **MSSQL** -illesztőprogrammal SQL Server Express adattárt biztosíthat a fejlesztéshez.
* Az **MSSQL** -illesztőprogrammal megadhat egy Azure SQL Database adattárat az éles környezetben.

Az Mobile Apps Node. js SDK az [MSSQL Node. js-csomag] használja a SQL Server Express és a SQL Database közötti kapcsolat létrehozásához és használatához. Ehhez a csomaghoz engedélyezni kell a TCP-kapcsolatokat a SQL Server Express-példányon.

> [!TIP]
> A memória-illesztőprogram nem biztosít teljes körű szolgáltatást a teszteléshez. Ha helyileg szeretné tesztelni a háttérrendszer használatát, javasoljuk, hogy használjon egy SQL Server Express adattárat és az MSSQL-illesztőprogramot.

1. Töltse le és telepítse a [Microsoft SQL Server 2014 Express]programot. Győződjön meg arról, hogy a SQL Server 2014 Express és a Tools Edition verzióját telepíti. Ha kifejezetten nem igényel 64-bites támogatást, a 32 bites verzió kevesebb memóriát használ a futtatáskor.
1. SQL Server 2014 Configuration Manager futtatása:

   a. Bontsa ki a **SQL Server hálózati konfiguráció** csomópontot a fa menüben.

   b. Válassza ki **a SQLExpress protokollokat**.

   c. Kattintson a jobb gombbal a **TCP/IP** elemre, és válassza az **Engedélyezés**lehetőséget. A felugró párbeszédpanelen kattintson **az OK gombra** .

   d. Kattintson a jobb gombbal a **TCP/IP** elemre, és válassza a **Tulajdonságok**lehetőséget.

   e. Válassza az **IP-címek** fület.

   f. Keresse meg a **IPAll** csomópontot. A **TCP-port** mezőben adja meg a **1433**értéket.

      ![A TCP/IP SQL Server Express konfigurálása][3]

   g. Kattintson az **OK** gombra. A felugró párbeszédpanelen kattintson **az OK gombra** .

   h. Válassza ki **SQL Server szolgáltatások** elemet a fa menüben.

   i. Kattintson a jobb gombbal a **SQL Server (SQLExpress)** elemre, majd válassza az **Újraindítás**lehetőséget.

   j. SQL Server 2014 Configuration Manager bezárásához.

1. Futtassa SQL Server 2014 Management Studio, és kapcsolódjon a helyi SQL Server Express-példányhoz:

   1. Kattintson a jobb gombbal a példányra Object Explorer és válassza a **Tulajdonságok**lehetőséget.
   1. Válassza a **Biztonság** lapot.
   1. Győződjön meg arról, hogy a **SQL Server és a Windows-hitelesítési mód** van kiválasztva.
   1. Kattintson az **OK** gombra.

      ![SQL Server Express hitelesítés konfigurálása][4]
   1. Bontsa ki a **biztonság** > **bejelentkezések** Object Explorerban elemet.
   1. Kattintson a jobb gombbal a **bejelentkezések** elemre, és válassza az **új bejelentkezés**lehetőséget.
   1. Adja meg a bejelentkezési nevet. Kattintson az **SQL Server-hitelesítés** lehetőségre. Adjon meg egy jelszót, majd adja meg ugyanazt a jelszót a **Jelszó megerősítése**mezőben. A jelszónak meg kell felelnie a Windows bonyolultsági követelményeinek.
   1. Kattintson az **OK** gombra.

      ![Új felhasználó hozzáadása SQL Server Express][5]
   1. Kattintson a jobb gombbal az új bejelentkezésre, és válassza a **Tulajdonságok**lehetőséget.
   1. Válassza ki a **kiszolgálói szerepkörök** lapot.
   1. Jelölje be a **dbcreator** kiszolgálói szerepkör jelölőnégyzetét.
   1. Kattintson az **OK** gombra.
   1. SQL Server 2015 Management Studio bezárásához.

Ügyeljen arra, hogy jegyezze fel a kiválasztott felhasználónevet és jelszót. Előfordulhat, hogy az adatbázis követelményeitől függően további kiszolgálói szerepköröket vagy engedélyeket kell rendelnie.

A Node. js-alkalmazás beolvassa a `SQLCONNSTR_MS_TableConnectionString` környezeti változót az adatbázishoz tartozó kapcsolatok karakterláncához. Ezt a változót beállíthatja a környezetében. A PowerShell használatával például beállíthatja a környezeti változót:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Az adatbázis elérése TCP/IP-kapcsolaton keresztül. Adja meg a kapcsolatok felhasználónevét és jelszavát.

### <a name="howto-config-localdev"></a>A projekt konfigurálása helyi fejlesztéshez

Mobile Apps beolvas egy *azureMobile. js* nevű JavaScript-fájlt a helyi fájlrendszerből. Ne használja ezt a fájlt az Mobile Apps SDK éles környezetben való konfigurálásához. Ehelyett használja a [Azure Portal] **alkalmazás beállításait** .

A azureMobile. js fájlnak exportálnia kell egy konfigurációs objektumot. A leggyakoribb beállítások a következők:

* Adatbázis-beállítások
* Diagnosztikai naplózási beállítások
* Alternatív CORS-beállítások

Ez a példa a **azureMobile. js** fájlt implementálja az előző adatbázis-beállításokkal:

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

Javasoljuk, hogy vegye fel a **azureMobile. js** fájlt a **. gitignore** fájlba (vagy más forráskód-vezérlőelem figyelmen kívül hagyása), hogy megakadályozza a jelszavak felhőben történő tárolását. Az **alkalmazás beállításaiban** mindig konfigurálja a termelési beállításokat a [Azure Portal]belül.

### <a name="howto-appsettings"></a>Alkalmazásbeállítások konfigurálása a Mobile App számára

A azureMobile. js fájl legtöbb beállítása egyenértékű alkalmazás-beállítást tartalmaz a [Azure Portal]. A következő lista használatával konfigurálhatja az alkalmazást az **alkalmazás beállításaiban**:

| Alkalmazás beállítása | azureMobile.js setting | Leírás | Érvényes értékek |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Az alkalmazás neve |Karakterlánc |
| **MS_MobileLoggingLevel** |naplózási szint |A naplózandó üzenetek minimális naplózási szintje |hiba, figyelmeztetés, információ, részletes, hibakeresés, buta |
| **MS_DebugMode** |Debug |Hibakeresési mód engedélyezése vagy letiltása |true, false |
| **MS_TableSchema** |az Adatséma |SQL-táblák alapértelmezett sémájának neve |karakterlánc (alapértelmezett: dbo) |
| **MS_DynamicSchema** |az dynamicSchema. |Hibakeresési mód engedélyezése vagy letiltása |true, false |
| **MS_DisableVersionHeader** |verzió (nem definiált értékre van állítva) |Letiltja az X-ZUMO-Server-Version fejlécet |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Letiltja az ügyfél API-verziójának ellenőrzését |true, false |

Alkalmazás beállításának beállítása:

1. Jelentkezzen be az [Azure Portal].
1. Válassza ki az **összes erőforrás** vagy **app Services**lehetőséget, majd válassza ki a mobil alkalmazás nevét.
1. A **Beállítások** ablaktábla alapértelmezés szerint megnyílik. Ha nem, válassza a **Beállítások**lehetőséget.
1. Az **általános** menüben válassza az **Alkalmazásbeállítások**lehetőséget.
1. Görgessen az **Alkalmazásbeállítások** szakaszhoz.
1. Ha az alkalmazás már létezik, válassza ki az alkalmazás beállítás értékét az érték szerkesztéséhez.
   Ha az alkalmazás beállítása nem létezik, adja meg az alkalmazás beállításait a **kulcs** mezőben **és az érték mezőben.**
1. Kattintson a **Mentés** gombra.

A legtöbb alkalmazás beállításainak módosításához a szolgáltatás újraindítása szükséges.

### <a name="howto-use-sqlazure"></a>SQL Database használata üzemi adattárként

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

A Azure SQL Database adattárakként való használata azonos az összes Azure App Service alkalmazás típusával. Ha még nem tette meg, kövesse az alábbi lépéseket Mobile Apps háttér létrehozásához:

1. Jelentkezzen be az [Azure Portal].
1. Az ablak bal felső részén válassza az **+ új** gombot > **web és mobil** > **Mobile alkalmazást**, majd adja meg a Mobile apps háttér nevét.
1. Az **erőforráscsoport** mezőben adja meg az alkalmazás nevének megadását.
1. Az alapértelmezett App Service terv van kiválasztva. Ha módosítani szeretné a App Service tervet:

   a. Válassza ki **app Service csomagot** >  **+ új létrehozása**lehetőséget.

   b. Adja meg az új App Service terv nevét, és válasszon ki egy megfelelő helyet.

   c. Válasszon ki egy megfelelő díjszabási szintet a szolgáltatáshoz. Az **összes megtekintése** elemre kattintva további díjszabási lehetőségeket jeleníthet meg, például az **ingyenes** és a **megosztott**lehetőséget.

   d. Kattintson a **kiválasztás** gombra.

   e. A **app Service terv** ablaktáblán kattintson az **OK gombra**.
1. Kattintson a **Létrehozás** gombra.

Egy Mobile Apps-háttér kiépítés néhány percig is eltarthat. A Mobile Apps háttér kiépítés után a portál megnyitja a Mobile Apps háttér **Beállítások** paneljét.

Választhat, hogy a meglévő SQL-adatbázist összekapcsolja-e a Mobile Apps háttérrel, vagy létrehozhat egy új SQL-adatbázist. Ebben a szakaszban egy SQL-adatbázist hozunk létre.

> [!NOTE]
> Ha már van olyan adatbázisa, amely a Mobile Apps háttérrel azonos helyen található, akkor válassza a **meglévő adatbázis használata** lehetőséget, majd válassza ki az adatbázist. A magasabb késések miatt nem javasoljuk, hogy más helyen használja az adatbázist.

1. Az új Mobile Apps háttérben válassza a **beállítások** > **Mobile App**@no__t **-3 @no__t**-5 **+ Hozzáadás**lehetőséget.
1. Az **adatkapcsolatok hozzáadása** panelen válassza a **SQL Database – a szükséges beállítások konfigurálása**@no__t – 2**új adatbázis létrehozása**elemet. Adja meg az új adatbázis nevét a **név** mezőben.
1. Válassza a **kiszolgáló**lehetőséget. Az **új kiszolgáló** panelen adjon meg egy egyedi kiszolgálónevet a **kiszolgáló neve** mezőbe, és adjon meg egy megfelelő kiszolgálói rendszergazdai felhasználónevet és jelszót. Győződjön meg arról, hogy az **Azure-szolgáltatások elérésének engedélyezése kiszolgáló** van kiválasztva. Kattintson az **OK** gombra.

   ![Azure SQL-adatbázis létrehozása][6]
1. Az **új adatbázis** ablaktáblán kattintson az **OK gombra**.
1. Az **adatkapcsolatok hozzáadása** panelen lépjen vissza a **kapcsolódási karakterlánc**elemre, és adja meg az adatbázis létrehozásakor megadott felhasználónevet és jelszót. Ha meglévő adatbázist használ, adja meg az adatbázishoz tartozó bejelentkezési hitelesítő adatokat. Kattintson az **OK** gombra.
1. Ismét az **adatkapcsolatok hozzáadása** ablaktáblán kattintson ismét az **OK** gombra az adatbázis létrehozásához.

<!--- END OF ALTERNATE INCLUDE -->

Az adatbázis létrehozása eltarthat néhány percig. A központi telepítés előrehaladásának figyeléséhez használja az **értesítések** területét. A folyamat addig nem fejeződik be, amíg az adatbázist nem sikerült üzembe helyezni. Az adatbázis üzembe helyezése után létrejön egy, a SQL Database példányhoz tartozó, a Mobile Apps háttérbeli alkalmazás beállításaiban létrehozott kapcsolatok karakterlánca. Ezt az alkalmazást a beállítások  > **alkalmazásbeállítások** > **kapcsolatok karakterláncok** **menüpontban**tekintheti meg.

### <a name="howto-tables-auth"></a>A táblákhoz való hozzáférés hitelesítésének megkövetelése

Ha App Service hitelesítést kíván használni a `tables` végponttal, először konfigurálnia kell App Service hitelesítést az [Azure Portal] . További információkért tekintse meg a használni kívánt identitás-szolgáltató konfigurációs útmutatóját:

* [Azure Active Directory hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [A Google-hitelesítés konfigurálása]
* [A Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Minden táblához tartozik egy hozzáférési tulajdonság, amellyel szabályozható a táblázathoz való hozzáférés. Az alábbi példa egy statikusan definiált táblázatot mutat be, amely hitelesítés szükséges.

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

A hozzáférési tulajdonság három érték egyikét veheti igénybe:

* a *Névtelen* érték azt jelzi, hogy az ügyfélalkalmazás hitelesítés nélkül is engedélyezheti az adatolvasást.
* a *hitelesített* érték azt jelzi, hogy az ügyfélalkalmazás érvényes hitelesítési jogkivonatot kell küldenie a kérelemmel.
* a *Letiltva* érték azt jelzi, hogy ez a tábla jelenleg le van tiltva.

Ha a hozzáférés tulajdonság nincs meghatározva, a nem hitelesített hozzáférés engedélyezett.

### <a name="howto-tables-getidentity"></a>Hitelesítési jogcímek használata a táblákkal
A hitelesítés beállításakor különböző jogcímeket is beállíthat. Ezek a jogcímek általában nem érhetők el a `context.user` objektumon keresztül. Ezeket azonban a `context.user.getIdentity()` metódus használatával kérheti le. A `getIdentity()` metódus egy olyan ígéretet ad vissza, amely egy objektumra van feloldva. Az objektumot a hitelesítési módszer (`facebook`, `google`, `twitter`, `microsoftaccount` vagy `aad`) alapján írtuk be.

Ha például beállít Microsoft-fiók hitelesítést, és kéri az e-mail-címek jogcímet, az alábbi táblázatos vezérlővel adhatja hozzá az e-mail-címet a rekordhoz:

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

Ha szeretné megtekinteni, hogy milyen jogcímek érhetők el, a webböngészőben megtekintheti a hely `/.auth/me` végpontját.

### <a name="howto-tables-disabled"></a>Adott tábla műveleteihez való hozzáférés letiltása

A táblázaton kívül a hozzáférési tulajdonság az egyes műveletek szabályozására is használható. Négy művelet létezik:

* a `read` a REST-lekérési művelet a táblán.
* a `insert` a REST utáni művelet a táblán.
* a `update` a REST-javító művelet a táblán.
* @no__t – 0 – a REST-alapú TÖRLÉSi művelet a táblán.

Előfordulhat például, hogy csak olvasható, nem hitelesített táblát szeretne megadni:

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

### <a name="howto-tables-query"></a>A táblázatos műveletekhez használt lekérdezés módosítása

A táblázatos műveletekre vonatkozó általános követelmény az adatok korlátozott nézetének biztosítása. Megadhat például egy olyan táblát, amely a hitelesített felhasználói AZONOSÍTÓval van megjelölve, hogy csak a saját rekordjait tudja olvasni vagy frissíteni. A következő táblázat definíciója biztosítja ezt a funkciót:

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

A lekérdezést általában futtató műveletek lekérdezési tulajdonsága a `where` záradék használatával módosítható. A lekérdezési tulajdonság egy [QueryJS] objektum, amely egy OData-lekérdezés átalakítására szolgál az adatháttér által feldolgozható értékre. Az egyszerű esélyegyenlőségi esetekben (például az előzőnél) használhatja a térképet. Emellett adott SQL-záradékokat is hozzáadhat:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Egy tábla helyreállítható törlésének konfigurálása

A helyreállítható törlés valójában nem törli A rekordokat. Ehelyett a törölt oszlop igaz értékre állításával az adatbázison belül töröltként jelöli meg őket. Az Mobile Apps SDK automatikusan eltávolítja a nem törölt rekordokat az eredményekből, kivéve, ha a Mobile Client SDK `IncludeDeleted()` értéket használ. Ha egy táblázatot szeretne konfigurálni a helyreállítható törléshez, állítsa be a `softDelete` tulajdonságot a tábla definíciós fájljába:

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

Létre kell hoznia egy mechanizmust a rekordok törléséhez: egy ügyfélalkalmazás, egy Webjobs, egy Azure-függvény vagy egy egyéni API.

### <a name="howto-tables-seeding"></a>Az adatbázis kiminősítése az adataival

Amikor új alkalmazást hoz létre, érdemes lehet egy táblázatot felosztania egy adattal. Ezt a táblázat definíciójának JavaScript-fájljában a következőképpen teheti meg:

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

Az adatgyűjtés csak akkor történik meg, ha a Mobile Apps SDK-t használta a tábla létrehozásához. Ha a tábla már létezik az adatbázisban, a táblázatba nem kerül be a rendszer az összes adattal. Ha a dinamikus séma be van kapcsolva, a séma a kikövetkeztetett adatokból származik.

Javasoljuk, hogy explicit módon hívja meg a `tables.initialize()` metódust a tábla létrehozásához, amikor a szolgáltatás elindul.

### <a name="Swagger"></a>A hencegés támogatásának engedélyezése
A Mobile Apps beépített [Swagger] -támogatással rendelkezik. A hencegés támogatásának engedélyezéséhez először telepítse a hencegő felületet a következő függőségként:

    npm install --save swagger-ui

Ezután engedélyezheti a hencegő támogatást a Mobile Apps konstruktorban:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Valószínűleg csak a fejlesztési kiadásokban szeretné engedélyezni a hencegés támogatását. Ezt a `NODE_ENV` alkalmazás beállításával teheti meg:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

A `swagger` végpont a következő helyen található: http://*sajatwebhely*. azurewebsites.net/Swagger. A hencegő FELÜLETET a `/swagger/ui` végponton keresztül érheti el. Ha úgy dönt, hogy a teljes alkalmazásban megköveteli a hitelesítést, a hencegés hibát eredményez. A legjobb eredmények érdekében a Azure App Service hitelesítési/engedélyezési beállításokban engedélyezze a nem hitelesített kérelmeket, majd a `table.access` tulajdonság használatával szabályozza a hitelesítést.

A azureMobile. js fájlhoz is hozzáadhatja a hencegés lehetőséget, ha azt szeretné, hogy a helyi fejlesztéshez csak a kivágási támogatás legyen használható.

## <a name="a-namepushpush-notifications"></a>@no__t – 0Push értesítések

A Mobile Apps az Azure Notification Hubs integrálódik, így az összes jelentős platformon több millió eszközre küldhet célként leküldéses értesítéseket. A Notification Hubs használatával leküldéses értesítéseket küldhet iOS-, Android-és Windows-eszközökre. Ha többet szeretne megtudni a Notification Hubsekkel kapcsolatos összes lehetőségről, tekintse meg a [Notification Hubs áttekintését](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Leküldéses értesítések küldése

A következő kód bemutatja, hogyan küldhet leküldéses értesítéseket a regisztrált iOS-eszközökre a `push` objektum használatával:

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

Egy sablon leküldéses regisztrációjának az ügyfélről történő létrehozásával Ehelyett egy sablon leküldéses üzenetet küldhet az összes támogatott platformon lévő eszközre. A következő kód bemutatja, hogyan küldhet el egy sablon-értesítést:

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

### <a name="push-user"></a>Leküldéses értesítések küldése hitelesített felhasználóknak címkék használatával
Ha egy hitelesített felhasználó regisztrálja a leküldéses értesítéseket, a rendszer automatikusan hozzáadja a felhasználói azonosító címkét a regisztrációhoz. A címke használatával leküldéses értesítéseket küldhet az adott felhasználó által regisztrált összes eszközre. A következő kód lekéri a kérést küldő felhasználó SID-azonosítóját, és leküldéses értesítést küld a felhasználó minden eszközének regisztrálására:

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

Ha hitelesített ügyfélről regisztrál leküldéses értesítéseket, a regisztráció megkísérlése előtt győződjön meg arról, hogy a hitelesítés befejeződött.

## <a name="CustomAPI"></a>Egyéni API-k

### <a name="howto-customapi-basic"></a>Egyéni API definiálása

Az `/tables` végponton keresztül az adatelérési API mellett Mobile Apps egyéni API-lefedettséget is biztosíthat. Az egyéni API-k hasonló módon vannak definiálva a táblázat-definíciók számára, és hozzáférhetnek ugyanahhoz a szolgáltatáshoz, beleértve a hitelesítést is.

Ha egyéni API-val szeretne App Service hitelesítést használni, először konfigurálnia kell App Service hitelesítést a [Azure Portal] . További információkért tekintse meg a használni kívánt identitás-szolgáltató konfigurációs útmutatóját:

* [Azure Active Directory hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [A Google-hitelesítés konfigurálása]
* [A Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Az egyéni API-k a Tables API-val megegyező módon vannak definiálva:

1. Hozzon létre egy `api` könyvtárat.
1. Hozzon létre egy API-definíció JavaScript-fájlt a `api` könyvtárban.
1. Az importálási módszer használatával importálhatja a `api` könyvtárat.

Itt látható a prototípus API-definíciója, amely a korábban használt Basic-app minta alapján történik:

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

Vegyük egy példa API-t, amely a `Date.now()` metódus használatával visszaadja a kiszolgáló dátumát. Itt látható az API/Date. js fájl:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Minden paraméter a szabványos REST-műveletek egyike: Letöltés, közzététel, javítás vagy törlés. A metódus egy standard [ExpressJS middleware] -függvény, amely elküldi a szükséges kimenetet.

### <a name="howto-customapi-auth"></a>Hitelesítés megkövetelése egyéni API-hoz való hozzáféréshez

A Mobile Apps SDK ugyanúgy valósítja meg a hitelesítést, mint a `tables` végpont és az egyéni API-k. Az előző szakaszban kifejlesztett API-hoz való hitelesítés hozzáadásához adjon hozzá egy `access` tulajdonságot:

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

Megadhatja az adott műveletek hitelesítését is:

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

A `tables` végponthoz használt jogkivonatot kell használni a hitelesítést igénylő egyéni API-khoz.

### <a name="howto-customapi-auth"></a>Nagyméretű fájlfeltöltés kezelése

Az Mobile Apps SDK a [Body-Parser middleware](https://github.com/expressjs/body-parser) használatával fogadja el és dekódolja a szövegtörzs tartalmát a beküldött szövegben. A szövegtörzs-elemző előre konfigurálható nagyobb fájlfeltöltés elfogadásához:

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

A fájl a Base-64 kódolású a továbbítás előtt. Ez a kódolás növeli a tényleges feltöltés méretét (és azt a méretet, amelyhez a szükséges).

### <a name="howto-customapi-sql"></a>Egyéni SQL-utasítások végrehajtása

A Mobile Apps SDK a kérelem objektumon keresztül lehetővé teszi a teljes környezet elérését. A paraméteres SQL-utasításokat egyszerűen végrehajthatja a definiált adatszolgáltatón:

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

## <a name="Debugging"></a>Hibakeresés

### <a name="howto-diagnostic-logs"></a>Hibakeresés, diagnosztizálás és hibaelhárítás Mobile Apps

A Azure App Service számos hibakeresési és hibaelhárítási eljárást biztosít a Node. js-alkalmazásokhoz.
A Node. js Mobile Apps háttér hibaelhárításának megkezdéséhez tekintse meg a következő cikkeket:

* [Figyelés Azure App Service]
* [Diagnosztikai naplózás engedélyezése Azure App Service]
* [A Visual Studióban Azure App Service hibakeresése]

A Node. js-alkalmazások számos diagnosztikai naplózási eszközhöz férnek hozzá. Belsőleg a Mobile Apps Node. js SDK a [Winston] használja a diagnosztikai naplózáshoz. A naplózás automatikusan engedélyezve van, ha engedélyezi a hibakeresési módot, vagy a [Azure Portal]a `MS_DebugMode` alkalmazás beállítása igaz értékre van állítva. A generált naplók a [Azure Portal]diagnosztikai naplóiban jelennek meg.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android-ügyfél rövid útmutatója]: app-service-mobile-android-get-started.md
[Apache Cordova-ügyfél gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[iOS-ügyfél rövid útmutatója]: app-service-mobile-ios-get-started.md
[Xamarin. iOS-ügyfél rövid útmutatója]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android Client quickstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin. Forms-ügyfél rövid útmutatója]: app-service-mobile-xamarin-forms-get-started.md
[Windows áruházbeli ügyfél rövid útmutatója]: app-service-mobile-windows-store-dotnet-get-started.md
[Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[Azure Active Directory hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-aad.md
[Facebook-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-facebook.md
[A Google-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-google.md
[A Microsoft-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-twitter.md
[Azure App Service telepítési útmutató]: ../app-service/deploy-local-git.md
[Figyelés Azure App Service]: ../app-service/web-sites-monitor.md
[Diagnosztikai naplózás engedélyezése Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[A Visual Studióban Azure App Service hibakeresése]: ../app-service/troubleshoot-dotnet-visual-studio.md
[a csomópont verziószámának meghatározása]: ../nodejs-specify-node-version-azure-apps.md
[Csomópont-modulok használata]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Ígéret]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp-minta a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[teendői minta a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Példák könyvtára a GitHubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node. js-eszközök a Visual studióhoz 1,1]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node. js-csomag]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
