---
title: A mobilalkalmazás, hogy a hívások webes API-k – egy webes API hívása |} A Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre, amely meghívja a webes API-k (webes API meghívása) mobilalkalmazás
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
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075115"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Webes API-hívás, amely meghívja a webes API-k – mobilalkalmazás

Az alkalmazás rendelkezik egy felhasználó bejelentkezett, és a kapott jogkivonatokat, MSAL több adatra is a felhasználó, a környezetükben és a kiállított jogkivonatokban információt tesz elérhetővé. Az alkalmazás ezeket az értékeket segítségével webes API-hívás, illetve egy felhasználó egy üdvözlő üzenet megjelenítéséhez.

Először is fog tárgyaljuk az MSAL eredmény, majd a hozzáférési jogkivonat használata a `AuthenticationResult` vagy `result` egy védett webes API meghívásához.

## <a name="msal-result"></a>Az MSAL eredménye

- `AccessToken`: Használja a védett webes API-k a kérelem HTTP-tulajdonosi hívására.
- `IdToken`: Storage a bejelentkezett felhasználó, például a nevét, saját bérlőjén és egyedi azonosítót kapcsolatos hasznos jogcímek tartalmazza.
- `ExpiresOn`: a jogkivonat lejárati idején. Az MSAL kezeli az alkalmazások automatikus frissítése.
- `TenantId`: A felhasználó bérlőjéhez való bejelentkezéshez használt azonosítója. A vendégfelhasználók (Azure AD B2B) Ez lesz a bérlő, a felhasználó bejelentkezve, nem a saját bérlőjén.  
- `Scopes`: a hatókörök, amelyeket megadott a tokennel. Ez lehet egy, a kért részét.

Ezenfelül az MSAL absztrakciója is biztosít egy `Account`. Egy fiók a fiókkal bejelentkezve az aktuális felhasználó jelöli.

- `HomeAccountIdentifier`: A felhasználó bérlőjének azonosítóját.
- `UserName`: A felhasználó elsődleges felhasználóneve. Ez az Azure AD B2C-felhasználókat üres is lehet.
- `AccountIdentifier`: A bejelentkezett felhasználó azonosítója. Ez lesz ugyanaz, mint a `HomeAccountIdentifier` a legtöbb esetben, ha a felhasználó nem a Vendég egy másik bérlőben.

## <a name="calling-an-api"></a>Az API meghívása

Miután készen áll a hozzáférési jogkivonatot, az egyszerű a webes API-hívás. Az alkalmazás is igénybe ezt a tokent, HTTP-kérelmek létrehozásának, és futtathatja a.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Több API-kérelem indítására

Ha meg kell hívnia az azonos API többször, vagy több API-k nincsenek további szempontok az alkalmazás:

- ***Növekményes hozzájárulási***: A Microsoft identity platform lehetővé teszi, hogy az alkalmazások felhasználói beleegyezés engedély szükséges ahelyett, hogy az összes kezdeti vannak. Minden alkalommal, amikor az alkalmazás készen áll a egy API-t, azt kell kérelem csak a hatókörök kíván használni.
- ***Feltételes hozzáférés***: Bizonyos esetekben előfordulhat, hogy beolvasása a további feltételes hozzáférési követelmények, több API-kérések küldésekor. Ebben a forgatókönyvben kezelése érdekében ügyeljen arra, beavatkozás nélküli kérelmek a hibák észlelését, és készüljön fel egy interaktív kérés. Ez akkor fordulhat elő, ha az első kérelem nincs feltételes hozzáférési szabályzatokat a alkalmazni, és az alkalmazás csendes hozzáférni egy új API-hoz feltételes hozzáférést igénylő megkísérli. További tudnivalókért lásd: [útmutató a feltételes hozzáférés](conditional-access-dev-guide.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-mobile-production.md)
