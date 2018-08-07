---
title: Hitelesítési forgatókönyvek az Azure ad-ben |} A Microsoft Docs
description: Az öt leggyakoribb hitelesítési forgatókönyvek áttekintést nyújt az Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2018
ms.author: celested
ms.reviewer: jmprieur, andret, nacanuma, hirsin
ms.custom: aaddev
ms.openlocfilehash: dda0d8e9496eb310f6d2a2791977e9de0eea6503
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578524"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Hitelesítési forgatókönyvek az Azure ad-ben

Az Azure Active Directory (Azure AD) identitás azáltal, hogy segítséget nyújtanak a különböző platformokhoz támogatja a szabványos protokollok, mint például az OAuth 2.0 és OpenID Connect szolgáltatást, valamint nyílt forráskódú kódtárként leegyszerűsíti a hitelesítés fejlesztőknek elkezdésére gyorsan. Ez a cikk segít megérteni a különféle forgatókönyvekhez az Azure AD támogatja, és bemutatják, hogyan kezdheti el. Ez a következő szakaszokból áll:

* [Az Azure AD-hitelesítés alapjai](#basics-of-authentication-in-azure-ad)
* [Az Azure AD biztonsági jogkivonatokat a jogcím](#claims-in-azure-ad-security-tokens)
* [Egy alkalmazás regisztrálása az Azure ad-ben való használatának alapvető](#basics-of-registering-an-application-in-azure-ad)
* [Alkalmazástípusok és forgatókönyvek](#application-types-and-scenarios)

  * [Webböngésző-webalkalmazáshoz](#web-browser-to-web-application)
  * [Az egyoldalas alkalmazás (SPA)](#single-page-application-spa)
  * [Natív alkalmazás webes API-hoz](#native-application-to-web-api)
  * [Webalkalmazás, webes API-hoz](#web-application-to-web-api)
  * [Démon, vagy egy kiszolgálói alkalmazás webes API-hoz](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Az Azure AD-hitelesítés alapjai

Ha ismeri az Azure AD authentication alapvető fogalmait, olvassa el az ebben a szakaszban. Ellenkező esetben előfordulhat, hogy kihagyni kívánt le a [alkalmazástípusok és forgatókönyvek](#application-types-and-scenarios).

Ahol azonosítót kötelező megadni lehető legegyszerűbb forgatókönyvet: a felhasználó böngészőben a webalkalmazás hitelesítenie kell. Ebben a forgatókönyvben ismertetett részletesebben a [webalkalmazást a webböngésző](#web-browser-to-web-application) szakaszt, de a bemutatják az Azure ad-ben képességeit, és a forgatókönyv működése conceptualize hasznos kiindulási pontot. Vegye figyelembe a következő ábra az ebben a forgatókönyvben:

![Bejelentkezés a webes alkalmazás áttekintése](./media/authentication-scenarios/basics_of_auth_in_aad.png)

A diagram felett szem előtt, az itt látható különböző összetevőinek ismernie kell:

* Azure ad-ben az identitásszolgáltató felelős a felhasználók és az alkalmazásokat, amelyek egy szervezet címtárában található identitásának ellenőrzésére, és végső soron a ezen felhasználók és alkalmazások a sikeres hitelesítést követően a biztonsági jogkivonatok kiállítása.
* Egy alkalmazás, amely az Azure AD-hitelesítés kiszervezik szeretne regisztrálni kell az Azure ad-ben, amely regisztrálja és egyedileg azonosítja az alkalmazást a címtárban.
* Fejlesztők használhatják a nyílt forráskódú Azure AD hitelesítési kódtárai megkönnyítése hitelesítési protokoll részleteinek kezeli az Ön számára. További információkért lásd: [Azure Active Directory Authentication Libraries](active-directory-authentication-libraries.md).
* Miután a felhasználó hitelesítését követően az alkalmazás ellenőrizni kell a felhasználó biztonsági jogkivonatot, győződjön meg arról, hogy a hitelesítés sikeres volt. Rendelkezünk, az alkalmazás kell mire nyelvet és keretrendszert a különböző minták [GitHub](https://github.com/Azure-Samples?q=active-directory). Az ASP.NET webalkalmazás létrehozásakor, tekintse meg a [bejelentkezés felvétele egy ASP.NET webes alkalmazás útmutató](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Ha fejleszt egy webes API-erőforrást az ASP.NET-ben, tekintse meg a [webes API-k – első lépések útmutató](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* A kérelmek és válaszok a hitelesítési folyamathoz határozzák meg a hitelesítési protokoll, amelyet használt, például az OAuth 2.0, OpenID Connect, SAML 2.0 vagy WS-Federation. Ezeket a protokollokat tárgyalja részletesen az [Azure Active Directory hitelesítési protokolljai](active-directory-authentication-protocols.md) cikk és az alábbi szakaszokban található.

> [!NOTE]
> Az Azure AD támogatja az OAuth 2.0 és OpenID Connect szabványok, amelyek kiterjedt a tulajdonosi jogkivonatokat, beleértve a tulajdonosi jogkivonatokat JWTs-kiszolgálókként. A *tulajdonosi jogkivonat* egy könnyen használható biztonsági jogkivonat, amely a "tulajdonos" védett erőforrásokhoz való hozzáférést biztosít. Ebben az értelemben a "tulajdonos" minden olyan entitás, amely a token is jelenthet. Bár egy entitás először hitelesítenie kell magát az Azure AD-fogadni a tulajdonosi jogkivonattal, ha a szükséges lépéseket a rendszer nem hajtja végre a biztonságos átvitelét és tárolását a jogkivonatot, hozzá, és egy nem kívánt entitás használják. Néhány biztonsági jogkivonatokat rendelkezik egy beépített mechanizmus megakadályozza az illetéktelen fél használja őket, amíg a tulajdonosi jogkivonatokat nem kell ezt a mechanizmust, és kell szállítani, például a transport layer security (HTTPS) biztonságos csatornán. A titkosítatlan továbbított tulajdonosi jogkivonattal, ha a man-in-the-middle támadások segítségével egy rosszindulatú fél beszerezni a jogkivonatot, és használja a védett erőforrások jogosulatlan hozzáférési. A biztonsági alapelveket alkalmazható, ha a tárolásával, sem a gyorsítótárazás a tulajdonosi jogkivonatokat későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](http://tools.ietf.org/html/rfc6750).

Most, hogy az alapokat, olvassa el az alábbiakban megismerheti, hogyan kiépítési működik az Azure ad-ben és a gyakori forgatókönyvek, amely támogatja az Azure AD áttekintést.

## <a name="claims-in-azure-ad-security-tokens"></a>Az Azure AD biztonsági jogkivonatokat a jogcím

Biztonsági jogkivonatok (hozzáférési és azonosító-jogkivonatokat), az Azure AD által kiadott jogcímeket, vagy a helyességi feltételek hitelesítését, a tulajdonos adatait tartalmaznak. Ezeket a jogcímeket a különböző feladatokhoz használhatják az alkalmazást. Ha például alkalmazások segítségével jogcímeket a jogkivonat érvényesítéséhez, directory-bérlő a tulajdonos azonosítása, felhasználói információ megjelenítése, határozza meg a tulajdonos engedélyezési és így tovább. Bármely adott biztonsági jogkivonatban található jogcímek jogkivonatot, hitelesíteni a felhasználót, és az alkalmazás konfigurációjában használt hitelesítő adat típusának típusától függenek. Az alábbi táblázat röviden az egyes Azure AD által kibocsátott jogcímek biztosítja. További információkért tekintse meg [támogatott jogkivonatok és jogcímtípusok](v1-id-and-access-tokens.md).

| Jogcím | Leírás |
| --- | --- |
| Alkalmazásazonosító | Azonosítja az alkalmazást, amely a token használja. |
| Célközönség | A jogkivonat szól a címzett erőforrás azonosítja. |
| Alkalmazás hitelesítési környezethez tartozó Osztályhivatkozása | Azt jelzi, hogy az ügyfél milyen volt a hitelesített (nyilvános ügyfél és a bizalmas ügyfél). |
| Azonnali hitelesítés | A dátum és idő, a hitelesítés fellépésének rögzíti. |
| Hitelesítési módszer | Azt jelzi, hogy a jogkivonat tárgyában hitelesítésének módját (jelszó, tanúsítvány, stb.). |
| Utónév | Biztosítja a felhasználó utóneve beállítása az Azure AD-ben. |
| Csoportok | Tartalmazza az objektum azonosítókat az Azure AD-csoportok, amely a felhasználó tagja. |
| Identitásszolgáltató | Az identitásszolgáltató a jogkivonat tárgyában hitelesített rögzíti. |
| Tulajdonos: | Az idő, amikor a jogkivonat kiállították, gyakran használt jogkivonat frissesség rögzíti. |
| Kiállító | Az STS-ben a jogkivonat, valamint az Azure AD-bérlő kibocsátott azonosítja. |
| Vezetéknév | Az Azure ad-ben beállított biztosít a felhasználó vezetékneve. |
| Name (Név) | Itt egy emberi olvasható érték, amely azonosítja a jogkivonat tárgyában. |
| Objektumazonosító | Nem módosítható, egyedi azonosítója, amelyet a tulajdonos tartalmazza az Azure ad-ben. |
| Szerepkörök | Az Azure AD alkalmazás-szerepkörök, amelyek a felhasználó megkapta-e rövid nevét tartalmazza. |
| Hatókör | Azt jelzi, hogy az ügyfélalkalmazás az engedélyeket. |
| Tárgy | Azt jelzi, hogy a rendszerbiztonsági tag arról, hogy mely a token használjon esetleg imperatív állításokat információkat. |
| Bérlőazonosító | Nem módosítható, egyedi azonosítója, amelyet a directory-bérlővel, amely kiállította a jogkivonatot tartalmazza. |
| Jogkivonat élettartama | Határozza meg az időintervallum, amelyen belül a jogkivonat érvénytelen. |
| Egyszerű felhasználónév | Tartalmazza az egyszerű felhasználónév az e-mail tárgyát. |
| Verzió | Tartalmazza a tokent verziószáma. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Egy alkalmazás regisztrálása az Azure ad-ben való használatának alapvető

Minden olyan alkalmazás, amely az Azure AD-hitelesítés outsources regisztrálni kell egy könyvtárban. Ennek a lépésnek része az Azure ad-ben mondanunk az alkalmazásról, beleértve az URL-címet, hogy hol található, küldje a választ az URI-t az alkalmazás és más azonosíthatja a hitelesítés után az URL-cím. Ez az információ akkor szükséges, néhány fő okok miatt:

* Azure ad-ben kell kommunikálnia a az alkalmazás bejelentkezési vagy cserélő jogkivonatok kezelésekor. Az Azure AD között továbbított információk és az alkalmazás a következőket tartalmazza:
  
  * **Alkalmazásazonosító URI-ja** -alkalmazás azonosítója. Ez az érték jelzi, hogy melyik alkalmazás a hívó szeretne egy tokent az Azure AD-hitelesítés során zajlik. Ezenkívül ezt az értéket tartalmazza a tokent, hogy az alkalmazás tudja, hogy ez volt a szándékolt cél.
  * **Válasz URL-cím** és **átirányítási URI-t** – egy webes API-t vagy webes alkalmazás, a válasz-URL az a hely, ahol az Azure AD elküldi a hitelesítési választ, beleértve egy jogkivonatot, ha a hitelesítés sikerült. Natív alkalmazás esetén az átirányítási URI az az egyedi azonosítója, amelyre az Azure AD átirányítja a felhasználói ügynököt az OAuth 2.0-kérelmek.
  * **Alkalmazásazonosító** -alkalmazáshoz, ha az alkalmazás regisztrálva van az Azure AD által generált az azonosító. Amikor kér az engedélyezési kód vagy a jogkivonatot, az alkalmazás Azonosítóját és kulcsát kapnak az Azure AD-hitelesítés során.
  * **Kulcs** -együtt küldött egy Alkalmazásazonosítót hitelesítéséhez az Azure AD a webes API-hívás a kulcsot.
* Az Azure AD meg kell győződnie arról az alkalmazás rendelkezik a szükséges engedélyekkel hozzáférhet a címtár adataihoz, más alkalmazásokat a szervezeten belül, és így tovább.

Kiépítés válik világosabb, miután megértette, hogy az alkalmazásokat, amelyek az alkalmazáskódok fejlesztésének és Azure AD-vel integrált két kategóriába sorolhatók:

* **Egyetlen bérlő alkalmazás** – egy egybérlős alkalmazást egy szervezeten belüli használatra szolgál. Ezek a általában az üzletági (LoB) alkalmazásokat egy vállalati fejlesztők által írt. Egy egybérlős alkalmazást csak kell a felhasználók egy címtárban lehet elérni, és ennek eredményeképpen csak kell egy címtárban ki kell építeni. Ezek az alkalmazások általában egy fejlesztő a szervezetben vannak regisztrálva.
* **Több-bérlős alkalmazás** – több-bérlős alkalmazás használatra szánt szervezetekben, nem csak egyetlen szervezet. Ezek a független szoftverszállító (ISV-k) által írt általában-as-szoftverszolgáltatások (SaaS) alkalmazások. Több-bérlős alkalmazásokhoz szükség lesz összes könyvtárban, ahol használni fogják őket, amelyhez szükség van a felhasználó vagy rendszergazda hozzájárulását, regisztrálja őket. A jóváhagyási folyamat akkor kezdődik, amikor egy alkalmazás regisztrálva van a címtárban, és a Graph API vagy akár egy másik webes API-hoz való hozzáféréssel kap. Amikor az alkalmazás regisztrál egy felhasználó vagy egy másik szervezet rendszergazdájának, jelenjenek meg ezek egy párbeszédpanel, amely megjeleníti az alkalmazáshoz szükséges engedélyeket. A felhasználó vagy rendszergazda ezután jóváhagyhatja az alkalmazáshoz, amely az alkalmazás hozzáférést biztosít a megadott adatokat, és végül regisztrálja az alkalmazást a címtárban. További információkért lásd: [a hozzájárulási keretrendszer áttekintése](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>További szempontok fejlesztése során egyetlen bérlő vagy a több-bérlős alkalmazások
Néhány további szempontot helyett egy egybérlős alkalmazást egy több-bérlős alkalmazások fejlesztése során merülnek fel. Például, ha az alkalmazás elérhető a felhasználók számára több címtár, kell meghatározni, melyik bérlőhöz egy olyan mechanizmust is a. Egy egybérlős alkalmazást kell csak egy felhasználó a saját címtárban keresse meg, amíg egy több-bérlős alkalmazást az Azure ad-ben egy adott felhasználó összes címtárakban azonosítania kell. Ennek a feladatnak, az Azure AD biztosít egy közös hitelesítési végpont, ahol minden olyan több-bérlős alkalmazás irányíthatók a bejelentkezési kérelmek, a bérlő-specifikus végpont helyett. Ez a végpont https://login.microsoftonline.com/common az Azure AD-ben minden címtár esetében, mivel lehet, hogy egy bérlő-specifikus végpont https://login.microsoftonline.com/contoso.onmicrosoft.com. A közös végpontot különösen fontos figyelembe venni, amikor az alkalmazás fejlesztése, mert a bejelentkezési, kijelentkezési, és a jogkivonat érvényesítése során több bérlő kezeléséhez szükséges logikát kell.

Ha éppen fejleszt egy egybérlős alkalmazást, de győződjön meg a rendelkezésre álló számos szervezet hasonló, egyszerűen módosításokat végezheti el az alkalmazást és annak konfigurációját az Azure ad-ben, hogy több-bérlős képes. Emellett az Azure AD használja az ugyanazon aláírókulcsot összes jogkivonat található összes, hogy meg van adva egy egybérlős vagy több-bérlős alkalmazásban hitelesítést.

A jelen dokumentumban szereplő minden egyes forgatókönyv egy alszakasz, amely leírja annak üzembe helyezési követelményeket tartalmazza. Részletesebb információ a kiépítése az alkalmazások az Azure AD és a egy vagy több-bérlős alkalmazások közötti különbségekről: [alkalmazások integrálása az Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) további információt. Az Azure ad-ben a gyakori alkalmazási helyzet megértéséhez olvassa tovább.

## <a name="application-types-and-scenarios"></a>Alkalmazástípusok és forgatókönyvek

Az itt leírt forgatókönyveket mindegyike fejleszthetők a különböző nyelvekhez és platformokhoz használatával. Ezek vannak mindehhez a szilárd hátteret a rendelkezésre álló teljes körű Kódminták a [Kódminták útmutató](sample-v1-code.md), vagy közvetlenül a megfelelő [minta GitHub-adattárak](https://github.com/Azure-Samples?q=active-directory). Emellett, ha az alkalmazásnak egy adott adatrészletet, vagy a szegmens egy végpontok közötti forgatókönyv a legtöbb esetben a funkció is hozzáadhatók egymástól függetlenül. Például ha egy natív alkalmazást, amely meghívja a webes API-k, egyszerűen hozzáadhat egy webalkalmazást, a webes API meghívásához. A következő diagram azt ábrázolja, ezek a forgatókönyvek és alkalmazástípusok, és hogyan adhatók hozzá különböző összetevők:

![Alkalmazástípusok és forgatókönyvek](./media/authentication-scenarios/application_types_and_scenarios.png)

Az Azure AD által támogatott öt elsődleges alkalmazás-forgatókönyvek a következők:

* [Webes alkalmazás webböngészőben](#web-browser-to-web-application): egy felhasználónak jelentkezzen be az Azure AD által védett webalkalmazás van szüksége.
* [Egyetlen lap alkalmazás (SPA)](#single-page-application-spa): egy felhasználónak szüksége van a bejelentkezni egy egyoldalas alkalmazás Azure AD által védett.
* [Webes API a natív alkalmazás](#native-application-to-web-api): egy natív alkalmazás egy telefonon, táblagépen vagy számítógépen futó kell hitelesíteni a felhasználót, hogy a webes API-k az Azure AD által védett erőforrások beolvasása.
* [Webes API-webalkalmazás](#web-application-to-web-api): webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.
* [Webes API-hoz démon vagy a server application](#daemon-or-server-application-to-web-api): egy démon, alkalmazás vagy a webes felhasználói felület nélkül kiszolgálói alkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.

### <a name="web-browser-to-web-application"></a>Webböngésző-webalkalmazáshoz

Ez a szakasz ismerteti egy alkalmazás, amely hitelesíti a felhasználót egy webalkalmazást a böngészőben. Ebben a forgatókönyvben a webes alkalmazás a felhasználó böngészőben jelentkezzen be őket az Azure AD irányítja. Az Azure AD választ jelentkezzen be a felhasználó böngészőjében, amely tartalmazza a biztonsági jogkivonat a felhasználóval kapcsolatos jogcímek keresztül. Ebben a forgatókönyvben támogatja a bejelentkezést a WS-Federation, SAML 2.0 és OpenID Connect protokollok használatával.

#### <a name="diagram"></a>Ábra

![A böngészőben a webalkalmazás a hitelesítési folyamat](./media/authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása

1. Amikor egy felhasználó meglátogat alkalmazáshoz, és jelentkezzen be kell, akkor a rendszer átirányítja egy bejelentkezési kérelmet a hitelesítési végpontra keresztül az Azure ad-ben.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Sikeres hitelesítés esetén a Azure ad-ben létrehoz egy hitelesítési tokent, és választ küld be az alkalmazás válasz URL-cím, amelyet az Azure Portalon. Éles alkalmazás esetén a válasz URL-cím HTTPS kell lennie. A visszaadott jogkivonat tartalmazza a jogkivonatot érvényesíteni az alkalmazás által igényelt jogcímeket a felhasználó és az Azure AD.
1. Az alkalmazás egy nyilvános aláírókulcs és issuer információk használatával érhető el, az összevonási metaadatok dokumentuma az Azure Active Directory érvényesíti a jogkivonatot. Az alkalmazás érvényesíti a jogkivonatot, miután a felhasználó egy új munkamenetet kezdődik. Ez a munkamenet a felhasználó az alkalmazás eléréséhez, amíg le nem jár.

#### <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták webböngésző webalkalmazás forgatókönyveket. És térjen vissza később gyakran – új mintát gyakran kerülnek. [Webes alkalmazás](sample-v1-code.md#web-applications).

#### <a name="registering"></a>Regisztrálása

* Egyetlen bérlő: Ha egy alkalmazás csak a szervezet számára hoz létre, azt regisztrálni kell a vállalati címtárban az Azure portal használatával.
* Több-Bérlős: Ha olyan alkalmazás, amely a szervezeten kívüli felhasználók által használható épít, azt a vállalati címtárban szerepelnie kell, de is regisztrálni kell az alkalmazást használó minden egyes szervezet címtárában. Elérhetővé teszi az alkalmazás a címtárban, megadhatja a regisztrációs folyamat, amely lehetővé teszi, hogy engedélyt adjanak az alkalmazásnak az ügyfelek számára. Amikor regisztrál az alkalmazáshoz, akkor megjelenik egy párbeszédpanel, amely tartalmazza az alkalmazáshoz szükséges engedélyeket, majd a beállítást, hogy engedélyt adjanak. Attól függően, a szükséges engedélyekkel a másik szervezet rendszergazda is szükség beleegyezés. A felhasználó vagy rendszergazda beleegyezik, az alkalmazás regisztrálva lesz a címtárban. További információkért lásd: [alkalmazások az Azure Active Directoryval való integrálását](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról

A felhasználói munkamenet lejár, ha lejár az Azure AD által kiállított jogkivonatok élettartamát. Az alkalmazás ebben az időszakban igény szerint adott ideig tartó alapuló felhasználók kijelentkeztetése például lerövidítheti. Ha a munkamenet lejár, a felhasználó kéri, jelentkezzen be újra.

### <a name="single-page-application-spa"></a>Az egyoldalas alkalmazás (SPA)

Ez a szakasz ismerteti a hitelesítés egyetlen lapon alkalmazás esetében, amelyek a webes API back end biztonságossá tételéhez használja az Azure AD és az OAuth 2.0 implicit engedélyezés biztosítanak. Egyetlen lap alkalmazások általában struktúrája rétegként JavaScript bemutató (előtérbeli) a böngésző és a egy webes API-háttéralkalmazás számára az olyan kiszolgálón fut, és valósítja meg az alkalmazás üzleti logika futtatható. Tudjon meg többet az implicit engedélyezés megadásáról, és segít eldönteni, hogy ez az alkalmazás forgatókönyvhöz jobb: [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat](v1-oauth2-implicit-grant-flow.md).

Ebben a forgatókönyvben, ha a felhasználó bejelentkezik, a JavaScript előtér-célból használ [Active Directory Authentication Library for JavaScript (adal-t. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) és a egy azonosító jogkivonat (id_token) beszerzése az Azure ad-ben az implicit engedélyezés megadásáról. A jogkivonatot a rendszer gyorsítótárazza, és az ügyfél csatlakoztatja a kérést, a tulajdonosi jogkivonatot, amikor a webes API háttéralkalmazás, amely az OWIN közbenső szoftver használatával lett biztonságossá téve. 

#### <a name="diagram"></a>Ábra

![Egyetlen lap alkalmazásdiagram](./media/authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása

1. A felhasználó ellép a webalkalmazáshoz.
1. Az alkalmazás a böngészőben a JavaScript-kezelőfelület (megjelenítési réteg) adja vissza.
1. A felhasználó bejelentkezési, például a bejelentkezési hivatkozásra kattintva indítja el. A böngészőben a GET-azonosító jogkivonat kérése az Azure AD engedélyezési végpont küld. Ezt a kérelmet az alkalmazás Azonosítóját és a válasz URL-címe tartalmazza a lekérdezési paraméterek.
1. Az Azure AD ellenőrzi a válasz URL-cím, szemben a regisztrált válasz URL-cím, amelyet az Azure Portalon.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Sikeres hitelesítés esetén az Azure AD-azonosító jogkivonat hoz létre, és visszaadja egy URL-cím töredék (#), az alkalmazás válasz URL-cím. Éles alkalmazás esetén a válasz URL-cím HTTPS kell lennie. A visszaadott jogkivonat tartalmazza a jogkivonatot érvényesíteni az alkalmazás által igényelt jogcímeket a felhasználó és az Azure AD.
1. A JavaScript-ügyfél a böngészőben futó kód kigyűjti a választ az alkalmazás webes API back end hívások biztonságossá tételéhez használja a jogkivonatot.
1. A böngésző meghívja az alkalmazás webes API vissza az engedélyezési fejléc végződhet az azonosító jogkivonat. Az Azure AD-hitelesítési szolgáltatás kiad egy azonosító jogkivonat használható tulajdonosi jogkivonattal, ha az erőforrás ugyanaz, mint az ügyfél-azonosító (ebben az esetben ez igaz, a webes API-t az alkalmazás saját háttér). 

#### <a name="code-samples"></a>Kódminták

Tekintse meg egyetlen oldal alkalmazás (SPA) forgatókönyvekhez Kódminták. Ügyeljen arra, hogy milyen gyakran visszatérnie – új mintát gyakran kerülnek. [Egyetlen lap alkalmazás (SPA)](sample-v1-code.md#single-page-applications).

#### <a name="registering"></a>Regisztrálása

* Egyetlen bérlő: Ha egy alkalmazás csak a szervezet számára hoz létre, azt regisztrálni kell a vállalati címtárban az Azure portal használatával.
* Több-Bérlős: Ha olyan alkalmazás, amely a szervezeten kívüli felhasználók által használható épít, azt a vállalati címtárban szerepelnie kell, de is regisztrálni kell az alkalmazást használó minden egyes szervezet címtárában. Elérhetővé teszi az alkalmazás a címtárban, megadhatja a regisztrációs folyamat, amely lehetővé teszi, hogy engedélyt adjanak az alkalmazásnak az ügyfelek számára. Amikor regisztrál az alkalmazáshoz, akkor megjelenik egy párbeszédpanel, amely tartalmazza az alkalmazáshoz szükséges engedélyeket, majd a beállítást, hogy engedélyt adjanak. Attól függően, a szükséges engedélyekkel a másik szervezet rendszergazda is szükség beleegyezés. A felhasználó vagy rendszergazda beleegyezik, az alkalmazás regisztrálva lesz a címtárban. További információkért lásd: [alkalmazások az Azure Active Directoryval való integrálását](quickstart-v1-integrate-apps-with-azure-ad.md).

Miután felvette az alkalmazást, azt kell konfigurálni az OAuth 2.0 típusú Implicit engedélyezés protokoll használatára. Ez a protokoll alkalmazások alapértelmezés szerint le van tiltva. Ahhoz, hogy az alkalmazás az OAuth2 típusú Implicit engedélyezés protokoll, az Azure Portalról az alkalmazásjegyzék szerkesztése és az "oauth2AllowImplicitFlow" értéket Igaz értékre van állítva. Részletes útmutatásért lásd: [engedélyezése OAuth 2.0 típusú Implicit engedélyezés egyetlen lapon alkalmazások](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról

ADAL.js használatával segít:

* egy lejárt jogkivonat frissítését
* a webes API-erőforrás meghívásához hozzáférési jogkivonatot kér

A sikeres hitelesítés után az Azure AD hozzon létre egy munkamenetet a felhasználó böngészőben a cookie-k ír. Vegye figyelembe, hogy létezik-e a munkamenet a felhasználó és az Azure ad-ben (nem a felhasználó és a webes alkalmazás közötti) között. Amikor a jogkivonat lejár, ADAL.js ehhez a munkamenethez beavatkozás nélkül juthat hozzá egy másik jogkivonatot használ. ADAL.js küldeni és fogadni a kérést, az OAuth típusú Implicit engedélyezés protokollal egy rejtett iFrame használatával. ADAL.js használatával is ugyanezzel a módszerrel a más webes API-erőforrásoknak az alkalmazás meghívja a mindaddig, amíg ezek az erőforrások támogatja az eltérő eredetű erőforrások megosztása (CORS), regisztrálva van a felhasználó könyvtárában, és minden szükséges jóváhagyás volt csendes hozzáférési tokenek beszerzése a felhasználó által megadott bejelentkezés során.

### <a name="native-application-to-web-api"></a>Natív alkalmazás webes API-hoz

Ez a szakasz ismerteti a natív alkalmazás, amely meghívja a webes API-k egy felhasználó nevében. Ebben a forgatókönyvben épül az OAuth 2.0 engedélyezési kód engedélyezési típusának nyilvános ügyfél, 4.1 szakaszában leírtak szerint a [OAuth 2.0 ismertetőjének](http://tools.ietf.org/html/rfc6749). A natív alkalmazás az OAuth 2.0 protokoll használatával szerzi be a felhasználó hozzáférési jogkivonatot. Ez a jogkivonat a kérelemben a webes API-t, amely engedélyezi a felhasználó, és adja vissza a kívánt erőforrást elküldi.

#### <a name="diagram"></a>Ábra

![Natív alkalmazás webes API-Diagram](./media/authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása

Az AD hitelesítési Kódtárai használja, ha a protokoll részletei az alábbiakban a legtöbb kezelik, például a böngészőben előugró ablak, a token-gyorsítótárazási és a frissítési biztonsági jogkivonat kezelését.

1. Egy böngészővel előugró ablakban a natív alkalmazás kérést küld az engedélyezési végpont az Azure ad-ben. A kérelem magában foglalja az Alkalmazásazonosítót és az átirányítási URI-ját a natív alkalmazás, ahogyan az az Azure Portalon, és az alkalmazás Alkalmazásazonosító URI-ja a webes API-hoz. Ha a felhasználó még nem tette meg, jelentkezzen be újra a figyelmeztetést kapnak
1. Az Azure AD akkor hitelesíti a felhasználót. Ha egy több-bérlős alkalmazásban, és az hozzájárulásra szükség az alkalmazás, a felhasználó hozzájárul, ha azok még nem tette volna meg kell adni. Engedély megadása után, és a sikeres hitelesítést követően az Azure AD kibocsát egy engedélyezési kód választ az ügyfélalkalmazásnak átirányítási URI-t.
1. Az Azure AD kibocsát egy engedélyezési kód választ az átirányítási URI-t, ha az ügyfélalkalmazás leállítja a böngésző interakció, és a hozzáférési kód kigyűjti a választ. Az engedélyezési kód használatával, az ügyfélalkalmazás kérelmet küld, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrásra vonatkozó részletek az Azure AD-jogkivonat végpontra (alkalmazás Alkalmazásazonosító URI-ja számára a webes API-t).
1. Az Azure AD érvényesíti az engedélyezési kódot, valamint az ügyfél és a webes API-val kapcsolatos információkat. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot. Emellett adja vissza az Azure AD az alapvető információkat szeretne a felhasználó, például a megjelenített nevét és a bérlő azonosítóját.
1. HTTPS-en keresztüli az ügyfélalkalmazás a kapott JWT jogkivonat a kérelem az engedélyezési fejléc a JWT-karakterlánc a "Tulajdonos" megnevezéssel hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.
1. Ha a hozzáférési jogkivonat lejár, az ügyfélalkalmazás fog egy hibaüzenetet, amely jelzi, hogy a felhasználónak újra hitelesítenie kell. Ha az alkalmazás egy érvényes frissítési jogkivonatot, akkor egy új hozzáférési jogkivonat beszerzéséhez jelentkezzen be újra, a felhasználó értesítése nélkül használható. Ha a frissítési jogkivonat lejár, az alkalmazás kell ismét interaktív módon a felhasználó hitelesítéséhez.

> [!NOTE]
> A frissítési jogkivonatot az Azure AD által kiadott több erőforrások eléréséhez használható. Például ha egy ügyfélalkalmazás, amely jogosult arra, hogy két webes API-k hívása, a frissítési jogkivonatot az beolvasni a hozzáférési jogkivonatot a más webes API-hoz is használható.

#### <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták natív alkalmazás webes API-forgatókönyvekhez. És térjen vissza később gyakran – gyakran hozzáadjuk az új mintát. [Webes API a natív alkalmazás](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Regisztrálása

* Egybérlős: Mind a natív alkalmazás és a webes API-t kell regisztrálni az ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, a natív alkalmazás férjenek hozzá az erőforrásokhoz használt konfigurálható. Az ügyfélalkalmazás ezután kiválasztja a kívánt engedélyekkel a "Engedélyeket az egyéb alkalmazások" legördülő menüből az Azure Portalon.
* Több-Bérlős: Először a natív alkalmazás csak a fejlesztői vagy regisztrált kiadó könyvtár. A második a natív alkalmazás jelzi a megfelelő működéséhez szükséges engedélyekkel van konfigurálva. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. A felhasználó vagy rendszergazda hozzájárul, csak a webes API regisztrálva lesz a címtárban. További információkért lásd: [alkalmazások az Azure Active Directoryval való integrálását](quickstart-v1-integrate-apps-with-azure-ad.md).

#### <a name="token-expiration"></a>Jogkivonat lejáratáról

A natív alkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használja, amikor a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használható újra hitelesíteni kell a felhasználó anélkül, hogy jelentkezzen be újra őket. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

### <a name="web-application-to-web-api"></a>Webalkalmazás, webes API-hoz

Ez a szakasz ismerteti egy webalkalmazást, amely a szükséges erőforrások lekérése a webes API-k. Ebben a forgatókönyvben két Összekötőtípus identitás, amellyel a webes alkalmazás hitelesítéséhez és a webes API hívása: Alkalmazásidentitás, vagy egy meghatalmazott felhasználói identitást.

*Identita aplikace:* ebben a példában az OAuth 2.0 ügyfélhitelesítő adatok hitelesítse magát az alkalmazást, és a webes API-k eléréséhez. A webes API-t csak azt észleli, hogy a webalkalmazás, hívja az Alkalmazásidentitás használatakor, a webes API-t nem kapja meg a felhasználó semmilyen információt. Az alkalmazás fogad a felhasználó adatait, ha az alkalmazás protokollon keresztül küld, és nem írta alá a Azure ad-ben. A webes API megbízik, hogy a webalkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve.

*Felhasználói identitás delegált:* ebben a forgatókönyvben két módon is elvégezhető: OpenID Connectet és az OAuth 2.0 hitelesítési kódmegadás bizalmas-ügyféllel. A webalkalmazás lekéri a hozzáférési jogkivonatot a felhasználóhoz, amely igazolja, a webes API-t, hogy a webalkalmazás sikeresen hitelesíteni a felhasználót és a webes alkalmazás volt a webes API meghívásához egy meghatalmazott felhasználói identitás szerezhetik be. A hozzáférési jogkivonatot a webes API-t, amely engedélyezi a felhasználó, és adja vissza a kívánt erőforrást. a kérelem küldése.

#### <a name="diagram"></a>Ábra

![Webalkalmazás, webes API-diagram](./media/authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása

Az alkalmazás azonosítóját és a delegált felhasználói identitás típusai a folyamat az alábbi tárgyalja. A fő különbség az, hogy a delegált felhasználó identitását először kell szerezni az engedélyezési kódot, mielőtt a felhasználó bejelentkezhet és a webes API-k eléréséhez.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace az OAuth 2.0 ügyfél-hitelesítő adatok megadása

1. A webalkalmazás az Azure AD a bejelentkezett felhasználó (lásd a [webböngésző-webalkalmazáshoz](#web-browser-to-web-application) fent).
1. A webalkalmazás kell, hogy a webes API-t hitelesítésre és a kívánt erőforrást beolvasni a hozzáférési jogkivonat beszerzése. Adatbeolvasási kérelmet küld az Azure AD jogkivonat végpontra, a hitelesítő adatokat, a Alkalmazásazonosító és a webes API-k alkalmazás Alkalmazásazonosító URI-t biztosít.
1. Az Azure AD hitelesíti magát az alkalmazást, és a webes API meghívásához használt JWT hozzáférési jogkivonatot ad vissza.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

##### <a name="delegated-user-identity-with-openid-connect"></a>A meghatalmazott felhasználó identitását az OpenID Connect

1. Egy felhasználó van bejelentkezve az Azure AD-vel webalkalmazásnak (lásd a [webalkalmazást a webböngésző](#web-browser-to-web-application) című fenti szakaszban). A webes alkalmazás a felhasználó még nem járult hozzá, így a webalkalmazás a webes API meghívásához nyújtsanak a nevében, ha a felhasználó jóváhagyást kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyekkel, nem lesz képes, hogy engedélyt adjanak a normál felhasználót a címtárban. A jóváhagyási folyamat csak több-bérlős alkalmazások, nem egybérlős alkalmazások arra vonatkozik, ahogy az alkalmazás már rendelkezik a szükséges engedélyekkel. Amikor a felhasználó bejelentkezett, a webes alkalmazás kapott egy azonosító jogkivonat a felhasználó, valamint egy hozzáférési kóddal kapcsolatos információkat.
1. Használja az Azure AD által kiállított hozzáférési kód, a webes alkalmazás egy kérést küld az Azure AD jogkivonat-végpont, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrást (Alkalmazásazonosító URI a webes API-t).
1. Az engedélyezési kódot, valamint a webalkalmazás és webes API Azure ad-ben érvényesítette. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>A meghatalmazott felhasználó identitását az OAuth 2.0 hitelesítési kódmegadás

1. A felhasználó már bejelentkezett egy webalkalmazás, amelynek hitelesítési mechanizmust független az Azure ad-ben.
1. Az alkalmazás egy hozzáférési jogkivonatot beszerezni, így a problémák az egy kérelmet a böngészőben az Azure AD engedélyezési végpont, így az alkalmazás azonosítója, és átirányítási URI a webes alkalmazás sikeres hitelesítés után az engedélyezési kódot igényel. A felhasználó bejelentkezik az Azure ad-hez.
1. A webes alkalmazás a felhasználó még nem járult hozzá, így a webalkalmazás a webes API meghívásához nyújtsanak a nevében, ha a felhasználó jóváhagyást kell. Az alkalmazás megjeleníti a szükséges engedélyekkel, és ha ezek egyikét sem rendszergazdai engedélyekkel, nem lesz képes, hogy engedélyt adjanak a normál felhasználót a címtárban. A jóváhagyás egyetlen és több-bérlős alkalmazás vonatkozik. Egyetlen bérlő esetben egy rendszergazda hajthat végre a rendszergazdai jóváhagyás való hozzájárulás azok a felhasználók nevében. Ehhez használja a `Grant Permissions` gombra a [az Azure Portal](https://portal.azure.com). 
1. Miután a felhasználó hozzájárult, a webes alkalmazás kap az engedélyezési kódot, amely a szükséges hozzáférési jogkivonat beszerzése.
1. Használja az Azure AD által kiállított hozzáférési kód, a webes alkalmazás egy kérést küld az Azure AD jogkivonat-végpont, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás (alkalmazás Azonosítóját és átirányítási URI-t), és a kívánt erőforrást (Alkalmazásazonosító URI a webes API-t).
1. Az engedélyezési kódot, valamint a webalkalmazás és webes API Azure ad-ben érvényesítette. Sikeres ellenőrzés esetén az Azure AD két jogkivonatok adja vissza: a JWT-jogkivonatokkal és a egy JWT frissítési jogkivonatot.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

#### <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták webalkalmazás, webes API-forgatókönyvekhez. És térjen vissza később gyakran – új mintát gyakran kerülnek. Webes [webes API alkalmazást](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Regisztrálása

* Egyetlen bérlő: Az alkalmazás azonosítóját és a delegált felhasználói identitás esetek, a webalkalmazás és a webes API regisztrálni kell ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, a webes alkalmazás férjenek hozzá az erőforrásokhoz használt konfigurálható. Ha egy meghatalmazott felhasználói identitástípus használatban van, a webes alkalmazás kell válassza ki a kívánt engedélyekkel a "Engedélyeket az egyéb alkalmazások" legördülő menüből az Azure Portalon. Ebben a lépésben nincs szükség, ha az identitás alkalmazástípus használatban van.
* Több-Bérlős: Először a webes alkalmazás van konfigurálva a megfelelő működéséhez szükséges engedélyek jelzi. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. Járul hozzá a felhasználó vagy rendszergazda, ha a webalkalmazás és a webes API egyaránt regisztrálva vannak a címtárban.

#### <a name="token-expiration"></a>Jogkivonat lejáratáról

Amikor a webalkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használ, a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használható újra hitelesíteni kell a felhasználó anélkül, hogy jelentkezzen be újra őket. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

### <a name="daemon-or-server-application-to-web-api"></a>Démon, vagy egy kiszolgálói alkalmazás webes API-hoz

Ez a szakasz azt ismerteti, hogy egy démon, vagy a kiszolgáló alkalmazást, amelyet erőforrások lekérése a webes API-k. Alkalmazandó az ebben a szakaszban két alárendelt forgatókönyv közül választhat: egy démon, amelyek webes API-k, az OAuth 2.0 ügyfél hitelesítő adatok engedélyezési típus; épül és a egy kiszolgálói alkalmazás (például a webes API-k), amelyek egy webes API-t OAuth 2.0-alapú meghatalmazásos draft specifikáció épül.

A forgatókönyvhöz egy démon alkalmazásnak kell webes API-hívás esetén fontos tudni, hogy néhány dolgot. Első lépésként felhasználói interakció esetén nem lehet egy démon alkalmazással, amelyhez szükség van az alkalmazás a saját identitás rendelkezik. Egy démon alkalmazás például a batch-feladat vagy egy operációs rendszer szolgáltatás fut a háttérben. Ez az alkalmazástípus egy hozzáférési jogkivonatot kér az alkalmazás azonosítóját és az Alkalmazásazonosítót, hitelesítő adat (jelszó vagy tanúsítvány) és alkalmazás bemutatása az Azure AD-Alkalmazásazonosító URI-ja. Sikeres hitelesítést követően a démon kap egy hozzáférési jogkivonatot az Azure AD-ben szereznek, majd a webes API meghívásához.

A forgatókönyvhöz kiszolgálói alkalmazás kell a webes API-hívás esetén hasznos egy példa. Tegyük fel, hogy a felhasználó a natív alkalmazások hitelesítette, és a natív alkalmazásnak kell webes API-hívás. Az Azure AD kibocsát egy JWT jogkivonat a webes API meghívásához. Ha a webes API meghívása egy másik alsóbb rétegbeli webes API-t, a alapú meghatalmazásos folyamat használhatja a felhasználói identitás delegálásához, és a második szintű webes API hitelesítésére.

#### <a name="diagram"></a>Ábra

![Démon, vagy az webes API-diagram](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Protokoll folyamat leírása

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace az OAuth 2.0 ügyfél-hitelesítő adatok megadása

1. Először a kiszolgálói alkalmazás hitelesítenie kell az Azure AD szolgáltatásba, például egy interaktív bejelentkezési párbeszédpanel emberi beavatkozás nélkül történik. Adatbeolvasási kérelmet küld az Azure AD jogkivonat végpontra, a hitelesítő adat, alkalmazás Azonosítóját és alkalmazás Alkalmazásazonosító URI-t biztosít.
1. Az Azure AD hitelesíti magát az alkalmazást, és a webes API meghívásához használt JWT hozzáférési jogkivonatot ad vissza.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>A meghatalmazott felhasználó identitását az OAuth 2.0-alapú meghatalmazásos Draft specifikáció

A folyamat az alábbiak ismertetik feltételezi, hogy a felhasználó hitelesítése egy másik alkalmazás (például egy natív alkalmazást), és az első szintű webes API hozzáférési jogkivonat beszerzése a felhasználói identitás használják.

1. A natív alkalmazás elküldi a hozzáférési jogkivonatot az első szintű webes API-t.
1. Az első szintű webes API-t egy kérést küld az Azure AD jogkivonat-végpont, így az alkalmazás azonosítója és a hitelesítő adatokat, valamint a felhasználói jogkivonat. Emellett a kérést küld-e egy on_behalf_of paraméter, amely azt jelzi, hogy a webes API-t az eredeti felhasználó nevében alsóbb rétegbeli webes API-hívás új jogkivonat kér.
1. Az Azure AD ellenőrzi, hogy az első szintű webes API-t a második szintű webes API-k elérésére jogosult, és érvényesíti a kérést, a JWT-jogkivonatokkal visszaadó jwt-t az első szintű webes API-jogkivonat frissítésére.
1. HTTPS-en keresztüli az első szintű webes API ekkor meghívja a második szintű webes API a jogkivonat-karakterláncot a kérelem az engedélyezési fejléc hozzáfűzésével. Az első szintű webes API-t továbbra is a második szintű webes API hívása, mindaddig, amíg a hozzáférési jogkivonatot és frissítési jogkivonatok érvényesek.

#### <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták démon vagy kiszolgálói alkalmazás webes API-forgatókönyvekhez. És térjen vissza később gyakran – új mintát gyakran kerülnek. [Kiszolgáló vagy alkalmazás démon a webes API-hoz](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Regisztrálása

* Egyetlen bérlő: Az alkalmazás azonosítóját és a delegált felhasználói identitás esetek, a démon vagy kiszolgálói alkalmazás regisztrálni kell ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, amelyek segítségével korlátozhatja a démon, vagy az erőforrásokhoz való hozzáférése konfigurálható. Ha egy meghatalmazott felhasználói identitástípus használatban van, a kiszolgálói alkalmazás kell válassza ki a kívánt engedélyekkel a "Engedélyeket az egyéb alkalmazások" legördülő menüből az Azure Portalon. Ebben a lépésben nincs szükség, ha az identitás alkalmazástípus használatban van.
* Több-Bérlős: Először a démon vagy kiszolgálói alkalmazás konfigurálva jelzi a megfelelő működéséhez szükséges engedélyekkel. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. Amikor a felhasználó vagy rendszergazda hozzájárul, mind a webes API-k a címtárban van regisztrálva.

#### <a name="token-expiration"></a>Jogkivonat lejáratáról

Az első alkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használja, amikor a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használható újra hitelesíteni kell a felhasználói hitelesítő adatok kérése nélkül. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

## <a name="see-also"></a>Lásd még:

[Az Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md)

[Az Azure Active Directory-Kódminták](sample-v1-code.md)

[Az Azure Active Directory Aláírókulcs vonatkozó fontos információk](active-directory-signing-key-rollover.md)

[OAuth 2.0, Azure AD-ben](https://msdn.microsoft.com/library/azure/dn645545.aspx)
