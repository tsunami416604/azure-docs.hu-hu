---
title: 'Oktatóanyag: Identitásszolgáltatók hozzáadása az alkalmazásokhoz'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá identitásszolgáltatókat az azure-beli Active Directory B2C-ben az Azure-portálon.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183516"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: Identitásszolgáltatók hozzáadása az alkalmazásokhoz az Azure Active Directory B2C-ben

Az alkalmazásokban érdemes lehet engedélyezni a felhasználók számára, hogy jelentkezzen be a különböző identitás-szolgáltatók. Az *identitásszolgáltató* létrehozza, karbantartja és kezeli az identitásadatokat, miközben hitelesítési szolgáltatásokat nyújt az alkalmazásoknak. Az Azure Active Directory B2C (Azure AD B2C) által támogatott identitásszolgáltatókat hozzáadhatja a [felhasználói folyamatokhoz](user-flow-overview.md) az Azure Portalhasználatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az identitásszolgáltatói alkalmazások létrehozása
> * Az identitásszolgáltatók hozzáadása a bérlőhöz
> * Az identitásszolgáltatók hozzáadása a felhasználói folyamathoz

Általában csak egy identitásszolgáltatót használ az alkalmazásokban, de lehetősége van további ad hozzá. Ez az oktatóanyag bemutatja, hogyan adhat hozzá egy Azure AD-identitásszolgáltatót és egy Facebook-identitásszolgáltatót az alkalmazáshoz. Mindkét identitásszolgáltató hozzáadása az alkalmazáshoz nem kötelező. Más identitásszolgáltatókat is hozzáadhat, például [az Amazont,](identity-provider-amazon.md) [a GitHubot, a](identity-provider-github.md) [Google-t, a LinkedInt,](identity-provider-linkedin.md)a [Microsoftot](identity-provider-microsoft-account.md)vagy [a Twittert.](identity-provider-twitter.md) [Google](identity-provider-google.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot,](tutorial-create-user-flows.md) amely lehetővé teszi a felhasználók számára, hogy regisztráljanak és jelentkezzenek be az alkalmazásba.

## <a name="create-applications"></a>Alkalmazások létrehozása

Az identitásszolgáltató iszfalka biztosítja az azonosítót és a kulcsot az Azure AD B2C-bérlővel való kommunikáció engedélyezéséhez. Ebben a szakaszban az oktatóanyag, hozzon létre egy Azure AD-alkalmazást, és egy Facebook-alkalmazás, amelyből azonosítók és kulcsok hozzáadása az identitásszolgáltatók a bérlőhöz. Ha csak az egyik identitásszolgáltatót adja hozzá, csak létre kell hoznia az alkalmazást az adott szolgáltatóhoz.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Az Azure AD-ből származó felhasználók bejelentkezésének engedélyezéséhez regisztrálnia kell egy alkalmazást az Azure AD-bérlőn belül. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
1. Csak ehhez az alkalmazáshoz fogadja el a fiókok kiválasztását ebben a **szervezeti címtárban.**
1. Az **átirányításURI,** fogadja el a **web** értékét, és adja meg `your-B2C-tenant-name` a következő URL-t az összes kisbetű, az Azure AD B2C-bérlő nevének helyett.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Mostantól minden URL-nek [b2clogin.com](b2clogin.md)kell használnia.

1. Válassza **a Regisztráció**lehetőséget, majd rögzítse az alkalmazás **(ügyfél) azonosítóját,** amelyet egy későbbi lépésben használ.
1. Az alkalmazás **menükezelés parancsában** válassza a **Tanúsítványok & titkos kulcsok**lehetőséget, majd válassza az Új **ügyféltitok**lehetőséget.
1. Adja meg az ügyféltitok **leírását.** Például: `Azure AD B2C App Secret`.
1. Válassza ki a lejárati időszakot. Ehhez az alkalmazáshoz, fogadja el a kiválasztás **1 év**.
1. Válassza **a Hozzáadás**lehetőséget, majd jegyezze fel az új ügyféltitok értékét, amelyet egy későbbi lépésben használ.

### <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha egy Facebook-fiókot szeretne identitásszolgáltatóként használni az Azure AD B2C-ben, létre kell hoznia egy alkalmazást a Facebookon. Ha még nem rendelkezik Facebook-fiókkal, a [https://www.facebook.com/](https://www.facebook.com/).

1. Jelentkezz be [a Facebookra a fejlesztők számára](https://developers.facebook.com/) a Facebook-fiók hitelesítő adataiddal.
1. Ha még nem tetted meg, facebook-fejlesztőként kell regisztrálnod. Ehhez válaszd az **Első lépések** lehetőséget a lap jobb felső sarkában, fogadd el a Facebook irányelveit, és hajtsd végre a regisztrációs lépéseket.
1. Válassza **a Saját alkalmazások** lehetőséget, majd az Alkalmazás létrehozása **lehetőséget.**
1. Adja meg **a megjelenítendő nevet** és az érvényes **partneri e-mailt.**
1. Kattintson **az Alkalmazásazonosító létrehozása gombra.** Ehhez szükség lehet arra, hogy elfogadd a Facebook platformszabályzatát, és elvégezz egy online biztonsági ellenőrzést.
1. Válassza az > **Alapbeállítások**lehetőséget. **Settings**
1. Válasszon például `Business and Pages` **egy kategóriát.** Ezt az értéket a Facebook nak kell megadnia, de az Azure AD B2C nem használja.
1. A lap alján válassza a **Platform hozzáadása**lehetőséget, majd a **Webhely**lehetőséget.
1. A **Webhely URL-címében**írja be `https://your-tenant-name.b2clogin.com/` a bérlő nevének helyettesítését. `your-tenant-name`
1. Adja meg például `http://www.contoso.com/`az **Adatvédelmi szabályzat URL-címét.** Az adatvédelmi irányelvek URL-címe egy olyan oldal, amelyet azért tart fenn, hogy az alkalmazás adatvédelmi adatait megadhassa.
1. Válassza a **Módosítások mentése lehetőséget.**
1. A lap tetején jegyezze fel az **Alkalmazásazonosító**értékét.
1. Az **Alkalmazástitkos kulcsot**csoportban válassza a **Megjelenítése** és rögzítése lehetőséget. Az Alkalmazásazonosító és az Alkalmazástitok segítségével a Facebookot identitásszolgáltatóként konfigurálhatja a bérlőben. **Az App Secret** egy fontos biztonsági hitelesítő adat, amelyet biztonságosan kell tárolnia.
1. A Termékek csoportban válassza a **Pluszjelet,** majd a **Facebook Bejelentkezés csoportban**válassza a **Beállítás**lehetőséget.
1. A Bal oldali menü **Facebook Bejelentkezés** területén válassza a **Beállítások lehetőséget.**
1. Az Érvényes OAuth átirányítási `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`URI-k mezőbe írja be a ( **érvényességi azonosító) átirányítási urisz-t.** Cserélje `your-tenant-name` le a bérlő nevét. Válassza a **Módosítások mentése lehetőséget** a lap alján.
1. Ha a Facebook-alkalmazást elérhetővé szeretné tenni az Azure AD B2C számára, kattintson az oldal jobb felső részén található **Állapotválasztóra,** és **kapcsolja** be az alkalmazás nyilvánossá tegyük, majd kattintson a **Megerősítés gombra.** Ezen a ponton az állapotnak **fejlesztési** ről **live-ra**kell változnia.

## <a name="add-the-identity-providers"></a>Az identitásszolgáltatók hozzáadása

Miután létrehozta az alkalmazást a hozzáadni kívánt identitásszolgáltatóhoz, hozzáadja az identitásszolgáltatót a bérlőhöz.

### <a name="add-the-azure-active-directory-identity-provider"></a>Az Azure Active Directory identitásszolgáltatójának hozzáadása

1. Győződjön meg arról, hogy az Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Új OpenID Connect szolgáltató**lehetőséget.
1. Írjon be egy **nevet**. Írja be például a *Contoso Azure AD értéket.*
1. A **metaadatok url-címéhez** `your-AD-tenant-domain` adja meg a következő URL-címet, amely az Azure AD-bérlő tartománynevére lép:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. **Ügyfélazonosító**esetén adja meg a korábban rögzített alkalmazásazonosítót.
1. Az **Ügyféltitkos kulcsot**adja meg a korábban rögzített ügyféltitok.
1. Hagyja meg a **Hatókör**, **Választípus**és Válasz mód alapértelmezett **értékeit.**
1. (Nem kötelező) Adjon meg egy értéket **a Domain_hint.** Például *a ContosoAD*. [A tartományi tanácsok](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) olyan irányelvek, amelyek egy alkalmazás hitelesítési kérelmében szerepelnek. Ezek segítségével felgyorsíthatja a felhasználó tetszésszerint idP bejelentkezési oldalra. Vagy egy több-bérlős alkalmazás használhatja őket, hogy gyorsítsa fel a felhasználót közvetlenül a márkajelzéssel ellátható Azure AD bejelentkezési oldalra a bérlő számára.
1. Az **Identitásszolgáltató jogcímekkel kapcsolatos hozzárendelése**csoportban adja meg a következő jogcímleképezési értékeket:

    * **Felhasználói azonosító**: *oid*
    * **Megjelenítendő név**: *név*
    * **Utónév**: *given_name*
    * **Vezetéknév**: *family_name*
    * **E-mail :** *unique_name*

1. Kattintson a **Mentés** gombra.

### <a name="add-the-facebook-identity-provider"></a>A Facebook-identitásszolgáltató hozzáadása

1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **Facebook**lehetőséget.
1. Írjon be egy **nevet**. Például a *Facebook*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Facebook-alkalmazás alkalmazásazonosítóját.
1. Az **ügyfél titkos kulcsot**adja meg az alkalmazás titkos, hogy a rögzített.
1. Kattintson a **Mentés** gombra.

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

Az előfeltételként elvégzett oktatóanyagban létrehozott egy felhasználói folyamatot a *B2C_1_signupsignin1*nevű regisztrációhoz és bejelentkezéshez. Ebben a szakaszban adja hozzá az identitásszolgáltatókat a *B2C_1_signupsignin1* felhasználói folyamathoz.

1. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd válassza ki a felhasználói folyamat *B2C_1_signupsignin1.*
2. Válassza **ki az identitásszolgáltatók**, válassza ki a **Facebook** és a **Contoso Azure AD** identitásszolgáltatók, amelyek hozzáadott.
3. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat Áttekintés lapján válassza a **Felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazáshoz**válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie. `https://jwt.ms`
1. Válassza **a Felhasználói folyamat futtatása**lehetőséget, majd jelentkezzen be egy korábban hozzáadott identitásszolgáltatóval.
1. Ismételje meg az 1–3.

Ha a bejelentkezési művelet sikeres, a parancs `https://jwt.ms` átirányítja, amely a dekódolt jogkivonatot jeleníti meg, hasonlóan a következőkhöz:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Az identitásszolgáltatói alkalmazások létrehozása
> * Az identitásszolgáltatók hozzáadása a bérlőhöz
> * Az identitásszolgáltatók hozzáadása a felhasználói folyamathoz

Ezután megtudhatja, hogyan szabhatja testre a felhasználók számára megjelenített oldalak felhasználói felületét az alkalmazásokban való identitásélményük részeként:

> [!div class="nextstepaction"]
> [Az alkalmazások felhasználói felületének testreszabása az Azure Active Directory B2C-ben](tutorial-customize-ui.md)
