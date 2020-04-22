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
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678036"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory-szervezethez az Azure Active Directory B2C-ben

Az Azure Active Directory (Azure AD) [identitásszolgáltatóként](authorization-code-flow.md) való használatához létre kell hoznia egy alkalmazást, amely képviseli azt. Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználói számára az Azure AD B2C felhasználói folyamat használatával.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitásszolgáltatóként

1. Győződjön meg arról, hogy az Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Új OpenID Connect szolgáltató**lehetőséget.
1. Írjon be egy **nevet**. Írja be például a *Contoso Azure AD értéket.*
1. A **metaadatok url-címéhez** `{tenant}` adja meg a következő URL-címet, amely az Azure AD-bérlő tartománynevére lép:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. **Ügyfélazonosító**esetén adja meg a korábban rögzített alkalmazásazonosítót.
1. Az **Ügyféltitkos kulcsot**adja meg a korábban rögzített ügyféltitok.
1. A **Hatókör mezőbe** `openid profile`írja be a.
1. Hagyja meg a **Választípus**és a Válasz mód alapértelmezett **értékeit.**
1. (Nem kötelező) A **Tartomány tippmezőbe**írja be a be a `contoso.com`mezőbe. További információ: [Közvetlen bejelentkezés beállítása az Azure Active Directory B2C használatával](direct-signin.md#redirect-sign-in-to-a-social-provider)című témakörben talál.
1. Az **Identitásszolgáltató jogcím-hozzárendelése**csoportban válassza ki a következő jogcímeket:

    * **Felhasználói azonosító**: *oid*
    * **Megjelenítendő név**: *név*
    * **Utónév**: *given_name*
    * **Vezetéknév**: *family_name*
    * **E-mail :** *unique_name*

1. Kattintson a **Mentés** gombra.
