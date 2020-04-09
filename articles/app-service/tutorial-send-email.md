---
title: 'Oktatóanyag: e-mail küldése a Logic Apps alkalmazásokkal'
description: Ismerje meg, hogyan hívhatja meg az üzleti folyamatokat az App Service-alkalmazásból. E-maileket, tweeteket és Facebook-bejegyzéseket küldhet, levelezési listákat adhat hozzá és még sok mást.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 44f2dfb83e96e1d8fa31ee1acf350193b954dbd8
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892852"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Oktatóanyag: E-mail küldése és más üzleti folyamatok meghívása az App Service-ből

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az App Service-alkalmazást az üzleti folyamataival. Ez gyakori a webalkalmazás-forgatókönyvek, például:

- Visszaigazoló e-mail küldése tranzakcióhoz
- Felhasználó hozzáadása a Facebook-csoporthoz
- Csatlakozzon külső rendszerekhez, például az SAP-hoz, a SalesForce-hoz stb.
- Exchange-szabványú B2B-üzenetek

Ebben az oktatóanyagban az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)használatával e-maileket küldhet a Gmaillel az App Service-alkalmazásból. A webalkalmazásból más módon is küldhet e-maileket, például a nyelvi keretrendszer által biztosított SMTP-konfiguráció. A Logic Apps azonban sokkal nagyobb teljesítményt nyújt az App Service-alkalmazásnak anélkül, hogy a kód összetettségét hozzáadná. A Logic Apps egy egyszerű konfigurációs felületet biztosít a legnépszerűbb üzleti integrációkhoz, és az alkalmazás bármikor hívhatja őket HTTP-kérelemmel.

## <a name="prerequisite"></a>Előfeltétel

Telepítsen egy alkalmazást az Ön által választott nyelvi keretrendszerrel az App Service-be. A mintaalkalmazás üzembe helyezéséhez az alábbi útmutató követéséhez lásd alább:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database használatával](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása az Azure App Service-ben](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Oktatóanyag: Node.js és MongoDB alkalmazás létrehozása az Azure-ban](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Oktatóanyag: PHP- és MySQL-alkalmazás létrehozása az Azure-ban](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Oktatóanyag: Python (Django) webalkalmazás futtatása a PostgreSQL szolgáltatással az Azure App Service-ben](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Ruby és Postgres alkalmazás létrehozása linuxos Azure App Service-ben](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)című útmutató utasításait követve. Amikor megjelenik a **Logic Apps Designer**, térjen vissza az oktatóanyaghoz.
1. A Logic Apps Designer kezdőlapján jelölje be a **HTTP-kérelem fogadásának ideje** lehetőséget **a Kezdés egy közös eseményindítóval**csoportban.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. A **HTTP-kérelem beérkezésekor**párbeszédpanelen jelölje be **a Mintahasznos adat használata séma létrehozásához**jelölőnégyzetet.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Másolja a következő JSON mintát a szövegdobozba, és válassza a **Kész**lehetőséget.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    A séma most jön létre a kívánt kérelem adatokat. A gyakorlatban csak rögzítheti az alkalmazáskód által létrehozott tényleges kérelemadatokat, és lehetővé teheti az Azure számára a JSON-séma létrehozásához. 
1. A Logic Apps Designer tetején válassza a **Mentés**lehetőséget. 

    Most már láthatja a HTTP-kérelem eseményindítóurl-címét. Válassza a másolás ikont, hogy másolja későbbi használatra.

    ![](./media/tutorial-send-email/http-request-url.png)

    Ez a HTTP-kérelem-definíció minden, amit ebben a logikai alkalmazásban szeretne megtenni, legyen az Gmail vagy bármi más. Később ezt az URL-címet az App Service-alkalmazásban fogja meghívni. A kérelemeseményindítóval kapcsolatos további információkért tekintse meg a [HTTP-kérelem/válasz hivatkozást.](../connectors/connectors-native-reqres.md)

1. A tervező alján kattintson az **Új lépés**gombra, írja be a **Gmail** kifejezést a műveletek keresőmezőjébe, és keresse meg az **E-mail küldése (V2)** lehetőséget.
    
    > [!TIP]
    > Más típusú integrációkat is kereshet, például a SendGridet, a MailChimp-et, az Office 365-öt és a SalesForce-ot. További információt a [Logic Apps dokumentációjában](https://docs.microsoft.com/azure/logic-apps/)talál.
1. A **Gmail** párbeszédpanelen válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be arra a Gmail-fiókra, amelyről az e-mailt el szeretné küldeni.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Miután bejelentkezett, kattintson a **Címzett** mezőbe, és a dinamikus tartalom párbeszédpanel automatikusan megnyílik.

1. **A HTTP-kérelem beérkezésekor** művelet mellett válassza a **Tovább**lehetőséget.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Most látnia kell a minta JSON-adatok által korábban használt három tulajdonságot. Ebben a lépésben használja ezeket a tulajdonságokat a HTTP-kérelem egy e-mailt.
1. Ha a **To** mező értékét választja, válassza az **e-mailt**. Ha szeretné, kapcsolja ki a dinamikus tartalom párbeszédpanelt a **Dinamikus tartalom hozzáadása**gombra kattintva.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Az **Új paraméter hozzáadása** legördülő menüben válassza a **Tárgy** és **a Törzs**lehetőséget.

1. Kattintson a **Tárgy** szövegmezőbe, és ugyanígy válassza a **tevékenység**lehetőséget. Ha a kurzor még mindig a **Tárgy** mezőben van, írja be a *létrehozva*a következőt. 

1. Kattintson a **Törzs**elemre, és ugyanúgy válassza a **Due**lehetőséget. A kurzor mozgatása a esedékesség után **balra,** és írja *be: Ez a munkaelem a mezőben esedékes.*

    > [!TIP]
    > Ha a HTML-tartalmat közvetlenül az e-mail törzsében szeretné szerkeszteni, válassza a **Kód nézet** lehetőséget a Logic Apps Designer ablak tetején. Csak győződjön meg róla, hogy megőrzi `@{triggerBody()?['due']}`a dinamikus tartalom kódot (például )
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Ezután adjon hozzá egy aszinkron HTTP-választ a HTTP-eseményindítóhoz. A HTTP-eseményindító és a **+** Gmail-művelet között kattintson a jelre, és válassza **a Párhuzamos ág hozzáadása**lehetőséget.

    ![](./media/tutorial-send-email/add-http-response.png)

1. A keresőmezőben keressen **választ,** majd jelölje ki a **Válasz** műveletet.

    ![](./media/tutorial-send-email/choose-response-action.png)

    Alapértelmezés szerint a válaszművelet HTTP 200-at küld. Ez elég jó ez a bemutató. További információt a [HTTP-kérelem/válasz hivatkozás című témakörben talál.](../connectors/connectors-native-reqres.md)

1. A Logic Apps Designer tetején válassza a **Mentés** újra lehetőséget. 

## <a name="add-http-request-code-to-app"></a>HTTP-kérelemkód hozzáadása az alkalmazáshoz

Győződjön meg arról, hogy korábban másolta a HTTP-kérelem eseményindítójának URL-címét. Mivel bizalmas információkat tartalmaz, ajánlott, hogy ne helyezze azt közvetlenül a kódba. Az App Service segítségével az alkalmazásbeállítások használatával környezeti változóként hivatkozhat rá. 

A [Cloud Shell](https://shell.azure.com)alkalmazásban hozza létre az alkalmazásbeállítást a következő paranccsal * \<(az alkalmazásnév>, * * \<az erőforráscsoport-név>* és * \<a logic-app-url>) *paranccsal:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

A kódban készítsen szabványos HTTP-bejegyzést az URL-címre bármely, a nyelvi keretrendszer számára elérhető HTTP-ügyfélnyelv használatával, a következő konfigurációval:

- A kérelem törzse ugyanazt a JSON formátumot tartalmazza, amelyet a logikai alkalmazásnak adott meg:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- A kérelem tartalmazza `Content-Type: application/json`a címsort. 
- A teljesítmény optimalizálása érdekében küldje el a kérelmet aszinkron módon, ha lehetséges.

Egy példa megtekintéséhez kattintson az alábbi előnyben részesített nyelv/keretrendszer fülre.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

A ASP.NET a HTTP-bejegyzést a [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) osztályral küldheti el. Például:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Ha teszteli ezt a kódot a minta alkalmazás [oktatóanyag: Hozzon létre egy ASP.NET alkalmazást az Azure-ban az SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)segítségével, akkor [használhatja,](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)hogy küldjön egy e-mail megerősítést a Create művelet, az `Todo` elem hozzáadása után. A fenti aszinkron kód használatához konvertálja a Create műveletet aszinkrondá.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

A ASP.NET Core alkalmazásban elküldheti a HTTP-bejegyzést a [System.Net.Http.HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) osztállyal. Például:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Ez a kód van írva az egyszerűség bizonyítás. A gyakorlatban ne példányosítson el egy `HttpClient` objektumot minden egyes kérelemhez. Kövesse a [Use IHttpClientFactory" útmutatót rugalmashttps://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient HTTP-kérelmek megvalósításához]( -http-requests).

Ha teszteli ezt a kódot a minta app [oktató: Hozzon létre egy ASP.NET Core és az SQL Database alkalmazást az Azure App Service-ben,](app-service-web-tutorial-dotnetcore-sqldb.md)akkor használhatja, hogy küldjön egy e-mailt megerősítést a [Create művelet,](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)miután az `Todo` elem hozzáadása.

# <a name="nodejs"></a>[Node.js](#tab/node)

A Node.js, akkor küldje el a HTTP post könnyen egy npm csomagot, mint [axiók](https://www.npmjs.com/package/axios). Például:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Ha ezt a kódot az oktatóanyagminta alkalmazásában [teszteli: Node.js és MongoDB alkalmazást hozhat létre az Azure-ban,](app-service-web-tutorial-nodejs-mongodb-app.md)akkor a [cikk sikeres mentése](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)után e-mailben is megerősítést küldhet a [létrehozási funkcióban.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)

# <a name="php"></a>[PHP](#tab/php)

A PHP, akkor küldje el a HTTP post könnyen [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Például:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Ha ezt a kódot az oktatóanyagminta alkalmazásában [teszteli: PHP- és MySQL-alkalmazást hozhat létre az Azure-ban,](app-service-web-tutorial-php-mysql.md)akkor e-mailben is elküldheti a [Route::post függvényt,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)közvetlenül a visszatérési utasítás előtt.

# <a name="python"></a>[Python](#tab/python)

A Pythonban a HTTP-bejegyzést egyszerűen elküldheti [kérésekkel.](https://pypi.org/project/requests/) Például:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Ha ezt a kódot teszteli a mintaalkalmazás [oktató: Futtasson egy Python (Django) webapp postgreSQL az Azure App Service,](containers/tutorial-python-postgresql-app.md)használhatja, hogy küldjön egy e-mail megerősítést a [Route::post függvény,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)közvetlenül a visszatérési utasítás előtt.

# <a name="ruby"></a>[Ruby](#tab/ruby)

A Ruby, akkor küldje el a HTTP post könnyen [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Például:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Ha ezt a kódot teszteli a mintaalkalmazásban a [Ruby és a Postgres alkalmazás létrehozása](containers/tutorial-ruby-postgres-app.md)az Azure App Service Linux on, akkor használhatja, hogy küldjön egy e-mail megerősítést a [létrehozási](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) művelet, [ha @task.save sikerül.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

# <a name="more-resources"></a>További erőforrások

[Oktatóanyag: CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben](app-service-web-tutorial-rest-api.md)  
[HTTP-kérelem/válasz hivatkozás a logikai alkalmazásokhoz](../connectors/connectors-native-reqres.md)  
[Rövid útmutató: Az első munkafolyamat létrehozása az Azure Logic Apps használatával – Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)