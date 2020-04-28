---
title: Feltételes hozzáférés VÁLLALATKÖZI együttműködési felhasználók számára – Azure AD
description: Azure Active Directory B2B-együttműködés támogatja a többtényezős hitelesítést (MFA) a vállalati alkalmazások szelektív eléréséhez
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272997"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Feltételes hozzáférés VÁLLALATKÖZI együttműködéssel rendelkező felhasználók számára

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication a B2B-felhasználók számára
Az Azure AD B2B együttműködés révén a szervezetek a VÁLLALATKÖZI felhasználók többtényezős hitelesítési (MFA) házirendjeit is kihasználhatják. Ezek a szabályzatok kikényszeríthető a bérlőre, az alkalmazásra vagy az egyéni felhasználói szintre, ugyanúgy, ahogy a teljes munkaidőben dolgozó alkalmazottak és a szervezet tagjai számára engedélyezve vannak. Az MFA-szabályzatokat az erőforrás-szervezet kényszeríti ki.

Példa:
1. Az A vállalat rendszergazdája vagy informatikai dolgozója meghívja a B vállalat felhasználóját az A vállalaton belül egy *foo* -alkalmazásba.
2. Az A vállalaton belüli Application *foo* úgy van konfigurálva, hogy az MFA használatát igényli.
3. Ha a B vállalat felhasználója megpróbál hozzáférni az alkalmazás *foo* -hez a bérlőn belül, a rendszer megkéri, hogy végezzenek el egy MFA-feladatot.
4. A felhasználó az A vállalatnál állíthatja be a MFA-t, és kiválaszthatja az MFA-beállítást.
5. Ez a forgatókönyv bármilyen identitás (Azure AD vagy MSA esetében működik), például ha a B vállalat felhasználóinak hitelesítése közösségi azonosító használatával történik.
6. Az A vállalatnak elegendő prémium szintű Azure AD-licenccel kell rendelkeznie az MFA támogatásához. A B vállalat felhasználója ezt a licencet használja az A vállalattól.

A meghívásos bérlet mindig felelős az MFA-nak a partner szervezet felhasználói számára, még akkor is, ha a partnerszervezet rendelkezik MFA-képességekkel.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Az MFA beállítása B2B csoportmunka-felhasználók számára
Ha szeretné megtudni, hogy az MFA hogyan állítható be VÁLLALATKÖZI együttműködési felhasználók számára, tekintse meg a következő videó útmutatását:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>VÁLLALATKÖZI felhasználói MFA-élmény az ajánlat beváltásához
Tekintse meg a következő animációt a beváltási élmény megtekintéséhez:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA alaphelyzetbe állítása B2B csoportmunka-felhasználók számára
A rendszergazda jelenleg csak a következő PowerShell-parancsmagok használatával kérheti újra a B2B-együttműködés felhasználóit:

1. Csatlakozás az Azure AD szolgáltatáshoz

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Az összes felhasználó beolvasása a proof up metódusokkal

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Például:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Egy adott felhasználó MFA-metódusának alaphelyzetbe állításával megkövetelheti, hogy a B2B Collaboration-felhasználó újra állítsa be a hitelesítési módszereket. Példa:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Miért végzünk MFA-t az erőforrás-bérletben?

A jelenlegi kiadásban az MFA a kiszámíthatóság miatt mindig az erőforrás-bérletben van. Tegyük fel például, hogy egy contoso-felhasználó (Sally) meghívja a fabrikam-t, és a fabrikam engedélyezte az MFA használatát a B2B-felhasználók számára.

Ha a contoso MFA-szabályzata engedélyezve van a App1, de nem App2, akkor ha megtekintjük a contoso MFA-jogcímet a jogkivonatban, a következő probléma jelenhet meg:

* 1. nap: A felhasználó rendelkezik MFA-val a contoso-ban, és hozzáfér a App1-hez, és nem jelenik meg további MFA-kérdés a fabrikam-ben.

* 2. nap: a felhasználó hozzáfért a contoso-on található 2. alkalmazáshoz, így most, amikor a Fabrikamhoz fér hozzá, regisztrálnia kell az MFA-hoz.

Ez a folyamat zavaró lehet, és a bejelentkezési befejezéshez vezethet.

Emellett akkor is, ha a contoso rendelkezik MFA-képességgel, nem minden esetben a fabrikam megbízik a contoso MFA-szabályzatban.

Végül pedig az erőforrás-bérlői MFA a MSAs és a közösségi azonosítók, valamint olyan partneri szervezethez esetében is működik, amelyeken nincs beállítva az MFA.

Ezért a VÁLLALATKÖZI felhasználók MFA-ra vonatkozó javaslata mindig megköveteli az MFA használatát a meghívó bérlőben. Ez a követelmény bizonyos esetekben kettős MFA-t eredményezhet, de ha a meghívó bérlőhöz fér hozzá, a végfelhasználói élmény kiszámítható: Sally regisztrálni kell az MFA-t a meghívó Bérlővel.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Eszközökön alapuló, hely-és kockázatalapú feltételes hozzáférés a B2B-felhasználók számára

Ha a contoso engedélyezi az eszközön alapuló feltételes hozzáférési szabályzatokat a vállalati adatokhoz, a hozzáférés megkerül a contoso által nem felügyelt eszközökön, és nem felel meg a contoso-eszközök szabályzatának.

Ha a B2B-felhasználó eszközét nem a contoso felügyeli, akkor a VÁLLALATKÖZI felhasználók a partneri szervezetektől való hozzáférése bármely kontextusban le van tiltva. A contoso azonban létrehozhat olyan kizárási listát, amely konkrét partner felhasználókat tartalmaz, hogy kizárják őket az eszközön alapuló feltételes hozzáférési szabályzatból.

#### <a name="mobile-application-management-policies-for-b2b"></a>A B2B-hez készült Mobile Application Management-szabályzatok

A feltételes hozzáférést biztosító alkalmazás-védelmi szabályzatok nem alkalmazhatók a B2B-felhasználókra, mert a meghívó szervezetnek nincs láthatósága a B2B-felhasználó otthoni szervezetében.

#### <a name="location-based-conditional-access-for-b2b"></a>Hely-alapú feltételes hozzáférés B2B-hez

A helyszíni feltételes hozzáférési szabályzatok kikényszeríthető a B2B-felhasználók számára, ha a meghívó szervezet képes létrehozni egy megbízható IP-címtartományt, amely meghatározza a partner szervezeteiket.

#### <a name="risk-based-conditional-access-for-b2b"></a>Kockázatalapú feltételes hozzáférés a B2B-hez

A kockázatalapú bejelentkezési szabályzatok jelenleg nem alkalmazhatók a B2B-felhasználókra, mert a kockázatértékelést a VÁLLALATKÖZI felhasználó otthoni szervezete végzi.

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködésének licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
