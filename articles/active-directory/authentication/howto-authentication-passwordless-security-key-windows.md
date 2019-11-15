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
ms.openlocfilehash: 7b3aa2add128cfc11a638fe6c7e03cfb25189afc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081561"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése Windows 10-es eszközökre (előzetes verzió)

Ez a dokumentum a FIDO2 biztonsági kulcson alapuló jelszavas hitelesítésnek a Windows 10-es eszközökön való engedélyezését összpontosítja. Ennek a cikknek a végén be tud jelentkezni a webalapú alkalmazásokra és az Azure AD-hez csatlakoztatott Windows 10-es eszközökre az Azure AD-fiókjával egy FIDO2 biztonsági kulcs használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Követelmények

- [Az Azure multi-factor Authentication](howto-mfa-getstarted.md)
- [A kombinált biztonsági információk regisztrációjának előzetes verziója](concept-registration-mfa-sspr-combined.md)
- Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)
- A WebAuthN a Windows 10 1809-es vagy újabb verzióját igényli
- Az [Azure ad-hez csatlakoztatott eszközökhöz](../devices/concept-azure-ad-join.md) a Windows 10 1809-es vagy újabb verziója szükséges
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (nem kötelező)
- Kiépítési csomag (nem kötelező)

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

- A Windows Server Active Directory tartományi szolgáltatások (AD DS) tartományhoz csatlakoztatott (csak helyszíni eszközök) központi telepítése **nem támogatott**.
- Az RDP-, VDI-és Citrix-forgatókönyvek **nem támogatottak** a biztonsági kulcs használatával.
- Az S/MIME **nem támogatott** a biztonsági kulcs használatával.
- A "Run as" **nem támogatott** a biztonsági kulcs használatával.
- A biztonsági kulccsal való bejelentkezés **nem támogatott**a kiszolgálóra.
- Ha nem használta a biztonsági kulcsot arra, hogy online állapotba jelentkezzen be az eszközre, nem fogja tudni használni a bejelentkezéshez vagy a zárolás feloldásához.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

Az Azure AD-hez csatlakoztatott eszközöknek, amelyekkel kísérletezni fog, a Windows 10 1809-es vagy újabb verziójának kell futnia. A legjobb élmény a Windows 10 1903-es vagy újabb verziója.

## <a name="enable-security-keys-for-windows-sign-in"></a>Biztonsági kulcsok engedélyezése a Windows-bejelentkezéshez

A szervezetek az alábbi módszerek közül egyet vagy többet is választhatnak, hogy a szervezet követelményei alapján engedélyezzék a Windows-bejelentkezéshez szükséges biztonsági kulcsok használatát.

- [Engedélyezés az Intune-nal](#enable-with-intune)
   - [Célként megadott Intune-telepítés](#targeted-intune-deployment)
- [Engedélyezés kiépítési csomaggal](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Engedélyezés az Intune-nal

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Keresse meg **Microsoft Intune** > **eszköz beléptetése** > **Windows-regisztráció** > **Windows Hello for Business** > **tulajdonságokat**.
1. A **Settings (beállítások** ) beállításnál a **bejelentkezéshez a biztonsági kulcsok használata** **engedélyezett**.

A bejelentkezéshez szükséges biztonsági kulcsok konfigurálása nem függ a vállalati Windows Hello konfigurálásának.

#### <a name="targeted-intune-deployment"></a>Célként megadott Intune-telepítés

A hitelesítő adatok szolgáltatójának engedélyezéséhez a következő egyéni beállításokat használhatja az Intune-on keresztül.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Microsoft Intune** > az **eszköz konfigurációjának** > **profiljait** , > **hozza létre a profilt**.
1. Konfigurálja az új profilt a következő beállításokkal
   1. Név: biztonsági kulcsok a Windows-bejelentkezéshez
   1. Leírás: lehetővé teszi, hogy a rendszer a Windows bejelentkezéskor használni kívánt biztonsági kulcsokat használja.
   1. Platform: Windows 10 és újabb verziók
   1. Profil típusa: Custom
   1. Egyéni OMA-URI beállítások:
      1. Név: a Windows-bejelentkezéshez tartozó. biztonsági kulcsok bekapcsolása
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Adattípus: egész szám
      1. Érték: 1
1. Ezt a házirendet meghatározott felhasználókhoz, eszközökhöz vagy csoportokhoz lehet hozzárendelni. További információt a következő cikkben talál: [Microsoft Intune felhasználói és eszköz profiljának társítása](https://docs.microsoft.com/intune/device-profile-assign).

![Egyéni Intune-eszköz konfigurációs szabályzatának létrehozása](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Engedélyezés kiépítési csomaggal

Az Intune által nem felügyelt eszközök esetében a kiépítési csomag telepíthető a funkció engedélyezéséhez. A Windows Configuration Designer alkalmazást a [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22)lehet telepíteni.

1. Indítsa el a Windows Configuration Designer alkalmazást.
1. Válassza a **fájl** > **új projekt**lehetőséget.
1. Adjon nevet a projektnek, és jegyezze fel az elérési utat, ahol a projekt létrejött.
1. Kattintson a **Tovább** gombra.
1. Hagyja kiválasztva a **kiépítési csomagot** a **kiválasztott projekt-munkafolyamatként** , és válassza a **tovább**lehetőséget.
1. Válassza ki az **összes Windows asztali kiadás** elemet a **válassza ki a megtekinteni és konfigurálni kívánt beállításokat** , majd válassza a **tovább**lehetőséget.
1. Válassza a **Finish** (Befejezés) elemet.
1. Az újonnan létrehozott projektben keresse meg a **futtatókörnyezet beállításait** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Állítsa be a **UseSecurityKeyForSignIn** beállítást **engedélyezve**értékre.
1. Válassza ki az > **kiépítési csomag** **exportálása** lehetőséget
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a **kiépítési csomag leírása** szakaszban, majd válassza a **tovább**lehetőséget.
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a kiépítési **csomag biztonsági adatainak kiválasztása** területen, majd válassza a **tovább**lehetőséget.
1. Jegyezze fel, vagy módosítsa a **Build** -ablakok elérési útját a **válassza ki, hová szeretné menteni a kiépítési csomagot** , és válassza a **tovább**lehetőséget.
1. Válassza a **Létrehozás** lehetőséget a kiépítési **csomag összeállítása** lapon.
1. Mentse a létrehozott két fájlt (ppkg és Cat) egy olyan helyre, ahol később is alkalmazhatja a gépeket.
1. A létrehozott kiépítési csomag alkalmazásához kövesse a következő cikkben található útmutatást: a [kiépítési csomag alkalmazása](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> A Windows 10 1809-es verzióját futtató eszközökön a megosztott számítógépes üzemmódot (EnableSharedPCMode) is engedélyeznie kell. A funtionality engedélyezésével kapcsolatos információkért tekintse meg a következő cikket: [megosztott vagy vendég számítógép beállítása Windows 10 rendszeren](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>Bejelentkezés a Windowsba FIDO2 biztonsági kulccsal

Az alábbi példában a felhasználói Bala FIDO2 már kiépített egy biztonsági kulcsot az előző cikkben leírt lépésekkel, [engedélyezheti a jelszó nélküli biztonsági kulcs bejelentkezését](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). A Bala kiválaszthatja a biztonsági kulcs hitelesítő adatait szolgáltatót a Windows 10 zárolási képernyőjén, és beillesztheti a biztonsági kulcsot a Windowsba való bejelentkezéshez.

![Biztonsági kulcs bejelentkezés a Windows 10 zárolási képernyőjén](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Biztonsági kulcs biometrikus kezelése, PIN-kód vagy alaphelyzetbe állítása

* Windows 10 1903-es vagy újabb verzió
   * A felhasználók megnyithatja az eszközön a **Windows beállításait** > **fiókok** > **biztonsági kulcs**
   * A felhasználók módosíthatják a PIN-kódját, frissíthetik a biometriat, vagy alaphelyzetbe állíthatják a biztonsági kulcsot

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákba ütközik a funkció megtekintése közben, ossza meg a Windows visszajelzési központ alkalmazáson keresztül.

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   1. Kategória: biztonság és adatvédelem
   1. Alkategória:
1. A naplók rögzítéséhez használja a következő lehetőséget: a **probléma újbóli létrehozása**

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-work-in-my-on-premises-environment"></a>Működik ez a helyszíni környezetben?

Ez a funkció nem működik tiszta helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetben.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A szervezetem két faktoros hitelesítést igényel az erőforrások eléréséhez, Mire használhatom ezt a követelményt?

A biztonsági kulcsok különféle formában jelennek meg. Vegye fel a kapcsolatot az eszköz gyártójával, és beszéljen arról, hogy az eszközük hogyan engedélyezhető PIN-kód vagy biometrikus azonosító használatával második tényezőként.

### <a name="can-admins-set-up-security-keys"></a>A rendszergazdák beállítják a biztonsági kulcsokat?

Ezen funkció általánosan elérhetővé vált ezen a lehetőségen.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Hol találhatom meg a megfelelő biztonsági kulcsokat?

[FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Mi a teendő, ha elveszítem a biztonsági kulcsot?

A kulcsokat a Azure Portalból távolíthatja el, ha a biztonsági adatok lapra navigál, és eltávolítja a biztonsági kulcsot.

## <a name="next-steps"></a>Következő lépések

[További információ az eszközök regisztrálásáról](../devices/overview.md)

[További információ az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
