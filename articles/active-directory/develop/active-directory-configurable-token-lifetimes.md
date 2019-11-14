---
title: Konfigurálható jogkivonat élettartama Azure Active Directory
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be az Azure AD által kiállított jogkivonatok élettartamát.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: ryanwi
ms.custom: aaddev, annaba, identityplatformtop40
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021d0c19ecc4bf63861bf95d99b6ba6b8e910220
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046555"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurálható jogkivonat élettartama Azure Active Directory (előzetes verzió)

Megadhatja Azure Active Directory (Azure AD) által kiállított jogkivonatok élettartamát. A szervezeten belüli összes alkalmazáshoz, több-bérlős (többszervezetes) alkalmazáshoz vagy a szervezet egy adott egyszerű szolgáltatásához is beállíthat jogkivonat-élettartamot.

> [!IMPORTANT]
> Az előzetes verzióban az ügyfelek meghallgatása után az Azure AD feltételes hozzáférés szolgáltatásban implementálta a [hitelesítési munkamenet-kezelési képességeket](https://go.microsoft.com/fwlink/?linkid=2083106) . Ezt az új funkciót használhatja a frissítési jogkivonat élettartamának konfigurálásához a bejelentkezési gyakoriság beállításával. 2020. május 1-től nem fogja tudni konfigurálni a konfigurálható jogkivonat-élettartam-szabályzatot a munkamenet-és frissítési tokenek konfigurálásához. A hozzáférési token élettartamát továbbra is beállíthatja az elavulás után.

Az Azure AD-ben a házirend-objektum az egyes alkalmazásokra vagy a szervezet összes alkalmazására kikényszerített szabályok halmazát jelöli. Minden egyes házirend-típushoz egyedi struktúra tartozik, amely a hozzájuk rendelt objektumokra érvényes tulajdonságokkal rendelkezik.

Megadhat egy házirendet a szervezet alapértelmezett szabályzatának megfelelően. A szabályzatot a rendszer a szervezet bármely alkalmazására alkalmazza, amennyiben azt nem bírálja felül magasabb prioritású házirend. Egy szabályzatot adott alkalmazásokhoz is hozzárendelhet. A prioritás sorrendje a házirend típusa szerint változhat.

> [!NOTE]
> A konfigurálható jogkivonat élettartama házirend nem támogatott a SharePoint Online-ban.  Bár a szabályzat a PowerShell használatával is létrehozható, a SharePoint Online nem fogja nyugtázni a szabályzatot. Ha többet szeretne megtudni az üresjárati munkamenetek időtúllépésének konfigurálásáról, tekintse meg a [SharePoint Online blogját](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) .
>* A SharePoint Online hozzáférési token alapértelmezett élettartama 1 óra. 
>* A SharePoint Online frissítési token alapértelmezett maximális inaktív ideje 90 nap.


## <a name="token-types"></a>Token-típusok

Megadhatja a jogkivonat élettartamára vonatkozó szabályzatokat a frissítési tokenekhez, a hozzáférési jogkivonatokhoz, az SAML-tokenekhez, a munkamenet-jogkivonatokhoz és az azonosító jogkivonatokhoz.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az ügyfelek hozzáférési jogkivonatokkal férnek hozzá egy védett erőforráshoz. Hozzáférési jogkivonat csak a felhasználó, az ügyfél és az erőforrás adott kombinációjára használható. A hozzáférési jogkivonatok nem vonhatók vissza, és a lejárat előtt érvényesek. Egy rosszindulatú színész, amely hozzáférési tokent kapott, az élettartama mértékének megfelelően használhatja. A hozzáférési jogkivonat élettartamának módosítása a rendszer teljesítményének javítása és a felhasználó fiókjának letiltása után az ügyfél által megőrzött idő növelése közötti kompromisszum. A rendszer jobb teljesítményét úgy érheti el, hogy csökkenti az ügyfelek által a friss hozzáférési token beszerzéséhez szükséges számú időt.  Az alapértelmezett érték 1 óra – 1 óra elteltével az ügyfélnek a frissítési tokent kell használnia (általában csendesen) új frissítési jogkivonat és hozzáférési jogkivonat beszerzése. 

### <a name="saml-tokens"></a>SAML-jogkivonatok

Az SAML-jogkivonatokat számos web-alapú SAAS-alkalmazás használja, és a Azure Active Directory egy SAML2 protokoll-végpontján keresztül szerezhetők be. Ezeket a WS-Federationt használó alkalmazások is használják. A token alapértelmezett élettartama 1 óra. Egy alkalmazás szemszögéből a jogkivonat érvényességi idejét a jogkivonat `<conditions …>` elemének NotOnOrAfter értéke adja meg. A jogkivonat érvényességi időtartamának lejárta után az ügyfélnek új hitelesítési kérelmet kell kezdeményeznie, amely az egyszeri bejelentkezés (SSO) munkamenet-jogkivonatának eredményeképpen gyakran elégedett lesz az interaktív bejelentkezés nélkül.

A NotOnOrAfter értéke módosítható a `TokenLifetimePolicy``AccessTokenLifetime` paraméterének használatával. A házirendben megadott élettartamra lesz beállítva, ha van ilyen, valamint egy óra, amely öt percet vesz igénybe.

Vegye figyelembe, hogy a `<SubjectConfirmationData>` elemben megadott tulajdonos megerősítő NotOnOrAfter nem érinti a jogkivonat élettartamának konfigurációja. 

### <a name="refresh-tokens"></a>Tokenek frissítése

Amikor egy ügyfél hozzáférési jogkivonatot kap egy védett erőforrás eléréséhez, az ügyfél frissítési jogkivonatot is kap. A frissítési jogkivonat az új hozzáférési/frissítési jogkivonat-párok beszerzésére szolgál, ha az aktuális hozzáférési jogkivonat lejár. A frissítési jogkivonat a felhasználó és az ügyfél kombinációjával van kötve. A frissítési tokenek bármikor [visszavonhatók](access-tokens.md#token-revocation), és a jogkivonat érvényességét a rendszer minden alkalommal ellenőrzi, amikor a jogkivonat használatban van.  A frissítési tokenek nem vonhatók vissza, amikor új hozzáférési jogkivonatok beolvasására használják – ez a legjobb megoldás, ha azonban a régi jogkivonatot biztonságosan törli, amikor egy újat kap. 

Fontos, hogy különbséget tegyen a bizalmas ügyfelek és a nyilvános ügyfelek között, mivel ez azt befolyásolja, hogy mennyi ideig használhatók a frissítési tokenek. További információ a különböző típusú ügyfelekről: [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Jogkivonat-élettartamok bizalmas ügyfél-frissítési jogkivonatokkal
A bizalmas ügyfelek olyan alkalmazások, amelyek biztonságos módon tárolhatják az ügyfél jelszavát (titkos kulcs). Bizonyítani tudják, hogy a kérések a biztonságos ügyfélalkalmazás felől érkeznek, nem rosszindulatú színésztől. Egy webalkalmazás például egy bizalmas ügyfél, mert a webkiszolgálón tárolhatja az ügyfél titkos kulcsát. Nincs kitéve. Mivel ezek a folyamatok biztonságosabbak, az ezekre a folyamatokra kiállított frissítési tokenek alapértelmezett élettartama `until-revoked`, nem módosítható a szabályzat használatával, és nem vonható vissza az önkéntes jelszó-visszaállítások.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokenek élettartama nyilvános ügyfél-frissítési jogkivonatokkal

A nyilvános ügyfelek nem tudják biztonságosan tárolni az ügyfél jelszavát (titkos kulcs). Egy iOS-/Android-alkalmazás például nem tud titkos kulcsot kialakítani az erőforrás-tulajdonostól, ezért nyilvános ügyfélnek számít. Az erőforrásokra vonatkozó szabályzatok megadásával megakadályozhatja, hogy a frissítési tokenek a megadott időszaknál régebbi nyilvános ügyfelekről szerezzenek új hozzáférési vagy frissítési jogkivonat-párokat. (Ehhez használja a refresh token Max inaktív idő tulajdonságát (`MaxInactiveTime`).) Házirendeket is használhat egy olyan időszak megadására, amelyen túl a frissítési tokenek már nem lesznek elfogadva. (Ehhez használja a refresh token Max Age tulajdonságát.) A frissítési token élettartama beállítható annak szabályozására, hogy a felhasználó mikor és milyen gyakran írja elő a hitelesítő adatok újbóli hitelesítését, ahelyett, hogy a rendszer egy nyilvános ügyfélalkalmazás használatával visszaadja a hitelesítő adatokat.

> [!NOTE]
> A Max Age tulajdonság azt az időtartamot használja, ameddig egyetlen jogkivonat használható. 

### <a name="id-tokens"></a>Azonosító jogkivonatok
Az azonosító jogkivonatok átadása a webhelyeknek és a natív ügyfeleknek. Az azonosító jogkivonatok egy felhasználó profiljára vonatkozó adatokat tartalmaznak. Az azonosító jogkivonat a felhasználó és az ügyfél adott kombinációjára van kötve. Az azonosító jogkivonatok érvényessége csak a lejárat után tekinthető érvényesnek. A webalkalmazások általában a felhasználó munkamenetének élettartamát tükrözik az alkalmazásban a felhasználó számára kiállított azonosító jogkivonat élettartama alapján. Az azonosító token élettartama beállítható annak szabályozására, hogy a webalkalmazás milyen gyakran járjon le az alkalmazás-munkamenetben, és hogy milyen gyakran szükséges, hogy a felhasználó újra hitelesítve legyen az Azure AD-vel (csendes vagy interaktív módon).

### <a name="single-sign-on-session-tokens"></a>Egyszeri bejelentkezés munkamenet-jogkivonatai
Amikor egy felhasználó az Azure AD-vel végzi a hitelesítést, az egyszeri bejelentkezési munkamenet (SSO) a felhasználó böngészőjével és az Azure AD-vel lesz létrehozva. A cookie-t tartalmazó SSO-jogkivonat ezt a munkamenetet jelöli. Az SSO-munkamenet tokenje nem kötődik egy adott erőforráshoz/ügyfélalkalmazás-alkalmazáshoz. Az egyszeri bejelentkezéses munkamenet-tokenek visszavonhatók, és az érvényességük minden használatkor be van jelölve.

Az Azure AD kétféle egyszeri bejelentkezéses munkamenet-jogkivonatot használ: állandó és nem állandó. Az állandó munkamenet-tokeneket a böngésző állandó cookie-ként tárolja. A nem állandó munkamenet-tokenek munkamenet-cookie-ként vannak tárolva. (A munkamenet-cookie-k megsemmisülnek a böngésző bezárásakor.) Általában nem állandó munkamenet-token van tárolva. Ha azonban a **felhasználó a bejelentkezéskor jelölőnégyzet** bejelölését választja, a rendszer állandó munkamenet-tokent tárol.

A nem állandó munkamenet-tokenek élettartama 24 óra. Az állandó tokenek élettartama 180 nap. Az egyszeri bejelentkezési munkamenet tokenjét az érvényességi időtartamon belül kell használni, az érvényességi időtartam a jogkivonat típusától függően egy másik 24 órán vagy 180 nap múlva meghosszabbodik. Ha a rendszer nem használja az egyszeri bejelentkezési munkamenet tokenjét az érvényességi időtartamon belül, akkor a rendszer lejártnak tekinti, és már nem fogadja el.

A szabályzattal megadhatja azt az időpontot, ameddig az első munkamenet-jogkivonat ki lett állítva, amely után a munkamenet-jogkivonat már nem fogadható el. (Ehhez használja a munkamenet-jogkivonat Max Age tulajdonságát.) A munkamenet-token élettartamát beállíthatja annak szabályozására, hogy a rendszer mikor és milyen gyakran írja elő a felhasználótól a hitelesítő adatok újbóli megadását a nem hitelesített hitelesítés helyett, ha webalkalmazást használ.

### <a name="token-lifetime-policy-properties"></a>Jogkivonat élettartama házirend tulajdonságai
A jogkivonat élettartama házirend olyan házirend-objektum, amely a jogkivonat élettartamának szabályait tartalmazza. A szabályzat tulajdonságai segítségével szabályozhatja a megadott jogkivonat-élettartamot. Ha nincs beállítva házirend, a rendszer kényszeríti az alapértelmezett élettartam értékét.

### <a name="configurable-token-lifetime-properties"></a>Konfigurálható jogkivonat élettartamának tulajdonságai
| Tulajdonság | Házirend tulajdonságának karakterlánca | Befolyásolja | Alapértelmezett | Minimális | Maximum |
| --- | --- | --- | --- | --- | --- |
| Hozzáférési jogkivonat élettartama |<sup>2</sup> . AccessTokenLifetime |Hozzáférési tokenek, azonosító tokenek, egy SAML2 tokenek |1 óra |10 perc |1 nap |
| Frissítési jogkivonat maximális inaktív ideje |MaxInactiveTime |Tokenek frissítése |90 nap |10 perc |90 nap |
| Egy tényező frissítési Tokenének maximális kora |MaxAgeSingleFactor |Tokenek frissítése (bármely felhasználó esetében) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Multi-Factor refresh token Max Age |MaxAgeMultiFactor |Tokenek frissítése (bármely felhasználó esetében) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Egy tényezős munkamenet-token maximális kora |MaxAgeSessionSingleFactor |Munkamenet-tokenek (állandó és nem állandó) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |
| Többtényezős munkamenet-token maximális kora |MaxAgeSessionMultiFactor |Munkamenet-tokenek (állandó és nem állandó) |Until-revoked |10 perc |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 nappal az attribútumok maximális explicit hosszúsága adható meg.
* <sup>2</sup> A Microsoft Teams Web Client működésének biztosítása érdekében ajánlott a AccessTokenLifetime 15 percnél hosszabb ideig megőrizni a Microsoft Teams szolgáltatásban.

### <a name="exceptions"></a>Kivételek
| Tulajdonság | Befolyásolja | Alapértelmezett |
| --- | --- | --- |
| Frissítési jogkivonat maximális kora (a nem elegendő visszavonási<sup>információval</sup>rendelkező összevont felhasználók számára kiállított) |Frissítési tokenek (olyan összevont felhasználók számára, akik nem rendelkeznek elegendő visszavonási információval<sup>1</sup>) |12 óra |
| Frissítési jogkivonat maximális inaktív ideje (bizalmas ügyfelek számára kiállítva) |Frissítési tokenek (bizalmas ügyfelek számára kiállítva) |90 nap |
| Frissítési token maximális kora (bizalmas ügyfelek számára kiállítva) |Frissítési tokenek (bizalmas ügyfelek számára kiállítva) |Until-revoked |

* <sup>1</sup> Azok az összevont felhasználók, akik nem rendelkeznek elegendő visszavonási információval, minden olyan felhasználóhoz tartoznak, akik nem rendelkeznek szinkronizált "LastPasswordChangeTimestamp" attribútummal. Ezek a felhasználók ezt a rövid maximális kort kapják meg, mert a HRE nem tudja ellenőrizni, hogy mikor kell visszavonni a régi hitelesítő adatokhoz kötődő jogkivonatokat (például a jelszót, amely megváltozott), és gyakrabban kell visszanéznie, hogy a felhasználó és a társított jogkivonatok továbbra is megfelelőek legyenek.  állandó. A környezet javítása érdekében a bérlői rendszergazdáknak biztosítaniuk kell, hogy szinkronizálják a "LastPasswordChangeTimestamp" attribútumot (ezt a felhasználói objektumhoz a PowerShell vagy a AADSync használatával lehet beállítani).

### <a name="policy-evaluation-and-prioritization"></a>Szabályzat kiértékelése és rangsorolása
Létrehozhat és hozzárendelhet egy jogkivonat-élettartam-szabályzatot egy adott alkalmazáshoz, a szervezetéhez és az egyszerű szolgáltatásokhoz. Egy adott alkalmazásra több házirend is alkalmazható. A jogkivonat élettartamára vonatkozó szabályzat a következő szabályokat követi:

* Ha a szabályzatot explicit módon rendeli hozzá a szolgáltatáshoz, azt a rendszer kényszeríti.
* Ha nincs kifejezetten hozzárendelve házirend az egyszerű szolgáltatáshoz, a rendszer kikényszeríti az egyszerű szolgáltatásnév fölérendelt szervezetéhez hozzárendelt szabályzatot.
* Ha a szolgáltatáshoz vagy a szervezethez explicit módon nem rendel hozzá szabályzatot, a rendszer kikényszeríti az alkalmazáshoz rendelt szabályzatot.
* Ha nem rendelt hozzá szabályzatot a szolgáltatáshoz, a szervezethez vagy az alkalmazás-objektumhoz, a rendszer az alapértelmezett értékeket kényszeríti ki. (Lásd a következő táblázatot a [konfigurálható jogkivonat élettartamának tulajdonságaiban](#configurable-token-lifetime-properties).)

További információ az alkalmazásobjektumok és a szolgáltatás-objektumok közötti kapcsolatról: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](app-objects-and-service-principals.md).

A jogkivonat érvényességét a rendszer a jogkivonat használatának időpontjában értékeli ki. Az elérni kívánt alkalmazás legmagasabb prioritású szabályzata érvénybe lép.

Az itt használt összes időtávok a C# [TimeSpan](/dotnet/api/system.timespan) objektum – D. HH: PP: mm.  Tehát 80 nap és 30 perc `80.00:30:00`.  A vezető D-t nullára lehet dobni, így 90 perc `00:90:00`.  

> [!NOTE]
> Példa erre a forgatókönyvre.
>
> A felhasználók két webalkalmazáshoz szeretnének hozzáférni: webalkalmazás A és A (z) B webalkalmazás.
> 
> Tényezők
> * Mindkét webalkalmazás ugyanahhoz a szülő szervezethez tartozik.
> * A jogkivonat élettartamának szabályzata 1. a munkamenet tokenje legfeljebb nyolc óra, a szülő szervezet alapértelmezett értékeként van beállítva.
> * Az A webalkalmazás egy normál használatú webalkalmazás, és nincs hozzárendelve szabályzatokhoz.
> * A "B" webalkalmazás a szigorúan bizalmas folyamatokhoz használatos. Az egyszerű szolgáltatás a jogkivonat-élettartamra vonatkozó szabályzat 2., amelynek a munkamenet-tokenje legfeljebb 30 percet vesz igénybe.
>
> A felhasználó 12:00 ÓRAKOR elindítja az új böngésző-munkamenetet, és megpróbál hozzáférni A webalkalmazáshoz. A rendszer átirányítja a felhasználót az Azure AD-be, és megkéri, hogy jelentkezzen be. Ez létrehoz egy cookie-t, amely egy munkamenet-tokent tartalmaz a böngészőben. A rendszer visszairányítja a felhasználót az A webalkalmazásba egy azonosító jogkivonattal, amely lehetővé teszi, hogy a felhasználó hozzáférjen az alkalmazáshoz.
>
> A felhasználó 12:15 ÓRAKOR megpróbál hozzáférni a (z) B webalkalmazáshoz. A böngésző átirányítja az Azure AD-be, amely észleli a munkamenet cookie-t. A webalkalmazás B szolgáltatásának egyszerű szolgáltatása a jogkivonat élettartama (2), de a szülő szervezet része is, és az alapértelmezett jogkivonat élettartama házirend 1. A jogkivonat élettartamára vonatkozó házirend 2 érvénybe lép, mert az egyszerű szolgáltatásokhoz kapcsolódó házirendek magasabb prioritással rendelkeznek, mint a szervezet alapértelmezett házirendjei. A munkamenet jogkivonatát eredetileg az elmúlt 30 percben adták ki, ezért érvényesnek számít. A rendszer visszairányítja a felhasználót a (B) webalkalmazásba egy olyan azonosító jogkivonattal, amely hozzáférést biztosít számukra.
>
> A felhasználó 1:00 ÓRAKOR megpróbál hozzáférni a (z) A webalkalmazáshoz. A rendszer átirányítja a felhasználót az Azure AD-ba. Az A webalkalmazás nincs hozzárendelve egyetlen házirendhez sem, azonban mivel az alapértelmezett jogkivonat-élettartamot biztosító szervezeten belül van, a házirend érvénybe lép. A rendszer az utolsó nyolc órában eredetileg kiállított munkamenet-cookie-t észleli. A felhasználó csendesen át lesz irányítva a webalkalmazásba egy új azonosító jogkivonattal. A felhasználónak nincs szüksége a hitelesítésre.
>
> Ezt követően a felhasználó megpróbál hozzáférni a B webalkalmazáshoz. A rendszer átirányítja a felhasználót az Azure AD-ba. Ahogy korábban is, a jogkivonat élettartamának szabályzata 2 érvénybe lép. Mivel a jogkivonat több mint 30 perccel ezelőtt lett kiállítva, a rendszer felszólítja a felhasználót, hogy adja meg újra a bejelentkezési hitelesítő adatait. A rendszer új munkamenet-tokent és azonosító jogkivonatot állít ki. A felhasználó Ezután elérheti a B webalkalmazást.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurálható házirend-Tulajdonságok részletei
### <a name="access-token-lifetime"></a>Hozzáférési jogkivonat élettartama
**Karakterlánc:** AccessTokenLifetime

A **következőket érinti:** Hozzáférési tokenek, azonosító tokenek, SAML-tokenek

**Összefoglalás:** Ez a házirend azt szabályozza, hogy az erőforráshoz tartozó hozzáférési és azonosító jogkivonatok érvényesek legyenek. A hozzáférési jogkivonat élettartama tulajdonságának csökkentése csökkenti annak kockázatát, hogy egy rosszindulatú színész egy hozzáférési jogkivonatot vagy egy azonosító jogkivonatot hosszabb ideig használ. (Ezek a tokenek nem vonhatók vissza.) A kompromisszum az, hogy a teljesítmény negatív hatással van, mivel a jogkivonatokat gyakrabban kell cserélni.

### <a name="refresh-token-max-inactive-time"></a>Frissítési jogkivonat maximális inaktív ideje
**Karakterlánc:** MaxInactiveTime

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a frissítési jogkivonat hány korábbi lehet, hogy az ügyfél többé nem tudja lekérni az új hozzáférési/frissítési jogkivonat-párokat, amikor megpróbál hozzáférni ehhez az erőforráshoz. Mivel a rendszer általában új frissítési jogkivonatot ad vissza a frissítési token használatakor, ez a házirend megakadályozza a hozzáférést, ha az ügyfél az aktuális frissítési jogkivonat használatával megpróbál hozzáférni az erőforrásokhoz a megadott időszakban.

Ez a házirend azokat a felhasználókat kényszeríti, akik nem voltak aktívak az ügyfélen az új frissítési jogkivonat lekéréséhez.

A frissítési token Max inaktív idő tulajdonságának alacsonyabb értékre kell állítani, mint az egytényezős token Max Age és a multi-Factor frissítési token Max Age tulajdonságai.

### <a name="single-factor-refresh-token-max-age"></a>Egy tényező frissítési Tokenének maximális kora
**Karakterlánc:** MaxAgeSingleFactor

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhat frissítési jogkivonatot új hozzáférési/frissítési jogkivonat-párok beszerzésére, miután a rendszer csak egyetlen tényező használatával sikeresen hitelesítette a hitelesítést. Miután egy felhasználó hitelesíti és új frissítési tokent kapott, a felhasználó a megadott időtartamra használhatja a frissítési jogkivonat folyamatát. (Ez akkor igaz, ha az aktuális frissítési jogkivonat nincs visszavonva, és az inaktív időpontnál hosszabb ideig nem használatban marad.) Ezen a ponton a felhasználónak újra hitelesítenie kell magát, hogy új frissítési tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy kisebb, mint a multi-Factor refresh token Max Age tulajdonsága.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor refresh token Max Age
**Karakterlánc:** MaxAgeMultiFactor

A **következőket érinti:** Tokenek frissítése

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhat frissítési jogkivonatot új hozzáférési/frissítési jogkivonat-párok beszerzéséhez, miután több tényezővel sikeresen elvégezte a hitelesítést. Miután egy felhasználó hitelesíti és új frissítési tokent kapott, a felhasználó a megadott időtartamra használhatja a frissítési jogkivonat folyamatát. (Ez akkor igaz, ha az aktuális frissítési jogkivonat nincs visszavonva, és az inaktívnál hosszabb ideig nem használható fel.) Ezen a ponton a felhasználóknak újra kell hitelesíteniük magukat, hogy új frissítési tokent kapjanak.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy nagyobb, mint az egytényezős frissítési jogkivonat Max Age tulajdonsága.

### <a name="single-factor-session-token-max-age"></a>Egy tényezős munkamenet-token maximális kora
**Karakterlánc:** MaxAgeSessionSingleFactor

A **következőket érinti:** Munkamenet-tokenek (állandó és nem állandó)

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a munkamenet-tokent új azonosító és munkamenet-jogkivonat beszerzésére, miután a rendszer csak egyetlen tényezővel sikeresen hitelesítette a hitelesítést. Miután egy felhasználó egy új munkamenet-tokent hitelesített és kap, a felhasználó a munkamenet-jogkivonat folyamatát a megadott ideig használhatja. (Ez akkor igaz, ha az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam elteltével a felhasználónak újra hitelesítenie kell magát, hogy új munkamenet-tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely a multi-Factor munkamenet-token Max Age tulajdonságával egyenlő vagy annál kisebb.

### <a name="multi-factor-session-token-max-age"></a>Többtényezős munkamenet-token maximális kora
**Karakterlánc:** MaxAgeSessionMultiFactor

A **következőket érinti:** Munkamenet-tokenek (állandó és nem állandó)

**Összefoglalás:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a munkamenet-tokent új azonosító és munkamenet-jogkivonat beszerzésére, miután a rendszer a legutóbbi sikeres hitelesítés után több tényezőt is sikeresen hitelesített. Miután egy felhasználó egy új munkamenet-tokent hitelesített és kap, a felhasználó a munkamenet-jogkivonat folyamatát a megadott ideig használhatja. (Ez akkor igaz, ha az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam elteltével a felhasználónak újra hitelesítenie kell magát, hogy új munkamenet-tokent kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egytényezős hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, javasoljuk, hogy ezt a tulajdonságot olyan értékre állítsa be, amely egyenlő vagy nagyobb, mint az egytényezős munkamenet-token Max Age tulajdonsága.

## <a name="example-token-lifetime-policies"></a>Példa a jogkivonat élettartamára vonatkozó szabályzatokra
Az Azure AD-ben számos forgatókönyv lehetséges, ha az alkalmazásokhoz, az egyszerű szolgáltatásokhoz és a teljes szervezethez token-élettartamot hozhat létre és kezelhet. Ebben a szakaszban néhány olyan általános házirend-forgatókönyvet ismertetünk, amely segíthet a következő új szabályok bevezetésében:

* Jogkivonat élettartama
* Token maximális inaktív ideje
* Token maximális kora

A példákban megtudhatja, hogyan végezheti el a következőket:

* A szervezet alapértelmezett házirendjének kezelése
* Házirend létrehozása webes bejelentkezéshez
* Szabályzat létrehozása egy webes API-t meghívó natív alkalmazáshoz
* Speciális szabályzat kezelése

### <a name="prerequisites"></a>Előfeltételek
Az alábbi példákban létrehozhat, frissíthet, csatolhat és törölhet szabályzatokat az alkalmazásokhoz, az egyszerű szolgáltatásokhoz és a teljes szervezethez. Ha még nem ismeri az Azure AD-t, javasoljuk, hogy Ismerje meg, [hogyan szerezhet be Azure ad-bérlőt](quickstart-create-new-tenant.md) , mielőtt folytatja ezeket a példákat.  

A kezdéshez hajtsa végre a következő lépéseket:

1. Töltse le a legújabb [Azure ad PowerShell-modul nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview).
2. Az `Connect` parancs futtatásával jelentkezzen be az Azure AD-rendszergazdai fiókjába. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Futtassa ezt a parancsot a legtöbb művelet után a következő esetekben. A parancs futtatása a szabályzatok * * * * beszerzését is segíti.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Példa: a szervezet alapértelmezett házirendjének kezelése
Ebben a példában olyan házirendet hoz létre, amely lehetővé teszi, hogy a felhasználók a teljes szervezeten belül ritkábban jelentkezzenek be. Ehhez hozzon létre egy jogkivonat-élettartam-szabályzatot az egytényezős frissítési tokenekhez, amelyet a rendszer a szervezeten belül alkalmaz. A szabályzatot a szervezet minden alkalmazására, valamint minden olyan egyszerű szolgáltatásnév alkalmazza, amely még nem rendelkezik házirend-beállítással.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Állítsa az egytényezős frissítési tokent a "visszavonás visszavonása" értékre. A jogkivonat nem jár le, amíg vissza nem vonja a hozzáférést. Hozza létre a következő szabályzat-definíciót:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. A szabályzat létrehozásához futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId**beszerzéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Frissítse a szabályzatot.

    Dönthet úgy is, hogy az ebben a példában megadott első szabályzat nem annyira szigorú, mint a szolgáltatás. Ha úgy szeretné beállítani az egytényezős frissítési tokent, hogy két nap múlva lejárjon, futtassa a következő parancsot:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Példa: házirend létrehozása webes bejelentkezéshez

Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban. Ezzel a szabályzattal állítható be a hozzáférési/azonosító tokenek élettartama, valamint a többtényezős munkamenet-tokenek maximális kora a webalkalmazás egyszerű szolgáltatásnév számára.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    Ez a házirend a webes bejelentkezéshez megadja a hozzáférési/azonosító jogkivonat élettartamát és az egytényezős munkamenet-token maximális életkorát két óráig.

    1. A szabályzat létrehozásához futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId**beszerzéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév **ObjectId** is le kell kérnie.

    1. A [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmaggal tekintheti meg az összes szervezet egyszerű szolgáltatását vagy egy egyszerű szolgáltatásnevet.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Ha rendelkezik az egyszerű szolgáltatással, futtassa a következő parancsot:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Példa: hozzon létre egy házirendet egy olyan natív alkalmazáshoz, amely meghívja a webes API-t
Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználóknak ritkábban kell hitelesíteniük magukat. A szabályzat emellett meghosszabbítja azt az időtartamot is, ameddig a felhasználó inaktív lehet, mielőtt a felhasználónak újra hitelesítenie kell magát. A szabályzatot a rendszer a webes API-ra alkalmazza. Ha a natív alkalmazás erőforrásként kéri a webes API-t, a rendszer alkalmazza ezt a házirendet.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Ha szigorú szabályzatot szeretne létrehozni egy webes API-hoz, futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Az új szabályzat megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Rendelje hozzá a szabályzatot a webes API-hoz. Emellett le kell kérnie az alkalmazás **ObjectId** is. Használja a [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) parancsmagot az alkalmazás **ObjectId**megkereséséhez, vagy használja a [Azure Portal](https://portal.azure.com/).

    Szerezze be az alkalmazás **ObjectId** , és rendelje hozzá a szabályzatot:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Példa: speciális szabályzat kezelése
Ebben a példában néhány szabályzatot hoz létre a prioritási rendszer működésének megismeréséhez. Azt is megtudhatja, hogyan kezelhet több objektumra alkalmazott házirendeket.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. A következő parancs futtatásával hozhat létre olyan alapértelmezett házirendet, amely az egytényezős frissítési jogkivonat élettartamát 30 napra állítja be:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Az új szabályzat megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Rendelje hozzá a szabályzatot egy egyszerű szolgáltatáshoz.

    Most már rendelkezik egy szabályzattal, amely a teljes szervezetre vonatkozik. Előfordulhat, hogy meg szeretné őrizni ezt a 30 napos házirendet egy adott egyszerű szolgáltatásnév esetében, de a szervezet alapértelmezett házirendjét a "visszavonás visszavonása" felső korlátra kell módosítania.

    1. A szervezet összes szolgáltatásának megtekintéséhez használja a [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmagot.

    2. Ha rendelkezik az egyszerű szolgáltatással, futtassa a következő parancsot:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. A `IsOrganizationDefault` jelző beállítása false (hamis) értékre:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Új szervezet alapértelmezett házirendjének létrehozása:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már rendelkezik a szolgáltatáshoz tartozó eredeti házirenddel, és az új szabályzat beállítása a szervezet alapértelmezett házirendje. Fontos megjegyezni, hogy az egyszerű szolgáltatásokra alkalmazott szabályzatok elsőbbséget élveznek a szervezet alapértelmezett házirendjeivel szemben.

## <a name="cmdlet-reference"></a>Parancsmag-referencia

### <a name="manage-policies"></a>Házirendek kezelése

A szabályzatok kezeléséhez a következő parancsmagokat használhatja.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Létrehoz egy új szabályzatot.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Definition</code> |A szabályzat összes szabályát tartalmazó sztringesített JSON tömbje. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |A szabályzat nevének karakterlánca |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Ha az értéke igaz, a szabályzatot a szervezet alapértelmezett házirendjé szerint állítja be. Hamis érték esetén nem. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |A házirend típusa. A jogkivonat élettartama esetén mindig használja a "TokenLifetimePolicy" értéket. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [nem kötelező] |A szabályzat alternatív AZONOSÍTÓjának beállítása. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Lekéri az összes Azure AD-házirendet vagy egy megadott szabályzatot.

```powershell
Get-AzureADPolicy
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> [nem kötelező] |A kívánt szabályzat **ObjectId (azonosító)** . |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
A Szabályzathoz kapcsolódó összes alkalmazás és szolgáltatásnév beolvasása.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt szabályzat **ObjectId (azonosító)** . |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Frissíti a meglévő szabályzatokat.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt szabályzat **ObjectId (azonosító)** . |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |A szabályzat nevének karakterlánca |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [nem kötelező] |A szabályzat összes szabályát tartalmazó sztringesített JSON tömbje. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [nem kötelező] |Ha az értéke igaz, a szabályzatot a szervezet alapértelmezett házirendjé szerint állítja be. Hamis érték esetén nem. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [nem kötelező] |A házirend típusa. A jogkivonat élettartama esetén mindig használja a "TokenLifetimePolicy" értéket. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [nem kötelező] |A szabályzat alternatív AZONOSÍTÓjának beállítása. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
A megadott házirend törlése.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt szabályzat **ObjectId (azonosító)** . | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Alkalmazás-házirendek
Az alkalmazás-házirendekhez a következő parancsmagokat használhatja.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
A megadott szabályzat csatolása egy alkalmazáshoz.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |A szabályzat **ObjectId** . | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Az alkalmazáshoz rendelt szabályzat beolvasása.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Eltávolít egy szabályzatot egy alkalmazásból.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |A szabályzat **ObjectId** . | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Egyszerű szolgáltatásnév házirendek
A következő parancsmagokat használhatja az egyszerű szolgáltatásnév házirendjeihez.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
A megadott házirend csatolása egy egyszerű szolgáltatáshoz.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |A szabályzat **ObjectId** . | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Lekéri a megadott egyszerű szolgáltatáshoz kapcsolódó házirendet.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Eltávolítja a szabályzatot a megadott egyszerű szolgáltatásból.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectId (azonosító)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |A szabályzat **ObjectId** . | `-PolicyId <ObjectId of Policy>` |
