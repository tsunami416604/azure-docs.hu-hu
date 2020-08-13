---
title: API-összekötők hozzáadása önkiszolgáló bejelentkezési folyamatokhoz – Azure AD
description: Webes API beállítása felhasználói folyamatokban való használatra.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f241fd038d0d7309d8e1e5578dd77f950261b68
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165175"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>API-összekötő hozzáadása felhasználói folyamathoz

Az API- [Összekötők](api-connectors-overview.md)használatához először létre kell hoznia az API-összekötőt, majd engedélyeznie kell azt egy felhasználói folyamaton.

## <a name="create-an-api-connector"></a>API-összekötő létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza **az összes API-összekötő (előzetes verzió)** lehetőséget, majd válassza az **új API-összekötő**lehetőséget.

   ![Új API-összekötő hozzáadása](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Adja meg a hívás megjelenítendő nevét. Például a **jóváhagyási állapotot kell bejelölni**.
6. Adja meg az API **-hívás végpontjának URL-címét** .
7. Adja meg az API hitelesítési adatait.

   - Jelenleg csak az alapszintű hitelesítés támogatott. Ha alapszintű hitelesítés nélküli API-t szeretne használni fejlesztési célokra, egyszerűen adja meg az API által figyelmen kívül hagyható dummy- **felhasználónevet** és- **jelszót** . Ha egy API-kulccsal rendelkező Azure-függvényt használ, a kódot lekérdezési paraméterként is megadhatja a **végpont URL-címében** (például https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Új API-összekötő hozzáadása](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Korábban be kellett állítania, hogy mely felhasználói attribútumok legyenek elküldve az API-nak ("küldési jogcímek"), és hogy mely felhasználói attribútumok fogadják el az API-t ("fogadott jogcímek"). Mostantól alapértelmezés szerint minden felhasználói attribútumot elküld a rendszer, ha értékkel rendelkezik, és az API egy "Folytatás" válaszban visszaadja a felhasználói attribútumot.

## <a name="the-request-sent-to-your-api"></a>Az API-nak továbbított kérelem
Az API-összekötők **http post** -kérelemként valósulnak meg, felhasználói attribútumok ("jogcímek") küldésével kulcs-érték párokként egy JSON-törzsben. Az attribútumok a [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) felhasználó tulajdonságaihoz hasonlóan lesznek szerializálva. 

**Példakérelem**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

A kérelemben csak a felhasználói tulajdonságok és az egyéni attribútumok szerepelnek a **Azure Active Directory**  >  **külső identitások**  >  **egyéni felhasználói attribútumaik** felületén.

Az egyéni attribútumok a címtár **extension_ \<extensions-app-id> _AttributeName**  formátumában találhatók. Az API-nak meg kell várnia, hogy a jogcímeket ugyanabban a szerializált formátumban fogadja. Az egyéni attribútumokkal kapcsolatos további információkért lásd: [Egyéni attribútumok definiálása önkiszolgáló bejelentkezési folyamatokhoz](user-flow-add-custom-attributes.md).

Emellett a **felhasználói felület területi beállítása ("ui_locales")** jogcímet alapértelmezés szerint minden kérelemben elküldjük. A felhasználó az eszközön konfigurált területi beállításokat biztosít, amelyeket az API használhat a nemzetközi válaszok visszaküldéséhez.

> [!IMPORTANT]
> Ha a küldendő jogcím nem rendelkezik értékkel az API-végpont hívásakor, a rendszer nem küldi el a jogcímet az API-nak. Az API-t úgy kell kialakítani, hogy explicit módon ellenőrizzék a várt értéket.

> [!TIP] 
> az API- [**k az identitások ("identitások")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) és az **e-mail-cím ("e-mail")** jogcímek használatával azonosíthatják a felhasználókat, mielőtt a bérlőben fiókkal rendelkeznek. Az "identitások" jogcímet akkor kell elküldeni, ha a felhasználó egy olyan identitás-szolgáltatóval hitelesít, mint például a Google vagy a Facebook. az "e-mail" küldése mindig elküldve.

## <a name="enable-the-api-connector-in-a-user-flow"></a>API-összekötő engedélyezése felhasználói folyamatokban

Az alábbi lépéseket követve hozzáadhat egy API-összekötőt egy önkiszolgáló bejelentkezési felhasználói folyamathoz.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza a **felhasználói folyamatok (előzetes verzió)** lehetőséget, majd válassza ki azt a felhasználói folyamatot, amelyhez hozzá kívánja adni az API-összekötőt.
5. Válassza az **API-összekötők**lehetőséget, majd válassza ki azokat az API-végpontokat, amelyeket a felhasználói folyamat következő lépéseiben szeretne meghívni:

   - **Az identitás-szolgáltatóval való bejelentkezés után**
   - **A felhasználó létrehozása előtt**

   ![API-k hozzáadása a felhasználói folyamathoz](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Kattintson a **Mentés** gombra.

## <a name="after-signing-in-with-an-identity-provider"></a>Az identitás-szolgáltatóval való bejelentkezés után

A regisztrálási folyamat ezen lépésében szereplő API-összekötőt közvetlenül a felhasználó személyazonosság-szolgáltatóval (Google, Facebook, Azure AD) való hitelesítése után hívja meg a rendszer. Ez a lépés megelőzi az ***attribútum-gyűjtemény lapot***, amely a felhasználó számára a felhasználói attribútumok összegyűjtésére bemutatott űrlap. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Példa erre a lépésre az API-nak küldendő kérelem
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Az API-nak küldött pontos jogcímek attól függnek, hogy az identitás-szolgáltató milyen információkat biztosít. az "e-mail" küldése mindig elküldve.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Várt válaszok a webes API-ból ebben a lépésben

Ha a webes API egy felhasználói folyamat során HTTP-kérést kap az Azure AD-től, a következő válaszokat adhatja vissza:

- Folytatási válasz
- Blokkoló válasz

#### <a name="continuation-response"></a>Folytatási válasz

A folytatólagos válasz azt jelzi, hogy a felhasználói folyamatnak továbbra is a következő lépésnek kell megjelennie: az attribútum-gyűjtemény lap.

A folytatási válaszban az API jogcímeket adhat vissza. Ha az API egy jogcímet ad vissza, a jogcím a következő műveleteket végzi el:

- Előzetesen kitölti a beviteli mezőt az attribútumok gyűjteménye lapon.

Tekintse meg a [folytatásra adott](#example-of-a-continuation-response)példát.

#### <a name="blocking-response"></a>Blokkoló válasz

A blokkolási válasz kilép a felhasználói folyamatból. Az API szándékosan kiállíthatja a felhasználói folyamat folytatását úgy, hogy egy blokk lapot jelenít meg a felhasználónak. A blokk oldal megjeleníti az `userMessage` API által biztosított értéket.

A [blokkolási válasz](#example-of-a-blocking-response)példáját lásd:.

## <a name="before-creating-the-user"></a>A felhasználó létrehozása előtt

A regisztrációs folyamat ezen lépésében található API-összekötőt a rendszer az attribútum-gyűjtemény lap után hívja meg, ha van ilyen. Ez a lépés mindig a felhasználói fiók Azure AD-ben való létrehozása előtt kerül meghívásra. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Példa erre a lépésre az API-nak küldendő kérelem

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Az API-nak küldött pontos jogcímek attól függnek, hogy milyen adatokat gyűjt a rendszer a felhasználótól, vagy amelyet az identitás szolgáltatója biztosít.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Várt válaszok a webes API-ból ebben a lépésben

Ha a webes API egy felhasználói folyamat során HTTP-kérést kap az Azure AD-től, a következő válaszokat adhatja vissza:

- Folytatási válasz
- Blokkoló válasz
- Érvényesítési válasz

#### <a name="continuation-response"></a>Folytatási válasz

A folytatólagos válasz azt jelzi, hogy a felhasználói folyamatnak továbbra is a következő lépéssel kell megjelennie: hozza létre a felhasználót a címtárban.

A folytatási válaszban az API jogcímeket adhat vissza. Ha az API egy jogcímet ad vissza, a jogcím a következő műveleteket végzi el:

- Felülbírál minden olyan értéket, amely már hozzá van rendelve a jogcímhez az attribútum gyűjteménye lapon.

Tekintse meg a [folytatásra adott](#example-of-a-continuation-response)példát.

#### <a name="blocking-response"></a>Blokkoló válasz
A blokkolási válasz kilép a felhasználói folyamatból. Az API szándékosan kiállíthatja a felhasználói folyamat folytatását úgy, hogy egy blokk lapot jelenít meg a felhasználónak. A blokk oldal megjeleníti az `userMessage` API által biztosított értéket.

A [blokkolási válasz](#example-of-a-blocking-response)példáját lásd:.

### <a name="validation-error-response"></a>Ellenőrzés – hiba válasza
 Ha az API egy érvényesítési-hibaüzenettel válaszol, a felhasználói folyamat az attribútum-gyűjtemény oldalon marad, a `userMessage` pedig megjelenik a felhasználó számára. A felhasználó Ezután szerkesztheti és újra elküldheti az űrlapot. Ezt a típusú választ lehet használni a bemeneti ellenőrzéshez.

Tekintse meg az [érvényesítési hiba](#example-of-a-validation-error-response)példáját.

## <a name="example-responses"></a>Válaszok – példa

### <a name="example-of-a-continuation-response"></a>Folytatási válasz – példa

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Paraméter                                          | Típus              | Kötelező | Leírás                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | Sztring            | Igen      | Az API verziója.                                                                                                                                                                                                                                                                |
| művelet                                             | Sztring            | Igen      | Az értéknek a számnak kell lennie `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nem       | Az értékeket a címtárban tárolhatja, ha a felhasználói folyamat API-összekötő konfigurációjában és **felhasználói attribútumaiban** való **fogadásra vonatkozó jogcímként** van kijelölve. Az értékek a tokenben adhatók vissza, ha **alkalmazási jogcímként**van kiválasztva.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nem       | A visszaadott jogcímnek nem kell tartalmaznia `_<extensions-app-id>_` . Az értékeket a címtárban kell tárolni, ha a felhasználói folyamat API-összekötő konfigurációjában és **felhasználói attribútumában** való **fogadásra vonatkozó jogcímként** vannak megadva. Egyéni attribútumok nem küldhetők vissza a jogkivonatban. |

### <a name="example-of-a-blocking-response"></a>Blokkoló válasz – példa

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Paraméter   | Típus   | Kötelező | Leírás                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Sztring | Igen      | Az API verziója.                                                    |
| művelet      | Sztring | Igen      | Az értéknek szerepelnie kell `ShowBlockPage`                                              |
| userMessage | Sztring | Igen      | A felhasználónak megjelenítendő üzenet.                                            |
| code        | Sztring | Nem       | Hibakód. Hibakeresési célokra használható. Nem jelenik meg a felhasználó számára. |

**Végfelhasználói élmény blokkoló válaszokkal**

![Példa blokk oldalra](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Érvényesítési hiba – példa

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Paraméter   | Típus    | Kötelező | Leírás                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Sztring  | Igen      | Az API verziója.                                                    |
| művelet      | Sztring  | Igen      | Az értéknek a számnak kell lennie `ValidationError` .                                           |
| status      | Egész szám | Igen      | ValidationError-válasz értékének kell lennie `400` .                        |
| userMessage | Sztring  | Igen      | A felhasználónak megjelenítendő üzenet.                                            |
| code        | Sztring  | Nem       | Hibakód. Hibakeresési célokra használható. Nem jelenik meg a felhasználó számára. |

**Végfelhasználói élmény érvényesítéssel – hiba-válasz**

![Példa érvényesítési oldal](./media/api-connectors-overview/validation-error-postal-code.png)

## <a name="using-azure-functions"></a>Az Azure Functions használata
Egy HTTP-triggert használhat Azure Functions egyszerű módon, ha API-végpontot hoz létre az API-összekötővel való használatra. Az Azure-függvényt használhatja [például](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)az érvényesítési logikára, és meghatározott tartományokra korlátozza a bejelentkezéseket. Kiterjedt forgatókönyvek esetén más webes API-kat, felhasználói áruházakat és más felhőalapú szolgáltatásokat is meghívhat és hívhat meg az Azure-függvényből.

## <a name="next-steps"></a>További lépések

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Ismerje meg, hogyan [adhat hozzá egyéni jóváhagyási munkafolyamatot önkiszolgáló regisztrációhoz](self-service-sign-up-add-approvals.md)
- Ismerkedjen meg az [Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)gyors üzembe helyezési mintákkal.
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
