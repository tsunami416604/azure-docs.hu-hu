---
title: Node.js Express-webalkalmazás létrehozása az Azure Active Directoryval történő bejelentkezéshez és kijelentkezéshez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Node.js Express MVC-webalkalmazást, amely a bejelentkezéshez az Azure AD-vel van integrálva.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19563f76c261fda1fca53babcb553f2dceeaa345
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990093"
---
# <a name="quickstart-build-a-nodejs-express-web-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Rövid útmutató: Node.js Express-webalkalmazás létrehozása az Azure Active Directoryval történő bejelentkezéshez és kijelentkezéshez

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

A Passport a Node.js-hez készült közbenső hitelesítési szoftver. A rugalmasan működő, moduláris Passport gyakorlatilag bármely Express- vagy Restify-alapú webalkalmazásba diszkréten beépíthető. A program számos különböző lehetőséget kínál a felhasználók hitelesítésére: felhasználónév/jelszó, Facebook- vagy Twitter-fiók és így tovább. Az Azure Active Directory (Azure AD) esetében először telepítenie kell a modult, majd hozzá kell adni az Azure AD `passport-azure-ad` bővítményt.

Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan használhatja a Passportot a következőkre:

* A felhasználó bejelentkeztetése az alkalmazásba az Azure AD használatával.
* A felhasználóval kapcsolatos információk megjelenítése.
* A felhasználó kijelentkeztetése az alkalmazásból.

Egy teljes körű, működő alkalmazás létrehozásához az alábbiakat kell tennie:

1. Az alkalmazás regisztrálása.
2. Az alkalmazás beállítása a `passport-azure-ad` stratégia használatára.
3. Be- és kijelentkezési kérések kiállítása az Azure AD számára a Passport használatával.
4. A felhasználóra vonatkozó adatok kinyomtatása.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként végre kell hajtania az alábbi lépéseket:

* [Töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip).
  
    * Klónozza a vázat:

        ```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

    A rövid útmutató kódjának kezelése a [GitHubon](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS) történik. A rövid útmutató végén az elkészült alkalmazást is megtalálja.

* Rendelkeznie kell egy Azure AD-bérlővel, amelyben felhasználókat hozhat létre, és regisztrálhat egy alkalmazást. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-app"></a>1. lépés: Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az oldal tetején található menüben válassza ki a fiókját. A **Címtár** lista alatt válassza ki azt az Active Directory-bérlőt, amelyben az alkalmazást regisztrálni kívánja.
1. Kattintson a **Minden szolgáltatás** lehetőségre a képernyő bal oldalán található panelen, majd válassza az **Azure Active Directory** elemet.
1. Válassza az **Alkalmazásregisztrációk**, majd a **Hozzáadás** elemet.
1. Kövesse az utasításokat egy **webalkalmazás** és/vagy **WebAPI** létrehozásához.

    * A **név** az alkalmazást írja le a felhasználók számára.
    * A **bejelentkezési URL** az alkalmazás alap URL-címe. A váz alapértelmezett címe: `http://localhost:3000/auth/openid/return`.

1. A regisztrációt követően az Azure AD egy egyedi alkalmazásazonosítót rendel hozzá az alkalmazáshoz. Erre az értékre szükség lesz a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
1. Frissítse az alkalmazásazonosító URI-ját az alkalmazás **Beállítások > Tulajdonságok** lapján. 
    
    Az **Alkalmazásazonosító URI** az alkalmazás egyedi azonosítója. Általánosan elfogadott a `https://<tenant-domain>/<app-name>` formátum használata, például: `https://contoso.onmicrosoft.com/my-first-aad-app`.

1. Az alkalmazás **Beállítások > Válasz URL-címek** oldalán adja hozzá az URL-címet az 5. lépésben megadott bejelentkezési URL-cím alapján, majd kattintson a **Mentés** elemre.
1. Titkos kulcs létrehozásához hajtsa végre az [Alkalmazások hitelesítő adatainak vagy engedélyek hozzáadása a webes API-k elérésére](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis) 4. lépését.

   > [!IMPORTANT]
   > Másolja ki az alkalmazáskulcs értékét. Ez a `clientSecret` értéke, amire az alábbi, **3. lépésben** lesz szüksége. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>2. lépés: Előfeltételek felvétele a könyvtárba

1. Ha még nem tette meg, a parancssorból módosítsa a könyvtárat a gyökérmappára, majd futtassa a következő parancsokat:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

1. Ehhez szükséges a `passport-azure-ad` is, ezért futtassa az alábbi parancsot:

    * `npm install passport-azure-ad`

Ez telepíti a könyvtárakat, amelyektől a `passport-azure-ad` függ.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. lépés: Az alkalmazás beállítása a Passport-Node.js stratégia használatára

Ebben a lépésben ne fogja állítani az Expresst, hogy az OpenID Connect hitelesítési protokollt használja. A Passport számos feladatot ellát, többek között kiállítja a be- és kijelentkezési kéréseket, kezeli a felhasználói munkameneteket, és lekéri a felhasználóra vonatkozó információkat.

1. Nyissa meg a projekt gyökerében található `config.js` fájlt, és adja meg az alkalmazás konfigurációs értékeit az `exports.creds` részben.

    * A `clientID` az alkalmazáshoz a regisztrációs portálon rendelt **alkalmazásazonosító**.
    * A `returnURL` a portálon megadott **válasz URL-cím**.
    * A `clientSecret` a portálon létrehozott titkos kulcs.

1. Ezután nyissa meg a projekt gyökerében található `app.js` fájlt. Majd adja hozzá a következő hívást a `passport-azure-ad` elemet kísérő `OIDCStrategy` stratégia meghívásához.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

1. Ezt követően használhatja a stratégiát a bejelentkezési kérések kezelésére.

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
   A Passport az összes stratégia esetében hasonló mintát alkalmaz (ideértve a Twittert és a Facebookot is), a stratégiafejlesztőknek ehhez kell tartaniuk magukat. Ha megnézi a stratégiát, láthatja, hogy átad neki egy függvényt, amelynek két paramétere egy jogkivonat és a done (kész) állapot. Ha a stratégia elvégezte a feladatát, visszatér Önhöz. Ezután érdemes eltárolni a felhasználót és menteni a jogkivonatot, hogy a műveletet ne kelljen megismételni.

   > [!IMPORTANT]
   > Az előző kód minden, hitelesítésen áteső felhasználót bejuttat a kiszolgálóra. Ezt nevezzük automatikus regisztrációnak. Azt javasoljuk, hogy senkit ne hitelesítsen egy éles kiszolgáló használatára, amíg nem esett át az Ön által meghatározott regisztrációs folyamaton. Általában ezt a mintát látjuk az olyan fogyasztói alkalmazásoknál, amelyek engedélyezik a Facebookkal végzett regisztrációt, de további adatok megadását kérik. Ha ez nem mintaalkalmazás lenne, a visszakapott jogkivonat-objektumból kinyerhette volna a felhasználó e-mail-címét, és megkérhette volna a felhasználót további adatok megadására. Mivel ez csak egy tesztkiszolgáló, a memóriában lévő adatbázishoz adja hozzá őket.

1. Adja hozzá a módszereket, amelyekkel a Passport előírásai szerint nyomon követheti a bejelentkezett felhasználókat. Ilyen módszer a felhasználók adatainak szerializálása és deszerializálása.

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

1. Adja hozzá a kódot az Express motor betöltése érdekében. Itt az Express által biztosított alapértelmezett /views és /routes mintát alkalmazzuk.

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

1. Végül adja hozzá az útvonalakat, amelyek kiosztják a tényleges bejelentkezési kéréseket a `passport-azure-ad` motornak:

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

## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. lépés: Be- és kijelentkezési kérések küldése az Azure AD-nek a Passport használatával

Az alkalmazás mostantól az OpenID Connect hitelesítési protokoll segítségével képes kommunikálni a végponttal. A `passport-azure-ad` már elvégezte a hitelesítési üzenetek létrehozását, az Azure AD-tól érkező jogkivonatok érvényességének ellenőrzését, valamint a felhasználói munkamenetek fenntartását. Most már csupán módot kell biztosítani a felhasználóknak a be- és kijelentkezésre, valamint további információkat kell gyűjteni a bejelentkezett felhasználókról.

1. Adjuk hozzá az alapértelmezett, a bejelentkezési, a fiókkal kapcsolatos és a kijelentkezési metódusokat az `app.js` fájlhoz:

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

1. Tekintsük át ezeket részletesen:

    * A `/` útvonal átirányít az index.ejs nézetre, illetve átadja a kérésben szereplő felhasználót (ha az létezik).
    * Az `/account` útvonal először *gondoskodik róla, hogy hitelesítve legyünk* (ezt a következő fogjuk implementálni), majd átadja a kérésben szereplő felhasználót, hogy további információkat lehessen róla beszerezni.
    * A `/login` útvonal meghívja a `passport-azuread` elemben található azuread-openidconnect hitelesítőt. Ha a meghívás nem jár sikerrel, visszairányítja a felhasználót a /login útvonalra.
    * A `/logout` útvonal egyszerűen meghívja a logout.ejs nézetet (és útvonalat), amely törli a cookie-kat, és visszairányítja a felhasználót az index.ejs nézetre.

1. Az `app.js` utolsó részeként adja hozzá az `/account` útvonalban használt **EnsureAuthenticated** metódust, amint azt korábban bemutattuk.

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

1. Végül hozza létre magát a kiszolgálót is az `app.js` elemben:

    ```JavaScript
    app.listen(3000);
    ```

## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>5. lépés: A nézetek és útvonalak létrehozása az Expressben a felhasználó a webhelyen való megjelenítéséhez

Az `app.js` elkészült. Már csak hozzá kell adnia a kapott információkat megjelenítő útvonalakat és nézeteket a felhasználóhoz, és kezelnie kell a létrehozott `/logout` és `/login` útvonalakat.

1. Hozza létre a gyökérkönyvtárban a `/routes/index.js` útvonalat.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

1. Hozza létre a gyökérkönyvtárban a `/routes/user.js` útvonalat.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

    Ezek továbbítják a kérést a nézeteknek, beleértve a felhasználót is, ha jelen van.

1. Hozza létre a gyökérkönyvtárban a `/views/index.ejs` útvonalat. Ez egy egyszerű lap, amely meghívja a bejelentkezési és kijelentkezési metódusokat, és lehetővé teszi a fiókinformációk megszerzését. Figyelje meg, hogy itt használható a feltételes `if (!user)`, mivel a kérésben átadott felhasználó kellő bizonyíték arra, hogy van bejelentkezett felhasználó.

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

1. Hozza létre a `/views/account.ejs` nézetet a gyökérkönyvtárban, amely lehetővé teszi a `passport-azure-ad` által a felhasználói kérésbe helyezett további információk megtekintését.

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
    <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

1. Adjon hozzá valamilyen elrendezést az oldal megjelenésének javítása érdekében. Hozza létre a gyökérkönyvtárban a `/views/layout.ejs` útvonalat.

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

## <a name="step-6-build-and-run-your-app"></a>6. lépés: Az alkalmazás összeállítása és futtatása

1. Futtassa a `node app.js` parancsot, majd lépjen a `http://localhost:3000` helyre.
1. Jelentkezzen be a személyes Microsoft-fiókjával vagy egy munkahelyi vagy iskolai fiókkal.

    Figyelje meg, hogy az /account lista tükrözi a felhasználó identitását. Egy olyan webalkalmazásra tett így szert, amelynek iparági szabványoknak megfelelő biztonsági protokolljain keresztül a felhasználók személyes és munkahelyi/iskolai fiókjaikkal is hitelesíthetik magukat.

    Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [.zip-fájlban is letöltheti](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/master.zip). Ha szeretné, a GitHubból is klónozhatja:

    ```git clone --branch master https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

## <a name="next-steps"></a>További lépések

Most már továbbléphet, és kipróbálhat más forgatókönyveket is:

> [!div class="nextstepaction"]
> [Webes API védelme az Azure AD-vel](quickstart-v1-nodejs-webapi.md)