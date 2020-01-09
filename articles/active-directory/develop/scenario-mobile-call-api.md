---
title: Webes API meghívása Mobile-alkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan mobil alkalmazást, amely webes API-kat hív meg (webes API-t hív meg)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f56d55a08c46338fb7916664834b69005419e72
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423752"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Webes API-kat meghívó mobil alkalmazás – webes API meghívása

Miután az alkalmazás bejelentkezett egy felhasználóval, és jogkivonatokat kapott, a MSAL a felhasználóval, a felhasználó környezetével és a kiadott jogkivonatokkal kapcsolatos számos információt elérhetővé tesz. Az alkalmazás ezeket az értékeket használhatja a webes API-k meghívásához vagy egy üdvözlő üzenet megjelenítéséhez a felhasználónak.

Először is megvizsgáljuk a MSAL eredményét. Ezután megvizsgáljuk, hogyan használhat hozzáférési jogkivonatot a `AuthenticationResult` vagy `result` egy védett webes API meghívásához.

## <a name="msal-result"></a>MSAL eredménye
A MSAL a következő értékeket biztosítja: 

- `AccessToken`: a védett webes API-k HTTP-tulajdonosi kérelemben való meghívására használatos.
- `IdToken`: a bejelentkezett felhasználóval kapcsolatos hasznos információkat tartalmaz, például a felhasználó nevét, a Kezdőlap bérlőjét és a tárolás egyedi azonosítóját.
- `ExpiresOn`: a jogkivonat lejárati ideje. A MSAL kezeli az alkalmazások automatikus frissítését.
- `TenantId`: annak a bérlőnek az azonosítója, amelyhez a felhasználó bejelentkezett. A vendég felhasználók (Azure Active Directory B2B) esetében ez az érték azonosítja azt a bérlőt, amelybe a felhasználó bejelentkezett, nem pedig a felhasználó saját bérlője.  
- `Scopes`: a tokenhez megadott hatókörök. A megadott hatókörök lehetnek a kért hatókörök részhalmazai.

A MSAL egy `Account`absztrakciót is biztosít. Az `Account` az aktuális felhasználó bejelentkezett fiókját jelöli.

- `HomeAccountIdentifier`: a felhasználó saját bérlőjának azonosítója.
- `UserName`: a felhasználó elsődleges felhasználóneve. A Azure Active Directory B2C felhasználók számára ez lehet üres.
- `AccountIdentifier`: a bejelentkezett felhasználó azonosítója. Ez az érték ugyanaz lesz, mint a legtöbb esetben a `HomeAccountIdentifier` érték, kivéve, ha a felhasználó egy másik bérlőben található vendég.

## <a name="call-an-api"></a>API meghívása

A hozzáférési jogkivonattal könnyen hívhat meg webes API-kat. Az alkalmazás a token használatával hozza létre a HTTP-kérelmet, majd futtatja a kérelmet.

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

A jogkivonatok beszerzésének módszerei egy `MSALResult` objektumot adnak vissza. a `MSALResult` a webes API-k meghívásához használható `accessToken` tulajdonságot teszi elérhetővé. A hozzáférési jogkivonatot fel kell venni a HTTP-engedélyezési fejlécbe, mielőtt a rendszer meghívja a védett webes API-hoz való hozzáférést.

Objective-C:

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

Swift

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

## <a name="making-several-api-requests"></a>Több API-kérelem készítése

Ha többször kell meghívnia ugyanazt az API-t, vagy ha több API-t kell meghívnia, vegye figyelembe az alábbiakat az alkalmazás létrehozásakor:

- **Növekményes beleegyezett**: a Microsoft Identity platform lehetővé teszi, hogy az alkalmazások a felhasználók beleegyeznek a szükséges engedélyekkel, nem pedig az elején. Minden alkalommal, amikor az alkalmazás készen áll egy API meghívására, csak a használni kívánt hatóköröket kell kérnie.
- **Feltételes hozzáférés**: bizonyos helyzetekben további feltételes hozzáférési követelmények is megjelenhetnek, ha több API-kérést hajt végre. Ez akkor fordulhat elő, ha az első kérelemben nincsenek feltételes hozzáférési szabályzatok alkalmazva, és az alkalmazás a feltételes hozzáférést igénylő új API csendes elérését kísérli meg. Ennek a forgatókönyvnek a kezeléséhez ügyeljen arra, hogy a csendes kérelmekkel kapcsolatos hibákat kapjon, és készüljön fel egy interaktív kérelem elvégzésére.  További információ: [útmutató a feltételes hozzáféréshez](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Több API hívása Xamarin vagy UWP – növekményes hozzájárulás és feltételes hozzáférés

Ha ugyanahhoz a felhasználóhoz több API-t kell meghívnia, akkor a felhasználóhoz tartozó jogkivonat megszerzését követően elkerülhető, hogy a felhasználó a hitelesítő adatok többszöri megadásával kéri a token beszerzését `AcquireTokenSilent`.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A beavatkozást igénylő esetek a következőket jelentik:

- A felhasználó jóváhagyta az első API-t, de most hozzá kell járulnia további hatókörökhöz (növekményes hozzájárulás)
- Az első API-nak nem volt szükség többtényezős hitelesítésre, de a következő lépés.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-mobile-production.md)
