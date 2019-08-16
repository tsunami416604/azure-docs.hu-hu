---
title: Bejelentkezés beállítása Azure Active Directory szervezet számára – Azure Active Directory B2C
description: Be kell állítania a bejelentkezést egy adott Azure Active Directory szervezet számára a Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 46755cb8d0383d166b10d50854eb476b676a6ee4
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509742"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory szervezet számára Azure Active Directory B2C

Ha egy Azure Active Directory (Azure AD) [identitás](active-directory-b2c-reference-oauth-code.md) -szolgáltatóként kíván használni a Azure ad B2Cban, létre kell hoznia egy alkalmazást, amely azt jelképezi. Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználóinak a Azure AD B2C felhasználói folyamatával.

## <a name="create-an-azure-ad-app"></a>Azure AD-alkalmazás létrehozása

Ha engedélyezni szeretné a bejelentkezést egy adott Azure AD-szervezet felhasználói számára, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül, amely nem azonos a Azure AD B2C-Bérlővel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár és előfizetés szűrőt** a felső menüben, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat. Ez nem ugyanaz a bérlő, mint a Azure AD B2C bérlője.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **új regisztráció**lehetőséget.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az **ebben a szervezeti könyvtárban lévő fiókok** kijelölésének elfogadása csak ehhez az alkalmazáshoz.
7. Az **átirányítási URI**esetében fogadja el a **web**értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol `your-B2C-tenant-name` a helyére a Azure ad B2C bérlő neve kerül. Például `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Az összes URL-nek most a [B2clogin.com](b2clogin.md)-t kell használnia.

8. Kattintson a **regisztrálás**gombra. Másolja a későbbiekben használni kívánt **alkalmazás-(ügyfél-) azonosítót** .
9. Válassza ki a **tanúsítványok & Secrets** elemet az alkalmazás menüben, majd válassza az **új ügyfél titka**lehetőséget.
10. Adja meg az ügyfél titkos kulcsának nevét. Például: `Azure AD B2C App Secret`.
11. Válassza ki a lejárati időszakot. Ehhez az alkalmazáshoz fogadja el az **1 év**kiválasztását.
12. Válassza a **Hozzáadás** lehetőséget, és másolja ki az új titkos kulcs értékét, amelyet később szeretne használni.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár és előfizetés szűrőt** a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
2. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
4. Adjon meg egy **nevet**. Adja meg például a következőt: `Contoso Azure AD`.
5. Válassza az **identitás-szolgáltató típusa**lehetőséget, válassza az **OpenID Connect**lehetőséget, majd kattintson **az OK**gombra.
6. Válassza ki **az identitás-szolgáltató beállítása** lehetőséget.
7. A **metaadatok URL-címéhez**adja meg az `your-AD-tenant-domain` alábbi URL-címet, amely az Azure ad-bérlő tartománynevét helyettesíti. Például `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Az **ügyfél-azonosító**mezőben adja meg a korábban rögzített alkalmazás azonosítóját, és az **ügyfél titkos**kódjánál adja meg a korábban rögzített ügyfél-titkot.
9. Igény szerint megadhat egy értéket a **Domain_hint**számára. Például: `ContosoAD`. Ez az az érték, amelyet akkor kell használni, ha a kérelemben szereplő *domain_hint* használatával hivatkozik erre az identitás-szolgáltatóra.
10. Kattintson az **OK** gombra.
11. Válassza ki az **identitás-szolgáltató** jogcímeit, és állítsa be a következő jogcímeket:

    - A **felhasználói azonosító**mezőben adja `oid`meg a következőt:.
    - A **megjelenítendő név**mezőbe írja `name`be a következőt:.
    - A **megadott név**mezőben adja `given_name`meg a következőt:.
    - A **vezetéknév**mezőbe írja `family_name`be a következőt:.
    - Az **e-mail**mezőbe írja `unique_name`be a következőt:.

12. Kattintson **az OK**gombra, majd a konfiguráció mentéséhez kattintson a **Létrehozás** gombra.
