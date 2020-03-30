---
title: Az Azure AD-felhasználókba bejelentkező alkalmazások létrehozása
titleSuffix: Microsoft identity platform
description: Bemutatja, hogyan hozhat létre egy több-bérlős alkalmazás, amely bármely Azure Active Directory-bérlői felhasználó bejelentkezhet.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: e15fb60ec339eae45f9f14a3333e8afe51fc05c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480861"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Útmutató: Jelentkezzen be bármely Azure Active Directory-felhasználóhoz a több-bérlős alkalmazásminta használatával

Ha egy szoftver szolgáltatásként (SaaS) alkalmazást kínál számos szervezetnek, konfigurálhatja az alkalmazást, hogy fogadja el a bejelentkezéseket bármely Azure Active Directory (Azure AD) bérlői bejelentkezések. Ezt a konfigurációt úgy hívják, *hogy az alkalmazás több-bérlős.* Bármely Azure AD-bérlő felhasználói bejelentkezhetnek az alkalmazásba, miután beleegyeztek abba, hogy használják a fiókjukat az alkalmazással.

Ha rendelkezik egy meglévő alkalmazás, amely saját fiókrendszerrel rendelkezik, vagy támogatja a más felhőszolgáltatók tól érkező más típusú bejelentkezések, az Azure AD-bejelentkezés hozzáadása bármely bérlőegyszerű. Csak regisztrálja az alkalmazást, adja hozzá a bejelentkezési kódot az OAuth2, az OpenID Connect vagy az SAML segítségével, és helyezzen el egy ["Bejelentkezés a Microsofttal" gombot][AAD-App-Branding] az alkalmazásban.

> [!NOTE]
> Ez a cikk feltételezi, hogy már ismeri az Azure AD egyetlen bérlői alkalmazás ának létrehozásához. Ha nem, kezdje az egyik rövid útmutató a [fejlesztői útmutató honlapján][AAD-Dev-Guide].

Az alkalmazás Azure AD több-bérlős alkalmazássá alakításához négy egyszerű lépés áll:

1. [Az alkalmazásregisztráció frissítése több-bérlősnek](#update-registration-to-be-multi-tenant)
2. [A kód frissítése a /common endpoint kéréseinek küldéséhez](#update-your-code-to-send-requests-to-common)
3. [A kód frissítése több kiállítói érték kezeléséhez](#update-your-code-to-handle-multiple-issuer-values)
4. [A felhasználók és a rendszergazda hozzájárulásának megértése és a megfelelő kódmódosítások végrehajtása](#understand-user-and-admin-consent)

Nézzük meg az egyes lépéseket részletesen. Közvetlenül a mintára is ugorhat: [Hozzon létre egy több-bérlős SaaS-webalkalmazást, amely az Azure AD és az OpenID Connect használatával hívja meg a Microsoft Graph-ot.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)

## <a name="update-registration-to-be-multi-tenant"></a>Regisztráció frissítése több-bérlősnek

Alapértelmezés szerint az Azure AD-ben a webapp/API-regisztrációk egyetlen bérlő. A regisztráció több-bérlős, ha megtalálja a **támogatott fióktípusok** kapcsolja be a **hitelesítési** ablaktáblán az alkalmazás regisztráció az [Azure Portalon,][AZURE-portal] és beállítja azt **a fiókok bármely szervezeti címtárban.**

Mielőtt egy alkalmazás több-bérlős, az Azure AD megköveteli, hogy az alkalmazás alkalmazásazonosító URI-ja az alkalmazás globálisan egyedi. Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazás esetében az alkalmazásazonosító URI-nak csak a bérlőn belül kell egyedinek lennie. Több-bérlős alkalmazás esetében azonban globálisan egyedinek kell lennie, hogy az Azure AD megtalálja az alkalmazást a különböző bérlők közt. A globális egyediség azzal kényszeríthető ki, hogy a rendszer megköveteli, hogy az alkalmazásazonosító URI egy olyan egy gazdanévvel rendelkezzen, amely egyezik az Azure AD-bérlő egy ellenőrzött tartományával.

Alapértelmezés szerint az Azure Portalon keresztül létrehozott alkalmazások egy globálisan egyedi alkalmazásazonosító URI-készletbe vannak állítva az alkalmazás létrehozásához, de módosíthatja ezt az értéket. Ha például a bérlő neve contoso.onmicrosoft.com akkor egy érvényes alkalmazásazonosító URI lesz. `https://contoso.onmicrosoft.com/myapp` Ha a bérlő rendelkezik `contoso.com`a ellenőrzött tartományban, majd egy `https://contoso.com/myapp`érvényes App ID URI is. Ha az alkalmazásazonosító URI nem ezt a mintát követi, az alkalmazások több-bérlősként való konfigurálása meghiúsul.

> [!NOTE]
> A natív ügyfélregisztrációk, valamint a [Microsoft identitásplatform-alkalmazások](./active-directory-appmodel-v2-overview.md) alapértelmezés szerint több-bérlősek. Nem kell semmilyen műveletet, hogy ezek az alkalmazás regisztrációk több-bérlős.

## <a name="update-your-code-to-send-requests-to-common"></a>A kód frissítése a /common

Egyetlen bérlői alkalmazásban a bejelentkezési kérelmek et a bérlő bejelentkezési végpontra küldi. Például contoso.onmicrosoft.com esetében a végpont `https://login.microsoftonline.com/contoso.onmicrosoft.com`a következő lenne: . A bérlő i végpontra küldött kérelmek bejelentkezhetnek a bérlők felhasználóihoz (vagy vendégeihez) az adott bérlőben lévő alkalmazásokba.

Egy több-bérlős alkalmazás, az alkalmazás nem tudja előre, hogy melyik bérlő a felhasználó, így nem küldhet kérelmeket a bérlő végpontjára. Ehelyett a kérelmeket egy olyan végpontra küldi a rendszer, amely az összes Azure AD-bérlőben multiplexel:`https://login.microsoftonline.com/common`

Amikor a Microsoft identity platform kérést kap a /common endpoint, bejelentkezik a felhasználó, és ennek következtében felderíti, hogy melyik bérlő a felhasználó származik. A /common endpoint az Azure AD által támogatott összes hitelesítési protokollral működik: OpenID Connect, OAuth 2.0, SAML 2.0 és WS-Federation.

Az alkalmazásra adott bejelentkezési válasz ezután tartalmaz egy jogkivonatot, amely a felhasználót képviseli. A jogkivonat kiállítói értéke közli egy alkalmazással, hogy a felhasználó melyik bérlőtől származik. Amikor egy válasz a /common endpoint-ból ad vissza, a jogkivonatban lévő kiállítói érték a felhasználó bérlőjének felel meg.

> [!IMPORTANT]
> A /common endpoint nem bérlő, és nem kibocsátó, csak egy multiplexer. A /common használata esetén az alkalmazás ban a jogkivonatok érvényesítéséhez a logikát frissíteni kell, hogy ezt figyelembe vegyék.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>A kód frissítése több kiállítói érték kezeléséhez

A webes alkalmazások és webes API-k jogkivonatokat fogadnak és érvényesítenek a Microsoft identity platformról.

> [!NOTE]
> Míg a natív ügyfélalkalmazások kérés és jogkivonatok fogadása a Microsoft identity platform, ezt azért, hogy küldje el api-k, ahol érvényesítik. A natív alkalmazások nem érvényesítik a jogkivonatokat, és átlátszatlanként kell kezelniük őket.

Nézzük meg, hogyan érvényesíti az alkalmazás a Microsoft identity platformról kapott jogkivonatokat. Egy bérlői alkalmazás általában egy végponti értéket vesz fel, például:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

és használja, hogy építsenek egy metaadat URL-t (ebben az esetben, OpenID Connect), mint:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

a jogkivonatok érvényesítéséhez használt két kritikus információ letöltéséhez: a bérlő aláíró kulcsait és kiállítói értékét. Minden Azure AD-bérlő rendelkezik az űrlap egyedi kiállítói értékével:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

ahol a GUID érték a bérlői azonosító átnevezés-biztonságos verziója. Ha az előző metaadat-hivatkozást választja a hoz, `contoso.onmicrosoft.com`akkor ez a kibocsátói érték látható a dokumentumban.

Amikor egy bérlői alkalmazás érvényesít egy jogkivonatot, ellenőrzi a jogkivonat aláírását a metaadat-dokumentum aláíró kulcsaival. Ez a teszt lehetővé teszi, hogy győződjön meg arról, hogy a kibocsátó értéke a jogkivonat megegyezik a metaadat-dokumentumban található.

Mivel a /common endpoint nem felel meg a bérlőnek, és nem kibocsátó, amikor a /common metaadatában a kibocsátó értékét vizsgálja, a tényleges érték helyett sablonos URL-címmel rendelkezik:

    https://sts.windows.net/{tenantid}/

Ezért egy több-bérlős alkalmazás nem érvényesítheti a jogkivonatokat csak a metaadatok kibocsátói értékének a `issuer` jogkivonatban lévő értékegyeztetésével. A több-bérlős alkalmazás logikára van szüksége annak eldöntéséhez, hogy mely kibocsátóértékek érvényesek, és melyek nem a kibocsátó érték bérlői azonosító részén alapulnak. 

Például ha egy több-bérlős alkalmazás csak lehetővé teszi a bejelentkezést az adott bérlők, akik feliratkozott `tid` a szolgáltatásra, majd ellenőriznie kell a kibocsátó értékét, vagy a jogcím értéke a jogkivonatban, hogy megbizonyosodjon arról, hogy a bérlő szerepel az előfizetők listájában. Ha egy több-bérlős alkalmazás csak egyénekkel foglalkozik, és nem hoz meg semmilyen hozzáférési döntéseket a bérlők alapján, akkor teljesen figyelmen kívül hagyhatja a kibocsátó értékét.

A [több-bérlős mintákban][AAD-Samples-MT]a kiállító érvényesítése le van tiltva, hogy bármely Azure AD-bérlő jelentkezzen be.

## <a name="understand-user-and-admin-consent"></a>A felhasználók és a rendszergazdai jóváhagyás megismerése

Ahhoz, hogy egy felhasználó jelentkezzen be egy alkalmazásba az Azure AD-ben, az alkalmazás nak meg kell jelenlennie a felhasználó bérlője. Ez lehetővé teszi a szervezet számára, hogy olyan dolgokat, mint például egyedi szabályzatok alkalmazása, amikor a felhasználók a bérlő bejelentkezik az alkalmazásba. Egyetlen bérlői alkalmazás esetén ez a regisztráció egyszerű; ez az, ami akkor történik, amikor regisztrálja az alkalmazást az [Azure Portalon.][AZURE-portal]

Egy több-bérlős alkalmazás esetén az alkalmazás kezdeti regisztrációja a fejlesztő által használt Azure AD-bérlőben él. Amikor egy másik bérlő felhasználója először jelentkezik be az alkalmazásba, az Azure AD megkéri őket, hogy járuljanak hozzá az alkalmazás által kért engedélyekhez. Ha ehhez hozzájárulnak, akkor az alkalmazás egy *egyszerű szolgáltatás* nevű ábrázolása jön létre a felhasználó bérlőjében, és a bejelentkezés folytatódhat. A delegálás abban a címtárban is létrejön, amely rögzíti a felhasználó hozzájárulását az alkalmazáshoz. Az alkalmazás alkalmazás- és ServicePrincipal objektumairól, valamint az egymáshoz való viszonyukról az [Alkalmazásobjektumok és az egyszerű szolgáltatásobjektumok][AAD-App-SP-Objects]című témakörben talál részleteket.

![Az egyrétegű alkalmazáshoz való hozzájárulás szemléltetése][Consent-Single-Tier]

Ezt a jóváhagyási élményt az alkalmazás által kért engedélyek befolyásolják. A Microsoft identity platform kétféle, csak alkalmazásra vonatkozó és delegált engedélyt támogat.

* A delegált engedély lehetővé teszi az alkalmazás számára, hogy bejelentkezett felhasználóként működjön a felhasználó által megtehető adatok egy részhalmaza esetén. Például megadhat egy alkalmazásnak a delegált engedélyt a bejelentkezett felhasználó naptárának olvasására.
* Csak alkalmazásengedély közvetlenül az alkalmazás identitásához adható meg. Például adhat egy alkalmazás csak az alkalmazás csak engedélyt olvasni a felhasználók listáját a bérlő, függetlenül attól, hogy ki van bejelentkezve az alkalmazásba.

Egyes engedélyeket egy normál felhasználó is jóváírhat, míg mások bérlői rendszergazda beleegyezését igénylik. 

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

Csak alkalmazás engedélyek mindig bérlői rendszergazda beleegyezését igényli. Ha az alkalmazás csak alkalmazásengedélyt kér, és egy felhasználó megpróbál bejelentkezni az alkalmazásba, hibaüzenet jelenik meg, amely szerint a felhasználó nem tud hozzájárulni.

Bizonyos delegált engedélyekhez bérlői rendszergazda hozzájárulása is szükséges. Például az a képesség, hogy írjon vissza az Azure AD-be, mint a bejelentkezett felhasználó bérlői rendszergazda beleegyezését igényel. A csak alkalmazásengedélyekhez hasonlóan, ha egy átlagos felhasználó megpróbál bejelentkezni egy olyan alkalmazásba, amely rendszergazdai jóváhagyást igénylő delegált engedélyt kér, az alkalmazás hibaüzenetet kap. Azt, hogy egy engedély hez rendszergazdai hozzájárulás szükséges-e, az erőforrást közzétenni közzétő fejlesztő határozza meg, és megtalálható az erőforrás dokumentációjában. A [Microsoft Graph API][MSFT-Graph-permission-scopes] engedélydokumentációja jelzi, hogy mely engedélyekhez szükséges a rendszergazda hozzájárulása.

Ha az alkalmazás rendszergazdai jóváhagyást igénylő engedélyeket használ, rendelkeznie kell egy kézmozdulattal, például egy gombbal vagy hivatkozással, ahol a rendszergazda kezdeményezheti a műveletet. A kérelem, amelyet az alkalmazás küld erre a műveletre, a szokásos `prompt=admin_consent` OAuth2/OpenID Connect engedélyezési kérelem, amely a lekérdezési karakterlánc paramétert is tartalmazza. Miután a rendszergazda hozzájárult, és a szolgáltatásnév jön létre az ügyfél bérlője, `prompt=admin_consent` a későbbi bejelentkezési kérelmek nem kell a paraméter. Mivel a rendszergazda úgy döntött, hogy a kért engedélyek elfogadhatók, a bérlő más felhasználóitól attól a ponttól kezdve nem kér jóváhagyást.

A bérlői rendszergazda letilthatja a rendszeres felhasználók számára az alkalmazások beleegyezését. Ha ez a funkció le van tiltva, rendszergazdai hozzájárulás mindig szükséges az alkalmazás a bérlőben való használathoz. Ha úgy szeretné tesztelni az alkalmazást, hogy a végfelhasználói hozzájárulás le van tiltva, a konfigurációs kapcsolót az [Azure Portalon][AZURE-portal] a Vállalati **alkalmazások**csoport **[Felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** szakaszában találja.

A `prompt=admin_consent` paramétert olyan alkalmazások is használhatják, amelyek rendszergazdai jóváhagyást nem igényelnek engedélyeket. Egy példa arra, hogy mikor lenne használva, ha az alkalmazás olyan felhasználói felületet igényel, amelyben a bérlői rendszergazda "egyszer "regisztrál", és ettől a ponttól kezdve más felhasználók nem kérnek beleegyezést.

Ha egy alkalmazás rendszergazdai jóváhagyást igényel, `prompt=admin_consent` és egy rendszergazda jelentkezik be a paraméter elküldése nélkül, amikor a rendszergazda sikeresen hozzájárul az alkalmazáshoz, **akkor csak a felhasználói fiókjára**vonatkozik. A rendszeres felhasználók továbbra sem tudnak bejelentkezni vagy hozzájárulni az alkalmazáshoz. Ez a szolgáltatás akkor hasznos, ha azt szeretné, hogy a bérlői rendszergazda képes legyen az alkalmazás felfedezése előtt, mielőtt más felhasználók számára hozzáférést.

> [!NOTE]
> Egyes alkalmazások olyan felhasználói felületet szeretnének, amelyben a rendszeres felhasználók kezdetben beleegyezhetnek, és később az alkalmazás bevonhatja a rendszergazdát, és rendszergazdai jóváhagyást igénylő engedélyeket kérhet. Ezt nem lehet megtenni egy v1.0-s alkalmazás regisztrációval az Azure AD-ben még ma; azonban a Microsoft identity platform (v2.0) végpont használatával lehetővé teszi az alkalmazások számára, hogy engedélyt kérjenek futásidőben a regisztráció helyett, ami lehetővé teszi ezt a forgatókönyvet. További információt a [Microsoft identity platform végpontja][AAD-V2-Dev-Guide]című témakörben talál.

### <a name="consent-and-multi-tier-applications"></a>Hozzájárulás és többszintű alkalmazások

Előfordulhat, hogy az alkalmazás több réteg, amelyek mindegyike saját regisztrációval rendelkezik az Azure AD-ben. Például egy natív alkalmazás, amely meghívja a webes API-t, vagy egy webes API-t meghívja. Mindkét esetben az ügyfél (natív alkalmazás vagy webalkalmazás) engedélyt kér az erőforrás (webes API) hívásához. Ahhoz, hogy az ügyfél sikeresen hozzájárult az ügyfél bérlője, minden erőforrás, amelyhez engedélyeket kér, már léteznie kell az ügyfél bérlőjében. Ha ez a feltétel nem teljesül, az Azure AD egy hibát ad vissza, hogy az erőforrást először hozzá kell adni.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Több réteg egy bérlőben

Ez akkor lehet probléma, ha a logikai alkalmazás két vagy több alkalmazásregisztrációból áll, például egy külön ügyfélből és erőforrásból. Hogyan juthat be először az erőforrás az ügyfél-bérlőbe? Az Azure AD lefedi ezt az esetet azáltal, hogy lehetővé teszi az ügyfél és az erőforrás egyetlen lépésben történő hozzájárulása. A felhasználó az ügyfél és az erőforrás által kért engedélyek összegét látja a hozzájárulási lapon. A viselkedés engedélyezéséhez az erőforrás alkalmazásregisztrációjának tartalmaznia kell az `knownClientApplications` ügyfél alkalmazásazonosítóját az [alkalmazásjegyzékben.][AAD-App-Manifest] Példa:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Ezt egy többrétegű natív ügyfél hívása webes API-minta a [kapcsolódó tartalom](#related-content) szakaszban a cikk végén. Az alábbi ábra áttekintést nyújt az egyetlen bérlőben regisztrált többrétegű alkalmazás hozzájárulásáról.

![A többrétegű ismert ügyfélalkalmazáshoz való hozzájárulás szemléltetése][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Több réteg több bérlőben

Hasonló eset történik, ha az alkalmazás különböző szintjei különböző bérlőkben vannak regisztrálva. Vegyük például az Office 365 Exchange Online API-t megadó natív ügyfélalkalmazás létrehozásának esetét. A natív alkalmazás fejlesztéséhez, majd később a natív alkalmazás futtatásához az ügyfél bérlőjében, az Exchange Online egyszerű szolgáltatásának jelen kell lennie. Ebben az esetben a fejlesztőnek és az ügyfélnek meg kell vásárolnia az Exchange Online-t a bérlőikben létrehozandó egyszerű szolgáltatáshoz.

Ha ez egy olyan API, amelyet nem a Microsoft, egy olyan API-t, amelyet nem a Microsoft, az API fejlesztőjének meg kell adnia a módját, hogy ügyfelei beleegyezzenek az alkalmazásba az ügyfelek bérlőibe. Az ajánlott kialakítás a külső fejlesztő számára az API-t úgy hozhatja létre, hogy webes ügyfélként is működjön a regisztráció megvalósításához. Ehhez tegye a következőket:

1. Kövesse a korábbi szakaszokat annak biztosításához, hogy az API megvalósítsa a több-bérlős alkalmazás regisztrációs/kód követelményeit.
2. Az API hatókörének/szerepköreinek felfedése mellett győződjön meg arról, hogy a regisztráció tartalmazza a "Bejelentkezés és olvasás felhasználói profil" engedélyt (alapértelmezés szerint biztosított).
3. Valósítson meg egy bejelentkezési/regisztrációs lapot a webes ügyfélben, és kövesse a [rendszergazdai hozzájárulási](#admin-consent) útmutatást.
4. Miután a felhasználó hozzájárul az alkalmazáshoz, a szolgáltatásnévés a hozzájárulásdelegálási hivatkozások jönnek létre a bérlőben, és a natív alkalmazás kaphat jogkivonatokat az API-hoz.

Az alábbi ábra áttekintést nyújt a különböző bérlőkben regisztrált többrétegű alkalmazások hozzájárulásáról.

![A többrétegű többrésztvevős alkalmazáshoz való hozzájárulás szemléltetése][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>A hozzájárulás visszavonása

A felhasználók és a rendszergazdák bármikor visszavonhatják az alkalmazáshoz való hozzájárulást:

* A felhasználók visszavonják az egyes alkalmazásokhoz való hozzáférést azáltal, hogy eltávolítják őket a [hozzáférési panel alkalmazások][AAD-Access-Panel] listájáról.
* A rendszergazdák visszavonják az alkalmazásokhoz való hozzáférést azáltal, hogy eltávolítják őket az [Azure Portal][AZURE-portal] [Enterprise-alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) szakaszával.

Ha egy rendszergazda hozzájárul egy alkalmazás hoz egy bérlő összes felhasználója, a felhasználók nem vonhatja vissza a hozzáférést külön-külön. Csak a rendszergazda vonhatja vissza a hozzáférést, és csak az egész alkalmazásra vonatkozóan.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Több-bérlős alkalmazások és gyorsítótárazási jogkivonatok

A több-bérlős alkalmazások is kaphatnak hozzáférési jogkivonatokat az Azure AD által védett API-k hívásához. Gyakori hiba, ha az Active Directory hitelesítési könyvtárat (ADAL) több-bérlős alkalmazással használja, hogy kezdetben a /common használatával rendelkező felhasználó számára kér jogkivonatot, választ kap, majd egy későbbi jogkivonatot kér ugyanahhoz a felhasználóhoz a /common használatával is. Mivel az Azure AD válasza egy bérlőtől származik, nem /common, az ADAL gyorsítótárazza a jogkivonatot a bérlőtől. A következő hívás /common-hoz kap egy hozzáférési jogkivonatot a felhasználó hiányzik a gyorsítótár-bejegyzés, és a felhasználó kéri, hogy jelentkezzen be újra. Annak elkerülése érdekében, hogy hiányzik a gyorsítótár, győződjön meg arról, hogy a már bejelentkezett felhasználó további hívásai a bérlő végpontjára kerülnek.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egy alkalmazást, amely bármely Azure AD-bérlőből bejelentkezhet egy felhasználót. Miután engedélyezte az egyszeri bejelentkezést az alkalmazás és az Azure AD között, frissítheti az alkalmazást a Microsoft-erőforrások, például az Office 365 által elérhetővé tett API-k eléréséhez. Ez lehetővé teszi, hogy személyre szabott élményt nyújtson az alkalmazásban, például környezeti információkat jelenítsen meg a felhasználóknak, például a profilképüket vagy a következő naptári találkozót. Ha többet szeretne megtudni az Azure AD és az Office 365-szolgáltatások , például az Exchange, a SharePoint, a OneDrive, a OneNote és más szolgáltatásokba történő API-hívásokról, látogasson el a [Microsoft Graph API-ba.][MSFT-Graph-overview]

## <a name="related-content"></a>Kapcsolódó tartalom

* [Több-bérlős alkalmazásminta](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Védjegyzési útmutató az alkalmazásokhoz][AAD-App-Branding]
* [Alkalmazásobjektumok és egyszerű szolgáltatásobjektumok][AAD-App-SP-Objects]
* [Alkalmazások integrálása az Azure Active Directoryval][AAD-Integrating-Apps]
* [A hozzájárulási keretrendszer áttekintése][AAD-Consent-Overview]
* [Microsoft Graph API-engedélyezési hatókörök][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
