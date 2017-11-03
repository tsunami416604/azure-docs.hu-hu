---
title: "Hitelesítési forgatókönyvek az Azure AD |} Microsoft Docs"
description: "Az öt leggyakoribb hitelesítési forgatókönyvek az Azure Active Directory (AAD) áttekintése"
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: 2f9410bdaa037f1839cf7c12c3532b51be669ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-scenarios-for-azure-ad"></a>Hitelesítési forgatókönyvek az Azure AD-hez
Azure Active Directory (Azure AD) egyszerűbbé teszi a fejlesztők számára hitelesítési megadásával identitás szolgáltatás, például az OAuth 2.0 és az OpenID Connect szabványos protokollt támogat, valamint nyitott forrásként tárak segítségével gyorsan elkezdésére különböző platformokon. Ez a dokumentum segít megérteni a különböző forgatókönyvek az Azure AD által támogatott, és bemutatja, hogyan lásson. Ez a következő részből áll:

* [Az Azure AD hitelesítési alapjai](#basics-of-authentication-in-azure-ad)
* [Az Azure AD biztonsági jogkivonatokat a jogcím](#claims-in-azure-ad-security-tokens)
* [Egy alkalmazás regisztrálása az Azure AD alapjai](#basics-of-registering-an-application-in-azure-ad)
* [Alkalmazástípusok és forgatókönyvek](#application-types-and-scenarios)
  
  * [Webalkalmazás webböngészőben](#web-browser-to-web-application)
  * [Egylapos alkalmazások (SPA)](#single-page-application-spa)
  * [Natív alkalmazás webes API-hoz](#native-application-to-web-api)
  * [Webalkalmazás, webes API-hoz](#web-application-to-web-api)
  * [Démon vagy kiszolgálói alkalmazás webes API-hoz](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Az Azure AD hitelesítési alapjai
Ha nem ismeri az Azure AD authentication alapvető fogalmait, olvassa el az ebben a szakaszban. Ellenkező esetben előfordulhat, hogy kihagyni kívánt le a [alkalmazástípusok és forgatókönyvek](#application-types-and-scenarios).

Mérlegeljük, a lehető legegyszerűbb eset, ahol szükség-e identitás: a felhasználó böngészőben nem hitelesíti a webalkalmazásokhoz. Ebben a forgatókönyvben a nagyobb részletes leírását lásd a [webböngészőt a webes alkalmazás](#web-browser-to-web-application) szakaszában, de a bemutatják, a képességek az Azure AD és a forgatókönyv működése conceptualize hasznos kiindulási pont. Vegye figyelembe az alábbi ábrán a forgatókönyv:

![Bejelentkezés a webes alkalmazás áttekintése](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

A fenti ábrának szem előtt, az itt található különböző összetevőinek ismernie kell:

* Az Azure AD az identitásszolgáltató felelős a felhasználók és az alkalmazásokat, amelyek a szervezetek könyvtárában identitásának ellenőrzésére, és végső soron a biztonsági jogkivonatokat ezen felhasználók és az alkalmazások sikeres hitelesítés után kiadása.
* Olyan alkalmazás, amely az Azure AD hitelesítési kihelyező szeretne regisztrálni kell az Azure AD, ami regisztrálja, és egyedileg azonosítja az alkalmazást a címtárban.
* A fejlesztők a nyílt forráskódú az Azure AD hitelesítési könyvtárat a hitelesítés a protokoll alfolyamatot kezelnek, megkönnyítése. Lásd: [Azure Active Directory hitelesítési Kódtárai](active-directory-authentication-libraries.md) további információt.

• A felhasználó hitelesítése után, az alkalmazásnak ellenőriznie kell a felhasználó biztonsági jogkivonat biztosításához, hogy a hitelesítés sikerült a tervezett felek számára. A fejlesztők a megadott hitelesítési könyvtárat a bármely jogkivonat az Azure AD, beleértve a JSON Web Tokens (JWT) vagy a SAML 2.0-s érvényességének kezelésére. Ha azt szeretné, manuálisan érvényességi végrehajtásához, tekintse meg a [JWT jogkivonat-kezelő](https://msdn.microsoft.com/library/dn205065.aspx) dokumentációját.

> [!IMPORTANT]
> Az Azure AD nyilvános kulcsú hitelesítésen használja a jogkivonatok aláírásához, és győződjön meg arról, hogy azok érvényesek. Lásd: [fontos információk kapcsolatos aláírási kulcs átfordulási az Azure AD](active-directory-signing-key-rollover.md) lévő szükséges logika további információt az alkalmazásban, annak érdekében, hogy mindig frissül a legfrissebb kulcsokkal kell rendelkeznie.
> 
> 

• A kérelmeit és válaszait a hitelesítési folyamathoz határozzák meg a hitelesítési protokoll, amelyet használt, például az OAuth 2.0, az OpenID Connect, vagy a WS-Federation, SAML 2.0-s. Ezek a protokollok részletes ismertetése a [Azure Active Directory hitelesítési protokolljai](active-directory-authentication-protocols.md) témakör és az alábbi szakaszokban.

> [!NOTE]
> Az Azure AD támogatja az OAuth 2.0-s, és OpenID Connect szabványok, amelyek nagy mennyiségű használja a tulajdonosi jogkivonatok, beleértve a tulajdonosi jogkivonatok JWTs-ként. Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a "tulajdonos". Abban az értelemben a "tulajdonos", amely a token is jelenthet félre. Egy entitás először hitelesítenie kell magát a tulajdonosi jogkivonattal, fogadni az Azure AD, ha a szükséges lépéseket a rendszer nem hajtja végre a lexikális elem szerepel az átvitel, illetve tárolás biztosításához, ha hozzá, és egy nem kívánt félnek használják. Míg néhány biztonsági jogkivonatokat egy beépített mechanizmust meggátolja, hogy a nem hitelesített felek használja őket, a tulajdonosi jogkivonatok nem rendelkezik a mechanizmus, és kell szállítani, például a transport layer security (HTTPS) biztonságos csatorna. Egy tulajdonosi jogkivonatot továbbított szövegként, ha egy man-a a középső támadás segítségével egy rosszindulatú fél jogkivonat és a védett erőforrásokhoz való illetéktelen hozzáférés használni. Az azonos biztonsági elveket alkalmazza, ha a tárolást, vagy a gyorsítótárazás tulajdonosi jogkivonatok későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságos módon tárolja a tulajdonosi jogkivonatokhoz. További biztonsági szempontok a tulajdonosi jogkivonatok, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).
> 
> 

Most, hogy az alapvető áttekintést, olvassa el a kiépítési működésének megismerése az Azure ad-ben az alábbi szakaszokat, és a gyakori forgatókönyvek az Azure AD támogatja.

## <a name="claims-in-azure-ad-security-tokens"></a>Az Azure AD biztonsági jogkivonatokat a jogcím
Az Azure AD által kiadott biztonsági jogkivonatokat jogcímeket vagy helyességi feltételek hitelesítése tulajdonos információt tartalmaz. Ezeket a jogcímeket a különböző feladatokhoz használhatják az alkalmazást. Például felhasználásuk ellenőrzése a jogkivonat, a tulajdonos directory-bérlő azonosíthatja, felhasználói információk megjelenítése, megállapítása a tulajdonos, és így tovább. A jogcímeket bármely adott biztonsági jogkivonat szerepel token, a felhasználó és az alkalmazás konfigurációját hitelesítéséhez használt hitelesítő adat típusának típusától függenek. Minden Azure AD által kibocsátott jogcím típusú rövid leírása az alábbi táblázatban találhatók. További információkért tekintse meg [támogatott jogkivonat és jogcímtípusok](active-directory-token-and-claims.md).

| Jogcím | Leírás |
| --- | --- |
| Alkalmazásazonosító |Azonosítja az alkalmazást, amely a token-t használja. |
| Célközönség |A jogkivonat az célja, hogy a címzett erőforrás azonosítja. |
| Alkalmazás hitelesítési környezeti osztályait ismertető dokumentációban |Azt jelzi, hogy az ügyfél volt hitelesített (nyilvános ügyfél és a bizalmas ügyfél). |
| Azonnali hitelesítés |A dátum és idő, amikor a hitelesítési történt rögzíti. |
| Hitelesítési módszer |Azt jelzi, hogy a jogkivonat tárgya hitelesítésének módját (jelszó, tanúsítvány, stb.). |
| Utónév |Az Azure AD-készlet biztosít a felhasználó utóneve. |
| Csoportok |A felhasználó tagja objektum azonosítók az Azure AD-csoportot tartalmaz. |
| Identitásszolgáltató |Az identitásszolgáltató, amely a token tárgya hitelesített rögzíti. |
| Ki: |Amellyel a token adta ki, gyakran használt token frissesség idejét rögzíti. |
| Kiállító |Az STS, amely a token, valamint az Azure AD-bérlő kibocsátott azonosítja. |
| Vezetéknév |Az Azure AD-készlet biztosít a felhasználó vezetékneve. |
| Név |Emberi olvasható érték, amely azonosítja a token tárgya biztosít. |
| Objektum azonosítója |Az Azure ad-ben a tulajdonos nem módosítható, egyedi azonosítót tartalmaz. |
| Szerepkörök |Az Azure AD alkalmazás-szerepkörök, amelyek a felhasználó számára engedélyezett rövid nevét tartalmazza. |
| Hatókör |Azt jelzi, hogy az ügyfélalkalmazás számára megadott engedélyeket. |
| Tárgy |Azt jelzi, hogy a rendszerbiztonsági tag, amelyekről a token állításokat információkat. |
| Bérlő azonosítója |A directory-bérlő a jogkivonat kiállító nem módosítható, egyedi azonosítót tartalmaz. |
| A jogkivonatok élettartama |Meghatározza a időtartam alatt, amelyen belül a lexikális elem érvénytelen. |
| Egyszerű felhasználónév |A felhasználó egyszerű felhasználóneve, a tulajdonos tartalmazza. |
| Verzió |A token a verziószámát tartalmazza. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Egy alkalmazás regisztrálása az Azure AD alapjai
Bármely alkalmazás, amely az Azure AD hitelesítési outsources regisztrálni kell a könyvtárban található. Ez a lépés magában foglalja a szólítja fel az Azure AD a alkalmazásról, beleértve az URL-címet, akkor rendelkezik helyét, küldje a választ az URI-t az alkalmazást, és több azonosítása a hitelesítés után az URL-cím. Ezek az információk néhány főbb okból is szükséges:

* Az Azure AD alkalmazással történő kommunikációra a bejelentkezéshez vagy cserélő jogkivonatok kezelésekor koordinátát kell. Ezek közé tartoznak a következők:
  
  * Application ID URI: Az alkalmazás azonosítója. Ez az érték az Azure AD-hitelesítés során küldött jelzi, hogy melyik alkalmazás a hívó szeretne rendelni egy token. Emellett ez az érték megtalálható a jogkivonatot, hogy az alkalmazás ismeri a kívánt célkörnyezet volt.
  * Válasz URL-CÍMÉT és átirányítási URI-: webes API vagy webalkalmazás esetén a válasz URL-címe: a helyet, amelyhez az Azure AD a hitelesítési válaszra, beleértve a jogkivonatot, ha a hitelesítés sikerült küldi. Esetében a natív alkalmazás átirányítási URI-t egy egyedi azonosítója, amelyhez az Azure AD átirányítja a felhasználói ügynök egy OAuth 2.0-lekérdezésben.
  * Alkalmazás azonosítója: Az alkalmazás, az alkalmazás regisztrálásakor Azure AD által generált azonosítója. Az engedélyezési kód vagy a token igénylésekor az alkalmazás Azonosítóját és kulcsát kerülnek az Azure AD-hitelesítés során.
  * Kulcs: A kulcs együtt küldött egy Alkalmazásazonosítót hitelesítéséhez az Azure AD a webes API-hívás.
* Az Azure AD-nek az alkalmazás a szükséges engedélyekkel, hozzáférhet a címtár adataihoz, a szervezet más alkalmazásokat, és így tovább kell

Kiépítés válik tisztább, Miután megismerte a fejlett és az Azure AD integrált alkalmazások két kategóriába sorolhatók:

* Bérlői alkalmazások egyszeri: egy bérlő egyetlen alkalmazás használatát az egyik szervezet számára készült. Ezek a általában az üzletági (LoB) alkalmazások a vállalati fejlesztők által írt. Egy bérlő egyetlen alkalmazást csak kell egy címtárban a felhasználók ne férhessenek hozzá, ezért az ennek eredményeként, csak úgy kell létrehozni egy címtárban. Ezek az alkalmazások általában egy fejlesztő a szervezet által regisztrált.
* Több-bérlős alkalmazás: A több-bérlős alkalmazás készült, számos szervezet, nem csak egyetlen szervezet. Ezek a független szoftverszállító (ISV) által írt általában szoftver,--szolgáltatás (SaaS) alkalmazások. Több-bérlős alkalmazásokhoz szükség minden egyes könyvtárban, ahol használni fogják őket, regisztrálja őket a felhasználó vagy a rendszergazda jóváhagyását igénylő lesz. A hozzájárulási folyamat akkor kezdődik, amikor egy alkalmazás a könyvtárban van regisztrálva, és a Graph API vagy akár egy másik webes API-k való hozzáférése. Amikor egy felhasználó vagy egy másik szervezet rendszergazda regisztrál, és használja az alkalmazást, jelenjenek meg ezek egy párbeszédpanelt, amely megjeleníti a az alkalmazás futtatásához szükséges engedélyekkel. A felhasználó vagy a rendszergazda is majd hozzájárul az alkalmazásról, így az alkalmazás hozzáférést biztosít a megadott adatokat, és végül regisztrálja az alkalmazást a címtárban. További információkért lásd: [hozzájárulás keretében áttekintése](active-directory-integrating-applications.md#overview-of-the-consent-framework).

További megfontolásokat helyett egy bérlő egyetlen alkalmazás egy több-bérlős alkalmazások fejlesztése során merülhetnek fel. Például, ha az alkalmazás érhető el a felhasználók számára több könyvtárban, szeretné meghatározni, melyik bérlői is a. Egy egybérlős alkalmazást kell csak egy felhasználó a saját címtárban keresse meg, amíg egy több-bérlős alkalmazást az Azure AD egy adott felhasználó az összes könyvtár azonosítania kell. Ennek a feladatnak az Azure AD egy közös hitelesítési végpontot, ahol több-bérlős alkalmazásokat lehet konfigurálni a bejelentkezési kérelmek, helyett egy bérlő-specifikus végpontot biztosít. Ehhez a végponthoz összes könyvtár https://login.microsoftonline.com/common az Azure ad-ben, mivel lehet, hogy a bérlő-specifikus végpont https://login.microsoftonline.com/contoso.onmicrosoft.com. A közös végpont különösen fontos figyelembe venni, amikor az alkalmazás fejlesztése, mert több bérlő kezelésének bejelentkezési, kijelentkezési, és a jogkivonatok érvényesség-ellenőrzése során szükséges logika lesz szüksége.

Ha jelenleg egy egybérlős alkalmazást fejleszt, de számos szervezet rendelkezésére bocsátja, könnyen módosításokat végezheti el az alkalmazás és az Azure AD-konfigurációjában abba, hogy több-bérlős képes. Emellett az Azure AD használ az azonos aláírási kulcs összes jogkivonatokat található összes, hogy meg van adva egy bérlő egyetlen vagy több-bérlős alkalmazás hitelesítési.

A jelen dokumentumban szereplő minden egyes forgatókönyv egy alterület, mely leírja az üzembe helyezési követelményei tartalmazza. Részletesebb információ a kiépítési az Azure AD alkalmazás- és egyetlen és több-bérlős alkalmazások közötti különbségek: [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md) további információt. Továbbra is az Azure AD alkalmazás szabhatják megértéséhez olvasása.

## <a name="application-types-and-scenarios"></a>Alkalmazástípusok és forgatókönyvek
Minden ebben a dokumentumban leírt forgatókönyv fejleszthetők a különböző nyelvekhez és platformokhoz használatával. Azok az összes üzemelnek elérhető teljes Kódminták a [mintakódok útmutató](active-directory-code-samples.md), vagy közvetlenül a megfelelő [minta GitHub-adattárak](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Emellett ha az alkalmazásnak egy adott vagy egy végpontok közötti forgatókönyv szegmens, a legtöbb esetben funkció felveheti egymástól függetlenül. Például ha egy natív alkalmazás, amely behívja a webes API-k, egyszerűen hozzáadhatja a webes API-t is meghívó webalkalmazás. A következő diagram azt ábrázolja, ezek a forgatókönyvek és alkalmazástípusokat, és hogyan felveheti különböző összetevőket:

![Alkalmazástípusok és forgatókönyvek](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Az Azure AD által támogatott öt elsődleges az alkalmazás-forgatókönyvek a következők:

* [Böngészőben webalkalmazásokhoz](#web-browser-to-web-application): A felhasználó bejelentkezni egy webalkalmazást, amelyet az Azure AD számára szükséges.
* [Egyetlen lap alkalmazás (SPA)](#single-page-application-spa): A felhasználó bejelentkezni egy egyoldalas alkalmazást, amelyet az Azure AD számára szükséges.
* [Webes API a natív alkalmazás](#native-application-to-web-api): egy natív alkalmazást egy telefon, a tábla vagy a számítógépen futó kell hitelesíteni a felhasználót, hogy egy webes API-t az Azure AD által védett erőforrások lekérése.
* [Webes API-webalkalmazás](#web-application-to-web-api): webalkalmazás kell egy webes API-t az Azure AD által védett erőforrások lekérése.
* [Démon vagy webes API kiszolgálói alkalmazás](#daemon-or-server-application-to-web-api): egy démon alkalmazást vagy egy kiszolgálói alkalmazás webes felhasználói felület nélkül kell egy webes API-t az Azure AD által védett erőforrások lekérése.

### <a name="web-browser-to-web-application"></a>Webalkalmazás webböngészőben
Ez a szakasz olyan alkalmazás, amely hitelesíti a felhasználót egy böngészőben webalkalmazásokhoz ismerteti. Ebben a forgatókönyvben a webes alkalmazás a felhasználó böngészőben való bejelentkezéshez őket az Azure AD irányítja. Az Azure AD tartalmaz egy biztonsági jogkivonatba a felhasználóval kapcsolatos jogcímek a felhasználó böngészőben a bejelentkezési választ ad. Ebben a forgatókönyvben támogatja a bejelentkezést a WS-Federation, SAML 2.0 és OpenID Connect protokollok használatával.

#### <a name="diagram"></a>Ábra
![Webes alkalmazás böngészőben hitelesítési folyamata](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása
1. Amikor a felhasználó felkeresi a alkalmazást, és jelentkezzen be, a rendszer visszairányítja a hitelesítési végpont bejelentkezési kérelem keresztül az Azure ad-ben.
2. A felhasználó bejelentkezik a bejelentkezési oldalon.
3. Sikeres hitelesítés esetén az Azure AD egy hitelesítési jogkivonatot hoz létre, és a bejelentkezési választ küld az alkalmazás válasz URL-címet adott meg az Azure portálon. Termelési alkalmazások esetében a válasz URL-CÍMEN HTTPS kell lennie. A visszaküldött jogkivonat felhasználói és az Azure AD szükséges jogcímeket, az alkalmazás ellenőrzése a jogkivonat tartalmazza.
4. Az alkalmazás használatával egy nyilvános aláírási kulcs és a kiállítói információk érhetők el az összevonási metaadatok dokumentum az Azure AD érvényesíti a jogkivonatot. Után az alkalmazás érvényesíti a jogkivonatot, az Azure AD egy új munkamenet a felhasználó indul. A munkamenet a felhasználó az alkalmazás eléréséhez, amíg le nem jár.

#### <a name="code-samples"></a>Kódminták
A Kódminták talál webböngésző webalkalmazás forgatókönyvek. És foglalkozzon gyakran--jelenleg felvenni új minták folyamatosan. [Böngészőben webalkalmazásokhoz](active-directory-code-samples.md#web-browser-to-web-application).

#### <a name="registering"></a>Regisztrálása
* Egybérlős: Ha egy alkalmazás csak a szervezet számára, regisztrálnia kell azt a vállalati címtárban az Azure portál használatával.
* Több-Bérlős: Ha egy alkalmazás, amely a szervezeten kívüli felhasználók által használható, az szerepelnie kell a vállalati címtárban, hanem is regisztrálni kell az alkalmazást használó minden egyes szervezet könyvtárban. Az alkalmazás akkor válik elérhetővé a könyvtárban, az ügyfelek, amelyek lehetővé teszik az alkalmazás hozzájárul, hogy a regisztrációs folyamat is megadhat. Amikor bejelentkeznek az alkalmazáshoz, azok számára jelenik meg olyan párbeszédpanel, amely az alkalmazás futtatásához szükséges engedélyek láthatók, és hozzájárulás lehetőséget. Attól függően, hogy a szükséges engedélyekkel, a másik szervezet rendszergazda előfordulhat, hogy kell azokat. Amikor a felhasználó vagy a rendszergazda beleegyezik, az alkalmazás regisztrálva van a címtár. További információkért lásd: [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
A felhasználói munkamenet lejár, ha az Azure AD által kibocsátott jogkivonat élettartamát lejár. Az alkalmazás ebben az időszakban szükség esetén például a felhasználók adott ideig tartó tétlenség alapján kijelentkezés lerövidíthető. Ha a munkamenet lejár, a felhasználó felszólítást kapnak újból bejelentkezni.

### <a name="single-page-application-spa"></a>Egylapos alkalmazások (SPA)
Ez a szakasz ismerteti a hitelesítési egyetlen oldal alkalmazás esetében, amely használja az Azure AD és az OAuth 2.0 típusú implicit engedélyezési adja meg a webes API vissza end biztonságossá. Egyetlen lap alkalmazások általában felépítése rétegként JavaScript bemutató (előtér-), amelyen a böngésző és a Web API háttérből, amely az olyan kiszolgálón fut, és megvalósítja az alkalmazás üzleti logikát. További tudnivalók az implicit hitelesítésengedélyezési, és segít eldönteni, hogy az alkalmazás forgatókönyv szerint jobb [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat](active-directory-dev-understanding-oauth2-implicit-grant.md).

Ebben a forgatókönyvben, amikor a felhasználó bejelentkezik, a JavaScript első célból használ [Active Directory hitelesítési tár JavaScript (adal-t. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) és az beszerzése egy azonosító jogkivonatot (id_token) az Azure AD implicit hitelesítésengedélyezési. A jogkivonat található a gyorsítótárban, és az ügyfél csatolja a kérelem a tulajdonosi jogkivonattal, akkor a Web API háttér, amely az OWIN köztes használatával lett biztonságossá téve. 

#### <a name="diagram"></a>Ábra
![Egyetlen lapon alkalmazásdiagram](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása
1. A felhasználók megnyitják a webes alkalmazás.
2. Az alkalmazás a JavaScript előtér (bemutató réteg) és a böngésző ad vissza.
3. A felhasználói bejelentkezés, például kezdeményez egy szereplő bejelentkezési hivatkozásra kattintva. A böngészőben egy GET küld az Azure AD engedélyezési végpont egy azonosító jogkivonatot kérni. A kérelem lekérdezési paraméterekhez az alkalmazás Azonosítóját és a válasz URL-CÍMÉT tartalmazza.
4. Az Azure AD ellenőrzi a válasz URL-CÍMEN, szemben a regisztrált válasz URL-címet adott meg az Azure portálon.
5. A felhasználó bejelentkezik a bejelentkezési oldalon.
6. Sikeres hitelesítés esetén az Azure AD egy azonosító adatblokkot hoz létre, és visszaadja egy URL-cím kódrészletet (#), a kérelem-válasz URL-CÍMEN. Termelési alkalmazások esetében a válasz URL-CÍMEN HTTPS kell lennie. A visszaküldött jogkivonat felhasználói és az Azure AD szükséges jogcímeket, az alkalmazás ellenőrzése a jogkivonat tartalmazza.
7. A böngészőjében futó JavaScript-ügyfél kód a token kiolvassa a hívások az alkalmazás webes API vissza end védelméhez használandó válasz.
8. A böngésző meghívja az alkalmazás webes API vissza a engedélyezési fejléc végződhet a hozzáférési jogkivonat.

#### <a name="code-samples"></a>Kódminták
Tekintse meg a mintakódok lap alkalmazás (SPA) forgatókönyvek esetén. Ügyeljen arra, hogy gyakran foglalkozzon – jelenleg felvenni új minták folyamatosan. [Egyetlen lap alkalmazás (SPA)](active-directory-code-samples.md#single-page-application-spa).

#### <a name="registering"></a>Regisztrálása
* Egybérlős: Ha egy alkalmazás csak a szervezet számára, regisztrálnia kell azt a vállalati címtárban az Azure portál használatával.
* Több-Bérlős: Ha egy alkalmazás, amely a szervezeten kívüli felhasználók által használható, az szerepelnie kell a vállalati címtárban, hanem is regisztrálni kell az alkalmazást használó minden egyes szervezet könyvtárban. Az alkalmazás akkor válik elérhetővé a könyvtárban, az ügyfelek, amelyek lehetővé teszik az alkalmazás hozzájárul, hogy a regisztrációs folyamat is megadhat. Amikor bejelentkeznek az alkalmazáshoz, azok számára jelenik meg olyan párbeszédpanel, amely az alkalmazás futtatásához szükséges engedélyek láthatók, és hozzájárulás lehetőséget. Attól függően, hogy a szükséges engedélyekkel, a másik szervezet rendszergazda előfordulhat, hogy kell azokat. Amikor a felhasználó vagy a rendszergazda beleegyezik, az alkalmazás regisztrálva van a címtár. További információkért lásd: [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md).

Az alkalmazás a regisztrálás után azt be kell állítani az OAuth 2.0 Implicit Grant protokoll használatára. Alapértelmezés szerint ez a protokoll le van tiltva, az alkalmazások. Ahhoz, hogy az alkalmazás az OAuth2 Implicit Grant protokoll, az Azure portálról az alkalmazásjegyzék szerkesztése és a "oauth2AllowImplicitFlow" értéket állítsa igaz értékre. Részletes útmutatásért lásd: [engedélyezése OAuth 2.0 Implicit Grant egyetlen oldal alkalmazások](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Ha ADAL.js használatával kezelheti az Azure AD hitelesítési, kihasználhatja a több funkciót is lehetővé teszi egy lejárt jogkivonat frissítését, valamint a jogkivonatok lekérésének további webes API-erőforrások, amelyeket a lehetséges, hogy az alkalmazás. Amikor a felhasználó sikeresen hitelesíti az Azure ad-vel, a cookie-k által védett munkamenet a felhasználó a böngésző és az Azure AD között. Fontos megjegyezni, hogy a munkamenet létezik-e a felhasználó és az Azure AD között és kapcsolat nem a felhasználó és a webalkalmazás fut a kiszolgálón. Amikor egy jogkivonat lejár, ADAL.js ehhez a munkamenethez segítségével csendes egy másik jogkivonat beszerzése. Ennek érdekében egy rejtett iFrame használatával küldésére és fogadására a kérelem, az OAuth Implicit Grant protokoll használatával. ADAL.js segítségével is ugyanezzel a módszerrel csendes hozzáférési tokenek beszerzése érdekében, az Azure AD, hogy az alkalmazás hívások mindaddig, amíg ezek az erőforrások támogatja az eltérő eredetű erőforrások megosztása (CORS) van regisztrálva az a felhasználó, és a webes API erőforrások számára szükséges beleegyezést bejelentkezéskor a felhasználó által adtak meg.

### <a name="native-application-to-web-api"></a>Natív alkalmazás webes API-hoz
Ez a szakasz ismerteti a natív alkalmazás, amely behívja a webes API-k egy felhasználó nevében. Ebben a forgatókönyvben épül, az OAuth 2.0 engedélyezési kód engedélytípus nyilvános ügyféllel 4.1 szakaszában leírtak szerint a [OAuth 2.0 specifikáció](http://tools.ietf.org/html/rfc6749). A natív alkalmazás a felhasználó számára egy jogkivonatot beolvassa az OAuth 2.0 protokoll használatával. Ez a jogkivonat küldi el a rendszer a kérelem a webes API-hoz, amely engedélyezi a felhasználó és a kívánt erőforrás adja vissza.

#### <a name="diagram"></a>Ábra
![Webes API-Diagram natív alkalmazás](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Hitelesítési folyamata az API a natív alkalmazás
#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása
Ha az AD-hitelesítési kódtárakkal használ, a protokoll részleteit az alábbiakban a legtöbb történik meg, például a böngészőben előugró ablak, a token-gyorsítótárazási és a frissítési jogkivonatokat kezelése.

1. Egy böngészővel előugró, a natív alkalmazás egy kérést küld az engedélyezési végpont Azure AD-ben. Ehhez a kérelemhez tartalmazza az alkalmazás Azonosítóját és az átirányítási URI-t a natív alkalmazás, ahogy az az Azure portálon, és az alkalmazás URI azonosítója a webes API. Ha a felhasználó még nem jelentkezett be, meg kell újból bejelentkezni
2. Az Azure AD akkor hitelesíti a felhasználót. Ha olyan több-bérlős alkalmazás, és hozzájárulási kell használni az alkalmazást, a felhasználó beleegyezését, ha azok még nem tette meg kell. Hozzájárulás megadása után, és a sikeres hitelesítés után az Azure AD kibocsát egy engedélyezési kód válaszul vissza az ügyfél-alkalmazás átirányítási URI-t.
3. Ha az Azure AD kibocsát egy engedélyezési kód válaszul vissza az átirányítási URI-t, az ügyfélalkalmazás leállítja a böngésző interakció, és a válaszban szereplő engedélyezési kódot kibontja. Az engedélyezési kód használatával, az ügyfélalkalmazás kérést küld az Azure AD token végpontot, amely engedélyezési kódot tartalmaz, akkor az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrás kapcsolatos információk (alkalmazás URI azonosítója a a webes API-k).
4. Az Azure AD érvényesíti az engedélyezési kód és az alkalmazás és a webes API-ja kapcsolatos információkat. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT jogkivonat és egy frissítési JWT jogkivonat. Ezenkívül az Azure AD a felhasználó, például a megjelenítendő nevét és a bérlői azonosító alapvető információt ad vissza
5. A HTTPS PROTOKOLLOKON keresztül az ügyfélalkalmazás az visszaadott JWT jogkivonat a JWT karakterlánc a "Tulajdonos" megnevezés a kérés hitelesítési fejlécéhez modul hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.
6. Ha a hozzáférési jogkivonat lejár, az ügyfélalkalmazás program hibaüzenetet, amely jelzi, hogy a felhasználónak újra hitelesíteni kell. Ha az alkalmazás egy érvényes frissítési jogkivonat, azt segítségével szerezzen be egy új hozzáférési jogkivonat jelentkezhet be a felhasználó értesítése nélkül. Ha a frissítési jogkivonat lejár, az alkalmazás kell ismét interaktív módon a felhasználó hitelesítésére.

> [!NOTE]
> Az Azure AD által kiadott frissítési jogkivonat segítségével több erőforrások elérését. Például ha egy ügyfél-alkalmazás, amely jogosult két webes API-k hívására, a frissítési jogkivonat segítségével szerezze be a hozzáférési tokent a más webes API-hoz is.
> 
> 

#### <a name="code-samples"></a>Kódminták
Tekintse meg a mintakódok natív alkalmazás számára, hogy a webes API-forgatókönyvek. És foglalkozzon gyakran--jelenleg felvenni új minták folyamatosan. [Webes API-t natív alkalmazás](active-directory-code-samples.md#native-application-to-web-api).

#### <a name="registering"></a>Regisztrálása
* Egyetlen bérlői: Mind a natív alkalmazás és a webes API-k regisztrálni kell ugyanabban a könyvtárban az Azure ad-ben. A webes API-k beállítható úgy, hogy az engedélyekkel, az erőforrások a natív alkalmazás hozzáférés korlátozására szolgáló tenni. Az ügyfélalkalmazás majd kiválasztja a kívánt engedélyeket az Azure portálon a "Engedélyek az egyéb alkalmazások" legördülő menüből.
* Több-Bérlős: Először a natív alkalmazás csak a fejlesztői vagy regisztrált publisher könyvtár. Jelzi a működéséhez szükséges engedélyekkel, a natív alkalmazás van konfigurálva. Amikor egy felhasználó vagy a rendszergazda a célkönyvtárban hozzájárulását adja az alkalmazásnak, amely lehetővé teszi a szervezet a szükséges engedélyek listája látható párbeszédpanel. Egyes alkalmazások csak a felhasználói szintű engedélyek, amelyek a szervezet bármely felhasználója is szükséges. Más alkalmazásoknak rendszergazdai engedélyek, amelyek a szervezetben a felhasználók nem járulhatnak hozzá. Csak a címtár rendszergazdája biztosíthat hozzájárulási Ez a jogosultsági szint igénylő alkalmazások számára. Amikor a felhasználó vagy a rendszergazda hozzájárul, csak a webes API regisztrálva van a címtár. További információkért lásd: [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
A natív alkalmazás használja az engedélyezési kód is elérheti a JWT jogkivonat, amikor JWT frissítési jogkivonat is megkapja. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével újra hitelesíteni a felhasználót anélkül, hogy jelentkezzen be újra. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonat és a frissítési jogkivonat eredményez.

### <a name="web-application-to-web-api"></a>Webalkalmazás, webes API-hoz
Ez a szakasz ismerteti a webalkalmazás, amelyet a webes API-k erőforrások lekérése. Ebben a forgatókönyvben kétfélék identitás, amely a webes alkalmazás hitelesítéséhez, és hívja meg a webes API-k segítségével: az alkalmazás azonosítóját, vagy delegált felhasználói azonosítót.

*Az Alkalmazásidentitás:* ebben a forgatókönyvben használja OAuth 2.0 ügyfél hitelesítő adatok megadása hitelesítse magát az alkalmazást, és a webes API-k eléréséhez. Ha egy alkalmazás azonosítóját, a webes API csak azt észleli, hogy a webes alkalmazás hívja meg, mint a webes API nem kapott információt a felhasználóval kapcsolatos. Ha az alkalmazás kap a felhasználó adatai, az alkalmazás protokollon keresztül küldi, és azt nem írta alá az Azure AD. A webes API-k megbízhatónak tekinti, hogy a webes alkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve.

*Felhasználói azonosító meghatalmazott:* ebben a forgatókönyvben két módon valósítható meg: OpenID Connect, és a kód hitelesítésengedélyezési OAuth 2.0 bizalmas ügyféllel. A webalkalmazás kap egy jogkivonatot a felhasználóhoz, amelynek kiderül, hogy a webes API, amely a webalkalmazás sikeresen hitelesíteni a felhasználót, és a webalkalmazás nem szerezhetnek be a webes API hívásához delegált felhasználói azonosítót. Ez a jogkivonat a webes API-hoz, amely engedélyezi a felhasználó, és adja vissza a kívánt erőforrás a kérelem küldése.

#### <a name="diagram"></a>Ábra
![Webes alkalmazás Web API diagramja](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása
Az Alkalmazásidentitás és a delegált felhasználói identitás típusok az alábbi folyamat ismerteti. A fő különbség a kettő között, hogy a felhasználó delegált identitása először kell szerezni az engedélyezési kód, mielőtt a felhasználó bejelentkezési és a webes API-k eléréséhez.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Az OAuth 2.0 ügyfél Alkalmazásidentitás hitelesítő adatok megadása
1. A felhasználó van bejelentkezve az Azure AD-t a webes alkalmazás (lásd a [webböngészőt a webes alkalmazás](#web-browser-to-web-application) fent).
2. A webalkalmazásnak kell szerezni a hozzáférési tokent, hogy a webes API-t a hitelesítéshez és a kívánt erőforrás lekérése. Ez egy kérést küld az Azure AD token-végpont, a hitelesítő adatokat, Alkalmazásazonosítót és webes API-alkalmazás URI azonosítója.
3. Az Azure AD hitelesíti az alkalmazást, és adja vissza a JWT jogkivonat, amellyel a webes API hívása.
4. A HTTPS PROTOKOLLOKON keresztül a webes alkalmazás használatával az visszaadott JWT jogkivonat a kérés hitelesítési fejlécéhez a "Tulajdonos" megjelölés JWT karakterlánc hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.

##### <a name="delegated-user-identity-with-openid-connect"></a>Delegált felhasználói identitás openid Connect
1. A felhasználó van bejelentkezve az Azure AD használata ingyenes webalkalmazás (lásd a [webböngészőt a webes alkalmazás](#web-browser-to-web-application) fenti szakaszban). Ha a felhasználó a webalkalmazás nem még hozzájárult lehetővé téve a webes alkalmazás a webes API hívásához a nevében, a felhasználó beleegyezését kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyek, a címtárban a normál felhasználók nem fogják tudni hozzájárulás. A hozzájárulási folyamat csak több-bérlős alkalmazásra vonatkozik, nem egyetlen bérlői alkalmazások, mint az alkalmazás már rendelkezik a szükséges engedélyekkel. Amikor a felhasználó jelentkezik be, a webes alkalmazás kapott egy azonosító jogkivonat a felhasználó, valamint az engedélyezési kód kapcsolatos információkat.
2. Az Azure AD által kiadott engedélyezési kódot használja, a webes alkalmazás kérést küld, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrás (Alkalmazásazonosító URI-alkalmazás a webes API) az Azure AD-jogkivonat végpontjához.
3. Az Azure AD érvényesíti az engedélyezési kódot és az információt a webalkalmazás és a webes API. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT jogkivonat és egy frissítési JWT jogkivonat.
4. A HTTPS PROTOKOLLOKON keresztül a webes alkalmazás használatával az visszaadott JWT jogkivonat a kérés hitelesítési fejlécéhez a "Tulajdonos" megjelölés JWT karakterlánc hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Az OAuth 2.0 kód Hitelesítésengedélyezési delegált felhasználók identitását
1. A felhasználó már bejelentkezett egy webes alkalmazás, amelynek hitelesítési módszer használata az Azure AD független.
2. A webalkalmazás az engedélyezési kód Szerezzen be a hozzáférési tokent, így azt a böngészőben egy kérelmet ad ki az Azure AD hitelesítési végpontra, Alkalmazásazonosító megadása és a sikeres hitelesítést követően a webes alkalmazás átirányítási URI-címe van szükség. A felhasználó bejelentkezik az Azure ad Szolgáltatásba.
3. Ha a felhasználó a webalkalmazás nem még hozzájárult lehetővé téve a webes alkalmazás a webes API hívásához a nevében, a felhasználó beleegyezését kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyek, a címtárban a normál felhasználók nem fogják tudni hozzájárulás. A hozzájárulási egyetlen és több-bérlős alkalmazásra vonatkozik.  Az egyetlen bérlő esetében egy rendszergazda hajthat végre a rendszergazda jóváhagyását a hozzájárulási azok a felhasználók nevében.  Ehhez használja a `Grant Permissions` gombra a [Azure Portal](https://portal.azure.com). 
4. Miután a felhasználó hozzájárult, a webalkalmazás kap olyan hozzáférési jogkivonatot szerezni szükséges engedélyezési kódot.
5. Az Azure AD által kiadott engedélyezési kódot használja, a webes alkalmazás kérést küld, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrás (Alkalmazásazonosító URI-alkalmazás a webes API) az Azure AD-jogkivonat végpontjához.
6. Az Azure AD érvényesíti az engedélyezési kódot és az információt a webalkalmazás és a webes API. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT jogkivonat és egy frissítési JWT jogkivonat.
7. A HTTPS PROTOKOLLOKON keresztül a webes alkalmazás használatával az visszaadott JWT jogkivonat a kérés hitelesítési fejlécéhez a "Tulajdonos" megjelölés JWT karakterlánc hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.

#### <a name="code-samples"></a>Kódminták
A Kódminták talál Web API forgatókönyvek webalkalmazás. És foglalkozzon gyakran--jelenleg felvenni új minták folyamatosan. Webes [alkalmazás webes API-hoz](active-directory-code-samples.md#web-application-to-web-api).

#### <a name="registering"></a>Regisztrálása
* Egybérlős: Az alkalmazás azonosítóját és a felhasználó delegált identitása esetekben, a webes alkalmazás és a webes API regisztrálni kell ugyanabban a könyvtárban az Azure ad-ben. A webes API-k engedélyekkel, szolgáló korlátozni az erőforrások eléréséhez a webalkalmazás közzétételét konfigurálható. A delegált felhasználói identitástípus használatos, ha a webalkalmazásnak kell válassza ki a kívánt engedélyeket az Azure portálon a "Engedélyek az egyéb alkalmazások" legördülő menüből. Ez a lépés nincs szükség, ha az alkalmazás identity típus használatban van.
* Több-Bérlős: Először a webes alkalmazás van konfigurálva a működéséhez szükséges engedélyek jelzi. Amikor egy felhasználó vagy a rendszergazda a célkönyvtárban hozzájárulását adja az alkalmazásnak, amely lehetővé teszi a szervezet a szükséges engedélyek listája látható párbeszédpanel. Egyes alkalmazások csak a felhasználói szintű engedélyek, amelyek a szervezet bármely felhasználója is szükséges. Más alkalmazásoknak rendszergazdai engedélyek, amelyek a szervezetben a felhasználók nem járulhatnak hozzá. Csak a címtár rendszergazdája biztosíthat hozzájárulási Ez a jogosultsági szint igénylő alkalmazások számára. Amikor a felhasználó vagy a rendszergazda hozzájárul, a webes alkalmazás és a webes API egyaránt regisztrálva van a címtárban.

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Ha a webes alkalmazás is elérheti a JWT jogkivonat az engedélyezési kód használ, a JWT-frissítési jogkivonat is kap. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével újra hitelesíteni a felhasználót anélkül, hogy jelentkezzen be újra. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonat és a frissítési jogkivonat eredményez.

### <a name="daemon-or-server-application-to-web-api"></a>Démon vagy kiszolgálói alkalmazás webes API-hoz
Ez a szakasz ismerteti, amelyet a webes API-k erőforrások lekérése démon vagy kiszolgálói alkalmazás. Két alárendelt esetben ez a szakasz vonatkozó: démon, amelyet a webes API OAuth 2.0 ügyfél hitelesítő adatok engedélytípus; épülő meghívása és a kiszolgáló alkalmazások (például a webes API-k), amelyet a webes API-k, OAuth 2.0 On-Behalf-Of vázlat specification épülő hívja.

A forgatókönyv esetében egy démon alkalmazást kell egy webes API-hívás esetén fontos tudni, hogy folyamatban van. Először nincs lehetőség egy démon alkalmazással, amelyhez az alkalmazás a saját identitással rendelkezik felhasználói beavatkozást. Egy démon alkalmazás például egy kötegelt, vagy a háttérben futó operációs rendszer szolgáltatásnak. Ez az alkalmazástípus kérései hozzáférési tokent az alkalmazás azonosítójának használatával, és az Alkalmazásazonosító, a hitelesítő adatok (jelszó vagy a tanúsítvány) és az alkalmazás bemutatása az Azure AD-Alkalmazásazonosító URI. Sikeres hitelesítést követően a démon megkapja a hozzáférési tokent az Azure AD, majd a webes API hívásához használt.

A forgatókönyvhöz egy kiszolgálói alkalmazás kell egy webes API-hívás esetén hasznos lehet egy példát. Tegyük fel, hogy a felhasználó a natív alkalmazás hitelesítette, és a natív alkalmazást kell webes API-hívás. Az Azure AD kibocsát egy JWT jogkivonat a webes API hívásához. Ha a webes API-t egy másik alsóbb rétegbeli webes API-t hívja, a a-meghatalmazásos folyamat használhatja a felhasználói identitás delegálásához, és az alacsonyabb szintű webes API-hoz.

#### <a name="diagram"></a>Ábra
![Démon vagy webes API-diagram kiszolgálói alkalmazás](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása
##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Az OAuth 2.0 ügyfél Alkalmazásidentitás hitelesítő adatok megadása
1. Először a kiszolgálóalkalmazás kell hitelesítenie magát az Azure ad szolgáltatással, mint például az interaktív bejelentkezési párbeszédpanel emberi beavatkozás nélkül. Ez egy kérést küld az Azure AD token-végpont, a hitelesítő adatok, a Alkalmazásazonosító és az alkalmazás URI azonosítója.
2. Az Azure AD hitelesíti az alkalmazást, és adja vissza a JWT jogkivonat, amellyel a webes API hívása.
3. A HTTPS PROTOKOLLOKON keresztül a webes alkalmazás használatával az visszaadott JWT jogkivonat a kérés hitelesítési fejlécéhez a "Tulajdonos" megjelölés JWT karakterlánc hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>OAuth 2.0 a nevében-az Vázlat meghatározásával delegált felhasználói azonosító
Az alábbiakban tárgyalt folyamata azt feltételezi, hogy, hogy a felhasználó hitelesítése egy másik alkalmazástól (például egy natív alkalmazás), és a felhasználói identitás már használja egy hozzáférési jogkivonatát az első szintű webes API-k megszerzésére.

1. A natív alkalmazás küld az első szintű webes API-t a hozzáférési jogkivonat.
2. Az első szintű webes API-t egy kérést küld az Azure AD token-végpont, így az alkalmazás Azonosítóját és hitelesítő adatait, valamint a felhasználói jogkivonat. Emellett a kérelem nem jut egy on_behalf_of paraméter, amely azt jelzi, a webes API által kért új jogkivonatai és az eredeti felhasználó nevében alsóbb rétegbeli webes API-hívás.
3. Az Azure AD ellenőrzi, hogy az első szintű webes API-k jogosult az elérésére, a második szintű webes API-t, és érvényesíti a kérelem egy JWT jogkivonatot ad vissza, és a jwt-t frissítési token az első szintű webes API-hoz.
4. A HTTPS PROTOKOLLOKON keresztül az első szintű web API majd meghívja a második szintű webes API-t a kérés hitelesítési fejlécéhez a lexikális elem karakterlánca hozzáfűzésével. Az első szintű webes API-t továbbra is a második szintű webes API-k hívható meg, amíg a hozzáférési jogkivonat és frissítési jogkivonatok érvényesek.

#### <a name="code-samples"></a>Kódminták
Tekintse meg a mintakódok démon vagy webes API-forgatókönyvek kiszolgálói alkalmazás. És foglalkozzon gyakran--jelenleg felvenni új minták folyamatosan. [Kiszolgáló vagy démon alkalmazás webes API-hoz](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Regisztrálása
* Egybérlős: Az alkalmazás azonosítóját és a felhasználó delegált identitása esetekben, a démon vagy kiszolgálói alkalmazás regisztrálni kell ugyanabban a könyvtárban az Azure ad-ben. A webes API-k beállítható úgy, hogy az engedélyekkel, korlátozza a démon vagy az erőforrások kiszolgáló elérésére használt tenni. A delegált felhasználói identitástípus használatos, ha a kiszolgálóalkalmazás kell válassza ki a kívánt engedélyeket az Azure portálon a "Engedélyek az egyéb alkalmazások" legördülő menüből. Ez a lépés nincs szükség, ha az alkalmazás identity típus használatban van.
* Több-Bérlős: Először a démon vagy a kiszolgáló alkalmazás van konfigurálva a működéséhez szükséges engedélyek jelzi. Amikor egy felhasználó vagy a rendszergazda a célkönyvtárban hozzájárulását adja az alkalmazásnak, amely lehetővé teszi a szervezet a szükséges engedélyek listája látható párbeszédpanel. Egyes alkalmazások csak a felhasználói szintű engedélyek, amelyek a szervezet bármely felhasználója is szükséges. Más alkalmazásoknak rendszergazdai engedélyek, amelyek a szervezetben a felhasználók nem járulhatnak hozzá. Csak a címtár rendszergazdája biztosíthat hozzájárulási Ez a jogosultsági szint igénylő alkalmazások számára. Amikor a felhasználó vagy a rendszergazda hozzájárul, mind a webes API-k a könyvtárban van regisztrálva.

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Ha az első alkalmazás használja az engedélyezési kód is elérheti a JWT jogkivonat, is kap egy frissítési JWT jogkivonat. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével újra hitelesíteni a felhasználót a hitelesítő adatok kérése nélkül. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonat és a frissítési jogkivonat eredményez.

## <a name="see-also"></a>Lásd még:
[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

[Az Azure Active Directory-Kódminták](active-directory-code-samples.md)

[Fontos információkat a kulcsváltás Azure AD-ben](active-directory-signing-key-rollover.md)

[OAuth 2.0 Azure AD-ben](https://msdn.microsoft.com/library/azure/dn645545.aspx)

