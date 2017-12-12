---
title: "A hozzáférés-vezérlés Azure-szolgáltatás áttelepítésére |} Microsoft Docs"
description: "Az alkalmazások és szolgáltatások áthelyezésére a hozzáférés-vezérlés Azure szolgáltatás beállításai"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: 5ce6b19ebe0b7159b6c68fc50d7d47f0479e0c27
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-from-the-azure-access-control-service"></a>A hozzáférés-vezérlés Azure-szolgáltatás áttelepítése

Az Azure hozzáférés-vezérlés, a szolgáltatás az Azure Active Directory (Azure AD), a November 2018 rendszerből. Alkalmazások és szolgáltatások, amelyek jelenleg használják a hozzáférés-vezérlés kell teljesen áttelepíteni egy másik hitelesítési módszert, majd által. Ez a cikk ismerteti a javaslatok a jelenlegi felhasználó esetén, mivel azt tervezi, hogy Ön miként használja a hozzáférés-vezérlés érvényteleníthető. Ha hozzáférés-vezérlés jelenleg nem használ, nem kell tennie semmit.


## <a name="overview"></a>Áttekintés

Hozzáférés-vezérlés egy hitelesítési felhőszolgáltatás, amely a webes alkalmazásokhoz és szolgáltatásokhoz való hozzáférés a felhasználók hitelesítéséhez és engedélyezéséhez egyszerű módszert kínál. Lehetővé teszi a hitelesítési és engedélyezési kívül a kódot kell figyelembe venni az egyes szolgáltatásai. Hozzáférés-vezérlés elsősorban a fejlesztők és a fejlesztők a Microsoft .NET-ügyfelek, az ASP.NET-webalkalmazások és a Windows Communication Foundation (WCF) webszolgáltatások.

Hozzáférés-vezérlés alkalmazási helyzetei három fő kategóriába sorolhatók:

- Bizonyos Microsoft felhőszolgáltatásai, beleértve az Azure Service Bus és a Dynamics CRM-hez való hitelesítése. Ügyfélalkalmazások jogkivonatok szerzi be hozzáférés-vezérlést, hogy ezek a szolgáltatások különböző műveleteket végezhetnek hitelesítést.
- Hozzáadását hitelesítés, egyéni mind előre csomagolt (például SharePoint), a webes alkalmazásokhoz. Hozzáférés-vezérlés "passzív" hitelesítést használ, webes alkalmazások támogathatják Bejelentkezés Microsoft-fiókjával (korábban Live ID), és a Google, a Facebook, a Yahoo, az Azure AD-fiókokkal és az Active Directory összevonási szolgáltatások (AD FS).
- Egyéni webes szolgáltatások biztonságossá tétele a hozzáférés-vezérlés által kiállított jogkivonatokat. "Aktív" hitelesítéssel, a webszolgáltatások biztosítható, hogy azok engedélyezik a hozzáférést csak ismert ügyfeleik hitelesített hozzáférés-vezérléssel.

Az alábbi szakaszok ezen használja esetek és az ajánlott áttelepítési stratégiák ismertetése. 

> [!WARNING]
> A legtöbb esetben a jelentős kódmódosításokat szükségesek a meglévő alkalmazások és szolgáltatások áttelepítése újabb technológiák. Azt javasoljuk, hogy azonnal elkezdheti tervezni az és elkerülése érdekében minden lehetséges valamilyen okból kimaradás vagy leállás az áttelepítés végrehajtása.

Hozzáférés-vezérlés a következő részből áll:

- A biztonságos biztonságijogkivonat-szolgáltatás (STS), amely fogadja a hitelesítési kérelmeket, és biztonsági jogkivonatokat bocsát ki ismét.
- A klasszikus Azure portálra, ahol hoz létre, törlése, és engedélyezi, és tiltsa le a hozzáférés-vezérlés névterek.
- Külön hozzáférés-vezérlés felügyeleti portált, amelyen testreszabása és hozzáférés-vezérlés névterek konfigurálása.
- Felügyeleti szolgáltatás segítségével automatizálhatja a portálok funkcióit.
- A token átalakítási szabály motort, amely segítségével kezelheti a hozzáférés-vezérlés kiállított jogkivonatokat a kimeneti formátum komplex logikai megadása.

Ezeket az összetevőket használatához létre kell hoznia egy vagy több hozzáférés-vezérlés névtérbe. A *névtér* hozzáférés-vezérlés kommunikációt az alkalmazások és szolgáltatások egy dedikált példánya. Egy névtér el különítve a többi hozzáférés-vezérlés ügyfél. Hozzáférés-vezérlés letölthetővé saját névterek használja. A hozzáférés-vezérlés névtér rendelkezik egy dedikált URL-címet, amely a következőképpen néz ki:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Az összes kommunikáció a STS és felügyeleti műveletek végzett az URL-címen. Elérési útja eltér más célra használhatja. Határozza meg, hogy az alkalmazások vagy szolgáltatások hozzáférés-vezérlés, megfigyeli https:// forgalom számára\<névtér\>. accesscontrol.windows.net. Ezen URL-adatforgalmat hozzáférés-vezérlés kezeli, és megszűnik a kell. 

Ez a kivétel, https://accounts.accesscontrol.windows.net az összes bejövő forgalom. Az URL-cím felé irányuló forgalom már kezeli egy másik szolgáltatást, és nem érinti a hozzáférés-vezérlés érvénytelenítése. 

Meg kell is jelentkezzen be a klasszikus Azure portálon és a hozzáférés-vezérlés névterek a saját előfizetések. Hozzáférés-vezérlési névterek szerepel a **hozzáférés-vezérlési névterek** lap a **Active Directory** szolgáltatás.

Hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Access Control Service 2.0 (archivált)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Használatból való kivonást ütemezése

Től November 2017 minden hozzáférés-vezérlés összetevői teljes mértékben támogatott és működik. Az egyetlen korlátozás, amely akkor [nem hozható létre új hozzáférés-vezérlés névterek, a klasszikus Azure portálon keresztül](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Ez a hozzáférés-vezérlés összetevői elavulttá ütemezése:

- **2017. november**: az Azure AD rendszergazdai élmény a klasszikus Azure portálon [kivonják](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Ezen a ponton névtér felügyeleti hozzáférés-vezérlési érhető el egy új, dedikált URL-címen: http://manage.windowsazure.com?restoreClassic=true. Használja a meglévő névterek megtekintéséhez, engedélyezése, és tiltsa le a névterek, és törli a névterek, ha úgy dönt, hogy.
- **Április 2018**: hozzáférés-vezérlés névtér felügyeleti már nem érhető el a dedikált http://manage.windowsazure.com?restoreClassic=true URL-címen. Ezen a ponton nem letiltja vagy engedélyezi, törlése vagy a hozzáférés-vezérlés névterek számbavétele. Azonban a hozzáférés-vezérlés kezelési portál lesz teljesen működőképes és https:// helye\<névtér\>. accesscontrol.windows.net. A hozzáférés-vezérlés összes többi összetevő továbbra is megfelelően működik.
- **November 2018**: hozzáférés-vezérlés összetevői véglegesen állnak le. Ez magában foglalja a hozzáférés-vezérlés kezelési portálon, a felügyeleti szolgáltatás, STS és a token átalakítási jogcímszabály-motor. Ezen a ponton minden küldött kérelmeket a hozzáférés-vezérlés (helye: \<névtér\>. accesscontrol.windows.net) sikertelen. Kell áttelepített meglévő alkalmazások és szolgáltatások más technológiákat is a megadott idő előtti.


## <a name="migration-strategies"></a>Áttelepítési stratégiák

A következő szakaszok ismertetik a hozzáférés-vezérlés telepít át más Microsoft-technológiák magas szintű javaslatok.

### <a name="clients-of-microsoft-cloud-services"></a>Az ügyfelek a Microsoft cloud services csomag

Minden Microsoft felhőszolgáltatás, amely fogadja a hozzáférés-vezérlés által most kiadott jogkivonatokat, támogatja a hitelesítési legalább egy másik formája. A megfelelő hitelesítési módszert az egyes szolgáltatásokhoz függően változik. Azt javasoljuk, tekintse meg az egyes szolgáltatásokhoz hivatalos útmutatót adott dokumentációját. Kényelmi célokat szolgál minden készlete dokumentációját itt biztosítja:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Azure Service Bus | [Közös hozzáférésű jogosultságkód áttelepítése](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Az Azure Service Bus-továbbító | [Közös hozzáférésű jogosultságkód áttelepítése](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Redis Cache | [Migrate to Azure Redis Cache (Áttelepítés Azure Redis Cache-re)](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure datamarket szolgáltatásból | [A kognitív szolgáltatások API-k áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Az Azure App Service Logic Apps szolgáltatás áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Az Azure Media Services | [Telepítse át az Azure AD-alapú hitelesítés](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Az Azure Backup szolgáltatás ügynökének frissítése](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-that-use-passive-authentication"></a>Passzív hitelesítést használó webalkalmazások

A felhasználói hitelesítés hozzáférés-vezérlést használó webalkalmazásokat, hozzáférés-vezérlés a következő szolgáltatásokat és képességeket nyújt a webes alkalmazás fejlesztők és a fejlesztők:

- Szoros integrációja Windows Identity Foundation (WIF).
- A Google, a Facebook, a Yahoo, a Azure Active Directory és az AD FS-fiókok és a Microsoft-fiókok összevonási.
- A következő hitelesítési protokollok támogatása: OAuth 2.0 Vázlat 13, a WS-Trust és a Web Services összevonási (WS-Federation).
- A következő token formátumok támogatása: JSON webes jogkivonat (JWT), a SAML 1.1, a SAML 2.0 és az egyszerű webes jogkivonat (SWT).
- A hitelesítőtartomány feltárási folyamata, WIF, integrálva, amely lehetővé teszi a felhasználók számára azok való bejelentkezéshez használt fiók. Ez a felület üzemelteti a webes alkalmazás, és teljes mértékben testreszabható.
- A token, amely lehetővé teszi a gazdag testreszabási a hozzáférés-vezérlés, a webes alkalmazás által fogadott jogcímek átalakítása többek között:
    - Az identitás-szolgáltatóktól származó jogcímet továbbítani.
    - További egyéni jogcímekként hozzáadása.
    - Egyszerű a Ha-akkor logikai jogcímek bizonyos körülmények között kiadására.

Sajnos nem áll rendelkezésre egy szolgáltatás, amely minden a megfelelő képességet nyújt. Hozzáférés-vezérlés mely szolgáltatásait meg kell, és válassza a között kell kiértékelni [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (az Azure AD B2C), vagy egy másik felhőalapú hitelesítés a szolgáltatás.

#### <a name="migrate-to-azure-active-directory"></a>Az Azure Active Directory áttelepítése

Egy elérési utat kell figyelembe venni az alkalmazások és szolgáltatások van integrálható közvetlenül az Azure ad-val. Az Azure AD felhőalapú identitásszolgáltató a Microsoft munkahelyi vagy iskolai fiókok. Az Azure AD az identitásszolgáltató az Office 365, Azure és még sok más. Hasonló biztosít összevont hitelesítési képességeket a hozzáférés-vezérlést, de minden hozzáférés-vezérlés funkció nem támogatja. 

Az elsődleges példája közösségi Identitásszolgáltatók, például a Facebook, Google és Yahoo összevonásában. Ha a felhasználók jelentkezzen be az ilyen típusú hitelesítő adatokat, az Azure AD nincs a megoldás az Ön. 

Az Azure AD is feltétlenül nem támogatja a pontos azonos hitelesítési protokollok, hozzáférés-vezérlést. Például bár a hozzáférés-vezérlés és az Azure AD támogatja az OAuth, nincsenek finom eltérések vannak minden megvalósítása között. Különböző megvalósítások használatba áttelepítés részeként kód módosítása.

Azonban az Azure AD lehetséges az alábbi előnyöket nyújtja hozzáférés-vezérlés ügyfelek. Az natív módon támogatja a Microsoft munkahelyi vagy iskolai fiókok felhőalapú, hozzáférés-vezérlés az ügyfelek által leggyakrabban használt. 

Az Azure AD-bérlő is is összevonva a helyszíni Active Directory, AD FS szolgáltatás használatával egy vagy több példányára. Ezzel a módszerrel az alkalmazást felhőalapú felhasználókat hitelesítheti, és a felhasználók, akik a helyben tárolt. A WS-Federation protokoll, amely lehetővé teszi a viszonylag egyszerű webalkalmazás integrálása WIF használatával is támogatja.

Az alábbi táblázat összehasonlítja a szolgáltatások hozzáférés-vezérlés, amely kapcsolódik a webes alkalmazásokat az Azure AD-ben elérhető szolgáltatások. 

Magas szinten *Azure Active Directory oka valószínűleg az áttelepítéshez a legjobb választás, ha engedélyezi a felhasználóknak jelentkezzen az csak a Microsoft munkahelyi vagy iskolai fiókok*.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Támogatott |
| Windows Server Active Directory és az Active Directory összevonási Szolgáltatásokkal fiókjai |-Támogatott összevonás az Azure AD-bérlő keresztül <br />-Támogatott közvetlen összevonás az AD FS segítségével | Összevonás az Azure AD-bérlő használatával csak támogatott | 
| Egyéb vállalati identitás felügyeleti rendszerek fiókjai |– Összevonás az Azure AD-bérlő keresztül lehetséges <br />-Támogatott közvetlen összevonási keresztül | Összevonás az Azure AD-bérlő keresztül lehetséges |
| Microsoft-fiókok személyes használatra | Támogatott | Az Azure AD v2.0 OAuth protokollt keresztül, de bármilyen más protokollokon keresztül nem támogatott | 
| Facebook, Google, Yahoo-fiókok | Támogatott | Nem támogatott vállal |
| **Protokollok és SDK-kompatibilitási** | | |
| WIF | Támogatott | Támogatott, de korlátozott útmutatás érhető el |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | Vázlat 13 támogatása | RFC 6749, a legtöbb modern megadását támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| JWT-T | A béta támogatott | Támogatott |
| SAML 1.1 | Támogatott | Támogatott |
| SAML 2.0 | Támogatott | Támogatott |
| SWT | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható hitelesítőtartomány felderítési/fiók-kiadási felhasználói felület | Alkalmazások integrálhatók letölthető kódot | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| Testre szabhatja a jogcímek jogkivonatokba |-Az identitás-szolgáltatóktól származó bemeneti jogcím továbbítása<br />-Juthatnak jogkivonat az identitásszolgáltatótól jogcímként<br />-Adja ki a kimenő jogcímeket a bemeneti jogcímek értékek alapján<br />-Kimenő jogcímeket kiadni az állandó értékek |-Az összevont identitás-szolgáltatóktól származó jogcím nem továbbítása<br />– Nem olvasható be hozzáférési jogkivonat az identitásszolgáltatótól jogcímként<br />-Nem ad a kimeneti jogcímeket a bemeneti jogcímek értékek alapján<br />-Adhatnak ki a kimeneti jogcímek állandó értékekkel<br />-Adhatnak ki a kimeneti jogcímek szinkronizálva az Azure AD-felhasználók tulajdonságai alapján |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálásához | Hozzáférés-vezérlési Management szolgáltatásban keresztül | A Microsoft Graph és az Azure AD Graph API használatával támogatott |

Ha úgy dönt, hogy az Azure AD-e a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások, kell ügyelnie két módon integrálható az alkalmazás az Azure ad-val.

A WS-Federation vagy a WIF az Azure AD integrálása, vegye figyelembe az alábbi megközelítés ismertetett [összevont egyszeri bejelentkezés nem galéria alkalmazások konfigurálása](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). A cikk az SAML-alapú egyszeri bejelentkezés az Azure AD konfigurálása hivatkozik, de is működik, WS-összevonás konfigurálása. Ezt a módszert használja a következő Azure AD Premium-licencre van szükség. Ez a módszer két előnyökkel jár:

- Az Azure AD-token testreszabás teljesen rugalmasan kap. A megfelelő hozzáférés-vezérlés által kiállított jogcímek az Azure AD által kiállított jogcímek testreszabása Ez különösen magában foglalja a felhasználói azonosító vagy név azonosító jogcímet. Továbbra is megjelenik a felhasználók számára egységes felhasználói azonosítók technológiák módosítása után, győződjön meg arról, hogy a felhasználói azonosítók ki az Azure AD-egyeztetés hozzáférés-vezérlés azokat.
- A jogkivonat-aláíró tanúsítványt adott, az alkalmazáshoz, és vezérelt élettartamán konfigurálhatja.

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Ezt a módszert használja az Azure AD Premium-licencre van szükség. Ha az ügyfél egy hozzáférés-vezérlés és a premium licenc szükséges egyszeri bejelentkezést az alkalmazás beállítása, lépjen kapcsolatba velünk. A Microsoft örömmel adja meg a fejlesztői licenc használatára lesz.

Egy másik módszert is, hogy kövesse [kódmintában](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), ami a WS-Federation beállításának némileg eltérő utasításait. A fenti nem használja a WIF, de ehelyett az ASP.NET 4.5 OWIN köztes. Azonban az utasításokat az alkalmazás regisztrálása érvényes WIF használó alkalmazásokhoz, és nem igényel az Azure AD Premium licenchez. 

Ha úgy dönt, ezt a módszert használja, meg kell ismernie [kulcsváltás bejelentkezés az Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Ezt a módszert használja az Azure AD globális probléma jogkivonatok aláírókulcsot használja. Alapértelmezés szerint WIF nem frissíti automatikusan aláírási kulcsokat. Az Azure AD a globális aláírási kulcsokat forog, amikor a WIF-implementációjának szüksége van, a módosítások elfogadására irányuló elő kell készíteni.

Ha integrálható az Azure AD keresztül az OpenID Connect vagy OAuth protokollt, javasoljuk, így. Tudunk kiterjedt dokumentáció és útmutatás az Azure AD integrálása a webes alkalmazás érhető el a [az Azure Active Directory fejlesztői útmutatója](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>Az Azure Active Directory B2C áttelepítése

A más áttelepítési fontolja meg az elérési út az Azure AD B2C. Az Azure AD B2C egy felhőalapú hitelesítési szolgáltatást, például a hozzáférés-vezérlés lehetővé teszi a fejlesztők a hitelesítés és identitás felügyeleti logika egy felhőalapú szolgáltatás kihelyező. Egy fizetős szolgáltatás (rétegekkel szabad és a prémium szintű), amely a felhasználók felé néző alkalmazások, amelyen esetleg aktív felhasználók akár több millió szolgál.

Hozzáférés-vezérlés, például az Azure AD B2C legtöbb vonzó szolgáltatások egyike, hogy az támogatja-e fiókok számos különböző típusú. Az Azure AD B2C-ben is használatával írja alá a felhasználók saját Microsoft-fiókjával, vagy a Facebook, Google, LinkedIn, GitHub vagy Yahoo fiókokat, és több. Az Azure AD B2C is támogatja a "helyi fiókoknak", vagy a felhasználónév és a felhasználói számára jelszavak kifejezetten az alkalmazáshoz. Az Azure AD B2C is, amelyek segítségével testre szabhatja a bejelentkezési adatfolyamok gazdag bővítési lehetőséget biztosít. 

Azonban az Azure AD B2C nem támogatja a hitelesítési protokollok megvalósítását végzi a hardverekről és az token formátumú lehet szükség a hozzáférés-vezérlést. Is használhatja az Azure AD B2C lekérése jogkivonatokat és a lekérdezés a felhasználó további információt az identitásszolgáltató, a Microsoft vagy más módon.

Az alábbi táblázat összehasonlítja a szolgáltatások hozzáférés-vezérlés, amely kapcsolódik a webes alkalmazások számára elérhető az Azure AD B2C. Magas szinten *az Azure AD B2C oka valószínűleg az áttelepítéshez a megfelelő választás irányuló fogyasztó alkalmazás esetén, vagy ha az támogatja-e fiókok számos különböző típusú.*

| Képesség | Hozzáférés-vezérlés támogatása | Azure AD B2C-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Egyéni házirendek keresztül  |
| Windows Server Active Directory és az Active Directory összevonási Szolgáltatásokkal fiókjai | Az AD FS közvetlen összevonási keresztül | SAML összevonási keresztül támogatja egyéni házirendekkel |
| Egyéb vállalati identitás felügyeleti rendszerek fiókjai | A WS-Federation közvetlen összevonási keresztül | SAML összevonási keresztül támogatja egyéni házirendekkel |
| Microsoft-fiókok személyes használatra | Támogatott | Támogatott | 
| Facebook, Google, Yahoo-fiókok | Támogatott | Facebook-on és a Google támogatott natív módon, Yahoo OpenID Connect összevonási keresztül támogatja egyéni házirendekkel |
| **Protokollok és SDK-kompatibilitási** | | |
| A Windows Identity Foundation (WIF) | Támogatott | Nem támogatott |
| WS-Federation | Támogatott | Nem támogatott |
| OAuth 2.0 | Vázlat 13 támogatása | RFC 6749, a legtöbb modern megadását támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| JWT-T | A béta támogatott | Támogatott |
| SAML 1.1 | Támogatott | Nem támogatott |
| SAML 2.0 | Támogatott | Nem támogatott |
| SWT | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható hitelesítőtartomány felderítési/fiók-kiadási felhasználói felület | Alkalmazások integrálhatók letölthető kódot | Teljes mértékben testreszabható felhasználói felületén keresztül egyéni CSS |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláírási kulcsokat, nem a tanúsítványok keresztül egyéni házirendek |
| Testre szabhatja a jogcímek jogkivonatokba |-Az identitás-szolgáltatóktól származó bemeneti jogcím továbbítása<br />-Juthatnak jogkivonat az identitásszolgáltatótól jogcímként<br />-Adja ki a kimenő jogcímeket a bemeneti jogcímek értékek alapján<br />-Kimenő jogcímeket kiadni az állandó értékek |-Az identitás-szolgáltatóktól; jogcím is továbbítása az egyes jogcímek szükséges egyéni házirendek<br />– Nem olvasható be hozzáférési jogkivonat az identitásszolgáltatótól jogcímként<br />-Állíthatnak ki kimeneti jogcímeket a bemeneti jogcímek keresztül egyéni házirendek értékek alapján<br />-Kimeneti jogcímek adhat ki az egyéni házirendek keresztül állandó értékek |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálásához | Hozzáférés-vezérlési Management szolgáltatásban keresztül |-Létrehozó felhasználó Azure AD Graph API-n keresztül<br />– Nem hozható létre B2C bérlő, alkalmazások és házirendek programozott módon |

Ha úgy dönt, hogy az Azure AD B2C-e a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások, a következő erőforrások kezdődik:

- [Az Azure AD B2C dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Az Azure AD B2C egyéni házirendek](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Az Azure AD B2C díjszabási](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Egyéb áttelepítési lehetőségek

Ha az Azure AD és az Azure AD B2C nem felelnek meg a webes alkalmazás igényeinek, lépjen velünk kapcsolatba. Tudunk segíteni, hogy azonosítsa a legjobb áttelepítési út.

<!--

#### Migrate to Ping Identity or Auth0

In some cases, you might find that Azure AD and Azure AD B2C aren't sufficient to replace Access Control in your web applications without making major code changes. Some common examples might include:

- Web applications that use WIF or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- Web applications that perform direct federation to an enterprise identify provider over the WS-Federation protocol.
- Web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by Access Control.
- Web applications with complex token transformation rules that Azure AD or Azure AD B2C can't reproduce.

In these cases, you might want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options. Each of the following options offer capabilities similar to Access Control:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of Access Control](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of Access Control and Auth0.
- Enterprise customers also should consider [Ping Identity](https://www.pingidentity.com). If you contact us, we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity and Auth0 is to ensure that all Access Control customers have a migration path for their apps and services that minimizes the amount of work required to move from Access Control.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webszolgáltatások aktív hitelesítést használó

A hozzáférés-vezérlés által kiállított jogkivonatokat védett web Services a hozzáférés-vezérlés a következő szolgáltatásokat és képességeket nyújt:

- Egy vagy több regisztrálása *identitások szolgáltatás* a hozzáférés-vezérlés névtérben. Szolgáltatás-identitások használható lekérni a jogkivonatokat.
- Az OAuth BURKOLÁSA és az OAuth 2.0 Vázlat 13 jogkivonatokat, a következő típusú hitelesítő adatok használatával a kért protokollok támogatása:
    - Egy egyszerű jelszót, amely a szolgáltatás identitásának jön létre
    - A a szimmetrikus kulcs vagy X509 SWT aláírt tanúsítvány
    - Egy megbízható identitásszolgáltató (jellemzően AD FS-példányt) által kiadott SAML-jogkivonatból
- A következő token formátumok támogatása: jwt-t, a SAML 1.1, a SAML 2.0 és a SWT.
- Egyszerű token átalakítási szabályok.

A hozzáférés-vezérlés szolgáltatás-identitások általában használt kiszolgáló kiszolgáló hitelesítés megvalósításához.  

#### <a name="migrate-to-azure-active-directory"></a>Az Azure Active Directory áttelepítése

Azt javasoljuk, az ilyen típusú hitelesítési folyamat, hogy át [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Az Azure AD felhőalapú identitásszolgáltató a Microsoft munkahelyi vagy iskolai fiókok. Az Azure AD az identitásszolgáltató az Office 365, Azure és még sok más. 

Is használhatja az Azure AD kiszolgálók hitelesítéshez az OAuth ügyfél hitelesítő adatok megadása az Azure AD végrehajtásának használatával. Az alábbi táblázat összehasonlítja a kiszolgálók hitelesítés számára elérhető Azure AD-ben a hozzáférés-vezérlés képességei.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| Hogyan kell regisztrálni egy webszolgáltatás-bővítmény | A hozzáférés-vezérlés kezelési portálon hozzon létre egy függő entitás | Az Azure AD-webalkalmazás létrehozása az Azure-portálon |
| Hogyan kell regisztrálni az ügyfél | Szolgáltatásidentitás létrehozása kezelési portál hozzáférés-vezérlés | Egy másik Azure AD-webalkalmazás létrehozása az Azure portálon |
| Használt protokoll |-OAuth ÚJRAINDULÁS protokoll<br />-OAuth 2.0 Vázlat 13 ügyfél hitelesítő adatai megadják | OAuth 2.0 ügyfél hitelesítő adatai megadják |
| Ügyfél-hitelesítési módszer |-Egyszerű jelszó<br />-Aláírt SWT<br />-Egy összevont identitáskezelési szolgáltató SAML jogkivonat |-Egyszerű jelszó<br />-A JWT aláírt |
| Token formátumok |-JWT<br />-SAML 1.1<br />-SAML 2.0<br />-SWT<br /> | Csak a JWT |
| Token átalakítása |-Jogcímeket adhatnak hozzá egyéni<br />-Ha-akkor egyszerű jogcímek kiállítási logika | Egyéni jogcímeket adhatnak hozzá | 
| Konfigurációs és felügyeleti feladatok automatizálásához | Hozzáférés-vezérlési Management szolgáltatásban keresztül | A Microsoft Graph és az Azure AD Graph API használatával támogatott |

Végrehajtási kiszolgálók forgatókönyvekkel kapcsolatos útmutatásért lásd a következőket:

- Szolgáltatások közötti szakasza a [az Azure Active Directory fejlesztői útmutatója](https://aka.ms/aaddev)
- [Egyszerű jelszó ügyfél hitelesítő adatok használatával démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Kódminta démon tanúsítvány ügyfél hitelesítő adatok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Egyéb áttelepítési lehetőségek

Ha az Azure AD nem felel meg a webszolgáltatás igényeinek, hagyja üresen a megjegyzés. Tudunk segíteni, hogy az adott esethez tartozó legjobb terv azonosítása.

<!--

#### Migrate to Ping Identity or Auth0

In some cases, you might find that the Azure AD client credentials and the OAuth grant implementation aren't sufficient to replace Access Control in your architecture without major code changes. Some common examples might include:

- Server-to-server authentication using token formats other than JWTs.
- Server-to-server authentication using an input token provided by an external identity provider.
- Server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you might consider migrating your web application to another cloud authentication service. We recommend exploring the following options. Each of the following options offer capabilities similar to Access Control:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of Access Control](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of Access Control and Auth0.
- Enterprise customers should also consider [Ping Identity](https://www.pingidentity.com). If you contact us, we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity and Auth0 is to ensure that all Access Control customers have a migration path for their apps and services that minimizes the amount of work required to move from Access Control.

-->

## <a name="questions-concerns-and-feedback"></a>Kérdéseit, problémákat és visszajelzés

Tudjuk, hogy számos hozzáférés-vezérlés ügyfél a cikk elolvasása után nem található egy tiszta áttelepítési elérési út. Néhány segítséget és útmutatást meghatározni, hogy a megfelelő csomag szükséges. Ha azt szeretné, hogy milyen a áttelepítési forgatókönyvek és kérdések, hozzászólásban ezen a lapon.
