---
title: Dinamikusan tiltott jelszavak – Azure Active Directory
description: Gyenge jelszavak betiltása a környezetből az Azure AD által dinamikusan tiltott jelszavakkal
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27530b143e46acad4152e8333836cbe9c79fab17
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168092"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>A szervezet helytelen jelszavainak eltávolítása

Az iparági vezetők azt mondják, hogy nem ugyanazt a jelszót használják több helyen, így összetett, és nem egyszerű, például "Password123". Hogyan garantálható a szervezetek számára, hogy a felhasználók a legjobb gyakorlati útmutatást használják? Hogyan biztosíthatják, hogy a felhasználók ne használják a gyenge jelszavakat vagy akár a gyenge jelszavakat is?

Az erősebb jelszavakkal rendelkező kezdeti lépés a felhasználók számára nyújt útmutatást. A jelen témakörben a Microsoft aktuális útmutatója a következő hivatkozásra kattintva érhető el:

[Microsoft-jelszó – útmutató](https://www.microsoft.com/research/publication/password-guidance)

Jó útmutatást biztosítunk, de még azzal is tisztában vagyunk vele, hogy sok felhasználó továbbra is a gyenge jelszavakat választja. Az Azure AD jelszavas védelme az ismert gyenge jelszavak és azok változatai észlelésével és blokkolásával védi a szervezetet, valamint opcionálisan blokkolja a szervezetére jellemző további gyenge kifejezéseket is.

További információ az aktuális biztonsági erőfeszítésekről: [Microsoft biztonsági intelligencia-jelentés](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Globális tiltott jelszavak listája

A Azure AD Identity Protection csapat folyamatosan elemzi az Azure AD biztonsági telemetria az általánosan használt gyenge vagy sérült jelszavakat keresve, vagy pontosabban a gyenge alapkifejezéseket, amelyek gyakran használatosak a gyenge jelszavak alapjául. Ha ilyen gyenge feltételek találhatók, azok hozzáadódnak a globálisan tiltott jelszavak listájához. A globálisan tiltott jelszavak listájának tartalma nem a külső adatforrásokon alapul. A globálisan tiltott jelszavak listája teljes egészében az Azure AD biztonsági telemetria és elemzésének folyamatos eredményein alapul.

Valahányszor új jelszót módosítanak vagy állítanak alaphelyzetbe bármely, az Azure AD-ben lévő bérlő bármelyik felhasználója számára, a rendszer a globálisan tiltott jelszavak aktuális verzióját használja a jelszó erősségének érvényesítése során. Ez az ellenőrzés sokkal erősebb jelszavakat eredményez az összes Azure AD-ügyfél számára.

> [!NOTE]
> A Cyber-bűnözők hasonló stratégiákat is használnak a támadások során. Ezért a Microsoft nem teszi közzé nyilvánosan a lista tartalmát.

## <a name="custom-banned-password-list"></a>Egyéni tiltott jelszavak listája

Előfordulhat, hogy egyes szervezetek még tovább szeretnék javítani a biztonságot azáltal, hogy hozzáadják a saját testreszabásokat a globálisan tiltott jelszavak listájához, amit a Microsoft meghívja az egyéni tiltott jelszavak listáját. A Microsoft azt javasolja, hogy a listához hozzáadott feltételek elsősorban a szervezeti feltételekre összpontosítanak, például a következőkre:

- Márkanevek
- Terméknév
- Helyek (például a vállalati központ)
- Vállalatra vonatkozó belső feltételek
- Adott céges jelentéssel rendelkező rövidítések.

Miután hozzáadta a feltételeket az egyéni tiltott jelszavak listájához, a jelszavak ellenőrzésekor a rendszer a globálisan tiltott jelszavak listájának feltételeit kombinálja.

> [!NOTE]
> Az egyéni tiltott jelszavak listáját a rendszer legfeljebb 1000 feltételre korlátozza. Nem kifejezetten a jelszavak rendkívül nagy listáját blokkolja. Az egyéni tiltott jelszavak listájának előnyeinek teljes kihasználásához a Microsoft azt javasolja, hogy először tekintse át és Ismerje meg a jelszó-értékelési algoritmust (lásd a [jelszavak kiértékelésének módját](concept-password-ban-bad.md#how-are-passwords-evaluated)), mielőtt új feltételeket adna hozzá az egyéni tiltott listához. Az algoritmus működésének megismerése lehetővé teszi a vállalat számára a nagy mennyiségű gyenge jelszó és a hozzájuk tartozó változatok hatékony észlelését és blokkolását.

Példa: Vegyünk például egy "contoso" nevű ügyfelet, amely Londonban található, és ez egy "widget" nevű terméket tesz elérhetővé. Az ilyen ügyfelek esetében a feleslegesség, valamint a kevésbé biztonságos, hogy megpróbálja letiltani a jelen feltételek adott változatát, például:

- "Contoso! 1"
- „Contoso@London”
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ... satöbbi

Ehelyett sokkal hatékonyabb és biztonságos, hogy csak a legfontosabb alapfogalmakat blokkolja:

- Contoso
- London
- Widget

A jelszó-ellenőrzési algoritmus ezután automatikusan letiltja a fenti gyenge változatokat és kombinációkat.

Az egyéni tiltott jelszavak listája és a helyszíni Active Directory integrációjának engedélyezése a Azure Portal használatával történik.

![Az egyéni tiltott jelszavak listájának módosítása a hitelesítési módszerek területen](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Jelszavas spray-támadások és a harmadik féltől származó feltört jelszavak listája

Az Azure AD jelszavas védelem egyik legfontosabb előnye, hogy segítsen megvédeni a jelszó-szórásos támadásokat. A legtöbb jelszavas szórásos támadás nem kísérli meg, hogy az adott egyedi fiók többször is megtámadjon, mert az ilyen viselkedés nagy mértékben növeli az észlelés valószínűségét, akár fiókzárolási, akár más módon. A jelszó-szórásos támadások többsége ezért arra támaszkodik, hogy csak kis mennyiségű ismert leggyengébb jelszót küld el egy vállalat minden fiókján. Ez a módszer lehetővé teszi, hogy a támadó gyorsan keressen egy könnyen feltört fiókot, és ezzel egyidejűleg elkerülje a lehetséges észlelési küszöbértékeket.

Az Azure AD jelszavas védelme úgy lett kialakítva, hogy hatékonyan letiltsa az összes olyan ismert gyenge jelszót, amely a jelszó-permetezési támadásokban valószínűleg használatban van az Azure AD által látott valós biztonsági telemetria alapján.  A Microsoft tudomásul veszi, hogy a harmadik féltől származó webhelyek több millió jelszót felsorolnak, amelyeket a korábbi nyilvánosan ismert biztonsági rések során feltörtek. Gyakori, hogy a harmadik féltől származó jelszó-ellenőrzési termékek a találgatásos összehasonlításon alapulnak a több millió jelszóval. A Microsoft úgy véli, hogy az ilyen technikák nem a legjobb módszer, ha a jelszó-megtámadók által használt tipikus stratégiák alapján javítja az általános jelszó erősségét.

> [!NOTE]
> A Microsoft globálisan tiltott jelszavas listája nem alapul semmilyen harmadik féltől származó adatforráson, beleértve a feltört jelszavak listáját is.

Bár a Microsoft globálisan tiltott listája kicsi a harmadik féltől származó tömeges listákkal összehasonlítva, a biztonsági hatásait az a tény fokozza, hogy a valós biztonsági telemetria származik a jelszó-szórásos támadásokkal szemben, valamint az a tény, hogy a Microsoft a jelszó-ellenőrzési algoritmus intelligens, fuzzy-egyeztetési technikákat használ. Ennek végeredménye az, hogy hatékonyan fogja felderíteni és blokkolni a leggyakoribb gyenge jelszavakat a vállalatnál. Azok az ügyfelek, akik úgy döntenek, hogy az egyéni tiltott jelszavak listájára felveszik a szervezetre vonatkozó feltételeket, ugyanezt az algoritmust is élvezik.

A jelszó-alapú biztonsági problémákra vonatkozó további információkat [a PA $ $wordban nem számítunk](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)fel.

## <a name="on-premises-hybrid-scenarios"></a>Helyszíni hibrid forgatókönyvek

A csak felhőalapú fiókok védelme hasznos, de számos szervezet olyan hibrid forgatókönyveket tart fenn, mint a helyszíni Windows Server Active Directory. Az Azure AD jelszavas védelem biztonsági előnyei a helyszíni ügynökök telepítésével is kiterjeszthetők a Windows Server Active Directory környezetbe. Most a felhasználók és rendszergazdák, akik a Active Directory jelszavakat módosítanak vagy állítanak alaphelyzetbe, meg kell felelniük a csak felhőalapú felhasználókkal megegyező jelszóházirend-házirendnek.

## <a name="how-are-passwords-evaluated"></a>A jelszavak kiértékelésének módja

Amikor egy felhasználó megváltoztatja vagy visszaállítja a jelszavát, az új jelszót a rendszer a globális és az egyéni tiltott jelszavak listájából (ha az utóbbi konfigurálva) a feltételek összevont listáján érvényesíti.

Ha a felhasználó jelszava tiltott jelszót tartalmaz, akkor a jelszó továbbra is elfogadható, ha a teljes jelszó elég erős, ellenkező esetben. Az újonnan konfigurált jelszavak az alábbi lépések végrehajtásával vizsgálják meg, hogy a rendszer elfogadja vagy elutasítja-e a teljes erősségét.

### <a name="step-1-normalization"></a>1\. lépés: normalizálás

Az új jelszó először a normalizálás folyamatán halad át. Ez a módszer lehetővé teszi, hogy a tiltott jelszavak kisebb halmaza legyen leképezve a potenciálisan gyenge jelszavak sokkal nagyobb készletére.

A normalizálás két részből áll.  Első lépésként az összes nagybetűt kisbetűsre változtatja.  Másodszor, a közös karakteres helyettesítések végrehajtása történik, például:  

| Eredeti levél  | Helyettesített betű |
| --- | --- |
| '0'  | o |
| '1'  | l |
| '$'  | képgalériája |
| '\@'  | egy |

Példa: tegyük fel, hogy az "üres" jelszó be van tiltva, és a felhasználó megpróbálja megváltoztatni a jelszavát a "Bl@nK" értékre. Bár a "Bl@nk" nincs kifejezetten kitiltva, a normalizálás folyamata ezt a jelszót "üres" értékre konvertálja, amely egy tiltott jelszó.

### <a name="step-2-check-if-password-is-considered-banned"></a>2\. lépés: Ellenőrizze, hogy a jelszó betiltottnak minősül-e

#### <a name="fuzzy-matching-behavior"></a>Homályos egyezési viselkedés

A rendszer a normalizált jelszóval azonosítja a fuzzy megfeleltetést, hogy az tartalmazza-e a globális vagy az egyéni tiltott jelszavak listáján található jelszót. A megfeleltetési folyamat egy (1) összehasonlítási távolságon alapul.  

Példa: tegyük fel, hogy a "ABCDEF" jelszó tiltott, és a felhasználó megpróbálja módosítani a jelszavát a következők egyikére:

"abcdeg" *(az utolsó karakter módosult "f" értékről "g"-re)* "abcdefg" *(g* ) "ABCDE" (a záró *"f" a végéről törölve* )

A fenti jelszavak mindegyike nem felel meg kifejezetten a betiltott "ABCDEF" jelszónak. Mivel azonban mindegyik példa a "ABCDEF" betiltott kifejezés 1. részén található, a "ABCDEF" értéknek megfelelőnek számít.

#### <a name="substring-matching-on-specific-terms"></a>Alkarakterlánc megfeleltetése (adott kifejezéseken)

A rendszer a normalizált jelszóval egyező alsztringet használ a felhasználó utónevét és vezetéknevét, valamint a bérlő nevét (vegye figyelembe, hogy a bérlői név egyeztetése nem történik meg a jelszavaknak egy Active Directory tartományvezérlőn való ellenőrzésekor).

Példa: tegyük fel, hogy van egy felhasználónk, a pol, aki szeretné visszaállítani a jelszavát a "P0l123fb" értékre. A normalizálás után ez a jelszó "pol123fb" lesz. A karakterláncok egyeztetése azt észleli, hogy a jelszó tartalmazza a felhasználó utónevét ("pol"). Annak ellenére, hogy a "P0l123fb" nem volt konkrétan a tiltott jelszavak listáján, a jelszóban a "pol" karakterlánc-egyezés szerepel. Ezért ezt a jelszót a rendszer elutasítja.

#### <a name="score-calculation"></a>Pontszám kiszámítása

A következő lépés a betiltott jelszavak összes példányának azonosítása a felhasználó normalizált új jelszavában. Ezután:

1. A felhasználó jelszavában található összes betiltott jelszó egy pontot kap.
2. Minden fennmaradó egyedi karakter egy pontot kap.
3. A jelszónak legalább öt (5) pontnak kell lennie ahhoz, hogy el lehessen fogadni.

A következő két példa esetében tegyük fel, hogy a contoso az Azure AD jelszavas védelmet használja, és a "contoso" az egyéni listán. Azt is feltételezzük, hogy az "üres" a globális listán található.

Példa: a felhasználó megváltoztatja a jelszavát a "C0ntos0Blank12" értékre.

A normalizálás után a jelszó "contosoblank12" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: contoso és üres. Ezt a jelszót a rendszer a következő pontszámmal adja meg:

[contoso] + [blank] + [1] + [2] = 4 pont, mivel a jelszó öt (5) pont alatt van, a rendszer elutasítja.

Példa: a felhasználó módosítja a jelszavát a "ContoS0Bl@nkf9!" értékre.

A normalizálás után ez a jelszó "contosoblankf9!" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: contoso és üres. Ezt a jelszót a rendszer a következő pontszámmal adja meg:

[contoso] + [blank] + [f] + [9] + [!] = 5 pont, mivel ez a jelszó legalább öt (5) pont, elfogadva.

   > [!IMPORTANT]
   > Vegye figyelembe, hogy a betiltott jelszó-algoritmus és a globális lista a folyamatos biztonsági elemzés és kutatás alapján bármikor megváltoztatható az Azure-ban. A helyszíni tartományvezérlő ügynök szolgáltatás esetében a frissített algoritmusok csak a DC-ügynök szoftverének újratelepítése után lépnek érvénybe.

## <a name="license-requirements"></a>Licenckövetelmények

|   | Azure AD jelszavas védelem a globálisan tiltott jelszavak listájával | Azure AD jelszavas védelem egyéni tiltott jelszavak listájával|
| --- | --- | --- |
| Csak felhőalapú felhasználók | Azure AD Free | prémium szintű Azure AD P1 vagy P2 |
| Helyi Windows Server-Active Directoryról szinkronizált felhasználók | prémium szintű Azure AD P1 vagy P2 | prémium szintű Azure AD P1 vagy P2 |

> [!NOTE]
> A helyszíni Windows Server Active Directory felhasználók, akik nincsenek szinkronizálva Azure Active Directory az Azure AD jelszavas védelem előnyeit is kihasználják a szinkronizált felhasználók meglévő licencelése alapján.

További licencelési információk, beleértve a költségeket is, a [Azure Active Directory díjszabási oldalon](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál visszaállítani egy jelszót a betiltott valamire, a következő hibaüzenet jelenik meg:

Sajnos a jelszó olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálhatja a jelszavát. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>Következő lépések

- [Egyéni tiltott jelszavak listájának konfigurálása](howto-password-ban-bad.md)
- [Azure AD-beli jelszavas védelmi ügynökök engedélyezése a helyszínen](howto-password-ban-bad-on-premises-deploy.md)
