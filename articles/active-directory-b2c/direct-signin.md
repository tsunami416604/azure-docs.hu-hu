---
title: Közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával | Microsoft Docs
description: Megtudhatja, hogyan lehet előre kitölteni a bejelentkezési nevet, vagy egyenesen átirányítani egy közösségi identitás-szolgáltatóra.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388595"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával

Ha Azure Active Directory (AD) B2C használatával állítja be az alkalmazásba való bejelentkezést, előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók.

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előre feltöltése

A bejelentkezési felhasználói úton a függő entitás alkalmazás egy adott felhasználót vagy tartománynevet is megcélozhat. Egy felhasználó megcélzásakor az alkalmazás megadhatja az engedélyezési kérelemben a `login_hint` lekérdezési paramétert a felhasználó bejelentkezési nevével. Azure AD B2C automatikusan feltölti a bejelentkezési nevet, míg a felhasználónak csak meg kell adnia a jelszót.

![Bejelentkezési oldal regisztrálása az URL-cím login_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/login-hint.png)

A felhasználó módosíthatja a bejelentkezési szövegmezőben lévő értéket.

Ha egyéni szabályzatot használ, bírálja felül a `SelfAsserted-LocalAccountSignin-Email` technikai profilt. A `<InputClaims>` szakaszban állítsa be a signInName jogcím DefaultValue értékét a következőre: `{OIDC:LoginHint}` . A `{OIDC:LoginHint}` változó tartalmazza a `login_hint` paraméter értékét. Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőt.

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

Ha úgy konfigurálta a bejelentkezési utat, hogy az alkalmazás belefoglalja a közösségi fiókokat, például a Facebookot, a LinkedInt vagy a Google-t, megadhatja a `domain_hint` paramétert. Ez a lekérdezési paraméter egy olyan célzást tartalmaz, amely a bejelentkezéshez használandó közösségi identitás-szolgáltatóról Azure AD B2C. Ha például az alkalmazás megadja, a `domain_hint=facebook.com` Bejelentkezés közvetlenül a Facebook bejelentkezési oldalára kerül.

![Bejelentkezési oldal regisztrálása az URL-cím domain_hint lekérdezési paraméterrel kiemelve](./media/direct-signin/domain-hint.png)

Ha egyéni szabályzatot használ, a tartománynevet a `<Domain>domain name</Domain>` tetszőleges XML-elem használatával is konfigurálhatja `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


