---
title: Oktatóanyag – identitás-szolgáltatóktól ad hozzá az alkalmazások – Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adja hozzá az identitás-szolgáltatóktól az alkalmazások az Azure Active Directory B2C az Azure portal használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ef264622be1e66c98f8c7f17cf3e04b3239e8553
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338275"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: Identitásszolgáltató hozzáadása az alkalmazásait az Azure Active Directory B2C-vel

Az Ön alkalmazásai érdemes engedélyezése a felhasználók számára, hogy jelentkezzen be a különböző identitásszolgáltatókkal. Egy *identitásszolgáltató* hoz létre, tárolja és kezeli a azonosító adatok, miközben alkalmazások hitelesítési szolgáltatásokat biztosít. Az Azure Active Directory (Azure AD) B2C-vel által támogatott Identitásszolgáltatók is hozzáadhat a [felhasználókövetési adatai](active-directory-b2c-reference-policies.md) az Azure portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az identity provider alkalmazások létrehozása
> * Az Identitásszolgáltatók hozzáadása a bérlőhöz
> * Az Identitásszolgáltatók hozzáadása a felhasználói folyamathoz

Az alkalmazások csak egy identitásszolgáltató általában használhat, de lehetősége van Továbbiak hozzáadásához. Az oktatóanyag bemutatja, hogyan adhat az Azure AD Identitásszolgáltatóként, és a egy Facebook-identitásszolgáltató az alkalmazás. Az Identitásszolgáltatók ezek mindegyikét hozzáadása az alkalmazáshoz nem kötelező. Más identitásszolgáltatókkal is hozzáadhat például [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), vagy [Twitter](active-directory-b2c-setup-twitter-app.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Felhasználói folyamat létrehozása](tutorial-create-user-flows.md) regisztráljon, és jelentkezzen be az alkalmazást használók. 

## <a name="create-applications"></a>Alkalmazások létrehozása

Szolgáltató alkalmazások adja meg a azonosítóját és kulcsát az Azure AD B2C-bérlőben való kommunikáció engedélyezése. Az oktatóanyag ezen szakaszában hoz létre egy Azure AD-alkalmazást, és a egy Facebook-alkalmazást, amelyről azonosítókat és az identitás-szolgáltatóktól hozzáadása a bérlői kulcsok beolvasása. Ha csak egy identitásszolgáltatóval, csak az alkalmazás az adott szolgáltató létrehozása kell.

### <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

Bejelentkezés engedélyezése a felhasználók számára az Azure ad-ből, meg kell regisztrálni egy alkalmazást az Azure AD-bérlővel. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD-bérlő tartalmazó könyvtárra.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az a **alkalmazástípus**válassza `Web app / API`.
7. Az a **bejelentkezési URL-**, adja meg a következő URL-cím csupa kisbetűvel, ahol `your-B2C-tenant-name` váltja fel az Azure AD B2C-bérlő nevével.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Most kell használnia az összes URL-címek [b2clogin.com](b2clogin.md).

8. Kattintson a **Create** (Létrehozás) gombra. Másolás a **Alkalmazásazonosító** későbbi felhasználás céljából.
9. Válassza ki az alkalmazást, és válassza **beállítások**.
10. Válassza ki **kulcsok**, adja meg a kulcs leírása, és válassza ki az időtartamot, majd kattintson a **mentése**. Másolja a kulcs értékét, így az oktatóanyag későbbi részében használni.

### <a name="create-a-facebook-application"></a>Egy Facebook-alkalmazás létrehozása

Egy Facebook-fiókban identitásszolgáltatójaként az Azure AD B2C-vel használandó kell létre alkalmazást, a Facebook. Ha még nem rendelkezik egy Facebook-fiókban, beszerezheti a [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Jelentkezzen be a [Facebook-fejlesztőknek](https://developers.facebook.com/) Facebook-fiókja hitelesítő adataival.
2. Ha ezt még nem tette meg, hogy Facebook-fejlesztőként regisztrálnia kell. Regisztrálni, válassza ki a **regisztrálása** az oldal jobb felső sarkában, fogadja el a Facebook-szabályzatokat, és végezze el a regisztrációs lépéseket.
3. Válassza ki **saját alkalmazások** majd **adjon hozzá egy új alkalmazást**. 
4. Adjon meg egy **megjelenítendő név** és a egy érvényes **kapcsolattartó E-mail**.
5. Kattintson a **Alkalmazásazonosító létrehozása**. Előfordulhat, hogy fogadja el a Facebook-platform házirendek és a egy online biztonsági ellenőrzés szükséges.
6. Válassza ki **beállítások** > **alapszintű**.
7. Válasszon egy **kategória**, például `Business and Pages`. Ez az érték Facebook által igényelt, de nem használta az Azure AD B2C-t.
8. Válassza a lap alján **hozzáadása Platform**, majd válassza ki **webhely**.
9. A **webhely URL-címe**, adja meg `https://your-tenant-name.b2clogin.com/` cseréje `your-tenant-name` a bérlő nevével. Adjon meg egy URL-címet a **adatvédelmi szabályzat URL-címe**, például `http://www.contoso.com`. A szabályzat URL-címe egy oldalon kell karbantartani, adja meg az alkalmazás adatvédelmi nyilatkozatát.
10. Válassza ki **módosítások mentése**.
11. Az oldal tetején lévő másolás értékét **Alkalmazásazonosító**. 
12. Kattintson a **megjelenítése** , és másolja az értéket a **titkos Alkalmazáskulcs**. Használhatja mindkettő konfigurálása Facebook identitás-szolgáltatóként, a bérlőben. **Alkalmazás titkos kulcsát** egy fontos biztonsági hitelesítő adat.
13. Válassza ki **termékek**, majd válassza ki **beállítása** alatt **Facebook-bejelentkezés**.
14. Válassza ki **beállítások** alatt **Facebook-bejelentkezés**.
15. A **érvényes OAuth átirányítási URI-k**, adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Cserélje le `your-tenant-name` a bérlő nevével. Kattintson a **módosítások mentése** az oldal alján.
16. Ahhoz, hogy a Facebook-alkalmazás elérhető az Azure AD B2C-t, kattintson a **állapot** választó tetején, az oldal jobb, beállíthatja azt a **a**. Kattintson a **Megerősítés** gombra. Ezen a ponton kell módosítani az állapotát a **fejlesztési** való **élő**.

## <a name="add-the-identity-providers"></a>Az identitásszolgáltató hozzáadása

Miután létrehozta az alkalmazást a hozzáadni kívánt identitásszolgáltató, az identitásszolgáltató hozzá a bérlőn.

### <a name="add-the-azure-active-directory-identity-provider"></a>Az Azure Active Directory identitásszolgáltató hozzáadása

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD B2C-bérlő tartalmazó könyvtárra.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy **neve**. Adja meg például *Contoso Azure ad-ben*.
5. Válassza ki **identitásszolgáltató típusa**válassza **Open ID Connect (előzetes verzió)**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. A **metaadatok URL-címe**, adja meg a következő URL-címet lecseréli `your-AD-tenant-domain` a tartomány nevét az Azure AD-bérlő.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. A **ügyfél-azonosító**, adja meg a korábban feljegyzett Alkalmazásazonosító és a **titkos Ügyfélkód**, adja meg a korábban feljegyzett kulcs értékét.
9. Szükség esetén adjon meg egy értéket **Domain_hint**. Például: `ContosoAD`. 
10. Kattintson az **OK** gombra.
11. Válassza ki **ezen identitásszolgáltató jogcímeinek hozzárendelése** és állítsa be a következő jogcímek:
    
    - A **Felhasználóazonosító**, adja meg `oid`.
    - A **megjelenítendő név**, adja meg `name`.
    - A **Utónév**, adja meg `given_name`.
    - A **Vezetéknév**, adja meg `family_name`.
    - A **E-mail**, adja meg `unique_name`.

12. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a konfigurációt.

### <a name="add-the-facebook-identity-provider"></a>A Facebook-identitásszolgáltató hozzáadása

1. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
2. Adjon meg egy **neve**. Adja meg például *Facebook*.
3. Válassza ki **identitásszolgáltató típusa**válassza **Facebook**, és kattintson a **OK**.
4. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója, mint korábban rögzített a **ügyfél-azonosító**. Adja meg az alkalmazás titkos kulcsát, feljegyzett a **titkos Ügyfélkód**.
5. Kattintson a **OK** majd **létrehozás** a Facebook-konfiguráció mentéséhez.

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

Az oktatóanyagban, amelyek az Előfeltételek részeként létrehozott egy felhasználói folyamat esetében a regisztráció és bejelentkezés nevű *B2C_1_signupsignin1*. Ebben a szakaszban az identitás-szolgáltatóktól történő hozzáadása a *B2C_1_signupsignin1* felhasználói folyamatot.

1. Válassza ki **felhasználókövetési adatai (szabályzatok)**, majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. Válassza ki **Identitásszolgáltatók**, jelölje be a **Facebook** és **Contoso Azure ad-ben** identitásszolgáltatókat, amelyek adott hozzá.
3. Kattintson a **Mentés** gombra.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A létrehozott felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
2. A **alkalmazás**, válassza ki a webalkalmazás nevű *webapp1* , amely korábban regisztrálva. A **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **felhasználói folyamat futtatása**, és a egy identitásszolgáltatótól az előzőleg hozzáadott majd jelentkezzen be.
4. Az egyéb identitás-szolgáltatóktól hozzáadott ismételje meg az 1 – 3.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Az identity provider alkalmazások létrehozása
> * Az Identitásszolgáltatók hozzáadása a bérlőhöz
> * Az Identitásszolgáltatók hozzáadása a felhasználói folyamathoz

> [!div class="nextstepaction"]
> [Az alkalmazások az Azure Active Directory B2C a felhasználói felület testreszabása](tutorial-customize-ui.md)