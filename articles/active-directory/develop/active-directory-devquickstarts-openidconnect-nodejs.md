---
title: "Ismerkedés az Azure AD Node.js webalkalmazás |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre, amely az Azure ad-val bejelentkezési Node.js Express MVC webalkalmazás."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: a0079c1f4265e1c2564b85247cf5f5e0289799db
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Ismerkedés az Azure AD Node.js webalkalmazás
Jelen példában használjuk a Passport:

* A felhasználói bejelentkezés az Azure Active Directory (Azure AD) alkalmazást.
* A felhasználói információkat jelenítenek meg.
* Jelentkezzen ki az alkalmazásból a felhasználó.

A Passport hitelesítési köztes a Node.js is. Rugalmas és, moduláris Passport diszkréten eldobja bármely Express-alapú vagy restify webes alkalmazást. Stratégiák széles választékát támogatja a felhasználónév és jelszó, Facebook, Twitter, és több használó hitelesítés. Kidolgoztunk is stratégiát a Microsoft Azure Active Directoryhoz. Azt telepítenie kell a modult, és vegye fel a Microsoft Azure Active Directory `passport-azure-ad` beépülő modult.

Ehhez tegye a következőket:

1. Az alkalmazás regisztrálásához.
2. Állítsa be az alkalmazás használatához a `passport-azure-ad` stratégia.
3. Be- és kijelentkezési kérések kiállítása az Azure AD számára a Passport használatával.
4. Nyomtassa ki a felhasználóval kapcsolatos adatokat.

Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Követéséhez is [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Az elkészült alkalmazást is megtalálja, valamint az oktatóanyag végén.

## <a name="step-1-register-an-app"></a>1. lépés: Az alkalmazás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A lap felső menüben válassza ki a fiókját. Az a **Directory** menüben válassza ki, hol szeretné az alkalmazás regisztrálása az Active Directory-bérlő.

3. Válassza ki **több szolgáltatások** a bal oldalán a képernyő, és válassza a menü **Azure Active Directory**.

4. Válassza ki **App regisztrációk**, majd válassza ki **Hozzáadás**.

5. Kövesse a megjelenő utasításokat hozzon létre egy **webalkalmazás** és/vagy **WebAPI**.
  * A **neve** az alkalmazás az alkalmazás a felhasználók számára ismerteti.

  * A **bejelentkezési URL-cím** az alkalmazás alap URL-címe.  A vázat alapértelmezett érték a "return http://localhost: 3000/auth/openid nyelvhez.

6. Miután regisztrálta, az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Ez az érték kell a következő szakaszokban, ezért másolja az alkalmazás oldalról.
7. Az a **beállítások** -> **tulajdonságok** az alkalmazás lapján frissítse a App ID URI. A **App ID URI** az alkalmazás egyedi azonosítója. Az egyezmény a formátumának a használatára `https://<tenant-domain>/<app-name>`, például: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>2. lépés: A címtár Előfeltételek felvétele
1. A parancssorból módosítsa a könyvtárat a gyökérmappára, ha már nem létezik, és futtassa a következő parancsokat:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Továbbá szüksége `passport-azure-ad`:
    * `npm install passport-azure-ad`

Ez telepíti a könyvtárak, amelyek `passport-azure-ad` függ.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. lépés: Az alkalmazás használatának beállítása a passport-csomópont-js stratégia
Itt azt az OpenID Connect hitelesítési protokoll használatára Express konfigurálása.  A Passport használatos különböző műveleteket, beleértve a probléma be- és kijelentkezési kérések, a felhasználói munkamenet kezelésére, és a felhasználó adatainak beolvasása.

1. Első lépésként nyissa meg a `config.js` a projekt gyökerében fájlt, és írja be az alkalmazás konfigurációs értékeit a `exports.creds` szakasz.

  * A `clientID` van a **alkalmazásazonosító** az alkalmazáshoz a regisztrációs portálon rendelt.

  * A `returnURL` van a **átirányítási Uri-** a portálon megadott.

  * A `clientSecret` a titkos kulcsot, a portálon létrehozott van.

2. Ezután nyissa meg a `app.js` fájlt a projekt gyökérkönyvtárában található. Majd adja hozzá a következő hívást meghívása a `OIDCStrategy` stratégia `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Ezt követően a stratégia azt említett a bejelentkezési kérések kezelésére használja.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
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
A Passport az összes stratégia (Twitter-, Facebook-on, és így tovább), amely stratégiafejlesztő a hasonló mintát alkalmaz. Megnézzük a stratégiát, láthatja, hogy azt adja át azt egy függvény, amely rendelkezik egy jogkivonat és kész a paraméterekként. A stratégia ismét elérhető lesz a számunkra után a tevékenységeket végez el. Majd szeretnénk tárolni a felhasználót és menteni a jogkivonatot, így azt nem kell megismételni.

> [!IMPORTANT]
Az előző kód készít minden olyan felhasználó, hogy a kiszolgáló hitelesítésére történik. Ez az úgynevezett automatikus regisztráció. Azt javasoljuk, ne engedélyezze, hogy bárki nélkül azokat, amelyeket a folyamat használatával regisztrálja azokat egy üzemi kiszolgálóra hitelesítéséhez. Ez általában az a megoldást látjuk a fogyasztói alkalmazásoknál, melyek lehetővé teszik regisztrálni a Facebook, de majd megkérdezi, hogy kiegészítő információkat. Ha ez nem egy mintaalkalmazást, kinyerhettük volna a felhasználó e-mail címe jogkivonat-objektumból, amely adott vissza, és felkérhettük volna a felhasználót, hogy további adatokat. Mivel ez egy tesztkiszolgálón, hogy adja hozzá a memóriában lévő adatbázishoz.


4. A következő adjuk hozzá a módszereket, amelyek lehetővé teszik a számunkra, hogy a Passport által előírt bejelentkezett felhasználók nyomon követése. Ezek a metódusok lehetnek szerializálása és deszerializálása során a felhasználó adatait.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
                done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
                findByEmail(id, function (err, user) {
                    done(err, user);
                });
            });

            // array to hold signed-in users
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

5.  A következő adjuk hozzá a kódot az Express motor betöltése. Jelen példában használjuk a alapértelmezett /views és Express /routes minta nyújt.

    ```JavaScript

        // configure Express (section 2)

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

6. Végezetül adjuk hozzá az útvonalakat, amelyek kiosztják a tényleges bejelentkezési kéréseket a `passport-azure-ad` motor:

    ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

        // GET /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.get('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });

        // POST /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.post('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });
     ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. lépés: Használatát a Passport való be- és kijelentkezési kérések kiállítása az Azure AD
Az alkalmazás megfelelően konfigurálva van az endpoint OpenID Connect hitelesítési protokoll használatával kommunikálnak.  `passport-azure-ad`rendelkezik az összes adat hitelesítési üzenetek létrehozásával, ellenőrzése az Azure ad-jogkivonatok és karbantartása a felhasználói munkamenetek végrehajtott fontos. Összes fennmaradó jogosultságot ad a felhasználók bejelentkezés és kijelentkezés, és a bejelentkezett felhasználókkal kapcsolatos további információk összegyűjtéséhez.

1. Első lépésként adjuk hozzá az alapértelmezett, bejelentkezési, fiókkal, és kijelentkezési metódusokat az `app.js` fájlt:

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
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  Tekintsük át részletesen ezek:

  * A `/`útvonal átirányítja a felhasználókat a index.ejs nézetet, a felhasználó átadja a kérésben (ha van ilyen).
  * A `/account` útvonal először *biztosítja, hogy hitelesítése* (megvalósítása, amely a következő példában), és a felhasználó majd átadja a kérésben, hogy megkaphassa a felhasználó további információt.
  * A `/login` útvonal hívja fel az azuread-openidconnect hitelesítő `passport-azuread`. Ha nem jár sikerrel, amely, vissza a Publication átirányítja a felhasználót.
  * A `/logout` útvonal egyszerűen hívja a logout.ejs (és útvonal), amely törli a cookie-kat, majd visszaadja a felhasználó vissza a index.ejs.

3. Az utolsó részének `app.js`, adjuk hozzá a **EnsureAuthenticated** a használt módszer `/account`, mert a korábban bemutatott.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Végül hozzon létre magát a kiszolgálót a `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>5. lépés: Szeretné megjeleníteni a felhasználó a webhelyen, hozza létre a nézetek és útvonalak az Express
Most `app.js` befejeződött. Egyszerűen kell hozzáadni az útvonalakat és nézeteket, amelyek megjelenítik az adatokat, azt a felhasználó eléréséhez, valamint kezelni a `/logout` és `/login` létrehozott útvonalakat.

1. Hozza létre a gyökérkönyvtárban a `/routes/index.js` útvonalat.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Hozza létre a gyökérkönyvtárban a `/routes/user.js` útvonalat.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Ezek adják át a kérést a nézetekhez, beleértve a felhasználó, ha van ilyen.

3. Hozza létre a gyökérkönyvtárban a `/views/index.ejs` útvonalat. Ez az egy egyszerű lap, amely a bejelentkezési és kijelentkezési módszereket hív meg, és lehetővé teszi, hogy a fiók adatait adása. Figyelje meg, hogy a feltételes hozzáférési használhatunk `if (!user)` átadta-e keresztül a kérelem a felhasználó a bejelentkezett felhasználó tudunk bizonyító adatok lesz.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Hozzon létre a `/views/account.ejs` megtekintése a gyökérkönyvtárban, így azt további információk is megtekinthetők, amely `passport-azuread` állította a felhasználói kérelemben.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Ellenőrizze a hely jó elrendezés hozzáadásával. Hozzon létre a ' / views/layout.ejs' nézetet a gyökérkönyvtárban.

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
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>További lépések
Végezetül hozza létre, és futtassa az alkalmazást. Futtatás `node app.js`, majd lépjen `http://localhost:3000`.

Jelentkezzen be személyes Microsoft-fiókkal vagy egy munkahelyi vagy iskolai fiókkal, és figyelje meg, hogyan jelenik meg a felhasználó identitását /account listájában. Most már rendelkezik egy webalkalmazást az iparági szabványos protokollok, amely képes hitelesíteni a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók védett.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [.zip-fájlban is letöltheti](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Azt is megteheti hogy is klónozza a Githubról:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Ön most már továbbléphet az összetettebb témákra. Előfordulhat, hogy ki szeretné próbálni:

[Védelem biztosítása webes API-t az Azure ad szolgáltatással](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
