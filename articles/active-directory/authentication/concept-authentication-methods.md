---
title: Hitelesítési módszerek és szolgáltatások – Azure Active Directory
description: További információ a Azure Active Directoryban elérhető különböző hitelesítési módszerekről és szolgáltatásokról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 08f3a3c17a5f6ea519eea04c4b8c5e7025adcbac
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052358"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Milyen hitelesítési és ellenőrzési módszerek érhetők el az Azure Active Directoryban?

A Azure Active Directory (Azure AD) fiókjaihoz tartozó bejelentkezési élmény részeként különböző módokon végezheti el a felhasználók hitelesítését. A Felhasználónév és a jelszó a leggyakoribb módszer a felhasználók számára a hitelesítő adatok megadására. Az Azure AD modern hitelesítési és biztonsági funkcióival az alapszintű jelszavak további hitelesítési módszerekkel kiegészíthetők vagy cserélhetők le.

Az Azure AD-beli felhasználók a következő hitelesítési módszerek egyikének használatával hitelesíthetők:

* Hagyományos Felhasználónév és jelszó
* Az alkalmazás jelszavas nélküli bejelentkezésének Microsoft Authenticator
* A hardver-jogkivonat vagy a FIDO2 biztonsági kulcsa
* SMS-alapú jelszó nélküli bejelentkezés

Az Azure AD-ben számos fiók engedélyezhető az önkiszolgáló jelszó-visszaállítás (SSPR) vagy az Azure Multi-Factor Authentication számára. Ezek a funkciók további ellenőrzési módszereket is tartalmaznak, például telefonhívást vagy biztonsági kérdéseket. Javasoljuk, hogy a felhasználóknak több ellenőrzési módszert kell regisztrálniuk. Ha az egyik metódus nem érhető el egy felhasználó számára, dönthetnek úgy, hogy más módszerrel hitelesítik magukat.

Az alábbi táblázat ismerteti, hogy milyen metódusok érhetők el az elsődleges vagy másodlagos hitelesítéshez:

| Metódus | Elsődleges hitelesítés | Másodlagos hitelesítés |
| --- | --- | --- |
| [Jelszó](#password) | Yes | |
| [A Microsoft Authenticator alkalmazás](#microsoft-authenticator-app) | Igen (előzetes verzió) | MFA és SSPR |
| [FIDO2 biztonsági kulcsok (előzetes verzió)](#fido2-security-keys) | Yes | Csak MFA |
| [Az eskü szoftver jogkivonatai](#oath-software-tokens) | No | MFA |
| [A hardver-tokenek ESKÜje (előzetes verzió)](#oath-hardware-tokens-preview) | No | MFA |
| [SMS](#phone-options) | Igen (előzetes verzió) | MFA és SSPR |
| [Hanghívás](#phone-options) | No | MFA és SSPR |
| [Biztonsági kérdések](#security-questions) | No | Csak SSPR |
| [E-mail-cím](#email-address) | No | Csak SSPR |
| [Alkalmazásjelszavak](#app-passwords). | No | MFA csak bizonyos esetekben |

Ezek a hitelesítési módszerek konfigurálhatók a Azure Portalban, és egyre inkább a [Microsoft Graph REST API bétaverzióját](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használják.

Ez a cikk az Azure AD-ben elérhető különböző hitelesítési és ellenőrzési módszereket, valamint az adott korlátozásokat és korlátozásokat ismerteti.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Jelszó

Az Azure AD-jelszó gyakran az egyik elsődleges hitelesítési módszer. A jelszó-hitelesítési módszer nem tiltható le.

Még ha olyan hitelesítési módszert használ, mint például az [SMS-alapú bejelentkezés](howto-authentication-sms-signin.md) , ha a felhasználó nem használja a jelszót az aláíráshoz, a jelszó elérhető hitelesítési módszer marad.

## <a name="microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás

A hitelesítő alkalmazás további biztonsági szintet biztosít az Azure AD munkahelyi vagy iskolai fiókjához vagy a Microsoft-fiókhoz, és [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)és [Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)rendszerekhez érhető el. A Microsoft Authenticator alkalmazással a felhasználók jelszó nélküli módon hitelesíthetők a bejelentkezés során, illetve további ellenőrzési lehetőségként az önkiszolgáló jelszó-visszaállítás (SSPR) vagy az Azure Multi-Factor Authentication események során.

A felhasználók értesítéseket kaphatnak a mobil alkalmazástól, hogy jóváhagyják vagy megtagadják, vagy a hitelesítő alkalmazással olyan eskü-ellenőrző kódot állítanak elő, amely bejelentkezési felületen adható meg. Ha az értesítési és ellenőrző kódot is engedélyezi, a hitelesítő alkalmazást regisztráló felhasználók bármelyik módszert használhatják az identitásuk ellenőrzéséhez.

Ha a hitelesítő alkalmazást a Felhasználónév és jelszó kombináció helyett a bejelentkezési üzenetben szeretné használni, tekintse meg [a jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással (előzetes verzió)](howto-authentication-passwordless-phone.md)című témakört.

> [!NOTE]
> A felhasználók nem regisztrálhatják a SSPR, amikor engedélyezik a mobileszköz regisztrálását. Ehelyett a felhasználók regisztrálhatják a mobil alkalmazást a (z) szolgáltatásban [https://aka.ms/mfasetup](https://aka.ms/mfasetup) a kombinált biztonsági adatok regisztrációjának részeként [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Értesítés a Mobile App használatával

A hitelesítő alkalmazás segít megakadályozni a fiókok jogosulatlan elérését, és letilthatja a csalárd tranzakciókat, ha értesítéseket küld az okostelefonra vagy a táblaszámítógépre. A felhasználók megtekinthetik az értesítést, és ha ez jogos, válassza az **ellenőrzés**lehetőséget. Ellenkező esetben a **Megtagadás**lehetőséget is kiválaszthatja.

![A bejelentkezési folyamat elvégzésére szolgáló, a hitelesítő alkalmazás értesítésére szolgáló webböngészőt kérő példa képernyőképe](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Ha a szervezete Kínában dolgozik vagy Kínába utazik, az Android-eszközökön a *Mobile App Method használatával történő értesítés* nem működik abban az országban vagy régióban, mint a Google Play-szolgáltatások (beleértve a leküldéses értesítéseket) a régióban. Az iOS-értesítés azonban működik. Android-eszközök esetén az adott felhasználók számára elérhetővé kell tenni alternatív hitelesítési módszereket.

### <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a Mobile appből

A hitelesítő alkalmazás szoftver-tokenként használható az eskü-ellenőrző kód létrehozásához. A Felhasználónév és a jelszó megadása után adja meg a hitelesítő alkalmazás által a bejelentkezési felületen megadott kódot. Az ellenőrzőkód egy második hitelesítési módként szolgál.

Előfordulhat, hogy a felhasználók legfeljebb öt ESKÜvel rendelkező hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást) kombinálnak, amelyet bármikor használatra konfiguráltak.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállítás legmagasabb szintű biztonságának biztosítása érdekében, ha csak egy módszer szükséges az alaphelyzetbe állításhoz, a felhasználók számára csak egy hitelesítési kód adható meg.
>
> Ha két módszerre van szükség, a felhasználók visszaállíthatják az értesítési vagy ellenőrző kódokat a más engedélyezett módszerek kiegészítéseként.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

A (gyors identitás online) Szövetség segíti a nyílt hitelesítési szabványok előléptetését, és a jelszavak felhasználói hitelesítés formájában történő csökkentését. A FIDO2 a legújabb szabvány, amely magában foglalja a webes hitelesítési (WebAuthn) szabványt.

Ha a FIDO2 biztonsági kulcsait nem a Felhasználónév és jelszó kombinációja helyett szeretné használni, tekintse meg a [jelszó nélküli FIDO2 biztonsági kulcsának engedélyezése (előzetes verzió)](howto-authentication-passwordless-security-key.md)című témakört.

A felhasználók regisztrálhatnak és kijelölhetnek egy FIDO2 biztonsági kulcsot a bejelentkezési felületen a hitelesítés fő eszközeként. Ezek a FIDO2 biztonsági kulcsok általában USB-eszközök, de a Bluetooth vagy az NFC használatát is használhatják. Ha a hitelesítést kezelő hardvereszközt használ, a rendszer megnöveli a fiók biztonságát, mivel nincs olyan jelszó, amely elérhetővé vagy kitalált.

Az Azure AD FIDO2 biztonsági kulcsai jelenleg előzetes verzióban érhetők el. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>ESKÜ tokenek

Az eskü TOTP (időalapú egyszeri jelszó) egy nyílt szabvány, amely meghatározza, hogy az egyszeri jelszavas (OTP) kódok hogyan jönnek létre. Az eskü TOTP a kódok létrehozásához szoftver vagy hardver használatával valósítható meg. Az Azure AD nem támogatja az eskü HOTP, amely egy másik kód generálási szabvány.

### <a name="oath-software-tokens"></a>Az eskü szoftver jogkivonatai

A szoftveres eskü-tokenek jellemzően olyan alkalmazások, mint a Microsoft Authenticator alkalmazás és más hitelesítő alkalmazások. Az Azure AD létrehozza a titkos kulcsot, vagy a magot, amely az alkalmazásba belép, és az egyes OTP-k létrehozásához használatos.

A hitelesítő alkalmazás automatikusan generál kódokat, ha úgy állítja be a leküldéses értesítéseket, hogy egy felhasználó biztonsági másolattal rendelkezzen, még akkor is, ha az eszköz nem rendelkezik kapcsolattal. A más gyártóktól származó, ESKÜt használó alkalmazások is használhatók TOTP létrehozásához.

Néhány ESKÜt TOTP, ami azt jelenti, hogy a hardver-jogkivonatok programozhatók, ezért nem tartoznak hozzá előre programozott titkos kulcs vagy mag. Ezek a programozható hardveres tokenek a szoftver-jogkivonat telepítési folyamata által beszerzett titkos kulccsal vagy mag használatával állíthatók be. Az ügyfelek vásárolhatják meg ezeket a jogkivonatokat a választott gyártótól, és a titkos kulcsot vagy magot használják a gyártó telepítési folyamatában.

### <a name="oath-hardware-tokens-preview"></a>A hardver-tokenek ESKÜje (előzetes verzió)

Az Azure AD az eskü-TOTP SHA-1 tokenek használatát támogatja, amelyek 30 vagy 60 másodpercenként frissítik a kódokat. Az ügyfelek megvásárolhatják ezeket a jogkivonatokat a választott gyártótól.

Az eskü TOTP-tokenek általában titkos kulccsal, vagy a tokenben előre programozott magokkal rendelkeznek. Ezeket a kulcsokat az alábbi lépésekben leírtaknak megfelelően be kell állítani az Azure AD-be. A titkos kulcsok legfeljebb 128 karakterből állhatnak, amelyek nem kompatibilisek az összes jogkivonattal. A titkos kulcs csak az *a-z* , a-z és *a-z* , valamint a *1-7*karakterből állhat, és a *Base32*-ben kell kódolni.

A feldolgozható, programozható eskü TOTP a szoftveres jogkivonat telepítési folyamatában is beállítható az Azure AD-ben.

A nyilvános előzetes verzió részeként a rendszer a hardveres jogkivonatokat is támogatja. További információ az előzetes verziókról: a  [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![ESKÜ-tokenek feltöltése az MFA-eskü tokenek paneljére](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A jogkivonatok beszerzése után a következő példában látható módon fel kell tölteni egy vesszővel tagolt (CSV) fájlformátumot, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ügyeljen rá, hogy a fejlécsor szerepeljen a CSV-fájlban.

Miután megfelelően formázott CSV-fájlként, a rendszergazda bejelentkezhet a Azure Portalba, és megnyithatja **Azure Active Directory > biztonsági > MFA > eskü-tokeneket**, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a folyamat eltarthat néhány percig. Kattintson a **frissítés** gombra az aktuális állapot lekéréséhez. Ha bármilyen hiba van a fájlban, letöltheti azt a CSV-fájlt, amely felsorolja a feloldható hibákat. A letöltött CSV-fájl mezőinek neve eltér a feltöltött verziótól.

A hibák elhárítása után a rendszergazda ezután aktiválhatja az egyes kulcsokat, ha a jogkivonat **aktiválása** elemre kattint, és beírja a tokenen megjelenő OTP elemet.

Előfordulhat, hogy a felhasználók legfeljebb öt ESKÜvel rendelkező hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást) kombinálnak, amelyet bármikor használatra konfiguráltak.

## <a name="phone-options"></a>Telefonos beállítások

A szöveges üzenetekkel történő közvetlen hitelesítéshez beállíthatja [és engedélyezheti a felhasználók számára az SMS-alapú hitelesítést (előzetes verzió)](howto-authentication-sms-signin.md). Az SMS-alapú bejelentkezés kiválóan használható az előtérben dolgozóknak. Az SMS-alapú bejelentkezéshez a felhasználóknak nem kell tudniuk az alkalmazások és a szolgáltatások eléréséhez szükséges felhasználónevet és jelszót. A felhasználó Ehelyett beírja a regisztrált mobiltelefonszámát, egy ellenőrző kódot tartalmazó szöveges üzenetet kap, és a bejelentkezési felületen megadja azt.

A felhasználók az Azure Multi-Factor Authentication vagy az önkiszolgáló jelszó-visszaállítás (SSPR) során is ellenőrizhetik magukat mobiltelefon vagy irodai telefon formájában.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: *+ 1 4251234567*.

> [!NOTE]
> Az ország/régió kódja és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a *+ 1 4251234567X12345* formátumban.

### <a name="mobile-phone-verification"></a>Mobiltelefon ellenőrzése

Az Azure Multi-Factor Authentication vagy SSPR esetében a felhasználók dönthetnek úgy, hogy szöveges üzenetet kapnak a bejelentkezési felületen megjelenő ellenőrző kóddal, vagy telefonhívást fogadnak a megadott PIN-kód megadásához.

Ha a felhasználó nem szeretné, hogy a mobil telefonszáma megjelenjen a címtárban, de azt szeretné használni a jelszó alaphelyzetbe állításához, akkor a rendszergazdák nem tölthetik fel a telefonszámot a címtárban. Ehelyett a felhasználóknak fel kell tölteniük a **hitelesítési telefonos** attribútumot a következő helyen: [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem jelennek meg.

![Képernyőkép a Azure Portalről, amely a hitelesítési módszereket jeleníti meg egy telefonszámmal feltöltve](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft nem garantálja a konzisztens SMS-és hangalapú Azure-Multi-Factor Authentication küldését ugyanazzal a számmal. A felhasználók érdekében bármikor hozzáadhatjuk vagy eltávolíthatjuk a rövid kódokat, ahogy az útvonal-kiigazításokat az SMS-kézbesítés javítására is felhasználjuk. A Microsoft nem támogatja a rövid kódokat az országok/régiók számára a Egyesült Államok és Kanada mellett.

#### <a name="text-message-verification"></a>SMS-üzenet ellenőrzése

Ha a SSPR vagy az Azure Multi-Factor Authentication során SMS-t szeretne ellenőrizni, egy SMS-t küld a rendszer az ellenőrző kódot tartalmazó mobiltelefon-telefonszámra. A bejelentkezési folyamat befejezéséhez a megadott ellenőrző kód bekerül a bejelentkezési felületre.

#### <a name="phone-call-verification"></a>Telefonhívás ellenőrzése

Ha a SSPR vagy az Azure Multi-Factor Authenticationban telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikus hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer kéri a felhasználótól, hogy adja meg a PIN-kódját, majd a billentyűzetén a # értéket.

### <a name="office-phone-verification"></a>Irodai telefon ellenőrzése

Az Office Phone-attribútumot az Azure AD rendszergazdája felügyeli, és a felhasználó nem regisztrálhat.

Ha a SSPR vagy az Azure Multi-Factor Authenticationban telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikus hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer kéri a felhasználótól, hogy adja meg a PIN-kódját, majd a billentyűzetén a # értéket.

### <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

Ha problémák merülnek fel az Azure AD-beli telefonos hitelesítéssel kapcsolatban, tekintse át a következő hibaelhárítási lépéseket:

* Blokkolt hívóazonosító egyetlen eszközön.
   * Tekintse át az eszközön konfigurált blokkolt számokat.
* Helytelen a telefonszám vagy az ország/régió kódja, vagy a személyes telefonszám és a munkahelyi telefonszám közötti zűrzavar.
   * A felhasználói objektum és a konfigurált hitelesítési módszerek hibáinak megoldása. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Helytelen PIN-kód van megadva.
   * Erősítse meg, hogy a felhasználó a megfelelő PIN-kódot használta a fiókjához való regisztrációhoz.
* A hívás a hangpostára lett továbbítva.
   * Győződjön meg arról, hogy a felhasználó telefonja be van kapcsolva, és hogy a szolgáltatás elérhető a saját területén, vagy használjon másik módszert.
* A felhasználó blokkolva van
   * Az Azure AD-rendszergazda feloldja a felhasználót a Azure Portal.
* Az SMS nincs előfizetve az eszközön.
   * A felhasználó módosíthatja a metódusokat, vagy aktiválhatja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók, például nem észlelhetők a telefonos eszközök, a DTMF-hangok hiánya, a letiltott hívóazonosító több eszközön, illetve az SMS több eszközön való letiltása.
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és SMS-üzenetek továbbítására a hitelesítéshez. Ha a fenti problémák bármelyikét látja, akkor a felhasználó öt percen belül legalább ötször megpróbálta használni a metódust, és a felhasználó információi elérhetők a Microsoft ügyfélszolgálatával való kapcsolatfelvételkor.

## <a name="security-questions"></a>Biztonsági kérdések

A bejelentkezési esemény során a biztonsági kérdések nem használhatók hitelesítési módszerként. Ehelyett biztonsági kérdéseket használhat az önkiszolgáló jelszó-visszaállítási (SSPR) folyamat során annak megerősítéséhez, hogy ki vagy. A rendszergazdai fiókok nem használhatnak biztonsági kérdéseket ellenőrzési módszerként a SSPR.

Amikor a felhasználók regisztráljanak a SSPR, a rendszer felkéri a használandó hitelesítési módszerek kiválasztására. Ha úgy döntenek, hogy biztonsági kérdéseket használnak fel, válasszon ki egy kérdést, hogy Kérdezzen rá, majd adja meg a saját válaszait.

![A biztonsági kérdések hitelesítési módszereit és lehetőségeit megjelenítő Azure Portal képernyőképe](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> A biztonsági kérdések tárolása a címtárban található felhasználói objektumon történik, és a felhasználók csak a regisztráció során válaszolnak. Nincs mód arra, hogy a rendszergazda beolvassa vagy módosítsa a felhasználó kérdéseit és válaszait.

A biztonsági kérdések kevésbé biztonságosak, mint más metódusok, mert egyes felhasználók tudják, hogy a válaszokat egy másik felhasználó kérdéseire. Ha a SSPR biztonsági kérdéseit használja, azt javasoljuk, hogy egy másik módszerrel együtt használja őket. A felhasználó kérheti a Microsoft Authenticator alkalmazás vagy a telefonos hitelesítés használatát a SSPR folyamat során a személyazonosságának ellenőrzéséhez, és csak akkor válassza a biztonsági kérdéseket, ha azok nem rendelkeznek telefonos vagy regisztrált eszközzel.

### <a name="predefined-questions"></a>Előre meghatározott kérdések

A következő előre meghatározott biztonsági kérdések használhatók ellenőrzési módszerként a SSPR. Az összes ilyen biztonsági kérdés fordítása és honosítása a felhasználó böngésző területi beállítása alapján a Microsoft 365 nyelvének teljes készletében történik:

* Melyik városban teljesítette az első házastársa/partnerét?
* Milyen városokban felelt meg a szülei?
* Melyik városban él a legközelebbi testvér?
* Melyik városban született az apja?
* Melyik városban volt az első feladata?
* Melyik városban született az anyja?
* Mi a város a New Year 's 2000-ben?
* Mi a vezetékneve kedvenc tanárának a középiskolában?
* Mi a neve annak a Főiskolának, amelyet alkalmazott, de nem vett részt?
* Mi a neve annak a helynek, amelyben az első esküvői fogadást megtartotta?
* Mi az apád középső neve?
* Mi a kedvenc étele?
* Mi az anyai nagymama első és vezetékneve?
* Mi az anyja középső neve?
* Mi a legrégebbi testvére születésnapjának hónapja és éve? (például 1985 november)
* Mi a legrégebbi testvérének középső neve?
* Apai nagyapa vezetékneve és keresztneve?
* Mi a legfiatalabb testvér középső neve?
* Milyen iskolát vett részt a hatodik osztályban?
* Mi volt az első és az utolsó név a gyermekkori legjobb barátodnak?
* Mi volt az első és az utolsó név, ami az első jelentős?
* Mi volt a vezetékneve kedvenc iskolai tanárának?
* Mi volt az első autójának vagy motorkerékpárjának gyártmánya és modellje?
* Mi volt az első iskolájának neve?
* Mi volt a kórház neve, amelyben született?
* Mi volt az első gyermekkori kezdőlapjának neve?
* Mi volt a gyermekkori hős neve?
* Mi volt a kedvenc töltött állatának neve?
* Mi volt az első kisállat neve?
* Mi volt a gyerekkori beceneve?
* Mi volt a kedvenc sportja a középiskolában?
* Mi volt az első feladata?
* Mi volt a gyermekkori telefonszámának utolsó négy számjegye?
* Ha fiatal volt, mit szeretne, amikor felnőtt?
* Ki a leghíresebb személy, akivel valaha is találkozott?

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

A további rugalmasság érdekében megadhatja saját egyéni biztonsági kérdéseit. Az egyéni biztonsági kérdések maximális hossza 200 karakter.

Az egyéni biztonsági kérdések nincsenek automatikusan honosítva, mint az alapértelmezett biztonsági kérdések. Minden egyéni kérdés a rendszergazda felhasználói felületen megadott nyelven jelenik meg, még akkor is, ha a felhasználó böngésző területi beállítása eltér. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

### <a name="security-question-requirements"></a>Biztonsági kérdésekkel kapcsolatos követelmények

Az alapértelmezett és az egyéni biztonsági kérdések esetében a következő követelmények és korlátozások érvényesek:

* A minimális válasz karakteres korlát három karakterből állhat.
* A maximális válasz karakteres korlátja 40 karakter.
* A felhasználók több alkalommal nem tudnak válaszolni ugyanarra a kérdésre.
* A felhasználók nem tudnak ugyanazt a választ adni több kérdéshez.
* Bármely karakterkészlet használható a kérdések és válaszok meghatározására, beleértve a Unicode karaktereket is.
* A megadott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztráláshoz szükséges kérdések számával.

## <a name="email-address"></a>E-mail-cím

Egy e-mail-cím nem használható közvetlen hitelesítési módszerként. Az e-mail-cím csak az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési beállításaként érhető el. Ha a SSPR során e-mail-cím van kiválasztva, a rendszer e-mailt küld a felhasználónak a hitelesítési/ellenőrzési folyamat elvégzéséhez.

A SSPR való regisztráció során a felhasználó megadja a használni kívánt e-mail-címet. Azt javasoljuk, hogy a vállalati fióktól eltérő e-mail-fiókot használjanak, hogy biztosan hozzáférhessenek a SSPR.

## <a name="app-passwords"></a>Alkalmazásjelszavak.

Bizonyos régebbi, nem böngészőbeli alkalmazások nem értik a hitelesítési folyamat szüneteltetését vagy megszakítását. Ha egy felhasználó engedélyezve van a többtényezős hitelesítéshez, és megkísérli használni a régebbi, nem böngészőbeli alkalmazások egyikét, általában nem sikerül hitelesíteni őket. Az alkalmazás jelszava lehetővé teszi a felhasználók számára, hogy megszakítás nélkül továbbra is sikeresen hitelesítsék a régebbi, nem böngészőbeli alkalmazásokat.

Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Ha lehetővé szeretné tenni a felhasználóknak az alkalmazás jelszavának létrehozását, jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése** jelölőnégyzetet, ha a felhasználó Azure-multi-Factor Authentication tulajdonságainál a *szolgáltatás beállításai* között a nem böngésző alkalmazások számára szeretne bejelentkezni.

![Képernyőkép a Azure Portalról, amely a többtényezős hitelesítés szolgáltatás beállításait mutatja be az alkalmazás jelszavainak felhasználójának engedélyezéséhez](media/concept-authentication-methods/app-password-authentication-method.png)

Ha a feltételes hozzáférési szabályzatok segítségével kényszeríti az Azure Multi-Factor Authenticationt, és nem a felhasználónkénti MFA-t használja, nem hozhat létre alkalmazás-jelszavakat. A feltételes hozzáférési szabályzatokat használó modern alkalmazások hozzáférés-vezérléséhez nincs szükségük az alkalmazás jelszavára.

Ha a szervezete egyszeri bejelentkezéshez (SSO) van összevont Azure AD-val, és az Azure Multi-Factor Authentication-t használja, a következő szempontokat kell figyelembe vennie:

* Az alkalmazás jelszavait az Azure AD ellenőrzi, így megkerüli az összevonást. Az összevonás csak az alkalmazás jelszavának beállításakor használatos. Összevont (SSO) felhasználók esetén a rendszer a jelszavakat a szervezeti AZONOSÍTÓban tárolja. Ha a felhasználó elhagyja a vállalatot, az információnak a szervezet AZONOSÍTÓját kell használnia az rSync használatával. A fiók letiltási vagy törlési eseményeinek szinkronizálása akár három órát is igénybe vehet, ami késlelteti az alkalmazás jelszavának letiltását/törlését az Azure AD-ben.
* Az alkalmazások jelszavai nem teljesítik a helyszíni ügyfél Access Control beállításait.
* Nem érhető el helyszíni hitelesítési naplózási vagy naplózási funkció az alkalmazás jelszavához.
* Bizonyos speciális építészeti kialakításokhoz a hitelesítésük helyétől függően a szervezeti felhasználónevek és jelszavak és az alkalmazások jelszavainak együttes használata szükséges.
    * A helyszíni infrastruktúrával hitelesítő ügyfelek esetében szervezeti felhasználónevet és jelszót kell használnia.
    * Az Azure AD-vel hitelesítő ügyfelek esetében használja az alkalmazás jelszavát.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure multi-Factor Authentication][tutorial-azure-mfa]című oktatóanyagot.

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ az MFA-fogalmakról: [how Azure multi-Factor Authentication Works][concept-mfa].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
