---
title: Jelszó nélküli biztonsági kulcs bejelentkezés (előzetes verzió) – Azure Active Directory
description: Jelszó nélküli biztonsági kulcs bejelentkezés engedélyezése az Azure AD-be a FIDO2 biztonsági kulcsokkal (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8574fcdff12e61f7039174ed6297d0558a66dc4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653938"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése (előzetes verzió)

A nagyvállalatok számára, amelyek ma jelszavakat használnak, és megosztott számítógépes környezettel rendelkeznek, a biztonsági kulcsok zökkenőmentes módot biztosítanak a dolgozók számára a hitelesítéshez felhasználónév vagy jelszó megadása nélkül. A biztonsági kulcsok nagyobb termelékenységet biztosítanak a dolgozók számára, és nagyobb biztonságot nyújtanak.

Ez a dokumentum a biztonsági kulcsalapú jelszó nélküli hitelesítés engedélyezésére összpontosít. A cikk végén fido2 biztonsági kulccsal jelentkezhet be webalapú alkalmazásokba az Azure AD-fiókkal.

|     |
| --- |
| A FIDO2 biztonsági kulcsok az Azure Active Directory nyilvános előzetes verziójú szolgáltatásai. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

## <a name="requirements"></a>Követelmények

- [Azure többtényezős hitelesítés](howto-mfa-getstarted.md)
- [Kombinált biztonsági adatok regisztrációs előnézete](concept-registration-mfa-sspr-combined.md)
- Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)
- A WebAuthN használatához windows 10-es vagy újabb verzió szükséges**

A webalkalmazásokba és szolgáltatásokba való bejelentkezéshez biztonsági kulcsokat kell használnia, olyan böngészővel kell rendelkeznie, amely támogatja a WebAuthN protokollt. Ezek közé tartozik a Microsoft Edge, a Chrome, a Firefox és a Safari.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése előnézetre

Az Azure AD-hez csatlakozott eszközök, amelyekkel a kísérleti kell futtatni a Windows 10-es vagy újabb verzió. A legjobb élmény a Windows 10 1903-as vagy újabb verziójában érhető el.

A hibrid Azure AD-hez csatlakozó eszközöknek windows 10 Insider Build 18945 vagy újabb rendszert kell futtatniuk.

## <a name="enable-passwordless-authentication-method"></a>Jelszó nélküli hitelesítési módszer engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztrációs élmény engedélyezése

A jelszó nélküli hitelesítési módszerek regisztrációs funkciói a kombinált regisztrációs előzetes verzión alapulnak. Kövesse a cikkben a [Kombinált biztonsági adatok regisztrációjának engedélyezése (előzetes verzió)](howto-registration-mfa-sspr-combined.md)című cikkben leírt lépéseket a kombinált regisztrációs előnézet engedélyezéséhez.

### <a name="enable-fido2-security-key-method"></a>FiDO2 biztonságikulcs-módszer engedélyezése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **hitelesítési módszereit** > **hitelesítési metódusházirend (előzetes verzió) .**
1. A **FIDO2 biztonsági kulcs**módszerével válassza a következő beállításokat:
   1. **Engedélyezés** - Igen vagy Nem
   1. **Cél** – Minden felhasználó vagy Felhasználók kiválasztása
1. **Mentse** a konfigurációt.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>A FIDO2 biztonsági kulcsok felhasználói regisztrációja és kezelése

1. Tallózás a tallózáshoz. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Jelentkezzen be, ha még nem.
1. Kattintson **a Biztonsági adatok gombra.**
   1. Ha a felhasználó már rendelkezik legalább egy regisztrált Azure többtényezős hitelesítési módszerrel, azonnal regisztrálhat egy FIDO2 biztonsági kulcsot.
   1. Ha nem rendelkeznek legalább egy regisztrált Azure többtényezős hitelesítési módszerrel, hozzá kell adniuk egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot a **Módszer hozzáadása** gombra kattintva, és válassza a Biztonsági **kulcs lehetőséget.**
1. Válassza **az USB-eszközt** vagy **az NFC-eszközt**.
1. Készítse elő a kulcsot, és válassza a **Tovább gombot.**
1. Megjelenik egy mező, amely arra kéri a felhasználót, hogy hozzon létre/adjon meg egy PIN-kódot a biztonsági kulcshoz, majd végezze el a kulcshoz szükséges kézmozdulatot, legyen az biometrikus vagy érintéses.
1. A felhasználó visszatér a kombinált regisztrációs élményhez, és felkéri, hogy adjon meg egy értelmes nevet a kulcsnak, hogy a felhasználó azonosíthassa, hogy melyik, ha több. Kattintson a **Tovább** gombra.
1. A folyamat befejezéséhez kattintson a **Kész** gombra.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

Az alábbi példában a felhasználó már kiépített a FIDO2 biztonsági kulcsot. A felhasználó dönthet úgy, hogy a FIDO2 biztonsági kulccsal jelentkezik be az interneten a Windows 10 1809-es vagy újabb verziójának támogatott böngészőjében.

![Bejelentkezés biztonsági kulcsa Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha a szolgáltatás megtekintése közben szeretné megosztani a visszajelzéseket, vagy problémákba ütközik, az alábbi lépésekkel megoszthatja a Windows Visszajelzési központ alkalmazáson keresztül:

1. Indítsa el **a Visszajelzési központot,** és győződjön meg arról, hogy be van jelentkezve.
1. Visszajelzés küldése a következő kategorizálás alatt:
   - Kategória: Biztonság és adatvédelem
   - Alkategória: FIDO
1. A naplók rögzítéséhez használja a **probléma újbóli létrehozását**

## <a name="known-issues"></a>Ismert problémák

### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítése

A biztonsági kulcsok rendszergazdai kiépítése és kiépítésének kiirtása nem érhető el a nyilvános előzetes verzióban.

### <a name="upn-changes"></a>UPN-módosítások

Dolgozunk egy olyan funkció támogatásán, amely lehetővé teszi az UPN-módosítást a hibrid Azure AD-hez és az Azure AD-hez csatlakozott eszközökön. Ha egy felhasználó felhasználói felülete megváltozik, a FIDO2 biztonsági kulcsok már nem módosíthatók a módosítás figyelembevétele érdekében. A megoldás az eszköz alaphelyzetbe állítása, és a felhasználónak újra regisztrálnia kell.

## <a name="next-steps"></a>További lépések

[FIDO2 biztonsági kulcs Windows 10 bejelentkezés](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-hitelesítés engedélyezése helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközregisztrációról](../devices/overview.md)

[További információ az Azure többtényezős hitelesítéséről](../authentication/howto-mfa-getstarted.md)
