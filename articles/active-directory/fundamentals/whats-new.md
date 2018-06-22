---
title: Újdonságok Az Azure AD kibocsátási megjegyzései |} Microsoft Docs
description: Ismerje meg, milyen újdonságokat Azure Active Directory (Azure AD), például a legújabb kibocsátási megjegyzései, az ismert problémák, hibajavításokat tartalmaz, az elavult funkciók és a jövőbeni változtatásokról.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: f5c2f8901db5fbeacbd9cc79a03c6dad63842ac9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268156"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

> Naprakész legyen what's new in Azure Active Directory (Azure AD) előfizet a [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).

Az Azure AD fogadja folyamatos fejlesztéseket. Maradjon naprakész, és a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

-   A legfrissebb verziókban
-   Ismert problémák
-   Hibajavítások
-   Elavult funkciók
-   Módosítások tervek

Ezen a lapon a havi frissül, ezért le újra rendszeresen.

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Értesítés módosítása: biztonsági azonnal kijavíthatja a hibákat az alkalmazások az Azure AD tevékenység naplók API delegált engedélyezési folyamata

**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** figyelés és jelentéskészítés  
**A termék képesség:** jelentéskészítés

Az erősebb biztonsági kényszerítési miatt volt módosítani az engedélyeket az eléréséhez a delegált engedélyezési folyamat használó alkalmazások [az Azure AD tevékenység naplók API-k](https://aka.ms/aadreportsapi). Ez a változás történik által **2018. június 26**.

Ha bármelyik, az alkalmazások az Azure AD tevékenység API-k, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás nem lesz új után a változás történik.

**Az alkalmazás engedélyek frissítése**

1.  Jelentkezzen be az Azure portálra.

2.  Válassza a bal oldali navigációs sávja **Azure Active Directory**, majd válassza ki **App regisztrációk**.

3.  Válassza ki az alkalmazást, amely az Azure AD tevékenység naplók API-t használja, válasszon **beállítások**, majd a **beállítások** panelen válassza **szükséges engedélyek**.

4.  Az a **szükséges engedélyek** panelen válassza a **Windows Azure Active Directory** API.

5.  A a **delegált engedélyekkel** területén a **hozzáférés engedélyezése** panelen, jelölje be a jelölőnégyzetet a **olvasási directory** adatokat, és válassza **mentése**.

    Fog megjelenni a **szükséges engedélyek** panelen.

    ![Delegált engedélyek olvasási directory lehetőséget](./media/whats-new/app-registration-delegate-read-directory.png)

6.  Válassza ki **engedélyeket**, majd válassza ki **Igen**.
    
    >[!Note]
    >Engedélyezze, hogy az alkalmazás a globális rendszergazdának kell lennie.

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>ExpressRoute támogatási módosításait

**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** Platform  

Az ajánlat, például az Azure Active Directory (Azure AD) úgy tervezték, hogy közvetlenül az interneten keresztül anélkül, hogy az expressroute-on vagy bármely más személyes VPN-alagutat címen működhet a legjobban szolgáltatott szoftver. Emiatt a **2018 augusztus 1**, ExpressRoute támogató használata az Azure nyilvános társviszony az Azure AD-szolgáltatások és a Microsoft társviszony-létesítés Azure Közösségek rendszer leállítása. Ez a változás által érintett szolgáltatások észrevette, hogy Azure AD-forgalom fokozatosan körűen ExpressRoute az internethez.

Amíg a Microsoft megváltoztatja a támogatási is tudjuk, hogy vannak továbbra is olyan helyzetekben, ahol szükség lehet használata a dedikált kapcsolatok az hitelesítési forgalom. Emiatt az Azure AD bérlő IP-címtartomány korlátozások használatával ExpressRoute szolgáltatások már a Microsoft társviszony-létesítés a "Más Office 365 Online szolgáltatások" közösségi támogatásához továbbra is. Ha a szolgáltatások érintettek, de ExpressRoute van szüksége, tegye a következőket:

- **Ha a számítógép az Azure nyilvános társviszony-létesítés.** Helyezze át a Microsoft társviszony-létesítés és regisztráljon kell a **más Office 365 Online szolgáltatásokhoz (12076:5100)** közösségi. Az Azure nyilvános társviszony-létesítést úgy Microsoft társviszony-létesítés mozgatása kapcsolatos további információkért tekintse meg a [áthelyezni egy nyilvános társviszony-létesítést úgy Microsoft társviszony-létesítés](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) cikk.

- **Ha a számítógép Microsoft társviszony-létesítés.** Be kell jelentkeznie a a **más Office 365 Online szolgáltatás (12076:5100)** közösségi. Útválasztási követelményeivel kapcsolatos további információkért tekintse meg a [BGP Közösségek szakasz támogatása](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) ExpressRoute útválasztási követelmények cikk.

Ha továbbra is használja a dedikált Kapcsolatcsoportok, szüksége lesz felvegye a Microsoft Account team használandó engedélyezési tudhat a **más Office 365 Online szolgáltatás (12076:5100)** közösségi. A MS Office által felügyelt felülvizsgáló Bizottság ellenőrzi, hogy e ezek a kapcsolatok szükségesek, és így megértheti a műszaki megvalósítását, hogy azokat. Az Office 365 szolgáltatáshoz nevű útvonal szűrők létrehozására tett kísérlet nem engedélyezett előfizetések egy hibaüzenetet fog kapni. 
 
---


### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API-k a használati feltételekkel kapcsolatos felügyeleti műveletek végrehajtásához

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** fejlesztők számára
 

 Az Azure AD-használati felügyeleti működéséhez jelentek meg Microsoft Graph API. Tudunk létrehozása, frissítése és a használati és-objektum törlése.

---
 


### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD-beli többérlős végpont identitásszolgáltatóként történő hozzáadása az Azure AD B2C-ben

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2C - felhasználói Identitáskezelés  
**A termék képesség:** B2B/B2C
 

Egyéni házirendekkel, most adhat az Azure AD közös végpont az Azure AD B2C identitás-szolgáltatóként. Ez lehetővé teszi, hogy egyetlen ponton vonatkozó összes az Azure AD-felhasználó jelentkezik be az alkalmazásokat. További információkért lásd: [Azure Active Directory B2C: engedélyezése a felhasználók számára, hogy jelentkezzen be egy egyéni házirendekkel több-bérlős az Azure AD-identitásszolgáltató](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

 

---


### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címek használata az alkalmazások tetszőleges helyről való eléréséhez a My Apps Sign-in bővítmény és az Azure AD Application Proxy segítségével

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

Most alkalmazások keresztül elérhető belső URL-címeket, még ha a vállalati hálózaton kívül a saját alkalmazások biztonságos bejelentkezési kiterjesztés az Azure AD használatával. Ez az Azure AD-alkalmazásproxy, bármilyen böngészőből is vannak telepítve a hozzáférési Panel bővítmény használatával közzétett bármilyen alkalmazással fog működni. Az URL-cím átirányítási funkció automatikusan engedélyezve lesz, ha egy felhasználó bejelentkezik a bővítményt az. A bővítmény letölthető a [peremhálózati](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), és [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).
 

---
 


### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>A régi hitelesítések megtekintése a bejelentkezések tevékenységnaplói révén

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** figyelés és jelentéskészítés
 

A Bevezetés egy mező neve **ügyfélalkalmazás** ezen a bejelentkezési tevékenységi naplóit, az ügyfelek most látható örökölt hitelesítések használó felhasználók. Ügyfelek is a bejelentkezések MS Graph API segítségével az adatok eléréséhez, vagy a bejelentkezés révén tevékenység naplózza az Azure AD portálon, ahol ugyanúgy használhatók a **ügyfélalkalmazás** örökölt hitelesítések szűréshez vezérlő. Tekintse meg a további részletek dokumentációjában.
 

---
 

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Európai ügyfeleink Azure Active Directory-beli adatainak Európán belüli tárolása

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** más  
**A termék képesség:** GoLocal
 

Ügyfelek Európában megköveteli az adatok Európában maradnak, és értekezlet adatvédelmi és Európai törvényi Európai adatközpontok kívül nem replikálja. Ez [cikk](https://go.microsoft.com/fwlink/?linkid=872328) milyen azonosító információkat a vonatkozó részletes Európa tárolja, és is biztosít a részletes adatokat tárolt külső Európai adatközpontok biztosít. 

 

---
 

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>2018. május – új felhasználókiépítési funkció az SaaS-alkalmazás-integrációkban

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App kiépítése  
**A termék képesség:** 3. fél integrációja
 

Azure AD lehetővé teszi, hogy automatizálja a létrehozását, a karbantartási és a felhasználói identitások SaaS-alkalmazásokhoz, például a Dropbox, Salesforce, a ServiceNow és egyéb eltávolítása. Előfordulhat, hogy 2018, a felhasználók átadásához, a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása jelentek meg:

- [BlueJeans](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Legfontosabb feladatai közé tartoznak kötegmérete](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Az összes olyan alkalmazás, amely támogatja a felhasználók átadása az Azure AD-katalógus listájáért lásd: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

 

---
 

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok és alkalmazások hozzáférésének Azure AD-beli felülvizsgálata már ismétlődő felülvizsgálatként is elérhető

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hozzáférés értékelést  
**A termék képesség:** irányítás
 

Hozzáférés ellenőrzi, hogy a csoportok és alkalmazások mostantól általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák tudják konfigurálása a hozzáférés értékelést csoporttagságot és alkalmazás-hozzárendelései például havonta vagy negyedévente rendszeres időközönként automatikusan megismétlődik.
 

---
 

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure AD-beli tevékenységnaplók (bejelentkezések és auditnaplók) már az MS Graph-ban is elérhetők

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** figyelés és jelentéskészítés
 

Azure Active Directory naplók, amely magában foglalja a bejelentkezéseket és a naplókat, most az MS Graph keresztül érhetők el. MS Graph ezek a naplók eléréséhez keresztül 2 végpontok azt van kitéve. Vegye ki a [dokumentumok](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) a programozott hozzáférést az Azure AD Reporting API-khoz a kezdéshez. 
 

---
 



### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>A B2B beváltási környezetének továbbfejlesztett funkciói és a cégfiók megszüntetése

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2B  
**A termék képesség:** B2B/B2C
 

**Csak az idő érvényesítési:** után erőforrás megosztása a Vendég felhasználó B2B API használatával – nem kell egy különös meghívó e-mailt küldeni. A legtöbb esetben a Vendég felhasználó egyszerűen hozzáférhet az erőforrás, és megnyílik a JIT érvényesítési kezelőfelület segítségével. Nincs több hatással miatt kimaradt e-maileket. Nincs több kérni a vendégfelhasználók "Adta meg érvényesítési hivatkozásra kattintva a rendszer küldött?". Ez azt jelenti, miután SPO használja a meghívó manager – zavaros mellékletek rendelkezhet minden felhasználó – belső és külső – bármely érvényesítési szerinti azonos kanonikus URL-CÍMÉT.

**Modern érvényesítési élmény:** nincs több vágási képernyő érvényesítési kezdőlapján. A felhasználók látni fogják a modern hozzájárulás kipróbálhatja a hívja fel szervezete adatvédelmi nyilatkozatát, ugyanúgy, mint 3. fél alkalmazások tehetik.


**Vendégfelhasználók is hagyhatja a szervezeti:** után a felhasználó egy szervezeti multiplicitású keresztül, akkor is önkiszolgáló kilépne a szervezetből. Nincs több hívja meg a hívja fel szervezeti admin "távolíthatók el", nincs több növelő támogatási jegyeket.
 

---
 


### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>2018 májusától új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** 3. fél integrációja
 

Előfordulhat, hogy 2018 jelentek meg a gyűjtemény összevonással a következő 18 új alkalmazások támogatják:

 

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), [Infogix Data3Sixty Govern](), [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

 

Is találhat az alkalmazások a [Itt](https://aka.ms/appstutorial).

Az alkalmazást az Azure AD-alkalmazásgyűjtemény listázása, lásd: [alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing). 

 

---
 

### <a name="deployment-plans---guidance-to-get-you-started-with-azure-active-directory"></a>Üzembe helyezési tervek – útmutatás az Azure Active Directory használatának megkezdéséhez

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** más  
**A termék képesség:** könyvtár
 

A részletes útmutatást (SSPR, SSO, CA, alkalmazás Proxy, a felhasználók átadása, az AD FS az ESP vagy PHS-ben az AD FS?) telepítése keresi. Központi telepítési csomagok segítségével van!

Ugrás a [Aka.ms/DeploymentPlans](http://Aka.ms/DeploymentPlans) ahonnan teljes körű útmutatót start érték lekérése a szolgáltatásokat.



- [Hogyan szerezhető be a központi telepítési csomagok](http://Aka.ms/DeploymentPlans)? 

- Ha visszajelzést [Itt](http://aka.ms/DeploymentPlanFeedback)

- Kérdései vannak? Érheti el: [IDGitDeploy@microsoft.com](mailto:IDGitDeploy@microsoft.com)



     

 

---
 

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése – további alkalmazások betöltése

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

Hiba történt az alkalmazások keresése, / service rendszerbiztonsági tagok? Vettünk képes betölteni a vállalati alkalmazások további alkalmazások minden alkalmazások listáját. Alapértelmezés szerint megmutatjuk, 20 alkalmazások. Most kattintson betöltése több megtekintéséhez további alkalmazásokat is. 
 

---
 


### <a name="public-preview-of-new-and-improved-sign-ins-user-experience-in-azure-portal"></a>Elérhető az új és továbbfejlesztett Bejelentkezések felület nyilvános előzetes verziója az Azure Portal webhelyen

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** figyelés és jelentéskészítés
 

Dolgozunk a Izgatottan várja, hogy az új és továbbfejlesztett bejelentkezési naplók felhasználói élmény a az Azure AD portálon belül bevezetése. Az új bejelentkezések felhasználói felületet, az ügyfelek most kérheti le a következő:

- Továbbfejlesztett várakozási ideje a 2 órát belül 5 perc. Tekintse meg a [késés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-latencies-azure-portal) docs további információt.

- A "Oszlop" gomb segítségével dinamikusan szűrők hozzáadásának lehetősége. Oszlopok felvétele a bejelentkezési jelentést a UX, automatikusan megtekintheti őket használata szűrőként.

- Rendezze a dátum, a felhasználónév és az alkalmazás lehetővé teszi.

- Felvétel a hagyományos hitelesítések és az örökölt hitelesítések használja az "Ügyfél alkalmazás" oszlop segítségével.

- Egy letölthető PowerShell-parancsfájlt, amely testre szabott felvétel a UX kiválasztott szűrési feltételek alapján A PowerShell parancsfájllal kaphat a lehető legtöbb sornyi adatot, ha Ön kívánt (a szűrési feltételek alapján), amely átadja a kimenetet .csv formátumban.

Ez a szolgáltatás további részletekért lásd: [bejelentkezési Tevékenységjelentések az Azure Active Directory portálon](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins).
 

---
 


### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az AAD Connect májusi kiadásának újdonságai a következők: a PingFederate-integráció nyilvános előzetes verziója, fontos biztonsági frissítések, számos hibajavítás és nagyszerű új hibaelhárítási eszközök. 

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** AD Connect  
**A termék képesség:** Identitáskezelés életciklusa
 

Az AAD Connect májusi kiadásának újdonságai a következők: a PingFederate-integráció nyilvános előzetes verziója, fontos biztonsági frissítések, számos hibajavítás és nagyszerű új hibaelhárítási eszközök. A kibocsátási megjegyzésekben található [Itt](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

 

---
 


### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-beli hozzáférési felülvizsgálat: automatikus alkalmazás

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** hozzáférés értékelést  
**A termék képesség:** irányítás
 

Hozzáférés ellenőrzi, hogy a csoportok és alkalmazások is általánosan elérhető az Azure AD Premium P2 részeként. A rendszergazda konfigurálhat a automatikusan a felülvizsgáló módosítások alkalmazásához adott csoportot vagy alkalmazást, a áttekintése befejeződik. A rendszergazda is megadhatja, mi történik a felhasználó is folyamatos hozzáférést biztosíthasson a, ha felülvizsgálók nem válaszol, megszünteti a hozzáférést, férjenek vagy rendszer ajánlásokat. 

 

---
 


### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Az új alkalmazások esetében az azonosító-jogkivonatok már nem adhatók vissza a query response_mode használatával. 

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 

4/25/2018 a következőnél létrehozott alkalmazások többé nem lesznek igényelni egy **id_token** használatával a **lekérdezés** response_mode.  Ekkor az Azure AD beágyazott OIDC előírásoknak, és segítséget nyújt az alkalmazások támadási felület csökkentése.  4/25/2018 előtt létrehozott alkalmazások nem sem használja a **lekérdezés** együtt, egy response_type response_mode **id_token**.  A hiba, amikor egy id_token kérnek AAD, **AADSTS70007: "query" érték nem támogatott a "response_mode" jogkivonatot kérésekor**.

A **töredék** és **form_post** response_modes továbbra is működik -, amikor új alkalmazásobjektumok létrehozása (pl. az alkalmazás Proxy használat), győződjön meg arról, valamint egy ezek response_modes ahhoz, hogy létre egy új az alkalmazás.  
 

---
 



## <a name="april-2018"></a>2018. április
 


### <a name="azure-ad-b2c-access-token-are-ga"></a>Általánosan elérhető az Azure AD B2C-alapú hozzáférési jogkivonat

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2C - felhasználói Identitáskezelés  
**A termék képesség:** B2B/B2C
 

Most már elérheti az Web API Azure AD B2C biztonságos hozzáférési jogkivonatok használatával. A funkció a nyilvános előzetes áthelyezése GA Az Azure AD B2C-alkalmazások és a webes API beállítása a felhasználói felületi élmény javult, és egyéb kisebb fejlesztések történtek.
 
További információkért lásd: [az Azure AD B2C: kérelmező hozzáférési jogkivonatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).


---
 

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Az SAML-alapú alkalmazások egyszeri bejelentkezési konfigurációjának tesztelése

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

Konfigurálása SAML-alapú egyszeri Bejelentkezést alkalmazások elkülönítésével lehetővé tesztelése az integráció a konfiguráció lapon. Ha a bejelentkezés során hibát észlel, megadhatja, hogy a tesztelési élményt nyújt a hiba, és az Azure AD megoldási lépések az adott probléma megoldásához nyújt.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)


---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati szabályzata felhasználónkénti jelentéskészítési funkcióval bővült

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi
 

A rendszergazdák mostantól egy adott használati feltételek kiválasztása és tekintse meg az összes olyan felhasználót, hozzájárult, hogy, hogy a használati feltételek és mi dátum/idő azt került sor.


További információkért lásd: a [használati feltételek az Azure AD szolgáltatás](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: az AD FS extranetzárolási védelmi funkciója a kockázatos IP-címek észlelésével bővült 

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** más  
**A termék képesség:** figyelés és jelentéskészítés
 

Connect Health mostantól támogatja a képes észlelni az IP-címek, amelyek mérete meghaladja a küszöbértéket, napi vagy óránkénti alapon sikertelen U/P bejelentkezések. Ez a szolgáltatás által biztosított a funkciók a következők:

- Átfogó jelentés megjeleníti az IP-cím és testre szabható küszöbértéket az óránként vagy naponta alapján jön létre a sikertelen bejelentkezések száma.
- E-mail alapú értesítések megjelenítése, amikor egy adott IP-cím túllépte a küszöbértéket sikertelen U/P bejelentkezések óránként vagy naponta alapon.
- Egy letöltés beállítása ehhez a részletes adatok elemzése


További információkért lásd: [kockázatos IP-jelentés](https://aka.ms/aadchriskyip).

 

---
 

### <a name="easy-app-config-with-metadata-file-or-url"></a>Könnyű alkalmazáskonfigurálás metaadatfájllal vagy URL-címmel

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

A vállalati alkalmazások lapon rendszergazdák fájlt tölthet fel SAML metaadatok SAML-alapú bejelentkezés az AAD mind a nem-gyűjteménye alkalmazás konfigurálása.

Emellett használhatja az Azure AD alkalmazás összevonási metaadatainak URL-CÍMÉT egyszeri bejelentkezés konfigurálása a megcélzott alkalmazáskiszolgálóval.

További információkért lásd: [konfigurálása egyszeri bejelentkezés alkalmazásokhoz, amelyek nincsenek rajta az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).
 

---
 

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Már általánosan elérhetők az Azure AD használati feltételei

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi
 

Az Azure AD-használati átkerült a nyilvános előzetes általánosan elérhető.

További információkért lásd: a [használati feltételek az Azure AD szolgáltatás](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

 

---
 

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Meghatározott cégek vállalatközi felhasználóinak küldött meghívók engedélyezése vagy letiltása

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2B  
**A termék képesség:** B2B/B2C
 

Mostantól megadhatja melyik fiókpartner-szervezetek megosztani, és az Azure AD B2B együttműködés együttműködni. Ehhez az szükséges, ha szeretné specifikus listájának létrehozása engedélyezi vagy megtagadja a tartományok. Ha egy tartomány blokkolva van, ezek a képességek segítségével, az alkalmazottak már nem küldhet meghívókat személyek számára az adott tartományban.

Ezzel a megoldással az erőforrásokhoz való hozzáférés vezérlésére egy zökkenőmentes élményt a jóváhagyott felhasználók engedélyezése során.

A B2B együttműködés funkció érhető el az összes Azure Active Directory-ügyfél számára, és használható együtt az Azure AD Premium-szolgáltatások, mint az feltételes hozzáférési és identity protection részletesebben vezérelhető, mikor és hogyan külső üzleti a felhasználók bejelentkeznek a és a hozzáférést.

További információkért lásd: [adott vállalatoknak B2B felhasználók engedélyezési vagy tiltólista meghívókat](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

 

---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** 3. fél integrációja
 

Április 2018 jelentek meg a gyűjtemény összevonással a következő 13 új alkalmazások támogatják:



HCM, feltételnek [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [titkos Server (helyszíni)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [szervezeti diagram Most](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Teljesítményfigyelő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco felhő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), forgalomban, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)



 A rendelkezésre álló oktatóanyagok listáját találja: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

További információkért lásd: [alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).


 

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Vállalatközi felhasználók helyi alkalmazásokhoz való hozzáférésének biztosítása az Azure AD-ben (nyilvános előzetes verzió)

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2B  
**A termék képesség:** B2B/B2C
 

Azure Active Directory (Azure AD) B2B együttműködés szolgáltatásait használja az Azure AD az erőforráspartner-szervezetek Vendég felhasználóknak szánt meghívó szervezetként mostantól megadhatja a B2B felhasználók helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások használhatják SAML-alapú hitelesítés vagy integrált Windows-hitelesítéssel (IWA) és a Kerberos által korlátozott delegálás (KCD).

További információkért lásd: [Grant B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)
 

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>SSO-integrációs oktatóanyagok beszerzése az Azure Marketplace-ről

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** más  
**A termék képesség:** 3. fél integrációja
 

Ha egy alkalmazás szerepel-e a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) támogatja a SAML alapú egyszeri bejelentkezést, kattintson a **most töltse le innen** tesz lehetővé az adott alkalmazáshoz tartozó integrációs oktatóanyag. 


---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Gyorsabb lett az Azure AD SaaS-alkalmazásokban való automatikus felhasználókiépítési funkciója

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** App kiépítése  
**A termék képesség:** 3. fél integrációja
 

Korábban, a kiépítés összekötők az SaaS-alkalmazásokhoz (például Salesforce, a ServiceNow és mezőben) Azure Active Directory-felhasználó ügyfelek sikerült teljesítménycsökkenés nagyon Ha az Azure AD-bérlő több mint 100 000 kombinált felhasználók és csoportok, és annak meghatározásához, hogy mely felhasználók üzembe kell felhasználó és csoport-hozzárendelések használna.

Április 2. a nagyon fontos teljesítményjavítás telepítve vannak az Azure AD létesítési szolgáltatás, amely jelentősen csökkenti a szinkronizálások közötti Azure Active Directory és a cél SaaS-alkalmazások végrehajtásához szükséges időt.


Emiatt sok ügyfél, amely kellett szinkronizálások alkalmazásokra, amelyek hány napon tartott vagy soha nem fejeződött be, pár perc vagy órán belül most hiba.

További információkért lásd: [mi történik a kiépítés során?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---
 

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Önkiszolgáló jelszóváltoztatás a hibrid Azure AD-hez csatlakoztatott gépek windows 10-es zárolási képernyőjéről

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** önkiszolgáló jelszó-változtatási  
**A termék képesség:** felhasználói hitelesítés
 

A Windows 10-es önkiszolgáló jelszó-Változtatási szolgáltatás támogatását csatlakozott az Azure AD hibrid gépeken lett frissítve. Ez a funkció érhető el a Windows 10 RS4 lehetővé teszi a felhasználóknak a zárolási képernyő, a Windows 10 gépek a jelszó visszaállítása. Engedélyezve van, és az önkiszolgáló jelszó-visszaállításhoz regisztrált felhasználók használhatják ezt a szolgáltatást.

További információkért lásd: [az Azure AD-jelszó alaphelyzetbe állítani a bejelentkezési képernyőt](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).
 

---



## <a name="march-2018"></a>2018 március
 

### <a name="certificate-expire-notification"></a>Tanúsítványlejárati értesítés

**Típus:** rögzített  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 
Az Azure AD, ha a tanúsítványt egy gyűjtemény értesítést küld, vagy nem galéria alkalmazás érvényessége hamarosan lejár. 

Egyes felhasználók nem kapta meg a vállalati alkalmazások SAML-alapú egyszeri bejelentkezés beállítása az értesítéseket. A probléma megoldódott. Az Azure AD-tanúsítványok 7, 30 és 60 nap múlva lejár értesítést küld. Ez az esemény a felügyeleti naplók látni áll. 

További információkért lásd:

- [Tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Naplózási Tevékenységjelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- és GitHub-identitásszolgáltatók az Azure AD B2C-ben

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** B2C - felhasználói Identitáskezelés  
**A termék képesség:** B2B/B2C
 
Az Azure AD B2C identitás-szolgáltatóként most Twitter vagy GitHub adhat hozzá. Twitter GA van áthelyezése nyilvános előzetes verzió GitHub a nyilvános előzetes kiadásra.


További információkért lásd: [Mi az az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>A böngészőalapú hozzáférés korlátozása az Intune Managed Browserrel és az Azure AD-alkalmazás-alapú feltételes hozzáféréssel (iOS és Android)

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi
 

**Nyilvános előzetes verzióként elérhető!**

**Intune Managed Browser SSO:** az alkalmazottak használható egyszeri bejelentkezéshez natív ügyfelek (például Microsoft Outlook) és az Intune Managed Browser minden Azure AD-kompatibilis alkalmazások.

**Intune Managed Browser feltételes hozzáférés támogatása:** most megkövetelheti az alkalmazottak az Intune Managed browser alkalmazás-alapú feltételes hozzáférési szabályzatokkal használatát.

További információk erről az a [blogbejegyzés](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

További információkért lásd:

- [Alkalmazás-alapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Felügyeltböngésző-szabályzatok konfigurálása](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Application Proxy-parancsmagok az általánosan elérhető Powershell-modulban

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App Proxy  
**A termék képesség:** hozzáférés-vezérlés
 
Az alkalmazás Webalkalmazásproxy-parancsmagok a rendszer támogatja a Powershell GA modul! Vegye figyelembe, hogy ez szükséges hozzá legyen naprakész a Powershell-modulok, mert több mint egy év mögött válik, néhány parancsmaggal esetleg nem működnek majd. 


További információkért lásd: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>A Seamless SSO egy nem interaktív protokoll segítségével támogatja a natív Office 365-ügyfeleket

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
Natív Office 365-ügyfél használatával felhasználói (16.0.8730.xxxx verzió vagy újabb verzió) egy csendes bejelentkezési élmény zökkenőmentes SSO használatával. Ez a támogatás biztosítja hozzáadását a nem interaktív (WS-Trust) protokoll az Azure AD.

További információkért lásd: [hogyan bejelentkezhet a zökkenőmentes SSO munkahelyi rendelkező natív ügyfelet?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>A felhasználók a Seamless SSO révén beavatkozás nélkül jelentkezhetnek be, ha egy alkalmazás bejelentkezési kérelmeket küld az Azure AD bérlői végpontjainak

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
A felhasználók megkaphatják a beavatkozás nélküli bejelentkezést, zökkenőmentes egyszeri bejelentkezési modellel, ha egy alkalmazás (például `https://contoso.sharepoint.com`) bejelentkezési kérelmek küldése az Azure AD-bérlő végpontok – Ez azt jelenti, hogy `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` - helyett az Azure AD közös végpontot (`https://login.microsoftonline.com/common/<...>`).

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>A Seamless SSO bevezetéséhez a korábbi kettő helyett csak egy Azure AD-beli URL-címet kell hozzáadni az intranetes zóna beállításaihoz

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
Számára, hogy zökkenőmentes egyszeri Bejelentkezést a felhasználók számára, hogy hozzáadja csak egy Azure AD URL-CÍMÉT a felhasználók az intranet zóna beállítását az Active Directory csoportházirend használatával: `https://autologon.microsoftazuread-sso.com`. Korábban az ügyfelek kellett két URL-címek hozzáadása.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgalériában

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** 3. fél integrációja
 
Március 2018 jelentek meg a gyűjtemény összevonással a következő 15 új alkalmazások támogatják:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Segéd által FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech digitális A vállalati kiszolgáló](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu felhő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
A dokumentáció az összes alkalmazáshoz itt található: [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>Általánosan elérhető a PIM for Azure Resources

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
Ha directory szerepkörök Azure AD Privileged Identity Management használ, most már használhatja PIM tartozó időhöz kötött hozzáférés és a hozzárendelés képességeket például előfizetések erőforráscsoportok, virtuális gépek vagy bármely más erőforrás támogatott Azure-erőforrás-szerepkörök az Azure erőforrás-kezelő által. Többtényezős hitelesítés kikényszerítéséhez a szerepkörök csak időponthoz kötött aktiválásakor, és ütemezés szerinti aktiválások jóváhagyott módosítása windows együtt. Emellett ez a kiadás nem érhető el, például egy frissített felhasználói felület, jóváhagyási munkafolyamatok és képes kiterjesztése a hamarosan lejáró szerepköröket, majd megújítani lejárt szerepkörök nyilvános előzetes fejlesztései.

További információkért lásd: [PIM az Azure-erőforrások (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Nem kötelező jogcímek hozzáadása az alkalmazások jogkivonataihoz (nyilvános előzetes verzió)

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
Az Azure AD alkalmazás mostantól az egyéni vagy választható jogcímkérések JWTs vagy SAML jogkivonatokat.  Ezek a jogcímeket a felhasználó vagy bérlői alapértelmezés szerint a a jogkivonatot, vagy a alkalmazhatósági vonatkozó korlátozások miatt nem szerepelnek.  Ez a funkció jelenleg az Azure AD alkalmazásaiban az 1.0-s és 2.0-s verzió végponton nyilvános előzetes verziójában.  Lásd a dokumentációban talál információkat milyen jogcímeket is hozzáadható, és kérje meg őket az alkalmazásjegyzék szerkesztése.  

További információkért lásd: [nem kötelező jogcímek, az Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Biztonságosabb OAuth-folyamatok a PKCE Azure AD általi támogatása révén.

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
Az Azure AD-dokumentumok PKCE, ami lehetővé teszi a biztonságosabb kommunikáció során az OAuth 2.0 hitelesítési kód grant flow támogatása megjegyezni frissítve lett.  Az 1.0-s és 2.0-s verzió végpontok S256 és a titkosítatlan szöveges code_challenges támogatottak. 

További információkért lásd: [egy engedélyezési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>A Workday Get_Workers API-jában elérhető összes felhasználói attribútumérték kiépítésének támogatása

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App kiépítése  
**A termék képesség:** 3. fél integrációja
 
A nyilvános előzetes verziója bejövő kiépítése a WORKDAY-ből az Active Directoryba és mostantól az Azure AD képes kinyerni és a kiépítés elérhető a Workday Get_Workers API attribútumértékeket támogatja. Ez biztosítja, hogy a több száz további standard támogatja, és az egyéni attribútumok a Workday kezdeti verzióval szállított is bejövő összekötő kiépítése.

További információkért lásd: [testreszabása a Workday felhasználói attribútumok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A csoporttagság dinamikusról statikusra történő módosítása, és fordítva

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** csoportok kezelése  
**A termék képesség:** együttműködés
 
Akkor lehet módosítani a módjára vonatkozik a csoport tagságát. Ez akkor hasznos, ha meg szeretné tartani a azonos csoport nevét és Azonosítóját a rendszer úgy, hogy minden meglévő csoporthoz továbbra is érvényes; Új csoport létrehozásakor igényelnének frissítése ezeket a hivatkozásokat.
Ez a funkció frissítettük az Azure AD felügyeleti központban támogatását. Most az ügyfelek konvertálhatja meglévő csoportok dinamikus tagságot a hozzárendelt tagságát, és fordítva. A meglévő PowerShell-parancsmagok is továbbra is elérhetők.

További információkért lásd: [dinamikus tagság módosítása a statikus és fordítva](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Seamless SSO – továbbfejlesztett kijelentkezési viselkedés

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés
 
Korábban még akkor is, ha a felhasználók explicit módon kijelentkezteti az Azure AD által védett alkalmazáshoz, ezek volna automatikusan megtörténik vissza a zökkenőmentes SSO használatával, ha azok próbál hozzáférni egy Azure AD-alkalmazást a vállalati hálózat belül újra tartományhoz csatlakoztatott eszközön. Ez a változás a kijelentkezési esetén támogatott.  Ez lehetővé teszi, hogy a felhasználók kiválaszthatják, az ugyanazon vagy másik Azure AD-fiókot a bejelentkezéshez vissza, a zökkenőmentes SSO használatával automatikusan aláírás alatt álló helyett.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezést.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>Megjelent az Application Proxy Connector 1.5.402.0-s verziója

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** App Proxy  
**A termék képesség:** identitás biztonsági és védelmi
 
A connector ezen verziója van fokozatosan megkezdődött. November keresztül. A connector új verziója tartalmazza a következő módosításokat:

- Az összekötő most tartományi szintű cookie-k helyette altartomány szintjének beállítása. Ez a gördülékenyebb egyszeri Bejelentkezéses felhasználói élményt biztosítja, és ezzel elkerülheti a redundáns hitelesítést kér.
- Darabolásos kódolási kérelmek támogatása
- Továbbfejlesztett összekötő állapotfigyelés 
- Több hibajavításokat és stabilitás

További információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

 
---
 

 



## <a name="february-2018"></a>2018. február
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigációs a felhasználók és csoportok kezelése

**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** Directory kezelése  
**A termék képesség:** könyvtár
 

A navigációs élmény, a felhasználók és csoportok kezelése úgy alakították ki. Most lépjen directory áttekintésében közvetlenül a lista az összes olyan felhasználó, egyszerűbb hozzáférést a törölt felhasználók listáját. Is léphet directory áttekintésében közvetlenül az összes listájának, egyszerűbb felügyeleti beállítások hozzáférést. És is a címtár – Áttekintés lapon kereshet egy felhasználó, csoport, a vállalati alkalmazás vagy alkalmazás regisztrációja.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Rendelkezésre állási bejelentkezéseket és a naplózási jelentések 21Vianet által működtetett Microsoft Azure-ban (Azure Kína 21Vianet)

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** állami felhők  
**A termék képesség:** figyelés és jelentéskészítés
 

Az Azure Active Directory a jelentések is elérhető (Azure Kína 21Vianet) példányok 21Vianet által működtetett Microsoft Azure-ban. Jelennek meg a következő naplók kapcsolódnak:

- **Bejelentkezések tevékenységi naplóit** -tartalmazza az összes bejelentkezések a tenanthoz társított naplókat.

- **Önkiszolgáló jelszó-vizsgálati naplókban** -magában foglalja az SSPR naplók.

- **Könyvtár felügyeleti naplózási naplózza** -tartalmaz minden a címtár-kezeléssel kapcsolatos felhasználói felügyeleti, felügyeleti és más hasonló a naplók.

Ezek a naplók az akkor is kaphat a hogyan működik a környezetben. A megadott adatokkal a következőket teheti:

- Határozza meg, hogyan az alkalmazások és szolgáltatások ki van használva a felhasználók által.

- Meggátolja, hogy a felhasználók a munka elvégzése elhárítása.

Ezek a jelentések használatával kapcsolatos további információkért lásd: [Azure Active Directory reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Használja a "Jelentés olvasó" szerepkört (a nem rendszergazda szerepkör) az Azure AD-tevékenység jelentések megtekintéséhez

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** figyelés és jelentéskészítés
 

Naplózza az ügyfelek visszajelzés nem rendszergazdai szerepkörök kell rendelkeznie az Azure Active Directory-hozzáférés engedélyezése a része, mivel azt engedélyezte a felhasználók számára a "Jelentés olvasó" szerepkörben bejelentkezések hozzáférési és naplózási tevékenység belül az Azure portál, valamint a Graph API-k használatával. 

További információ jelentésekkel tudnivalókat [Azure Active Directory reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID jogcím elérhető felhasználói attribútum és a felhasználói azonosító

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

Konfigurálható **EmployeeID** a felhasználói azonosítót és a felhasználói attribútum tag felhasználók és a B2B vendégek SAML-alapú bejelentkezés alkalmazásokban a vállalati alkalmazás felhasználói felületén.

További információkért lásd: [a SAML-jogkivonat a vállalati alkalmazások az Azure Active Directoryban kiállított jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Helyettesítő karakterek használata az Azure AD alkalmazásproxy egyszerűsített felügyelet

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App Proxy  
**A termék képesség:** felhasználói hitelesítés
 

Alkalmazás központi telepítésének megkönnyítése, és csökkentheti a felügyelettel járó többletterhelést, mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételére képes. Helyettesítő alkalmazás közzététele, hajtsa végre a szabványos alkalmazás közzétételi folyamata, de használja egy helyettesítő karaktert a belső és külső URL-címek.

További információkért lásd: [helyettesítő alkalmazásokat az Azure Active Directory alkalmazásproxyt](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfiguráció támogatásához új parancsmagok

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App Proxy  
**A termék képesség:** Platform
 

A legújabb kiadás AzureAD PowerShell Preview modul, amely lehetővé teszi az ügyfelek konfigurálása az alkalmazás Proxy alkalmazások PowerShell használatával új parancsmagokat tartalmazza.

Az új parancsmagok az alábbiak: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>A csoportok konfiguráció támogatásához új parancsmagok

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** App Proxy  
**A termék képesség:** Platform
 

A legújabb kiadás AzureAD PowerShell-moduljának csoportok kezelése az Azure AD-parancsmagokat tartalmaz. Ezeket a parancsmagokat a AzureADPreview modul korábban elérhető volt, és most hozzáadódnak a AzureAD modul

A csoport parancsmagok, amelyek az általános elérhetőség most kiadás az alábbiak: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Az Azure AD Connect egy új kiadásában érhető el

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** AD szinkronizálása  
**A termék képesség:** Platform
 

Az Azure AD Connect egy az elsődleges eszköz, amely szinkronizálja az adatokat az Azure AD között, valamint a helyileg üzemeltetett adatforrásainak, beleértve a Windows Server Active Directory és az LDAP.

**Fontos**
 
A build vezet be a séma- és szinkronizálási szabályok módosításokat. Az Azure AD Connect szinkronizálási szolgáltatás teljes importálást és teljes szinkronizálást lépéseket elindítja a frissítés után. Ez a viselkedés módosításának módjával kapcsolatos információkért lásd: [hogyan késlelteti a frissítés után teljes szinkronizálást](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ebben a kiadásban a következő frissítéseinek és változásainak rendelkezik:

**Javított problémák**

- Hárítsa el időzítési az háttérfeladatok partíció szűrés lap a következő lapra váltáskor.
- Rögzített egy hiba, a hozzáférési kísérlet során a configdb elemre egyéni művelet miatt.
- Sql-kapcsolat időtúllépés helyreállítás programhiba rögzített.
- Ha a tanúsítványok SAN helyettesítő karaktereket is tartalmazó előfeltétel-ellenőrzés nem rögzített hiba.
- Egy hiba, aminek következtében miiserver.exe összeomlási AAD connector exportálás során rögzített.
- Rögzített hiba, mely rossz jelszó kísérlet aad-ben futó naplózva a tartományvezérlőn connect varázsló beállításainak módosításához

**Új szolgáltatásait és fejlesztéseit**


 
- Telemetriai - rendszergazdák megváltoztathatja az osztályba tartozó adatok be-és kikapcsolása.

- Az Azure AD Health data - rendszergazdák kell látogasson el a health portálon az Eszközállapot-beállítások szabályozásához. Ha a szolgáltatás házirend módosítva lett, az ügynökök olvassa el, és betartatni a.

- Eszközműveletek visszaírási konfigurációs és a lap inicializálási folyamatjelző segítségével hozzá.

- A HTML-jelentést és a teljes adatgyűjtést egy ZIP-szöveg az általános diagnosztika továbbfejlesztett / HTML-jelentést.

- Továbbfejlesztett megbízhatóságának automatikus frissítése, és hozzá további telemetriai annak érdekében, hogy a kiszolgáló állapotának lehet meghatározni.

- Rendelkezésre álló jogosultságok korlátozása AD-összekötő fiókhoz fiókokhoz. A varázsló új telepítések esetén korlátozza a kiemelt jogosultságú fiókok engedélyekkel rendelkezik a MSOL fiók MSOL-fiók létrehozása után. A módosítások befolyásolják az Expressz telepítés és egyéni telepítések fiókkal automatikus létrehozása.

- Módosította a telepítő futtatásával az AADConnect tiszta telepítése rendszergazdai jogosultság szükséges.

- Új segédprogram egy adott objektum szinkronizálási problémák elhárításához. Jelenleg a segédprogram ellenőrzi a következő:

    - Szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD-bérlő UserPrincipalName nem egyezik.
  
    - Ha az objektum szűrve van a szinkronizálás tartomány szűrés miatt
  
    - Ha az objektum szűrve van a szervezeti egység (OU) szűrés miatt szinkronizálás

- Új segédprogram szinkronizálni az aktuális Jelszókivonat az egy adott felhasználói fiók a helyszíni Active Directoryban tárolja. A segédprogram nem követeli meg a jelszó módosítása. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>A vett támogató Intune alkalmazás adatvédelmi szabályzatok használata az Azure AD alkalmazás-alapú feltételes hozzáférés

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi
 

További alkalmazások alkalmazás-alapú feltételes hozzáférést támogató jelentek meg. Most hogy ingyenesen juthatnak az Office 365 és más Azure AD-csatlakoztatott felhőalapú alkalmazások jóváhagyott alkalmazások.

A következő alkalmazások kerülnek. február végéig 

- Microsoft PowerBI

- Microsoft indítója

- Microsoft számlázás

További információkért lásd:

- [Jóváhagyott app követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Használati feltételek frissítése mobil élmény 

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi
 

Amikor megjelennek a használati feltételeket, most kattintson **problémába rendelkező? Kattintson ide**. Ez a hivatkozás megnyitja a használati feltételeket natív módon az eszközön. A betűméret a dokumentumban és az eszköz képernyő méretétől függetlenül nagyítás, és elolvasni a dokumentumot, igény szerint. 
 

---
 
## <a name="january-2018"></a>2018. január
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgalériában 

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** 3. fél integrációja
 

A január 2018 a összevonási támogatást a következő új alkalmazások az alkalmazásgyűjtemény lettek hozzáadva:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelmi felügyeleti szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont Directory](https://go.microsoft.com/fwlink/?linkid=864699) és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az összes rendelkezésre álló oktatóanyag teljes áttekintése, lásd: [SaaS alkalmazások integrálása az Azure Active Directoryval](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>További észlelt kockázattal rendelkező bejelentkezés

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Identity Protection  
**A termék képesség:** identitás biztonsági és védelmi
 

A a észlelt kockázati események történő betekintést az Azure AD-előfizetés van kötve. Az Azure AD Premium P2 kiadásával kap minden alapul szolgáló észlelések részletesebb információt.

Az Azure AD Premium P1 kiadásával észlelések, amelyekre nem vonatkozik a licenc jelennek meg a kockázat esemény bejelentkezési további kockázattal észlelte.

További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) foglalkozó cikket.
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazások a végfelhasználó hozzáférési panel elrejtése

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

Most jobban kezelhető hogyan Office 365-alkalmazások jelenik meg a felhasználói hozzáférés panelek egy új felhasználói beállítás segítségével. Ez a beállítás akkor hasznos, ha Ön szeretné csak az Office portálon az Office-alkalmazások megjelenítése az alkalmazások a felhasználó hozzáférési paneleken csökkentésével a. A beállítás a található a **felhasználói beállítások** és lett címkézve **csak értesülhet az Office 365 portál Office 365-alkalmazások**.
 

További információkért lásd: [elrejtése az alkalmazás a felhasználói élmény az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Zökkenőmentes bejelentkezési engedélyezve a jelszó SSO közvetlenül az alkalmazás URL-címről alkalmazásokba 

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés
 

A személyes alkalmazások bővítmény kényelmes eszköz használatával, amely lehetővé teszi a személyes alkalmazások egyszeri bejelentkezést a funkció a böngészőben gyors most érhető el. Után a felhasználó telepítése jelenik meg a böngészőben egy waffle ikon, amely azokat gyors hozzáférést biztosít alkalmazások. Felhasználók mostantól kihasználhatja:

- Képes közvetlenül jelentkezzen be a jelszó-SSO-alapú alkalmazások az alkalmazás bejelentkezési oldalról
- Indítsa el a kívánt alkalmazást, gyors keresési funkciója
- A bővítmény legutóbb használt alkalmazások parancsikonjai
- A bővítmény él, Chrome és Firefox érhető el.
 
További információkért lásd: [saját alkalmazások biztonságos bejelentkezési bővítmény](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élmény a klasszikus Azure portálon visszavontuk.

**Típus:** elavult   
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** könyvtár
 

Től január 8, 2018, az Azure AD felügyeleti élmény a klasszikus Azure portálon visszavontuk. A klasszikus Azure portálon magát a kivonása együtt történt. Következő lépésként kell használnia a [az Azure AD felügyeleti központban](https://aad.portal.azure.com) valamennyi a portál-alapú felügyeleti az Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor webportál visszavontuk.

**Típus:** elavult  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** könyvtár
 

2018. január 8.,-től a PhoneFactor webportál eltávolították. A portál számára a multi-factor Authentication kiszolgáló lett megadva, de olyan feladatokat be az Azure portálon: portal.azure.com is át lett helyezve. 

A többtényezős hitelesítés konfigurálása a következő helyen található: **Azure Active Directory \> MFA kiszolgáló**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések érvényteleníthető


**Típus:** elavult  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** Identitáskezelés életciklusa  


Az általános rendelkezésre az új Azure Active Directory felügyeleti konzol és az új API-k most már hozzáférhető tevékenység és a biztonsági jelentések, a jelentés API-k alatt "/ jelentések" végpont rendelkezik visszavontuk 2017. December 31. a végén.


**Mi az az elérhető?**

Az új felügyeleti konzol áttérés részeként hajtottunk 2 új API-k érhető el az Azure AD tevékenységi naplóit. Az új API-készlet adja meg a gazdagabb szűrési és rendezési funkció gazdagabb vizsgálati és bejelentkezési tevékenységek nyújtása mellett. Korábban a biztonsági jelentések keresztül elérhető adatok most Identity Protection kockázati események API Microsoft Graph keresztül érhető el.

További információkért lásd:

- [Bevezetés az Azure Active Directory reporting API használatába](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017. december
 

### <a name="terms-of-use-in-the-access-panel"></a>A hozzáférési Panel a használati feltételek

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi
 
Most már a hozzáférési panel megnyithatja és megtekintheti a használati feltételeket, amelyek korábban elfogadott.

Kövesse az alábbi lépéseket:

1. Lépjen a [MyApps portal](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza ki **profil** a listából. 

3. Az a **profil**, jelölje be **tekintse át a használati feltételek**. 

4. Most már megtekintheti a használati feltételeket elfogadott. 

További információkért lásd: a [(előzetes verzió) szolgáltatás az Azure AD feltételeinek](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezés során tapasztal élmény

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** felhasználói hitelesítés
 
Az Azure AD és a Microsoft-fiók identitásrendszere UI volt újratervezett úgy, hogy a konzisztens Megjelenés és működés. Emellett az Azure AD bejelentkezési oldal gyűjti a felhasználónév először, a második képernyőn a hitelesítő adatok követ.

További információkért lásd: [az új Azure AD bejelentkezés során tapasztal élmény mostantól nyilvános előzetes verziójában](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérdésben: egy új "bejelentkezve szeretnék maradni" tapasztalattal az Azure AD-bejelentkezés

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** felhasználói hitelesítés
 
A **bejelentkezve szeretnék maradni** jelölőnégyzetet az Azure AD bejelentkezési oldal váltotta fel egy új értesítés megjelenő után sikeresen hitelesíteni. 

Ha **Igen** kell a figyelmeztetésre, a szolgáltatás lehetővé teszi egy állandó frissítési jogkivonat. Ez a viselkedés megegyezik a kiválasztott amikor a **bejelentkezve szeretnék maradni** jelölőnégyzetet a régi élményt nyújt. Összevont bérlők esetén ez a kérdés az összevont szolgáltatás sikeres hitelesítést követően jeleníti meg.

További információkért lásd: [kevesebb bejelentkezési kérdésben: az új "bejelentkezve szeretnék maradni" az Azure AD szolgáltatás Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>A használati feltételek elfogadása előtt kell bővíteni szükséges konfiguráció hozzáadása

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi
 
A beállítás a rendszergazdák a felhasználónak kell bontsa ki a használati feltételeket a feltételek elfogadása előtt.

Válassza ki vagy **a** vagy **ki** a felhasználók bontsa ki a használati feltételeket. A **a** beállítás használatakor a felhasználóknak a megtekintése előtt őket a használati feltételek.

További információkért lásd: a [(előzetes verzió) szolgáltatás az Azure AD feltételeinek](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>A megfelelő szerepkör-hozzárendelések hatókörön belüli aktiválás

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
Hatókörön belüli aktiválási jogosult Azure-erőforrás szerepkör-hozzárendelések kisebb, mint az eredeti hozzárendelés alapértelmezett önállóan aktiválásához használhatja. Például akkor, ha a kapott előfizetés tulajdonosaként az Ön bérelt szolgáltatásának. A hatókörbe tartozó aktiválási aktiválhatja a tulajdonosi szerepkört az előfizetés (például a csoportok és a virtuális gépek) lévő öt erőforrások. Az aktiválás hatókörének előfordulhat, hogy az esélye, a kritikus fontosságú Azure-erőforrások nemkívánatos módosítások végrehajtásakor.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD-alkalmazásgyűjtemény

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** 3. fél integrációja
 
A December 2017 a összevonási támogatást a következő új alkalmazások az alkalmazásgyűjtemény lettek hozzáadva:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Az összes rendelkezésre álló oktatóanyag teljes áttekintése, lásd: [SaaS alkalmazások integrálása az Azure Active Directoryval](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure Active directory szerepkörök a jóváhagyási munkafolyamatai

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
Az Azure Active directory szerepkörök jóváhagyási munkafolyamata az általánosan elérhető.

A jóváhagyási munkafolyamata kiemelt szerepkör rendszergazdák megkövetelheti szerepkör aktiválás kéréséhez jogosult-szerepkör tagjai az emelt szintű szerepkör használatához. Több felhasználók és csoportok delegált jóváhagyási feladatkörök lehet. Jogosult szerepkör tagjai értesítések jóváhagyási befejeződött, és a szerepkör aktív.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Áteresztő hitelesítés: a Skype vállalati verziójának ügyfélszolgálatával

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés


Áteresztő hitelesítés támogatja a felhasználói bejelentkezések Skype üzleti ügyfélalkalmazások, amely támogatja a modern hitelesítést, amely tartalmazza az online és a hibrid topológiák. 

További információkért lásd: [a modern hitelesítést használó támogatott üzleti topológiák Skype](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC (előzetes verzió) Azure AD Privileged Identity Management frissítések

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
A nyilvános előzetes verzió frissítése az Azure AD Privileged Identity Management (PIM) a átruházásához hozzáférés-vezérlés (RBAC) az alábbi műveleteket hajthatja végre:

* Éppen elég felügyelettel használja.
* Erőforrás-szerepkörök aktiválása jóváhagyást igényel.
* Egy szerepkör, amely jóváhagyásra van szüksége a két Azure AD és az Azure RBAC-szerepkörök a jövőbeli aktiválás ütemezni.

 
További információkért lásd: [az Azure-erőforrások (előzetes verzió) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Hozzáférés-vezérlés szolgáltatás kivonása



**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** hozzáférés-vezérlés szolgáltatás  
**A termék képesség:** hozzáférés-vezérlés szolgáltatás 


 Az Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlés szolgáltatás) késői 2018 a rendszerből. A részletes ütemezés és a magas szintű áttelepítési útmutató tartalmaz további információt nyújtanak a következő néhány hétben. Hagyhatja megjegyzések ezen a lapon, a hozzáférés-vezérlés szolgáltatás kapcsolatos kérdéseket, és a-csoport tagja lesz válaszol rájuk.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune Managed Browser böngésző-hozzáférés korlátozása 


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem




Office 365 és más Azure AD-csatlakoztatott felhőalkalmazások böngészőalapú hozzáférés korlátozhatja az Intune Managed Browser használják, mint az engedélyezett alkalmazások. 

Most már konfigurálhatja az alkalmazás-alapú feltételes hozzáférés a következő feltételt:

**Ügyfélalkalmazások:** böngésző

**Mi az a változás hatását?**

Jelenleg nincs hozzáférése a probléma használatakor. A kép nem érhető el, minden hozzáférés szükséges a használja a felügyelt böngésző alkalmazást. 

Keresse meg a képességre és a közelgő blogok és a kibocsátási megjegyzések a további információk. 

További információkért lásd: [feltételes hozzáférés az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem




A következő alkalmazások listájának hozzáadni tervezett [ügyfélalkalmazások jóváhagyott](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


További információkért lásd:

- [Jóváhagyott app követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek több nyelven támogatása



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi





A rendszergazdák mostantól hozhat létre új használati feltételek több PDF-dokumentumot tartalmazó. Jelölheti meg a megfelelő nyelvű verzióját PDF dokumentumokhoz. A felhasználóknak megjelenik a PDF-fájl a megfelelő nyelvű verzióját preferenciáik alapján. Nincs egyezés, ha az alapértelmezett nyelv jelenik meg.


---
 

### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó visszaírási ügyfél állapota



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** önkiszolgáló jelszóváltoztatás  
**A termék képesség:** felhasználói hitelesítés


 

Most már a helyszíni jelszó visszaírási ügyfél állapotát tekintheti meg. Ez a beállítás érhető el a **helyszíni integráció** szakasza a [jelszó-átállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lap. 

Ha a kapcsolat a helyszíni visszaírási ügyfél problémák vannak, egy hibaüzenet, amely tartalmazza a jelenik meg:

- Miért nem tud csatlakozni a helyszíni visszaírási ügyfél információkat.
- Dokumentáció, és segítséget nyújt a probléma megoldását mutató hivatkozást. 


További információkért lásd: [helyszíni integráció](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD, alkalmazás-alapú feltételes hozzáférés 



 
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** identitás biztonság és védelem





Most korlátozzuk Office 365 és más Azure AD-csatlakoztatott felhőalapú alkalmazások [ügyfélalkalmazások jóváhagyott](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , amely támogatja az Intune app adatvédelmi szabályzatok használatával [az Azure AD alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Intune app adatvédelmi szabályzatok segítségével konfigurálhatja, és ezeket az ügyfél alkalmazásokat a vállalati adatok védelme.

Kombinálásával [alkalmazás-alapú](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) rendelkező [eszközalapú](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) feltételes hozzáférési házirendeket, így rugalmasan adatok a személyes és vállalati eszközök védelme érdekében.

A következő feltételek és szabályozza a való használathoz az alkalmazás-alapú feltételes hozzáférés mostantól érhetők el:

**Támogatott platformonként feltétel**

- iOS
- Android

**Ügyfél alkalmazások feltétel**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése


További információkért lásd: [az Azure AD alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Az Azure-portálon az Azure AD-eszközök kezelése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** eszközök regisztrációja és kezelése  
**A termék képesség:** identitás biztonság és védelem

 



Most már található az Azure ad Szolgáltatásba csatlakoztatott összes eszközre és az eszközzel kapcsolatos tevékenységek egy helyen. Nincs olyan új felügyeleti élmény kezeli az eszköz identitások és beállításokat az Azure portálon. Ebben a kiadásban a következőket teheti:

- Megtekintheti az eszközök által biztosított a feltételes hozzáférés az Azure ad-ben.
- Megtekintheti a tulajdonságait, többek között a hybrid Azure AD-csatlakoztatott eszközökhöz.
- BitLocker-kulcsok a Azure AD-csatlakoztatott eszközök keresése, az eszköz Intune-nal, és több kezelését.
- Az Azure AD eszközzel kapcsolatos beállítások kezelése.

További információkért lásd: [eszközök kezelése az Azure portál használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>A feltételes hozzáférés az Azure AD eszköz platformként macOS támogatása 



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem 
 

Most belefoglalható (vagy kizárása) macOS eszköz platform feltétele az Azure AD feltételes hozzáférési házirendben. MacOS a támogatott eszközplatformok való hozzáadásával a következőket teheti:

- **Eszközök regisztrálására és kezelésére macOS Intune-nal.** Más platformokon, például az iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el macOS egyesített regisztrációkat elvégzéséhez. Az új vállalati portál alkalmazás macOS segítségével regisztrálhasson egy eszközt az Intune-nal, és regisztrálhatja azt az Azure ad-val.
- **Győződjön meg arról, macOS eszközök igazodnia kell a szervezet megfelelőségi szabályzatok az Intune-ban megadva.** Az Intune-ban az Azure portálon most már beállíthat macOS eszközök megfelelőségi szabályzatainak. 
- **Alkalmazások való hozzáférés korlátozása csak a megfelelő macOS eszközökre az Azure AD-ben.** MacOS feltételes hozzáférési házirend szerzői rendelkezik egy különálló eszköz platform lehetőséget. Most a célzott alkalmazás beállítása az Azure-ban macOS vonatkozó feltételes hozzáférési házirendjei is létrehozhat.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló bővítmény Azure multi-factor Authentication 


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** többtényezős hitelesítés  
**A termék képesség:** felhasználói hitelesítés




A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication a hitelesítési infrastruktúra felhőalapú többtényezős hitelesítés lényeges képességét biztosítja a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló kiterjesztésű adhat hozzá a telefonhívás, szöveges üzenetet vagy telefonszám alkalmazás ellenőrzése a meglévő hitelesítési folyamatot. Nincs telepítése, konfigurálása és karbantartása az új kiszolgálók. 

Ezt a bővítményt a szervezeteknek, amelyek a virtuális magánhálózati kapcsolatok védelme az Azure multi-factor Authentication kiszolgáló telepítés nélkül kívánja lett létrehozva. A hálózati házirend-kiszolgáló között RADIUS és a felhőalapú Azure multi-factor Authentication második tényezőként hitelesítésének biztosításához adapterként működik bővítmény összevont, vagy a szinkronizált felhasználók.


További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy véglegesen eltávolítja a törölt felhasználók


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** felhasználók kezelése  
**A termék képesség:** könyvtár 



Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Állítsa vissza a felhasználóhoz. 
- A felhasználó véglegesen törli.


**Próbálja:**

1. Válassza ki az Azure AD felügyeleti központban [minden felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a a **kezelése** szakasz. 

2. Az a **megjelenítése** listáról válassza ki **nemrég törölt felhasználók**. 

3. Válasszon legalább egy nemrég törölt felhasználók, és majd állíthatja vissza azokat, vagy véglegesen törli.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem


A következő alkalmazások listáját a rendszer adott [ügyfélalkalmazások jóváhagyott](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


További információkért lásd:

- [Jóváhagyott app követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Használja a feltételes hozzáférési házirend vezérlőelemek között a "vagy" 


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem

 
Most már használhatja "vagy" (van szükség a kijelölt vezérlők egyik) a feltételes hozzáférés-vezérlést. E szolgáltatás használatával házirendek létrehozása, a "vagy" közötti hozzáférés-vezérlést. Például használhatja ezt a szolgáltatást, amelyhez a felhasználó a multi-factor authentication használatával bejelentkezni "vagy" kell lennie a megfelelő eszközökre házirend létrehozása.

További információkért lásd: [vezérlők az Azure AD feltételes hozzáférésével](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Valós idejű kockázati események összesítése


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** Identity protection  
**A termék képesség:** identitás biztonság és védelem


Az Azure AD Identity Protection összes valós idejű kockázat származó eseményt az azonos IP-cím egy adott napon most összesítése az egyes kockázat esemény. Ez a változás a felhasználó biztonsági változás nélkül látható kockázati események mennyiségének korlátozza.

Az alapul szolgáló valós idejű észlelés működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha egy bejelentkezési kockázat biztonsági házirend beállítása a multi-factor authentication blokkolja a hozzáférést, továbbra is indított minden kockázatos bejelentkezés során.

 
---
 




## <a name="october-2017"></a>2017. október


### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések érvényteleníthető


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** Identitáskezelés életciklusa  



Az Azure-portálon biztosítja:

- Egy új Azure AD felügyeleti konzolon.
- Új API-khoz, tevékenységek és biztonsági jelentések.
 
Az új lehetőségekhez, mert a jelentés API-k alapján a/Reports végpont 2017. December 10. volt használatból. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezés mező észlelése


**Típus:** rögzített   
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés  



Az Azure AD egy HTML-felhasználó nevét és jelszavát mező leképező alkalmazások automatikus bejelentkezési mező észlelését támogatja. Ezeket a lépéseket ismertetett [automatikusan rögzítése az alkalmazás bejelentkezési mezők](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Ez a funkció található hozzáadásával egy *nem-gyűjtemény* alkalmazás a a **vállalati alkalmazások** lapját a [Azure-portálon](http://aad.portal.azure.com). Emellett konfigurálhatja a **egyszeri bejelentkezés** módra az új alkalmazást a **jelszó-alapú egyszeri bejelentkezést**, adja meg a webszolgáltatás URL-címet, és mentse a a lap.
 
Ez a funkció egy szolgáltatási probléma miatt ideiglenesen letiltotta. A probléma, és az automatikus bejelentkezési mező észlelési érhető el újra.

---

### <a name="new-multi-factor-authentication-features"></a>Új többtényezős hitelesítési szolgáltatások


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** többtényezős hitelesítés  
**A termék képesség:** identitás biztonság és védelem  



Többtényezős hitelesítés (MFA) a szervezet védelmének fontos részét képezi. Csak azok több adaptív hitelesítő adatokat és a felhasználói élmény zökkenőmentesebb, a következő funkciók lettek hozzáadva: 

- Multi-factor Authentication ellenőrző eredmények közvetlenül integrálva vannak az Azure AD-bejelentkezés jelentést, amely magában foglalja a többtényezős hitelesítés eredmények programozott hozzáférést.
- A többtényezős hitelesítés konfigurációs mélyebb integrálva van az Azure Active Directory beállítása az Azure portálon tapasztalható.

Ebben az nyilvános előzetes MFA felügyeleti és jelentéskészítési konfigurációs tapasztalatok core az Azure AD integrált részét képezik. Most már a többtényezős hitelesítés felügyeleti portál funkció az Azure AD-élmény belül kezelheti.

További információkért lásd: [a többtényezős hitelesítés az Azure portálon jelentési hivatkozás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Használati feltételek



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** megfelelőségi  



Használhatja az Azure AD használati feltételeket, például a jogi vagy megfelelőségi követelményeknek megfelelő nyilatkozatok van a felhasználók számára.

Az Azure AD használati feltételek a következő helyzetekben használhatja:

- Általános használati feltételeket a szervezete összes felhasználója számára
- Egy felhasználói attribútum (például ápolók és orvosi) vagy belföldi és nemzetközi alkalmazottai, a dinamikus csoportok által megadott használati feltételek
- Konkrét használati feltételek a nagy jelentőségű üzleti alkalmazások, például a Salesforce elérése

További információkért lásd: [az Azure AD használati feltételek](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>A Privileged Identity Management fejlesztései


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management  


Az Azure AD Privileged Identity Management kezelése, szabályozása, és figyelje az Azure-erőforrások (előzetes verzió) hozzáférést a szervezeten belül:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Erőforrások az Azure portálon az Azure RBAC-funkciók használatára kihasználhatja a biztonsági és életciklus-kezelési képességei, amelyre az Azure AD Privileged Identity Management kínálnak.

További információkért lásd: [Privileged Identity Management az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** értékelést eléréséhez  
**A termék képesség:** megfelelőségi  



Szervezet hozzáférés értékelést (előzetes verzió) segítségével hatékonyan tudja kezelni a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférés: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. Felülvizsgálók hatékonyan eldöntheti, hogy a vendégek továbbra is biztosítja a hozzáférés értékelést feltárása alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [ellenőrzi, hogy az Azure AD hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Külső felek alkalmazásainak a személyes alkalmazások és az Office 365 alkalmazás indító elrejtése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés  



Most jobban kezelhető jelenik meg a felhasználói portálon keresztül egy új alkalmazások **app elrejtése** tulajdonság. Azokban az esetekben, ahol alkalmazások ikonjaihoz jelenik meg a háttér-szolgáltatásaihoz vagy ismétlődő csempék és zsúfoltságát felhasználók app kilövők alkalmazások elrejthetők. A váltógomb van a **tulajdonságok** szakasz a külső alkalmazás és lett címkézve **látható a felhasználó számára?** Egy alkalmazás programozott módon a PowerShell segítségével is elrejthetők. 

További információkért lásd: [egy külső alkalmazások az Azure AD-ben a felhasználói élmény elrejtése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Mi az az elérhető?**

 Az új felügyeleti konzol, a két új API-k történő áttelepítés részeként az Azure Active Directory érhetők el naplók. Az új API-készlet gazdagabb szűrési és rendezési gazdagabb vizsgálati és bejelentkezési tevékenységek nyújtása mellett funkciókat biztosít. Korábban a biztonsági jelentések most keresztül elérhető adatok Identity Protection kockázati események API Microsoft Graph is elérhetőek.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>A gyorsjavítás az Identity Manager


**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Identity Manager  
**A termék képesség:** Identitáskezelés életciklusa  



A kumulatív gyorsjavítás (build 4.4.1642.0) szeptember 25, 2017, Identity Manager 2016 Service Pack 1-től érhető el. A csomag:

- Oldja fel a problémákat, és hozzáadja a fejlesztései.
- Az összesítő frissítés, amely kiváltja a build 4.4.1459.0 Identity Manager 2016 legfeljebb frissítéseinek Identity Manager 2016 Service Pack 1. 
- Szükséges hozzá Identity Manager 2016 4.4.1302.0 felépítéséhez. 

További információkért lásd: [kumulatív gyorsjavítás (build 4.4.1642.0) érhető el az Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
