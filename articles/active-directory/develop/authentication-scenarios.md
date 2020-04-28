---
title: Hitelesítés a Microsoft Identity platformon | Azure
description: Tudnivalók a Microsoft Identity platform hitelesítésének alapjairól (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161741"
---
# <a name="authentication-basics"></a>Hitelesítési alapok

Ez a cikk számos olyan hitelesítési koncepciót ismertet, amelyeknek meg kell ismernie a védett webes API-kat hívó webalkalmazások, webes API-k vagy alkalmazások létrehozásához szükséges tudnivalókat. Ha úgy látja, hogy nem ismeri a kifejezést, próbálja ki a [szószedetet](developer-glossary.md) vagy a [Microsoft Identity platform videóit](identity-videos.md) , amelyek az alapfogalmakat fedik le.

## <a name="authentication-vs-authorization"></a>Hitelesítés és engedélyezés

A **hitelesítés** az a folyamat, amelynek a bebizonyítása Ön szerint van. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OpenID Connect](https://openid.net/connect/) protokollt a hitelesítés kezelésére.

Az **Engedélyezés** a hitelesített fél engedélyének megadására irányuló művelet. Itt adhatja meg, hogy milyen adatelérési lehetőségekkel férhet hozzá, és hogy mit tehet az adott adattal. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OAuth 2,0](https://oauth.net/2/) protokollt az engedélyezés kezelésére.

Ahelyett, hogy olyan alkalmazásokat hozna létre, amelyek mindegyike megtartja a saját felhasználónevét és jelszavát, ami magas adminisztrációs terhet jelent, ha több alkalmazásban kell felhasználókat felvennie vagy eltávolítania, az alkalmazások delegálni tudják a felelősséget egy központi identitás-szolgáltatónak.

Azure Active Directory (Azure AD) egy központi identitás-szolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása olyan forgatókönyveket tesz lehetővé, mint például a feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a felhasználó egy adott helyen legyen, a többtényezős hitelesítés használata, valamint a felhasználó egyszeri bejelentkezésének engedélyezése, majd automatikusan bejelentkezzen az összes olyan webalkalmazásba, amely ugyanazzal a központi címtárral rendelkezik. Ez a funkció **egyszeri bejelentkezésre (SSO)** hivatkozik.

A Microsoft Identity platform leegyszerűsíti az alkalmazások fejlesztőinek hitelesítését és engedélyezését azáltal, hogy szolgáltatásként szolgáltatja a szolgáltatást, és támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID Connect használatát, valamint a különböző platformokhoz készült nyílt forráskódú kódtárakat, amelyek segítségével gyorsan elkezdheti a kódolást. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek az összes Microsoft-identitást bejelentkeznek, a [Microsoft Graph](https://developer.microsoft.com/graph/), más Microsoft API-k vagy a fejlesztők által készített API-k meghívására. További információ: [a Microsoft Identity platform fejlődése](about-microsoft-identity-platform.md).

## <a name="security-tokens"></a>Biztonsági jogkivonatok

A központosított identitás-szolgáltató különösen fontos azon alkalmazások esetében, amelyeken a világon található felhasználók nem feltétlenül jelentkeznek be a vállalati hálózatról. A Microsoft Identity platform hitelesíti a felhasználókat, és biztonsági jogkivonatokat biztosít, például [hozzáférési tokent](developer-glossary.md#access-token), [frissítési tokent](developer-glossary.md#refresh-token)és [azonosító](developer-glossary.md#id-token)jogkivonatokat, amelyek lehetővé teszik az [ügyfélalkalmazás](developer-glossary.md#client-application) számára a védett erőforrások elérését egy [erőforrás-kiszolgálón](developer-glossary.md#resource-server).

A **hozzáférési** jogkivonatok egy engedélyezési kiszolgáló által kiadott biztonsági jogkivonat. A felhasználóval és az alkalmazással kapcsolatos információkat tartalmaz, amelyekhez a jogkivonat készült. a webes API-k és más védett erőforrások elérésére használható. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan bocsát ki hozzáférési jogkivonatokat, tekintse meg a [hozzáférési jogkivonatokat](access-tokens.md).

A hozzáférési jogkivonatok csak rövid ideig érvényesek, ezért az engedélyezési kiszolgálók időnként kiállítanak egy **frissítési jogkivonatot** a hozzáférési jogkivonat kiadásakor. Az ügyfélalkalmazás ezután szükség esetén átadhatja ezt a frissítési tokent egy új hozzáférési jogkivonathoz. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan használja a frissítési jogkivonatokat az engedélyek visszavonására, lásd: [jogkivonat-visszavonás](access-tokens.md#token-revocation)

Az **azonosító jogkivonatokat** az ügyfélalkalmazás az [OpenID Connect](v2-protocols-oidc.md) folyamat részeként elküldi az alkalmazásnak. A felhasználók az oldalon vagy a hozzáférési jogkivonat helyett is elküldhetők, és az ügyfél a felhasználó hitelesítésére használja. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan okoz problémát azonosító jogkivonatokat, tekintse meg az [azonosító jogkivonatokat](id-tokens.md).

### <a name="validating-security-tokens"></a>Biztonsági jogkivonatok ellenőrzése

Ez az alkalmazás, amelyhez a jogkivonat létrejött, a felhasználó által bejelentkezett webalkalmazást vagy a meghívott webes API-t a jogkivonat érvényesítéséhez. A tokent a biztonsági jogkivonat-kiszolgáló (STS) írja alá titkos kulccsal. Az STS közzéteszi a megfelelő nyilvános kulcsot. A jogkivonat érvényesítéséhez az alkalmazás ellenőrzi az aláírást az STS nyilvános kulcsának használatával annak ellenőrzéséhez, hogy az aláírás a titkos kulccsal lett-e létrehozva.

A tokenek csak korlátozott időtartamra érvényesek. Az STS általában egy pár tokent biztosít:

* Hozzáférési token az alkalmazáshoz vagy a védett erőforráshoz való hozzáféréshez, valamint
* A hozzáférési jogkivonat frissítéséhez használt frissítési jogkivonat, ha a hozzáférési jogkivonat lejár.

Hozzáférési jogkivonatok átadása egy webes API-nak a `Authorization` fejlécben szereplő tulajdonosi jogkivonatként. Egy alkalmazás frissítési tokent biztosíthat az STS számára, és ha az alkalmazáshoz való hozzáférés nem lett visszavonva, egy új hozzáférési tokent és egy új frissítési jogkivonatot fog kapni. Így történik a vállalatot elhagyó személy forgatókönyvének kezelése. Ha az STS megkapja a frissítési jogkivonatot, nem ad ki másik érvényes hozzáférési jogkivonatot, ha a felhasználó már nem rendelkezik jogosultsággal.

### <a name="json-web-tokens-jwts-and-claims"></a>JSON webes tokenek (JWTs) és jogcímek

A Microsoft Identity platform a jogcímeket tartalmazó JSON webes tokenként (JWTs) implementálja a biztonsági jogkivonatokat.

A [jogcím](developer-glossary.md#claim) egy entitásra, például egy ügyfélalkalmazás vagy [erőforrás-tulajdonosra](developer-glossary.md#resource-owner)vonatkozó kijelentéseket biztosít egy másik entitáshoz, például egy erőforrás-kiszolgálóhoz.

A jogcímek olyan név/érték párok, amelyek a jogkivonat tárgyával kapcsolatos adatokat továbbítanak. Előfordulhat például, hogy a jogcím az [engedélyezési kiszolgáló](developer-glossary.md#authorization-server)által hitelesített rendszerbiztonsági tag tényeit is tartalmazza. Az adott jogkivonatban található jogcímek számos dologtól függenek, beleértve a token típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatokat, az alkalmazás konfigurációját stb.

Az alkalmazások különféle feladatokhoz használhatják a jogcímeket, például:

* A jogkivonat ellenőrzése
* A token tulajdonos bérlőjának azonosítása
* Felhasználói adatok megjelenítése
* A tulajdonos hitelesítésének meghatározása

A jogcím kulcs-érték párokból áll, amelyek olyan információkat biztosítanak, mint például a:

* A jogkivonatot létrehozó biztonsági jogkivonat-kiszolgáló
* A jogkivonat létrehozásának dátuma
* Tárgy (például a felhasználó – a démonok kivételével)
* A célközönség, amely az az alkalmazás, amelyhez a jogkivonat létrejött
* Az alkalmazás (a-ügyfél), amely a tokent kéri. A webalkalmazások esetében ez lehet ugyanaz, mint a célközönség

Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan valósítja meg a jogkivonatokat és a jogcímeket, tekintse meg a [hozzáférési jogkivonatok](access-tokens.md) és [azonosító jogkivonatok](id-tokens.md)

### <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Az ügyfél felépítésének módjától függően a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy (vagy több) is használható. Ezek a folyamatok különféle tokeneket (id_tokens, frissítési tokeneket, hozzáférési tokeneket) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek a működésük érdekében. Ez a diagram áttekintést nyújt:

|Folyamat | Igényel | id_token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód |
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |

Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-cím (ahol `response_mode` a `query` vagy `fragment`a) a böngészőbe kerül vissza.  Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímek.

## <a name="tenants"></a>Bérlők

A Felhőbeli identitás-szolgáltató számos szervezetet szolgál ki. Ha az Azure AD-t a különböző szervezetek felhasználói számára külön szeretné megőrizni, a bérlők egy-egy Bérlővel vannak particionálva.

A bérlők nyomon követhetik a felhasználókat és az azokhoz kapcsolódó alkalmazásokat. A Microsoft Identity platform olyan felhasználókat is támogat, akik személyes Microsoft-fiókkal jelentkeznek be.

Az Azure AD Azure Active Directory B2C is biztosít, hogy a szervezetek a felhasználók, jellemzően az ügyfelek számára is bejelentkezhetnek a közösségi identitások, például a Google-fiók használatával. További információ: [Azure Active Directory B2C dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c) .

Most, hogy áttekintette az alapismereteket, olvassa el a következőt: az Identity app Model és az API megismerése, a kiépítés működése a Microsoft Identity platformon, valamint hivatkozások a Microsoft Identity platform által támogatott gyakori forgatókönyvekre vonatkozó részletes információkra.

## <a name="application-model"></a>Alkalmazásmodell

Az alkalmazások maguk is bejelentkezhetnek a felhasználókba, vagy delegálhatja a bejelentkezést egy identitás-szolgáltatóhoz. A Microsoft Identity platform által támogatott bejelentkezési forgatókönyvek megismeréséhez tekintse meg a [hitelesítési folyamatokat és az alkalmazás forgatókönyveit](authentication-flows-app-scenarios.md) .

Ahhoz, hogy egy identitás-szolgáltató tudja, hogy egy felhasználó hozzáfér egy adott alkalmazáshoz, a felhasználónak és az alkalmazásnak is regisztrálva kell lennie az identitás-szolgáltatónál. Ha az Azure AD-vel regisztrálja az alkalmazást, olyan identitás-konfigurációt biztosít az alkalmazáshoz, amely lehetővé teszi, hogy integrálható legyen a Microsoft Identity platformmal. Az alkalmazás regisztrálása a következőket is lehetővé teszi:

* Szabja testre az alkalmazás arculatát a bejelentkezési párbeszédablakban. Ez azért fontos, mert ez az első olyan felhasználói élmény, amelyet a felhasználó az alkalmazással fog rendelkezni.
* Döntse el, hogy csak akkor szeretné-e bejelentkezni a felhasználók számára, ha azok a szervezethez tartoznak. Ez egyetlen bérlői alkalmazás. Vagy bármely munkahelyi vagy iskolai fiókkal való bejelentkezés engedélyezése a felhasználók számára. Ez egy több-bérlős alkalmazás. A személyes Microsoft-fiókokat, illetve a LinkedIn, a Google és egyéb közösségi fiókokat is lehetővé teheti.
* Hatókör-engedélyek kérése. Kérheti például a "user. Read" hatókört, amely engedélyt ad a bejelentkezett felhasználó profiljának olvasásához.
* Adja meg a webes API-hoz való hozzáférést meghatározó hatóköröket. Általában, amikor egy alkalmazás szeretne hozzáférni az API-hoz, engedélyt kell kérnie az Ön által meghatározott hatókörökre.
* Ossza meg a titkos kódot a Microsoft Identity platformmal, amely igazolja az alkalmazás identitását.  Ez abban az esetben fontos, ha az alkalmazás bizalmas ügyfélalkalmazás. A bizalmas ügyfélalkalmazások olyan alkalmazások, amelyek biztonságosan tárolhatják a hitelesítő adatokat. A hitelesítő adatok tárolásához megbízható háttér-kiszolgálót igényelnek.

A regisztrálást követően az alkalmazás egyedi azonosítót kap, amelyet az alkalmazás a Microsoft Identity platformmal oszt meg, amikor jogkivonatokat kér. Ha az alkalmazás egy [bizalmas ügyfélalkalmazás](developer-glossary.md#client-application), akkor a titkos kulcsot vagy a nyilvános kulcsot is megosztja, attól függően, hogy a rendszer a tanúsítványokat és a titkokat használta-e.

A Microsoft Identity platform a két fő funkciót teljesítő modellt használó alkalmazásokat jelöli:

* Azonosítsa az alkalmazást az általa támogatott hitelesítési protokollok alapján
* Adja meg a hitelesítéshez szükséges összes azonosítót, URL-címet, titkot és kapcsolódó információt.

Microsoft Identity platform:

* A hitelesítés futtatásának támogatásához szükséges összes adattal rendelkezik
* Az összes adat megtartásával döntheti el, hogy az alkalmazás milyen erőforrásokhoz férhet hozzá, és milyen esetekben kell teljesítenie egy adott kérést
* Infrastruktúrát biztosít az alkalmazás-kiépítés megvalósításához az alkalmazás fejlesztői bérlője és bármely más Azure AD-bérlő között
* Kezeli a felhasználói hozzájárulásukat a jogkivonat-kérelmek ideje alatt, és megkönnyíti a bérlők közötti alkalmazások dinamikus kiépítés

A jóváhagyás az a folyamat, amelynek során az erőforrás-tulajdonos engedélyt ad egy ügyfélalkalmazás számára a védett erőforrások eléréséhez, az adott engedélyek alatt az erőforrás tulajdonosának nevében. Microsoft Identity platform:

* Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
* Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

A Microsoft Identity platformban az [Application Object](developer-glossary.md#application-object) egy alkalmazást ír le. A központi telepítés ideje alatt a Microsoft Identity platform az Application objektumot használja tervrajzként egy [egyszerű szolgáltatásnév](developer-glossary.md#service-principal-object)létrehozásához, amely egy adott alkalmazás konkrét példányát jelöli a címtárban vagy a bérlőn belül. Az egyszerű szolgáltatásnév azt határozza meg, hogy az alkalmazás mit tud valójában egy adott címtárban, ki használhatja azt, milyen erőforrásokhoz férhet hozzá, és így tovább. A Microsoft Identity platform egy egyszerű szolgáltatásnevet hoz létre egy alkalmazás-objektumból a **beleegyező**módon.

Az alábbi ábrán egy egyszerűsített Microsoft Identity platform kiépítési folyamata látható. Két bérlőt mutat be: *A* és *B*.

* A *bérlő* az alkalmazás tulajdonosa.
* A *B bérlő* az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. A Microsoft Identity platform az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben.
1. A felhasználó megkapja a kért jogkivonatot.

Ezt a folyamatot további bérlők esetében is megismételheti. Az A bérlő megőrzi az alkalmazás tervét (Application Object). Az összes többi bérlő felhasználói és rendszergazdái, akikkel az alkalmazás beleegyezik, folyamatosan szabályozhatja, hogy az alkalmazás milyen műveleteket végezhet el az egyes bérlők megfelelő egyszerű szolgáltatásán keresztül. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Webalkalmazás-bejelentkezési folyamat a Microsoft Identity platformmal

Amikor egy felhasználó egy webalkalmazáshoz navigál a böngészőben, a következők történnek:

* A webalkalmazás meghatározza, hogy a felhasználó hitelesítése megtörtént-e.
* Ha a felhasználó nincs hitelesítve, a webalkalmazás delegálja az Azure AD-t a felhasználónak való bejelentkezéshez. A bejelentkezés megfelel a szervezet házirendjének, ami azt jelenti, hogy a felhasználónak meg kell adnia a hitelesítő adatait a többtényezős hitelesítés használatával, vagy egyáltalán nem kell jelszót használnia (például a Windows Hello használatával).
* A felhasználónak meg kell adnia a hozzáférést az ügyfélalkalmazás által igényelt hozzáféréshez. Ezért kell regisztrálni az ügyfélalkalmazások Azure AD-ben való regisztrálását, hogy a Microsoft Identity platform olyan jogkivonatokat nyújtson, amelyeknek a felhasználó beleegyezett a hozzáférésbe.

A felhasználó sikeres hitelesítése után:

* A Microsoft Identity platform egy jogkivonatot küld a webalkalmazásnak.
* A rendszer menti a cookie-t az Azure AD tartományához társítva, amely a felhasználó identitását tartalmazza a böngésző cookie jar-ban. Amikor az alkalmazás legközelebb megnyitja a Microsoft Identity platform engedélyezési végpontját, a böngésző megjeleníti a cookie-t, így a felhasználónak nem kell újra bejelentkeznie. Ez azt is lehetővé teszi, hogy az egyszeri bejelentkezés elérhető legyen. A cookie-t az Azure AD állítja elő, és csak az Azure AD értelmezhető.
* A webalkalmazás ezután érvényesíti a jogkivonatot. Ha az ellenőrzés sikeres, a webalkalmazás megjeleníti a védett lapot, és menti a munkamenet-cookie-t a böngésző cookie jar-fájljában. Ha a felhasználó egy másik oldalra navigál, a webalkalmazás tudja, hogy a felhasználó a munkamenet-cookie alapján van hitelesítve.

A következő adatsor összefoglalja ezt az interakciót:

![webalkalmazás-hitelesítési folyamat](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>A webalkalmazások meghatározása, hogy a felhasználó hitelesítése megtörtént-e

A webalkalmazás-fejlesztők jelezhetik, hogy az összes vagy csak bizonyos lapok hitelesítést igényelnek-e. Az ASP.NET/ASP.NET Core-ban például az `[Authorize]` attribútum a vezérlő műveleteihez való hozzáadásával valósítható meg.

Ez az attribútum azt eredményezi, hogy a ASP.NET a felhasználó identitását tartalmazó munkamenet-cookie jelenlétét vizsgálja. Ha a cookie nincs jelen, a ASP.NET átirányítja a hitelesítést a megadott identitás-szolgáltatóhoz. Ha az identitás szolgáltatója Azure AD, a webalkalmazás átirányítja a hitelesítést `https://login.microsoftonline.com`a szolgáltatásba, amely megjeleníti a bejelentkezési párbeszédpanelt.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>A webalkalmazások bejelentkeznek a Microsoft Identity platformba, és jogkivonatot szereznek be

A felhasználó hitelesítése a böngészőn keresztül történik. Az OpenID protokoll szabványos HTTP protokoll-üzeneteket használ.

* A webalkalmazás a Microsoft Identity platform használatához HTTP 302 (átirányítás) üzenetet küld a böngészőnek.
* A felhasználó hitelesítése után a Microsoft Identity platform elküldi a jogkivonatot a webalkalmazásnak a böngészőn keresztüli átirányítás használatával.
* Az átirányítást a webalkalmazás egy átirányítási URI formájában kapja meg. Ez az átirányítási URI regisztrálva van az Azure AD Application objektumban. Több átirányítási URI is lehet, mert az alkalmazás több URL-címen is üzembe helyezhető. Így a webalkalmazásnak is meg kell adnia a használandó átirányítási URI-t.
* Az Azure AD ellenőrzi, hogy a webalkalmazás által eljuttatott átirányítási URI a regisztrált átirányítási URI-k egyike az alkalmazáshoz.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Asztali és mobil alkalmazások bejelentkezési folyamata a Microsoft Identity platformmal

A fent ismertetett folyamat enyhe eltérésekkel, asztali és mobil alkalmazásokkal is foglalkozik.

Az asztali és a mobil alkalmazások a hitelesítéshez használhatnak beágyazott webes vezérlőt vagy rendszerböngészőt. Az alábbi ábra azt szemlélteti, hogy egy asztali vagy mobil alkalmazás hogyan használja a Microsoft Authentication Library (MSAL) szolgáltatást a hozzáférési jogkivonatok beszerzéséhez és a webes API-k meghívásához.

![Az asztali alkalmazás megjelenése](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL egy böngészőt használ a tokenek lekéréséhez. Akárcsak a Web Apps esetében, a hitelesítés a Microsoft Identity platformra van delegálva.

Mivel az Azure AD ugyanazt az identitás-cookie-t menti a böngészőben, mint a webalkalmazások esetében, ha a natív vagy a Mobile alkalmazás a rendszerböngészőt használja, a rendszer azonnal beolvassa az SSO-t a megfelelő webalkalmazással.

Alapértelmezés szerint a MSAL a rendszerböngészőt használja. A kivétel a .NET-keretrendszer asztali alkalmazásai, amelyekben egy beágyazott vezérlőt használunk az integráltabb felhasználói élmény biztosításához.

## <a name="next-steps"></a>További lépések

* A gyakori feltételek megismeréséhez tekintse meg a [Microsoft Identity platform fejlesztői szószedetét](developer-glossary.md) .
* További információ a Microsoft Identity platform által támogatott felhasználók hitelesítéséhez használható egyéb forgatókönyvekről: [hitelesítési folyamatok és alkalmazási forgatókönyvek](authentication-flows-app-scenarios.md) .
* A Microsoft-fiókokkal, az Azure AD-fiókokkal és a Azure AD B2C felhasználókkal együttműködve olyan alkalmazásokat fejleszthet a [MSAL-tárakkal](msal-overview.md) , amelyek segítségével egyetlen, áramvonalas programozási modellben dolgozhat.
* A App Service alkalmazás hitelesítésének konfigurálásáról további információt a [app Service integrálása a Microsoft Identity platformmal](/azure/app-service/configure-authentication-provider-aad) című témakörben talál.
