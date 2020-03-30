---
title: Áttelepítés az Azure Access Control Szolgáltatásról | Microsoft dokumentumok
description: Ismerje meg az alkalmazások és szolgáltatások azure access control service (ACS) általi áthelyezésének lehetőségeit.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 3168d36bf4c2d3c696173725f669b12dc168dcc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155001"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Útmutató: Áttelepítés az Azure Access Control Szolgáltatásból

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

2018. november 7-én megszűnik a Microsoft Azure Access Control Service (ACS), az Azure Active Directory (Azure AD) egyik szolgáltatása. A hozzáférés-vezérlést jelenleg használó alkalmazásokat és szolgáltatásokat addigra teljesen át kell telepíteni egy másik hitelesítési mechanizmusba. Ez a cikk a jelenlegi ügyfeleknek szóló javaslatokat ismerteti, mivel a hozzáférés-vezérlés használatának emittálását tervezi. Ha jelenleg nem használja a hozzáférés-vezérlést, nem kell semmilyen műveletet végrehajtania.

## <a name="overview"></a>Áttekintés

A hozzáférés-vezérlés egy felhőalapú hitelesítési szolgáltatás, amely egyszerű módot kínál a felhasználók hitelesítésére és engedélyezésére a webes alkalmazásokhoz és szolgáltatásokhoz való hozzáféréshez. Lehetővé teszi, hogy a hitelesítés és az engedélyezés számos szolgáltatását figyelembe lehessen venni a kódból. A hozzáférés-vezérlést elsősorban a Microsoft .NET ügyfelek, ASP.NET webalkalmazások és a Windows Kommunikációs Alapítvány (WCF) webszolgáltatásainak fejlesztői és építészei használják.

A hozzáférés-vezérlés használati esetei három fő kategóriába sorolhatók:

- Hitelesítés bizonyos Microsoft felhőszolgáltatásokhoz, beleértve az Azure Service Bus és a Dynamics CRM szolgáltatást. Az ügyfélalkalmazások jogkivonatokat szereznek be a hozzáférés-vezérléstől, hogy hitelesítsék magukat ezeken a szolgáltatásokon, és különböző műveleteket hajtsanak végre.
- Hitelesítés hozzáadása a webalkalmazásokhoz, egyéni és előre csomagolt (például SharePoint). A hozzáférés-vezérlés "passzív" hitelesítésével a webalkalmazások támogathatják a Microsoft-fiókkal (korábban Live ID) és a Google, a Facebook, a Yahoo, az Azure AD és az Active Directory összevonási szolgáltatások (AD FS) fiókokkal történő bejelentkezést.
- Egyéni webszolgáltatások biztonságossá tétele a hozzáférés-vezérlés által kibocsátott jogkivonatokkal. Az "aktív" hitelesítés használatával a webszolgáltatások biztosíthatják, hogy csak a hozzáférés-vezérléssel hitelesített ismert ügyfelek számára engedélyezze a hozzáférést.

A következő szakaszok ismertetik a felhasználási esetek és az ajánlott áttelepítési stratégiák mindegyikét.

> [!WARNING]
> A legtöbb esetben jelentős kódmódosításokra van szükség a meglévő alkalmazások és szolgáltatások újabb technológiákra való áttelepítéséhez. Azt javasoljuk, hogy azonnal kezdje meg az áttelepítés tervezését és végrehajtását az esetleges kimaradások vagy állásidők elkerülése érdekében.

A hozzáférés-vezérlés a következő összetevőket tartalmaz:

- Egy biztonságos jogkivonat-szolgáltatás (STS), amely megkapja a hitelesítési kérelmeket, és biztonsági jogkivonatokat ad ki cserébe.
- Az Azure klasszikus portál, ahol hozzon létre, töröl, és engedélyezi és tiltsa le a hozzáférés-vezérlés i.
- Külön hozzáférés-vezérlési felügyeleti portál, ahol testre szabhatja és konfigurálhatja a hozzáférés-vezérlés iterét.
- Egy felügyeleti szolgáltatás, amellyel automatizálhatja a portálok funkcióit.
- Jogkivonat-átalakítási szabálymotor, amellyel összetett logikát definiálhat a hozzáférés-vezérlési problémákat okozó jogkivonatok kimeneti formátumának kezeléséhez.

Ezen összetevők használatához létre kell hoznia egy vagy több hozzáférés-vezérlési névteret. A *névtér* a hozzáférés-vezérlés egy dedikált példánya, amelyekkel az alkalmazások és a szolgáltatások kommunikálnak. A névtér el van különítve az összes többi hozzáférés-vezérlési ügyféltől. Más hozzáférés-vezérlési ügyfelek saját névterüket használják. A hozzáférés-vezérlésben lévő névtér a következőképpen néz ki:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Az STS-sel és a felügyeleti műveletekkel folytatott minden kommunikáció ezen az URL-címen történik. Különböző elérési utakat használ különböző célokra. Annak megállapításához, hogy az alkalmazások vagy szolgáltatások használja-e a hozzáférés-vezérlést, figyelje a https://&lt;névtérbe&gt;(accesscontrol.windows.net) irányuló forgalmat. Az URL-címre irányuló forgalmat a hozzáférés-vezérlés kezeli, és meg kell szüntetni. 

Ez alól kivételt képez `https://accounts.accesscontrol.windows.net`a forgalmat. Az URL-címforgalvaló forgalmat már kezeli egy másik **szolgáltatás,** és a hozzáférés-vezérlés ellenőrizhetetlenné nem befolyásolja. 

A hozzáférés-vezérlésről további információt a [Hozzáférés-vezérlési szolgáltatás 2.0 (archivált) című témakörben talál.](https://msdn.microsoft.com/library/hh147631.aspx)

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Annak megtudd, hogy mely alkalmazásokat érinti a hatás

Az ebben a szakaszban ismertetett lépéseket követve megtudhatja, hogy mely alkalmazásokat érinti az ACS-megszüntetés.

### <a name="download-and-install-acs-powershell"></a>Az ACS PowerShell letöltése és telepítése

1. Nyissa meg a PowerShell-galériát, és töltse le [az Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Telepítse a modult a

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Az összes lehetséges parancs listájának beszerezése futással

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Ha segítségre van szüksége egy adott parancshoz, futtassa a következő parancsot:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    hol `[Command-Name]` van az ACS parancs neve.

### <a name="list-your-acs-namespaces"></a>Az ACS-névterek listázása

1. Csatlakozzon az ACS-hez a **Connect-AcsAccount** parancsmag használatával.
  
    Előfordulhat, hogy `Set-ExecutionPolicy -ExecutionPolicy Bypass` futnia kell, mielőtt parancsokat futtathatna, és az előfizetések rendszergazdájának kell lennie a parancsok végrehajtásához.

2. Az elérhető Azure-előfizetések listája a **Get-AcsSubscription** parancsmag használatával.
3. Sorolja fel az ACS-névtereket a **Get-AcsNamespace** parancsmag használatával.

### <a name="check-which-applications-will-be-impacted"></a>Annak ellenőrzése, hogy mely alkalmazásokra lesz hatással

1. Használja az előző lépés névterét, és lépjen a`https://<namespace>.accesscontrol.windows.net`

    Ha például az egyik névtér contoso-teszt, lépjen a`https://contoso-test.accesscontrol.windows.net`

2. A **Megbízhatósági kapcsolatok csoportban**válassza a **Függő entitás alkalmazások** lehetőséget az ACS-megszüntetés által érintett alkalmazások listájának megtekintéséhez.
3. Ismételje meg az 1-2.

## <a name="retirement-schedule"></a>Nyugdíjazási ütemterv

2017 novemberétől minden hozzáférés-vezérlési összetevő teljes mértékben támogatott és működőképes. Az egyetlen korlátozás az, hogy [nem hozhat létre új hozzáférés-vezérlési névtereket az Azure klasszikus portálon keresztül.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

A hozzáférés-vezérlés összetevőinek elavulttal történő leküzdésének ütemezése:

- **2017. november:** Az Azure AD-rendszergazdai élmény [e-nek kivan vezetve.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Ezen a ponton a hozzáférés-vezérlés névtér-kezelése egy `https://manage.windowsazure.com?restoreClassic=true`új, dedikált URL-címen érhető el: . Ezzel az URL-vel megtekintheti a meglévő névtereket, engedélyezheti és letilthatja a névtereket, és törölheti a névtereket, ha úgy dönt.
- **2018. április 2.** Ezen a ponton nem tilthatja le és nem engedélyezheti, törölheti és nem számba veheti a hozzáférés-vezérlési névtereket. A hozzáférés-vezérlési felügyeleti portál azonban teljes `https://\<namespace\>.accesscontrol.windows.net`mértékben működőképes lesz, és a helyen található. A hozzáférés-vezérlés minden más összetevője továbbra is normálisan működik.
- **2018. november 7.:** Az összes hozzáférés-vezérlési összetevő véglegesen leáll. Ez magában foglalja a hozzáférés-vezérlési felügyeleti portál, a felügyeleti szolgáltatás, az STS és a jogkivonat-átalakítási szabály motor. Ezen a ponton a hozzáférés-vezérlésre \<(a\>.accesscontrol.windows.net névtérben található) küldött kérelmek sikertelenek lesznek. Az összes meglévő alkalmazást és szolgáltatást már jóval ez idő előtt át kellett volna telepítenie más technológiákra.

> [!NOTE]
> A házirend letiltja azokat a névtereket, amelyek egy ideig nem kértek jogkivonatot. 2018. szeptember elejétől ez az időszak jelenleg 14 napos inaktivitás, de ez a következő hetekben 7 napos inaktivitásra rövidül. Ha jelenleg le van tiltva a hozzáférés-vezérlési névterek, [letöltheti és telepítheti az ACS PowerShellt](#download-and-install-acs-powershell) a névtér(ek) újbóli engedélyezéséhez.

## <a name="migration-strategies"></a>Migrálási stratégiák

Az alábbi szakaszok magas szintű javaslatokat tartalmaznak a hozzáférés-vezérlésről más Microsoft-technológiákra való áttérésre vonatkozóan.

### <a name="clients-of-microsoft-cloud-services"></a>A Microsoft felhőszolgáltatásainak ügyfelei

Minden olyan Microsoft felhőszolgáltatás, amely elfogadja a hozzáférés-vezérlés által kibocsátott jogkivonatokat, mostantól legalább egy alternatív hitelesítési formát támogat. A megfelelő hitelesítési mechanizmus szolgáltatásonként eltérő. Javasoljuk, hogy hivatalos útmutatásért olvassa el az egyes szolgáltatások dokumentációját. Az egyszerűség kedvéért minden egyes dokumentációkészlet itt található:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Azure Service Bus | [Áttelepítés megosztott hozzáférésű aláírásokra](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure szolgáltatásbusz-továbbító | [Áttelepítés megosztott hozzáférésű aláírásokra](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure felügyelt gyorsítótár | [Migrálás a Redis Azure-gyorsítótárába](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Áttelepítés a Cognitive Services API-kra](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk szolgáltatások | [Áttelepítés az Azure App Service Logikai alkalmazások szolgáltatására](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Áttelepítés az Azure AD-hitelesítésre](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Az Azure Backup-ügynök frissítése](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-ügyfelek

A SharePoint 2013, 2016 és a SharePoint Online-ügyfelek már régóta használják az ACS-t hitelesítési célokra felhőben, a helyszínen és a hibrid forgatókönyvekben. Egyes SharePoint-funkciókat és használati eseteket az ACS-megszüntetés, míg másokat nem. Az alábbi táblázat összefoglalja az ACS-t használó legnépszerűbb SharePoint-szolgáltatások áttelepítési útmutatását:

| Szolgáltatás | Útmutatás |
| ------- | -------- |
| Felhasználók hitelesítése az Azure AD-ből | Korábban az Azure AD nem támogatta a SharePoint által a hitelesítéshez szükséges SAML 1.1 tokeneket, és az ACS-t közvetítőként használták, amely a SharePointot kompatibilissé tette az Azure AD tokenformátumokkal. Mostantól [közvetlenül csatlakoztathatja a SharePointot az Azure AD-hez a helyszíni Azure AD App Gallery SharePoint használatával.](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial) |
| [Az alkalmazáshitelesítés & kiszolgálóés kiszolgáló között a helyszíni SharePointban](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Nem érinti az ACS nyugdíjba vonulás; nincs szükség változtatásra. | 
| [Alacsony megbízhatósági jogosultság a SharePoint-bővítményekhez (üzemeltetett szolgáltató és SharePoint üzemeltetett)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Nem érinti az ACS nyugdíjba vonulás; nincs szükség változtatásra. |
| [SharePoint-felhőhibrid keresés](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Nem érinti az ACS nyugdíjba vonulás; nincs szükség változtatásra. |

### <a name="web-applications-that-use-passive-authentication"></a>Passzív hitelesítést használó webalkalmazások

A hozzáférés-vezérlést felhasználói hitelesítéshez használó webalkalmazások esetében a hozzáférés-vezérlés a következő szolgáltatásokat és képességeket biztosítja a webalkalmazás-fejlesztők és -tervezők számára:

- Mély integráció a Windows Identity Foundation (WIF) rendszerrel.
- Összevonás Google-, Facebook-, Yahoo-, Azure Active Directory- és AD FS-fiókokkal és Microsoft-fiókokkal.
- A következő hitelesítési protokollok támogatása: OAuth 2.0 Draft 13, WS-Trust és Web Services Federation (WS-Federation).
- A következő tokenformátumok támogatása: JSON Web Token (JWT), SAML 1.1, SAML 2.0 és Simple Web Token (SWT).
- A WIF-be integrált otthoni birodalom felderítési élménye, amely lehetővé teszi a felhasználók számára, hogy kiválasszák a bejelentkezéshez használt fiók típusát. Ezt a felhasználói élményt a webalkalmazás üzemelteti, és teljes mértékben testreszabható.
- Jogkivonat-átalakítás, amely lehetővé teszi a webalkalmazás által a hozzáférés-vezérlésből kapott jogcímek gazdag testreszabását, beleértve a következőket:
    - Az identitásszolgáltatóktól származó jogcímek áthárítása.
    - További egyéni jogcímek hozzáadása.
    - Egyszerű if-then logika, hogy bizonyos feltételek mellett jogcímeket adjon ki.

Sajnos nincs olyan szolgáltatás, amely mindezeket az egyenértékű képességeket kínálja. Ki kell értékelnie, hogy mely hozzáférés-vezérlési képességekre van szüksége, majd válasszon az [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), az Azure Active Directory [B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) vagy egy másik felhőalapú hitelesítési szolgáltatás használata között.

#### <a name="migrate-to-azure-active-directory"></a>Áttelepítés az Azure Active Directoryba

Egy megfontolandó útvonal az alkalmazások és szolgáltatások közvetlen integrálása az Azure AD-vel. Az Azure AD a Microsoft munkahelyi vagy iskolai fiókok felhőalapú identitásszolgáltatója. Az Azure AD az Office 365, az Azure és még sok más identitásszolgáltatója. Hasonló összevont hitelesítési lehetőségeket biztosít a hozzáférés-vezérléshez, de nem támogatja az összes hozzáférés-vezérlési szolgáltatást. 

Az elsődleges példa a közösségi identitásszolgáltatókkal, például a Facebookkal, a Google-lal és a Yahoo-val való összevonás. Ha a felhasználók ilyen típusú hitelesítő adatokkal jelentkeznek be, az Azure AD nem a megoldás az Ön számára. 

Az Azure AD nem feltétlenül támogatja pontosan ugyanazokat a hitelesítési protokollokat, mint a hozzáférés-vezérlés. Például bár mind a hozzáférés-vezérlés és az Azure AD támogatja az OAuth, vannak finom különbségek az egyes megvalósítások között. A különböző implementációk megkövetelik a kód módosítását az áttelepítés részeként.

Az Azure AD azonban számos potenciális előnyt biztosít a hozzáférés-vezérlési ügyfelek számára. Natív módon támogatja a microsoftos munkahelyi vagy iskolai fiókokat a felhőben, amelyeket gyakran használnak az Access Control ügyfelei. 

Az Azure AD-bérlők is összeegyíthető egy vagy több példánya a helyszíni Active Directory az AD FS-en keresztül. Így az alkalmazás hitelesítheti a felhőalapú felhasználókat és a helyszíni üzemeltetett felhasználókat. Támogatja a WS-Federation protokollt is, amely viszonylag egyszerűvé teszi a webalkalmazással való integrálását a WIF használatával.

Az alábbi táblázat összehasonlítja a hozzáférés-vezérlés, amelyek relevánsak a webes alkalmazások azokkal a funkciókkal, amelyek az Azure AD-ben érhetők el. 

Magas szinten *az Azure Active Directory valószínűleg a legjobb választás az áttelepítéshez, ha csak a Microsoft munkahelyi vagy iskolai fiókjukkal jelentkezik be*a felhasználók.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD támogatása |
| ---------- | ----------- | ---------------- |
| **A fiókok típusai** | | |
| Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Támogatott |
| Windows Server Active Directory és AD FS fiókok |- Az Azure AD-bérlővel való összevonáson keresztül támogatott <br />- Közvetlen összevonással támogatott az AD FS-szel | Csak az Azure AD-bérlővel való összevonáson keresztül támogatott | 
| Más vállalati identitáskezelő rendszerekből származó fiókok |- Lehetséges összevonásegy Azure AD-bérlővel <br />- Közvetlen összevonással támogatott | Azure AD-bérlővel való összevonással lehetséges |
| Microsoft-fiókok személyes használatra | Támogatott | Az Azure AD v2.0 OAuth protokollon keresztül támogatott, de más protokollokon keresztül nem | 
| Facebook, Google, Yahoo fiókok | Támogatott | Nem támogatott egyáltalán |
| **Protokollok és SDK-kompatibilitás** | | |
| WIF | Támogatott | Támogatott, de korlátozott utasítások állnak rendelkezésre |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | A tervezet támogatása 13 | Az RFC 6749 támogatása, a legmodernebb specifikáció |
| WS-megbízhatóság | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| Jwt | Béta verzióban támogatott | Támogatott |
| SAML 1.1 | Támogatott | Előzetes verzió |
| SAML 2.0 | Támogatott | Támogatott |
| Swt | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testreszabható otthoni birodalom felderítési/fiókkitárolási felhasználói felület | Letölthető kód, amely beépíthető az alkalmazásokba | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| Jogcímek testreszabása jogkivonatokban |- A személyazonosság-szolgáltatók tól származó bemeneti jogcímek áthaladása<br />- Hozzáférési jogkivonat beszerzése az identitásszolgáltatótól jogcímként<br />- Kimeneti jogcímek kibocsátása az inputjogcímek értékei alapján<br />- Kimeneti jogcímek kiállítása állandó értékekkel |- Nem lehet átmenni az összevont identitásszolgáltatók tól származó jogcímeken<br />- Nem lehet hozzáférési jogkivonatot beszerezni az identitásszolgáltatótól jogcímként<br />- Nem lehet kimeneti jogcímeket kiadni a bemeneti jogcímek értékei alapján<br />- Állandó értékekkel rendelkező kimeneti jogcímeket bocsáthat ki<br />- Az Azure AD-vel szinkronizált felhasználók tulajdonságai alapján kimeneti jogcímeket adhat ki |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Hozzáférés-vezérlés-kezelési szolgáltatással támogatott | A Microsoft Graph API-val támogatott |

Ha úgy dönt, hogy az Azure AD a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások, tisztában kell lennie az alkalmazás azure AD-vel való integrálásának két módja.

A WS-Federation vagy a WIF használatával integrálható az Azure AD, javasoljuk, hogy kövesse a megközelítés leírt [konfigurálása összevont egyszeri bejelentkezés egy nem katalógus alkalmazás.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) A cikk az Azure AD saml-alapú egyszeri bejelentkezés konfigurálására hivatkozik, de a WS-Federation konfigurálása is működik. Ezt a megközelítést követve egy Azure AD Premium-licenc szükséges. Ennek a megközelítésnek két előnye van:

- Az Azure AD-token ek testreszabásának teljes rugalmasságát kapja. Testreszabhatja az Azure AD által kiadott jogcímeket, hogy megfeleljenek a hozzáférés-vezérlés által kiadott jogcímeknek. Ez különösen magában foglalja a felhasználói azonosító vagy névazonosító jogcímet. Ha továbbra is konzisztens felhasználói azonosítókat szeretne kapni a felhasználók számára a technológiák módosítása után, győződjön meg arról, hogy az Azure AD által kiadott felhasználói azonosítók megegyeznek a hozzáférés-vezérlés által kiadott azonosítókkal.
- Konfigurálhatja az alkalmazásra jellemző jogkivonat-aláíró tanúsítványt, amely egy élettartammal, amelyet ön szabályoz.

> [!NOTE]
> Ehhez a megközelítéshez Egy Azure AD Premium-licenc szükséges. Ha Ön hozzáférés-vezérlési ügyfél, és prémium licencre van szüksége az egybejelentkezéses bejelentkezés hez, lépjen kapcsolatba velünk. Örömmel biztosítunk önnek fejlesztői licenceket.

Egy másik megközelítés, hogy kövesse [ezt a kódmintát](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), amely kissé eltérő utasításokat ad a WS-Federation beállításához. Ez a kódminta nem a WIF-et használja, hanem a 4,5 OWIN ASP.NET. Az alkalmazásregisztrációra vonatkozó utasítások azonban a WIF-et használó alkalmazásokra érvényesek, és nem igényelnek Azure AD Premium-licencet. 

Ha ezt a módszert választja, meg kell értenie az [aláírási kulcs váltását az Azure AD-ben.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) Ez a megközelítés az Azure AD globális aláíró kulcs jogkivonatok kiadásához. Alapértelmezés szerint a WIF nem frissíti automatikusan az aláíró kulcsokat. Amikor az Azure AD elforgatja a globális aláíró kulcsokat, a WIF-implementációnak készen kell állnia a módosítások elfogadására. További információ: [Fontos információ az azure-beli kulcsváltás aláírásáról az Azure AD-ben.](https://msdn.microsoft.com/library/azure/dn641920.aspx)

Ha az OpenID Connect vagy az OAuth protokollokon keresztül integrálható az Azure AD-vel, javasoljuk, hogy tegye ezt. Az [Azure AD fejlesztői útmutatójában](https://aka.ms/aaddev)elérhető webes alkalmazásba való integrálásáról részletes dokumentációt és útmutatást nyújtunk.

#### <a name="migrate-to-azure-active-directory-b2c"></a>Áttelepítés az Azure Active Directory B2C könyvtárba

A másik áttelepítési útvonalat, hogy fontolja meg az Azure AD B2C. Az Azure AD B2C egy felhőalapú hitelesítési szolgáltatás, amely a hozzáférés-vezérléshez hasonlóan lehetővé teszi a fejlesztők számára, hogy kiszervezzék a hitelesítési és identitáskezelési logikájukat egy felhőszolgáltatásba. Ez egy fizetős szolgáltatás (ingyenes és prémium szintekkel), amelyet olyan fogyasztói alkalmazásokhoz terveztek, amelyek akár több millió aktív felhasználóval is rendelkezhetnek.

A hozzáférés-vezérléshez hasonlóan az Azure AD B2C egyik legvonzóbb funkciója, hogy számos különböző típusú fiókot támogat. Az Azure AD B2C segítségével microsoftos vagy Facebook-, Google-, LinkedIn-, GitHub- vagy Yahoo-fiókjukkal és egyebek használatával jelentkezhet be a felhasználókhoz. Az Azure AD B2C támogatja a "helyi fiókokat", illetve a felhasználónevet és a jelszavakat, amelyeket a felhasználók kifejezetten az alkalmazáshoz hoznak létre. Az Azure AD B2C is nyújt gazdag bővíthetőséget, amely segítségével testre szabhatja a bejelentkezési folyamatok. 

Azonban az Azure AD B2C nem támogatja a hitelesítési protokollok és jogkivonat-formátumok, amelyek hozzáférés-vezérlés i ügyfelek szükség lehet. Emellett az Azure AD B2C használatával jogkivonatokat is lekaphat, és további információkat kaphat a felhasználóról az identitásszolgáltatótól, a Microsofttól vagy más módon.

Az alábbi táblázat összehasonlítja a webes alkalmazások hoz tartozó hozzáférés-vezérlési funkciókat az Azure AD B2C-ben elérhető funkciókkal. Magas szinten az *Azure AD B2C valószínűleg a megfelelő választás a migrálás, ha az alkalmazás a fogyasztó előtt álló, vagy ha támogatja a különböző típusú fiókok.*

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD B2C támogatása |
| ---------- | ----------- | ---------------- |
| **A fiókok típusai** | | |
| Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Egyéni házirendek által támogatott  |
| Windows Server Active Directory és AD FS fiókok | Közvetlen összevonással támogatott az AD FS-sel | SAML-összevonással támogatja egyéni házirendek használatával |
| Más vállalati identitáskezelő rendszerekből származó fiókok | Közvetlen összevonással támogatott a WS-Federation-en keresztül | SAML-összevonással támogatja egyéni házirendek használatával |
| Microsoft-fiókok személyes használatra | Támogatott | Támogatott | 
| Facebook, Google, Yahoo fiókok | Támogatott | A Facebook és a Google natív módon támogatta a Yahoo-t az OpenID Connect összevonáson keresztül egyéni irányelvek használatával |
| **Protokollok és SDK-kompatibilitás** | | |
| Windows Identity Foundation (WIF) | Támogatott | Nem támogatott |
| WS-Federation | Támogatott | Nem támogatott |
| OAuth 2.0 | A tervezet támogatása 13 | Az RFC 6749 támogatása, a legmodernebb specifikáció |
| WS-megbízhatóság | Támogatott | Nem támogatott |
| **Token formátumok** | | |
| Jwt | Béta verzióban támogatott | Támogatott |
| SAML 1.1 | Támogatott | Nem támogatott |
| SAML 2.0 | Támogatott | Nem támogatott |
| Swt | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testreszabható otthoni birodalom felderítési/fiókkitárolási felhasználói felület | Letölthető kód, amely beépíthető az alkalmazásokba | Teljesen testreszabható felhasználói felület egyéni CSS-en keresztül |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláíró kulcsok, nem tanúsítványok, egyéni házirendeken keresztül támogatva |
| Jogcímek testreszabása jogkivonatokban |- A személyazonosság-szolgáltatók tól származó bemeneti jogcímek áthaladása<br />- Hozzáférési jogkivonat beszerzése az identitásszolgáltatótól jogcímként<br />- Kimeneti jogcímek kibocsátása az inputjogcímek értékei alapján<br />- Kimeneti jogcímek kiállítása állandó értékekkel |- átadhatja a személyazonosság-szolgáltatóktól származó követeléseket; egyes jogcímekhez egyéni házirendek szükségesek<br />- Nem lehet hozzáférési jogkivonatot beszerezni az identitásszolgáltatótól jogcímként<br />- A kimeneti jogcímek az egyéni házirendeken keresztüli bemeneti jogcímek értékei alapján bocsáthatók ki<br />- A kimeneti jogcímek állandó értékekkel az egyéni házirendeken keresztül |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Hozzáférés-vezérlés-kezelési szolgáltatással támogatott |- A Microsoft Graph API használatával engedélyezett felhasználók létrehozása<br />- Nem lehet b2C bérlőket, alkalmazásokat vagy házirendeket létrehozni programozott módon |

Ha úgy dönt, hogy az Azure AD B2C a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások számára, kezdje a következő erőforrásokkal:

- [Az Azure AD B2C dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Az Azure AD B2C egyéni szabályzatai](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Az Azure AD B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés pingelési identitásra vagy hitelesítési azonosítóra

Bizonyos esetekben előfordulhat, hogy az Azure AD és az Azure AD B2C nem elegendőek a hozzáférés-vezérlés lecseréléséhez a webalkalmazásokban anélkül, hogy jelentős kódmódosításokat végezne. Néhány gyakori példa:

- A WIF vagy a WS-Federation szolgáltatást használó webes alkalmazások a közösségi identitásszolgáltatókkal, például a Google-lal vagy a Facebookkal való bejelentkezéshez.
- Olyan webalkalmazások, amelyek a WS-Federation protokollon keresztül közvetlen összevonást hajtanak végre egy vállalati identitásszolgáltatóval.
- Olyan webes alkalmazások, amelyek hez a közösségi identitásszolgáltató (például a Google vagy a Facebook) által kibocsátott hozzáférési jogkivonatot igénylik jogcímként a hozzáférés-vezérlés által kibocsátott jogkivonatokban.
- Olyan összetett jogkivonat-átalakítási szabályokkal rendelkező webalkalmazások, amelyeket az Azure AD vagy az Azure AD B2C nem tud reprodukálni.
- Több-bérlős webalkalmazások, amelyek az ACS segítségével központilag kezelik az összevonást számos különböző identitásszolgáltatóhoz

Ezekben az esetekben érdemes lehet a webalkalmazás áttelepítése egy másik felhőalapú hitelesítési szolgáltatásba. Javasoljuk, hogy vizsgálja meg az alábbi lehetőségeket. Az alábbi lehetőségek mindegyike a hozzáférés-vezérléshez hasonló lehetőségeket kínál:

|     |     |
| --- | --- |
| ![Ezen a képen az Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png) | [Az Auth0](https://auth0.com/acs) egy rugalmas felhőalapú identitáskezelési szolgáltatás, amely magas szintű áttelepítési útmutatást hozott létre [a hozzáférés-vezérlés ügyfelei számára,](https://auth0.com/acs)és az ACS szinte minden funkcióját támogatja. |
| ![Ezen a képen a Ping Identity embléma látható](./media/active-directory-acs-migration/rsz-ping.png) | [A Ping Identity](https://www.pingidentity.com) az ACS-hez hasonló két megoldást kínál. A PingOne egy felhőalapú identitásszolgáltatás, amely számos olyan szolgáltatást támogat, mint az ACS, és a PingFederate hasonló a helyszíni identitástermék, amely nagyobb rugalmasságot biztosít. A [pingi ACS-megszüntetési útmutatóban](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) további részleteket talál ezekről a termékekről. |

A Ping Identity és az Auth0 szolgáltatással való együttműködés célja annak biztosítása, hogy minden hozzáférés-vezérlési ügyfél rendelkezik az alkalmazások és szolgáltatások áttelepítési útvonalával, amely minimálisra csökkenti a hozzáférés-vezérlésről való áttéréshez szükséges munka mennyiségét.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Aktív hitelesítést használó webszolgáltatások

A hozzáférés-vezérlés által kibocsátott jogkivonatokkal védett webszolgáltatások esetében a hozzáférés-vezérlés a következő szolgáltatásokat és képességeket kínálja:

- Egy vagy több *szolgáltatásidentitás regisztrálása* a hozzáférés-vezérlési névtérben. A szolgáltatásidentitások jogkivonatok igénylésére használhatók.
- Az OAuth WRAP és az OAuth 2.0 Draft 13 protokollok támogatása jogkivonatok igényléséhez a következő típusú hitelesítő adatok használatával:
    - Egyszerű jelszó, amely a szolgáltatásidentitáshoz jön létre
    - Aláírt SWT szimmetrikus kulccsal vagy X509 tanúsítvánnyal
    - Megbízható identitásszolgáltató (általában AD FS-példány) által kibocsátott SAML-jogkivonat
- A következő tokenformátumok támogatása: JWT, SAML 1.1, SAML 2.0 és SWT.
- Egyszerű tokenátalakítási szabályok.

A hozzáférés-vezérlés szolgáltatásidentitásait általában a kiszolgálók és a kiszolgálók hitelesítésének megvalósítására használják. 

#### <a name="migrate-to-azure-active-directory"></a>Áttelepítés az Azure Active Directoryba

Az ilyen típusú hitelesítési folyamatra vonatkozó javaslatunk az [Azure Active Directoryba](https://azure.microsoft.com/develop/identity/signin/)való áttelepítés. Az Azure AD a Microsoft munkahelyi vagy iskolai fiókok felhőalapú identitásszolgáltatója. Az Azure AD az Office 365, az Azure és még sok más identitásszolgáltatója. 

Az Azure AD-t is használhatja a kiszolgálók és a kiszolgálók között történő hitelesítéshez az OAuth-ügyfél hitelesítő adatok megadásának Azure AD-implementációjával. Az alábbi táblázat összehasonlítja a hozzáférés-vezérlés képességeit a kiszolgálók közötti hitelesítésben az Azure AD-ben elérhetőkkel.

| Képesség | Hozzáférés-vezérlés támogatása | Az Azure AD támogatása |
| ---------- | ----------- | ---------------- |
| Webszolgáltatás regisztrálása | Függő entitás létrehozása a hozzáférés-vezérlési portálon | Hozzon létre egy Azure AD-webalkalmazást az Azure Portalon |
| Ügyfél regisztrálása | Szolgáltatásidentitás létrehozása a hozzáférés-vezérlési portálon | Hozzon létre egy másik Azure AD-webalkalmazást az Azure Portalon |
| Használt protokoll |- OAuth WRAP protokoll<br />- OAuth 2.0 Tervezet 13 ügyfél hitelesítő adatok támogatás | OAuth 2.0-ügyfél hitelesítő adatainak megadása |
| Ügyfélhitelesítési módszerek |- Egyszerű jelszó<br />- Aláírt SWT<br />- SAML token összevont identitásszolgáltatótól |- Egyszerű jelszó<br />- Aláírt JWT |
| Token formátumok |- JWT<br />- SAML 1.1<br />- SAML 2,0<br />- SWT<br /> | Csak JWT |
| Token átalakítása |- Egyéni jogcímek hozzáadása<br />- Egyszerű if-then jogcímkiállítási logika | Egyéni jogcímek hozzáadása | 
| Konfigurációs és felügyeleti feladatok automatizálása | Hozzáférés-vezérlés-kezelési szolgáltatással támogatott | A Microsoft Graph API-val támogatott |

A kiszolgálók és kiszolgálók között történő forgatókönyvek megvalósításával kapcsolatos útmutatást az alábbi forrásokban talál:

- Az [Azure AD fejlesztői](https://aka.ms/aaddev) útmutatószolgáltatás-szolgáltatás szakasza
- [Démonkódminta egyszerű jelszóügyfél-hitelesítő adatok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Démonkódminta a tanúsítványügyfél hitelesítő adatainak használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés pingelési identitásra vagy hitelesítési azonosítóra

Bizonyos esetekben előfordulhat, hogy az Azure AD-ügyfél hitelesítő adatai és az OAuth-támogatás megvalósítása nem elegendőek a hozzáférés-vezérlés lecseréléséhez az architektúrában jelentős kódmódosítások nélkül. Néhány gyakori példa:

- Kiszolgáló-kiszolgáló hitelesítés jwt-któl eltérő tokenformátumok használatával.
- Kiszolgáló-kiszolgáló hitelesítés egy külső identitásszolgáltató által biztosított bemeneti jogkivonat használatával.
- Kiszolgáló-kiszolgáló hitelesítés token átalakítási szabályokkal, amelyeket az Azure AD nem tud reprodukálni.

Ezekben az esetekben érdemes lehet a webalkalmazás áttelepítése egy másik felhőalapú hitelesítési szolgáltatásba. Javasoljuk, hogy vizsgálja meg az alábbi lehetőségeket. Az alábbi lehetőségek mindegyike a hozzáférés-vezérléshez hasonló lehetőségeket kínál:

|     |     |
| --- | --- |
| ![Ezen a képen az Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png) | [Az Auth0](https://auth0.com/acs) egy rugalmas felhőalapú identitáskezelési szolgáltatás, amely magas szintű áttelepítési útmutatást hozott létre [a hozzáférés-vezérlés ügyfelei számára,](https://auth0.com/acs)és az ACS szinte minden funkcióját támogatja. |
| ![Ezen a képen a Ping Identity embléma látható](./media/active-directory-acs-migration/rsz-ping.png) | [A Ping Identity](https://www.pingidentity.com) az ACS-hez hasonló két megoldást kínál. A PingOne egy felhőalapú identitásszolgáltatás, amely számos olyan szolgáltatást támogat, mint az ACS, és a PingFederate hasonló a helyszíni identitástermék, amely nagyobb rugalmasságot biztosít. A [pingi ACS-megszüntetési útmutatóban](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) további részleteket talál ezekről a termékekről. |

A Ping Identity és az Auth0 szolgáltatással való együttműködés célja annak biztosítása, hogy minden hozzáférés-vezérlési ügyfél rendelkezik az alkalmazások és szolgáltatások áttelepítési útvonalával, amely minimálisra csökkenti a hozzáférés-vezérlésről való áttéréshez szükséges munka mennyiségét.

#### <a name="passthrough-authentication"></a>Áthaladási hitelesítés

Az tetszőleges jogkivonat-átalakítással végzett áthárítási hitelesítéshez nincs egyenértékű Microsoft-technológia az ACS-vel. Ha az ügyfeleknek erre van szükségük, az Auth0 lehet az, aki a legközelebbi közelítési megoldást nyújtja.

## <a name="questions-concerns-and-feedback"></a>Kérdések, aggodalmak és visszajelzések

Tisztában vagyunk azzal, hogy sok hozzáférés-vezérlési ügyfél nem talál egyértelmű áttelepítési útvonalat a cikk elolvasása után. A megfelelő terv meghatározásához segítségre vagy útmutatásra lehet szüksége. Ha szeretné megvitatni a migrációs forgatókönyveket és kérdéseket, kérjük, hagyjon egy megjegyzést ezen az oldalon.
