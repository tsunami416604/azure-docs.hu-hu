---
title: Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással – Azure Active Directory
description: Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be a Microsoft Authenticator alkalmazással (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3412938cfc2ad3fbec293fd33f64e114e14e6f7e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450972"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió)

A Microsoft Authenticator alkalmazás segítségével jelszó használata nélkül jelentkezhet be bármely Azure AD-fiókba. A [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)technológiájához hasonlóan a Microsoft Authenticator is kulcsalapú hitelesítést használ az eszközhöz kapcsolt és biometrikus vagy PIN-kódot használó felhasználói hitelesítő adatok engedélyezéséhez. Ez a hitelesítési módszer bármely eszközplatformon használható, beleértve a mobilt is, valamint minden olyan alkalmazásban vagy webhelyen, amely integrálható a Microsoft hitelesítési kódtáraival. 

![Példa böngészőbejelentkezésre, amely a felhasználót a bejelentkezés jóváhagyására kéri](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Ahelyett, hogy a felhasználónév megadása után a jelszóra vonatkozó kérdést látná, a Microsoft Authenticator alkalmazásból telefonos bejelentkezést engedélyező személy egy üzenetet fog látni, amely arra kéri őket, hogy koppintson egy számra az alkalmazásban. Az alkalmazásban a felhasználónak meg kell egyeznie a számmal, válassza a Jóváhagyás lehetőséget, majd meg kell adnia a PIN-kódját vagy biometrikus adatait, majd a hitelesítés befejeződik.

> [!NOTE]
> Ez a funkció 2017 márciusa óta a Microsoft Authenticator alkalmazásban található, így előfordulhat, hogy ha a szabályzat engedélyezve van egy címtárhoz, a felhasználók azonnal találkozhatnak ezzel a folyamattal, és hibaüzenetet láthatnak, ha a szabályzat nem engedélyezte őket. Vegye figyelembe és készítse fel a felhasználókat erre a változásra.

## <a name="prerequisites"></a>Előfeltételek

- Azure többtényezős hitelesítés, leküldéses értesítésekkel, ellenőrzési módszerként 
- A Microsoft Authenticator legújabb verziója iOS 8.0 vagy újabb vagy androidos 6.0 vagy újabb rendszerű eszközökön.

> [!NOTE]
> Ha engedélyezte az előző Microsoft Authenticator alkalmazás jelszó nélküli bejelentkezési előzetes verzióját az Azure AD PowerShell használatával, akkor a teljes címtárban engedélyezve volt. Ha engedélyezi ezzel az új módszerrel, akkor felülfogja a PowerShell-szabályzatot. Azt javasoljuk, hogy engedélyezve az összes felhasználó a bérlőben az új hitelesítési módszerek, különben a felhasználók nem az új házirendben már nem tud nak bejelentkezni passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Jelszó nélküli hitelesítési módszerek engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztrációs élmény engedélyezése

A jelszó nélküli hitelesítési módszerek regisztrációs szolgáltatásai a kombinált regisztrációs szolgáltatáson alapulnak. Kövesse a cikkben a [Kombinált biztonsági adatok regisztrálásának engedélyezése](howto-registration-mfa-sspr-combined.md)című cikkben a kombinált regisztráció engedélyezéséhez.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Jelszó nélküli telefonbejelentkezési hitelesítési módszerek engedélyezése

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. Keresse meg és válassza ki az *Azure Active Directoryt*. **Válassza** > a Biztonsági**hitelesítés módszereinek** > **hitelesítési metódusházirend (előzetes verzió) lehetőséget.**
1. A **Jelszó nélküli telefonbejelentkezés csoportban**adja meg az alábbi lehetőségeket:
   1. **Engedélyezés** - Igen vagy Nem
   1. **Cél** – Minden felhasználó vagy Felhasználók kiválasztása
1. **Mentés** az új házirend beállításához

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás felhasználói regisztrációja és kezelése

1. Tallózás a[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Bejelentkezés, ha még nem tette meg
1. Hitelesítő alkalmazás hozzáadása a **Módszer hozzáadása**gombra, a **Hitelesítő alkalmazás**kiválasztására, majd a **Hozzáadás** parancsra kattintva
1. Kövesse az utasításokat a Microsoft Authenticator alkalmazás telepítéséhez és konfigurálásához az eszközön
1. Kattintson **a Kész** gombra a Hitelesítő MFA-alkalmazás beállítási folyamatának befejezéséhez. 
1. A **Microsoft Hitelesítő**ben válassza a Fiók legördülő **menüTelefonos bejelentkezés engedélyezése** parancsát.
1. Kövesse az alkalmazás utasításait a jelszó nélküli telefonos bejelentkezés regisztrálásának befejezéséhez. 

A szervezetek a felhasználókat a [telefonnal való bejelentkezés, a Microsoft](../user-help/microsoft-authenticator-app-phone-signin-faq.md) Authenticator alkalmazásban való további beállításhoz és a telefonos bejelentkezés engedélyezéséhez való bejelentkezéshez a felhasználóikat irányíthatják. A beállítások alkalmazásához előfordulhat, hogy ki kell jelentkeznie, és újra ki kell jelentkeznie a bérlőbe. 

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

A nyilvános előzetes verzióban nincs mód arra, hogy a felhasználók létrehozzák vagy használják ezt az új hitelesítő adatokat. A felhasználó csak akkor találkozik jelszó nélküli bejelentkezéssel, ha a rendszergazda engedélyezte a **bérlőt, és** a felhasználó frissítette a Microsoft Authenticator alkalmazást a telefonos bejelentkezés engedélyezéséhez.

Miután beírta a felhasználónevét a weben, és a **Tovább**lehetőséget választotta, a felhasználók egy számot mutatnak be, és a Microsoft Authenticator alkalmazásban a rendszer felszólítja őket, hogy a jelszó használata helyett válasszák ki a hitelesítéshez szükséges számot. 

![Példa böngészőbejelentkezésre a Microsoft Authenticator alkalmazással](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>A felhasználót nem engedélyezi a házirend, de még mindig rendelkezik jelszónélküli bejelentkezési módszerrel a Microsoft Hitelesítőben

Lehetséges, hogy a felhasználó egy bizonyos ponton létrehozott egy jelszó nélküli bejelentkezési hitelesítő adatokat a jelenlegi Microsoft Hitelesítő alkalmazásban vagy egy korábbi eszközön. Miután a rendszergazda engedélyezi a hitelesítési módszer házirend jelszó nélküli telefon bejelentkezés, minden felhasználó a hitelesítő regisztrációval, megkezdi az új bejelentkezési kérdés, függetlenül attól, hogy engedélyezték-e a házirend használatát, vagy sem. Ha a felhasználó nem használhatja a hitelesítő adatokat a házirend szerint, hibaüzenetjelenik meg a hitelesítési folyamat befejezése után. 

A rendszergazda engedélyezheti, hogy a felhasználó jelszó nélküli bejelentkezést használjon, vagy a felhasználónak el kell távolítania a metódust. Ha a felhasználó már nem rendelkezik a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) regisztrált eszközzel, akkor megy, és távolítsa el azt. Ha még mindig a Hitelesítő t használja az MFA-hoz, a Microsoft Hitelesítőből választhatja a **Telefon-bejelentkezés letiltása** lehetőséget.  

### <a name="ad-fs-integration"></a>AD FS integráció

Ha egy felhasználó engedélyezte a Microsoft Hitelesítő jelszó nélküli hitelesítő adatait, az adott felhasználó hitelesítése alapértelmezés szerint alapértelmezés szerint jóváhagyást kérő értesítést küld. Ez a logika megakadályozza, hogy a hibrid bérlő felhasználói az ADFS-hez irányítsák a bejelentkezési ellenőrzéshez anélkül, hogy a felhasználó további lépést tett volna a "Jelszó használata helyette" gombra kattintva. Ez a folyamat a helyszíni feltételes hozzáférési házirendeket is megkerüli, és átmenő hitelesítési folyamatok. 

Ha a felhasználó náluk van egy jelszó nélküli, jelszó nélküli bejelentkezési ellenőrzés, és megpróbál újra bejelentkezni, előfordulhat, hogy a felhasználó az ADFS-hez kerül, és jelszót ad meg.  

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

Azok a végfelhasználók, akik a szervezet helyszíni Azure MFA-kiszolgálóján keresztül engedélyezve vannak az MFA-hitelesítéshez, továbbra is létrehozhatnak és használhatnak egyetlen jelszó nélküli bejelentkezési hitelesítő adatot. Ha a felhasználó a Microsoft Authenticator több (5+) telepítését próbálja frissíteni a hitelesítő adatokkal, ez a módosítás hibát okozhat.  

### <a name="device-registration"></a>Eszközregisztráció

Az új erős hitelesítő adatok létrehozásának egyik előfeltétele az, hogy az eszköz, ahol a Microsoft Authenticator alkalmazás telepítve van, szintén regisztrálva kell lennie az Azure AD-bérlőn belül egy adott felhasználó számára. Az aktuális eszközregisztrációs korlátozások miatt az eszköz csak egyetlen bérlőben regisztrálható. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető a telefonos bejelentkezéshez.

### <a name="intune-mobile-application-management"></a>Intune mobilalkalmazás-kezelés 

Azok a végfelhasználók, akikre olyan házirend vonatkozik, amely mobilalkalmazás-kezelést (MAM) igényel, nem regisztrálhatják a jelszó nélküli hitelesítő adatokat a Microsoft Authenticator alkalmazásban. 

> [!NOTE]
> Az eszközregisztráció nem egyezik meg az eszközkezeléssel vagy az "MDM"-el. Csak egy eszközazonosítót és egy felhasználói azonosítót társít együtt az Azure AD-címtárban.  

## <a name="next-steps"></a>További lépések

[Mi a jelszó nélküli hitelesítés?](concept-authentication-passwordless.md)

[További információ az eszközregisztrációról](../devices/overview.md#getting-devices-in-azure-ad)

[További információ az Azure többtényezős hitelesítéséről](../authentication/howto-mfa-getstarted.md)
