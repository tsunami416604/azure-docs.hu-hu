---
title: Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C |} A Microsoft Docs
description: Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 64cd440947c95de92ea156c14e4c524ecdc8e76c
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268816"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Állítsa be az Azure Active Directory-fiókok bejelentkezési beépített szabályzatot az Azure Active Directory B2C-vel

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban. Az éles környezetben ne használja a szolgáltatást.

Ez a cikk bemutatja, hogyan való bejelentkezés engedélyezése a felhasználók számára egy adott Azure Active Directory (Azure AD) szervezet beépített házirend segítségével az Azure Active Directory (Azure AD) B2C-ben.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel.

>[!NOTE]
>`Contoso.com` a szervezeti használt Azure AD-bérlő és `fabrikamb2c.onmicrosoft.com` használja a következő utasítások az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, hogy a könyvtár, amely tartalmazza az Azure AD B2C-bérlő (fabrikamb2c.onmicrosoft.com) kattintson a címtár és előfizetés szűrőt a felső menüben majd a könyvtárat, amely tartalmazza az Azure AD B2C-bérlő használata esetén.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az a **alkalmazástípus**válassza `Web app / API`.
7. A a **bejelentkezési URL-**, adja meg a következő URL-cím csupa kisbetűvel, ahol `your-tenant` váltja fel az Azure AD B2C-bérlő (fabrikamb2c.onmicrosoft.com) nevére:

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Kattintson a **Create** (Létrehozás) gombra. Másolás a **Alkalmazásazonosító** későbbi felhasználás céljából.
9. Válassza ki az alkalmazást, és válassza **beállítások**.
10. Válassza ki **kulcsok**, adja meg a kulcs leírása, és válassza ki az időtartamot, majd kattintson a **mentése**. Másolja későbbi felhasználás jelenik meg a kulcs értékét.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurálja az Azure AD Identitásszolgáltatóként a bérlőben

1. Győződjön meg arról, hogy használja a könyvtárat, amely tartalmazza a szervezeti Azure AD-bérlő (contoso.com) kattintva a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy **neve**. Adja meg például a "Contoso Azure AD".
5. Válassza ki **identitásszolgáltató típusa**válassza **Open ID Connect**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. A **metaadatok URL-címe**, adja meg a következő URL-címet lecseréli `your-tenant` az Azure AD-bérlő nevével. Ha például a contoso.com:

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```

8. Szükség esetén adjon meg egy értéket **tartomány** (pl. `ContosoAD`). Ez az érték az identitásszolgáltató használatával való megnevezésekor használandó *domain_hint* a kérésben. 
9. Kattintson az **OK** gombra.
10. Válassza ki **ezen identitásszolgáltató jogcímeinek hozzárendelése** és állítsa be a következő jogcímek:
    
    - A **Felhasználóazonosító**, adja meg `oid`.
    - A **megjelenítendő név**, adja meg `name`.
    - A **Utónév**, adja meg `given_name`.
    - A **Vezetéknév**, adja meg `family_name`.
    - A **E-mail**, adja meg `unique_name`.

11. Kattintson a **OK**, majd **létrehozás** kattintva mentse a konfigurációt.
