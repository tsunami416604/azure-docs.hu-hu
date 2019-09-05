---
title: Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése az Azure AD-ben (előzetes verzió) – Azure Active Directory
description: Jelszó nélküli biztonsági kulcs engedélyezése az Azure AD-be az FIDO2 biztonsági kulcsok használatával (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316a523a6216354ae5b6166be55e183a4e050766
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305075"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése az Azure AD-ben (előzetes verzió)

## <a name="requirements"></a>Követelmények

* Azure Multi-Factor Authentication
* Kombinált regisztráció előzetes verziója a SSPR engedélyezett felhasználók számára
* A FIDO2 biztonsági kulcs előzetes verziójának kompatibilis FIDO2 biztonsági kulcsokra van szüksége
* A WebAuthN használatához a Microsoft Edge szükséges a Windows 10 1809-es vagy újabb verziójához
* A FIDO2-alapú Windows-bejelentkezéshez az Azure AD-hez csatlakoztatott Windows 10 1809-es vagy újabb verziójára van szükség

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

A teszteléshez használt eszközökön a Windows 10 1809-es vagy újabb verziójának kell futnia. A legjobb élmény a Windows 10 1903-es vagy újabb verziója.

## <a name="enable-security-keys-for-windows-sign-in"></a>Biztonsági kulcsok engedélyezése Windows-bejelentkezéshez

A szervezetek dönthetnek úgy, hogy az alábbi módszerek közül egy vagy több használatával engedélyezik a Windows-bejelentkezéshez szükséges biztonsági kulcsok használatát.

### <a name="enable-credential-provider-via-intune"></a>Hitelesítő adatok szolgáltatójának engedélyezése az Intune-on keresztül

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással **Microsoft Intune** > **eszköz beléptetése** > Windows-**regisztráció** > Windows**Hello for Business** > -**Tulajdonságok**.
1. A **Settings (beállítások** ) beállításnál a **bejelentkezéshez a biztonsági kulcsok használata** **engedélyezett**.

A bejelentkezéshez szükséges biztonsági kulcsok konfigurálása nem függ a vállalati Windows Hello konfigurálásának.

#### <a name="enable-targeted-intune-deployment"></a>A célként megadott Intune üzembe helyezésének engedélyezése

A hitelesítő adatok szolgáltatójának engedélyezéséhez a következő egyéni beállításokat használhatja az Intune-on keresztül. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Microsoft Intune** > **eszköz konfigurációs** > **profiljainak** > **profil létrehozása**lehetőséget.
1. Konfigurálja az új profilt a következő beállításokkal
   1. Név: Biztonsági kulcsok a Windows-bejelentkezéshez
   1. Leírás: Lehetővé teszi, hogy a Windows bejelentkezéskor használni lehessen a következő biztonsági kulcsokat:
   1. Platform: Windows 10 és újabb verziók
   1. Profil típusa: Egyéni
   1. Egyéni OMA-URI beállítások:
      1. Név: A Windows-bejelentkezéshez tartozó a biztonsági kulcsok bekapcsolása
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Adattípus: Integer
      1. Érték 1 
1. Ezt a házirendet meghatározott felhasználókhoz, eszközökhöz vagy csoportokhoz lehet hozzárendelni. További információt a következő cikkben talál: [Microsoft Intune felhasználói és eszköz profiljának társítása](https://docs.microsoft.com/intune/device-profile-assign).

![Egyéni Intune-eszköz konfigurációs szabályzatának létrehozása](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Hitelesítőadat-szolgáltató engedélyezése a kiépítési csomag használatával

Az Intune által nem felügyelt eszközök esetében a kiépítési csomag telepíthető a funkció engedélyezéséhez. A Windows Configuration Designer alkalmazást a [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)lehet telepíteni.

1. Indítsa el a Windows Configuration Designer alkalmazást.
1. Válassza a **fájl** > **új projekt**lehetőséget.
1. Adjon nevet a projektnek, és jegyezze fel az elérési utat, ahol a projekt létrejött.
1. Kattintson a **Tovább** gombra.
1. Hagyja kiválasztva a **kiépítési csomagot** a **kiválasztott projekt-munkafolyamatként** , és válassza a **tovább**lehetőséget.
1. Válassza ki az **összes Windows asztali kiadás** elemet a **válassza ki a megtekinteni és konfigurálni kívánt beállításokat** , majd válassza a **tovább**lehetőséget.
1. Válassza a **Finish** (Befejezés) elemet.
1. Az újonnan létrehozott projektben keresse meg a **Futásidejű beállítások** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Állítsa be a **UseSecurityKeyForSignIn** beállítást **engedélyezve**értékre.
1. Válassza ki a**kiépítési csomag** **exportálása** > lehetőséget
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a **kiépítési csomag leírása** szakaszban, majd válassza a **tovább**lehetőséget.
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a kiépítési **csomag biztonsági adatainak kiválasztása** területen, majd válassza a **tovább**lehetőséget.
1. Jegyezze fel, vagy módosítsa a **Build** -ablakok elérési útját a **válassza ki, hová szeretné menteni a kiépítési csomagot** , és válassza a **tovább**lehetőséget.
1. Válassza a **Létrehozás** lehetőséget a kiépítési **csomag összeállítása** lapon.
1. Mentse a létrehozott két fájlt (ppkg és Cat) egy olyan helyre, ahol később is alkalmazhatja a gépeket.
1. A létrehozott kiépítési csomag alkalmazásához kövesse a következő cikkben található útmutatást: a [kiépítési csomag alkalmazása](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

## <a name="obtain-fido2-security-keys"></a>FIDO2 biztonsági kulcsainak beszerzése

A támogatott kulcsokkal és gyártókkal kapcsolatos további információkért tekintse meg a FIDO2 biztonsági kulcsait [ismertető cikket.](concept-authentication-passwordless.md)

> [!NOTE]
> Az NFC-alapú biztonsági kulcsok megvásárlása és megtervezése esetén támogatott NFC-olvasóra lesz szüksége.

## <a name="enable-passwordless-authentication-method"></a>Jelszóval nem rendelkező hitelesítési módszer engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztráció használatának engedélyezése

A jelszóval nem rendelkező hitelesítési módszerek regisztrációs funkciói a közös regisztráció előzetes verziójára támaszkodnak. A kombinált regisztráció előzetes verziójának engedélyezéséhez kövesse a [kombinált biztonsági információk regisztrálásának engedélyezése (előzetes verzió)](howto-registration-mfa-sspr-combined.md)című cikkben ismertetett lépéseket.

### <a name="enable-new-passwordless-authentication-method"></a>Új, jelszóval nem rendelkező hitelesítési módszer engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Keresse meg **Azure Active Directory** > **biztonsági** > **hitelesítési**módszerekhitelesítési > **módszerének házirendjét (előzetes verzió)**
1. Az egyes **módszerek**alatt válassza ki a következő beállításokat
   1. **Engedélyezés** – igen vagy nem
   1. **Cél** – az összes felhasználó vagy a felhasználók kiválasztása
1. Minden metódus **mentése**

> [!WARNING]
> A "Key korlátozási szabályzatok" FIDO2 még nem működik. Ez a funkció az általános elérhetőség előtt elérhető lesz, ne módosítsa ezeket a szabályzatokat az alapértelmezett értékre.

> [!NOTE]
> Nem kell mindkettőt a jelszó nélkül használni (ha csak egy jelszóval nem rendelkező metódust szeretne megtekinteni, csak ezt a metódust engedélyezheti). Javasoljuk, hogy mindkét módszert kipróbálhatja, mivel mindkettőnek saját előnyei vannak.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 biztonsági kulcsok felhasználói regisztrációja és kezelése

1. Tallózással keresse meg a[https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Ha még nem tette meg, jelentkezzen be
1. Kattintson a **biztonsági adatok** lehetőségre
   1. Ha a felhasználónak már van legalább egy Azure Multi-Factor Authentication-metódusa, akkor azonnal regisztrálhatnak egy FIDO2 biztonsági kulcsot.
   1. Ha nincs legalább egy Azure Multi-Factor Authentication-metódus regisztrálva, hozzá kell adnia egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot a **metódus hozzáadása** és a **biztonsági kulcs** kiválasztása elemre kattintva.
1. **USB-eszköz** vagy **NFC-eszköz** kiválasztása
1. Álljon készen a kulcsra, és válassza a **tovább** lehetőséget.
1. Megjelenik egy mező, és megkéri, hogy hozzon létre/adjon meg egy PIN-kódot a biztonsági kulcshoz, majd végezze el a szükséges kézmozdulatot a kulcshoz: biometrikus vagy Touch.
1. A rendszer visszaadja a kombinált regisztrációs élményt, és arra kéri, hogy adjon meg egy értelmes nevet a jogkivonat számára, hogy megismerje, melyik, ha több van. Kattintson a **Tovább** gombra.
1. A folyamat befejezéséhez kattintson a **kész** gombra.

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Biztonsági kulcs biometrikus kezelése, PIN-kód vagy alaphelyzetbe állítása

* Windows 10 1809-es verzió
   * A biztonsági kulcs gyártójától származó kiegészítő szoftver szükséges
* Windows 10 1903-es vagy újabb verzió
   * A felhasználók megnyitják a **Windows beállításait** az eszközön > **fiókok** > **biztonsági kulcsával**
   * A felhasználók módosíthatják a PIN-kódját, frissíthetik a biometriat, vagy alaphelyzetbe állíthatják a biztonsági kulcsot

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás felhasználói regisztrációja és kezelése

Ha be szeretné állítani a Microsoft Authenticator alkalmazást a telefonos bejelentkezéshez, kövesse a [bejelentkezni a fiókba a Microsoft Authenticator alkalmazás használatával](../user-help/user-help-auth-app-sign-in.md)című cikkben található útmutatást.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

### <a name="sign-in-at-the-lock-screen"></a>Bejelentkezés a zárolási képernyőn

Az alábbi példában a felhasználó, Bala FIDO2 már kiépített egy biztonsági kulcsot. A Bala kiválaszthatja a biztonsági kulcs hitelesítő adatait szolgáltatót a Windows 10 zárolási képernyőjén, és beillesztheti a biztonsági kulcsot a Windowsba való bejelentkezéshez.

![Biztonsági kulcs bejelentkezés a Windows 10 zárolási képernyőjén](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Bejelentkezés a weben

Az alábbi példában a felhasználó már kiépített egy FIDO2 biztonsági kulcsot. A felhasználó dönthet úgy, hogy a Microsoft Edge böngészőben a Windows 10 1809-es vagy újabb verzióján belül bejelentkezik a webes FIDO2 biztonsági kulcsával.

![Biztonsági kulcs aláírása a Microsoft Edge-ben](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítés

A nyilvános előzetes verzióban nem érhető el a rendszergazdai kiépítés és a biztonsági kulcsok kiépítés.

### <a name="hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás

Azok a felhasználók, akik felügyelt hitelesítő adatokat használnak, például a FIDO2 biztonsági kulcsokat vagy a jelszó nélküli bejelentkezést a Microsoft Authenticator alkalmazással, a Windows 10 rendszerhez való hibrid csatlakozásra van szükségük az egyszeri bejelentkezés előnyeinek kihasználásához. A biztonsági kulcsok azonban csak Azure Active Directory csatlakoztatott gépeken működnek. Javasoljuk, hogy a tiszta Azure Active Directory csatlakoztatott gépeken csak a Windows zárolási képernyőjén próbálja ki a FIDO2 biztonsági kulcsait. Ez a korlátozás nem vonatkozik a webes használatra.

### <a name="upn-changes"></a>UPN-változások

Dolgozunk azon funkció támogatásán, amely lehetővé teszi az UPN-változást a hibrid AADJ és AADJ eszközökön. Ha a felhasználó egyszerű felhasználóneve megváltozik, már nem módosíthatja a FIDO2 biztonsági kulcsait az adott fiókhoz. Ezért az egyetlen módszer, ha alaphelyzetbe állítja az eszközt, és a felhasználónak újra regisztrálnia kell.

## <a name="next-steps"></a>További lépések

[Tudnivalók az eszközök regisztrálásáról](../devices/overview.md)

[További tudnivalók az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
