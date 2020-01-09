---
title: Hitelesítés a Microsoft Identity platformon | Azure
description: A hitelesítés alapjai a Microsoft Identity platformon (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb09228b561718819c0c48aac4fc66d8205fe384
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424611"
---
# <a name="authentication-basics"></a>Hitelesítési alapok

## <a name="what-is-authentication"></a>Mi az a hitelesítés?

Ez a cikk számos olyan hitelesítési koncepciót ismertet, amelyeknek meg kell ismernie a védett webes API-kat hívó webalkalmazások, webes API-k vagy alkalmazások létrehozásához szükséges tudnivalókat.

A **hitelesítés** az a folyamat, amelynek a bebizonyítása Ön szerint van. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

Az **Engedélyezés** a hitelesített fél engedélyének megadására irányuló művelet. Itt adhatja meg, hogy milyen adatelérési lehetőségekkel férhet hozzá, és hogy mit tehet az adott adattal. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

Ahelyett, hogy olyan alkalmazásokat hozna létre, amelyek mindegyike megtartja a saját felhasználónevét és jelszavát, ami magas adminisztrációs terhet jelent, ha több alkalmazásban kell felhasználókat felvennie vagy eltávolítania, az alkalmazások delegálni tudják a felelősséget egy központi identitás-szolgáltatónak.

Azure Active Directory (Azure AD) egy központi identitás-szolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása olyan forgatókönyveket tesz lehetővé, mint például a feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a felhasználó egy adott helyen legyen, a többtényezős hitelesítés használata, valamint a felhasználó egyszeri bejelentkezésének engedélyezése, majd automatikusan Jelentkezzen be az összes olyan webalkalmazásba, amelyek ugyanazt a központi könyvtárat használják. Ez a funkció egyszeri bejelentkezésre (SSO) hivatkozik.

A központosított identitás-szolgáltató még ennél is fontosabb olyan alkalmazások esetében, amelyeken a világon található felhasználók nem feltétlenül jelentkeznek be a vállalati hálózatról. Az Azure AD hitelesíti a felhasználókat, és hozzáférési jogkivonatokat biztosít. A hozzáférési jogkivonat egy engedélyezési kiszolgáló által kiállított biztonsági jogkivonat. A felhasználóval és az alkalmazással kapcsolatos információkat tartalmaz, amelyekhez a jogkivonat készült, amely a webes API-k és más védett erőforrások elérésére használható.

A Microsoft Identity platform leegyszerűsíti az alkalmazás-fejlesztők hitelesítését azáltal, hogy szolgáltatja a szolgáltatásként nyújtott identitást, és támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID Connect szolgáltatást, valamint a különböző rendszerekhez készült nyílt forráskódú kódtárakat platformok, amelyek segítségével gyorsan elindíthatja a kódolást. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg a Microsoft Graphot, más Microsoft API-kat vagy olyan API-kat, amelyeket fejlesztők készítettek. További információ: [a Microsoft Identity platform fejlődése](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Bérlők

A Felhőbeli identitás-szolgáltató számos szervezetet szolgál ki. Ha az Azure AD-t a különböző szervezetek felhasználói számára külön szeretné megőrizni, a bérlők egy-egy Bérlővel vannak particionálva.

A bérlők nyomon követhetik a felhasználókat és az azokhoz kapcsolódó alkalmazásokat. A Microsoft Identity platform olyan felhasználókat is támogat, akik személyes Microsoft-fiókkal jelentkeznek be.

Az Azure AD Azure Active Directory B2C is biztosít, hogy a szervezetek a felhasználók, jellemzően az ügyfelek számára is bejelentkezhetnek a közösségi identitások, például a Google-fiók használatával. További információ: [Azure Active Directory B2C dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Biztonsági jogkivonatok

A biztonsági tokenek a felhasználókkal és az alkalmazásokkal kapcsolatos információkat tartalmaznak. Az Azure AD JSON-alapú jogkivonatokat (JWTs) használ, amelyek jogcímeket tartalmaznak. A jogcím az egyik entitásról egy másikra vonatkozó kijelentéseket biztosít. Az alkalmazások különféle feladatokhoz használhatják a jogcímeket, például:

* A jogkivonat ellenőrzése
* A tulajdonos címtár-bérlőjának azonosítása
* Felhasználói adatok megjelenítése
* A tulajdonos hitelesítésének meghatározása

A jogcím kulcs-érték párokból áll, amelyek olyan információkat biztosítanak, mint például a következők:

- a jogkivonatot létrehozó biztonsági jogkivonat-kiszolgáló.
- a jogkivonat létrehozásának dátuma.
- a tárgy, például a felhasználó (a démonok kivételével).
- a célközönség, amely az az alkalmazás, amelyhez a jogkivonat létrejött.
- az alkalmazás (a-ügyfél), amelyik a tokent kérte. A webalkalmazások esetében ez lehet ugyanaz, mint a célközönség.

További részletekért tekintse meg a [hozzáférési jogkivonatokat](access-tokens.md) és az [azonosító jogkivonatokat](id-tokens.md).

Ez az alkalmazás, amelyhez a jogkivonat létrejött, a felhasználó által bejelentkezett webalkalmazást vagy a meghívott webes API-t a jogkivonat érvényesítéséhez. A tokent a biztonsági jogkivonat-kiszolgáló (STS) írja alá titkos kulccsal. Az STS közzéteszi a megfelelő nyilvános kulcsot. A jogkivonat érvényesítéséhez az alkalmazás ellenőrzi az aláírást az STS nyilvános kulcsának használatával annak ellenőrzéséhez, hogy az aláírás a titkos kulccsal lett-e létrehozva.

A tokenek csak korlátozott időtartamra érvényesek. Az STS általában egy pár tokent biztosít: egy hozzáférési jogkivonatot, amely hozzáfér az alkalmazáshoz vagy a védett erőforráshoz, valamint egy frissítési tokent, amely a hozzáférési jogkivonat frissítésére szolgál, amikor a hozzáférési jogkivonat lezárult. 

Hozzáférési jogkivonatok átadása egy webes API-nak a `Authorization` fejléc tulajdonosi jogkivonata. Egy alkalmazás frissítési tokent biztosíthat az STS számára, és ha az alkalmazáshoz való hozzáférés nem lett visszavonva, egy új hozzáférési tokent és egy új frissítési jogkivonatot fog kapni. Így történik a vállalatot elhagyó személy forgatókönyvének kezelése. Ha az STS megkapja a frissítési jogkivonatot, nem ad ki másik érvényes hozzáférési jogkivonatot, ha a felhasználó már nem rendelkezik jogosultsággal.

## <a name="application-model"></a>Alkalmazásmodell

Az alkalmazások maguk is bejelentkezhetnek a felhasználókba, vagy delegálhatja a bejelentkezést egy identitás-szolgáltatóhoz. Az Azure AD által támogatott bejelentkezési forgatókönyvek megismeréséhez tekintse meg a [hitelesítési folyamatokat és az alkalmazás forgatókönyveit](authentication-flows-app-scenarios.md) .

Ahhoz, hogy egy identitás-szolgáltató tudja, hogy egy felhasználó hozzáfér egy adott alkalmazáshoz, a felhasználónak és az alkalmazásnak is regisztrálva kell lennie az identitás-szolgáltatónál. Ha az Azure AD-vel regisztrálja az alkalmazást, olyan identitás-konfigurációt biztosít az alkalmazáshoz, amely lehetővé teszi, hogy integrálható legyen az Azure AD-vel. Az alkalmazás regisztrálása a következőket is lehetővé teszi:

- szabja testre az alkalmazás arculatát a bejelentkezési párbeszédablakban. Ez azért fontos, mert ez az első olyan felhasználói élmény, amelyet a felhasználó az alkalmazással fog rendelkezni.
- Döntse el, hogy csak akkor szeretné-e bejelentkezni a felhasználók számára, ha azok a szervezethez tartoznak. Ez egyetlen bérlői alkalmazás. Vagy bármely munkahelyi vagy iskolai fiókkal való bejelentkezés engedélyezése a felhasználók számára. Ez egy több-bérlős alkalmazás. A személyes Microsoft-fiókokat, illetve a LinkedIn, a Google és egyéb közösségi fiókokat is lehetővé teheti.
- hatókör-engedélyek kérése. Kérheti például a "user. Read" hatókört, amely engedélyt ad a bejelentkezett felhasználó profiljának olvasásához.
- adja meg a webes API-hoz való hozzáférést meghatározó hatóköröket. Általában, amikor egy alkalmazás szeretne hozzáférni az API-hoz, engedélyt kell kérnie az Ön által meghatározott hatókörökre.
- Ossza meg az Azure ad-val egy titkos kulcsot, amely igazolja az alkalmazás identitását az Azure AD-nek.  Ez abban az esetben fontos, ha az alkalmazás bizalmas ügyfélalkalmazás. A bizalmas ügyfélalkalmazások olyan alkalmazások, amelyek biztonságosan tárolhatják a hitelesítő adatokat. A hitelesítő adatok tárolásához megbízható háttér-kiszolgálót igényelnek.

A regisztrálás után az alkalmazás egy GUID azonosítót kap, amelyet az alkalmazás az Azure AD-val oszt meg, amikor jogkivonatokat kér. Ha az alkalmazás egy bizalmas ügyfélalkalmazás, akkor a titkos kulcsot vagy a nyilvános kulcsot is megosztja, attól függően, hogy a rendszer a tanúsítványokat és a titkokat használta-e.

A Microsoft Identity platform a két fő funkciót teljesítő modellt használó alkalmazásokat jelöli:

Azonosítsa az alkalmazást az általa támogatott hitelesítési protokollok alapján, és adja meg a hitelesítéshez szükséges összes azonosítót, URL-címet, titkot és kapcsolódó információt.
A Microsoft Identity platform:

* A futtatáskor a hitelesítés támogatásához szükséges összes adattal rendelkezik.
* Az összes adat megtartásával döntheti el, hogy az alkalmazás milyen erőforrásokhoz férhet hozzá, és milyen esetekben kell teljesítenie az adott kérést.
* Infrastruktúrát biztosít az alkalmazások kiépítésének megvalósításához az alkalmazás fejlesztői bérlője és bármely más Azure AD-bérlő között.
* Kezeli a felhasználói hozzájárulásukat a jogkivonat-kérelmek ideje alatt, és megkönnyíti a bérlők közötti alkalmazások dinamikus kiépítés

A jóváhagyás az a folyamat, amelynek során az erőforrás-tulajdonos engedélyt ad egy ügyfélalkalmazás számára a védett erőforrások eléréséhez, az adott engedélyek alatt az erőforrás tulajdonosának nevében. A Microsoft Identity platform:

* Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
* Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

Az **Application Object** a Microsoft Identity platformon absztrakt entitásként írja le az alkalmazást. A központi telepítés ideje alatt a Microsoft Identity platform az Application objektumot használja tervrajzként egy **egyszerű szolgáltatásnév**létrehozásához, amely egy adott alkalmazás konkrét példányát jelöli a címtárban vagy a bérlőn belül. Az egyszerű szolgáltatásnév azt határozza meg, hogy az alkalmazás mit tud valójában egy adott címtárban, ki használhatja azt, milyen erőforrásokhoz férhet hozzá, és így tovább. A Microsoft Identity platform egy egyszerű szolgáltatásnevet hoz létre egy alkalmazás-objektumból a **beleegyező**módon.

Az alábbi ábrán egy egyszerűsített Microsoft Identity platform kiépítési folyamata látható. Két bérlőt mutat be (A és B). A bérlő az alkalmazás tulajdonosa. A B bérlő az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. A Microsoft Identity platform az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben.
1. A felhasználó megkapja a kért jogkivonatot.

Ezt a folyamatot további bérlők esetében is megismételheti. Az A bérlő megőrzi az alkalmazás tervét (Application Object). Az összes többi bérlő felhasználói és rendszergazdái, akikkel az alkalmazás beleegyezik, folyamatosan szabályozhatja, hogy az alkalmazás milyen műveleteket végezhet el az egyes bérlők megfelelő egyszerű szolgáltatásán keresztül. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Webalkalmazás-bejelentkezési folyamat az Azure AD-vel

Amikor egy felhasználó egy webalkalmazáshoz navigál a böngészőben, a következők történnek:

- A webalkalmazás meghatározza, hogy a felhasználó hitelesítése megtörtént-e.
- Ha a felhasználó nincs hitelesítve, a webalkalmazás delegálja az Azure AD-t a felhasználónak való bejelentkezéshez. A bejelentkezés megfelel a szervezet házirendjének, ami azt jelenti, hogy a felhasználónak meg kell adnia a hitelesítő adatait a többtényezős hitelesítés használatával, vagy egyáltalán nem kell jelszót használnia (például a Windows Hello használatával).
- A felhasználónak meg kell adnia a hozzáférést az ügyfélalkalmazás által igényelt hozzáféréshez. Ezért kell regisztrálni az ügyfélalkalmazások számára az Azure AD-t, hogy az Azure AD olyan jogkivonatokat nyújtson, amelyeknek a felhasználó beleegyezett a hozzáférésbe.

A felhasználó sikeres hitelesítése után:

- Az Azure AD jogkivonatot küld a webalkalmazásnak.
- A rendszer menti a cookie-t az Azure AD tartományához társítva, amely a felhasználó identitását tartalmazza a böngésző cookie jar-ban. Amikor az alkalmazás legközelebb megnyitja az Azure AD-engedélyezési végpontot, a böngésző megjeleníti a cookie-t, így a felhasználónak nem kell újra bejelentkeznie. Ez azt is lehetővé teszi, hogy az egyszeri bejelentkezés elérhető legyen. A cookie-t az Azure AD állítja elő, és csak az Azure AD értelmezhető.
- A webalkalmazás ezután érvényesíti a jogkivonatot. Ha az ellenőrzés sikeres, a webalkalmazás megjeleníti a védett lapot, és menti a munkamenet-cookie-t a böngésző cookie jar-fájljában. Ha a felhasználó egy másik oldalra navigál, a webalkalmazás tudja, hogy a felhasználó a munkamenet-cookie alapján van hitelesítve.

A következő adatsor összefoglalja ezt az interakciót:

![webalkalmazás-hitelesítési folyamat](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>A webalkalmazások meghatározása, hogy a felhasználó hitelesítése megtörtént-e

A webalkalmazás-fejlesztők jelezhetik, hogy az összes vagy csak bizonyos lapok hitelesítést igényelnek-e. Az ASP.NET/ASP.NET Core-ban például ezt a `[Authorize]` attribútumnak a vezérlő műveleteihez való hozzáadásával teheti meg. 

Ez az attribútum azt eredményezi, hogy a ASP.NET a felhasználó identitását tartalmazó munkamenet-cookie jelenlétét vizsgálja. Ha a cookie nincs jelen, a ASP.NET átirányítja a hitelesítést a megadott identitás-szolgáltatóhoz. Ha az Identitáskezelő az Azure AD, a webalkalmazás átirányítja a hitelesítést https://login.microsoftonline.com re, amely megjeleníti a bejelentkezési párbeszédpanelt.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Hogyan delegál egy webalkalmazás az Azure AD-ba való bejelentkezést, és jogkivonatot szerez be

A felhasználó hitelesítése a böngészőn keresztül történik. Az OpenID protokoll szabványos HTTP protokoll-üzeneteket használ.
- A webalkalmazás HTTP 302 (átirányítás) üzenetet küld a böngészőnek az Azure AD használatához.
- A felhasználó hitelesítése után az Azure AD elküldi a jogkivonatot a webalkalmazásnak a böngészőn keresztüli átirányítás használatával.
- Az átirányítást a webalkalmazás egy átirányítási URI formájában kapja meg. Ez az átirányítási URI regisztrálva van az Azure AD Application objektumban. Több átirányítási URI is lehet, mert az alkalmazás több URL-címen is üzembe helyezhető. Így a webalkalmazásnak is meg kell adnia a használandó átirányítási URi-t.
- Az Azure AD ellenőrzi, hogy a webalkalmazás által eljuttatott átirányítási URI a regisztrált átirányítási URI-k egyike az alkalmazáshoz.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Asztali és mobil alkalmazások bejelentkezési folyamata az Azure AD-vel

A fent ismertetett folyamat enyhe eltérésekkel, asztali és mobil alkalmazásokkal is foglalkozik.

Az asztali és a mobil alkalmazások a hitelesítéshez használhatnak beágyazott webes vezérlőt vagy rendszerböngészőt. Az alábbi ábra azt szemlélteti, hogy egy asztali vagy mobil alkalmazás hogyan használja a Microsoft Authentication Library (MSAL) szolgáltatást a hozzáférési jogkivonatok beszerzéséhez és a webes API-k meghívásához.

![Az asztali alkalmazás megjelenése](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL egy böngésző használatával kapja meg a jogkivonatokat, és a webalkalmazásokhoz hasonlóan az Azure AD-be delegálja a hitelesítést.

Mivel az Azure AD ugyanazt az identitás-cookie-t menti a böngészőben, mint a webalkalmazások esetében, ha a natív vagy a Mobile alkalmazás a rendszerböngészőt használja, a rendszer azonnal beolvassa az SSO-t a megfelelő webalkalmazással.

Alapértelmezés szerint a MSAL a rendszerböngészőt használja, kivéve a .NET-keretrendszer asztali alkalmazásaiban, ahol egy beágyazott vezérlőt használnak az integráltabb felhasználói élmény biztosításához.

## <a name="next-steps"></a>Következő lépések

- A gyakori feltételek megismeréséhez tekintse meg a [Microsoft Identity platform fejlesztői szószedetét](developer-glossary.md) .
- A Microsoft Identity platform által támogatott felhasználók hitelesítésével kapcsolatos egyéb forgatókönyvekről további információt a [hitelesítési folyamatok és az alkalmazások forgatókönyvei](authentication-flows-app-scenarios.md) című témakörben talál.
- A Microsoft-fiókokkal, az Azure AD-fiókokkal és a Azure AD B2C felhasználókkal együttműködve olyan alkalmazásokat fejleszthet a [MSAL-tárakkal](msal-overview.md) , amelyek segítségével egyetlen, áramvonalas programozási modellben dolgozhat.
- A App Service alkalmazás hitelesítésének konfigurálásával kapcsolatos további információkért lásd: [integráció app Service a Microsfot Identity platformmal](/azure/app-service/configure-authentication-provider-aad) .
