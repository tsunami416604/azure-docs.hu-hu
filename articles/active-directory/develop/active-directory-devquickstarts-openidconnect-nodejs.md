---
title: Ismerkedés az Azure AD Node.js webalkalmazás |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Node.js Express MVC-webalkalmazást, amely integrálható az Azure AD-be.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 7aa48b65423db2a3af032ed64d9d571fa603668d
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144754"
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Ismerkedés az Azure AD Node.js webalkalmazás
Jelen példában használjuk a Passport:

* A felhasználó jelentkezzen be az alkalmazást az Azure Active Directory (Azure AD).
* A felhasználó információit jeleníti meg.
* Jelentkezzen ki az alkalmazásból a felhasználó.

A Passport node.js-hez készült közbenső hitelesítési szoftver is. Rugalmas, moduláris Passport diszkréten törölhetők bármely Express-alapú vagy restify webes alkalmazást. Stratégiák átfogó készletét támogatja a hitelesítés által használt felhasználónév és jelszó, Facebook, Twitter, stb. Mi a Microsoft Azure Active Directory kidolgoztunk egy stratégiát. Hogy ez a modul telepítéséhez, és vegye fel a Microsoft Azure Active Directory `passport-azure-ad` beépülő modult.

Ehhez tegye a következőket:

1. Alkalmazás regisztrálása.
2. Állítsa be az alkalmazás használatához a `passport-azure-ad` stratégia.
3. Be- és kijelentkezési kérések kiállítása az Azure AD számára a Passport használatával.
4. Nyomtassa ki a felhasználóval kapcsolatos adatok.

Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Követéséhez is [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

A kész alkalmazáshoz, valamint ez az oktatóanyag végén van megadva.

## <a name="step-1-register-an-app"></a>1. lépés: Alkalmazás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az oldal tetején lévő menüben válassza a fiókját. Alatt a **Directory** menüben válassza ki az Active Directory-bérlő kívánja az alkalmazás regisztrálásához.

3. Válassza ki **minden szolgáltatás** a képernyőn, és válassza ki a bal oldali menüben **Azure Active Directory**.

4. Válassza ki **alkalmazásregisztrációk**, majd válassza ki **Hozzáadás**.

5. Kövesse az utasításokat követve hozzon létre egy **webalkalmazás** és/vagy **WebAPI**.
  * A **neve** az alkalmazás ismerteti a felhasználók az alkalmazást.

  * A **bejelentkezési URL-** az alkalmazás alap URL-címe. A vázban alapértelmezés szerint `http://localhost:3000/auth/openid/return`.

6. Miután regisztrálta, az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Ez az érték szükséges a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
7. Az a **beállítások** -> **tulajdonságok** oldalon az alkalmazás, frissítse az Alkalmazásazonosító URI-t. A **Alkalmazásazonosító URI-t** az alkalmazás egyedi azonosítója. Az egyezmény az, hogy használja a következő formátumot `https://<tenant-domain>/<app-name>`, például: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Az a **beállítások** -> **válasz URL-címek** oldalon az alkalmazás a bejelentkezési URL-az 5. lépésben hozzáadott URL-címet, majd kattintson a mentés.

9. Titkos kulcs létrehozásához kövesse a 4. lépés [alkalmazás hitelesítő adatait, vagy a webes API-k elérésére jogosult hozzáadandó](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Másolja az alkalmazás kulcs értékét. Ez az az érték a `clientSecret`, amelyre szüksége **3. lépés** alatt. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>2. lépés: A címtár Előfeltételek felvétele
1. A parancssorból módosítsa a könyvtárat a gyökérmappára, ha még nem létezik, és futtassa a következő parancsokat:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Emellett szüksége `passport-azure-ad`:
    * `npm install passport-azure-ad`

Ez telepíti a kódtárakat, amely `passport-azure-ad` függ.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. lépés: Állítsa be az alkalmazás használata a passport-node-js stratégia
Itt Express, az OpenID Connect hitelesítési protokoll használatára konfigurálja azt. A Passport szolgál, így rengeteg mindent kezdhet, beleértve a probléma bejelentkezési és kijelentkezési kérések, kezelheti a felhasználói munkamenetek és a felhasználó adatainak beolvasása.

1. A kezdéshez nyissa meg a `config.js` fájlt a projekt gyökérkönyvtárában, és adja meg az alkalmazás konfigurációs értékeit az `exports.creds` szakaszban.

  * A `clientID` van a **alkalmazásazonosító** az alkalmazáshoz a regisztrációs portálon rendelt.

  * A `returnURL` van a **válasz URL-cím** , amely a portálon megadott.

  * A `clientSecret` a portálon létrehozott titkos kulcs.

2. Ezután nyissa meg a `app.js` fájlt a projekt gyökérkönyvtárában található. Ezután adja hozzá a következő hívást meghívása a `OIDCStrategy` stratégiára, amely együttműködik a `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Ezt követően használja a stratégiát, hogy csak a bejelentkezési kérések kezelésére hivatkozott.

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
A Passport az összes stratégia esetében (Twitter, Facebook és így tovább), amely az összes stratégiafejlesztő által hasonló mintát alkalmaz. Megnézzük a stratégiát, láthatja, hogy adjuk át azt egy függvényt, amely rendelkezik egy jogkivonatot, és ennek paraméterei egy kész. A stratégia ismét velünk a kapcsolatot után annak működik. A Microsoft kívánja tárolni a felhasználót, és a jogkivonat Panelcsoport, így nem szükséges kelljen megismételni.

> [!IMPORTANT]
Az előző kód időt vesz igénybe, azok a felhasználók, a kiszolgáló hitelesítésére történik. Ez az úgynevezett automatikus regisztráció. Azt javasoljuk, hogy ne engedélyezze, hogy bárki azokat egy üzemi kiszolgálóra első kellene őket egy folyamat, amely dönt is keresztül regisztrálniuk használata nélkül végezzen hitelesítést. Ez általában a a megoldást látjuk a fogyasztói alkalmazásoknál, amelyek lehetővé teszik, hogy regisztrálja a Facebookkal, de aztán további információt adhat meg. Ha ez nem egy mintaalkalmazást, hogy sikerült rendelkezik kinyert a felhasználó e-mail címét a jogkivonat-objektumból, amely adott vissza, és ezután kéri a felhasználót, hogy adjon meg további információkat. Mivel ez egy tesztkiszolgálón, hozzáadjuk őket a memóriában lévő adatbázishoz.


4. Ezután vegyünk fel a módszereket, amelyek lehetővé teszik számunkra, hogy a Passport által előírt, bejelentkezett felhasználók nyomon követése. Ezek a metódusok lehetnek szerializálása és deszerializálása a felhasználói adatokat.

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

5. Következő lépésként adjunk hozzá a kódot az Express motor betöltése. Itt az alapértelmezett /views használjuk, és Express /routes minta biztosít.

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

6. Utolsó lépésként adjunk az útvonalakat, amelyek kiosztják a tényleges bejelentkezési kéréseket a `passport-azure-ad` motor:

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


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. lépés: Használata a Passport a bejelentkezési és kijelentkezési kérések kiállítása az Azure AD
Az alkalmazás most már megfelelően van konfigurálva, az OpenID Connect hitelesítési protokoll használatával kommunikálnak a végpontot. `passport-azure-ad` rendelkezik az összes adat elvégezte a hitelesítési üzenetek, az Azure AD érkező jogkivonatok érvényességének és karbantartása a felhasználói munkamenetek elvégzi. Hogy az összes jogosultságot ad a felhasználók bejelentkezés és kijelentkezés, és a bejelentkezett felhasználóval kapcsolatos további információk összegyűjtéséhez.

1. Első lépésként adjunk hozzá az alapértelmezett, bejelentkezési, fiókkal és kijelentkezési metódusokat az `app.js` fájlt:

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

2. Vizsgáljuk meg részletesebben ezeket:

  * A `/`útvonal átirányítja a felhasználót a index.ejs nézetben, a felhasználó átadja a kérésben (ha létezik).
  * A `/account` útvonal először *biztosítja, hogy a rendszer hitelesíti* (megvalósítása, amely a következő példában), és majd a rendszer átadja a felhasználó a kérelmet, hogy a felhasználó további információt kapunk.
  * A `/login` útvonal hívja meg az Azure ad-openidconnect hitelesítő `passport-azuread`. Ha nem jár sikerrel, amely, vissza a Publication átirányítja a felhasználót.
  * A `/logout` útvonal egyszerűen hívja a logout.ejs (és útvonal), amely törli a cookie-kat, és ezután visszaküldi a felhasználó index.ejs.

3. Az utolsó részét `app.js`, adjuk hozzá a **EnsureAuthenticated** használt módszer `/account`, ahogyan korábban.

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

4. Végezetül hozzunk létre magát a kiszolgálót a `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>5. lépés: A felhasználónak megjelennek a webhely, hozzon létre nézetek és útvonalak az Express
Most már `app.js` befejeződött. Egyszerűen csak hozzá kell adnunk a az útvonalakat és nézeteket, azt mutatják be az adatokat, hogy a felhasználó első, valamint kezeli a `/logout` és `/login` létrehozott útvonalakkal.

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

3. Hozza létre a gyökérkönyvtárban a `/views/index.ejs` útvonalat. Ez az egy egyszerű lap, amely meghívja a bejelentkezési és kijelentkezési metódusokat, és lehetővé teszi számunkra, a fiókadatok. Figyelje meg, hogy a feltételes hozzáférési használhatók `if (!user)` felhasználóként a kérelemben szereplő átadott keresztül bizonyíték van bejelentkezett felhasználó.

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

4. Hozzon létre a `/views/account.ejs` megtekintése a gyökérkönyvtárban, így további információk megtekinthetők, amelyek `passport-azure-ad` állapotúra állította a felhasználói kérelem.

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

5. Tekintsük meg a jó elrendezés hozzáadásával. Hozzon létre a "/ views/layout.ejs' nézetet a gyökérkönyvtárban.

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

## <a name="next-steps"></a>További lépések
Végül hozhat létre, és futtassa az alkalmazást. Futtatás `node app.js`, majd lépjen `http://localhost:3000`.

Jelentkezzen be személyes Microsoft-fiókkal vagy a munkahelyi vagy iskolai fiókkal, és figyelje meg, hogyan a felhasználó identitását a /account listában is megjelenik. Most már rendelkezik egy webalkalmazást, amely az iparági szabványos protokollok, amely hitelesíteni tudja a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók védi.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [.zip-fájlban is letöltheti](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Másik lehetőségként klónozhatja a Githubról:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Akkor most már továbbléphet az összetettebb témákra. Előfordulhat, hogy szeretné próbálni:

[Védelem biztosítása webes API Azure AD-vel](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
