---
title: Token beszereznie egy webes API-kat meghívjaó webalkalmazásban – Microsoft identity platform | Azure
description: További információ a webes API-kat meghívjaó webalkalmazások jogkivonatának beszerzéséről
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 196d941c5c3b18b737f7a11c25ebbb9eab91be1e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885038"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívjaó webalkalmazás: Jogkivonat beszerzése az alkalmazáshoz

Az ügyfélalkalmazás-objektumot felépítette. Most fogja használni, hogy szerezzen be egy jogkivonatot egy webes API-t. A ASP.NET vagy ASP.NET Core-ban a webes API-k hívása a vezérlőben történik:

- A token cache segítségével a webes API-t kap egy token. A token beszerzéséhez hívja `AcquireTokenSilent` meg a metódust.
- Hívja meg a védett API-t, és adja át a hozzáférési jogkivonatot paraméterként.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A vezérlőmetódusokat egy `[Authorize]` attribútum védi, amely a hitelesítést a webalkalmazás használatára kényszeríti a felhasználókat. A Microsoft Graph-ot az alábbiakszerint szólítja meg:

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

A `ITokenAcquisition` szolgáltatást a ASP.NET függőségi injektálással injektálják.

Az egyszerűsített kódot a művelethez, `HomeController`amely kap egy jogkivonatot a Microsoft Graph hívásához:

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

Az ehhez a forgatókönyvhöz szükséges kód jobb[2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)megértéséhez tekintse meg az [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyag 2.

Vannak más összetett változatok, mint például:

- Több API hívása.
- Növekményes hozzájárulás és feltételes hozzáférés feldolgozása.

Ezeket a speciális lépéseket a [3-WebApp-multi-API-k](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) oktatóanyagának 3.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET kódja hasonlít a core ASP.NET kódjához:

- Az [Authorize] attribútum által védett vezérlőművelet kinyeri a `ClaimsPrincipal` vezérlőtag bérlői azonosítóját és felhasználói azonosítóját. (ASP.NET `HttpContext.User`használja .)
- Onnan épít egy MSAL.NET `IConfidentialClientApplication` objektumot.
- Végül meghívja `AcquireTokenSilent` a bizalmas ügyfélalkalmazás metódusát.

# <a name="java"></a>[Java](#tab/java)

A Java mintában az API-t meghívó kód az [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)metódusgetUsersFromGraph metódusában található.

A metódus megpróbálja `getAuthResultBySilentFlow`meghívni a . Ha a felhasználónak több hatókörhöz kell hozzájárulnia, a kód feldolgozza az `MsalInteractionRequiredException` objektumot, hogy kihívást jelentsen a felhasználó számára.

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

# <a name="python"></a>[Python](#tab/python)

A Python-mintában a Microsoft Graph-ot megíró kód az [app.py#L53-L62-ben található.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)

A kód megkísérli a jogkivonat ot a jogkivonat-gyorsítótárból leadni. Ezután az engedélyezési fejléc beállítása után meghívja a webes API-t. Ha nem tud token, akkor újra bejelentkezik a felhasználó.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
