---
title: 'Az Azure Active Directory B2C: Egy beépített házirendek segítségével az Azure AD-szolgáltató felvétele |} Microsoft Docs'
description: Útmutató az Open ID Connect identitásszolgáltató (az Azure AD) hozzáadása
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2018
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Az Azure Active Directory B2C: Jelentkezzen be Azure AD-fiókok egy beépített házirendeken keresztül

>[!NOTE]
> A funkció jelenleg nyilvános előzetes verziójához. Üzemi környezetben ne használja a szolgáltatást.

Ez a cikk bemutatja, hogyan bejelentkezhet a felhasználók egy adott szervezet beépített házirendek Azure Active Directory (Azure AD) származó engedélyezéséhez.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Bejelentkezés a felhasználók egy meghatározott engedélyezése az Azure AD a szervezeten belül, regisztrálnia kell az alkalmazás belül a szervezeti Azure AD-bérlő.

>[!NOTE]
> A szervezeti a használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" az Azure AD B2C bérlő az alábbi utasításokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját. Az a **Directory** menüben válassza ki a szervezeti Azure AD-bérlő, amelyben regisztrálni kívánja az alkalmazás (contoso.com).
1. Válassza ki **minden szolgáltatás** a bal oldali ablaktáblán, és keresse meg az "App regisztráció."
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adjon meg egy nevet az alkalmazáshoz (például `Azure AD B2C App`).
1. Válassza a **Webalkalmazás/API** lehetőséget az alkalmazás típusaként.
1. A **bejelentkezési URL-cím**, adja meg a következő URL-cím, ahol `yourtenant` cseréli le az Azure AD B2C bérlő neve (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >A "yourtenant" értékének kell lennie a kisbetűket a **bejelentkezési URL-cím**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Mentse az Alkalmazásazonosító, amelyet adattárként fog használni a következő szakaszban az ügyfél-azonosítót.
1. Az a **beállítások** panelen válassza **kulcsok**.
1. Adjon meg egy **kulcs leírását** alatt a **jelszavak** szakaszt, és állítsa be a **időtartam** a "Soha nem jár le". 
1. Kattintson a **mentése**, és jegyezze fel az eredményül kapott kulcs **érték**, amelyet adattárként fog használni a következő szakaszban a titkos ügyfélkulcsot.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Az Azure AD-bérlőben identitás-szolgáltatóként konfigurálása

1. A felső sávon válassza ki a fiókját. Az a **Directory** menüben válassza ki az Azure AD B2C-bérlő (fabrikamb2c.onmicrosoft.com).
1. [Keresse meg az Azure AD B2C-beállítások menüjében](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
1. Az Azure AD B2C beállítások menüben kattintson a **identitás-szolgáltatóktól**.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "Contoso Azure AD".
1. Kattintson a **identitás szolgáltatótípus**, jelölje be **Open ID Connect**, és kattintson a **OK**.
1. Kattintson a **az identitásszolgáltató beállítása**
1. A **metaadatainak URL-címét**, adja meg a következő URL-cím, ahol `yourtenant` cseréli le az Azure AD-bérlő nevét (pl. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Az a **ügyfél-azonosító** és **ügyfélkulcs**, adja meg az alkalmazás Azonosítóját és kulcsát az előző szakaszban.
1. Tartsa meg az alapértelmezett értéket **hatókör**, amely meg `openid`.
1. Tartsa meg az alapértelmezett értéket **választípus**, amely meg `code`.
1. Tartsa meg az alapértelmezett értéket **válasz mód**, amely meg `form_post`.
1. Ha szükséges, adjon meg egy értéket, a **tartomány** (pl. `ContosoAD`). Ez az érték az identitásszolgáltató használatával történő hivatkozás során használandó *domain_hint* a kérelemben. 
1. Kattintson az **OK** gombra.
1. Kattintson a **leképezése az identitásszolgáltató jogcímek**.
1. A **Felhasználóazonosító**, adja meg `oid`.
1. A **megjelenített név**, adja meg `name`.
1. A **Utónév**, adja meg `given_name`.
1. A **vezetékneve**, adja meg `family_name`.
1. A **E-mail**, adja meg `unique_name`
1. Kattintson a **OK**, majd **létrehozása** kattintva mentse a konfigurációt.

## <a name="next-steps"></a>További lépések

Adja hozzá az újonnan létrehozott identitásszolgáltató az Azure AD egy beépített házirend, hogy visszajelzéseikkel [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
