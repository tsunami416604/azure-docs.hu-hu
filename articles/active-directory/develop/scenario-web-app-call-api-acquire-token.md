---
title: Token beszerzése webes API-kat meghívó webalkalmazásban – Microsoft Identity platform | Azure
description: Útmutató a webes API-kat meghívó webalkalmazások jogkivonatának beszerzéséhez
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759074"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webalkalmazás: az alkalmazás jogkivonatának beszerzése

Létrehozott egy ügyfélalkalmazás-objektumot. Most ezt a lehetőséget fogja használni a webes API-t meghívó token beszerzéséhez. A ASP.NET vagy ASP.NET Core a webes API meghívása a vezérlőben történik:

- Szerezze be a webes API tokenjét a jogkivonat-gyorsítótár használatával. A jogkivonat beszerzéséhez hívja meg a `AcquireTokenSilent` metódust.
- Hívja meg a védett API-t, és adja át a hozzáférési jogkivonatot paraméterként.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A vezérlő módszereit egy `[Authorize]` attribútum védi, amely arra kényszeríti a felhasználókat, hogy a webalkalmazás használatára legyenek hitelesítve. A Microsoft Graph meghívására szolgáló kód:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

A `ITokenAcquisition` szolgáltatást a ASP.NET a függőségi befecskendezés használatával fecskendezi be.

Itt látható a `HomeController`műveletének egyszerűsített kódja, amely a Microsoft Graph meghívásához szükséges tokent kapja meg:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

A forgatókönyvhöz szükséges kód jobb megismeréséhez tekintse meg a 2. fázis ([2-1-Web App calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) lépést az [MS-Identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyagban.

Más összetett változatok is léteznek, például:

- Több API meghívása.
- Növekményes beleegyezett és feltételes hozzáférés feldolgozása.

Ezek a speciális lépések a [3 – WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) -k oktatóanyag 3. fejezetében vannak lefoglalva.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET kódja hasonló a ASP.NET Corehoz megjelenített kódhoz:

- Egy [engedélyezés] attribútum által védett vezérlő művelet kibontja a vezérlő `ClaimsPrincipal` tagjának bérlői AZONOSÍTÓját és felhasználói AZONOSÍTÓját. (A ASP.NET `HttpContext.User`használ.)
- Ettől kezdve létrehoz egy MSAL.NET `IConfidentialClientApplication` objektumot.
- Végezetül meghívja a bizalmas ügyfélalkalmazás `AcquireTokenSilent` metódusát.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-mintában az API-t meghívó kód a [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)getUsersFromGraph metódusában található.

A metódus megkísérli a `getAuthResultBySilentFlow`hívását. Ha a felhasználónak több hatókörhöz kell hozzájárulnia, a kód feldolgozza a `MsalInteractionRequiredException` objektumot, hogy megtámadja a felhasználót.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-mintában a Microsoft Graph meghívására szolgáló kód az [app. a # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

A kód kísérletet tesz a jogkivonat-gyorsítótárból való lekérésére. Ezután az engedélyezési fejléc beállítása után meghívja a webes API-t. Ha nem tud jogkivonatot beolvasni, a rendszer ismét aláírja a felhasználót.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
