---
title: Az Azure Active Directoryban konfigurálható jogkivonatok élettartamának |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure AD által kiállított jogkivonatok élettartamát.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b0242a8e3745a0014e5c2a1289ca2bc8c85c75
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484543"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Az Azure Active Directoryban (előzetes verzió) konfigurálható jogkivonatok élettartama

Az Azure Active Directory (Azure AD) által kiállított jogkivonatok élettartamát is megadhat. Beállíthatja a jogkivonatok élettartamának, a szervezet minden alkalmazás, több-bérlős (több cég) alkalmazás vagy egy adott szolgáltatásnév a szervezetben.

> [!IMPORTANT]
> Várjuk ügyfeleink az előzetes verzió használata során, miután azt tervezzük, hogy cserélje le ezt a funkciót az Azure Active Directory feltételes hozzáférés új szolgáltatása.  Az új funkció befejeződése után ez a funkció idővel elavulttá válik értesítési idő elteltével.  Ha a jogkivonat élettartama konfigurálható szabályzatot használ, váltson át az új feltételes hozzáférési funkciót, ha érhető el kell készíteni. 

Az Azure AD-ben a csoportházirend-objektum egy szabálykészletet, a kényszerített egyes alkalmazások, illetve a szervezeten belüli összes alkalmazás jelöli. Minden házirendtípus egy egyedi szerkezetűek, és a egy objektumot, amelyhez hozzá van rendelve alkalmazott tulajdonságkészlettel rendelkezik.

Kijelölhet egy házirendet az alapértelmezett házirend a szervezet számára. Bármely alkalmazás számára a szervezeten belül, a szabályzat érvényes mindaddig, amíg az nem bírálják felül egy magasabb prioritású házirend. Emellett is szabályzatot rendel egy adott alkalmazásokhoz. A prioritásuk szerinti sorrendben kell házirend típusa szerint változó.

> [!NOTE]
> Konfigurálható jogkivonat élettartama a házirend nem támogatott a SharePoint online-hoz.  Annak ellenére, hogy a Powershellen keresztül a szabályzat létrehozásának lehetősége van, a SharePoint online-hoz nem visszaigazolja ezt a házirendet. Tekintse meg a [SharePoint online-hoz blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) üresjárati időtúllépés miatt konfigurálásával kapcsolatos további.
>* A SharePoint online-hoz hozzáférési jogkivonatot az alapértelmezett élettartama 1 órára. 
>* Az alapértelmezett maximális inaktív a SharePoint online-ban frissítési jogkivonat ideje 90 nap.

## <a name="token-types"></a>Jogkivonat típusa

A frissítési biztonsági jogkivonat, hozzáférési jogkivonatok, munkamenetek, és azonosító-jogkivonatokat jogkivonat élettartama házirendek állíthatja be.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az ügyfelek hozzáférési jogkivonatok használatával védett erőforrások eléréséhez. Hozzáférési jogkivonat csak olyan felhasználó, az ügyfél és az erőforrás egyedi kombinációja használható. Hozzáférési jogkivonatok nem vonható vissza, és azok lejáratig érvényesek. Egy rosszindulatú aktor, amely a kapott hozzáférési jogkivonat használhatja azt élettartamuk mértékét. Hozzáférési jogkivonat élettartama módosításával között a rendszer a teljesítmény fokozása és növelése az idő, hogy az ügyfél megőrzi-e a hozzáférést, miután a felhasználói fiók le van tiltva. Továbbfejlesztett rendszer teljesítmény akkor érhető el, hogy hányszor egy friss hozzáférési token beszerzéséhez az ügyfélnek kell csökkentésével.  Az alapértelmezett érték 1 óra – 1 óra elteltével az ügyfelet kell használnia a frissítési jogkivonat (általában csendes) egy új frissítési jogkivonatot beszerezni, és a hozzáférési tokent. 

### <a name="refresh-tokens"></a>Frissítési jogkivonatok

Amikor egy ügyfél védett erőforrások eléréséhez hozzáférési jogkivonatot szerez be, az ügyfél is fogad egy frissítési jogkivonatot. A frissítési jogkivonat segítségével a új hozzáférési vagy frissítési jogkivonat párok Ha a jelenlegi hozzáférési jogkivonat lejár. A frissítési jogkivonat felhasználói és az ügyfél kombinációja van kötve. A frissítési jogkivonatok lehet [bármikor visszavont](access-tokens.md#token-revocation), és a jogkivonat érvényességi be van jelölve, minden alkalommal, amikor a jogkivonat.  Frissítési jogkivonatok nem kerülnek visszavonásra, amikor használatával kéri le az új hozzáférési jogkivonatok – ajánlott eljárás, azonban, hogy biztonságosan törölje a régi tokent, egy új beolvasásakor. 

Fontos, hogy bizalmas ügyfelek és a nyilvános ügyfelek közötti különbséget, ez hatással van, hogy mennyi ideig frissítési biztonsági jogkivonat használható. További információ a különböző típusú ügyfelek: [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Bizalmas ügyfél frissítési biztonsági jogkivonat a jogkivonatok élettartama
Bizalmas ügyfelek olyan alkalmazások, amelyek biztonságosan tárolhatja ügyféljelszó (titkos). Bizonyítják, hogy érkeznek-e a biztonságos ügyfélalkalmazástól nem pedig egy rosszindulatú aktor. Például egy webes alkalmazást azért bizalmas ügyfél képes tárolni egy ügyfélkulcsot a webkiszolgálón. Azt nem érhető el. Mivel ezek a folyamatok biztonságosabb, van-e a frissítési biztonsági jogkivonat, ezek a folyamatok tulajdonos alapértelmezett élettartamának `until-revoked`, a csoportházirend használatával nem módosítható és nem vonható önkéntes jelszó kérésekor.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Nyilvános ügyfél frissítési biztonsági jogkivonat a jogkivonatok élettartama

Nyilvános ügyfelek nem biztonságos tárolása ügyféljelszó (titkos). Például egy iOS vagy Android-alkalmazás nem rejtse az erőforrás tulajdonosa, a titkos kulcs, így nyilvános ügyfél minősül. Beállíthatja a házirendek megakadályozza, hogy a régebbi, mint a megadott időszakban nyilvános ügyfelek frissítési biztonsági jogkivonat hozzáférés/frissítési jogkivonat párokat erőforrásokon. (Ehhez használja a frissítési jogkivonat maximális inaktív idő tulajdonság (`MaxInactiveTime`).) Házirendek beállítása az adott időszakban, amelyek a frissítési biztonsági jogkivonat már nem fogad is használja. (Ehhez használja a frissítési jogkivonat maximális életkora tulajdonságot.) Szabályozhatja, mikor és milyen gyakran szükséges-e a felhasználó éppen csendes hitelesíteni, egy nyilvános ügyfélalkalmazás használata helyett a hitelesítő adatok újbóli egy frissítési jogkivonat élettartama módosíthatja.

### <a name="id-tokens"></a>Azonosító jogkivonatok
Azonosító-jogkivonatokat a rendszer átadja a websites és a natív ügyfelek. Azonosító-jogkivonatokat a felhasználói profil adatait tartalmazzák. Egy azonosító jogkivonat felhasználói és az ügyfél egy adott kombinációja van kötve. Azonosító-jogkivonatokat a lejáratig érvényes minősülnek. Általában egy webes alkalmazás megfelel-e a felhasználó a munkamenet élettartamára az alkalmazásban az azonosító jogkivonat élettartama kiadott a felhasználó számára. Beállíthatja, hogy milyen gyakran a webes alkalmazás az alkalmazás munkamenet lejár, és milyen gyakran van szükség a felhasználót, hogy az Azure ad-vel (csendes vagy interaktív) hitelesíthető egy azonosító jogkivonat élettartama.

### <a name="single-sign-on-session-tokens"></a>Egyszeri bejelentkezés munkamenet-jogkivonatok
Amikor egy felhasználó hitelesíti magát az Azure ad-vel, egy egyszeri bejelentkezési (SSO) munkamenet és a felhasználó a böngésző és az Azure ad-ben. Az SSO-jogkivonat, a cookie-k formájában jelöli ehhez a munkamenethez. Vegye figyelembe, hogy az egyszeri bejelentkezés munkamenet-jogkivonat nincs kötve egy adott erőforrás vagy ügyfélalkalmazás. Egyszeri bejelentkezés munkamenet jogkivonatok visszavonhatók, és azok érvényességi be van jelölve, minden alkalommal használni őket.

Az Azure AD egyszeri bejelentkezés munkamenet jogkivonatok kétféle használ: állandó és nem állandó. Állandó munkamenet jogkivonatokat a böngésző maradandó cookie-k formájában tárolja. Nem állandó munkamenetek jogkivonatok munkamenet cookie-k formájában tárolja. (Munkamenet cookie-k elvész, amikor a böngésző be van zárva.) Általában egy nonpersistent munkamenet-azonosító van tárolva. De, amikor a felhasználó kiválasztja az **bejelentkezve szeretnék maradni** egy állandó munkamenet-jogkivonat-hitelesítés során a jelölőnégyzet be van tárolva.

Nem állandó munkamenetek jogkivonatok élettartama pedig 24 óra rendelkezik. Állandó egy élettartama 180 nap lehet. Az egyszeri bejelentkezés munkamenet jogkivonata használja az érvényességi idején belül bármikor a érvényességi időszaka egy másik 24 órás vagy 180 nap, a jogkivonat típusától függően. Ha az egyszeri bejelentkezés munkamenet token nem használatos az érvényességi idején belül, akkor számít a lejárt, és már elfogadták.

Egy házirend használatával adja meg, miután az első munkamenet-azonosító mellett, amely a munkamenet-jogkivonat már nem elfogadható lett kiállítva. (Ehhez használja a munkamenet-jogkivonat Max-Age tulajdonságot.) Szabályozhatja, mikor és milyen gyakran egy felhasználóra szükség, csendes hitelesített, egy webalkalmazás használata helyett a hitelesítő adatok újbóli egy munkamenet-jogkivonat élettartama módosíthatja.

### <a name="token-lifetime-policy-properties"></a>Jogkivonat élettartama házirend tulajdonságai
A jogkivonat élettartama házirend egy csoportházirend-objektum, amely tartalmazza a jogkivonat élettartama szabályok típusú. A házirend tulajdonságainak segítségével szabályozza a megadott jogkivonat élettartama. Ha nincs beállítva szabályzat, a rendszer érvényesíti az alapértelmezett élettartam érték.

### <a name="configurable-token-lifetime-properties"></a>Konfigurálható jogkivonat élettartama tulajdonságai
| Tulajdonság | A házirend tulajdonság karakterlánc | Hatással van | Alapértelmezett | Minimális | Maximum |
| --- | --- | --- | --- | --- | --- |
| Hozzáférési jogkivonat élettartama |AccessTokenLifetime |Hozzáférési jogkivonatok, azonosító-jogkivonatokat, egy SAML2 jogkivonatok |1 óra |10 perc |1 nap |
| Frissítési jogkivonat maximális inaktív időpont |MaxInactiveTime |Frissítési jogkivonatok |90 nap |10 perc |90 nap |
| Egyetlen tényezős frissítési jogkivonat maximális életkora |MaxAgeSingleFactor |Frissítési jogkivonatok (minden olyan felhasználók számára) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Multi-factor Authentication frissítési jogkivonat maximális életkora |MaxAgeMultiFactor |Frissítési jogkivonatok (minden olyan felhasználók számára) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Egyetlen tényezős munkamenet-jogkivonat maximális életkora |MaxAgeSessionSingleFactor<sup>2</sup> |Munkamenet-jogkivonatok (állandó és nem állandó) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Multi-factor Authentication munkamenet-jogkivonat maximális életkora |MaxAgeSessionMultiFactor<sup>3</sup> |Munkamenet-jogkivonatok (állandó és nem állandó) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 nap során az a maximális explicit módon, hogy ezek az attribútumok is beállítható.

### <a name="exceptions"></a>Kivételek
| Tulajdonság | Hatással van | Alapértelmezett |
| --- | --- | --- |
| Frissítési jogkivonat maximális életkora (összevont felhasználók, akik rendelkeznek a megfelelő visszavonási információk kiadva<sup>1</sup>) |Frissítési jogkivonatok (összevont felhasználók, akik rendelkeznek a megfelelő visszavonási információk kiadva<sup>1</sup>) |12 óra |
| Frissítési jogkivonat maximális inaktív időtartama (bizalmas ügyfeleknek kibocsátott) |Frissítési jogkivonatok (bizalmas ügyfeleknek kibocsátott) |90 nap |
| Frissítési jogkivonat maximális életkora (bizalmas ügyfeleknek kibocsátott) |Frissítési jogkivonatok (bizalmas ügyfeleknek kibocsátott) |Until-revoked |

* <sup>1</sup>az összevont felhasználók, akik rendelkeznek a megfelelő visszavonási információk közé tartozik minden olyan felhasználók, akik nem rendelkeznek a "LastPasswordChangeTimestamp" attribútum szinkronizálva. Ezek a felhasználók kapnak e rövid Max-Age, mivel aad-ben nem tudja ellenőrizni, mikor érdemes visszavonni a tokeneket, amelyek egy régi hitelesítő adatok (például a jelszó megváltozott,) vannak társítva, és visszaállítja az egyéb gyakori annak érdekében, hogy a felhasználó és a kapcsolódó jogkivonatok továbbra is ellenőriznie kell a megfelelő választás  állandó. Ez élmény javítása érdekében a bérlői rendszergazdák biztosítania kell, hogy azok is szinkronizálja az "LastPasswordChangeTimestamp" (Ez a user objektum Powershell-lel vagy állítható AADSync keresztül).

### <a name="policy-evaluation-and-prioritization"></a>Szabályzat-kiértékelés és rangsorolási
Hozzon létre, és rendelje hozzá egy jogkivonat élettartama házirendet egy adott alkalmazást, a szervezet és az egyszerű szolgáltatások. Egy adott alkalmazást több szabályzat vonatkozhatnak. A jogkivonat élettartama a házirend érvénybe ezeket a szabályokat követi:

* Egy házirend explicit módon hozzá van rendelve, az egyszerű szolgáltatás, ha van kényszerítve.
* Nincs szabályzat explicit módon hozzá van rendelve a szolgáltatásnevet, ha egy egyszerű szolgáltatás szülő szervezete explicit módon hozzárendelt lép életbe.
* Nincs szabályzat explicit módon van hozzárendelve, az egyszerű szolgáltatás vagy a szervezet számára, ha a szabályzatot az alkalmazáshoz hozzárendelt lép érvénybe.
* Nincs szabályzat van rendelve a szolgáltatásnevet, a szervezet vagy az alkalmazásobjektum, ha az alapértelmezett értékeket lép érvénybe. (Lásd a táblázatot a [konfigurálható jogkivonat élettartama tulajdonságok](#configurable-token-lifetime-properties).)

Alkalmazásobjektumok és egyszerű szolgáltatási objektumok közötti kapcsolat kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](app-objects-and-service-principals.md).

A jogkivonat érvényességi a jogkivonat időben értékeli ki. A szabályzat az alkalmazás, amelyek hozzáférnek a legmagasabb prioritású lép érvénybe.

A C# megfelelően formázott itt használt összes mérföldkövei [TimeSpan](https://msdn.microsoft.com/library/system.timespan) objektum - D.HH:MM:SS.  Így 80 napban és 30 perc lesz `80.00:30:00`.  A vezető, D törölhetők, ha nulla, így 90 perc lesz `00:90:00`.  

> [!NOTE]
> Íme egy példa.
>
> A felhasználó használni szeretne hozzáférni a két webes alkalmazásokhoz: Webalkalmazás és a webes alkalmazás B.
> 
> Tényezők:
> * Mindkét webalkalmazások szülő szervezeten belül találhatók.
> * A munkamenet Token maximális életkora nyolc óra jogkivonat élettartama szabályzat 1 a szülő szervezet alapértelmezettként van beállítva.
> * A webalkalmazás egy normál használható webalkalmazás, és nem csatolt bármilyen házirendek.
> * Webes alkalmazás B szigorúan bizalmas folyamatokat szolgál. Az egyszerű szolgáltatás jogkivonat élettartama szabályzat 2, amely rendelkezik egy munkamenet-Token maximális életkora 30 perc van csatolva.
>
> 12:00 Órakor a felhasználó egy új böngésző-munkamenet indítása és próbál meg hozzáférni a webalkalmazás-t. A felhasználó a rendszer átirányítja az Azure ad-hez, és felkéri, hogy jelentkezzen be. Ez létrehoz egy cookie-t, amely rendelkezik egy munkamenet-jogkivonat a böngészőben. Vissza a webalkalmazás egy egy azonosító jogkivonat, amely lehetővé teszi a felhasználó az alkalmazás eléréséhez a rendszer átirányítja a felhasználót.
>
> A 12:15-kor a felhasználó megpróbál hozzáférni a webes alkalmazás b A böngésző átirányítja az Azure ad-hez, amely észleli a munkamenetcookie-t. Webes alkalmazás B szolgáltatás egyszerű jogkivonat élettartama szabályzat 2 kapcsolódik, de a szülő szervezet alapértelmezett jogkivonat élettartama szabályzat 1 része. Jogkivonat élettartama szabályzat 2 lép érvénybe, mivel az egyszerű szolgáltatások kapcsolódó házirendek, a szervezet alapértelmezett szabályzatok magasabb prioritású. A munkamenet-jogkivonat eredetileg adta ki az elmúlt 30 percen belül, így minősül érvényes. Vissza a webes alkalmazás B-azonosító jogkivonat, amely hozzáférést biztosít a rendszer átirányítja a felhasználót.
>
> Bővít 1:00 Órakor a felhasználó megpróbál hozzáférni a webalkalmazás-t. Az Azure AD a rendszer átirányítja a felhasználót. Webes alkalmazás egy nem csatolt bármilyen házirendek, de mivel a szolgáltatás alapértelmezett jogkivonat élettartama szabályzat 1 rendelkező szervezetekben, adott házirend érvénybe lép. A rendszer észlelte, hogy a legutóbbi nyolc órán belül kiadástól munkamenetcookie-t. Vissza a webalkalmazás egy egy új azonosító jogkivonat csendes rendszer átirányítja a felhasználót. A felhasználónak nem kell hitelesíteni.
>
> Ezt követően azonnal a felhasználó megpróbál hozzáférni a webes alkalmazás B. Az Azure AD a rendszer átirányítja a felhasználót. Mivel, mielőtt jogkivonat élettartama szabályzat 2 lép érvénybe. A token bocsátotta a több mint 30 perccel ezelőtt történt, mert a rendszer kéri a felhasználót, hogy adja meg újból a bejelentkezési hitelesítő adataikat. Egy vadonatúj munkamenet-jogkivonat és azonosító jogkivonat adják ki. A felhasználó ezután hozzáférhetnek a webes alkalmazás B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurálható szabályzatot tulajdonságainak részleteit
### <a name="access-token-lifetime"></a>Hozzáférési jogkivonat élettartama
**Karakterlánc:** AccessTokenLifetime

**Van hatással:** Hozzáférési jogkivonatok, azonosító-jogkivonatokat

**Összefoglalás:** Ez az irányelv szabályozza, hogy mennyi ideig hozzáférési és azonosító-jogkivonatokat ehhez az erőforráshoz tekintendők érvényes. A hozzáférési jogkivonat élettartama tulajdonság csökkentése csökkenti egy hozzáférési jogkivonatot, vagy hosszabb ideig rosszindulatú aktor által használt azonosító jogkivonat kockázatát. (Ezek a jogkivonatok nem vonható vissza.) Kompromisszumot kötni a, hogy a teljesítményt hátrányosan érinti, mivel a jogkivonatok gyakrabban kell cserélni.

### <a name="refresh-token-max-inactive-time"></a>Frissítési jogkivonat maximális inaktív időpont
**Karakterlánc:** MaxInactiveTime

**Van hatással:** Frissítési jogkivonatok

**Összefoglalás:** Ez az irányelv szabályozza, hogy hogyan régi a frissítési jogkivonatok lehet, mielőtt egy ügyfél már nem használható, hozzáférési és frissítési jogkivonat párokat beolvasni az erőforrás elérésére tett kísérlet közben. Egy új frissítési jogkivonatot általában adja vissza a frissítési jogkivonatok használata, mert ez a szabályzat megakadályozza, hogy a hozzáférést, ha az ügyfél megpróbál a megadott időtartam során a jelenlegi frissítési jogkivonat használatával bármely erőforrás eléréséhez szükséges.

Ez a szabályzat arra kényszeríti a felhasználók, akik nem használtak az ügyfél hitelesítse magát újra egy új frissítési jogkivonatot beolvasni.

A frissítési jogkivonat maximális inaktív idő tulajdonság, mint az egyetlen tényezős Token maximális életkora és a multi-factor Authentication Token maximális frissítése életkora tulajdonságok alacsonyabb értékűre kell állítani.

### <a name="single-factor-refresh-token-max-age"></a>Egyetlen tényezős frissítési jogkivonat maximális életkora
**Karakterlánc:** MaxAgeSingleFactor

**Van hatással:** Frissítési jogkivonatok

**Összefoglalás:** A szabályzat vezérlőelemek mennyi a felhasználók is használhatják a frissítési jogkivonatok hozzáférés/frissítési jogkivonat párokat azok utolsó hitelesítése után sikeresen csak egyetlen tényező használatával. Miután a felhasználó végzi a hitelesítést, és a egy új frissítési jogkivonatot kap, a felhasználó használhatja a frissítési jogkivonat folyamat a megadott időtartam elteltéig. (Ez igaz, amíg a jelenlegi frissítési jogkivonat nem vonták vissza és nem hagyják a tétlenség ideje hosszabb fel nem használt.) Ezen a ponton a felhasználónak kötelező hitelesítse magát újra, megjelenik egy új frissítési jogkivonatot.

A felhasználók is végezhetnek gyakrabban csökkenti a maximálisidőtartam kényszeríti. Mivel számít, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy ezzel a tulajdonsággal, amely kisebb vagy egyenlő, mint a multi-factor Authentication Token maximális frissítése életkora tulajdonság értékre.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-factor Authentication frissítési jogkivonat maximális életkora
**Karakterlánc:** MaxAgeMultiFactor

**Van hatással:** Frissítési jogkivonatok

**Összefoglalás:** A házirend vezérlőelemek mennyi a felhasználók is használhatják a frissítési jogkivonatok hozzáférés/frissítési jogkivonat párokat azok utolsó hitelesítése után sikeresen számos tényező használatával. Miután a felhasználó végzi a hitelesítést, és a egy új frissítési jogkivonatot kap, a felhasználó használhatja a frissítési jogkivonat folyamat a megadott időtartam elteltéig. (Ez igaz, amíg a jelenlegi frissítési jogkivonat nincs-e visszavonva, és nem a tétlenség ideje hosszabb fel nem használt.) Ezen a ponton felhasználók kényszerítve hitelesítse magát újra, megjelenik egy új frissítési jogkivonatot.

A felhasználók is végezhetnek gyakrabban csökkenti a maximálisidőtartam kényszeríti. Mivel számít, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy ezzel a tulajdonsággal, amely egyenlő vagy nagyobb, mint az egyetlen tényezős Token maximális frissítése életkora tulajdonság értékre.

### <a name="single-factor-session-token-max-age"></a>Egyetlen tényezős munkamenet-jogkivonat maximális életkora
**Karakterlánc:** MaxAgeSessionSingleFactor

**Van hatással:** Munkamenet-jogkivonatok (állandó és nem állandó)

**Összefoglalás:** A házirend vezérlőelemek mennyi a felhasználó használhatja egy munkamenet-jogkivonat beszerzése az új azonosító és tokenu relace, utolsó hitelesítése után sikeresen csak egyetlen tényező használatával. Miután egy felhasználó hitelesíti magát, és egy új munkamenet-jogkivonat kap, a felhasználó használhatja a munkamenet-jogkivonat folyamat a megadott időszakra vonatkozóan. (Ez igaz, ha a jelenlegi munkamenet-jogkivonat nem vonták vissza és nem járt le.) A megadott időszak után a felhasználónak kötelező hitelesítse magát újra egy új munkamenet-jogkivonat fogadásához.

A felhasználók is végezhetnek gyakrabban csökkenti a maximálisidőtartam kényszeríti. Mert számít, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy ezzel a tulajdonsággal, amely egyenlő vagy annál kisebb, mint a multi-factor Authentication munkamenet Token maximális életkora tulajdonság értékre.

### <a name="multi-factor-session-token-max-age"></a>Multi-factor Authentication munkamenet-jogkivonat maximális életkora
**Karakterlánc:** MaxAgeSessionMultiFactor

**Van hatással:** Munkamenet-jogkivonatok (állandó és nem állandó)

**Összefoglalás:** A szabályzatok vezérlőit mennyi egy felhasználó egy munkamenet-jogkivonat használatával egy új azonosító és a munkamenet jogkivonat beszerzése az utolsó időpont után csak a hitelesítést sikeresen számos tényező használatával. Miután egy felhasználó hitelesíti magát, és egy új munkamenet-jogkivonat kap, a felhasználó használhatja a munkamenet-jogkivonat folyamat a megadott időszakra vonatkozóan. (Ez igaz, ha a jelenlegi munkamenet-jogkivonat nem vonták vissza és nem járt le.) A megadott időszak után a felhasználónak kötelező hitelesítse magát újra egy új munkamenet-jogkivonat fogadásához.

A felhasználók is végezhetnek gyakrabban csökkenti a maximálisidőtartam kényszeríti. Mivel számít, hogy kevésbé biztonságos, mint a multi-factor authentication hitelesítést, azt javasoljuk, hogy ezzel a tulajdonsággal, amely egyenlő vagy nagyobb, mint az egyetlen tényezős munkamenet Token maximális életkora tulajdonság értékre.

## <a name="example-token-lifetime-policies"></a>A házirendek például jogkivonat élettartama
Számos forgatókönyvek is előfordulhatnak, amikor létre és kezelheti a jogkivonatok élettartamának alkalmazások, az egyszerű szolgáltatások és a teljes szervezet Azure AD-ben. Ez a szakasz ismerteti azokat a lépéseket néhány gyakori házirend alkalmazási forgatókönyveket, amelyek segítségével új szabályokat ír elő:

* Jogkivonat élettartama
* Token maximális várakozási időtartam
* Token maximális életkora

A példákban is megismerheti, hogyan lehet:

* A szervezet alapértelmezett házirend kezelése
* Webes bejelentkezés szabályzat létrehozása
* Egy natív alkalmazást, amely meghívja a webes API-k szabályzat létrehozása
* Egy speciális házirend kezelése

### <a name="prerequisites"></a>Előfeltételek
A következő példákban, létrehozása, frissítése, hivatkozás és törli a szabályzatok, alkalmazások, az egyszerű szolgáltatásnevekről és a teljes szervezet számára. Ha most ismerkedik az Azure ad-ben, azt javasoljuk, hogy ismerkedjen [Azure AD-bérlő beszerzése](quickstart-create-new-tenant.md) ezekben a példákban a folytatás előtt.  

A kezdéshez kövesse az alábbi lépéseket:

1. Töltse le a legújabb [az Azure AD PowerShell modul nyilvános előzetes kiadás](https://www.powershellgallery.com/packages/AzureADPreview).
2. Futtassa a `Connect` paranccsal jelentkezzen be az Azure AD rendszergazdai fiókot. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenet indításához.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. A szervezetben létrehozott összes szabályzat megtekintéséhez futtassa a következő parancsot. Ezzel a paranccsal futtassa a következő esetekben legtöbb művelet után. A következő parancs futtatásával is segít a ** ** szabályzatot.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Példa: A szervezet alapértelmezett házirend kezelése
Ebben a példában létrehozott egy szabályzatot, amely lehetővé teszi, hogy a felhasználók kevésbé gyakran jelentkezzen be a teljes szervezet számára. Ehhez hozzon létre egy jogkivonat élettartama házirendet egyetlen tényezős frissítési jogkivonatokat, a szervezetben alkalmazott. A szabályzat minden alkalmazásához a szervezetben, és minden egyszerű szolgáltatás, amely még nem rendelkezik beállított házirend érvényes.

1. Hozzon létre egy jogkivonat élettartama szabályzatot.

    1.  Beállítása a többtényezős egyetlen frissítési jogkivonat az "until-visszavonva." A jogkivonat le nem jár, amíg hozzáférést visszavonták. A következő szabályzatdefiníció létrehozása:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  A szabályzat létrehozásához futtassa a következő parancsot:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Az új házirend megtekintéséhez, és hogy lekérje a házirendet **ObjectId**, futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy
        ```

2. A szabályzat frissítése.

    Dönthet úgy, hogy az első, ebben a példában a beállított szabályzat ne legyen olyan szigorúak, mint a szolgáltatásnak szüksége van. Ha szeretné beállítani az egyetlen tényezős frissítési jogkivonat két nap múlva lejár, futtassa a következő parancsot:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Példa: Webes bejelentkezés szabályzat létrehozása

Ebben a példában létrehozott egy szabályzatot, amely megköveteli a felhasználóktól a webalkalmazásokban gyakran hitelesítéséhez. Ez a szabályzat a hozzáférés és az azonosító jogkivonatok élettartama és a egy multi-factor Authentication munkamenet-jogkivonat maximálisidőtartam állítja be az egyszerű szolgáltatás a webalkalmazás.

1. Hozzon létre egy jogkivonat élettartama szabályzatot.

    Ezt a házirendet, a webes jelentkezzen be, beállítja a hozzáférési és azonosító jogkivonat élettartama, és a maximális egyetlen tényezős munkamenet jogkivonat élettartama, két óra.

    1.  A szabályzat létrehozásához a következő parancs futtatásával:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy lekérje a házirendet **ObjectId**, futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy
        ```

2.  A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell beszereznie a **ObjectId** szolgáltatásneve. 

    1.  A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti, vagy a [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) vagy a [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ezenkívül tesztelheti ezt a a [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/), és a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) az Azure AD-fiók használatával.

    2.  Ha rendelkezik a **ObjectId** szolgáltatásneve, futtassa a következő parancsot:

        ```powershell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Példa: Egy natív alkalmazást, amely meghívja a webes API-k szabályzat létrehozása
Ebben a példában létrehozhat egy szabályzatot, amely a felhasználók számára a kevésbé gyakran hitelesítést igényel. A szabályzat is hosszabb lesz a idő a felhasználó lehet inaktív, mielőtt a felhasználó ismét hitelesítenie kell magát. A webes API a szabályzat érvényes. Amikor a natív alkalmazás a webes API-erőforrásként, a házirend érvényben van.

1. Hozzon létre egy jogkivonat élettartama szabályzatot.

    1.  Webes API-hoz a szigorú házirend létrehozásához futtassa a következő parancsot:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy lekérje a házirendet **ObjectId**, futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy
        ```

2. A szabályzat hozzárendelése a webes API-hoz. Is kell beszereznie a **ObjectId** az alkalmazás. A legjobb módszer az alkalmazás **ObjectId** használata a [az Azure portal](https://portal.azure.com/).

   Ha rendelkezik a **ObjectId** az alkalmazás a következő parancsot:

        ```powershell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Példa: Egy speciális házirend kezelése
Ebben a példában létrehoz néhány házirendeket, ismerje meg a prioritást rendszer működését. Még több több objektumot alkalmazott házirendek kezelése tudhat meg.

1. Hozzon létre egy jogkivonat élettartama szabályzatot.

    1.  Hozzon létre egy szervezet alapértelmezett szabályzatot, amely az egyetlen tényezős frissítési jogkivonat élettartama 30 napra, futtassa a következő parancsot:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Az új házirend megtekintéséhez, és hogy lekérje a házirendet **ObjectId**, futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy
        ```

2. A szabályzat hozzárendelése egy egyszerű szolgáltatást.

    Most hogy egy szabályzatot, amely a teljes szervezetre vonatkozik. Előfordulhat, hogy szeretné megőrizni a 30 napos szabályzat egy adott szolgáltatás egyszerű, de módosítsa a szervezet alapértelmezett házirendet a felső korlátja "until-visszavonva."

    1.  A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti, vagy a [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) vagy a [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ezenkívül tesztelheti ezt a a [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/), és a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) az Azure AD-fiók használatával.

    2.  Ha rendelkezik a **ObjectId** szolgáltatásneve, futtassa a következő parancsot:

            ```powershell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Állítsa be a `IsOrganizationDefault` jelző false értékre:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Hozzon létre egy új szervezeti alapértelmezett házirend:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már a szolgáltatásnévhez társított az eredeti házirenddel rendelkezik, és az új szabályzat a szervezet alapértelmezett házirend van beállítva. Fontos megjegyezni, hogy a szolgáltatásnevek alkalmazott házirendek elsőbbséget élveznek a szervezet alapértelmezett házirendeket.

## <a name="cmdlet-reference"></a>A parancsmagok leírása

### <a name="manage-policies"></a>Szabályzatok kezelése

A következő parancsmagok segítségével kezelheti a szabályzatokat.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Létrehoz egy új szabályzatot.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Definition</code> |A szabályzat minden olyan szabályokat tartalmaz sztringesített JSON tömbje. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Karakterlánc, a házirend nevét. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Igaz értéke esetén a szabályzat beállítása a szervezet alapértelmezett szabályzat. Ha nem, nem csinál semmit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Szabályzat típusát. A jogkivonatok élettartamának mindig használja az "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcionális] |A szabályzat egy másik Azonosítót állít be. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Minden Azure AD-házirendek vagy a megadott házirend beolvasása.

```powershell
Get-AzureADPolicy
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcionális] |**(Azonosító) ObjectId** , a kívánt házirendet. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Lekéri az összes alkalmazás és egyszerű szolgáltatásokat helyezi, amely egy házirend van csatolva.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** , a kívánt házirendet. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Frissíti egy meglévő szabályzatot.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** , a kívánt házirendet. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Karakterlánc, a házirend nevét. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcionális] |A szabályzat minden olyan szabályokat tartalmaz sztringesített JSON tömbje. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcionális] |Igaz értéke esetén a szabályzat beállítása a szervezet alapértelmezett szabályzat. Ha nem, nem csinál semmit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcionális] |Szabályzat típusát. A jogkivonatok élettartamának mindig használja az "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcionális] |A szabályzat egy másik Azonosítót állít be. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
A megadott házirend törlése.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** , a kívánt házirendet. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Alkalmazás-házirendek
A következő parancsmagokat használhatja az alkalmazás-házirendek.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
A megadott házirend alkalmazáshoz való hivatkozásokat.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** a szabályzat. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Lekéri a szabályzatot, amely egy alkalmazáshoz van hozzárendelve.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Egy házirend távolít el egy alkalmazást.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** a szabályzat. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Szolgáltatásnév-házirendek
Szolgáltatásnév-házirendek a következő parancsmagokat is használhat.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
A megadott házirend csatol egy egyszerű szolgáltatást.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** a szabályzat. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Lekérdezi a megadott egyszerű szolgáltatásnév kapcsolódó házirendek.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
A megadott egyszerű szolgáltatás eltávolítja a házirendet.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |**(Azonosító) ObjectId** az alkalmazás. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** a szabályzat. | `-PolicyId <ObjectId of Policy>` |
