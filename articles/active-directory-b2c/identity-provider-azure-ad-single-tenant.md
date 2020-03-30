---
title: Bejelentkezés beállítása egy Azure AD-szervezethez
titleSuffix: Azure AD B2C
description: Állítsa be a bejelentkezést egy adott Azure Active Directory-szervezethez az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188307"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory-szervezethez az Azure Active Directory B2C-ben

Az Azure Active Directory (Azure AD) [identitásszolgáltatóként](authorization-code-flow.md) való használatához létre kell hoznia egy alkalmazást, amely képviseli azt. Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználói számára az Azure AD B2C felhasználói folyamat használatával.

## <a name="create-an-azure-ad-app"></a>Azure AD-alkalmazás létrehozása

Egy adott Azure AD-szervezet felhasználói nak engedélyezéséhez regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül, amely nem ugyanaz, mint az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD-bérlőt tartalmazó könyvtárat. Ez nem ugyanaz a bérlő, mint az Azure AD B2C-bérlő.
3. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
4. Válassza **az Új regisztráció lehetőséget.**
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Csak ehhez az alkalmazáshoz fogadja el a fiókok kiválasztását ebben a **szervezeti címtárban.**
7. Az **átirányításURI,** fogadja el a **web**értékét, és adja meg `your-B2C-tenant-name` a következő URL-t az összes kisbetű, ahol lecseréli az Azure AD B2C bérlő nevét. Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Mostantól minden URL-nek [b2clogin.com](b2clogin.md)kell használnia.

8. Kattintson a **Regisztráció gombra.** Másolja a később használandó **alkalmazásazonosítót.**
9. Válassza az alkalmazás **menüjének Tanúsítványok & titkos kulcsok** parancsát, majd az Új **ügyféltitok**lehetőséget.
10. Adja meg az ügyféltitok nevét. Például: `Azure AD B2C App Secret`.
11. Válassza ki a lejárati időszakot. Ehhez az alkalmazáshoz, fogadja el a kiválasztás **1 év**.
12. Válassza **a Hozzáadás** és másolás az új ügyféltitok értékét, amely később megjelenik.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitásszolgáltatóként

1. Győződjön meg arról, hogy az Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Új OpenID Connect szolgáltató**lehetőséget.
1. Írjon be egy **nevet**. Írja be például a *Contoso Azure AD értéket.*
1. A **metaadatok url-címéhez** `your-AD-tenant-domain` adja meg a következő URL-címet, amely az Azure AD-bérlő tartománynevére lép:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Ne** használja például az Azure AD v2.0 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`metaadat-végpontját. Ha így tesz, a `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` bejelentkezéshez hasonló hiba lép fel.

1. **Ügyfélazonosító**esetén adja meg a korábban rögzített alkalmazásazonosítót.
1. Az **Ügyféltitkos kulcsot**adja meg a korábban rögzített ügyféltitok.
1. Hagyja meg a **Hatókör**, **Választípus**és Válasz mód alapértelmezett **értékeit.**
1. (Nem kötelező) Adjon meg egy értéket **a Domain_hint.** Például *a ContosoAD*. Ez az az érték, amelyet akkor kell használni, amikor erre az identitásszolgáltatóra hivatkozik *a domain_hint* használatával a kérelemben.
1. Az **Identitásszolgáltató jogcímekkel kapcsolatos hozzárendelése**csoportban adja meg a következő jogcímleképezési értékeket:

    * **Felhasználói azonosító**: *oid*
    * **Megjelenítendő név**: *név*
    * **Utónév**: *given_name*
    * **Vezetéknév**: *family_name*
    * **E-mail :** *unique_name*

1. Kattintson a **Mentés** gombra.
