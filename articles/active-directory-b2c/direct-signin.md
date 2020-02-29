---
title: Közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával | Microsoft Docs
description: Megtudhatja, hogyan lehet előre kitölteni a bejelentkezési nevet, vagy egyenesen átirányítani egy közösségi identitás-szolgáltatóra.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188766"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával

Ha Azure Active Directory (AD) B2C használatával állítja be az alkalmazásba való bejelentkezést, előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók.

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előre feltöltése

A bejelentkezési felhasználói úton a függő entitás alkalmazás egy adott felhasználót vagy tartománynevet is megcélozhat. Egy felhasználó megcélzásakor az alkalmazás megadhatja az engedélyezési kérelemben az `login_hint` lekérdezési paramétert a felhasználó bejelentkezési nevével. Azure AD B2C automatikusan feltölti a bejelentkezési nevet, míg a felhasználónak csak meg kell adnia a jelszót.

![Bejelentkezési oldal regisztrálása az URL-cím login_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/login-hint.png)

A felhasználó módosíthatja a bejelentkezési szövegmezőben lévő értéket.

Ha egyéni szabályzatot használ, bírálja felül a `SelfAsserted-LocalAccountSignin-Email` technikai profilt. A `<InputClaims>` szakaszban állítsa be a signInName jogcím DefaultValue értékét `{OIDC:LoginHint}`re. A `{OIDC:LoginHint}` változó a `login_hint` paraméter értékét tartalmazza. Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőt.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Bejelentkezés átirányítása egy közösségi szolgáltatóba

Ha úgy konfigurálta a bejelentkezési utat, hogy az alkalmazás belefoglalja a közösségi fiókokat, például a Facebookot, a LinkedInt vagy a Google-t, megadhatja a `domain_hint` paramétert. Ez a lekérdezési paraméter egy olyan célzást tartalmaz, amely a bejelentkezéshez használandó közösségi identitás-szolgáltatóról Azure AD B2C. Ha például az alkalmazás megadja `domain_hint=facebook.com`, a bejelentkezés közvetlenül a Facebook bejelentkezési oldalára kerül.

![Bejelentkezési oldal regisztrálása az URL-cím domain_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/domain-hint.png)

Ha egyéni szabályzatot használ, a tartománynevet bármely `<ClaimsProvider>``<Domain>domain name</Domain>` XML-elemével is konfigurálhatja.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


