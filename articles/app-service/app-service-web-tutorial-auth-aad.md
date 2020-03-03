---
title: 'Oktatóanyag: AuthN/AuthO végpontok közötti'
description: Ez a cikk ismerteti, hogyan használhatja az App Service-hitelesítést és -engedélyezést az App Service-alkalmazások védelmének biztosításához, beleértve a távoli API-khoz történő hozzáférést.
keywords: app service, azure app service, authN, authZ, védelem, biztonság, többrétegű, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 46750383c1436a2d053d6db7fed858c7c0f8a9fe
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226300"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Az App Service továbbá beépített támogatást nyújt a [felhasználók hitelesítéséhez és engedélyezéséhez](overview-authentication-authorization.md). Ebből az oktatóanyagból megtudhatja, hogyan gondoskodhat az alkalmazások védelméről az App Service-hitelesítés és -engedélyezés segítségével. Az oktatóanyag ASP.NET Core-alkalmazást használ Angular.js előtérrendszerrel, de ez csak példaként szolgál. Az App Service-hitelesítés és -engedélyezés támogatja az összes nyelvi futtatókörnyezetet, Ön pedig az oktatóanyag elvégzésével megismerheti, hogyan alkalmazhatja ezt a kívánt nyelvre.

Az oktatóanyag a mintaalkalmazás segítségével ismerteti, hogyan tehet biztonságossá egy önálló alkalmazást (a [Hitelesítés és engedélyezés háttéralkalmazáshoz történő engedélyezéséről](#enable-authentication-and-authorization-for-back-end-app) szóló szakaszban).

![Egyszerű hitelesítés és engedélyezés](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

Azt is bemutatja, hogyan gondoskodhat egy többrétegű alkalmazás védelméről egy védett háttérrendszeri API a hitelesített felhasználó nevében történő elérésével mind a [kiszolgálói kódból](#call-api-securely-from-server-code), mind a [böngészőkódból](#call-api-securely-from-browser-code).

![Speciális hitelesítés és engedélyezés](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

Ez mindössze néhány az App Service-ben elérhető összes lehetséges hitelesítési és engedélyezési eljárás közül. 

Az alábbi lista az oktatóanyagban ismertetett témaköröket tartalmazza:

> [!div class="checklist"]
> * Beépített hitelesítés és engedélyezés engedélyezése
> * Alkalmazások biztonságossá tétele a nem hitelesített kérelmek ellen
> * Az Azure Active Directory használata identitásszolgáltatóként
> * Távoli alkalmazás elérése a bejelentkezett felhasználó nevében
> * Biztonságos szolgáltatások közötti hívások jogkivonat-hitelesítéssel
> * Hozzáférési kódok használata kiszolgálói kódból
> * Hozzáférési kódok használata az ügyfél (böngésző) kódjából

Az oktatóanyag lépései macOS, Linux és Windows rendszerre is vonatkoznak.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Telepítse a Git szoftvert](https://git-scm.com/).
* [Telepítse a .NET Core-t](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Helyi .NET Core-alkalmazás létrehozása

Ebben a lépésben a helyi .NET Core-projektet állíthatja be. Ugyanezen projekt használatával telepít egy háttérrendszeri API-alkalmazást és egy előtér-webalkalmazást.

### <a name="clone-and-run-the-sample-application"></a>A mintaalkalmazás klónozása és futtatása

Az alábbi parancsok futtatásával klónozza, majd futtassa a mintaadattárat.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Navigáljon ide: `http://localhost:5000`, majd adja hozzá, szerkessze vagy távolítsa el a teendőket. 

![Helyileg futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/local-run.png)

Ha bármikor le szeretné állítani az ASP.NET Core-t, nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Alkalmazások telepítése az Azure-ba

Ebben a lépésben üzembe helyezi a projektet két App Service-alkalmazásban. Az egyik az előtéri, a másik a háttéralkalmazás.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

A Cloud Shellben futtassa a következő parancsokat két webalkalmazás létrehozásához. Cserélje le _\<előtér-alkalmazás neve >_ és _\<háttér-alkalmazás-név >_ két globálisan egyedi alkalmazás-névvel (érvényes karakterek `a-z`, `0-9`és `-`). A parancsokról további információt talál a [CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben](app-service-web-tutorial-rest-api.md) című szakaszban.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Mentse a git-távvezérlők URL-címeit az előtér-és háttér-alkalmazásokhoz, amelyek a `az webapp create`kimenetében láthatók.
>

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A _helyi terminálablakba_ visszalépve futtassa a következő Git-parancsokat a háttéralkalmazás telepítéséhez. Cserélje le a _\<deploymentLocalGitUrl-of-back-end-app>_ részt a távoli Git-elem URL-címére, amelyet az [Azure-erőforrások létrehozása](#create-azure-resources) lépés során mentett. Amikor a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, győződjön meg arról, hogy a [központi telepítési hitelesítő](deploy-configure-credentials.md)adatokat adja meg, nem pedig a Azure Portalba való bejelentkezéshez használt hitelesítő adatokat.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

A helyi terminálablakban futtassa a következő Git-parancsokat ugyanezen kódnak az előtér-alkalmazásba történő telepítéséhez. Cserélje le a _\<deploymentLocalGitUrl-of-front-end-app>_ részt a távoli Git-elem URL-címére, amelyet az [Azure-erőforrások létrehozása](#create-azure-resources) lépés során mentett.

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Tallózással keresse meg az alkalmazásokat

Nyissa meg a következő URL-címeket egy böngészőben, és ellenőrizze a két alkalmazás működését.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Ha az alkalmazás újraindul, láthatja, hogy az új adatok törölve lettek. Ez az elvárt működés, ugyanis az ASP.NET Core mintaalkalmazás memóriában lévő adatbázist használ.
>
>

## <a name="call-back-end-api-from-front-end"></a>Háttérrendszeri API meghívása az előtérből

Ebben a lépésben kijelöli az előtér-alkalmazás kiszolgálói kódját, hogy hozzáférjen a háttérrendszeri API-hoz. Később engedélyezni fogja az előtértől a háttérbe irányuló hitelesített hozzáférést.

### <a name="modify-front-end-code"></a>Az előtérkód módosítása

A helyi adattárban nyissa meg a következőt: _Controllers/TodoController.cs_. A `TodoController` osztály elején adja hozzá a következő sorokat, és cserélje le _\<háttér-alkalmazásnév >_ a háttérbeli alkalmazás nevére:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Keresse meg a `GetAll()` metódust, és cserélje le a kapcsos zárójelek közötti kódot a következőre:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Az első sor `GET /api/Todo` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezt követően keresse meg a `GetById(long id)` metódust, és cserélje le a kapcsos zárójelek közötti kódot a következőre:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

Az első sor `GET /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezt követően keresse meg az `Create([FromBody] TodoItem item)` metódust, és cserélje le a kapcsos zárójelek közötti kódot a következőre:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

Az első sor `POST /api/Todo` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezt követően keresse meg az `Update(long id, [FromBody] TodoItem item)` metódust, és cserélje le a kapcsos zárójelek közötti kódot a következőre:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

Az első sor `PUT /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezt követően keresse meg az `Delete(long id)` metódust, és cserélje le a kapcsos zárójelek közötti kódot a következőre:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

Az első sor `DELETE /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>A módosítások ellenőrzése

Nyissa meg a `http://<front-end-app-name>.azurewebsites.net` címet, és adjon hozzá néhány elemet, például a `from front end 1` és `from front end 2` elemet.

Nyissa meg a `http://<back-end-app-name>.azurewebsites.net` címet az előtér-alkalmazásból hozzáadott elemek megtekintéséhez. Továbbá adjon hozzá néhány elemet, például a `from back end 1` és a `from back end 2` elemet, majd frissítse az előtér-alkalmazást annak ellenőrzéséhez, hogy az tükrözi-e a módosításokat.

![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Hitelesítés konfigurálása

Ebben a lépésben engedélyezni fogja a két alkalmazás hitelesítését és engedélyezését. Konfigurálni fogja továbbá az előtér-alkalmazást, hogy létrehozzon egy hozzáférési jogkivonatot, amelynek segítségével hitelesített hívásokat indíthat a háttéralkalmazáshoz.

Az Azure Active Directoryt fogja használni identitásszolgáltatóként. További információért tekintse meg [az Azure Active Directory-hitelesítés az App Services-alkalmazáshoz való konfigurálásáról](configure-authentication-provider-aad.md) szóló részt.

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Hitelesítés és engedélyezés engedélyezése a háttéralkalmazás számára

1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az *erőforráscsoportok* lehetőséget bármely oldalon.

1. Az **erőforráscsoportok**területen keresse meg és válassza ki az erőforráscsoportot. Az **Áttekintés**területen válassza ki a háttérbeli alkalmazás felügyeleti lapját.

   ![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

1. A háttérbeli alkalmazás bal oldali menüjében válassza a **hitelesítés/engedélyezés**lehetőséget, majd **a**bejelöléssel engedélyezze app Service hitelesítést.

1. Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a **Bejelentkezés az Azure Active Directoryval** lehetőséget.

1. A **hitelesítésszolgáltatók**területen válassza a **Azure Active Directory** 

   ![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

1. Válassza az **expressz**lehetőséget, majd fogadja el az alapértelmezett beállításokat új ad-alkalmazás létrehozásához, majd kattintson **az OK gombra**.

1. A **hitelesítés/engedélyezés** lapon válassza a **Mentés**lehetőséget.

   Ha megjelenik az értesítés a `Successfully saved the Auth Settings for <back-end-app-name> App` üzenettel, frissítse a lapot.

1. Válassza a **Azure Active Directory** ismét lehetőséget, majd válassza ki a **Azure ad alkalmazás**.

1. Másolja az Azure AD-alkalmazás **ügyfél-azonosítóját** egy Jegyzettömbbe. Erre az értékre később szüksége lesz.

   ![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Hitelesítés és engedélyezés engedélyezése az előtér-alkalmazás számára

Kövesse ugyanezen lépéseket a háttérbeli alkalmazáshoz, de hagyja ki az utolsó lépést. Nincs szüksége az előtér-alkalmazás ügyfél-AZONOSÍTÓJÁRA.

Igény szerint navigáljon a következőhöz: `http://<front-end-app-name>.azurewebsites.net`. Ez egy biztonságos bejelentkezési oldalra irányítja át. A bejelentkezést követően továbbra sem fog tudni hozzáférni a háttéralkalmazás adataihoz, mert előbb el kell végeznie az alábbi három teendőt:

- Hozzáférés engedélyezése az előtér-alkalmazás számára a háttéralkalmazáshoz
- Az App Service konfigurálása használható jogkivonat visszaadására
- A jogkivonat használata a kódban

> [!TIP]
> Ha hibákba ütközik, és újrakonfigurálja az alkalmazás hitelesítési/engedélyezési beállításait, előfordulhat, hogy a jogkivonat-tároló nem hozza létre újból a jogkivonatokat az új beállításokból. A jogkivonatok újbóli létrehozásának biztosításához ki kell jelentkeznie, majd újból be kell jelentkeznie az alkalmazásba. Ennek egyszerű módja, hogy a böngészőt privát üzemmódban használja, és bezárja, majd újból megnyitja a böngészőt privát üzemmódban az alkalmazások beállításainak módosítása után.

### <a name="grant-front-end-app-access-to-back-end"></a>Hozzáférés engedélyezése az előtér-alkalmazás számára a háttéralkalmazáshoz

Most, hogy engedélyezve van a hitelesítés és az engedélyezés mindkét alkalmazás számára, mindkét alkalmazás AD-alkalmazáson alapul. Ebben a lépésben engedélyeket ad az előtér-alkalmazásnak, hogy hozzáférhessen a háttéralkalmazáshoz a felhasználó nevében. (Voltaképpen az előtér _AD-alkalmazásának_ ad engedélyt a háttér _AD-alkalmazásához_ való hozzáférésre a felhasználó nevében.)

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg, majd válassza a *Azure Active Directory* lehetőséget bármelyik lapon.

1. Válassza **Alkalmazásregisztrációk** > **birtokolt alkalmazások**elemet. Válassza ki az előtér-alkalmazás nevét, majd válassza az **API-engedélyek**lehetőséget.

   ![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

1. Válassza az **engedély hozzáadása**lehetőséget, majd válassza **a saját api** -k >  **\<háttér-alkalmazás-neve >** lehetőséget.

1. A háttérbeli alkalmazáshoz tartozó **API-engedélyek kérése** lapon válassza a **delegált engedélyek** és **User_impersonation**, majd az **engedélyek hozzáadása**elemet.

   ![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Az App Service konfigurálása használható hozzáférési jogkivonat visszaadására

Az előtér-alkalmazás most már rendelkezik a szükséges engedélyekkel, amelyekkel hozzáfér a háttérbeli alkalmazáshoz a bejelentkezett felhasználóként. Ebben a lépésben konfigurálja az App Service-hitelesítést és -engedélyezést, hogy használható jogkivonatot adjon a háttéralkalmazás eléréséhez. Ebben a lépésben a háttér-ügyfél-AZONOSÍTÓra van szüksége, amelyet a [hitelesítés engedélyezése és a háttérbeli alkalmazás](#enable-authentication-and-authorization-for-back-end-app)engedélyezése elemre másolt.

Jelentkezzen be az [Azure Erőforrás-kezelőbe](https://resources.azure.com). Kattintson az **Olvasás/Írás** elemre a lap tetején az Azure-erőforrások szerkesztésének engedélyezéséhez.

![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

A bal oldali böngészőben kattintson az **előfizetések** >  **_\<az előfizetése >_**  > **resourceGroups** > **myAuthResourceGroup** > **szolgáltatók** > **Microsoft. Web** > **helyek** >  **_\<előtér-alkalmazás-neve >_**  > **config** > **authsettings elemre**.

Az **authsettings** nézetben kattintson a **Szerkesztés** gombra. Állítsa `additionalLoginParams` a következő JSON-karakterláncra a másolt ügyfél-azonosító használatával. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

A beállítások mentéséhez kattintson a **PUT** elemre.

Az alkalmazások konfigurálása kész. Az előtér-alkalmazás most már hozzáfér a háttéralkalmazáshoz a megfelelő hozzáférési jogkivonattal.

A más szolgáltatók hozzáférési jogkivonatának konfigurálásával kapcsolatos további információkért lásd: [személyazonosság-szolgáltatói tokenek frissítése](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>API biztonságos hívása a kiszolgálói kódból

Ebben a lépésben engedélyezi a korábban módosított kiszolgálói kód számára, hogy hitelesített hívásokat indítson a háttérrendszeri API irányába.

Az előtér-alkalmazás most már rendelkezik a szükséges engedélyekkel, és hozzáadja a háttér-ügyfél AZONOSÍTÓját is a bejelentkezési paraméterekhez. Ezáltal hozzáférési jogkivonatot szerezhet a háttéralkalmazással történő hitelesítéshez. Az App Service úgy adja meg a jogkivonatot a kiszolgálói kódhoz, hogy injektál egy `X-MS-TOKEN-AAD-ACCESS-TOKEN` fejlécet minden hitelesített kéréshez (lásd az [alkalmazáskódban lévő jogkivonatok lekéréséről](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) szóló részt).

> [!NOTE]
> Ezeket a fejléceket a rendszer minden támogatott nyelven injektálja. Az egyes nyelvek szokásos mintájának használatával férhet hozzájuk.

A helyi adattárban nyissa meg újból a következőt: _Controllers/TodoController.cs_. A `TodoController(TodoContext context)` konstruktorhoz adja hozzá a következő kódot:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Ez a kód hozzáadja a szabványos `Authorization: Bearer <access-token>` HTTP-fejlécet az összes távoli API-híváshoz. Az ASP.NET Core MVC kérelem-végrehajtási folyamatban az `OnActionExecuting` a megfelelő műveleti metódus (például `GetAll()`) előtt közvetlenül fut, így minden kimenő API-hívás bemutatja a hozzáférési jogkivonatot.

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Jelentkezzen be újra az `https://<front-end-app-name>.azurewebsites.net` helyre. A felhasználói adatok használatára vonatkozó megállapodás lapján kattintson az **Elfogadás** gombra.

Ekkor már képesnek kell lennie az adatok létrehozására, olvasására, frissítésére és törlésére a háttéralkalmazásból, mint korábban. Az egyetlen különbség, hogy az App Service hitelesítése és engedélyezése most már mindkét alkalmazást védi, beleértve a szolgáltatások közötti hívásokat is.

Gratulálunk! A kiszolgálói kód most már hozzáfér a háttéralkalmazás adataihoz a hitelesített felhasználó nevében.

## <a name="call-api-securely-from-browser-code"></a>API biztonságos hívása a böngészőkódból

Ebben a lépésben az előtérrendszeri Angular.js alkalmazást a háttérrendszeri API-hoz fogja irányítani. Ezáltal megtudhatja, hogyan kérheti le a hozzáférési jogkivonatot, és hogyan indíthat azzal API-hívásokat a háttéralkalmazás felé.

Míg a kiszolgálói kód hozzáfér a kérelemfejlécekhez, az ügyfélkód ugyanezeket a hozzáférési jogkivonatokat a következőből érheti el: `GET /.auth/me` (lásd az [alkalmazáskódban lévő jogkivonatok lekéréséről](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) szóló részt).

> [!TIP]
> Ez a szakasz a szabványos HTTP-metódusok használatával mutatja be a biztonságos HTTP-hívásokat. Használhatja azonban az [Active Directory Authentication Library (ADAL) JavaScripthez](https://github.com/AzureAD/azure-activedirectory-library-for-js) szolgáltatást is az Angular.js-alkalmazásminta leegyszerűsítéséhez.
>

### <a name="configure-cors"></a>A CORS konfigurálása

A Cloud Shellben engedélyezze a CORS-t az ügyfél URL-címéhez az [`az resource update`](/cli/azure/resource#az-resource-update) paranccsal. Cserélje le a _\<háttér-alkalmazás neve >_ és _\<előtér-végpont-alkalmazás neve >_ helyőrzőket.

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back-end-app-name> --set properties.cors.allowedOrigins="['https://<front-end-app-name>.azurewebsites.net']" --api-version 2015-06-01
```

Ez a lépés nem kapcsolódik a hitelesítéshez és az engedélyezéshez. Azonban szükség van rá ahhoz, hogy a böngésző lehetővé tegye a tartományok közötti API-hívásokat az Angular.js-alkalmazásból. További információ: [CORS-funkció hozzáadása](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js-alkalmazás átirányítása a háttérrendszeri API felé

A helyi adattárban nyissa meg a következőt: _wwwroot/index.html_.

Az 51. sorban állítsa be az `apiEndpoint` változót a háttérrendszeri API URL-címére (`https://<back-end-app-name>.azurewebsites.net`). Cserélje le _\<háttér-alkalmazás neve >_ az alkalmazás nevére a app Serviceban.

Nyissa meg a helyi adattárban a _wwwroot/app/scripts/todoListSvc.js_ fájlt, és ellenőrizze, hogy minden API-hívás a következővel van-e kiegészítve: `apiEndpoint`. Az Angular.js-alkalmazás most már a háttérrendszer API-kat fogja hívni. 

### <a name="add-access-token-to-api-calls"></a>Hozzáférési jogkivonat hozzáadása az API-hívásokhoz

A _wwwroot/app/scripts/todoListSvc.js_ fájlban az API-hívások listája (`getItems : function(){`. sor) fölött adja hozzá a következő függvényt a listához:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

A rendszer ezt a függvényt fogja meghívni az alapértelmezett `Authorization` fejléc a hozzáférési jogkivonattal történő beállításához. A következő lépésben fogja meghívni.

A helyi adattárban nyissa meg a _wwwroot/app/scripts/app.js_ fájlt, és keresse meg a következő kódot:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Cserélje le a teljes kódblokkot az alábbi kódra:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Az új módosítás hozzáadja a `resolve` leképezést, amely a `/.auth/me` meghívása után beállítja a hozzáférési jogkivonatot. Ellenőrzi, hogy rendelkezik-e a hozzáférési jogkivonattal, mielőtt létrehozza a `todoListCtrl` vezérlő példányát. E módon minden, a vezérlő által kezdeményezett API-hívás tartalmazni fogja a jogkivonatot.

### <a name="deploy-updates-and-test"></a>Frissítések telepítése és tesztelése

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Keresse meg újból a következőt: `https://<front-end-app-name>.azurewebsites.net`. Most már képesnek kell lennie az adatok közvetlenül az Angular.js-alkalmazásból történő létrehozására, olvasására, frissítésére és törlésére.

Gratulálunk! Az ügyfélkód most már hozzáfér a háttéradatokhoz a hitelesített felhasználó nevében.

## <a name="when-access-tokens-expire"></a>A hozzáférési jogkivonatok lejártakor

A hozzáférési jogkivonatok bizonyos idő elteltével lejárnak. További információ a hozzáférési jogkivonatok frissítéséről anélkül, hogy a felhasználóknak újra hitelesíteniük kell magukat az alkalmazással kapcsolatban: az [identitás-szolgáltatói tokenek frissítése](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

A parancs futtatása egy percig is eltarthat.

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Beépített hitelesítés és engedélyezés engedélyezése
> * Alkalmazások biztonságossá tétele a nem hitelesített kérelmek ellen
> * Az Azure Active Directory használata identitásszolgáltatóként
> * Távoli alkalmazás elérése a bejelentkezett felhasználó nevében
> * Biztonságos szolgáltatások közötti hívások jogkivonat-hitelesítéssel
> * Hozzáférési kódok használata kiszolgálói kódból
> * Hozzáférési kódok használata az ügyfél (böngésző) kódjából

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)
