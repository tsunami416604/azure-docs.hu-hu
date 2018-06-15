---
title: Egy Azure Active Directory v2.0 webes API biztonságossá tétele a Node.js használatával |} Microsoft Docs
description: Megtudhatja, hogyan build egy Node.js webes API-t, amely fogadja a jogkivonatokat, a személyes Microsoft-fiók pedig a munkahelyi vagy iskolai fiókok.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19ad25c7b08ff073097cacf3be359772ca0a327f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158370"
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Biztonságos webes API-k számára a Node.js segítségével
> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók használata a v2.0-végponttól. Annak megállapításához, hogy használja a v2.0-végpontra vagy az 1.0-s verziójú végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

Az Azure Active Directory (Azure AD) v2.0-végponttól használata esetén használhatja [OAuth 2.0](active-directory-v2-protocols.md) hozzáférési jogkivonatok védelme érdekében a webes API-t. OAuth 2.0 hozzáférési jogkivonatok, felhasználók, akik egy személyes Microsoft-fiók és a munkahelyi vagy iskolai fiókok biztonságosan elérhet a webes API-t.

A *Passport* a Node.js-hez készült közbenső hitelesítési szoftver. Rugalmas és, moduláris Passport diszkréten eldobása minden Express-alapú vagy restify webalkalmazás. Passport stratégiák széles választékát támogatja a hitelesítést egy felhasználónév és jelszó, Facebook-on, Twitter vagy egyéb beállítások használatával. Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure AD esetében is felhasználható. Ebben a cikkben azt mutatja be a modul telepítése, és adja hozzá a az Azure AD `passport-azure-ad` beépülő modult.

## <a name="download"></a>Letöltés
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Az oktatóanyag ismerteti, hogy is [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip), vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Ez az oktatóanyag végén az elkészült alkalmazás is beszerezheti.

## <a name="1-register-an-app"></a>1: az alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy hajtsa végre a [a részletes lépéseket](active-directory-v2-app-registration.md) regisztrálnia az alkalmazást. Ellenőrizze, hogy:

* Másolás a **alkalmazásazonosító** az alkalmazáshoz hozzárendelni. Szüksége lesz az ehhez az oktatóanyaghoz.
* Adja hozzá a **Mobile** platform az alkalmazásra vonatkozóan.
* Másolás a **átirányítási URI-** a portálról. Az alapértelmezett URI értéket kell használnia `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: telepítse a Node.js-
Ebben az oktatóanyagban a minta használatához le kell [telepítse a Node.js-](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: a MongoDB telepítése
Ez a minta használatához le kell [a MongoDB telepítése](http://www.mongodb.org). Ez a példa azt mongodb-re a REST API állandó különböző kiszolgálópéldányok közötti.

> [!NOTE]
> Ez a cikk feltételezzük, hogy az alapértelmezett telepítését és kiszolgálóvégpontjait használja MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: a restify-modulok telepítése a webes API
Resitfy hozhat létre REST API-n használjuk. A restify egy minimális igényű és rugalmas Node.js alkalmazási keretrendszer származó Express van. A restify tartozik egy hatékony funkciókat, amelyek hozhat létre REST API-k létrehozásához.

### <a name="install-restify"></a>A restify telepítése
1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

    Ha a **azuread** könyvtár nem létezik, hozza létre:

    `mkdir azuread`

2.  A restify telepítése:

    `npm install restify`

    Ez a parancs kimenetében kell kinéznie:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Hibaüzenetet kapott?
Egyes operációs rendszerek használatakor a `npm` parancsban, láthatja, hogy ez az üzenet: `Error: EPERM, chmod '/usr/local/bin/..'`. A hiba, akkor próbálja meg a rendszergazdaként futtatja a figyelembe kérelmet követi. Ha ez történik, a paranccsal `sudo` futtatásához `npm` magasabb jogosultsági szinten.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Vonatkozó DTrace hibaüzenetet kapott?
Amikor telepíti a restify, láthatja, hogy ez az üzenet:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

A restify egy rendkívül erős eszköz többi meghívja a DTrace segítségével nyomkövetési rendelkezik. DTrace azonban számos operációs rendszeren nem érhető el. Ez a hibaüzenet nyugodtan figyelmen kívül hagyhatja.


## <a name="5-install-passportjs-in-your-web-api"></a>5: telepítés Passport.js a webes API
1.  A parancssorban lépjen be **azuread**.

2.  Passport.js telepítése:

    `npm install passport`

    A parancs kimenetében kell kinéznie:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: a passport-azure-ad hozzá a web API
Ezután adja hozzá az OAuth stratégiát, a passport-azuread használatával. `passport-azuread` a stratégiacsomag szolgál, amely az Azure AD connect a Passport van. Ez a stratégia használható a tulajdonosi jogkivonatokhoz a REST API minta használjuk.

> [!NOTE]
> Bár az OAuth 2.0-s, amelyben bármely ismert jogkivonat típus kibocsáthatja keretet biztosít, néhány gyakran használják. Tulajdonosi jogkivonatok gyakran használják a végpontok védelme. Tulajdonosi jogkivonatok olyan OAuth 2.0 token leggyakrabban kibocsátott típusú. Sok OAuth 2.0 típusú hitelesítés megvalósításához feltételezik, hogy tulajdonosi jogkivonatokat bocsátottak ki csak ilyen típusú.
> 
> 

1.  A parancssorban lépjen be **azuread**.

    `cd azuread`

2.  Telepítse a Passport.js `passport-azure-ad` modul:

    `npm install passport-azure-ad`

    A parancs kimenetében kell kinéznie:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: MongoDB-modulok hozzáadása a webes API
Ez a példa azt a mongodb az adattárban. 

1.  Telepítse a mongoose bővítményt, széles körben használt beépülő modult, modellek és sémák kezelésére: 

    `npm install mongoose`

2.  Az adatbázis illesztőprogram telepítése a mongodb, más néven MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: további modulok telepítése
Telepítse a további szükséges modulokat.

1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  Adja meg a következő parancsokat. A parancsok a következő modulok telepítése a node_modules könyvtárban:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: a függőségek Server.js fájl létrehozása
A Server.js fájl tartalmazza a a funkciók legnagyobb részét a webes API-kiszolgálóhoz. A kód a legtöbb hozzáadása ehhez a fájlhoz. Élesben való használat akkor is refactor a funkciókat a kisebb fájlok, például különálló útvonalak és vezérlők. Ebben a cikkben a Server.js erre a célra használjuk.

1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  A szerkesztővel az Ön által választott, hozzon létre egy Server.js fájlt. Adja hozzá a fájlhoz a következő információkat:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Mentse a fájlt. Ekkor visszakerül az hamarosan.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: az Azure AD-beállítások tárolására konfigurációs fájl létrehozása
Ez a kódfájl adja át a konfigurációs paraméterek az Azure AD portálon Passport.js. A webes API-t a cikk elején a portálra való felvételekor létrehozta ezeket a konfigurációs értékeket. A kód másolását követően azt ismertetjük, ezek a paraméterek kitöltésének elhelyezhető.

1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  Hozzon létre egy Config.js fájlt valamelyik szerkesztőben. Adja meg a következő információkat:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Kötelező értékek

*   **IdentityMetadata**: Ez akkor, ha `passport-azure-ad` keresi a konfigurációs adatokat az identitásszolgáltató (IDP) és a kulcsok a JSON Web Tokens (JWTs) érvényesítéséhez. Ha az Azure AD használ, nem érdemes lehet módosítani.

*   **a célközönség**: az átirányítási URI-t a portálról.

> [!NOTE]
> Rotálja a kulcsokat rendszeres időközönként. Győződjön meg arról, hogy Ön mindig húzza a "openid_keys" URL-címről, és, hogy az alkalmazás képes-e érni az internetet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: a konfiguráció hozzáadása a Server.js fájlhoz
Az alkalmazás kell kiolvasni az értékeket a most létrehozott konfigurációs fájl. Adja hozzá a .config kiterjesztésű fájlt kötelező erőforrásként az alkalmazásban. A globális változók értékét, amit a Config.js.

1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  Nyissa meg a Server.js valamelyik szerkesztőben. Adja meg a következő információkat:

    ```Javascript
    var config = require('./config');
    ```

3.  Új szakasz hozzáadása a Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: a MongoDB-modell és séma-információk hozzáadása a Mongoose segítségével
Ezek a fájlok a REST API-szolgáltatás a következő csatlakozzon.

Ebben a cikkben a MongoDB a feladatok tárolására használjuk. Ezt a tárgyaljuk *4. lépés*.

A Config.js fájl 11 lépésben létrehozott, az adatbázis neve *tasklist*. Amely volt helyezze a mongoose_auth_local kapcsolati URL-cím végén. Ezt az adatbázist nem szükséges előre létrehozni a MongoDB-ben. Az adatbázis létrehozása (feltéve, hogy az adatbázis már nem létezik) a kiszolgálóalkalmazás első alkalommal történő futtatásakor.

Megadta, hogy rendelkezik a kiszolgáló milyen MongoDB-adatbázist használja. Ezután meg kell írnia a modell és a kiszolgáló feladatok séma létrehozásához további kódot.

### <a name="the-model"></a>A modell
A séma modell nagyon egyszerű. Ha szeretné bővítheti. 

A séma modellje ezeket az értékeket:

*   **NÉV**. A feladathoz rendelt személy. Ez egy **karakterlánc** érték.
*   **A FELADAT**. A feladat nevét. Ez egy **karakterlánc** érték.
*   **DÁTUM**. A dátum, a feladat miatt. Ez egy **datetime** érték.
*   **BEFEJEZŐDÖTT**. Hogy a feladat befejeződött. Ez egy **logikai** érték.

### <a name="create-the-schema-in-the-code"></a>A séma kódjának megírása
1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  A szerkesztő nyissa meg a Server.js. A konfigurációs bejegyzés alatt adja meg a következő információkat:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Ez a kód a MongoDB-kiszolgálóhoz csatlakozik. Emellett egy séma-objektumot ad vissza.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>A modell a kódban a séma használatával hozhat létre
Az előzőekben látható kód alatt adja hozzá a következő kódot:

```Javascript
// Create a basic schema to store your tasks and users.
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

Mivel megadható, hogy a kód, először hoz létre a sémát. A következő modell-objektumot hoz létre. A modellobjektumot használatával tárolja az adatait, a kód egészében, ha a **útvonalak**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: a feladat REST API-kiszolgálóhoz a mutató útvonalak hozzáadása
Most, hogy egy adatbázismodell együttműködni, adja hozzá az útvonalakat a REST API-kiszolgálóhoz használni.

### <a name="about-routes-in-restify"></a>Az útvonalakra vonatkozó restify
Az útvonalak restify munkahelyi ugyanúgy így tesznek, amikor az Express verem használata. Az útvonalakat azon URI használatával kell meghatározni, amelyet elképzelései szerint az ügyfélalkalmazások meg fognak hívni. Az útvonalakat általában külön fájlban definiálni. Ebben az oktatóanyagban a Server.js az útvonalak elhelyezni azt. Éles környezetben való használathoz javasoljuk a saját fájlba útvonalak figyelembe.

A restify-útvonal egy tipikus mintája a következő:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Ez a minta a legalapvetőbb szinten. A restify (és az Express) sokkal összetettebb funkciók, például a alkalmazástípusok és a különböző végpontok között összetett útválasztási adja meg.

#### <a name="add-default-routes-to-your-server"></a>Alapértelmezett útvonalak beállítása a kiszolgálón
Adja hozzá az alapszintű CRUD-útvonalakat: **létrehozása**, **beolvasása**, **frissítése**, és **törlése**.

1.  A parancssorban lépjen be **azuread**:

    `cd azuread`

2.  Nyissa meg a Server.js valamelyik szerkesztőben. A korábban létrehozott adatbázis-bejegyzések az alábbiakban adja meg a következő információkat:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
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
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Hibakezelés beállítása az útvonalakhoz
Hibakezelést, az ügyfélnek a tapasztalt problémáról kommunikálhat.

Adja hozzá a következő kódot már megírt kód alá:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: a kiszolgáló létrehozása
Az utolsó lépés, hogy a kiszolgáló példányát adja hozzá. A server-példány kezeli a hívásokat.

A restify (és az Express) részletes testreszabási, melyekkel egy REST API-kiszolgálóval rendelkezik. Ebben az oktatóanyagban a legalapvetőbb telepítő használjuk.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: (hitelesítés nélkül, most) az útvonalak hozzáadása
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
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
// Register a default '/' handler
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
## <a name="16-run-the-server"></a>16: a kiszolgáló futtatása
Célszerű ellenőrizni a kiszolgáló hitelesítési hozzáadása előtt.

A legegyszerűbben úgy lehet ellenőrizni a kiszolgáló van a curl használatával parancsot a parancssorba. Ehhez szüksége egy egyszerű segédprogramra, melyekkel kimeneti adatok JSON-ként értelmezni. 

1.  Telepítse a JSON-segédprogramot, amely azt használja az alábbi példák:

    `$npm install -g jsontool`

    Ezzel globálisan telepíti a JSON-segédprogramot.

2.  Ellenőrizze, hogy fut-e a MongoDB-példány:

    `$sudo mongod`

3.  Lépjen be **azuread**, majd futtassa a curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
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

4.  Egy feladat hozzáadása:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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

5.  Lista feladatok Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Ha ezek a parancsok futtatása nem jelenik meg hibaüzenet, készen áll OAuth hozzáadása a REST API-kiszolgálóhoz.

*Most már rendelkezik a MongoDB-vel futó REST API-kiszolgáló!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: hitelesítés hozzáadása a REST API-kiszolgálóhoz
Most, hogy a futó REST API-t, beállítása az Azure AD-val használandó.

A parancssorban lépjen be **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>A passport-azure-ad részét képező képező oidcbearerstrategy használata
Az eddigi létrehozott egy tipikus REST TODO kiszolgálót hitelesítés nélkül. Ezután adja hozzá a hitelesítést.

Először is jelzi, hogy szeretné-e használni a Passport. Ez a jogosultság helyezze a korábbi kiszolgáló konfigurálása után:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> API-k írásakor mindig kapcsolja az adatokat egy egyedi, amely a felhasználói nem tudnak jogosulatlanul hozzáférni a jogkivonatból jó ötlet is. Ha a kiszolgáló tárolja a TODO elemeket tárolja őket a felhasználó a token (más néven token.sub keresztül) szereplő előfizetés-azonosító alapján. A token.sub be a "tulajdonos" mező. Ez biztosítja, hogy csak adott felhasználó hozzáférhet-e a felhasználó TODOs. A megadott TODOs senki más nem érheti el. Nem jelenik meg sehol van az API-t, a "tulajdonos". A külső felhasználó kérhet más felhasználók TODOs, még akkor is, ha hitelesített.
> 
> 

Ezután használhatja a nyitott ID Connect tulajdonosi stratégia `passport-azure-ad`. Be az alábbiakat mi beillesztett korábban:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
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
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

A Passport hasonló mintát alkalmaz az összes stratégia (Twitter-, Facebook-on, és így tovább). Stratégiafejlesztő mintát. A stratégia átadni egy `function()` tokent használ, és `done` paraméterekként. A stratégia ad vissza, miután a tevékenységeket végez el. Tárolni a felhasználót és menteni a jogkivonatot, így Önnek nem kell megismételni.

> [!IMPORTANT]
> Az előzőekben látható kód minden olyan felhasználó, amely képes hitelesíteni a kiszolgáló vesz igénybe. Ez az úgynevezett automatikus regisztráció. Az üzemi kiszolgálón így engedélyezni kívánja bárki, aki nélkül őket nyissa meg a regisztrációs folyamat az Ön által. Ez általában az a megoldást látjuk a fogyasztói alkalmazásoknál. Az alkalmazás is lehetővé teheti, regisztrálni a Facebook, de azt kéri, hogy további adatokat. Ha ez az oktatóanyag nem parancssori program alkalmaz, jogkivonat-objektumból visszaadott lehetett kibontani a az e-mailt. Ezután meg lehet, hogy kérnie a felhasználót adhat meg további információt. Mivel ez egy tesztkiszolgálón, hozzáadja a felhasználó közvetlenül a memóriában lévő adatbázishoz.
> 
> 

### <a name="protect-endpoints"></a>A végpontok védelme
A végpontok védelme megadásával a **passport.authenticate()** hívja meg a használni kívánt protokollt.

Az útvonal egy speciális beállítás a kiszolgáló kódjában található módosíthatja:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: futtassa újra a kiszolgálóalkalmazás
Curl újra használja OAuth 2.0 védelmi van-e a végpontokon. Ehhez minden az ügyfél-SDK-kat a végponton futtatása előtt. A visszaadott fejlécek kell mondja el, ha a hitelesítés megfelelően működik-e.

1.  Győződjön meg arról, hogy fut-e a MongoDB isntance:

    `$sudo mongod`

2.  Módosítsa a **azuread** könyvtárra, és használata curl:

    `$ cd azuread`

    `$ node server.js`

3.  Próbálkozzon meg egy egyszerű POST művelettel:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

A 401-es válasz azt jelzi, hogy a Passport réteg megpróbálja átirányítani a hitelesítési végpontra, vagyis pontosan mit szeretne.

*Most már rendelkezik egy REST API-szolgáltatás által használt OAuth 2.0!*

Ön már megtettünk mindent, ami az OAuth 2.0-kompatibilis ügyfél használata nélkül is ezen a kiszolgálón. Ehhez szüksége lesz egy további oktatóanyag áttekintése.

## <a name="next-steps"></a>További lépések
Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként biztosított [egy .zip fájl](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Akkor is klónozhatja azt a Githubról:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Most már továbbléphet az összetettebb témákra. Akkor célszerű [a v2.0-végpontra használata Node.js-webalkalmazás biztonságos](active-directory-v2-devquickstarts-node-web.md).

Az alábbiakban néhány további források:

* [Az Azure AD v2.0 fejlesztői útmutató](active-directory-appmodel-v2-overview.md)
* [A verem túlcsordulás "azure-active-directory" címke](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy regisztráljon biztonsági incidensek fordulhat elő, ha értesítést szeretne kapni. Az a [Microsoft műszaki biztonsági értesítéseket](https://technet.microsoft.com/security/dd252948) lapon, a biztonsági tanácsadók riasztást fizessen elő.

