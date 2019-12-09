---
title: Token beszerzése webes API-kat meghívó webalkalmazásokban – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webalkalmazást (token beszerzése az alkalmazáshoz)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 398b68221681f0d14dbcc20ac7c0cb603313eaee
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919463"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webes API-kat meghívó webalkalmazás – jogkivonat beszerzése az alkalmazáshoz

Most, hogy létrehozta az ügyfélalkalmazás-objektumot, felhasználhatja egy, a webes API-t meghívó token beszerzéséhez. A ASP.NET vagy ASP.NET Coreban a webes API-k meghívása a vezérlőn történik. Az alábbiakról szól:

- Jogkivonat-gyorsítótár beszerzése a webes API-hoz. A jogkivonat beszerzéséhez hívja meg `AcquireTokenSilent`.
- A védett API meghívása a hozzáférési jogkivonattal.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A vezérlő módszereit egy `[Authorize]` attribútum védi, amely arra kényszeríti a felhasználókat, hogy a webalkalmazás használatára legyenek hitelesítve. Itt látható a Microsoft Graph meghívására szolgáló kód.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

A `ITokenAcquisition` szolgáltatást a ASP.NET a függőségek befecskendezésével fecskendezi be.


Itt látható a HomeController műveletének egyszerűsített kódja, amely lekéri a tokent a Microsoft Graph meghívásához.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

A forgatókönyvhöz szükséges kód alaposabb megismeréséhez tekintse meg a 2. fázis ([2-1-Web App calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) lépést az [MS-Identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyagban.

Számos további bonyolultság van, például:

- Több API meghívása,
- növekményes beleegyezett és feltételes hozzáférés feldolgozása.

Ezeket a speciális lépéseket az oktatóanyag [3 – WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) -k 3. fejezete dolgozza fel

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET hasonló dolgok:

- Az [engedélyezés] attribútum által védett vezérlő művelet kibontja a vezérlő `ClaimsPrincipal` tagjának bérlői AZONOSÍTÓját és felhasználói AZONOSÍTÓját. (A ASP.NET `HttpContext.User`használ.)
- Ettől kezdve létrehoz egy MSAL.NET `IConfidentialClientApplication`.
- Végezetül meghívja a bizalmas ügyfélalkalmazás `AcquireTokenSilent` metódusát.

A kód hasonló a ASP.NET Corehoz megjelenített kódhoz.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-mintában az API-t meghívó kód a [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)getUsersFromGraph metódusban található.

Megkísérli a `getAuthResultBySilentFlow`hívását. Ha a felhasználónak több hatókörhöz kell hozzájárulnia, a kód feldolgozza a `MsalInteractionRequiredException` a felhasználó megvitatásához.

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

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
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
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-mintában a Microsoft Graphot hívó kód az [app. a # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kísérletet tesz a jogkivonat-gyorsítótárból való lekérésre, majd az engedélyezési fejléc beállítása után meghívja az EB API-t. Ha nem, akkor újra aláírja a felhasználót.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
