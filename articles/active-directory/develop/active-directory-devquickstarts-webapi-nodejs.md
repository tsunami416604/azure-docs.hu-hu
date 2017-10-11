---
title: "Ismerkedés az Azure AD Node.js |} Microsoft Docs"
description: "Hogyan hozhat létre a többi Node.js webes API-k, amely az Azure AD használatára a hitelesítéshez."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 4f58177f540c14172d7ece8b4bc8c8a2b9787f8f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-web-apis-for-nodejs"></a>A Node.js webes API-knak az első lépései
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

A *Passport* a Node.js-hez készült közbenső hitelesítési szoftver. Rugalmas és, moduláris Passport diszkréten eldobja bármely Express-alapú vagy Restify webes alkalmazást. Stratégiák széles választékát támogatja a hitelesítés és az felhasználónév és jelszó, Facebook, Twitter, és több. Kidolgoztunk is stratégiát a Microsoft Azure Active Directory (Azure AD). Azt telepítenie kell a modult, és vegye fel a Microsoft Azure Active Directory `passport-azure-ad` beépülő modult.

Ehhez a következőket kell tennie:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Az alkalmazás beállítása a Passport által használandó `passport-azure-ad` beépülő modult.
3. A teendőlista webes API hívásához ügyfélalkalmazás konfigurálása.

Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> A cikk nem tér ki, megvalósítható bejelentkezési, regisztrációs vagy Azure AD B2C profilok kezelése. A cikk foglalkozik hívó Web API-kat a felhasználó már hitelesítése után.  Azt javasoljuk, hogy a kiindulási pont [integrálása az Azure Active Directory-dokumentum](active-directory-how-to-integrate.md) az Azure Active Directory alapjainak megismeréséhez.
>
>

Azt is, amely a futó példa github MIT licenccel a forráskód, így nyugodtan klón (vagy még jobban elágazás), és visszajelzést és lekéréses kérelmek.

## <a name="about-nodejs-modules"></a>A Node.js modulok kapcsolatos
Ebben a bemutatóban a Node.js modulok használjuk. A modulokra betölthető JavaScript-csomagok, amelyek bizonyos funkciók biztosítanak az alkalmazáshoz. Általában telepítése modult az NPM telepítési könyvtárában a Node.js egy NPM parancssori eszköz használatával. Azonban néhány modulok, például a HTTP-modulja az alapvető Node.js csomagban található.

Telepített modulok lesznek mentve a **node_modules** könyvtárhoz, a Node.js telepítőkönyvtár gyökerében. Minden modulja a **node_modules** directory kezeli a saját **node_modules** , amelyektől függ modulokat tartalmazó könyvtárat. Emellett minden egyes szükséges a modulnak a **node_modules** könyvtár. A rekurzív könyvtárstruktúrát a függőségi láncból jelöli.

Ez a függőségi lánc struktúra egy nagyobb alkalmazás erőforrásigényét eredményez. De azt is biztosítja, hogy az összes függősége teljesítésének és szolgál, hogy a modulok szolgál a fejlesztői verzióját is éles. Ez sokkal kiszámíthatóbbá teszi a termelési alkalmazása működését, és megakadályozza, hogy a versioning problémák, amelyek hatással lehetnek a felhasználók.

## <a name="step-1-register-an-azure-ad-tenant"></a>1. lépés: Az Azure AD-bérlő regisztrálása
Ez a minta használatához szüksége van az Azure Active Directory-bérlő. Ha még nem meg arról, hogy milyen a bérlő vagy az beszerzése, lásd: [az Azure AD-bérlő beszerzése](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>2. lépés: Az alkalmazás létrehozása
Ezután hoz létre egy alkalmazást a címtárában, amely biztosítja, hogy az alkalmazás biztonságos kommunikációhoz szükséges információkat az Azure AD.  Az ügyfélalkalmazást és a webes API-t egyetlen jelölik **Alkalmazásazonosító** ebben az esetben, mert alkalmazássá logikai.  Az alkalmazást a következő [utasítások](active-directory-how-applications-are-added.md) alapján hozza létre. A sor üzleti alkalmazás készítésekor [hasznosak lehetnek a további utasítások](../active-directory-applications-guiding-developers-for-lob-applications.md).

Alkalmazás létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A felső menüben válassza ki a fiókját. Ekkor a a **Directory** menüben válassza ki, hol szeretné az alkalmazás regisztrálása az Active Directory-bérlő.

3. A bal oldali menüben válasszon ki **több szolgáltatások**, majd válassza ki **Azure Active Directory**.

4. Válassza ki **App regisztrációk**, majd válassza ki **Hozzáadás**.

5. Kövesse a megjelenő utasításokat hozzon létre egy **webalkalmazás és/vagy WebAPI**.

      * A **neve** , az alkalmazás írja le az alkalmazást a végfelhasználók számára.

      * A **bejelentkezési URL-cím** az alkalmazás alap URL-címe.  Az alapértelmezett URL-címe mintakódot `https://localhost:8080`.

6. Miután regisztrálta, az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás oldalról.

7. Az a **beállítások** -> **tulajdonságok** az alkalmazás lapján frissítse a App ID URI. A **App ID URI** az alkalmazás egyedi azonosítója. Az egyezmény használandó `https://<tenant-domain>/<app-name>`, például: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Hozzon létre egy **kulcs** az alkalmazás a **beállítások** lapon, majd másolja ki valahová. Szüksége lehet rájuk hamarosan.

## <a name="step-3-download-nodejs-for-your-platform"></a>3. lépés: A Node.js letöltése a platformra
A minta használatához rendelkeznie kell a Node.js telepített és működő verziójával.

Telepítse a Node.js-t [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>4. lépés: Telepítse MongoDB a platformon
Ez a minta használatához a MongoDB telepített és működő kell rendelkeznie. A mongodb a REST API különböző kiszolgálópéldányok közötti állandó ellenőrizze.

Telepítse a mongodb-t a [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Ez a forgatókönyv azt feltételezi, hogy kell használni az alapértelmezett telepítését és kiszolgálóvégpontjait a MongoDB, amely írásának időpontjában mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>5. lépés: A Restify-modulok telepítése a webes API
Restify hozhat létre REST API-n használjuk. A restify egy minimális igényű és rugalmas Node.js alkalmazási keretrendszer származó Express van. Számos hatékony funkciót kínál a Connectre épített REST API-k létrehozásához.

### <a name="install-restify"></a>A Restify telepítése
1. A parancssorban lépjen a **azuread** könyvtár. Ha a **azuread** könyvtár nem létezik, hozza létre.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Írja be a következő parancsot:

    `npm install restify`

    Ez a parancs elvégzi a Restify telepítését.

#### <a name="did-you-get-an-error"></a>Hibaüzenetet kapott?
Egyes operációs rendszerek NPM használata esetén előfordulhat, hogy hibaüzenet, amely szerint **hiba: EPERM, chmod "/ usr/helyi/bin /..."** és rendszergazdaként futtatja a figyelembe próbálja javaslatot. Ha ez történik, a sudo utasítás használatával NPM futtassa magasabb jogosultsági szinten.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Vonatkozó DTRACE hibaüzenetet kapott?
Ilyen hiba láthatja a Restify telepítése során:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```
A Restify a DTrace segítségével biztosít a REST-hívások követésére szolgáló funkciókat. Számos operációs rendszer azonban nem rendelkezik DTrace. Ezeket a hibákat nyugodtan figyelmen kívül hagyhatja.

Ez a parancs kimenete a következő kimeneti hasonlóan kell kinéznie:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>6. lépés: Telepítse Passport.js a webes API
A [Passport](http://passportjs.org/) a Node.js-hez készült közbenső hitelesítési szoftver. Rugalmas és, moduláris Passport diszkréten eldobja bármely Express-alapú vagy Restify webes alkalmazást. Stratégiák széles választékát támogatja a hitelesítés és az felhasználónév és jelszó, Facebook, Twitter, és több.

Az Azure Active Directory kidolgoztunk is stratégiát. Azt telepítenie kell a modult, és adja hozzá az Azure Active Directory stratégiai bővítményt.

1. A parancssorban lépjen a **azuread** könyvtár.

2. Passport.js telepítéséhez adja meg a következő parancsot:

    `npm install passport`

    A parancs a következőhöz hasonlóan kell kinéznie:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>7. lépés: A Passport-Azure-AD hozzá a web API
Ezután azt hozzá az OAuth stratégiát `passport-azure-ad`, a stratégiacsomag szolgál az Azure Active Directory Passport-hez. Ez a stratégia használható a tulajdonosi jogkivonatokhoz a REST API minta használjuk.

> [!NOTE]
> Bár az OAuth2 keretrendszerében, amelyben bármely ismert jogkivonat típus kibocsáthatja, csak bizonyos tokentípusokat gyakran használják. Tulajdonosi jogkivonatok a leggyakrabban használt jogkivonatokat a végpontok védelmére. A leggyakrabban kibocsátott típusú lexikális elem szerepel az OAuth2. Számos implementáció eleve feltételezik, hogy csak az ilyen típusú kiadott jogkivonatokat, tulajdonosi jogkivonatoknak nevezzük.
>
>

A parancssorban lépjen a **azuread** könyvtár.

Telepítéséhez írja be a következő parancsot a Passport.js `passport-azure-ad module`:

`npm install passport-azure-ad`

A parancs a következő kimeneti hasonlóan kell kinéznie:


    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>8. lépés: A MongoDB-modulok hozzáadása a webes API-hoz
Az adattároló azt a mongodb. Ezért azt telepítenie kell a széles körben használt beépülő modul hívott Mongoose modellek és sémák kezelésére. Azt is telepítenie kell az adatbázis-illesztőprogramját mongodb-protokolltámogatással (amelyet a MongoDB is neveznek).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>9. lépés: További modulok telepítése
Ezután a további szükséges modulokat telepítésére.

1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

    `cd azuread`

2. Adja meg a következő parancsok futtatásával telepítse ezeket a modulokat a **node_modules** könyvtár:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>10. lépés: Hozzon létre egy server.js a függőségek
A server.js fájl legtöbb funkciója biztosítja a web API-kiszolgálóhoz. Jelenleg felvenni található kód jelentős részét ehhez a fájlhoz. Élesben való használat azt javasoljuk, hogy refactor-e a kisebb fájlok, például különálló útvonalak és vezérlők funkciókat. Ebben a bemutatóban a server.js használjuk funkcióhoz.

1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

    `cd azuread`

2. Hozzon létre egy `server.js` a kedvenc szerkesztő fájlt, és adja meg a következő információkat:

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Mentse a fájlt. Még visszatérünk rá hamarosan.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>11. lépés: Az Azure AD-beállítások tárolására konfigurációs fájl létrehozása
Ez a kódfájl adja át a konfigurációs paraméterek az Azure Active Directory portálon Passport.js. A webes API-t a portálhoz, a forgatókönyv első része az hozzáadásakor létrehozta ezeket a konfigurációs értékeket. A paraméterek kitöltésének módját a kód másolását követően ismertetjük.

1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

    `cd azuread`

2. Hozzon létre egy `config.js` a kedvenc szerkesztő fájlt, és adja meg a következő információkat:

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Mentse a fájlt.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>12. lépés: A konfigurációs értékek hozzáadása a server.js fájlhoz
Igazolnia kell olvasni ezeket az értékeket az alkalmazás között létrehozott .config kiterjesztésű fájl. Ehhez az szükséges, jelenleg felvenni a .config fájl kötelező erőforrásként az alkalmazásban. Majd hivatott meg kell egyeznie a változók a config.js dokumentumban a globális változókat.

1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

    `cd azuread`

2. Nyissa meg a `server.js` a kedvenc szerkesztő fájlt, és adja meg a következő információkat:

    ```Javascript
    var config = require('./config');
    ```
3. Adja meg az új szakasz `server.js` az alábbi kódra:

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Mentse a fájlt.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>13. lépés: A MongoDB-modellre és -sémára információk hozzáadása a Mongoose segítségével
Ez a felkészítés érintetlen most fizető, mivel ezek a fájlok egyesítése azt REST API-szolgáltatás elindításához.

Jelen kalauz használatához a MongoDB a feladatok tárolására, lásd a 4. lépés használjuk.

Az a `config.js` fájlt, hogy a 11. lépésben létrehozott, az adatbázis hívtuk `tasklist`, mert, hogy mi azt put végén a **mogoose_auth_local** kapcsolat URL-címet. Ezt az adatbázist nem szükséges előre létrehozni a MongoDB-ben. Ehelyett MongoDB hoz létre ez az USA első alkalommal történő futtatásakor a kiszolgálói alkalmazás (feltéve, hogy az adatbázis nem létezik).

Most, hogy megadta, hogy rendelkezik a kiszolgáló melyik MongoDB-adatbázist kell használni, azt kell írnia a tartozó modell és séma a kiszolgálói feladatok létrehozásához további kódot.

### <a name="discussion-of-the-model"></a>Az ismertető a modell
A sémamodell felettébb egyszerű. Szükség szerint bontsa ki.

: A név a feladathoz rendelt személy. A **karakterlánc**.

FELADAT: A feladat magát. A **karakterlánc**.

DÁTUM: Az a dátum, a feladat miatt. A **DATETIME**.

BEFEJEZŐDÖTT: Ha a feladat befejeződött-e. A **LOGIKAI**.

### <a name="creating-the-schema-in-the-code"></a>A kódban a séma létrehozása
1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

    `cd azuread`

2. Nyissa meg a `server.js` a kedvenc szerkesztő fájlt, és adja meg a következő adatokat a konfigurációs bejegyzés alatt:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
    var TaskSchema = new Schema({
        owner: String,
        task: String,
        completed: Boolean,
        date: Date
    });

    // Use the schema to register a model.
    mongoose.model('Task', TaskSchema);
    var Task = mongoose.model('Task');
    ```
Megadható, hogy a kód, mivel azt először hozza létre a séma. Ezután a modellobjektumot, amelyet az adatok a kód egészében tárolja, amikor meghatároztuk használatával hoz létre, azt a **útvonalak**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>14. lépés: A feladat REST API-kiszolgálóhoz az mutató útvonalak hozzáadása
Most, hogy egy adatbázismodell együttműködni, adjuk hozzá a REST API-kiszolgálóhoz használni fogjuk útvonalak.

### <a name="about-routes-in-restify"></a>Az útvonalak működése a Restify programban
Útvonalak az Express verem az azonos módon működnek működése a Restify programban. Az útvonalakat azon URI használatával kell meghatározni, amelyet elképzelései szerint az ügyfélalkalmazások meg fognak hívni. Az útvonalakat általában külön fájlban definiálni. A célokra azt helyezze el az útvonalakat a server.js fájlt. Azt javasoljuk, hogy a saját üzemi használatra fájlba figyelembe ezeket az útvonalakat.

A Restify-útvonal egy tipikus mintája a következőképpen történik:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Ez a minta legalapvetőbb megjelenése. A restify (és az Express) sokkal összetettebb funkciók, például velük az alkalmazástípusokat és a különböző végpontok között összetett útválasztási adja meg. A célból hogy megakadályozzák ezeket az útvonalakat egyszerű.

### <a name="add-default-routes-to-our-server"></a>A kiszolgáló alapértelmezett útvonalak hozzáadása
A Microsoft most hozzáadása az alapszintű CRUD-útvonalakat létrehozása, beolvasása, frissítése és törlése.

1. A parancssorban lépjen a **azuread** mappát, ha már nem létezik:

    `cd azuread`

2. Nyissa meg a `server.js` a kedvenc szerkesztőben fájlt, és adja meg a következő információkat végzett korábbi adatbázis-bejegyzések alá:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name.

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks.

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name.

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="add-error-handling-in-our-apis"></a>Az API-Jainkkal a hibakezelés
```

///--- Errors for communicating something interesting back to the client.

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="step-15-create-your-server"></a>15. lépés: A kiszolgáló létrehozása
Az adatbázis definiáltuk, és az útvonalak legyenek érvényben. Az utolsó lépés, adja hozzá a hívásokat kezelő kiszolgálópéldányt.

A Restify (és az Express) teheti a nagy mennyiségű testreszabási egy REST API-kiszolgálóhoz, de ismét fogjuk használni a legalapvetőbb beállítása a célokra.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>16. lépés: Az útvonalak hozzáadása a kiszolgálóhoz (hitelesítés nélkül a lépést)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler.
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>17. lépés: A kiszolgáló futtatása (előtt OAuth-támogatás hozzáadása)
Ahhoz, hogy fel hitelesítési tesztelje a kiszolgálót.

A legegyszerűbben úgy lehet ellenőrizni a kiszolgáló van, a parancssorban curl használatával. Nézzük meg, igazolnia kell egy segédprogram, amely lehetővé teszi, hogy a kimeneti adatok JSON-ként értelmezni.

1. Telepítse a következő JSON-segédprogramot, (az alábbi példák ezzel az eszközzel):

    `$npm install -g jsontool`

    Ezzel globálisan telepíti a JSON-segédprogramot. Most, hogy azt már valósítható meg, amely, most lejátszása a kiszolgálóval:

2. Először is győződjön meg arról, hogy fut-e a mongoDB-példány:

    `$sudo mongod`

3. Ezután nyissa meg azt a könyvtárat, és indítsa el a sütővasak:

    `$ cd azuread` `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4. Ezt követően hozzá lehessen adni a feladat ily módon:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    A következő választ kell kapnia:

        ```Shell
        HTTP/1.1 201 Created
        Connection: close
        Access-Control-Allow-Origin: *
        Access-Control-Allow-Headers: X-Requested-With
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5
        Date: Tue, 04 Feb 2014 01:02:26 GMT
        Hello
        ```
    És megjelenő feladatok Brandon az ilyen módon:

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Ez akkor működik, ha még hozzáadhatja az OAuth a REST API-kiszolgálóhoz.

MongoDB-vel futó REST API-kiszolgáló rendelkezik!

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>18. lépés: Hitelesítés hozzáadása a REST API-kiszolgálóhoz
Most, hogy a futó REST API-t, először az Azure ad-val hasznos információkkal.

A parancssorban lépjen a **azuread** mappát, ha már nem létezik.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>A passport-azure-ad részét képező OIDCBearerStrategy használata
Mi építettünk, amennyiben egy tipikus REST TODO kiszolgálót hitelesítés nélkül. Ez azért, ha először üzembe, amelyek.

1. Először azt kell jelezheti, hogy kívánja-e használni a Passport. Ez a jogosultság helyezze a másik kiszolgáló konfigurációja után:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > API-k írásakor azt javasoljuk, hogy Ön mindig kapcsolja az adatokat egy egyedi, amely a felhasználói nem tudnak jogosulatlanul hozzáférni a jogkivonatból. Ha a kiszolgáló tárolja a TODO elemeket tárolja őket (a token.OID), a jogkivonatot, amely a "tulajdonos" mező be azt a felhasználó objektum azonosítója alapján. Ez biztosítja, hogy csak adott felhasználó hozzáférhet-e a TODOs. Nincs nem jelenik meg sehol a "tulajdonos" API-ban, a külső felhasználó kérhet mások TODOs, még akkor is, ha hitelesített.                    

2. Következő most használja az rendelkező részét képező tulajdonosi stratégiát `passport-azure-ad`. Most tekintse meg a kódot, és a többi azt ismertetjük, hamarosan. Be az alábbiakat a fentiekben beillesztett:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
    **/

    var findById = function(id, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            if (user.sub === id) {
                log.info('Found user: ', user);
                return fn(null, user);
            }
        }
        return fn(null, null);
    };


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

A Passport az összes stratégia (Twitter-, Facebook-on, és így tovább), amely stratégiafejlesztő a hasonló mintát alkalmaz. Megnézzük a stratégiát, láthatja azt adja át, amely rendelkezik a jogkivonatot, és kész paraméterek függvényt. A stratégia ismét elérhető lesz a számunkra után a tevékenységeket végez el. Miután Igen, azt tárolni a felhasználót, és menteni a jogkivonatot, így azt nem kell megismételni.

> [!IMPORTANT]
> Az előző kód készít minden olyan felhasználó, hogy a kiszolgáló hitelesítésére történik. Ez az úgynevezett automatikus regisztráció. Az üzemi kiszolgálók, azt javasoljuk, ne engedélyezze, hogy bárki, aki nélkül őket keresse meg, amelyeket a regisztrációs folyamatot. Ez általában az a megoldást látjuk a fogyasztói alkalmazásoknál, melyek lehetővé teszik regisztrálni a Facebook, de majd kérje meg, hogy további adatokat. Ha a cserét nem parancssori program, kinyerhettük volna az e-mailt jogkivonat-objektumból, amely adott vissza, és felkérhettük volna a felhasználót, hogy további adatokat. Mivel a kiszolgáló csak ellenőrzési célokat szolgál, most egyszerűen hozzáadjuk őket a memóriában lévő adatbázishoz.
>
>

### <a name="protect-some-endpoints"></a>Egyes végpontok védelme
Védelmet biztosít a végpontoknak megadásával a `passport.authenticate()` hívja meg a használni kívánt protokollt.

Szeretné, hogy a kiszolgálóoldali kódban valami ennél is érdekesebb megoldást, a most Szerkesztés az útvonal.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19. lépés: Futtassa újra a kiszolgálóalkalmazás, és győződjön meg arról, hogy elutasítja
Most használja `curl` ismételt használatával ellenőrizheti, ha már tudunk OAuth2-védelem a végpontok ellen. Ez a vizsgálat előtt futtató az ügyfél-SDK-kat a végponton végezzük. A visszakapott fejlécek alapján kell lennie ahhoz, hogy adja meg, ha az oktatóanyagban módosítjuk jó úton jár le.

1. Először is győződjön meg arról, hogy fut-e a mongoDB-példány:

    `$sudo mongod`

2. Ezután nyissa meg azt a könyvtárat, és indítsa el a sütővasak.

      `$ cd azuread` `$ node server.js`

3. Próbálja meg egy egyszerű POST MŰVELETTEL.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

A 401-es értéke a keresett Itt választ. Ez a válasz azt jelzi, hogy a Passport réteg megpróbálja átirányítani a engedélyezett végpontot, amely pontosan mit szeretne.

## <a name="next-steps"></a>Következő lépések
Ön már megtettünk mindent, ami az OAuth2-kompatibilis ügyfél használata nélkül is ezen a kiszolgálón. Szüksége lesz egy további forgatókönyv keresztül halad.

Most már tudja, hogyan egy REST API Restify és OAuth2 segítségével megvalósítható. Lehetősége van több mint elég tartani a szolgáltatás fejlesztés, és hogyan hozhat létre ebben a példában a kódot is.

Ha érdekli a következő lépéseket a a ADAL használatában, az alábbiakban néhány azt javasoljuk, hogy tovább dolgozik a támogatott ADAL ügyfelek.

Klónozza a fejlesztői gépen le, és konfigurálja a bemutatóban leírtak szerint.

[Az IOS-es ADAL](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL androidhoz](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
