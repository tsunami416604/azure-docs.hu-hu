---
title: Az egyéni házirend alapszintű csomagjának összetevői
titleSuffix: Azure AD B2C
description: A szabályzatok áttekintése a Azure Active Directory B2C egyéni házirend alapszintű csomagban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948181"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>A Azure AD B2C egyéni házirend alapszintű csomagjának egyéni házirendjeinek megismerése

Ez a szakasz felsorolja az **alapszintű csomaghoz** tartozó B2C_1A_base házirend összes alapvető elemét, amelyet a rendszer a saját szabályzatok az *B2C_1A_base_extensions házirend*öröklésével történő létrehozásához használ.

Ez különösen a már definiált jogcím-típusokra, a jogcímek átalakítására, a tartalmi definícióra, a szolgáltatóra és a műszaki profil (ok) ra vonatkozó, valamint az alapvető felhasználói útvonalakra összpontosít.

> [!IMPORTANT]
> A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot a fenti információk tekintetében. A módosítások a ga-idő, a GA Time vagy a After után bármikor bevezethetők.

A saját szabályzatok és a B2C_1A_base_extensions házirend felülbírálhatja ezeket a definíciókat, és szükség esetén további igényeket is kiterjesztheti.

A *B2C_1A_base szabályzat* alapvető elemei a jogcím típusai, a jogcím-átalakítások és a tartalmi definíciók. Ezek az elemek a saját szabályzatokban, valamint a *B2C_1A_base_extensions házirendben*is szerepelhetnek.

## <a name="claims-schemas"></a>Jogcím-sémák

A jogcím-sémák három szakaszra vannak osztva:

1.  Első szakasz, amely felsorolja a felhasználói utazások megfelelő működéséhez szükséges minimális jogcímeket.
2.  Egy második szakasz, amely felsorolja a lekérdezési karakterlánc paramétereinek és a más jogcím-szolgáltatóknak átadandó egyéb speciális paramétereket, különösen a hitelesítés login.microsoftonline.com. **Kérjük, ne módosítsa ezeket a jogcímeket**.
3.  Végül pedig egy harmadik szakasz, amely felsorolja azokat a további, választható jogcímeket, amelyeket a rendszer a címtárban tárol, és a bejelentkezés során jogkivonatokban is elküldhet. Ebben a szakaszban az új, a felhasználótól és/vagy a jogkivonatban eljuttatott jogcím-típust lehet felvenni.

> [!IMPORTANT]
> A jogcím séma bizonyos jogcímek, például jelszavak és felhasználónevek korlátozásait tartalmazza. A megbízhatósági keretrendszer (TF) házirendje az Azure AD-t minden más jogcím-szolgáltatóként kezeli, és az összes korlátozását az egyéni házirend alapján modellezi. Egy házirend módosítható további korlátozások hozzáadására, vagy egy másik jogcím-szolgáltató használatára a hitelesítő adatok tárolásához, amely saját korlátozásokkal rendelkezik.

Az elérhető jogcím-típusok az alábbiakban láthatók.

### <a name="claims-that-are-required-for-the-user-journeys"></a>A felhasználói utazásokhoz szükséges jogcímek

A felhasználói utazások megfelelő működéséhez a következő jogcímek szükségesek:

| Jogcím típusa | Leírás |
|-------------|-------------|
| *UserId* | Felhasználónév |
| *signInName* | Bejelentkezés neve |
| *tenantId* | A Azure AD B2C felhasználói objektumának bérlői azonosítója (azonosító) |
| *objectId* | A Azure AD B2C felhasználói objektumának objektumazonosító (azonosítója) |
| *jelszó* | Jelszó |
| *ÚjJelszó* | |
| *reenterPassword* | |
| *passwordPolicies* | Az Azure AD B2C által használt jelszóházirend a jelszó erősségének, lejáratának stb. meghatározásához. |
| *Sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Felhasználó telefonszáma |
| *Ellenőrizve. strongAuthenticationPhoneNumber* | |
| *e-mail* | A felhasználóval való kapcsolatfelvételhez használható e-mail-cím |
| *signInNamesInfo. emailAddress* | A felhasználó által a bejelentkezéshez használt e-mail-cím |
| *otherMails* | A felhasználóval való kapcsolatfelvételhez használható e-mail-címek |
| *userPrincipalName* | A Azure AD B2Cban tárolt Felhasználónév |
| *upnUserName* | Felhasználónév az egyszerű felhasználónév létrehozásához |
| *mailNickName* | A felhasználó e-mail-becenév neve, ahogy a Azure AD B2C tárolva |
| *newUser* | |
| *végrehajtott SelfAsserted – bemenet* | Jogcím, amely megadja, hogy a rendszer az attribútumokat gyűjtötte-e a felhasználótól |
| *végrehajtott PhoneFactor – bemenet* | Az a jogcím, amely megadja, hogy a rendszer új telefonszámot gyűjtött-e a felhasználótól |
| *authenticationSource* | Megadja, hogy a felhasználó hitelesítése a közösségi identitás-szolgáltatónál, login.microsoftonline.com vagy helyi fióknál történt-e |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>A lekérdezési karakterlánc paraméterei és egyéb speciális paraméterek esetén szükséges jogcímek

A következő jogcímek szükségesek a speciális paraméterek (többek között a lekérdezési karakterlánc paraméterei) átadásához más jogcím-szolgáltatók számára:

| Jogcím típusa | Leírás |
|-------------|-------------|
| *nux* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *NVÜ* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *gyors* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *MKT* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *LC* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *grant_type* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *hatókör* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *client_id* | A helyi fiók hitelesítéséhez átadott speciális paraméter a login.microsoftonline.com |
| *objectIdFromSession* | Az alapértelmezett munkamenet-kezelő szolgáltató által megadott paraméter, amely azt jelzi, hogy az objektumazonosító beolvasása egyszeri bejelentkezéses munkamenetből történt |
| *isActiveMFASession* | Az MFA-munkamenet felügyelete által biztosított paraméter azt jelzi, hogy a felhasználó aktív MFA-munkamenettel rendelkezik |

### <a name="additional-optional-claims-that-can-be-collected"></a>További (nem kötelező) jogcímek gyűjthetők

A következő jogcímek olyan további jogcímek, amelyeket a rendszer a címtárban tárolt felhasználóktól gyűjthet, és a jogkivonatban is elküldhet. Az előzőekben leírtak szerint további jogcímek is hozzáadhatók a listához.

| Jogcím típusa | Leírás |
|-------------|-------------|
| *givenName* | A felhasználó utóneve (más néven Utónév) |
| *vezetéknév* | Felhasználó vezetékneve (más néven családi név vagy vezetéknév) |
| *Extension_picture* | A felhasználó képe a közösségi |

## <a name="claim-transformations"></a>Jogcím-átalakítások

Az elérhető jogcím-átalakítások az alábbiakban láthatók.

| Jogcím-átalakítás | Leírás |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Tartalmi definíciók

Ez a szakasz a *B2C_1A_base* szabályzatban már deklarált tartalmi definíciókat ismerteti. Ezek a tartalmi definíciók arra hajlamosak, hogy a saját szabályzatokban, valamint a *B2C_1A_base_extensions* -házirendben szereplő igények szerint hivatkozhatnak, felülbírálják és/vagy kiterjeszthetők legyenek.

| Jogcím-szolgáltató | Leírás |
|-----------------|-------------|
| *Facebook* | |
| *Helyi fiók bejelentkezési* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Saját érvényesített* | |
| *Helyi fiók* | |
| *Munkamenet-kezelés* | |
| *Trustframework házirend motorja* | |
| *TechnicalProfiles* | |
| *Jogkivonat kiállítója* | |

## <a name="technical-profiles"></a>Technikai profilok

Ez a szakasz a *B2C_1A_base* szabályzatban már deklarált technikai profilokat ábrázolja. Ezek a műszaki profilok a saját szabályzatokban, valamint a *B2C_1A_base_extensions* házirendben szükséges további hivatkozásokat, felülbírált és/vagy kibővítést igényelnek.

### <a name="technical-profiles-for-facebook"></a>A Facebook technikai profiljai

| Technikai profil | Leírás |
|-------------------|-------------|
| *Facebook – OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Helyi fiók bejelentkezési tartozó műszaki profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *Bejelentkezés – nem interaktív* | |

### <a name="technical-profiles-for-phone-factor"></a>Technikai profilok a telefonos faktorhoz

| Technikai profil | Leírás |
|-------------------|-------------|
| *PhoneFactor – bemenet* | |
| *PhoneFactor – InputOrVerify* | |
| *PhoneFactor – ellenőrzés* | |

### <a name="technical-profiles-for-azure-active-directory"></a>A Azure Active Directory technikai profiljai

| Technikai profil | Leírás |
|-------------------|-------------|
| *HRE – gyakori* | A más HRE – XXX technikai profilok részét képező technikai profil |
| *HRE – UserWriteUsingAlternativeSecurityId* | Technikai profil a közösségi bejelentkezésekhez |
| *HRE – UserReadUsingAlternativeSecurityId* | Technikai profil a közösségi bejelentkezésekhez |
| *HRE-UserReadUsingAlternativeSecurityId – hiba* | Technikai profil a közösségi bejelentkezésekhez |
| *HRE – UserWritePasswordUsingLogonEmail* | Helyi fiókok technikai profilja |
| *HRE – UserReadUsingEmailAddress* | Helyi fiókok technikai profilja |
| *HRE – UserWriteProfileUsingObjectId* | Technikai profil a felhasználói rekordok frissítéséhez a objectId használatával |
| *HRE – UserWritePhoneNumberUsingObjectId* | Technikai profil a felhasználói rekordok frissítéséhez a objectId használatával |
| *HRE – UserWritePasswordUsingObjectId* | Technikai profil a felhasználói rekordok frissítéséhez a objectId használatával |
| *HRE – UserReadUsingObjectId* | A technikai profil a felhasználó hitelesítése után az adatolvasásra használatos |

### <a name="technical-profiles-for-self-asserted"></a>Saját igényekkel rendelkező műszaki profilok

| Technikai profil | Leírás |
|-------------------|-------------|
| *SelfAsserted – közösségi* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Helyi fiók technikai profiljai

| Technikai profil | Leírás |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>A munkamenet-kezelés technikai profiljai

| Technikai profil | Leírás |
|-------------------|-------------|
| *SM – NOOP* | |
| *SM – HRE* | |
| *SM – SocialSignup* | A profil neve a regisztráció és a bejelentkezés közötti HRE-munkamenet egyértelműsítse használatos. |
| *SM – SocialLogin* | |
| *SM – MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>A megbízhatósági keretrendszer házirend-motorjának technikai profiljai

Jelenleg nem határoznak meg technikai profilokat a **Trustframework Policy Engine TechnicalProfiles** -szolgáltatóhoz.

### <a name="technical-profiles-for-token-issuer"></a>A jogkivonat-kiállító technikai profiljai

| Technikai profil | Leírás |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Felhasználói utazások

Ez a szakasz a *B2C_1A_base* szabályzatban már deklarált felhasználói útvonalakat ábrázolja. Ezek a felhasználói utazások a saját szabályzatokban, valamint a *B2C_1A_base_extensions* -házirendben szükséges további hivatkozásokat, felülbírálják és/vagy kiterjeszthetők.

| Felhasználói út | Leírás |
|--------------|-------------|
| *Regisztráció* | |
| *Bejelentkezési* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
