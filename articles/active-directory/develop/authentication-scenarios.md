---
title: Hitelesítés a Microsoft identitásplatformján | Azure
description: Ismerje meg a hitelesítés alapjait a Microsoft identity platformon (2.0-s verzió).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6191e67f097b5ab471c5b31eff11a0e570d1c990
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617029"
---
# <a name="authentication-basics"></a>Hitelesítési alapok

## <a name="what-is-authentication"></a>Mi a hitelesítés?

Ez a cikk számos hitelesítési fogalmat ismerteti, amelyeket meg kell értenie a védett webalkalmazások, webes API-k vagy védett webes API-kat hívó alkalmazások létrehozásához. Ha olyan kifejezést lát, amelyet nem ismer, próbálja ki [szószedetünket](developer-glossary.md) vagy [microsoftos identitásplatform-videóinkat,](identity-videos.md) amelyek az alapvető fogalmakat ismertetik.

**A hitelesítés** annak bizonyítása, hogy az vagy, akinek mondod, az vagy. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

**Az engedélyezés** az a cselekmény, hogy egy hitelesített fél engedélyt ad valamire. Itt adható meg, hogy milyen adatokhoz férhet hozzá, és mit tehet ezekkel az adatokkal. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

Ahelyett, hogy olyan alkalmazásokat hozna létre, amelyek mindegyike saját felhasználónevet és jelszót tart fenn, ami magas felügyeleti terhet jelent, amikor felhasználókat kell hozzáadnia vagy eltávolítania több alkalmazásban, az alkalmazások átruházhatják ezt a felelősséget egy központi identitásszolgáltatóra.

Az Azure Active Directory (Azure AD) egy központi identitásszolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása lehetővé teszi olyan forgatókönyvek használatát, mint például a feltételes hozzáférési házirendek, amelyek megkövetelik, hogy a felhasználó egy adott helyen legyen, a többtényezős hitelesítés használatát, valamint lehetővé teszi a felhasználó számára, hogy egyszer jelentkezzen be, majd automatikusan bejelentkezzen az összes olyan webalkalmazásba, amely ugyanazt a központi könyvtárat használja. Ezt a képességet egyszeri bejelentkezésnek (SSO) nevezik.

A központosított identitásszolgáltató még fontosabb az olyan alkalmazások esetében, amelyek felhasználói a világ minden táján találhatók, és nem feltétlenül jelentkeznek be a vállalat hálózatáról. Az Azure AD hitelesíti a felhasználókat, és hozzáférési jogkivonatokat biztosít. A [hozzáférési jogkivonat](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) egy hitelesítési kiszolgáló által kiadott biztonsági jogkivonat. Információkat tartalmaz a felhasználóról és az alkalmazásról, amelynek a token et szánják; amely a webes API-k és más védett erőforrások elérésére használható.

A Microsoft identity platform leegyszerűsíti az alkalmazásfejlesztők hitelesítését azáltal, hogy az identitást szolgáltatásként biztosítja, és támogatja az olyan iparági szabványnak megfelelő protokollokat, mint az [OAuth 2.0](https://oauth.net/2/) és az [OpenID Connect,](https://openid.net/connect/)valamint a különböző platformok nyílt forráskódú kódtártárkönyvtárait a gyors kódolás megkezdése érdekében. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek minden Microsoft-identitást aláírnak, jogkivonatokat kapnak a [Microsoft Graph](https://developer.microsoft.com/graph/), más Microsoft API-k vagy a fejlesztők által készített API-k hívásához. További információ: [Evolution of Microsoft identity platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Bérlők

A felhőalapú identitásszolgáltató számos szervezetet szolgál ki. A különböző szervezetekből származó felhasználók elkülönítése érdekében az Azure AD bérlőkre van particionálva, szervezetenként egy bérlővel.

A bérlők nyomon követhetik a felhasználókat és a hozzájuk tartozó alkalmazásokat. A Microsoft identity platform támogatja azokat a felhasználókat is, akik személyes Microsoft-fiókkal jelentkeznek be.

Az Azure AD az Azure Active Directory B2C szolgáltatást is biztosítja, így a szervezetek bejelentkezhetnek a felhasználókhoz, általában az ügyfelekhez, akik közösségi identitások, például Google-fiók használatával jelentkezhetnek be. További információ: [Azure Active Directory B2C dokumentáció.](https://docs.microsoft.com/azure/active-directory-b2c)

### <a name="security-tokens"></a>Biztonsági jogkivonatok

A biztonsági jogkivonatok a felhasználókra és az alkalmazásokra vonatkozó információkat tartalmaznak. Az Azure AD json alapú jogkivonatokat (JWT-ket) használ, amelyek jogcímeket tartalmaznak.

A jogcím egy entitással, például egy [ügyfélalkalmazással](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) vagy [az erőforrás tulajdonosával](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)kapcsolatos állításokat biztosít egy másik entitásnak, például egy [erőforráskiszolgálónak.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

A jogcímek olyan név-/értékpárok, amelyek a jogkivonat tárgyával kapcsolatos tényeket továbbítják. Egy jogcím például tartalmazhat az engedélyezési kiszolgáló által hitelesített rendszerbiztonsági [taggal](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server)kapcsolatos tényeket. Egy adott jogkivonatban jelen lévő jogcímek sok mindentől függenek, beleértve a jogkivonat típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatok típusát, az alkalmazás konfigurációját és így tovább.

Az alkalmazások különböző feladatokhoz használhatnak jogcímeket, például:

* A token érvényesítése
* A jogkivonat-tulajdonos bérlőjének azonosítása
* Felhasználói adatok megjelenítése
* Az érintett engedélyének meghatározása

A jogcím kulcsérték-párokból áll, amelyek olyan információkat szolgáltatnak, mint például:

* A jogkivonatot létrehozó biztonsági jogkivonat-kiszolgáló
* A token létrehozásának dátuma
* Tárgy (például a felhasználó - kivéve a démonokat)
* Célközönség, amely az az alkalmazás, amelyhez a token létrejött
* Alkalmazás (az ügyfél), amely a jogkivonatot kérte. A webes alkalmazások esetében ez megegyezhet a

További részletes jogcíminformációkat a [hozzáférési jogkivonatok](access-tokens.md) és [azonosítójogtok című témakörben talál.](id-tokens.md)

Az alkalmazás, amelyhez a jogkivonat ot hozták létre, a webalkalmazás, amely bejelentkezett a felhasználó, vagy a webes API-t hívják, a jogkivonat érvényesítéséhez. A jogkivonatot a biztonsági jogkivonat-kiszolgáló (STS) írja alá egy titkos kulccsal. Az STS közzéteszi a megfelelő nyilvános kulcsot. A jogkivonat érvényesítéséhez az alkalmazás az STS nyilvános kulcs használatával ellenőrzi, hogy az aláírás a személyes kulccsal lett-e létrehozva.

A tokenek csak korlátozott ideig érvényesek. Általában az STS biztosít egy pár jogkivonatot: egy hozzáférési jogkivonatot az alkalmazás vagy a védett erőforrás eléréséhez, és egy frissítési jogkivonatot a hozzáférési jogkivonat frissítéséhez, ha a hozzáférési jogkivonat közel jár le.

A hozzáférési jogkivonatok a `Authorization` fejlécben tulajdonosi jogkivonatként kerülnek átadásra egy webes API-nak. Egy alkalmazás egy frissítési jogkivonatot biztosíthat az STS-nek, és ha a felhasználói hozzáférést az alkalmazáshoz nem vonták vissza, akkor egy új hozzáférési jogkivonatot és egy új frissítési jogkivonatot kap vissza. Így kezelik a vállalatból kilépő személy forgatókönyvét. Amikor az STS megkapja a frissítési jogkivonatot, nem ad ki egy másik érvényes hozzáférési jogkivonatot, ha a felhasználó már nem jogosult.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hogyan bocsát ki az egyes folyamatok jogkivonatokat és kódokat?

Attól függően, hogy az ügyfél épül, használhatja az Azure AD által támogatott hitelesítési folyamatok egy (vagy több) használatát. Ezek a folyamatok különböző jogkivonatokat (id_tokens, frissítési jogkivonatokat, hozzáférési jogkivonatokat) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek, hogy működjenek. Ez a diagram áttekintést nyújt:

|Folyamat | Megköveteli | id_token | hozzáférési jogkivonat | token frissítése | engedélyezési kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamata](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC-folyamat](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Token beváltásának frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazásoknál)| | |

Az implicit módban kibocsátott tokenek hosszkorlátozással rendelkeznek, mivel az URL-en keresztül visszakerülnek a böngészőbe (hol `response_mode` van `query` vagy `fragment`).  Egyes böngészőkben korlátozható az URL mérete, amelyet a böngészősávba lehet helyezni, és ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek, `groups` vagy `wids` jogcímek. 

Most, hogy áttekintést kapott az alapokról, olvassa el az identitásalkalmazás-modell és API megismeréséhez, ismerje meg, hogyan működik az Azure AD-ben a kiépítés, és részletes információkat kaphat az Azure AD által támogatott gyakori forgatókönyvekről.

## <a name="application-model"></a>Alkalmazásmodell

Az alkalmazások maguk is bejelentkezhetnek a felhasználókba, vagy delegálhatnak bejelentkezést egy identitásszolgáltatónak. Tekintse meg [a hitelesítési folyamatok és az alkalmazásforgatókönyvek](authentication-flows-app-scenarios.md) az Azure AD által támogatott bejelentkezési forgatókönyvek megismeréséhez.

Ahhoz, hogy egy identitásszolgáltató tudja, hogy a felhasználó hozzáfér egy adott alkalmazáshoz, mind a felhasználót, mind az alkalmazást regisztrálni kell az identitásszolgáltatónál. Amikor regisztrálja az alkalmazást az Azure AD-vel, olyan identitáskonfigurációt biztosít az alkalmazáshoz, amely lehetővé teszi az Azure AD-vel való integrációt. Az alkalmazás regisztrálása lehetővé teszi a következőket is:

* Az alkalmazás márkajelzésének testreszabása a bejelentkezési párbeszédpanelen. Ez azért fontos, mert ez az első élmény, amelyet a felhasználó az alkalmazással kapcsolatban tapasztal.
* Döntse el, hogy csak akkor szeretné-e beengedni a felhasználókat, ha a szervezethez tartoznak. Ez egy egyetlen bérlői alkalmazás. Vagy engedélyezheti a felhasználóknak, hogy bármilyen munkahelyi vagy iskolai fiókkal jelentkezzenek be. Ez egy több-bérlős alkalmazás. Engedélyezheti a személyes Microsoft-fiókokat, illetve a LinkedIn, a Google és így tovább közösségi fiókokat is.
* Hatókör-engedélyek kérése. Kérheti például a "user.read" hatókört, amely engedélyt ad a bejelentkezett felhasználó profiljának olvasására.
* Adja meg azokat a hatóköröket, amelyek meghatározzák a webes API-hoz való hozzáférést. Általában, ha egy alkalmazás szeretne hozzáférni az API-t, akkor kell engedélyt kérni a megadott hatókörökhöz.
* Osszon meg egy titkos azure AD, amely bizonyítja az alkalmazás identitását az Azure AD.  Ez akkor fontos, ha az alkalmazás bizalmas ügyfélalkalmazás. A bizalmas ügyfélalkalmazás olyan alkalmazás, amely biztonságosan tudja tárolni a hitelesítő adatokat. A hitelesítő adatok tárolásához megbízható háttérkiszolgálóra van szükség.

A regisztrációt követően az alkalmazás kap egy egyedi azonosítót, amely az alkalmazás megosztja az Azure AD-vel, amikor jogkivonatokat kér. Ha az alkalmazás [bizalmas ügyfélalkalmazás,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)akkor a titkos kulcsot vagy a nyilvános kulcsot*is megosztja attól függően, hogy tanúsítványokat vagy titkos kulcsokat használtak-e.

A Microsoft identity platform olyan alkalmazásokat jelöl, amelyek két fő funkciót látnak el:

* Az alkalmazás azonosítása az általa támogatott hitelesítési protokollok alapján
* Adja meg a hitelesítéshez szükséges összes azonosítót, URL-t, titkos titkot és kapcsolódó információt

A Microsoft identitásplatformja:

* A hitelesítés futásidőben való támogatásához szükséges összes adat letartása
* Az összes adatot tartalmazza annak eldöntéséhez, hogy egy alkalmazásnak milyen erőforrásokhoz kell hozzáférnie, és milyen körülmények között kell teljesítenie egy adott kérést.
* Infrastruktúrát biztosít az alkalmazáskiépítés megvalósításához az alkalmazásfejlesztő bérlőjén belül, valamint bármely más Azure AD-bérlő számára
* Kezeli a felhasználói beleegyezést a jogkivonat-kérelem ideje alatt, és megkönnyíti az alkalmazások dinamikus kiépítését a bérlők között

A hozzájárulás az a folyamat, amelynek során az erőforrás-tulajdonos engedélyt ad egy ügyfélalkalmazásnak a védett erőforrásokhoz való hozzáférésre, meghatározott engedélyek mellett, az erőforrás tulajdonosa nevében. A Microsoft identitásplatformja:

* Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
* Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

A Microsoft identity platformon egy [alkalmazásobjektum](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) egy alkalmazást ír le. Üzembe helyezéskor a Microsoft identitásplatform az alkalmazásobjektumot használja egy [egyszerű szolgáltatás](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)létrehozása tervként, amely egy könyvtáron vagy bérlőn belüli alkalmazás konkrét példányát képviseli. A szolgáltatásnév határozza meg, hogy az alkalmazás ténylegesen mit tehet egy adott célkönyvtárban, ki használhatja, milyen erőforrásokhoz fér hozzá, és így tovább. A Microsoft identity platform **hozzájárulással**létrehoz egy egyszerű szolgáltatást egy alkalmazásobjektumból.

Az alábbi ábrán egy egyszerűsített Microsoft-identitásplatform-kiépítési folyamat látható, amelyet hozzájárulás hajt. Azt mutatja, két bérlő: A és B. Bérlő a tulajdonosa az alkalmazás. "B" bérlő egy egyszerű szolgáltatáson keresztül példányosítja ki az alkalmazást.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A b bérlőből származó felhasználó megpróbál bejelentkezni az alkalmazással, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A rendszer beszerzi és ellenőrzi a felhasználói hitelesítő adatokat a hitelesítéshez.
1. A felhasználónak beleegyezését kell adnia ahhoz, hogy az alkalmazás hozzáférjen a B bérlőhöz.
1. A Microsoft identity platform az "A" bérlőben lévő alkalmazásobjektumot használja a B bérlőben egy egyszerű szolgáltatás létrehozásának tervrajzaként.
1. A felhasználó megkapja a kért jogkivonatot.

Ezt a folyamatot további bérlők esetében is megismételheti. "A" bérlő megtartja az alkalmazás (alkalmazásobjektum) tervrajzát. Az összes többi bérlő, ahol az alkalmazás beleegyezését kapja, az összes többi bérlő felhasználói és rendszergazdái szabályozhatja, hogy az alkalmazás mit tehet a megfelelő egyszerű szolgáltatásobjektumon keresztül az egyes bérlőkben. További információt az [Alkalmazás és a szolgáltatás egyszerű objektumai a Microsoft identity platformon című témakörben talál.](app-objects-and-service-principals.md)

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Webalkalmazás-bejelentkezési folyamat az Azure AD-vel

Amikor egy felhasználó a böngészőben egy webalkalmazáshoz navigál, a következő történik:

* A webalkalmazás határozza meg, hogy a felhasználó hitelesítve van-e.
* Ha a felhasználó nincs hitelesítve, a webalkalmazás delegálja az Azure AD-t a felhasználó bejelentkezéséhez. Ez a bejelentkezés megfelel a szervezet házirendjének, ami azt jelentheti, hogy megkéri a felhasználót, hogy adja meg hitelesítő adatait, többtényezős hitelesítést használ, vagy egyáltalán ne használjon jelszót (például a Windows Hello használatával).
* A felhasználónak beleegyezését kell adnia az ügyfélalkalmazásáltal szükséges hozzáféréshez. Ez az oka annak, hogy az ügyfélalkalmazásokat regisztrálni kell az Azure AD-vel, hogy az Azure AD olyan jogkivonatokat tud biztosítani, amelyek a felhasználó által jóváhagyott hozzáférést képviselik.

Ha a felhasználó sikeresen hitelesítette magát:

* Az Azure AD egy jogkivonatot küld a webalkalmazásnak.
* A rendszer menti a cookie-kat, amelyek az Azure AD tartományhoz kapcsolódnak, és amelyek a felhasználó identitását tartalmazzák a böngésző cookie-tasajarjában. A következő alkalommal, amikor egy alkalmazás a böngésző segítségével navigál az Azure AD engedélyezési végpont, a böngésző bemutatja a cookie-t, hogy a felhasználónak nem kell újra bejelentkezni. Az SSO-k így is elérhetők. A cookie-t az Azure AD állítja elő, és csak az Azure AD értheti.
* A webalkalmazás ezután érvényesíti a jogkivonatot. Ha az ellenőrzés sikeres, a webalkalmazás megjeleníti a védett oldalt, és egy munkamenet-cookie-t ment a böngésző cookie-dobozába. Amikor a felhasználó egy másik oldalra navigál, a webalkalmazás tudja, hogy a felhasználó hitelesítése a munkamenet-cookie alapján történik.

A következő szekvenciadiagram összefoglalja ezt a kölcsönhatást:

![webalkalmazás hitelesítési folyamata](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hogyan határozza meg egy webalkalmazás, hogy a felhasználó hitelesítve van-e?

A webalkalmazás-fejlesztők jelezhetik, hogy az összes vagy csak bizonyos lapok hitelesítést igényelnek-e. A Core ASP.NET/ASP.NET például ezt úgy `[Authorize]` lehet megtenni, hogy hozzáadjuk az attribútumot a vezérlőműveletekhez. 

Ez az attribútum hatására ASP.NET ellenőrzi, hogy a felhasználó identitását tartalmazó munkamenet-cookie jelen van-e. Ha nincs cookie, ASP.NET átirányítja a hitelesítést a megadott identitásszolgáltatóra. Ha az identitásszolgáltató az Azure AD, a webalkalmazás átirányítja a hitelesítést a rendszerbe, `https://login.microsoftonline.com`amely egy bejelentkezési párbeszédpanelt jelenít meg.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Hogyan delegál egy webalkalmazás az Azure AD-be való bejelentkezést, és hogyan szerez be egy jogkivonatot?

A felhasználói hitelesítés a böngészőn keresztül történik. Az OpenID protokoll szabványos HTTP protokollüzeneteket használ.
* A webalkalmazás http 302-es (átirányítás) küld a böngészőnek az Azure AD használatához.
* A felhasználó hitelesítése után az Azure AD elküldi a jogkivonatot a webalkalmazás nak egy átirányítás a böngészőn keresztül.
* Az átirányítást a webalkalmazás biztosítja átirányítási URI formájában. Ez az átirányítási URI regisztrálva van az Azure AD alkalmazásobjektummal. Több átirányítási URI-k is lehetnek, mert az alkalmazás több URL-en is telepíthető. Így a webalkalmazás nak is meg kell adnia a használni kívánt átirányítási URI-t.
* Az Azure AD ellenőrzi, hogy a webalkalmazás által küldött átirányítási URI az alkalmazás regisztrált átirányítási URI-k egyike.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Asztali és mobilalkalmazás-bejelentkezési folyamat az Azure AD-vel

A fent leírt folyamat kis eltérésekkel vonatkozik az asztali és mobil alkalmazásokra.

Az asztali és mobilalkalmazások használhatnak beágyazott webes vezérlőt vagy rendszerböngészőt a hitelesítéshez. Az alábbi ábra bemutatja, hogy egy asztali vagy mobilalkalmazás hogyan használja a Microsoft hitelesítési függvénytárat (MSAL) hozzáférési jogkivonatok beszerzésére és webes API-k hívására.

![Asztali alkalmazás, hogyan tűnik annak](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

Az MSAL egy böngészőt használ a tokenek bekéréséhez. A webalkalmazásokhoz is a hitelesítés delegált az Azure AD-ben.

Mivel az Azure AD menti ugyanazt az identitás cookie-t a böngészőben, mint a webalkalmazások, ha a natív vagy mobilalkalmazás használja a rendszerböngészőt, akkor azonnal megkapja az SSO a megfelelő webalkalmazás.

Alapértelmezés szerint az MSAL a rendszerböngészőt használja. Kivételt képeznek a .

## <a name="next-steps"></a>További lépések

* A gyakori kifejezések megismeréséhez tekintse meg a [Microsoft identity platform fejlesztői szószedetét.](developer-glossary.md)
* [Lásd: Hitelesítési folyamatok és alkalmazásforgatókönyvek,](authentication-flows-app-scenarios.md) ha többet szeretne megtudni a Microsoft-identitásplatform által támogatott felhasználók hitelesítésére szolgáló egyéb forgatókönyvekről.
* Tekintse meg az [MSAL-tárakat,](msal-overview.md) amelyek segítenek a Microsoft-fiókokkal, Az Azure AD-fiókokkal és az Azure AD B2C-felhasználókkal egyetlen, egyszerűsített programozási modellben dolgozó alkalmazások fejlesztésében.
* Az [App Service integrálása a Microsoft identity platformmal](/azure/app-service/configure-authentication-provider-aad) című témakörben megtudhatja, hogyan konfigurálhatja a hitelesítést az App Service-alkalmazáshoz.
