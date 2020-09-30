---
title: Jelszó nélküli bejelentkezés a Microsoft Authenticator app-Azure Active Directory
description: Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be az Microsoft Authenticator alkalmazással (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4792e73f6326bb9ac67ce3aabe10b8314bb826
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568206"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió)

A Microsoft Authenticator alkalmazás jelszó használata nélkül is bejelentkezhet bármely Azure AD-fiókba. A [vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)technológiához hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus vagy PIN-kódot használ. Ez a hitelesítési módszer bármilyen eszköz platformon használható, beleértve a mobilt, valamint a Microsoft hitelesítési könyvtáraival integrált bármely alkalmazást vagy webhelyet.

![Böngészőbeli bejelentkezés – példa arra, hogy a felhasználó jóváhagyja a bejelentkezést](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

A felhasználónevek beírása után a jelszó kérése helyett a Microsoft Authenticator alkalmazásban engedélyező telefonos bejelentkezést kérő személy egy üzenetet lát, amely arra kéri őket, hogy koppintson egy számra az alkalmazásban. Az alkalmazásban a bejelentkezési folyamat befejezéséhez a felhasználónak meg kell egyeznie a számmal, válassza a **jóváhagyás**lehetőséget, majd adja meg a PIN-kódját vagy a biometrikus értéket.

## <a name="prerequisites"></a>Előfeltételek

Ha jelszó nélküli telefonos bejelentkezést szeretne használni a Microsoft Authenticator alkalmazással, a következő előfeltételek teljesülése szükséges:

- Azure Multi-Factor Authentication, a leküldéses értesítések engedélyezése ellenőrzési módszerként.
- Az Microsoft Authenticator legújabb verziója iOS 8,0 vagy újabb, vagy Android 6,0 vagy újabb rendszert futtató eszközökön.

> [!NOTE]
> Ha az Azure AD PowerShell használatával engedélyezte Microsoft Authenticator az alkalmazás jelszavas nélküli bejelentkezési előzetes verzióját, akkor az a teljes címtárhoz engedélyezve volt. Ha engedélyezi ezt az új metódust, az supercedes a PowerShell-házirendet. Azt javasoljuk, hogy a bérlő összes felhasználója számára engedélyezze az új *hitelesítési módszerek* menüt, ellenkező esetben az új szabályzatban nem szereplő felhasználók nem tudnak többé jelszó nélkül bejelentkezni.

## <a name="enable-passwordless-authentication-methods"></a>Jelszóval nem rendelkező hitelesítési módszerek engedélyezése

A jelszó nélküli hitelesítés az Azure AD-ben való használatához először engedélyezze a kombinált regisztrációs élményt, majd engedélyezze a felhasználók számára a kevesebb jelszót.

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a kombinált regisztráció funkcióra támaszkodnak. Ha szeretné, hogy a felhasználók maguk végezzék el a közös regisztrációt, kövesse a következő lépéseket a [kombinált biztonsági információk regisztrálásának engedélyezéséhez](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Jelszó nélküli telefonos bejelentkezési hitelesítési módszerek engedélyezése

Az Azure AD segítségével kiválaszthatja, hogy mely hitelesítési módszereket lehet használni a bejelentkezési folyamat során. A felhasználók ezután regisztrálhatják a használni kívánt metódusokat.

A jelszó nélküli telefonos bejelentkezés hitelesítési módszerének engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* fiókkal.
1. Keresse meg és válassza ki a *Azure Active Directory*, majd keresse meg a **biztonsági**  >  **hitelesítési módszerek**  >  **hitelesítési módszer házirend (előzetes verzió) lehetőséget.**
1. A **jelszó nélküli telefonos bejelentkezés**területen válassza a következő lehetőségeket:
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. Az új szabályzat alkalmazásához válassza a **Mentés**lehetőséget.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás felhasználói regisztrációja és kezelése

Az Azure AD-ben használható, jelszóval nem rendelkező hitelesítési módszerrel a felhasználóknak most regisztrálniuk kell magukat a jelszóval nem rendelkező hitelesítési módszerhez a következő lépések végrehajtásával:

1. Lépjen a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) lapra.
1. Jelentkezzen be, majd adja hozzá a hitelesítő alkalmazást a **method > hitelesítő alkalmazás hozzáadása**lehetőség kiválasztásával, majd **adja hozzá**a elemet.
1. Az eszközön a Microsoft Authenticator alkalmazás telepítéséhez és konfigurálásához kövesse az utasításokat.
1. A hitelesítő konfigurációjának befejezéséhez válassza a **kész** lehetőséget.
1. **Microsoft Authenticator** alkalmazásban válassza a **telefonos bejelentkezés engedélyezése** lehetőséget a regisztrált fiók legördülő menüjében.
1. Kövesse az alkalmazás utasításait a fiók jelszavas telefonos bejelentkezéshez való regisztrálásának befejezéséhez.

A szervezetek elérhetik a felhasználókat [, hogy bejelentkezzenek a telefonjára, nem](../user-help/user-help-auth-app-sign-in.md) pedig az Microsoft Authenticator alkalmazás konfigurálásához és a telefonos bejelentkezés engedélyezéséhez szükséges további segítségért.

> [!NOTE]
> Azok a felhasználók, akik nem engedélyezték a telefonos bejelentkezést, a továbbiakban nem tudják engedélyezni a Microsoft Authenticator alkalmazáson belül.  

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

A felhasználó megkezdheti a jelszó nélküli bejelentkezés használatát, ha a rendszergazda engedélyezte a bérlőt, és a felhasználó frissítette a Microsoft Authenticator alkalmazást a telefonos bejelentkezés engedélyezéséhez.

A telefonos bejelentkezés használatának megkezdéséhez, miután begépelt egy felhasználónevet a bejelentkezési oldalon, és kiválasztja a **Next (tovább**) lehetőséget, előfordulhat, hogy a felhasználóknak ki kell választaniuk a **Bejelentkezés egyéb módszereit**, majd **jóvá kell hagynia a kérelmet a Microsoft Authenticator alkalmazásban**. A felhasználók ezután egy számmal jelennek meg, és a rendszer a Microsoft Authenticator alkalmazásban kéri, hogy a jelszó használata helyett a hitelesítéshez válassza ki a megfelelő számot. Ha a felhasználók jelszó nélküli telefonos bejelentkezést használtak, a rendszer ismét azt kéri, hogy a válasszon másik módszert.

![Böngészőbeli bejelentkezés – példa a Microsoft Authenticator alkalmazás használatával](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Ismert problémák

Az aktuális előzetes verzióban a következő ismert problémák léteznek.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nem jelenik meg a jelszó nélküli telefonos bejelentkezés beállítása

Ha a felhasználó nem válaszol, és a rendszer megkísérli bejelentkezni a jelszó nélküli telefonos bejelentkezési ellenőrzésre, akkor a felhasználó csak a jelszó megadására szolgáló lehetőséget láthatja. Nyissa meg a Microsoft Authenticator, és válaszoljon minden értesítési kérésre, hogy továbbra is használhassa a jelszó nélküli telefonos bejelentkezést.

### <a name="federated-accounts"></a>Összevont fiókok

Ha egy felhasználó engedélyezte a jelszó nélküli hitelesítő adatokat, az Azure AD-bejelentkezések login_hint használatával leállnak, hogy felgyorsítsa a felhasználót egy összevont bejelentkezési helyre. Ez a logika megakadályozza, hogy a hibrid bérlő felhasználói ne kelljen AD FS a bejelentkezési ellenőrzésre, anélkül, hogy a felhasználó további lépést kellene tennie, hogy rákattint a jelszó használata helyett.

### <a name="azure-mfa-server"></a>Azure MFA-kiszolgáló

Azok a végfelhasználók, akik az MFA számára engedélyezve vannak a szervezet helyszíni Azure MFA-kiszolgálóján keresztül, továbbra is létrehozhatnak és használhatnak egyszeri, jelszó nélküli telefonos bejelentkezési hitelesítő adatokat. Ha a felhasználó a hitelesítő adatokkal rendelkező Microsoft Authenticator több telepítésének (5 +) frissítését kísérli meg, ez a változás hibát okozhat.  

### <a name="device-registration"></a>Eszközregisztráció

Az új, erős hitelesítő adatok létrehozásának egyik előfeltétele, hogy az eszközön, ahol a Microsoft Authenticator alkalmazást telepítették, az Azure AD-bérlőn belül is regisztrálni kell egy adott felhasználóra. Az eszközök aktuális regisztrációs korlátozásai miatt az eszközök csak egyetlen bérlőben regisztrálhatók. Ez a korlát azt jelenti, hogy a Microsoft Authenticator alkalmazásban csak egy munkahelyi vagy iskolai fiók engedélyezhető a telefonos bejelentkezéshez.

> [!NOTE]
> Az eszköz regisztrálása nem ugyanaz, mint az Eszközkezelő vagy a "MDM". A szolgáltatás csak az eszköz AZONOSÍTÓját és egy felhasználói azonosítót társít az Azure AD-címtárban.  

## <a name="next-steps"></a>Következő lépések

Az Azure AD-hitelesítéssel és a jelszóval nem rendelkező módszerekkel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Ismerje meg, hogyan működik a jelszavas hitelesítés](concept-authentication-passwordless.md)
* [Tudnivalók az eszközök regisztrálásáról](../devices/overview.md#getting-devices-in-azure-ad)
* [További tudnivalók az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
