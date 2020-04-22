---
title: Konfigurálható Azure AD-tokenélettartamok
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan állíthatja be az Azure AD által kiadott jogkivonatok élettartamát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680072"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurálható jogkivonat-élettartam az Azure Active Directoryban (előzetes verzió)

Megadhatja az Azure Active Directory (Azure AD) által kiállított jogkivonatok élettartamát. Beállíthatja a cégen belüli összes alkalmazás jogkivonatának élettartamát több-bérlős alkalmazások (több cég) vagy munkahelyen belüli adott szolgáltatásnév esetén.

> [!IMPORTANT]
> Miután az előzetes verzió során meghallotta az ügyfelek, [hitelesítési munkamenet-kezelési képességeket](https://go.microsoft.com/fwlink/?linkid=2083106) valósítottunk meg az Azure AD feltételes hozzáférésben. Ezzel az új funkcióval konfigurálhatja a frissítési jogkivonat élettartamát a bejelentkezési gyakoriság beállításával. 2020. május 1-je után nem fogja tudni használni a konfigurálható token élettartama házirendet a munkamenetek és a tokenek frissítéséhez. Az eprecáció után is konfigurálhatja a hozzáférési jogkivonat élettartamát.

Az Azure AD-ben egy szabályzatobjektum olyan szabályokat jelöl, amelyek az egyes alkalmazásokon vagy a szervezet összes alkalmazásában vannak kényszerítve. Minden házirendtípus egyedi struktúrával rendelkezik, és olyan tulajdonságokat tartalmaz, amelyek azokra az objektumokra vonatkoznak, amelyekhez hozzá vannak rendelve.

A házirendet kijelölheti a szervezet alapértelmezett házirendjeként. A házirend a szervezet bármely alkalmazására vonatkozik, feltéve, hogy egy magasabb prioritású házirend nem bírálja felül. Adott alkalmazásokhoz házirendet is hozzárendelhet. A prioritás imassa házirendtípusonként változik.

> [!NOTE]
> A konfigurálható jogkivonat élettartamára vonatkozó házirend csak azokra a mobil- és asztali ügyfelekre vonatkozik, amelyek a SharePoint Online- és a OneDrive Vállalati verzió erőforrásait férnek hozzá, és nem vonatkozik a webböngésző-munkamenetekre.
> A SharePoint Online és a OneDrive Vállalati verzió webböngésző-munkameneteinek élettartamának kezeléséhez használja a [Feltételes hozzáférés munkamenetek élettartamát.](../conditional-access/howto-conditional-access-session-lifetime.md) Az írásnemi szolgáltatások időmegosztásának konfigurálásáról a [SharePoint Online-blogban](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) olvashat bővebben.

## <a name="token-types"></a>Tokentípusok

Beállíthatja a tokenek élettartama szabályzatok frissítési jogkivonatok, hozzáférési jogkivonatok, SAML-jogkivonatok, munkamenet-jogkivonatok és azonosító jogkivonatok.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az ügyfelek hozzáférési jogkivonatokat használnak a védett erőforrások eléréséhez. A hozzáférési jogkivonat csak a felhasználó, az ügyfél és az erőforrás adott kombinációjához használható. A hozzáférési jogkivonatok nem vonhatók vissza, és lejáratukig érvényesek. A hozzáférési jogkivonatot megszerző rosszindulatú aktor élettartama során használhatja azt. A hozzáférési jogkivonat élettartamának módosítása kompromisszumot jelent a rendszer teljesítményének javítása és az ügyfél hozzáférésének növelése között, miután a felhasználó fiókja le van tiltva. A jobb rendszerteljesítmény azáltal érhető el, hogy csökkenti az ügyfél új hozzáférési jogkivonat beszerzéséhez szükséges időszámát.  Az alapértelmezett érték 1 óra - 1 óra elteltével az ügyfélnek a frissítési jogkivonatot kell használnia (általában csendesen) egy új frissítési jogkivonat és hozzáférési jogkivonat beszerzéséhez. 

### <a name="saml-tokens"></a>SAML-jogkivonatok

SAML-jogkivonatokat számos webalapú SAAS-alkalmazás használja, és az Azure Active Directory SAML2 protokollvégpontjával szerezhetők be. A WS-Federation-t használó alkalmazások is használják őket. A jogkivonat alapértelmezett élettartama 1 óra. Az alkalmazás szempontjából a jogkivonat érvényességi idejét a tokenben lévő `<conditions …>` elem NotOnOrAfter értéke határozza meg. A jogkivonat érvényességi ideje lejárta után az ügyfélnek új hitelesítési kérelmet kell kezdeményeznie, amely gyakran teljesül az egyszeri bejelentkezési (SSO) munkamenet-jogkivonat eredményeképpen interaktív bejelentkezés nélkül.

A NotOnOrOrAfter értéke a `AccessTokenLifetime` paraméter rel `TokenLifetimePolicy`módosítható. A házirendben beállított élettartamra lesz beállítva, plusz egy ötperces óradöntési tényezőre.

Vegye figyelembe, hogy a tulajdonos megerősítése `<SubjectConfirmationData>` NotOnOrMi-ban megadott elem nem befolyásolja a Token Élettartam konfiguráció. 

### <a name="refresh-tokens"></a>Tokenek frissítése

Amikor egy ügyfél beszerez egy hozzáférési jogkivonatot egy védett erőforrás eléréséhez, az ügyfél egy frissítési jogkivonatot is kap. A frissítési jogkivonat új hozzáférési/frissítési jogkivonat-párok beszerzésére szolgál, amikor az aktuális hozzáférési jogkivonat lejár. A frissítési jogkivonat a felhasználó és az ügyfél kombinációjához van kötve. A frissítési jogkivonat [bármikor visszavonható,](access-tokens.md#token-revocation)és a jogkivonat érvényességét minden alkalommal ellenőrzi a jogkivonat.  A frissítési jogkivonatok nem vonhatók vissza, ha új hozzáférési jogkivonatok lekéréséhez használják – ajánlott azonban biztonságosan törölni a régi jogkivonatot, amikor újat kap. 

Fontos különbséget tenni a bizalmas ügyfelek és a nyilvános ügyfelek között, mivel ez hatással van arra, hogy mennyi ideig használható a frissítési jogkivonatok. A különböző típusú ügyfelekről a [6749.](https://tools.ietf.org/html/rfc6749#section-2.1)

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token élettartama bizalmas ügyfélfrissítési jogkivonatoksegítségével
A bizalmas ügyfelek olyan alkalmazások, amelyek biztonságosan tárolhatják az ügyféljelszót (titkos). Bizonyítani tudják, hogy a kérelmek a biztonságos ügyfélalkalmazásból érkeznek, és nem egy rosszindulatú aktorból. Egy webalkalmazás például bizalmas ügyfél, mert egy ügyféltitkot tárolhat a webkiszolgálón. Nincs kitéve. Mivel ezek a folyamatok biztonságosabbak, az ezekre a `until-revoked`folyamatokra kiadott frissítési jogkivonatok alapértelmezett élettartama nem módosítható házirend használatával, és nem vonható vissza az önkéntes jelszó-visszaállításesetén.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokenélettartamok nyilvános ügyfélfrissítési jogkivonatokkal

A nyilvános ügyfelek nem tudják biztonságosan tárolni az ügyféljelszót (titkos). Például egy iOS/Android alkalmazás nem tudja elhomályosítani az erőforrás-tulajdonos titkos titkát, ezért nyilvános ügyfélnek minősül. Az erőforrásokházirendek házirendek megakadályozhatja, hogy a megadott időszaknál régebbi nyilvános ügyfelek től származó frissítési jogkivonatokat egy új hozzáférési/frissítési jogkivonat-pár beszerzése. (Ehhez használja a Token maximális inaktív idő`MaxInactiveTime`frissítése tulajdonságot ( ).) A házirendek segítségével is beállíthatja azt az időszakot, amelyen túl a frissítési jogkivonatok már nem fogadják el. (Ehhez használja a Token max kor frissítése tulajdonságot.) A frissítési jogkivonat élettartamát úgy módosíthatja, hogy a nyilvános ügyfélalkalmazás használatakor a felhasználónak mikor és milyen gyakran kell újra megadnia a hitelesítő adatokat, ahelyett, hogy csendben újra hitelesítené.

> [!NOTE]
> A Max Age tulajdonság az az időtartam, amvanegy token használható. 

### <a name="id-tokens"></a>Azonosító jogkivonatok
Az azonosító jogkivonatokat a webhelyek és a natív ügyfelek továbbítják. Az azonosító tokenek egy felhasználó profiladatait tartalmazzák. Egy azonosító jogkivonat a felhasználó és az ügyfél egy adott kombinációjához van kötve. Az azonosító jogkivonatok lejáratukig érvényesnek minősülnek. A webalkalmazás általában egyezteti a felhasználó munkamenet-élettartamát az alkalmazásban a felhasználó számára kiadott azonosító jogkivonat élettartamával. Módosíthatja az azonosító jogkivonat élettartamát, hogy szabályozhassa, hogy a webalkalmazás milyen gyakran járjon le az alkalmazásmunkamenetben, és milyen gyakran igényli a felhasználó újrahitelesítését az Azure AD-vel (csendesen vagy interaktívan).

### <a name="single-sign-on-session-tokens"></a>Egyszeri bejelentkezési munkamenet-jogkivonatok
Amikor egy felhasználó hitelesíti magát az Azure AD-vel, egyetlen bejelentkezési munkamenet (SSO) jön létre a felhasználó böngészőjével és az Azure AD-vel. Az Egyszeri bejelentkezés token cookie formájában képviseli ezt a munkamenetet. Az egyszeri kiszolgáló munkamenet-jogkivonata nincs egy adott erőforráshoz/ügyfélalkalmazáshoz kötve. Egyszeri jelleg munkamenet-tokenek visszavonhatók, és azok érvényességét minden használatkor ellenőrzik.

Az Azure AD kétféle egyszeri szolgáltatás munkamenet-jogkivonatot használ: állandó és nem állandó. Az állandó munkamenet-tokeneket a böngésző állandó cookie-ként tárolja. A nem állandó munkamenet-tokenek munkamenet-cookie-kként tárolódnak. (A munkamenet-cookie-k a böngésző bezárásakor megsemmisülnek.) Általában egy nem állandó munkamenet-jogkivonat tárolása történik. De ha a felhasználó bejelöli a **Bejelentkezés bejelentkezve** jelölőnégyzetet a hitelesítés során, egy állandó munkamenet-jogkivonat tárolódik.

A nem állandó munkamenet-jogkivonatok élettartama 24 óra. Az állandó jogkivonatok élettartama 180 nap. Ha egy egyszeri biztonsági adotta munkamenet-jogkivonatot használ az érvényességi időn belül, az érvényességi idő a jogkivonat típusától függően további 24 órával vagy 180 nappal meghosszabbodik. Ha egy egyszeri biztonsági tanúsítvány munkamenet-jogkivonata nem használható az érvényességi időszakon belül, akkor lejártnak minősül, és már nem fogadható el.

Egy szabályzat segítségével beállíthatja az időt az első munkamenet-jogkivonat kiadása után, amelyen túl a munkamenet-jogkivonat már nem fogadható el. (Ehhez használja a Session Token Max Age tulajdonságot.) A munkamenet-jogkivonat élettartamát úgy állíthatja be, hogy a felhasználónak mikor és milyen gyakran kell újra megadnia a hitelesítő adatokat, ahelyett, hogy csendben hitelesítené, amikor egy webalkalmazást használ.

### <a name="token-lifetime-policy-properties"></a>Jogkivonat élettartamra vonatkozó házirendtulajdonságai
A jogkivonat élettartamra vonatkozó szabályzata olyan típusú házirend-objektum, amely jogkivonat élettartama szabályokat tartalmaz. A megadott jogkivonat-élettartamok vezérléséhez használja a házirend tulajdonságait. Ha nincs beállítva házirend, a rendszer kényszeríti az alapértelmezett élettartam-értéket.

### <a name="configurable-token-lifetime-properties"></a>Konfigurálható token élettartamának tulajdonságai
| Tulajdonság | Házirend tulajdonságkarakterlánca | Befolyásolja | Alapértelmezett | Minimális | Maximum |
| --- | --- | --- | --- | --- | --- |
| Hozzáférési jogkivonat élettartama |AccessTokenLifetime<sup>2</sup> |Hozzáférési jogkivonatok, azonosító jogkivonatok, SAML2-tokenek |1 óra |10 perc |1 nap |
| Token maximális inaktív idejének frissítése |MaxInactiveTime |Tokenek frissítése |90 nap |10 perc |90 nap |
| Egyfaktoros frissítési token maximális életkora |Maxage SingleFactor (MaxageSingleFactor) |Tokenek frissítése (bármely felhasználó számára) |Visszavonásig |10 perc |Visszavonásig<sup>1</sup> |
| Többtényezős frissítési token maximális életkora |MaxAgeMultiFactor |Tokenek frissítése (bármely felhasználó számára) |Visszavonásig |10 perc |Visszavonásig<sup>1</sup> |
| Egytényezős munkamenet token maximális életkora |MaxAgeSessionSingleFactor |Munkamenet-jogkivonatok (állandó és nem állandó) |Visszavonásig |10 perc |Visszavonásig<sup>1</sup> |
| Többtényezős munkamenet token maximális életkora |MaxAgeSessionMultiFactor |Munkamenet-jogkivonatok (állandó és nem állandó) |Visszavonásig |10 perc |Visszavonásig<sup>1</sup> |

* <sup>1</sup>365 nap az attribútumokhoz beállítható maximális explicit hossz.
* <sup>2.</sup> A Microsoft Teams webes ügyfélszolgáltatás működésének biztosítása érdekében ajánlott az AccessTokenLifetime élettartamát 15 percnél hosszabb ideig tartani a Microsoft Teams számára.

### <a name="exceptions"></a>Kivételek
| Tulajdonság | Befolyásolja | Alapértelmezett |
| --- | --- | --- |
| Token maximális korának frissítése (olyan összevont felhasználók számára, akik nem rendelkeznek visszavonási információval<sup>1</sup>) |Jogkivonatok frissítése (olyan összevont felhasználók számára, akik nem rendelkeznek visszavonási információval<sup>1)</sup> |12 óra |
| Token maximális inaktív idejének frissítése (bizalmas ügyfelek számára) |Tokenek frissítése (bizalmas ügyfelek számára) |90 nap |
| Token maximális korának frissítése (bizalmas ügyfelek számára) |Tokenek frissítése (bizalmas ügyfelek számára) |Visszavonásig |

* <sup>1 1</sup> Azok az összevont felhasználók, akik nem rendelkeznek visszavonási információkkal, közéjük tartoznak azok a felhasználók, akik nem rendelkeznek a "LastPasswordChangeTimestamp" attribútummal szinkronizálva. Ezek a felhasználók kapnak ez a rövid Max Age, mert AAD nem tudja ellenőrizni, hogy mikor kell visszavonni a tokenek, amelyek kapcsolódnak egy régi hitelesítő adatok (például egy jelszó, amely megváltozott), és ellenőriznie kell vissza gyakrabban annak érdekében, hogy a felhasználó és a kapcsolódó jogkivonatok még mindig jó helyzetben van. A felhasználói élmény javítása érdekében a bérlői rendszergazdáknak meg kell győződniük arról, hogy szinkronizálják a "LastPasswordChangeTimestamp" attribútumot (ez beállítható a felhasználói objektumon a Powershell használatával vagy az AADSync használatával).

### <a name="policy-evaluation-and-prioritization"></a>Házirend-értékelés és rangsorolás
Létrehozhat és rendelhet hozzá egy jogkivonat élettartam-szabályzatát egy adott alkalmazáshoz, a szervezethez és a szolgáltatásnévi tagokhoz. Egy adott alkalmazásra több szabályzat is vonatkozhat. A jogkivonat élettartamra vonatkozó szabályzata az alábbi szabályokat követi:

* Ha egy házirend explicit módon van hozzárendelve a szolgáltatásnévhez, a rendszer kényszeríti.
* Ha az egyszerű szolgáltatáshoz nincs explicit házirend, a rendszer a szolgáltatásnév szülőszervezetéhez explicit módon hozzárendelt házirendet érvényesít.
* Ha nincs explicit módon hozzárendelve a szolgáltatásnévhez vagy a szervezethez, a rendszer az alkalmazáshoz rendelt házirendet érvényesíti.
* Ha nincs házirend hozzárendelve a szolgáltatásnévhez, a szervezethez vagy az alkalmazásobjektumhoz, az alapértelmezett értékek érvénybe lépnek. (Lásd a [tábla konfigurálható token élettartama tulajdonságok](#configurable-token-lifetime-properties).)

Az alkalmazásobjektumok és az egyszerű szolgáltatásobjektumok közötti kapcsolatról az [Azure Active Directory alkalmazás- és egyszerű szolgáltatásobjektumai](app-objects-and-service-principals.md)című témakörben talál további információt.

A jogkivonat érvényességét a jogkivonat használatkor értékeli ki a jogkivonat. A hozzáférés alatt lévő alkalmazás legmagasabb prioritású házirendje lép érvénybe.

Az itt használt összes időtartomány a C# [TimeSpan](/dotnet/api/system.timespan) objektumnak megfelelően van formázva - D.Hh:MM:SS.  Tehát 80 nap és 30 perc lenne `80.00:30:00`.  A vezető D lehet dobni, ha nulla, `00:90:00`így 90 perc lenne .  

> [!NOTE]
> Íme egy példa forgatókönyv.
>
> A felhasználó két webalkalmazáshoz szeretne hozzáférni: az A és a B webalkalmazáshoz.
> 
> Tényezők:
> * Mindkét webalkalmazás ugyanabban a szülőszervezetben található.
> * Token élettartama házirend 1 egy munkamenet token maximális kora nyolc óra van beállítva a szülő szervezet alapértelmezett.
> * Az "A" webalkalmazás egy rendszeresen használható webalkalmazás, és nem kapcsolódik semmilyen házirendhez.
> * A B webalkalmazás rendkívül érzékeny folyamatokhoz használatos. Az egyszerű szolgáltatás a Token élettartama házirend 2, amely egy munkamenet-jogkivonat maximális kora 30 perc.
>
> 12:00 órakor a felhasználó új böngészőmunkamenetet indít, és megpróbál hozzáférni az A webalkalmazáshoz. A felhasználó átirányítja az Azure AD,és meg kell adnia, hogy jelentkezzen be. Ez létrehoz egy cookie-t, amely munkamenet-jogkivonatot hoz létre a böngészőben. A felhasználó egy azonosító tokent tartalmazó azonosító tokent ad vissza az A webalkalmazásba, amely lehetővé teszi a felhasználó számára az alkalmazás elérését.
>
> 12:15-kor a felhasználó megpróbál hozzáférni a B webalkalmazáshoz. A böngésző átirányítja az Azure AD, amely észleli a munkamenet cookie-t. A B webalkalmazás egyszerű szolgáltatása a Token élettartamra szóló házirend 2-höz kapcsolódik, de egyben a szülőszervezet része is, az alapértelmezett jogkivonat élettartam-házirendje 1. A token élettartamra szóló házirend 2 érvénybe lép, mert a szolgáltatásnévi tagokhoz kapcsolódó házirendek prioritása magasabb, mint a szervezet alapértelmezett házirendjei. A munkamenet-jogkivonat eredetileg az utolsó 30 percben lett kiadva, így érvényesnek minősül. A felhasználó egy olyan azonosító jogkivonattal visszairányítja vissza a B webalkalmazásba, amely hozzáférést biztosít számukra.
>
> 13:00-kor a felhasználó megpróbál hozzáférni az A webalkalmazáshoz. A felhasználó átlesz irányítva az Azure AD-re. Az "A" webalkalmazás nem kapcsolódik házirendekhez, hanem azért, mert egy olyan szervezetben van, amely alapértelmezett jogcím-élettartam-házirend1, akkor a házirend lép érvénybe. Az eredetileg az elmúlt nyolc órában kiadott munkamenet-cookie észlelésre kerül. A felhasználó csendben visszairányítja az A webalkalmazásegy új azonosító jogkivonattal. A felhasználónak nem kell hitelesítenie magát.
>
> Közvetlenül ezután a felhasználó megpróbál hozzáférni a B webalkalmazáshoz. A felhasználó átlesz irányítva az Azure AD-re. A token élettartamra vonatkozó házirend 2-hez úgy lép érvénybe, mint korábban. Mivel a jogkivonat több mint 30 perccel ezelőtt lett kiadva, a rendszer kéri a felhasználót, hogy adja meg újra a bejelentkezési hitelesítő adatait. Egy vadonatúj munkamenet-jogkivonat és azonosító jogkivonat áll ki. A felhasználó ezután elérheti a B webalkalmazást.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurálható házirendtulajdonság-adatok
### <a name="access-token-lifetime"></a>Hozzáférési jogkivonat élettartama
**Karakterlánc:** AccessTokenLifetime

**Hatással van:** Hozzáférési jogkivonatok, azonosító jogkivonatok, SAML-tokenek

**Összefoglaló:** Ez a házirend szabályozza, hogy az erőforrás hoz a hozzáférés és az azonosító jogkivonatok mennyi ideig minősülnek érvényesnek. Az Access Jogkivonat élettartama tulajdonság csökkentése csökkenti annak kockázatát, hogy egy rosszindulatú szereplő hosszabb ideig egy rosszindulatú aktor által használt hozzáférési jogkivonatot vagy azonosítójogkivonatot használ. (Ezek a tokenek nem vonhatók vissza.) A kompromisszum az, hogy a teljesítmény hátrányosan befolyásolja, mert a tokenek gyakrabban kell cserélni.

### <a name="refresh-token-max-inactive-time"></a>Token maximális inaktív idejének frissítése
**Karakterlánc:** MaxInactiveTime

**Hatással van:** Tokenek frissítése

**Összefoglaló:** Ez a házirend szabályozza, hogy hány éves lehet egy frissítési jogkivonat, mielőtt az ügyfél már nem használhatja egy új hozzáférési/frissítési jogkivonat-pár lekéréséhez az erőforrás elérésekor. Mivel egy új frissítési jogkivonat általában frissítési jogkivonat használata esetén ad vissza, ez a házirend megakadályozza a hozzáférést, ha az ügyfél megpróbál hozzáférni bármely erőforráshoz az aktuális frissítési jogkivonat használatával a megadott időszakban.

Ez a házirend kényszeríti azokat a felhasználókat, akik nem aktívak az ügyfélen, hogy újra hitelesítsék magukat egy új frissítési jogkivonat lekéréséhez.

A Token maximális inaktív idő frissítése tulajdonságot alacsonyabb értékre kell beállítani, mint az egytényezős token maximális kora és a többtényezős frissítési jogkivonat maximális korának tulajdonsága.

### <a name="single-factor-refresh-token-max-age"></a>Egyfaktoros frissítési token maximális életkora
**Karakterlánc:** Maxage SingleFactor (MaxageSingleFactor)

**Hatással van:** Tokenek frissítése

**Összefoglaló:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a frissítési jogkivonatot egy új hozzáférési/frissítési jogkivonat-pár lekéréséhez, miután az utolsó hitelesítése csak egyetlen tényező használatával történt. Miután a felhasználó hitelesíti magát, és kap egy új frissítési jogkivonatot, a felhasználó használhatja a frissítési jogkivonat-folyamatot a megadott ideig. (Ez mindaddig igaz, amíg az aktuális frissítési jogkivonat nincs visszavonva, és nem marad használaton kívül az inaktív időnél hosszabb ideig.) Ezen a ponton a felhasználó kénytelen újrahitelesíteni egy új frissítési jogkivonat fogadásához.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egyfaktoros hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, azt javasoljuk, hogy állítsa be ezt a tulajdonságot olyan értékre, amely megegyezik a Többtényezős frissítési token maxkortulajdonságával.

### <a name="multi-factor-refresh-token-max-age"></a>Többtényezős frissítési token maximális életkora
**Karakterlánc:** MaxAgeMultiFactor

**Hatással van:** Tokenek frissítése

**Összefoglaló:** Ez a házirend azt szabályozza, hogy a felhasználó mennyi ideig használhatja a frissítési jogkivonatot egy új hozzáférési/frissítési jogkivonat-pár lekéréséhez, miután több tényező használatával utoljára sikeresen hitelesítették. Miután a felhasználó hitelesíti magát, és kap egy új frissítési jogkivonatot, a felhasználó használhatja a frissítési jogkivonat-folyamatot a megadott ideig. (Ez mindaddig igaz, amíg az aktuális frissítési jogkivonat nincs visszavonva, és nem használható hosszabb ideig, mint az inaktív idő.) Ezen a ponton a felhasználók kénytelenek újrahitelesíteni egy új frissítési jogkivonat fogadása.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egyfaktoros hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, azt javasoljuk, hogy állítsa be ezt a tulajdonságot olyan értékre, amely megegyezik az egyfaktoros frissítési token maximális kortulajdonságával.

### <a name="single-factor-session-token-max-age"></a>Egytényezős munkamenet token maximális életkora
**Karakterlánc:** MaxAgeSessionSingleFactor

**Hatással van:** Munkamenet-jogkivonatok (állandó és nem állandó)

**Összefoglaló:** Ez a házirend azt szabályozza, hogy a munkamenet-jogkivonat használatával a felhasználó mennyi ideig használhatja az új azonosítót és a munkamenet-jogkivonatot, miután csak egyetlen tényező használatával sikeresen hitelesítették. Miután a felhasználó hitelesíti magát, és kap egy új munkamenet-jogkivonatot, a felhasználó használhatja a munkamenet-jogkivonat-folyamatot a megadott ideig. (Ez mindaddig igaz, amíg az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam után a felhasználó kénytelen újrahitelesíteni, hogy egy új munkamenet-jogkivonatot kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egyfaktoros hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, azt javasoljuk, hogy állítsa be ezt a tulajdonságot olyan értékre, amely megegyezik a Többtényezős munkamenet token max kor tulajdonságával.

### <a name="multi-factor-session-token-max-age"></a>Többtényezős munkamenet token maximális életkora
**Karakterlánc:** MaxAgeSessionMultiFactor

**Hatással van:** Munkamenet-jogkivonatok (állandó és nem állandó)

**Összefoglaló:** Ez a házirend azt szabályozza, hogy a munkamenet-jogkivonat használatával a felhasználó mennyi ideig használhatja az új azonosítót és a munkamenet-jogkivonatot, miután a hitelesítés sikeresen több tényező használatával történt. Miután a felhasználó hitelesíti magát, és kap egy új munkamenet-jogkivonatot, a felhasználó használhatja a munkamenet-jogkivonat-folyamatot a megadott ideig. (Ez mindaddig igaz, amíg az aktuális munkamenet-jogkivonat nincs visszavonva, és nem járt le.) A megadott időtartam után a felhasználó kénytelen újrahitelesíteni, hogy egy új munkamenet-jogkivonatot kapjon.

A maximális életkor csökkentése arra kényszeríti a felhasználókat, hogy gyakrabban hitelesítsék magukat. Mivel az egyfaktoros hitelesítés kevésbé biztonságos, mint a többtényezős hitelesítés, azt javasoljuk, hogy állítsa be ezt a tulajdonságot olyan értékre, amely megegyezik az egyfaktoros munkamenet token max kor tulajdonságával.

## <a name="example-token-lifetime-policies"></a>Példa token élettartamra vonatkozó házirendjeire
Az Azure AD-ben számos forgatókönyv lehetséges, ha létrehozhat és kezelhet jogkivonat-élettartamokat az alkalmazások, a szolgáltatástagok és a teljes szervezet számára. Ebben a szakaszban néhány gyakori szakpolitikai forgatókönyvet vezetünk be, amelyek segíthetnek új szabályok bevezetésében:

* Jogkivonat élettartama
* Token maximális inaktív ideje
* Token maximális életkora

A példákban megtudhatja, hogyan teheti meg:

* Szervezet alapértelmezett házirendjének kezelése
* Szabályzat létrehozása a webes bejelentkezéshez
* Webes API-t meghívjaó natív alkalmazás házirendjének létrehozása
* Speciális házirend kezelése

### <a name="prerequisites"></a>Előfeltételek
Az alábbi példákban alkalmazások, egyszerű szolgáltatás- és szolgáltatásnévi szabályzatok létrehozása, frissítése, csatolása és törlése. Ha most ismerkedik az Azure AD-vel, azt javasoljuk, hogy ismerje meg, [hogyan szerezhet be egy Azure AD-bérlőt,](quickstart-create-new-tenant.md) mielőtt továbblépne ezekkel a példákkal.  

Első lépésként tegye a következőket:

1. Töltse le az [Azure AD PowerShell-modul nyilvános előzetes verziójának legújabb kiadását.](https://www.powershellgallery.com/packages/AzureADPreview)
2. Futtassa a parancsot az `Connect` Azure AD-rendszergazdai fiókba való bejelentkezéshez. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Futtassa ezt a parancsot a legtöbb művelet után a következő esetekben. A parancs futtatása abban is segít, hogy megkapja a szabályzatok ** ** -át.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Példa: Egy szervezet alapértelmezett házirendjének kezelése
Ebben a példában olyan szabályzatot hoz létre, amely lehetővé teszi a felhasználók ritkább bejelentkezését a teljes szervezetben. Ehhez hozzon létre egy jogkivonat élettartam-szabályzatot az egytényezős frissítési jogkivonatokhoz, amely a szervezeten belül alkalmazva van. A szabályzat a szervezet minden olyan alkalmazására vonatkozik, és minden olyan egyszerű szolgáltatásra, amely még nem rendelkezik házirendkészletével.

1. Hozzon létre egy token élettartamra vonatkozó szabályzatot.

    1. Állítsa az egytényezős frissítési jogkivonatot "vissza nem vonva"-ra. A jogkivonat nem jár le, amíg a hozzáférést vissza nem vonják. Hozza létre a következő házirend-definíciót:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. A házirend létrehozásához futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. A szóközterület eltávolításához futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Az új házirend megtekintéséhez és a házirend **ObjectId**azonosítójának levételéhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Frissítse a házirendet.

    Dönthet úgy, hogy az első házirend, amelyet ebben a példában beállított, nem olyan szigorú, mint a szolgáltatás megköveteli. Ha két napon belül le szeretné járatni az egytényezős frissítési jogkivonatot, futtassa a következő parancsot:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Példa: Szabályzat létrehozása webes bejelentkezéshez

Ebben a példában olyan szabályzatot hoz létre, amely megköveteli a felhasználóktól, hogy gyakrabban hitelesítsék magukat a webalkalmazásban. Ez a szabályzat a hozzáférési/id-jogkivonatok élettartamát és a többtényezős munkamenet-jogkivonat maximális korát állítja be a webalkalmazás egyszerű szolgáltatásához.

1. Hozzon létre egy token élettartamra vonatkozó szabályzatot.

    Ez a szabályzat a webes bejelentkezéshez a hozzáférési/azonosítójogkivonat élettartamát és a maximális egyfaktoros munkamenet-jogkivonat korát két órára állítja be.

    1. A házirend létrehozásához futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új házirend megtekintéséhez és a házirend **ObjectId**bekéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. A szolgáltatásnév **ObjectId** azonosítóját is be kell szereznie.

    1. A [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) parancsmag használatával a szervezet összes szolgáltatásnévi vagy egyetlen egyszerű szolgáltatásért való megtekintéséhez.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa a következő parancsot:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Példa: Hozzon létre egy szabályzatot egy webes API-t meghívjani tartalmazó natív alkalmazáshoz
Ebben a példában olyan házirendet hoz létre, amely ritkábban igényli a felhasználók hitelesítését. A házirend azt is meghosszabbítja, hogy a felhasználó mennyi ideig lehet inaktív, mielőtt a felhasználónak újra kell hitelesítenie magát. A szabályzat a webes API-ra vonatkozik. Amikor a natív alkalmazás erőforrásként kéri a webes API-t, ez a szabályzat lesz alkalmazva.

1. Hozzon létre egy token élettartamra vonatkozó szabályzatot.

    1. Ha szigorú házirendet szeretne létrehozni egy webes API-hoz, futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új házirend megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot a webes API-hoz. Azt is meg kell, hogy az **alkalmazás ObjectId.** A [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) parancsmag segítségével keresse meg az alkalmazás **ObjectId,** vagy használja az [Azure Portalon.](https://portal.azure.com/)

    Az alkalmazás **ObjectId** azonosítójának lekértése és a szabályzat hozzárendelése:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Példa: Speciális házirend kezelése
Ebben a példában hozzon létre néhány szabályzatot, hogy megtudja, hogyan működik a prioritási rendszer. Azt is megtudhatja, hogyan kezelheti a több objektumra alkalmazott több szabályzatot.

1. Hozzon létre egy token élettartamra vonatkozó szabályzatot.

    1. Ha olyan szervezet alapértelmezett házirendjét szeretne létrehozni, amely az egytényezős frissítési jogkivonat élettartamát 30 napra állítja be, futtassa a következő parancsot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Az új házirend megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a házirendet egy egyszerű szolgáltatáshoz.

    Most már van egy házirendje, amely az egész szervezetre vonatkozik. Előfordulhat, hogy meg szeretné őrizni ezt a 30 napos házirendet egy adott egyszerű szolgáltatáshoz, de módosítsa a szervezet alapértelmezett házirendjét a "vissza nem vonható" felső korlátra.

    1. A szervezet összes szolgáltatásnévi tag megtekintéséhez használja a [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) parancsmag.

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa a következő parancsot:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Állítsa `IsOrganizationDefault` a jelzőt hamisra:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Új szervezet alapértelmezett házirendjének létrehozása:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már rendelkezik az eredeti szabályzattal a szolgáltatásnévhez, és az új házirend van beállítva a szervezet alapértelmezett házirendjeként. Fontos megjegyezni, hogy a szolgáltatásnévi tagokra alkalmazott házirendek elsőbbséget élveznek a szervezet alapértelmezett házirendjeivel szemben.

## <a name="cmdlet-reference"></a>Parancsmag-referencia

### <a name="manage-policies"></a>A szabályzatok kezelése

A következő parancsmagok használatával kezelheti a házirendeket.

#### <a name="new-azureadpolicy"></a>Új-AzureAD-szabályzat

Új házirendet hoz létre.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Stringified JSON tömb, amely tartalmazza a házirend összes szabályát. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |A házirend nevének karakterlánca. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Ha igaz, a házirendet állítja be a szervezet alapértelmezett házirendjeként. Ha hamis, nem csinál semmit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |A házirend típusa. A token élettartama esetén mindig használja a "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Nem kötelező] |Alternatív azonosítót állít be a házirendhez. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Leveszi az összes Azure AD-szabályzatot vagy egy megadott szabályzatot.

```powershell
Get-AzureADPolicy
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code>[Nem kötelező] |A kívánt házirend **ObjectId azonosítója (ID).** |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject objektum
Leképezi a szabályzathoz kapcsolódó összes alkalmazást és szolgáltatásnévet.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt házirend **ObjectId azonosítója (ID).** |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADházirend
Meglévő házirend frissítése.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt házirend **ObjectId azonosítója (ID).** |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |A házirend nevének karakterlánca. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Nem kötelező] |Stringified JSON tömb, amely tartalmazza a házirend összes szabályát. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Nem kötelező] |Ha igaz, a házirendet állítja be a szervezet alapértelmezett házirendjeként. Ha hamis, nem csinál semmit. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Nem kötelező] |A házirend típusa. A token élettartama esetén mindig használja a "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Nem kötelező] |Alternatív azonosítót állít be a házirendhez. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Eltávolítás-AzureADPolicy
Törli a megadott házirendet.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |A kívánt házirend **ObjectId azonosítója (ID).** | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Tanúsítványhasználati házirend
Az alkalmazásházirendekhez a következő parancsmagokat használhatja.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
A megadott házirendet egy alkalmazáshoz kapcsolja.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |A házirend **objectid** azonosítója. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Lekéri az alkalmazáshoz rendelt házirendet.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Eltávolítás-AzureADApplicationPolicy
Eltávolítja a házirendet az alkalmazásból.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |A házirend **objectid** azonosítója. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Egyszerű szolgáltatásházirendek
A következő parancsmagokat az egyszerű szolgáltatásházirendekhez használhatja.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
A megadott házirendet egy egyszerű szolgáltatáshoz kapcsolja.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |A házirend **objectid** azonosítója. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Letöltő minden szabályzat kapcsolódik a megadott szolgáltatásnév.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Eltávolítás-AzureADServicePrincipalPolicy
Eltávolítja a házirendet a megadott egyszerű szolgáltatásból.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Paraméterek | Leírás | Példa |
| --- | --- | --- |
| <code>&#8209;Id</code> |Az alkalmazás **ObjectID azonosítója (ID).** | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |A házirend **objectid** azonosítója. | `-PolicyId <ObjectId of Policy>` |
