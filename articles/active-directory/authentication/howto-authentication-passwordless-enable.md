---
title: (Előzetes verzió) beállításának bejelentkezés Azure Active Directory konfigurálása
description: Az Azure ad-bA FIDO2 biztonsági hitelesítő adatok vagy a Microsoft Authenticator alkalmazás (előzetes verzió) beállításának bejelentkezés engedélyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712071"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Az Azure ad-ben (előzetes verzió) beállításának bejelentkezés engedélyezése

## <a name="requirements"></a>Követelmények

* Azure Multi-Factor Authentication
* Kombinált regisztrációs előzetes verzió
* Biztonsági kulcs előzetes FIDO2 kompatibilis FIDO2 biztonsági kulcsra van szükség.
* WebAuthN igényel a Microsoft Edge a Windows 10-es 1809 vagy újabb verzió
* Alapján Windows jelentkezzen be az Azure AD szükséges FIDO2 csatlakoztatott Windows 10-es 1809 vagy újabb verzió

## <a name="prepare-devices-for-preview"></a>Eszközök előzetes előkészítése

Eszközöket, amelyek az ügyfélteszteléssel futnia kell a Windows 10-es verzió 1809 vagy újabb verziója. A legjobb élményt 1903 vagy újabb verziójú Windows 10-be van kapcsolva.

## <a name="enable-security-keys-for-windows-sign-in"></a>A Windows bejelentkezési biztonsági kulcsok engedélyezése

Szervezet dönthet elvégzéséhez, vagy jelentkezzen be az alábbi módszerekkel ahhoz, hogy a biztonsági kulcsok használatát a Windows.

### <a name="enable-credential-provider-via-intune"></a>Intune-on keresztül a hitelesítőadat-szolgáltató engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **a Microsoft Intune** > **eszközregisztráció** > **Windows regisztrációs** > **Windows Hello a vállalati** > **tulajdonságok**.
1. A **beállítások** beállítása **biztonsági kulcsok használata a bejelentkezéshez** való **engedélyezve**.

A bejelentkezéshez, a biztonsági kulcsok konfigurálása nem függ a Windows Hello for Business konfigurálása.

#### <a name="enable-targeted-intune-deployment"></a>Célzott Intune üzembe helyezésének engedélyezése

Cél ahhoz, hogy a hitelesítőadat-szolgáltató megfelelő eszközcsoportok, használja a következő egyéni beállítások az Intune-on keresztül. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **a Microsoft Intune** > **eszközkonfiguráció** > **profilok** > **profillétrehozása**.
1. Az új profil konfigurálása a következő beállításokkal
   1. Név: A Windows bejelentkezési biztonsági kulcsok
   1. Leírás: Lehetővé teszi, hogy a Windows bejelentkezési során használandó FIDO biztonsági kulcsok
   1. Platform: Windows 10 és újabb verziók
   1. Platform típusa: Egyéni
   1. Egyéni OMA-URI beállítások:
      1. Név: FIDO biztonsági kulcs kapcsolja be a Windows-bejelentkezés
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Adattípus: Egész szám
      1. Érték: 1 
1. Ez a szabályzat adott felhasználók, eszközök és csoportok is hozzárendelhető. További információ a cikkben található [hozzárendelése a felhasználói és eszközprofilok a Microsoft Intune-ban](https://docs.microsoft.com/intune/device-profile-assign).

![Az Intune egyéni konfigurációs szabályzat létrehozása](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Kiépítési csomag keresztül a hitelesítőadat-szolgáltató engedélyezése

Az Intune által nem felügyelt eszközök esetén a kiépítési csomag funkcióinak is telepíthető. A Windows Configuration Designerben telepíthető a [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Indítsa el a Windows Configuration Designer.
1. Válassza ki **fájl** > **új projekt**.
1. Adja meg a projekt nevét, és jegyezze fel azokat, az elérési utat, ahol a projekt létrejön.
1. Kattintson a **Tovább** gombra.
1. Hagyja **kiépítési csomag** érték van kiválasztva a a **kijelölt projekt munkafolyamat** válassza **tovább**.
1. Válassza ki **minden Windows asztali kiadások** alatt **mely beállítások megtekintéséhez és konfigurálásához válassza** válassza **tovább**.
1. Válassza a **Finish** (Befejezés) elemet.
1. Az újonnan létrehozott projektben keresse meg a **futásidejű beállításokat** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Állítsa be **UseSecurityKeyForSignIn** való **engedélyezve**.
1. Válassza ki **exportálása** > **kiépítési csomag**
1. Meghagyhatja az alapértelmezett beállításokat, az a **hozhat létre** időszak alatt **ismertetik a kiépítési csomagot** válassza **tovább**.
1. Meghagyhatja az alapértelmezett beállításokat, az a **hozhat létre** időszak alatt **válassza ki a kiépítési csomagot vonatkozó biztonsági információkat** válassza **tovább**.
1. Jegyezze fel, vagy módosítsa az elérési utat a **hozhat létre** windows alatt **válassza ki a kiépítési csomag mentési helyének** válassza **tovább**.
1. Válassza ki **hozhat létre** a a **a kiépítési csomagot készíthet** lapot.
1. Mentse a két létrehozott fájlok (ppkg és cat) egy olyan helyre, ahol alkalmazhatja őket gépekhez később.
1. Kövesse a cikk útmutatást [kiépítési csomag alkalmazása](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), hogy a kiépítési csomagot létrehozta a alkalmazni.

## <a name="obtain-fido2-security-keys"></a>FIDO2 biztonsági kulcsok beszerzése

: A cikk szakasz FIDO2 biztonsági kulcsait, [beállításának mi?](concept-authentication-passwordless.md) támogatott kulcsok és a gyártók további információt.

> [!NOTE]
> Ha vásárol, és bizonyos NFC-alapú biztonsági kulcsok használatához szüksége lesz egy támogatott NFC-olvasó.

## <a name="enable-passwordless-authentication-methods"></a>Beállításának hitelesítési módszerek engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztrációs felület engedélyezéséhez

Eszközregisztrációs szolgáltatások FIDO2 biztonsági kulcsok esetén az egyesített regisztrációs előzetes támaszkodnak. A kombinált regisztrációs előzetes verziójának engedélyezése az alábbi lépésekkel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Keresse meg a **az Azure Active Directory** > **felhasználói beállítások**
   1. Kattintson a **hozzáférési panel előzetes verziójú funkciók beállításainak kezelése**
   1. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálása és biztonsági adatai – továbbfejlesztett kezelésének**.
      1. Válasszon **kijelölt** , és válassza ki a csoport számára, aki részt fog venni az előzetes verzióban érhető el.
      1. Vagy válasszon **összes** ahhoz, hogy a címtár minden tagja számára.
1. Kattintson a **Save** (Mentés) gombra

### <a name="enable-new-passwordless-authentication-methods"></a>Új beállításának hitelesítési módszerek engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Keresse meg a **Azure Active Directory** > **hitelesítési módszerek** > **hitelesítési módszer házirend (előzetes verzió)**
1. Minden **metódus**, a következő beállítások
   1. **Engedélyezése** – igen / nem
   1. **Cél** – összes felhasználó vagy a kiválasztott felhasználók
1. **Mentés** az egyes módszerek

> [!WARNING]
> A "Kulcs a szoftverkorlátozó házirendek" nem működnek még FIDO2. Ez a funkció általános elérhetősége előtt, kérjük, ne módosítsa ezeket a szabályzatokat az alapértelmezett.

> [!NOTE]
> Nem kell mind a beállításának módszerek engedélyezése (előzetes verzió csak egy beállításának módszert szeretné, ha engedélyezheti csak ez a módszer). Javasoljuk, hogy próbálja ki a két módszert, mivel mindkettő saját előnyeit.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Felhasználói regisztráció és felügyeleti FIDO2 biztonsági kulcsok

1. Keresse meg a [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Jelentkezzen, ha még nem már
1. Kattintson a **biztonsági adatai**
   1. A felhasználónak már van regisztrálva legalább egy Azure multi-factor Authentication módszer, ha azokat közvetlenül regisztrálhatja FIDO2 biztonsági kulcs.
   1. Ha nem rendelkeznek legalább egy Azure multi-factor Authentication módszer regisztrált, hozzá kell egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot kattintva **metódus hozzáadása** kiválasztása és **biztonsági kulcs**
1. Válasszon **USB-eszköz** vagy **NFC-eszköz**
1. Készen áll, és válassza ki a kulcsot **tovább**
1. Egy mező fogja jelennek meg, és megkérjük, hogy hozzon létre vagy adja meg a PIN-kód a biztonsági kulcs, majd a szükséges hitelesítési módok végrehajtását a kulcshoz biometrikus vagy touch.
1. Ön visszaadott az egyesített regisztrációs felhasználói felületre és meg kell adnia egy kifejező nevet a jogkivonatot, így ha több azonosíthatja melyiket fogja. Kattintson a **Tovább** gombra.
1. Kattintson a **kész** befejezése

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Biztonsági kulcs biometrikus, PIN-kód, kezelése vagy a biztonsági kulcs visszaállítása

* A Windows 10-es verzió 1809
   * A biztonsági kulcs szállítótól kiegészítő szoftver megadása kötelező
* A Windows 10-es 1903 vagy újabb verzió
   * Felhasználó meg tudja nyitni **Windows beállítások** az eszköz > **fiókok** > **biztonsági kulcs**
   * A felhasználók a PIN, biometrika, illetve a biztonsági kulcs visszaállítása

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Felhasználói regisztráció és a Microsoft Authenticator alkalmazás kezelése

A Microsoft Authenticator alkalmazást a telefonos bejelentkezés konfigurálásához kövesse a cikk útmutatást [jelentkezzen be a Microsoft Authenticator alkalmazás használata a fiókok](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Jelentkezzen be beállításának hitelesítő adataival

### <a name="sign-in-at-the-lock-screen"></a>Jelentkezzen be a zárolási képernyőn

A felhasználó az alábbi példában Bala Sandhu már kiépített azok FIDO2 biztonsági kulcs. Bala is válassza ki a biztonsági kulcs hitelesítőadat-szolgáltató a Windows 10-es zárolási képernyőről, és jelentkezzen be a Windows, a biztonsági kulcsot.

![A Windows 10-es zárolási képernyőn jelentkezzen be biztonsági kulcs](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Jelentkezzen be a weben

A felhasználó az alábbi példában már osztott azok FIDO2 biztonsági kulcs. A felhasználó jelentkezzen be a weben azok FIDO2 biztonsági kulcs a Windows 10-es 1809 vagy újabb verzióját a Microsoft Edge böngésző belül lehet váltani.

![Biztonsági kulcs bejelentkezés a Microsoft Edge-ben](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Bejelentkezés a Microsoft Authenticator alkalmazással kapcsolatos információkat lásd: a cikk [jelentkezzen be a Microsoft Authenticator alkalmazás használata a fiókok](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Ismert problémák

### <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

#### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítése

Rendszergazda üzembe helyezést és megszüntetést biztonsági kulcsok nem érhető el a nyilvános előzetes verzióban érhető el.

#### <a name="hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás

WIA SSO függő felügyelt hitelesítő adatokat használó felhasználók például FIDO2 biztonsági kulcsait, vagy beállításának jelentkezzen be a Microsoft Authenticator alkalmazás hibrid csatlakozniuk kell a Windows 10-es egyszeri Bejelentkezéssel kihasználása érdekében. Azonban a biztonsági kulcsok az Azure Active Directoryhoz csatlakoztatott gépek egyelőre csak munkahelyi. Azt javasoljuk, hogy csak kipróbálhatja FIDO2 biztonsági kulcsok az Azure Active Directoryhoz csatlakoztatott gépeken tiszta Windows zárolási képernyőjén. Ez a korlátozás nem vonatkozik a webes.

#### <a name="upn-changes"></a>Egyszerű felhasználónév módosítások

Dolgozunk a szolgáltatás lehetővé teszi a hibrid AADJ UPN változás- és AADJ eszközök támogatása. Ha módosítja egy felhasználó egyszerű Felhasználónevét, FIDO2 biztonsági kulcsok a fiók számára, amely már nem módosíthatja. Így az egyetlen módszer visszaállítani az eszközt, és a felhasználó rendelkezik az újbóli regisztrációt.

### <a name="authenticator-app"></a>Hitelesítő alkalmazás

#### <a name="ad-fs-integration"></a>AD FS-integráció

Amikor egy felhasználó engedélyezte a Microsoft Authenticator beállításának hitelesítő adatokat, az adott felhasználó hitelesítési mindig alapértelmezés szerint egy jóváhagyási értesítést küld. A logikai meggátolja a felhasználókat a hibrid bérlő irányítja az ADFS-be a bejelentkezés ellenőrzésének anélkül, hogy a felhasználó egy további lépést tart kattintson a "Inkább jelszó használata." Ez a folyamat is megkerülik bármilyen helyszíni feltételes hozzáférési szabályzatokat, és átmenő hitelesítési folyamatok. A kivétel ez alól egy login_hint-e megadva, a felhasználó autoforwarded az AD FS-hez, és megkerülése a beállításának hitelesítő adatok használatát.

#### <a name="azure-mfa-server"></a>Az Azure MFA-kiszolgáló

Végfelhasználók, akik az MFA engedélyezve vannak a szervezet helyszíni Azure MFA-kiszolgálón keresztül továbbra is létrehozhat és egyetlen beállításának telefon bejelentkezési hitelesítő adatok használata. Ha a felhasználó megpróbálja több telepített (5 +) frissítése a Microsoft Authenticator a hitelesítő adataival, a módosítás hibát eredményezhet.  

#### <a name="device-registration"></a>Eszközregisztráció

Az előfeltételeket az új, erős hitelesítő adat, létrehozása egyik célja, hogy hol vannak az eszköz regisztrálva van-e belül az Azure AD-bérlővel, egy adott felhasználóhoz. Eszköz regisztrációs korlátozások miatt egy eszközhöz csak egy bérlőben lehet regisztrálni. Ezt a korlátot, az azt jelenti, hogy csak egy munkahelyi vagy iskolai fiókkal, a Microsoft Authenticator alkalmazást a telefonos bejelentkezés esetén is engedélyezhető.

## <a name="next-steps"></a>További lépések

[Ismerje meg az eszköz regisztrálása](../devices/overview.md)

[További tudnivalók az Azure multi-factor Authentication](../authentication/howto-mfa-getstarted.md)
