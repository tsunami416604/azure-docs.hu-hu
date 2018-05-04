---
title: Hogyan hozhat létre egy alkalmazást, minden Azure AD-felhasználó bejelentkezés
description: Bemutatja, hogyan hozható létre olyan több-bérlős alkalmazás, amely bejelentkezhet a felhasználó bármely Azure Active Directory-bérlőhöz.
services: active-directory
documentationcenter: ''
author: celestedg
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 281f50a942a9396bf1163f5a20feb98bf450e6eb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Bármely a több-bérlős alkalmazásminta használó Azure Active Directory felhasználói bejelentkezés
Ha egy szoftver számos szervezet hasonló szolgáltatás alkalmazásként, konfigurálhatja az alkalmazás Azure Active Directory (AD) a panelhez bejelentkezések fogadására. Ez a konfiguráció elnevezése, így az alkalmazás több-bérlős. Bármely Azure AD-bérlő felhasználók fog tudni bejelentkezni az alkalmazás után hozzájárul ahhoz, hogy a fiókot használja az alkalmazással.  

Ha egy meglévő alkalmazást a saját fiók rendszer, vagy más egyéb szolgáltatók bejelentkezések használatát támogatja, felvétele az Azure AD-bejelentkezés a panelhez használata egyszerű. Csak az alkalmazás regisztrálásához, adja hozzá az OAuth2, az OpenID Connect vagy a SAML-bejelentkezés kódot, és hogy egy ["Sign In with Microsoft" gomb] [ AAD-App-Branding] az alkalmazásban.

> [!NOTE] 
> Ez a cikk feltételezi, hogy már ismeri az Azure AD egy egybérlős alkalmazás felépítése. Ha nem, akkor kell kezdődnie, a quickstarts egyikét a a [fejlesztői útmutató kezdőlap][AAD-Dev-Guide].

Az alkalmazás alakítani egy több-bérlős az Azure AD alkalmazás négy egyszerű lépésben történik:

1. [A több-bérlős kell regisztrációja frissítése](#update-registration-to-be-multi-tenant)
2. [Frissítse a kódot kérelmeket küldeni a/Common végpont](#update-your-code-to-send-requests-to-common)
3. [Frissítse a kódot több kibocsátó érték kezelése](#update-your-code-to-handle-multiple-issuer-values)
4. [Felhasználó és rendszergazda jóváhagyását megértéséhez, valamint a megfelelő kódot módosításokat](#understanding-user-and-admin-consent)

Nézzük részletes lépéseit. Akkor is is ugorhat rögtön [ebben a listában több-bérlős minták][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Több-bérlős kell regisztráció frissítése
Alapértelmezés szerint web app/API regisztráció az Azure ad-ben található egybérlős.  Akkor is használhatja a regisztrációs több-bérlős keresése a **több központjaként** váltani a **tulajdonságok** az alkalmazáshoz való regisztráció ablaktábláján a [Azure-portálon] [ AZURE-portal] értékre állítaná, és **Igen**.

Előtt egy alkalmazás több-bérlős, az Azure AD globális egyedinek kell lennie az alkalmazás az App ID URI van szükség. Az App ID URI az alkalmazás azonosítható protokoll üzeneteinek eljárások valamelyikével. Egyetlen bérlői alkalmazások a rendszer megfelelő a App ID URI bérlőre belül egyedinek kell lennie. Egy több-bérlős alkalmazáshoz kell legyen globálisan egyedi, az Azure AD megtalálja az alkalmazás összes bérlők között. Globális egyediségi azzal, hogy rendelkezik egy állomásnevet, amely megfelel az Azure AD-bérlő ellenőrzött tartományt App ID URI érvényesül. Alapértelmezés szerint az Azure-portálon létrehozott alkalmazások is rendelkeznek egy globálisan egyedi App ID URI be az alkalmazás létrehozása, de az érték módosítására képes.

Például, ha a bérlő neve contoso.onmicrosoft.com, majd egy érvényes App ID URI lenne `https://contoso.onmicrosoft.com/myapp`.  Ha a bérlő az ellenőrzött tartományt kellett `contoso.com`, majd egy érvényes App ID URI is használhatók lesznek `https://contoso.com/myapp`. Ha az App ID URI nem követi a ebben a mintában beállítását egy alkalmazás több-bérlős sikertelen lesz.

> [!NOTE] 
> Natív ügyfél-regisztrációk, valamint [v2 alkalmazások](./active-directory-appmodel-v2-overview.md) több-bérlős alapértelmezés szerint.  Nem kell több-bérlős a alkalmazás regisztrációk el semmilyen művelet végrehajtására.

## <a name="update-your-code-to-send-requests-to-common"></a>Frissítse a kódot kérelmeket küldeni/Common
Bejelentkezési kérelmek egy egybérlős alkalmazást, a bérlő bejelentkezési végpont kell küldeni. Például a contoso.onmicrosoft.com a végpont a következő lesz: `https://login.microsoftonline.com/contoso.onmicrosoft.com`

A tenant végpont küldött kérelmeket a bérlőre elérhető alkalmazásokra mutató bérlőre jelentkezhetnek be a felhasználók (vagy a vendégek). Egy több-bérlős alkalmazással az alkalmazás nem tud előre milyen bérlő, a felhasználó tartozik, így nem tudnak kérelmeket küldeni a tenant végpont.  Ehelyett kérelmeket küldött egy végpontot, amely minden Azure AD bérlők között multiplexes: `https://login.microsoftonline.com/common`

Ha az Azure AD meg az/Common kérelem érkezik a végponthoz, azt a felhasználó bejelentkezik, és, következésképpen felderíti a felhasználó mely bérlő. A/közös végpont működik együtt, minden Azure AD által támogatott hitelesítési protokoll: OpenID Connect, a OAuth 2.0, a SAML 2.0 és a WS-Federation.

Az alkalmazás ezután a bejelentkezési választ a felhasználó jelképező jogkivonatot tartalmazza. A jogkivonatot kibocsátó értéke be van állítva egy alkalmazás a felhasználó milyen bérlő. Ha választ ad vissza, a/Common végpont, a jogkivonat kiállítójának értéke megfelel a felhasználó-bérlő. 

> [! IMPORTANTNT] a/Common végpont nem a bérlőt, és nincs kibocsátó, hogy csak a multiplexer. / Common használata esetén az alkalmazás érvényesíthet jogkivonatokat logika figyelembe ennek frissíteni kell. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Frissítse a kódot több kibocsátó érték kezelése
Webalkalmazások és webes API-k kap, és érvényesítse az Azure AD.  

> [!NOTE]
> Natív ügyfélalkalmazások kérése és jogkivonatokat fogadni az Azure AD, ehhez a elküldhesse az összeset API-k, ahol érvényesítve.  Natív alkalmazások nem érvényesíthet jogkivonatokat és kell kezeli őket nem átlátszó.

Nézzük, hogyan ellenőrzi az alkalmazás a jogkivonatokat kap az Azure AD.  Egy egybérlős alkalmazás általában tart egy végpont értékét, például:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

és használ egy metaadatainak URL-CÍMÉT (ebben az esetben az OpenID Connect) összeállításához hasonlóan:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

két fontos adatot, amely segítségével érvényesítse letöltése: a bérlő által aláíró kulcsok és a kiállító érték. Minden Azure AD-bérlő egyedi kibocsátó értéke a következő formátumban:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

a GUID-érték esetén a bérlő bérlői azonosító átnevezése szálbiztos verzióját. Ha az előző metaadatok hivatkozásra `contoso.onmicrosoft.com`, láthatja, hogy ez a dokumentum az kibocsátó érték.

Amikor egy bérlő egyetlen alkalmazás érvényesíti a jogkivonatot, ellenőrzi az aláírás a jogkivonat az aláírási kulcsokat a metaadat-dokumentum ellen. Ez a vizsgálat lehetővé teszi, hogy ellenőrizze, hogy a jogkivonat kiállítójának értéke megegyezik, amely a metaadat-dokumentum található.

Mivel a/közös végpont a bérlő nem felel meg, és nem egy kibocsátó, vizsgálja meg a kibocsátó az metaadatok / közös rendelkezik sablonalapú URL-cím helyett egy tényleges érték:

    https://sts.windows.net/{tenantid}/

Ezért egy több-bérlős alkalmazás nem érvényesíthet jogkivonatokat csak a metaadatok a kibocsátó értékével egyező által a `issuer` a token értékét. Egy több-bérlős alkalmazást kell logika döntse el, hogy mely kibocsátó értékek érvényes, amely nem a bérlői azonosító részét a kibocsátó érték alapján.  

Például ha egy több-bérlős alkalmazás csak lehetővé teszi, hogy bejelentkezési adott bérlő, aki regisztrált-e a szolgáltatás, akkor azt kell ellenőrizze vagy a kibocsátó érték vagy a `tid` jogcím értéke a jogkivonatban, győződjön meg arról, hogy a bérlő az előfizetők a listában. Ha csak egy több-bérlős alkalmazás egyének foglalkozik, és nem minden hozzáférés alapján döntéseket bérlők, majd azt figyelmen kívül hagyhatja a kibocsátó érték regisztrálását.

Az a [több-bérlős minták][AAD-Samples-MT], kibocsátó ellenőrzése le van tiltva, bejelentkezhet bármely Azure AD-bérlő engedélyezéséhez.

## <a name="understanding-user-and-admin-consent"></a>Understanding felhasználói és rendszergazdai hozzájárulási
Egy felhasználó bejelentkezni az Azure AD-alkalmazás az alkalmazás meg kell jelennie a felhasználó bérlői. Ez lehetővé teszi, hogy a szervezet például egyedi házirendek alkalmazása, amikor a bérlő felhasználóit jelentkeznek be az alkalmazást. Egyetlen bérlői alkalmazások a regisztráció az egyszerű; , amely akkor fordul elő, amikor regisztrálja az alkalmazást a [Azure-portálon][AZURE-portal].

Egy több-bérlős alkalmazáshoz az alkalmazás a kezdeti regisztráció él, az Azure AD-bérlő a fejlesztők használják. Amikor egy felhasználó egy másik bérlőhöz az alkalmazás első alkalommal jelentkezik be, az Azure AD megkéri, hogy az engedélyeket, az alkalmazás által kért hozzájárulás. Ha azok hozzájárulás, akkor az alkalmazás egy ábrázolása nevű egy *egyszerű* jön létre a felhasználó bérlői, és a bejelentkezés. A delegálás rögzíti az alkalmazás a felhasználó hozzájárul a címtárban is létrejön. További részletek az alkalmazás alkalmazás- és szolgáltatásnév objektum, és hogyan kapcsolódnak egymáshoz: [alkalmazás és szolgáltatás egyszerű objektumok][AAD-App-SP-Objects].

![Egyszintű alkalmazásba hozzájárulás][Consent-Single-Tier] 

A hozzájárulási forgatókönyvben az engedélyeket, az alkalmazás által kért van hatással. Az Azure AD kétféle engedélyek, csak alkalmazást, és a delegált támogatja.

* Egy engedélyt biztosít az alkalmazás lehetővé teszi, hogy a művelet egy részét a felhasználói bejelentkezés, a felhasználó teheti meg. Például engedélyt adhat egy alkalmazást a delegált a bejelentkezett felhasználó nevében naptár olvasni.
* Az alkalmazás csak az engedélyt közvetlenül az alkalmazáshoz. Például engedélyt adhat egy alkalmazást a csak alkalmazás olvasni a bérlőt, függetlenül attól, akik bejelentkezett az alkalmazásba a felhasználók listáját.

Néhány engedély is lehet hozzájárulását normál felhasználói, míg más egy Bérlői rendszergazda jóváhagyását. 

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás
Csak alkalmazás engedélyek minden esetben szükséges egy Bérlői rendszergazda jóváhagyását. Ha az alkalmazás az alkalmazás csak engedélyt kér, és a felhasználó megpróbál bejelentkezni az alkalmazás, egy hibaüzenet jelenik meg arról, hogy a felhasználó nem tudja hozzájárulás.

Bizonyos delegált jogosultságokkal sikeresen telepítették is szükség lehet egy Bérlői rendszergazda jóváhagyását. Írjanak vissza az Azure AD a bejelentkezett felhasználó nevében, például egy Bérlői rendszergazda jóváhagyását igényli. Csak alkalmazáshoz engedéllyel, például ha egy szokásos felhasználó megpróbál bejelentkezni egy alkalmazás, amelyet a rendszergazda jóváhagyását igénylő engedélyt igényel az alkalmazás egy hibaüzenetet kap. Engedély szükséges-e rendszergazda jóváhagyását határozza meg a fejlesztői erőforrás közzétett, és képes az erőforrás dokumentációjában található. Az engedélyek dokumentációját a [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] és [Microsoft Graph API] [ MSFT-Graph-permision-scopes] jogosultságokat igényel rendszergazdai jelző hozzájárulás.

Ha az alkalmazás a rendszergazda jóváhagyását igénylő engedélyek használja, a hitelesítési mód, például egy gombra való kattintást vagy hivatkozás rendelkezik, ahol a rendszergazda a művelet is kezdeményezhető szeretné. Az alkalmazás számára ez a művelet nem a szokásos OAuth2/OpenID Connect hitelesítési kéréseket, amelyek is elküldi a kérelmet a `prompt=admin_consent` lekérdezési karakterlánc. Ha a rendszergazda hozzájárult, és a szolgáltatás egyszerű az ügyfél bérlő jön létre, új bejelentkezési kérelmek nem kell a `prompt=admin_consent` paraméter. A rendszergazda úgy dönt, a kért engedélyeket elfogadhatók, mivel a rendszer bekéri a bérlő nincs más felhasználói hozzájárulás ettől kezdve.

A bérlői rendszergazda letilthatja az alkalmazások beleegyezését rendszeres felhasználók. Ha ez a funkció le van tiltva, a rendszergazda hozzájárulás megadása mindig kötelező az alkalmazás a bérlő által használandó. Ha azt szeretné, az alkalmazás teszteléséhez a felhasználó beleegyezésével le van tiltva, a konfigurációs kapcsolót a található a [Azure-portálon] [ AZURE-portal] a a **[felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** szakaszában **vállalati alkalmazások**.

A `prompt=admin_consent` paraméter is használható az engedélyeket, nincs szükség a rendszergazda jóváhagyását igénylő alkalmazásokkal. Ha ez akkor használható, például, ha az alkalmazás a számára, ahol a bérlői rendszergazda "előfizet" egy időben, és egyéb rendszer kéri a felhasználóktól a hozzájárulásukat adják meg.

Ha egy alkalmazás megkövetel egy rendszergazda jóváhagyását, és hogy egy rendszergazda jelentkezik be nélkül a `prompt=admin_consent` küldi el, amikor a rendszergazda sikeresen járul hozzá az alkalmazáshoz fog alkalmazni paraméter **csak a hozzájuk tartozó felhasználói fiókok**. Rendszeres felhasználók továbbra is nem fognak tudni jelentkezni, vagy hogy az alkalmazás. Ez a szolgáltatás akkor hasznos, ha szeretne adni a bérlői rendszergazda Fedezze fel az alkalmazást, mielőtt más felhasználók hozzáférést lehessen.

> [!NOTE]
> Egyes alkalmazások szeretné, hogy hol rendszeres felhasználók kezdetben hozzájárulás, és később magába foglaló az alkalmazást a rendszergazda és a kérelem engedélyek rendszergazda jóváhagyását igénylő élményt. Nincs mód ehhez a V1-es alkalmazás regisztrálása az Azure AD ma; azonban a v2 végpont használatával lehetővé teszi, hogy engedélyeket alkalmazások ahelyett, hogy futásidőben regisztrációs időpontban, amely lehetővé teszi, hogy ebben a forgatókönyvben. További információkért lásd: a [v2 végpont][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Hozzájárulás és a többrétegű alkalmazások
Előfordulhat, hogy az alkalmazás több rétegek, a saját regisztrációs által képviselt minden Azure AD-ben. Például egy webes API-t egy natív alkalmazás, vagy egy webes alkalmazás, amely meghívja a webes API-k. Mindkét esetben a az ügyfél (a natív alkalmazással vagy a web app) kér engedélyeket az erőforrás (webes API-k) hívni. Az ügyfél számára kell sikeresen átadni kívánt hozzájárult e az ügyfél bérlői, amelyhez engedélyeket igényel minden erőforrás már léteznie kell az ügyfél-bérlőben. Ha ez a feltétel nem teljesül, az Azure AD, hogy az erőforrás először hozzá kell adni a hibát adja vissza.

**Egy bérlő több rétegből**

Ez problémát okozhat, ha a logikai alkalmazás két vagy több alkalmazás regisztrációk, például egy külön ügyfél- és erőforrás áll. Hogyan kapunk az erőforrás a felhasználói bérlő az első? Az Azure AD hozzá van rendelve ebben az esetben engedélyezése az ügyfél és a erőforrás átadni kívánt hozzájárult e egyetlen lépésben. A felhasználónál az engedélyeket, az ügyfél és a hozzájárulási lapon erőforrás által kért összessége. Ahhoz, hogy ez a viselkedés, az erőforrás-alkalmazás regisztrációja tartalmaznia kell az ügyfél alkalmazás azonosítója egy `knownClientApplications` a saját [alkalmazásjegyzék][AAD-App-Manifest]. Példa:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Ezt mutatják be egy natív ügyfél többrétegű, webes API-mintát hívása a [kapcsolódó tartalom](#related-content) szakasz Ez a cikk végén. A következő ábra áttekintést hozzájárulási egy többrétegű alkalmazást, regisztrálni az egyetlen bérlő számára.

![Többrétegű ismert ügyfélalkalmazás hozzájárulás][Consent-Multi-Tier-Known-Client] 

**A több bérlő több rétegből**

Hasonló esetek történik, ha az alkalmazások különböző rétegek a különböző bérlők van regisztrálva. Vegyük példaként a kis-és az Office 365 Exchange Online API-t egy natív ügyfélalkalmazás létrehozása. A natív, és újabb verzióiban a natív alkalmazás ügyfél-bérlőben futtatásához kialakításához, az Exchange Online szolgáltatás egyszerű jelen kell lennie. Ebben az esetben a fejlesztői és az ügyfél kell vásárolnia, Exchange online-hoz létre kell hozni a bérlők egyszerű szolgáltatás.  

Esetén az API-t egy szervezet, nem a Microsoft által az API-t a fejlesztői lehetővé teszik az ügyfelek számára, az alkalmazást az ügyfelek bérlők beleegyezését kell. A javasolt tervezési van, a harmadik féltől származó fejlesztő hozhat létre az API-t úgy, hogy működni a webes ügyfél regisztráció végrehajtásához. Ehhez tegye a következőket:

1. Kövesse a korábbi szakaszokban annak érdekében, az API-t a több-bérlős alkalmazáskövetelményeket regisztrációs/kód valósítja meg.
2. Mellett, hogy csökkenjen az API-t hatókörök/szerepkörök, ellenőrizze, hogy a regisztrációs tartalmaz a "Bejelentkezés és felhasználói profil olvasása" az Azure AD engedélyt (alapértelmezés szerint).
3. Valósít meg a webes ügyféllel, a következő bejelentkezési-a/regisztrációs oldalon a [admin hozzájárulási](#admin-consent) korábban tárgyalt útmutatást.
4. Miután a felhasználó hozzájárul, hogy az alkalmazás, a szolgáltatás egyszerű és beleegyezése delegálás hivatkozásokat hoz létre a bérlői, és a natív alkalmazás tud jogkivonatokhoz az API-hoz.

A következő ábra áttekintést hozzájárulási a különböző bérlők regisztrált többrétegű alkalmazást.

![Többrétegű több résztvevős alkalmazásba hozzájárulás][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Hozzájárulás visszavonása
Felhasználók és rendszergazdák vissza tudja vonni a bármikor az alkalmazásba hozzájárulás:

* Felhasználók visszavonni a hozzáférést az egyes alkalmazások eltávolítja azokat a [Panel alkalmazásokat] [ AAD-Access-Panel] listája.
* A rendszergazdák alkalmazások hozzáférés megvonása ehhez távolítsa el az Azure AD használatával a [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) szakasza a [Azure-portálon][AZURE-portal].

Járul hozzá a rendszergazda egy alkalmazás a bérlő a összes felhasználója számára, ha a felhasználók hozzáférést külön-külön nem visszavonása. Csak a rendszergazda visszavonhatja a hozzáférést, és csak a teljes alkalmazáshoz.

### <a name="consent-and-protocol-support"></a>Hozzájárulás és protokollok támogatása
Hozzájárulás támogatott keresztül az OAuth, OpenID Connect, az Azure AD-ben és a WS-Federation, SAML protokoll. A WS-Federation és az SAML protokoll nem támogatják a `prompt=admin_consent` paraméter, így a rendszergazda jóváhagyását csak az OAuth és az OpenID Connect keresztül lehetséges.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Több-bérlős alkalmazásokhoz és a hozzáférési jogkivonatok gyorsítótár
Több-bérlős alkalmazásokhoz is kérheti le a hozzáférési jogkivonatok az Azure AD által védett API-k meghívásához. Gyakori hiba az Active Directory Authentication Library (ADAL) használata egy több-bérlős alkalmazás esetén először kérjen egy token/Common, használó felhasználó kapott választ, akkor kérjen az, hogy a felhasználók is használja a/Common későbbi tokent. Mivel az Azure ad-válasz nem származik a bérlő vagy közös, ADAL gyorsítótárazza a jogkivonatot, hogy a bérlőtől. Olyan hozzáférési jogkivonatot beolvasni a felhasználói/Common későbbi hívása sikertelen a gyorsítótári bejegyzést, és kéri a felhasználót, hogy jelentkezzen be újra. Hiányzik a gyorsítótár elkerülése érdekében győződjön meg arról, hogy a tenant végpont már bejelentkezett felhasználók további hívások történik.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozhat létre olyan alkalmazás, amely az Azure AD-bérlő a felhasználói bejelentkezés. Miután engedélyezte az egyszeri bejelentkezés (SSO) az alkalmazás és az Azure AD között, az alkalmazás által a Microsoft erőforrásokat, például az Office 365 API-k elérésére is frissítheti. Ez lehetővé teszi az alkalmazás, például a környezetfüggő adatainak megjelenítése a felhasználóknak, a profilkép vagy a következő naptári időpontot egyeztessen a személyre szabott élményt. További API készítése meghívja az Azure AD és az Office 365 szolgáltatások, például az Exchange, SharePoint, a OneDrive, a OneNote, Planner, Excel és több, látogasson el a [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Kapcsolódó tartalom
* [Több-bérlős alkalmazás minták][AAD-Samples-MT]
* [Az alkalmazások arculati útmutatóját][AAD-App-Branding]
* [Alkalmazás és szolgáltatás egyszerű objektumok][AAD-App-SP-Objects]
* [Alkalmazások integrálása az Azure Active Directoryban][AAD-Integrating-Apps]
* [A hozzájárulási keretrendszer áttekintése][AAD-Consent-Overview]
* [Microsoft Graph API-engedélyhatókörök][MSFT-Graph-permision-scopes]
* [Az Azure AD Graph API-engedélyhatókörök][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
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
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
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














