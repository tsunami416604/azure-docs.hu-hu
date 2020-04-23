---
title: Hitelesítési módszerek – Azure Active Directory
description: Az Azure AD-ben elérhető hitelesítési módszerek az MFA és a SSPR esetében
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

Rendszergazdaként válassza az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszerei lehetőséget, ha a felhasználóknak több hitelesítési módszert kell regisztrálniuk. Ha egy felhasználó hitelesítési módszere nem érhető el, dönthet úgy, hogy egy másik módszerrel végez hitelesítést.

A rendszergazdák meghatározhatják a szabályzatban, hogy mely hitelesítési módszerek érhetők el a SSPR és az MFA felhasználói számára. Előfordulhat, hogy egyes hitelesítési módszerek nem állnak rendelkezésre az összes szolgáltatás számára. A szabályzatok konfigurálásával kapcsolatos további információkért tekintse meg a cikk az önkiszolgáló [jelszó-visszaállítás sikeres](howto-sspr-deployment.md) elindítását és [a felhőalapú Azure-multi-Factor Authentication tervezését](howto-mfa-getstarted.md) ismertető cikket.

A Microsoft nagymértékben javasolja a rendszergazdáknak, hogy a minimálisan szükséges számú hitelesítési módszer közül többet válasszanak, ha nem férnek hozzájuk.

|Hitelesítési módszer|Használat|
| --- | --- |
| Jelszó | MFA és SSPR |
| Biztonsági kérdések | Csak SSPR |
| E-mail-cím | Csak SSPR |
| A Microsoft Authenticator alkalmazás | MFA és SSPR |
| A hardver-token ESKÜje | Az MFA és a SSPR nyilvános előzetes verziója |
| SMS | MFA és SSPR |
| Hanghívás | MFA és SSPR |
| Alkalmazásjelszavak. | MFA csak bizonyos esetekben |

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Az MFA és a SSPR-hez tartozó hardver-tokenek a Azure Active Directory nyilvános előzetes verziójának funkciói. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Jelszó

Az Azure AD-jelszó hitelesítési módszernek minősül. Ez az egyetlen olyan metódus, amely **nem tiltható le**.

## <a name="security-questions"></a>Biztonsági kérdések

A biztonsági kérdések **csak az Azure ad önkiszolgáló jelszó-visszaállítási szolgáltatásában** érhetők el a nem rendszergazdai fiókokra.

Ha biztonsági kérdéseket használ, azt javasoljuk, hogy más módszerekkel együtt használja őket. A biztonsági kérdések kevésbé biztonságosak, mint más metódusok, mert egyes felhasználók tudják, hogy a válaszokat egy másik felhasználó kérdéseire.

> [!NOTE]
> A biztonsági kérdések tárolása a címtárban található felhasználói objektumon történik, és a felhasználók csak a regisztráció során válaszolnak. A rendszergazda nem tudja beolvasni vagy módosítani a felhasználó kérdéseit és válaszait.
>

### <a name="predefined-questions"></a>Előre meghatározott kérdések

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
* Mi a legrégebbi testvére születésnapjának hónapja és éve? (például 1985. november)
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

Az előre definiált biztonsági kérdések mindegyike le van fordítva és honosítva van az Office 365 teljes készletében, a felhasználó böngésző területi beállítása alapján.

### <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

Az egyéni biztonsági kérdések nincsenek honosítva. Minden egyéni kérdés a rendszergazda felhasználói felületen megadott nyelven jelenik meg, még akkor is, ha a felhasználó böngésző területi beállítása eltér. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

Az egyéni biztonsági kérdések maximális hossza 200 karakter.

### <a name="security-question-requirements"></a>Biztonsági kérdésekkel kapcsolatos követelmények

* A minimális válasz karakteres korlát három karakterből állhat.
* A maximális válasz karakteres korlátja 40 karakter.
* A felhasználók több alkalommal nem tudnak válaszolni ugyanarra a kérdésre.
* A felhasználók nem tudnak ugyanazt a választ adni több kérdéshez.
* Bármely karakterkészlet használható a kérdések és válaszok meghatározására, beleértve a Unicode karaktereket is.
* A megadott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztráláshoz szükséges kérdések számával.

## <a name="email-address"></a>E-mail-cím

Az e-mail **-cím csak az Azure ad önkiszolgáló jelszó-visszaállítási szolgáltatásában**érhető el.

A Microsoft olyan e-mail-fiók használatát javasolja, amely nem igényli a felhasználó Azure AD-jelszavának elérését.

## <a name="microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás

A Microsoft Authenticator alkalmazás további biztonsági szintet biztosít az Azure AD munkahelyi vagy iskolai fiókjához vagy a Microsoft-fiókhoz.

A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) rendszereken érhető el.

> [!NOTE]
> A felhasználók nem regisztrálhatják a mobil alkalmazásaikat az önkiszolgáló jelszó-visszaállításhoz való regisztráláskor. Ehelyett a felhasználók a biztonsági adatok regisztrációjának előzetes [https://aka.ms/mfasetup](https://aka.ms/mfasetup) verziójában regisztrálhatják a Mobile alkalmazást [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Értesítés a Mobile App használatával

A Microsoft Authenticator alkalmazás segít megakadályozni a fiókok jogosulatlan elérését, és letilthatja a csalárd tranzakciókat, ha értesítéseket küld az okostelefonra vagy a táblaszámítógépre. A felhasználók megtekinthetik az értesítést, és ha ez jogos, válassza az ellenőrzés lehetőséget. Ellenkező esetben a Megtagadás lehetőséget is kiválaszthatja.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállításhoz, ha csak egy módszer szükséges az alaphelyzetbe állításhoz, az ellenőrzési kód az egyetlen lehetőség a felhasználók számára a **legmagasabb szintű biztonság biztosításához**.
>
> Ha két módszer szükséges, a felhasználók visszaállíthatják az értesítési **vagy** ellenőrzési **kódokat az egyéb** engedélyezett módszerek kiegészítéseként.
>

Ha engedélyezi mindkét értesítés használatát a Mobile App és az ellenőrző kód használatával a Mobile apps szolgáltatásban, a Microsoft Authenticator alkalmazást az értesítéseket használó felhasználók értesítést és kódot is használhatnak az identitásuk ellenőrzéséhez.

> [!NOTE]
> Ha a szervezete Kínában dolgozik vagy Kínába utazik, az **Android-eszközökön** a **Mobile App metóduson keresztül küldött értesítés** nem működik az adott országban. Ezeket a felhasználókat alternatív módszereket kell elérhetővé tenni.

### <a name="verification-code-from-mobile-app"></a>Ellenőrző kód a Mobile appből

A Microsoft Authenticator alkalmazás vagy más külső féltől származó alkalmazások szoftver-tokenként használhatók eskü-ellenőrző kód létrehozásához. A Felhasználónév és a jelszó megadása után adja meg az alkalmazás által a bejelentkezési képernyőn megadott kódot. Az ellenőrzőkód egy második hitelesítési módként szolgál.

> [!WARNING]
> Az önkiszolgáló jelszó-visszaállításhoz, ha csak egy módszerre van szükség az ellenőrző kód alaphelyzetbe állításához, az egyetlen lehetőség a felhasználók számára a **legmagasabb szintű biztonság biztosításához**.
>

Előfordulhat, hogy a felhasználók legfeljebb öt olyan hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást, amelyet a használatra konfiguráltak) kombinálnak.

## <a name="oath-hardware-tokens-public-preview"></a>A hardver-tokenek ESKÜje (nyilvános előzetes verzió)

Az eskü egy nyílt szabvány, amely meghatározza, hogy az egyszeri jelszavas (OTP) kódok hogyan jönnek létre. Az Azure AD támogatja az eskü-TOTP SHA-1 tokenek használatát a 30 másodperces vagy a 60-Second fajta esetében. Ezek a jogkivonatok a választott gyártótól származnak. A titkos kulcsok legfeljebb 128 karakterből állhatnak, amelyek nem kompatibilisek az összes jogkivonattal. A titkos kulcs csak az *a-z* , a-z és *a-z* , valamint a *1-7*karakterből állhat, és a Base32-ben kell kódolni.

![ESKÜ-tokenek feltöltése az MFA-eskü tokenek paneljére](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A nyilvános előzetes verzió részeként a rendszer a hardveres jogkivonatokat is támogatja. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A jogkivonatok beszerzése után a következő példában látható módon fel kell tölteni egy vesszővel tagolt (CSV) fájlformátumot, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ügyeljen rá, hogy a fejlécsor szerepeljen a CSV-fájlban.

Miután megfelelően formázott CSV-fájlként, a rendszergazda bejelentkezhet a Azure Portalba, és megnyithatja **Azure Active Directory** > **biztonsági** > **MFA** > -**eskü tokeneket**, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a folyamat eltarthat néhány percig. Kattintson a **frissítés** gombra az aktuális állapot lekéréséhez. Ha a fájlban hibák merülnek fel, lehetősége lesz letölteni egy CSV-fájlt, amely felsorolja a feloldható hibákat. A letöltött CSV-fájl mezőinek neve eltér a feltöltött verziótól.

A hibák elhárítása után a rendszergazda ezután aktiválhatja az egyes kulcsokat, ha a jogkivonat aktiválása elemre kattint, és beírja a tokenen megjelenített egyszeri **jelszavas** azonosítót.

Előfordulhat, hogy a felhasználók legfeljebb öt olyan hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást, amelyet a használatra konfiguráltak) kombinálnak.

## <a name="phone-options"></a>Telefonos beállítások

### <a name="mobile-phone"></a>Mobiltelefon

A mobil telefonok esetében két lehetőség érhető el.

Ha a felhasználó nem szeretné, hogy a mobil telefonszáma megjelenjen a címtárban, de továbbra is szeretné használni azt a jelszó-visszaállításhoz, a rendszergazdáknak nem szabad azt a címtárban feltölteniük. A felhasználók a [jelszó-visszaállítási regisztrációs portálon](https://aka.ms/ssprsetup)tölthetik fel a **hitelesítési telefonos** attribútumot. A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem jelennek meg.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: + 1 4255551234.

> [!NOTE]
> Az országkód és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a + 1 4255551234X12345 formátumban.

A Microsoft nem garantálja a konzisztens SMS-és hangalapú Multi-Factor Authentication kérések kézbesítését ugyanazzal a számmal. A felhasználók érdekében a Microsoft bármikor hozzáadhat vagy eltávolíthat rövid kódokat, ahogyan az útvonal-kiigazításokat az SMS-kézbesítés javítása érdekében. A Microsoft nem támogatja a rövid kódokat a Egyesült Államok és Kanadában kívüli országok/régiók számára.

#### <a name="text-message"></a>Szöveges üzenet

A rendszer SMS-t továbbít az ellenőrző kódot tartalmazó mobil telefonszámra. A folytatáshoz adja meg a bejelentkezési felületen megadott ellenőrző kódot.

#### <a name="phone-call"></a>Telefonhívás

Az Ön által megadott telefonszámra automatikusan hanghívás történik. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a # gombot a telefon billentyűzetén.

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

### <a name="office-phone"></a>Munkahelyi telefon

Az Ön által megadott telefonszámra automatikusan hanghívás történik. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a # gombot.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: + 1 4255551234.

Az Office Phone-attribútumot a rendszergazda felügyeli.

> [!IMPORTANT]
> Az 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők az MFA és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívás továbbra is elérhető lesz a fizetős Azure AD-bérlők felhasználói számára. Ez a változás csak az ingyenes/próbaverziós Azure AD-bérlőket befolyásolja.

> [!NOTE]
> Az országkód és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a + 1 4255551234X12345 formátumban.

### <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

A hitelesítési módszerekkel kapcsolatos gyakori problémák telefonszám használatával:

* Blokkolt hívó azonosítója egyetlen eszközön
   * Eszköz hibáinak megoldása
* Helytelen telefonszám, helytelen országkód, otthoni telefonszám és munkahelyi telefonszám
   * Felhasználói objektum és konfigurált hitelesítési módszerek hibakeresése. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Helytelen PIN-kód van megadva
   * Erősítse meg, hogy a felhasználó használta az Azure MFA-kiszolgálón regisztrált megfelelő PIN-kódot.
* Hívás továbbítása a hangpostára
   * Győződjön meg arról, hogy a felhasználó bekapcsolta a telefont, és a szolgáltatás elérhető a saját területén, vagy használjon alternatív módszert.
* A felhasználó blokkolva van
   * A rendszergazda feloldja a felhasználó blokkolását a Azure Portalban.
* Az SMS nincs előfizetve az eszközön
   * A felhasználó módosíthatja a metódusokat, vagy aktiválhatja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók (nem észlelhetők telefonos bemenetek, hiányzó DTMF-hangok, letiltott hívóazonosító több eszközön, vagy a több eszközön letiltott SMS-ek)
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és SMS-üzenetek továbbítására a hitelesítéshez. Ha a fenti problémák bármelyikét látja, akkor a felhasználó 5 percen belül legalább 5 alkalommal megpróbálta használni a metódust, és a felhasználó információi elérhetők a Microsoft ügyfélszolgálatával való kapcsolatfelvételkor.

## <a name="app-passwords"></a>Alkalmazás jelszavai

Bizonyos böngészőn kívüli alkalmazások nem támogatják a többtényezős hitelesítést, ha a felhasználó számára engedélyezve van a többtényezős hitelesítés, és a nem böngészőbeli alkalmazások használatát kísérli meg, nem tudnak hitelesíteni. Az alkalmazás jelszava lehetővé teszi, hogy a felhasználók továbbra is hitelesítsék magukat

Ha a feltételes hozzáférési szabályzatok segítségével kényszeríti Multi-Factor Authentication, és nem a felhasználónkénti MFA-n keresztül, nem hozhat létre alkalmazás-jelszavakat. A feltételes hozzáférési házirendeket használó alkalmazások hozzáférés-vezérléséhez nem szükségesek az alkalmazás jelszavai.

Ha a szervezete az Azure AD-vel történő egyszeri bejelentkezéshez készült, és az Azure MFA-t fogja használni, vegye figyelembe a következő adatokat:

* Az alkalmazás jelszavait az Azure AD ellenőrzi, így megkerüli az összevonást. Az összevonás csak az alkalmazás jelszavának beállításakor használatos. Összevont (SSO) felhasználók esetén a rendszer a jelszavakat a szervezeti AZONOSÍTÓban tárolja. Ha a felhasználó elhagyja a vállalatot, az információnak a szervezet AZONOSÍTÓját kell használnia az rSync használatával. A fiókok letiltása/törlése akár három órát is igénybe vehet, ami késlelteti az alkalmazások jelszavainak letiltását/törlését az Azure AD-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Nem érhető el helyszíni hitelesítési naplózási/naplózási képesség az alkalmazások jelszavainak megadásához.
* Bizonyos speciális építészeti kialakításokhoz szükség lehet a szervezeti felhasználónevek és jelszavak és az alkalmazások jelszavának együttes használatára, ha kétlépéses ellenőrzést használ az ügyfelekkel a hitelesítés helyétől függően. A helyszíni infrastruktúrával hitelesítő ügyfelek esetében szervezeti felhasználónevet és jelszót kell használnia. Az Azure AD-vel hitelesítő ügyfelek esetében használja az alkalmazás jelszavát.
* Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Ha lehetővé szeretné tenni a felhasználóknak az alkalmazás jelszavának létrehozását, jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget a szolgáltatás beállításai területen lévő nem böngészőalapú alkalmazásokba való bejelentkezéshez** .

## <a name="next-steps"></a>További lépések

[Önkiszolgáló jelszó-visszaállítás engedélyezése a szervezet számára](quickstart-sspr.md)

[Azure-Multi-Factor Authentication engedélyezése a szervezet számára](howto-mfa-getstarted.md)

[Kombinált regisztráció engedélyezése a bérlőben](howto-registration-mfa-sspr-combined.md)

[A végfelhasználói hitelesítési módszer konfigurációs dokumentációja](https://aka.ms/securityinfoguide)
