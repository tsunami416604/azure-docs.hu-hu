---
title: Jelszóval nem rendelkező hitelesítési telepítés befejezése az Azure AD-vel
description: Azure Active Directory jelszó nélküli hitelesítés központi telepítésének befejezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f07e1dd8e9252d2b6e00b85a47ba2e19f8bd8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603457"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Jelszóval nem rendelkező hitelesítési telepítés befejezése

A maihoz többsége a szervezeten belül valakitől ellopott felhasználónévvel és jelszóval kezdődik. 

A szervezetek megpróbálják elhárítani a fenyegetést a felhasználók általi használat megkövetelésével:

- Hosszú jelszavak
- Összetett jelszavak
- Gyakori jelszó-változtatások
- Multi-factor authentication (MFA)

A Microsoft [kutatásai azt mutatják](https://aka.ms/passwordguidance) , hogy ezek a felhasználók bántják a felhasználókat és felgyorsítják a támogatási költségeket. További információ: [a PA $ $Word nem számít](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

A jelszóval nem rendelkező hitelesítés telepítése a következő előnyöket nyújtja:

- Fokozott biztonság. A jelszavak támadási felületen való eltávolításával csökkentheti az adathalászat és a jelszó-szórás elleni támadások kockázatát.
- Jobb felhasználói élmény. Kényelmes hozzáférést biztosíthat a felhasználóknak az adatok bárhonnan való eléréséhez, és egyszerű hozzáférést biztosítanak az Outlookhoz, a OneDrive, az Office-hoz és más mobilhoz.
- Robusztus bepillantást nyerhet. Betekintést nyerhet a felhasználók jelszavas tevékenységeibe robusztus naplózással és naplózással.

A jelszót a rendszer lecseréli egy olyanra, amit Ön, vagy amit tud. Például a vállalati Windows Hello szolgáltatásban biometrikus kézmozdulat, például arc vagy ujjlenyomat, vagy egy eszközre jellemző PIN-kód, amely nincs továbbítva a hálózaton.

A Microsoft három, több forgatókönyvre kiterjedő, jelszóval nem rendelkező hitelesítési lehetőséget kínál. Ezek a módszerek párhuzamosan is használhatók. 

- A [vállalati Windows Hello](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) a legjobb a dedikált Windows-számítógépeken lévő felhasználók számára.
- A biztonsági kulcs [FIDO2 biztonsági kulcsaival](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) való bejelentkezés különösen hasznos azoknak a felhasználóknak, akik olyan megosztott gépekbe jelentkeznek be, mint a kioszkok, a telefonok használatának korlátozásával, valamint a magas jogosultsági szintű identitások esetében.
- A [Microsoft Authenticator alkalmazással](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) való telefonos bejelentkezés hasznos lehet a mobileszközök felhasználói számára a jelszó nélküli beállítás biztosításához. A felhasználók bármilyen platformra vagy böngészőbe bejelentkezhetnek az iOS-vagy Android-alapú telefonokba. A felhasználók bejelentkezhetnek, ha értesítést küldenek a telefonjára, és a képernyőn megjelenő számnak megfelelnek a telefonján, majd a biometrikus adatok vagy PIN-kód használatával megerősítik a jóváhagyást.

## <a name="compare-passwordless-authentication-methods"></a>A jelszóval nem rendelkező hitelesítési módszerek összehasonlítása

A Microsoft jelszavas hitelesítési módszerei különböző forgatókönyveket tesznek lehetővé. Vegye figyelembe a szervezeti igényeket, előfeltételeket és az egyes hitelesítési módszerek képességeit a jelszóval nem rendelkező hitelesítési stratégia kiválasztásához. Javasoljuk, hogy minden Windows 10-es eszközt használó szervezet a vállalati Windows Hello szolgáltatást használja. Ezután adja hozzá a telefonos bejelentkezést (a Microsoft Authenticator alkalmazással) vagy a biztonsági kulcsokat a további forgatókönyvekhez.

### <a name="passwordless-authentication-scenarios"></a>Jelszóval nem rendelkező hitelesítési forgatókönyvek

| Forgatókönyv | Telefonos hitelesítés | Biztonsági kulcsok | Vállalati Windows Hello |
| --- | --- | --- | --- |
| **Számítógép bejelentkezés**: <br> Hozzárendelt Windows 10-es eszközről | **Nem** | **Igen** <br> Biometrikus kóddal | **Igen**<br>biometrikus felismeréssel és PIN-kóddal |
| **Számítógép bejelentkezés**: <br> Megosztott Windows 10-es eszközről | **Nem** | **Igen** <br> Biometrikus kóddal  | **Nem** |
| **Webalkalmazás-bejelentkezés**: <br>felhasználó által dedikált számítógépről | **Igen** | **Igen** <br> A megadott egyszeri bejelentkezést a számítógép-bejelentkezés lehetővé teszi az alkalmazásokba. | **Igen**<br> A megadott egyszeri bejelentkezést a számítógép-bejelentkezés lehetővé teszi az alkalmazásokba. |
| **Webalkalmazás-bejelentkezés**: <br> mobil vagy nem Windows rendszerű eszközről | **Igen** | **Nem** | **Nem** |
| **Számítógép bejelentkezés**: <br> Nem Windows rendszerű számítógép | **Nem** | **Nem** | **Nem** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technikai megfontolások a Microsoft Authenticator alkalmazáshoz

**AD FS integráció** – ha egy felhasználó engedélyezi a jelszóval nem rendelkező Microsoft Authenticator hitelesítő adatokat, akkor az adott felhasználó számára a hitelesítés jóváhagyás céljából elküld egy értesítést. A hibrid bérlőben lévő felhasználókat a rendszer nem irányítja át az ADFS-be a bejelentkezéshez, kivéve, ha a "jelszó használata helyett" lehetőséget választja. Ez a folyamat megkerüli a helyszíni feltételes hozzáférési szabályzatokat és a továbbított hitelesítési folyamatokat is. Ha azonban meg van adva egy login_hint, a rendszer továbbítja a felhasználót az ADFS-nek, és megkerüli a jelszóval nem rendelkező hitelesítő adatok használatára vonatkozó lehetőséget.

**Azure MFA-kiszolgáló** – az MFA számára a szervezet helyszíni Azure MFA-kiszolgálóján keresztül engedélyezett végfelhasználók továbbra is létrehozhatnak és használhatnak egyszeri jelszó nélküli telefonos bejelentkezési hitelesítő adatokat. Ha a felhasználó a hitelesítő adatokkal rendelkező Microsoft Authenticator több telepítésének (5 +) frissítését kísérli meg, ez a változás hibát okozhat.

**Eszköz regisztrálása** – a hitelesítő alkalmazás jelszavas hitelesítésre való használatához regisztrálni kell az eszközt az Azure ad-bérlőben, és nem lehet megosztott eszköz. Az eszközök csak egyetlen bérlőben regisztrálhatók. Ez a korlát azt jelenti, hogy csak egy munkahelyi vagy iskolai fiók támogatott a telefonos bejelentkezéshez a hitelesítő alkalmazás használatával.

## <a name="prerequisites"></a>Előfeltételek

A szervezeteknek a következő előfeltételeknek kell megfelelniük a jelszóval nem rendelkező központi telepítés megkezdése előtt.

| Előfeltétel | Hitelesítő alkalmazás | FIDO2 biztonsági kulcsok |
| --- | --- | --- |
| Az [Azure MFA és az önkiszolgáló jelszó-visszaállítás (SSPR) együttes regisztrálása](howto-registration-mfa-sspr-combined.md) engedélyezve van (előzetes verziójú funkció) | √ | √ |
| [A felhasználók el tudják végezni az Azure MFA-t](howto-mfa-getstarted.md) | √ | √ |
| [A felhasználók regisztrálva vannak az Azure MFA és a SSPR számára](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [A felhasználók regisztrálták a mobileszközök Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 1809-es vagy újabb verzió egy támogatott böngészőben, például a Microsoft Edge vagy a Mozilla Firefox használatával <br> (67-es vagy újabb verzió). <br> *A Microsoft natív támogatás esetén a 1903-es vagy újabb verziót javasolja*. |   | √ |
| Kompatibilis FIDO2 biztonsági kulcsok. Győződjön meg arról, hogy [Microsoft által tesztelt és ellenőrzött](howto-authentication-passwordless-enable.md) FIDO2 biztonsági eszközt vagy más kompatibilis FIDO2 biztonsági eszközt használ. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>A vállalati Windows Hello használatának előfeltételei

A Windows Hello előfeltételei nagymértékben függenek attól, hogy helyszíni, hibrid vagy kizárólag felhőalapú konfigurációban végez-e üzembe helyezést. További információkért tekintse meg a [vállalati Windows Hello előfeltételeinek teljes listáját](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

A felhasználók az Azure MFA regisztrációs folyamat részeként regisztrálják a jelszóval nem rendelkező metódust. A többtényezős hitelesítés a felhasználónévvel és a jelszóval együtt egy másik regisztrált módszerrel is használható tartalékként, ha bizonyos helyzetekben nem tudják használni a telefonját vagy a biztonsági kulcsát.

### <a name="security-key-lifecycle"></a>Biztonsági kulcs életciklusa

A biztonsági kulcsok lehetővé teszik az erőforrások elérését, és meg kell terveznie ezeknek a fizikai eszközöknek a felügyeletét.

1. Kulcs terjesztése: Tervezze meg, hogyan fogja kiépíteni a kulcsokat a szervezet számára. Lehet, hogy központosított kiépítési folyamattal rendelkezik, vagy lehetővé teszi a végfelhasználók számára, hogy a 2,0-kompatibilis kulcsokat vásároljanak.
1. Kulcs aktiválása: a végfelhasználóknak saját maguknak kell aktiválniuk a biztonsági kulcsot. A végfelhasználók a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) regisztrálják a biztonsági kulcsaikat, és az első használat során engedélyezik a második tényezőt (PIN-kódot vagy biometrikus adatokat).
1. Kulcs letiltása: a biztonsági kulcs funkciója az előzetes verzióban van, így a rendszergazda nem távolíthat el kulcsot egy felhasználói fiókból. A felhasználónak el kell távolítania. A kulcs elvesztése vagy ellopása esetén:
   1. Távolítsa el a felhasználót bármely olyan csoportból, amely engedélyezve van a jelszó nélküli hitelesítéshez.
   1. Ellenőrizze, hogy eltávolította-e a kulcsot hitelesítési módszerként.
   1. Adjon ki egy új kulcsot.
1. Kulcs cseréje: a felhasználók egyszerre két biztonsági kulcsot engedélyezhetik. Egy biztonsági kulcs cseréjekor győződjön meg arról, hogy a felhasználó eltávolította a lecserélt kulcsot is.

### <a name="enable-windows-10-support"></a>A Windows 10-es támogatásának engedélyezése

Ahhoz, hogy a Windows 10-es bejelentkezés engedélyezve legyen a FIDO2 biztonsági kulcsaival, engedélyezni kell a hitelesítő adatok szolgáltatójának működését a Windows 10 rendszerben. A két módszer egyikének engedélyezése:

- [Hitelesítőadat-szolgáltató engedélyezése az Intune-nal](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Az Intune üzembe helyezése az ajánlott lehetőség.
- [Hitelesítőadat-szolgáltató engedélyezése kiépítési csomaggal](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Ha az Intune üzembe helyezése nem lehetséges, a rendszergazdáknak minden gépen telepíteniük kell egy csomagot, hogy engedélyezze a hitelesítő adatok szolgáltatójának működőképességét. A csomag telepítését a következő lehetőségek egyikével végezheti el:
      - Csoportházirend vagy System Center Configuration Manager (SCCM)
      - Helyi telepítés Windows 10 rendszerű gépen

### <a name="register-security-keys"></a>Biztonsági kulcsok regisztrálása

A felhasználóknak regisztrálniuk kell a biztonsági kulcsot minden Azure Active Directory csatlakoztatott Windows 10 rendszerű gépen.

További információ: [a FIDO2 biztonsági kulcsainak felhasználói regisztrálása és kezelése](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licencelés jelszavas hitelesítéshez

A jelszó nélküli hitelesítéshez nem kell további díjat fizetni, bár bizonyos előfeltételek esetén prémium előfizetés szükséges. A [Azure Active Directory licencelése lapon](https://azure.microsoft.com/pricing/details/active-directory/)tekintse meg a részletes funkció-és licencelési információkat.

## <a name="develop-a-plan"></a>Terv kidolgozása

Vegye figyelembe az üzleti igényeket és az egyes hitelesítési módszerek használati eseteit. Ezután válassza ki az igényeinek leginkább megfelelő módszert.

### <a name="use-cases"></a>Használati esetek

Az alábbi táblázat a projekt során megvalósítandó használati eseteket ismerteti.

| Terület | Leírás |
| --- | --- |
| **Access (Hozzáférés)** | A jelszó nélküli bejelentkezés a vállalati hálózaton belüli vagy kívüli vállalati vagy személyes eszközről érhető el. |
| **Naplózás** | A használati adatok a rendszergazdák számára a közel valós időben történő naplózáshoz érhetők el. <br> A használati adatokat legalább 29 naponta letölti a vállalati rendszerbe, vagy az SIEM eszközt használja. |
| **Cégirányítási** | A megfelelő hitelesítési módszerhez és a társított csoportokhoz tartozó felhasználói hozzárendelések életciklusa definiálva és figyelve van. |
| **Biztonság** | A megfelelő hitelesítési módszerhez való hozzáférés a felhasználók és csoportok hozzárendelésein keresztül vezérelhető. <br> Csak a jogosultsággal rendelkező felhasználók használhatják a jelszó nélküli bejelentkezést. |
| **Teljesítmény** | A hozzáférési hozzárendelések terjesztési ütemterveit dokumentálják és figyelik. <br> A bejelentkezési idő mérése egyszerű használat érdekében történik. |
| **Felhasználói élmény** | A felhasználók tisztában vannak a mobil kompatibilitással. <br> A felhasználók megadhatják a hitelesítő alkalmazás jelszavas bejelentkezését. |
| **Támogatás** | A felhasználók tisztában vannak azzal, hogy miként találhatják meg a jelszó nélküli bejelentkezési problémák támogatását. |

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségi körök eltérő elvárásai vannak. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) , és hogy a projektben szereplő, az érintett felek szerepkörei jól megértettek.

### <a name="organization-communications"></a>Szervezeti kommunikáció

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon közli, hogy a felhasználók hogyan változnak, Mikor változnak, és hogyan szerezhet támogatást, ha problémákat tapasztalnak.

A végfelhasználók felé irányuló kommunikációhoz a következőkre lesz szüksége:

- [A kombinált biztonsági regisztráció használatának engedélyezése](howto-authentication-passwordless-phone.md)
- [A Microsoft Authenticator alkalmazás letöltése](../user-help/user-help-auth-app-download-install.md)
- [Regisztrálás a Microsoft Authenticator alkalmazásban](howto-authentication-passwordless-phone.md)
- [Bejelentkezés telefonnal](../user-help/user-help-auth-app-sign-in.md)

A Microsoft MFA [kommunikációs sablonokat](https://aka.ms/mfatemplates), önkiszolgáló jelszó-visszaállítási (SSPR) [kommunikációs sablonokat](https://www.microsoft.com/download/details.aspx?id=56768)és [végfelhasználói dokumentációt](../user-help/security-info-setup-signin.md) biztosít a kommunikáció megtervezéséhez. Az adott oldalon található biztonsági információk hivatkozásaira kattintva a felhasználók közvetlenül regisztrálhatnak [https://myprofile.microsoft.comra](https://myprofile.microsoft.com/) .

### <a name="testing-passwordless"></a>Jelszóval való tesztelés

Az üzembe helyezés minden egyes fázisában ellenőrizze, hogy az eredmények a várt módon vannak-e tesztelve.

#### <a name="testing-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás tesztelése

Az alábbiakban a Microsoft Authenticator alkalmazással való jelszavas hitelesítésre vonatkozó példákat vizsgáljuk.

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat Microsoft Authenticator alkalmazást | A felhasználó regisztrálhatja az alkalmazást a aka.ms/mysecurityinfo |
| A felhasználó engedélyezheti a telefonos bejelentkezést | A munkahelyi fiókhoz konfigurált telefonos bejelentkezés |
| A felhasználó egy telefonos bejelentkezéssel rendelkező alkalmazást is elérhet | A felhasználó a telefonos bejelentkezési folyamaton keresztül éri el a kijelölt alkalmazást. |
| A telefonos bejelentkezés regisztrációjának tesztelése a Azure Active Directory portál hitelesítési módszerek képernyőjén a jelszó nélküli bejelentkezés Microsoft Authenticator kikapcsolásával | A korábban engedélyezett felhasználók nem tudják használni a jelszó nélküli bejelentkezést Microsoft Authenticator. |
| Telefonos bejelentkezés eltávolítása Microsoft Authenticator alkalmazásból | A munkahelyi fiók már nem érhető el Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Biztonsági kulcsok tesztelése

A következő példák tesztelési eseteket biztosítanak a biztonsági kulcsokkal való jelszavas hitelesítéshez.

**Jelszó nélküli, Azure Active Directory csatlakoztatott Windows 10-es eszközökhöz**

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat FIDO2 eszközt (1809) | A felhasználó regisztrálhat FIDO2-eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt (1809) | A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt gyártó szoftverrel |
| A felhasználó bejelentkezhet a FIDO2 eszközzel (1809) | A felhasználó a bejelentkezési ablakban kiválaszthatja a biztonsági kulcsot, és sikeresen bejelentkezhet. |
| A felhasználó regisztrálhat FIDO2 eszközt (1903) | A felhasználó regisztrálhat FIDO2-eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt (1903) | A felhasználó alaphelyzetbe állíthatja a FIDO2 eszközt a beállítások > fiókok > bejelentkezési beállítások > biztonsági kulcs |
| A felhasználó bejelentkezhet a FIDO2 eszközzel (1903) | A felhasználó a bejelentkezési ablakban kiválaszthatja a biztonsági kulcsot, és sikeresen bejelentkezhet. |

**Jelszó nélküli, bejelentkezés az Azure AD web appsbe**

| Forgatókönyv | Várt eredmények |
| --- | --- |
| A felhasználó regisztrálhat FIDO2-eszközt a aka.ms/mysecurityinfo-on a Microsoft Edge használatával | A regisztrációnak sikeresnek kell lennie |
| A felhasználó regisztrálhat FIDO2-eszközt a aka.ms/mysecurityinfo-on a Firefox használatával | A regisztrációnak sikeresnek kell lennie |
| A felhasználó a Microsoft Edge használatával tud bejelentkezni a OneDrive online FIDO2-eszköz használatával | A bejelentkezésnek sikeresnek kell lennie |
| A felhasználó bejelentkezhet a OneDrive online FIDO2-eszköz használatával a Firefox használatával | A bejelentkezésnek sikeresnek kell lennie |
| A FIDO2-eszköz regisztrációjának visszavonása a Azure Active Directory-portál hitelesítési módszerek paneljének kikapcsolásával | A rendszer felszólítja a felhasználókat, hogy jelentkezzenek be a biztonsági kulccsal, majd a következő hibaüzenet jelenik meg: "a céges szabályzat megköveteli, hogy más módszert használjon a bejelentkezéshez". A felhasználóknak ezután ki kell tudniuk választani egy másik metódust, és sikeresen be kell jelentkezniük. Az ablak bezárásához és újbóli bejelentkezéshez ellenőrizze, hogy nem ugyanaz a hibaüzenet jelenik-e meg. |

### <a name="auditing-passwordless"></a>Jelszavas naplózás

Az Azure AD olyan jelentéseket tartalmaz, amelyek technikai és üzleti ismereteket biztosítanak. Az üzleti és a műszaki alkalmazások tulajdonosai a szervezet igényeinek megfelelően feltételezik és használják ezeket a jelentéseket.

A hitelesítési módszerek szakasz a Azure Active Directory-portálon belül, ahol a rendszergazdák engedélyezhetik és kezelhetik a jelszó nélküli hitelesítő adatok beállításait.

Az Azure AD bejegyzéseket ad hozzá a naplókhoz a következő esetekben:

- A rendszergazdák módosításokat hajtanak végre a hitelesítési módszerek szakaszban.
- A felhasználó Azure Active Directoryon belül bármilyen módosítást végez a hitelesítő adatain.

Az alábbi táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

|   | A kockázatok kezelése | Termelékenység növelése | Cégirányítás és megfelelőség |
| --- | --- | --- | --- |
| **Jelentéstípusok** | Hitelesítési módszerek – a kombinált biztonsági regisztrációhoz regisztrált felhasználók | Hitelesítési módszerek – alkalmazás-értesítéshez regisztrált felhasználók | Bejelentkezések: Tekintse át, ki fér hozzá a bérlőhöz, és hogyan |
| **Lehetséges műveletek** | A célként még nem regisztrált felhasználók | Microsoft Authenticator alkalmazás vagy biztonsági kulcsok átvétele | Hozzáférés visszavonása vagy további biztonsági házirendek betartatása a rendszergazdák számára |

Az **Azure ad 30 napig megőrzi a legtöbb naplózási adatát** , és az Azure felügyeleti portálon vagy API-n keresztül elérhetővé teszi az adatait az Analysis Systems-be való letöltéshez. Ha a szervezete megtartja a hosszú adatmegőrzést, a naplókat olyan SIEM-eszközökbe kell exportálni és felhasználni, mint például az [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), a splunk vagy a Sumo Logic. [További információ a hozzáférési és használati jelentések megtekintéséről](../reports-monitoring/overview-reports.md).

A felhasználók regisztrálhatják és kezelhetik hitelesítő adataikat a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)való navigáláskor. Ez a hivatkozás a felhasználókat arra a végfelhasználói hitelesítőadat-kezelési felületre irányítja, amelyet a kombinált SSPR/MFA regisztrációs felületén keresztül engedélyeztek. A FIDO2 biztonsági eszközök regisztrációját, illetve a hitelesítési módszerek felhasználó általi módosítását a rendszer naplózza a Azure Active Directory naplókba.

Ha a felhasználók engedélyezik vagy letiltják a fiókot egy biztonsági kulcson, vagy a Windows 10 rendszerű gépeken a biztonsági kulcs második tényezőjét alaphelyzetbe állítja, a rendszer hozzáad egy bejegyzést a biztonsági naplóhoz, és a következő eseményazonosító alá esik: 4670, 5382.

### <a name="plan-for-rollback"></a>A visszaállítás megtervezése

Bár a jelszó-alapú hitelesítés egy egyszerű funkció, amely minimális hatással van a végfelhasználók számára, szükséges lehet a visszaállítás.

A visszaállításhoz a rendszergazdának be kell jelentkeznie a Azure Active Directory-portálra, ki kell választania a kívánt erős hitelesítési módszereket, és módosítania kell az Engedélyezés lehetőséget a "nem" értékre. Ez a folyamat kikapcsolja az összes felhasználó jelszavas funkcionalitását.

A már regisztrált FIDO2 biztonsági eszközöket a rendszer a következő bejelentkezéskor fogja kérni a biztonsági eszköz használatára, és a következő hibaüzenet jelenik meg:

![Válasszon másik módszert a bejelentkezéshez](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Tervezés a pilóta számára

A jelszó nélküli hitelesítés telepítésekor először engedélyeznie kell egy vagy több kísérleti csoportot. A kifejezetten erre a célra szolgáló [csoportokat hozhat létre](../fundamentals/active-directory-groups-create-azure-portal.md) . Adja hozzá azokat a felhasználókat, akik részt vesznek a pilótában a csoportokban. Ezután engedélyezze az új, jelszóval nem rendelkező hitelesítési módszereket a kiválasztott csoportokhoz.

A csoportok a helyszíni címtárból vagy az Azure AD-ből is szinkronizálhatók. Ha elégedett a pilóta eredményeivel, átválthatja a jelszó nélküli hitelesítést az összes felhasználóra.

Tekintse meg az [ajánlott eljárásokat a pilóta számára](https://aka.ms/deploymentplans) a központi telepítési tervek lapon.

## <a name="deploy-passwordless-authentication"></a>Jelszóval nem rendelkező hitelesítés üzembe helyezése

Kövesse az alábbi, a választott módszerhez igazított lépéseket.

### <a name="required-administrative-roles"></a>Szükséges rendszergazdai szerepkörök

| Azure AD-szerepkör | Leírás |
| --- | --- |
| Hitelesítés rendszergazdája | A hitelesítési módszerek megvalósításához és kezeléséhez szükséges legalacsonyabb jogosultsági szintű szerepkör |
| Felhasználó | A legkevesebb Kiemelt szerepkör a hitelesítő alkalmazás konfigurálásához az eszközön, vagy a biztonsági kulcs eszközének regisztrálása a web vagy a Windows 10-es bejelentkezéshez. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Telefonos bejelentkezés üzembe helyezése a Microsoft Authenticator alkalmazással

Kövesse a cikk lépéseit, [engedélyezze a jelszó nélküli bejelentkezést a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md) , hogy az Microsoft Authenticator alkalmazás jelszavas hitelesítési módszerként legyen engedélyezve a szervezetben.

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 biztonsági kulcsának üzembe helyezése

Kövesse a cikk lépéseit, [engedélyezze a jelszó nélküli biztonsági kulcs bejelentkezését az Azure ad](howto-authentication-passwordless-security-key.md) -be, hogy a FIDO2 biztonsági kulcsokat jelszó nélküli hitelesítési módszerként engedélyezze a szervezetben.

### <a name="troubleshoot-phone-sign-in"></a>Telefonos bejelentkezés – problémamegoldás

| Forgatókönyv | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem engedélyezheti a telefonos bejelentkezési hitelesítő alkalmazást | Győződjön meg arról, hogy a felhasználó hatókörben van a telepítéshez |
| A felhasználó nem szerepel a jelszó nélküli hitelesítés hatókörében, de a jelszó nélküli bejelentkezés lehetőség jelenik meg, amely nem hajtható végre. | Ez a forgatókönyv akkor fordul elő, ha a felhasználó engedélyezte a telefonos bejelentkezést az alkalmazásban, mielőtt a létrehozott házirend létrejött. <br> A bejelentkezés engedélyezése: adja hozzá a felhasználót a jelszó nélküli bejelentkezéshez engedélyezett felhasználók hatóköréhez. <br> A bejelentkezés blokkolásához: a felhasználó eltávolítja az alkalmazáshoz tartozó hitelesítő adatokat. |

### <a name="troubleshoot-security-key-sign-in"></a>Biztonsági kulcs bejelentkezésének hibakeresése

| Forgatókönyv | Megoldás |
| --- | --- |
| A felhasználó nem tudja végrehajtani a kombinált regisztrációt | Győződjön meg arról, hogy a [kombinált regisztráció](concept-registration-mfa-sspr-combined.md) engedélyezve van. |
| A felhasználó nem adhat hozzá biztonsági kulcsot a [biztonsági beállításokban](https://aka.ms/mysecurityinfo) | Győződjön meg arról, hogy a [biztonsági kulcsok](howto-authentication-passwordless-security-key.md) engedélyezve vannak. |
| A felhasználó nem adhat hozzá biztonsági kulcsot a Windows 10 bejelentkezési beállításaiban | [Győződjön meg arról, hogy a Windows-bejelentkezéshez szükséges biztonsági kulcsok](howto-authentication-passwordless-enable.md) |
| **Hibaüzenet**: azt észlelte, hogy ez a böngésző vagy operációs rendszer nem támogatja a FIDO2 biztonsági kulcsait. | A jelszó nélküli FIDO2 biztonsági eszközöket csak a támogatott böngészőkben (a Microsoft Edge, a Firefox 67-es verziójában) lehet regisztrálni a Windows 10 1809-es vagy újabb verziójában. |
| **Hibaüzenet**: a vállalati házirend megköveteli, hogy más módszert használjon a bejelentkezéshez. | A nem biztos, hogy a biztonsági kulcsok engedélyezve vannak a bérlőben. |
| A Windows 10 1809-es verziójában a felhasználó nem tudja kezelni a biztonsági kulcsot | Az 1809-es verzió használatához a FIDO2 kulcs gyártója által biztosított biztonsági kulcs-felügyeleti szoftvert kell használnia. Támogatásért forduljon a gyártóhoz. |
| Azt hiszem, hogy a FIDO2 biztonsági kulcsa hibás lehet – hogyan tesztelhető | Lépjen a [https://webauthntest.azurewebsites.net/re ](https://webauthntest.azurewebsites.net/), adja meg a tesztelési fiók hitelesítő adatait, csatlakoztassa a gyanús biztonsági kulcsot, kattintson a képernyő jobb felső részén található + gombra, kattintson a Létrehozás gombra, és folytassa a létrehozás folyamatát. Ha ez a forgatókönyv meghiúsul, lehetséges, hogy az eszköz hibás. |

## <a name="next-steps"></a>További lépések

- [Jelszó nélküli biztonsági kulcsok engedélyezése az Azure AD-ba való bejelentkezéshez](howto-authentication-passwordless-security-key.md)
- [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](howto-authentication-passwordless-phone.md)
- [További információ a hitelesítési módszerekről & információk](howto-authentication-methods-usage-insights.md)
