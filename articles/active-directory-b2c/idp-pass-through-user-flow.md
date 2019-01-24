---
title: Felhasználói folyamat segítségével egy hozzáférési jogkivonatot át az alkalmazását az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adhat át egy hozzáférési jogkivonatot keresztül OAuth2.0 identitásszolgáltatókhoz való jogcímként az Azure Active Directory B2C felhasználói folyamatokban.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 279e60d76d56cd139b4de991bc0434cbf129adee
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844670"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Felhasználói folyamat segítségével egy hozzáférési jogkivonatot át alkalmazását az Azure Active Directory B2C-vel

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

> [!Important]
> A nyilvános előzetes verziójú szolgáltatás átmenetileg nem érhető el.

A [felhasználói folyamat](active-directory-b2c-reference-policies.md) az Azure Active Directory (Azure AD) B2C lehetőséget kínál a felhasználók az alkalmazás regisztráljon vagy jelentkezzen be egy identitásszolgáltatóval. Amikor elindul az utazás, az Azure AD B2C-t kap egy [hozzáférési jogkivonat](active-directory-b2c-reference-tokens.md) az identitásszolgáltatótól. Az Azure AD B2C használja ezt a jogkivonatot a felhasználóval kapcsolatos információk lekéréséhez. A jogkivonat segítségével átadása az alkalmazásokat, amelyek az Azure AD B2C-ben regisztrálja a felhasználói folyamat engedélyezi egy jogcímet.

Az Azure AD B2C jelenleg csak az támogatja a hozzáférési jogkivonatot az átadott [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) identitás-szolgáltatóktól, többek között [Facebook](active-directory-b2c-setup-fb-app.md) és [Google](active-directory-b2c-setup-goog-app.md). Minden egyéb identitás-szolgáltatóktól a jogcím vissza üres.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás kell használnia egy [v2 felhasználói folyamat](user-flow-versions.md).
- A felhasználói folyamat az OAuth 2.0 identitásszolgáltató van konfigurálva.

## <a name="enable-the-claim"></a>Az igényt engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **felhasználókövetési adatai**, majd jelölje ki a felhasználói folyamatot. Ha például **B2C_1_SignupSignIn**.
5. Válassza az **Alkalmazásjogcímek** lehetőséget.
6. Engedélyezése **Identity Provider hozzáférési jogkivonat**.

    ![Alkalmazás jogcíme](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kattintson a **mentése** menteni a felhasználói folyamatot.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

Ha teszteli az alkalmazások Azure AD B2C-ben, hasznos lehet az Azure AD B2C jogkivonat vissza lehet `https://jwt.ms` , tekintse át a jogcímek, az azt.

1. A felhasználói folyamat áttekintése lapon válassza az **felhasználói folyamat futtatása**.
2. A **alkalmazás**, válassza ki az alkalmazását, amely korábban regisztrálva. Megtekintheti a tokent az alábbi példában a **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **felhasználói folyamat futtatása**, majd jelentkezzen be a fiók hitelesítő adatait. Az identitásszolgáltató a hozzáférési jogkivonatot kell megjelennie a **idp_access_token** jogcím.

    Az alábbi példához hasonló kell megjelennie:

    ![A dekódolt jogkivonat](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>További lépések

További tudnivalók a tokeneket a [Azure Active Directory-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).




