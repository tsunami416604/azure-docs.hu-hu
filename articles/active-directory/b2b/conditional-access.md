---
title: Feltételes hozzáférés az Azure Active Directory B2B együttműködési felhasználókat |} A Microsoft Docs
description: Az Azure Active Directory B2B együttműködés támogatja a többtényezős hitelesítés (MFA) szelektív hozzáférést a vállalati alkalmazások
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 8938f4bcf012eadb8d4b0ced92b9996bc58236a5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432047"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Feltételes hozzáférés B2B-együttműködés felhasználók

## <a name="multi-factor-authentication-for-b2b-users"></a>Többtényezős hitelesítés a B2B-felhasználók
Az Azure AD B2B együttműködés szervezetek kényszerítheti a többtényezős hitelesítés (MFA) házirendek a B2B-felhasználók. Ezek a szabályzatok ugyanúgy, hogy a teljes munkaidejű alkalmazottak és a szervezet tagjai engedélyezve vannak a bérlő, alkalmazás vagy egyéni felhasználói szintjén, kényszeríthető. MFA házirendeket a rendszer érvényesíti az erőforrás-szervezetben.

Példa:
1. A vállalat rendszergazdai vagy adatokat feldolgozó meghívót küld a felhasználó céges B alkalmazás *Foo* vállalat rögzíti.
2. Alkalmazás *Foo* vállalat A többtényezős hitelesítés megkövetelése a hozzáférési van konfigurálva.
3. Amikor a felhasználó céges B megpróbálja elérni az alkalmazást *Foo* a vállalat egy bérlő, a rendszer megkéri, végezze el az MFA-hitelesítést.
4. A felhasználó állíthatja be a többtényezős hitelesítés az a vállalat és a többtényezős hitelesítés lehetőséget választja.
5. Ebben a forgatókönyvben minden identitáshoz működik (Azure ad-ben vagy az MSA, például, ha a B vállalat felhasználóinak hitelesítése társadalombiztosítási azonosító használatával)
6. A vállalat, amely támogatja az MFA-elegendő az Azure AD Premium-licencet kell rendelkeznie. A felhasználó céges B ezt a licencet. a cég használ fel.

A meghívó bérlőben felelős mindig a multi-factor Authentication a fiókpartner-szervezet, a felhasználók akkor is, ha a fiókpartner-szervezet MFA képességekkel rendelkezik.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B-együttműködés felhasználók többtényezős hitelesítés beállítása
Fedezze fel, mennyire egyszerű is B2B együttműködési felhasználókat a többtényezős hitelesítés beállításához, tekintse meg az alábbi videó hogyan:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B-felhasználók az MFA élmény a beváltási ajánlat
Tekintse meg az alábbi animáció megtekintéséhez a beváltási élményt:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Többtényezős hitelesítés a B2B-együttműködés felhasználók visszaállítása
Jelenleg a rendszergazda megkövetelhetik B2B együttműködés koncepció be újra csak a következő PowerShell-parancsmagok használata:

1. Csatlakozás az Azure AD szolgáltatáshoz

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. A koncepció módszereket minden felhasználó beolvasása

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Például:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Állítsa alaphelyzetbe az MFA módszer egy adott felhasználó a B2B együttműködés felhasználót proof-up módszerek újra beállítani. Példa:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Miért azt végre MFA, az erőforrás bérlős?

A jelenlegi kiadásban MFA neve mindig a erőforrás bérlős okait, kiszámíthatóságot nyújtanak. Például tegyük fel, a Contoso-felhasználó (Éva) felkérik, hogy a Fabrikam, és a Fabrikam engedélyezte a többtényezős hitelesítés B2B-felhasználók számára.

Ha Contoso többtényezős hitelesítési szabályzat az App1 számítógépen, de nem App2 engedélyezve van, majd ha megnézzük a Contoso MFA-jogcímet a jogkivonatban, előfordulhat, hogy láthatjuk, a következő problémaleírást:

* Day 1: Egy felhasználó van az MFA a Contoso szervezetben, és az App1, akkor nincs további MFA fér hozzá a Fabrikam kijelölt alkalmazottai üzenet jelenik meg.

* 2. napon: A felhasználó érte el az alkalmazás 2 a Contoso szervezetben, így most már a Fabrikam elérésekor, akkor regisztrálnia kell a multi-factor Authentication van.

Ez a folyamat zavaró lehet, és dobja el a bejelentkezési befejezések vezethet.

Ezenkívül akkor is, ha a Contoso MFA lehetőséggel rendelkezik, nincs mindig a helyzet a Fabrikam szeretne megbízható Contoso többtényezős hitelesítési szabályzat.

Végül erőforrás bérlőt többtényezős hitelesítés is használható az msa-k és közösségi azonosítóknak és partneri szervezetek számára, amelyek nem rendelkeznek a többtényezős hitelesítés beállítása az.

Ezért a B2B-felhasználók a multi-factor Authentication a javaslat, hogy mindig szükség van az MFA a meghívó bérlőben. Ez a követelmény bizonyos esetekben dupla MFA vezethet, de fér hozzá a meghívó bérlőben, amikor a végfelhasználók élmény kiszámítható: A meghívó bérlőben fel a multi-factor Authentication kell regisztrálni.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Eszköz-, hely- és kockázatalapú feltételes hozzáférés B2B-felhasználók

Ha a Contoso eszközalapú feltételes hozzáférési szabályzatok a vállalati adatok számára engedélyezi, eszközök, amelyek nem a Contoso által felügyelt és nem felel meg a Contoso szabályzatok megakadályozta a hozzáférés.

Ha a rendszer a B2B-felhasználó eszközt nem felügyeli a Contoso, a fiókpartner-szervezetek a B2B-felhasználók a hozzáférés le van tiltva a bármilyen környezetben ezek a házirendek érvényben vannak. Contoso azonban a eszközalapú feltételes hozzáférési szabályzatból kizárandó konkrét partner felhasználókat tartalmazó kizárási listákat hozhat létre.

#### <a name="location-based-conditional-access-for-b2b"></a>Helyalapú feltételes hozzáférés B2B-hez

Helyalapú feltételes hozzáférési szabályzatok kényszeríthető B2B-felhasználók számára, ha a meghívó szervezetet létre tudja hozni egy megbízható IP-címtartományt, amely meghatározza a fiókpartner-szervezetek.

#### <a name="risk-based-conditional-access-for-b2b"></a>Kockázatalapú feltételes hozzáférés B2B-hez

Jelenleg kockázatalapú bejelentkezési szabályzatok nem alkalmazható a B2B-felhasználók számára, mert a kockázat kiértékelésekor történik a B2B-felhasználó otthoni szervezetben.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködés licencelése](licensing-guidance.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
