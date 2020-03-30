---
title: Közvetlen bejelentkezés beállítása az Azure Active Directory B2C használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet előre kitölteni a bejelentkezési nevet, vagy közvetlenül egy közösségi identitásszolgáltatóhoz átirányítani.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188766"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Közvetlen bejelentkezés beállítása az Azure Active Directory B2C használatával

Amikor az Azure Active Directory (AD) B2C használatával állítja be az alkalmazásba való bejelentkezést, előre feltöltheti a bejelentkezési nevet vagy közvetlen bejelentkezést egy adott közösségi identitásszolgáltatónak, például a Facebooknak, a LinkedInnek vagy egy Microsoft-fióknak.

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előzetes feltöltése

A bejelentkezési felhasználói út során a függő entitás alkalmazás megcélozhat egy adott felhasználó- vagy tartománynevet. Felhasználó célzásakor az alkalmazás az engedélyezési kérelemben `login_hint` megadhatja a felhasználói bejelentkezési névvel rendelkező lekérdezési paramétert. Az Azure AD B2C automatikusan feltölti a bejelentkezési nevet, míg a felhasználónak csak a jelszót kell megadnia.

![Bejelentkezési lap regisztrálása az URL-ben kiemelt login_hint lekérdezés param param-mal](./media/direct-signin/login-hint.png)

A felhasználó módosíthatja a bejelentkezési szövegmező értékét.

Ha egyéni házirendet használ, felülbírálja a `SelfAsserted-LocalAccountSignin-Email` technikai profilt. A `<InputClaims>` szakaszban állítsa a signInName jogcím `{OIDC:LoginHint}`DefaultValue értékét a értékre. A `{OIDC:LoginHint}` változó a `login_hint` paraméter értékét tartalmazza. Az Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegdobozt.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Bejelentkezés átirányítása közösségi szolgáltatóhoz

Ha úgy állította be a bejelentkezési utat, hogy az alkalmazás közösségi fiókokat , például a Facebookot, a LinkedInt vagy a Google-t is tartalmazza, megadhatja a `domain_hint` paramétert. Ez a lekérdezési paraméter egy tippet nyújt az Azure AD B2C-hez a bejelentkezéshez használandó közösségi identitásszolgáltatóval kapcsolatban. Ha például az alkalmazás `domain_hint=facebook.com`azt adja meg, a bejelentkezés közvetlenül a Facebook bejelentkezési oldalára kerül.

![Bejelentkezési lap regisztrálása az URL-ben kiemelt domain_hint lekérdezés param param-mal](./media/direct-signin/domain-hint.png)

Ha egyéni házirendet használ, a tartománynevet bármely `<Domain>domain name</Domain>` `<ClaimsProvider>`XML-elem használatával konfigurálhatja.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


