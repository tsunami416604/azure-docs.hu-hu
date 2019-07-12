---
title: Oktatóanyag – identitás-szolgáltatóktól ad hozzá az alkalmazások – Azure Active Directory B2C-vel
description: Ismerje meg, hogyan adja hozzá az identitás-szolgáltatóktól az alkalmazások az Azure Active Directory B2C az Azure portal használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655219"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel

Az Ön alkalmazásai érdemes engedélyezése a felhasználók számára, hogy jelentkezzen be a különböző identitásszolgáltatókkal. Egy *identitásszolgáltató* hoz létre, tárolja és kezeli a azonosító adatok, miközben alkalmazások hitelesítési szolgáltatásokat biztosít. Az Azure Active Directory (Azure AD) B2C-vel által támogatott Identitásszolgáltatók is hozzáadhat a [felhasználókövetési adatai](active-directory-b2c-reference-policies.md) az Azure portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az identity provider alkalmazások létrehozása
> * Az Identitásszolgáltatók hozzáadása a bérlőhöz
> * Az Identitásszolgáltatók hozzáadása a felhasználói folyamathoz

Az alkalmazások csak egy identitásszolgáltató általában használhat, de lehetősége van Továbbiak hozzáadásához. Az oktatóanyag bemutatja, hogyan adhat az Azure AD Identitásszolgáltatóként, és a egy Facebook-identitásszolgáltató az alkalmazás. Az Identitásszolgáltatók ezek mindegyikét hozzáadása az alkalmazáshoz nem kötelező. Más identitásszolgáltatókkal is hozzáadhat például [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), vagy [Twitter](active-directory-b2c-setup-twitter-app.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Felhasználói folyamat létrehozása](tutorial-create-user-flows.md) regisztráljon, és jelentkezzen be az alkalmazást használók.

## <a name="create-applications"></a>Alkalmazások létrehozása

Szolgáltató alkalmazások adja meg a azonosítóját és kulcsát az Azure AD B2C-bérlőben való kommunikáció engedélyezése. Az oktatóanyag ezen szakaszában hoz létre egy Azure AD-alkalmazást, és a egy Facebook-alkalmazást, amelyről azonosítókat és az identitás-szolgáltatóktól hozzáadása a bérlői kulcsok beolvasása. Ha csak egy identitásszolgáltatóval, csak az alkalmazás az adott szolgáltató létrehozása kell.

### <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Bejelentkezés engedélyezése a felhasználók számára az Azure ad-ből, meg kell regisztrálni egy alkalmazást az Azure AD-bérlővel. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy használja az Azure AD-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD-bérlő tartalmazó könyvtárra.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
1. Válassza ki **új regisztrációs**.
1. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
1. Fogadja el a kijelölt **fiókok csak a szervezeti könyvtárban található** ehhez az alkalmazáshoz.
1. Az a **átirányítási URI-t**, fogadja el a **webes** , és adja meg a következő URL-cím csak kisbetűk, és cserélje le a `your-B2C-tenant-name` az Azure AD B2C-bérlő nevével.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Most kell használnia az összes URL-címek [b2clogin.com](b2clogin.md).

1. Válassza ki **regisztrálása**, majd rögzítse a **Alkalmazásazonosítót (ügyfél)** , amelyet használhat egy későbbi lépésben.
1. A **kezelés** az alkalmazás menüjében válassza **tanúsítványok és titkos kulcsok**, majd **új titkos ügyfélkulcsot**.
1. Adjon meg egy **leírás** számára a titkos ügyfélkulcsot. Például: `Azure AD B2C App Secret`.
1. Válassza ki a lejárati idejét. A jelen alkalmazás esetében fogadja el a kijelölt **az 1 év**.
1. Válassza ki **Hozzáadás**, majd jegyezze fel az új ügyfél titkos kód, amelyet használhat egy későbbi lépésben.

### <a name="create-a-facebook-application"></a>Egy Facebook-alkalmazás létrehozása

Egy Facebook-fiókban identitásszolgáltatójaként az Azure AD B2C-vel használandó kell létre alkalmazást, a Facebook. Ha még nem rendelkezik egy Facebook-fiókban, beszerezheti a [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Jelentkezzen be a [Facebook-fejlesztőknek](https://developers.facebook.com/) Facebook-fiókja hitelesítő adataival.
1. Ha ezt még nem tette meg, hogy Facebook-fejlesztőként regisztrálnia kell. Ehhez válassza ki a **Ismerkedés** az oldal jobb felső sarkában, fogadja el a Facebook-szabályzatokat, és végezze el a regisztrációs lépéseket.
1. Válassza ki **saját alkalmazások** , majd **alkalmazás létrehozása**.
1. Adjon meg egy **megjelenítendő név** és a egy érvényes **kapcsolattartó E-mail**.
1. Kattintson a **Alkalmazásazonosító létrehozása**. Ez előfordulhat, hogy fogadja el a Facebook-platform házirendek és a egy online biztonsági ellenőrzés.
1. Válassza ki **beállítások** > **alapszintű**.
1. Válasszon egy **kategória**, például `Business and Pages`. Ez az érték Facebook által igényelt, de nem használják az Azure AD B2C-t.
1. Válassza a lap alján **hozzáadása Platform**, majd válassza ki **webhely**.
1. A **webhely URL-címe**, adja meg `https://your-tenant-name.b2clogin.com/` cseréje `your-tenant-name` a bérlő nevével.
1. Adjon meg egy URL-címet a **adatvédelmi szabályzat URL-címe**, például `http://www.contoso.com/`. Az adatvédelmi szabályzat URL-címe egy oldalon kell karbantartani, adja meg az alkalmazás adatvédelmi nyilatkozatát.
1. Válassza ki **módosítások mentése**.
1. Az oldal tetején lévő jegyezze fel az értékét **Alkalmazásazonosító**.
1. A **titkos Alkalmazáskulcs**válassza **megjelenítése** , és jegyezze fel az értékét. Az Alkalmazásazonosító és a titkos Alkalmazáskulcs segítségével konfigurálhatja a Facebook Identitásszolgáltatóként, a bérlőben. **Alkalmazás titkos kulcsát** egy fontos biztonsági hitelesítő adat, amely biztonságosan tárolja.
1. Mellett kattintson a plusz **termékek**, majd **Facebook-bejelentkezés**válassza **beállítása**.
1. A **Facebook-bejelentkezés** a bal oldali menüben válassza ki a **beállítások**.
1. A **érvényes OAuth átirányítási URI-k**, adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Cserélje le `your-tenant-name` a bérlő nevével. Válassza ki **módosítások mentése** az oldal alján.
1. Elérhetővé a Facebook-alkalmazás számára az Azure AD B2C-t, kattintson a **állapota** választó tetején, az oldal jobb kapcsolja **a** legyen az alkalmazás nyilvános, és kattintson a **megerősítése** . Ezen a ponton kell módosítani az állapotát a **fejlesztési** való **élő**.

## <a name="add-the-identity-providers"></a>Az identitásszolgáltató hozzáadása

Miután létrehozta az alkalmazást a hozzáadni kívánt identitásszolgáltató, az identitásszolgáltató hozzá a bérlőn.

### <a name="add-the-azure-active-directory-identity-provider"></a>Az Azure Active Directory identitásszolgáltató hozzáadása

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD B2C-bérlő tartalmazó könyvtárra.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
1. Adjon meg egy **neve**. Adja meg például *Contoso Azure ad-ben*.
1. Válassza ki **identitásszolgáltató típusa**válassza **Open ID Connect (előzetes verzió)** , és kattintson a **OK**.
1. Kattintson a **az identitásszolgáltató beállítása**
1. A **metaadatok URL-címe**, adja meg a következő URL-címet, és cserélje le `your-AD-tenant-domain` a tartomány nevét az Azure AD-bérlő.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. A **ügyfél-azonosító**, adja meg a *Alkalmazásazonosítót (ügyfél)* korábban feljegyzett.
1. A **titkos Ügyfélkód**, adja meg a *titkos Ügyfélkód* korábban feljegyzett értéket.
1. Szükség esetén adjon meg egy értéket **Domain_hint**. Például: `ContosoAD`. [Tartomány mutatók](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) irányelvek, a hitelesítési kérelmet egy alkalmazás részét képező vannak. Gyorsítsa fel a felhasználó bejelentkezési oldalra összevont IdP használható. Ezek segítségével egy több-bérlős alkalmazás a felhasználó gyorsabban rögtön az a vállalati arculattal vagy a bérlő Azure AD bejelentkezési oldala.
1. Kattintson az **OK** gombra.
1. Válassza ki **ezen identitásszolgáltató jogcímeinek hozzárendelése** és állítsa be a következő jogcímek:

    - A **Felhasználóazonosító**, adja meg `oid`.
    - A **megjelenítendő név**, adja meg `name`.
    - A **Utónév**, adja meg `given_name`.
    - A **Vezetéknév**, adja meg `family_name`.
    - A **E-mail**, adja meg `unique_name`.

1. Válassza ki **OK**, majd **létrehozás** kattintva mentse a konfigurációt.

### <a name="add-the-facebook-identity-provider"></a>A Facebook-identitásszolgáltató hozzáadása

1. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
1. Adjon meg egy **neve**. Adja meg például *Facebook*.
1. Válassza ki **identitásszolgáltató típusa**válassza **Facebook**, majd **OK**.
1. Válassza ki **az identitásszolgáltató beállítása** , és adja meg a *Alkalmazásazonosító* korábban rögzített a **ügyfél-azonosító**.
1. Adja meg a *titkos Alkalmazáskulcs* feljegyzett a **titkos Ügyfélkód**.
1. Válassza ki **OK** majd **létrehozás** a Facebook-konfiguráció mentéséhez.

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

Az oktatóanyagban, amelyek az Előfeltételek részeként létrehozott egy felhasználói folyamat esetében a regisztráció és bejelentkezés nevű *B2C_1_signupsignin1*. Ebben a szakaszban az identitás-szolgáltatóktól történő hozzáadása a *B2C_1_signupsignin1* felhasználói folyamatot.

1. Válassza ki **felhasználókövetési adatai (szabályzatok)** , majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. Válassza ki **Identitásszolgáltatók**, jelölje be a **Facebook** és **Contoso Azure ad-ben** identitásszolgáltatókat, amelyek adott hozzá.
3. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
1. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
1. Válassza ki **felhasználói folyamat futtatása**, és a egy identitásszolgáltatótól az előzőleg hozzáadott majd jelentkezzen be.
1. Az egyéb identitás-szolgáltatóktól hozzáadott ismételje meg az 1 – 3.

Ha a bejelentkezési művelet sikeres, a program átirányítja `https://jwt.ms` megjeleníti a dekódolt Token, hasonló:

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

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Az identity provider alkalmazások létrehozása
> * Az Identitásszolgáltatók hozzáadása a bérlőhöz
> * Az Identitásszolgáltatók hozzáadása a felhasználói folyamathoz

Ezután megtudhatja, hogyan szabhatja testre a felhasználói felületen, a lapok jelenik meg az alkalmazások identitáskezelési feladatok részeként:

> [!div class="nextstepaction"]
> [Az alkalmazások az Azure Active Directory B2C a felhasználói felület testreszabása](tutorial-customize-ui.md)
