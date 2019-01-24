---
title: Egyéni házirendek, az alapszintű csomag az Azure Active Directory B2C ismertetése |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cfb3894b98ea562eede4c223ca6125abce65b0d3
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845571"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Az Azure AD B2C-vel egyéni szabályzat alapszintű csomag az egyéni szabályzatok ismertetése

Ez a szakasz felsorolja a B2C_1A_base házirend az összes mag elemét a **Kezdőcsomag** és a saját örökségének szabályzatok létrehozásához-t használja, amely a *B2C_1A_base_extensions házirend* .

Mint ilyen különösen több cikk foglalkozik a már definiált jogcímtípusok, a jogcímek átalakítása, tartalomdefiníciók, a technikai profil, és a központi felhasználói utak Jogcímszolgáltatók.

> [!IMPORTANT]
> A Microsoft nem vállal kifejezett vagy törvényi garanciát az alábbiakban megadott információk. Elérhetővé Válás időpontjában, vagy után, akkor a módosítások előtt végleges, bármikor előfordulhat, hogy kell bevezetni.

Megfelelő saját házirendeket és a B2C_1A_base_extensions szabályzatot is felülbírálhatja ezeket a definíciókat, és igény szerint további eszközök biztosításával a szülő házirend kiterjesztését.

Az alapvető elemei a *B2C_1A_base házirend* jogcímtípusok, a jogcímek átalakítása és tartalomdefiníciók. Ezeket az elemeket is ki van téve a megfelelő saját házirendeket is hasonlóan a lehet hivatkozni a *B2C_1A_base_extensions házirend*.

## <a name="claims-schemas"></a>Jogcímek sémák

A jogcím-sémák három részből áll:

1.  Első szakasz, amely felsorolja a minimális jogcímek a felhasználó Journey megfelelő működéséhez szükséges.
2.  Egy második szakasz, amely felsorolja a jogcímek más jogcímszolgáltatóktól, különösen a hitelesítéshez login.microsoftonline.com átadandó lekérdezési karakterlánc paraméterei és más speciális paraméterek megadása kötelező. **Ne módosítsa ezeket a jogcímeket**.
3.  És végül a harmadik szakasz további, opcionális jogcímeket a felhasználótól gyűjtött felsoroló könyvtárban, és küldött jogkivonatokat a bejelentkezés során. Új jogcím típusa kér a felhasználótól, és a jogkivonatban elküldött ebben a szakaszban is hozzáadhatók.

> [!IMPORTANT]
> A jogcímek séma például jelszavakat és a felhasználónevek egyes jogcímek korlátozásokat tartalmazza. A megbízható keretrendszer (TF) szabályzat kezeli az Azure AD bármilyen más jogcímszolgáltatótól, és minden korlátozást a rendszer az egyéni házirend modellezni. Sikerült módosítani a házirend hozzáadhat több korlátozást, vagy egy másik jogcím-szolgáltatói használja a hitelesítő adatok tárolása, aki a saját korlátozások.

A rendelkezésre álló jogcímtípusok, amelyeket alább láthatók.

### <a name="claims-that-are-required-for-the-user-journeys"></a>A felhasználói utak szükséges jogcímek

Felhasználói utak megfelelő működéséhez a következő jogcímek szükségesek:

| Jogcím típusa | Leírás |
|-------------|-------------|
| *UserId* | Felhasználónév |
| *signInName* | Jelentkezzen be a nevet |
| *tenantId* | Bérlő azonosítója (ID) az Azure AD B2C felhasználói objektum |
| *objectId* | Objektum azonosítója (ID) az Azure AD B2C felhasználói objektum |
| *jelszó* | Jelszó |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | A jelszóházirendek meghatározni a jelszó erősségét, lejárati és egyéb Azure AD B2C által használt. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | A felhasználó telefonszáma |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Lépjen kapcsolatba a használható e-mail-cím |
| *signInNamesInfo.emailAddress* | E-mail-címet, amely a felhasználó használhatja a bejelentkezni |
| *otherMails* | Kapcsolattartás a felhasználókkal használt e-mail címek |
| *userPrincipalName* | Az Azure AD B2C-ben tárolt felhasználónév |
| *upnUserName* | Egyszerű felhasználónév létrehozására szolgáló felhasználónév |
| *mailNickName* | Az Azure AD B2C-ben tárolt felhasználó mail nick neve |
| *newUser* | |
| *executed-SelfAsserted-Input* | Jogcímet, amely meghatározza, hogy attribútumok a felhasználótól gyűjtött |
| *executed-PhoneFactor-Input* | Jogcímet, amely meghatározza, hogy új telefonszámot gyűjtötte a program a felhasználó elől |
| *authenticationSource* | Itt adhatja meg, hogy a felhasználó hitelesítési közösségi Identitásszolgáltatóval, login.microsoftonline.com vagy helyi fiók |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>A lekérdezési karakterlánc paraméterei és más speciális paraméter szükséges jogcímek

A következő jogcímek szükségesek a Speciális paraméterek (beleértve az egyes lekérdezési karakterlánc paraméterei) át más jogcímszolgáltatóktól:

| Jogcím típusa | Leírás |
|-------------|-------------|
| *nux* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *nca* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *parancssor* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *mkt* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *lc* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *grant_type* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *Hatókör* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *client_id* | Speciális paramétert az helyi fiók hitelesítési login.microsoftonline.com |
| *objectIdFromSession* | Az alapértelmezett munkamenet-felügyeleti szolgáltató jelzi, hogy az objektum egy egyszeri bejelentkezés munkamenet olvassa-e által biztosított paraméter |
| *isActiveMFASession* | Az MFA munkamenet-kezelés jelzi, hogy a felhasználó rendelkezik-e a többtényezős hitelesítés aktív munkamenet által biztosított paraméter |

### <a name="additional-optional-claims-that-can-be-collected"></a>Gyűjthető további (nem kötelező) jogcím

A következő jogcímek további is gyűjtött a felhasználók, a címtárban tárolt és a jogkivonatban elküldött. Vázolt előtt, további jogcímek is hozzáadhatók a listához.

| Jogcím típusa | Leírás |
|-------------|-------------|
| *givenName* | A felhasználó utóneve (más néven keresztneve) |
| *Vezetéknév* | A felhasználó vezetékneve (más néven csomagcsalád nevét vagy vezetéknév) |
| *Extension_picture* | A felhasználó társadalombiztosítási kép |

## <a name="claim-transformations"></a>A jogcímek átalakításához

A rendelkezésre álló jogcímek átalakításához alább láthatók.

| Jogcím-átalakítást | Leírás |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Tartalomdefiníciók

Ez a szakasz ismerteti a már bejelentett tartalomdefiníciók a *B2C_1A_base* házirend. Ezek tartalomdefiníciók fogékonyak kell hivatkozott, felül, és/vagy kiterjesztett megfelelő saját házirendeket, valamint az igény szerint a *B2C_1A_base_extensions* házirend.

| Jogcím-szolgáltatói | Leírás |
|-----------------|-------------|
| *Facebook* | |
| *Bejelentkezés helyi fiókba* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Maga a kiszolgáló által megerősített* | |
| *Helyi fiók* | |
| *Munkamenet-kezelés* | |
| *Trustframework szabályzat motor* | |
| *TechnicalProfiles* | |
| *Jogkivonat kibocsátója* | |

## <a name="technical-profiles"></a>Technikai profilok

Ez a szakasz bemutatja a technikai profilok száma a jogcím-szolgáltató már deklarálva a *B2C_1A_base* házirend. Ezek a technikai profilok lehet további hivatkozott, felül, és/vagy kiterjesztett megfelelő saját házirendeket, valamint az igény szerint ki vannak téve a *B2C_1A_base_extensions* házirend.

### <a name="technical-profiles-for-facebook"></a>Technikai profilok, a Facebookra

| Technikai profil | Leírás |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Technikai profilok helyi fiókba

| Technikai profil | Leírás |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>A Phone Factor technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Az Azure Active Directory technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *AAD-Common* | Technikai profil más AAD-xxx technikai profilok szerint hozzáadva |
| *AAD-UserWriteUsingAlternativeSecurityId* | Közösségi bejelentkezések technikai profil |
| *AAD-UserReadUsingAlternativeSecurityId* | Közösségi bejelentkezések technikai profil |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Közösségi bejelentkezések technikai profil |
| *AAD-UserWritePasswordUsingLogonEmail* | Helyi fiókok technikai profil |
| *AAD-UserReadUsingEmailAddress* | Helyi fiókok technikai profil |
| *AAD-UserWriteProfileUsingObjectId* | Technikai profil használatával objectId felhasználói rekord frissítése |
| *AAD-UserWritePhoneNumberUsingObjectId* | Technikai profil használatával objectId felhasználói rekord frissítése |
| *AAD-UserWritePasswordUsingObjectId* | Technikai profil használatával objectId felhasználói rekord frissítése |
| *AAD-UserReadUsingObjectId* | Technikai profil segítségével adatokat olvasni. Miután a felhasználók hitelesítése |

### <a name="technical-profiles-for-self-asserted"></a>Az önkiszolgáló kiszolgáló által megerősített technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Helyi fiók technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>A munkamenet-kezelés technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Elem egyértelműségének biztosításához AAD munkamenet között jelentkezzen be, és jelentkezzen be a profil-név használatban van |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>A bizalmi keretrendszer házirend motor technikai profilok

Jelenleg nincsenek technikai profilok meghatározott a **Trustframework szabályzat motor TechnicalProfiles** jogcím-szolgáltatói.

### <a name="technical-profiles-for-token-issuer"></a>Jogkivonat kibocsátója technikai profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Felhasználói utak

Ez a szakasz mutatja be a felhasználói utak már deklarálva a *B2C_1A_base* házirend. Ezek az felhasználói utak fogékonyak lehet további hivatkozott, felül, és/vagy kiterjesztett megfelelő saját házirendeket, valamint az igény szerint a *B2C_1A_base_extensions* házirend.

| Felhasználói interakciósorozat | Leírás |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
