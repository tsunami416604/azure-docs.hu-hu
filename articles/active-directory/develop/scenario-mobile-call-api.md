---
title: Webes API hívása mobilalkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról. (Webes API-hívás.)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160151"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Webes API hívása mobilalkalmazásból

Miután az alkalmazás aláírja a felhasználót, és jogkivonatokat kap, a Microsoft Authentication Library (MSAL) információkat tár fel a felhasználóról, a felhasználó környezetéről és a kiadott jogkivonatokról. Az alkalmazás ezekkel az értékekkel hívhat meg egy webes API-t, vagy üdvözlő üzenetet jeleníthet meg a felhasználónak.

Ebben a cikkben először megnézzük az MSAL eredményét. Ezután megvizsgáljuk, hogyan használhat egy `AuthenticationResult` hozzáférési `result` jogkivonatot, vagy hogyan hívhat meg egy védett webes API-t.

## <a name="msal-result"></a>MSAL eredmény
Az MSAL a következő értékeket adja meg: 

- `AccessToken`http-tulajdonosi kérelemben védett webes API-kat hív meg.
- `IdToken`hasznos információkat tartalmaz a bejelentkezett felhasználóról. Ez az információ tartalmazza a felhasználó nevét, az otthoni bérlőt és a tárolás egyedi azonosítóját.
- `ExpiresOn`a jogkivonat lejárati ideje. Az MSAL kezeli az alkalmazás automatikus frissítését.
- `TenantId`Annak a bérlőnek az azonosítója, ahol a felhasználó bejelentkezett. Az Azure Active Directory (Azure AD) B2B vendégfelhasználói számára ez az érték azonosítja azt a bérlőt, ahol a felhasználó bejelentkezett. Az érték nem azonosítja a felhasználó otthoni bérlőjét.  
- `Scopes`a jogkivonattal kapott hatóköröket jelzi. A megadott hatókörök a kért hatókörök egy részhalmaza lehet.

Az MSAL egy `Account` érték absztrakciót is biztosít. Az `Account` érték az aktuális felhasználó bejelentkezett fiókját jelöli:

- `HomeAccountIdentifier`azonosítja a felhasználó otthoni bérlőjét.
- `UserName`a felhasználó által előnyben részesített felhasználónév. Ez az érték üres lehet az Azure AD B2C-felhasználók számára.
- `AccountIdentifier`azonosítja a bejelentkezett felhasználót. A legtöbb esetben ez az érték `HomeAccountIdentifier` megegyezik az értékkel, kivéve, ha a felhasználó egy másik bérlő vendége.

## <a name="call-an-api"></a>API hívása

Miután rendelkezik a hozzáférési jogkivonat, meghívhat egy webes API-t. Az alkalmazás a jogkivonatot fogja használni egy HTTP-kérelem létrehozásához, majd futtatja a kérelmet.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL iOS és macOS rendszerekre

A jogkivonatok beszerzésének `MSALResult` módszerei egy objektumot adnak vissza. `MSALResult`egy `accessToken` ingatlant. Webes API-k hívására is használható. `accessToken` Adja hozzá ezt a tulajdonságot a HTTP-engedélyezési fejléchez, mielőtt meghívja a védett webes API eléréséhez.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Több API-kérelem kérése

Ha többször is meg kell hívnia ugyanazt az API-t, vagy ha több API-t kell hívnia, vegye figyelembe a következő témákat az alkalmazás létrehozásakor:

- **Növekményes hozzájárulás: A**Microsoft identity platform lehetővé teszi, hogy az alkalmazások engedélyt kapjanak a felhasználótól, ha az engedélyek szükségesek, nem pedig az elején. Minden alkalommal, amikor az alkalmazás készen áll egy API-hívásra, csak a szükséges hatóköröket kell kérnie.

- **Feltételes hozzáférés:** Ha több API-kérelmet, bizonyos esetekben előfordulhat, hogy további feltételes hozzáférési követelményeknek kell megfelelnie. A követelmények ily módon növekedhetnek, ha az első kérelem nem rendelkezik feltételes hozzáférési szabályzatokkal, és az alkalmazás megpróbál csendben hozzáférni egy feltételes hozzáférést igénylő új API-hoz. A probléma megoldásához győződjön meg arról, hogy a néma kérelmekből származó hibákat észleli, és készen áll egy interaktív kérésre.  További információt a [Használati útmutató a feltételes hozzáféréshez](../azuread-dev/conditional-access-dev-guide.md)című témakörben talál.

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Több API hívása növekményes hozzájárulás és feltételes hozzáférés használatával

Ha ugyanannak a felhasználónak több API-t kell hívnia, miután beszerzett egy jogkivonatot a felhasználó `AcquireTokenSilent` számára, elkerülheti, hogy ismételten hitelesítő adatokat kérjen a felhasználótól, ha ezt követően egy jogkivonatot kér:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakcióra akkor van szükség, ha:

- A felhasználó hozzájárult az első API-hoz, de most több hatókörhöz is hozzá kell járulnia. Ebben az esetben növekményes hozzájárulást használ.
- Az első API-hoz nincs szükség többtényezős hitelesítésre, de a következő API igen.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-mobile-production.md)
