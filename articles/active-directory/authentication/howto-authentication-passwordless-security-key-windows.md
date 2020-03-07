---
title: Jelszó nélküli biztonsági kulcs bejelentkezési Windows-Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a jelszó nélküli biztonsági kulcsos bejelentkezést Azure Active Directory FIDO2 biztonsági kulcsok használatával (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6ef244a887e75a0d8b9bb663d5325a33cd1e89
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378192"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése a Windows 10-es eszközökre Azure Active Directory (előzetes verzió)

Ez a dokumentum a FIDO2 biztonsági kulcson alapuló jelszavas hitelesítésnek a Windows 10-es eszközökön való engedélyezését összpontosítja. Ennek a cikknek a végén be tud jelentkezni az Azure ad-be és a hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökre az Azure AD-fiókkal egy FIDO2 biztonsági kulcs használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Követelmények

| Eszköztípus | Azure AD-hez csatlakoztatott | Hibrid Azure AD-csatlakozás |
| --- | --- | --- |
| [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [A kombinált biztonsági információk regisztrációjának előzetes verziója](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| A WebAuthN a Windows 10 1809-es vagy újabb verzióját igényli | X | X |
| Az [Azure ad-hez csatlakoztatott eszközökhöz](../devices/concept-azure-ad-join.md) a Windows 10 1903-es vagy újabb verziója szükséges | X |   |
| A [hibrid Azure ad-hez csatlakoztatott eszközökhöz](../devices/concept-azure-ad-join-hybrid.md) Windows 10 bennfentes Build 18945 vagy újabb verzió szükséges |   | X |
| Teljes mértékben kijavítottuk a Windows Server 2016/2019 rendszerű tartományvezérlőket. |   | X |
| [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0 vagy újabb verzió |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (nem kötelező) | X | X |
| Kiépítési csomag (nem kötelező) | X | X |
| Csoportházirend (nem kötelező) |   | X |

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak:

- Windows Server Active Directory tartományi szolgáltatások (AD DS) tartományhoz csatlakoztatott (csak helyszíni eszközök) telepítése.
- RDP-, VDI-és Citrix-forgatókönyvek biztonsági kulccsal.
- S/MIME biztonsági kulccsal.
- "Run as" (Futtatás másként) biztonsági kulcs használatával.
- Jelentkezzen be egy kiszolgálóra egy biztonsági kulccsal.
- Ha nem használta a biztonsági kulcsot arra, hogy online állapotba jelentkezzen be az eszközre, nem használhatja azt a kapcsolat nélküli bejelentkezéshez vagy a zárolás feloldásához.
- Windows 10 rendszerű eszköz beléptetése vagy feloldása több Azure AD-fiókot tartalmazó biztonsági kulccsal. Ez a forgatókönyv a biztonsági kulcshoz hozzáadott utolsó fiókot használja. A WebAuthN lehetővé teszi a felhasználók számára a használni kívánt fiók kiválasztását.
- Windows 10 1809-es verziót futtató eszköz zárolásának feloldása. A legjobb megoldás, ha a Windows 10 1903-es vagy újabb verzióját használja.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése az előzetes verzióra

Az Azure AD-hez csatlakoztatott eszközök, amelyeket a szolgáltatás előzetes verziójában végez, a Windows 10 1809-es vagy újabb verzióját kell futtatnia. A legjobb élmény a Windows 10 1903-es vagy újabb verziója.

A hibrid Azure AD-hez csatlakoztatott eszközökön a Windows 10 bennfentes Build 18945 vagy újabb verziójának kell futnia.

## <a name="enable-security-keys-for-windows-sign-in"></a>Biztonsági kulcsok engedélyezése a Windows-bejelentkezéshez

A szervezetek dönthetnek úgy, hogy az alábbi módszerek közül egy vagy több használatával engedélyezik a Windows-bejelentkezéshez szükséges biztonsági kulcsok használatát a szervezet követelményei alapján:

- [Engedélyezés az Intune-nal](#enable-with-intune)
- [Célként megadott Intune-telepítés](#targeted-intune-deployment)
- [Engedélyezés kiépítési csomaggal](#enable-with-a-provisioning-package)
- [Engedélyezés Csoportházirendsal (csak hibrid Azure AD-hez csatlakoztatott eszközök)](#enable-with-group-policy)

> [!IMPORTANT]
> A **hibrid Azure ad-hez csatlakoztatott eszközökhöz** tartozó szervezeteknek **is** el kell végezniük a cikkben ismertetett lépéseket, engedélyezni kell a [FIDO2 hitelesítést a helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md) , mielőtt a Windows 10 FIDO2 biztonsági kulcs hitelesítése működik.
>
> Az **Azure ad-hez csatlakoztatott eszközökhöz** tartozó szervezeteknek ezt azelőtt kell megtenniük, hogy az eszközeik a helyszíni erőforrásokhoz FIDO2 biztonsági kulccsal hitelesítve legyenek.

### <a name="enable-with-intune"></a>Engedélyezés az Intune-nal

A biztonsági kulcsok Intune használatával történő használatának engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Keresse meg **Microsoft Intune** > **eszköz beléptetése** > **Windows-regisztráció** > **Windows Hello for Business** > **tulajdonságokat**.
1. A **Beállítások**területen állítsa be a **biztonsági kulcsok használata a bejelentkezéshez** **beállítást.**

A bejelentkezéshez szükséges biztonsági kulcsok konfigurálása nem függ a vállalati Windows Hello konfigurálásának.

### <a name="targeted-intune-deployment"></a>Célként megadott Intune-telepítés

A hitelesítő adatok szolgáltatójának engedélyezéséhez a következő egyéni beállításokat használhatja az Intune-on keresztül:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Tallózással keresse meg **Microsoft Intune** > az **eszköz konfigurációjának** > **profiljait** , > **hozza létre a profilt**.
1. Konfigurálja az új profilt a következő beállításokkal:
   - Név: biztonsági kulcsok a Windows-bejelentkezéshez
   - Leírás: lehetővé teszi, hogy a rendszer a Windows bejelentkezéskor használni kívánt biztonsági kulcsokat használja.
   - Platform: Windows 10 és újabb verziók
   - Profil típusa: Custom
   - Egyéni OMA-URI beállítások:
      - Név: a Windows-bejelentkezéshez tartozó. biztonsági kulcsok bekapcsolása
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Adattípus: egész szám
      - Érték: 1
1. Ezt a házirendet meghatározott felhasználókhoz, eszközökhöz vagy csoportokhoz lehet hozzárendelni. További információ: [felhasználói és eszköz profilok társítása Microsoft Intuneban](https://docs.microsoft.com/intune/device-profile-assign).

![Egyéni Intune-eszköz konfigurációs szabályzatának létrehozása](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Engedélyezés kiépítési csomaggal

Az Intune által nem felügyelt eszközök esetében a kiépítési csomag telepíthető a funkció engedélyezéséhez. A Windows Configuration Designer alkalmazást a [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)lehet telepíteni. A kiépítési csomag létrehozásához hajtsa végre a következő lépéseket:

1. Indítsa el a Windows Configuration Designer alkalmazást.
1. Válassza a **fájl** > **új projekt**lehetőséget.
1. Adjon nevet a projektnek, és jegyezze fel a projekt létrehozási útját, majd kattintson a **tovább**gombra.
1. Hagyja kiválasztva a *kiépítési csomagot* a **kiválasztott projekt-munkafolyamatként** , és válassza a **tovább**lehetőséget.
1. Válassza ki az *összes Windows asztali kiadás* elemet a **válassza ki a megtekinteni és konfigurálni kívánt beállításokat**, majd kattintson a **tovább**gombra.
1. Válassza a **Finish** (Befejezés) elemet.
1. Az újonnan létrehozott projektben keresse meg a **futtatókörnyezet beállításait** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Állítsa be a **UseSecurityKeyForSignIn** beállítást *engedélyezve*értékre.
1. Válassza ki az > **kiépítési csomag** **exportálása** lehetőséget
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a **kiépítési csomag leírása**területen, majd kattintson a **tovább**gombra.
1. Hagyja meg az alapértelmezett értékeket a **létrehozási** ablakban a kiépítési **csomag biztonsági adatainak kiválasztása** területen, majd válassza a **tovább**lehetőséget.
1. Jegyezze fel, vagy módosítsa a **Build** -ablakok elérési útját a **válassza ki, hová szeretné menteni a kiépítési csomagot** , és válassza a **tovább**lehetőséget.
1. Válassza a **Létrehozás** lehetőséget a kiépítési **csomag összeállítása** lapon.
1. Mentse a létrehozott két fájlt (*ppkg* és *Cat*) egy olyan helyre, ahol később is alkalmazhatja a gépeket.
1. A létrehozott kiépítési csomag alkalmazásához tekintse meg a [kiépítési csomag alkalmazása](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)című témakört.

> [!NOTE]
> A Windows 10 1809-es verzióját futtató eszközökön a megosztott számítógépes üzemmódot (*EnableSharedPCMode*) is engedélyeznie kell. A funkció engedélyezésével kapcsolatos további információkért lásd: [megosztott vagy vendég számítógép beállítása Windows 10 rendszeren](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Engedélyezés Csoportházirend

A **hibrid Azure ad-hez csatlakoztatott eszközökhöz**a szervezetek a következő csoportházirend-beállítást konfigurálhatják, hogy lehetővé tegyék a. biztonsági kulcsos bejelentkezést. A beállítás a **Számítógép konfigurációja** > **Felügyeleti sablonok** > **rendszer** > **Bejelentkezés** > a **biztonsági kulcs bejelentkezésének bekapcsolása**elemnél található:

- A házirend **engedélyezésének engedélyezése** lehetővé teszi a felhasználók számára a biztonsági kulcsokkal való bejelentkezést.
- Ha a házirendet **Letiltva** vagy **nincs konfigurálva** , a felhasználók nem jelentkezhetnek be a biztonsági kulcsokkal.

Ennek a Csoportházirend beállításnak a `credentialprovider.admx` Csoportházirend sablon frissített verzióját kell megadnia. Ez az új sablon a Windows Server következő verziójával és a Windows 10 20H1 érhető el. Ezt a beállítást olyan eszközzel lehet felügyelni, amely a Windows újabb verzióinak egyikét futtatja, vagy központilag a támogatási témakör útmutatását követve a [windows Csoportházirend felügyeleti sablonok központi tárolójának létrehozása és kezelése](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Bejelentkezés FIDO2 biztonsági kulccsal

Az alábbi példában egy Bala FIDO2 nevű felhasználó már kiépítte a biztonsági kulcsot az előző cikkben leírt lépésekkel, [engedélyezheti a jelszó nélküli biztonsági kulcs bejelentkezését](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Hibrid Azure AD-hez csatlakoztatott eszközök esetén győződjön meg arról, hogy [a jelszó nélküli biztonsági kulcs bejelentkezését is engedélyezte a helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md). A Bala kiválaszthatja a biztonsági kulcs hitelesítő adatait szolgáltatót a Windows 10 zárolási képernyőjén, és beillesztheti a biztonsági kulcsot a Windowsba való bejelentkezéshez.

![Biztonsági kulcs bejelentkezés a Windows 10 zárolási képernyőjén](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Biztonsági kulcs biometrikus kezelése, PIN-kód vagy alaphelyzetbe állítása

* Windows 10 1903-es vagy újabb verzió
   * A felhasználók megnyithatja az eszközön a **Windows beállításait** > **fiókok** > **biztonsági kulcs**
   * A felhasználók módosíthatják a PIN-kódját, frissíthetik a biometriat, vagy alaphelyzetbe állíthatják a biztonsági kulcsot

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákat tapasztal a funkció megtekintése közben, ossza meg a Windows visszajelzési központ alkalmazáson keresztül a következő lépésekkel:

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   - Kategória: biztonság és adatvédelem
   - Alkategória:
1. A naplók rögzítéséhez használja a problémát a **probléma újbóli létrehozásához** .

## <a name="next-steps"></a>Következő lépések

[Helyszíni erőforrásokhoz való hozzáférés engedélyezése az Azure AD-hez és a hibrid Azure AD-hez csatlakoztatott eszközökhöz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközök regisztrálásáról](../devices/overview.md)

[További információ az Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
