---
title: Közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával | Microsoft Docs
description: Megtudhatja, hogyan lehet előre kitölteni a bejelentkezési nevet, vagy egyenesen átirányítani egy közösségi identitás-szolgáltatóra.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8c0f3d8f3f49001e1326688ccc794e19d1148e5d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846889"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával

Ha Azure Active Directory (AD) B2C használatával állítja be az alkalmazásba való bejelentkezést, előre feltöltheti a bejelentkezési nevet vagy a közvetlen bejelentkezést egy adott közösségi identitás-szolgáltatóba, például a Facebook, a LinkedIn vagy egy Microsoft-fiók.

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előre feltöltése

A bejelentkezési felhasználói úton a függő entitás alkalmazás egy adott felhasználót vagy tartománynevet is megcélozhat. Egy felhasználó megcélzásakor az alkalmazás megadhatja az engedélyezési kérelemben a `login_hint` lekérdezési paramétert a felhasználó bejelentkezési nevével. Azure AD B2C automatikusan feltölti a bejelentkezési nevet, míg a felhasználónak csak meg kell adnia a jelszót.

![Bejelentkezési oldal regisztrálása az URL-ben Kiemelt login_hint lekérdezési paraméterekkel](./media/direct-signin/login-hint.png)

A felhasználó módosíthatja a bejelentkezési szövegmezőben lévő értéket.

Ha egyéni szabályzatot használ, bírálja felül a `SelfAsserted-LocalAccountSignin-Email` technikai profilt. A `<InputClaims>` szakaszban állítsa be a signInName `{OIDC:LoginHint}`jogcím DefaultValue értékét a következőre:. A `{OIDC:LoginHint}` változó tartalmazza a `login_hint` paraméter értékét. Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőt.

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

Ha úgy konfigurálta a bejelentkezési utat, hogy az alkalmazás belefoglalja a közösségi fiókokat, például a Facebookot, a linkedint vagy a `domain_hint` Google-t, megadhatja a paramétert. Ez a lekérdezési paraméter egy olyan célzást tartalmaz, amely a bejelentkezéshez használandó közösségi identitás-szolgáltatóról Azure AD B2C. Ha például az alkalmazás megadja `domain_hint=facebook.com`, a bejelentkezés közvetlenül a Facebook bejelentkezési oldalára kerül.

![Bejelentkezési oldal regisztrálása az URL-ben Kiemelt domain_hint lekérdezési paraméterekkel](./media/direct-signin/domain-hint.png)

Ha egyéni szabályzatot használ, a tartománynevet a `<Domain>domain name</Domain>` tetszőleges `<ClaimsProvider>`XML-elem használatával is konfigurálhatja.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


