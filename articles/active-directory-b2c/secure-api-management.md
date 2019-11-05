---
title: Azure API Management API biztonságossá tétele Azure Active Directory B2C használatával
description: Ismerje meg, hogyan használhatja a Azure Active Directory B2C által kiadott hozzáférési jogkivonatokat az Azure API Management API-végpontok biztonságossá tételéhez.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 531f6d86d57be550d0a1147e131d93ae6e298406
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474744"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure API Management API biztonságossá tétele Azure AD B2C

Megtudhatja, hogyan korlátozhatja az Azure API Management-(APIM-) API-hoz való hozzáférést a Azure Active Directory B2C (Azure AD B2C) hitelesítéssel rendelkező ügyfelek számára. A cikk lépéseit követve hozzon létre és tesztelje a APIM bejövő házirendjét, amely korlátozza a hozzáférést csak azokra a kérésekre, amelyek érvényes Azure AD B2C kiállított hozzáférési jogkivonatot tartalmaznak.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek folytatása előtt a következő erőforrásokra van szükség:

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* Az [alkalmazás regisztrálva](tutorial-register-applications.md) van a bérlőben
* A bérlőn [létrehozott felhasználói folyamatok](tutorial-create-user-flows.md)
* [Közzétett API](../api-management/import-and-publish.md) az Azure API Management
* [Poster](https://www.getpostman.com/) a biztonságos hozzáférés teszteléséhez (nem kötelező)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C alkalmazás AZONOSÍTÓjának beolvasása

Ha az Azure-API Managementban található API-t Azure AD B2C használatával védi, több értékre van szüksége a APIM-ben létrehozott [bejövő házirendhez](../api-management/api-management-howto-policies.md) . Először jegyezze fel egy korábban a Azure AD B2C-bérlőben létrehozott alkalmazás AZONOSÍTÓját. Ha az előfeltételekben létrehozott alkalmazást használja, használja a *webbapp1*alkalmazás-azonosítóját.

Az alkalmazás AZONOSÍTÓjának beszerzéséhez használhatja az aktuális **alkalmazások** vagy az új Unified **Alkalmazásregisztrációk (előzetes verzió)** felhasználói felületét. [További információ az előzetes](http://aka.ms/b2cappregintro)verzióról.

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. A **kezelés**területen válassza az **alkalmazások**lehetőséget.
1. Jegyezze fel a *webapp1* vagy egy korábban létrehozott másik alkalmazás **alkalmazás-azonosító** oszlopában található értéket.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza a **birtokolt alkalmazások** fület.
1. Jegyezze fel a *webapp1* vagy egy korábban létrehozott másik alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosító** oszlopában található értéket.

* * *

## <a name="get-token-issuer-endpoint"></a>Jogkivonat kiállítói végpontjának beolvasása

Ezután szerezze be a jól ismert konfigurációs URL-címet az egyik Azure AD B2C felhasználói folyamathoz. Szüksége lesz az Azure API Management támogatni kívánt jogkivonat-kiállítói végpont URI-ra is.

1. Tallózással keresse meg Azure AD B2C-bérlőjét a [Azure Portalban](https://portal.azure.com).
1. A **házirendek**területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válasszon ki egy meglévő szabályzatot, például *B2C_1_signupsignin1*, majd válassza a **felhasználói folyamat futtatása**lehetőséget.
1. Jegyezze fel az URL-címet a lap tetején található **felhasználói folyamat futtatása** fejléc alatt látható hiperhivatkozásban. Ez az URL-cím az OpenID Connect jól ismert felderítési végpontja a felhasználói folyamat számára, és a következő szakaszban azt használja, amikor konfigurálja a bejövő házirendet az Azure API Managementban.

    ![Jól ismert URI-hivatkozás a Azure Portal futtatása lapon](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Válassza ki a hiperhivatkozást, hogy megkeresse az OpenID Connect jól ismert konfigurációs lapját.
1. A böngészőben megnyíló lapon jegyezze fel a `issuer` értéket, például:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Ezt az értéket a következő szakaszban kell használni az API Azure API Management-ban való konfigurálásakor.

Most két, a következő szakaszban való használatra rögzített URL-címet kell megadnia: az OpenID Connect jól ismert konfigurációs végpont URL-címét és a kibocsátó URI-JÁT. Példa:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Bejövő házirend konfigurálása az Azure-ban API Management

Most már készen áll arra, hogy hozzáadja a bejövő szabályzatot az Azure API Managementban, amely ellenőrzi az API-hívásokat. Egy olyan [JWT-érvényesítési](../api-management/api-management-access-restriction-policies.md#ValidateJWT) szabályzat hozzáadásával, amely egy hozzáférési jogkivonatban ellenőrzi a célközönséget és a kiállítót, biztosíthatja, hogy csak az érvényes tokent tartalmazó API-hívások legyenek elfogadva.

1. Tallózással keresse meg az Azure API Management-példányát a [Azure Portalban](https://portal.azure.com).
1. Válassza az **API-k** lehetőséget.
1. Válassza ki azt az API-t, amelyet Azure AD B2C szeretne biztonságossá tenni.
1. Válassza ki a **Tervezés** fület.
1. A **bejövő feldolgozás**területen válassza a **\</\>** lehetőséget a szabályzat-szerkesztő megnyitásához.
1. Helyezze a következő `<validate-jwt>` címkét a `<inbound>` házirendbe.

    1. Frissítse a `<openid-config>` elem `url` értékét a szabályzat jól ismert konfigurációs URL-címével.
    1. Frissítse az `<audience>` elemet a B2C-bérlőben korábban létrehozott alkalmazás (például *webapp1*) alkalmazás-azonosítójával.
    1. Frissítse a `<issuer>` elemet a korábban feljegyzett jogkivonat-kiállítói végponttal.

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

Annak biztosítása érdekében, hogy csak a hitelesített hívók férhessenek hozzá az API-hoz, a [Poster](https://www.getpostman.com/)használatával ellenőrizheti az Azure API Management konfigurációját.

Az API meghívásához a Azure AD B2C által kiállított hozzáférési jogkivonat és egy APIM-előfizetési kulcs szükséges.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Először Azure AD B2C által kiállított jogkivonatra van szüksége, amelyet a Poster `Authorization` fejlécében kell használni. A regisztrálási/bejelentkezési felhasználói folyamat **Futtatás most** funkciójának használatával beszerezhet egyet, amelyet az előfeltételek egyikének kell létrehoznia.

1. Tallózással keresse meg Azure AD B2C-bérlőjét a [Azure Portalban](https://portal.azure.com).
1. A **házirendek**területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válasszon ki egy meglévő regisztrációs vagy bejelentkezési felhasználói folyamatot, például *B2C_1_signupsignin1*.
1. **Alkalmazás**esetében válassza a *webapp1*lehetőséget.
1. A **Válasz URL-címéhez**válassza a `https://jwt.ms`lehetőséget.
1. Válassza a **felhasználói folyamat futtatása**lehetőséget.

    ![Felhasználói folyamat futtatása lap a bejelentkezéshez használt felhasználói folyamat Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Fejezze be a bejelentkezést. `https://jwt.ms`re kell átirányítani.
1. Jegyezze fel a böngészőben megjelenő kódolt jogkivonat értékét. Ezt a jogkivonat-értéket használja a Poster hitelesítési fejlécében.

    ![A jwt.ms megjelenített kódolt jogkivonat értéke](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API-előfizetés kulcsának beolvasása

A közzétett API-t meghívó ügyfélalkalmazás (ebben az esetben a posta) érvényes API Management előfizetési kulcsot kell tartalmaznia a HTTP-kéréseiben az API-hoz. A Poster HTTP-kérelmében szerepeltetni kívánt előfizetési kulcs beszerzése:

1. Tallózással keresse meg az Azure API Management Service-példányt a [Azure Portalban](https://portal.azure.com).
1. Válassza az **Előfizetések** lehetőséget.
1. Válassza ki a termék három pontját **: korlátlan**, majd válassza a **kulcsok megjelenítése/elrejtése**lehetőséget.
1. Jegyezze fel a termék **elsődleges kulcsát** . Ezt a kulcsot a Poster-ben a HTTP-kérelemben szereplő `Ocp-Apim-Subscription-Key` fejléchez használhatja.

![Előfizetési kulcs lapja a Azure Portalban kiválasztott kulcsok megjelenítése/elrejtése elemnél](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Biztonságos API-hívás tesztelése

Ha a hozzáférési jogkivonat és a APIM-előfizetés kulcsa rögzített, most már készen áll annak tesztelésére, hogy helyesen konfigurálta-e a biztonságos hozzáférést az API-hoz.

1. Hozzon létre egy új `GET` kérelmet a [Poster](https://www.getpostman.com/)-ben. A kérelem URL-címéhez adja meg az előfeltételként közzétett API hangszórók listája végpontját. Példa:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Ezután adja hozzá a következő fejléceket:

    | Jelmagyarázat | Érték |
    | --- | ----- |
    | `Authorization` | A korábban feljegyzett kódolt jogkivonat-érték, `Bearer ` (a "tulajdonos" utáni területtel együtt) |
    | `Ocp-Apim-Subscription-Key` | A korábban rögzített APIM-előfizetés kulcsa |

    A **Get** kérelem URL-címének és **fejlécének** a következőhöz hasonlóan kell megjelennie:

    ![Poster felhasználói felület, amely a GET kérelem URL-címét és fejléceit mutatja](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. A kérelem végrehajtásához kattintson a Poster **Send (Küldés** ) gombjára. Ha mindent helyesen konfigurált, akkor a rendszer egy JSON-választ jelenít meg a konferencia-hangszórók gyűjteményéből (itt látható csonkolt):

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

Most, hogy sikeresen elvégezte a kérést, tesztelje a hiba esetét, hogy a rendszer a várt módon visszautasítja az API-nak egy *érvénytelen* tokent tartalmazó hívásokat. A teszt végrehajtásának egyik módja a jogkivonat értékében szereplő néhány karakter hozzáadása vagy módosítása, majd a korábban megegyező `GET` kérelem végrehajtása.

1. Adjon hozzá több karaktert a jogkivonat értékéhez egy érvénytelen token szimulálása érdekében. Például adja hozzá az "Érvénytelen" értéket a jogkivonat értékéhez:

    ![A Poster felhasználói felületének fejlécek szakasza, amely a jogkivonat érvénytelen hozzáadását mutatja](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. A kérelem végrehajtásához kattintson a **Küldés** gombra. Érvénytelen token esetén a várt eredmény egy `401` nem engedélyezett állapotkód:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Ha a `401` állapotkód jelenik meg, akkor ellenőrizte, hogy csak a Azure AD B2C által kiadott érvényes hozzáférési jogkivonattal rendelkező hívók tudnak sikeres kéréseket készíteni az Azure API Management API-hoz.

## <a name="support-multiple-applications-and-issuers"></a>Több alkalmazás és kiállító támogatása

Számos alkalmazás általában egyetlen REST APIsal kommunikál. Annak engedélyezéséhez, hogy az API több alkalmazáshoz tartozó jogkivonatokat fogadjon el, adja hozzá az alkalmazás azonosítóit a APIM bejövő házirend `<audiences>` eleméhez.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Hasonlóképpen, a több jogkivonat-kiállítók támogatásához adja hozzá a végponti URI-ket a APIM bejövő házirend `<issuers>` eleméhez.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrálás a b2clogin.com

Ha rendelkezik olyan APIM API-val, amely az örökölt `login.microsoftonline.com` végpont által kiállított jogkivonatokat érvényesíti, akkor át kell telepítenie az API-t és az azt meghívó alkalmazásokat a [b2clogin.com](b2clogin.md)által kiállított jogkivonatok használatára.

Ezt az általános eljárást követve elvégezheti a szakaszos áttelepítést:

1. Adja hozzá a APIM bejövő szabályzatának támogatását a b2clogin.com és a login.microsoftonline.com által kiállított jogkivonatokhoz.
1. Frissítse alkalmazásait egyenként a tokenek b2clogin.com-végpontból való beszerzéséhez.
1. Miután az összes alkalmazás helyesen beszerezte a jogkivonatokat a b2clogin.com-ból, távolítsa el a login.microsoftonline.com által kibocsátott jogkivonatok támogatását az API-ból.

A következő példában a APIM bejövő házirendje azt szemlélteti, hogyan fogadhatja el a b2clogin.com és a login.microsoftonline.com által kiállított jogkivonatokat. Emellett két alkalmazás API-kérelmeit is támogatja.

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

Az Azure API Management-szabályzatokkal kapcsolatos további részletekért tekintse meg a [APIM házirend-referenciájának indexét](../api-management/api-management-policies.md).

A OWIN-alapú webes API-k és alkalmazásaik áttelepítésével kapcsolatos információkat a b2clogin.com a [OWIN-alapú webes api b2clogin.com](multiple-token-endpoints.md)-re való áttelepítését ismertető témakörben talál.
