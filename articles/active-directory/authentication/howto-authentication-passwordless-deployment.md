---
title: Jelszó nélküli hitelesítési telepítés megtervezése az Azure AD-vel
description: Az Azure Active Directory jelszó nélküli hitelesítési implementációjának megtervezése és üzembe helyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ed549e51b911452bca7d4d4a16c7ef45594a8f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451431"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Jelszó nélküli hitelesítési telepítés megtervezése az Azure Active Directoryban

> [!NOTE]
> A központi telepítési terv offline verziójának létrehozásához használja a böngésző Nyomtatás PDF formátumra funkcióját.

A legtöbb kibertámadás feltört felhasználónévvel és jelszóval kezdődik. A szervezetek úgy próbálják meg ellensúlyozni a fenyegetést, hogy a felhasználóknak az alábbi módszerek egyikét kell használniuk:

- Hosszú jelszavak
- Összetett jelszavak
- Gyakori jelszómódosítások
- Multi-factor authentication (MFA)

A Microsoft [kutatásai azt mutatják,](https://aka.ms/passwordguidance) hogy ezek az erőfeszítések bosszantják a felhasználókat, és megfokozzák a támogatási költségeket. További információ: [A Pa $$word nem számít](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>A jelszó nélküli hitelesítés előnyei

- **Fokozott biztonság**. Csökkentse az adathalászat és a jelszószórási támadások kockázatát a jelszavak támadási felületként való eltávolításával.
-  **Jobb felhasználói élmény**. Kényelmes módot biztosít a felhasználóknak az adatok bárhonnan való elérésére. Egyszerű hozzáférést biztosít hathatós alkalmazásokhoz és szolgáltatásokhoz, például az Outlookhoz, a OneDrive-hoz vagy az Office-hoz mobilon.
-  **Erőteljes betekintést**. A hatékony naplózásés naplózás segítségével betekintést nyerhet a felhasználók jelszó nélküli tevékenységébe.

A jelszó nélküli, a jelszó helyébe valami van, plusz valami, ami vagy, vagy valami, amit tud. A Windows Hello vállalati verzió például használhat biometrikus kézmozdulatokat, például arcot vagy ujjlenyomatot, vagy olyan eszközspecifikus PIN-kódot, amelyet nem továbbít a hálózaton keresztül.

## <a name="passwordless-authentication-methods"></a>Jelszó nélküli hitelesítési módszerek
A Microsoft három jelszó nélküli hitelesítési lehetőséget kínál, amelyek számos forgatókönyvre vonatkoznak. Ezek a módszerek párhuzamosan használhatók:

- [A Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) a legalkalmasbb a dedikált Windows-számítógépekfelhasználói számára.
- A [fido2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) biztonsági kulcsokkal történő biztonsági kulcsbejelentkezés különösen hasznos azoknak a felhasználóknak, akik megosztott gépekre, például kioszkokra jelentkeznek be, olyan helyzetekben, ahol a telefonok használata korlátozott, valamint magas jogosultsági szintű identitások esetén.
- A [Microsoft Authenticator alkalmazással](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) történő telefonos bejelentkezés akkor hasznos, ha jelszó nélküli lehetőséget biztosít a mobileszközökkel rendelkező felhasználóknak. A Hitelesítő alkalmazás bármely iOS vagy Android telefont erős, jelszó nélküli hitelesítő adattá alakít, lehetővé téve a felhasználók számára, hogy bármilyen platformra vagy böngészőbe bejelentkezjenek. A felhasználók úgy jelentkeznek be, hogy értesítést kapnak a telefonjukra, amely megfelel a képernyőn megjelenő számnak a telefonjukon lévőszámnak, majd biometrikus adataikkal vagy PIN-kódjukkal megerősítik.

### <a name="passwordless-authentication-scenarios"></a>Jelszó nélküli hitelesítési forgatókönyvek

A Microsoft jelszó nélküli hitelesítési módszerei különböző forgatókönyveket tesznek lehetővé. Vegye figyelembe a szervezeti igényeket, előfeltételeket és az egyes hitelesítési módszerek képességeit a jelszó nélküli hitelesítési stratégia kiválasztásához. Azt javasoljuk, hogy minden Windows 10-es eszközt használó szervezet használja a Windows Hello vállalati verziót. Ezután adja hozzá a telefonos bejelentkezést (a Microsoft Authenticator alkalmazással) vagy biztonsági kulcsokat további esetekhez.

| Forgatókönyv | Telefonos hitelesítés | Biztonsági kulcsok | Vállalati Windows Hello |
| --- | --- | --- | --- |
| **Számítógép bejelentkezés:** <br> Hozzárendelt Windows 10-es eszközről | **Nem** | **Igen** <br> Biometrikus, PIN-kód | **Igen**<br>biometrikus felismeréssel és VAGY PIN-kóddal |
| **Számítógép bejelentkezés:** <br> Megosztott Windows 10-eszközről | **Nem** | **Igen** <br> Biometrikus, PIN-kód  | **Nem** |
| **Webalkalmazás-bejelentkezés**: <br>felhasználó által létrehozott számítógépről | **Igen** | **Igen** <br> Feltéve, hogy az alkalmazásokba való egyszeri bejelentkezést a számítógép bejelentkezése engedélyezi | **Igen**<br> Feltéve, hogy az alkalmazásokba való egyszeri bejelentkezést a számítógép bejelentkezése engedélyezi |
| **Webalkalmazás-bejelentkezés**: <br> mobil vagy nem windowsos eszközről | **Igen** | **Nem** | **Nem** |
| **Számítógép bejelentkezés:** <br> Nem Windows rendszerű számítógép | **Nem** | **Nem** | **Nem** |

A szervezet számára legmegfelelőbb módszer kiválasztásáról a [Jelszó nélküli módszer kiválasztása](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

A szervezeteknek a jelszó nélküli telepítés megkezdése előtt meg kell felelniük az alábbi előfeltételeknek:

| Előfeltétel | Hitelesítő alkalmazás | FIDO2 biztonsági kulcsok |
| --- | --- | --- |
| [Az Azure többtényezős hitelesítésének és az önkiszolgáló jelszó-visszaállításnak (SSPR) kombinált regisztrációja](howto-registration-mfa-sspr-combined.md) engedélyezve van | √ | √ |
| [A felhasználók azure-os többtényezős hitelesítést végezhetnek](howto-mfa-getstarted.md) | √ | √ |
| [A felhasználók regisztráltak az Azure többtényezős hitelesítésére és az SSPR-re](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [A felhasználók regisztrálták mobileszközeiket az Azure Active Directoryba](../devices/overview.md) | √ |   |
| Windows 10-es vagy újabb verzió egy támogatott böngészővel, például a Microsoft Edge-el vagy a Mozilla Firefox-tal <br> (67-es vagy újabb verzió). <br> *A Microsoft az 1903-as vagy újabb verziót ajánlja a natív támogatáshoz.* |   | √ |
| Kompatibilis FIDO2 biztonsági kulcsok. Győződjön meg arról, hogy [microsoftáltal tesztelt és ellenőrzött](howto-authentication-passwordless-enable.md) FIDO2 biztonsági eszközt vagy más kompatibilis FIDO2 biztonsági eszközt használ. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>A Windows Hello vállalati verzió előfeltételei

A Windows Hello előfeltételei nagymértékben függenek attól, hogy helyszíni, hibrid vagy csak felhőalapú konfigurációban telepít-e. További információt a [Windows Hello vállalati verzió előfeltételeinek teljes listájában talál.](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

A felhasználók regisztrálják jelszó nélküli módszerüket az Azure többtényezős hitelesítésregisztrációs folyamatának részeként. A felhasználónévvel és jelszóval, valamint egy másik regisztrált módszerrel történő többtényezős hitelesítés tartalékként használható arra az esetre, ha bizonyos esetekben nem tudják használni a telefonjukat vagy a biztonsági kulcsukat.

### <a name="licensing"></a>Licencek 
A jelszó nélküli hitelesítésért nincs további költség, bár egyes előfeltételek prémium szintű előfizetést igényelhetnek. Részletes szolgáltatás- és licencelési információkért az [Azure Active Directory licencelési lapján.](https://azure.microsoft.com/pricing/details/active-directory/) 

## <a name="develop-a-plan"></a>Terv kidolgozása

Vegye figyelembe az üzleti igényeket és az egyes hitelesítési módszerek használati eseteit. Ezután válassza ki az igényeinek leginkább megfelelő módszert.

### <a name="use-cases"></a>Használati esetek

Az alábbi táblázat a projekt során megvalósítandó használati eseteket ismerteti.

| Terület | Leírás |
| --- | --- |
| **Hozzáférés** | A jelszó nélküli bejelentkezés a vállalati hálózaton belüli vagy kívüli vállalati vagy személyes eszközről érhető el. |
| **Naplózás** | A használati adatok közel valós idejű naplózásra a rendszergazdák rendelkezésére állnak. <br> A használati adatok legalább 29 naponta letöltődnek a vállalati rendszerekbe, vagy SIEM eszközt használnak. |
| **Szabályozás** | A megfelelő hitelesítési módszerhez és a kapcsolódó csoportokhoz való felhasználói hozzárendelések életciklusa meghatározásra és figyelésre kerül. |
| **Biztonság** | A megfelelő hitelesítési módszerhez való hozzáférést felhasználói és csoportos hozzárendelések szabályozzák. <br> Csak a jogosult felhasználók használhatják jelszó nélküli bejelentkezést. |
| **Teljesítmény** | Az access-hozzárendelések propagálási ütemterveit dokumentálja és figyeli. <br> A bejelentkezési időket a könnyű használat érdekében mérjük. |
| **Felhasználói élmény** | A felhasználók tisztában vannak a mobilkompatibilitással. <br> A felhasználók beállíthatják a Hitelesítő alkalmazást jelszó nélküli bejelentkezéshez. |
| **Támogatás** | A felhasználók tisztában vannak azzal, hogyan találhatják meg a jelszó nélküli bejelentkezési problémák támogatását. |

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek sikertelenek, az általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt van. A buktatókat elkerülve [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be,](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) és hogy az érdekelt felek szerepe idomulanak a projekthez.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon közölje, hogyan változik a felhasználói élmény, mikor változik, és hogyan szerezhet támogatást, ha problémákat tapasztalnak.

A végfelhasználókfelé irányuló kommunikációnak a következő információkat kell tartalmaznia:

- [A kombinált biztonsági regisztrációs élmény engedélyezése](howto-authentication-passwordless-phone.md)
- [A Microsoft Authenticator alkalmazás letöltése](../user-help/user-help-auth-app-download-install.md)
- [Regisztráció a Microsoft Hitelesítő alkalmazásban](howto-authentication-passwordless-phone.md)
- [Bejelentkezés a telefonnal](../user-help/user-help-auth-app-sign-in.md)

A Microsoft [többtényezős hitelesítésű kommunikációs sablonokat,](https://aka.ms/mfatemplates)Önkiszolgáló jelszó-visszaállítási (SSPR) [kommunikációs sablonokat](https://www.microsoft.com/download/details.aspx?id=56768)és [végfelhasználói dokumentációt](../user-help/security-info-setup-signin.md) biztosít a kommunikáció elkészítéséhez. A felhasználókat [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) közvetlenül is elküldheti a regisztrációhoz, ha kiválasztja az adott oldalon található **Biztonsági adatok** hivatkozásokat.

### <a name="plan-to-pilot"></a>Terv a kísérleti

Jelszó nélküli hitelesítés telepítésekor először engedélyeznie kell egy vagy több kísérleti csoportot. Kifejezetten erre a célra [hozhat létre csoportokat.](../fundamentals/active-directory-groups-create-azure-portal.md) Adja hozzá a felhasználókat, akik részt vesznek a kísérleti a csoportok. Ezután engedélyezze az új jelszó nélküli hitelesítési módszereket a kiválasztott csoportokhoz.

A csoportok szinkronizálhatók egy helyszíni címtárból vagy az Azure AD-ből. Ha elégedett a próbaprojekt eredményeivel, bekapcsolhatja a jelszó nélküli hitelesítést az összes felhasználó számára.

Tekintse meg a kísérleti módszerek [gyakorlati tanácsait](https://aka.ms/deploymentplans) a telepítési tervek oldalon.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Jelszó nélküli hitelesítés megtervezése a Microsoft Authenticator alkalmazással

A Microsoft Authenticator alkalmazás ingyenesen letölthető a Google Playről vagy az Apple App Store áruházból. [További információ a Microsoft Authenticator alkalmazás letöltéséről.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) A felhasználók kalkulálják a Microsoft Authenticator alkalmazást. és kövesse az utasításokat a telefonos bejelentkezés engedélyezéséhez. 

Minden iOS vagy Android telefont erős, jelszó nélküli hitelesítő adattá alakít. A felhasználók úgy jelentkeznek be bármely platformra vagy böngészőbe, hogy értesítést kapnak a telefonjukra, amely megfelel a képernyőn megjelenő számnak a telefonjukon lévőszámnak, majd biometrikus vagy PIN-kód dal megerősítik. [A Microsoft Authenticator alkalmazás működéséről című témakörben talál részleteket.](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)

![bejelentkezés a Hitelesítő alkalmazással](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás technikai szempontjai

**AD FS-integráció** – Ha a felhasználó engedélyezi a Microsoft Hitelesítő jelszó nélküli hitelesítő adatait, a felhasználó hitelesítése alapértelmezés szerint jóváhagyásra értesítést küld. A hibrid bérlő felhasználói nem irányítják az ADFS-be a bejelentkezéshez, kivéve, ha a "Jelszó használata helyette" lehetőséget választják. Ez a folyamat megkerüli a helyszíni feltételes hozzáférési házirendeket és az átmenő hitelesítési folyamatokat is. Ha azonban meg van adva egy *login_hint,* a rendszer továbbítja a felhasználót az ADFS-hez, és megkerüli a jelszó nélküli hitelesítő adatok használatának lehetőségét.

**Azure többtényezős hitelesítési kiszolgáló** – A végfelhasználók számára engedélyezett a többtényezős hitelesítés a szervezet helyszíni Azure MFA-kiszolgáló hozhat létre, és használja az egyetlen jelszó nélküli telefon bejelentkezési hitelesítő adatokat. Ha a felhasználó a Microsoft Authenticator több (legalább 5) telepítését próbálja frissíteni a hitelesítő adatokkal, ez a módosítás hibát okozhat.

**Eszközregisztráció** – a Hitelesítő alkalmazás jelszó nélküli hitelesítéshez való használatához az eszköz regisztrálva kell lennie az Azure AD-bérlőben, és nem lehet megosztott eszköz. Egy eszköz csak egyetlen bérlőben regisztrálható. Ez a korlát azt jelenti, hogy csak egy munkahelyi vagy iskolai fiók támogatott telefonos bejelentkezéshez az Authenticator alkalmazással.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Jelszó nélküli hitelesítés megtervezése FIDO2 biztonsági kulcsokkal
A jelszó nélküli bejelentkezési telepítések három típusa érhető el biztonsági kulcsokkal:

-    Azure Active Directory-webalkalmazások támogatott böngészőben
-    Az Azure Active Directoryhoz csatlakozott Windows 10-eszközök
-    Hibrid Azure Active Directorycsatlakozásos Windows 10-eszközök (előzetes verzió)
     -    Hozzáférést biztosít a felhőalapú és a helyszíni erőforrásokhoz. A helyszíni erőforrásokhoz való hozzáférésről további információt a helyszíni erőforrásokra való egyszeri használat [fidop2-kulcsokat használó egyszeri használatú erőforrásokhoz való szi-ban talál.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Engedélyeznie kell **a Kompatibilis FIDO2 biztonsági kulcsokat.** Mikroszkóp [jelentették be kulcs társas viszony -val FIDO2 kulcs eladó](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Azure AD-webalkalmazások és az Azure AD Windows-hoz csatlakozott eszközök:**

-    A Windows 10 1809-es vagy újabb verziója egy támogatott böngészővel, például a Microsoft Edge vagy a Mozilla Firefox (67-es vagy újabb verzió) használatával. 
-    A Windows 10 1809-es verziója támogatja a FIDO2 bejelentkezést, és szükség lehet a FIDO2 kulcs gyártójának szoftvertelepítésére. Az 1903-as vagy újabb verziót javasoljuk. 

**Hibrid Azure Active Directorytartományi csatlakozású eszközök esetén:** 
-    Windows 10 Insider build 18945 vagy újabb
-    Teljesen javított tartományi kiszolgálók Windows Server 2016 vagy 2019 rendszerrel.
-    Az Azure AD Connect legújabb verziója

A követelmények teljes listáját a [Jelszó nélküli biztonsági kulcs bejelentkezés engedélyezése windows 10-es eszközökre az Azure Active Directoryval című témakörben tartalmazza.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)


### <a name="security-key-life-cycle"></a>Biztonsági kulcs életciklusa

A biztonsági kulcsok lehetővé teszik az erőforrásokhoz való hozzáférést, és meg kell terveznie a fizikai eszközök kezelését.

1. **Kulcselosztás:** Tervezze meg, hogyan építhet ki kulcsokat a szervezethez. Előfordulhat, hogy egy központosított létesítési folyamat, vagy lehetővé teszi a végfelhasználók számára, hogy fido 2.0-kompatibilis kulcsokat vásárolni.
1. **Kulcsaktiválás**: A végfelhasználóknak saját maguknak kell aktiválniuk a biztonsági kulcsot. A végfelhasználók a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) második tényezőnél (PIN-kód vagy biometrikus) regisztrálják biztonsági kulcsaikat, és engedélyezik az első használatkor.
1. **Kulcs letiltása:** Bár a biztonsági kulcs funkciói az előzetes verzió fázisában vannak, a rendszergazda nem távolíthat el egy kulcsot a felhasználói fiókból. A felhasználónak el kell távolítania. Ha egy kulcs elvész vagy ellopják:
   1. Távolítsa el a felhasználót a jelszó nélküli hitelesítéshez engedélyezett bármely csoportból.
   1. Ellenőrizze, hogy eltávolították-e a kulcsot hitelesítési módszerként.
   1. Adjon ki egy új kulcsot. **Kulcscsere**: A felhasználók egyszerre két biztonsági kulcsot is engedélyezhetnek. Biztonsági kulcs cseréjekor győződjön meg arról, hogy a felhasználó eltávolította a lecserélt kulcsot is.

### <a name="enable-windows-10-support"></a>A Windows 10 támogatásának engedélyezése

A FIDO2 biztonsági kulcsokkal történő Windows 10-bejelentkezés engedélyezéséhez engedélyezni kell a hitelesítő adatok szolgáltatójának működését a Windows 10-ben. Válasszon egyet az alábbi lehetőségek közül:

- [Hitelesítő adatok szolgáltatójának engedélyezése az Intune-nal](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Az Intune központi telepítése az ajánlott beállítás.
- [Hitelesítő adatok szolgáltatójának engedélyezése kiépítési csomaggal](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Ha az Intune központi telepítése nem lehetséges, a rendszergazdáknak minden gépen telepíteniük kell egy csomagot a hitelesítő adatok szolgáltatójának működésének engedélyezéséhez. A csomag telepítése az alábbi lehetőségek egyikével végezhető el:
      - Csoportházirend vagy konfigurációkezelő
      - Helyi telepítés Windows 10-es gépen
- [Hitelesítő adatok szolgáltatójának engedélyezése csoportházirenddel](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Csak hibrid Azure AD-hez csatlakozott eszközök támogatott.

#### <a name="enable-on-premises-integration"></a>Helyszíni integráció engedélyezése

A helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez kövesse a [jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése a helyszíni erőforrásokba (előzetes verzió)](howto-authentication-passwordless-security-key-on-premises.md)című lépéseit.

> [!IMPORTANT]
> Ezeket a lépéseket is el kell végezni minden hibrid Azure AD-hez csatlakozó eszközök fido2 biztonsági kulcsok használata a Windows 10 bejelentkezéshez.

### <a name="register-security-keys"></a>Biztonsági kulcsok regisztrálása

A felhasználóknak regisztrálniuk kell a biztonsági kulcsot az Azure Active Directoryhoz csatlakozott Windows 10-es rendszerű gépeiken.

További információ: [Felhasználói regisztráció és a FIDO2 biztonsági kulcsok kezelése.](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)


## <a name="plan-auditing-security-and-testing"></a>Naplózás, biztonság és tesztelés tervezése
A szervezeti és megfelelőségi keretrendszereknek megfelelő naplózás megtervezése elengedhetetlen része a telepítésnek.

### <a name="auditing-passwordless"></a>Jelszó nélküli naplózás

Az Azure AD olyan jelentéseket tartalmaz, amelyek technikai és üzleti elemzéseket biztosítanak. A szervezet követelményei alapján a vállalati és a műszaki alkalmazások tulajdonosai vállalják a jelentések tulajdonjogát, és használják fel azokat.

Az Azure Active Directory portálon található **Hitelesítési** módszerek szakaszban a rendszergazdák engedélyezhetik és kezelhetik a jelszó nélküli hitelesítő adatok beállításait.

Az Azure AD bejegyzéseket ad hozzá a naplónaplókhoz, ha:

- A rendszergazda módosításokat hajt végre a Hitelesítési módszerek szakaszban.
- A felhasználó bármilyen módosítást hajt el a hitelesítő adataiban az Azure Active Directoryban.

Az alábbi táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre:

|   | A kockázatok kezelése | Növelje a termelékenységet | Cégirányítás és megfelelőség |
| --- | --- | --- | --- |
| **Jelentéstípusok** | Hitelesítési módszerek – kombinált biztonsági regisztrációra regisztrált felhasználók | Hitelesítési módszerek – alkalmazásértesítésre regisztrált felhasználók | Bejelentkezések: tekintse át, hogy ki és hogyan fér hozzá a bérlőhöz |
| **Lehetséges intézkedések** | A még nem regisztrált felhasználók megcélzása | A Microsoft Authenticator alkalmazás vagy biztonsági kulcsok bevezetésének ösztönzése | Hozzáférés visszavonása vagy további biztonsági házirendek kényszerítése a rendszergazdák számára |

**Az Azure AD 30 napig megőrzi a legtöbb naplózási adatot,** és elérhetővé teszi az adatokat az Azure Felügyeleti portálon vagy API-n keresztül, hogy letölthesse az elemző rendszerekbe. Ha hosszabb megőrzésre van szüksége, exportálja és használja a naplókat egy SIEM eszközben, például az [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk vagy Sumo Logic. [További információ a hozzáférési és használati jelentések megtekintéséről.](../reports-monitoring/overview-reports.md)

A felhasználók regisztrálhatják és kezelhetik hitelesítő adataikat a címre [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)navigálva. Ez a hivatkozás a végfelhasználói hitelesítő adatok kezeléséhez, amely az egyesített SSPR/többtényezős hitelesítési regisztrációs felületen keresztül engedélyezve lett. Az Azure AD naplózza a FIDO2 biztonsági eszközök regisztrációját, és a felhasználók hitelesítési módszereinek módosításait.

### <a name="plan-security"></a>A biztonság megtervezése
A bevezetési terv részeként a Microsoft azt javasolja, hogy a jelszó nélküli hitelesítés legyen engedélyezve az összes kiemelt jogosultságú rendszergazdai fiókhoz.

Amikor a felhasználók engedélyezik vagy letiltják a fiókot egy biztonsági kulcson, vagy alaphelyzetbe állítják a biztonsági kulcs második tényezőjét a Windows 10-es gépeiken, a rendszer egy bejegyzést ad hozzá a biztonsági naplóhoz, és a következő eseményazonosítók alatt áll: *4670* és *5382*.

### <a name="plan-testing"></a>Terv tesztelése

A forgatókönyvek tesztelése és a bevezetés tesztelése során a központi telepítés minden egyes szakaszában győződjön meg arról, hogy az eredmények a várt módon teljesülnek.

#### <a name="testing-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás tesztelése

A Microsoft Authenticator alkalmazással való jelszó nélküli hitelesítéshez az alábbi mintatesztesetek et követünk:

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhatja a Microsoft Authenticator alkalmazást | A felhasználó regisztrálhatja az alkalmazást a aka.ms/mysecurityinfo |
| A felhasználó engedélyezheti a telefonos bejelentkezést | Munkahelyi fiókhoz konfigurált telefonbejelentkezés |
| A felhasználó telefonos bejelentkezéssel érheti el az alkalmazást | Felhasználó megy keresztül telefon bejelentkezési folyamat, és eléri az alkalmazást. |
| A telefonos bejelentkezési regisztráció visszaállítása a Microsoft Authenticator jelszó nélküli bejelentkezésének kikapcsolásával az Azure Active Directory portál hitelesítési módszerek képernyőjén | A korábban engedélyezett felhasználók nem tudják használni a Microsoft Authenticator jelszó nélküli bejelentkezését. |
| A telefonos bejelentkezés eltávolítása a Microsoft Authenticator alkalmazásból | A munkahelyi fiók már nem érhető el a Microsoft Authenticator szolgáltatásban |

#### <a name="testing-security-keys"></a>Biztonsági kulcsok tesztelése

Az alábbiakban a jelszó nélküli, biztonsági kulcsokkal való hitelesítéshez szükséges tesztesetek et példázjuk.

**Jelszó nélküli FIDO-bejelentkezés az Azure Active Directoryhoz csatlakozott Windows 10-eszközökbe**

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhatja fido2 eszköz (1809) | A felhasználó regisztrálhatja a FIDO2 eszközt a Beállítások > fiókok > bejelentkezési beállításokat > biztonsági kulcs |
| A felhasználó visszaállíthatja FIDO2 eszköz (1809) | A felhasználó visszaállíthatja a FIDO2 eszközt a gyártó szoftverével |
| A felhasználó fido2 eszközzel (1809) jelentkezhet be | A felhasználó kiválaszthatja a biztonsági kulcsot a bejelentkezési ablakból, és sikeresen bejelentkezhet. |
| A felhasználó regisztrálhatja a FIDO2 eszközt (1903) | A felhasználó regisztrálhatja a FIDO2 eszközt a Beállítások > fiókok > bejelentkezési beállításokat > biztonsági kulcs |
| A felhasználó visszaállíthatja FIDO2 eszköz (1903) | A felhasználó visszaállíthatja a FIDO2 eszközt a Beállítások > fiókok > bejelentkezési beállításokat > biztonsági kulcs |
| A felhasználó fido2 eszközzel (1903) jelentkezhet be | A felhasználó kiválaszthatja a biztonsági kulcsot a bejelentkezési ablakból, és sikeresen bejelentkezhet. |

**Jelszó nélküli FIDO-bejelentkezés az Azure AD webalkalmazásokba**

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhatja a FIDO2 eszközt aka.ms/mysecurityinfo a Microsoft Edge segítségével | A regisztrációnak sikeresnek kell lennie |
| A felhasználó regisztrálhatja a FIDO2 eszközt a aka.ms/mysecurityinfo a Firefox | A regisztrációnak sikeresnek kell lennie |
| A felhasználó a FIDO2 eszköz segítségével a FIDO2 eszközsegítségével jelentkezhet be a OneDrive-ra online | A bejelentkezésnek sikeresnek kell lennie |
| A felhasználó a FIDO2 eszköz segítségével online bejelentkezhet a OneDrive-ra a Firefox segítségével | A bejelentkezésnek sikeresnek kell lennie |
| A FIDO2-eszköz regisztrációjának tesztelése a FIDO2 biztonsági kulcsok kikapcsolásával az Azure Active Directory portál hitelesítési metódusablakában | A rendszer kéri a felhasználókat, hogy a biztonsági kulcsukkal jelentkezzenek be. A felhasználók sikeresen bejelentkeznek, és egy hiba jelenik meg: "A vállalati szabályzat megköveteli, hogy egy másik módszert használja a bejelentkezéshez". A felhasználóknak ezután másik módszert kell választaniuk, és sikeresen be kell jelentkezniük. Zárja be az ablakot, és jelentkezzen be újra, és ellenőrizze, hogy nem ugyanazt a hibaüzenetet látja-e. |

### <a name="plan-for-rollback"></a>Visszaállítás megtervezése

Bár a jelszó nélküli hitelesítés egy könnyű funkció, amely minimális hatással van a végfelhasználókra, szükség lehet a visszaállításra.

A visszaállításhoz a rendszergazdának be kell jelentkeznie az Azure Active Directory portálra, ki kell választania a kívánt erős hitelesítési módszereket, és az engedélyezési beállítást **Nem**értékre kell módosítania. Ez a folyamat minden felhasználó számára kikapcsolja a jelszó nélküli funkciókat.

Azok a felhasználók, akik már regisztrálták a FIDO2 biztonsági eszközöket, a következő bejelentkezéskor a biztonsági eszköz használatára kérik a rendszert, majd a következő hibaüzenet jelenik meg:

![másik bejelentkezési mód kiválasztása](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Jelszó nélküli hitelesítés telepítése és hibaelhárítása

Kövesse az alábbi, a választott módszerhez igazított lépéseket.

### <a name="required-administrative-roles"></a>Szükséges felügyeleti szerepkörök

| Azure AD-szerepkör | Leírás |
| --- | --- |
| Globális rendszergazda|A legkevésbé privilegizált szerepkör képes a kombinált regisztrációs élmény megvalósítására. |
| Hitelesítési rendszergazda | A hitelesítési módszerek megvalósításához és kezeléséhez legkisebb jogosultsággal rendelkező szerepkör. |
| Felhasználó | A hitelesítési alkalmazás eszközön való konfigurálásához, illetve a biztonsági kulcseszköz webes vagy Windows 10-es bejelentkezéshez való regisztrálásához a legkevésbé kiemelt szerepkör. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Telefonos bejelentkezés telepítése a Microsoft Authenticator alkalmazással

Kövesse a Cikkben található, [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md) című cikkben, amely szerint a Microsoft Authenticator alkalmazás jelszó nélküli hitelesítési módszerként engedélyezze a szervezetet.

### <a name="deploy-fido2-security-key-sign-in"></a>A FIDO2 biztonságikulcs-bejelentkezés telepítése

Kövesse a cikkben leírt lépéseket, amelyek jelszó nélküli biztonsági kulcs bejelentkezést tesznek lehetővé [az Azure AD-ben](howto-authentication-passwordless-security-key.md) a FIDO2 biztonsági kulcsok jelszó nélküli hitelesítési módszerekként való engedélyezéséhez.

### <a name="troubleshoot-phone-sign-in"></a>Telefonos bejelentkezés – problémamegoldás

| Forgatókönyv | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt. | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem tudja engedélyezni a telefonbejelentkező hitelesítő alkalmazást. | Győződjön meg arról, hogy a felhasználó a telepítés hatókörében van. |
| A felhasználó nem szerepel a jelszó nélküli hitelesítés hatókörében, de jelszó nélküli bejelentkezési lehetőség gel jelenik meg, amelyet nem tudnak befejezni. | Ez a forgatókönyv akkor fordul elő, ha a felhasználó engedélyezte a telefonos bejelentkezést az alkalmazásba a házirend létrehozása előtt. <br> *A bejelentkezés engedélyezése*: Adja hozzá a felhasználót a jelszó nélküli bejelentkezéshez engedélyezett felhasználók hatóköréhez. <br> *A bejelentkezés letiltása*: a felhasználó távolítsa el a hitelesítő adatait az adott alkalmazásból. |

### <a name="troubleshoot-security-key-sign-in"></a>Biztonsági kulcs bejelentkezés – problémamegoldás

| Forgatókönyv | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt. | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem tud biztonsági kulcsot hozzáadni a [biztonsági beállításaikhoz.](https://aka.ms/mysecurityinfo) | Győződjön meg arról, hogy a [biztonsági kulcsok](howto-authentication-passwordless-security-key.md) engedélyezve vannak. |
| A felhasználó nem tud biztonsági kulcsot hozzáadni a Windows 10 bejelentkezési beállításaihoz. | [Annak ellenőrzése, hogy a Windows biztonsági kulcsai bevannak-e jelentkezve](howto-authentication-passwordless-enable.md) |
| **Hibaüzenet**: Azt észleltük, hogy ez a böngésző vagy operációs rendszer nem támogatja a FIDO2 biztonsági kulcsokat. | A jelszó nélküli FIDO2 biztonsági eszközök csak a támogatott böngészőkben regisztrálhatók (Microsoft Edge, Firefox 67-es verzió) a Windows 10 1809-es vagy újabb verziójában. |
| **Hibaüzenet:** A vállalati házirend használatához más módszert kell használnia a bejelentkezéshez. | Nem biztos, hogy a biztonsági kulcsok engedélyezve vannak a bérlőben. |
| A felhasználó nem tudja kezelni a biztonsági kulcsomat a Windows 10 1809-es verzióján | Az 1809-es verzió megköveteli, hogy a FIDO2 kulcsszállító által biztosított biztonságikulcs-kezelő szoftvert használja. Kérjen támogatást a forgalmazótól. |
| Azt hiszem, a FIDO2 biztonsági kulcs hibás lehet, hogyan tudom tesztelni. | Keresse [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)meg a , adja meg a hitelesítő adatokat egy **+** tesztfiókhoz, csatlakoztassa a gyanús biztonsági kulcsot, válassza ki a képernyő jobb felső részén található gombot, kattintson a létrehozás gombra, és menjen végig a létrehozási folyamaton. Ha ez a forgatókönyv nem sikerül, lehet, hogy az eszköz hibás. |

## <a name="next-steps"></a>További lépések

- [Jelszó nélküli biztonsági kulcsok engedélyezése az Azure AD-be való bejelentkezéshez](howto-authentication-passwordless-security-key.md)
- [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)
- [További információ a hitelesítési módszerek használatáról & elemzések](howto-authentication-methods-usage-insights.md)

