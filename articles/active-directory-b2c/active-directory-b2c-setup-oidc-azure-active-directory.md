---
title: Jelentkezzen be egy Azure Active Directory-szervezet – Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Állítsa be jelentkezzen be az Azure Active Directory B2C egy adott Azure Active Directory szervezet számára.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508419"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Jelentkezzen be egy adott Azure Active Directory-szervezet az Azure Active Directory B2C beállítása

>[!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el. Az éles környezetben ne használja a szolgáltatást.

Az Azure Active Directory (Azure AD) használatára egy [identitásszolgáltató](active-directory-b2c-reference-oauth-code.md) az Azure AD B2C-kell hoznia egy alkalmazás, amely azt jelöli. Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára egy adott Azure AD-ből az Azure AD B2C flow cégen felhasználó.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel, amely nem ugyanaz, mint az Azure AD B2C-bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, hogy a könyvtár, amely tartalmazza az Azure AD-bérlő használata esetén. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben, és válassza ki a könyvtárat, amely tartalmazza az Azure AD-bérlővel. Ez a nem ugyanahhoz a bérlőhöz, mint az Azure AD B2C-bérlőben.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
4. Válassza ki **új regisztrációs**.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Fogadja el a kijelölt **fiókok csak a szervezeti könyvtárban található** ehhez az alkalmazáshoz.
7. A a **átirányítási URI-t**, fogadja el a **webes**, és adja meg a következő URL-cím csupa kisbetűvel, ahol `your-B2C-tenant-name` váltja fel az Azure AD B2C-bérlő nevét. Ha például `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Most kell használnia az összes URL-címek [b2clogin.com](b2clogin.md).

8. Kattintson a **regisztrálása**. Másolás a **Alkalmazásazonosítót (ügyfél)** későbbi felhasználás céljából.
9. Válassza ki **tanúsítványok és titkos kulcsok** az alkalmazás menüből, és válassza ki a **új titkos ügyfélkulcsot**.
10. Adjon meg egy nevet az ügyfél titkos kulcsát. Például: `Azure AD B2C App Secret`.
11. Válassza ki a lejárati idejét. A jelen alkalmazás esetében fogadja el a kijelölt **az 1 év**.
12. Válassza ki **Hozzáadás** , és másolja az új titkos ügyfélkulcsot későbbi felhasználás céljából megjelenített értékét.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurálja az Azure AD identitás-szolgáltatóként

1. Győződjön meg arról, hogy a könyvtár, amely tartalmazza az Azure AD B2C-bérlőt használja. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben válassza az Azure AD B2C-bérlő tartalmazó könyvtárba.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy **neve**. Adja meg például a következőt: `Contoso Azure AD`.
5. Válassza ki **identitásszolgáltató típusa**válassza **Open ID Connect (előzetes verzió)** , és kattintson a **OK**.
6. Válassza ki **az identitásszolgáltató beállítása**
7. A **metaadatok URL-címe**, adja meg a következő URL-címet lecseréli `your-AD-tenant-domain` a tartomány nevét az Azure AD-bérlő. Például `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. A **ügyfél-azonosító**, adja meg a korábban feljegyzett Alkalmazásazonosító és a **titkos Ügyfélkód**, adja meg a korábban feljegyzett titkos ügyfélkulcsot.
9. Szükség esetén adjon meg egy értéket **Domain_hint**. Például: `ContosoAD`. Ez az érték az identitásszolgáltató használatával való megnevezésekor használandó *domain_hint* a kérésben. 
10. Kattintson az **OK** gombra.
11. Válassza ki **ezen identitásszolgáltató jogcímeinek hozzárendelése** és állítsa be a következő jogcímek:
    
    - A **Felhasználóazonosító**, adja meg `oid`.
    - A **megjelenítendő név**, adja meg `name`.
    - A **Utónév**, adja meg `given_name`.
    - A **Vezetéknév**, adja meg `family_name`.
    - A **E-mail**, adja meg `unique_name`.

12. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a konfigurációt.
