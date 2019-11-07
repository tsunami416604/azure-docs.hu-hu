---
title: Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése az Azure AD-ben (előzetes verzió) – Azure Active Directory
description: Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése az Azure AD-be az FIDO2 biztonsági kulcsok használatával (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50af82e79e7ba8b979ab28a1b3f608ec7e41bfb2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603437"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése (előzetes verzió)

Azok a vállalatok, amelyek a jelszavakat ma és megosztott számítógépes környezettel használják, a biztonsági kulcsok zökkenőmentesen biztosítják a feldolgozók számára a Felhasználónév vagy a jelszó beírása nélkül történő hitelesítést. A biztonsági kulcsok nagyobb hatékonyságot biztosítanak a dolgozók számára, és jobb biztonságot nyújtanak.

Ez a dokumentum a biztonsági kulcson alapuló jelszavas hitelesítés engedélyezését összpontosítja. A cikk végén az Azure AD-fiókjával bejelentkezhet a web-alapú alkalmazásokba egy FIDO2 biztonsági kulccsal.

|     |
| --- |
| A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Követelmények

- [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)
- [A kombinált biztonsági információk regisztrációjának előzetes verziója](concept-registration-mfa-sspr-combined.md)
- Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)
- A WebAuthN a Windows 10 1809-es vagy újabb verzióját igényli * *

A webalkalmazásokhoz és szolgáltatásokhoz való bejelentkezéshez szükséges biztonsági kulcsok használatához olyan böngészőre van szükség, amely támogatja az WebAuthN protokollt. Ezek közé tartozik a Microsoft Edge, a Chrome, a Firefox és a Safari.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

A teszteléshez használt eszközökön a Windows 10 1809-es vagy újabb verziójának kell futnia. A legjobb élmény a Windows 10 1903-es vagy újabb verziója.

## <a name="enable-passwordless-authentication-method"></a>Jelszóval nem rendelkező hitelesítési módszer engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a közös regisztráció előzetes verziójára támaszkodnak. A kombinált regisztráció előzetes verziójának engedélyezéséhez kövesse a [kombinált biztonsági információk regisztrálásának engedélyezése (előzetes verzió)](howto-registration-mfa-sspr-combined.md)című cikkben ismertetett lépéseket.

### <a name="enable-fido2-security-key-method"></a>FIDO2 biztonsági kulcs módszerének engedélyezése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory** > **biztonsági** > **hitelesítési módszereit** > **hitelesítési módszer házirendje (előzetes verzió)** .
1. A Method **FIDO2 biztonsági kulcs**alatt válassza a következő beállításokat:
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. **Mentse** a konfigurációt.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 biztonsági kulcsok felhasználói regisztrációja és kezelése

1. Tallózással keresse meg [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Ha még nem tette meg, jelentkezzen be.
1. Kattintson a **biztonsági adatok**elemre.
   1. Ha a felhasználónak már van legalább egy Azure Multi-Factor Authentication-metódusa, akkor azonnal regisztrálhatnak egy FIDO2 biztonsági kulcsot.
   1. Ha nincs legalább egy Azure Multi-Factor Authentication-metódus regisztrálva, hozzá kell adnia egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot a **metódus hozzáadása** és a **biztonsági kulcs**kiválasztása elemre kattintva.
1. Válasszon **USB-eszközt** vagy **NFC-eszközt**.
1. Álljon készen a kulcsra, és válassza a **tovább**lehetőséget.
1. Megjelenik egy mező, és megkéri a felhasználót, hogy hozzon létre/adjon meg egy PIN-kódot a biztonsági kulcshoz, majd végezze el a szükséges kézmozdulatot a kulcshoz, vagy a biometrikus vagy az érintést.
1. A rendszer visszaadja a felhasználót a közös regisztrációs élménynek, és a rendszer arra kéri, hogy adjon meg egy értelmes nevet a kulcs számára, hogy a felhasználó azonosítani tudja, melyik a többhöz. Kattintson a **Tovább** gombra.
1. A folyamat befejezéséhez kattintson a **kész** gombra.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

Az alábbi példában a felhasználó már kiépített egy FIDO2 biztonsági kulcsot. A felhasználó úgy is dönthet, hogy a Windows 10 1809-es vagy újabb verziójában egy támogatott böngészőn belül bejelentkezik a webes FIDO2 biztonsági kulcsával.

![Biztonsági kulcs aláírása a Microsoft Edge-ben](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákba ütközik a funkció megtekintése közben, ossza meg a Windows visszajelzési központ alkalmazáson keresztül.

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   1. Kategória: biztonság és adatvédelem
   1. Alkategória:
1. A naplók rögzítéséhez használja a következő lehetőséget: a **probléma újbóli létrehozása**

## <a name="known-issues"></a>Ismert problémák

### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítés

A nyilvános előzetes verzióban nem érhető el a rendszergazdai kiépítés és a biztonsági kulcsok kiépítés.

### <a name="upn-changes"></a>UPN-változások

Ha a felhasználó egyszerű felhasználóneve megváltozik, már nem módosíthatja a FIDO2 biztonsági kulcsait a módosításhoz. A megoldás az eszköz alaphelyzetbe állítása, és a felhasználónak újra regisztrálnia kell a FIDO2 biztonsági kulcsait.

## <a name="next-steps"></a>További lépések

[FIDO2 biztonsági kulcs Windows 10-es bejelentkezés](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-hitelesítés engedélyezése helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközök regisztrálásáról](../devices/overview.md)

[További információ az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
