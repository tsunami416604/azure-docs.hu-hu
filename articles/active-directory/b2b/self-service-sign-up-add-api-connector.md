---
title: API-összekötő hozzáadása felhasználói folyamathoz
description: Webes API beállítása felhasználói folyamatokban való használatra.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 738e25a0cd9778f3ee767fc3f9b6944c9c2e6473
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680055"
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

8. Válassza ki azokat a jogcímeket, amelyeket el szeretne küldeni az API-nak.
9. Válassza ki azokat a jogcímeket, amelyeket vissza szeretne kapni az API-ból.
 
    ![API-összekötő jogcímeinek beállítása](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Kattintson a **Mentés** gombra.
 
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

Útmutató az [API-összekötők felhasználói folyamatokban való engedélyezéséhez](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Kérelem elküldve az API-nak
Az API-összekötők HTTP POST-kérésként valósulnak meg, és a kiválasztott jogcímeket kulcs-érték párokként küldik egy JSON-törzsben. A válasznak a HTTP-fejlécet is tartalmaznia kell `Content-Type: application/json` . Az attribútumok a Microsoft Graph felhasználói attribútumoknál hasonló módon vannak szerializálva. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Példakérelem
```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<aad-extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<aad-extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Alapértelmezés szerint a rendszer a **felhasználói felület területi beállítása ("ui_locales")** jogcímet elküldi az összes kérelemben. Lehetővé teszi a felhasználó területi beállítását, és az API-t használja a nemzetközi válaszok visszaküldéséhez. Nem jelenik meg az API-konfiguráció ablaktáblán.

Ha a küldendő jogcím nem rendelkezik értékkel az API-végpont hívásakor, a rendszer nem küldi el a jogcímet az API-nak.

Egyéni attribútumok hozhatók létre a felhasználó számára a **extension_ \<aad-extensions-app-id> _ \<AttributeName> ** formátum használatával. Az API-nak meg kell várnia, hogy a jogcímeket ugyanabban a szerializált formátumban fogadja. Az API-nak ugyanezen a formátumban kell visszaadnia a jogcímeket, de a nem `<aad-extensions-app-id>` . <!--TODO: update once fix is rolled out--> Az egyéni attribútumokkal kapcsolatos további információkért lásd: [Egyéni attribútumok definiálása önkiszolgáló bejelentkezési folyamatokhoz](user-flow-add-custom-attributes.md).

> [!TIP]
> Az [**identitások ("identitások")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) és az **e-mail-cím ("email_address")** jogcímek segítségével azonosíthatja a felhasználókat, mielőtt a bérlőben fiókkal rendelkeznek. Az "identitások" jogcímet akkor küldik el a rendszer, ha a felhasználó Google-vagy Facebook-hitelesítéssel rendelkezik, és a "email_address" mindig el lesz küldve.


## <a name="expected-response-types-from-the-web-api"></a>Várt válaszok a webes API-ból

Ha a webes API egy felhasználói folyamat során HTTP-kérést kap az Azure AD-től, a következő válaszokat adhatja vissza:

- [Folytatási válasz](#continuation-response)
- [Blokkoló válasz](#blocking-response)
- [Ellenőrzés – hiba válasza](#validation-error-response)

### <a name="continuation-response"></a>Folytatási válasz

A folytatólagos válasz azt jelzi, hogy a felhasználói folyamatnak továbbra is a következő lépésre kell lépnie. A folytatási válaszban az API jogcímeket adhat vissza.

Ha az API egy jogcímet ad vissza, és a **fogadáshoz jogcímként**van kiválasztva, akkor a jogcím a következő:

- A beviteli mezők előzetes kitöltése az attribútumok gyűjteménye lapon, ha az API-összekötő meghívása megtörténik az oldal megjelenítése előtt.
- Felülbírál minden olyan értéket, amely már hozzá van rendelve a jogcímhez.
- Egy értéket rendel a jogcímhez, ha az korábban null volt.

#### <a name="example-of-a-continuation-response"></a>Folytatási válasz – példa
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0", 
    "action": "Continue",  
    "postalCode": "12349", // return claim 
    "extension_CustomAttribute": "value" // return claim 
}
```

| Paraméter  | Típus  | Kötelező | Leírás |
|---|---|---|---|
| version | Sztring | Yes | Az API verziója. |
| action  | Sztring | Yes | Az értéknek a számnak kell lennie `Continue` . |
| \<builtInUserAttribute> | \<attribute-type> | No  | Az értékeket a címtárban tárolhatja, ha a felhasználói folyamat API-összekötő konfigurációjában és **felhasználói attribútumaiban** való **fogadásra vonatkozó jogcímként** van kijelölve. Az értékek a tokenben adhatók vissza, ha **alkalmazási jogcímként**van kiválasztva. |
| \<extension_CustomAttribute> | \<attribute-type> | No  | A visszatérési jogcím *nem* rendelkezik `_<aad-extensions-app-id>_` . Az értékeket a címtárban kell tárolni, ha a felhasználói folyamat API-összekötő konfigurációjában és **felhasználói attribútumában** való **fogadásra vonatkozó jogcímként** vannak megadva. Egyéni attribútumok nem küldhetők vissza a jogkivonatban. |

### <a name="blocking-response"></a>Blokkoló válasz

A blokkolási válasz kilép a felhasználói folyamatból. Az API szándékosan kiállíthatja a felhasználói folyamat folytatását úgy, hogy egy blokk lapot jelenít meg a felhasználónak. A blokk oldal megjeleníti az `userMessage` API által biztosított értéket. 

A következő példa a blokkolási választ szemlélteti:

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

| Paraméter  | Típus  | Kötelező | Leírás |
|---|---|---|---|
| version         | Sztring           | Yes      | Az API verziója.    |
| action          | Sztring           | Yes      | Az értéknek szerepelnie kell`ShowBlockPage`  |
| userMessage     | Sztring           | Yes      | A felhasználónak megjelenítendő üzenet.    |
| code            | Sztring           | No       | Hibakód. Hibakeresési célokra használható. Nem jelenik meg a felhasználó számára.    |

#### <a name="end-user-experience-with-a-blocking-response"></a>Végfelhasználói élmény blokkoló választal

![Példa blokk oldalra](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Ellenőrzés – hiba válasza

Egy attribútum-gyűjtemény lap után meghívott API-hívás egy érvényesítési-hibaüzenetet adhat vissza. Ha így tesz, a felhasználói folyamat az attribútum-gyűjtemény oldalon marad, a `userMessage` pedig megjelenik a felhasználó számára. A felhasználó Ezután szerkesztheti és újra elküldheti az űrlapot. Ezt a típusú választ lehet használni a bemeneti ellenőrzéshez.

#### <a name="example-of-a-validation-error-response"></a>Érvényesítési hiba – példa

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

| Paraméter  | Típus  | Kötelező | Leírás |
|---|---|---|---|
| version         | Sztring           | Yes      | Az API verziója.   |
| action          | Sztring           | Yes      | Az értéknek a számnak kell lennie `ValidationError` .   |
| status          | Egész szám          | Yes      | ValidationError-válasz értékének kell lennie `400` .  |
| userMessage     | Sztring           | Yes      | A felhasználónak megjelenítendő üzenet.   |
| code            | Sztring           | No       | Hibakód. Hibakeresési célokra használható. Nem jelenik meg a felhasználó számára.    |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Végfelhasználói élmény érvényesítéssel – hiba-válasz

![Példa érvényesítési oldal](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="next-steps"></a>Következő lépések
- Ismerje meg, [Hogyan válaszolhat az API-nak](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api)
- Útmutató [az API-összekötők engedélyezéséhez](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)
- Ismerje meg, hogyan [adhat hozzá egyéni jóváhagyási munkafolyamatot önkiszolgáló regisztrációhoz](self-service-sign-up-add-approvals.md)
<!-- - Learn how to [use API connectors for identity proofing](code-samples-self-service-sign-up.md#identity-proofing) -->