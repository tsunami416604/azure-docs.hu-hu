---
title: "Bejelentkezés felvétele Node.js-webalkalmazásokba az Azure B2C-ben | Microsoft Docs"
description: "Node.js webalkalmazás létrehozása, amely a B2C-bérlő segítségével képes bejelentkeztetni a felhasználókat."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: c85b8f8434d1e837ac96ac63b9b37f990677ed6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Bejelentkezés felvétele Node.js-webalkalmazásokba

A **Passport** a Node.js-hez készült közbenső hitelesítési szoftver. A rendkívül rugalmasan működő, moduláris Passport gyakorlatilag bármely Express- vagy Restify-alapú webalkalmazásba diszkréten telepíthető. A program számos különböző lehetőséget kínál a felhasználók hitelesítésére: felhasználónév/jelszó, Facebook- vagy Twitter-fiók és így tovább.

Kidolgoztunk egy stratégiát, amellyel a szoftver az Azure Active Directory (Azure AD) esetében is felhasználható. Először telepítenie kell a modult, majd hozzá kell adni az Azure AD `passport-azure-ad` bővítményt.

Ehhez a következőket kell tennie:

1. Alkalmazás regisztrálása az Azure AD használatával.
2. Az alkalmazás beállítása a `passport-azure-ad` bővítmény használatára.
3. Be- és kijelentkezési kérések kiállítása az Azure AD számára a Passport használatával.
4. Felhasználói adatok kinyomtatása.

Az oktatóanyag kódjának [kezelése a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). A lépések követéséhez [töltse le az alkalmazás vázát .zip-fájlként](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). A vázprojektet klónozhatja is:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Az oktatóanyag végén az elkészült alkalmazást is megtalálja.

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt.  A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és sok minden mást. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne ebben az útmutatóban.

## <a name="create-an-application"></a>Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az ügyfélalkalmazást és a webes API-t is egyetlen **alkalmazásazonosító** képviseli, mivel a két elem közös logikai alkalmazássá áll össze. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

- Az alkalmazás tartalmazzon egy **webalkalmazást**/**webes API-t**.
- A **Reply URL** (Válasz URL-cím) legyen a következő: `http://localhost:3000/auth/openid/return`. Ez a kódminta alapértelmezett URL-címe.
- Hozzon létre egy **alkalmazástitkot** az alkalmazáshoz, majd másolja. Erre később még szüksége lesz. Ne feledje, hogy az értékben használat előtt [az XML-nek megfelelő feloldójelekkel](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) kell megjelölni a vezérlőkaraktereket.
- Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Később erre is szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Szabályzatok létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Az alkalmazás három különböző, identitással kapcsolatos műveletet tartalmaz: regisztráció, bejelentkezés és bejelentkezés Facebook-fiókkal. Az összes típushoz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy) leírtak szerint. A három szabályzat létrehozásakor ügyeljen arra, hogy:

- A regisztrációs szabályzatban adja meg a **Megjelenített név** értékét, illetve az egyéb regisztrációs attribútumokat.
- Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet. Ezenfelül más jogcímeket is használhat.
- Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A házirendek nevére később még szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

Felhívjuk figyelmét, hogy ebben a cikkben nem foglalkozunk a létrehozott szabályzatok használatával. Ha szeretné megismerni a szabályzatok Azure AD B2C alatti működését, végezze el a [.NET-es webalkalmazások használatába bevezető oktatóanyagot](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Előfeltételek felvétele a könyvtárba

A parancssorból módosítsa a könyvtárat a gyökérmappára, ha még nem tette meg. Futtassa az alábbi parancsot:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Az útmutatóban található vázban szereplő előzetes verzióban ezek mellett a `passport-azure-ad`-t is használtuk.

- `npm install passport-azure-ad`

Ez telepíti a kódtárakat, amelyektől a `passport-azure-ad` függ.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Az alkalmazás beállítása a Passport-Node.js stratégia használatára
Állítsa be az Express közbenső szoftvert az OpenID Connect hitelesítési protokoll használatára. A rendszer többek között a Passport használatával fogja kiállítani a be- és kijelentkezési kéréseket, kezelni a felhasználói munkameneteket, valamint lekérni a felhasználókra vonatkozó információkat.

Nyissa meg a projekt gyökerében található `config.js` fájlt, és adja meg az alkalmazás konfigurációs értékeit az `exports.creds` részben.
- `clientID`: az alkalmazáshoz a regisztrációs portálon rendelt **alkalmazásazonosító**.
- `returnURL`: a portálon megadott **átirányítási URI**.
- `tenantName`: az alkalmazáshoz tartozó bérlő neve (például **contoso.onmicrosoft.com**).

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Nyissa meg a projekt gyökerében található `app.js` fájlt. Adja hozzá a következő hívást a `passport-azure-ad`-hez tartozó `OIDCStrategy` stratégia meghívásához.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

A bejelentkezési kérések kezelésére használja a korábban említett stratégiát.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
A Passport az összes stratégia (ideértve a Twittert és a Facebookot is) esetében hasonló mintát alkalmaz. A stratégiafejlesztők általában ehhez a mintához tartják magukat. Ha megnézzük a stratégiát, láthatjuk, hogy át kell adni neki egy `function()` elemet, ennek paraméterei egy jogkivonat és egy `done`. Ha a stratégia elvégezte teendőit, visszatér Önhöz. Érdemes tárolni a felhasználót és menteni a jogkivonatot, hogy a műveletet ne kelljen megismételni.

> [!IMPORTANT]
Az előzőekben látható kód befogadja a kiszolgáló által hitelesített összes felhasználót. Ezt nevezzük automatikus regisztrációnak. Éles kiszolgálók használata során általában csak akkor érdemes beengedni a felhasználókat, ha már elvégezték az Ön által előírt regisztrációs folyamatot. Ez gyakori eljárás a fogyasztói alkalmazásoknál. Ezek az alkalmazások sok esetben lehetővé teszik a facebookos regisztrációt, de aztán további adatok megadását is kérik. Ha az alkalmazás nem csupán mintaként szolgálna, most kinyerhetnénk a visszaküldött jogkivonat-objektumból az e-mail-címet, majd megkérhetnénk a felhasználót, hogy adjon meg további információkat. Mivel a kiszolgáló csak tesztelésre szolgál, egyszerűen hozzáadjuk a felhasználókat a memóriában lévő adatbázishoz.

Adja hozzá a metódusokat, amelyek lehetővé teszik a Passport által előírt bejelentkezést elvégző felhasználók nyomon követését. Ehhez tartozik a felhasználói adatok szerializálása és deszerializálása is:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Adja hozzá a kódot az Express motor betöltése érdekében. Az alábbiakban megfigyelheti, hogy az Express által biztosított alapértelmezett `/views` és `/routes` mintákat használjuk.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Adja hozzá a `POST` útvonalakat, amelyek kiosztják a tényleges bejelentkezési kéréseket a `passport-azure-ad` motornak:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Be- és kijelentkezési kérések küldése az Azure AD-nek a Passport használatával

Az alkalmazás mostantól az OpenID Connect hitelesítési protokoll segítségével képes kommunikálni a v2.0-végponttal. `passport-azure-ad` már elvégezte a hitelesítési üzenetek létrehozását, az Azure AD-tól érkező jogkivonatok érvényességének ellenőrzését, valamint a felhasználói munkamenetek fenntartását. Most már csupán módot kell biztosítani a felhasználóknak a be- és kijelentkezésre, valamint további információkat kell gyűjteni a bejelentkező felhasználókról.

Első lépésként adja hozzá az alapértelmezett, bejelentkezési, fiókkal kapcsolatos és kijelentkezési metódusokat az `app.js` fájlhoz:

```JavaScript

//Routes (Section 4)

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

A metódusok részletes leírása:
- Az `/` útvonal az `index.ejs` nézetre mutató átirányítást tartalmaz, illetve átadja a kérésben szereplő felhasználót (ha az létezik).
- Az `/account` útvonal először ellenőrzi, hogy a felhasználó átment-e a hitelesítésen (ennek implementációja alább található). Az útvonal ezt követően átadja a kérésben szereplő felhasználót, hogy Ön további információkat szerezhessen róla.
- A `/login` útvonal hívja meg a `passport-azure-ad`-ben található `azuread-openidconnect` hitelesítőt. Ha nem jár sikerrel, az útvonal visszairányítja a felhasználót a `/login`-hoz.
- `/logout` egyszerűen meghívja a `logout.ejs`-t (és annak útvonalát). Ez törli a cookie-kat, és visszairányítja a felhasználót az `index.ejs`-hez.


Az `app.js` utolsó részeként adja hozzá az `/account` útvonalban használt `EnsureAuthenticated` metódust.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Végül hozza létre magát a kiszolgálót is az `app.js`-ben.

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>A szabályzatokat meghívó nézetek és útvonalak létrehozása az Expressben

Az `app.js` ezzel elkészült. Már csak hozzá kell adnia az útvonalakat és nézeteket, amelyek lehetővé teszik a bejelentkezési és regisztrációs szabályzatok meghívását. Ezek kezelik a létrehozott `/logout` és `/login` útvonalakat is.

Hozza létre a gyökérkönyvtárban a `/routes/index.js` útvonalat.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Hozza létre a gyökérkönyvtárban a `/routes/user.js` útvonalat.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Ezek az egyszerű útvonalak adják át a kéréseket a nézeteknek. Ha elérhető felhasználó, azt is tartalmazzák.

Hozza létre a gyökérkönyvtárban a `/views/index.ejs` útvonalat. Ez egy egyszerű lap, amely a bejelentkezési és kijelentkezési szabályzatok meghívását végzi. Ezenfelül a fiókadatok beszerzésére is használható. Felhívjuk figyelmét, hogy a felhasználónak egy kéréssel történő átadása közben a feltételes `if (!user)` segítségével megbizonyosodhat róla, hogy a felhasználó bejelentkezett-e.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Hozza létre a `/views/account.ejs` nézetet a gyökérkönyvtárban, amely lehetővé teszi a `passport-azure-ad` által a felhasználói kérésbe helyezett további információk megtekintését.

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
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Most már lefordíthatja, és futtathatja az alkalmazást.

Futtassa a `node app.js`-t, és nyissa meg a következő oldalt: `http://localhost:3000`


Regisztráljon az alkalmazásra e-mail vagy Facebook használatával, majd jelentkezzen be. Jelentkezzen ki, majd jelentkezzen be ismét egy másik felhasználóval.

##<a name="next-steps"></a>Következő lépések

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [.zip-fájlban is letöltheti](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Ezenfelül a GitHubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Most már továbbléphet az összetettebb témákra. Próbálkozzon meg a következőkkel:

[Védelem biztosítása webes API-k számára a Node.js-hez készült B2C-modell segítségével](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
