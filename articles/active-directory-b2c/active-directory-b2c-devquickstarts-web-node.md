<properties
    pageTitle="將登入功能新增至適用於 Azure B2C 預覽的 Node.js Web 應用程式 | Microsoft Azure"
    description="如何建置使用 B2C 租用戶登入使用者的 Node.js Web 應用程式。"
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
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="brandwe"/>

# B2C 預覽：將登入功能加入至 nodeJS Web 應用程式


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
    本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。  而會著重在如何在使用者已通過驗證後呼叫 Web API。
如果您還沒有這麼做，您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md) 來了解 Azure AD B2C 的基本概念。

**Passport** 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。 我們已為 Microsoft Azure Active Directory 開發一項策略。 我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

若要執行此作業，您需要執行下列動作：

1. 向 Azure AD 註冊應用程式
2. 設定您的 App 來使用 Passport 的 azure-ad-passport 外掛程式。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS)。  若要跟著做，您可以 [下載為.zip 的應用程式的基本架構](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) 或再製基本架構:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

本教學課程最後也會提供完整的應用程式。

> [AZURE.WARNING]   我們 B2C 預覽您必須使用相同的用戶端識別碼/應用程式識別碼和原則，Web API 工作伺服器和用戶端連線到它。 這適用於 iOS 及 Android 教學課程。 如果您先前已在任何一個快速入門中建立過應用程式，請直接使用那些值，無需再如下列所示建立新值。

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄為所有使用者、應用程式、群組等項目的容器。  如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。  用戶端應用程式和 web API 會由單一 **應用程式識別碼** 在此情況下，因為它們組成一個邏輯應用程式。  若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。  請務必

- 包含 **web 應用程式/web api** 應用程式中
- 輸入 `http://localhost/TodoListService` 為 **回覆 URL** -它是此程式碼範例的預設 URL。
- 建立 **應用程式密碼** 應用程式並將它複製下來。  稍後您將會用到此資訊。
- 複製 **應用程式識別碼** ，其指派給您的應用程式。  稍後您也會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [**原則**](active-directory-b2c-reference-policies.md)。  此應用程式包含三種 
身分識別遇到位註冊、 登入，以及使用 Facebook 登入。  您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。  建立您的三個原則時，請務必：

- 選擇 **顯示名稱** 和您註冊的原則中的其他幾個註冊屬性。
- 選擇 **顯示名稱** 和 **物件識別碼** 應用程式宣告每個原則。  您也可以選擇其他宣告。
- 複製 **名稱** 的每個原則建立之後。  其前置詞應該為 `b2c_1_`。  稍後您將需要這些原則名稱。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。  如果您想要深入了解在 Azure AD B2C，原則的運作方式
您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。



## 4.在目錄中新增必要條件

從命令列中，將目錄位置變更至根資料夾 (若目錄位置原本不在該處)，然後執行下列命令：

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

- 此外，我們在快速入門的架構中為「預覽」使用 `passport-azure-ad`。 

- `npm install passport-azure-ad`


如此會安裝 passport-azure-ad 做為依據的程式庫。

## 5.設定您的 App 以使用 passport-node-js 策略。
我們將在此設定 Express 中介軟體，以使用 OpenID Connect 驗證通訊協定。  Express 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-   若要開始，請開啟專案根目錄中的 `config.js` 檔案，並在 `exports.creds` 區段中輸入應用程式的組態值。
    -    `clientID:` 是 **應用程式識別碼** 指派給您的應用程式中註冊入口網站。
    -    `returnURL` 是 **重新導向 URI** 您輸入在入口網站。
    -  `tenantName:` 是 **租用戶名稱** 應用程式，例如 contoso.onmicrosoft.com

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

- 接下來開啟專案根中的 `app.js` 檔案，並新增下列呼叫以叫用與 `passport-azure-ad` 一併使用的 `OIDCStrategy` 策略


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- 之後，請使用我們僅供參考的策略來處理登入要求

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
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
Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。 查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。 一旦策略完成所有工作之後，便會盡責地返回。 當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。

> [AZURE.IMPORTANT]
上述程式碼會讓所有使用者經歷伺服器的驗證。 這就是所謂的自動註冊。 在生產伺服器中，您想要讓所有人都必須先經歷您所決定的註冊過程。 這通常是您在取用者 App 中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。 如果這不是範例應用程式，我們就只能從傳回的權杖物件中擷取電子郵件，然後要求他們填寫其他資訊。 由於這是測試伺服器，因此，我們直接將它們加入至記憶體中的資料庫。

- 接下來，我們會新增方法，依 Passport 所要求，允許我們持續追蹤已登入的使用者。 這包括將使用者資訊序列化和還原序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
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

- 接下來，加入可載入 express 引擎的程式碼。 您會在此處看到我們使用 Express 所提供的預設 /views 和 /routes 模式。

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
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後會加入 POST 路由，這會將實際的登入要求遞交至 `passport-azure-ad` 引擎：

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });
```

## 4.使用 Passport，向 Azure AD 發出登入和登出要求

您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 v2.0 端點通訊。  `passport-azure-ad` 已經處理所有製作驗證訊息、 驗證 Azure ad 的權杖和維護使用者工作階段的瑣碎詳細資料。  所有剩餘的部分就是為使用者提供一種方式來登入、登出，以及收集關於已登入使用者的其他資訊。

- 首先，將預設、登入、帳戶及登出方法加入 `app.js` 檔案：

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

-   讓我們詳細檢閱這些方法：
    -   `/` 路由將重新導向至 index.ejs 檢視，其會在要求中傳遞使用者 (若有的話)
    -  `/account` 路由會先 ***確保我們驗證*** (我們的實作如下)，然後將使用者傳遞要求中，好讓我們可以取得使用者的其他資訊。
    - `/login` 路由將從 `passport-azuread` 呼叫 azuread-openidconnect 驗證器，如果失敗，則會再次將使用者重新導向至 /login
    - `/logout` 會直接呼叫 logout.ejs (以及路由)，其會清除 Cookie，然後讓使用者返回至 index.ejs


- 針對 `app.js` 的最後一個部分，加入可在上述 `/account` 中使用的 EnsureAuthenticated 方法。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- 最後，在 `app.js` 中實際建立伺服器本身：

```JavaScript

app.listen(3000);

```


## 5.建立快速檢視和路由以呼叫原則

我們已完成 `app.js`。 現在僅須新增路由和檢視即可，這兩者可讓我們呼叫登入與註冊原則，並處理已建立的 `/logout` 和 `/login` 路由。

- 在根目錄下方建立 `/routes/index.js` 路由。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 在根目錄下方建立 `/routes/user.js` 路由

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由只會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

- 建立 `/views/index.ejs` 的根目錄下的檢視。 這是登入和登出呼叫我們的原則，並讓我們抓取帳戶資訊的簡單網頁。 請注意，我們可以使用條件式 `if (!user)`，因為在要求中傳遞使用者就證實我們擁有已登入的使用者。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login/?p=your facebook policy">Sign In with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign In With Email</a>
    <a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- 在根目錄下方建立 `/views/account.ejs` 檢視，如此即可檢視 `passport-azuread` 放置於使用者要求的其他資訊。

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

最後，建置並執行您的應用程式！

執行 `node app.js` 並瀏覽至 `http://localhost:3000`


使用電子郵件或 Facebook 註冊或登入應用程式。 登出，再以另一個使用者身分重新登入。



##後續步驟

(不含您的設定值) 已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ，或您可以從 GitHub 複製它:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

您現在可以進入更進階的主題。  您可以嘗試：

[在 node.js 中使用 B2C 模型保護 Web API >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

如需其他資源，請參閱：
您現在可以進入更進階的 B2C 主題。  您可以嘗試：

[從 node.js Web 應用程式呼叫 node.js Web API >>]()

[自訂 B2C 應用程式的 UX >>]()

-->


