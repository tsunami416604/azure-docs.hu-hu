---
title: Állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Ismerje meg, hogy feltöltse a bejelentkezési neve, vagy közvetlenül egy közösségi identitásszolgáltatót irányítsa át.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 62ded87067bf597a2f40ec8e8405142297d4fb78
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440536"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Állítsa be a közvetlen bejelentkezés az Azure Active Directory B2C használatával

Állítson be jelentkezzen be az alkalmazás az Azure Active Directory (AD) B2C-vel, megadhatja a bejelentkezési név vagy közvetlen jelentkezzen be egy adott közösségi identitásszolgáltató, például a Facebook, a LinkedIn, vagy a Microsoft-fiókkal. 

## <a name="prepopulate-the-sign-in-name"></a>Feltöltse a bejelentkezési neve

Során a bejelentkezési felhasználói interakciósorozat egy függő entitás alkalmazás megcélozhatnak egy adott felhasználó vagy tartomány neve. Amikor megcélozni a felhasználókat, megadhatja az alkalmazás, az engedélyezési kérésben a `login_hint` lekérdezési paraméter az a felhasználó bejelentkezési nevét. Az Azure AD B2C automatikusan feltölti a bejelentkezési neve, amíg a felhasználó csak kell adnia a jelszót.

![bejelentkezési mutató használatával](./media/direct-signin/login-hint.png) 

A felhasználó nem módosíthatja a bejelentkezési szövegmező értékét.

Ha egyéni szabályzatot használ, bírálja felül a `SelfAsserted-LocalAccountSignin-Email` technikai profil. Az a `<InputClaims>` területen állítsa be a signInName kérelemnek a DefaultValue `{OIDC:LoginHint}`. A `{OIDC:LoginHint}` változó értékét tartalmazza a `login_hint` paraméter. Az Azure AD B2C beolvassa a signInName jogcím értékét, és előre feltölti a signInName szövegmezőbe.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Jelentkezzen be egy közösségi szolgáltató átirányítása

Ha konfigurálta a bejelentkezési utazás, hogy az alkalmazás közé tartozik a közösségi fiókok, mint a Facebook, a LinkedIn, vagy a Google, megadhatja a `domain_hint` paraméter. A lekérdezési paraméter mutatót Azure AD B2C-vel kapcsolatban a közösségi identitásszolgáltató a bejelentkezéshez használandó. Például, ha az alkalmazás az `domain_hint=facebook.com`, jelentkezzen be közvetlenül kerül a Facebook-Bejelentkezés lapra.

![tartományemlékeztető használatával](./media/direct-signin/domain-hint.png) 

Ha egyéni szabályzatot használ, a tartomány neve használatával konfigurálhatja a `<Domain>domain name</Domain>` XML-elem bármely `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


