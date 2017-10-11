---
title: "Az Azure Active Directoryban konfigurálható jogkivonat élettartamát |} Microsoft Docs"
description: "Tudnivalók az Azure AD által kiállított jogkivonatokat élettartamai."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: d23721eba308096a05211eb6e26e1338a69cae0c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Konfigurálható jogkivonat élettartamát az Azure Active Directoryban (nyilvános előzetes verzió)
Megadhat egy Azure Active Directory (Azure AD) által kiállított jogkivonat élettartamát. A szervezet jogkivonat élettartamát a szervezet összes alkalmazást, egy több-bérlős (több szervezet) alkalmazáshoz, vagy egy adott szolgáltatás egyszerű állíthatja be.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verziójához. Készüljön visszaállítja, vagy távolítsa el a módosításokat. A szolgáltatás bármely Azure Active Directory-előfizetés nyilvános előzetes érhető el. Azonban, amikor a szolgáltatás általánosan elérhetővé válik, a funkció az egyes funkcióit szükség lehet egy [Azure Active Directory Premium](active-directory-get-started-premium.md) előfizetés.
>
>

Az Azure AD egy csoportházirend-objektum csomagok olyan szabályokat, amelyek az egyes alkalmazásokra, illetve a szervezeten belüli összes alkalmazás lépnek érvénybe. Minden házirendtípus struktúrája egy egyedi, objektumok, amelyek hozzá lesznek rendelve alkalmazott tulajdonságainak vannak beállítva.

Kijelölhet egy házirendet az alapértelmezett házirendet a szervezet számára. A szabályzatot a szervezet bármely alkalmazás mindaddig, amíg nem bírálják felül a magasabb prioritású házirend. Is rendelhet egy házirend bizonyos alkalmazásokat. A prioritásuk szerinti sorrendben függ a házirend típusát.


## <a name="token-types"></a>Tokentípusokat

A frissítési jogkivonatokat, a hozzáférési jogkivonatok, a munkamenet-jogkivonatokat és az azonosító-jogkivonatokat a jogkivonatok élettartama házirendek állíthatja be.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok
Ügyfelek hozzáférési jogkivonatok használják a védett erőforrások eléréséhez. Olyan hozzáférési jogkivonatot csak olyan felhasználó, az ügyfél és az erőforrás egyedi kombinációja használható. Hozzáférési jogkivonatok nem vonható vissza, és azok lejártáig érvényesek. Egy rosszindulatú szereplő, amely egy hozzáférési jogkivonatot kapott használhatja az élettartamuk mértékét. Beállítja a hozzáférési token élettartama között a rendszer teljesítményének javítása és növelése ennyi idő alatt, hogy az ügyfél megőrzi-e a hozzáférést, miután a felhasználói fiók le van tiltva. Továbbfejlesztett rendszer teljesítmény akkor érhető el a szám, ahányszor egy ügyfél szerezzen be egy új jogkivonatot kell csökkentésével.

### <a name="refresh-tokens"></a>Frissítési jogkivonatok
Létrejöttekor olyan hozzáférési jogkivonatot, védett erőforrások eléréséhez, az ügyfél megkapja egy frissítési jogkivonat és a hozzáférési tokent. A frissítési token beszerzése a token párok új hozzáférési/frissítés az aktuális jogkivonat lejártakor történik. A frissítési jogkivonat felhasználói és az ügyfél van kötve. A frissítési jogkivonat visszavonhatók, és a token érvényességi be van jelölve, minden alkalommal, amikor a jogkivonat.

Fontos és bizalmas nyilvános ügyfelek közötti különbségtétel. További információ a különböző típusú ügyfelek: [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>A frissítési jogkivonatokat bizalmas ügyfél token élettartama
Bizalmas ügyfelek olyan alkalmazások, amelyek tudja biztonságosan tárolni ügyfél jelszót (titkos). Hogy a rendszer tudja, hogy kérések érkeznek, az ügyfélalkalmazás nem pedig egy rosszindulatú szereplő. Például a webes alkalmazás nem bizalmas ügyfél, mert egy ügyfélkulcsot képes tárolni a webkiszolgálón. Nem lesz közzétéve. Mivel ezek a folyamatok biztonságosabb, van-e a frissítési jogkivonatokat folyamok kiadott alapértelmezett élettartamának `until-revoked`házirend használatával nem módosítható és nem lehet visszavonni a önkéntes jelszó alaphelyzetbe állítását.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>A nyilvános ügyfél frissítési jogkivonatokat token élettartama

Nyilvános ügyfelek nem tudja biztonságosan tárolni (titkos) ügyfél jelszót. Például az iOS vagy Android-alkalmazások nem takarják tulajdonostól, titkos kulcs, egy nyilvános ügyfél akkor tekinthető. A házirendbe erőforrásokon a akadályozni, hogy frissítési jogkivonatokat, nyilvános ügyfelek régebbi, mint a megadott időszak hozzáférési/frissítési jogkivonat párokat. (Ehhez használja a frissítési Token maximális tétlenség ideje tulajdonságát.) Házirendek beállítása az adott időszakban, amelyen túl már nem fogad a frissítési jogkivonatokat is használja. (Ehhez használja a frissítési Token maximális életkora tulajdonságát.) Módosíthatja is szabályozhatja, mikor és milyen gyakran szükséges-e a felhasználó éppen csendes hitelesíteni, egy nyilvános ügyfélalkalmazás használata helyett a hitelesítő adatok újbóli frissítési jogkivonat élettartamát.

### <a name="id-tokens"></a>Azonosító-jogkivonatokat
Webhelyek és natív ügyfelek átadott azonosító-jogkivonatokat. Azonosító-jogkivonatokat a felhasználói profil adatait tartalmazzák. Egy azonosító jogkivonat felhasználói és az ügyfél egyedi kombinációja van kötve. Azonosító-jogkivonatokat érvényesek a lejártáig. Általában egy webes alkalmazás megfelel a felhasználó a munkamenetek élettartamát az az alkalmazás azonosítója jogkivonat élettartamát adja ki a felhasználó. Egy azonosító jogkivonatot, milyen gyakran a webes alkalmazás lejár az alkalmazás munkamenet, és milyen gyakran szükség van a felhasználó számára (csendes vagy interaktív) az Azure AD-val hitelesíthető vezérlésére élettartama módosíthatja.

### <a name="single-sign-on-session-tokens"></a>Egyszeri bejelentkezés munkamenetet jogkivonatok
Amikor egy felhasználó hitelesíti magát az Azure ad-val, és kiválasztja a **bejelentkezve szeretnék maradni** jelölőnégyzetet, egy egyszeri bejelentkezés (SSO) munkamenet a felhasználó böngésző és az Azure AD. A cookie-k formájában, az egyszeri bejelentkezési token jelöli ehhez a munkamenethez. Vegye figyelembe, hogy az egyszeri bejelentkezés munkameneti jogkivonat nincs kötve egy adott erőforrás/ügyfélalkalmazást. Egyszeri bejelentkezési munkamenet jogkivonatok visszavonhatók, és azok érvényességi be van jelölve, minden alkalommal, amikor a segítségükkel.

Az Azure AD használ egyszeri bejelentkezési munkamenet jogkivonatok kétféle: állandó, és nem állandó. Állandó munkamenet jogkivonatok állandó cookie-kat a böngészőben tárolódnak. Nem állandó munkamenet jogkivonatok munkamenet cookie-kat, tárolódnak. (A munkamenet cookie-k megsemmisülnek a böngésző bezárásakor.)

Nem állandó munkamenet jogkivonatok élettartama pedig 24 óra. Állandó jogkivonatok élettartama 180 nap. Az egyszeri bejelentkezési munkamenet jogkivonat az érvényességi idején belül bármikor érvényességi ki van bővítve másik 24 óra vagy 180 nap, attól függően, hogy a jogkivonat típusa. Ha egy egyszeri bejelentkezési munkamenet-azonosító nem használják az érvényességi idején belül, lejárt, és már nem elfogadható minősül.

Egy házirend használatával adja meg, miután az első munkamenet-azonosító túl, amely már nem a munkamenet jogkivonatából elfogadható ki. (Ehhez a tulajdonsággal munkamenet Token maximális életkora.) A munkamenet jogkivonatából szabályozásához, hogy mikor és milyen gyakran kell-e a felhasználó helyett csendes hitelesített, a webes alkalmazás használatakor a hitelesítő adatok újbóli élettartama módosíthatja.

### <a name="token-lifetime-policy-properties"></a>A jogkivonatok élettartama házirend tulajdonságai
A jogkivonatok élettartama házirend a csoportházirend-objektum, amely tartalmazza a jogkivonatok élettartama szabályok típusú. A házirend tulajdonságainak segítségével szabályozhatja a megadott jogkivonat élettartamát. Ha nincs házirend van beállítva, a rendszer az alapértelmezett élettartam érték érvénybe lépteti.

### <a name="configurable-token-lifetime-properties"></a>A jogkivonatok élettartama konfigurálható tulajdonságai
| Tulajdonság | Házirend tulajdonság karakterlánc | Érint | Alapértelmezett | Minimális | Maximális |
| --- | --- | --- | --- | --- | --- |
| Hozzáférés a jogkivonatok élettartama |AccessTokenLifetime |Hozzáférési jogkivonatok, azonosító-jogkivonatokat, egy SAML2 jogkivonatok |1 óra |10 perc |1 nap |
| Frissítse a Token maximális tétlenség ideje |MaxInactiveTime |Frissítési jogkivonatok |14 napja |10 perc |90 nap |
| Single-tényező frissítési jogkivonat maximális életkora |MaxAgeSingleFactor |Frissítési jogkivonatok (a felhasználók számára) |Amíg visszavonása |10 perc |Amíg visszavont<sup>1</sup> |
| Multi-factor Authentication frissítési jogkivonat maximális életkora |MaxAgeMultiFactor |Frissítési jogkivonatok (a felhasználók számára) |Amíg visszavonása |10 perc |Amíg visszavont<sup>1</sup> |
| Single-tényező munkamenet Token maximális életkora |MaxAgeSessionSingleFactor<sup>2</sup> |Munkamenet-jogkivonatok (állandó és nem állandó) |Amíg visszavonása |10 perc |Amíg visszavont<sup>1</sup> |
| Multi-factor Authentication munkamenet Token maximális életkora |MaxAgeSessionMultiFactor<sup>3</sup> |Munkamenet-jogkivonatok (állandó és nem állandó) |Amíg visszavonása |10 perc |Amíg visszavont<sup>1</sup> |

* <sup>1</sup>365 nap a maximális hosszúságát, hogy ezek az attribútumok állítható be.
* <sup>2</sup>Ha **MaxAgeSessionSingleFactor** nincs beállítva, ez az érték szükséges a **MaxAgeSingleFactor** érték. Ha sem a paraméter értéke, a tulajdonság az alapértelmezett érték (amíg visszavont) vesz igénybe.
* <sup>3</sup>Ha **MaxAgeSessionMultiFactor** nincs beállítva, ez az érték szükséges a **MaxAgeMultiFactor** érték. Ha sem a paraméter értéke, a tulajdonság az alapértelmezett érték (amíg visszavont) vesz igénybe.

### <a name="exceptions"></a>Kivételek
| Tulajdonság | Érint | Alapértelmezett |
| --- | --- | --- |
| Frissítse a Token maximális életkora (összevont rendelkező felhasználók számára megfelelő visszavonási információ kiadott<sup>1</sup>) |Frissítési jogkivonatok (összevont rendelkező felhasználók számára megfelelő visszavonási információ kiadott<sup>1</sup>) |12 óra |
| Frissítse a Token maximális tétlenség ideje (bizalmas ügyfelek ki) |Frissítési jogkivonatok (bizalmas ügyfelek ki) |90 nap |
| Frissítse a Token maximális életkora (bizalmas ügyfelek ki) |Frissítési jogkivonatok (bizalmas ügyfelek ki) |Amíg visszavonása |

* <sup>1</sup>külső felhasználók, akik a megfelelő visszavonási információk közé tartozik a szinkronizált "LastPasswordChangeTimestamp" attribútum nem rendelkező felhasználók. Ezek a felhasználók kapnak e rövid maximális életkora, mert aad-ben nem tudja ellenőrizni, mikor érdemes visszavonni egy régi hitelesítő adatok (például egy jelszót, amely módosult) vannak társítva, és kell látogasson vissza több gyakran annak érdekében, hogy a felhasználó és a kapcsolódó jogkivonatok továbbra is a helyes állandó jogkivonatok. A felhasználói élmény javítása érdekében a bérlői rendszergazda biztosítania kell, hogy azok szinkronizálását végzi a "LastPasswordChangeTimestamp" attribútumot (Ez a user objektum Powershell használatával vagy állítható AADSync keresztül).

### <a name="policy-evaluation-and-prioritization"></a>Házirend kiértékelése és rangsorolási
Hozzon létre, és hozzárendelheti a jogkivonatok élettartama házirend, az adott alkalmazást, a szervezet számára, és szolgáltatásnevekről. Az adott alkalmazást több házirendet is vonatkozhatnak. A jogkivonatok élettartama házirend érvénybe ezeket a szabályokat követi:

* Ha egy házirend explicit módon van rendelve a szolgáltatás egyszerű, a rendszer érvényesíti.
* Ha nincs házirend explicit módon van rendelve a szolgáltatás egyszerű, egy a szolgáltatás egyszerű szülő szervezése explicit módon hozzárendelt lesz alkalmazva,
* Nincs házirend explicit módon van hozzárendelve, a szolgáltatás egyszerű vagy a szervezet számára, ha a szabályzatot az alkalmazáshoz rendelt érvényesül.
* Ha a szolgáltatás egyszerű, a szervezet vagy az objektum nincs házirend lett rendelve, a rendszer érvényesíti az alapértelmezett értékeket. (Lásd a táblázatot [konfigurálható a jogkivonatok élettartama tulajdonságok](#configurable-token-lifetime-properties).)

A kapcsolat, alkalmazás és szolgáltatás egyszerű objektumok között kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](active-directory-application-objects.md).

A token érvényességi ki lesz értékelve a jogkivonat időpontjában. A legmagasabb prioritású az alkalmazást, amely használatban van a házirend érvénybe lép.

> [!NOTE]
> Íme egy példa.
>
> A felhasználó hozzá kíván férni két webes alkalmazások: A webalkalmazás és a webes alkalmazás a b kiszolgálóra.
> 
> Tényezői:
> * Mindkét webalkalmazások az ugyanazon szülő vállalatnál találhatók.
> * A munkamenet biztonságijogkivonat maximális életkora nyolc óra jogkivonat élettartamát házirend 1 a szülő szervezeti alapértelmezés állítja be.
> * A webalkalmazás rendszeres használható webalkalmazás, és nem kapcsolódik a szabályzatoknak.
> * Webes alkalmazás B szigorúan bizalmas folyamatok szolgál. Az egyszerű szolgáltatásnév a jogkivonat élettartamát házirend 2, amely rendelkezik egy munkamenet Token maximális életkora 30 perces van csatolva.
>
> A 12:00 PM a felhasználó új böngésző-munkamenet indítása, és megpróbál hozzáférni a webes alkalmazás újraindul A felhasználó a rendszer átirányítja az Azure AD, és van-e be kell jelentkeznie. Ezzel létrehoz egy cookie-t, amely rendelkezik a munkamenet jogkivonatának a böngészőben. A felhasználó biztonsági Azonosítót jogkivonatban lehetővé teszi a felhasználónak az alkalmazás eléréséhez A webalkalmazás van átirányítva.
>
> A 12:15 előtti a felhasználó megpróbál hozzáférni a webes alkalmazás a b kiszolgálóra. A böngésző átirányítja az Azure AD, amely észleli a munkamenetcookie-t. Webes alkalmazás B szolgáltatás egyszerű jogkivonat élettartamát házirend 2 kapcsolódik, de a szülő szervezet alapértelmezett jogkivonat élettartamát házirend 1 részét. Jogkivonat élettartamát házirend 2 lép érvénybe, mert szolgáltatásnevekről kapcsolódó házirendek magasabb prioritású, mint a szervezet alapértelmezett házirendeket. A munkamenet jogkivonatából eredetileg ki az utolsó 30 percen belül, érvényes tekinthető. A felhasználó vissza a webes alkalmazás B Azonosítót jogkivonatban hozzáférést biztosít az átirányítási.
>
> Du. 1:00 a felhasználó megpróbál hozzáférni a webes alkalmazás újraindul A felhasználó a rendszer átirányítja az Azure ad Szolgáltatásba. Web Application egy nem kapcsolódik a házirendekben, de mivel alapértelmezett jogkivonat élettartamát házirend 1 rendelkező szervezeteknél, a házirend érvénybe lép. A rendszer észlelte a munkamenetcookie-t, amely eredetileg adta ki a legutóbbi nyolc órán belül. A felhasználó csendes átirányítási vissza a webes alkalmazás egy új azonosítója jogkivonattal. A felhasználó nem kell hitelesíteni.
>
> Ezt követően azonnal a felhasználó megpróbál hozzáférni a webes alkalmazás a b kiszolgálóra. A felhasználó a rendszer átirányítja az Azure ad Szolgáltatásba. Mivel előtt, a jogkivonat élettartamát házirend 2 lép érvénybe. A token ki több, mint harminc perccel ezelőtt történt, mert a bejelentkezési hitelesítő adatok újbóli kéri a felhasználót. Egy vadonatúj munkamenet és azonosító jogkivonatot adják ki. A felhasználó ezután hozzáférhetnek a webes alkalmazás a b kiszolgálóra.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurálható szabályzatot tulajdonság részletei
### <a name="access-token-lifetime"></a>Hozzáférés a jogkivonatok élettartama
**Karakterlánc:** AccessTokenLifetime

**Érinti:** hozzáférési jogkivonatok, azonosító-jogkivonatokat

**Összefoglalás:** Ez az irányelv szabályozza, hogy mennyi ideig hozzáférési és azonosító-jogkivonatokat ehhez az erőforráshoz érvényesek. Csökkenti a hozzáférési jogkivonat élettartamát tulajdonság csökkenti egy hozzáférési jogkivonatot, vagy a azonosító token idő hosszú időn keresztül a rosszindulatú szereplő használják. (Ezeket a jogkivonatokat nem vonható vissza.) A kompromisszum, hogy a teljesítményt hátrányosan kihatással van, mert a jogkivonatok gyakrabban le kell cserélni.

### <a name="refresh-token-max-inactive-time"></a>Frissítse a Token maximális tétlenség ideje
**Karakterlánc:** MaxInactiveTime

**Érinti:** frissítési jogkivonatok

**Összefoglalás:** Ez az irányelv szabályozza, hogy hány éves frissítési jogkivonat lehet, mielőtt egy ügyfél nem használhatja azt hozzáférési/frissítési jogkivonat párokat beolvasni az erőforrás elérésére tett kísérlet során. Egy új frissítési jogkivonat általában ad vissza egy frissítési jogkivonat használata esetén, mert a házirend meggátolja hozzáférés, ha az ügyfél megpróbálja során a megadott időtartam a jelenlegi frissítési jogkivonat használatával bármilyen olyan erőforrás elérésére.

Ez a házirend kényszeríti a felhasználók, akik nem használtak az ügyfél egy új frissítési jogkivonat beolvasása újból hitelesítésre.

A frissítési Token maximális tétlenség ideje tulajdonságot meg kell a Single-tényező Token maximális életkora és a multi-factor Authentication Token maximális frissítése életkora tulajdonságok alacsonyabb értékeket.

### <a name="single-factor-refresh-token-max-age"></a>Single-tényező frissítési jogkivonat maximális életkora
**Karakterlánc:** MaxAgeSingleFactor

**Érinti:** frissítési jogkivonatok

**Összefoglalás:** a házirend szabályozza, mennyi ideig egy felhasználó felhasználhat egy frissítési jogkivonat hozzáférési/frissítési jogkivonat párokat azok legutóbbi hitelesítése után sikeresen csak tényező használatával. Miután egy felhasználó hitelesíti, és egy új frissítési jogkivonat kap, a felhasználó használhatja a frissítési jogkivonat-folyamat a megadott időn. (Ez igaz, ha az aktuális frissítési jogkivonat nem vonták vissza, és azt nem marad több mint a tétlenség ideje nem használt.) Ezen a ponton a felhasználónak meg kell újból egy új frissítési jogkivonat fogadásához hitelesítésre.

A felhasználók hitelesítést gyakrabban csökkenti a maximális életkora kényszeríti. Mert minősül, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy egy érték, amely egyenlő vagy kisebb, mint a multi-factor Authentication Token maximális frissítése életkora tulajdonság állítani ezt a tulajdonságot.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-factor Authentication frissítési jogkivonat maximális életkora
**Karakterlánc:** MaxAgeMultiFactor

**Érinti:** frissítési jogkivonatok

**Összefoglalás:** a házirend szabályozza, mennyi ideig egy felhasználó felhasználhat egy frissítési jogkivonat hozzáférési/frissítési jogkivonat párokat azok legutóbbi hitelesítése után sikeresen több tényezővel használatával. Miután egy felhasználó hitelesíti, és egy új frissítési jogkivonat kap, a felhasználó használhatja a frissítési jogkivonat-folyamat a megadott időn. (Ez igaz, amíg a jelenlegi frissítési jogkivonat nem vonták vissza, és nincs több mint a tétlenség ideje nem használt.) Ezen a ponton felhasználók kényszerítve vannak-e egy új frissítési jogkivonat fogadásához újból hitelesítésre.

A felhasználók hitelesítést gyakrabban csökkenti a maximális életkora kényszeríti. Mert minősül, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy egy érték, amely egyenlő vagy nagyobb, mint a Single-tényező Token maximális frissítése életkora tulajdonság állítani ezt a tulajdonságot.

### <a name="single-factor-session-token-max-age"></a>Single-tényező munkamenet Token maximális életkora
**Karakterlánc:** MaxAgeSessionSingleFactor

**Érinti:** munkamenet jogkivonatok (állandó és nem állandó)

**Összefoglalás:** a házirend szabályozza, mennyi ideig egy felhasználó felhasználhat egy munkamenet-azonosító egy új Azonosítót és a munkameneti jogkivonat azok legutóbbi hitelesítése után sikeresen csak tényező használatával. Miután egy felhasználó hitelesíti, és egy új munkamenet jogkivonatot kap a felhasználó használhatja a munkamenet jogkivonat-folyamat a megadott időn. (Ez igaz, amíg a jelenlegi munkamenet-azonosító nem vonták vissza és nem járt le.) A megadott idő után a felhasználónak meg kell újból hitelesítésre fogadásához egy új munkamenet-azonosító.

A felhasználók hitelesítést gyakrabban csökkenti a maximális életkora kényszeríti. Mert minősül, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy egy érték, amely egyenlő vagy kisebb, mint a multi-factor Authentication munkamenet Token maximális életkora tulajdonság állítani ezt a tulajdonságot.

### <a name="multi-factor-session-token-max-age"></a>Multi-factor Authentication munkamenet Token maximális életkora
**Karakterlánc:** MaxAgeSessionMultiFactor

**Érinti:** munkamenet jogkivonatok (állandó és nem állandó)

**Összefoglalás:** a házirend vezérlőelemek mennyi ideig a felhasználók egy munkamenet-azonosító használatával egy új Azonosítót és egy munkamenet-token beszerzése után azok sikeresen hitelesített több tényezővel használatával legutóbbi. Miután egy felhasználó hitelesíti, és egy új munkamenet jogkivonatot kap a felhasználó használhatja a munkamenet jogkivonat-folyamat a megadott időn. (Ez igaz, amíg a jelenlegi munkamenet-azonosító nem vonták vissza és nem járt le.) A megadott idő után a felhasználónak meg kell újból hitelesítésre fogadásához egy új munkamenet-azonosító.

A felhasználók hitelesítést gyakrabban csökkenti a maximális életkora kényszeríti. Mert minősül, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy egy érték, amely egyenlő vagy nagyobb, mint a Single-tényező munkamenet Token maximális életkora tulajdonság állítani ezt a tulajdonságot.

## <a name="example-token-lifetime-policies"></a>A házirendek például a jogkivonatok élettartama
Számos forgatókönyv is előfordulhatnak, ha akkor hozhat létre és kezelhet jogkivonat élettartamát az alkalmazásokkal, szolgáltatásnevekről és a teljes szervezet Azure AD-ben. Ez a szakasz azt bízná néhány olyan gyakori házirend forgatókönyvet, amelyik segíthet a maximálásához új szabályokat is:

* A jogkivonatok élettartama
* Inaktív token maximális idő
* Token maximális életkora

A példákban is megismerheti, hogyan:

* A szervezetek alapértelmezett házirend kezelése
* Webalkalmazás-bejelentkezés házirend létrehozása
* Hozzon létre egy házirendet, amely behívja a webes API-k natív alkalmazások
* Egy speciális házirend kezelése

### <a name="prerequisites"></a>Előfeltételek
A következő példákban meg létrehozása, frissítése, hivatkozás és törölje az alkalmazásokkal, szolgáltatásnevekről és a teljes szervezet házirendeket. Ha most ismerkedik az Azure AD, azt javasoljuk, hogy Ismerkedjen meg kapcsolatos [az Azure AD-bérlő beszerzése](active-directory-howto-tenant.md) ezekben a példákban folytatása előtt.  

A kezdéshez tegye a következőket:

1. Töltse le a legújabb [az Azure AD PowerShell modul nyilvános előzetes verzió](https://www.powershellgallery.com/packages/AzureADPreview).
2. Futtassa a `Connect` parancs futtatásával jelentkezzen be az Azure AD rendszergazdai fiókot. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenet indításához.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. A szervezet létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Ez a parancs futtatása után a következő esetekben a műveleteik. A parancs futtatása is segíti, hogy a ** ** a házirendek.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Példa: Egy szervezet alapértelmezett házirend kezelése
Ebben a példában létrehozhat olyan házirendet, amely lehetővé teszi, hogy a felhasználók ritkábban jelentkezzen be a teljes szervezetben. Ehhez a jogkivonatok élettartama házirend létrehozása egyetlen tényezős frissítési jogkivonatokat, a szervezetben alkalmazott. Minden alkalmazásához a szervezetben, és minden egyszerű szolgáltatásnév, amely még nem rendelkezik a beállított házirend van érvényben.

1. A jogkivonatok élettartama házirend létrehozása.

    1.  Állítsa a Single-tényező frissítési jogkivonat "amíg visszavont." A jogkivonat nem jár le, amíg hozzáférést visszavonva. A következő házirend-definíció létrehozása:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  A szabályzat létrehozásához futtassa a következő parancsot:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot **ObjectId**, a következő parancsot:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. A házirend frissítése.

    Dönthet úgy, hogy az első házirend, ebben a példában beállítása nincs legszigorúbb a szolgáltatásnak szüksége van. A Single-tényező frissítési jogkivonat két nap múlva lejár beállításához futtassa a következő parancsot:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Példa: A webalkalmazás-bejelentkezés házirend létrehozása

Ebben a példában a felhasználók a webalkalmazáson belüli gyakrabban hitelesítést igénylő házirend létrehozása. Ez a házirend a webalkalmazás egyszerű a hozzáférés vagy-azonosító jogkivonatok élettartama, és a multi-factor Authentication munkameneti jogkivonat maximális életkora állítja be.

1. A jogkivonatok élettartama házirend létrehozása.

    Ezt a házirendet, a web-bejelentkezés, a hozzáférés vagy-azonosító a jogkivonatok élettartama és a maximális egyetlen tényezős munkamenet jogkivonat élettartamát kétórás állítja be.

    1.  A házirend létrehozásához futtassa a parancsot:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot **ObjectId**, a következő parancsot:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  A házirend hozzárendelése a szolgáltatásnevet. Is kell beszereznie a **ObjectId** a szolgáltatás rendszerbiztonsági tag. 

    1.  A szervezet szolgáltatásnevekről megtekintéséhez kérdezze [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Vagy a [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/), jelentkezzen be az Azure AD-fiókot.

    2.  Ha rendelkezik a **ObjectId** , a szolgáltatás egyszerű, a következő parancsot:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Példa: Egy natív alkalmazás, amely behívja a webes API-k házirend létrehozása
Ebben a példában a felhasználók ritkábban hitelesítést igénylő házirend létrehozása. A házirend is hosszabb lesz idő a felhasználó lehet inaktív, mielőtt a felhasználó ismét hitelesítenie kell magát. A szabályzatot a webes API-hoz. Ha a natív alkalmazással a webes API-erőforrásként kér, a házirend érvényben van.

1. A jogkivonatok élettartama házirend létrehozása.

    1.  A webes API-k szigorú házirend létrehozásához a következő parancsot:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot **ObjectId**, a következő parancsot:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. A házirend hozzárendelése a webes API-t. Is kell beszereznie a **ObjectId** az alkalmazás. A legjobb módszer az alkalmazásban található **ObjectId** használata a [Azure-portálon](https://portal.azure.com/).

   Ha rendelkezik a **ObjectId** az alkalmazás a következő parancsot:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Példa: Egy speciális házirend kezelése
Ebben a példában néhány házirendek, megtudhatja, hogyan működik, a prioritás rendszer hoz létre. Is megismerheti az több objektumot alkalmazott több házirend kezeléséhez.

1. A jogkivonatok élettartama házirend létrehozása.

    1.  Egy szervezet alapértelmezett házirendet, amely beállítja a Single-tényező frissítse a jogkivonatok élettartama 30 nap szeretne létrehozni, a következő parancsot:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot **ObjectId**, a következő parancsot:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. A házirend hozzárendelése egy egyszerű szolgáltatást.

    Most hogy egy házirendet az egész szervezetre érvényes. Előfordulhat, hogy szeretné megőrizni a 30 napos házirend az egy adott szolgáltatás egyszerű, de a szervezet alapértelmezett házirend módosítsa a felső korlátja "amíg visszavont."

    1.  A szervezet szolgáltatásnevekről megtekintéséhez kérdezze [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Vagy a [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/), jelentkezzen be az Azure AD-fiókot.

    2.  Ha rendelkezik a **ObjectId** , a szolgáltatás egyszerű, a következő parancsot:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Állítsa be a `IsOrganizationDefault` jelző hamis értékre:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Hozzon létre egy új szervezeti alapértelmezett házirendet:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már a szolgáltatás egyszerű csatolva az eredeti házirenddel rendelkezik, és a szervezet alapértelmezett házirend be van állítva az új házirend. Fontos megjegyezni, hogy a szolgáltatás rendszerbiztonsági tagok alkalmazott házirendek elsőbbséget élveznek a szervezet alapértelmezett házirendeket.

## <a name="cmdlet-reference"></a>A parancsmagok leírása

### <a name="manage-policies"></a>Házirendek kezelése

Az alábbi parancsmagok segítségével kezelheti a házirendeket.

#### <a name="new-azureadpolicy"></a>Új AzureADPolicy

Létrehoz egy új házirendet.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Definition</code> |A szabályzat előírásainak tartalmazó stringified JSON tömbje. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |A név karakterláncának részlete házirend. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Igaz értéke esetén a szervezet alapértelmezett házirend állítja be a házirendet. Ha hamis, nincs hatása. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Házirend típusa. A jogkivonat élettartamát mindig használja az "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Választható] |Beállítja a házirend alternatív azonosítója. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Minden Azure AD-házirendek vagy a megadott házirend lekérése.

```PowerShell
Get-AzureADPolicy
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code>[Választható] |**Objektumazonosító (Id)** a kívánt házirendet. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Minden alkalmazást és egy házirend csatolt szolgáltatásnevekről lekérdezi.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** a kívánt házirendet. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Frissíti a meglévő szabályzatokat.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** a kívánt házirendet. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |A név karakterláncának részlete házirend. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Választható] |A szabályzat előírásainak tartalmazó stringified JSON tömbje. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Választható] |Igaz értéke esetén a szervezet alapértelmezett házirend állítja be a házirendet. Ha hamis, nincs hatása. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Választható] |Házirend típusa. A jogkivonat élettartamát mindig használja az "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Választható] |Beállítja a házirend alternatív azonosítója. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
A megadott házirend törlése.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** a kívánt házirendet. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Alkalmazás-házirendek
A következő parancsmagokat használhatja az alkalmazás-házirendek.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Adja hozzá AzureADApplicationPolicy
A megadott házirend alkalmazásokra mutató hivatkozások.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** a házirend. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Lekérdezi a házirendet, amely hozzá van rendelve egy alkalmazást.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Egy alkalmazás-házirend eltávolítása.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** a házirend. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Szolgáltatás egyszerű házirendjei
Szolgáltatás egyszerű házirendjei a következő parancsmag használható.

#### <a name="add-azureadserviceprincipalpolicy"></a>Adja hozzá AzureADServicePrincipalPolicy
A megadott házirend csatol egy egyszerű szolgáltatást.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** a házirend. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Lekérdezi a megadott szolgáltatásnév kapcsolódó házirendek.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
A megadott egyszerű szolgáltatás eltávolítja a házirendet.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objektumazonosító (Id)** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** a házirend. | `-PolicyId <ObjectId of Policy>` |
