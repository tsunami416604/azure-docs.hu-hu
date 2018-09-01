---
title: Adjon hozzá egy Azure AD-szolgáltató segítségével beépített szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá az Open ID Connect identitásszolgáltató (az Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e09ad89f3225af9de40781fafc022c8326f80619
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338638"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Az Azure Active Directory B2C: Jelentkezzen be a beépített szabályzat használatával az Azure AD-fiókok

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban. Az éles környezetben ne használja a szolgáltatást.

Ez a cikk bemutatja, hogyan ahhoz, hogy jelentkezzen be egy adott szervezet beépített szabályzatokat az Azure Active Directory (Azure AD) a felhasználók számára.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel.

>[!NOTE]
> A szervezeti használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" a következő utasítások az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a felső menüsávon válassza a fiókját. Az a **Directory** menüben válassza ki a szervezeti Azure AD-bérlővel, amelyen regisztrálni fogja az alkalmazás (contoso.com).
1. Válassza ki **minden szolgáltatás** a bal oldali panelen, és keresse meg az "Alkalmazásregisztrációk".
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adja meg az alkalmazás nevét (például `Azure AD B2C App`).
1. Válassza a **Webalkalmazás/API** lehetőséget az alkalmazás típusaként.
1. A **bejelentkezési URL-**, adja meg a következő URL-címet, ahol `yourtenant` váltotta fel az Azure AD B2C-bérlő neve (`fabrikamb2c`):

    >[!NOTE]
    >"Yourtenant" értéke csak kisbetűket a a **bejelentkezési URL-**.

    ```Console
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Mentse az Alkalmazásazonosítót, melyiket fogja használni a következő szakaszban, az ügyfél-azonosítót.
1. Alatt a **beállítások** panelen válassza ki **kulcsok**.
1. Adjon meg egy **kulcs leírása** alatt a **jelszavak** szakaszt, és állítsa be a **időtartama** a "Soha nem jár le". 
1. Kattintson a **mentése**, és jegyezze fel az eredményül kapott kulcs **érték**, melyiket fogja használni a következő szakaszban, a titkos ügyfélkulcsot.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurálja az Azure AD Identitásszolgáltatóként a bérlőben

1. Az a felső menüsávon válassza a fiókját. Az a **Directory** menüben válassza ki az Azure AD B2C-bérlő (fabrikamb2c.onmicrosoft.com).
1. [Keresse meg az Azure AD B2C-beállítások menüben](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
1. Az Azure AD B2C beállítások menüben kattintson a **Identitásszolgáltatók**.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "Contoso Azure AD".
1. Kattintson a **identitásszolgáltató típusa**válassza **Open ID Connect**, és kattintson a **OK**.
1. Kattintson a **az identitásszolgáltató beállítása**
1. A **metaadatok URL-címe**, adja meg a következő URL-címet, ahol `yourtenant` váltotta fel az Azure AD-bérlő nevét (pl. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Az a **ügyfél-azonosító** és **titkos Ügyfélkód**, adja meg az alkalmazás Azonosítóját és kulcsát az előző szakaszban.
1. Tartsa meg az alapértelmezett érték a **hatókör**, amely értékre kell állítani `openid`.
1. Tartsa meg az alapértelmezett érték a **választípus**, amely értékre kell állítani `code`.
1. Tartsa meg az alapértelmezett érték a **Válaszmód**, amely értékre kell állítani `form_post`.
1. Szükség esetén adjon meg egy értéket **tartomány** (pl. `ContosoAD`). Ez az érték az identitásszolgáltató használatával való megnevezésekor használandó *domain_hint* a kérésben. 
1. Kattintson az **OK** gombra.
1. Kattintson a **ezen identitásszolgáltató jogcímeinek hozzárendelése**.
1. A **Felhasználóazonosító**, adja meg `oid`.
1. A **megjelenítendő név**, adja meg `name`.
1. A **Utónév**, adja meg `given_name`.
1. A **Vezetéknév**, adja meg `family_name`.
1. A **E-mail**, adja meg `unique_name`
1. Kattintson a **OK**, majd **létrehozás** kattintva mentse a konfigurációt.

## <a name="next-steps"></a>További lépések

Adja hozzá az újonnan létrehozott Azure ad-ben identitásszolgáltató beépített szabályzatot, és visszajelzést küldhet [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
