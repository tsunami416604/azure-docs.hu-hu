---
title: Azure-API-felügyeleti API biztonságossá tétele az Azure Active Directory B2C használatával
description: Ismerje meg, hogyan használhatja az Azure Active Directory B2C által kiadott hozzáférési jogkivonatokat az Azure API Management API-végpont védelméhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186930"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure API-felügyeleti API biztonságossá tétele az Azure AD B2C-vel

Megtudhatja, hogyan korlátozhatja az Azure API Management (APIM) API-hoz való hozzáférést az Azure Active Directory B2C (Azure AD B2C) szolgáltatással hitelesített ügyfelekre. Kövesse a jelen cikkben leírt lépéseket egy bejövő szabályzat létrehozásához és teszteléséhez az APIM-ben, amely csak az érvényes Azure AD B2C által kiadott hozzáférési jogkivonatot tartalmazó kérelmekhez korlátozza a hozzáférést.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések folytatása előtt a következő erőforrásokra van szükség:

* [Azure AD B2C-bérlő](tutorial-create-tenant.md)
* [A](tutorial-register-applications.md) bérlőben regisztrált alkalmazás
* [A](tutorial-create-user-flows.md) bérlőben létrehozott felhasználói folyamatok
* [Közzétett API](../api-management/import-and-publish.md) az Azure API Management ben
* [Postás](https://www.getpostman.com/) a biztonságos hozzáférés teszteléséhez (nem kötelező)

## <a name="get-azure-ad-b2c-application-id"></a>Az Azure AD B2C alkalmazásazonosító jának beszereznie

Ha az Azure AD B2C-vel biztonságossá teszi az Azure API-kezelést, több értékre van szüksége az [APIM-ben](../api-management/api-management-howto-policies.md) létrehozott bejövő szabályzathoz. Először rögzítse az Azure AD B2C-bérlőben korábban létrehozott alkalmazás azonosítóját. Ha az előfeltételekben létrehozott alkalmazást használja, használja a *webbapp1 alkalmazásazonosítóját.*

Használhatja az aktuális **alkalmazások** tapasztalat, vagy az új, egységes **alkalmazás regisztrációk (Előzetes verzió)** tapasztalat, hogy az alkalmazás azonosítóját. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. A **Kezelés csoportban**válassza az **Alkalmazások**lehetőséget.
1. Rögzítse az értéket a *webapp1* vagy egy másik, korábban létrehozott alkalmazás **APPLICATION ID** oszlopában.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza a **Saját alkalmazások** lapot.
1. Rögzítse az értéket az **Alkalmazás (ügyfél) azonosító** oszlopban a *webapp1* vagy egy másik alkalmazás korábban létrehozott.

* * *

## <a name="get-token-issuer-endpoint"></a>Token kiállítói végpont bekéselése

Ezután az Azure AD B2C-felhasználói folyamatok egyikének jól ismert konfigurációs URL-címét. Az Azure API Managementben támogatni kívánt tokenkiállító végpontURI-jára is szüksége van.

1. Tallózással keresse meg az Azure AD B2C-bérlőt az [Azure Portalon.](https://portal.azure.com)
1. A **Házirendek**csoportban válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
1. Jelöljön ki egy meglévő házirendet, például *B2C_1_signupsignin1,* majd válassza **a Felhasználói folyamat futtatása**lehetőséget.
1. Rögzítse az URL-címet a lap tetején található **Felhasználói folyamat futtatása** címsor alatt megjelenő hivatkozásban. Ez az URL-cím az OpenID Connect jól ismert felderítési végpont a felhasználói folyamat, és használja a következő szakaszban, amikor konfigurálja a bejövő szabályzat ot az Azure API Management.

    ![Jól ismert URI-hivatkozás az Azure Portal Futtatás a lapján](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Jelölje ki a hiperhivatkozást a jól ismert OpenID Connect oldalra.
1. A böngészőben megnyíló lapon `issuer` jegyezze fel az értéket, például:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Ezt az értéket a következő szakaszban használja, amikor konfigurálja az API-t az Azure API Managementben.

Most már két URL-t kell rögzíteni a következő szakaszban való használatra: az OpenID Connect jól ismert konfigurációs végpont URL-címét és a kibocsátó URI-ját. Példa:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Bejövő házirend konfigurálása az Azure API Management ben

Most már készen áll a bejövő szabályzat hozzáadására az Azure API Managementben, amely érvényesíti az API-hívásokat. Egy [JWT-érvényesítési](../api-management/api-management-access-restriction-policies.md#ValidateJWT) szabályzat hozzáadásával, amely ellenőrzi a közönség és a kibocsátó egy hozzáférési jogkivonatot, biztosíthatja, hogy csak az érvényes jogkivonattal rendelkező API-hívásokat fogadja el.

1. Tallózással keresse meg az Azure API Management-példányt az [Azure Portalon.](https://portal.azure.com)
1. Válassza az **API-k** lehetőséget.
1. Válassza ki az Azure AD B2C-vel biztonságossá tenni kívánt API-t.
1. Válassza ki a **Tervezés** fület.
1. A Bejövő feldolgozás ** \< / ** **csoportban**válassza a házirendkód-szerkesztő megnyitásához válassza a lehetőséget.
1. Helyezze a `<validate-jwt>` következő `<inbound>` címkét a házirendbe.

    1. Frissítse `url` az elem `<openid-config>` értékét a házirend jól ismert konfigurációs URL-címével.
    1. Frissítse `<audience>` az elemet a B2C-bérlőben korábban létrehozott alkalmazás (például *webapp1)* alkalmazásazonosítójával.
    1. Frissítse `<issuer>` az elemet a korábban rögzített tokenkiállító-végponttal.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Biztonságos API-hozzáférés ellenőrzése

Annak érdekében, hogy csak a hitelesített hívók férhessenek hozzá az API-hoz, érvényesítheti az Azure API Management konfigurációját, ha meghívja az API-t a [Postman](https://www.getpostman.com/)segítségével.

Az API-híváshoz az Azure AD B2C által kiadott hozzáférési jogkivonatra és egy APIM-előfizetési kulcsra is szükség van.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Először szüksége van egy jogkivonat ot az Azure `Authorization` AD B2C a postás fejlécében való használathoz. A regisztráció/bejelentkezés felhasználói folyamat **futtatása funkcióval** kaphat egyet, amelyet az egyik előfeltételként kellett volna létrehoznia.

1. Tallózással keresse meg az Azure AD B2C-bérlőt az [Azure Portalon.](https://portal.azure.com)
1. A **Házirendek**csoportban válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
1. Válasszon ki egy meglévő regisztrációs/bejelentkezési felhasználói folyamatot, például *B2C_1_signupsignin1.*
1. Az **Alkalmazás csoportban**válassza a *webapp1 lehetőséget.*
1. A **Válasz URL-címén**válassza a lehetőséget. `https://jwt.ms`
1. Válassza **a Felhasználói folyamat futtatása**lehetőséget.

    ![Felhasználói folyamatlap futtatása a bejelentkezési felhasználói folyamatra való feliratkozáshoz az Azure Portalon](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Fejezze be a bejelentkezést. Át kell irányítani a. `https://jwt.ms`
1. Rögzítse a böngészőben megjelenített kódolt tokenértéket. Ezt a tokenértéket használja a Postman engedélyezési fejlécéhez.

    ![Kódolt tokenérték jelenik meg a jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API-előfizetési kulcs beszerezni

Egy ügyfélalkalmazás (ebben az esetben postman), amely meghívja a közzétett API-t tartalmaznia kell egy érvényes API Management-előfizetési kulcsot az API-ra vonatkozó HTTP-kérések. A Postman HTTP-kérelembe való bekerülési előfizetési kulcs ának bekérése:

1. Tallózással keresse meg az Azure API Management szolgáltatáspéldányát az [Azure Portalon.](https://portal.azure.com)
1. Válassza az **Előfizetések** lehetőséget.
1. Válassza a három pont lehetőséget a **Termék: Korlátlan**hoz, majd a **Billentyűk megjelenítése/elrejtése lehetőséget.**
1. Jegyezze fel a termék **elsődleges kulcsát.** Ezt a kulcsot `Ocp-Apim-Subscription-Key` használja a fejléchez a HTTP-kérelemben a Postman ben.

![Előfizetési kulcs lap az Azure Portalon kiválasztott Kulcsok megjelenítése/elrejtése funkcióval](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Biztonságos API-hívás tesztelése

A hozzáférési jogkivonat és az APIM-előfizetési kulcs rögzített, most már készen áll annak tesztelésére, hogy helyesen konfigurált a biztonságos hozzáférést az API-hoz.

1. Hozzon `GET` létre egy új kérelmet a [Postman](https://www.getpostman.com/). A kérelem URL-címéhez adja meg a hangszórók listáját végpontja az API-t közzétett egyik előfeltételeként. Példa:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Ezután adja hozzá a következő fejléceket:

    | Kulcs | Érték |
    | --- | ----- |
    | `Authorization` | Kódolt tokenérték, amelyet korábban rögzített, előtaggal `Bearer ` (a "Bemutatóra"mező utáni szóközt is tartalmazza) |
    | `Ocp-Apim-Subscription-Key` | A korábban rögzített APIM-előfizetési kulcs |

    A **GET** kérés URL-címének és **fejlécének** a következőhöz hasonlóan kell megjelennie:

    ![Postman felhasználói felület, amely a GET kérelem URL-címét és fejléceit jeleníti meg](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. A kérelem végrehajtásához válassza a **Küldés** gombot a Postman ben. Ha mindent megfelelően konfigurált, akkor json-választ kell adni a konferenciaelőadók gyűjteményével (itt látható: csonkolva):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Nem biztonságos API-hívás tesztelése

Most, hogy sikeres kérést tett, tesztelje a hibaesetet, és győződjön meg arról, hogy az *API-hoz* egy érvénytelen jogkivonattal érkező hívásokat a várt módon utasítja el a rendszer. A teszt végrehajtásának egyik módja, ha hozzáad vagy módosít néhány karaktert a jogkivonat értékében, majd ugyanazt `GET` a kérést hajtja végre, mint korábban.

1. Adjon hozzá több karaktert a token értékéhez egy érvénytelen jogkivonat szimulálásához. Adja hozzá például az "INVALID" értéket a tokenértékhez:

    ![A Postman felhasználói felületfejlécek szakasza, amely a tokenhez hozzáadott ÉRVÉNYTELEN](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. A kérés végrehajtásához kattintson a **Küldés** gombra. Érvénytelen jogkivonat nál a várt `401` eredmény egy nem engedélyezett állapotkód:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Ha látja `401` az állapotkódot, ellenőrizte, hogy csak az Azure AD B2C által kiadott érvényes hozzáférési jogkivonattal rendelkező hívók tehetnek sikeres kéréseket az Azure API-felügyeleti API-ra.

## <a name="support-multiple-applications-and-issuers"></a>Több alkalmazás és kibocsátó támogatása

Számos alkalmazás általában egyetlen REST API-val kommunikál. Annak engedélyezéséhez, hogy az API több alkalmazáshoz szánt jogkivonatokat fogadjon el, adja hozzá az alkalmazásazonosítókat az `<audiences>` APIM bejövő házirendeleméhez.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Hasonlóképpen több jogkivonat-kibocsátók támogatásához adja hozzá `<issuers>` a végpontURI-kat az APIM bejövő házirend eleméhez.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Áttelepítés a b2clogin.com

Ha rendelkezik egy APIM API-val, amely `login.microsoftonline.com` ellenőrzi az örökölt végpont által kibocsátott jogkivonatokat, telepítse át az API-t és az azt hívó alkalmazásokat [a b2clogin.com](b2clogin.md)által kibocsátott jogkivonatok használatára.

A szakaszos áttelepítés végrehajtásához kövesse ezt az általános folyamatot:

1. Add támogatás az APIM bejövő szabályzat a b2clogin.com és login.microsoftonline.com által kiadott jogkivonatok.
1. Frissítse az alkalmazások at egyenként a b2clogin.com végponttokenek beszerzéséhez.
1. Miután az összes alkalmazás helyesen szerzi be a jogkivonatokat b2clogin.com, távolítsa el a login.microsoftonline.com által kiadott jogkivonatok támogatását az API-ból.

A következő példa APIM bejövő szabályzat bemutatja, hogyan fogadja el a b2clogin.com és login.microsoftonline.com által kiadott jogkivonatokat. Emellett támogatja a két alkalmazás API-kérelmeit.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>További lépések

Az Azure API-kezelési szabályzatokkal kapcsolatos további részleteket az [APIM-szabályzat referenciaindexében](../api-management/api-management-policies.md)talál.

Az OWIN-alapú webes API-k és alkalmazásaik áttelepítéséről az [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md)b2clogin.com.
