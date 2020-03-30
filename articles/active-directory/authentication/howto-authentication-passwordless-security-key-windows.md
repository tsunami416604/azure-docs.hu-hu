---
title: Jelszó nélküli biztonsági kulcs bejelentkezés windows – Azure Active Directory
description: Megtudhatja, hogy miként engedélyezheti a jelszó nélküli biztonsági kulcsbejelentkezést az Azure Active Directoryba a FIDO2 biztonsági kulcsokkal (előzetes verzió)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263906"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezés engedélyezése Windows 10-es eszközökre az Azure Active Directoryval (előzetes verzió)

Ez a dokumentum a FIDO2 biztonsági kulcsalapú jelszó nélküli hitelesítés engedélyezésére összpontosít a Windows 10-es eszközökkel. A cikk végén az Azure AD és a hibrid Azure AD csatlakozott a Windows 10-es eszközök az Azure AD-fiók egy FIDO2 biztonsági kulcs használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsok az Azure Active Directory nyilvános előzetes verziójú szolgáltatásai. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

## <a name="requirements"></a>Követelmények

| Eszköz típusa | Azure AD-hez csatlakoztatva | csatlakozik a Hibrid Azure AD-hez |
| --- | --- | --- |
| [Azure többtényezős hitelesítés](howto-mfa-getstarted.md) | X | X |
| [Kombinált biztonsági adatok regisztrációs előnézete](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| A WebAuthN használatához Windows 10-es vagy újabb verzió szükséges | X | X |
| [Az Azure AD-hez csatlakozó eszközökhasználatához](../devices/concept-azure-ad-join.md) Windows 10-es vagy újabb verzió szükséges | X |   |
| [A hibrid Azure AD-hez csatlakozó eszközök](../devices/concept-azure-ad-join-hybrid.md) használatához Windows 10 Insider Build 18945 vagy újabb szükséges |   | X |
| Teljesen javított Windows Server 2016/2019 tartományvezérlők. |   | X |
| [Az Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0-s vagy újabb verziója |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (nem kötelező) | X | X |
| Kiépítési csomag (nem kötelező) | X | X |
| Csoportházirend (nem kötelező) |   | X |

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő esetekben nem támogatottak:

- A Windows Server Active Directory tartományi szolgáltatások (AD DS) tartományhoz csatlakozott (csak helyszíni eszközök) központi telepítése.
- RDP, VDI és Citrix forgatókönyvek biztonsági kulccsal.
- Biztonsági kulccsal.
- "Futtatás másként" egy biztonsági kulccsal.
- Bejelentkezés a kiszolgálóra biztonsági kulccsal.
- Ha nem használta a biztonsági kulcsot az eszközre való bejelentkezéshez online módban, nem használhatja azt a bejelentkezéshez vagy a kapcsolat nélküli zárolás feloldásához.
- Windows 10-es eszköz bejelentkezése vagy feloldása több Azure AD-fiókot tartalmazó biztonsági kulccsal. Ez a forgatókönyv a biztonsági kulcshoz hozzáadott utolsó fiókot használja. A WebAuthN lehetővé teszi a felhasználók számára, hogy kiválasszák a használni kívánt fiókot.
- A Windows 10 1809-es verzióját futtató eszközök zárolásának feloldása. A legjobb élmény érdekében használja a Windows 10 1903-as vagy újabb verzióját.

## <a name="prepare-devices-for-preview"></a>Eszközök előkészítése előnézetre

Az Azure AD-hez csatlakozott eszközök, amelyek a funkció előnézete során a windows 10-es 1809-es vagy újabb verziót kell futtatnia. A legjobb élmény a Windows 10 1903-as vagy újabb verziójában érhető el.

A hibrid Azure AD-hez csatlakozó eszközöknek windows 10 Insider Build 18945 vagy újabb rendszert kell futtatniuk.

## <a name="enable-security-keys-for-windows-sign-in"></a>Biztonsági kulcsok engedélyezése a Windows bejelentkezéshez

A szervezetek dönthetnek úgy, hogy az alábbi módszerek közül egyet vagy többet használnak a biztonsági kulcsok használatának engedélyezéséhez a Windows bejelentkezéshez a szervezet követelményei alapján:

- [Engedélyezés az Intune-nal](#enable-with-intune)
- [Célzott Intune-telepítés](#targeted-intune-deployment)
- [Engedélyezés kiépítési csomaggal](#enable-with-a-provisioning-package)
- [Engedélyezés csoportházirenddel (csak hibrid Azure AD-hez csatlakozott eszközök esetén)](#enable-with-group-policy)

> [!IMPORTANT]
> A **hibrid Azure AD-hez csatlakozó eszközökkel** rendelkező szervezeteknek **is** el kell végezniük a [fido2-hitelesítés engedélyezése a helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md) című cikkben leírt lépéseket, mielőtt a Windows 10 FIDO2 biztonságikulcs-hitelesítés működik.
>
> Az **Azure AD-hez csatlakozó eszközökkel** rendelkező szervezeteknek ezt kell megtenniük, mielőtt az eszközeik a FIDO2 biztonsági kulcsokkal hitelesíthetik magukat a helyszíni erőforrásokban.

### <a name="enable-with-intune"></a>Engedélyezés az Intune-nal

A biztonsági kulcsok Intune használatával történő használatának engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Tallózással keresse meg a Microsoft > **Intune-eszközigénylési** > **Windows-igénylési** > **Windows Hello for Business** > **tulajdonságai .** **Microsoft Intune**
1. A **Beállítások csoportban**állítsa be a **Biztonsági kulcsok használata a bejelentkezéshez** **beállítást Engedélyezve értékre.**

A bejelentkezéshez szükséges biztonsági kulcsok konfigurálása nem függ a Windows Hello vállalati verzió konfigurálásától.

### <a name="targeted-intune-deployment"></a>Célzott Intune-telepítés

Ha adott eszközcsoportokat szeretne megcélozni a hitelesítő adatszolgáltató engedélyezéséhez, használja a következő egyéni beállításokat az Intune-on keresztül:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Tallózással keresse meg a **Microsoft Intune-eszköz** > **konfigurációs** > **profilok** > **profil létrehozása című sablont.**
1. Konfigurálja az új profilt a következő beállításokkal:
   - Név: Biztonsági kulcsok a Windows bejelentkezéshez
   - Leírás: Engedélyezi a FIDO biztonsági kulcsok windowsos bejelentkezéssorán való használatát
   - Platform: Windows 10 és újabb
   - Profiltípusa: Egyéni
   - Egyéni OMA-URI beállítások:
      - Név: A FIDO biztonsági kulcsainak bekapcsolása a Windows bejelentkezéshez
      - OMA-URI: ./Eszköz/Szállító/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Adattípus: Egész szám
      - Érték: 1
1. Ez a házirend adott felhasználókhoz, eszközökhöz vagy csoportokhoz rendelhető. További információt a [Felhasználói és eszközprofilok hozzárendelése a Microsoft Intune-ban című témakörben talál.](https://docs.microsoft.com/intune/device-profile-assign)

![Az Intune egyéni eszközkonfigurációs házirendjének létrehozása](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Engedélyezés kiépítési csomaggal

Az Intune által nem felügyelt eszközök esetében a funkció engedélyezéséhez létesítési csomag telepíthető. A Windows Configuration Designer alkalmazás a [Microsoft Store-ból](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)telepíthető. A kiépítési csomag létrehozásához hajtsa végre az alábbi lépéseket:

1. Indítsa el a Windows konfigurációtervezőt.
1. Válassza **az** > **Új fájl projekt lehetőséget.**
1. Adjon nevet a projektnek, és jegyezze fel a projekt létrehozásának útvonalát, majd válassza a **Tovább**gombot.
1. Hagyja, hogy *a Kiépítési csomag* kijelölve **a Kijelölt projekt munkafolyamata,** és válassza a **Tovább**lehetőséget.
1. Válassza *az Összes Windows asztali kiadás* lehetőséget a **Megtekinteni és konfigurálni kívánt beállítások kiválasztása**csoportban, majd válassza a **Tovább**gombot.
1. Válassza a **Finish** (Befejezés) elemet.
1. Az újonnan létrehozott projektben keresse meg a**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn** **futásidejű beállításait.** > 
1. Állítsa **a UseSecurityKeyForSignIn** értéket *engedélyezve.*
1. Válassza a Kiépítési csomag **exportálása** > **lehetőséget.**
1. Hagyja az alapértelmezett értékeket a **Build** ablakban **a Kiépítési csomag leírása**csoportban, majd válassza a **Tovább**gombot.
1. Hagyja az alapértelmezett értékeket a **Build** ablakban **a Biztonsági részletek kiválasztása a létesítési csomaghoz,** és válassza a **Tovább**gombot.
1. Vegye figyelembe vagy módosítsa az elérési utat a **Build** ablakok csoportban **Válassza ki, hová szeretné menteni a kiépítési csomagot,** és válassza a **Tovább**gombot.
1. Válassza a Build a létesítési csomag létrehozása lapon válassza a **Build.Select** on **the Build the létesítési csomag** lapon.
1. Mentse a két létrehozott fájlokat *(ppkg* és *macska)* egy olyan helyre, ahol később alkalmazhatja őket a gépekre.
1. A létrehozott kiépítési csomag alkalmazásáról a [Kiépítési csomag alkalmazása](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)című témakörben jelenik meg.

> [!NOTE]
> A Windows 10 1809-es verzióját futtató eszközöknek engedélyezniük kell a megosztott PC módot is (*EnableSharedPCMode*). A funkció engedélyezéséről további információt A [megosztott vagy vendég számítógép beállítása windows 10-nel](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)című témakörben talál.

### <a name="enable-with-group-policy"></a>Engedélyezés csoportházirenddel

Hibrid **Azure AD-hez csatlakozó eszközök**esetén a szervezetek a következő csoportházirend-beállítást konfigurálhatják a FIDO biztonsági kulcs bejelentkezésének engedélyezéséhez. A**System** > beállítás a **Számítógép konfigurációs** > **felügyeleti sablonok** > **rendszerbejelentkezés** > i beállításaiközött**található:**

- Ha a házirendet **engedélyezve** van, a felhasználók biztonsági kulcsokkal jelentkeznek be.
- Ha ezt a házirendet **Letiltott** vagy nincs konfigurálva beállításra **állítja,** a felhasználók nem jelentkezhetnek be biztonsági kulcsokkal.

Ehhez a csoportházirend-beállításhoz `credentialprovider.admx` a csoportházirend-sablon frissített verziója szükséges. Ez az új sablon a Windows Server következő verziójával és a Windows 10 20H1 rendszerrel érhető el. Ez a beállítás a Windows újabb verzióinak egyikét futtató eszközzel vagy központilag kezelhető a támogatási témakörben, [a Központi áruház létrehozása és kezelése a Windows csoportházirend-felügyeleti sablonjaihoz című témakörben.](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)

## <a name="sign-in-with-fido2-security-key"></a>Bejelentkezés fido2 biztonsági kulccsal

Az alábbi példában egy Bala Sandhu nevű felhasználó már kiépítette a FIDO2 biztonsági kulcsát az előző, Jelszó nélküli biztonsági kulcs bejelentkezés engedélyezése című [cikkben](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)leírt lépésekkel. Hibrid Azure AD-hez csatlakozó eszközök esetén győződjön meg arról is, hogy [jelszó nélküli biztonsági kulcs-bejelentkezést engedélyezett a helyszíni erőforrásokba.](howto-authentication-passwordless-security-key-on-premises.md) Bala kiválaszthatja a biztonsági kulcs hitelesítő adatait a Windows 10 zárolási képernyőjéről, és beillesztheti a biztonsági kulcsot a Windows rendszerbe való bejelentkezéshez.

![Biztonsági kulcs bejelentkezésa a Windows 10 zárolási képernyőjén](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>A biztonsági kulcs biometriai, PIN-kód- vagy alaphelyzetbe állítása

* Windows 10 1903-as vagy újabb verzió
   * A felhasználók megnyithatják a **Windows beállításait** az eszközükön > **fiókok** > **biztonsági kulcsa**
   * A felhasználók módosíthatják PIN-kódjukat, frissíthetik a biometrikus adatokat, vagy alaphelyzetbe állíthatják biztonsági kulcsukat

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha a szolgáltatás megtekintése közben szeretné megosztani a visszajelzéseket, vagy problémákba ütközik, az alábbi lépésekkel megoszthatja a Windows Visszajelzési központ alkalmazáson keresztül:

1. Indítsa el **a Visszajelzési központot,** és győződjön meg arról, hogy be van jelentkezve.
1. Visszajelzés küldése a következő kategorizálás alatt:
   - Kategória: Biztonság és adatvédelem
   - Alkategória: FIDO
1. A naplók rögzítéséhez használja a **probléma újbóli létrehozását**

## <a name="next-steps"></a>További lépések

[Hozzáférés engedélyezése a helyszíni erőforrásokhoz az Azure AD és a hibrid Azure AD-hez csatlakozó eszközök számára](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközregisztrációról](../devices/overview.md)

[További információ az Azure többtényezős hitelesítéséről](../authentication/howto-mfa-getstarted.md)
