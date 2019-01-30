---
title: Újdonságok archívuma? – Az azure Active Directory |} A Microsoft Docs
description: A Mi az új kibocsátási megjegyzések az áttekintésben Ez a szakasz tartalmazza a tevékenység 6 hónap. 6 hónapra elemeket a fő cikkben eltávolítja és archív cikkben üzembe.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: ba538aed855667417e14bef12b708e80f34cd8c4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243926"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Újdonságok archívuma? az Azure Active Directoryban

Az elsődleges [Mi az új kibocsátási megjegyzések](whats-new.md) cikk tartalmazza a legújabb 6 hónapra visszamenőleg adatokat, amíg ez a cikk a régebbi adatokat tartalmaz.

A Mi új kibocsátási megjegyzések-információkat biztosít Önnek kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Módosítási értesítés: A delegált engedélyezési folyamat használata az Azure AD tevékenység naplók API-alkalmazások biztonsági javítás

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Az erősebb biztonsági kényszerítési miatt volt módosítást a delegált engedélyezési folyamatok eléréséhez használó alkalmazásokra vonatkozó engedélyeinek [az Azure AD tevékenység naplók API-k](https://aka.ms/aadreportsapi). Ez a változás történik a **2018. június 26**.

Ha bármely, az alkalmazások az Azure AD tevékenység API-k, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás nem lesz történik, a módosítás után új.

**Az Alkalmazásengedélyek frissítése**

1. Jelentkezzen be az Azure Portalon, válassza **Azure Active Directory**, majd válassza ki **Alkalmazásregisztrációk**.
2. Válassza ki az Azure AD tevékenység naplók API-t használó, válassza ki az alkalmazást **beállítások**, jelölje be **szükséges engedélyek**, majd válassza ki a **Windows Azure Active Directory** API.
3. A a **delegált engedélyek** területén a **hozzáférés engedélyezése** panelen jelölje be a **olvasási directory** adatokat, és válassza ki **mentése**.
4. Válassza ki **engedélyeket**, majd válassza ki **Igen**.
    
    >[!Note]
    >A következőkhöz biztosítanak engedélyeket: az alkalmazás globális rendszergazdának kell lennie.

További információkért lásd: a [engedélyeket](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) eléréséhez az Azure AD jelentéskészítési API-t a cikk az Előfeltételek területéhez.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Az Azure AD-szolgáltatásokhoz, a PCI DSS TLS-beállítások konfigurálása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** –  
**A termék szolgáltatás:** Platform

Transport Layer Security (TLS) protokoll, amely két kommunikáló alkalmazás közötti adatvédelmi és adatintegritás biztosít, és a jelenleg használt leggyakrabban telepített biztonsági protokoll.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy korai verziói a TLS és a Secure Sockets Layer (SSL) le kell tiltani, új és biztonságosabb alkalmazások protokollok engedélyezése, a megfelelőségi től értéke **június 30., 2018-as**. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz és PCI-DSS szabvány igényelnek, le kell tiltania a TLS 1.0. A TLS több verziói elérhetők, de a TLS 1.2 a legújabb verzió érhető el az Azure Active Directory Services. Erősen ajánlott közvetlenül a TLS 1.2-es ügyfél-kiszolgáló és a böngésző és a kiszolgáló kombinációk áthelyezése.

Elavult böngészők nem támogatja a TLS újabb verziók, például a TLS 1.2. A böngésző által támogatott mely verziói a TLS megtekintéséhez nyissa meg a [Qualys SSL Labs](https://www.ssllabs.com/) webhelyről, és kattintson a **teszteléséhez a böngésző**. Javasoljuk, hogy a böngésző legújabb verziójára frissítse és lehetőleg engedélyezése csak a TLS 1.2-es.

**A TLS 1.2-es, böngésző engedélyezése**

- **A Microsoft Edge és az Internet Explorer (mindkettő vannak beállítva az Internet Explorerben)**

    1. Válassza ki az Internet Explorer megnyitásához **eszközök** > **Internetbeállítások** > **speciális**.
    2. Az a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki **OK**.
    3. Minden böngészőablakot bezárni, majd indítsa újra az Internet Explorerben. 

- **Google Chrome**

    1. Nyissa meg a Google Chrome, típus *chrome://settings/* a címsorában, majd nyomja le az **Enter**.
    2. Bontsa ki a **speciális** lehetőségeket, nyissa meg a **rendszer** területre, és válassza ki **nyissa meg a proxybeállítások**.
    3. Az a **internetes tulajdonságok** jelölje ki a **speciális** lépjen a **biztonsági** területen válassza **TLS 1.2 használatára**, majd válassza ki a  **OK**.
    4. Minden böngészőablakot bezárni, majd indítsa újra a Google Chrome-ban.

- **Mozilla Firefox**

    1. Nyissa meg a Firefox, típus *kapcsolatos: config* címsorában, és nyomja le az **Enter**.
    2. Keresse meg az érvényességi *TLS*, majd válassza ki a **security.tls.version.max** bejegyzés.
    3. Állítsa az értékét **3** kényszerítése a böngészőben, hogy a TLS 1.2-es verzió használatával, és válassza **OK**.

        >[!NOTE]
        >Firefox 60.0 verzió támogatja a TLS 1.3-as, így a security.tls.version.max érték is megadható **4**.

    4. Minden böngészőablakot bezárni, majd indítsa újra a Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. június

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 június tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 15 új alkalmazásokról:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [zene stabilizálódási](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Végponti biztonsági mentési](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho egy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Helyszíni SharePoint-](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [CX Suite fája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Nyilvános előzetes verzióban érhető el az Azure AD jelszóvédelem

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Felhasználók hitelesítése

Segítségével az Azure AD jelszóvédelem kiiktathatja könnyen kitalálni a jelszót a környezetből. Mivel ezek a jelszavak segítséget nyújt egy jelszó megfelelő típusú támadás a biztonsági sérülés kockázata.

Pontosabban az Azure AD jelszóvédelem nyújt segítséget:

- A munkahelyi fiókok védelmét is az Azure AD-ben és a Windows Server Active Directory (AD). 
- Leállítja a felhasználók listáját a leggyakrabban használt jelszavak több mint 500, és több mint 1 millió karakter helyettesítő változata ezeket a jelszavakat a jelszavak használatával.
- Felügyelheti az Azure AD-jelszó védelmi egyetlen helyről az Azure AD-portálon is az Azure ad és helyszíni Windows Server AD.

Az Azure AD jelszóvédelem kapcsolatos további információkért lásd: [rossz jelszavak, a szervezet számára](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Új "az összes vendégek" feltételes hozzáférési házirend sablon használati feltételek (feltételek) létrehozása során létrehozott

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás

A használati feltételek (feltételek) elkészítése során egy új feltételes hozzáférési szabályzat sablont is létrejön a "minden Vendég" és "minden alkalmazás". Az új csoportházirend-sablon létrehozásának és érvényesítési folyamat egyszerűsítésével vendégek újonnan létrehozott használati feltételek vonatkoznak.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Új "egyéni" feltételes hozzáférési szabályzat sablon létrehozása a használati feltételek (feltételek) létrehozása közben

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás

A használati feltételek (feltételek) elkészítése során egy új "egyéni" feltételes hozzáférési szabályzat sablont is létrejön. Az új csoportházirend-sablon lehetővé teszi a használati feltételek létrehozása, és azonnal folytassa a feltételes hozzáférési szabályzat létrehozása panelen anélkül, hogy manuálisan keresse meg a portálon keresztül.

További információkért lásd: [Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Üzembe helyezése az Azure multi-factor Authentication az új és átfogó útmutatást

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Identitásbiztonság és -védelem
 
Kiadás új részletes útmutatást nyújt az Azure multi-factor Authentication (MFA) telepítése a szervezetben.

Az MFA üzembe helyezési útmutató megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD delegált felügyeleti szerepkör a nyilvános előzetes verzióban érhető el

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Hozzáférés-vezérlés

A rendszergazdák mostantól delegálhatja kezelési feladatokat a globális rendszergazdai szerepkör hozzárendelése nélkül. Az új szerepkörök és funkciók a következők:

- **Új standard szintű Azure AD felügyeleti szerepkörök:**

    - **Alkalmazás-rendszergazda.** Minden alkalmazást, beleértve a regisztráció, egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és a licencelés, alkalmazást proxybeállításokat és jóváhagyás minden aspektusa kezelhető képesek (kivéve az Azure AD-erőforrások).

    - **Felhőalkalmazás-rendszergazda.** Biztosít minden, az alkalmazás-rendszergazda funkcióit, kivéve az alkalmazásproxy, mert ez nem biztosít helyszíni hozzáférés.

    - **Alkalmazás fejlesztője.** Listázását teszi létrehozása alkalmazásregisztrációkat, akkor is, ha a **engedélyezése a felhasználók számára alkalmazások regisztrálása** beállítás ki van kapcsolva.

- **Tulajdonjog (alkalmazásonkénti regisztrációs és enterprise alkalmazás beállítása, a csoport tulajdonjogának folyamat hasonló:**
 
    - **Alkalmazásregisztráció tulajdonosa.** A saját tulajdonú alkalmazásregisztráció, beleértve az alkalmazás-jegyzékfájl, és további tulajdonosok hozzáadása minden aspektusa kezelhető szerepkörök.

    - **Vállalati alkalmazás tulajdonosa.** Szerepkörök a vállalati tulajdonban lévő alkalmazások, beleértve az egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és jóváhagyás számos szempontból kezelése (kivéve az Azure AD-erőforrások).

További információ a nyilvános előzetes verzióban: a [Azure ad-ben delegált felügyelet szerepkör a nyilvános előzetes verzióban érhető el!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. További információ a szerepkörökről és engedélyekről: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute-támogatás módosításai

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Platform  

A szoftver egy szolgáltatott szoftverként, például az Azure Active Directory (Azure AD) úgy tervezték, hogy leginkább végigkövetésével közvetlenül az internethez, anélkül, hogy az expressroute-on vagy bármely más személyes VPN-alagutat. Emiatt a **2018. augusztus 1**, hogy-környezetei már nem az ExpressRoute használatával az Azure nyilvános társviszony-létesítés Azure AD-szolgáltatások és a Microsoft társviszony-létesítés Azure Közösségek. Ez a változás által érintett szolgáltatások előfordulhat, hogy figyelje meg, hogy az Azure AD-forgalom fokozatosan pótlása ExpressRoute-ból az interneten.

Amíg tudjuk módosítani a támogatási azzal is tisztában van vannak továbbra is olyan helyzetekben, ahol szüksége lehet használ egy dedikált Kapcsolatcsoportok a hitelesítési forgalomhoz. Ez az oka Azure ad-ben továbbra is támogatja a bérlőnkénti IP-címtartomány korlátozások használata az ExpressRoute és a szolgáltatások már a Microsoft társviszony-létesítés az "Egyéb Office 365 Online szolgáltatások"-Közösség tagjaival. Ha a szolgáltatásokat érintő eseményekről, de az ExpressRoute van szüksége, tegye a következőket:

- **Ha Ön az Azure nyilvános társviszony-létesítés.** Ugrás a Microsoft társviszony-létesítés, és regisztráljon a **egyéb Office 365-szolgáltatások (12076:5100)** közösségi. Az Azure nyilvános társviszony-létesítésről Microsoft társviszony-létesítésre áthelyezéséről további információ: a [áthelyezni egy nyilvános társviszony-létesítés Microsoft társviszony-létesítésre](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) cikk.

- **Ha Ön a Microsoft társviszony-létesítés.** Regisztráljon a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. Útválasztási követelményeivel kapcsolatos további információkért tekintse meg a [támogatása BGP-Közösségek szakasz](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) az ExpressRoute útválasztási követelményei című cikkben.

Ha folytatja, dedikált Kapcsolatcsoportok használni kell, kommunikáljon a Microsoft Account team való használatához engedélyt kell a **egyéb Office 365 Online szolgáltatás (12076:5100)** közösségi. A MS Office-felügyelt felülvizsgáló Bizottság ellenőrzi-e ezeket a Kapcsolatcsoportok kell, és győződjön meg arról, hogy megszegéseinek műszaki tartja őket. Az Office 365-höz útvonalszűrők létrehozására tett kísérlet nem engedélyezett előfizetések hibaüzenetet fog kapni. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek a használati feltételek a Microsoft Graph API-k

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Fejlesztői környezet
 
Lehetőségekkel bővült a Microsoft Graph API-k az Azure AD – használati felügyeleti működéséhez. Is tudja létesítése, frissítése és a használati feltételek objektum törlése.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Identitás-szolgáltatóként az Azure AD B2C az Azure AD több-bérlős végpont hozzáadása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C
 
Egyéni szabályzatok használatával most már hozzáadhat az Azure AD közös végpont identitásszolgáltatójaként az Azure AD B2C-ben. Ez lehetővé teszi, hogy az összes olyan jelentkezik be az alkalmazások az Azure AD-felhasználó bejegyzés hibaérzékeny pont. További információkért lásd: [Azure Active Directory B2C: Jelentkezzen be egy egyéni szabályzatok használatával több-bérlős Azure AD-identitásszolgáltató felhasználók](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címeket használnak az alkalmazások bárhonnan elérheti az Azure AD-alkalmazásproxy és a saját alkalmazások bejelentkezési bővítménye

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** SSO
 
Felhasználók most már elérheti alkalmazások belső URL-címeket, még ha a vállalati hálózaton kívülről keresztül a saját alkalmazások biztonságos bejelentkezési bővítménye az Azure ad használatával. Ez minden olyan alkalmazással, amely bármely böngészőben, amelyen telepítve van hozzáférési Panel böngészőbővítményének használatánál az Azure AD-alkalmazásproxy használatával közzétett fog működni. Az URL-cím átirányítási funkció automatikusan engedélyezve lesz, miután egy felhasználó bejelentkezik a bővítményt. A bővítmény letölthető [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), és [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Az Azure Active Directory - adatok Európában Európa-ügyfelek számára

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** GoLocal

Ügyfeleknek az Európai szükséges az Európai maradjon, és Európai adatközpontok értekezlet védelmében, és az Európai törvényi kívül nem replikálódnak. Ez [cikk](https://go.microsoft.com/fwlink/?linkid=872328) milyen azonosító információkat a vonatkozó részletes Európa tárolja, és adatokat részleteiért tárolt kívül az Európai adatközpontok biztosítanak. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználók SaaS-alkalmazás Integrációk – 2018. május

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék szolgáltatás:** Külső integráció
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 május hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Alapkövei OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Minden alkalmazás, amely támogatja a felhasználók átadásának az Azure AD katalógusban listáját lásd: [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok Azure AD hozzáférési felülvizsgálatok és alkalmazás-hozzáférés mostantól lehetőséget nyújt az ismétlődő értékelések

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Szabályozás
 
Hozzáférési felülvizsgálat csoportok és alkalmazások már általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák tudják a hozzáférési felülvizsgálatok például havonta vagy negyedévente rendszeres időközönként automatikusan ismétlődni csoporttagságok és alkalmazás-hozzárendelések konfigurálása.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure Active Directory-naplók (bejelentkezési és hitelesítési) mostantól elérhetők az MS Graph használatával

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés
 
Az Azure AD tevékenységeket tartalmazó naplók, amely tartalmazza a bejelentkezések és auditnaplók, mostantól elérhetők az MS Graph használatával. A Microsoft közzétette a két végpontok keresztül az MS Graph ezek a naplók eléréséhez. Tekintse meg a [dokumentumok](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az első lépések az Azure AD Reporting API programozás alapú hozzáférést. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Élmény és a egy szervezeti hagyja a B2B-érvényesítési fejlesztései

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C

**Csak az érvényesítési idő:** Után B2B API-val vendégfelhasználóval megosztott erőforrás – nem kell egy különleges meghívás e-mailt küld. A legtöbb esetben a vendégfelhasználó az erőforrás eléréséhez, és megnyílik a beváltási élmény szerinti keresztül. Nincs több hatással miatt kimaradt e-maileket. Nincs több kell megadniuk a Vendég "Volt, érvényesítési hivatkozásra kattintva a rendszer akkor küld?". Ez azt jelenti, miután SPO a meghívó Managert használja – zavaros mellékleteket is rendelkezhet – belső és külső – bármely érvényesítési állapotát az összes felhasználó azonos kanonikus URL-CÍMÉT.

**A modern érvényesítési feladatok:** Nincs több felosztása képernyő érvényesítési kezdőlap. A modern a felhasználók látják az adatvédelmi nyilatkozat a meghívó szervezetet, felhasználói jóváhagyás, ugyanúgy, mint az ilyen harmadik felek alkalmazásaihoz.

**Vendégfelhasználók a szervezettel hagyhatja:** Ha a felhasználó kapcsolat szervezettel való, azokat is önkiszolgáló a szervezetből. Nincs több történt a meghívó szervezeti admin "el kell távolítani", több ritikus támogatási jegyeket.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. május

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 május tettünk elérhetővé összevonással 18 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozzák, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [a Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montázs Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc-közzététel – egyszeri bejelentkezés ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [állományt](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Az Azure Active Directory új részletes üzembe helyezési útmutatók

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Címtár
 
Új, részletes útmutató Azure Active Directory (Azure AD), beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), üzembe helyezésével kapcsolatos egyszeri bejelentkezést (SSO), a feltételes hozzáférés (CA), -alkalmazásproxyval, a felhasználók átadásához, az Active Directory összevonási szolgáltatások (ADFS) Az átmenő hitelesítés (ESP), és az AD FS a Jelszókivonat-szinkronizálás (nál).

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg a [identitás üzembe helyezési útmutatók](https://aka.ms/DeploymentPlans) adattárat a Githubon. Az üzembe helyezési útmutatók visszajelzést megadásához használja a [központi telepítési csomag visszajelzési űrlap](https://aka.ms/deploymentplanfeedback). Ha az üzembe helyezési útmutatók kapcsolatban bármilyen kérdése van, írjon nekünk az [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése, – további alkalmazások betöltése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO
 
Ha nem találja az alkalmazás / egyszerű szolgáltatások? Hozzáadtunk további alkalmazásokat a vállalati alkalmazások betöltéséhez összes alkalmazások listáját. Alapértelmezés szerint 20 alkalmazás mutatjuk be. Rákattinthat, **Továbbiak betöltése** további alkalmazások megtekintéséhez. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. 

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** AD Connect  
**A termék szolgáltatás:** Identitáséletciklus-menedzsment
 
Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. A kibocsátási megjegyzésekben talál [Itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Az Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazása

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Szabályozás

A hozzáférési felülvizsgálatok a csoportok és alkalmazások most már általánosan elérhetők az Azure AD Premium P2 részeként. Automatikusan a felülvizsgáló módosítások alkalmazásához, csoport vagy alkalmazás, a hozzáférési felülvizsgálatot követően a rendszergazdák konfigurálhatják. A rendszergazda azt is megadhatja, folyamatos hozzáférésre a felhasználó, mi történik, ha a felülvizsgálók nem válaszol, távolítsa el a hozzáférést, férjenek vagy rendszer ajánlásokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Azonosító-jogkivonatokat már nem adható vissza a lekérdezés response_mode használatával új alkalmazások esetében. 

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Be- és 2018. április 25. után létrehozott alkalmazások már nem igényelhetnek egy **id_token** használatával a **lekérdezés** response_mode.  Ekkor az Azure AD beágyazott OIDC előírásoknak, és segítséget nyújt az alkalmazások támadási felület csökkentése.  2018. április 25. előtt létrehozott alkalmazások nincs letiltva a használatával a **lekérdezés** együtt, egy response_type response_mode **id_token**.  A hiba, az aad-ből, id_token kérésekor **AADSTS70007: "query" érték nem támogatott a "response_mode" jogkivonat kérése során**.

A **töredék** és **form_post** response_modes továbbra is működni – Ha létrehozni új alkalmazás objektumok (például az alkalmazásproxy-használat), győződjön meg, hogy ezek response_modes egyikének használata előtt, hozhatnak létre egy Új alkalmazás.  

---
 
## <a name="april-2018"></a>2018. április 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Az Azure AD B2C hozzáférési tokent is általánosan elérhető

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C 

Most már elérheti az Azure AD B2C által védett webes API-k hozzáférési jogkivonatok használatával. A funkció nyilvános előzetes verzióról való általánosan elérhető A felhasználói felület konfigurálása az Azure AD B2C-alkalmazást és a webes API-kat úgy lett továbbfejlesztve, és más kisebb fejlesztések történtek.
 
További információkért lásd: [Azure AD B2C-vel: Hozzáférési jogkivonat kérése](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Egyszeri bejelentkezés beállításainak a SAML-alapú alkalmazások tesztelése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

Egyszeri bejelentkezési SAML-alapú alkalmazások konfigurálása, ha már tudjuk tesztelni az integrációt a lapon. Bejelentkezés során hibát észlel, ha a hibát a tesztelési élményt biztosíthat, és az Azure AD nyújt megoldási lépéseket az adott probléma megoldásához.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD – használati most már rendelkezik felhasználói jelentési

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség
 
A rendszergazdák mostantól egy adott használati feltételek kiválasztása, és tekintse meg az összes felhasználót, hogy hozzájárult, hogy, hogy a jelen használati feltételek és milyen dátum/idő, került sor.

További információkért lásd: a [az Azure AD használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Az AD FS extranetes fiókzárolási védelem kockázatos IP-cím 

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Connect Health mostantól támogatja a képességére IP-címek, amely meghaladja a küszöbértéket hibás felhasználónév/jelszó bejelentkezések óránkénti vagy napi rendszerességgel. Ez a szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés IP-cím és az óránként/naponta alapján testre szabható küszöbértékkel hozza létre a sikertelen bejelentkezések száma.
- E-mail alapú értesítések megjelenítése, ha egy adott IP-cím túllépte a küszöbértéket, hibás felhasználónév/jelszó bejelentkezések egy óránként/naponta történik.
- A letöltési lehetőséget ehhez az adatok részletes elemzés

További információkért lásd: [kockázatos IP jelentés](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás konfigurációs metaadatfájl vagy URL-címe

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

A vállalati alkalmazások oldalon rendszergazdák feltölthet egy SAML-metaadatfájl SAML-alapú bejelentkezés az AAD-galéria és a katalógusban nem szereplő alkalmazás konfigurálásához.

Az Azure AD alkalmazás összevonási metaadatainak URL-címe segítségével ezenkívül a célzott alkalmazás egyszeri bejelentkezés konfigurálása.

További információkért lásd: [konfigurálása egyszeri bejelentkezéshez, az alkalmazásokat, amelyek nem szerepelnek az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD használati feltételek már általánosan elérhető

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség
 

Az Azure AD – használati átkerültek a nyilvános előzetes verziója az általánosan elérhető.

További információkért lásd: a [az Azure AD használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Az adott szervezetek a B2B-felhasználók meghívások engedélyezése vagy letiltása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C
 

Mostantól megadhatja, mely kívánt megosztás és együttműködés az Azure AD B2B együttműködés a fiókpartner-szervezetek. Ehhez hozzon létre adott választhatja engedélyezik vagy megtagadják a tartományok. Tartomány le van tiltva, ezek a képességek használatával, amikor az alkalmazottak már nem küldhet meghívókat személyek ebben a tartományban.

Ez segít, hogy ki férhet hozzá az erőforrásokat, miközben lehetővé teszi a zökkenőmentes felhasználói élmény engedélyezett.

A B2B-együttműködés funkció az összes Azure Active Directory-ügyfelek számára érhető el, és használható együtt a feltételes hozzáférés és az identity protection az Azure AD prémium szintű szolgáltatások pontosabban szabályozhatja a mikor és hogyan külső üzleti felhasználói bejelentkeznek a és hozzáférést szerezzenek.

További információkért lásd: [engedélyezési és blokkolási Segítségkérések B2B-felhasználók az adott szervezetek](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény elérhető új összevont alkalmazások

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció

2018 április tettünk elérhetővé összevonással 13 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

HCM, feltétel [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [titkos kiszolgáló (helyszíni)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [szervezeti diagram Most már](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Teljesítményfigyelő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco felhőalapú](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), kereskedelmi, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások (nyilvános előzetes verzió)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C

Azure Active Directory (Azure AD) B2B együttműködési lehetőségeket az Azure ad a fiókpartner-szervezetek vendégfelhasználók használó szervezetek, most már megadhat a B2B-felhasználók a helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások az SAML-alapú hitelesítés vagy az integrált Windows-hitelesítés (IWA) a Kerberos által korlátozott delegálás (KCD).

További információkért lásd: [Grant B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Egyszeri bejelentkezés az integrációs oktatóanyagok lekérése az Azure Marketplace-ről

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Külső integráció

Ha egy alkalmazás, amely szerepel az a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) támogatja az SAML egyszeri bejelentkezéshez, kattintson a alapú **Letöltés most** biztosít az adott alkalmazáshoz kapcsolódó integrációs oktatóanyagát. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Nagyobb teljesítmény az Azure ad-ben a felhasználók automatikus átadása a SaaS-alkalmazásokban

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék szolgáltatás:** Külső integráció
 
Korábban, az Azure Active Directory-felhasználó kiépítési összekötőket az olyan SaaS-alkalmazások (például Salesforce, ServiceNow és a Box) használó ügyfelek sikerült teljesítménycsökkenést tapasztal, ha az Azure AD-bérlőt tartalmazott több mint 100 000 összevont felhasználók és csoportok, és meghatározni, hogy mely felhasználókat kell létrehozni felhasználó és csoport-hozzárendelések használta.

2018. április 2. fontos teljesítményjavítás üzembe helyezése az Azure AD létesítési szolgáltatás, amely nagy mértékben csökkentheti az Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálás végrehajtásához szükséges idő a.

Ennek eredményeképpen a számos ügyfél, amely eddig szinkronizálások az alkalmazásoknak a tartott, hány nap vagy soha nem fejeződött be, most belül percek vagy órák során hiba.

További információkért lásd: [mi történik a kiépítés során?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Az önkiszolgáló jelszó-visszaállítást a hibrid Azure ad-ben a Windows 10-es zárolási képernyőről csatlakoztatott gépek

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Frissítettük a Windows 10-es SSPR funkció támogatását olyan gépeken, amelyek hibrid Azure AD-hez. Ez a funkció érhető el a Windows 10 RS4 lehetővé teszi, hogy a felhasználók visszaállíthatják a jelszavukat a Windows 10-es gép a zárolási képernyőről. Felhasználók, akik engedélyezve van, és új jelszó önkiszolgáló kérésére regisztrált Ez a szolgáltatás képes használni.

További információkért lásd: [az Azure AD-jelszó visszaállítása a bejelentkezési képernyőről](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március
 
### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítés

**Írja be:** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO
 
Az Azure AD elküld egy értesítést, ha egy gyűjtemény egy tanúsítványt, vagy a katalógusban nem szereplő alkalmazás érvényessége hamarosan lejár. 

Egyes felhasználók nem kapják meg a vállalati alkalmazásokhoz SAML-alapú egyszeri bejelentkezés konfigurálva értesítések. A probléma megoldódott. Az Azure AD elküldi az értesítést a tanúsítványok, 7, 30 és 60 napon belül lejár. Ön ezt az eseményt naplóiban láthatja. 

További információkért lásd:

- [Tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C-vel a Twitter és a GitHub Identitásszolgáltatók

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C
 
Most már hozzáadhat Twitter- és GitHub identitásszolgáltatójaként az Azure AD B2C-ben. Twitter kerül át a nyilvános előzetes verzióban általánosan elérhető GitHub kiadásra nyilvános előzetes verzióban érhető el.

További információkért lásd: [Mi az Azure AD B2B együttműködés?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Az Azure AD-beli alkalmazásalapú feltételes hozzáférési iOS és Android rendszerhez készült Intune Managed Browser használatával böngésző-hozzáférés korlátozása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem
 
**Nyilvános előzetes verziója!**

**Az Intune Managed Browser egyszeri bejelentkezés:** Az alkalmazottak használhatja az egyszeri bejelentkezés natív ügyfelek (például a Microsoft Outlook) és az Intune Managed Browser összes az Azure AD-hez csatlakozó alkalmazást.

**Az Intune Managed Browser feltételes hozzáférést támogató:** Most már megkövetelheti az alkalmazottak az alkalmazásalapú feltételes hozzáférési szabályzatokkal az Intune Managed browser használatára.

További információ erről a [blogbejegyzés](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

További információkért lásd:

- [Alkalmazásalapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Felügyeltböngésző-szabályzatok konfigurálása](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Alkalmazás Webalkalmazásproxy-parancsmagok a Powershell általánosan elérhető a modul

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés
 
Alkalmazásproxy parancsmagok támogatása már általánosan elérhető a Powershell-modult az! Ehhez szükség, hogy legyen naprakész a Powershell-modulok – Ha Ön több mint egy év mögött, egyes parancsmagok leállhat. 

További információkért lásd: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Közvetlen egyszeri bejelentkezés használatával nem interaktív protokoll által támogatott natív Office 365-ügyfelek

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
A felhasználó Office 365-ügyfél natív használatával (verzió 16.0.8730.xxxx és újabb) csendes bejelentkezési felületet közvetlen egyszeri bejelentkezés használatával. A támogatási szolgáltatását az hozzáadását a nem interaktív (WS-Trust) protokoll az Azure AD.

További információkért lásd: [hogyan jelentkezzen be egy natív ügyfél közvetlen egyszeri bejelentkezés munkahelyi?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>A felhasználók kapnak a beavatkozás nélküli bejelentkezést, a közvetlen egyszeri bejelentkezés, ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD-bérlő végpontok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
A felhasználók kapnak a beavatkozás nélküli bejelentkezést, a közvetlen egyszeri bejelentkezés, ha egy alkalmazás (például `https://contoso.sharepoint.com`) bejelentkezési kéréseket küld, az Azure AD-bérlő végpontok – `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` – helyett az Azure AD közös végpontot (`https://login.microsoftonline.com/common/<...>`).

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Közvetlen egyszeri bejelentkezés bevezetése felhasználók Intranet zóna beállítását csak egy Azure ad-ben URL-cím helyett a korábban a két URL-címet hozzá kell adnia

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Közvetlen egyszeri bejelentkezés vezethet be a felhasználók számára, hozzá, csak egy Azure AD URL-CÍMÉT a felhasználók az intranet zóna beállításait az Active Directory csoportházirend használatával kell: `https://autologon.microsoftazuread-sso.com`. Korábban az ügyfelek kellett adjon hozzá két URL-címet.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció

2018 március tettünk elérhetővé összevonással 15 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Segéd által FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech digitális Vállalati kiszolgáló](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu felhőalapú](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Az Azure-erőforrások PIM szolgáltatás általánosan elérhető

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Ha az Azure AD Privileged Identity Management-címtárbeli szerepkörökhöz tartozó használ, mostantól használhatja a PIM időhöz kötött access és a hozzárendelés képességek például az előfizetések, erőforráscsoportok, virtuális gépeket és bármely más, a támogatott erőforrás Azure-erőforrások szerepköreihez tartozó az Azure Resource Manager által. Többtényezős hitelesítés kikényszerítéséhez a Just-In-Time szerepkörök aktiválása során, és jóváhagyott módosítása a windows együttes aktiválások ütemezése. Ez a kiadás emellett bővült a fejlesztések többek között egy frissített felhasználói Felületet, jóváhagyási munkafolyamatokat, valamint a hamarosan lejáró szerepkörök bővítése, és lejárt szerepkörök megújítása a nyilvános előzetes verzióban nem érhető el.

További információkért lásd: [PIM az Azure-erőforrások (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Nem kötelező jogcímek, az alkalmazások jogkivonatok (nyilvános előzetes verzió) hozzáadása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Az Azure AD-alkalmazás mostantól az egyéni vagy választható jogcímkérések JWTs vagy SAML jogkivonatokat.  Ezek a jogcímeket a felhasználó vagy bérlői kapcsolatos, amelyek nem tartoznak a jogkivonat mérete vagy alkalmazhatósági megkötések miatt alapértelmezés szerint.  Ez jelenleg nyilvános előzetes verzióban elérhető az Azure AD-alkalmazások az 1.0-s és 2.0-s verziójú végpontok.  Információ dokumentációjában milyen jogcímek lehet hozzáadni, a tanúsítványkérelmeket az alkalmazásjegyzék szerkesztése.  

További információkért lásd: [nem kötelező az Azure AD-jogcímek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja PKCE biztonságosabb OAuth-folyamatok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Vegye figyelembe a PKCE, amely lehetővé teszi több biztonságos kommunikáció során az OAuth 2.0 engedélyezési kód engedélyezési folyamatával támogatása az Azure AD-docs frissítve lett-e.  Az 1.0-s és 2.0-s verziójú végpontok S256 mind a titkosítatlan szöveges code_challenges támogatottak. 

További információkért lásd: [hozzáférési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Az összes felhasználói attribútum értékei a Workday Get_Workers API-ban elérhető kiépítés támogatása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék szolgáltatás:** Külső integráció
 
A nyilvános előzetes verziója bejövő való üzembe helyezést, a Workday az Active Directory és az most már az Azure AD képes kinyerni és kiépítés a Workday Get_Workers API-ban elérhető összes attribútum értéket támogatja. Ez hozzáadja a több száz további standard szintű csomag támogatja, és tartalmazza a szükséges kezdeti verziójában a Workday túlmutató egyéni attribútumok bejövő összekötő kiépítése.

További információkért lásd: [Workday felhasználói attribútumok listája testre szabható](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A dinamikus csoporttagság statikusra, és fordítva módosítása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés
 
Akkor lehet módosítani egy csoport tagságát kezeléséről. Ez akkor hasznos, ha meg szeretné tartani csoport neve és azonosítója a rendszeren, hogy a csoport bármely meglévő hivatkozások még érvényesek; Új csoport létrehozása miatt frissíteni kellene az ezeket a hivatkozásokat.
Frissítettük az Azure AD felügyeleti központban az funkciót. Most már használó ügyfelek átalakíthatják a meglévő csoportokat a dinamikus tagsági hozzárendelt tagságot és fordítva. A meglévő PowerShell-parancsmagok is továbbra is elérhetők.

További információkért lásd: [statikus, és ez fordítva dinamikus csoporttagság módosítása](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Közvetlen egyszeri bejelentkezés továbbfejlesztett kijelentkezési viselkedés

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Korábban még akkor is, ha a felhasználók explicit módon az Azure AD által védett alkalmazáshoz kijelentkezik, ezek lenne automatikusan megtörténik vissza a közvetlen egyszeri bejelentkezés használatával, ha azok megpróbált hozzáférni egy Azure AD-alkalmazást újra a vállalati hálózaton belül, tartományhoz csatlakoztatott eszközök. Ezzel a kijelentkezési használata támogatott.  Ez lehetővé teszi, hogy a felhasználók kiválaszthatják az azonos vagy eltérő Azure AD-fiók jelentkezzen be újra, alatt automatikusan megtörténik a közvetlen egyszeri bejelentkezés használata helyett.

További információkért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Application Proxy Connector ezen verziója, amely a 1.5.402.0

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Identitásbiztonság és -védelem
 
A connector ezen verziója fokozatosan vezetjük be November keresztül. A connector új verziója az alábbi újításokat tartalmazza:

- Az összekötő már tartományi szintű cookie-k helyett altartomány szintjének beállítása. Ez biztosítja, hogy egy egyenletesebb egyszeri Bejelentkezéses felhasználói élmény, és elkerülhető az redundáns hitelesítési kérések.
- Darabolásos kódolás kérelmek támogatása
- Továbbfejlesztett összekötő állapotfigyelés 
- Számos hibajavítás és stabilitási fejlesztések

További információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>2018. február
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigáció a felhasználók és csoportok kezelése

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Címtárkezelés  
**A termék szolgáltatás:** Címtár

Egyszerűsítettük a navigációt a felhasználók és csoportok kezelése. Akkor most már elhagyhatja a címtár áttekintő közvetlenül, a lista az összes olyan felhasználó, a könnyebb elérhetőség érdekében a törölt felhasználók listájához. Is elérheti a címtár áttekintő a közvetlenül a csoportok, a könnyebb elérhetőség érdekében a csoport felügyeleti beállítások listája. És még a címtár áttekintő lapján kereshet egy felhasználó, csoport, a vállalati alkalmazás vagy alkalmazásregisztráció. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Bejelentkezések és naplózási jelentések a 21Vianet által működtetett Microsoft Azure-ban (Azure China 21Vianet)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Azure Stack  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Az Azure Active Directory naplózási jelentések mostantól elérhetők a 21Vianet (Azure China 21Vianet) példányok által működtetett Microsoft Azure-ban. A következő naplók kapcsolódnak foglalja magában:

- **Bejelentkezési tevékenységeket tartalmazó naplók** – tartalmazza az összes bejelentkezés a bérlő naplóihoz.

- **Önkiszolgáló jelszó-Auditnaplók** – az SSPR auditnaplókkal tartalmazza.

- **Könyvtár-felügyeleti Audit naplók** -naplók mindegyikét tartalmazza a directory felügyelettel kapcsolatos naplózási például a felhasználói felügyeleti, felügyeleti és mások.

Ezek a naplók is, hogy a környezet működésébe elemzéseket kaphat. A megadott adatokkal a következőket teheti:

- Határozza meg, hogyan az alkalmazások és szolgáltatások vannak az egyes a felhasználók számára.

- Végezzen hibaelhárítást a hibákat, amelyek megakadályozzák a felhasználók hozzáférése a munkájukat.

Ezek a jelentések használatával kapcsolatos további információkért lásd: [Azure Active Directory jelentéskészítési](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"A jelentés olvasó" szerepkör (nem rendszergazdai szerepkör) használata az Azure AD-Tevékenységjelentések megtekintése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Tartozó ügyfelek visszajelzés engedélyezése nem rendszergazda szerepkörök férnek hozzá az Azure Active Directory-naplókat, engedélyeztük a lehetőségét, hogy a felhasználók számára a "Jelentés olvasó" szerepkör a bejelentkezések hozzáférési és naplózási tevékenység belül az Azure Portalon, valamint a Graph API-k használatával. 

További információ jelentések, olvassa el [Azure Active Directory jelentéskészítési](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az EmployeeID jogcím felhasználói attribútumként és felhasználói azonosítóként

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO
 
Konfigurálható **EmployeeID** a felhasználói azonosító és a felhasználói attribútum tagfelhasználó és B2B-vendégek a SAML-alapú bejelentkezés alkalmazásokat a vállalati alkalmazás felhasználói felületén.

További információkért lásd: [az Azure Active Directory vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Helyettesítő karakterek használata az Azure AD-alkalmazásproxy egyszerűsített felügyelet

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Alkalmazás központi telepítésének megkönnyíti, valamint csökkentheti a felügyelettel járó többletterhelést, mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételének lehetősége. Helyettesítő karaktert tartalmazó alkalmazás közzététele, hajtsa végre a szabványos alkalmazás-közzétételi folyamat, de használja egy helyettesítő karaktert a belső és külső URL-címeket.

További információkért lásd: [helyettesítő alkalmazásokat az Azure Active Directory application proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfigurálását támogató új parancsmagok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Platform

Az Azure ad PowerShell előzetes modul legújabb kiadása új parancsmagokat, amelyek lehetővé teszik az ügyfelek konfigurálása az Application Proxy alkalmazásai PowerShell-lel tartalmazza.

Az új parancsmagok a következők: 

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
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>A csoportok konfigurálását támogató új parancsmagok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Platform

Az Azure ad PowerShell-modul legújabb kiadása a csoportok kezelése az Azure AD-parancsmagokat tartalmaz. Ezek a parancsmagok korábban az AzureADPreview modulban számára elérhető, és most megjelennek az Azure ad-modul

A csoport parancsmagok, amelyek általánosan elérhető kiadás most a következők: 

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
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Az Azure AD Connect új kiadása érhető el

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
**A termék szolgáltatás:** Platform
 
Az Azure AD Connect az az előnyben részesített eszköz szinkronizálhatja az Azure AD között, és a helyszíni adatforráshoz, beleértve a Windows Server Active Directory és az LDAP adatait.

>[!Important]
>A build bemutatja a séma és a szinkronizálási szabály módosításokat. Az Azure AD Connect szinkronizálási szolgáltatás elindít egy teljes importálást és teljes szinkronizálást lépéseket frissítés után. Ezt a viselkedést módosításának módjával kapcsolatos információkért lásd: [késlelteti a frissítés után teljes szinkronizálást hogyan](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ebben a kiadásban a következő frissítéseket és a változások rendelkezik:

**Rögzített kapcsolatos problémák**

- Javítsa ki a időzítési háttérfeladatok laphoz Partíciószűrés a következő lapra történő váltáskor.

- Kijavítva a hiba, amely engedély nélküli hozzáférési kísérlet során a configdb elemre egyéni művelet okozza.

- Kijavítva a hiba, helyreállítás az sql-kapcsolat időtúllépése.

- Kijavítva a hiba, ahol a SAN helyettesítő karaktereket is tartalmazó tanúsítványok előfeltétel-ellenőrzés sikertelen.

- Kijavítva a hiba, okozó miiserver.exe összeomlási AAD connector exportálás során.

- Kijavítva a hiba, ahol a egy helytelen jelszómegadási kísérlet DC bejelentkezve futtatását okozta az AAD connect varázsló konfigurációjának módosítása

**Új funkciók és fejlesztések**
 
- Alkalmazáshasználati - rendszergazdák válthat Ez az osztály az adatok be-és kikapcsolása.

- Az Azure AD Health-data - rendszergazdák fel kell keresnie azok állapotfigyelő beállításait szabályozhatja a health portálon. Után a szolgáltatás házirend módosítva lett, az ügynökök olvassa el, és azt kényszerítése.

- A jelszóvisszaíró konfigurációs eszközműveletek és se inicializace stránky folyamatjelző hozzáadva.

- A HTML-jelentést és a egy ZIP-szöveg teljes adatgyűjtés általános diagnosztikai továbbfejlesztett / HTML-jelentést.

- Az automatikus megbízhatósága frissítése, és hozzá lehet meghatározni a kiszolgáló állapotának biztosítása érdekében további telemetriai adatokat.

- Kiemelt jogosultságú fiókok AD-összekötő fiókhoz elérhető engedélyek korlátozása. Az új, a varázsló korlátozza az engedélyeket, a kiemelt jogosultságú fiókok tartozik az MSOL-fiók létrehozását követően a MSOL-fiókhoz. A módosítások befolyásolják az Expressz telepítés és a fiók automatikus létrehozása az egyéni telepítés.

- A telepítő, hogy ne követelje meg az aad Connect tiszta telepítését rendszergazdai jogosultság módosítani.

- Egy adott objektumra szinkronizálás hibáinak elhárítása új segédprogramot. Jelenleg a segédprogram ellenőrzi az alábbiakat:

    - Szinkronizált felhasználói objektum és a felhasználói fiók az Azure AD-bérlő a UserPrincipalName nem egyezik.
  
    - Ha az objektum ki lett szűrve a tartomány szűrés miatt
  
    - Ha az objektum ki lett szűrve a szervezeti egység (OU) szűrés miatt

- Új segédprogram az egy adott felhasználói fiók az a helyszíni Active Directoryban tárolt aktuális Jelszókivonat szinkronizálása. A segédprogram nem szükséges a jelszó módosítása. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD-beli alkalmazásalapú feltételes hozzáférés használata a támogató Intune alkalmazásvédelmi szabályzatokat a hozzáadott alkalmazások

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Hozzáadtunk további, alkalmazásalapú feltételes hozzáférést támogató alkalmazások. Most már hozzáférhet az Office 365-höz és más Azure AD-hez csatlakozó felhőalkalmazások ezeket a jóváhagyott ügyfélalkalmazások használatával.

A következő alkalmazások szerint február végétől lesznek hozzáadva:

- Microsoft Power BI

- A Microsoft indítója

- A Microsoft számlázás

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Az Azure AD, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Használati feltételek mobilos felhasználói felülettel kapcsolatos frissítése 

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség

Amikor megjelennek a használati feltételeket, rákattinthat a **megtekintéssel? Kattintson ide a**. A hivatkozásra kattintva megnyílik a használati feltételeket, natív módon az eszközön. A betűméret a dokumentum vagy eszköz a képernyő méretétől függetlenül nagyításra, és igény szerint, olvassa el a dokumentumot. 

---
 
## <a name="january-2018"></a>2018. január
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény 

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció

A 2018 január összevonási támogatással a következő új alkalmazások az alkalmazáskatalógusban lettek hozzáadva:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelmi felügyeleti szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont könyvtárat, és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Jelentkezzen be a további észlelt kockázattal érintett

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Az insight észlelt kockázati eseményekhez kap az Azure AD-előfizetéshez vannak kötve. Az Azure AD Premium P2 kiadás az a legrészletesebb információkat minden mögöttes észlelés beolvasása.

Az Azure AD Premium P1 Edition, amelyek nem tartoznak a licenc tartalomként jelennek meg a bejelentkezési kockázati esemény további észlelt kockázattal érintett.

További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) foglalkozó cikket.
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazásokhoz a felhasználó hozzáférési paneljein elrejtése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** SSO

Most már jobban kezelhető hogyan Office 365-alkalmazások jelennek meg az új felhasználói beállítás segítségével a felhasználók hozzáférési paneljein. Ez a beállítás hasznos lehet a felhasználó hozzáférési paneljein alkalmazások számának csökkentése Ha inkább csak Office-alkalmazások jelennek meg az Office portálon. A beállítás a található a **felhasználói beállítások** és címkével ellátott, **csak a tekintse meg az Office 365 portálon az Office 365-alkalmazások**.

További információkért lásd: [alkalmazás elrejtése a felhasználói élmény az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Zökkenőmentes bejelentkezési engedélyezve a jelszó SSO közvetlenül az alkalmazás URL-alkalmazásokba 

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** SSO

A saját alkalmazások webböngésző-bővítmény már elérhető kényelmes eszköz használatával, amely áttekintést nyújt a saját alkalmazások egyszeri bejelentkezés lehetőségét a parancsikont a böngészőben. Telepítés után a felhasználó a böngészőben, amely az alkalmazások gyors hozzáférést biztosít számukra egy gofri ikon látható. Felhasználók mostantól igénybe veheti:

- Lehetővé teszi a közvetlenül bejelentkezni jelszó SSO-alapú alkalmazások az alkalmazás bejelentkezési lapján
- Indítsa el minden olyan alkalmazást kész keresési funkciója
- A bővítmény a legutóbb használt alkalmazások parancsikonjai
- A bővítmény a Microsoft Edge, Chrome és a Firefox érhető el.
 
További információkért lásd: [saját alkalmazások biztonságos bejelentkezési bővítménye](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élmény a klasszikus Azure portálon kivonása

**Írja be:** Elavult   
**Szolgáltatás kategóriája:** Azure AD  
**A termék szolgáltatás:** Címtár

8 2018 január, az Azure AD felügyeleti-től a klasszikus Azure portál felület kivonásra került. A klasszikus Azure portál maga funkciókészletét együtt történt. A későbbiekben, használjon a [Azure AD felügyeleti központban](https://aad.portal.azure.com) valamennyi a portal-alapú felügyeleti az Azure ad-ben.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor-webportál kivonása

**Írja be:** Elavult  
**Szolgáltatás kategóriája:** Azure AD  
**A termék szolgáltatás:** Címtár
 
2018. január 8. a PhoneFactor-webportál visszavontuk. Az MFA-kiszolgáló felügyeletéhez használt ezen a portálon, de ezekhez a függvényekhez rendelkezik át lett helyezve az Azure Portalon a portal.azure.com webhelyen. 

Az MFA konfigurálása a következő helyen található: **Az Azure Active Directory \> MFA-kiszolgáló**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Írja be:** Elavult  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Identitáséletciklus-menedzsment  


Az általánosan elérhető az új Azure Active Directory felügyeleti konzol és az új API-k már elérhető a tevékenység és a biztonsági jelentések, a jelentéskészítő API-k az a "/ jelentések" végpont kivonásra került, 2017. December 31-ig a végén.

**Mi érhető el?**

Az új felügyeleti konzol az átmenet részeként elérhetővé vált 2 új API-k az Azure AD-Tevékenységnaplók beolvasása. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. Az adatok korábban már elérhető a biztonsági jelentések mostantól elérhető az Identity Protection kockázati események API-t a Microsoft Graph használatával.

További információkért lásd:

- [Az Azure Active Directory reporting API használatának első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Az Azure Active Directory Identity Protection és a Microsoft Graph használatának első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség
 
Most nyissa meg a hozzáférési panelen és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Nyissa meg a [MyApps portálról](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban, válassza ki a nevét, és válassza **profil** a listából. 

3. Az a **profil**válassza **használati feltételek áttekintése**. 

4. Most már megtekintheti a használati feltételeket a elfogadva. 

További információkért lásd: a [az Azure AD használati feltételek funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezési felület

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék szolgáltatás:** Felhasználóhitelesítés
 
Az Azure AD és a Microsoft identitás-fiókrendszer előkészíthetik a korábbinál sokoldalúbban úgy, hogy egy egységes megjelenést és működést. Emellett az Azure AD bejelentkezési oldal gyűjti a felhasználónév először egy második képernyőn a hitelesítő adatok követ.

További információkért lásd: [az új Azure AD bejelentkezési felület jelenleg nyilvános előzetes verzióban elérhető](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérések: Egy új "bejelentkezve szeretnék maradni" tapasztalattal az Azure AD-be

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék szolgáltatás:** Felhasználóhitelesítés
 
A **bejelentkezve szeretnék maradni** váltotta fel egy új parancssort, hogy megjelenik-e sikeres hitelesítést követően az Azure AD bejelentkezési lapján jelölje be jelölőnégyzetet. 

Ha válaszol **Igen** , ez a kérdés a szolgáltatása, amely egy állandó frissítési jogkivonatot. Ez a viselkedés megegyezik a kiválasztott mikor a **bejelentkezve szeretnék maradni** jelölőnégyzetet a régi felületet. Összevont bérlők esetén ez a kérdés mutatja be, miután sikeresen hitelesíteni az összevont szolgáltatással.

További információkért lásd: [kevesebb bejelentkezési kérések: Az Azure ad az új "bejelentkezve szeretnék maradni" funkció jelenleg előzetes verzióban](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>A használati feltételek elfogadása előtt ki kell bővíteni kell a konfiguráció hozzáadása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség
 
Lehetőség a rendszergazdák számára szükséges a felhasználókat a feltételeket, mielőtt elfogadja a használati feltételek megtekintése.

Ezek közül bármelyikre **a** vagy **ki** a felhasználóktól a használati feltételeket, bontsa ki. A **a** beállítás megköveteli a felhasználóktól a használati feltételeket, mielőtt elfogadhatnák őket megtekintéséhez.

További információkért lásd: a [az Azure AD használati feltételek funkció (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Jogosult szerepkör-hozzárendelések hatókörrel rendelkező aktiválás

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Használhatja a hatókörön belüli aktiválási kevesebb, mint az eredeti hozzárendelés alapértelmezett autonómia jogosult Azure-erőforrás szerepkör-hozzárendelések aktiválása. Ilyen például, ha a kapott az előfizetés tulajdonosaként a bérlőben. Hatókörön belüli-aktiválási aktiválhatja a tulajdonosi szerepkör legfeljebb öt erőforrások (például az erőforráscsoportok és virtuális gépek) előfizetésen belül található. Az aktiválás felmerülő előfordulhat, hogy az esélye, kritikus fontosságú Azure-erőforrások nemkívánatos módosítások végrehajtása.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény új összevont alkalmazások

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció

2017 December vezettünk be, ezeket az új összevonási alkalmazásokat az app-galériában támogatja:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager [EFI digitális kirakat](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [RENDSZERKÉP működését](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron az Azure AD-integrációs](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [bambusz feloldási GmbH által a SAML SSO](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO a Bitbucket felbontása GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD-integráció.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial).

Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamatokat

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamat szolgáltatás általánosan elérhető.

Jóváhagyási munkafolyamat, a rendszerjogosultságú szerepkör rendszergazdák megkövetelhetik, szerepkör-aktiválás kéréséhez jogosult-szerepkör tagjai az emelt szintű szerepkör használatához. Több felhasználó és csoport is lehet delegált jóváhagyó felelőssége. Jogosult szerepkör tagjai értesítéseket kaphat, amikor a jóváhagyás befejeződött, és azok szerepét aktív.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Az átmenő hitelesítés: Skype for Business-támogatás

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználóhitelesítés

Átmenő hitelesítés most támogatja a felhasználói bejelentkezéseket a Skype vállalati verzióhoz való ügyfél, amely támogatja a modern hitelesítést, amely tartalmazza az online és a hibrid topológiák. 

További információkért lásd: [Skype for Business topológiákat támogatja a modern hitelesítést használó](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC (előzetes verzió) az Azure AD Privileged Identity Management frissítések

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Az Azure AD Privileged Identity Management (PIM) az Azure szerepköralapú hozzáférés-vezérlés (RBAC) a nyilvános előzetes verzióban frissítéssel az alábbi műveleteket hajthatja végre:

* Ezen éppen elég felügyelettel.
* Erőforrás-szerepkörökkel aktiváláshoz jóváhagyásra van szükség.
* Ütemezzen egy jövőbeli aktiválását egy szerepkör, amely jóváhagyásra van szüksége a két Azure AD és az Azure RBAC-szerepkörökhöz.
 
További információkért lásd: [Privileged Identity Management (előzetes verzió) Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Access Control szolgáltatás kivonása

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Access Control Service szolgáltatást  
**A termék szolgáltatás:** Access Control Service szolgáltatást 

Az Azure Active Directory Access Control (más néven a hozzáférés-vezérlés szolgáltatás) kivezetjük késői 2018-ban. Az elkövetkező néhány hét fog adni egy részletes ütemezés és a magas szintű áttelepítési útmutató tartalmaz további információt. Hagyhatja megjegyzéseket ezen az oldalon bármilyen kérdése van, a hozzáférés-vezérlés szolgáltatással kapcsolatos, és a egy csapattag rájuk válaszolni fog.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune Managed Browser böngésző-hozzáférés korlátozása 

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem

Office 365-höz és más Azure AD-hez kapcsolódó felhőalapú alkalmazáshoz való böngészőalapú hozzáférés egy jóváhagyott alkalmazáshoz, az Intune Managed Browser használatával korlátozhatja. 

Most már konfigurálhat alkalmazásalapú feltételes hozzáférés a következő feltételt:

**Ügyfélalkalmazások:** Böngésző

**Mi az a változás hatását?**

Használja ezt az állapotot még ma, hozzáférés le lesz tiltva. Az előzetes verzió érhető el, ha minden hozzáférést, a managed browser alkalmazást használatára lesz szükség. 

Keresse meg ezt a funkciót, és a közelgő blogok és kibocsátási megjegyzések további információt. 

További információkért lásd: [feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD-beli alkalmazásalapú feltételes hozzáférés – a jóváhagyott ügyfélalkalmazások listája új alkalmazásokkal bővült

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem

A következő alkalmazások olyan listájában [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [A Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Az Azure AD, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek több nyelv támogatása

**Írja be:** Új funkció    
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség

A rendszergazdák most már hozhat létre új használati feltételek, amelyek több PDF-dokumentumok tartalmazzák. Megjelölheti a PDF-dokumentumok egy megfelelő nyelvet. Felhasználók preferenciáik alapján megfelelő nyelven jelennek meg a PDF-fájlt. Ha nem szerepel, az alapértelmezett nyelv jelenik meg.

---
 
### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó-visszaírási ügyfél állapotát

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Új jelszó önkiszolgáló kérése  
**A termék szolgáltatás:** Felhasználóhitelesítés

Most már megtekintheti a helyi jelszó-visszaírási ügyfél állapotát. Ez a beállítás érhető el a **a helyszíni integrációs** szakaszában a [új jelszó kérésére vonatkozó](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lapot. 

Probléma merül fel a helyszíni visszaírási ügyfélhez a kapcsolattal rendelkező, lásd: egy hibaüzenet, amely a következőket tartalmazza:

- Miért nem lehet kapcsolódni a helyszíni visszaírási ügyfélhez információk.
- Dokumentáció, amely segítséget nyújt a problémák elhárításához mutató hivatkozást. 

További információkért lásd: [a helyszíni integrációs](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Az Azure AD, alkalmazásalapú feltételes hozzáférés 
 
**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
**A termék szolgáltatás:** Identitás-biztonság és védelem

Most már korlátozzuk Office 365-höz és más Azure AD-hez csatlakozó-felhőalkalmazások [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , amelyek támogatják az Intune alkalmazásvédelmi szabályzatai segítségével [Azure AD alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Az Intune alkalmazásvédelmi szabályzatai segítségével konfigurálhatja, és ezek ügyfélalkalmazások számára a vállalati adatok védelmét.

Kombinálásával [alkalmazásalapú](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) a [eszközalapú](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) feltételes hozzáférési házirendeket, választhat, az adatok a személyes és vállalati eszközök védelme.

A következő feltételeit és szabályzóit számára érhető el az alkalmazásalapú feltételes hozzáférés használata:

**Támogatott platform feltétel**

- iOS
- Android

**Ügyfél alkalmazások feltétel**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése

További információkért lásd: [Azure AD alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Az Azure Portalon az Azure AD-eszközök kezelése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Az eszköz regisztrálása és kezelése  
**A termék szolgáltatás:** Identitás-biztonság és védelem

Most már megtalálhatja az Azure ad-hez csatlakoztatott eszközök és a egy helyen az eszközzel kapcsolatos tevékenységeket. Nincs új felügyeleti felület kezeli az eszközidentitások és a beállítások az Azure Portalon. Ebben a kiadásban a következőket teheti:

- Megtekintheti az Azure AD feltételes hozzáférés rendelkezésre álló összes eszközre.
- Tekintse meg a tulajdonságokat, többek között a hibrid Azure AD-hez csatlakoztatott eszközök.
- Keresse meg a BitLocker-kulcsok az Azure AD-hez csatlakoztatott eszközök, az eszköz Intune-nal, és további kezelését.
- Az Azure AD-eszközzel kapcsolatos beállítások kezelése.

További információkért lásd: [eszközök kezelése az Azure portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>MacOS-eszköz platformként az Azure AD feltételes hozzáférés támogatása 

**Írja be:** Új funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem 

Most már belefoglalhat (vagy kizárása) MacOS rendszerű eszköz platform feltételeként az Azure AD feltételes hozzáférési házirendben. MacOS-gépeken, a támogatott eszközplatformok igény szerinti hozzáadásával a következőket teheti:

- **Regisztrálhat és kezelhet a macOS-eszközök Intune-nal.** Más platformokon, például iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el a macOS-hez egységes regisztrációk ehhez. Az új céges portál alkalmazás macOS-hez készült segítségével egy eszközöm Intune-nal, és regisztrálja az Azure ad-ben.
- **Győződjön meg arról, macOS-eszközök a vállalat megfelelőségi szabályzatok az Intune-ban definiált formátumhoz.** Az Intune-ban az Azure Portalon mostantól beállíthat is macOS-eszközök megfelelőségi szabályzatainak. 
- **Alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő MacOS rendszerű eszközökhöz az Azure AD-ben.** MacOS feltételes hozzáférési szabályzat szerzői megegyezik egy különálló eszköz platform lehetőséget. Most már hozhat létre macOS-specifikus feltételes hozzáférési szabályzatok beállítása az Azure-ban a megcélzott alkalmazáshoz.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Az Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló kiterjesztése az Azure multi-factor Authentication 

**Írja be:** Új funkció    
**Szolgáltatás kategóriája:**  Multi-Factor Authentication  
**A termék szolgáltatás:** Felhasználóhitelesítés

A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication a hitelesítési infrastruktúra felhőalapú multi-factor Authentication funkcióinak hozzáadja a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló kiterjesztésű telefonhívás, szöveges üzenet vagy telefonos alkalmazás ellenőrzés adhat a meglévő hitelesítési folyamatot. Nem kell telepítése, konfigurálása és új kiszolgálók karbantartása. 

Ez a bővítmény olyan szervezeteknek, amelyek számára védelmet kíván biztosítani virtuális magánhálózati kapcsolatok az Azure multi-factor Authentication-kiszolgáló üzembe helyezése nélkül lett létrehozva. A hálózati házirend-kiszolgáló bővítmény úgy működik, mint az adapter beállításai között, adja meg a hitelesítés második tényezőjét, a RADIUS- és felhőalapú Azure multi-factor Authentication összevont, vagy a szinkronizált felhasználók.

További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy végleg eltávolítani a törölt felhasználók

**Írja be:** Új funkció    
**Szolgáltatás kategóriája:** Felhasználókezelés  
**A termék szolgáltatás:** Címtár 

Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Törölt felhasználói visszaállítása. 
- Felhasználó végleges törlése.

**Kipróbálás:**

1. Az Azure AD felügyeleti központban válassza [minden felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a a **kezelés** szakaszban. 

2. Az a **megjelenítése** listáról válassza ki **nemrégiben törölt felhasználók**. 

3. Válassza ki egy vagy több nemrégiben törölt felhasználók, és vagy visszaállíthatja a őket, vagy véglegesen törli azokat.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD-beli alkalmazásalapú feltételes hozzáférés – a jóváhagyott ügyfélalkalmazások listája új alkalmazásokkal bővült
 
**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem

A következő alkalmazások listájának hozzáadott [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- A Microsoft Planner
- Azure Information Protection 

További információkért lásd:

- [Jóváhagyott alkalmazás megkövetelése](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Az Azure AD, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Használata "Vagy" között a feltételes hozzáférési szabályzat vezérlői 

**Írja be:** Megváltozott funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem
 
Most már használhatja "vagy" (a kijelölt feltételek egyikének megkövetelése) a feltételes hozzáférés-vezérlést. Ez a funkció segítségével szabályzatok létrehozása a "vagy" közötti hozzáférés-vezérlést. Ez a funkció segítségével például olyan szabályzatot, amely egy felhasználó jelentkezhet be a multi-factor Authentication szolgáltatás használata "vagy" a megfelelő eszköz lehet szükséges.

További információkért lásd: [szabályozza az Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Valós idejű kockázati események összesítése

**Írja be:** Megváltozott funkció    
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék szolgáltatás:** Identitás-biztonság és védelem

Az Azure AD Identity Protection az összes olyan valós idejű kockázati események, amelyek az ugyanazon IP-cím származik az adott napon most már az egyes kockázati esemény összesítjük. Ez a változás bármilyen változás nélkül jelenik meg a felhasználó biztonsági kockázati események mennyiségét korlátozza.

Az alapul szolgáló valós idejű észlelését működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha rendelkezik egy olyan bejelentkezési kockázat-biztonsági házirendet, állítsa be a multi-factor Authentication szolgáltatás blokkolja a hozzáférést, továbbra is aktivált minden kockázatos bejelentkezés során.
 
---
 
## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Identitáséletciklus-menedzsment  

Az Azure Portalon a következőket tartalmazza:

- Egy új Azure AD felügyeleti konzolján.
- Új API-k tevékenység és a biztonsági jelentések.
 
Új funkciók miatt a jelentést, a/Reports végpont API-k visszavontuk, 2017. December 10. 

---

### <a name="automatic-sign-in-field-detection"></a>Az automatikus bejelentkezési mezők

**Írja be:** Rögzített   
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** Egyszeri bejelentkezés  

Az Azure AD automatikus bejelentkezési mezők egy HTML-felhasználói név és jelszó mezőt megjelenítő alkalmazások esetében támogatja. Ezeket a lépéseket vannak dokumentálva [automatikusan az alkalmazás bejelentkezési mezők rögzítése](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Adja hozzá ezt a képességet talál egy *katalógusban nem szereplő* alkalmazás a a **vállalati alkalmazások** lapját a [az Azure portal](https://aad.portal.azure.com). Ezenkívül konfigurálhatja a **egyszeri bejelentkezés** módban az új alkalmazás a **jelszóalapú egyszeri bejelentkezés**, adjon meg egy webes URL-címet, és mentse az oldal.
 
Ez a funkció egy szolgáltatási hiba miatt ideiglenesen letiltotta. A probléma megoldódott, és az automatikus bejelentkezési mezők észlelési érhető el újra.

---

### <a name="new-multi-factor-authentication-features"></a>A multi-factor Authentication szolgáltatás új funkciók

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Multi-Factor Authentication  
**A termék szolgáltatás:** Identitás-biztonság és védelem  

Többtényezős hitelesítés (MFA), a szervezet védelme fontos részét képezi. Ahhoz, hogy több adaptív hitelesítő adatokat, és a felhasználói élményt a zökkenőmentes, a következő funkciók lettek hozzáadva: 

- Multi-factor Authentication ellenőrző eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentést, amely programozott hozzáférést biztosít a többtényezős hitelesítés eredménye.
- Az MFA konfigurálása az Azure AD-konfigurációjának több mélyen integrált élmény az Azure Portalon.

A nyilvános előzetes verziója, az MFA felügyeleti és jelentéskészítési az alapvető Azure AD-konfigurációs felület integrált részét képezik. Mostantól felügyelheti a MFA felügyeleti portálon funkciókat nyújt az Azure AD lehetőséget.

További információkért lásd: [többtényezős hitelesítés az Azure Portalon jelentéskészítési referenciája](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Használati feltételek

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Megfelelőség  

Az Azure AD használati feltételek szükséges információkkal, mint például a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatok felhasználók is használhatja.

Az Azure AD használati feltételek az alábbiakhoz használhatja:

- A szervezet minden tagjára vonatkozó általános használati
- Specifikus használati feltételek a felhasználói attribútumok (például orvosi nővérek és) vagy a belföldi és nemzetközi alkalmazottak, dinamikus csoportok által végzett alapján
- Specifikus használati feltételek a nagy hatású üzleti alkalmazások, mint például a Salesforce elérése

További információkért lásd: [az Azure AD használati feltételek](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management fejlesztései

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management  

Az Azure AD Privileged Identity Management kezelheti, irányíthatja, és a szervezet Azure-erőforrások (előzetes verzió) való hozzáférés figyelése:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Összes erőforrást az Azure Portalon az Azure RBAC-funkciókat használó igénybe veheti a biztonsági és az Azure AD Privileged Identity Management által biztosított lehetőségeket alkalmazásélettartam-kezelési képességgel.

További információkért lásd: [Privileged Identity Management az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Megfelelőség  

Szervezetek számára a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelheti a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. Felülvizsgálók-e, hogy a vendégek folyamatos hozzáférést a hozzáférési felülvizsgálatok által nyújtott alapján eldöntését is.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Saját alkalmazások és az Office 365 appindítóján a külső alkalmazások elrejtése

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** Egyszeri bejelentkezés  

Most már jobban kezelhető alkalmazásokat, amelyeket a felhasználók portálokon keresztül egy új megjelenni **alkalmazás elrejtése** tulajdonság. Azokban az esetekben, ahol a Háttérszolgáltatásokhoz vagy ismétlődő csempék és legyen zsúfolt felhasználók alkalmazás kilövők megjelenni alkalmazáscsempék alkalmazások elrejtéséhez. A váltógomb ki van a **tulajdonságok** szakaszában a harmadik féltől származó alkalmazások és feliratú **felhasználó számára látható?** Egy alkalmazás programozás útján PowerShell-lel is elrejthetők. 

További információkért lásd: [egy külső alkalmazás az Azure ad-ben a felhasználói élmény elrejtése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Mi érhető el?**

 Az új felügyeleti konzol, a két új API-kkal való áttérés részeként az Azure Active Directory érhetők el naplók. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. Az adatok korábban már elérhető a biztonsági jelentések mostantól elérhetők az Identity Protection kockázati események API-k a Microsoft Graph.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>A gyorsjavítás az Identity Managerhez

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identity Manager  
**A termék szolgáltatás:** Identitás-életciklus-felügyelete  

2017. szeptember 25, az Identity Manager 2016 Service Pack 1 (build 4.4.1642.0) gyorsjavítás kumulatív csomag érhető el. Ez a kumulatív csomag:

- Oldja fel a problémákat, és fejlesztéseket.
- Az összesítő frissítés, amely lecseréli az összes Identity Manager 2016 Service Pack 1 frissítés akár build 4.4.1459.0 Identity Manager 2016. 
- Megköveteli, hogy rendelkezik az Identity Manager 2016 4.4.1302.0 hozhat létre. 

További információkért lásd: [gyorsjavítás frissítésgyűjtemény (build 4.4.1642.0) érhető el az Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
