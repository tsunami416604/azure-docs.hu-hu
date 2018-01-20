
## <a name="setting-up-your-web-server-or-project"></a>A webkiszolgáló vagy a projekt létrehozása

> Ez a minta-projekt letöltése helyette inkább? 
> - [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> vagy
> - [Töltse le a projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) helyi webkiszolgáló, például a Python
>
> Majd folytassa a és a [konfigurációs lépés](#create-an-application-express) a kódminta konfigurálása előtt futtatnia kell.

## <a name="prerequisites"></a>Előfeltételek
Például egy helyi webkiszolgálót [Python http.server](https://www.python.org/downloads/), [http-kiszolgáló](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core), vagy az IIS Express integrációja [Visual Studio 2017](https://www.visualstudio.com/downloads/) Ez az interaktív telepítés futtatásához szükséges. 

Ez az útmutató utasításait a Python és a Visual Studio 2017 alapulnak, de szabadon használhatja a fejlesztési környezet vagy webkiszolgálón.

## <a name="create-your-project"></a>A projekt létrehozása 

> ### <a name="option-1-visual-studio"></a>Option 1: Visual Studio 
> Ha a Visual Studio használ, és a rendszer létrehoz egy új projektet, kövesse az alábbi lépéseket egy új Visual Studio megoldás létrehozásához:
> 1.    A Visual Studio:`File` > `New` > `Project`
> 2.    A `Visual C#\Web`, jelölje be`ASP.NET Web Application (.NET Framework)`
> 3.    Az alkalmazás neve, és kattintson a *OK*
> 4.    A `New ASP.NET Web Application`, jelölje be`Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>2. lehetőség: Python vagy egyéb webkiszolgálókon
> Győződjön meg arról, hogy a telepített [Python](https://www.python.org/downloads/), majd hajtsa végre az alábbi:
> - Hozzon létre egy mappát, az alkalmazás futtatásához.


## <a name="create-your-single-page-applications-ui"></a>Az egyetlen oldal alkalmazás felhasználói Felületüket létrehozni
1.  Hozzon létre egy *index.html* a JavaScript SPA fájlt. Ha a Visual Studio használ, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal, és válassza: `Add`  >  `New Item`  >  `HTML page` és adjon neki nevet index.html
2.  Adja hozzá a következő kódot a lap:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
