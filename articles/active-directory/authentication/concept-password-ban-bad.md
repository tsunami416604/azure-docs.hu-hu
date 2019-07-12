---
title: Dinamikusan letiltott jelszavak – Azure Active Directory
description: A gyenge jelszavakat a környezetből az Azure ad-ben dinamikusan letiltott jelszavak letiltása
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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703063"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Rossz jelszavak, a szervezet számára

Iparági vezetők mondja el, hogy nem használja ugyanazt a jelszót több helyen, győződjön meg arról, hogy bonyolult, és nem teszi azt például a "/"Password123 egyszerű. Hogyan biztosítható a szervezetek számára, hogy a felhasználók ajánlott eljárások útmutatást követi? Hogyan is, győződjön meg arról, hogy a felhasználók nem használ a gyenge jelszavakat, vagy akár változatok a gyenge jelszavakat?

A kezdeti lépése erősebb a jelszavakat, hogy útmutatást nyújtanak a felhasználók számára. A Microsoft jelenlegi útmutatást ebben a témakörben tekinthet meg a következő hivatkozásra:

[Tájékoztató a jelszavakról Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Jó útmutatást, akkor fontos, de még akkor is, az, hogy tudjuk, hogy hány felhasználó fog továbbra is megtörténhet kiválasztása a gyenge jelszavakat. Az Azure AD jelszóvédelem észleli és blokkolja-e ismert gyenge jelszavakat és azok variantní hodnoty, valamint igény szerint blokkolja a további gyenge feltételek, amelyek a szervezet specifikus a szervezet védi.

Az aktuális biztonsági erőfeszítések kapcsolatos további információkért tekintse meg a [a Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Globális letiltott jelszavak listája

Az Azure AD Identity Protection csapata folyamatosan elemzi az Azure AD biztonsági telemetriai adatok keresése a gyakran használt gyenge vagy sérült biztonságú jelszavakat, illetve pontosabban, a weak kiinduló gyakran használt kifejezések alapjaként a gyenge jelszavakat. Ha található ilyen gyenge feltételek, kerülnek, a globális letiltott jelszavak listájához. A globális letiltott jelszavak listáját nem alapuló bármilyen külső adatforrást. A globális letiltott jelszavak lista teljes egészében az Azure AD biztonsági telemetriai és analitikai a folyamatos eredményeket alapul.

Amikor egy új jelszó módosítva vagy bármely felhasználó minden olyan bérlő számára az Azure ad-ben alaphelyzetbe állítása, a globális letiltott jelszavak lista aktuális verziója lesz a kulcsot adjon meg a jelszó erőssége érvényesítése során. Ellenőrzés erősebb jelszót az Azure ad-ben minden ügyfelünk esetében eredményez.

> [!NOTE]
> Bűnözők is hasonló stratégiákat használnak a saját támadások. Ezért a Microsoft nem tehető közzé a lista tartalma nyilvánosan.

## <a name="custom-banned-password-list"></a>Egyéni le van tiltva a jelszó-lista

Egyes szervezetek biztonsági még tovább növelheti a saját testreszabások felül a globális letiltott jelszavak lista hozzáadása a Microsoft által a letiltott jelszavak egyéni lista érdemes. A Microsoft azt javasolja, hogy a listához adott feltételek elsősorban összpontosítanak szervezetspecifikus feltételek például:

- Márkanevek
- A termék nevét
- Helyeket (például például vállalati központ)
- A vállalatra jellemző belső feltételek
- Az rövidítéseket, amelyek adott vállalatot, ami azt jelenti.

Miután használati a letiltott jelszavak egyéni lista van hozzáadva, azok hozzáadódik a globális letiltott jelszavak lista jelszavak érvényesítése során.

> [!NOTE]
> A letiltott jelszavak egyéni lista korlátozva a legfeljebb 1000 feltételeket. Nem célja a jelszavak rendkívül nagy méretű listák blokkolásához. Annak érdekében, hogy teljes mértékben kihasználják a letiltott jelszavak egyéni lista előnyeit, a Microsoft javasolja, hogy először tekintse át és megismerheti a jelszó kiértékelése algoritmus (lásd: [hogyan jelszavak értékeli ki a](concept-password-ban-bad.md#how-are-passwords-evaluated)) az új feltételek hozzáadása előtt a egyéni kitiltott listája. Annak ismertetése, hogyan a algoritmus működését engedélyezi a vállalatnál az hatékonyan észleli és blokkolja a nagy mennyiségű gyenge jelszavakat és azok variantní hodnoty.

Például: fontolja meg a "Contoso", londoni alapján, és amely lehetővé teszi, hogy a termék "Widget" nevű nevű ügyfélhez. Egy ügyfél lenne pazarló, valamint a kevésbé biztonságos próbál meg ezeket a feltételeket az adott változata létezik például letiltása:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

Ehelyett érdemes sokkal hatékonyabb és biztonságos csak a fő alapvető használati blokkolása:

- "Contoso"
- "London"
- "Widget"

A jelszó adatérvényesítési algoritmust majd blokkolja automatikusan a gyenge variantní hodnoty és levelezésre.

Az egyéni le van tiltva, jelszó listáját és az ezekben a helyszíni Active Directory-integráció kezeli az Azure portal használatával.

![A hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Jelszó megfelelő támadások és biztonsági kockázatot jelentő jelszó külső listák

Egy kulcsot az Azure AD jelszó védelmi előny, hogy segítséget nyújt a jelszó megfelelő támadásokkal szemben. A legtöbb jelszó megfelelő támadások ne kísérelje meg a támadásokkal szemben bármely megadott egyéni fiók több mint néhány alkalommal óta viselkedést jelentősen növeli az elágazás az észlelési, vagy a fiók zárolása vagy más módon. A jelszó megfelelő támadások többsége ezért támaszkodnak csak kis számú ellen a vállalaton belül a fiókok mindegyike ismert leggyengébb jelszavak küldése. Ez a módszer lehetővé teszi, hogy a támadó ezzel elkerülve lehetséges észlelési küszöbérték egy időben egyszerűen feltört fiók gyors keresése.

Az Azure AD jelszóvédelem célja hatékonyan letiltása az összes ismert gyenge jelszavakat, amelyek valószínűleg a jelszó megfelelő támadások, való életből vett biztonsági telemetriai adatokat az Azure AD-szerinti alapján használja.  A Microsoft külső webhelyeket, amelyeket a számba venni, hogy sérült a biztonsága az előző nyilvánosan ismert biztonsági rések több millió tudatában. Szokás külső jelszó érvényesítése termékek jelszavak millió találgatásos összehasonlításához kell alapulnia. A Microsoft úgy érzi, hogy ilyen technikák nem állnak-e a legjobb módszer a jelszó megfelelő támadók által használt jellemző stratégiák megadott általános jelszó erőssége javítása érdekében.

> [!NOTE]
> A Microsoft globális letiltott jelszavak lista alapján nézve a külső adatok adatforrásokat, beleértve a biztonsági kockázatot jelentő jelszó listák.

A Microsoft globális kitiltott listában nem kisebb néhány külső tömeges listák támogatáshoz képest korlátozottabb, biztonsági hatásaival bővíteni vannak-e a tény, hogy a tényleges jelszót megfelelő támadások, valamint azt a tényt a való életből vett biztonsági telemetriai származtatható, amely a Microsoft az intelligens intelligens egyező módszereket használ a jelszó-ellenőrzési algoritmus. A végeredmény, hogy azt fogja hatékonyan észleli és blokkolja a leggyakoribb gyenge jelszavakat használja a vállalati el több millió. Ajánljuk, akik szervezetekre vonatkozó feltételek hozzáadása az egyéni letiltott jelszavak listához is élvezhetik ugyanazt az algoritmust.

## <a name="on-premises-hybrid-scenarios"></a>A helyszíni hibrid forgatókönyvek

Kizárólag felhőalapú fiókok védelme akkor lehet hasznos, de számos szervezet hibrid környezetekben, beleértve a helyszíni Windows Server Active Directory karbantartása. Az Azure AD jelszóvédelem a Windows Server Active Directory ügynökök a helyszíni a letiltott jelszavak listáit, a meglévő infrastruktúra kiterjesztésére telepítése lehetőség. Most, hogy a felhasználók és rendszergazdák, akik módosítja, állítsa be, vagy jelszavakat a helyszíni szükségesek a csak felhőalapú felhasználói is azonos jelszóházirend ahhoz, hogy.

## <a name="how-are-passwords-evaluated"></a>Hogyan értékeli a jelszavak

Amikor a felhasználó módosítja, vagy a jelszó alaphelyzetbe állítása, az új jelszót a rendszer ellenőrzi erősségét és a bonyolultságot érvényesítésével azonosítsa a globális és a letiltott jelszavak egyéni lista alapján (Ha ez utóbbi van konfigurálva).

Akkor is, ha egy felhasználó jelszavát egy letiltott jelszavak tartalmaz, a jelszó előfordulhat, hogy továbbra is fogadja, ha a teljes jelszó nem elég erős más módon. Egy újonnan konfigurált jelszóval a következő lépéseket kell meghatározni, ha azt kell elfogad vagy elutasít általános erősségét felmérése fog áthaladni.

### <a name="step-1-normalization"></a>1\. lépés: Normalizálási

Új jelszó először halad végig egy normalizálási folyamat lépéseit követve. Ez a módszer egy kis készletét letiltott jelszavak le kell képezni egy potenciálisan gyenge jelszavakat sokkal nagyobb készlete tesz lehetővé.

Normalizálási két részből áll.  Először is, a nagybetűs betűket kisbetűsre módosult.  Második, közös karaktert helyettesítő például a következőket kell végrehajtani:  

| Eredeti betűjele  | A helyettesített betűjele |
| --- | --- |
| '0'  | "o' |
| '1'  | "l" |
| '$'  | a " |
| '\@'  | "a" |

Példa: Tegyük fel, hogy a jelszó "üres" le van tiltva, és a egy felhasználó próbál módosítani a jelszavát, a "Bl@nK". Annak ellenére, hogy "Bl@nk" van normalizálási folyamat alakítja át nem kifejezetten le van tiltva, ezt a jelszót "blank", amely egy letiltott jelszavak.

### <a name="step-2-check-if-password-is-considered-banned"></a>2\. lépés: Annak ellenőrzése, hogy a jelszó számít le van tiltva

#### <a name="fuzzy-matching-behavior"></a>Az intelligens egyező viselkedés

Intelligens megfelelő normalizált jelszavát a azonosítására szolgál, ha az egyéni le van tiltva a jelszavak listáit vagy található vagy a globális jelszót tartalmaz. A megfelelő folyamat egy szerkesztési távolság egy (1) összehasonlítás alapján történik.  

Példa: Tegyük fel, hogy a jelszó "abcdef" le van tiltva, és a egy felhasználó megpróbál módosítania kell a jelszavát, az alábbi lehetőségek közül:

"abcdeg"    *(legutóbbi változása: "f" karakter "g")* "abcdefg"   *"(g" hozzáfűzött célból)* "abcde"     *(a záró "f" end végpontjától törölve)*

A fenti jelszavak mindegyike külön nem egyezik a letiltott jelszavak "abcdef". Azonban mivel valamennyi példa 1 "abcdef" tiltott időszak egy szerkesztési távolságon belül, azok minden tekinthetők, "abcdef" egyezést.

#### <a name="substring-matching-on-specific-terms"></a>(Az adott feltételek) egyező karakterláncrészletet

Karakterláncrészletet megfelelő szolgál a normalizált jelszót ellenőrizze a felhasználó első és a Vezetéknév, valamint a bérlő neve (vegye figyelembe, hogy bérlői névegyeztetés nem történik meg egy Active Directory-tartományvezérlő jelszavak érvényesítése során).

Példa: feltételezik, hogy van-e egy felhasználó, Pol jelszavuk "P0l123fb" kívánó. Normalizálási, miután ezt a jelszót "pol123fb" válnak. Megállapítja, hogy a jelszó tartalmazza-e a felhasználó utóneve "Pol" megfelelő karakterláncrészletet. Annak ellenére, hogy kifejezetten vagy letiltott jelszavak listán szereplő nem volt "P0l123fb", "Pol" karakterláncrészletet megfelelő található a jelszót. Ezért ezt a jelszót szeretne elutasították.

#### <a name="score-calculation"></a>Pontszám számítási

A következő lépés, hogy azonosítsa a normalizált új jelszót a felhasználó letiltott jelszavak az összes példányát. Ezután:

1. Minden egyes letiltott jelszavak, amely megtalálható a jelszó van megadva egy pont.
2. Minden fennmaradó egyedi karaktert kap egy pont.
3. Egy jelszónak kell lennie ahhoz, hogy fogadja a pontok legalább öt (5).

A következő két példákban feltételezzük, hogy a Contoso Azure AD jelszóvédelem használja, és saját egyéni lista a "contoso" rendelkezik. Azt is feltételezzük, hogy "üres" szerepel a globális listán.

Példa: a felhasználó megváltoztatja jelszavát az "C0ntos0Blank12"

Normalizálási, miután ezt a jelszót a "contosoblank12" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó tartalmazza-e két letiltott jelszavak: contoso és üres. Ez a jelszó majd pontszámot kap:

[contoso] + [üres] + [1] + [2] = 4 ponttal mivel ezt a jelszót a öt (5) pontot, akkor a rendszer elutasítja.

Példa: a felhasználó megváltoztatja a jelszavát "ContoS0Bl@nkf9!".

Normalizálási, miután ezt a jelszót a "contosoblankf9!" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó tartalmazza-e két letiltott jelszavak: contoso és üres. Ez a jelszó majd pontszámot kap:

[contoso] + [üres] + [f] + [9] + [!] = 5 pontot mivel ez a jelszó legalább öt (5) pontokat, fogadja el a rendszer.

   > [!IMPORTANT]
   > Vegye figyelembe, hogy képes-e a globális listájával együtt a letiltott jelszavak algoritmust és az Azure-ban a folyamatban lévő biztonsági elemzés és kutatási alapján bármikor módosíthatja. A helyszíni tartományvezérlő ügynök szolgáltatás frissített algoritmusok csak után lépnek érvénybe a tartományvezérlő ügynökszoftver újratelepíti a bővítményt.

## <a name="license-requirements"></a>Licenckövetelmények

|   | Az Azure AD jelszóvédelem globális letiltott jelszavak listájával | Az Azure AD jelszóvédelem egyéni letiltott jelszavak listájával|
| --- | --- | --- |
| Csak felhőbeli felhasználók | Azure AD Free | Az Azure AD Premium P1 vagy P2 |
| Szinkronizálja a helyszíni Windows Server Active Directory felhasználók | Az Azure AD Premium P1 vagy P2 | Az Azure AD Premium P1 vagy P2 |

> [!NOTE]
> A helyszíni Windows Server Active Directory felhasználók, amelyek nincsenek szinkronizálva az Azure Active Directoryhoz is további bejelentkezések nélkül hozzáférhet a szinkronizált felhasználók a meglévő licencelés alapján az Azure AD jelszóvédelem előnyeit.

További licencelési információk, beleértve a költségek, találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, megjelenik a következő hibaüzenetet kapja:

Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

- [Az egyéni letiltott jelszavak listájának konfigurálása](howto-password-ban-bad.md)
- [Engedélyezze az Azure AD jelszó védelmi ügynököket a helyszíni](howto-password-ban-bad-on-premises-deploy.md)
