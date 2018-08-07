---
title: Hogyan hozhat létre egy alkalmazást, amely képes az Azure AD-felhasználók bejelentkeztetése
description: Bemutatja, hogyan hozhat létre egy több-bérlős alkalmazást, amely egy Azure Active Directory felhasználói bejelentkezhet.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2018
ms.author: celested
ms.reviewer: elisol
ms.custom: aaddev
ms.openlocfilehash: 3d221de12d18ff3153ff0e7f7882ee4bafb9ca64
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581466"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Hogyan lehet Azure Active Directory-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával
Ha egy alkalmazás számos szervezet hasonló, konfigurálhatja az alkalmazás minden olyan Azure Active Directory (AD) bérlő történő bejelentkezések fogadására. Ez a konfiguráció nevezzük, így az alkalmazás több-bérlős. Minden olyan Azure AD-bérlőben felhasználók tudják, beleegyezik abba, hogy az alkalmazás-fiókjuk használata után az alkalmazásnak bejelentkezni. 

Ha rendelkezik egy meglévő alkalmazást, amely a saját fiók rendszerrel rendelkezik, vagy egyéb felhőszolgáltatók történő bejelentkezések egyéb típusú támogatja, hozzáadása az Azure AD-be minden bérlő használata egyszerű. Csak az alkalmazás regisztrálására, adja hozzá az OAuth2, OpenID Connect vagy SAML bejelentkezési kódot, és helyezze egy ["Sign In with Microsoft" gomb] [ AAD-App-Branding] az alkalmazásban.

> [!NOTE] 
> Ez a cikk feltételezi, hogy már ismeri az Azure AD-hez egy egybérlős alkalmazást készít. Ha nem Ön, először érdemes egy a rövid útmutatók a a [fejlesztői útmutató kezdőlapja][AAD-Dev-Guide].

Az alkalmazás alakítható át egy Azure ad-ben több-bérlős alkalmazás négy egyszerű lépésben történik:

1. [Frissíteni kell a több-bérlős az alkalmazás regisztrálása](#update-registration-to-be-multi-tenant)
2. [Frissítse a kódot, hogy kérelmeket küldjön a/Common végpont](#update-your-code-to-send-requests-to-common)
3. [Több kiállítók a következők kezeléséhez a kód frissítése](#update-your-code-to-handle-multiple-issuer-values)
4. [Megismerheti a felhasználói és rendszergazdai hozzájárulás, és adja meg a megfelelő kód módosítása](#understanding-user-and-admin-consent)

Tekintsük át részletesen minden lépése. Emellett egyenesen is ugorhat [ezen a listán szereplő minták több-bérlős][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Frissíteni kell a több-bérlős regisztrációt
Alapértelmezés szerint a web app és az API regisztrációkat az Azure ad-ben egyetlen új bérlő. Akkor is használhatja a regisztrációs több-bérlős keresése a **több ábráját** kapcsolható be a a **tulajdonságok** ablaktábláján az alkalmazás regisztrálása az a [az Azure portal] [ AZURE-portal] értékre állítaná, és **Igen**.

Egy alkalmazás több-bérlős lehet tenni, mielőtt az Azure AD szükséges az Alkalmazásazonosító URI-t az alkalmazás globálisan egyedinek kell lennie. Az Alkalmazásazonosító URI az az alkalmazás a protokollüzenetekről azonosítja módszerekkel egyik. Egybérlős alkalmazás esetén a rendszer megfelelő az Alkalmazásazonosító URI a bérlőn belül egyedinek kell lennie. Több-bérlős alkalmazás esetében, globálisan egyedinek kell lennie, az Azure ad-ben található az alkalmazás az összes bérlőre kiterjedő. Globális egyedi-e kényszerítve van azzal, hogy az Alkalmazásazonosító URI-t szeretné, hogy egy állomásnevet, amely megfelel az Azure AD-bérlő ellenőrzött tartományt. Alapértelmezés szerint az Azure Portalon létrehozott alkalmazás rendelkezik egy globálisan egyedi Alkalmazásazonosító URI-t állítsa be az alkalmazás létrehozása, de módosíthatja ezt az értéket.

Például, ha a bérlő nevével contoso.onmicrosoft.com és a egy érvényes App ID URI lenne `https://contoso.onmicrosoft.com/myapp`. Ha a bérlő ellenőrzött tartományának `contoso.com`, és a egy érvényes Alkalmazásazonosító URI-t is lenne `https://contoso.com/myapp`. Ha az Alkalmazásazonosító URI nem követi a ebben a mintában beállítása egy alkalmazást, több-bérlős sikertelen lesz.

> [!NOTE] 
> Natív ügyfél-regisztrációk, valamint [v2 alkalmazások](./active-directory-appmodel-v2-overview.md) több-bérlős alapértelmezés szerint. Nem kell semmit sem, hogy ezek alkalmazásregisztrációkat, több-bérlős.

## <a name="update-your-code-to-send-requests-to-common"></a>Kérések/Common küldéséhez a kód frissítése
Bejelentkezési kérelmek egy egybérlős alkalmazást, a bérlő bejelentkezési végpont kell küldeni. Ha például a contoso.onmicrosoft.com a végpont a következő lesz: `https://login.microsoftonline.com/contoso.onmicrosoft.com`

Egy bérlő végpontra küldött kérelmeket a felhasználókat (vagy a vendégek) bejelentkezhet a bérlőt, az adott bérlőn belüli alkalmazások. Egy több-bérlős alkalmazással az alkalmazás nem ismert meghozni milyen bérlő, a felhasználó nem származik, így Ön nem végpontra kérést küldhet egy bérlőt. Ehelyett küld egy végpontot, amely minden Azure AD-bérlőre kiterjedő eszközalkalmazásnál: `https://login.microsoftonline.com/common`

Ha az Azure AD meg az/Common kérést kap végpont, azt a felhasználó bejelentkezik, és, következésképpen felderíti a felhasználó melyik bérlőhöz. A/közös végpont működik, az összes az Azure AD által támogatott hitelesítési protokollok: OpenID Connect, az OAuth 2.0, az SAML 2.0 és WS-Federation.

Az alkalmazás ezután a bejelentkezési választ a felhasználó jelképező jogkivonatot tartalmazza. A jogkivonat a kibocsátó értékét arra utasítja a alkalmazás milyen a felhasználó nem a bérlő. Ha választ adja vissza a/Common végpont, a kibocsátó értékét a jogkivonat felel meg a felhasználó bérlőjéhez. 

> [!IMPORTANT]
> A/közös végpontja nem a bérlő pedig nem kibocsátó, csak a multiplexer. / Common használatakor a jogkivonatainak érvényesítéséhez szükséges az alkalmazás logikáját ezt figyelembe kell venni frissíteni kell. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Több kiállítók a következők kezeléséhez a kód frissítése
Webalkalmazások és webes API-k fogadása, és érvényesítse az Azure ad-ből. 

> [!NOTE]
> Natív ügyfélalkalmazások kérelem, és az Azure AD-ből származó jogkivonatokat fogadni, ehhez a API-khoz, ahol a rendszer érvényesíti el őket. Natív alkalmazások ne ellenőrizze a jogkivonatokat, és kell kezelnie, nem átlátszó.

Nézzük meg, miként ellenőrzi az alkalmazás a jogkivonatokat kap az Azure ad-ből. Egy egybérlős alkalmazást általában vesz igénybe egy végpont értékét, például:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

és a segítségével hozza létre a metaadatok URL-cím (ebben az esetben az OpenID Connect) például:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

két fontos információt jogkivonatainak érvényesítéséhez használt letöltése: a bérlő általi aláírásának kulcsokat és a kibocsátó értékét. Minden egyes Azure AD-bérlő egyedi kiállítót értéke a következő formában:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

ahol a GUID érték a bérlő Azonosítóját a bérlő átnevezése szálbiztos verzióját. Ha az előző metaadatok hivatkozására `contoso.onmicrosoft.com`, láthatja, hogy a dokumentum a kibocsátó értékét.

Amikor egy egybérlős alkalmazást érvényesíti a jogkivonatot, ellenőrzi a metaadat-dokumentumból az aláíró kulcsok ellen a jogkivonat aláírása. Ez a teszt lehetővé teszi, hogy ellenőrizze, hogy a kibocsátó értékét a jogkivonat illeszkedik, amely a metaadat-dokumentumban található.

Mivel a/közös végpont nem felel meg a bérlőt, és nem egy kibocsátó, ha megvizsgálja a kibocsátó értékét az metaadatok/közös rendelkezik egy sablonalapú URL-cím helyett egy tényleges érték:

    https://sts.windows.net/{tenantid}/

Ezért több-bérlős alkalmazás nem érvényesítse a kibocsátó értékét a metaadatoknak a egyeztetésével csak a `issuer` érték a jogkivonatban. Több-bérlős alkalmazás eldönteni, melyik kiállítók a következők érvényesek, és a bérlői azonosító részét a kibocsátó értékét nem alapuló logikai van szüksége. 

Például ha egy több-bérlős alkalmazást csak lehetővé teszi, hogy jelentkezzen be az adott bérlő regisztrált a szolgáltatásukhoz, majd, ellenőriznie kell a kibocsátó értékét, vagy a `tid` jogcímértékű győződjön meg arról, hogy a bérlő szerepel a lista-előfizetők, hogy a jogkivonat. Ha több-bérlős alkalmazás csak egyének foglalkozik, és nem döntéseket minden olyan hozzáférési bérlők alapján, majd azt figyelmen kívül hagyhatja a kibocsátó értékét elő.

Az a [minták több-bérlős][AAD-Samples-MT], kibocsátó ellenőrzése le van tiltva, ahhoz, hogy minden olyan Azure AD-bérlővel való bejelentkezéshez.

## <a name="understanding-user-and-admin-consent"></a>Understanding felhasználói és rendszergazdai jóváhagyás
Jelentkezzen be az Azure AD-alkalmazás egy felhasználó az alkalmazás meg kell jelennie a felhasználó bérlőjéhez. Ez lehetővé teszi a szervezet van például egyedi házirendek alkalmazása a felhasználók bejelentkezésekor a bérlők közül az alkalmazáshoz. Egybérlős alkalmazás esetében a regisztráció az egyszerű; azt, amelyik akkor fordul elő, amikor regisztrálja az alkalmazás legyen a [az Azure portal][AZURE-portal].

Több-bérlős alkalmazás esetében a kezdeti regisztráció az alkalmazás fejlesztője által használt Azure AD-bérlő él. Ha egy felhasználó egy másik bérlőben történő az alkalmazás első alkalommal jelentkezik be, az Azure AD megkéri, hogy engedélyt adjanak az alkalmazás által kért engedélyeket. Azok a hozzájárulás, akkor az alkalmazás reprezentációját nevű egy *szolgáltatásnév* jön létre a felhasználó bérlőjéhez, és jelentkezzen be. A delegálás is jön létre, amely rögzíti a felhasználó beleegyezésével az alkalmazáshoz a címtárban. További információ az alkalmazás alkalmazás- és ServicePrincipal objektumot, és hogyan kapcsolódnak egymáshoz: [alkalmazásobjektumok és egyszerű szolgáltatási objektumok][AAD-App-SP-Objects].

![Egyrétegű alkalmazásba hozzájárulás megadása][Consent-Single-Tier] 

A jóváhagyási felületen befolyásolja az alkalmazás által kért engedélyeket. Azure ad-ben kétféle engedélyek, csak az alkalmazásra vonatkozó és a delegált támogatja.

* Delegált engedély teheti lehetővé teszi, hogy a dolgot egyik részhalmazához bejelentkezett felhasználóként a felhasználó alkalmazások számára biztosít. Ha például biztosíthat egy alkalmazás olvasni a bejelentkezett felhasználó naptár delegált engedély.
* Egy csak az alkalmazásra vonatkozó engedélyt közvetlenül az alkalmazás identitását. Például biztosíthat az alkalmazás csak az alkalmazásra vonatkozó olvasásához szükséges engedély a bérlőben, függetlenül attól, aki be van jelentkezve az alkalmazás felhasználók listáját.

Bizonyos jogosultságokat is lehet fogadta el a normál felhasználói, míg mások egy bérlői rendszergazdai jóváhagyást igényelnek. 

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás
Csak az alkalmazásra vonatkozó engedélyeket mindig szükség van egy bérlői rendszergazdai jóváhagyást. Ha az alkalmazás egy csak az alkalmazásra vonatkozó engedélyt kér, és a egy felhasználó megpróbál bejelentkezni az alkalmazásba, egy hibaüzenet jelenik meg arról, hogy a felhasználó nem tudja, hogy engedélyt adjanak.

Egyes delegált engedélyeket is egy bérlői rendszergazdai jóváhagyás szükséges. Írási vissza az Azure AD a bejelentkezett felhasználó, például egy Bérlői rendszergazda jóváhagyást igényel. Csak az alkalmazásra vonatkozó engedélyeket, mint egy átlagos felhasználó próbál bejelentkezni az olyan alkalmazás, amely kéri a rendszergazdai jóváhagyást, igénylő delegált engedély az alkalmazás kap hibaüzenetet. Engedélyt igényel-e rendszergazdai jóváhagyásra határozza meg a fejlesztői, az erőforrás közzétett el, és az erőforrás a dokumentációban található. Az engedélyek dokumentációját a [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] és [Microsoft Graph API] [ MSFT-Graph-permision-scopes] azt jelzik, milyen engedélyeket igényel rendszergazdai hozzájárulás megadása.

Az alkalmazás használja az engedélyeket, amelyek rendszergazdai jóváhagyás megkövetelése, ha szüksége van egy kézmozdulatot, például egy gomb vagy hivatkozás, ahol a rendszergazda kezdeményezhet a művelet. Az alkalmazás által esetében ez a művelet nem a szokásos OAuth2/OpenID Connect engedélyezési kérést, amely szintén tartalmazza a kérés a `prompt=admin_consent` lekérdezési karakterlánc paraméter. A rendszergazda hozzájárult, és az egyszerű szolgáltatás létrehozása az ügyfél-bérlőben, nem kell további bejelentkezési kérések a `prompt=admin_consent` paraméter. A rendszergazda úgy döntött, a kért engedélyeket is, mivel nem a bérlő többi felhasználójával a rendszer ettől kezdve beleegyezését kéri.

Bérlői rendszergazda letilthatja a normál felhasználók, hogy engedélyt adjanak az alkalmazások. Ez a funkció le van tiltva, ha rendszergazdai jóváhagyás mindig szükség az alkalmazás a bérlő által használandó. Ha szeretné az alkalmazás tesztelése a végfelhasználó hozzájárulását le van tiltva, Észreveheti, hogy a konfigurációs kapcsoló a [az Azure portal] [ AZURE-portal] a a **[felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** szakaszba **vállalati alkalmazások**.

A `prompt=admin_consent` paraméterrel is használhatja az alkalmazásokat, amelyek nem igénylik a rendszergazdai jóváhagyás engedélyek kéréséhez. Lehet, ha a használni kívánt például, ha az alkalmazás egy környezetet, ahol a bérlői rendszergazda "regisztrál" egy időben, és nincs más felhasználók ettől kezdve beleegyezését megerősítését.

Ha egy alkalmazás rendszergazdai jóváhagyást igénylő és nélkül bejelentkezik egy rendszergazda a `prompt=admin_consent` küldi el, ha a rendszergazda sikeresen járul hozzá a alkalmazni fogja az alkalmazás paraméter **csak a felhasználói fiókjuk**. Normál felhasználók továbbra is nem képesek lesznek a bejelentkezéshez vagy a hozzájárulás az alkalmazáshoz. Ez a funkció akkor hasznos, ha lehetővé teheti a bérlői rendszergazda Fedezze fel az alkalmazást, mielőtt más felhasználóknak hozzáférést szeretne.

> [!NOTE]
> Egyes alkalmazások szeretné egy felület, ahol a normál felhasználók be tudnak kezdetben jóváhagyást, és később a kérelmet is magában foglalhat a rendszergazda és a kérés engedélyeket, amelyek rendszergazdai jóváhagyásra van szükség. Semmilyen módon nem lehet elvégezni egy v1 alkalmazás regisztrálása az Azure AD ma; azonban a v2 végpont használata lehetővé teszi az alkalmazások engedélyek kéréséhez helyett futásidőben regisztrációs időpontban, amely lehetővé teszi, hogy ebben a forgatókönyvben. További információkért lásd: a [v2 végpont][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Jóváhagyás és Többrétegű alkalmazások
Az alkalmazásnak lehetnek több réteget, mindegyik a saját regisztrációs által jelölt Azure AD-ben. Például egy natív alkalmazást, amely meghívja a webes API-hoz vagy egy webalkalmazást, amely meghívja a webes API-t. Mindkét ezekben az esetekben az ügyfél (a natív alkalmazás vagy a web app) kér engedélyeket hívja az erőforrás (webes API-t). Az ügyfél lehet sikeresen megadta a hozzájárulást az ügyfél-bérlőbe minden erőforrás, amely engedélyt kér már léteznie kell az ügyfél-bérlőben. Ha ez a feltétel nem teljesül, az Azure AD, hogy az erőforrás először kell adni a hibát adja vissza.

**Több réteget egy bérlőben**

Ez lehet egy problémát, ha két vagy több alkalmazást az alkalmazásregisztrációk, például egy külön ügyfél- és erőforrás áll a logikai alkalmazás. Hogyan kapunk az erőforrás be az ügyfélbérlőn első? Az Azure AD ügyfél és a resource lehet egyetlen lépésben megadta a hozzájárulást engedélyezésével ebben az esetben ismerteti. A felhasználónál az ügyfél és a hozzájárulást kérő lap az erőforrás által kért engedélyeket összegét. Ahhoz, hogy ezt a viselkedést, az erőforrás-alkalmazás regisztrációja tartalmaznia kell az ügyfélalkalmazás-azonosító, egy `knownClientApplications` a hozzá tartozó [alkalmazásjegyzék][AAD-App-Manifest]. Példa:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Ezt mutatják be egy natív ügyfél többszintű, webes API-minta hívja meg a [kapcsolódó tartalom](#related-content) szakaszban Ez a cikk végén található. Az alábbi ábrán egy többrétegű alkalmazást, regisztrálni az egyetlen bérlő számára hozzájárulási áttekintést nyújt.

![Többrétegű ismert ügyfélalkalmazás hozzájárulás megadása][Consent-Multi-Tier-Known-Client] 

**A több bérlő több rétegre**

Hasonló eset akkor fordul elő, ha egy alkalmazás különböző rétegek regisztrálva lettek a különböző bérlők. Vegyük példaként a kis-és a egy natív ügyfélalkalmazás, amely meghívja ezt az Office 365 Exchange Online API létrehozásához. Fejlesztés a natív alkalmazás, és újabb verzióiban a natív alkalmazás ügyfél-bérlőben futtatásához, az Exchange online-hoz egyszerű szolgáltatás jelen kell lennie. Ebben az esetben a fejlesztői és az ügyfél kell vásárolnia az Exchange Online hozhatók létre a bérlőknek az egyszerű szolgáltatás. 

A fejlesztői API-Microsofton kívül más szervezetek által készített API esetén kell az ügyfeleknek, hogy engedélyt adjanak az alkalmazást az ügyfeleik bérlők lehetőséget biztosíthat. A javasolt tervezési van, a harmadik féltől származó fejlesztők az API-t hozhat létre, így is működhetnek a webes ügyfél-előfizetési megvalósításához. Ehhez tegye a következőket:

1. Kövesse a korábbi szakaszokban, annak érdekében, hogy az API-t valósít meg a több-bérlős alkalmazás regisztrációs/kód követelményeknek.
2. Mellett is közzéteheti az API-k hatóköröket vagy szerepköröket, ellenőrizze, hogy a regisztráció tartalmazza a "Bejelentkezés és felhasználói profil olvasása" (alapértelmezés szerint biztosított) az Azure AD engedélyt.
3. A webes ügyfél, a következő bejelentkezési-a vagy a regisztráláshoz oldal implementálása a [rendszergazdai jóváhagyás](#admin-consent) korábban ismertetett útmutatást.
4. Miután a felhasználó hozzájárul, hogy az alkalmazás, a szolgáltatás egyszerű és jóváhagyás delegálás hivatkozásokat hoz létre a bérlő, és a natív alkalmazás jogkivonatokat beszerezheti az API-hoz.

Az alábbi ábrán egy többrétegű alkalmazás regisztrálva a különböző bérlők hozzájárulási áttekintést nyújt.

![Többrétegű több résztvevős alkalmazásba hozzájárulás megadása][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Jóváhagyás visszavonása
A felhasználók és rendszergazdák hozzájárulás az alkalmazáshoz, bármikor visszavonhatja:

* Felhasználók egyedi alkalmazásokhoz való hozzáférés visszavonása eltávolításával, a saját [hozzáférési Panel alkalmazásai] [ AAD-Access-Panel] listája.
* A rendszergazdák alkalmazásokhoz való hozzáférés visszavonása eltávolítja azokat az Azure AD-t a [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) szakaszában a [az Azure portal][AZURE-portal].

Egy rendszergazda jóváhagy egy alkalmazás a bérlő összes felhasználója esetében, ha a felhasználók hozzáférési külön-külön nem tudja visszavonni. Csak a rendszergazda visszavonhatja a hozzáférést, és csak a teljes alkalmazáshoz.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Több-bérlős alkalmazások és a hozzáférési jogkivonatok gyorsítótárazása
Több-bérlős alkalmazásokat is tokenekhez is hozzáférhetnek az Azure AD által védett API-k meghívására. Gyakori hiba az Active Directory Authentication Library (ADAL) használatával több-bérlős alkalmazás esetén először a/Common, használó felhasználó számára egy jogkivonat kérelmezéséhez kap választ, akkor szükséges, hogy a felhasználók is használja a/Common későbbi jogkivonat kérése. Mivel az Azure AD-ből a válasz nem származik a bérlőt, és közös, ADAL gyorsítótárazza a jogkivonat, hogy a bérlő. A hozzáférési jogkivonatot kapjon a felhasználó/Common későbbi hívása a gyorsítótárbeli sikertelen keresések a gyorsítótár-bejegyzés, és kéri a felhasználót, hogy jelentkezzen be újra. Elkerülése érdekében a gyorsítótár hiányzik, ellenőrizze, egy már bejelentkezett felhasználó hívások a bérlő végpont menjenek végbe.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozhat létre olyan alkalmazás, amely bármely Azure AD-bérlőből a felhasználó bejelentkezhet. Egyszeri bejelentkezés (SSO) engedélyezése az alkalmazás és az Azure AD között, után is frissítheti az alkalmazás által az Office 365-höz hasonló Microsoft-erőforrások API-k elérésére. Ez lehetővé teszi a személyre szabott élményt az alkalmazásban, például a környezeti információk megjelenítése a felhasználóknak, mint a saját profilképét vagy a következő naptári időpontot. API végrehajtásával kapcsolatos további meghívja az Azure AD és az Office 365, Exchange, SharePoint, onedrive vállalati verzió, a OneNote, Planner, Excel és további információkért látogasson el a szolgáltatások [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Kapcsolódó tartalom
* [A minták több-bérlős alkalmazást][AAD-Samples-MT]
* [Alkalmazások arculati útmutatóját][AAD-App-Branding]
* [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok][AAD-App-SP-Objects]
* [Alkalmazások integrálása az Azure Active Directoryval][AAD-Integrating-Apps]
* [A hozzájárulási keretrendszer áttekintése][AAD-Consent-Overview]
* [A Microsoft Graph API-engedélyhatókörök][MSFT-Graph-permision-scopes]
* [Az Azure AD Graph API-engedélyhatókörök][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:v1-id-and-access-tokens.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














