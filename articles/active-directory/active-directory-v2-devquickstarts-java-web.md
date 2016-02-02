<properties
    pageTitle="App 模型 v2.0 Node.js Web App | Microsoft Azure"
    description="如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 Node JS Web 應用程式。"
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="brandwe"/>


# 應用程式模型 v2.0 預覽：將登入加入 nodeJS Web App

  > [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。


我們將在此處使用 Passport 來執行下列動作：

- 使用 Azure AD 和 v2.0 App 模型將使用者登入 App。
- 顯示使用者的一些相關資訊。
- 讓使用者登出 App。

**Passport** 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。 我們已為 Microsoft Azure Active Directory 開發一項策略。

為執行此作業，您必須執行下列動作：

1. 註冊應用程式。
2. 設定您的 App 以使用 Passport-azure-ad 策略。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。





本教學課程最後也會提供完整的應用程式。

## 1.註冊應用程式

請確定：

- 將指派給您應用程式的**應用程式識別碼**複製起來，您很快會需要用到這些識別碼。
- 為您的應用程式新增 **Web** 平台。
- 輸入正確的**重新導向 URI**。

## 2.在目錄中新增必要條件

從命令列中，將目錄位置變更至根資料夾 (若目錄位置原本不在該處)，然後執行下列命令：

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

- 

- 


如此會安裝 passport-azure-ad 做為依據的程式庫。

## 3.設定您的 App 以使用 passport-node-js 策略。

我們將在此設定 Express 中介軟體，以使用 OpenID Connect 驗證通訊協定。 Express 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-   
    -   
    -   
    - 

- 


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
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    //scope: config.creds.scope
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
查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。 一旦策略完成所有工作之後，便會盡責地返回。 當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。
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

- 

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

您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 v2.0 端點通訊。  所有剩餘的部分就是為使用者提供一種方式來登入、登出，以及收集關於已登入使用者的其他資訊。

- 

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
    -   
    - 
    - 
    - 

- 

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

- 

```JavaScript

app.listen(3000);
```


## 5.在 Express 中建立檢視與路由以在網站中顯示使用者



- 

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由只會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

- 

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

- 

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

- 最後，可藉由新增版面配置，使它看起來很美觀。 在根目錄底下建立 '/views/layout.ejs' 檢視

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

最後，建置並執行您的應用程式！




使用個人的 Microsoft 帳戶或工作或學校帳戶登入，並注意 /account 清單中使用者身分識別的反映狀態。 您的 Web 應用程式現在使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

## 後續步驟





您現在可以進入更進階的主題。 您可以嘗試：



如需其他資源，請參閱：
- 
- [StackOverflow [azure-active directory 的 「 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)





