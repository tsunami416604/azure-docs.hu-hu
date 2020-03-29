---
title: Hitelesítési módszerek – Azure Active Directory
description: Az Azure AD-ben elérhető hitelesítési módszerek az MFA és az SSPR számára
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968772"
---
# <a name="what-are-authentication-methods"></a>Mik a hitelesítési módszerek?

Rendszergazdaként az Azure többtényezős hitelesítéshez és az önkiszolgáló jelszó-visszaállításhoz (SSPR) választott hitelesítési módszerek kiválasztása ajánlott, hogy a felhasználóknak több hitelesítési módszert kell regisztrálniuk. Ha egy hitelesítési módszer nem érhető el a felhasználó számára, választhat, hogy egy másik módszerrel hitelesíti magát.

A rendszergazdák házirendben meghatározhatják, hogy mely hitelesítési módszerek érhetők el az SSPR és az MFA felhasználói számára. Előfordulhat, hogy egyes hitelesítési módszerek nem érhetők el minden szolgáltatás számára. A szabályzatok konfigurálásáról további információt az [önkiszolgáló jelszó-visszaállítás sikeres bevezetése](howto-sspr-deployment.md) és [a felhőalapú Azure többtényezős hitelesítés tervezése című](howto-mfa-getstarted.md) cikkekben talál.

A Microsoft azt javasolja a Rendszergazdáknak, hogy a felhasználók a minimálisan szükséges számú hitelesítési módszernél többet válasszanak ki, ha nem férnek hozzá.

|Hitelesítési módszer|Használat|
| --- | --- |
| Jelszó | MFA és SSPR |
| Biztonsági kérdések | Csak SSPR |
| E-mail-cím | Csak SSPR |
| A Microsoft Authenticator alkalmazás | MFA és SSPR |
| OATH hardvertoken | Nyilvános előzetes verzió az MFA-hoz és az SSPR-hez |
| SMS | MFA és SSPR |
| Hanghívás | MFA és SSPR |
| Alkalmazásjelszavak. | MFA csak bizonyos esetekben |

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Az OATH hardvertokenek az MFA és az SSPR számára az Azure Active Directory nyilvános előzetes verziójú szolgáltatásai. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

## <a name="password"></a>Jelszó

Az Azure AD-jelszó hitelesítési módszernek minősül. Ez az egyetlen módszer, amely **nem tiltható le**.

## <a name="security-questions"></a>Biztonsági kérdések

A biztonsági kérdések **csak az Azure AD önkiszolgáló jelszó-visszaállítás nem** rendszergazdai fiókokban érhetők el.

Ha biztonsági kérdéseket használ, javasoljuk, hogy azokat más módszerrel együtt használja. A biztonsági kérdések kevésbé biztonságosak lehetnek, mint más módszerek, mivel néhány felhasználó esetleg tudja a választ egy másik felhasználó kérdéseire.

> [!NOTE]
> A biztonsági kérdéseket a rendszer a címtárban lévő felhasználói objektumon tárolja, és csak a regisztráció során válaszolhat meg. A rendszergazda nem olvashatja vagy módosíthatja a felhasználó kérdéseit vagy válaszait.
>

### <a name="predefined-questions"></a>Előre meghatározott kérdések

* Melyik városban találkozott az első házastársával/élettársával?
* Melyik városban találkoztak a szüleid?
* Melyik városban lakik a legközelebbi testvére?
* Melyik városban született az apád?
* Melyik városban volt az első munkád?
* Melyik városban született az anyád?
* Melyik városban voltál 2000 szilveszterkor?
* Mi a kedvenc tanárod vezetékneve a gimiben?
* Mi a neve annak a főiskolának, ahová jelentkeztél, de nem vettél részt?
* Mi a neve annak a helynek, ahol az első esküvői fogadást tartottad?
* Mi az apja középső neve?
* Mi a kedvenc ételed?
* Mi az anyai nagyanyád vezeték- és keresztneve?
* Mi az anyád középső neve?
* Mi a legidősebb testvéred születésnapja hónapban és évben? (pl. 1985. november)
* Mi a legidősebb testvéred középső neve?
* Mi az apai nagyapád vezeték- és keresztneve?
* Mi a legfiatalabb testvéred középső neve?
* Melyik iskolába jártál hatodikban?
* Mi volt a gyerekkori legjobb barátod vezeték- és keresztneve?
* Mi volt az első jelentős ebbid vezeték- és keresztneve?
* Mi volt a kedvenc általános iskolai tanárod vezetékneve?
* Mi volt a gyártmánya és modellje az első autóvagy motorkerékpár?
* Mi volt a neve az első iskolának, ahol járt?
* Mi volt a neve annak a kórháznak, ahol születtél?
* Mi volt a neve az első gyerekkori otthonod utcájának?
* Mi volt a neve a gyerekkori hősödnek?
* Mi volt a kedvenc plüssállatod neve?
* Mi volt az első háziállatod neve?
* Mi volt a gyerekkori beceneved?
* Mi volt a kedvenc sportod a gimiben?
* Mi volt az első munkád?
* Mi volt a gyerekkori telefonszámának utolsó négy számjegye?
* Amikor fiatal voltál, mi akartál lenni, amikor felnőttél?
* Ki a leghíresebb ember, akivel valaha találkoztál?

Az összes előre meghatározott biztonsági kérdés lefordításra és az Office 365-nyelvek teljes készletére lesz honos, a felhasználó böngészőjének területi beállítása alapján.

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

Az egyéni biztonsági kérdések nincsenek honosítva. Minden egyéni kérdés ugyanazon a nyelven jelenik meg, mint a hogy a rendszergazdai felhasználói felületen bevan írva, még akkor is, ha a felhasználó böngészőterületi beállításai eltérőek. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

Az egyéni biztonsági kérdések maximális hossza 200 karakter.

### <a name="security-question-requirements"></a>Biztonsági kérdések követelményei

* A válasz karakterének minimális korlátja három karakter.
* A válaszkarakter maximális korlátja 40 karakter.
* A felhasználók nem válaszolhatnak ugyanarra a kérdésre egynél többször.
* A felhasználók nem adhatnak ugyanazt a választ egynél több kérdésre.
* Bármely karakterkészlet használható a kérdések és a válaszok meghatározására, beleértve a Unicode karaktereket is.
* A megadott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztrációhoz szükséges kérdések számával.

## <a name="email-address"></a>E-mail-cím

Az **e-mail-cím csak az Azure AD önkiszolgáló jelszó-visszaállításban**érhető el.

A Microsoft azt javasolja, hogy egy e-mail fiók, amely nem igényel a felhasználó Azure AD-jelszavát eléréséhez.

## <a name="microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás

A Microsoft Authenticator alkalmazás további biztonsági szintet biztosít az Azure AD-munkahelyi vagy iskolai fiókjához vagy a Microsoft-fiókjához.

A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) rendszereken érhető el.

> [!NOTE]
> A felhasználóknak nem lesz lehetőségük regisztrálni mobilalkalmazásukat az önkiszolgáló jelszó-visszaállításra való regisztrációsorán. Ehelyett a felhasználók regisztrálhatják [https://aka.ms/mfasetup](https://aka.ms/mfasetup) mobilalkalmazásukat a biztonsági [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)adatok regisztrációs előnézetében vagy a rendszerben a.
>

### <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

A Microsoft Authenticator alkalmazás segít megakadályozni a fiókokhoz való jogosulatlan hozzáférést, és leállíthatja a csalárd tranzakciókat azáltal, hogy értesítést küld az okostelefonjára vagy táblagépére. A felhasználók megtekinthetik az értesítést, és ha az jogos, válassza az Ellenőrzés lehetőséget. Ellenkező esetben a Megtagadás lehetőséget választhatják.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállítás esetén, ha csak egy módszer szükséges az alaphelyzetbe állításhoz, az ellenőrző kód az egyetlen lehetőség a felhasználók számára **a legmagasabb szintű biztonság biztosítása érdekében.**
>
> Ha két módszerre van szükség, a felhasználók bármely más engedélyezett módszer mellett vagy **értesítési** **vagy** ellenőrző kód használatával alaphelyzetbe állíthatják őket.
>

Ha engedélyezi az értesítés mobilalkalmazáson keresztüli használatát és a mobilalkalmazásellenőrző kódját, a Microsoft Authenticator alkalmazást értesítéssel regisztráló felhasználók értesítéssel is használhatják az értesítést és a kódot személyazonosságuk ellenőrzésére.

> [!NOTE]
> Ha a szervezet munkatársai Kínában dolgoznak vagy Kínába utaznak, az **Értesítés mobilalkalmazáson keresztül** módszer **Android-eszközökön** nem működik az adott országban. Alternatív módszereket kell elérhetővé tenni a felhasználók számára.

### <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a mobilalkalmazásból

A Microsoft Authenticator alkalmazás vagy más külső alkalmazások szoftvertokenként használhatók az OATH ellenőrző kód létrehozásához. Miután megadta a felhasználónevét és a jelszavát, beírja az alkalmazás által megadott kódot a bejelentkezési képernyőre. Az ellenőrzőkód egy második hitelesítési módként szolgál.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállítás esetén, ha csak egy módszer szükséges az ellenőrző kód visszaállításához, az egyetlen lehetőség a felhasználók számára **a legmagasabb szintű biztonság biztosítása érdekében.**
>

A felhasználók legfeljebb öt OATH hardvertoken vagy hitelesítő alkalmazás, például a Microsoft Authenticator alkalmazás bármikor használatra konfigurált kombinációjával rendelkezhetnek.

## <a name="oath-hardware-tokens-public-preview"></a>OATH hardvertokenek (nyilvános előzetes verzió)

Az OATH egy nyílt szabvány, amely meghatározza az egyszeri jelszókódok (OTP) létrehozásának módját. Az Azure AD támogatja az OATH-TOTP SHA-1 tokenek használatát a 30 másodperces vagy 60 másodperces fajtából. Az ügyfelek beszerezhetik ezeket a jogkivonatokat az általuk választott szállítótól. A titkos kulcsok legfeljebb 128 karakterből állhatnak, ami nem feltétlenül kompatibilis az összes jogkivonattal. A titkos kulcs csak az *a-z* vagy *A-Z* karaktereket és *az 1-7*számjegyeket tartalmazhatja, és a Base32-ben kell kódolni.

![OATH-tokenek feltöltése az MFA OATH tokenek panelre](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Az OATH hardvertokenek nyilvános előzetes verzió részeként támogatottak. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.

A tokenek beszerzése után azokat vesszővel tagolt (CSV) fájlformátumban kell feltölteni, beleértve az upn-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt, ahogy az a következő példában látható:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Győződjön meg arról, hogy a fejlécsort beilleszti a CSV-fájlba.

Miután megfelelően formázta a CSV-fájlként, a rendszergazda ezután jelentkezzen be az Azure Portalon, keresse meg az **Azure Active Directory** > **biztonsági** > **MFA** > **OATH-tokeneket,** és töltse fel az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően néhány percig is eltarthat. Kattintson a **Frissítés** gombra az aktuális állapot leminősítéséhez. Ha bármilyen hiba van a fájlban, akkor lehetősége van letölteni egy CSV fájlt, amely felsorolja a megoldandó hibákat. A letöltött CSV-fájlban szereplő mezőnevek eltérnek a feltöltött verziótól.

A hibák kijavítása után a rendszergazda aktiválhatja az egyes kulccsal az **Aktiválás gombra** kattintva az aktiválandó jogkivonatot, és beírja a tokenen megjelenő OTP-t.

A felhasználók legfeljebb öt OATH hardvertoken vagy hitelesítő alkalmazás, például a Microsoft Authenticator alkalmazás bármikor használatra konfigurált kombinációjával rendelkezhetnek.

## <a name="phone-options"></a>Telefon beállításai

### <a name="mobile-phone"></a>Mobiltelefon

Két lehetőség áll rendelkezésre a felhasználók számára a mobiltelefonok.

Ha a felhasználók nem szeretnék, hogy a mobiltelefonszámuk látható legyen a címtárban, de továbbra is jelszavas visszaállításra szeretnék használni, a rendszergazdák ne taszák ki a címtárban. A felhasználóknak a [jelszó-visszaállítási regisztrációs portálon](https://aka.ms/ssprsetup)keresztül kell feltöltenie **a Hitelesítési telefon** attribútumukat. A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem tették közzé.

A megfelelő működéshez a telefonszámok formátumának *+CountryCode Telefonszám,* például +1 4255551234 formátumúnak kell lennie.

> [!NOTE]
> Az országkód és a telefonszám között szóköznek kell lennie.
>
> A jelszó-visszaállítás nem támogatja a telefonbővítményeket. Még a +1 4255551234X12345 formátumban is a bővítmények et eltávolítja a hívás megkezdése előtt.

A Microsoft nem garantálja az egységes SMS- vagy hangalapú többtényezős hitelesítési gyorskézbesítést ugyanazzal a számmal. Felhasználóink érdekében a Microsoft bármikor hozzáadhat vagy eltávolíthat rövid kódokat, miközben az SMS-ek kézbesíthetőségének javítása érdekében útvonal-módosításokat végzünk. A Microsoft az Egyesült Államokon és Kanadán kívül nem támogatja a rövid kódokat az Országok/Régiók számára.

#### <a name="text-message"></a>Szöveges üzenet

A rendszer SMS-t küld az ellenőrző kódot tartalmazó mobiltelefonszámra. Adja meg a bejelentkezési felületen megadott ellenőrző kódot a folytatáshoz.

#### <a name="phone-call"></a>Telefonhívás

A megadott telefonszámon automatikus hanghívás történik. Fogadja a hívást, és nyomja meg a # gombot a telefon billentyűzetén a

> [!IMPORTANT]
> 2019 márciusátantól a telefonhívási beállítások nem lesznek elérhetők az MFA- és SSPR-felhasználók számára az ingyenes/próbaverziós Azure AD-bérlőkben. Az SMS-üzeneteket ez a változás nem érinti. A telefonhívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a módosítás csak az ingyenes/próba verziós Azure AD-bérlők.

### <a name="office-phone"></a>Munkahelyi telefon

A megadott telefonszámon automatikus hanghívás történik. Fogadja a hívást, és megnyomja a # gombot a telefon billentyűzetén a hitelesítéshez.

A megfelelő működéshez a telefonszámok formátumának *+CountryCode Telefonszám,* például +1 4255551234 formátumúnak kell lennie.

Az irodai telefon attribútumot a rendszergazda kezeli.

> [!IMPORTANT]
> 2019 márciusátantól a telefonhívási beállítások nem lesznek elérhetők az MFA- és SSPR-felhasználók számára az ingyenes/próbaverziós Azure AD-bérlőkben. Az SMS-üzeneteket ez a változás nem érinti. A telefonhívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a módosítás csak az ingyenes/próba verziós Azure AD-bérlők.

> [!NOTE]
> Az országkód és a telefonszám között szóköznek kell lennie.
>
> A jelszó-visszaállítás nem támogatja a telefonbővítményeket. Még a +1 4255551234X12345 formátumban is a bővítmények et eltávolítja a hívás megkezdése előtt.

### <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

A telefonszám használatával történő hitelesítési módszerekkel kapcsolatos gyakori problémák:

* Letiltott hívóazonosító egyetlen eszközön
   * Eszköz – problémamegoldás
* Rossz telefonszám, helytelen országkód, otthoni telefonszám és munkahelyi telefonszám
   * Felhasználói objektum és konfigurált hitelesítési módszerek – problémamegoldás. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Nem megfelelő PIN-kód lépett be
   * Ellenőrizze, hogy a felhasználó az Azure MFA-kiszolgálón regisztrált megfelelő PIN-kódot használta.Confirm user has used the correct PIN registered in Azure MFA Server.
* Hívás átirányítása hangpostára
   * Győződjön meg arról, hogy a felhasználó be van kapcsolva a telefon, és hogy a szolgáltatás elérhető a saját területükön, vagy használjon alternatív módszert.
* A felhasználó blokkolva van
   * A rendszergazda feloldja a felhasználó letiltását az Azure Portalon.
* Az SMS nem előfizetett a készüléken
   * A felhasználó változtassa meg a módszereket, vagy aktiválja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók (Nem észleltek telefonbemenetet, hiányoznak a DTMF-hangokkal kapcsolatos problémák, több eszközön blokkolt hívóazonosító vagy több eszközön blokkolt SMS)
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és sms-üzenetek hitelesítésre való továbbítására. Ha a fenti problémák bármelyikét tapasztalja, a felhasználó 5 percen belül legalább 5 alkalommal próbálja meg használni a módszert, és a microsoftos támogatási szolgálathoz való kapcsolatfelvételkor a felhasználó adatai elérhetők legyenek.

## <a name="app-passwords"></a>Alkalmazásjelszavak

Egyes nem böngészős alkalmazások nem támogatják a többtényezős hitelesítést, ha a felhasználó engedélyezve van a többtényezős hitelesítéshez, és megkísérli a nem böngészőalkalmazások használatát, nem tudnak hitelesíteni. Az alkalmazásjelszó lehetővé teszi a felhasználók számára a hitelesítés folytatását

Ha feltételes hozzáférési házirendeken keresztül kényszeríti ki a többtényezős hitelesítést, és nem a felhasználónkénti többtényezős hitelesítésen keresztül, nem hozhat létre alkalmazásjelszavakat. A hozzáférés vezérléséhez feltételes hozzáférési házirendeket használó alkalmazásoknak nincs szükségük alkalmazásjelszavakra.

Ha a szervezet össze van egyítve az SSO az Azure AD-vel, és az Azure MFA-t fogja használni, akkor vegye figyelembe az alábbi részleteket:

* Az alkalmazás jelszavát az Azure AD ellenőrzi, ezért megkerüli az összevonást. Az összevonás csak az alkalmazásjelszavak beállításakor használatos. Összevont (SSO) felhasználók számára a jelszavak a szervezeti azonosítóban tárolódnak. Ha a felhasználó elhagyja a vállalatot, az információnak a DirSync használatával kell a szervezeti azonosítóhoz áramlania. A fiók letiltása/törlése akár három órát is igénybe vehet a szinkronizálás, ami késlelteti az alkalmazásjelszavak letiltását/törlését az Azure AD-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszavakhoz nem érhető el helyszíni hitelesítésnaplózási/naplózási képesség.
* Bizonyos speciális architekturális tervek szükség lehet a szervezeti felhasználónév és jelszavak és alkalmazásjelszavak kombinációjának használatára, amikor kétlépéses ellenőrzést használnak az ügyfelekkel, attól függően, hogy hol hitelesítik magukat. A helyszíni infrastruktúrával hitelesítendő ügyfelek szervezeti felhasználónevet és jelszót kell használnia. Az Azure AD-vel hitelesítendő ügyfelek esetében az alkalmazás jelszavát kell használnia.
* Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazásjelszavakat. Ha engedélyeznie kell a felhasználóknak az alkalmazásjelszavak létrehozását, a szolgáltatásbeállítások alatt jelölje be a **Felhasználók számára alkalmazásjelszavak létrehozásához nem böngészőalkalmazásokba való bejelentkezést a** szolgáltatásbeállítások alatt.

## <a name="next-steps"></a>További lépések

[Önkiszolgáló jelszó-visszaállítás engedélyezése a szervezetszámára](quickstart-sspr.md)

[Az Azure többtényezős hitelesítésének engedélyezése a szervezetszámára](howto-mfa-getstarted.md)

[Kombinált regisztráció engedélyezése a bérlőben](howto-registration-mfa-sspr-combined.md)

[Végfelhasználói hitelesítési módszer konfigurációs dokumentációja](https://aka.ms/securityinfoguide)
