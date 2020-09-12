---
title: Bejelentkezés beállítása Azure AD-szervezetekhez
titleSuffix: Azure AD B2C
description: Be kell állítania a bejelentkezést egy adott Azure Active Directory szervezet számára a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004459"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory szervezet számára Azure Active Directory B2C

Ha egy Azure Active Directory (Azure AD) [identitás-szolgáltatóként](authorization-code-flow.md) kíván használni a Azure ad B2Cban, létre kell hoznia egy alkalmazást, amely azt jelképezi. Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználóinak a Azure AD B2C felhasználói folyamatával.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider**lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *contoso Azure ad*-t.
1. A **metaadatok URL-címéhez**adja meg az alábbi URL-címet, `{tenant}` amely az Azure ad-bérlő tartománynevét helyettesíti:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Például: `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Az **ügyfél-azonosító**mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa**mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **hatókörben**adja meg a következőt: `openid profile` .
1. Hagyja meg a **Válasz típusa**és a **válasz mód**alapértelmezett értékeit.
1. Választható A **tartományhoz tartozó tipp**esetében adja meg a következőt: `contoso.com` . További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Az **Identity Provider jogcímek leképezése**területen válassza ki a következő jogcímeket:

    * **Felhasználói azonosító**: *OID*
    * **Megjelenítendő név**: *név*
    * **Utónév**: *given_name*
    * **Vezetéknév**: *family_name*
    * **E-mail**: *unique_name*

1. Válassza a **Mentés** lehetőséget.
