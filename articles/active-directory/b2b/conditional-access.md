---
title: Feltételes hozzáférés a B2B együttműködési felhasználók számára – Azure AD
description: Az Azure Active Directory B2B együttműködése támogatja a többtényezős hitelesítést (MFA) a vállalati alkalmazásokhoz való szelektív hozzáféréshez
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272997"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Feltételes hozzáférés a B2B együttműködési felhasználók számára

## <a name="multi-factor-authentication-for-b2b-users"></a>Többtényezős hitelesítés B2B felhasználók számára
Az Azure AD B2B együttműködéssel a szervezetek többtényezős hitelesítési (MFA) szabályzatokat kényszeríthetnek ki a B2B-felhasználók számára. Ezek a szabályzatok a bérlői, alkalmazás- vagy egyéni felhasználói szinten is kényszeríthetők, ugyanúgy, ahogy a teljes munkaidőben foglalkoztatottak és a szervezet tagjai számára engedélyezve vannak. Az MFA-házirendek az erőforrás-szervezetnél vannak kényszerítve.

Példa:
1. Az "A" vállalat rendszergazdája vagy információs munkatársa meghívja a b vállalat felhasználóját egy *Foo* alkalmazásra az A vállalatban.
2. Az "A" vállalat *alkalmazásfoo-ja* úgy van beállítva, hogy a hozzáféréshez többfa-t igényeljen.
3. Amikor a b vállalat felhasználója megpróbál hozzáférni a *Foo* alkalmazáshoz az "A" vállalat bérlőjében, a rendszer megkéri őket, hogy végezzenek el egy MFA-kihívást.
4. A felhasználó beállíthatja az MFA-t az A vállalattal, és kiválaszthatja az MFA-beállítást.
5. Ez a forgatókönyv bármilyen identitásnál működik (Az Azure AD vagy az MSA, például ha a B vállalat felhasználói közösségi azonosítóval hitelesítik magukat)
6. "A" vállalatnak elegendő prémium szintű Azure AD-licenccel kell rendelkeznie, amely támogatja az MFA-t. A B vállalat felhasználója ezt a licencet az A vállalattól használja fel.

A meghívó bérleti mindig felelős a partnerszervezet felhasználóiszámára az MFA-ért, még akkor is, ha a partnerszervezet MFA-képességekkel rendelkezik.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Az MFA beállítása a B2B együttműködési felhasználók számára
Ha meg szeretné tudni, hogy milyen egyszerű az MFA beállítása a B2B együttműködési felhasználók számára, tekintse meg, hogyan az alábbi videóban:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B felhasználók MFA tapasztalat ajánlat visszaváltás
A beváltási élmény megtekintéséhez tekintse meg az alábbi animációt:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Az MFA alaphelyzetbe állítása a B2B együttműködési felhasználók számára
Jelenleg a rendszergazda megkövetelheti a B2B együttműködési felhasználóktól, hogy csak a következő PowerShell-parancsmagok használatával igazolják újra:

1. Csatlakozás az Azure AD szolgáltatáshoz

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Minden felhasználó beszerezni a próba-up módszereket

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Például:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Állítsa alaphelyzetbe az MFA-módszert egy adott felhasználó számára, hogy a B2B együttműködési felhasználónak újra be kell állítania a próbanyomati módszereket. Példa:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Miért végezzük az MFA-t az erőforrás-bérlőnél?

Az aktuális kiadásban az MFA mindig az erőforrás-bérleti, a kiszámíthatóság miatt. Tegyük fel például, hogy egy Contoso-felhasználó (Sally) meghívást kap a Fabrikamba, és a Fabrikam engedélyezte az MFA-t a B2B-felhasználók számára.

Ha a Contoso alkalmazás1-hez engedélyezve van az MFA-szabályzat, de az App2 nem, akkor ha megnézzük a Contoso MFA-jogcímet a jogkivonatban, a következő probléma jelenhet meg:

* 1. nap: A felhasználó mfa-val rendelkezik a Contoso-ban, és az App1-hez ér hozzá, majd a Fabrikam ban nem jelenik meg további MFA-üzenet.

* 2. nap: A felhasználó hozzáfért az App 2-höz a Contoso-ban, így most a Fabrikam elérésekor regisztrálnia kell az MFA-ra.

Ez a folyamat zavaró lehet, és a bejelentkezési befejezések csökkenéséhez vezethet.

Továbbá, még akkor is, ha a Contoso rendelkezik MFA-képességgel, nem mindig ez a helyzet, hogy a Fabrikam megbízik a Contoso MFA-házirendben.

Végül az erőforrás-bérlői MFA msa-khoz és közösségi azonosítókhoz, valamint olyan partnerszervezetekhez is működik, amelyekhez nincs mfa-beállítás.

Ezért a B2B-felhasználók több-, mfa-felhasználók nak azt a javaslatot, hogy mindig szükség MFA a meghívó bérlő. Ez a követelmény bizonyos esetekben dupla Többszintű környezetben, de amikor a meghívó bérlő elérésekor a végfelhasználók élménye kiszámítható: Sally regisztrálnia kell az MFA-ra a meghívó bérlővel.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Eszközalapú, helyalapú és kockázatalapú feltételes hozzáférés A B2B-felhasználók számára

Ha a Contoso engedélyezi az eszközalapú feltételes hozzáférési szabályzatokat a vállalati adatokhoz, a hozzáférés nem érhető el a Contoso által nem felügyelt és a Contoso-eszközházirendeknek nem megfelelő eszközökről.

Ha a B2B-felhasználó eszközét nem a Contoso kezeli, a partnerszervezetek B2B-felhasználóinak hozzáférése le van tiltva, bármilyen környezetben ezek a házirendek érvénybe lépnek. A Contoso azonban létrehozhat bizonyos partnerfelhasználókat tartalmazó kizárási listákat, hogy kizárja őket az eszközalapú feltételes hozzáférési szabályzatból.

#### <a name="mobile-application-management-policies-for-b2b"></a>Mobilalkalmazás-kezelési házirendek a B2B-hez

A feltételes hozzáférésű alkalmazásvédelmi szabályzatok nem alkalmazhatók a B2B-felhasználókra, mert a meghívó szervezet nem rendelkezik a B2B-felhasználó otthoni szervezetének láthatóságával.

#### <a name="location-based-conditional-access-for-b2b"></a>Helyalapú feltételes hozzáférés a B2B-hez

A helyalapú feltételes hozzáférési házirendek kényszeríthetők a B2B-felhasználók számára, ha a meghívó szervezet képes létrehozni egy megbízható IP-címtartományt, amely meghatározza a partnerszervezeteiket.

#### <a name="risk-based-conditional-access-for-b2b"></a>Kockázatalapú feltételes hozzáférés a B2B-hez

Jelenleg a kockázatalapú bejelentkezési szabályzatok nem alkalmazhatók a B2B-felhasználókra, mert a kockázatértékelést a B2B-felhasználó otthoni szervezeténél hajtják végre.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködésének licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
