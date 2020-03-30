---
title: Dinamikusan tiltott jelszavak – Azure Active Directory
description: Az Azure AD dinamikusan tiltott jelszavaival kitilthatja a gyenge jelszavakat a környezetéből
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263997"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>A rossz jelszavak kiküszöbölése a szervezetben

Az iparági vezetők azt mondják, hogy ne használja ugyanazt a jelszót több helyen, hogy bonyolultlegyen, és ne legyen egyszerű, mint a "Password123". Hogyan garantálhatják a szervezetek, hogy a felhasználók követik az ajánlott eljárásokra vonatkozó útmutatást? Hogyan győződhetnek meg arról, hogy a felhasználók nem használnak gyenge jelszavakat, vagy akár a gyenge jelszavak at?

Az erősebb jelszavak kezdeti lépése, hogy útmutatást nyújtson a felhasználóknak. A Microsoft jelenlegi útmutatója ebben a témában az alábbi linken található:

[Útmutató a Microsoft jelszóhoz](https://www.microsoft.com/research/publication/password-guidance)

Miután jó útmutatást fontos, de még azzal is tudjuk, hogy sok felhasználó továbbra is a végén választotta gyenge jelszavakat. Az Azure AD password protection védi a szervezetet az ismert gyenge jelszavak és azok variánsainak észlelésével és blokkolásával, valamint opcionálisan a szervezetre jellemző további gyenge kifejezések blokkolásával.

Az aktuális biztonsági erőfeszítésekről a [Microsoft security intelligence jelentésben](https://www.microsoft.com/security/operations/security-intelligence-report)talál további információt.

## <a name="global-banned-password-list"></a>Globális tiltott jelszólista

Az Azure AD Identity Protection csapata folyamatosan elemzi az Azure AD biztonsági telemetriai adatait, amelyek a gyakran használt gyenge vagy feltört jelszavakat keresik, pontosabban a gyenge alapkifejezéseket, amelyek gyakran a gyenge jelszavak alapjául szolgálnak. Ha ilyen gyenge kifejezéseket talál, azok felkerülnek a globális tiltott jelszavak listájára. A globális tiltott jelszavak listájának tartalma nem külső adatforráson alapul. A globális tiltott jelszó lista teljes mértékben az Azure AD biztonsági telemetriai adatok és elemzés folyamatos eredményein alapul.

Amikor egy új jelszót módosítanak vagy alaphelyzetbe állítanak bármely bérlő számára az Azure AD-ben, a globális tiltott jelszólista aktuális verziója lesz a kulcsbemenet a jelszó erősségének érvényesítésekor. Ez az ellenőrzés sokkal erősebb jelszavakat eredményez az összes Azure AD-ügyfél számára.

> [!NOTE]
> A kiberbűnözők hasonló stratégiákat is alkalmaznak a támadásaiksorán. Ezért a Microsoft nem teszi közzé a lista tartalmát nyilvánosan.

## <a name="custom-banned-password-list"></a>Egyéni tiltott jelszavak listája

Egyes szervezetek további biztonságnövelést szeretnének, ha saját testreszabásaikat is felvennék a globális tiltott jelszólista tetejére, amit a Microsoft az egyéni tiltott jelszavak listájának nevez. A Microsoft azt javasolja, hogy a listához hozzáadott kifejezések elsősorban a szervezeti-specifikus kifejezésekre összpontosítsanak, mint például:

- Márkanevek
- Terméknevek
- Helyek (például a vállalat székhelye)
- Vállalatspecifikus belső kifejezések
- Konkrét vállalati jelentéssel bíró rövidítések.

Miután a kifejezéseket hozzáadta az egyéni tiltott jelszavak listájához, a jelszavak érvényesítésekén a globális tiltott jelszavak listájában szereplő kifejezésekkel kombinálódik.

> [!NOTE]
> Az egyéni tiltott jelszavak listája legfeljebb 1000 kifejezésre korlátozódik. Nem alkalmas a rendkívül nagy jelszavak blokkolására. Annak érdekében, hogy teljes mértékben kihasználja az egyéni tiltott jelszavak listájának előnyeit, a Microsoft azt javasolja, hogy először tekintse át és ismerje meg a jelszóértékelési algoritmust (lásd: [Hogyan értékelik ki](concept-password-ban-bad.md#how-are-passwords-evaluated)a jelszavakat ), mielőtt új kifejezéseket ad hozzá az egyéni tiltott listához. Az algoritmus működésének megértése lehetővé teszi a vállalat számára, hogy hatékonyan észlelje és blokkolja a gyenge jelszavak és azok változatai nagy számát.

Például: fontolja meg egy "Contoso" nevű ügyfelet, amely Londonban található, és amely egy "Widget" nevű terméket készít. Egy ilyen ügyfél számára pazarló és kevésbé biztonságos lenne a következő kifejezések bizonyos változatainak blokkolása:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Satöbbi

Ehelyett sokkal hatékonyabb és biztonságosabb, hogy blokkolja csak a legfontosabb alapfeltételek:

- "Contoso"
- "London"
- "Widget"

A jelszó érvényesítési algoritmus ezután automatikusan blokkolja a gyenge változatokat és a fentiek kombinációit.

Az egyéni tiltott jelszó lista és a helyszíni Active Directory-integráció engedélyezésének lehetővé tétele az Azure Portalon keresztül történik.

![Az egyéni tiltott jelszavak listájának módosítása a hitelesítési módszerek csoportban](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Jelszószórásos támadások és harmadik fél által feltört jelszólisták

Az Egyik legfontosabb Azure AD jelszavas védelem előnye, hogy segít megvédeni a jelszó spray támadások ellen. A legtöbb jelszóspray-támadás nem próbál meg több mint néhány alkalommal megtámadni egy adott fiókot, mivel az ilyen viselkedés nagymértékben növeli az észlelés valószínűségét, akár fiókzárolással, akár más módon. A jelszó-szórótámadások többsége ezért az ismert leggyengébb jelszavaknak csak kis számú, a vállalat egyes fiókjaihoz való benyújtásán alapul. Ez a módszer lehetővé teszi a támadó számára, hogy gyorsan keressen egy könnyen feltört fiókot, miközben elkerüli a potenciális észlelési küszöbértékeket.

Az Azure AD jelszavas védelmet úgy tervezték, hogy hatékonyan blokkolja az összes ismert gyenge jelszavakat, amelyek valószínűleg jelszóspray-támadások, az Azure AD által látott valós biztonsági telemetriai adatok alapján.  A Microsoft nak tudomása van olyan külső webhelyekről, amelyek több millió olyan jelszót számlálnak, amelyeket a korábbi nyilvánosan ismert biztonsági rések során feltörtek. Gyakori, hogy a harmadik féltől származó jelszóérvényesítési termékek alapja a találgatásos összehasonlítás a több millió jelszóhoz képest. A Microsoft úgy véli, hogy az ilyen technikák nem a legjobb módja annak, hogy javítsa az általános jelszó erőssége, mivel a tipikus stratégiák által használt jelszó spray támadók.

> [!NOTE]
> A Microsoft globálistiltott jelszólistája egyáltalán nem alapul külső adatforrásokon, beleértve a feltört jelszólistákat is.

Bár a Microsoft globális tiltott lista kicsi képest néhány harmadik fél tömeges listák, a biztonsági hatások erősítik az a tény, hogy származik a valós biztonsági telemetriai tényleges jelszó spray támadások, valamint az a tény, hogy a Microsoft a jelszóérvényesítési algoritmus intelligens fuzzy-matching technikákat használ. A végeredmény az, hogy hatékonyan észleli és blokkolja a leggyakoribb gyenge jelszavak millióit a vállalatnál. Azok az ügyfelek, akik úgy döntenek, hogy szervezetspecifikus kifejezéseket adnak az egyéni tiltott jelszavak listájához, ugyanannak az algoritmusnak a előnyeit is élvezhetik.

A jelszóalapú biztonsági problémákkal kapcsolatos további információkat a [Pa$$word nem számít.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

## <a name="on-premises-hybrid-scenarios"></a>Helyszíni hibrid forgatókönyvek

A csak felhőalapú fiókok védelme hasznos, de számos szervezet hibrid forgatókönyveket tart fenn, beleértve a helyszíni Windows Server Active Directoryt is. Az Azure AD jelszavas védelmének biztonsági előnyei a helyszíni ügynökök telepítésével a Windows Server Active Directory-környezetre is kiterjeszthetők. Mostantól azoknak a felhasználóknak és rendszergazdáknak, akik módosítják vagy alaphelyzetbe állítják a jelszavakat az Active Directoryban, meg kell felelniük a csak felhőalapú felhasználókkal azonos jelszóházirendnek.

## <a name="how-are-passwords-evaluated"></a>A jelszavak kiértékelése

Amikor egy felhasználó módosítja vagy alaphelyzetbe állítja a jelszavát, az új jelszó erőssége és összetettsége szempontjából ellenőrizve azt a globális és egyéni tiltott jelszólistákban szereplő kifejezések kombinált listájával szemben (ha az utóbbi konfigurálva van).

Még ha a felhasználó jelszava tiltott jelszót is tartalmaz, a jelszó akkor is elfogadható, ha az általános jelszó egyébként elég erős. Az újonnan konfigurált jelszó a következő lépéseken megy keresztül annak általános erősségének felméréséhez, hogy el kell-e fogadni vagy el kell-e utasítani.

### <a name="step-1-normalization"></a>1. lépés: Normalizálás

Az új jelszó először egy normalizálási folyamaton megy keresztül. Ez a technika lehetővé teszi, hogy a tiltott jelszavak egy kis készletét leképezve legyen egy sokkal nagyobb, potenciálisan gyenge jelszavakra.

A normalizáció nak két része van.  Először is, az összes nagybetű kisbetűsre változik.  Másodszor, gyakori karakterhelyettesítéseket végeznek, például:  

| Eredeti levél  | Helyettesített levél |
| --- | --- |
| '0'  | "o" |
| '1'  | "l" |
| '$'  | "s" |
| '\@'  | "a" |

Példa: tegyük fel, hogy az "üres" jelszó ki vanBl@nKtiltva, és a felhasználó megpróbálja megváltoztatni a jelszavát " ""-ra. Annak ellenére, hogy a "Bl@nk" nincs kifejezetten tiltva, a normalizálási folyamat ezt a jelszót "üres"-re konvertálja, ami egy tiltott jelszó.

### <a name="step-2-check-if-password-is-considered-banned"></a>2. lépés: Ellenőrizze, hogy a jelszó tiltottnak minősül-e

#### <a name="fuzzy-matching-behavior"></a>Fuzzy egyezési viselkedés

Az intelligens megfeleltetés a normalizált jelszóban használatos annak azonosítására, hogy tartalmaz-e jelszót a globális vagy az egyéni tiltott jelszólistákon. Az egyeztetési folyamat egy (1) összehasonlítás szerkesztési távolságán alapul.  

Példa: tegyük fel, hogy az "abcdef" jelszó ki van tiltva, és a felhasználó megpróbálja megváltoztatni a jelszavát a következők egyikére:

"abcdeg" *(az utolsó karakter 'f'-ről 'g'-re változott)* 'abcdefg' *'(g' végezetül hozzáfűzve)* "abcde" *(a záró "f" karaktert törölték a végéről)*

A fenti jelszavak mindegyike nem kifejezetten egyezik a tiltott "abcdef" jelszóval. Mivel azonban minden példa az "abcdef" tiltott kifejezés közül 1-es szerkesztési távolságon belül van, mindegyik az "abcdef" kifejezéssel egyezik.

#### <a name="substring-matching-on-specific-terms"></a>Karakterlánc-részegyezés (meghatározott feltételek kel)

A részkarakterlánc-egyeztetés a normalizált jelszóban a felhasználó vezeték- és keresztnevének, valamint a bérlő nevének ellenőrzéséhez használatos (vegye figyelembe, hogy a bérlői név egyeztetése nem történik meg, ha az Active Directory tartományvezérlőn érvényesíti a jelszavakat).

Példa: tegyük fel, hogy van egy felhasználónk, Pol, aki vissza akarja állítani a jelszavát a "P0l123fb"-re. Normalizálás után ez a jelszó lesz "pol123fb". A karakterláncrész-egyeztetés megállapítja, hogy a jelszó a felhasználó "Pol" utónevét tartalmazza. Annak ellenére, hogy a "P0l123fb" nem volt kifejezetten sem tiltott jelszó listán, a "Pol" karakterlánc-illesztés a jelszóban található. Ezért ezt a jelszót a rendszer elutasítja.

#### <a name="score-calculation"></a>Pontszám számítása

A következő lépés a tiltott jelszavak összes példányának azonosítása a felhasználó normalizált új jelszavában. Ezután:

1. Minden a felhasználó jelszavában található tiltott jelszó egy pontot kap.
2. Minden fennmaradó egyedi karakter kap egy pontot.
3. A jelszónak legalább öt (5) pontnak kell lennie ahhoz, hogy elfogadják.

A következő két példa, tegyük fel, hogy a Contoso az Azure AD password protection, és a "contoso" az egyéni listán. Tételezzük fel azt is, hogy az "üres" szerepel a globális listán.

Példa: a felhasználó "C0ntos0Blank12" jelszóra módosítja a jelszavát

A normalizálás után ez a jelszó "contosoblank12" lesz. Az egyeztetési folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: contoso és üres. Ez a jelszó ezután kap egy pontszámot:

[contoso] + [blank] + [1] + [2] = 4 pont Mivel ez a jelszó öt (5) pont alatt van, a rendszer elutasítja.

Példa: a felhasználó aContoS0Bl@nkf9jelszavát " "!"-ra módosítja.

A normalizálás után ez a jelszó "contosoblankf9" lesz. Az egyeztetési folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: contoso és üres. Ez a jelszó ezután kap egy pontszámot:

[contoso] + [blank] + [f] + [9] + [!] = 5 pont Mivel ez a jelszó legalább öt (5) pont, akkor elfogadásra kerül.

   > [!IMPORTANT]
   > Kérjük, vegye figyelembe, hogy a tiltott jelszó algoritmus a globális listával együtt bármikor módosíthatja és módosíthatja az Azure-ban a folyamatos biztonsági elemzés és kutatás alapján. A helyszíni tartományvezérlő-ügynök szolgáltatás esetében a frissített algoritmusok csak a tartományvezérlő-ügynök szoftverének újbóli telepítése után lépnek életbe.

## <a name="license-requirements"></a>Licenckövetelmények

|   | Azure AD jelszavas védelem globális tiltott jelszólistával | Azure AD jelszavas védelem egyéni tiltott jelszólistával|
| --- | --- | --- |
| Csak felhőalapú felhasználók | Azure AD Free | Azure AD Premium P1 vagy P2 |
| Helyszíni Windows Server Active Directoryból szinkronizált felhasználók | Azure AD Premium P1 vagy P2 | Azure AD Premium P1 vagy P2 |

> [!NOTE]
> Az Azure Active Directoryval nem szinkronizált helyszíni Windows Server Active Directory-felhasználók is részesülnek az Azure AD jelszavas védelméből a szinkronizált felhasználók meglévő licencelése alapján.

További licencelési információk, beleértve a költségeket, megtalálható az [Azure Active Directory díjszabási helyen.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbálja visszaállítani a jelszót valamire, ami ki lenne tiltva, a következő hibaüzenet jelenik meg:

Sajnos a jelszó olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálja a jelszót. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

- [Az egyéni tiltott jelszavak listájának konfigurálása](howto-password-ban-bad.md)
- [Az Azure AD jelszavas védelmi ügynökök engedélyezése a helyszínen](howto-password-ban-bad-on-premises-deploy.md)
