---
title: "Node.js web app bejelentkezés az Azure Active Directory v2.0 |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre Node.js webalkalmazásokba jelentkezik be egy felhasználó személyes Microsoft-fiókkal és a munkahelyi vagy iskolai fiók használatával."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Bejelentkezés felvétele Node.js webalkalmazásokba

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók használata a v2.0-végponttól. Annak megállapításához, hogy használja a v2.0-végpontra vagy az 1.0-s verziójú végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 

Ebben az oktatóanyagban Passport használjuk a következő feladatok elvégzéséhez:

* A webalkalmazásban jelentkezzen be a felhasználó Azure Active Directory (Azure AD) és a v2.0-végponttól.
* A felhasználói információkat jelenítenek meg.
* Jelentkezzen ki az alkalmazásból a felhasználó.

A **Passport** a Node.js-hez készült közbenső hitelesítési szoftver. Rugalmas és, moduláris Passport diszkréten eldobása minden Express-alapú vagy restify webalkalmazás. Passport stratégiák széles választékát támogatja a hitelesítést egy felhasználónév és jelszó, Facebook-on, Twitter vagy egyéb beállítások használatával. Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure AD esetében is felhasználható. Ebben a cikkben azt mutatja be a modul telepítése, és adja hozzá a az Azure AD `passport-azure-ad` beépülő modult.

## <a name="download"></a>Letöltés
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Az oktatóanyag ismerteti, hogy is [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Ez az oktatóanyag végén az elkészült alkalmazás is beszerezheti.

## <a name="1-register-an-app"></a>1: az alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy hajtsa végre a [a részletes lépéseket](active-directory-v2-app-registration.md) regisztrálnia az alkalmazást. Ellenőrizze, hogy:

* Másolás a **alkalmazásazonosító** az alkalmazáshoz hozzárendelni. Ebben az oktatóanyagban van szükség.
* Adja hozzá a **webes** platform az alkalmazásra vonatkozóan.
* Másolás a **átirányítási URI-** a portálról. Az alapértelmezett URI értéket kell használnia `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>2: prerequisities adni a könyvtárhoz
Egy parancssorban lépjen nyissa meg a gyökérmappára, ha nem már létezik. Futtassa az alábbi parancsot:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

Ezenkívül használjuk `passport-azure-ad` a a vázat is használtuk:

* `npm install passport-azure-ad`

Ez telepíti a könyvtárak, amelyek `passport-azure-ad` használja.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: az alkalmazás beállítása a passport-csomópont-js stratégia használatára
Állítsa be az Express közbenső szoftvert az OpenID Connect hitelesítési protokoll használandó. A Passport használatával be- és kijelentkezési kérések kiállítása, a felhasználói munkamenet kezelése és a felhasználó, többek között adatainak beolvasása.

1.  A projekt gyökérkönyvtárában nyissa meg a Config.js fájlt. Az a `exports.creds` területen adja meg az alkalmazás konfigurációs értékeit.
  
  * `clientID`: A **alkalmazásazonosító** , amely hozzá van rendelve az alkalmazást az Azure portálon.
  * `returnURL`: A **átirányítási URI-** a portálon megadott.
  * `clientSecret`: A titkos kulcsot, a portálon létrehozott.

2.  A projekt gyökérkönyvtárában nyissa meg az App.js fájlban. A OIDCStrategy stratey, amely a meghívni kívánt `passport-azure-ad`, adja hozzá a következő hívást:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  A bejelentkezési kérések kezelésére, használja a korábban említett stratégiát:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

A Passport hasonló mintát alkalmaz az összes stratégia (Twitter-, Facebook-on, és így tovább). Stratégiafejlesztő mintát. A stratégia átadni egy `function()` tokent használ, és `done` paraméterekként. A stratégia ad vissza, miután a tevékenységeket végez el. Tárolni a felhasználót és menteni a jogkivonatot, így Önnek nem kell megismételni.

  > [!IMPORTANT]
  > Az előzőekben látható kód minden olyan felhasználó, amely képes hitelesíteni a kiszolgáló vesz igénybe. Ez az úgynevezett automatikus regisztráció. Az üzemi kiszolgálón így engedélyezni kívánja bárki, aki nélkül őket nyissa meg a regisztrációs folyamat az Ön által. Ez általában egy mintát, amely a fogyasztói alkalmazásoknál megjelenik az. Az alkalmazás is lehetővé teheti, regisztrálni a Facebook, de azt kéri, hogy további adatokat. Ha ez az oktatóanyag nem parancssori program alkalmaz, jogkivonat-objektumból visszaadott lehetett kibontani a az e-mailt. Ezután meg lehet, hogy kérnie a felhasználót adhat meg további információt. Mivel ez egy tesztkiszolgálón, hozzáadja a felhasználó közvetlenül a memóriában lévő adatbázishoz.
  > 
  > 

4.  Adja hozzá a metódusokat, amelyek segítségével nyomon követni a felhasználók, akik van bejelentkezve, a Passport által előírt. Ez magában foglalja a szerializálása és deszerializálása során a felhasználó adatait:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  Adja hozzá a kódot, amely az Express motor betöltése. Az alapértelmezett /views használ, és Express /routes minta biztosítja:

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  Adja hozzá a FELADÁS egy vagy több irányítja, hogy a tényleges bejelentkezési kéréseket átadása a `passport-azure-ad` motor:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: a be- és kijelentkezési kérések kiállítása az Azure AD használata a Passport
Az alkalmazás most már be van állítva az OpenID Connect hitelesítési protokoll használatával kommunikálnak a v2.0-végponttól. A `passport-azure-ad` stratégia gondoskodik hitelesítési üzenetek létrehozásával, ellenőrzése az Azure ad-jogkivonatok és karbantartása a felhasználói munkamenet összes részleteit. Ehhez marad csak a kell biztosítani a felhasználóknak olyan módon, bejelentkezés és kijelentkezés, valamint a bejelentkezett felhasználó további információt gyűjteni.

1.  Adja hozzá a **alapértelmezett**, **bejelentkezési**, **fiók**, és **kijelentkezési** az App.js fájl módszerek:

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Az alábbiak:
    
    * A `/` útvonal átirányítja a felhasználókat a index.ejs nézetet. Átadja a felhasználó a kérés (ha van ilyen).
    * A `/account` útvonal először *biztosítja, hogy hitelesítése* (megvalósítása, amely a következő kódrészlet). Ezt követően átadja a felhasználó a kérelemben. Ez az, hogy a felhasználó további információt.
    * A `/login` útvonal hívja a `azuread-openidconnect` a hitelesítő `passport-azuread`. Ha, amely nem jár sikerrel, az átirányítja a felhasználó vissza `/login`.
    * A `/logout` útvonal meghívja a logout.ejs nézet (és). Ez törli a cookie-kat, és adja vissza a felhasználót vissza index.ejs.

2.  Adja hozzá a **EnsureAuthenticated** , amelyet korábban a használt módszer `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  Az App.js fájl a kiszolgáló létrehozása:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: a nézetek és útvonalak létrehozása az Express a felhasználó a webhely megjelenítése
Adja hozzá az útvonalakat és nézeteket, amelyek az adatok megjelenítése a felhasználónak. Az útvonalakat és nézeteket is kezelni a `/logout` és `/login` létrehozott útvonalak.

1. Hozzon létre a gyökérkönyvtárban a `/routes/index.js` útvonal.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  Hozzon létre a gyökérkönyvtárban a `/routes/user.js` útvonal.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js`és `/routes/user.js` egyszerű útvonalak adják át a nézetekhez, ha van ilyen, többek között a felhasználó a kérés vannak.

3.  Hozzon létre a gyökérkönyvtárban a `/views/index.ejs` nézet. Ezen a lapon meghívja a **bejelentkezési** és **kijelentkezési** módszerek. Is használhatja a `/views/index.ejs` nézetre, és a fiók adatait rögzíti. Használhatja a feltételes hozzáférési `if (!user)` átadta-e keresztül a kérelemben szereplő felhasználóként. Fontos, hogy rendelkezik-e a bejelentkezett felhasználó bizonyító adatok.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  Hozzon létre a gyökérkönyvtárban a `/views/account.ejs` nézet. A `/views/account.ejs` nézet lehetővé teszi a további információk megjelenítéséhez, amelyek `passport-azuread` a felhasználói kérelem helyezi.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Adjon hozzá egy elrendezés. Hozzon létre a gyökérkönyvtárban a `/views/layout.ejs` nézet.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Build és az alkalmazás futtatása futtassa `node app.js`. Folytassa a `http://localhost:3000`.

7.  Jelentkezzen be személyes Microsoft-fiókkal vagy egy munkahelyi vagy iskolai fiókkal. Vegye figyelembe, hogy a felhasználó identitását tükröz /account listájában. 

Most már rendelkezik egy webalkalmazást, amelyet az iparági szabványos protokollok használatával. Az alkalmazás a felhasználók a személyes és munkahelyi vagy iskolai fiókok használatával képes hitelesíteni.

## <a name="next-steps"></a>Következő lépések
Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként biztosított [egy .zip fájl](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Akkor is klónozhatja azt a Githubról:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

A következő továbbléphet az összetettebb témákra. Előfordulhat, hogy ki szeretné próbálni:

[Node.js webes API biztonságossá a v2.0-végpontra használatával](active-directory-v2-devquickstarts-node-api.md)

Az alábbiakban néhány további források:

* [Az Azure AD v2.0 fejlesztői útmutató](active-directory-appmodel-v2-overview.md)
* [A verem túlcsordulás "azure-active-directory" címke](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy regisztráljon biztonsági incidensek fordulhat elő, ha értesítést szeretne kapni. Az a [Microsoft műszaki biztonsági értesítéseket](https://technet.microsoft.com/security/dd252948) lapon, a biztonsági tanácsadók riasztást fizessen elő.

