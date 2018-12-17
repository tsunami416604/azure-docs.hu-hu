---
title: A Node.js háttérrendszeri kiszolgáló SDK használata a Mobile Apps-alkalmazáshoz |} A Microsoft Docs
description: Megtudhatja, hogyan használható a Node.js háttérrendszeri kiszolgáló SDK az Azure App Service Mobile Apps a.
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
ms.openlocfilehash: e435baf4a5e3382e3e77195e5315299e3963ff5c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409258"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>A Mobile Apps Node.js SDK használata

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Ez a cikk részletes információkat nyújt, és használata a Node.js-példák az Azure App Service Mobile Apps funkcióját a háttéralkalmazás.

## <a name="Introduction"></a>Bevezetés

A Mobile Apps lehetővé teszi, hogy mobileszközökre optimalizált adatelérési webes API hozzáadása egy webalkalmazáshoz. A Mobile Apps SDK-t az ASP.NET és a Node.js webes alkalmazások biztosítunk. Az SDK biztosítja a következő műveleteket:

* Tábla műveletek (olvassa el, beszúrása, frissítése és törlése) az adatok eléréséhez
* Egyéni API-műveletek

Mindkét művelet összes Identitásszolgáltatók, amelyek az Azure App Service lehetővé teszi, hogy egységes a hitelesítéshez. Ezek a szolgáltatók közé tartozik a közösségi identitásszolgáltató, például Facebook, Twitter, Google, és a Microsoft, valamint az Azure Active Directory vállalati identitás.

Szemléltető példák kereséséhez az egyes használati esetekhez a [minták könyvtárát a Githubon].

## <a name="supported-platforms"></a>Támogatott platformok

A Mobile Apps Node.js SDK támogatja a jelenlegi LTS verzió, csomópont és újabb verziók. A legújabb LTS verzióját jelenleg csomópont v4.5.0. Csomópont más verzióiban előfordulhat, hogy működik, de nem támogatottak.

A Mobile Apps Node.js SDK két adatbázis-illesztőprogram támogatja: 

* A csomópont-mssql-illesztőprogram támogatja az Azure SQL Database és a helyi SQL Server-példányokat.  
* A sqlite3 illesztőprogram csak egyetlen példányán SQLite-adatbázis támogatja.

### <a name="howto-cmdline-basicapp"></a>Hozzon létre egy alapszintű Node.js-háttérrendszer a parancssor használatával

Minden Mobile Apps Node.js háttérrendszer ExpressJS alkalmazást indítja el. ExpressJS népszerű webes szolgáltatás keretében elérhető, a node.js-hez. Létrehozhat egy alapszintű [Express] alkalmazás a következőképpen:

1. Egy parancs vagy a PowerShell-ablakot hozzon létre egy könyvtárat a projekthez:

        mkdir basicapp

1. Futtatás `npm init` inicializálása a csomag szerkezete:

        cd basicapp
        npm init

   A `npm init` parancs kéri a projekt inicializálása kérdésekre. A példa kimenet látható:

   ![Az npm init kimenet][0]

1. Telepítse a `express` és `azure-mobile-apps` az npm-adattárból könyvtárak:

        npm install --save express azure-mobile-apps

1. Hozzon létre egy app.js fájlban az alapvető mobileszköz kiszolgáló megvalósításához:

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

Ezt az alkalmazást a mobileszközökre optimalizált webes API-t hoz létre egyetlen végpontot (`/tables/TodoItem`), amely az alapul szolgáló SQL-adattár nem hitelesített hozzáférést biztosít a dinamikus séma használatával. Ez ideális az ügyféloldali kódtár rövid útmutatók a következő:

* [Android ügyfél a rövid útmutató]
* [Apache Cordova-ügyfél gyors üzembe helyezés]
* [iOS-es ügyfél a rövid útmutató]
* [Windows Store-ügyfél a rövid útmutató]
* [Xamarin.iOS-ügyfél a rövid útmutató]
* [Xamarin.Android-ügyfél a rövid útmutató]
* [Xamarin.Forms-ügyfél a rövid útmutató]

Ez a alapvető alkalmazás találja a kódot a [basicapp minta a Githubon].

### <a name="howto-vs2015-basicapp"></a>Egy Node.js-háttérrendszer létrehozása a Visual Studio 2015 használatával

Visual Studio 2015-bővítményt az ide Node.js-alkalmazások fejlesztéséhez szükséges. Először telepítse a [1.1 node.js-eszközök a Visual Studióhoz]. Amikor befejezi a telepítést, hozzon létre egy expressz 4.x-es alkalmazás:

1. Nyissa meg a **új projekt** párbeszédpanel (a **fájl** > **új** > **projekt**).
1. Bontsa ki a **sablonok** > **JavaScript** > **Node.js**.
1. Válassza ki **alapszintű Azure Node.js Express 4-alkalmazás**.
1. Adja meg a projekt nevét. Kattintson az **OK** gombra.

   ![A Visual Studio 2015-ben új projekt][1]
1. Kattintson a jobb gombbal a **npm** csomópontra, és válassza **új telepítése az npm csomagok**.
1. Akkor lehet, hogy frissítenie kell az npm-katalógus az első Node.js-alkalmazás létrehozása után. Válassza ki **frissítése** szükség esetén.
1. Adja meg **azure-mobilalkalmazások** kifejezést a keresőmezőbe. Válassza ki a **azure-mobile-alkalmazások 2.0.0-s** csomagot, és válassza ki **telepítőcsomag**.

   ![Új npm-csomagok telepítése][2]
1. Válassza ki **Bezárás**.
1. Nyissa meg a Mobile Apps SDK-támogatás hozzáadása az app.js fájlban. At sor 6 at a tár alján `require` utasítások, adja hozzá a következő kódot:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Egymás után körülbelül sor 27 at `app.use` utasítások, adja hozzá a következő kódot:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Mentse a fájlt.

1. Akár az a alkalmazás helyileg történő futtatása (az API-t a kiszolgált http://localhost:3000) vagy közzéteheti az Azure-bA.

### <a name="create-node-backend-portal"></a>Egy Node.js-háttérrendszer létrehozása az Azure portal használatával

A Mobile Apps-háttéralkalmazás közvetlenül is létrehozhat a a [Azure Portal]. A következő lépéseket, vagy hozzon létre egy ügyfél és kiszolgáló együtt a következő a [mobilalkalmazás létrehozása](app-service-mobile-ios-get-started.md) oktatóanyag. Az oktatóanyag egy egyszerűsített változata ezeket az utasításokat tartalmaz, és nem proof-of-concept projektek esetén ajánlott használni.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Térjen vissza a **első lépései** panel alatt **tábla API létrehozása**, válassza a **Node.js** háttér-nyelvet.
Jelölje be a **tudomásul veszem, hogy ezzel felülírja az összes hely tartalmának**, majd válassza ki **TodoItem tábla létrehozása**.

### <a name="download-quickstart"></a>Töltse le a Node.js háttér-rövid kód projekt a Git használatával

Ha egy Node.js Mobile Apps-háttéralkalmazás létrehozása a portál használatával **gyors üzembe helyezési** ablaktáblán egy Node.js-projektet létre, és a hely telepítve. A portálon adja hozzá a táblák és API-k, és a Node.js-háttérrendszer tartozó kódfájlok szerkesztése. Különböző központi telepítési eszközök használatával töltse le a háttér-projektet, így hozzáadása vagy módosítása a táblák és API-k, és a projekt majd újbóli. További információkért lásd: a [Az Azure App Service üzembe helyezési útmutató].

Az alábbi eljárás egy Git-tárház használatával töltse le a rövid útmutató projekt kódja:

1. Ha ezt még nem tette meg, telepítse a Git. A Git telepítéséhez szükséges lépések eltérőek lehetnek, operációs rendszerek között. Operációsrendszer-specifikus disztribúciók és a telepítéssel kapcsolatos útmutató: [telepítése Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
1. Lásd: [készítse elő a tárház](../app-service/app-service-deploy-local-git.md#prepare-your-repository) ahhoz, hogy a Git-tárházba, a háttér-helyhez. Jegyezze fel a központi telepítési felhasználónevet és jelszót.
1. A panelen for a Mobile Apps-háttéralkalmazáshoz, jegyezze fel a **Git clone URL-cím** beállítás.
1. Hajtsa végre a `git clone` parancsot a Git clone URL-cím használatával. Adja meg a jelszót, ha szükséges, az alábbi példában látható módon:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

1. Tallózással keresse meg a helyi könyvtárban (`/todolist` az előző példában), és figyelje meg, hogy project fájlok le vannak töltve. Keresse meg a todoitem.json fájlt a `/tables` könyvtár. Ez a fájl engedélyeit a tábla határozza meg. Ugyanabban a címtárban todoitem.js fájl is található. Azt határozza meg a CRUD-MŰVELETEKKEL művelet parancsfájlok a táblához.
1. Soubory projektu: futtassa a következő parancsokat hozzáadása, módosítása után véglegesítés, és ezután töltse fel a módosítások a hely:

        $ git commit -m "updated the table script"
        $ git push origin master

   Ha új fájlokat ad hozzá a projekthez, először futtassa a `git add .` parancsot.

A hely minden alkalommal, amikor a véglegesítés új készletét a rendszer továbbítja a hely ismételt közzététele.

### <a name="howto-publish-to-azure"></a>A Node.js-háttérrendszer közzététele az Azure-bA

Microsoft Azure lehetőséget kínál számos mechanizmusok közzétételéhez a Mobile Apps Node.js háttér az Azure-szolgáltatás. Ezek a mechanizmusok központi telepítési eszközök a Visual Studio integrált parancssori eszközökkel és verziókövetési alapuló folyamatos üzembe helyezési beállítások. További információkért lásd: a [Az Azure App Service üzembe helyezési útmutató].

Az Azure App Service rendelkezik konkrét útmutatásért tekintse át Node.js-alkalmazásokhoz a háttérrendszer közzététele előtt:

* Hogyan [adja meg a csomópont verziója]
* Hogyan [Node-modulok használata]

### <a name="howto-enable-homepage"></a>Az alkalmazás kezdőlapját engedélyezése

Számos alkalmazás olyan webes és mobilalkalmazások kombinációját. Használhatja a ExpressJS keretrendszer úgy, hogy a két metszettel. Egyes esetekben azonban érdemes csak a mobil felület megvalósításához. Ez nem hasznos, ha szeretné, adja meg a kezdőlap, győződjön meg arról, hogy működik az app Service-ben, és futó. Adja meg a saját kezdőlapján, vagy egy ideiglenes kezdőlap engedélyezése. Ahhoz, hogy egy ideiglenes kezdőlapja, a következő kód használatával hozza létre a Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Ha csak ez a beállítás érhető el helyi fejlesztése során, ezzel a beállítással adhat a azureMobile.js fájlt.

## <a name="TableOperations"></a>Tábla műveletek

Az azure-mobilalkalmazások Node.js Server SDK teszi közzé a webes API-ként az Azure SQL Database-ben tárolt adattáblák mechanizmusokat biztosít. Öt műveleti biztosít:

| Művelet | Leírás |
| --- | --- |
| GET /tables/*táblanév* |A tábla összes rekord beolvasása. |
| GET /tables/*tablename*/:id |Kérje le a táblázat egy adott rekordot. |
| POST /tables/*táblanév* |Hozzon létre egy rekordot a táblában. |
| JAVÍTÁS /tables/*tablename*/:id |A táblázat rekordjának frissítése. |
| TÖRLÉS /tables/*tablename*/:id |A tábla rekord törlése. |

A webes API támogatja a [OData] és kiterjeszti a támogatásához a következő tábla sémáját [offline adatszinkronizálást].

### <a name="howto-dynamicschema"></a>-Táblát határoz meg a dinamikus séma használatával

Egy tábla használata előtt meg kell határoznia. Statikus séma használatával (amikor Ön határozza meg az oszlopok a sémában) meghatározhatja a táblák vagy dinamikusan (ahol az SDK szabályozza a bejövő kérések alapján séma). Emellett a webes API meghatározott részeire szabályozhatja definíciójának JavaScript-kód hozzáadásával.

Ajánlott eljárásként, be kell állítania az minden táblát egy JavaScript fájljában a `tables` könyvtárra, és majd a `tables.import()` módszerrel importálhatja a táblákat. Az alapszintű mintaalkalmazásban kiterjesztése, akkor módosíthatja az app.js fájlban:

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

A tábla meghatározása. / tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Táblák használata a dinamikus sémák alapértelmezés szerint. A dinamikus sémák kikapcsolását globálisan, állítsa be a `MS_DynamicSchema` Alkalmazásbeállítás hamisra az Azure Portalon.

A teljes példát talál a [teendőlista-minta a Githubon].

### <a name="howto-staticschema"></a>-Táblát határoz meg egy statikus séma használatával

A webes API-n keresztül közzétenni kívánt oszlopok explicit módon határozhatja meg. Az azure-mobilalkalmazások Node.js SDK automatikusan hozzáadja a listához, Ön által megadott offline adatszinkronizálást szükséges további oszlopokat. Ha például a a rövid útmutató ügyfélalkalmazások kell két oszlopot tartalmazó táblát: `text` (karakterlánc) és `complete` (Boolean).  
A tábla lehet definiálni a tábla definícióját JavaScript-fájlt (található a `tables` könyvtár) a következő:

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

Táblák statikusan megadása esetén is hívja meg a `tables.initialize()` metódussal hoz létre az adatbázissémát az indításakor. A `tables.initialize()` metódus adja vissza egy [Megtartva] úgy, hogy a webszolgáltatás nem kiszolgálni a kérelmeket az adatbázis inicializálása előtt.

### <a name="howto-sqlexpress-setup"></a>SQL Server Expresst használ a helyi gépen fejlesztési adattárként

A Mobile Apps Node.js SDK-t, de az adatok beépített három lehetőséget kínál:

* Használja a **memória** illesztőprogram adjon meg egy nem állandó példa tárolóban.
* Használja a **mssql** illesztőprogram egy SQL Server Express adattár számára történő fejlesztését.
* Használja a **mssql** illesztőprogram adjon meg egy Azure SQL Database data store éles üzemi környezetek részei.

A Mobile Apps Node.js SDK-t használ a [mssql Node.js-csomag] létrehozásához, és a egy SQL Server Express és SQL Database-kapcsolat használatát. Ez a csomag igényli-e, hogy az SQL Server Express-példány a TCP-kapcsolatok engedélyezése.

> [!TIP]
> A memória-illesztőprogram nem biztosít tesztelési eszközök teljes körét. Tesztelje helyben a háttérrendszerének kívánt, ha egy SQL Server Express adattár és az mssql-illesztőprogram használatát javasoljuk.

1. Töltse le és telepítse [Microsoft SQL Server 2014 Express]. Győződjön meg arról, hogy telepíti az SQL Server 2014 Express Edition eszközök. Csak 64 bites támogatása explicit módon van szüksége, a 32 bites verziója a futtatásakor kevesebb memóriát használ fel.
1. Futtassa az SQL Server 2014 Configuration Manager:

   a. Bontsa ki a **SQL Server hálózati konfigurációja** csomópontra a fanézetben menü.

   b. Válassza ki **protokollokat az SQLEXPRESS**.

   c. Kattintson a jobb gombbal **TCP/IP** válassza **engedélyezése**. Válassza ki **OK** az előugró párbeszédpanelen.

   d. Kattintson a jobb gombbal **TCP/IP** válassza **tulajdonságok**.

   e. Válassza ki a **IP-címek** fülre.

   f. Keresse meg a **IPAll** csomópont. Az a **TCP-Port** írja be a következőt **1433-as**.

      ![Az SQL Server Express TCP/IP konfigurálása][3]

   g. Kattintson az **OK** gombra. Válassza ki **OK** az előugró párbeszédpanelen.

   h. Válassza ki **SQL Server Services** a fa menüben.

   i. Kattintson a jobb gombbal **SQL Server (SQLEXPRESS)** válassza **indítsa újra a**.

   j. Zárja be az SQL Server 2014 Configuration Manager.

1. SQL Server 2014 Management Studióban futtassa, és csatlakozzon az SQL Server Express helyi példányának:

   1. Kattintson a jobb gombbal a példány, az Object Explorerben, és válassza ki **tulajdonságok**.
   1. Válassza ki a **biztonsági** lapot.
   1. Ügyeljen arra, hogy **SQL Server és a Windows-hitelesítési mód** van kiválasztva.
   1. Kattintson az **OK** gombra.

      ![Az SQL Server Express-hitelesítés konfigurálása][4]
   1. Bontsa ki a **biztonsági** > **bejelentkezések** az Object Explorerben.
   1. Kattintson a jobb gombbal **bejelentkezések** válassza **új bejelentkezés**.
   1. Adja meg a bejelentkezési névhez. Kattintson az **SQL Server-hitelesítés** lehetőségre. Adjon meg egy jelszót, és írja be ugyanezt a jelszót **jelszó megerősítése**. A jelszónak meg kell felelnie a bonyolultsági feltételeknek Windows.
   1. Kattintson az **OK** gombra.

      ![Új felhasználó hozzáadása SQL Server Express][5]
   1. Kattintson a jobb gombbal az új bejelentkezési adatait, és válassza ki **tulajdonságok**.
   1. Válassza ki a **kiszolgálói szerepkörök** lapot.
   1. Jelölje be a jelölőnégyzetet a **dbcreator** kiszolgálói szerepkört.
   1. Kattintson az **OK** gombra.
   1. Zárja be az SQL Server 2015 Management Studiót.

Mindenképpen jegyezze fel a felhasználónevet és jelszót, amelyet kiválasztott. Előfordulhat, hogy szeretne további kiszolgálói szerepkörök vagy engedélyek, az adatbázis követelményei hozzárendelése.

A Node.js-alkalmazás olvasást a `SQLCONNSTR_MS_TableConnectionString` környezeti változó a kapcsolati karakterlánc ehhez az adatbázishoz. Ez a változó állíthatja be a környezetben. Például a PowerShell használatával állítsa be ezt a környezeti változót:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP-kapcsolaton keresztül érik el az adatbázist. Adjon meg egy felhasználónevet és jelszót a kapcsolathoz.

### <a name="howto-config-localdev"></a>Helyi fejlesztési projekt konfigurálásához

A Mobile Apps olvasó nevű JavaScript-fájlt *azureMobile.js* a helyi fájlrendszerből. A Mobile Apps SDK konfigurálásához éles környezetben ne használja ezt a fájlt. Ehelyett használjon **Alkalmazásbeállítások** a a [Azure Portal].

A azureMobile.js fájlt egy konfigurációs objektumot kell exportálni. A leggyakrabban használt beállítások a következők:

* Adatbázis-beállítások
* Diagnosztikai naplózás beállításait
* Alternatív CORS-beállítások

Ebben a példában **azureMobile.js** fájl valósítja meg az előző adatbázis beállításait:

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

Javasoljuk, hogy adja hozzá **azureMobile.js** , a **.gitignore** fájl (vagy más verziókövetési figyelmen kívül hagyja a fájl) megakadályozza, hogy a jelszavak tárolása a felhőben. Mindig úgy állítsa be az éles beállítások **Alkalmazásbeállítások** belül a [Azure Portal].

### <a name="howto-appsettings"></a>A mobilalkalmazás beállításainak konfigurálása

A azureMobile.js fájl beállításai a legtöbb rendelkezik egyenértékű alkalmazásbeállítást az [Azure Portal]. Az alábbi lista segítségével állítsa be alkalmazását a **Alkalmazásbeállítások**:

| Alkalmazás-beállítás | azureMobile.js setting | Leírás | Érvényes értékek |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |név |Az alkalmazás neve |sztring |
| **MS_MobileLoggingLevel** |Logging.level |Minimális naplózási szint üzenetek naplózása |Hiba, figyelmeztetés, információ részletes, hibakeresési, silly |
| **MS_DebugMode** |hibakeresés |Engedélyezi vagy letiltja a hibakeresési mód |IGAZ, hamis |
| **MS_TableSchema** |Data.Schema |SQL-táblák alapértelmezett séma neve |karakterlánc (alapértelmezett: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Engedélyezi vagy letiltja a hibakeresési mód |IGAZ, hamis |
| **MS_DisableVersionHeader** |verzió (a nem definiált beállítása) |Az X-ZUMO-Server-verzió fejlécet letiltása |IGAZ, hamis |
| **MS_SkipVersionCheck** |skipversioncheck |Letiltja az ügyfél API-verziójának ellenőrzése |IGAZ, hamis |

Alkalmazásbeállítás megadása:

1. Jelentkezzen be az [Azure Portal].
1. Válassza ki **összes erőforrás** vagy **App Services**, majd válassza a mobilalkalmazás neve.
1. A **beállítások** panelen alapértelmezés szerint megnyílik. Ha nem, és válassza az **beállítások**.
1. Az a **általános** menüjében válassza **Alkalmazásbeállítások**.
1. Görgessen a **Alkalmazásbeállítások** szakaszban.
1. Ha az alkalmazás, beállítás már létezik, válassza ki az érték szerkesztéséhez Alkalmazásbeállítás értékét.
   Ha az alkalmazás-beállítás nem létezik, adja meg az Alkalmazásbeállítás a **kulcs** mezőbe, és az értéket a **érték** mezőbe.
1. Kattintson a **Mentés** gombra.

A legtöbb alkalmazás beállításainak módosítása a szolgáltatás újraindítását igényli.

### <a name="howto-use-sqlazure"></a>Használata SQL Database-t az üzemi adatok tárolására.

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Azure SQL Database használatával adattárként megegyezik az Azure App Service-alkalmazás összes típusa. Ha Ön még nem tette már, kövesse az alábbi lépéseket egy Mobile Apps-háttéralkalmazás létrehozásához:

1. Jelentkezzen be az [Azure Portal].
1. Az ablak bal felső sarokban válassza ki a **+ új** gomb > **Web + mobil** > **mobilalkalmazás**, majd adja meg egy nevet a Mobile Apps-háttéralkalmazás számára.
1. Az a **erőforráscsoport** mezőbe írja be ugyanazzal a névvel, az alkalmazás.
1. Az alapértelmezett App Service-csomag van kiválasztva. Ha azt szeretné, az App Service-csomag módosítása:

   a. Válassza ki **App Service-csomag** > **+ új létrehozása**.

   b. Adjon meg egy nevet az új App Service-csomag, és válassza ki a megfelelő helyére.

   c. Válasszon egy megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **az összes megtekintése** több díjszabás a beállítások, például a **ingyenes** és **megosztott**.

   d. Kattintson a **kiválasztása** gombra.

   e. Térjen vissza a **App Service-csomag** ablaktáblán válassza előbb **OK**.
1. Kattintson a **Létrehozás** gombra.

Egy Mobile Apps-kiépítés háttérrendszer néhány percig is eltarthat. Miután a Mobile Apps biztonsági célból ki van építve, a portál megnyitja az **beállítások** panelen a Mobile Apps-háttéralkalmazás számára.

Választhat, vagy egy meglévő SQL database csatlakozhat a Mobile Apps-háttéralkalmazást, vagy hozzon létre egy új SQL-adatbázis. Ebben a szakaszban egy SQL-adatbázist hozunk létre.

> [!NOTE]
> Ha Ön már rendelkezik adatbázissal ugyanazon a helyen, a Mobile Apps-háttéralkalmazáshoz, ehelyett válassza **létező adatbázis használata** , és válassza ki, hogy az adatbázis. Az egy másik helyen található adatbázis használata nem ajánlott miatt nagyobb késleltetéssel jár.

1. Válassza ki az új Mobile Apps-háttéralkalmazást, **beállítások** > **mobilalkalmazás** > **adatok** > **+ Hozzáadás**.
1. Az a **adatkapcsolat hozzáadása** ablaktáblán válassza **SQL Database – kötelező beállítások konfigurálása** > **hozzon létre egy új adatbázist**. Adja meg az új adatbázis nevét a **neve** mezőbe.
1. Válassza ki **kiszolgáló**. Az a **új kiszolgáló** panelen adjon meg egy egyedi kiszolgálónevet a a **kiszolgálónév** mezőbe, majd adja meg a megfelelő kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát. Ügyeljen arra, hogy **azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** van kiválasztva. Kattintson az **OK** gombra.

   ![Azure SQL-adatbázis létrehozása][6]
1. Az a **új adatbázis** ablaktáblán válassza előbb **OK**.
1. Térjen vissza a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **kapcsolati karakterlánc**, és adja meg a bejelentkezési és az adatbázis létrehozásakor megadott jelszót. Ha egy meglévő adatbázist használ, adja meg a bejelentkezési hitelesítő adatokat, hogy az adatbázis. Kattintson az **OK** gombra.
1. Térjen vissza a **adatkapcsolat hozzáadása** panelt, válassza **OK** az adatbázis létrehozásához.

<!--- END OF ALTERNATE INCLUDE -->

Az adatbázis létrehozása néhány percet is igénybe vehet. Használja a **értesítések** területen nyomon követheti az üzembe helyezés. Halad mindaddig, amíg az adatbázis sikeres üzembe helyezését. Az adatbázis üzembe helyezését követően egy kapcsolati karakterláncot a Mobile Apps-háttéralkalmazás között az SQL Database-példányban jön létre. Megtekintheti ennek az alkalmazásbeállításnak a **beállítások** > **Alkalmazásbeállítások** > **kapcsolati karakterláncok**.

### <a name="howto-tables-auth"></a>Hitelesítés megkövetelése a hozzáféréshez táblákhoz

Ha azt szeretné, az App Service-hitelesítés használatára a `tables` végpont, konfigurálnia kell az App Service-hitelesítés a [Azure Portal] első. További információkért olvassa el az, hogy a használni kívánt identitásszolgáltató konfigurációs Útmutató:

* [Az Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [A Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Minden tábla-hozzáférés tulajdonságot, amely segítségével férhet hozzá a tábla rendelkezik. Az alábbi minta statikusan meghatározott táblázatát jeleníti meg, a hitelesítés szükséges.

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

A hozzáférés tulajdonság három érték egyikét hajthatja végre:

* *Névtelen* azt jelzi, hogy az ügyfélalkalmazás engedélyezett a hitelesítés nélkül adatokat olvasni.
* *hitelesített* azt jelzi, hogy az ügyfélalkalmazás el kell küldenie egy érvényes hitelesítési jogkivonatra a kérelemmel.
* *le van tiltva* azt jelzi, hogy ez a táblázat jelenleg le van tiltva.

Ha a hozzáférési tulajdonság nincs meghatározva, nem hitelesített hozzáférést engedélyezett.

### <a name="howto-tables-getidentity"></a>A táblák használata a hitelesítéshez jogcímeket
Beállíthat különböző jogcímeket kerül sor, amikor a hitelesítés be van állítva. Ezeket a jogcímeket nem használhatók általában a `context.user` objektum. Azonban, hogy le lehet kérdezni őket használatával a `context.user.getIdentity()` metódust. A `getIdentity()` metódus egy promise, amelyet egy objektumot ad vissza. Az objektum a hitelesítési módszer van ellátva (`facebook`, `google`, `twitter`, `microsoftaccount`, vagy `aad`).

Például ha beállította a Microsoft-fiók hitelesítési és az e-mail-címeket jogcím-kérelem, adhat hozzá az e-mail-cím a rekord a következő táblázat vezérlővel:

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

Tekintse meg, milyen jogcímeket érhetők el, egy webes böngésző segítségével megtekintheti a `/.auth/me` végpont a webhely.

### <a name="howto-tables-disabled"></a>Tiltsa le a hozzáférést az adott táblát műveleteire

Mellett jelenik meg a tábla, az access tulajdonság segítségével szabályozhatja az egyes műveletek. Nincsenek négy műveletet:

* `read` az a REST-alapú beolvasása művelet a táblán.
* `insert` az a REST-alapú POST művelet a táblán.
* `update` az a REST-alapú JAVÍTÁSI művelet a táblán.
* `delete` az a REST-alapú törlése művelet a táblán.

Például előfordulhat, hogy szeretne biztosítani a nem hitelesített írásvédett tábla:

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

### <a name="howto-tables-query"></a>A lekérdezést, table műveletek használt

Általános követelmény a table műveletek, hogy az adatok korlátozott nézete. Ha például egy táblát, amely a hitelesített felhasználói azonosítóval van megjelölve, úgy, hogy a csak olvasási vagy a saját rekordok frissítése biztosíthat. A következő tábla definícióját a funkciókat biztosítja:

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

Műveletek, amelyek normál esetben a lekérdezés futtatása rendelkezik használatával módosíthatja lekérdezési tulajdonságot egy `where` záradékban. A lekérdezés a tulajdonság egy [QueryJS] OData-lekérdezés úgy, hogy a háttéralkalmazás az adatok átalakítása használt objektumot tud feldolgozni. Olyan esetekben, egyszerű egyenlőség (például az előző egyet) a térkép is használhatja. Adott SQL záradékot is hozzáadhat:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Egy tábla a helyreállítható törlés konfigurálása

A helyreállítható törlés valójában nem töröl rekordokat. Ehelyett azt jelöli meg azokat az adatbázison belül a törölt oszlop igaz értékre állításával töröltként. A Mobile Apps SDK automatikusan eltávolítja helyreállíthatóan törölt rekordok eredményeket, kivéve, ha a Mobile ügyfél SDK-t használ `IncludeDeleted()`. Konfigurálja a táblát, a helyreállítható törlés, állítsa be a `softDelete` tulajdonság a tábla szolgáltatásdefiníciós fájlban:

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

Létre kell hoznia egy mechanizmust bejegyzések törlése: az ügyfélalkalmazások, a Webjobs, egy Azure-függvényt vagy egyéni API-t.

### <a name="howto-tables-seeding"></a>Rendezze az adatokat az adatbázisba

Egy új alkalmazást hoz létre, akkor érdemes rendezi az adatokat tartalmazó táblát. Ehhez a tábla definíciójának JavaScript fájlban a következő:

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

Az adatok beültetés történik, csak akkor, ha már használta a Mobile Apps SDK-t a tábla létrehozásához. Ha a tábla már létezik az adatbázisban, nincs adat szúrhatja be a táblába. A dinamikus sémák be van kapcsolva, ha a sémát a fióktól vette a beállításait az előkészített adatok.

Azt javasoljuk, hogy explicit módon hívja a `tables.initialize()` metódus a tábla létrehozásához, amikor a szolgáltatás elindul.

### <a name="Swagger"></a>A Swagger-támogatás engedélyezése
A Mobile Apps együttműködik a beépített [Swagger] támogatja. Swagger-támogatás engedélyezéséhez először telepítse a swagger-ui függőségei:

    npm install --save swagger-ui

Engedélyezheti a Mobile Apps konstruktor Swagger-támogatás:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Valószínűleg csak szeretné fejlesztési változatban Swagger-támogatás engedélyezése. Ezt megteheti használatával is a `NODE_ENV` Alkalmazásbeállítás:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

A `swagger` végpont a http://*sajatwebhely*.azurewebsites.net/swagger. A Swagger felhasználói felületén keresztül érheti el a `/swagger/ui` végpont. Ha a teljes alkalmazás közötti hitelesítést igényelnek, a Swagger hibát ad vissza. A legjobb eredmények érdekében válassza ki, hogy a nem hitelesített kérelmek az Azure App Service hitelesítés/engedélyezés között, és majd hitelesítés használatával szabályozhatja a `table.access` tulajdonság.

Is hozzáadhat a Swagger-beállítást a azureMobile.js fájl Ha kizárólag a Swagger-támogatás helyileg fejlesztéséhez.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Leküldéses értesítések küldése

Mobile Apps integrálható az Azure Notification hubs használatával, így célzott leküldéses értesítéseket küldhet eszközök millióira minden jelentősebb platformon. A Notification Hubs használatával leküldéses értesítéseket küldhet az iOS, Android és Windows eszközökhöz. A Notification hubs használatával elvégezhető az összes kapcsolatos további információkért tekintse meg a [Notification Hubs – áttekintés](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Leküldéses értesítések küldése

A következő kód bemutatja, hogyan használhatja a `push` objektum leküldéses értesítések küldéséhez regisztrált iOS-eszközök:

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

Az ügyfél hoz létre a sablon leküldéses regisztrálását, inkább elküldheti a sablon leküldéses üzenet eszközökre az összes támogatott platformon. A következő kód bemutatja, hogyan sablon értesítést küldeni:

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

### <a name="push-user"></a>Leküldéses értesítések küldése hitelesített felhasználók a címkék használatával
Ha egy hitelesített felhasználó regisztrálja a leküldéses értesítésekhez, felhasználói azonosító címkét automatikusan hozzáadódik a regisztráció. Ez a címke használatával küldhet leküldéses értesítéseket egy adott felhasználó által regisztrált összes eszközt. A következő kód lekéri a kérés küldője, és minden eszköz regisztrálása a sablon leküldéses értesítést küld az adott felhasználó felhasználó biztonsági azonosítója:

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

Regisztrálásakor, hogy a leküldéses értesítéseket állíthat be egy hitelesített ügyfél a győződjön meg arról, hogy a hitelesítés befejeződik, mielőtt elkezdené a regisztrációs.

## <a name="CustomAPI"></a> Egyéni API-k

### <a name="howto-customapi-basic"></a>Egyéni API definiálása

Az adatelérési API-n keresztül mellett a `/tables` Mobile Apps-végpont egyéni API lefedettség tud biztosítani. Egyéni API-k a tábladefiníciókat hasonló módon definiált, és hozzáférhetnek ugyanazokat létesítményekben, beleértve a hitelesítést.

Ha szeretné használni az App Service-hitelesítés egy egyéni API-val, konfigurálnia kell az App Service-hitelesítés a [Azure Portal] első. További információkért olvassa el az, hogy a használni kívánt identitásszolgáltató konfigurációs Útmutató:

* [Az Azure Active Directory-hitelesítés konfigurálása]
* [Facebook-hitelesítés konfigurálása]
* [Google-hitelesítés konfigurálása]
* [A Microsoft-hitelesítés konfigurálása]
* [Twitter-hitelesítés konfigurálása]

Egyéni API-k a tábla API-ként ugyanolyan módon vannak meghatározva:

1. Hozzon létre egy `api` könyvtár.
1. Hozzon létre egy API-definíciós JavaScript a fájlt a `api` könyvtár.
1. Importálási módszer használatával importálhatja a `api` könyvtár.

Íme a korábban használt alap-alkalmazást a mintán alapuló API-definíció prototípus:

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

Vegyünk egy példát, API, amely segítségével a kiszolgáló dátumot adja vissza a `Date.now()` metódust. Íme a api/date.js fájlt:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Egyes paramétereket az alábbiak egyike a szabványos REST-alapú HTTP-parancsokat: ELSŐ, közzététel, javítása vagy törlése. A metódus egy olyan szabvány [ExpressJS közbenső szoftver] -függvény, amely a szükséges kimenetet küld.

### <a name="howto-customapi-auth"></a>Hitelesítés megkövetelése a hozzáféréshez egy egyéni API-hoz

A Mobile Apps SDK-t valósít meg hitelesítési ugyanúgy mind a `tables` végpont és az egyéni API-k. Hitelesítés hozzáadása az API-t az előző szakaszban fejlesztett, adjon hozzá egy `access` tulajdonság:

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

Hitelesítés a meghatározott műveleteket is megadhat:

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

Ugyanezt a tokent szolgálja ki a `tables` végpontot az egyéni API-k hitelesítést igénylő kell használnia.

### <a name="howto-customapi-auth"></a>Kezeli a nagy méretű fájlok feltöltése

A Mobile Apps SDK-t használ a [body-parser közbenső](https://github.com/expressjs/body-parser) elfogadásához és a törzs tartalma a beküldött dekódolása. Fogadja el a nagyobb méretű fájlt tölt fel a törzselemzőt előre is megadhatja:

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

A fájl nem base-64 kódolású átvitele előtt. Ehhez a kódoláshoz a tényleges feltöltés (és a méretét, figyelembe kell venni) méretét is megnöveli.

### <a name="howto-customapi-sql"></a>Egyéni SQL-utasítások végrehajtása

A Mobile Apps SDK lehetővé teszi a hozzáférést a teljes környezetet a kérelem objektumon keresztül. Egyszerűen a meghatározott adatszolgáltató paraméteres SQL-utasításokkal hajthat végre:

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

## <a name="Debugging"></a>Hibakeresés, könnyen kezelhető táblák és egyszerű API-k

### <a name="howto-diagnostic-logs"></a>Hibakeresés, diagnosztizálása és hibaelhárítása a Mobile Apps

Az Azure App Service számos hibakeresési és hibaelhárítási módszerekről Node.js-alkalmazásokhoz biztosít.
Ismerkedés a Node.js a Mobile Apps-háttéralkalmazást a hibaelhárítás, tekintse meg a következő cikkeket:

* [Az Azure App Service figyelése]
* [Az Azure App Service-diagnosztikai naplózás engedélyezése]
* [Az Azure App Service a Visual Studio alkalmazásban hibaelhárításához]

NODE.js-alkalmazások férhetnek hozzá a diagnosztikai naplót eszközök széles. Belsőleg, a Mobile Apps Node.js SDK-t használ [Winston] diagnosztikai naplózás. Naplózási automatikusan aktiválódik, amikor engedélyezi a hibakeresési mód vagy a set a `MS_DebugMode` Alkalmazásbeállítás true-ra a [Azure Portal]. Generált naplók jelennek meg a diagnosztikai naplókat a [Azure Portal].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Könnyen kezelhető táblák használata az Azure Portalon

Könnyen kezelhető táblák használatával létrehozhat és táblák közvetlenül a portálon kezelheti. A könnyen kezelhető táblák adatkészlet tölthet fel a CSV formátumban. Vegye figyelembe, hogy nem használhat a tulajdonságnevek (adatkészletben a fürt megosztott kötetei szolgáltatás), amely ütközik a Mobile Apps-háttéralkalmazás rendszer tulajdonság nevére. A rendszer nevei a következők:
* createdAt
* updatedAt
* törölve
* version

Az App Service Editor table műveletek is szerkesztheti. Ha bejelöli **könnyen kezelhető táblák** a háttér-webhely között hozzáadása, módosítása és törlése egy táblát. A tábla adatait is megtekintheti.

![Könnyen kezelhető táblák használata](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

A következő parancsokat egy táblát a parancssáv érhetők el:

* **Engedélyek módosítása**: Az olvasási engedélyekkel, beszúrása, frissítése és törlési műveletek a táblán.
 Is, hogy engedélyezze a névtelen hozzáférést, a hitelesítést igényel, vagy a művelet való hozzáférés teljes letiltásához.
* **Parancsfájl szerkesztése**: A parancsfájl a táblához tartozó App Service Editor van nyitva.
* **Séma kezelése**: Adja hozzá az oszlopok törlése, vagy módosítani a tábla indexe.
* **Tábla tartalmának törlése**: Meglévő tábla csonkolása törlésével az összes adatsor, de a séma változatlanul.
* **Sorok törlése**: Az adatok az egyes sorok törlése.
* **Folyamatos átviteli naplók megtekintése**: Csatlakozzon a streamelési naplózási szolgáltatás webhelye számára.

### <a name="work-easy-apis"></a>Egyszerű API-k használata az Azure Portalon

Egyszerű API-k segítségével egyéni API-k közvetlenül a portálon a létrehozását és használatát. API-parancsprogramokat az App Service Editor használatával szerkesztheti.

Ha bejelöli **egyszerű API-k** a háttér-webhely között hozzáadása, módosítása vagy egy egyéni API-végpont törlése.

![Egyszerű API-khoz](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

A portálon egy HTTP-művelet hozzáférési engedélyeinek módosítása, az API-parancsfájlt az App Service Editor szerkesztése vagy a folyamatos átviteli naplók megtekintéséhez.

### <a name="online-editor"></a>Az App Service Editor kód szerkesztése

Az Azure Portalon, a Node.js háttér-parancsfájlok az App Service Editor szerkesztheti a projekt letöltése a helyi számítógépen nélkül. Az online szerkesztőjében parancsfájlok szerkesztése:

1. A ablaktábla számára a Mobile Apps-háttéralkalmazáshoz, válassza a **minden beállítás** > vagy **könnyen kezelhető táblák** vagy **egyszerű API-k**. Egy tábla vagy API-t, majd válassza ki és **parancsfájl szerkesztése**. A parancsfájl az App Service Editor nyílik meg.

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. A módosítások a kódfájl a online szerkesztőjében. Módosítások mentése automatikusan történik, akkor írja be.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android ügyfél a rövid útmutató]: app-service-mobile-android-get-started.md
[Apache Cordova-ügyfél gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[iOS-es ügyfél a rövid útmutató]: app-service-mobile-ios-get-started.md
[Xamarin.iOS-ügyfél a rövid útmutató]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android-ügyfél a rövid útmutató]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-ügyfél a rövid útmutató]: app-service-mobile-xamarin-forms-get-started.md
[Windows Store-ügyfél a rövid útmutató]: app-service-mobile-windows-store-dotnet-get-started.md
[offline adatszinkronizálást]: app-service-mobile-offline-data-sync.md
[Az Azure Active Directory-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-aad.md
[Facebook-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-facebook.md
[Google-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-google.md
[A Microsoft-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter-hitelesítés konfigurálása]: ../app-service/configure-authentication-provider-twitter.md
[Az Azure App Service üzembe helyezési útmutató]: ../app-service/app-service-deploy-local-git.md
[Az Azure App Service figyelése]: ../app-service/web-sites-monitor.md
[Az Azure App Service-diagnosztikai naplózás engedélyezése]: ../app-service/web-sites-enable-diagnostic-log.md
[Az Azure App Service a Visual Studio alkalmazásban hibaelhárításához]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[Adja meg a csomópont verziója]: ../nodejs-specify-node-version-azure-apps.md
[Node-modulok használata]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Megtartva]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp minta a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[Teendőlista-minta a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[minták könyvtárát a Githubon]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[1.1 node.js-eszközök a Visual Studióhoz]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node.js-csomag]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS közbenső szoftver]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
