---
title: Webes API-kat meghívó mobil alkalmazás – webes API-k hívása | Microsoft Identity platform
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
ms.openlocfilehash: 76f0cddfa889376d3795726e74d82e53417b31f1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413570"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Webes API-kat meghívó mobil alkalmazás – webes API meghívása

Miután az alkalmazás bejelentkezett egy felhasználóval, és jogkivonatokat kapott, a MSAL a felhasználóval, a felhasználó környezetével és a kiadott jogkivonatokkal kapcsolatos számos információt elérhetővé tesz. Az alkalmazás ezeket az értékeket használhatja a webes API-k meghívásához vagy egy üdvözlő üzenet megjelenítéséhez a felhasználónak.

Először is megvizsgáljuk a MSAL eredményét. Ezután megvizsgáljuk, hogyan használhat hozzáférési tokent a `AuthenticationResult` vagy `result` egy védett webes API meghívásához.

## <a name="msal-result"></a>MSAL eredménye
A MSAL a következő értékeket biztosítja: 

- `AccessToken`: A védett webes API-k HTTP-tulajdonosi kérelemben való meghívására használatos.
- `IdToken`: Hasznos információkat tartalmaz a bejelentkezett felhasználóról, például a felhasználó nevére, a Kezdőlap bérlőre, valamint egy egyedi azonosítóra a tároláshoz.
- `ExpiresOn`: A jogkivonat lejárati ideje. A MSAL kezeli az alkalmazások automatikus frissítését.
- `TenantId`: Annak a bérlőnek az azonosítója, amelyhez a felhasználó bejelentkezett. A vendég felhasználók (Azure Active Directory B2B) esetében ez az érték azonosítja azt a bérlőt, amelybe a felhasználó bejelentkezett, nem pedig a felhasználó saját bérlője.  
- `Scopes`: A jogkivonattal megadott hatókörök. A megadott hatókörök lehetnek a kért hatókörök részhalmazai.

A MSAL egy absztrakciót `Account`is biztosít. A az aktuális felhasználó bejelentkezett fiókját jelöli.`Account`

- `HomeAccountIdentifier`: A felhasználó saját bérlőjának azonosítója.
- `UserName`: A felhasználó elsődleges felhasználóneve. A Azure Active Directory B2C felhasználók számára ez lehet üres.
- `AccountIdentifier`: A bejelentkezett felhasználó azonosítója. Ez az érték ugyanaz, mint a `HomeAccountIdentifier` legtöbb esetben az érték, kivéve, ha a felhasználó egy másik bérlőben található vendég.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Több API-kérelem készítése

Ha többször kell meghívnia ugyanazt az API-t, vagy ha több API-t kell meghívnia, vegye figyelembe az alábbiakat az alkalmazás létrehozásakor:

- **Növekményes beleegyezett**: A Microsoft Identity platform lehetővé teszi az alkalmazások számára, hogy a felhasználó beleegyezik, mivel engedélyekre van szükség, nem pedig az elején. Minden alkalommal, amikor az alkalmazás készen áll egy API meghívására, csak a használni kívánt hatóköröket kell kérnie.
- **Feltételes hozzáférés**: Bizonyos helyzetekben további feltételes hozzáférési követelmények is megjelenhetnek, ha több API-kérést hajt végre. Ez akkor fordulhat elő, ha az első kérelemben nincsenek feltételes hozzáférési szabályzatok alkalmazva, és az alkalmazás a feltételes hozzáférést igénylő új API csendes elérését kísérli meg. Ennek a forgatókönyvnek a kezeléséhez ügyeljen arra, hogy a csendes kérelmekkel kapcsolatos hibákat kapjon, és készüljön fel egy interaktív kérelem elvégzésére.  További információ: [útmutató a feltételes hozzáféréshez](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Több API hívása Xamarin vagy UWP – növekményes hozzájárulás és feltételes hozzáférés

Ha ugyanahhoz a felhasználóhoz több API-t kell meghívnia, akkor a felhasználóhoz tartozó jogkivonat megszerzését követően elkerülhető, `AcquireTokenSilent` hogy a felhasználó a hitelesítő adatok többszöri megadásával kéri a token lekérését.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A beavatkozást igénylő esetek a következőket jelentik:

- A felhasználó jóváhagyta az első API-t, de most hozzá kell járulnia további hatókörökhöz (növekményes hozzájárulás)
- Az első API-nak nem volt szükség többtényezős hitelesítésre, de a következő lépés.

```CSharp
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
> [Áthelyezés éles környezetbe](scenario-mobile-production.md)
