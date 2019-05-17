---
title: A mobilalkalmazás, hogy a hívások webes API-k – egy webes API hívása |} A Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre, amely meghívja a webes API-k (webes API hívása) mobilalkalmazás
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46d8b138a566727f9172b627b8df3353e7216fa5
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550325"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Webes API-hívás, amely meghívja a webes API-k – mobilalkalmazás

Miután az alkalmazás a felhasználó bejelentkezett, és kapott jogkivonatok, MSAL több adatra a felhasználó, a felhasználói környezet és a kiállított jogkivonatokban tesz elérhetővé. Az alkalmazás ezeket az értékeket segítségével webes API-hívás, illetve a felhasználó egy üdvözlő üzenet megjelenítéséhez.

Először áttekintjük az MSAL eredményt. Ezután megnézzük, hogyan használható a hozzáférési jogkivonatot a `AuthenticationResult` vagy `result` egy védett webes API meghívásához.

## <a name="msal-result"></a>Az MSAL eredménye
Az MSAL biztosítja a következő értékeket: 

- `AccessToken`: Egy tulajdonosi HTTP-kérelem a védett webes API-k meghívásához használt.
- `IdToken`: A bejelentkezett felhasználó, például a tároló egyedi azonosítója, a felhasználó nevét és a saját bérlőjén hasznos információkat tartalmaz.
- `ExpiresOn`: A jogkivonat lejárati ideje. Az MSAL kezeli az alkalmazások automatikus frissítését.
- `TenantId`: A bérlőt, amelyhez a bejelentkezett felhasználó azonosítója. A vendégfelhasználók számára (az Azure Active Directory B2B) Ez az érték a-bérlőt, amelyhez a felhasználó bejelentkezve, nem a felhasználó saját bérlőjén azonosítja.  
- `Scopes`: A hatókörök, amelyeket megadott a tokennel. A megadott hatókörök lehet az Ön által kért hatókörök egy részét.

Az MSAL is biztosít a absztrakciós egy `Account`. Egy `Account` az aktuális felhasználó bejelentkezett fiókban jelöli.

- `HomeAccountIdentifier`: A felhasználó bérlőjének azonosítóját.
- `UserName`: A felhasználó elsődleges felhasználó nevét. Ez lehet üres, az Azure Active Directory B2C-felhasználók számára.
- `AccountIdentifier`: A bejelentkezett felhasználó azonosítója. Ez az érték azonos lesz a `HomeAccountIdentifier` értékét a legtöbb esetben, ha a felhasználó nem a Vendég egy másik bérlőben.

## <a name="call-an-api"></a>Az API meghívása

Miután a hozzáférési jogkivonatot, könnyebbé vált a webes API-hívás. Az alkalmazás a jogkivonat használatával hozhat létre a HTTP-kérést, és futtassa a kérelmet.

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

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Több API-kérelem indítására

Ha az azonos API hívása több alkalommal van szüksége, vagy több API-k meghívásához szükséges, az alkalmazás készítése során hajtsa végre a következőket:

- **Növekményes hozzájárulási**: A Microsoft identity platform lehetővé teszi, hogy az alkalmazások felhasználói beleegyezés engedélyekre szükség, ahelyett, hogy az összes kezdetekor. Minden alkalommal, amikor az alkalmazás készen áll a egy API-t, azt fel kell kérnie csak a hatóköröket kell használnia.
- **Feltételes hozzáférés**: Bizonyos esetekben kaphat további feltételes hozzáférési követelmények Ha létrehoz több API-kérések. Ez akkor fordulhat elő, ha az első kérelem nincs feltételes hozzáférési szabályzatokat a alkalmazni, és az alkalmazás csendes hozzáférni egy új API-hoz feltételes hozzáférést igénylő megkísérli. Ebben a forgatókönyvben kezelése érdekében ügyeljen arra, beavatkozás nélküli kérelmek a hibák észlelését, és készüljön fel egy interaktív kérés.  További tudnivalókért lásd: [útmutató a feltételes hozzáférés](conditional-access-dev-guide.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-mobile-production.md)
