---
title: Dinamikusan letiltott jelszavak az Azure ad-ben
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
ms.openlocfilehash: ca079fa2ec41f85c365102fbd81ffde23e97e2c4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990492"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Rossz jelszavak, a szervezet számára

|     |
| --- |
| Az Azure AD jelszóvédelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Iparági vezetők mondja el, hogy nem összetett, és nem révén egyszerűen/Password123 például a több helyen, ugyanazt a jelszót. Hogyan biztosítható a szervezetek számára, hogy a felhasználók útmutatást követi? Hogyan lehet, győződjön meg arról, felhasználók nem közös jelszavak vagy szereplő legutóbbi adatszivárgásokat ismert jelszavak?

## <a name="global-banned-password-list"></a>Globális letiltott jelszavak listája

A Microsoft mindig azon dolgozik, hogy egy lépéssel a számítógépes bűnözők előtt járjon. Ezért az Azure AD Identity Protection csapata folyamatosan keresse meg a gyakran használt és a feltört jelszavakat. Ezután letiltják ezeket a jelszavakat, hogy mi a globális letiltott jelszavak lista neve túl gyakori beállításkulcsoknak. Bűnözők is hasonló stratégiákat használnak a saját támadások, ezért a Microsoft nem teszi közzé a lista tartalma nyilvánosan. Ezek a sebezhető jelszavak le vannak tiltva, mielőtt azok a valódi fenyegetést Microsoft ügyfeleire vonatkozik. Az aktuális biztonsági erőfeszítések kapcsolatos további információkért tekintse meg a [a Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Előzetes verzió: Letiltott jelszavak egyéni listája

Egyes szervezetek biztonsági egy lépéssel tovább igénybe a Microsoft által a letiltott jelszavak egyéni lista a globális letiltott jelszavak lista fölött saját testreszabások hozzáadásával lehet. Vállalati felhasználók például Contoso Ezután dönthet blokkolása a márka nevüket, a vállalatra jellemző használati vagy más elemeket tartalmazza.

Az egyéni le van tiltva, jelszó listáját és az ezekben a helyszíni Active Directory-integráció kezeli az Azure portal használatával.

![Az Azure Portalon a hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>A helyszíni hibrid forgatókönyvek

Kizárólag felhőalapú fiókok védelme akkor lehet hasznos, de számos szervezet hibrid környezetekben, beleértve a helyszíni Windows Server Active Directory karbantartása. A Windows Server Active Directory (előzetes verzió) ügynökök a helyszíni a letiltott jelszavak listáit, a meglévő infrastruktúra kiterjesztésére az Azure AD jelszóvédelem telepítése lehetőség. Most, hogy a felhasználók és rendszergazdák, akik módosítja, állítsa be, vagy jelszavakat a helyszíni szükségesek a csak felhőalapú felhasználói is azonos jelszóházirend ahhoz, hogy.

## <a name="how-are-passwords-evaluated"></a>Hogyan értékeli a jelszavak

Amikor a felhasználó módosítja, vagy a jelszó alaphelyzetbe állítása, az új jelszót a rendszer ellenőrzi erősségét és a bonyolultságot érvényesítésével azonosítsa a globális és a letiltott jelszavak egyéni lista alapján (Ha ez utóbbi van konfigurálva).

Akkor is, ha egy felhasználó jelszavát egy letiltott jelszavak tartalmaz, a jelszó előfordulhat, hogy továbbra is fogadja, ha a teljes jelszó nem elég erős más módon. Egy újonnan konfigurált jelszóval a következő lépéseket kell meghatározni, ha azt kell elfogad vagy elutasít általános erősségét felmérése fog áthaladni.

### <a name="step-1-normalization"></a>1. lépés: Normalizálási

Új jelszó először halad végig egy normalizálási folyamat lépéseit követve. Ez lehetővé teszi egy kis készletét letiltott jelszavak le kell képezni egy potenciálisan gyenge jelszavakat sokkal nagyobb készlete.

Normalizálási két részből áll.  Először is, a nagybetűs betűket kisbetűsre módosult.  Második, közös karaktert helyettesítő például a következőket kell végrehajtani:  

| Eredeti betűjele  | A helyettesített betűjele |
| --- | --- |
| '0'  | "o' |
| '1'  | "l" |
| '$'  | a " |
| '@'  | "a" |

Példa: Tegyük fel, hogy a jelszó "üres" le van tiltva, és a egy felhasználó próbál módosítani a jelszavát, a "Bl@nK". Annak ellenére, hogy "Bl@nk" van normalizálási folyamat alakítja át nem kifejezetten le van tiltva, ezt a jelszót "blank", amely egy letiltott jelszavak.

### <a name="step-2-check-if-password-is-considered-banned"></a>2. lépés: Annak ellenőrzése, hogy a jelszó számít le van tiltva

#### <a name="fuzzy-matching-behavior"></a>Az intelligens egyező viselkedés

Intelligens megfelelő normalizált jelszavát a azonosítására szolgál, ha az egyéni le van tiltva a jelszavak listáit vagy található vagy a globális jelszót tartalmaz. A megfelelő folyamat egy szerkesztési távolság egy (1) összehasonlítás alapján történik.  

Példa: Tegyük fel, hogy a jelszó "abcdef" le van tiltva, és a egy felhasználó megpróbál módosítania kell a jelszavát, az alábbi lehetőségek közül:

"abcdeg"    *(legutóbbi változása: "f" karakter "g")* "abcdefg"   *"(g" hozzáfűzött célból)* "abcde"     *(a záró "f" end végpontjától törölve)*

A fenti jelszavak mindegyike külön nem egyezik a letiltott jelszavak "abcdef". Azonban mivel valamennyi példa az 1. a letiltott token "abcdef" Szerkesztés távolságát belül, azok minden tekinthetők, "abcdef" egyezést.

#### <a name="substring-matching-on-specific-terms"></a>(Az adott feltételek) egyező karakterláncrészletet

Karakterláncrészletet megfelelő szolgál a normalizált jelszót ellenőrizze a felhasználó első és a Vezetéknév, valamint a bérlő neve (vegye figyelembe, hogy bérlői névegyeztetés nem történik meg egy Active Directory-tartományvezérlő jelszavak érvényesítése során).

Példa: Tegyük fel, hogy van-e, amely a "J0hn123fb" jelszó visszaállítása szeretne John Doe felhasználó. Normalizálási, miután ezt a jelszót "john123fb" válnak. Megállapítja, hogy a jelszó tartalmazza-e a felhasználó utóneve "János" megfelelő karakterláncrészletet. Annak ellenére, hogy kifejezetten vagy letiltott jelszavak listán szereplő nem volt "J0hn123fb", "János" karakterláncrészletet megfelelő található a jelszót. Ezért ezt a jelszót szeretne elutasították.

#### <a name="score-calculation"></a>Pontszám számítási

A következő lépés, hogy azonosítsa a normalizált új jelszót a felhasználó letiltott jelszavak az összes példányát. Ezután:

1. Minden egyes letiltott jelszavak, amely megtalálható a jelszó van megadva egy pont.
2. Minden fennmaradó egyedi karaktert kap egy pont.
3. Egy jelszónak kell lennie ahhoz, hogy el kell fogadni legalább 5 pontot.

A következő két példákban feltételezzük, hogy a Contoso Azure AD jelszóvédelem használja, és saját egyéni lista a "contoso" rendelkezik. Azt is feltételezzük, hogy "üres" szerepel a globális listán.

Példa: a felhasználó megváltoztatja jelszavát az "C0ntos0Blank12"

Normalizálási, miután ezt a jelszót a "contosoblank12" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó tartalmazza-e két letiltott jelszavak: contoso és üres. Ez a jelszó majd pontszámot kap:

[contoso] + [üres] + [1] + [2] = 4 ponttal mivel ezt a jelszót a 5 pontot, akkor a rendszer elutasítja.

Példa: a felhasználó megváltoztatja a jelszavát "ContoS0Bl@nkf9!".

Normalizálási, miután ezt a jelszót a "contosoblankf9!" lesz. A megfelelő folyamat megállapítja, hogy ez a jelszó tartalmazza-e két letiltott jelszavak: contoso és üres. Ez a jelszó majd pontszámot kap:

[contoso] + [üres] + [f] + [9] + [!] = 5 pontot mivel ez a jelszó legalább 5 pontot, fogadja el a rendszer.

   > [!IMPORTANT]
   > Vegye figyelembe, hogy képes-e a globális listájával együtt a letiltott jelszavak algoritmust és az Azure-ban a folyamatban lévő biztonsági elemzés és kutatási alapján bármikor módosíthatja. A helyi tartományvezérlő ügynök szolgáltatás frissített algoritmusok csak után lépnek érvénybe a tartományvezérlő ügynökszoftver újratelepíti a bővítményt.

## <a name="license-requirements"></a>Licenckövetelmények

|   | Az Azure AD jelszóvédelem globális letiltott jelszavak listájával | Az Azure AD jelszóvédelem egyéni letiltott jelszavak listájával|
| --- | --- | --- |
| Csak felhőbeli felhasználók | Azure AD Free | Azure AD Basic |
| Szinkronizálja a helyszíni Windows Server Active Directory felhasználók | Az Azure AD Premium P1 vagy P2 | Az Azure AD Premium P1 vagy P2 |

További licencelési információk, beleértve a költségek, találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, megjelenik a következő hibaüzenetet kapja:

Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

* [Az egyéni letiltott jelszavak listájának konfigurálása](howto-password-ban-bad.md)
* [Engedélyezze az Azure AD jelszó védelmi ügynököket a helyszíni](howto-password-ban-bad-on-premises-deploy.md)
