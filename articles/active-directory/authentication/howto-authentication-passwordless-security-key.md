---
title: Jelszó nélküli biztonsági kulcs bejelentkezés (előzetes verzió) – Azure Active Directory
description: Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése az Azure AD-be az FIDO2 biztonsági kulcsok használatával (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ac8cf172a13e7198233170634ee4a3954793cd2
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743428"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése (előzetes verzió)

Azok a vállalatok, amelyek a jelszavakat ma és megosztott számítógépes környezettel használják, a biztonsági kulcsok zökkenőmentesen biztosítják a feldolgozók számára a Felhasználónév vagy a jelszó beírása nélkül történő hitelesítést. A biztonsági kulcsok nagyobb hatékonyságot biztosítanak a dolgozók számára, és jobb biztonságot nyújtanak.

Ez a dokumentum a biztonsági kulcson alapuló jelszavas hitelesítés engedélyezését összpontosítja. A cikk végén az Azure AD-fiókjával bejelentkezhet a web-alapú alkalmazásokba egy FIDO2 biztonsági kulccsal.

> [!NOTE]
> A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a  [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- A [kombinált biztonsági információk regisztrációjának előzetes](concept-registration-mfa-sspr-combined.md) verziójának engedélyezése
- Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)
- A WebAuthN a Windows 10 1903-es vagy újabb verzióját igényli * *

A webalkalmazásokhoz és szolgáltatásokhoz való bejelentkezéshez szükséges biztonsági kulcsok használatához olyan böngészőre van szükség, amely támogatja az WebAuthN protokollt. Ezek közé tartozik a Microsoft Edge, a Chrome, a Firefox és a Safari.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

Az Azure AD-hez csatlakoztatott eszközök esetében a legjobb megoldás a Windows 10 1903-es vagy újabb verziója.

A hibrid Azure AD-hez csatlakoztatott eszközöknek a Windows 10 2004-es vagy újabb verzióját kell futtatniuk.

## <a name="enable-passwordless-authentication-method"></a>Jelszóval nem rendelkező hitelesítési módszer engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a kombinált regisztráció funkcióra támaszkodnak. A kombinált regisztráció engedélyezéséhez kövesse a [kombinált biztonsági információk regisztrálásának engedélyezése (előzetes verzió)](howto-registration-mfa-sspr-combined.md)című cikkben ismertetett lépéseket.

### <a name="enable-fido2-security-key-method"></a>FIDO2 biztonsági kulcs módszerének engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **Azure Active Directory**  >  **biztonsági**  >  **hitelesítési módszerek**  >  **hitelesítési módszer házirendjét (előzetes verzió)**.
1. A Method **FIDO2 biztonsági kulcs** alatt válassza a következő beállításokat:
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. **Mentse** a konfigurációt.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 biztonsági kulcsok felhasználói regisztrációja és kezelése

1. Lépjen a [https://myprofile.microsoft.com](https://myprofile.microsoft.com) lapra.
1. Ha még nem tette meg, jelentkezzen be.
1. Kattintson a **biztonsági adatok** elemre.
   1. Ha a felhasználónak már van legalább egy Azure AD Multi-Factor Authentication-metódusa, akkor azonnal regisztrálhatnak egy FIDO2 biztonsági kulcsot.
   1. Ha nem rendelkeznek legalább egy Azure AD Multi-Factor Authentication-metódussal, hozzá kell adni egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot a **metódus hozzáadása** és a **biztonsági kulcs** kiválasztása elemre kattintva.
1. Válasszon **USB-eszközt** vagy **NFC-eszközt**.
1. Álljon készen a kulcsra, és válassza a **tovább** lehetőséget.
1. Megjelenik egy mező, és megkéri a felhasználót, hogy hozzon létre/adjon meg egy PIN-kódot a biztonsági kulcshoz, majd végezze el a szükséges kézmozdulatot a kulcshoz, vagy a biometrikus vagy az érintést.
1. A rendszer visszaadja a felhasználót a közös regisztrációs élménynek, és a rendszer arra kéri, hogy adjon meg egy értelmes nevet a kulcs számára, hogy a felhasználó azonosítani tudja, melyik a többhöz. Kattintson a **Tovább** gombra.
1. A folyamat befejezéséhez kattintson a **kész** gombra.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

Az alábbi példában a felhasználó már kiépített egy FIDO2 biztonsági kulcsot. A felhasználó úgy is dönthet, hogy a Windows 10 1903-es vagy újabb verziójában egy támogatott böngészőn belül bejelentkezik a webes FIDO2 biztonsági kulcsával.

![Biztonsági kulcs aláírása a Microsoft Edge-ben](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákat tapasztal a funkció megtekintése közben, ossza meg a Windows visszajelzési központ alkalmazáson keresztül a következő lépésekkel:

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   - Kategória: biztonság és adatvédelem
   - Alkategória:
1. A naplók rögzítéséhez használja a problémát a **probléma újbóli létrehozásához** .

## <a name="known-issues"></a>Ismert problémák

### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítés

A nyilvános előzetes verzióban nem érhető el a rendszergazdai kiépítés és a biztonsági kulcsok kiépítés.

### <a name="upn-changes"></a>UPN-módosítások

Dolgozunk azon funkció támogatásán, amely lehetővé teszi az UPN-változást a hibrid Azure AD-hez csatlakoztatott és az Azure AD-hez csatlakoztatott eszközökön. Ha a felhasználó egyszerű felhasználóneve megváltozik, már nem módosíthatja a FIDO2 biztonsági kulcsait a módosításhoz. A megoldás az eszköz alaphelyzetbe állítása, és a felhasználónak újra regisztrálnia kell.

## <a name="next-steps"></a>További lépések

[FIDO2 biztonsági kulcs Windows 10-es bejelentkezés](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-hitelesítés engedélyezése helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközök regisztrálásáról](../devices/overview.md)

[További információ az Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
