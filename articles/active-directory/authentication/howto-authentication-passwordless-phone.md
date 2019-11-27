---
title: Jelszó nélküli bejelentkezés a Microsoft Authenticator app-Azure Active Directory
description: Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be az Microsoft Authenticator alkalmazással (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a76aa0ca7cbda3f2db564c220ba12fec60f60509
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381866"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió)

A Microsoft Authenticator alkalmazás jelszó használata nélkül is bejelentkezhet bármely Azure AD-fiókba. A [vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)technológiához hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus vagy PIN-kódot használ. Ez a hitelesítési módszer bármilyen eszköz platformon használható, beleértve a mobilt, valamint a Microsoft hitelesítési könyvtáraival integrált bármely alkalmazást vagy webhelyet. 

![Böngészőbeli bejelentkezés – példa arra, hogy a felhasználó jóváhagyja a bejelentkezést](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

A felhasználónevek beírása után a jelszó megadását nem kell látnia, ha a Microsoft Authenticator alkalmazásban engedélyezte a telefonos bejelentkezést, egy üzenet jelenik meg, amely azt jelzi, hogy egy számra koppintanak az alkalmazásban. Az alkalmazásban a felhasználónak meg kell egyeznie a számmal, válassza a jóváhagyás lehetőséget, majd adja meg a PIN-kódját vagy biometrikus adatait, majd a hitelesítés befejeződik.

> [!NOTE]
> Ez a képesség a Microsoft Authenticator-alkalmazásban, a 2017-as márciusa óta, így előfordulhat, hogy ha a házirend engedélyezve van egy címtárban, a felhasználók azonnal találkozhatnak ezzel a folyamattal, és hibaüzenet jelenik meg, ha a szabályzat nem engedélyezte őket. Vegye figyelembe a módosításokat, és készítse elő a felhasználókat.

## <a name="prerequisites"></a>Előfeltételek

- Azure Multi-Factor Authentication, ellenőrzési módszerként engedélyezett leküldéses értesítésekkel 
- Az Microsoft Authenticator legújabb verziója iOS 8,0 vagy újabb, vagy Android 6,0 vagy újabb rendszert futtató eszközökön.

> [!NOTE]
> Ha az Azure AD PowerShell használatával engedélyezte az előző Microsoft Authenticator alkalmazás jelszavas nélküli bejelentkezési előnézetét, akkor az a teljes címtárhoz engedélyezve volt. Ha engedélyezi ezt az új metódust, a rendszer előzte meg a PowerShell-házirendet. Javasoljuk, hogy a bérlő összes felhasználója számára engedélyezze az új hitelesítési módszereket, ellenkező esetben az új szabályzatban nem szereplő felhasználók többé nem fognak tudni bejelentkezni a passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Jelszóval nem rendelkező hitelesítési módszerek engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a közös regisztráció előzetes verziójára támaszkodnak. A kombinált regisztráció előzetes verziójának engedélyezéséhez kövesse a [kombinált biztonsági információk regisztrálásának engedélyezése (előzetes verzió)](howto-registration-mfa-sspr-combined.md)című cikkben ismertetett lépéseket.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Jelszó nélküli telefonos bejelentkezési hitelesítési módszerek engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Keresse meg és válassza ki a *Azure Active Directory*. Válassza a **biztonsági** > **hitelesítési módszerek** > a **hitelesítési módszer házirendje (előzetes verzió) lehetőséget.**
1. A **jelszó nélküli telefonos bejelentkezés**területen válassza a következő beállításokat
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. **Mentés** az új házirend beállításához

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás felhasználói regisztrációja és kezelése

1. Tallózással keresse meg [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Ha még nem tette meg, jelentkezzen be
1. Adjon hozzá egy hitelesítő alkalmazást a **metódus hozzáadása**, a **hitelesítő alkalmazás**kiválasztása, majd a **Hozzáadás** elemre kattintva.
1. Kövesse a Microsoft Authenticator alkalmazás telepítésére és konfigurálására vonatkozó utasításokat az eszközön
1. Kattintson a **kész** gombra a hitelesítő MFA-alkalmazás telepítési folyamatának befejezéséhez. 
1. A **Microsoft Authenticator**válassza a **telefonos bejelentkezés engedélyezése** lehetőséget a fiók legördülő menüből.
1. A jelszó nélküli telefonos bejelentkezéshez való regisztráció befejezéséhez kövesse az alkalmazás utasításait. 

A szervezetek arra is rámutatnak a felhasználóknak [, hogy bejelentkeznek a telefonnal, nem](../user-help/microsoft-authenticator-app-phone-signin-faq.md) pedig az Microsoft Authenticator alkalmazásban és a telefonos bejelentkezés engedélyezésével kapcsolatos további segítséghez szükséges jelszava.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

A nyilvános előzetes verzióban nem lehet kényszeríteni a felhasználókat arra, hogy új hitelesítő adatokat hozzanak létre vagy használjanak. A felhasználók csak akkor jelentkeznek a jelszó nélküli bejelentkezéssel, ha a rendszergazda engedélyezte a bérlőt **, és** a felhasználó frissítette Microsoft Authenticator alkalmazást a telefonos bejelentkezés engedélyezéséhez.

Miután beírta a felhasználónevet a weben, és kiválasztja a **Next (tovább**) lehetőséget, a felhasználók számot kapnak, és a rendszer megkéri a Microsoft Authenticator alkalmazásban, hogy a jelszó használata helyett a hitelesítéshez megfelelő számot adja meg. 

![Böngészőbeli bejelentkezés – példa a Microsoft Authenticator alkalmazás használatával](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>A felhasználó nincs engedélyezve a házirendben, de még mindig rendelkezik jelszó nélküli telefonos bejelentkezési módszerrel Microsoft Authenticator

Lehetséges, hogy a felhasználó egy bizonyos ponton létrehozott egy jelszó nélküli telefonos bejelentkezési hitelesítő adatot a jelenlegi Microsoft Authenticator alkalmazásában vagy egy korábbi eszközön. Miután a rendszergazda engedélyezte a hitelesítési módszer házirendjét a jelszó nélküli telefonos bejelentkezéshez, a hitelesítő adatokkal rendelkező felhasználók megkezdik az új bejelentkezési kérést, függetlenül attól, hogy engedélyezve vannak-e a szabályzat használatára. Ha a felhasználó nem engedélyezte a hitelesítő adatok használatát a házirend alapján, a hitelesítési folyamat befejezése után hibaüzenet jelenik meg. 

A rendszergazda dönthet úgy, hogy engedélyezi a felhasználó számára a jelszó nélküli telefonos bejelentkezés használatát, vagy a felhasználónak el kell távolítania a metódust. Ha a felhasználó már nem rendelkezik a regisztrált eszközzel, akkor [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) , és távolítsa el azt. Ha továbbra is az MFA hitelesítő adatait használják, akkor a Microsoft Authenticatoron is választhatják a **telefonos bejelentkezés letiltása** lehetőséget.  

### <a name="ad-fs-integration"></a>AD FS integráció

Ha egy felhasználó engedélyezte a Microsoft Authenticator jelszóval nem rendelkező hitelesítő adatokat, akkor a felhasználó hitelesítése mindig az alapértelmezett, ha értesítést szeretne küldeni jóváhagyásra. Ez a logika megakadályozza, hogy a hibrid bérlő felhasználói ne legyenek átirányítva az ADFS-be a bejelentkezési ellenőrzéshez anélkül, hogy a felhasználó további lépést kellene tennie a jelszó használata helyett. Ez a folyamat a helyszíni feltételes hozzáférési szabályzatokat és az átmenő hitelesítési folyamatokat is megkerüli. 

Ha a felhasználó nem válaszol, és a rendszer megkísérli bejelentkezni a jelszó nélküli telefonos bejelentkezési ellenőrzésre, akkor előfordulhat, hogy a felhasználót a rendszer az ADFS-be fogja írni a jelszó megadására.  

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

Azok a végfelhasználók, akik az MFA számára engedélyezve vannak a szervezet helyszíni Azure MFA-kiszolgálóján keresztül, továbbra is létrehozhatnak és használhatnak egyszeri, jelszó nélküli telefonos bejelentkezési hitelesítő adatokat. Ha a felhasználó a hitelesítő adatokkal rendelkező Microsoft Authenticator több telepítésének (5 +) frissítését kísérli meg, ez a változás hibát okozhat.  

### <a name="device-registration"></a>Eszközregisztráció

Az új, erős hitelesítő adatok létrehozásának egyik előfeltétele, hogy az eszközön, ahol a Microsoft Authenticator alkalmazást telepítették, az Azure AD-bérlőn belül is regisztrálni kell egy adott felhasználóra. Az eszközök aktuális regisztrációs korlátozásai miatt az eszközök csak egyetlen bérlőben regisztrálhatók. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető a telefonos bejelentkezéshez.

> [!NOTE]
> Az eszköz regisztrálása nem ugyanaz, mint az Eszközkezelő vagy a "MDM". A szolgáltatás csak az eszköz AZONOSÍTÓját és egy felhasználói azonosítót társít az Azure AD-címtárban.  

## <a name="next-steps"></a>Következő lépések

[Mi az a jelszó?](concept-authentication-passwordless.md)

[Tudnivalók az eszközök regisztrálásáról](../devices/overview.md#getting-devices-in-azure-ad)

[További tudnivalók az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
