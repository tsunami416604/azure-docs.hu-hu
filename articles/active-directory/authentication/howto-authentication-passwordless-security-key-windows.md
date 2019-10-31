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
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172167"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése Windows 10-es eszközökre (előzetes verzió)

Ez a dokumentum a FIDO2 biztonsági kulcson alapuló jelszavas hitelesítésnek a Windows 10-es eszközökön való engedélyezését összpontosítja. Ennek a cikknek a végén be tud jelentkezni a webalapú alkalmazásokra és az Azure AD-hez csatlakoztatott Windows 10-es eszközökre az Azure AD-fiókjával egy FIDO2 biztonsági kulcs használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Követelmények

| Eszköz típusa | Azure AD-hez csatlakoztatott | Hibrid Azure AD-csatlakozás |
| --- | --- | --- |
| [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [A kombinált biztonsági információk regisztrációjának előzetes verziója](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| A WebAuthN a Windows 10 1809-es vagy újabb verzióját igényli | X | X |
| Az [Azure ad-hez csatlakoztatott eszközökhöz](../devices/concept-azure-ad-join.md) a Windows 10 1809-es vagy újabb verziója szükséges | X |   |
| A [hibrid Azure ad-hez csatlakoztatott eszközökhöz](../devices/concept-azure-ad-join-hybrid.md) Windows 10 bennfentes Build 18945 vagy újabb verzió szükséges |   | X |
| Teljes mértékben kijavítottuk a Windows Server 2016/2019 rendszerű tartományvezérlőket. |   | X |
| Frissítés a [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) legújabb verziójára |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (nem kötelező) | X | X |
| Kiépítési csomag (nem kötelező) | X | X |
| Csoportházirend (nem kötelező) |   | X |

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

- A Windows Server Active Directory tartományi szolgáltatások (AD DS) tartományhoz csatlakoztatott (csak helyszíni eszközök) központi telepítése **nem támogatott**.
- Az RDP-, VDI-és Citrix-forgatókönyvek **nem támogatottak** a biztonsági kulcs használatával.
- Az S/MIME **nem támogatott** a biztonsági kulcs használatával.
- A "Run as" **nem támogatott** a biztonsági kulcs használatával.
- A biztonsági kulccsal való bejelentkezés **nem támogatott**a kiszolgálóra.
- Ha nem használta a biztonsági kulcsot arra, hogy online állapotba jelentkezzen be az eszközre, nem fogja tudni használni a bejelentkezéshez vagy a zárolás feloldásához.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

Az Azure AD-hez csatlakoztatott eszközöknek, amelyekkel kísérletezni fog, a Windows 10 1809-es vagy újabb verziójának kell futnia. A legjobb élmény a Windows 10 1903-es vagy újabb verziója.

A teszteléshez használt hibrid Azure AD-hez csatlakoztatott eszközöket a Windows 10 Insider Build 18945 vagy újabb verziójának kell futnia.

## <a name="enable-security-keys-for-windows-sign-in"></a>Biztonsági kulcsok engedélyezése a Windows-bejelentkezéshez

A szervezetek az alábbi módszerek közül egyet vagy többet is választhatnak, hogy a szervezet követelményei alapján engedélyezzék a Windows-bejelentkezéshez szükséges biztonsági kulcsok használatát.

- [Engedélyezés az Intune-nal](#enable-with-intune)
   - [Célként megadott Intune-telepítés](#targeted-intune-deployment)
- [Engedélyezés kiépítési csomaggal](#enable-with-a-provisioning-package)
- [Engedélyezés Csoportházirendsal (csak hibrid Azure AD-hez csatlakoztatott eszközök)](#enable-with-group-policy)

> [!IMPORTANT]
> A **hibrid Azure ad-hez csatlakoztatott eszközökhöz** tartozó szervezeteknek **is** el kell végezniük a cikkben ismertetett lépéseket, engedélyezni kell a [FIDO2 hitelesítést a helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md) , mielőtt a Windows 10 FIDO2 a biztonsági kulcs hitelesítése működni fog.
>
> Az **Azure ad-hez csatlakoztatott eszközökhöz** tartozó szervezeteknek ezt azelőtt kell megtenniük, hogy az eszközeik a FIDO2 biztonsági kulcsokkal képesek legyenek hitelesíteni a helyszíni erőforrásokon.

### <a name="enable-with-intune"></a>Engedélyezés az Intune-nal

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Keresse meg **Microsoft Intune** > **eszköz beléptetése** > **Windows-regisztráció** > **Windows Hello for Business** > **tulajdonságokat**.
1. A **Settings (beállítások** ) beállításnál a **bejelentkezéshez a biztonsági kulcsok használata** **engedélyezett**.

A bejelentkezéshez szükséges biztonsági kulcsok konfigurálása nem függ a vállalati Windows Hello konfigurálásának.

#### <a name="targeted-intune-deployment"></a>Célként megadott Intune-telepítés

A hitelesítő adatok szolgáltatójának engedélyezéséhez a következő egyéni beállításokat használhatja az Intune-on keresztül.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
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

Az Intune által nem felügyelt eszközök esetében a kiépítési csomag telepíthető a funkció engedélyezéséhez. A Windows Configuration Designer alkalmazást a [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)lehet telepíteni.

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

### <a name="enable-with-group-policy"></a>Engedélyezés Csoportházirend

A **hibrid Azure ad-hez csatlakoztatott eszközökhöz** tartozó szervezetek a következő csoportházirend-beállítást konfigurálhatják, hogy lehetővé tegyék a. biztonsági kulcsos bejelentkezést.

A beállítás a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **rendszer** > **Bejelentkezés** > a **biztonsági kulcs bejelentkezésének bekapcsolása**lehetőség alatt található.

- Ha ezt a házirendet **engedélyezve** értékre állítja, lehetővé teszi a felhasználók számára a biztonsági kulcsokkal való bejelentkezést.
- Ha a házirendet **le szeretné tiltani** vagy **nincs konfigurálva** , a felhasználók nem tudnak bejelentkezni a biztonsági kulcsokkal.

Ennek a Csoportházirend beállításnak a `credentialprovider.admx` Csoportházirend sablon frissített verzióját kell megadnia. Ez az új sablon a Windows Server következő verziójával és a Windows 10 20H1 érhető el. Ezt a beállítást olyan eszközzel lehet felügyelni, amely a Windows újabb verzióinak egyikét futtatja, vagy központilag a támogatási témakör útmutatását követve a [windows Csoportházirend felügyeleti sablonok központi tárolójának létrehozása és kezelése](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Bejelentkezés FIDO2 biztonsági kulccsal

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

## <a name="next-steps"></a>Következő lépések

[Helyszíni erőforrásokhoz való hozzáférés engedélyezése az Azure AD-hez és a hibrid Azure AD-hez csatlakoztatott eszközökhöz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközök regisztrálásáról](../devices/overview.md)

[További információ az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
