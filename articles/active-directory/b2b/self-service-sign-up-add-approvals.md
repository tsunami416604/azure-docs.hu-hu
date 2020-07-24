---
title: Egyéni jóváhagyások hozzáadása önkiszolgáló bejelentkezési folyamatokhoz – Azure AD
description: API-összekötők hozzáadása egyéni jóváhagyási munkafolyamatokhoz külső identitások önkiszolgáló bejelentkezési Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d1a4495b1d637b1cf8592f8c17e63ad456ea3c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027461"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Egyéni jóváhagyási munkafolyamat hozzáadása az önkiszolgáló regisztrációhoz

Az [API-összekötők](api-connectors-overview.md)használatával integrálhatja saját egyéni jóváhagyási munkafolyamatait önkiszolgáló regisztrációval, így kezelheti, hogy mely vendég felhasználói fiókok jönnek létre a bérlőben.

Ez a cikk bemutatja, hogyan integrálható egy jóváhagyási rendszerrel. Ebben a példában az önkiszolgáló bejelentkezési felhasználói folyamat a regisztrációs folyamat során gyűjti a felhasználói adatokat, és átadja a jóváhagyási rendszernek. Ezt követően a jóváhagyási rendszer a következőket teheti:

- Automatikusan hagyja jóvá a felhasználót, és engedélyezze az Azure AD számára a felhasználói fiók létrehozását.
- Manuális áttekintést indíthat. Ha a kérést jóváhagyják, a jóváhagyási rendszer a Microsoft Graph használatával kiépíti a felhasználói fiókot. A jóváhagyási rendszer azt is értesíti a felhasználót, hogy a fiókja létrejött.

## <a name="register-an-application-for-your-approval-system"></a>Alkalmazás regisztrálása a jóváhagyási rendszerhez

Regisztrálnia kell a jóváhagyási rendszerét alkalmazásként az Azure AD-bérlőben, hogy az képes legyen hitelesíteni az Azure AD-vel, és jogosult legyen a felhasználók létrehozására. További információ a [Microsoft Graph hitelesítési és engedélyezési alapjairól](https://docs.microsoft.com/graph/auth/auth-concepts).

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
4. Adja meg az alkalmazás **nevét** (például: _regisztráció jóváhagyása_).

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Válassza a **Regisztráció** lehetőséget. Más mezőket is hagyhat az alapértelmezett értékeken.

   ![Alkalmazás-oldal regisztrálása](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. A bal oldali menü **kezelés** területén válassza az **API-engedélyek**lehetőséget, majd kattintson az **engedély hozzáadása**lehetőségre.
7. Az **API-engedélyek kérése** lapon válassza a **Microsoft Graph**lehetőséget, majd válassza az **alkalmazás engedélyei**lehetőséget.
8. Az **engedélyek kiválasztása**alatt bontsa ki a **felhasználó**elemet, majd válassza a **User. ReadWrite. All** jelölőnégyzetet. Ez az engedély lehetővé teszi a jóváhagyási rendszer számára, hogy jóváhagyás után létrehozza a felhasználót. Ezután válassza az **engedélyek hozzáadása**lehetőséget.

   ![Alkalmazás-oldal regisztrálása](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Az **API-engedélyek** lapon válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget, majd válassza az **Igen**lehetőséget.
10. A bal oldali menü **kezelés** területén válassza a **tanúsítványok & titkok**lehetőséget, majd válassza az **új ügyfél titka**lehetőséget.
11. Adja meg a titok **leírását** , például a _jóváhagyások ügyfél titkát_, és válassza ki azt az időtartamot, ameddig az ügyfél titkos kulcsa **lejár**. Ezután válassza a **Hozzáadás**lehetőséget.
12. Másolja ki az ügyfél titkos kulcsának értékét.

    ![Az ügyfél titkos kulcsának másolása a jóváhagyási rendszerbe való használatra](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Konfigurálja úgy a jóváhagyási rendszerét, hogy az **alkalmazás azonosítóját** használja ügyfél-azonosítóként, valamint az Azure ad-vel való hitelesítéshez létrehozott **ügyfél-titkos kulcsot** .

## <a name="create-the-api-connectors"></a>Az API-összekötők létrehozása

Ezután [létrehozza az API-összekötőket](self-service-sign-up-add-api-connector.md#create-an-api-connector) az önkiszolgáló bejelentkezési felhasználói folyamathoz. A jóváhagyási rendszerapi-nak két összekötőre és megfelelő végpontokra van szüksége, például az alább látható példákhoz. Ezek az API-összekötők a következőket végzik el:

- **Jóváhagyás állapotának bejelölése**. Közvetlenül az identitás-szolgáltatóval való bejelentkezés után küldje el a jóváhagyási rendszer hívását, és ellenőrizze, hogy a felhasználó rendelkezik-e meglévő jóváhagyási kéréssel, vagy már meg lett tagadva. Ha a jóváhagyási rendszere csak automatikus jóváhagyási döntéseket tartalmaz, előfordulhat, hogy ez az API-összekötő nem szükséges. Példa a "jóváhagyás állapotának engedélyezése" API-összekötőre.

  ![A jóváhagyási állapot API-összekötő konfigurációjának engedélyezése](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Kérelem jóváhagyása** – ha a felhasználó befejezte az attribútum-gyűjtemény lapot, de a felhasználói fiók létrehozása előtt meghívja a jóváhagyást, küldjön egy hívást a jóváhagyási rendszernek. A jóváhagyási kérést automatikusan megadhatja vagy manuálisan is áttekintheti. Példa a "kérelem jóváhagyása" API-összekötőre. Válassza ki azokat a **jogcímeket, amelyeket el szeretne küldeni** , hogy a jóváhagyási rendszernek jóváhagyási döntést kell hoznia.

  ![Kérelem-jóváhagyási API-összekötő konfigurálása](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Az összekötők létrehozásához kövesse az API- [összekötő létrehozása](self-service-sign-up-add-api-connector.md#create-an-api-connector)című témakör lépéseit.

## <a name="enable-the-api-connectors-in-a-user-flow"></a>API-összekötők engedélyezése felhasználói folyamatokban

Most adja hozzá az API-összekötőket önkiszolgáló bejelentkezési felhasználói folyamathoz a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza a **felhasználói folyamatok (előzetes verzió)** lehetőséget, majd válassza ki azt a felhasználói folyamatot, amely számára engedélyezni kívánja az API-összekötőt.
5. Válassza az **API-összekötők**lehetőséget, majd válassza ki azokat az API-végpontokat, amelyeket a felhasználói folyamat következő lépéseiben szeretne meghívni:

   - Az **identitás-szolgáltatóval való bejelentkezés után**: válassza ki a jóváhagyási állapot API-összekötőt, például a _jóváhagyási állapot ellenőrzését_.
   - **A felhasználó létrehozása előtt**: válassza ki a jóváhagyási kérelem API-összekötőjét, például a _kérelem jóváhagyását_.

   ![API-k hozzáadása a felhasználói folyamathoz](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Válassza a **Mentés** lehetőséget.

## <a name="control-the-sign-up-flow-with-api-responses"></a>A regisztrációs folyamat kezelése API-válaszokkal

A jóváhagyási rendszer a két API-végpont [API-Response típusait](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api) használhatja a regisztrációs folyamat vezérléséhez.

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Kérelmek és válaszok a "jóváhagyás állapotának engedélyezése" API-összekötőhöz

Példa az API által a "jóváhagyás állapotának ellenőrzéséhez" API-összekötőtől kapott kérelemre:

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-check-approval-status"></a>Folytatási válasz a "jóváhagyás állapotának ellenőrzését"

Az **ellenőrzési jóváhagyási állapot** API-végpontjának a folytatási választ kell visszaadnia, ha:

- A felhasználó korábban nem kért jóváhagyást.

A folytatási válasz példája:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>A "jóváhagyás állapotának ellenőrzését" jelző válasz blokkolása

Az **ellenőrzések jóváhagyási állapotának** API-végpontjának blokkoló választ kell visszaadnia, ha:

- A felhasználó jóváhagyása függőben van.
- A rendszer megtagadta a felhasználót, és nem kérheti újra a jóváhagyást.

A következő példák blokkolják a válaszokat:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Kérelmek és válaszok a "kérés jóváhagyása" API-összekötőhöz

Példa az API által a "kérelem jóváhagyása" API-összekötőtől kapott HTTP-kérelemre:

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-request-approval"></a>Folytatási válasz a "kérés jóváhagyása"

A **kérelem-jóváhagyási** API-végpontnak a következőket kell visszaadnia, ha:

- A felhasználó **_automatikusan jóváhagyható_**.

A folytatási válasz példája:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Ha a folytatási válasz érkezik, az Azure AD létrehoz egy felhasználói fiókot, és átirányítja a felhasználót az alkalmazásba.

#### <a name="blocking-response-for-request-approval"></a>A "kérés jóváhagyása" válaszának blokkolása

A **kérelem-jóváhagyási** API-végpontnak blokkoló választ kell visszaadnia, ha:

- A rendszer létrehozta a felhasználó jóváhagyási kérelmét, és már függőben van.
- A felhasználó jóváhagyási kérelmét A rendszer automatikusan megtagadta.

A következő példák blokkolják a válaszokat:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

A `userMessage` válaszban látható a felhasználó számára, például:

![Példa függőben lévő jóváhagyási lapra](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Felhasználói fiók létrehozása manuális jóváhagyás után

A manuális jóváhagyás beszerzését követően az egyéni jóváhagyási rendszer létrehoz egy [felhasználói](https://docs.microsoft.com/graph/azuread-users-concept-overview) fiókot [Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)használatával. A jóváhagyási rendszernek a felhasználói fiókra vonatkozó kiépítésének módja a felhasználó által használt identitás-szolgáltatótól függ.

### <a name="for-a-federated-google-or-facebook-user"></a>Összevont Google-vagy Facebook-felhasználó esetén

> [!IMPORTANT]
> A jóváhagyási rendszernek explicit módon ellenőriznie kell, hogy létezik-e, és a `identities` `identities[0]` `identities[0].issuer` `identities[0].issuer` "Facebook" vagy a "Google" értékkel egyenlő a metódus használatára.

Ha a felhasználó Google-vagy Facebook-fiókkal jelentkezett be, használhatja a [felhasználói létrehozási API](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http)-t.

1. A jóváhagyási rendszer a HTTP-kérést a felhasználói folyamattól kapja meg.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. A jóváhagyási rendszer a Microsoft Graph használatával hoz létre egy felhasználói fiókot.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Paraméter                                           | Kötelező | Leírás                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Yes      | A az `email` API-nak eljuttatott jogcím alapján hozható létre, és a karaktert lecseréli a `@` `_` értékre, és előre függőben kell lennie `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Yes      | Értékre kell állítani `true` .                                                                                                                                                 |
| Levelezés                                                | Yes      | Az `email` API-nak eljuttatott jogcímet.                                                                                                               |
| userType                                            | Yes      | Kell lennie `Guest` . A felhasználó kijelölése vendég felhasználóként.                                                                                                                 |
| identitások                                          | Yes      | Az összevont identitás adatai.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | No       | Egyéb beépített attribútumok `displayName` , például,, `city` és mások. A paraméterek nevei ugyanazok, mint az API-összekötő által eljuttatott paraméterek.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | No       | A felhasználó egyéni attribútumai. A paraméterek nevei ugyanazok, mint az API-összekötő által eljuttatott paraméterek.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>Összevont Azure Active Directory-felhasználó esetén

Ha egy felhasználó összevont Azure Active Directory-fiókkal jelentkezik be, a [meghívót használó API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0) -val létre kell hoznia a felhasználót, majd opcionálisan a [felhasználó frissítési API](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0) -ját, hogy további attribútumokat rendeljen a felhasználóhoz.

1. A jóváhagyási rendszer a HTTP-kérést a felhasználói folyamattól kapja meg.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. A jóváhagyási rendszerek létrehozzák a meghívást az `email` API-összekötő által biztosított használatával.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Példa a válaszra:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. A jóváhagyási rendszer a meghívott felhasználó AZONOSÍTÓját használja a felhasználó fiókjának az összegyűjtött felhasználói attribútumokkal való frissítéséhez (opcionális).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg az [Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)gyors üzembe helyezési mintákkal.
- Az [önkiszolgáló regisztrációt a vendég felhasználók manuális jóváhagyási mintával regisztrálhatják](code-samples-self-service-sign-up.md#custom-approval-workflows). 
