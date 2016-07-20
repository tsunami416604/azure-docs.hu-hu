<properties
    pageTitle="B2C előzetes verzió: védelem biztosítása webes API-k számára a Node.js segítségével | Microsoft Azure"
    description="Node.js webes API létrehozása, amely fogadja a B2C-bérlők által küldött jogkivonatokat"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="brandwe"/>

# B2C előzetes verzió: védelem biztosítása webes API-k számára a Node.js segítségével

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] A cikk nem tér ki az Azure AD B2C segítségével megvalósítható bejelentkezési, regisztrációs és profilkezelési műveletekre. A cikk a webes API-knak a felhasználó hitelesítését követő meghívásával foglalkozik. Ha még nem tette meg, az Azure Active Directory B2C alapjainak megismerése érdekében végezze el a következő oktatóanyagot: [.NET web app getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md) (Bevezetés a .NET-es webalkalmazásokba). 


> [AZURE.NOTE]  Ezt a mintát úgy írtuk meg, hogy az [iOS B2C mintaalkalmazással](active-directory-b2c-devquickstarts-ios.md) lehessen hozzá csatlakozni. Először végezze el az itt ismertetett lépéseket, majd folytassa a mintaalkalmazással.

A **Passport** a Node.js-hez készült közbenső hitelesítési szoftver. A rendkívül rugalmasan működő, moduláris Passport gyakorlatilag bármely Express- vagy Restify-alapú webalkalmazásba diszkréten telepíthető. A program számos különböző lehetőséget kínál a felhasználók hitelesítésére: felhasználónév/jelszó, Facebook- vagy Twitter-fiók és így tovább. Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure Active Directory (Azure AD) esetében is felhasználható. Először telepítenie kell a modult, majd hozzá kell adni az Azure AD `passport-azure-ad` bővítményt.

Ehhez a következőket kell tennie:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Az alkalmazás beállítása a Passport `azure-ad-passport` bővítményének használatára.
3. Ügyfélalkalmazás konfigurálása a „feladatlista” webes API meghívására.

Az oktatóanyag kódjának [kezelése a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). A lépések követéséhez [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip), vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

Az oktatóanyag végén az elkészült alkalmazást is megtalálja.

> [AZURE.WARNING]   A B2C előzetes verziójában ugyanazt az **ügyfél-azonosítót**/**alkalmazásazonosítót**, illetve szabályzatokat kell használnia a webes API feladatkiszolgálójánál és az ahhoz csatlakozó ügyfélnél. Ez az iOS-re és az Androidra vonatkozó oktatóanyagokra is igaz. Ha egy másik gyorsindítási útmutató során már létrehozott egy alkalmazást, használja az ott megadott értékeket, ne hozzon létre újakat.


## Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt.  A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket.  Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## Alkalmazás létrehozása

Következő lépésként létre kell hoznia egy alkalmazást a B2C-címtárban, amely ellátja az Azure AD-t a biztonságos kommunikációhoz szükséges információkkal. Ebben az esetben az ügyfélalkalmazást és a webes API-t is egyetlen **alkalmazásazonosító** képviseli, mivel a két elem közös logikai alkalmazássá áll össze. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

- Az alkalmazás tartalmazzon egy **webalkalmazást vagy webes API-t**
- A **Reply URL** (Válasz URL-cím) legyen a következő: `http://localhost/TodoListService`. Ez a kódminta alapértelmezett URL-címe.
- Hozzon létre egy **alkalmazástitkot** az alkalmazáshoz, majd másolja. Erre később még szüksége lesz. Erre hamarosan szüksége lesz. Ne feledje, hogy az értékben használat előtt [az XML-nek megfelelő feloldójelekkel](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) kell megjelölni a vezérlőkaraktereket.
- Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Később erre is szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Szabályzatok létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Az alkalmazás három különböző, identitással kapcsolatos műveletet tartalmaz: regisztráció, bejelentkezés és bejelentkezés Facebook-fiókkal. Mindháromhoz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) leírtak szerint.  A három szabályzat létrehozásakor ügyeljen arra, hogy:

- A regisztrációs szabályzatban adja meg a **Megjelenített név** értékét, illetve az egyéb regisztrációs attribútumokat.
- Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet.  Ezenfelül más jogcímeket is használhat.
- Az egyes szabályzatok létrehozását követően másolja a szabályzat **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A szabályzatok nevére később még szüksége lesz.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

Felhívjuk figyelmét, hogy ebben a cikkben nem foglalkozunk a létrehozott szabályzatok használatával. Ha szeretné megismerni a szabályzatoknak az Azure AD B2C alatti működését, végezze el a [.NET-es webalkalmazások használatába bevezető oktatóanyagot](active-directory-b2c-devquickstarts-web-dotnet.md).

## A Node.js letöltése a platformra

A minta használatához rendelkeznie kell a Node.js telepített és működő verziójával.

Telepítse a Node.js-t a [nodejs.org](http://nodejs.org) oldalról.

## A MongoDB telepítése a platformra

A minta használatához a MongoDB telepített és működő verziójával is rendelkeznie kell. A MongoDB-re a REST API különböző kiszolgálópéldányok közötti perzisztenciájának kialakításához van szükség.

Telepítse a MongoDB-t a [mongodb.org](http://www.mongodb.org) oldalról.

> [AZURE.NOTE] Ebben az útmutatóban abból indulunk ki, hogy Ön a MongoDB alapértelmezett telepítését és kiszolgálóvégpontjait használja, amely a cikk írásának időpontjában a következő: `mongodb://localhost`.

## A Restify-modulok telepítése a webes API-ra.

A REST API létrehozásához a Restify programot fogjuk használni. A Restify egy minimális igényű és rugalmas Node.js alkalmazási keretrendszer, amely az Express alapján készült. Számos hatékony funkciót kínál a Connectre épített REST API-k létrehozásához.

### A Restify telepítése

A parancssorban módosítsa a következőre a könyvtárat: `azuread`. Ha az `azuread` könyvtár nem létezik, hozza létre.

`cd azuread` vagy `mkdir azuread;`

Írja be a következő parancsot:

`npm install restify`

Ez a parancs elvégzi a Restify telepítését.

#### Hibaüzenetet kapott?

Egyes operációs rendszereken az `npm` parancs használata esetén az `Error: EPERM, chmod '/usr/local/bin/..'` szövegű hiba jelenik meg, és a rendszer felszólítja, hogy futtassa rendszergazdaként a fiókot. Ha ez történik, a `sudo` parancs segítségével futtassa magasabb jogosultsági szinten az `npm` parancsot.

#### DTrace hibaüzenetet kapott?

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

A parancs végeredményének a következőképp kell kinéznie:

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

## A Passport telepítése a webes API-ba

A [Passport](http://passportjs.org/) a Node.js-hez készült közbenső hitelesítési szoftver. A rendkívül rugalmasan működő, moduláris Passport gyakorlatilag bármely Express- vagy Restify-alapú webalkalmazásba diszkréten telepíthető. A program számos különböző lehetőséget kínál a felhasználók hitelesítésére: felhasználónév/jelszó, Facebook- vagy Twitter-fiók és így tovább. Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure AD esetében is felhasználható. Először telepítenie kell a modult, majd hozzá kell adni az Azure AD stratégiai bővítményt.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van).

Használja az alábbi parancsot a Passport telepítéséhez:

`npm install passport`

A parancs végeredményének a következőképpen kell kinéznie:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## A passport-azuread hozzáadása a webes API-hoz

Következő lépésként a `passport-azuread` használatával adja hozzá az OAuth stratégiát. Ez a stratégiacsomag szolgál az Azure AD és a Passport összekapcsolására. A REST API-mintában ez a stratégia használható a tulajdonosi jogkivonatokhoz.

> [AZURE.NOTE] Az OAuth2 keretrendszerében ugyan bármely ismert jogkivonat kibocsátható, csupán néhány típust használnak szélesebb körben. A végpontok védelmére szolgáló jogkivonatokat tulajdonosi jogkivonatoknak nevezzük. Ezek számítanak a leggyakrabban kibocsátott jogkivonattípusnak az OAuth2-ben. Számos implementáció eleve úgy veszi, hogy csak tulajdonosi jogkivonatokat bocsátottak ki.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van).

Használja az alábbi parancsot a Passport `passport-azure-ad` moduljának telepítéséhez:

`npm install passport-azure-ad`

A parancs végeredményének a következőképpen kell kinéznie:

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

## MongoDB-modulok hozzáadása a webes API-hoz

A MongoDB lesz az adattároló. Ezért telepítenie kell a népszerű Mongoose bővítményt, amely modellek és sémák kezelésére szolgál, valamint a MongoDB adatbázis-illesztőprogramját, amelyet szintén MongoDB-nek hívnak.

* `npm install mongoose`
* `npm install mongodb`

## A kiegészítő modulok telepítése

Következő lépésként telepítse a további szükséges modulokat.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Az alábbi parancsok kiadásával telepítse a szükséges modulokat a `node_modules` könyvtárba:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## A függőségeknek megfelelő server.js fájl létrehozása

A `server.js` fájl biztosítja a funkciók legnagyobb részét a webes API kiszolgálója számára. Az itt található kód jelentős részét ehhez a fájlhoz kell adni. Élesben való használat esetén érdemes lehet kisebb fájlokra bontani a funkciókat, például különálló útvonalak és vezérlők szerint. A jelen oktatóanyag esetében használja az összes funkcióhoz a server.js fájlt.

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

## Az Azure AD-beállításokat tároló config.js fájl létrehozása

Ez a kódfájl adja át a konfigurációs paramétereket az Azure AD-portálból a `Passport.js` fájlnak. Ezeket a konfigurációs értékeket akkor hozta létre, amikor az útmutató korábbi részében hozzáadta a webes API-t a portálhoz. A paraméterek kitöltésének módját a kód másolását követően ismertetjük.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Hozza létre a `config.js` fájlt valamelyik szerkesztőben. Adja meg a következő információkat:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Kötelező értékek

`IdentityMetadata`: a `passport-azure-ad` itt fogja keresni az identitásszolgáltató számára szükséges konfigurációs adatokat. Ezenfelül a rendszer itt keresi a JSON webes jogkivonatainak érvényesítéséhez szükséges kulcsokat is. Ha az Azure AD-t használja, valószínűleg nem szükséges módosítani.

`audience`: a szolgáltatást azonosító portál URI-ja. A mintánkban ez a `http://localhost/TodoListService`.

`tenantName`: a bérlő neve (például **contoso.onmicrosoft.com**).

`policyName`: a kiszolgálóra érkező jogkivonatok érvényesítésére használni kívánt szabályzat. Ez legyen ugyanaz a szabályzat, mint amelyet az ügyfélalkalmazásban használ a bejelentkezéshez.

> [AZURE.NOTE] A B2C előzetes verziójában az ügyfél és a kiszolgáló beállítása során is ugyanazokat a szabályzatokat használjuk. Ha már más oktatóanyagok keretében létrehozta ezeket a szabályzatokat, nem szükséges ismét létrehoznia őket. Mivel korábban már elvégezte az előírt lépéseket, nem szükséges új szabályzatokat létrehozni a webhelyen az ügyfelekre vonatkozó útmutatás részeként.

## Konfiguráció hozzáadása a server.js fájlhoz

Az alkalmazás minden részében szükség van az imént létrehozott `config.js` fájl értékeinek beolvasására. Ehhez adja hozzá a `.config` fájlt kötelező erőforrásként az alkalmazáshoz, majd állítsa a globális változókat a `config.js` dokumentumban megadott értékekre.

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
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## A MongoDB-modellre és -sémára vonatkozó információk hozzáadása a Mongoose segítségével

Itt érik be a korábban elvégzett munkálatok gyümölcse: az elkészített három fájlt most összehozzuk a REST API-szolgáltatásban.

Az itt olvasható lépéseknél (ahogy arra már korábban utaltunk) a MongoDB-t használja a feladatok tárolására.

A létrehozott `config.js` fájlban az adatbázis a **tasklist** nevet kapta. Ez a megnevezés került a `mongoose_auth_local` csatlakozási URL-cím végére is. Ezt az adatbázist nem szükséges előre létrehozni a MongoDB-ben. Ha még nem létezik, a program a kiszolgálóalkalmazás első alkalommal történő futtatásakor létrehozza az adatbázist.

Miután közli a kiszolgálóval, hogy melyik MongoDB-adatbázist használja, a kiszolgálói feladatokhoz tartozó modell és séma létrehozásához további kódot kell írnia.

### A modell kibővítése

Ez a sémamodell felettébb egyszerű. Ez azt is jelenti, hogy szükség esetén könnyedén kibővíthető.

`name`: a feladathoz rendelt személy. Ez egy **karakterlánc**.

`task`: maga a feladat. Ez egy **karakterlánc**.

`date`: a feladat határideje. Ez egy **dátum és idő** típusú érték.

`completed`: azt jelzi, hogy a feladat kész van-e. Ez egy **logikai érték**.

### A séma kódjának megírása

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Nyissa meg a `server.js` fájlt valamelyik szerkesztőben. Helyezze el a következő adatokat a konfigurációs bejegyzés alatt:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Ezzel a program csatlakozik a MongoDB-kiszolgálóhoz, és visszaadja a sémaobjektumot.

### A modell létrehozása a kódban a séma segítségével

A fentiekben megírt kód alá írja be az alábbi kódot:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Először létre kell hozni a sémát, majd a modellobjektumot, amelyet arra fog használni, hogy a kód egészében tárolja adatait az **útvonalak** meghatározása során.

## A REST API-feladatkiszolgálóra mutató útvonalak hozzáadása

Most, hogy létrehozta az adatbázismodellt, adja hozzá a REST API-kiszolgálóhoz használandó útvonalakat.

### Az útvonalak működése a Restify programban

Az útvonalak a Restifyban ugyanúgy működnek, mint az Express verem használata esetén. Az útvonalakat azon URI használatával kell meghatározni, amelyet elképzelései szerint az ügyfélalkalmazások meg fognak hívni. A legtöbb esetben érdemes külön fájlban definiálni az útvonalakat. Ebben az oktatóanyagban a `server.js` fájlban helyezzük el az útvonalakat. Javasoljuk, hogy éles környezetben ezeket bontsa külön-külön fájlokra.

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

Ez a minta legalapvetőbb megjelenése. A Restify és az Express ennél jóval összetettebb funkciók biztosítására is képes: például definiálhatja velük az alkalmazástípusokat, valamint bonyolult útvonalvezetést valósíthat meg a különböző végpontok között. Ebben az oktatóanyagban egyszerűbb útvonalakat használunk.

#### Alapértelmezett útvonalak beállítása a kiszolgálón

Most hozzáadjuk az alapszintű CRUD-útvonalakat: **létrehozás**, **lekérés**, **frissítés** és **törlés**.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

Nyissa meg a `server.js` fájlt valamelyik szerkesztőben. Írja be az alábbi információkat a fentiekben létrehozott adatbázis-bejegyzések alá:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
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
// Delete a task by name
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
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
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
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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

#### Hibakezelés beállítása az útvonalakhoz

Érdemes hibakezelést is beállítani, hogy az észlelt problémákat olyan formában közölhesse az ügyféllel, amelyet az képes megérteni.

A fentiekben megírt kód alá írja be az alábbi kódot:

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


## A kiszolgáló létrehozása

Készen áll az adatbázis, és az útvonalak is a helyükön vannak. Utolsó lépésként hozzá kell adni a hívásokat kezelő kiszolgálópéldányt.

A Restify és az Express mélyreható konfigurációs funkciókat biztosítanak a REST API-kiszolgálóhoz, de itt most az alapszintű beállításokat fogjuk használni.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
}));
```
## Útvonalak hozzáadása a kiszolgálóhoz (hitelesítés nélkül)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## A kiszolgáló futtatása az OAuth-támogatás hozzáadása előtt

A hitelesítés beállítása előtt érdemes ellenőrizni a kiszolgáló működését.

Ennek legegyszerűbb módja a `curl` parancssori használata. Ezelőtt azonban szükség lesz egy egyszerű segédprogramra, amely lehetővé teszi a kimeneti adatok JSON-ként történő elemzését. Először telepítse a JSON-segédprogramot.

`$npm install -g jsontool`

Ezzel globálisan telepíti a JSON-segédprogramot. A JSON-segédprogram telepítését követően tesztelje a kiszolgálót:

Ellenőrizze, hogy fut-e a MongoDB-példány.

`$sudo mongodb`

Váltson át az `azuread` könyvtárra, és használja a `curl` parancsot.

`$ cd azuread`
`$ node server.js`

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

Adjon hozzá egy feladatot:

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
A „Brandon” nevű felhasználóhoz tartozó feladatokat a következőképp listázhatja ki:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Ha minden megfelelően működik, hozzáadhatja az OAuth-hitelesítést a REST API-kiszolgálóhoz.

Rendelkezésre áll a MongoDB-vel futó REST API-kiszolgáló.

## Hitelesítés hozzáadása a REST API-kiszolgálóhoz

Most, hogy rendelkezésére áll a futó REST API-kiszolgáló, felhasználhatja azt az Azure AD-ben is.

A parancssorban módosítsa a következőre a könyvtárat: `azuread` (ha jelenleg nem ebben a könyvtárban van):

`cd azuread`

### A passport-azure-ad részét képező OIDCBearerStrategy használata

Az eddigiekben létrehoztunk egy tipikus REST ToDo kiszolgálót, amelyhez nem tartozik hitelesítés. Most hozzáfoghat a hitelesítés összeállításának.

Először meg kell adnia, hogy a Passport programot szeretné használni. Írja be az alábbiakat közvetlenül a másik kiszolgáló konfigurációja alá:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Az API-k írásakor mindig kapcsolja az adatokat a jogkivonat valamely különleges részéhez, amelyhez a felhasználók nem tudnak jogosulatlanul hozzáférni. A kiszolgáló a jogkivonat az „owner” mezőjében szereplő felhasználónak (a token.oid segítségével meghívott) **objektumazonosítója** alapján tárolja a ToDo elemeket. Ez garantálja, hogy a felhasználók kizárólag a saját ToDo elemeikhez férjenek hozzá. Így senki más nem érheti el a bevitt ToDo elemeket. Az „owner” API-ja nem jelenik meg sehol, így még a hitelesített külső felhasználók sem kérelmezhetik mások ToDo elemeit.

Ezt követően használja a `passport-azure-ad` részét képező tulajdonosi stratégiát. (Egyelőre csak megtekintjük a kódot.) Illessze be az alábbiakat a fentiekben hozzáadott részek alá:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
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
passport.use(oidcStrategy);
```

A Passport által a stratégiákhoz (így a Twitterhez és a Facebookhoz) használt minta a legtöbb stratégiafejlesztő által előnyben részesített megoldásokhoz hasonlít. Át kell neki adni egy `function()` elemet, amelynek paramétereihez tartozik a `token` és a `done`. Ha a stratégia elvégezte teendőit, visszatér Önhöz. Ekkor érdemes tárolni a felhasználót és menteni a jogkivonatot, hogy a műveletet ne kelljen megismételni.

> [AZURE.IMPORTANT]
A fenti kódrészlettel a hitelesítésen áteső felhasználók bejuthatnak a kiszolgálóra. Ezt automatikus regisztrációnak nevezzük. Az élesben működő kiszolgálók esetében általában csak akkor érdemes beengedni a felhasználókat, ha elvégezték az Ön által előírt regisztrációs folyamatot. Általában ezt a megoldást látjuk a fogyasztói alkalmazásoknál, amelyek engedélyezik a Facebook segítségével történő regisztrációt, de aztán további adatok megadását kérik. Ha ez nem parancssori program lenne, a visszakapott jogkivonat-objektumból kinyerhettük volna az e-mail-címet, és felkérhettük volna a felhasználót a kiegészítő adatok megadására. Mivel a kiszolgáló csak ellenőrzési célokat szolgál, most egyszerűen hozzáadjuk őket a memóriában lévő adatbázishoz.

### A végpontok védelme

Védelmet biztosít a végpontoknak, amikor a használni kívánt protokoll segítségével meghatározza a `passport.authenticate()` hívást.

A kiszolgáló kódjában található útvonal módosításával ennél is érdekesebb megoldást valósíthatunk meg:

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

## A kiszolgálóalkalmazás ismételt futtatása annak ellenőrzése érdekében, hogy elutasítja-e Önt

A `curl` ismételt használatával ellenőrizheti, hogy az OAuth2-védelem működik-e a végpontokon. Mindenképpen végezze el ezt a műveletet, mielőtt futtatja az ügyfél-SDK-kat a végponton. A visszakapott fejlécek alapján megállapíthatja, hogy jó úton jár-e.

Ellenőrizze, hogy fut-e a MongoDB-példány:

    $sudo mongodb

Váltson át a könyvtárra, és használja a `curl` parancsot:

    $ cd azuread
    $ node server.js

Próbálkozzon meg egy egyszerű POST művelettel:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

A cél, hogy 401-es hiba jelenjen meg. Ez azt jelzi, hogy a Passport réteg megpróbálja átirányítani a hitelesítési végpontra.


## Az OAuth2-t használó REST API-szolgáltatás ezzel elkészült

Megtettünk mindent, ami a kiszolgálón OAuth2-kompatibilis ügyfél nélkül lehetséges volt. A továbbiakhoz újabb oktatóanyagokat kell elvégeznie.

Ha csupán a REST API Restify és OAuth2 segítségével történő megalkotásával kapcsolatban keresett információkat, most már rendelkezésre állnak a megfelelő kódrészletek, amelyek segítségével tovább fejlesztheti szolgáltatását.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [.zip-fájlban is letöltheti](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Ezenfelül a GitHubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Következő lépések

Most már továbbléphet az összetettebb témákra, például:

[Connect to a web API by using iOS with B2C (Csatlakozás webes API-hoz az iOS rendszer és a B2C segítségével).](active-directory-b2c-devquickstarts-ios.md)



<!--HONumber=Jun16_HO2-->


