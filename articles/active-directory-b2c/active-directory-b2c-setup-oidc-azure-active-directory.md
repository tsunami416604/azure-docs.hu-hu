---
title: Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C |} A Microsoft Docs
description: Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: beb2d618d93f4c599f946194bd483326471065f4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944803"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C-vel

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban. Az éles környezetben ne használja a szolgáltatást.

Ez a cikk bemutatja, hogyan való bejelentkezés engedélyezése a felhasználók számára egy adott Azure Active Directory (Azure AD) szervezet beépített házirend segítségével az Azure Active Directory (Azure AD) B2C-ben.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel, amely nem ugyanaz, mint az Ön Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, hogy a könyvtár, amely tartalmazza az Azure AD-bérlő kattintson a címtár és előfizetés szűrőt a felső menüben majd a könyvtárat, amely tartalmazza az Azure AD-bérlő használata esetén.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az a **alkalmazástípus**válassza `Web app / API`.
7. Az a **bejelentkezési URL-**, adja meg a következő URL-cím csupa kisbetűvel, ahol `your-B2C-tenant-name` váltja fel az Azure AD B2C-bérlő nevével. Ha például `https://fabrikam.b2clogin.com/fabrikam.b2clogin.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.b2clogin.com/oauth2/authresp
    ```

    Most kell használnia az összes URL-címek [b2clogin.com](b2clogin.md).

8. Kattintson a **Create** (Létrehozás) gombra. Másolás a **Alkalmazásazonosító** későbbi felhasználás céljából.
9. Válassza ki az alkalmazást, és válassza **beállítások**.
10. Válassza ki **kulcsok**, adja meg a kulcs leírása, és válassza ki az időtartamot, majd kattintson a **mentése**. Másolja későbbi felhasználás jelenik meg a kulcs értékét.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurálja az Azure AD identitás-szolgáltatóként

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtár a **címtár és előfizetés-szűrő** a felső menüben, és kiválasztása az Azure AD B2C-bérlő tartalmazó könyvtárra.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy **neve**. Adja meg például a "Contoso Azure AD".
5. Válassza ki **identitásszolgáltató típusa**válassza **Open ID Connect (előzetes verzió)**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. A **metaadatok URL-címe**, adja meg a következő URL-címet lecseréli `your-AD-tenant-domain` a tartomány nevét az Azure AD-bérlő. Például `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. A **ügyfél-azonosító**, adja meg a korábban feljegyzett Alkalmazásazonosító és a **titkos Ügyfélkód**, adja meg a korábban feljegyzett kulcs értékét.
9. Szükség esetén adjon meg egy értéket **Domain_hint**. Például: `ContosoAD`. Ez az érték az identitásszolgáltató használatával való megnevezésekor használandó *domain_hint* a kérésben. 
10. Kattintson az **OK** gombra.
11. Válassza ki **ezen identitásszolgáltató jogcímeinek hozzárendelése** és állítsa be a következő jogcímek:
    
    - A **Felhasználóazonosító**, adja meg `oid`.
    - A **megjelenítendő név**, adja meg `name`.
    - A **Utónév**, adja meg `given_name`.
    - A **Vezetéknév**, adja meg `family_name`.
    - A **E-mail**, adja meg `unique_name`.

12. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a konfigurációt.
