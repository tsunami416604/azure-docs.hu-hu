---
title: Biztonságos webes API-k számára a Node.js segítségével az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan hozhat létre egy Node.js webes API, amely a B2C-bérlő származó jogkivonatokat fogad el.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 93c3bd3f902f08c8f019744b3f30745c1fd9fa01
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442423"
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: Védelem biztosítása webes API-k számára a Node.js segítségével
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Az Azure Active Directory (Azure AD) B2C segítségével védetté tehet egy webes API-t OAuth 2.0 hozzáférési jogkivonatok használatával. Ezek a jogkivonatok engedélyezik az Azure AD B2C-t alkalmazó ügyfélalkalmazások számára az API hitelesítését. Ebből a cikkből megtudhatja, hogyan hozhat létre egy olyan „feladatlista” API-t, amelyben a felhasználók megjeleníthetik a listát és új feladatokat adhatnak hozzá. A webes API-t az Azure AD B2C látja el védelemmel, így csak a hitelesített felhasználók kezelhetik a feladatlistájukat.

> [!NOTE]
> Ezt a mintát úgy írtuk meg, hogy az [iOS B2C mintaalkalmazással](active-directory-b2c-devquickstarts-ios.md) lehessen hozzá csatlakozni. Először haladjon végig ezen az ismertetőn, és aztán térjen rá az említett mintára.
>
>

A **Passport** a Node.js-hez készült közbenső hitelesítési szoftver. A rugalmasan működő, moduláris Passport gyakorlatilag bármely Express- vagy Restify-alapú webalkalmazásba diszkréten telepíthető. A program számos különböző lehetőséget kínál a felhasználók hitelesítésére: felhasználónév/jelszó, Facebook- vagy Twitter-fiók és így tovább. Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure Active Directory (Azure AD) esetében is felhasználható. Először telepítenie kell a modult, majd hozzá kell adni az Azure AD `passport-azure-ad` bővítményt.

Ennek a mintának az elvégzéséhez először az alábbiakat kell elvégeznie:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Az alkalmazás beállítása a Passport `passport-azure-ad` bővítményének használatára.
3. Ügyfélalkalmazás konfigurálása a „feladatlista” webes API meghívására.

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt.  A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket.  Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása
Következő lépésként létre kell hoznia egy alkalmazást a B2C-címtárban, amely ellátja az Azure AD-t a biztonságos kommunikációhoz szükséges információkkal. Ebben az esetben az ügyfélalkalmazáshoz és a webes API-hoz egyetlen **alkalmazásazonosító** tartozik, mivel a két elem egyetlen logikai alkalmazás lesz. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

* Az alkalmazás tartalmazzon egy **webalkalmazást vagy webes API-t**
* A **Reply URL** (Válasz URL-cím) legyen a következő: `http://localhost/TodoListService`. Ez a kódminta alapértelmezett URL-címe.
* Hozzon létre egy **alkalmazástitkot** az alkalmazáshoz, majd másolja. Erre az adatra később még szükség lesz. Ne feledje, hogy az értékben használat előtt [az XML-nek megfelelő feloldójelekkel](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) kell megjelölni a vezérlőkaraktereket.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Erre az adatra később még szükség lesz.

## <a name="create-your-policies"></a>Szabályzatok létrehozása
Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Ez az alkalmazás két, identitással kapcsolatos műveletet tartalmaz: regisztráció és bejelentkezés. Mindkettőhöz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy) leírtak szerint.  A három szabályzat létrehozásakor ügyeljen arra, hogy:

* A regisztrációs szabályzatban adja meg a **Megjelenített név** értékét, illetve az egyéb regisztrációs attribútumokat.
* Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet.  Ezenfelül más jogcímeket is használhat.
* Az egyes szabályzatok létrehozását követően másolja a szabályzat **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A szabályzatok nevére később még szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

Ha szeretné megismerni a szabályzatoknak az Azure AD B2C alatti működését, végezze el a [.NET-es webalkalmazások használatába bevezető oktatóanyagot](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>A kód letöltése
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). A minta menet közbeni létrehozásához [letöltheti a projektvázát tartalmazó .zip-fájlt](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). A vázprojektet klónozhatja is:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

A kész alkalmazás [.zip fájlként](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) vagy `complete` az adott adattár ágában is elérhető.

## <a name="download-nodejs-for-your-platform"></a>A Node.js letöltése a platformra
A minta használatához rendelkeznie kell a Node.js telepített és működő verziójával.

Telepítse a Node.js-t a [nodejs.org](http://nodejs.org) oldalról.

## <a name="install-mongodb-for-your-platform"></a>A MongoDB telepítése a platformra
A minta használatához rendelkeznie kell a MongoDB telepített és működő verziójával. A MongoDB-re a REST API különböző kiszolgálópéldányok közötti perzisztenciájának kialakításához van szükség.

Telepítse a MongoDB-t a [mongodb.org](http://www.mongodb.org) oldalról.

> [!NOTE]
> Ebben az útmutatóban feltételezzük, hogy Ön a MongoDB alapértelmezett telepítését és kiszolgálóvégpontjait használja, amely a cikk írásának időpontjában a következő: `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>A Restify-modulok telepítése a webes API-ra.
A REST API létrehozásához a Restify programot használjuk. A Restify egy minimális igényű és rugalmas Node.js alkalmazási keretrendszer, amely az Express alapján készült. Számos hatékony funkciót kínál a Connectre épített REST API-k létrehozásához.

### <a name="install-restify"></a>A Restify telepítése
A parancssorban módosítsa a következőre a könyvtárat: `azuread`. Ha az `azuread` könyvtár nem létezik, hozza létre.

`cd azuread` vagy `mkdir azuread;`

Írja be a következő parancsot:

`npm install restify`

Ez a parancs elvégzi a Restify telepítését.

#### <a name="did-you-get-an-error"></a>Hibaüzenetet kapott?
Egyes operációs rendszereken az `npm` parancs használata esetén az `Error: EPERM, chmod '/usr/local/bin/..'` szövegű hiba jelenik meg, és a rendszer felszólítja, hogy futtassa rendszergazdaként a fiókot. Ha ez a probléma jelentkezik, a `sudo` parancs használatával futtassa magasabb jogosultsági szinten az `npm` parancsot.

#### <a name="did-you-get-a-dtrace-error"></a>DTrace hibaüzenetet kapott?
Előfordulhat, hogy a Restify telepítése során az alábbihoz hasonló üzenet jelenik meg:

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

A Restify a DTrace segítségével biztosít a REST-hívások követésére szolgáló funkciókat. Számos operációs rendszerben azonban nem található meg a DTrace. Ezeket a hibákat nyugodtan figyelmen kívül hagyhatja.

A parancs kimenetének a következőképp kell kinéznie:

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

## <a name="install-passport-in-your-web-api"></a>A Passport telepítése a webes API-ba
A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van).

A Passport telepítéséhez használja az alábbi parancsot :

`npm install passport`

A parancs kimenetének a következőképpen kell kinéznie:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>A passport-azuread hozzáadása a webes API-hoz
Következő lépésként a `passport-azuread` használatával adja hozzá az OAuth stratégiát. Ez a stratégiacsomag szolgál az Azure AD és a Passport összekapcsolására. A REST API-mintában ez a stratégia használható a tulajdonosi jogkivonatokhoz.

> [!NOTE]
> Az OAuth2 keretrendszerében ugyan bármely ismert jogkivonat kibocsátható, csupán néhány típust használnak szélesebb körben. A végpontok védelmére szolgáló jogkivonatokat tulajdonosi jogkivonatoknak nevezzük. Az OAuth2-ben ezek a leggyakrabban kibocsátott jogkivonattípusok. Számos implementáció eleve úgy veszi, hogy csak tulajdonosi jogkivonatokat bocsátottak ki.
>
>

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van).

Telepítse a `passport-azure-ad` Passport-modult az alábbi paranccsal:

`npm install passport-azure-ad`

A parancs kimenetének a következőképpen kell kinéznie:

``
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
``

## <a name="add-mongodb-modules-to-your-web-api"></a>MongoDB-modulok hozzáadása a webes API-hoz
Ebben a példában a MongoDB lesz az adattároló. Ezért telepítenie kell a népszerű Mongoose bővítményt, amely modellek és sémák kezelésére szolgál.

* `npm install mongoose`

## <a name="install-additional-modules"></a>A kiegészítő modulok telepítése
Következő lépésként telepítse a további szükséges modulokat.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Telepítse a modulokat a `node_modules` könyvtárba:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>A függőségeknek megfelelő server.js fájl létrehozása
A `server.js` fájl biztosítja a funkciók legnagyobb részét a webes API kiszolgálója számára.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Hozza létre a `server.js` fájlt valamelyik szerkesztőben. Adja meg a következő információkat:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Mentse a fájlt. Később még visszatérünk rá.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Az Azure AD-beállításokat tároló config.js fájl létrehozása
Ez a kódfájl adja át a konfigurációs paramétereket az Azure AD-portálból a `Passport.js` fájlnak. Ezeket a konfigurációs értékeket akkor hozta létre, amikor az útmutató korábbi részében hozzáadta a webes API-t a portálhoz. A paraméterek kitöltésének módját a kód másolását követően ismertetjük.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Hozza létre a `config.js` fájlt valamelyik szerkesztőben. Adja meg a következő információkat:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Kötelező értékek
`clientID`: A webes API-alkalmazás ügyfél-azonosítója.

`IdentityMetadata`: a `passport-azure-ad` itt fogja keresni az identitásszolgáltatóra vonatkozó konfigurációs adatokat, továbbá a JSON webes jogkivonatainak érvényesítéséhez szükséges kulcsokat is.

`audience`: A hívást indító alkalmazást azonosító portál URI-ja.

`tenantName`: A bérlő neve (például **contoso.onmicrosoft.com**).

`policyName`: A kiszolgálóra érkező jogkivonatok érvényesítésére használni kívánt szabályzat. Ez ugyanaz a szabályzat legyen, mint amelyet az ügyfélalkalmazásban használ a bejelentkezéshez.

> [!NOTE]
> Egyelőre az ügyfél és a kiszolgáló beállítása során használja ugyanazokat a házirendeket. Ha már más oktatóanyagok keretében létrehozta ezeket a szabályzatokat, nem szükséges ismét létrehoznia őket. Mivel korábban már elvégezte az előírt lépéseket, nem szükséges új szabályzatokat létrehozni a webhelyen az ügyfelekre vonatkozó útmutatás részeként.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Konfiguráció hozzáadása a server.js fájlhoz
A létrehozott `config.js` fájl értékeinek kiolvasásához kötelező erőforrásként adja hozzá a `.config` fájlt az alkalmazáshoz, majd állítsa a globális változókat a `config.js` dokumentumban megadott értékekre.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Nyissa meg a `server.js` fájlt valamelyik szerkesztőben. Adja meg a következő információkat:

```Javascript
var config = require('./config');
```
Adjon egy új szakaszt a `server.js` fájlhoz. A szakasz a következő kódot tartalmazza:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

A következő lépésben hozzon létre helyőrzőket a hívó alkalmazásból érkező felhasználók számára.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Most pedig hozzon létre naplózót.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>A MongoDB-modellre és -sémára vonatkozó információk hozzáadása a Mongoose segítségével
Itt érik be a korábban elvégzett munka gyümölcse: az elkészített három fájlt most összehozzuk egy REST API-szolgáltatásban.

Az itt olvasható lépéseknél (ahogy arra már korábban utaltunk) a MongoDB-t használja a feladatok tárolására.

A `config.js` fájlban az adatbázis a **tasklist** nevet kapta. Ez az név került a `mongoose_auth_local` csatlakozási URL-cím végére is. Ezt az adatbázist nem szükséges előre létrehozni a MongoDB-ben. Az adatbázist az kiszolgálóalkalmazás az első futtatásakor létrehozza.

Miután közli a kiszolgálóval, hogy melyik MongoDB-adatbázist használja, a kiszolgálói feladatokhoz tartozó modell és séma létrehozásához további kódot kell írnia.

### <a name="expand-the-model"></a>A modell kibővítése
Ez a sémamodell egyszerű. Ez azt is jelenti, hogy szükség esetén könnyedén kibővíthető.

`owner`: A feladathoz rendelt személy. Ez egy **sztring**.  

`Text`: Maga a feladat. Ez egy **sztring**.

`date`: A feladat határideje. Ez egy **datetime** (dátum és idő) típusú objektum.

`completed`: Azt jelzi, hogy a feladat elkészült-e. Ez egy **logikai** típusú objektum.

### <a name="create-the-schema-in-the-code"></a>A séma kódjának megírása
A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Nyissa meg a `server.js` fájlt valamelyik szerkesztőben. Helyezze el a következő adatokat a konfigurációs bejegyzés alatt:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Először létre kell hozni a sémát, majd a modellobjektumot, amelyet arra fog használni, hogy a kód egészében tárolja adatait az **útvonalak** meghatározása során.

## <a name="add-routes-for-your-rest-api-task-server"></a>A REST API-feladatkiszolgálóra mutató útvonalak hozzáadása
Most, hogy létrehozta az adatbázismodellt, adja hozzá a REST API-kiszolgálóhoz használandó útvonalakat.

### <a name="about-routes-in-restify"></a>Az útvonalak működése a Restify programban
Az útvonalak a Restifyban ugyanúgy működnek, mint az Express verem használata esetén. Az útvonalakat azon URI használatával kell meghatározni, amelyet elképzelései szerint az ügyfélalkalmazások meg fognak hívni.

Íme egy jellegzetes Restify-útvonal:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

A Restify és az Express ennél jóval összetettebb funkciók biztosítására is képes: például definiálhatja velük az alkalmazástípusokat, valamint bonyolult útvonalvezetést valósíthat meg a különböző végpontok között. Ebben az oktatóanyagban egyszerűbb útvonalakat használunk.

#### <a name="add-default-routes-to-your-server"></a>Alapértelmezett útvonalak beállítása a kiszolgálón
Most a REST API-hoz hozzáadjuk az alapszintű CRUD-útvonalakat: **létrehozás** és **listázás**. A példa `complete` részében további útvonalak is szerepelnek.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Nyissa meg a `server.js` fájlt valamelyik szerkesztőben. Írja be az alábbi információkat a fentiekben létrehozott adatbázis-bejegyzések alá:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

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
            log.warn(err, "There is no tasks in the database. Add one!");
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


#### <a name="add-error-handling-for-the-routes"></a>Hibakezelés beállítása az útvonalakhoz
Állítson be hibakezelést is, hogy az észlelt problémákat olyan formában továbbíthassa az ügyfélhez, amelyet az képes megérteni.

Adja hozzá a következő kódot:

```Javascript
///--- Errors for communicating something interesting back to the client
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


## <a name="create-your-server"></a>A kiszolgáló létrehozása
Készen áll az adatbázis, és az útvonalak is a helyükön vannak. Utolsó lépésként hozzá kell adni a hívásokat kezelő kiszolgálópéldányt.

A Restify és az Express mélyreható konfigurációs funkciókat biztosítanak a REST API-kiszolgálóhoz, de itt most az alapszintű beállításokat fogjuk használni.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Útvonalak hozzáadása a kiszolgálóhoz (hitelesítés nélkül)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Hitelesítés hozzáadása a REST API-kiszolgálóhoz
Most, hogy rendelkezésére áll a futó REST API-kiszolgáló, felhasználhatja azt az Azure AD-ben is.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>A passport-azure-ad részét képező OIDCBearerStrategy használata
> [!TIP]
> Az API-k írásakor mindig kapcsolja az adatokat a jogkivonat valamely különleges részéhez, amelyhez a felhasználók nem tudnak jogosulatlanul hozzáférni. A kiszolgáló a jogkivonat „owner” mezőjében szereplő felhasználónak (a token.oid segítségével meghívott) **objektumazonosítója** alapján tárolja a ToDo elemeket. Ez az érték biztosítja, hogy csak az adott felhasználó férhet hozzá a saját ToDo elemeihez. Az „owner” API-ja nem jelenik meg sehol, így még a hitelesített külső felhasználók sem kérelmezhetik mások ToDo elemeit.
>
>

Ezt követően használja a `passport-azure-ad` részét képező tulajdonosi stratégiát.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

A Passport az összes stratégia esetében hasonló mintát alkalmaz. Át kell neki adni egy `function()` elemet, amelynek paramétereihez tartozik a `token` és a `done`. Ha a stratégia elvégezte teendőit, visszatér Önhöz. Ekkor érdemes tárolni a felhasználót és menteni a jogkivonatot, hogy a műveletet ne kelljen megismételni.

> [!IMPORTANT]
> A fenti kódrészlettel a hitelesítésen áteső felhasználók bejuthatnak a kiszolgálóra. Ezt a folyamatot automatikus regisztrációnak nevezzük. Az élesben működő kiszolgálók esetében csak akkor engedélyezze az API-khoz való felhasználói hozzáférést, ha már elvégezték a regisztrációs folyamatot. Általában ezt a megoldást látjuk az olyan fogyasztói alkalmazásoknál, amelyek engedélyezik a Facebook segítségével történő regisztrációt, de aztán további adatok megadását kérik. Ha ez nem parancssori program lenne, a visszakapott jogkivonat-objektumból kinyerhettük volna az e-mail-címet, és felkérhettük volna a felhasználót a kiegészítő adatok megadására. Mivel ez csupán egy példa, most egyszerűen hozzáadjuk őket a memóriában lévő adatbázishoz.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>A kiszolgálóalkalmazás futtatása annak ellenőrzése érdekében, hogy elutasítja-e Önt
A `curl` használatával ellenőrizheti, hogy az OAuth2-védelem működik-e a végpontokon. A visszakapott fejlécek alapján már megállapíthatja, hogy jó úton jár-e.

Ellenőrizze, hogy fut-e a MongoDB-példány:

    $sudo mongodb

Váltson át a könyvtárra és futtassa a kiszolgálót:

    $ cd azuread
    $ node server.js

Egy új terminálablakban futtassa a `curl` parancsot

Próbálkozzon meg egy egyszerű POST művelettel:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

A cél, hogy 401-es hiba jelenjen meg. Ez azt jelzi, hogy a Passport réteg megpróbálja átirányítani a hitelesítési végpontra.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Az OAuth2-t használó REST API-szolgáltatás ezzel elkészült
A Restify és az OAuth használatával elkészítette a REST API-t! Most már rendelkezésre állnak a megfelelő kódrészletek, amelyek segítségével tovább fejlesztheti szolgáltatását. Megtettünk mindent, ami a kiszolgálón OAuth2-kompatibilis ügyfél nélkül lehetséges volt. A következő lépéshez használjon újabb útmutatókat, például a [Csatlakozás webes API-hoz az iOS rendszer és a B2C segítségével](active-directory-b2c-devquickstarts-ios.md) című témakört.

## <a name="next-steps"></a>További lépések
Most már továbbléphet az összetettebb témákra, például:

[Csatlakozás webes API-hoz az iOS rendszer és a B2C segítségével](active-directory-b2c-devquickstarts-ios.md)
