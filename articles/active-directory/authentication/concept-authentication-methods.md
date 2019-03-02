---
title: Az Azure AD-hitelesítési módszerek
description: Milyen hitelesítési módszerek érhetők el az SSPR MFA és az Azure AD-ben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d90407dab29f431f5a592e077c859aea997c4c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214095"
---
# <a name="what-are-authentication-methods"></a>Mik a hitelesítési módszerek?

További információk a hitelesítési módszerek és biztonsági adatai áttekintéséhez néven erősítse meg, akinek mondja magát a társított szolgáltatások használata esetén az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) és a multi-factor Authentication (MFA) kérhet.

A házirend milyen hitelesítési módszerek érhetők el az SSPR és az MFA felhasználói rendszergazdák adhatja meg. Bizonyos hitelesítési módszereknél nem lehet elérhető összes szolgáltatáshoz.

A Microsoft nyomatékosan javasolja a rendszergazdák engedélyezés felhasználóknak, hogy több, mint a hitelesítési módszerek a minimális számú abban az esetben, ha nincs hozzáférése egy.

|Hitelesítési módszer|Használat|
| --- | --- |
| Jelszó | Többtényezős hitelesítés és az SSPR |
| Biztonsági kérdések | SSPR Only |
| E-mail-cím | SSPR Only |
| A Microsoft Authenticator alkalmazás | Többtényezős hitelesítés és az SSPR nyilvános előzetes verzió |
| OATH-token hardver | A többtényezős hitelesítés és az SSPR nyilvános előzetes verzió |
| SMS | Többtényezős hitelesítés és az SSPR |
| Hanghívás | Többtényezős hitelesítés és az SSPR |
| Alkalmazásjelszók | Bizonyos esetekben csak a többtényezős hitelesítés |

![Használja a bejelentkezési képernyőn hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH hardvertokenek többtényezős hitelesítés és az SSPR és a Mobile app értesítési vagy a mobilalkalmazás-kód módszerek az Azure AD önkiszolgáló jelszó-visszaállítás nyilvános előzetes verziójú funkciók az Azure Active Directory rendszer. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Jelszó

Az Azure AD-jelszó számít egy hitelesítési módszert. Az egyik módszer, amely **nem lehet letiltani**.

## <a name="security-questions"></a>Biztonsági kérdések

Biztonsági kérdések érhetők el **csak az Azure AD önkiszolgáló jelszó-visszaállítási** a nem rendszergazdai fiókoknak.

Biztonsági kérdések használatakor javasoljuk egy másik módszerrel együtt használja őket. Biztonsági kérdések kevésbé biztonságos, mint más módszerekkel is lehet, mivel vannak, akik előfordulhat, hogy egy másik felhasználó kérdésekre adott válaszokat.

> [!NOTE]
> Biztonsági kérdések a címtárban lévő felhasználói objektum a privát módon és biztonságosan tárolja, és csak választ a felhasználók regisztrációja során. Nincs lehetőség a rendszergazda számára olvasására vagy módosítására egy felhasználói kérdések és válaszok.
>

### <a name="predefined-questions"></a>Előre definiált kérdések

* Melyik városban ismerkedett meg az első házastársával/párjával?
* Melyik településen találkoztak a szülei?
* Melyik városban lakik a legközelebbi rokona?
* Az édesapja melyik városban született?
* Melyik városban volt az első munkahelye?
* Melyik városban született az édesanyja?
* Hol töltötte a 2000. év újévét?
* Mi a vezetékneve és az utóneve a kedvenc középiskolai tanárának?
* Milyen felsőoktatási intézménybe jelentkezett, de nem vették fel?
* Hogy hívták a helyet, ahol az első lakodalmát tartották?
* Mi az apja második utóneve?
* Mi a kedvenc étele?
* Anyai nagyanyja vezeték- és keresztneve?
* Mi az édesanyja második utóneve?
* Mi a legidősebb testvérének születési éve és hónapja? (Példa: 1985. November)
* Mi a második utóneve a legidősebb testvérének?
* Mi a vezetékneve és az utóneve az apai nagyapjának?
* Mi a második utóneve a legfiatalabb testvérének?
* Melyik iskolában végezte el a hatodik osztályt?
* Mi volt a vezetékneve és az utóneve a legjobb gyerekkori barátjának?
* Mi volt a vezetékneve és az utóneve az első szerelmének?
* Mi volt a kedvenc általános iskolai tanárának vezetékneve?
* Mi volt az első autója vagy motorkerékpárja márkája és típusa?
* Hogy hívták az első iskoláját?
* Mi a neve annak a kórháznak, ahol született?
* Milyen utcában lakott gyermekkorában?
* Hogy hívják a gyermekkori példaképét?
* Kedvenc plüssállatának neve?
* Mi volt az első háziállatának neve?
* Mi volt a beceneve gyerekkorában?
* Mi volt a kedvenc sportja a középiskolában?
* Mi volt az első munkaköre?
* Mi volt a gyerekkori telefonszámának utolsó négy számjegye?
* Gyerekkorában mi szeretett volna lenni, ha majd felnő?
* Ki a leghíresebb ember, akivel életében találkozott?

Az előre definiált biztonsági kérdések lefordított és a felhasználó a böngésző nyelve alapján Office 365 nyelveken a teljes körű honosítva.

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

Nem honosított egyéni biztonsági kérdések. Minden egyéni kérdéseket vannak feltüntetve ugyanazt a nyelvet, szerepel a rendszergazdai felhasználói felület, akkor is, ha a felhasználó a böngésző nyelve nem egyezik. Ha honosított kérdésekre, az előre definiált kérdésekre kell használnia.

Egyéni biztonsági kérdések maximális hossza 200 karakternél hosszabb.

### <a name="security-question-requirements"></a>Biztonsági kérdés követelmények

* A minimális válasz karakterszámot három karakterből áll.
* A válasz maximális karakterszámot 40 karakterből áll.
* Felhasználók ugyanezt a kérdést egynél több alkalommal nem válaszol.
* Felhasználók nem adhatók meg a kérdéshez eltérő válaszokat.
* Bármely karakterkészlet segítségével határozza meg a kérdéseket és válaszokat, beleértve a Unicode-karaktereket.
* A megadott kérdések száma nagyobb vagy egyenlő, amelyek korábban regisztrálásához szükséges kérdések száma kell lennie.

## <a name="email-address"></a>E-mail-cím

E-mail-cím áll rendelkezésre **csak az Azure AD önkiszolgáló jelszó-visszaállítási**.

A Microsoft azt javasolja, hogy egy e-mail-fiókot, amely nincs szüksége a felhasználó Azure AD jelszó használatát.

## <a name="microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás

A Microsoft Authenticator alkalmazás egy további, az Azure ad-ben munkahelyi vagy iskolai Microsoft-fiókja biztonsági szintet biztosít.

A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el.

> [!NOTE]
> Felhasználó nem fog regisztrálni az mobilalkalmazás regisztráció önkiszolgáló jelszó-visszaállítás során. Ehelyett a felhasználók regisztrálhatják saját mobilalkalmazást található [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) vagy a biztonsági adatok regisztrálása verzió díjának [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Értesítés mobilalkalmazáson keresztül

A Microsoft Authenticator alkalmazás segítségével a fiókokhoz való illetéktelen hozzáférés megakadályozása és a egy rövid értesítést a okostelefonján vagy táblagépén küldésével állítsa le a csalárd tranzakciókat. Felhasználók tekintse meg az értesítést, és ha az megbízható, válassza ki az ellenőrzés. Ellenkező esetben Megtagadás kijelölve.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállítás amikor csak egyik módja szükség a visszaállításhoz, ellenőrző kódot csak a lehetőség a felhasználók számára elérhető **annak biztosítása érdekében a legmagasabb szintű biztonság**.
>
> Ha két módszer szükség felhasználók tudják alaphelyzetbe **vagy** értesítési **vagy** ellenőrzőkódot kívül bármely más módszerek engedélyezve van.
>

Ha engedélyezi a használatát mindkét értesítés, mobilalkalmazás és az ellenőrző kódot a mobilalkalmazásban, felhasználók, akik regisztrálni a Microsoft Authenticator alkalmazás használatával értesítést értesítés és kód is használhatják az identitásukat.

### <a name="verification-code-from-mobile-app"></a>Mobilalkalmazás ellenőrzőkódja

A Microsoft Authenticator alkalmazást, vagy más harmadik felek alkalmazásaihoz használható szoftvertokenként OATH-Ellenőrzőkód generálásához. Miután megadta a felhasználónevét és jelszavát, írja be a bejelentkezési képernyőn az alkalmazás által biztosított kódot. Az ellenőrző kódot biztosít egy második hitelesítési mód.

> [!WARNING]
> A önkiszolgáló jelszó-átállítási mikor csak egy módszert szükség ellenőrző kód alaphelyzetbe állítása az egyetlen lehetőség a felhasználók számára elérhető **annak biztosítása érdekében a legmagasabb szintű biztonság**.
>

## <a name="oath-hardware-tokens-public-preview"></a>OATH hardvertokenek (nyilvános előzetes verzió)

A rendszer OATH nyílt szabvány, amely megadja, hogy egyszeri jelszavas (OTP) kódok jönnek létre. Azure ad-ben az OATH-TOTP SHA-1-tokenek a 30 másodperces vagy 60 másodperces fajta használatát támogatja. Ügyfelek is be kell szereznie ezek a jogkivonatok a szállítótól választott eszközükön. Vegye figyelembe, hogy a titkos kulcsok legfeljebb 128 karakter hosszúságú lehet, nem lesz kompatibilis az összes jogkivonat.

![Az OATH-tokenek tölt fel az MFA-kiszolgáló OATH-tokenek panel az Azure Portalon](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Az OATH-tokenek hardver részeként egy nyilvános előzetes verziója támogatott. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Miután jogkivonatok beszerzett, fel kell tölteni egy vesszővel tagolt (CSV) fájlformátumban, például az egyszerű felhasználónév, sorozatszám, titkos kulcs, időtartam alatt, gyártó és modell, az alábbi példában.

```
upn,serial number,secret key,timeinterval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ellenőrizze, hogy a fejléc sorában szerepeltetni a CSV-fájl, ahogy fent látható.

Egyszer megfelelően formázott CSV-fájl, a rendszergazda ezután jelentkezzen be az Azure Portalon, és keresse meg **Azure Active Directory**, **MFA-kiszolgáló**, **OATH-tokenek**, és az eredményül kapott CSV-fájl feltöltése.

A CSV-fájl méretétől függően eltarthat néhány perc alatt dolgozza fel. Kattintson a **frissítése** gombra az aktuális állapotát. Ha hiba történik a fájlban, akkor ajánlati feloldhatja az esetleges hibákat a CSV-fájl letöltése lehetőséget.

Hibák elhárítása után a rendszergazda ezután aktiválhatja minden kulcs kattintva **aktiválás** a token aktiválását és belépés az egyszeri Jelszavas jelenik meg a jogkivonatot.

Előfordulhat, hogy a felhasználók legfeljebb 5 hardver OATH-tokenek vagy authenticator alkalmazásokhoz, mint a Microsoft Authenticator alkalmazás bármikor használatra konfigurált kombinációját.

## <a name="mobile-phone"></a>Mobiltelefon

Két lehetőség áll rendelkezésre a felhasználók számára a mobiltelefonon.

Ha a felhasználók nem szeretnének a mobiltelefonszám lesznek láthatók a címtárban, de azok továbbra is azt szeretné használni a jelszó-visszaállításhoz, a rendszergazdák nem kitölti ezt a címtárban. Felhasználók fel kell töltenie saját **hitelesítéshez használt telefon** keresztül attribútum a [jelszóátállítási regisztrációs portál](https://aka.ms/ssprsetup). A rendszergazdák ezt az információt a felhasználói profil látható, de nincs közzétéve máshol.

Megfelelően működjön, telefonszámokat a következő formátumban kell lennie *és CountryCode PhoneNumber*, például + 1 4255551234.

> [!NOTE]
> Szükség van egy országhívószámát és telefonszámát közötti hely.
>
> Új jelszó kérése a telefonos mellékek nem támogatja. A + 1 4255551234 X 12345 formátumban, még akkor is, bővítmények előtt távolítsa el a a hívást kezdeményeznek.

### <a name="text-message"></a>Szöveges üzenet

A mobiltelefonszámot egy ellenőrző kódot tartalmazó SMS küld. Adja meg a bejelentkezési felületen, a folytatáshoz a megadott ellenőrző kód.

### <a name="phone-call"></a>Telefonhívás

Automatikus hanghívást indít, adja meg a telefonszámot kérés érkezett. A hívás, és nyomja le a hitelesítéshez lenyomja a telefon billentyűzetén a #

> [!IMPORTANT]
> A telefonhívási beállítások verzióját 2019. március kezdve nem lesz elérhető ingyenes vagy próbaverziója az Azure AD-bérlők MFA és az SSPR felhasználók számára. SMS-ezni, ez a változás nem érinti. Telefonhívás továbbra is elérhető a felhasználók számára a fizetős Azure AD-bérlőt. Ez a változás csak az Azure AD ingyenes vagy próbaverziója bérlők hatással van.

## <a name="office-phone"></a>Irodai telefon

Automatikus hanghívást indít, adja meg a telefonszámot kérés érkezett. A hívás és a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot.

Megfelelően működjön, telefonszámokat a következő formátumban kell lennie *és CountryCode PhoneNumber*, például + 1 4255551234.

Az office telefonszám attribútuma a rendszergazda felügyeli.

> [!IMPORTANT]
> A telefonhívási beállítások verzióját 2019. március kezdve nem lesz elérhető ingyenes vagy próbaverziója az Azure AD-bérlők MFA és az SSPR felhasználók számára. SMS-ezni, ez a változás nem érinti. Telefonhívás továbbra is elérhető a felhasználók számára a fizetős Azure AD-bérlőt. Ez a változás csak az Azure AD ingyenes vagy próbaverziója bérlők hatással van.

> [!NOTE]
> Szükség van egy országhívószámát és telefonszámát közötti hely.
>
> Új jelszó kérése a telefonos mellékek nem támogatja. A + 1 4255551234 X 12345 formátumban, még akkor is, bővítmények előtt távolítsa el a a hívást kezdeményeznek.

## <a name="app-passwords"></a>Alkalmazásjelszavak

Bizonyos böngészőn kívüli alkalmazások nem támogatja a többtényezős hitelesítést, ha egy felhasználó a többtényezős hitelesítés engedélyezve van, és megkísérli böngészőn kívüli alkalmazások használatához, nem tudják hitelesíteni. Egy alkalmazásjelszót lehetővé teszi, hogy a felhasználók hitelesítése

Feltételes hozzáférési házirendek és a felhasználónkénti MFA keresztül nem kényszeríti a multi-factor Authentication, az alkalmazásjelszavak nem hozható létre. Az alkalmazásjelszavak nem kell hozzáférés vezérléséhez feltételes hozzáférési szabályzatokat használó alkalmazásokat.

Ha a szervezet össze van vonva az Azure AD egyszeri bejelentkezéshez, és szeretné használni az Azure MFA, majd vegye figyelembe a következő adatokat:

* Az alkalmazás jelszavának ellenőrizte az Azure AD-e, és így megkerüli az összevonási. Összevonási csak akkor használható, állítson be alkalmazásjelszót. Összevont felhasználók (SSO), a jelszavak tárolódnak a szervezeti azonosítójával. Ha a felhasználó elhagyja a vállalatot, a megjelenő rendelkezik áramlanak a DirSync használatával. A fiókok akár három órát vehet való szinkronizálás, ami késlelteti az alkalmazásjelszó letiltását/törlését az Azure ad-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Alkalmazásjelszavak használata esetén nem a helyszíni hitelesítési naplózás/naplózási képesség érhető el.
* Bizonyos speciális architekturális tervek a szervezeti felhasználónévvel és a jelszavak és az alkalmazásjelszók kombinációját használó ügyfelek, attól függően, hol hitelesítéshez a kétlépéses ellenőrzés használata esetén lehet szükség. Az ügyfelek számára, amely egy helyszíni infrastruktúrát a hitelesítésre akkor érdemes használnia a szervezeti felhasználónévvel és jelszóval. -Ügyfelek, amelyek az Azure AD hitelesítése akkor érdemes használnia az alkalmazásjelszót.
* Alapértelmezés szerint a felhasználók az alkalmazásjelszavak nem hozható létre. Ha szeretné lehetővé teszik a felhasználók könnyedén hozhatnak létre alkalmazásjelszókat, válassza ki a **engedélyezése a felhasználóknak alkalmazásjelszavakat jelentkezzen be a böngészőn kívüli alkalmazások lehetőséget** szolgáltatás beállításaiban.

## <a name="next-steps"></a>További lépések

[A szervezet számára önkiszolgáló jelszóátállítás engedélyezése](quickstart-sspr.md)

[Az Azure multi-factor Authentication szolgáltatás engedélyezése a szervezet számára](howto-mfa-getstarted.md)

[A bérlő kombinált regisztrációs engedélyezése](howto-registration-mfa-sspr-combined.md)

[Végfelhasználói hitelesítés metódus konfigurációs dokumentációja](https://aka.ms/securityinfoguide)
