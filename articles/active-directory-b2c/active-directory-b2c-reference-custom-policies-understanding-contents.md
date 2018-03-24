---
title: 'Az Azure Active Directory B2C: Az alapszintű csomag egyéni házirendjeinek ismertetése |} Microsoft Docs'
description: 'Témakör: Azure Active Directory B2C egyéni házirendek'
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 624a40b1e40db6ceac9c567926b3932449e7bf7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Az Azure AD B2C egyéni házirend alapszintű csomag egyéni házirendjeinek ismertetése

Ez a rész felsorolja a B2C_1A_base szabályzat részeként elérhető összes core elem a **alapszintű csomag** és, hogy megfelelő saját házirendeket a örökléssel tartalomkészítéshez elkészítéséhez használja a *B2C_1A_base_extensions házirend* .

Mint ilyen akkor különösen mutatja be a már definiált jogcímtípusok, a jogcímek átalakítása, tartalom definíciók, az a műszaki profil és a központi felhasználói utak jogcímszolgáltatóktól.

> [!IMPORTANT]
> A Microsoft nem vállal sem kifejezett szavatosságot, a továbbiakban megadott adatai. GA idő, illetve után a módosítások vihetők bármikor GA időpont előtt.

Megfelelő saját házirendeket, mind a B2C_1A_base_extensions házirend felülírja ezeket a definíciókat, és a szülő házirend kiterjesztése újak megadásával, igény szerint.

A fő elemei a *B2C_1A_base házirend* jogcímtípusok, a jogcímek átalakítása és a tartalom definíciókat. Ezeket az elemeket is ki vannak téve a megfelelő saját házirendeket is és a lehet hivatkozni a *B2C_1A_base_extensions házirend*.

## <a name="claims-schemas"></a>Jogcímek sémák

A jogcím-sémák három részből áll:

1.  Első szakasz, amely felsorolja a minimális jogcímet a felhasználó utak helyes működéséhez szükséges.
2.  A jogcímek listája második szakasz szükséges lekérdezési karakterlánc és más különleges paraméterei más jogcímszolgáltatóktól, különösen a hitelesítéshez login.microsoftonline.com átadandó. **Ne módosítsa ezeket a jogcímeket**.
3.  És végül a harmadik szakasz további, opcionális jogcímeket a felhasználó a gyűjtendő felsoroló a könyvtárban tárolja, és a bejelentkezés során küldött jogkivonatokat. A felhasználó gyűjtött vagy a token küldött új jogcím típusa ebben a szakaszban lehet hozzáadni.

> [!IMPORTANT]
> A jogcímek séma egyes jogcímek, például a jelszavak és a felhasználónevek korlátozásokat tartalmaz. A megbízható keretrendszer (TF) házirend más jogcím-szolgáltató kezeli az Azure AD és a korlátozások vannak modellezni a prémium szintű házirendben. További korlátozások hozzáadása, vagy egy másik jogcím-szolgáltató használata a hitelesítő adatok tárolása, amelynek a saját korlátozások házirend volt módosítani.

A rendelkezésre álló jogcím-típusok listája látható.

### <a name="claims-that-are-required-for-the-user-journeys"></a>A felhasználó utak szükséges jogcímeket

A következő jogcímek felhasználói utak helyes működéséhez szükségesek:

| Jogcím típusa | Leírás |
|-------------|-------------|
| *UserId* | Felhasználónév |
| *signInName* | Jelentkezzen be neve |
| *tenantId* | Bérlő azonosítóját az Azure AD B2C-támogatás a felhasználói objektum |
| *objectId* | Objektumazonosító (ID) az Azure AD B2C-támogatás a felhasználói objektum |
| *Jelszó* | Jelszó |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | A jelszóházirendek az Azure AD B2C prémium alapján határozzák meg a jelszó erőssége, a lejárat, stb. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | A felhasználó telefonszáma |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Kapcsolattartás a felhasználókkal használható e-mail cím |
| *signInNamesInfo.emailAddress* | A felhasználó használhatja-e bejelentkezni az e-mail címet |
| *otherMails* | Kapcsolattartás a felhasználókkal használt e-mail címek |
| *userPrincipalName* | Az Azure AD B2C prémium tárolt felhasználónév |
| *upnUserName* | Egyszerű felhasználónév létrehozásához felhasználónév |
| *mailNickName* | Mail nick felhasználónév tárolt az Azure AD B2C-támogatás |
| *newUser* | |
| *executed-SelfAsserted-Input* | Amely meghatározza, hogy attribútumok gyűjtötte a program a felhasználói jogcímek |
| *executed-PhoneFactor-Input* | Jogcímet, amely megadja, hogy egy új telefonszámot gyűjtötte a program a felhasználó részéről |
| *authenticationSource* | Megadja, hogy a felhasználó hitelesítési közösségi identitásszolgáltató, login.microsoftonline.com vagy helyi fiók |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>A lekérdezési karakterlánc és más különleges paraméterei szükséges jogcímeket

A következő jogcímeket más jogcímszolgáltatóktól kell továbbítani a Speciális paraméterek (beleértve az egyes lekérdezési karakterlánc paraméterek):

| Jogcím típusa | Leírás |
|-------------|-------------|
| *nux* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *nca* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *prompt* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *mkt* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *lc* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *grant_type* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *scope* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *client_id* | Speciális paramétert a helyi fiók hitelesítési login.microsoftonline.com |
| *objectIdFromSession* | Az alapértelmezett munkamenet felügyeleti szolgáltató annak jelzésére, hogy az egyszeri bejelentkezési munkamenet objektumazonosító beolvasása a megadott paraméter |
| *isActiveMFASession* | A többtényezős hitelesítés annak jelzésére, hogy a felhasználó rendelkezik-e többtényezős hitelesítés aktív munkamenet munkamenet-kezelés által biztosított paraméter |

### <a name="additional-optional-claims-that-can-be-collected"></a>További (nem kötelező) jogcímeket is

A következő jogcímek további gyűjtött felhasználói, a címtárban tárolt, és a token küldi. Mielőtt leírtak további jogcímeket is hozzáadhatók erre a listára.

| Jogcím típusa | Leírás |
|-------------|-------------|
| *givenName* | A megadott felhasználónév (más néven Keresztnév) |
| *surname* | Felhasználó vezetékneve (más néven Családnév vagy vezetéknevet) |
| *Extension_picture* | Felhasználó társadalombiztosítási kép |

## <a name="claim-transformations"></a>A jogcímek átalakításához

A rendelkezésre álló a jogcímek átalakításához alább láthatók.

| Claim transformation | Leírás |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Tartalom definíciók

Ez a szakasz ismerteti a tartalom-definíciók már deklarálva a *B2C_1A_base* házirend. A tartalom definíciók lehet hivatkozni, felül, illetve szükség esetén a megfelelő saját házirendeket, valamint hasonlóan a kiterjesztett ki vannak téve a *B2C_1A_base_extensions* házirend.

| Jogcím-szolgáltató | Leírás |
|-----------------|-------------|
| *Facebook* | |
| *Helyi fiók SignIn* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Önkiszolgáló magas* | |
| *Helyi fiók* | |
| *Munkamenet-kezelés* | |
| *A házirendmotor Trustframework* | |
| *TechnicalProfiles* | |
| *Jogkivonatot kibocsátó* | |

## <a name="technical-profiles"></a>Műszaki profilok

Ez a szakasz mutatja be a műszaki profilok száma a jogcímszolgáltató már deklarálva a *B2C_1A_base* házirend. A műszaki profilok lehet további hivatkozott, felül, illetve szükség esetén a megfelelő saját házirendeket, valamint hasonlóan a kiterjesztett ki vannak téve a *B2C_1A_base_extensions* házirend.

### <a name="technical-profiles-for-facebook"></a>Műszaki profilokat a Facebook-on

| Műszaki profil | Leírás |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>A helyi fiókkal bejelentkezik műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>A Phone Factor műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Az Azure Active Directory műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *Az AAD-közös* | A más AAD-xxx műszaki profil része műszaki profil |
| *AAD-UserWriteUsingAlternativeSecurityId* | A közösségi bejelentkezések során műszaki profil |
| *AAD-UserReadUsingAlternativeSecurityId* | A közösségi bejelentkezések során műszaki profil |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | A közösségi bejelentkezések során műszaki profil |
| *Az AAD-UserWritePasswordUsingLogonEmail* | A helyi fiókok műszaki profil |
| *AAD-UserReadUsingEmailAddress* | A helyi fiókok műszaki profil |
| *AAD-UserWriteProfileUsingObjectId* | Műszaki profil használatával objectId felhasználói rekord frissítéséhez |
| *AAD-UserWritePhoneNumberUsingObjectId* | Műszaki profil használatával objectId felhasználói rekord frissítéséhez |
| *AAD-UserWritePasswordUsingObjectId* | Műszaki profil használatával objectId felhasználói rekord frissítéséhez |
| *AAD-UserReadUsingObjectId* | Műszaki profil segítségével adatokat olvasni, miután a felhasználók hitelesítése |

### <a name="technical-profiles-for-self-asserted"></a>Az önkiszolgáló magas műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Helyi fiók műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Műszaki profilokat a munkamenet-kezelés

| Műszaki profil | Leírás |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Elem egyértelműségének biztosításához AAD munkamenet között jelentkezzen be, és jelentkezzen be a profil neve használatban van |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Trustframework házirend motor TechnicalProfiles műszaki profilok

Jelenleg nincsenek technikai profilok meghatározása a **Trustframework házirend motor TechnicalProfiles** jogcím-szolgáltató.

### <a name="technical-profiles-for-token-issuer"></a>Jogkivonatot kibocsátó műszaki profilok

| Műszaki profil | Leírás |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Felhasználói utak

Ez a szakasz mutatja be a felhasználó utak már deklarálva a *B2C_1A_base* házirend. Ezek az felhasználói utak amelyek ki vannak téve lehet további hivatkozott, felül, és/vagy szükség esetén a megfelelő saját házirendeket, valamint hasonlóan a kiterjesztett a *B2C_1A_base_extensions* házirend.

| Felhasználói út | Leírás |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
