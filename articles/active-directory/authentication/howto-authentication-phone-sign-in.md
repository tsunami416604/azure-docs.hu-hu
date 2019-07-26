---
title: Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással (előzetes verzió) – Azure Active Directory
description: Bejelentkezés az Azure AD-be a Microsoft Authenticator alkalmazással a jelszó használata nélkül (nyilvános előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357133"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Jelszó nélküli telefonos bejelentkezés a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió)

A Microsoft Authenticator alkalmazás jelszó használata nélkül is bejelentkezhet bármely Azure AD-fiókba. A [vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)technológiához hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus vagy PIN-kódot használ.

![Böngészőbeli bejelentkezés – példa arra, hogy a felhasználó jóváhagyja a bejelentkezést](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Ahelyett, hogy a rendszer jelszót kér a jelszó beírása után, a Microsoft Authenticator alkalmazásban engedélyező telefonos bejelentkezést kérő személy egy üzenetet fog látni, amely arra figyelmezteti, hogy egy számot koppintanak az alkalmazásban. Az alkalmazásban a felhasználónak meg kell egyeznie a számmal, válassza a jóváhagyás lehetőséget, majd adja meg a PIN-kódját vagy biometrikus adatait, majd a hitelesítés befejeződik.

## <a name="enable-my-users"></a>Felhasználók engedélyezése

### <a name="tenant-prerequisites"></a>Bérlői előfeltételek

* Azure Active Directory
* Az Azure multi-Factor Authentication szolgáltatáshoz engedélyezett végfelhasználók
* A felhasználók regisztrálhatják eszközeiket

### <a name="steps-to-enable"></a>Az engedélyezés lépései

Kövesse a jelszó [nélküli bejelentkezés engedélyezése az Azure ad](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods)-ban című cikk lépéseit a jelszó nélküli hitelesítési módszerek engedélyezéséhez a címtárban.

> [!NOTE]
> Ha korábban már engedélyezte ezt a szolgáltatást a bérlőhöz egy PowerShell-parancsfájl használatával, a felhasználók és csoportok új szabályzatának beállítása felülírja a meglévő, bérlőre kiterjedő házirendet. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Hogyan engedélyezhető a végfelhasználók számára a telefonos bejelentkezés?

A nyilvános előzetes verzióban nem lehet kényszeríteni a felhasználókat arra, hogy új hitelesítő adatokat hozzanak létre vagy használjanak. A végfelhasználók csak akkor jelentkeznek a jelszó nélküli bejelentkezéssel, ha a rendszergazda engedélyezte a bérlőt, és a felhasználó frissítette a Microsoft Authenticator alkalmazást a telefonos bejelentkezés engedélyezéséhez.

> [!NOTE]
> Ez a képesség a 2017-as március óta az alkalmazásban van, így ha a házirend engedélyezve van a bérlő számára, akkor a felhasználók azonnal találkozhatnak ezzel a folyamattal. Vegye figyelembe a módosításokat, és készítse elő a felhasználókat.
>

1. Regisztrálás az Azure multi-Factor Authentication szolgáltatásban
1. Az Microsoft Authenticator legújabb verziója iOS 8,0 vagy újabb, vagy Android 6,0 vagy újabb rendszert futtató eszközökön.
1. Munkahelyi vagy iskolai fiók, amely leküldéses értesítésekkel bővült az alkalmazáshoz. A végfelhasználói dokumentáció a következő címen érhető [https://aka.ms/authappstart](https://aka.ms/authappstart)el:.

Miután a felhasználó rendelkezik a Microsoft Authenticator alkalmazásban beállított leküldéses értesítésekkel rendelkező MFA-fiókkal, a telefonos bejelentkezési regisztráció befejezéséhez [nem](../user-help/microsoft-authenticator-app-phone-signin-faq.md) a jelszavához tartozó cikk lépéseit követheti.

## <a name="known-issues"></a>Ismert problémák

### <a name="ad-fs-integration"></a>AD FS integráció

Ha egy felhasználó engedélyezte a Microsoft Authenticator jelszóval nem rendelkező hitelesítő adatokat, akkor a felhasználó hitelesítése mindig az alapértelmezett, ha értesítést szeretne küldeni jóváhagyásra. Ez a logika megakadályozza, hogy a hibrid bérlő felhasználói ne legyenek átirányítva az ADFS-be a bejelentkezési ellenőrzéshez anélkül, hogy a felhasználó további lépést kellene tennie a jelszó használata helyett. Ez a folyamat a helyszíni feltételes hozzáférési szabályzatokat és az átmenő hitelesítési folyamatokat is megkerüli. Ennek a folyamatnak a kivétele, ha meg van adva egy login_hint, a rendszer automatikusan továbbítja a felhasználót a AD FSba, és megkerüli a jelszóval nem rendelkező hitelesítő adatok használatára vonatkozó lehetőséget.

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

Azok a végfelhasználók, akik az MFA számára engedélyezve vannak a szervezet helyszíni Azure MFA-kiszolgálóján keresztül, továbbra is létrehozhatnak és használhatnak egyszeri, jelszó nélküli telefonos bejelentkezési hitelesítő adatokat. Ha a felhasználó a hitelesítő adatokkal rendelkező Microsoft Authenticator több telepítésének (5 +) frissítését kísérli meg, ez a változás hibát okozhat.  

### <a name="device-registration"></a>Eszközregisztráció

Az új, erős hitelesítő adatok létrehozásának egyik előfeltétele, hogy az eszköz, ahol a hely található, regisztrálva van az Azure AD-bérlőn belül az egyes felhasználók számára. Az eszköz regisztrációs korlátozásai miatt egy eszköz csak egyetlen bérlőben regisztrálható. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető a telefonos bejelentkezéshez.

## <a name="next-steps"></a>További lépések

[Mi az a jelszó?](concept-authentication-passwordless.md)

[Tudnivalók az eszközök regisztrálásáról](../devices/overview.md#getting-devices-in-azure-ad)

[Az Azure multi-Factor Authentication ismertetése](../authentication/howto-mfa-getstarted.md)
