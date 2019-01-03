---
title: Az Azure Access Control Service át |} A Microsoft Docs
description: Az alkalmazások és szolgáltatások áthelyezésére az Azure Access Control Service (ACS) a lehetőségek ismertetése.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: celested
ms.reviewer: jlu, annaba, hirsin
ms.openlocfilehash: 01781725e3224e2cab49a5e7cc7dcc33030ce9fb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971552"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Útmutató: Migrálás az Azure Access Control Service-ből

A Microsoft Azure Access Control Service (ACS), az Azure Active Directory (Azure AD), a szolgáltatás 2018. November 7 kivezetjük. Alkalmazások és szolgáltatások, amelyek jelenleg használják a hozzáférés-vezérlés kell teljes áttelepíteni meg egy másik hitelesítési mechanizmust. Ez a cikk ismerteti a javaslatok a jelenlegi ügyfelek tervezésekor kivezetjük a hozzáférés-vezérlés használatát. Ha hozzáférés-vezérlés jelenleg nem használja, akkor nem kell semmit sem.

## <a name="overview"></a>Áttekintés

Hozzáférés-vezérlés egy hitelesítési felhőszolgáltatás, amely a webes alkalmazások és szolgáltatások elérését a felhasználók hitelesítése és engedélyezése egyszerű megoldást kínál a rendszer. Lehetővé teszi a hitelesítés és engedélyezés a kód kívül megosztani, számos funkcióját. Hozzáférés-vezérlés elsősorban a fejlesztői és tervezői számára a Microsoft .NET-ügyfelek, az ASP.NET-webalkalmazások és a Windows Communication Foundation (WCF) webszolgáltatások.

Hozzáférés-vezérlés alkalmazási helyzetei bonthatók három fő kategóriába sorolhatók:

- Bizonyos Microsoft felhőszolgáltatásai, beleértve az Azure Service Bus és a Dynamics CRM-hez való hitelesítése. Ügyfélalkalmazások számára a hitelesítést ezekhez a szolgáltatásokhoz, különféle műveleteket végezhet a hozzáférés-vezérlés jogkivonatok lekérésére.
- Hozzáadásával hitelesítést a webes alkalmazásokhoz, egyéni és az előre összeállított (például SharePoint). "Passzív" hitelesítési hozzáférés-vezérlés használatával a webes alkalmazások támogathat, jelentkezzen be Microsoft-fiókhoz (korábban Live ID), és a Google, Facebook, a Yahoo!, az Azure AD-ben lévő fiókok és az Active Directory összevonási szolgáltatások (AD FS).
- Egyéni webkiszolgáló-szolgáltatások biztosítása a hozzáférés-vezérlés által kiállított jogkivonatokban. Webszolgáltatások "aktív" hitelesítés használatával biztosítható, hogy azok engedélyezik a hozzáférést csak ismert, hitelesített ügyfelek a hozzáférés-vezérléssel.

Minden egyes használatieset-forgatókönyveit és a javasolt migrálási stratégiák ismertetése a következő szakaszokban.

> [!WARNING]
> A legtöbb esetben a jelentős kódmódosítás szükségesek a meglévő alkalmazások és szolgáltatások áttelepítése újabb technológiák. Azt javasoljuk, hogy azonnal kezdje el, és minden lehetséges valamilyen okból kimaradás lép, és az üzemkimaradások elkerülése érdekében a migrálás végrehajtása.

Hozzáférés-vezérlés a következő összetevőket tartalmazza:

- A biztonságos jogkivonat-szolgáltatás (STS), amely megkapja a hitelesítési kéréseket, és biztonsági jogkivonatokat cserébe.
- A klasszikus Azure portálra, ahol hoz létre, töröl, és engedélyezze, és tiltsa le a hozzáférés-vezérlés névterek.
- Egy külön hozzáférés-vezérlési felügyeleti portálon, testre szabhatja, illetve konfigurálhatja a hozzáférés-vezérlés névterek.
- Felügyeleti szolgáltatás segítségével automatizálható a portálok funkcióit.
- Egy jogkivonatok átalakítását szabályalapú motor, amellyel a kimeneti formátum jogkivonatokat, amelyeket a hozzáférés-vezérlés kapcsolatos problémák kezelésére összetett logika definiálható.

Ezeket az összetevőket használ, létre kell hoznia egy vagy több hozzáférés-vezérlés névtérbe. A *névtér* egy dedikált példánya, amely az alkalmazások és szolgáltatások kommunikálni hozzáférés-vezérlés. Egy névtér el különítve a többi hozzáférés-vezérlés ügyfél. Más hozzáférés-vezérlés saját névterüket használják. A hozzáférés-vezérlési névtér rendelkezik egy dedikált URL-címe a következőhöz hasonló:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Az STS és felügyeleti műveletek folytatott minden kommunikáció zajlik az URL-címen. Különböző elérési különböző felhasználási célokra használhatja. Annak megállapításához, hogy az alkalmazások és szolgáltatások használja-e a hozzáférés-vezérlés, figyeli, hogy minden forgalom a https://&lt;névtér&gt;. accesscontrol.windows.net. Az URL-címet az összes bejövő forgalom hozzáférés-vezérlés kezeli, és meg kell szüntetni kell. 

Ez a kivétel, az összes bejövő forgalom `https://accounts.accesscontrol.windows.net`. Az URL-címet a forgalmat egy másik szolgáltatás már kezeli és **nem** a hozzáférés-vezérlés elavulásának által érintett. 

Hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Access Control Service 2.0 (archivált)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ismerje meg, amely az alkalmazások érint

Kövesse az ebben a szakaszban megtudhatja, hogy az alkalmazások, amelyek ACS használatból való kivonást egyaránt érinti.

### <a name="download-and-install-acs-powershell"></a>Töltse le és telepítse az ACS PowerShell

1. Nyissa meg a PowerShell-galériából, és töltse le [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Futtassa a modul telepítése

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Az összes lehetséges parancsok listájának lekéréséhez futtassa

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Segítség kérése egy bizonyos paranccsal, futtassa:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    ahol `[Command-Name]` az ACS-parancs neve.

### <a name="list-your-acs-namespaces"></a>Az Access Control-névtereket listázása

1. Az ACS használatával csatlakozhat a **Connect-AcsAccount** parancsmagot.
  
    Szükség lehet futtatni `Set-ExecutionPolicy -ExecutionPolicy Bypass` előtt futtathat parancsokat és a rendszergazda ezen előfizetések kell ahhoz, hogy hajtsa végre a parancsokat.

1. Az használatával érhető el az Azure-előfizetések listájának a **Get-AcsSubscription** parancsmagot.
1. Az ACS névterek használatával listázása a **Get-AcsNamespace** parancsmagot.

### <a name="check-which-applications-will-be-impacted"></a>Ellenőrizze, hogy mely alkalmazások érinti.

1. A névtér használatára az előző lépésből, és nyissa meg `https://<namespace>.accesscontrol.windows.net`

    Például ha a névterek egyik, a contoso-test, lépjen a `https://contoso-test.accesscontrol.windows.net`

1. Alatt **megbízhatósági kapcsolatok**válassza **függő fél alkalmazások** ACS használatból való kivonást egyaránt által érintett alkalmazások listájának megtekintéséhez.
1. Bármely más ACS namespace(s), amely rendelkezik ismételje meg az 1 – 2.

## <a name="retirement-schedule"></a>Kivezetési ütemezése

2017. November minden hozzáférés-vezérlés összetevői teljes körűen támogatott, és működik. Az egyetlen korlátozás, amely akkor [nem hozható létre új hozzáférés-vezérlés névterek, a klasszikus Azure portálon keresztül](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

A következő hozzáférés-vezérlés összetevői kivezetése ütemezése:

- **2017 november**:  Az Azure AD-rendszergazda a klasszikus Azure portál felületét [kivonják a forgalomból](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Ezen a ponton a hozzáférés-vezérlési névtér-felügyelet érhető el egy új, dedikált URL-címen: `https://manage.windowsazure.com?restoreClassic=true`. Használja az URL-cím megtekintheti a meglévő névterekhez, engedélyezése és letiltása a névterek, és törli a névtereket, ha úgy dönt, hogy.
- **2018. április 2.**: A klasszikus Azure portálon teljesen eltávolították, ami azt jelenti, hozzáférés-vezérlési névtér-felügyelet már nem érhető el minden olyan URL-CÍMEN keresztül. Ezen a ponton nem letiltása vagy engedélyezése, törlése, vagy a hozzáférés-vezérlés névterek számbavétele. Azonban a hozzáférés-vezérlési felügyeleti portálon lesz a teljes működési és a következő helyen található `https://\<namespace\>.accesscontrol.windows.net`. Továbbra is szokott hozzáférés-vezérlés összes összetevőjét.
- **2018. november 7**: Véglegesen állítsa le az összes hozzáférés-vezérlés összetevői. Ez magában foglalja a hozzáférés-vezérlési felügyeleti portálon, a felügyeleti szolgáltatás, STS és a jogkivonatok átalakítását szabályalapú motor. Ezen a ponton minden olyan hozzáférés-vezérlés küldött kérelmek (található \<névtér\>. accesscontrol.windows.net) sikertelen. Kell áttelepítette az összes meglévő alkalmazások és szolgáltatások más technológiák is a megadott idő előtti.

> [!NOTE]
> Egy szabályzat letiltja a névterek, amely nem kérte token egy ideig. 2018. szeptember korai ennyi ideig jelenleg, 14 nap inaktivitás után, de ez 7 nap inaktivitás az elkövetkező hetektől csonkolva lesz. Ha rendelkezik, amely jelenleg nincs engedélyezve a hozzáférés-vezérlés névtereket, [töltse le és telepítse az ACS PowerShell](#download-and-install-acs-powershell) kívánja újból engedélyezni a namespace(s).

## <a name="migration-strategies"></a>Migrálási stratégiák

A következő szakaszok ismertetik a hozzáférés-vezérlés más Microsoft-technológiákhoz áttelepíthető magas szintű ajánlásokat.

### <a name="clients-of-microsoft-cloud-services"></a>Az ügyfelek a Microsoft cloud services

Minden Microsoft-felhőszolgáltatás, amely hozzáférés-vezérlés által most kiállított jogkivonatokat fogad legalább egy másik formája, hitelesítést támogatja. A megfelelő hitelesítési mechanizmus az egyes szolgáltatások változik. Azt javasoljuk, hogy az egyes szolgáltatásokhoz tartozó hivatalos útmutatást tartozó dokumentációra hivatkozik. Az egyszerűség kedvéért minden készlete dokumentáció biztosított itt:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Azure Service Bus | [Közös hozzáférésű jogosultságkódok áttelepítése](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Közös hozzáférésű jogosultságkódok áttelepítése](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Az Azure Cache redis áttelepítése](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [A Cognitive Services API-k áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Az Azure App Service Logic Apps funkcióját áttelepítése](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Az Azure Media Services | [Az Azure AD-hitelesítés áttelepítése](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Az Azure Backup-ügynök frissítése](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-ügyfelek számára

A SharePoint 2013-hoz, 2016-ban, és a SharePoint Online ügyfelek hosszú használja az ACS a felhőbeli, helyszíni és hibrid forgatókönyvek hitelesítési célra. Egyes SharePoint-szolgáltatások és alkalmazási helyzetek hatálya alá ACS használatból való kivonást egyaránt, míg mások nem lesz. Az alábbi táblázat összefoglalja migrálási útmutató segítséget nyújt az egyes a legnépszerűbb SharePoint szolgáltatást, hogy használják az ACS:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Az Azure AD-ből származó felhasználók hitelesítése | Korábban az Azure AD nem támogatja az SAML 1.1-es jogkivonatok SharePoint-hitelesítéshez szükséges, és ACS SharePoint kompatibilis az Azure AD-token formátumok fényében, amelyek a köztes lett megadva. Mostantól [SharePoint közvetlenül csatlakozhat az Azure AD-bA az Azure AD-Alkalmazásgyűjtemény SharePoint helyszíni alkalmazás](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Alkalmazás-hitelesítés és a kiszolgálók közötti hitelesítés a helyszíni SharePoint](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Nincs hatással az ACS kivezetési; nem szükséges módosításokat. | 
| [A SharePoint-bővítmények (üzemeltetett szolgáltató és üzemeltetett SharePoint) alacsony megbízhatósági engedélyezése](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Nincs hatással az ACS kivezetési; nem szükséges módosításokat. |
| [SharePoint cloud hybrid search](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Nincs hatással az ACS kivezetési; nem szükséges módosításokat. |

### <a name="web-applications-that-use-passive-authentication"></a>Passzív hitelesítést használó webalkalmazásokat

Webes alkalmazásokhoz, melyek hozzáférés-vezérlés használata felhasználói hitelesítéshez, hozzáférés-vezérlés a következő szolgáltatásokat és képességeket biztosít a webes alkalmazás fejlesztői és tervezői számára:

- Szoros integrációt a Windows Identity Foundation (WIF).
- Összevonás a Google, Facebook, Yahoo, Azure Active Directory és az AD FS-fiókok és a Microsoft-fiókok.
- A következő hitelesítési protokollok támogatása: OAuth 2.0-s Draft 13, a WS-Trust és a Web Services Federation (WS-összevonás).
- A következő token formátumok támogatását: JSON webes jogkivonat (JWT), a SAML 1.1-es, a SAML 2.0 és az egyszerű webes (SWT) tokent.
- A hitelesítőtartomány felfedezési, WIF, integrálva, amely lehetővé teszi a felhasználók számára milyen típusú fiókkal való bejelentkezéshez használják. Ez a webalkalmazás által futtatott, és teljes mértékben testreszabható.
- Jogkivonat-átalakítást, amely lehetővé teszi, hogy a jogcímek, a hozzáférés-vezérlés, a webes alkalmazás által fogadott gazdag testreszabási többek között:
    - Identitás-szolgáltatóktól származó jogcímek továbbítása.
    - További egyéni jogcímek hozzáadása.
    - Bizonyos körülmények között jogcímeket kiadni, egyszerű if-majd logikai.

Sajnos nem áll rendelkezésre egy mindezen egyenértékű képességeket nyújtó szolgáltatás. Ki kell értékelni melyik hozzáférés-vezérlés képességeinek meg kell, és válassza az között [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C-vel), vagy egy másik felhőalapú hitelesítés a szolgáltatás.

#### <a name="migrate-to-azure-active-directory"></a>Az Azure Active Directory áttelepítése

Fontolja meg egy elérési útja van az alkalmazások és szolgáltatások közvetlenül az Azure AD integrálása. Az Azure AD a felhőalapú identitás-szolgáltató egy Microsoft munkahelyi vagy iskolai fiókokat. Azure ad-ben az identitásszolgáltató az Office 365, Azure és még sok más. Biztosít hasonló összevont hitelesítési képességek a hozzáférés-vezérlést, de minden hozzáférés-vezérlés funkció nem támogatja. 

Az elsődleges például az összevonás a közösségi identitásszolgáltató – például a Facebook, a Google vagy a Yahoo!. Ha a felhasználók az ilyen típusú hitelesítő adatokkal jelentkezik be, Azure ad-ben, nem a megoldás az Ön számára. 

Azure ad-ben is nem feltétlenül támogatja a pontos azonos hitelesítési protokollokat, hozzáférés-vezérlés. Például bár a hozzáférés-vezérlés és az Azure AD támogatja az OAuth, nincsenek finom eltérések minden implementáció között. Implementálható szükséges kód módosítása az áttelepítés részeként.

Azonban az Azure AD több lehetséges az előnyöket biztosítják hozzáférés-vezérlés ügyfelek számára. Ezt natív módon támogatja a Microsoft munkahelyi vagy iskolai fiókok, felhőalapú hozzáférés-vezérlés ügyfelek által leggyakrabban használt. 

Az Azure AD-bérlő is is összevonva a helyszíni Active Directory, AD FS szolgáltatás használatával egy vagy több példányára. Ezzel a módszerrel az alkalmazás hitelesíteni tudja a felhőalapú felhasználók és a felhasználóknak, akik a helyszínen üzemeltetett. Ezenkívül támogatja a WS-Federation protokollt, ami lehetővé teszi egy webes alkalmazás integrálása a WIF viszonylag egyszerű.

Az alábbi táblázat hasonlítja össze a szolgáltatások hozzáférés-vezérlést, amely a webes alkalmazások ezeket az Azure ad-ben elérhető funkciókat. 

Magas szinten *Azure Active Directory oka valószínűleg az áttelepítéshez a legjobb választás Ha engedélyezi a felhasználók bejelentkezését az csak a Microsoft a munkahelyi vagy iskolai fiókok*.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Támogatott |
| Fiókok, a Windows Server Active Directory és az AD FS használatával |– Keresztül támogatott összevonás az Azure AD-bérlő <br />– Keresztül támogatott a közvetlen összevonás az AD FS-sel | Csak az Azure AD-bérlő összevonási keresztül támogatott | 
| Egyéb vállalati identitáskezelő-rendszer fiókok |– Összevonás az Azure AD-bérlő keresztül lehetséges <br />– Keresztül támogatott a közvetlen összevonási | Összevonás az Azure AD-bérlő keresztül lehetséges |
| Microsoft-fiókok személyes használatra | Támogatott | Az Azure AD v2.0 protokoll OAuth-n keresztül, de semmilyen más protokollt nem keresztül támogatott | 
| A Facebook, Google, Yahoo-fiókok | Támogatott | Nem támogatott ajánlattevőről |
| **Protokollok és SDK-kompatibilitási** | | |
| WIF | Támogatott | Támogatott, de korlátozott utasítások |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | Vázlat 13 támogatása | RFC 6749, a legtöbb modern specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| JWT | A béta támogatott | Támogatott |
| SAML 1.1 | Támogatott | Előzetes verzió |
| SAML 2.0 | Támogatott | Támogatott |
| SWT | Támogatott | Nem támogatott |
| **A testreszabások** | | |
| Testre szabható kezdőtartomány felderítése/fiók-kiadási felhasználói felület | Letölthető kódot, amely integrálható az alkalmazásokba | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| A jogkivonatok jogcímek testreszabása |– Identitás-szolgáltatóktól származó bemeneti jogcímek továbbítása<br />-Hozzáférési jogkivonat beszerzése az identitásszolgáltató jogcímként<br />– A bemeneti jogcím értékei alapján kimenő jogcímeket kiadni<br />-Kimeneti jogcímeket kiadni az állandó értékek |-A nem összevont identitás-szolgáltatóktól származó jogcímek továbbítása<br />– Nem szükséges hozzáférési jogkivonat beszerzése az identitásszolgáltató jogcímként<br />– Nem adható ki a kimeneti jogcímek bemeneti jogcím értékei alapján<br />-Adhat ki a kimeneti jogcímek az állandó értékek<br />-Adhat ki a kimeneti jogcímek a felhasználók szinkronizálása az Azure AD-tulajdonságok alapján |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control Management szolgáltatáson keresztül támogatott | A Microsoft Graph és az Azure AD Graph API-n keresztül támogatott |

Ha úgy dönt, hogy Azure ad-ben-e a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások, az alkalmazás integrálása az Azure ad-ben kétféle módon tisztában kell lennie.

WS-Federation vagy WIF segítségével integrálhatja az Azure ad-vel, javasoljuk, hogy a megközelítés ismertetett alábbi [konfigurálni összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). A cikk az SAML-alapú egyszeri bejelentkezés az Azure AD konfigurálása hivatkozik, de is használható a WS-összevonás konfigurálása. Ennek a módszernek egy prémium szintű Azure AD-licenc szükséges. Ez a módszer két előnnyel jár:

- Az Azure AD-token testreszabás teljesen rugalmasan kap. Testre szabhatja a jogcímek, az Azure AD megfelelő hozzáférés-vezérlés által kibocsátott jogcímek által kiállított. Ez különösen akkor tartalmazza a felhasználói azonosító vagy Névazonosító jogcímet. Továbbra is megjelenik a felhasználók egységes felhasználói azonosítók technológiák módosítása után, győződjön meg arról, hogy a felhasználói azonosítók által kiállított Azure AD-egyezés azokat, amelyeket a hozzáférés-vezérlés által.
- Konfigurálhatja egy adott, az alkalmazáshoz, és Ön által megadott élettartamú jogkivonat-aláíró tanúsítványt.

> [!NOTE]
> Ez a megközelítés egy prémium szintű Azure AD-licencre van szükség. Ha az ügyfél egy hozzáférés-vezérlés és egyszeri bejelentkezést az alkalmazáshoz beállításának prémium licencre van szüksége, lépjen kapcsolatba velünk. Készséggel adja meg a fejlesztői licenc kell használni.

Egy másik módszert, hogy [Ez a kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), WS-Federation beállításának némileg eltérő utasításait révén. Ez a kódminta nem használja a WIF, de ehelyett az ASP.NET 4.5-ös OWIN közbenső szoftvert. Azonban az utasításokat az alkalmazás regisztrációját a WIF használó alkalmazások esetében érvényesek, és nem egy prémium szintű Azure AD-licenc szükséges. 

Ha ezt a módszert választja, tisztában kell [az Azure Active Directory aláírókulcs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Ez a megközelítés az Azure AD globális probléma jogkivonatok aláírókulcsot használja. Alapértelmezés szerint WIF nem frissül automatikusan aláírási kulcsokat. Az Azure AD globális aláíró kulcsai forog, amikor a WIF-implementációjának szüksége van, elő kell készíteni a módosítások elfogadásához. További információkért lásd: [fontos információ az Azure Active Directory aláírókulcs](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Ha integrálható az Azure AD-n keresztül az OpenID Connect vagy OAuth protokollt, javasoljuk, így. Széles körű dokumentációval és útmutatást az Azure AD integrálása a webes alkalmazások, elérhető a [az Azure AD fejlesztői útmutató](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Az Azure Active Directory B2C-vel áttelepítése

A más áttelepítési út kell figyelembe venni, ami az Azure AD B2C-t. Az Azure AD B2C egy felhőalapú hitelesítési szolgáltatás, amely, például hozzáférés-vezérlés lehetővé teszi a fejlesztők számára a hitelesítés és identitás felügyeleti logikai felhőszolgáltatás kiszervezik. (Az ingyenes és prémium szinten) egy fizetős szolgáltatás, amely előfordulhat, hogy akár több millió aktív felhasználóval érintkező alkalmazások számára tervezett.

Hozzáférés-vezérlés, hasonlóan az a legvonzóbb Azure AD B2C-funkciók egyikét is, hogy számos különböző típusú fiókokat támogatja. Az Azure AD B2C is használatával írja alá a felhasználók saját Microsoft-fiókjával, vagy a Facebook, Google, LinkedIn, GitHub vagy a Yahoo!-fiókokat, és több. Az Azure AD B2C is támogatja a "helyi fiókoknak," vagy a felhasználónév és a, hogy a felhasználók csak ehhez az alkalmazáshoz. Az Azure AD B2C-t is biztosít, amelyek segítségével testre szabhatja a bejelentkezést folyamatok gazdag bővíthetőség. 

Azonban az Azure AD B2C-vel nem támogatja a hitelesítési protokollok technológiai spektrumunk kihasználtságának növelését, és jogkivonat formázza az ügyfelek szükség lehet a hozzáférés-vezérlést. Még nem használhat az Azure AD B2C-vel jogkivonatok és lekérdezési kaphat a felhasználó további információt az identitásszolgáltató, a Microsoft vagy más módon.

Az alábbi táblázat a hozzáférés-vezérlés, amely a webes alkalmazásokhoz az Azure AD B2C-ben elérhető funkcióit hasonlítja össze. Magas szinten *Azure AD B2C-vel oka valószínűleg az áttelepítéshez a megfelelő választás, ha az alkalmazás felhasználói, vagy ha a számos különböző típusú fiókokat támogatja.*

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD B2C-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| A Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Egyéni szabályzatok keresztül támogatott  |
| Fiókok, a Windows Server Active Directory és az AD FS használatával | Közvetlen összevonás az AD FS-n keresztül támogatott | Egyéni szabályzatok használatával SAML összevonási keresztül támogatott |
| Egyéb vállalati identitáskezelő-rendszer fiókok | WS-Federation keresztül közvetlen összevonási keresztül támogatott | Egyéni szabályzatok használatával SAML összevonási keresztül támogatott |
| Microsoft-fiókok személyes használatra | Támogatott | Támogatott | 
| A Facebook, Google, Yahoo-fiókok | Támogatott | Facebook és a Google támogatott natív módon, Yahoo egyéni szabályzatok használatával összevonási OpenID Connect-n keresztül támogatott |
| **Protokollok és SDK-kompatibilitási** | | |
| Windows Identity Foundation (WIF) | Támogatott | Nem támogatott |
| WS-Federation | Támogatott | Nem támogatott |
| OAuth 2.0 | Vázlat 13 támogatása | RFC 6749, a legtöbb modern specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| JWT | A béta támogatott | Támogatott |
| SAML 1.1 | Támogatott | Nem támogatott |
| SAML 2.0 | Támogatott | Nem támogatott |
| SWT | Támogatott | Nem támogatott |
| **A testreszabások** | | |
| Testre szabható kezdőtartomány felderítése/fiók-kiadási felhasználói felület | Letölthető kódot, amely integrálható az alkalmazásokba | Egyéni CSS-n keresztül teljes mértékben testre szabható felhasználói felület |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláírási kulcsokat, nem a tanúsítványok, egyéni szabályzatok keresztül támogatott |
| A jogkivonatok jogcímek testreszabása |– Identitás-szolgáltatóktól származó bemeneti jogcímek továbbítása<br />-Hozzáférési jogkivonat beszerzése az identitásszolgáltató jogcímként<br />– A bemeneti jogcím értékei alapján kimenő jogcímeket kiadni<br />-Kimeneti jogcímeket kiadni az állandó értékek |-Tevékenységeken keresztül; identitás-szolgáltatóktól származó jogcímek egyéni szabályzatok egyes jogcímek megadása kötelező<br />– Nem szükséges hozzáférési jogkivonat beszerzése az identitásszolgáltató jogcímként<br />-Adhat ki a kimeneti jogcímek egyéni szabályzatok segítségével a bemeneti jogcím értékei alapján<br />-Adhat ki a kimeneti jogcímek az állandó értékek egyéni szabályzatok használatával |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control Management szolgáltatáson keresztül támogatott |-Felhasználók Azure AD Graph API-n keresztül engedélyezett létrehozása<br />– Nem hozható létre B2C bérlő, alkalmazásokat és szabályzatokat programozott módon |

Ha úgy dönt, hogy az Azure AD B2C-vel-e a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások, a következő erőforrások kezdődik:

- [Az Azure AD B2C dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Az Azure AD B2C-vel egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Az Azure AD B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>A Ping Identity vagy Auth0 áttelepítése

Bizonyos esetekben előfordulhat, hogy az Azure AD és az Azure AD B2C-vel nem elegendő a hozzáférés-vezérlés cserélnie a webes alkalmazások főbb kódmódosítások nélkül. Néhány gyakori példa a következők lehetnek:

- A bejelentkezés közösségi Identitásszolgáltatók, például a Google vagy a Facebook-WIF vagy WS-Federation hitelesítést használó webalkalmazásokat.
- Webes alkalmazások, amelyek a WS-Federation protokollt egy vállalati identitásszolgáltatóval való közvetlen összevonási végre.
- Webes alkalmazások, mint a hozzáférés-vezérlés által kiállított jogkivonatokat a jogcím a egy közösségi identitásszolgáltatót (például a Google vagy a Facebook) által kiállított hozzáférési jogkivonat igénylő.
- Webes alkalmazások, amelyek az Azure AD vagy az Azure AD B2C-vel nem képesek összetett token átalakítási szabályok.
- Az ACS használatával központilag kezelhető az összevonási számos különböző identitás-szolgáltatóktól, több-bérlős webalkalmazások

Ezekben az esetekben érdemes megfontolni a felhő egy másik hitelesítési szolgáltatást a webes alkalmazásba való migrálás. Azt javasoljuk, hogy a következő opciók megismerését. Hozzáférés-vezérlés hasonló képességeket kínálnak az alábbi beállításokat:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) egy olyan rugalmas felhőalapú identitásszolgáltatás, által létrehozott [magas szintű áttelepítési útmutató az ügyfelek a hozzáférés-vezérlés](https://auth0.com/acs), és szinte az összes funkció, amely az ACS támogatja. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [A ping Identity](https://www.pingidentity.com) hasonló az ACS-két megoldást kínál. PingOne olyan felhőalapú identitásszolgáltatás, amely támogatja a ugyanazokat a szolgáltatásokat, mint az ACS számos, a PingFederate pedig egy hasonló helyszíni identitás-termék, amely nagyobb rugalmasságot biztosít. Tekintse meg [Ping tartozó ACS használatból való kivonást egyaránt útmutatást](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) termékek használatával kapcsolatos további részletekért. |

A Ping Identity és az Auth0 használata a célja, hogy győződjön meg arról, hogy minden hozzáférés-vezérlés ügyfél rendelkezik egy áttelepítési útvonal az alkalmazások és szolgáltatások számára, hogy a hozzáférés-vezérlés áthelyezéséhez szükséges munka mennyiségét.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Aktív hitelesítést használó webszolgáltatások

A webes szolgáltatásokat, amelyeket az hozzáférés-vezérlés által kiállított jogkivonatokban a hozzáférés-vezérlés a következő szolgáltatásokat és képességeket kínál:

- Egy vagy több regisztrálása *szolgáltatásidentitásokat* a hozzáférés-vezérlés névtérben. Szolgáltatás-identitások jogkivonatokat kérhet használható.
- Támogatja az OAuth BURKOLÁSA és az OAuth 2.0-s Draft 13 protokollok a jogkivonatokat, kérése a következő típusú hitelesítő adatok használatával:
    - Olyan egyszerű jelszavakat, amely jön létre a felügyeltszolgáltatás-identitás
    - Egy aláírt SWT szimmetrikus kulcs vagy X509 tanúsítvány
    - Egy megbízható identitásszolgáltatóra (általában az AD FS-példány) által kiállított SAML-jogkivonat
- A következő token formátumok támogatását: JWT, a SAML 1.1-es, a SAML 2.0 és a SWT.
- Egyszerű token átalakítási szabályok.

Szolgáltatásidentitások a hozzáférés-vezérlés megvalósításához a kiszolgálók közötti hitelesítéshez jellemzően használják. 

#### <a name="migrate-to-azure-active-directory"></a>Az Azure Active Directory áttelepítése

Azt javasoljuk, az ilyen típusú hitelesítési folyamatát, hogy át [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Az Azure AD a felhőalapú identitás-szolgáltató egy Microsoft munkahelyi vagy iskolai fiókokat. Azure ad-ben az identitásszolgáltató az Office 365, Azure és még sok más. 

Az OAuth ügyfélhitelesítő adatok megvalósítása az Azure AD használatával a kiszolgálók közötti hitelesítéshez is használható az Azure AD. Az alábbi táblázat összehasonlítja a hozzáférés-vezérlés a kiszolgálók közötti hitelesítés az Azure AD-ben elérhető képességeit.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| Webszolgáltatás regisztrálása | A hozzáférés-vezérlési felügyeleti portálon hozzon létre egy függő entitás | Egy Azure AD-webalkalmazás létrehozása az Azure Portalon |
| Egy ügyfél regisztrálása | Szolgáltatásidentitás létrehozása a hozzáférés-vezérlési felügyeleti portálon | Egy másik Azure AD-webalkalmazás létrehozása az Azure Portalon |
| Használt protokoll |-OAuth ÚJRAINDULÁS protokoll<br />– OAuth 2.0-s Draft 13 ügyfél hitelesítő adatainak megadása | OAuth 2.0-ügyfél hitelesítő adatainak megadása |
| Ügyfél-hitelesítési módszer |– Egyszerű jelszó<br />-Aláírt SWT<br />-Egy összevont identitásszolgáltatótól az SAML jogkivonat |– Egyszerű jelszó<br />-A JWT aláírt |
| Token formátumok |-JWT<br />-SAML 1.1-ES<br />-SAML 2.0-S<br />-SWT<br /> | Csak a JWT |
| Jogkivonatok átalakítását |– Az egyéni jogcímek hozzáadása<br />– Egyszerű if-majd jogcím-kiállítási logika | Adja hozzá az egyéni jogcímek | 
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control Management szolgáltatáson keresztül támogatott | A Microsoft Graph és az Azure AD Graph API-n keresztül támogatott |

Végrehajtási kiszolgálók-forgatókönyvekkel kapcsolatos útmutatásért lásd a következőket:

- Szolgáltatások közötti szakaszában a [az Azure AD fejlesztői útmutató](https://aka.ms/aaddev)
- [Egyszerű jelszó ügyfél-hitelesítő adatok használatával démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Kódminta démon tanúsítvány ügyfél-hitelesítő adatok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>A Ping Identity vagy Auth0 áttelepítése

Bizonyos esetekben előfordulhat, hogy az Azure AD-ügyfél hitelesítő adatait, és az OAuth biztosítson megvalósítása nem elegendő, cserélje le a hozzáférés-vezérlés az architektúra jelentős programkód módosítása nélkül. Néhány gyakori példa a következők lehetnek:

- Kiszolgálók közötti hitelesítés eltérő JWTs token formátumok használata.
- Kiszolgálók közötti hitelesítéshez egy külső identitásszolgáltatónak által biztosított bemeneti token használatával.
- Kiszolgálók közötti hitelesítési jogkivonatok átalakítását szabályok, amelyek nem képesek a Azure ad-ben.

Ezekben az esetekben érdemes lehet egy másik felhőalapú hitelesítési szolgáltatást a webes alkalmazásba való migrálás. Azt javasoljuk, hogy a következő opciók megismerését. Hozzáférés-vezérlés hasonló képességeket kínálnak az alábbi beállításokat:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) egy olyan rugalmas felhőalapú identitásszolgáltatás, által létrehozott [magas szintű áttelepítési útmutató az ügyfelek a hozzáférés-vezérlés](https://auth0.com/acs), és szinte az összes funkció, amely az ACS támogatja. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [A ping Identity](https://www.pingidentity.com) hasonló az ACS-két megoldást kínál. PingOne olyan felhőalapú identitásszolgáltatás, amely támogatja a ugyanazokat a szolgáltatásokat, mint az ACS számos, a PingFederate pedig egy hasonló helyszíni identitás-termék, amely nagyobb rugalmasságot biztosít. Tekintse meg [Ping tartozó ACS használatból való kivonást egyaránt útmutatást](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) termékek használatával kapcsolatos további részletekért. |

A Ping Identity és az Auth0 használata a célja, hogy győződjön meg arról, hogy minden hozzáférés-vezérlés ügyfél rendelkezik egy áttelepítési útvonal az alkalmazások és szolgáltatások számára, hogy a hozzáférés-vezérlés áthelyezéséhez szükséges munka mennyiségét.

#### <a name="passthrough-authentication"></a>Átmenő hitelesítés

Az átmenő hitelesítés a tetszőleges jogkivonatok átalakítását nem az ACS-nem megfelelő Microsoft-technológiát. Ha az ügyfelek kell, Auth0 lehet, aki a legközelebbi előállításához megoldást biztosít egy.

## <a name="questions-concerns-and-feedback"></a>Kérdések, a problémák és visszajelzés

Tisztában vagyunk vele, hogy számos hozzáférés-vezérlés ügyfél nem találja meg egy tiszta áttelepítési út a cikk elolvasása után. Néhány segítséget és útmutatást a megfelelő terv meghatározásához szükséges. Ha szeretné megvitatni a áttelepítési forgatókönyvek és a kérdésekre, hozzászólásban ezen az oldalon.
