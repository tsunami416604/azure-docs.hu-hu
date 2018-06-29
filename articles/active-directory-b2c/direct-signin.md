---
title: Állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával |} Microsoft Docs
description: Megtudhatja, hogyan feltöltse a bejelentkezési név vagy egy közösségi identitásszolgáltató rögtön átirányítása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc3baa8fe4139acec94a722bf8c2bccb708a9470
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102325"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával

Beállításakor bejelentkezés Azure Active Directory (AD) B2C használatával az alkalmazáshoz, megadhatja a bejelentkezési név vagy közvetlen bejelentkezés az egy adott közösségi identitásszolgáltató, például a Facebook, a LinkedIn vagy a Microsoft-fiókkal. 

## <a name="prepopulate-the-sign-in-name"></a>A bejelentkezési név előre

A bejelentkezési felhasználói út során a függő entitás alkalmazás egy adott felhasználó vagy tartomány neve lehet, hogy célként. Ha a célcsoport-kezelési egy felhasználó egy alkalmazást adhat meg, a hitelesítési kérés az `login_hint` lekérdezésparaméter a felhasználói bejelentkezési névvel. Az Azure AD B2C automatikusan tölti fel a bejelentkezési név, amíg a felhasználó csak kell megadni. a jelszó.

![bejelentkezési mutatót használ](./media/direct-signin/login-hint.png) 

A felhasználó nem tudja módosítani a bejelentkezési szövegmező értékét.

Ha egyéni házirendet használja, akkor bírálja felül a `SelfAsserted-LocalAccountSignin-Email` műszaki profil. Az a `<InputClaims>` területen állítsa be az signInName jogcím DefaultValue `{OIDC:LoginHint}`. A `{OIDC:LoginHint}` változó értékét tartalmazza a `login_hint` paraméter. Az Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőjét.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>A közösségi szolgáltató bejelentkezési átirányítása

Ha konfigurálta a bejelentkezési útra közé tartozik a közösségi fiókok, például a Facebook, a LinkedIn vagy a Google, hogy az alkalmazás, megadhatja a `domain_hint` paraméter. A következő lekérdezésparaméter mutatót Azure AD B2C a bejelentkezéshez használandó közösségi identitásszolgáltató kapcsolatban. Ha az alkalmazás megadja például `domain_hint=facebook.com`, bejelentkezési közvetlenül a Facebook bejelentkezési lapra ugrik.

![tartomány mutatót használ](./media/direct-signin/domain-hint.png) 

Ha egyéni házirendet használja, a tartomány neve használatával konfigurálhatja a `<Domain>domain name</Domain>` XML-elemet minden `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


