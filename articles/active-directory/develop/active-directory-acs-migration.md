---
title: "Azure Access Control Service (ACS) áttelepítése"
description: "Alkalmazások és szolgáltatások Azure Access Control Service ki áthelyezésére szolgáló beállítások |} A Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Azure Access Control Service (ACS) áttelepítése

A Microsoft Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatásban vagy az ACS) November 2018 a használatból van.  Alkalmazások és szolgáltatások jelenleg az ACS használatával kell teljesen át egy másik hitelesítési módszert, ez a dátum előtt. Ez a dokumentum ismerteti a jelenlegi ügyfelekkel kapcsolatos ajánlások azok tervezése érvényteleníthető ACS használatát. Ha ACS jelenleg nem használ, nem kell tennie semmit.


## <a name="brief-acs-overview"></a>ACS rövid áttekintése

ACS egy hitelesítési felhőszolgáltatás, amely egyszerűen és felhasználók hitelesítésére eléréséhez a webes alkalmazások és szolgáltatások miközben lehetővé teszi a hitelesítési és engedélyezési kívül a kódot kell figyelembe venni az egyes szolgáltatásai. Elsősorban a fejlesztők és a .NET-ügyfelekről, ASP.NET-webalkalmazások és WCF-webszolgáltatások a fejlesztők által használható.

Az ACS alkalmazási helyzetei három fő kategóriába sorolhatók:

- Bizonyos Microsoft felhőszolgáltatásai, beleértve az Azure Service Bus, a Dynamics CRM és egyéb hitelesítéséhez. Ügyfélalkalmazások jogkivonatok tudta megszerezni az ACS-től használható, hogy ezek a szolgáltatások különböző műveleteket végezhetnek hitelesítést.
- Hitelesítés hozzáadása a webes alkalmazásokhoz, mind az egyéni kialakítású fajta & a előcsomagolt fajta (például Sharepointról). "Passzív" ACS-hitelesítést használ, webes alkalmazások támogathatják jelentkezzen be Google, Facebook-on, Yahoo, Microsoft fiók (Live ID), Azure Active Directory és az AD FS származó fiókokat.
- Egyéni beépített webes szolgáltatások biztosítása az ACS által kiállított jogkivonatokat. "Aktív" hitelesítést használ, web services sikerült győződjön meg arról, hogy azok csak engedélyezik a hozzáférést, az ACS használatával hitelesített ismert ügyfelekről.

Minden egyes esetekben használható, és az ajánlott áttelepítési stratégia a következő szakaszban tárgyalt. A esetekben többsége jelentős kódmódosításokat ahhoz, hogy a meglévő alkalmazások és szolgáltatások áttelepítése újabb technológiák. Javasoljuk, hogy kezdje tervezési & azonnal minden lehetséges valamilyen okból kimaradás vagy az állásidő elkerülése érdekében az áttelepítés végrehajtása.

> [!WARNING]
> A legtöbb esetben a jelentős kódmódosításokat ahhoz, hogy a meglévő alkalmazások és szolgáltatások áttelepítése újabb technológiák. Javasoljuk, hogy kezdje tervezési & azonnal minden lehetséges valamilyen okból kimaradás vagy az állásidő elkerülése érdekében az áttelepítés végrehajtása.

ACS architektúráját, teljes mértékben a következő összetevőkből áll:

- A biztonságos biztonságijogkivonat-szolgáltatás (STS), amelyek a hitelesítési kéréseket fogad & biztonsági jogkivonatokat bocsát ki ismét.
- A klasszikus Azure portálon, amely létrehozása, törlése és ACS névterek engedélyezése vagy tiltása.
- Külön ACS felügyeleti portált, amely testreszabása és az ACS-névtér viselkedésének konfigurálása.
- Felügyeleti szolgáltatás segítségével automatizálhatja a portálok funkcióit.
- A token átalakítási szabály motort, amely segítségével határozza meg az ACS által kiállított jogkivonatokat a kimeneti formátum kezelésére szolgáló összetett logikát.

Ezek az összetevők használatához létre kell hoznia egy vagy több ACS **névterek**. Egy névtér, egy dedikált példánya, amely az alkalmazások és szolgáltatások kommunikálni; ACS az el különítve a többi ACS használó összes ügyfél, számára a saját névterek. Az ACS névtér rendelkezik egy dedikált URL-címet, például:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Az összes kommunikáció a STS és felügyeleti műveletek végzett többféle célra elérési útja eltér a következő URL-címen. Határozza meg, ha az alkalmazások vagy szolgáltatások ACS, figyelje a forgalom számára `https://{namespace}.accesscontrol.windows.net`.  Ezen URL-adatforgalmat ACS kezeli, és meg kell megszűnik a forgalom.  Az egyetlen kivétel ez alól az összes bejövő forgalom `https://accounts.accesscontrol.windows.net` -forgalmat az URL-címet már egy másik szolgáltatás kezeli, és nem befolyásolja az ACS érvénytelenítése.  Meg kell, hogy a klasszikus Azure portál és a saját előfizetések bármely ACS névterek a bejelentkezéshez.  Az ACS-névterek jelennek meg a **Active Directory** szolgáltatáshoz, a a **hozzáférés-vezérlési névterek** lapon.

Az ACS további információkért lásd: [ez archivált MSDN dokumentációjában](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Használatból való kivonást ütemezése

Től November 2017 minden ACS összetevői teljes mértékben támogatott & működési. Az egyetlen korlátozás, amely [új ACS-névterek nem hozható létre a klasszikus Azure-portálon](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Ezek az összetevők elavulása ütemtervét követi ezt az ütemezést:

- **2017. november**: az Azure AD rendszergazdai élmény a klasszikus Azure portálon [kivonják](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Ezen a ponton névtér felügyeleti ACS le lesz elérhető, ezzel új, dedikált URL-CÍMEK: `http://manage.windowsazure.com?restoreClassic=true`. Lehetővé teszi a meglévő névterek megtekintéséhez engedélyezését vagy letiltását, azokat, és törölje őket teljesen, ha kívánja azt.
- **Április 2018**: ACS névtér felügyeleti rendszer többé nem lesznek elérhetők a dedikált URL-címen. Ezen a ponton az időben is nem engedélyezése vagy tiltása, törlésére és az ACS-névterek számbavétele. Az ACS felügyeleti portálján, azonban teljes mértékben működőképes és helye `https://{namespace}.accesscontrol.windows.net`. Az ACS összes többi összetevő továbbra is megfelelően működik.
- **November 2018**: összes ACS-összetevőkhöz véglegesen le. Ez magában foglalja az ACS felügyeleti portálra, a felügyeleti szolgáltatás, STS és token átalakítási jogcímszabály-motor. Ezen a ponton minden ACS küldött kérelmek (helye: `{namespace}.accesscontrol.windows.net`) sikertelen. Kell áttelepített meglévő alkalmazások és szolgáltatások más technológiákat is előtt ebben az időszakban.


## <a name="migration-strategies"></a>Áttelepítési stratégiák

A következő szakaszok ismertetik a magas szintű javaslatok ACS ki más Microsoft-technológiák történő áttelepítéséhez.

### <a name="clients-of-microsoft-cloud-services"></a>Az ügyfelek a Microsoft cloud services csomag

Mostantól az ACS által kiállított jogkivonatokat támogató Microsoft-felhőszolgáltatás mindegyikének támogatja a hitelesítési legalább egy másik formája. A megfelelő hitelesítési módszert változó minden szolgáltatásra, ezért azt javasoljuk, minden egyes szolgáltatás hivatalos útmutatót adott dokumentáció. Kényelmi célokat szolgál minden készlete dokumentációját itt biztosítja:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Azure Service Bus | [Közös hozzáférésű Jogosultságkód áttelepítése](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [Közös hozzáférésű Jogosultságkód áttelepítése](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Az Azure Cache | [Migrate to Azure Redis Cache (Áttelepítés Azure Redis Cache-re)](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Az Azure Data piaci | [A kognitív szolgáltatások API-k áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Az Azure Logic Apps alkalmazások áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Az Azure Media Services | [Telepítse át az Azure AD-alapú hitelesítés](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Frissítés az Azure Backup szolgáltatás ügynöke](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Passzív hitelesítést használó webalkalmazások

Az ACS használatával a felhasználók hitelesítéséhez a webes alkalmazásokhoz a fejlesztők és a fejlesztők a webalkalmazások a ACS biztosítja a következő funkciók és képességek:

- Szoros integrációja Windows Identity Foundation (WIF).
- A Google, Facebook-on, Yahoo, Microsoft fiók (Live ID), Azure Active Directory és az AD FS összevonási.
- A következő hitelesítési protokollok támogatása: OAuth 2.0 Vázlat 13, a Ws-Trust, és a Ws-Federation.
- A következő token formátumok támogatása: JSON webes jogkivonat (JWT), a SAML 1.1, a SAML 2.0 és a egyszerű webes jogkivonat (SWT).
- A kezdőtartomány felderítési élmény integrálva WIF, felhasználók azok való bejelentkezéshez használt fiók számára engedélyezett. Ez a felület tárolt a webes alkalmazás és a teljes mértékben testreszabható.
- Token, hogy a jogcímek, az ACS-től, a webes alkalmazás által fogadott gazdag testreszabási átalakítása többek között:
    - az identitás-szolgáltatóktól származó jogcímek áthaladó
    - További egyéni jogcímekként hozzáadása
    - egyszerű Ha-akkor logikai jogcímek bizonyos körülmények között kiadására

Sajnos nincs nem egy szolgáltatás, amely az összes azonos képességek.  Akkor ki kell értékelnie, milyen lehetőségek az ACS meg kell, és válassza a között [ **Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (az Azure AD), [ **az Azure AD B2C** ](https://azure.microsoft.com/services/active-directory-b2c/), vagy egy másik felhőalapú hitelesítési szolgáltatást.

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory áttelepítése

Egy elérési utat kell figyelembe venni az alkalmazások és szolgáltatások van integrálható közvetlenül az Azure Active Directoryban. Az Azure AD a felhő alapú identitásszolgáltató a Microsoft munkahelyi és iskolai fiókok – az identitásszolgáltató az Office 365, Azure és még sok más. Hasonló biztosít összevont hitelesítési funkciókat az ACS, azonban nem támogatja az összes ACS-szolgáltatásokat. Az elsődleges példája közösségi Identitásszolgáltatók, például a Facebook, Google és Yahoo összevonásában. Ha a felhasználók jelentkezzen be az ilyen típusú hitelesítő adatokat, az Azure AD nincs meg. Is végez el nem feltétlenül a pontos azonos hitelesítési protokollok, mint az ACS - mindkét ACS közben támogatása és az Azure AD-támogatás OAuth, például van minden szükséges kód módosíthatja az áttelepítés megvalósítása között finom eltérések vannak.

Az Azure AD azonban több lehetséges előnyöket biztosítja az ACS-ügyfél számára. Natív módon támogatja a Microsoft munkahelyi & iskolai fiókok felhőalapú, ACS-ügyfelek által leggyakrabban használt.  Az Azure AD-bérlő is is összevonva a helyszíni Active Directory keresztül az AD FS, a felhőalapú felhasználók és a helyszínen tárolt felhasználók hitelesítésére alkalmazás egy vagy több példányára.  A Ws-Federation protokoll, amely lehetővé teszi a Windows Identity Foundation (WIF) használata egy webalkalmazás integrálása viszonylag egyszerű is támogatja.

Az alábbi táblázat összehasonlítja a rendelkezésre álló alkalmazások megfelelő ACS részeit, az Azure ad-ben. Magas szinten **Azure Active Directory esetén valószínűleg a megfelelő választás az áttelepítéshez csak engedélyezi a felhasználóknak, jelentkezzen be a Microsoft munkahelyi és iskolai fiókok**.

| Képesség | Az ACS-támogatás | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi és iskolai fiókok | Támogatott | Támogatott |
| Windows Server Active Directory és az AD FS fiókjai | Összevonás az Azure AD-bérlő keresztül <br /> Közvetlen összevonás az AD FS használatával támogatott | Összevonás az Azure AD-bérlő használatával csak támogatott | 
| Egyéb vállalati identitás felügyeleti rendszerek fiókjai | Összevonás az Azure AD-bérlő keresztül lehetséges <br />Közvetlen összevonási keresztül | Összevonás az Azure AD-bérlő keresztül lehetséges |
| Microsoft-fiókok személyes használatra (Windows Live ID) | Támogatott | Az Azure AD v2.0 OAuth protokollt keresztül, de bármilyen más protokollokon keresztül nem támogatott. | 
| Facebook, Google, Yahoo-fiókok | Támogatott | Nem támogatott vállal. |
| **Protokollok és SDK-kompatibilitási** | | |
| A Windows Identity Foundation (WIF) | Támogatott | Támogatott, de korlátozott utasításokat érhető el. |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | Vázlat 13 támogatása | Támogatja az RFC 6749, a legtöbb modern megadását. |
| WS-Trust | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| JSON webes jogkivonatok (JWTs) | A béta támogatott | Támogatott |
| 1.1 a SAML-jogkivonatokat | Támogatott | Támogatott |
| SAML 2.0 jogkivonatok | Támogatott | Támogatott |
| Egyszerű webes jogkivonatok (SWT) | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható hitelesítőtartomány felderítési/fiók felhasználói felületén válassza háttérszínnek. | Alkalmazások integrálhatók letölthető kódot | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| Testre szabhatja a jogcímek jogkivonatokba | Az identitás-szolgáltatóktól származó csatlakoztatott a bemeneti jogcímek<br />Szerezze be hozzáférési tokent az identitásszolgáltató jogcímként<br />A bemeneti jogcímek értékek alapján kimenő jogcímeket kiadni<br />Kimenő jogcímeket kiadni az állandó értékek | Nem lehet közvetlenül csatlakoztatott jogcímek, az összevont identitás-szolgáltatóktól származó<br />Nem olvasható be hozzáférési jogkivonat az identitásszolgáltatótól jogcímként<br />Nem ad a kimeneti jogcímeket a bemeneti jogcímek értékek alapján<br />Az állandó értékek kiadhatja a kimeneti jogcímek<br />Adhatnak ki a kimeneti jogcímek szinkronizálva az Azure AD-felhasználók tulajdonságai alapján |
| **Automatizálás** | | |
| Konfiguráció /-felügyeleti feladatok automatizálásához | Támogatott ACS felügyeleti szolgáltatáson keresztül | A Microsoft Graph & Azure AD Graph API használatával támogatott. |

Ha úgy dönt, hogy az Azure AD-e a megfelelő elérési útját előre az alkalmazások és szolgáltatások, meg kell ügyelnie, két lehetősége is van az alkalmazás integrálja az Azure ad-val.

Az Azure AD integrálása Ws-összevonási/WIF használatához ajánlott következő [ebben a cikkben leírt megközelítést](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). A cikk a webalkalmazásokra konfigurálása az Azure ad-val SAML-alapú egyszeri bejelentkezést, de a Ws-összevonás konfigurálása, valamint működik. Ezt a módszert használja a következő Azure AD Premium-licencre van szükség, de két előnyökkel jár:

- Az Azure AD-token testreszabás teljesen rugalmasan kap. Ez lehetővé teszi, hogy testre szabhatja a megfelelő ACS, különösen ideértve a felhasználói azonosító vagy név azonosító a jogcím azokat az Azure AD által kiadott jogcímeket. Győződjön meg arról, hogy a felhasználó által kibocsátott azok ACS által kibocsátott az Azure AD-egyeztetés továbbra is megjelenik a felhasználók számára egységes felhasználói azonosítók technológiák módosítása után is azonosítók kell.
- Beállíthatja, hogy a jogkivonat-aláíró tanúsítvány az alkalmazásra vonatkoznak, amelynek élettartama szabályozhatja.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Ez a megközelítés törekszik Azure AD Premium-licencre van szükség. Ha az ACS-ügyfél és a premium licenc szükséges egyszeri bejelentkezést az alkalmazás beállítása, kapcsolatba velünk, és örömmel fejlesztői licencet biztosít a használatára.

Egy másik módszert is, hogy kövesse [kódmintában](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), némileg eltérő utasításokat ennek révén a Ws-Federation beállításával. A fenti nem használja a WIF, de ehelyett az ASP.NET 4.5 OWIN köztes. Azonban az utasításokat az alkalmazás regisztrálása érvényes WIF használó alkalmazásokhoz, és nincs szükség az Azure AD Premium licenchez.  Ha úgy dönt, ezt a megközelítést, heneed megértéséhez [kulcsváltás bejelentkezés az Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Ezt a módszert használja az Azure AD globális probléma jogkivonatok aláírókulcsot használja. Alapértelmezés szerint WIF nem frissíti automatikusan aláírási kulcsokat. Az Azure AD a globális aláírási kulcsokat forog, amikor a WIF-implementációjának szüksége van, a módosítások elfogadására irányuló elő kell készíteni.

Ha az OpenID Connect vagy OAuth protokollt keresztül az Azure AD integrálása, javasoljuk, így.  Az Azure AD integrálása a webes alkalmazás érhető el útmutató és részletes dokumentációt kell a [az Azure Active Directory fejlesztői útmutatója](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Az Azure AD B2C történő

A más áttelepítési fontolja meg az elérési út az Azure AD B2C.  B2C jelenleg egy felhőalapú hitelesítés szolgáltatáshoz, hasonló ACS, lehetővé teszi a fejlesztők a hitelesítés és identitás felügyeleti logika egy felhőalapú szolgáltatás kihelyező.  Egy fizetős szolgáltatás, amely a fogyasztó az alkalmazásokat, amelyek akár az aktív felhasználók millióit is elérhető (rétegekkel szabad & prémium).

Például az ACS szolgáltatásban, a legtöbb vonzó funkciók az B2C valamelyike, amely számos különböző típusú fiókokat támogatja. B2C, akkor is bejelentkezhet a Facebook, a Google, a Microsoft, a LinkedIn, a github webhelyen, Yahoo-fiókokat, a felhasználók és egyéb. B2C emellett támogatja a "helyi fiókoknak", vagy a felhasználónév és a felhasználói számára jelszavak kifejezetten az alkalmazáshoz. Az Azure AD B2C is, amelyek segítségével testre szabhatja a bejelentkezési adatfolyamok gazdag bővítési lehetőséget biztosít. Azonban nem, támogatja a hitelesítési protokollok & ACS vevők token formátumok hardverekről. Azt is nem használható a jogkivonatok lekérésére & További információ az identitásszolgáltató, a Microsoft által a felhasználó lekérdezése vagy más módon.

A következő táblázat az ACS kapcsolódik az Azure AD B2C elérhető alkalmazások funkcióit hasonlítja össze. Magas szinten **az Azure AD B2C oka valószínűleg az áttelepítéshez a megfelelő választás irányuló fogyasztó alkalmazás esetén, vagy ha az támogatja-e fiókok számos különböző típusú.**

| Képesség | Az ACS-támogatás | Az Azure AD B2C-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi és iskolai fiókok | Támogatott | Egyéni házirendek keresztül  |
| Windows Server Active Directory és az AD FS fiókjai | Közvetlen összevonás az AD FS használatával támogatott | Egyéni házirendekkel SAML összevonási keresztül |
| Egyéb vállalati identitás felügyeleti rendszerek fiókjai | Ws-Federation keresztül keresztül közvetlen összevonási támogatott | Egyéni házirendekkel SAML összevonási keresztül |
| Microsoft-fiókok személyes használatra (Windows Live ID) | Támogatott | Támogatott | 
| Facebook, Google, Yahoo-fiókok | Támogatott | Facebook & Google támogatott natív módon, Yahoo keresztül OpenID Connect összevonási egyéni házirendekkel |
| **Protokollok és SDK-kompatibilitási** | | |
| A Windows Identity Foundation (WIF) | Támogatott | Nem támogatott. |
| WS-Federation | Támogatott | Nem támogatott. |
| OAuth 2.0 | Vázlat 13 támogatása | Támogatja az RFC 6749, a legtöbb modern megadását. |
| WS-Trust | Támogatott | Nem támogatott. |
| **Token formátumok** | | |
| JSON webes jogkivonatok (JWTs) | A béta támogatott | Támogatott |
| 1.1 a SAML-jogkivonatokat | Támogatott | Nem támogatott |
| SAML 2.0 jogkivonatok | Támogatott | Nem támogatott |
| Egyszerű webes jogkivonatok (SWT) | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható hitelesítőtartomány felderítési/fiók felhasználói felületén válassza háttérszínnek. | Alkalmazások integrálhatók letölthető kódot | Teljes mértékben testreszabható felhasználói felületén keresztül egyéni CSS. |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláírási kulcsokat, nem a tanúsítványok, egyéni házirendek használatával támogatott. |
| Testre szabhatja a jogcímek jogkivonatokba | Az identitás-szolgáltatóktól származó csatlakoztatott a bemeneti jogcímek<br />Szerezze be hozzáférési tokent az identitásszolgáltató jogcímként<br />A bemeneti jogcímek értékek alapján kimenő jogcímeket kiadni<br />Kimenő jogcímeket kiadni az állandó értékek | Az identitás-szolgáltatóktól származó csatlakoztatott jogcímeket is. Egyéni házirendeket az egyes jogcímek szükséges.<br />Nem olvasható be hozzáférési jogkivonat az identitásszolgáltatótól jogcímként<br />Egyéni házirendek segítségével a bemeneti jogcím értékei alapján kimeneti jogcímek adhatnak ki.<br />Kimeneti jogcímek adhat ki az egyéni házirendek keresztül állandó értékek |
| **Automatizálás** | | |
| Konfiguráció /-felügyeleti feladatok automatizálásához | Támogatott ACS felügyeleti szolgáltatáson keresztül | Azure AD Graph API-n keresztül engedélyezett felhasználók létrehozása. Nem hozható létre B2C bérlő, alkalmazások és házirendek programozott módon. |

Ha úgy dönt, hogy az Azure AD B2C-e a megfelelő elérési útját előre az alkalmazások és szolgáltatások, a következőket kell kezdődnie:

- [Az Azure AD B2C-dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Az Azure AD B2C egyéni házirendek](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Az Azure AD B2C díjszabási](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Egyéb áttelepítési lehetőségek

Ha sem az Azure AD és az Azure AD B2C megfelel a webes alkalmazás igényeinek, lépjen kapcsolatba velünk és tudunk segíteni, hogy azonosítsa a legjobb útvonalat előre.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Webszolgáltatások aktív hitelesítést használó

Védi az ACS által kiállított jogkivonatokat a web Services ACS megadott, a következő funkciók és képességek:

- Egy vagy több regisztrálása **identitások szolgáltatás** a jogkivonatok kéréséhez használható ACS névterében.
- A jogkivonatokat, használja a következő típusú hitelesítő adatokat kér az OAuth BURKOLÁSA & OAuth 2.0 Vázlat 13 protokollt támogat:
    - A szolgáltatásidentitás létrehozott egyszerű jelszó
    - A szimmetrikus kulcs vagy X509 SWT aláírt tanúsítvány
    - Egy megbízható identitásszolgáltató (általában az AD FS-példány) által kiadott SAML-jogkivonatból
- A következő token formátumok támogatása: JSON webes jogkivonat (JWT), a SAML 1.1, a SAML 2.0 és a egyszerű webes jogkivonat (SWT).
- Egyszerű token átalakítási szabályok:

Szolgáltatás-identitások az ACS-kiszolgálók (közötti S2S) például a hitelesítés végrehajtásához általában használják.  

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory áttelepítése

Azt javasoljuk, az ilyen típusú hitelesítési folyamat, hogy át [ **Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (az Azure AD). Az Azure AD a felhő alapú identitásszolgáltató a Microsoft munkahelyi és iskolai fiókok – az identitásszolgáltató az Office 365, Azure és még sok más. De is használható a kiszolgáló hitelesítése az Azure AD végrehajtásának OAuth ügyfél hitelesítő adatok megadását.  Az alábbi táblázat összehasonlítja az Azure AD-ben elérhető a kiszolgáló hitelesítési az ACS képességeit.

| Képesség | Az ACS-támogatás | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| Hogyan kell regisztrálni egy webszolgáltatás-bővítmény | Hozzon létre egy függő entitás az ACS felügyeleti portálon. | Az Azure AD-webalkalmazás létrehozása az Azure portálon. |
| Hogyan kell regisztrálni az ügyfél | Szolgáltatásidentitás létrehozása ACS felügyeleti portálon. | Egy másik Azure AD-webalkalmazás létrehozása az Azure portálon. |
| Használt protokoll | OAuth BURKOLÁSA protokoll<br />OAuth 2.0 Vázlat 13 ügyfél hitelesítő adatai megadják | OAuth 2.0 ügyfél hitelesítő adatai megadják |
| Ügyfél-hitelesítési módszer | Egyszerű jelszó<br />Aláírt SWT<br />Az összevont IDP SAML-jogkivonat | Egyszerű jelszó<br />Aláírt JWT |
| Token formátumok | JWT-T<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | Csak a JWT |
| Token átalakítása | Egyéni jogcímeket adhatnak hozzá<br />Egyszerű Ha-akkor jogcím-kiállítási logika | Egyéni jogcímeket adhatnak hozzá | 
| Konfiguráció /-felügyeleti feladatok automatizálásához | Támogatott ACS felügyeleti szolgáltatáson keresztül | A Microsoft Graph & Azure AD Graph API használatával támogatott. |

Végrehajtási kiszolgálók forgatókönyvek útmutatást tekintse meg a következőket:

- [Szolgáltatások közötti szakasza az Azure Active Directory fejlesztői útmutatójában](https://aka.ms/aaddev).
- [Egyszerű jelszó ügyfél hitelesítő adataival démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Tanúsítvány ügyfél hitelesítő adataival démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Egyéb áttelepítési lehetőségek

Az Azure AD nem felel meg a webszolgáltatás igényeinek megfelelően, ha hozzászólásban, és azt segítenek azonosítani azokat a konkrét esetben ajánlott tervezése.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Kérdéseit, aggályokat & Visszajelzés

Tudjuk, hogy sok ACS-ügyfél nem találja egy tiszta áttelepítési útvonalaként a cikk elolvasása után, és esetleg néhány segítséget vagy meghatározni, hogy a megfelelő csomag útmutatást. Ha azt szeretné, és beszéljék meg az áttelepítési forgatókönyvek & kérdéseket, ezen az oldalon hagyja a megjegyzés.
