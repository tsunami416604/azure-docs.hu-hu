---
title: Archiválja a Azure Active Directory újdonságait? | Microsoft Docs
description: A Mi az új kibocsátási megjegyzések az áttekintésben Ez a szakasz tartalmazza a tevékenység 6 hónap. 6 hónapra elemeket a fő cikkben eltávolítja és archív cikkben üzembe.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4e2e25189b35f7d17ef42536aa7cd86ac92ec82
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185868"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiválja a Azure Active Directory újdonságait?

A [Azure Active Directory legfontosabb újdonságai? a kiadási megjegyzések](whats-new.md) cikke az elmúlt hat hónapban tartalmaz frissítéseket, míg ez a cikk az összes régebbi információt tartalmazza.

A Azure Active Directory újdonságai a kibocsátási megjegyzések a alábbiakról nyújtanak információt:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

---

## <a name="july-2019"></a>2019. július

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Tervezze meg a változást: az Application proxy szolgáltatás frissítése csak a TLS 1,2-et támogatja

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Ha segítségre van szüksége a legerősebb titkosításhoz, megkezdjük az alkalmazásproxy szolgáltatás elérésének korlátozását csak a TLS 1,2 protokollokhoz. Ez a korlátozás kezdetben a TLS 1,2 protokollokat használó ügyfelek számára lesz bevezetve, így a hatás nem fog megjelenni. A TLS 1,0 és a TLS 1,1 protokollok teljes elavulása a 2019. augusztus 31-én fejeződik be. A TLS 1,0-et és a TLS 1,1-t használó ügyfeleink speciális értesítést kapnak a változás előkészítéséhez.

Annak érdekében, hogy az alkalmazásproxy szolgáltatáshoz való csatlakozás megmaradjon a változás során, javasoljuk, hogy az ügyfél-kiszolgáló és a böngészőalapú kiszolgálók kombinációit a TLS 1,2 használatára frissítse. Azt is javasoljuk, hogy minden olyan ügyfélszoftvert tartalmazzon, amelyet az alkalmazottak az alkalmazásproxy szolgáltatáson keresztül közzétett alkalmazások eléréséhez használnak.

További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Tervezze meg a változást: a terv frissítései az alkalmazás-katalógushoz jönnek

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Új felhasználói felületi változások jönnek létre a **Hozzáadás** az **alkalmazás hozzáadása** panel katalógus területéről. Ezek a változások könnyebben megtalálják az alkalmazásokat, amelyek támogatják az automatikus kiépítés, az OpenID Connect, a Security Assertion Markup Language (SAML) és a jelszavas egyszeri bejelentkezést (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Tervezze meg a változást: az MFA-kiszolgáló IP-címének eltávolítása az Office 365 IP-címről

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

A rendszer eltávolítja az MFA-kiszolgáló IP-címét az [Office 365 IP-címe és az URL-webszolgáltatás webszolgáltatásból](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Ha ezekkel az oldalakkal frissíti a tűzfalbeállítások frissítését, meg kell győződnie arról, hogy az Azure Multi-Factor Authentication-kiszolgáló című cikk [első lépéseiben](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) az **Azure multi-Factor Authentication-kiszolgáló tűzfal követelményei** című részben ismertetett IP-címek listáját is tartalmazza.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Az alkalmazáshoz tartozó tokenek mostantól megkövetelik az ügyfélalkalmazás létezését az erőforrás-bérlőben

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

2019. július 26-án módosítjuk, hogy az [ügyfél hitelesítő adataival](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)hogyan biztosítjuk az alkalmazáshoz tartozó jogkivonatokat. Korábban az alkalmazások jogkivonatokat kérhetnek más alkalmazások meghívásához, függetlenül attól, hogy az ügyfélalkalmazás a bérlőben volt-e. Frissítettük ezt a viselkedést, így az egybérlős erőforrásokat – más néven webes API-kat – csak az erőforrás-bérlőben található ügyfélalkalmazások hívhatják meg.

Ha az alkalmazás nem az erőforrás-bérlőben található, a következő hibaüzenet jelenik meg: `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` a probléma megoldásához létre kell hoznia az ügyfélalkalmazás szolgáltatást a bérlőben, a [rendszergazdai hozzájárulási végpont](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) vagy a [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)használatával, amely biztosítja, hogy a bérlő a bérlőn belül működjön az alkalmazás engedélyével.

További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Az ügyfél és az API közötti meglévő beleegyezés továbbra sem szükséges. Az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Új jelszó nélküli bejelentkezés az Azure AD-be az FIDO2 biztonsági kulcsainak használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD-ügyfelek mostantól szabályzatokat állíthatnak be a szervezet felhasználóinak és csoportjainak FIDO2 biztonsági kulcsainak kezeléséhez. A végfelhasználók emellett saját maguk is regisztrálhatják biztonsági kulcsaikat, a kulcsokkal bejelentkezhetnek a Microsoft-fiókjaikat a webhelyekre, miközben a rendszerre képes eszközökön, valamint az Azure AD-hez csatlakoztatott Windows 10-es eszközökön is bejelentkezhetnek.

További információkért lásd: a [jelszó nélküli bejelentkezés engedélyezése az Azure ad-ben (előzetes verzió)](/azure/active-directory/authentication/concept-authentication-passwordless) a rendszergazda által kapcsolatos információkhoz, valamint biztonsági adatok beállítása a végfelhasználókkal kapcsolatos információk [biztonsági kulcsának (előzetes verzió) használatához](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – július 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2019 júliusában az alábbi 18 új alkalmazást bővítettük az alkalmazás-katalógusban található összevonási támogatással:

[Ungerboeck szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [okos minta Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO hozzáférés Ethidex megfelelőségi iroda™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [absztrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [átfedés](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wanda](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), i2B- [kapcsolat](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [beli jfrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Tárcsázz](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont könyvtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>A hálózati biztonsági csoport új Azure AD Domain Services szolgáltatásának címkéje

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Ha nem tudja kezelni az IP-címek és tartományok hosszú listáját, az Azure hálózati biztonsági csoportjában található új **AzureActiveDirectoryDomainServices** hálózati szolgáltatás címkével biztonságossá teheti a Azure ad Domain Services virtuális hálózati alhálózatra irányuló bejövő adatforgalmat.

További információ erről az új szolgáltatási címkéről: [hálózati biztonsági csoportok Azure ad domain Serviceshoz](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segít a hitelesítési szolgáltatásokhoz való kritikus betekintésben, ha a biztonsági naplózási eseményeket a célként megadott erőforrások, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub használatával biztosítja az Azure AD tartományi szolgáltatással portál.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Új hitelesítési módszerek a használat & a bepillantást (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** & Jelentéskészítés figyelése

Az új hitelesítési módszerek használati & a betekintő jelentések segítségével megismerheti, hogy az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási funkció hogyan legyen regisztrálva és használatban a szervezetében, beleértve a regisztrált az egyes szolgáltatásokhoz tartozó felhasználók, hogy milyen gyakran használják az önkiszolgáló jelszó-visszaállítást a jelszavak alaphelyzetbe állítására, és hogy az Alaphelyzetbe állítás milyen módon történik.

További információ: [hitelesítési módszerek használata & információk (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Új biztonsági jelentések érhetők el az összes Azure AD-rendszergazda számára (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Az Azure AD-rendszergazdák mostantól kiválaszthatják a meglévő biztonsági jelentések (például a kockázati jelentésre **megjelölt felhasználók** ) felső részén látható szalagcímet, hogy az új biztonsági élményt használják a **kockázatos felhasználók** és a **kockázatos bejelentkezések** jelentéseiben. Az idő múlásával az összes biztonsági jelentés a régebbi verziókról az új verzióra kerül, és az új jelentések a következő további képességeket biztosítják:

- Speciális szűrés és rendezés

- Tömeges műveletek, például a felhasználói kockázat elvetése

- Sérült vagy biztonságos entitások megerősítése

- Kockázati állapot, amely a következőkre terjed ki: veszélyeztetett, elvetett, szervizelt és megerősített sérült

További információ: [kockázatos felhasználók jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) és [kockázatos bejelentkezések jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segít a hitelesítési szolgáltatásokhoz való kritikus betekintésben, ha a biztonsági naplózási eseményeket a célként megadott erőforrások, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub használatával biztosítja az Azure AD tartományi szolgáltatással portál.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Új B2B közvetlen összevonás SAML/WS-fed használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

A közvetlen összevonás segít megkönnyíteni az olyan partnerekkel való munkavégzést, akik felügyelt identitási megoldását nem az Azure AD, az SAML-vagy WS-fed szabványokat támogató Identity Systems használatával. Miután létrehozott egy közvetlen összevonási kapcsolatot egy partnerrel, az adott tartományból meghívó új vendég-felhasználók a meglévő szervezeti fiókkal dolgozhatnak, így a vendégek zökkenőmentesen használhatják a felhasználói élményt.

További információ: [közvetlen összevonás AD FS és külső szolgáltatókkal a vendég felhasználók számára (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Tárcsázz](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont könyvtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ismétlődő csoportok nevének új keresése az Azure AD-portálon

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Most, amikor létrehoz vagy frissít egy csoportnevet az Azure AD-portálról, akkor ellenőrizze, hogy van-e duplikálva egy meglévő csoportnév az erőforrásban. Ha azt állapítjuk meg, hogy a nevet már egy másik csoport használja, a rendszer megkéri, hogy módosítsa a nevét.

További információ: [csoportok kezelése az Azure ad-portálon](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Az Azure AD mostantól támogatja a statikus lekérdezési paramétereket a válasz (átirányítás) URI-k között.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD-alkalmazások most már regisztrálhatják és használhatják a válasz-(átirányítási) URI-ket statikus lekérdezési paraméterekkel (például `https://contoso.com/oauth2?idp=microsoft`) a OAuth 2,0-kérelmek esetében. A statikus lekérdezési paraméter a válasz URI azonosítójának megfelelő karakterláncra vonatkozik, ugyanúgy, mint a válasz URI más része. Ha nincs olyan regisztrált karakterlánc, amely megfelel az URL-cím-dekódolású átirányítás-URI azonosítónak, a rendszer elutasítja a kérelmet. Ha a válasz URI-ja megtalálható, a rendszer a teljes karakterláncot használja a felhasználó átirányítására, beleértve a statikus lekérdezési paramétert is.

A dinamikus válasz URI-k továbbra is tiltottak, mert biztonsági kockázatot jelentenek, és nem használhatók az állapotadatok megőrzésére a hitelesítési kérelem során. Erre a célra használja a `state` paramétert.

Jelenleg a Azure Portal alkalmazás-regisztrációs képernyői továbbra is letiltják a lekérdezési paramétereket. Az alkalmazás jegyzékfájlját azonban manuálisan is szerkesztheti, ha lekérdezési paramétereket ad hozzá az alkalmazáshoz. További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Az Azure AD-hez készült Tevékenységnaplók (MS Graph API-k) mostantól a PowerShell-parancsmagok használatával érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy az Azure AD-tevékenységek naplói (naplózási és bejelentkezési jelentések) mostantól elérhetők az Azure AD PowerShell-modulon keresztül. Korábban létrehozhat saját parancsfájlokat MS Graph API-végpontok használatával, és most már kiterjesztjük ezt a képességet a PowerShell-parancsmagokra.

További információ a parancsmagok használatáról: [Azure ad PowerShell-parancsmagok jelentéskészítéshez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Frissített szűrési vezérlők az Azure AD naplózási és bejelentkezési naplóihoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Frissítettük a naplózási és bejelentkezési naplózási jelentéseket, így mostantól különböző szűrőket alkalmazhat anélkül, hogy azokat oszlopként kellene hozzáadni a jelentés képernyőjén. Emellett most már eldöntheti, hogy hány szűrőt szeretne megjeleníteni a képernyőn. Ezek a frissítések mind együtt működnek, hogy a jelentések könnyebben olvashatók legyenek, és több hatókört is igénybe lehessen venni.

További információ ezekről a frissítésekről: a [naplózási naplók szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) és [a bejelentkezési tevékenységek szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>2019. június

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API a Microsoft Graphhoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük be, hogy a Microsoft Graph új riskDetections API már nyilvános előzetes verzióban érhető el. Ezzel az új API-val megtekintheti a szervezete személyazonosságának védelmével kapcsolatos felhasználói és bejelentkezési kockázati észlelések listáját. Ezzel az API-val hatékonyabban kérdezheti le a kockázati észleléseket, beleértve az észlelési típussal, az állapottal, a szinttel és egyebekkel kapcsolatos részleteket is.

További információ: [kockázatkezelési API-dokumentáció](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – június 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2019 júniusában a következő 22 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [SKORE](https://app.justskore.it/), [Oracle FELHŐALAPÚ infrastruktúra konzol](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise os](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Kiskereskedelmi merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, a PeopleSoft Oracle IDCS, Oracle IDCS for JD Edwards

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Nagyítás](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Különmegbízottja](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD-kiépítési szolgáltatás valós idejű előrehaladásának megtekintése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Frissítettük az Azure AD üzembe helyezési élményét, hogy szerepeljen egy új folyamatjelző sáv, amely megmutatja, hogy meddig tart a felhasználó kiépítési folyamata. Ez a frissített élmény az aktuális ciklusban kiosztott felhasználók számával, valamint a dátummal rendelkező felhasználók számával kapcsolatos információkat is tartalmazza.

További információ: [a felhasználó kiépítési állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A céges arculat mostantól megjelenik a kijelentkezés és a hibaüzenetek képernyőjén

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Frissítettük az Azure AD-t, hogy a cég arculata mostantól megjelenjen a kijelentkezés és a hibák képernyőjén, valamint a bejelentkezési oldalon. A funkció bekapcsolásához semmit nem kell tennie, az Azure AD egyszerűen azokat az eszközöket használja, amelyeket már beállított a Azure Portal **vállalati védjegyezés** területén.

A vállalat arculatának beállításával kapcsolatos további információkért lásd: [branding hozzáadása a szervezet Azure Active Directory oldalaihoz](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló már nem érhető el az új üzemelő példányokhoz

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. A többtényezős hitelesítést a szervezetében megkövetelő új ügyfeleknek most már a felhőalapú Azure-Multi-Factor Authentication kell használniuk. Az MFA-kiszolgálót július 1. előtt aktivált ügyfelek nem fogják látni a változást. Továbbra is letöltheti a legújabb verziót, lekérheti a jövőbeli frissítéseket, és előállíthatja az aktiválási hitelesítő adatokat.

További információ: [Bevezetés az Azure multi-Factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)használatába. További információ a felhőalapú Azure-Multi-Factor Authenticationről: [felhőalapú Azure-beli multi-Factor Authentication üzembe helyezésének tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Szolgáltatás módosítása: az alkalmazásproxy szolgáltatásban csak a TLS 1,2 protokollok jövőbeli támogatása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

A legjobb titkosítás biztosítása érdekében ügyfeleinknek biztosítjuk, hogy csak a TLS 1,2 protokollokra korlátozza a hozzáférést az alkalmazásproxy szolgáltatásban. Ezt a változást fokozatosan a TLS 1,2 protokollokat használó ügyfeleknek kell bevezetni, így nem kell semmilyen változást látnia.

A TLS 1,0 és a TLS 1,1 elavult változata a 2019-as augusztus 31-én történik, de további speciális értesítéseket is biztosítunk, így a változás előkészítéséhez időt kell igénybe vennie. A módosítás előkészítéséhez győződjön meg arról, hogy az ügyfél és a böngésző kiszolgálói kombinációja, beleértve azokat az ügyfeleket, amelyeket a felhasználók az Application proxyn keresztül közzétett alkalmazások elérésére használnak, frissülnek a TLS 1,2 protokoll használatával az alkalmazással létesített kapcsolat fenntartásához. Proxy szolgáltatás. További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Az alkalmazással kapcsolatos bejelentkezési adatok megtekintéséhez használja a használati és elemzési jelentést.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** & Jelentéskészítés figyelése

Mostantól használhatja a Azure Portal **vállalati alkalmazások** területén található használati és elemzési jelentést a bejelentkezési adatok alkalmazás-központú nézetének beszerzéséhez, beleértve a következőkkel kapcsolatos információkat:

- A szervezet leggyakrabban használt alkalmazásai

- A legtöbb sikertelen bejelentkezést tartalmazó alkalmazások

- Leggyakoribb bejelentkezési hibák az egyes alkalmazásokhoz

A szolgáltatással kapcsolatos további információkért tekintse [meg a használati és adatáttekintési jelentést a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>A felhasználók üzembe helyezésének automatizálása a Cloud apps szolgáltatásban az Azure AD használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** & Jelentéskészítés figyelése

Ezeket az új oktatóanyagokat követve az Azure AD kiépítési szolgáltatásával automatizálhatja a következő felhőalapú alkalmazások felhasználói fiókjainak létrehozását, törlését és frissítését:

- [Megfelelés](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Ezt az új [Dropbox-oktatóanyagot](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)is követheti, amely a csoport objektumainak kiépítésével kapcsolatos információkat tartalmaz.

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Az identitások biztonságos pontszáma mostantól elérhető az Azure AD-ben (általános elérhetőség)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Identitás-biztonsági & védelme

Most már nyomon követheti és javíthatja az identitás biztonsági állapotát az Azure AD biztonságos pontszám funkciója segítségével. Az Identitáskezelés biztonságos pontszáma funkció egyetlen irányítópultot használ, amely a következőket nyújtja:

- Az azonosító biztonsági helyzet tárgyilagos mérése az 1 és 223 közötti pontszám alapján.

- Tervezze meg az identitás biztonsági frissítéseit

- Tekintse át a biztonsági tökéletesítések sikerességét

További információ a személyazonossági biztonsági pontszám funkcióról: [Mi az Identity Secure pontszám a Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Mostantól elérhető az új Alkalmazásregisztrációki élmény (általános rendelkezésre állás)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Fejlesztői élmény

Az új [Alkalmazásregisztrációk](https://aka.ms/appregistrations) élmény már általánosan elérhető. Ez az új felhasználói élmény magában foglalja a Azure Portal és az alkalmazás regisztrációs portáljának összes fontos funkcióját, és a következőkön keresztül fejleszti őket:

- **Hatékonyabb alkalmazások kezelése.** Az alkalmazások különböző portálokon való megjelenítése helyett mostantól egyetlen helyen láthatja az összes alkalmazást.

- **Egyszerűsített alkalmazás regisztrálása.** A továbbfejlesztett navigációs felületről a kijavított engedélyek kiválasztásának folyamata révén könnyebben regisztrálhatók és kezelhetők az alkalmazások.

- **Részletesebb információ.** Az alkalmazással kapcsolatos további részleteket, például a gyors útmutatókat és egyebeket is megtalálhatja.

További információ: a [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) és a [Alkalmazásregisztrációk-élmény már általánosan elérhető!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog bejelentése.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>A kockázatos felhasználók API-ban a személyazonosság védelme érdekében elérhető új képességek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük, hogy mostantól a kockázatos felhasználók API-val lekérheti a felhasználók kockázati előzményeit, elhagyhatja a kockázatos felhasználókat, és megerősítheti a felhasználókat a feltörtek szerint. Ezzel a módosítással hatékonyabban frissítheti a felhasználók kockázati állapotát, és megismerheti a kockázati Előzményeiket.

További információkért tekintse meg a [kockázatos felhasználók API-referenciájának dokumentációját](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások – 2019. május

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

A 2019-es verzióban a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [valódi hivatkozások](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [egyszerű aláírás](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [forrasztás](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [lejátszó](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo értékesítések](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [rendszerek](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globális HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum munkahely](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API-felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [vezérlés](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Továbbfejlesztett csoportok létrehozása és kezelése az Azure AD-portálon

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Továbbfejlesztettük az Azure AD-portálon a csoportokkal kapcsolatos tapasztalatokat. Ezen fejlesztésekkel a rendszergazdák hatékonyabban kezelhetik a csoportok listáját, a tagok listáját, és további létrehozási lehetőségeket biztosíthatnak.

Fejlesztések a következők:

- Alapszintű szűrés a tagság típusa és a csoport típusa alapján.

- Új oszlopok, például forrás-és e-mail-cím hozzáadása.

- A csoportok, a tagok és a tulajdonosi listák többszörös kiválasztásának lehetősége az egyszerű törléshez.

- Lehetőség van e-mail-cím kiválasztására és a tulajdonosok hozzáadására a csoportok létrehozása során.

További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (általánosan elérhető)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>A Microsoft Graph API-végpontok mostantól elérhetők az Azure AD-tevékenységek naplóihoz (általános elérhetőség)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy Microsoft Graph API-végpontok támogatásának általános elérhetősége az Azure AD-tevékenységek naplóiban. Ebben a kiadásban mostantól az Azure AD-naplók 1,0-es verzióját, valamint a bejelentkezési naplók API-jait is használhatja.

További információ: az [Azure ad naplózási naplójának API áttekintése](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák mostantól feltételes hozzáférést is használhatnak a kombinált regisztrációs folyamathoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme  

A rendszergazdák mostantól feltételes hozzáférési szabályzatokat hozhatnak létre a kombinált regisztrációs lap általi használatra. Ez magában foglalja a szabályzatok alkalmazását a regisztráció engedélyezéséhez, ha:

- A felhasználók megbízható hálózaton vannak.

- A felhasználók alacsony bejelentkezési kockázatot jelentenek.

- A felhasználók felügyelt eszközön találhatók.

- A felhasználók elfogadják a szervezet használati feltételeit (felhasználási feltételek).

A feltételes hozzáféréssel és a jelszó-visszaállítással kapcsolatos további információkért tekintse meg az [Azure ad kombinált MFA és a jelszó-változtatási regisztrációs élmény blogbejegyzésének feltételes hozzáférését](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). A kombinált regisztrációs folyamat feltételes hozzáférési házirendjeivel kapcsolatos további információkért lásd a [feltételes hozzáférési szabályzatok a kombinált regisztrációhoz](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)című témakört. További információ az Azure AD használati feltételeiről: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>2019. április

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD veszélyforrások felderítésének új észlelése mostantól a Azure AD Identity Protection részeként érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Az Azure AD Threat Intelligence észlelése mostantól a frissített Azure AD Identity Protection funkció részeként érhető el. Ez az új funkció lehetővé teszi, hogy szokatlan felhasználói tevékenységet jelezzen egy adott felhasználó vagy tevékenység számára, amely konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásai alapján ismert támadási mintákkal.

A Azure AD Identity Protection frissített verziójával kapcsolatos további információkért tekintse meg a [négy jelentős Azure ad Identity Protection fejlesztést a nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a mi az [Azure Active Directory Identity Protection (frissítve)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) cikk. Az Azure AD veszélyforrások észlelésével kapcsolatos további információkért tekintse meg a [Azure Active Directory Identity Protection kockázati észlelésekkel](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) foglalkozó cikket.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD-jogosultságok kezelése mostantól elérhető (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identitás-szabályozás  
A **termék képességei:** Identitás-szabályozás

A nyilvános előzetes verzióban elérhető Azure AD-jogosultsági felügyelet segít az ügyfeleknek a hozzáférési csomagok felügyeletének delegálásában, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan igényelhetnek hozzáférést, jóvá kell hagynia, és hogy mennyi ideig férhetnek hozzá. A hozzáférési csomagok kezelhetik az Azure AD és az Office 365-csoportok tagságát, a vállalati alkalmazásokban lévő szerepkör-hozzárendeléseket, valamint a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. További információ a jogosultságok kezeléséről az [Azure ad-jogosultságok kezelésének áttekintése című](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)témakörben olvasható. Ha többet szeretne megtudni a Azure AD Identity Governance-funkciók széles köréről, beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatokat és a használati feltételeket, tekintse meg a [Mi az Azure ad Identity Governance?](../governance/identity-governance-overview.md)című témakört.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-Tevékenységnaplók mostantól Azure Monitorban érhetők el (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-tevékenység naplóival kapcsolatos vizualizációk visszajelzésének megkönnyítése érdekében a Log Analytics új elemzési funkciót vezetünk be. Ez a funkció segít az Azure AD-erőforrásokkal kapcsolatos elemzések megszerzésében interaktív sablonjaink használatával, az úgynevezett munkafüzeteknél. Ezek az előre elkészített munkafüzetek részletes adatokat biztosíthatnak az alkalmazásokhoz és a felhasználókhoz, és a következőket tartalmazzák:

- **Bejelentkezések.** Részletes információkat nyújt az alkalmazásokról és a felhasználókról, beleértve a bejelentkezési helyet, a használatban lévő operációs rendszert vagy böngésző-ügyfelet és-verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Örökölt hitelesítés és feltételes hozzáférés.** A régi hitelesítést használó alkalmazások és felhasználók részletes adatait tartalmazza, beleértve a feltételes hozzáférési házirendek által aktivált, feltételes hozzáférési házirendeket használó alkalmazások és így tovább Multi-Factor Authentication használatát.

- **Sikertelen bejelentkezések elemzése.** Segít megállapítani, hogy a bejelentkezési hibák egy felhasználói művelet, házirend-problémák vagy az infrastruktúra miatt történnek-e.

- **Egyéni jelentések.** Létrehozhat új, vagy szerkesztheti a meglévő munkafüzeteket, amelyek segítségével testre szabhatja a szervezete által használt adatellenőrzési funkciót.

További információ: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – április 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Április 2019-én a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [átszivárog](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [padok](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), PageDNA, EduBrite [](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), RStudio- [kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)Mitel- [kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (szerepköralapú SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), Sectigo [Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakoriságának beállítása és több szerepkör kiválasztása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás

Az Azure AD hozzáférési felülvizsgálatok új frissítései a következőket teszik lehetővé:

- A korábbi, heti, havi, negyedéves és évente meglévő lehetőségeken felül a hozzáférési felülvizsgálatok gyakoriságát **is módosíthatja**félévente.

- Egyetlen hozzáférési felülvizsgálat létrehozásakor több Azure AD-és Azure-erőforrás-szerepkört is kijelölhet. Ebben az esetben minden szerepkör ugyanazokkal a beállításokkal van beállítva, és minden felülvizsgáló értesítést kap egyszerre.

A hozzáférési felülvizsgálat létrehozásával kapcsolatos további információkért lásd: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)felülvizsgálatokban.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail-riasztási rendszer (ek) át lettek irányítva, és minden ügyfél számára új e-mail feladói adatokat küldenek

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform

Azure AD Connect az e-mail-riasztási rendszer (ek) átváltásának folyamata, amely egy új e-mail-feladót mutat be. Ennek megoldásához hozzá kell adnia `azure-noreply@microsoft.com` a szervezet engedélyezési listájához, vagy nem fogja tudni fogadni a fontos riasztásokat az Office 365, az Azure vagy a Sync Services szolgáltatásból.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN-utótag módosításai mostantól sikeresek az összevont tartományok között Azure AD Connect

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform

Most már sikeresen módosíthatja egy felhasználó UPN-utótagját az egyik összevont tartományból egy másik összevont tartományba Azure AD Connectban. Ez a javítás azt jelenti, hogy a szinkronizálási ciklusban többé nem tapasztalja a FederatedDomainChangeError hibaüzenetet, vagy értesítést kap a következőről: "nem lehet frissíteni ezt az objektumot Azure Active Directory, mert az attribútum [ A FederatedUser. UserPrincipalName] érvénytelen. Frissítse az értéket a helyi címtárszolgáltatások szolgáltatásban.

További információ: [hibaelhárítási hibák a szinkronizálás során](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az App Protection-alapú feltételes hozzáférési szabályzattal az Azure AD-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az App Protection-alapú feltételes hozzáférés mostantól az alkalmazás- **védelmi házirend megkövetelése** eszközzel érhető el. Ez az új szabályzat segít a szervezet biztonságának növelésében azáltal, hogy megakadályozza a következőket:

- A felhasználók Microsoft Intune licenc nélkül férhetnek hozzá az alkalmazásokhoz.

- A felhasználók nem tudnak Microsoft Intune app Protection-szabályzatot beolvasni.

- A felhasználók a konfigurált Microsoft Intune app Protection-házirend nélkül szereznek hozzáférést az alkalmazásokhoz.

További információkért lásd: [alkalmazás-védelmi szabályzat megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez a Microsoft Edge-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Bővítettük a Microsoft Edge-hez készült Azure AD-támogatást, amely új támogatást nyújt az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez. Ha korábban már használta Microsoft Intune Managed Browser, a Microsoft Edge-t használhatja helyette.

Az eszközök és alkalmazások feltételes hozzáférés használatával történő beállításával és kezelésével kapcsolatos további információkért lásd: [felügyelt eszközök megkövetelése a felhőalapú alkalmazásokhoz való hozzáféréshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) , valamint [jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). További információ arról, hogyan kezelhető a hozzáférés a Microsoft Edge használatával Microsoft Intune házirendekkel: az [Internet-hozzáférés kezelése Microsoft Intune házirenddel védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Az identitási élmény keretrendszere és az egyéni szabályzatok támogatása Azure Active Directory B2C már elérhető (GA)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Mostantól létrehozhat egyéni házirendeket Azure AD B2Cban, beleértve a következő feladatokat, amelyek az Azure SLA-ban támogatottak:

- Egyéni hitelesítési felhasználói útvonalak létrehozása és feltöltése egyéni szabályzatok használatával.

- A felhasználói útvonalak lépésenkénti ismertetése a jogcímek szolgáltatói közötti cseréjeként.

- Feltételes elágazás definiálása felhasználói útvonalakon.

- A jogcímek átalakítása és leképezése valós idejű döntésekben és kommunikációkban való használatra.

- Az egyéni hitelesítési felhasználói útvonalakon REST API-kompatibilis szolgáltatásokat használhat. Például az e-mail-szolgáltatókkal, a CRM és a saját engedélyezési rendszerekkel.

- A összevonása az OpenIDConnect protokollnak megfelelő identitás-szolgáltatókkal rendelkeznek. Például több-bérlős Azure AD-vel, közösségi fiókkal vagy kétfaktoros ellenőrző szolgáltatókkal.

Az egyéni szabályzatok létrehozásával kapcsolatos további információkért tekintse meg az [Egyéni házirendek fejlesztői megjegyzéseit Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) és olvassa el [Alex Simon blogbejegyzését, beleértve az esettanulmányokat is](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – március 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Március 2019-án a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[ISEC7 Mobile Exchange-delegált](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [magyarázat-alapú naplózási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [sovány](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool teljesítményű ügyek](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [írisz intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizont](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler-és Atlassian-létesítési összekötők az Azure AD-galériában – március 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció

A következő alkalmazásokhoz tartozó felhasználói fiókok létrehozásának, frissítésének és törlésének automatizálása:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler három](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://aka.ms/ProvisioningDocumentation)című témakört.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Törölt Office 365-csoportok visszaállítása és kezelése az Azure AD-portálon

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD-portálon. Ez a módosítás segít megtekinteni, hogy mely csoportok állíthatók vissza a visszaállításhoz, és hogy véglegesen törli azokat a csoportokat, amelyek nem szükségesek a szervezet számára.

További információ: [lejárt vagy törölt csoportok visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Az egyszeri bejelentkezés mostantól elérhető az Azure AD SAML-védelemmel ellátott helyszíni alkalmazásokhoz az Application proxyn keresztül (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Mostantól egyszeri bejelentkezéses (SSO) élményt biztosíthat a helyszíni, SAML-hitelesítésen alapuló alkalmazásokhoz, valamint az alkalmazások távoli elérését az Application proxyn keresztül. Az SAML SSO helyszíni alkalmazásokkal való beállításával kapcsolatos további információkért lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)használatával.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A megbízhatóság és a felhasználói élmény fokozása érdekében a kérelemben szereplő ügyfélalkalmazások megszakadnak.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az ügyfélalkalmazások nem tudják megfelelően kiadni több száz azonos bejelentkezési kérést rövid idő alatt. Ezek a kérések, akár sikeresek, akár nem, mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.

Ez a frissítés `invalid_grant` hibát küld: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` olyan ügyfélalkalmazások számára, amelyek rövid idő alatt többször is kiadják a duplikált kérelmeket, a normál működés hatókörén kívül. A problémát tapasztaló ügyfélalkalmazások interaktív üzenetet jelenítenek meg, amely megköveteli, hogy a felhasználó újra bejelentkezzen. További információ erről a változásról, valamint arról, hogy az alkalmazás Hogyan oldható meg, ha ez a hiba ütközik: Újdonságok [a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új naplózási naplók – felhasználói élmény most elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Létrehoztunk egy új Azure AD- **naplókat** tartalmazó oldalt, amely segít az olvashatóság és az információk megkeresésének javításában. Ha meg szeretné tekinteni **az új naplók** lapot, válassza a **naplók** lehetőséget az Azure ad **tevékenység** szakaszában.

![Új naplózási naplók oldal, a minta adataival](media/whats-new/audit-logs-page.png)

További információ az új **naplók** lapról: [naplózási tevékenység jelentései a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatás a nem megfelelően konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazdai zárolásának megakadályozásához

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Annak megakadályozása érdekében, hogy a rendszergazdák véletlenül zárolják magukat a saját bérlőik nem megfelelően konfigurált feltételes hozzáférési szabályzatokkal, új figyelmeztetéseket hoztunk létre, és frissítettük az útmutatót a Azure Portal. Az új útmutatással kapcsolatos további információkért lásd: [Mi a szolgáltatás függőségei a Azure Active Directory feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Jobb végfelhasználói használati feltételek a mobileszközök esetében

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

Frissítettük a meglévő használati feltételeket, amelyek segítségével javítható a mobileszközök használati feltételeinek áttekintése és belefoglalása. Most nagyíthatja és kibővítheti, visszatérhet, letöltheti az adatokat, és kiválaszthatja a hiperhivatkozásokat. A frissített használati feltételekkel kapcsolatos további információkért lásd: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Az Azure AD-beli új tevékenység naplóinak letöltési felülete elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Most már nagy mennyiségű tevékenységet is letölthet közvetlenül a Azure Portalból. Ez a frissítés a következőket teszi lehetővé:

- Legfeljebb 250 000 sort tölthet le.

- Értesítést kap a letöltés befejeződése után.

- Szabja testre a fájlnevet.

- Határozza meg a kimeneti formátumot JSON-ként vagy CSV-ként.

További információ erről a szolgáltatásról: gyors útmutató [: naplózási jelentés letöltése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Változás megszakítása: az Exchange ActiveSync (EAS) által a feltételek kiértékelésének frissítései

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Access Control

Folyamatban van az Exchange ActiveSync (EAS) az alábbi feltételek kiértékelésének frissítése:

- Felhasználói hely, ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszköz platformja

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési házirendekben, vegye figyelembe, hogy a feltétel viselkedése változhat. Ha például korábban a felhasználói hely feltételét használta egy házirendben, akkor a rendszer a felhasználó helye alapján kihagyhatja a házirendet.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható Azure AD SAML-jogkivonat titkosítása (nyilvános előzetes verzió) 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Mostantól a támogatott SAML-alkalmazások is konfigurálhatók titkosított SAML-tokenek fogadására. Az Azure AD az Azure AD-ben tárolt tanúsítványból beszerzett nyilvános kulccsal titkosítja a kibocsátott SAML-kijelentéseket, ha az alkalmazás konfigurálva van és használatban van.

Az SAML-jogkivonat titkosításának konfigurálásával kapcsolatos további információkért lásd: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Hozzáférési felülvizsgálat létrehozása csoportok vagy alkalmazások számára az Azure AD hozzáférési felülvizsgálatok használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási

Mostantól több csoportot vagy alkalmazást is hozzáadhat egyetlen Azure AD-hozzáférési felülvizsgálatban csoporttagság vagy alkalmazás-hozzárendelés esetén. A különböző csoportokkal vagy alkalmazásokkal való hozzáférési felülvizsgálatok ugyanazokkal a beállításokkal vannak beállítva, és az összes belefoglalt felülvizsgálók egyszerre vannak bejelentve.

További információ az Azure AD hozzáférési felülvizsgálatok használatával történő hozzáférési felülvizsgálat létrehozásáról: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) felülvizsgálatokban

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – február 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Február 2019-án az alábbi 27 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [ég felé Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), engedély kattintás, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile, Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [, Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [szeizmikus](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [az álom megosztása](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webmódszerek integrációs felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [ismeret Bárhol az LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [periszkóp](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)-és [NetOp-portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp hatékonyságnövelő platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Bővített összetett MFA/SSPR-regisztráció

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Az ügyfelek visszajelzései alapján továbbfejlesztettük a kombinált MFA/SSPR regisztráció előzetes verzióját, így a felhasználók gyorsabban regisztrálhatják biztonsági adataikat mind az MFA-, mind a SSPR. 

**Az alábbi lépéseket követve kapcsolja be a felhasználók számára a továbbfejlesztett felhasználói élményt:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be a Azure Portalba, és lépjen **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése**lehetőségre. 

2. Azokon a **felhasználóknál, akik használhatják az előzetes verziójú funkciókat a biztonsági adatok regisztrálásához és kezeléséhez – frissítés** lehetőség, válassza a **kiválasztott felhasználók** vagy az **összes felhasználó**funkcióinak bekapcsolását.

A következő hetekben eltávolítjuk a régi, kombinált MFA/SSPR regisztráció előzetes verzióját azon bérlők esetében, amelyeknek még nincs bekapcsolva.

**Az alábbi lépéseket követve ellenőrizheti, hogy a vezérlő el lesz-e távolítva a bérlőhöz:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be a Azure Portalba, és lépjen **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése**lehetőségre.  

2. Ha a **biztonsági adatok regisztrálására és kezelésére szolgáló előzetes verziójú funkciókat használó felhasználók** **nem értékre vannak állítva, akkor**a rendszer eltávolítja a lehetőséget a bérlőről.

Függetlenül attól, hogy korábban bekapcsolta-e a régi, kombinált MFA/SSPR regisztráció előzetes verzióját a felhasználók számára, vagy sem, a régi élmény jövőbeli időpontban ki lesz kapcsolva. Ezért azt javasoljuk, hogy a lehető leghamarabb váltson az új, továbbfejlesztett élményre.

A továbbfejlesztett regisztrációs felülettel kapcsolatos további információkért tekintse [meg az Azure ad kombinált MFA-és jelszó-visszaállítási regisztrációs felületének gyakori fejlesztéseit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Frissített házirend-kezelési élmény a felhasználói folyamatokhoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A szabályzat létrehozásának és felügyeletének folyamatát frissítettük a felhasználói folyamatok (korábbi nevén a beépített házirendek) egyszerűbbé tétele érdekében. Ez az új felhasználói élmény mostantól az összes Azure AD-bérlő alapértelmezett alapszolgáltatása.

A portál képernyő felső részén található a **visszajelzés küldése** terület a mosoly vagy a komor ikon használatával további visszajelzést és javaslatokat is megadhat.

További információ az új házirend-kezelési feladatokról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki a Azure AD B2C által biztosított adott oldal elemeinek verzióját.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Mostantól kiválaszthatja a Azure AD B2C által biztosított oldal elemeinek egy adott verzióját. Egy adott verzió kiválasztásával tesztelheti a frissítéseket, mielőtt megjelennek az oldalon, és kiszámítható működést érhet el. Emellett a JavaScript-testreszabások engedélyezéséhez is engedélyezheti, hogy bizonyos lapokat is kikényszerítse. A funkció bekapcsolásához nyissa meg a felhasználói folyamatok **Tulajdonságok** lapját.

További információ az oldalelemek adott verzióinak kiválasztásáról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>A B2C (GA) konfigurálható végfelhasználói jelszavának követelményei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Most már beállíthatja a szervezete jelszavának összetettségét a végfelhasználók számára, ahelyett, hogy a natív Azure AD-beli jelszavas szabályzatot kellene használnia. A felhasználói folyamatok (korábbi nevén beépített szabályzatok) **Tulajdonságok** paneljén kiválaszthatja az **egyszerű** vagy **erős**jelszó bonyolultságát, vagy létrehozhat egy **Egyéni** követelményt is.

A jelszó-összetettségi követelmények konfigurálásával kapcsolatos további információkért lásd: az [összetettségi követelmények konfigurálása a jelszavakhoz a Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új alapértelmezett sablonok az egyéni márkás hitelesítési élményekhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Az új alapértelmezett sablonokat a felhasználói folyamatok (korábbi nevén beépített szabályzatok) **lap elrendezések** paneljén is használhatja, hogy egyéni márkás hitelesítési élményt hozzon létre a felhasználók számára.

További információ a sablonok használatáról: [Azure ad B2C most már rendelkezik JavaScript-testreszabással és számos további új funkcióval](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>VÁLLALATKÖZI együttműködés Active Directory egyszeri jelszó-hitelesítéssel (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

Bevezetünk egy egyszeri jelszavas hitelesítést (OTP) a B2B vendég felhasználói számára, akik nem hitelesíthetők más módon (például Azure AD, Microsoft-fiók (MSA) vagy Google Federation). Ez az új hitelesítési módszer azt jelenti, hogy a vendég felhasználóknak nem kell új Microsoft-fiók létrehozniuk. Ehelyett a meghívások beváltásakor vagy egy megosztott erőforrás elérésekor a vendég felhasználó egy ideiglenes kódot kérhet az e-mail-címre történő küldéshez. Ezt az ideiglenes kódot használva a vendég felhasználó továbbra is bejelentkezhet.

További információ: az [e-mailek egyszeri jelszavas hitelesítése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blog, az [Azure ad lehetővé teszi a megosztást és együttműködést bármilyen fiókkal rendelkező felhasználó számára](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD Application Proxy cookie-beállítások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Három új cookie-beállítást vezettünk be, amelyek elérhetők az Application proxyn keresztül közzétett alkalmazások számára:

- **Használjon csak HTTP-alapú cookie-t.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy-hozzáférési és munkamenet-cookie-kra. A beállítás bekapcsolása további biztonsági előnyöket biztosít, például segít megakadályozni a cookie-k másolását vagy módosítását az ügyféloldali parancsfájlok használatával. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon biztonságos cookie-t.** Beállítja az alkalmazásproxy-hozzáférési és munkamenet-cookie-k **biztonságos** jelölőjét. A beállítás bekapcsolása további biztonsági előnyöket biztosít, mivel a cookie-k továbbítása csak TLS-alapú biztonságos csatornákon keresztül történik (például HTTPS). Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon állandó cookie-t.** Megakadályozza, hogy a böngésző bezárása után a hozzáférés-cookie-k lejárnak. Ezek a cookie-k a hozzáférési jogkivonat élettartamának végéig tartanak. A cookie-k azonban alaphelyzetbe állnak, ha eléri a lejárati időt, vagy ha a felhasználó manuálisan törli a cookie-t. Azt javasoljuk, hogy tartsa meg az alapértelmezett **beállítást, és**csak olyan régebbi alkalmazások beállítását kapcsolja be, amelyek nem osztják meg a cookie-kat a folyamatok között.

További információ az új cookie-król: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – január 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Január 2019-én ezeket a 35 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lejárati emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CORPTAX](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitális Bezárás](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [működőképes](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNEXUS SSO-rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [látogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD Identity Protection fejlesztések (nyilvános előzetes verzió)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük be, hogy a következő fejlesztéseket adtuk hozzá a Azure AD Identity Protection nyilvános előzetes ajánlathoz, beleértve a következőket:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés a gépi tanulás segítségével

- A kockázatos felhasználók és a kockázatos bejelentkezések teljes termékre kiterjedő összehangolása

További információ a fejlesztésekről: [Mi az Azure Active Directory Identity Protection (frissítve)?](https://aka.ms/IdentityProtectionDocs) Ha többet szeretne megtudni, és megoszthatja gondolatait a terméken belüli kérdésekkel.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>A Microsoft Authenticator alkalmazás új alkalmazás-zárolási funkciója iOS-és Android-eszközökön

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Microsoft Authenticator alkalmazás  
A **termék képességei:** Identitás-biztonsági & védelme

Az egyszer használatos PIN-kódok, az alkalmazásadatok és az Alkalmazásbeállítások biztonságosabbá tételéhez bekapcsolhatja az alkalmazás zárolási funkcióját a Microsoft Authenticator alkalmazásban. Az alkalmazás zárolásának bekapcsolása azt jelenti, hogy minden alkalommal, amikor megnyitja a Microsoft Authenticator alkalmazást, a rendszer kérni fogja a PIN-kód vagy a biometrikus adatok hitelesítését.

További információ: [Microsoft Authenticator alkalmazás – gyakori kérdések](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD Privileged Identity Management (PIM) exportálási képességei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management

A Privileged Identity Management (PIM) rendszergazdák mostantól exportálják az összes aktív és jogosult szerepkör-hozzárendelést egy adott erőforráshoz, beleértve az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket is. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket.

További információ: a [PIM-beli Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem váltanak csak felhőalapú fiókokra

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Directory

>[!Important]
>A javítás miatt meghallottuk és megértettük a frusztrációt. Ezért ezt a módosítást csak addig a lépésig adtuk vissza, amíg a javítás könnyebben megvalósítható a szervezetében.

Kijavítottunk egy hibát, amelyben a felhasználó DirSyncEnabled jelölője helytelenül lett **hamis** értékre állítva, ha a Active Directory tartományi szolgáltatások (AD DS) objektumot kizárták a szinkronizációs hatókörből, majd az Azure ad-ben a következő szinkronizálási ciklusban helyezték át a Lomtárba. A javítás eredményeképpen, ha a felhasználó ki van zárva a szinkronizálási hatókörből, és ezt követően az Azure AD Lomtárból lett visszaállítva, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ből, és nem kezelhető a felhőben, mivel a szolgáltatói forrás (SoA) továbbra is a következő marad: helyszíni AD.

A javítás előtt probléma merült fel, ha a DirSyncEnabled jelzőt hamis értékre váltották. Helytelen benyomást tett, hogy ezeket a fiókokat csak felhőalapú objektumokra konvertálták, és a fiókokat a felhőben lehet felügyelni. Azonban a fiókok továbbra is megtartották a SoA-t a helyszínen, és minden szinkronizált tulajdonságot (Shadow Attribute) a helyszíni AD-ből. Ez az állapot több problémát okozott az Azure AD-ben és más Felhőbeli számítási feladatokban (például az Exchange Online-ban), amely a fiókokat az Active Directory-ból szinkronizálva várta, de mostantól csak a csak felhőalapú fiókokkal működött.

Ebben az esetben az egyetlen módja, hogy valóban átalakítson egy szinkronizált-ból-AD-fiókot a csak felhőalapú fiókra, ha letiltja az rSync-t a bérlői szinten, ami egy háttérbeli műveletet indít el a SoA átviteléhez. Ilyen típusú SoA-változásra van szükség (de nem korlátozódik a) a helyszíni kapcsolódó attribútumok (például a LastDirSyncTime és az árnyék attribútumai) tisztítására, és más Felhőbeli számítási feladatokhoz is küld egy jelet, hogy a megfelelő objektuma csak felhőalapú fiókra legyen konvertálva. .

Ez a javítás ezért megakadályozza, hogy a közvetlen frissítések az AD-ből szinkronizált felhasználó ImmutableID attribútumán legyenek, ami a múltban bizonyos esetekben szükséges. A tervezés szerint az Azure AD-beli objektum ImmutableID, ahogy azt a név is jelenti, nem változtatható. Az Azure AD Connect Health és Azure AD Connect szinkronizálási ügyfél által megvalósított új szolgáltatások az ilyen forgatókönyvek kezeléséhez érhetők el:

- **Nagy léptékű ImmutableID-frissítés számos felhasználó számára a szakaszos megközelítésekben**
  
  Tegyük fel például, hogy hosszú AD DS erdők közötti áttelepítést kell végeznie. Megoldás: a Azure AD Connect segítségével **konfigurálja a forrás-horgonyt** , és a felhasználó áttelepítésekor másolja a meglévő ImmutableID-értékeket az Azure ad-ből a helyi AD DS felhasználó ms-DS-konzisztencia-GUID attribútumára az új erdőben. További információ: [using ms-DS-ConsistencyGuid as sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Nagy méretű ImmutableID-frissítések egy adott felvétel sok felhasználója számára**

  A Azure AD Connect megvalósítása során például hiba történt, és most meg kell változtatnia a SourceAnchor attribútumot. Megoldás: tiltsa le az rSync-et a bérlői szinten, és törölje az összes érvénytelen ImmutableID-értéket. További információ: [a címtár-szinkronizálás kikapcsolása az Office 365](/office365/enterprise/turn-off-directory-synchronization)-ben.

- **Helyi felhasználó megkeresése egy meglévő felhasználóval az Azure ad-ben** Például egy, a AD DSban újra létrehozott felhasználó létrehoz egy duplikált értéket az Azure AD-fiókban ahelyett, hogy egy meglévő Azure AD-fiókkal (árva objektummal) társítja. Megoldás: használja a Azure Portal Azure AD Connect Health a forrás-és ImmutableID újrafelhasználásához. További információ: [árva objektum forgatókönyve](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Változás megszakítása: a naplózási és a bejelentkezési naplókra vonatkozó frissítések Azure Monitor

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Jelenleg a naplózási és a bejelentkezési naplókat is közzéteszjük Azure Monitoron keresztül, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analyticsokkal. A visszajelzések alapján, és a szolgáltatás általánosan elérhetővé tételének előkészítésében a következő módosításokat végezjük el a sémán. Ezek a séma-változások és a kapcsolódó dokumentációs frissítések a januári első héten történnek.

#### <a name="new-fields-in-the-audit-schema"></a>Új mezők a naplózási sémában
Új **Művelettípus** mezőt adunk hozzá az erőforráson végrehajtott művelet típusának megadásához. Például: **Hozzáadás**, **frissítés**vagy **Törlés**.

#### <a name="changed-fields-in-the-audit-schema"></a>A naplózási séma módosított mezői
A következő mezők változnak a naplózási sémában:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Kategória|Ez volt a **szolgáltatás neve** mező. Most már a **naplózási kategóriák** mező. A **szolgáltatás neve** átnevezve lett a **loggedByService** mezőre.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazások kezelése</li></ul>|
|targetResources|A legfelső szinten tartalmazza a **TargetResourceType** .|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoport</li></ul>|
|loggedByService|Annak a szolgáltatásnak a nevét adja meg, amely a naplót generálta.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|A naplók eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sikeres</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Módosult mezők a bejelentkezési sémában
A bejelentkezési sémában a következő mezők változnak:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Most már a **appliedConditionalAccessPolicies** mező.|Nincs változás besorolás|Nincs változás besorolás|
|conditionalAccessStatus|A feltételes hozzáférési házirend állapotának eredményét adja meg a bejelentkezéskor. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|
|appliedConditionalAccessPolicies: eredmény|A bejelentkezéskor az egyes feltételes hozzáférési szabályzatok állapotának eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|

További információ a sémáról: [Az Azure ad-naplók sémájának értelmezése Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>A felügyelt gépi tanulási modell és a kockázati pontszám motorjának Identity Protection-fejlesztése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Kockázati pontszámok

Az Identity Protection szolgáltatással kapcsolatos felhasználói és bejelentkezési kockázatkezelési motor fejlesztései segíthetnek a felhasználók kockázati pontosságának és lefedettségének javításában. A rendszergazdák láthatják, hogy a felhasználói kockázati szint már nem kapcsolódik az adott észlelések kockázati szintjéhez, és a kockázatos bejelentkezési események száma és szintje is növekszik.

A kockázatos észleléseket a felügyelt gépi tanulási modell értékeli ki, amely a felhasználó bejelentkezési funkcióinak és észlelési mintázatának további funkcióival kiszámítja a felhasználói kockázatot. A modell alapján előfordulhat, hogy a rendszergazda magas kockázati pontszámokkal rendelkező felhasználókat talál, még akkor is, ha az adott felhasználóhoz tartozó észlelések alacsony vagy közepes kockázattal rendelkeznek. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió) alaphelyzetbe állíthatják a saját jelszavukat

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD-rendszergazdák mostantól alaphelyzetbe állíthatják a saját jelszavukat a Microsoft Authenticator alkalmazás értesítésein vagy egy, a mobil hitelesítő alkalmazásból vagy a hardver-tokenből származó kóddal. A saját jelszavának alaphelyzetbe állításához a rendszergazdák mostantól a következő módszerek közül kettőt képesek használni:

- Microsoft Authenticator alkalmazás értesítése

- Egyéb mobil hitelesítő alkalmazás/hardver token kódja

- E-mail

- Telefonhívás

- Szöveges üzenet

További információ a jelszavak alaphelyzetbe állítására szolgáló Microsoft Authenticator alkalmazás használatáról: [Azure ad önkiszolgáló jelszó-visszaállítás – Mobile App és SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új Azure AD Cloud Device-rendszergazdai szerepkör (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Hozzáférés-vezérlés

A rendszergazdák a felhőalapú eszköz rendszergazdai feladatainak elvégzéséhez rendelhetnek felhasználókat az új felhőalapú eszköz rendszergazdai szerepkörhöz. A Cloud Device Administrators szerepkörrel rendelkező felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, valamint beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal.

További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök kezelése az Azure AD új tevékenység-időbélyegével (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Eszközök életciklusának kezelése

Tisztában vagyunk azzal, hogy idővel frissítenie kell és ki kell vonnia a szervezete eszközeit az Azure AD-ben, hogy elkerülje az elavult eszközöket a környezetben. Ennek a folyamatnak az elősegítése érdekében az Azure AD mostantól új tevékenység-időbélyeggel frissíti az eszközöket, és segít az eszközök életciklusának kezelésében.

További információ az időbélyegző beszerzéséről és használatáról [: How to: az elavult eszközök kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól a használati feltételek elfogadását minden eszközön

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási
 
A rendszergazdák most bekapcsolhatják a **felhasználók megkövetelése minden eszközre** lehetőséget, hogy a felhasználók elfogadják a használati feltételeket a bérlőn használt összes eszközön.

További információ: a Azure Active Directory használati [feltételeinek használati feltételei című szakasza](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét egy ismétlődő ütemterv alapján

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási
 

A rendszergazdák mostantól bekapcsolhatják a **lejárati hozzájárulási** lehetőséget, hogy a használati feltételek lejárnak az összes felhasználó számára a megadott ismétlődő ütemterv alapján. Az ütemterv lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell fogadniuk.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét az egyes felhasználók ütemterve alapján

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A rendszergazdák mostantól megadhatnak egy időtartamot, amelyet a felhasználónak újra el kell fogadnia a használati feltételeket. A rendszergazdák például megadhatják, hogy a felhasználóknak minden 90 naponként újra el kell fogadniuk a használati feltételeket.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új Azure AD Privileged Identity Management (PIM) e-mailek Azure Active Directory szerepkörökhöz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
A Azure AD Privileged Identity Managementt (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, beleértve az elmúlt hét nap alábbi adatait:

- A legfontosabb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- A PIM-szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-n kívüli szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-ben "állandónak" kitett felhasználók száma

A PIM-vel és az elérhető e-mail-értesítésekkel kapcsolatos további információkért lásd: [e-mailes értesítések a PIM-ben](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoport alapú licencelés mostantól általánosan elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

A csoportos licencelés kívül esik a nyilvános előzetes verzióban, és már általánosan elérhető. Ennek az általános kiadásnak a részeként a szolgáltatás skálázható, és lehetővé tette a csoportos licencelési hozzárendelések újrafeldolgozását egyetlen felhasználó számára, valamint az Office 365 E3/a3 licenccel rendelkező csoportos licencelés használatát.

A csoport alapú licenceléssel kapcsolatos további információkért lásd: [Mi az a Azure Active Directory csoportos licencelés?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – november 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
November 2018-én a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [végtelen Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), Wrike [SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge tag portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), Allbound [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [plex alkalmazások – klasszikus teszt ](https://test.plexonline.com/signon), [Plex-alkalmazások – klasszikus](https://www.plexonline.com/signon), [plex-alkalmazások – UX-teszt](https://test.cloud.plex.com/sso), [plex-alkalmazások – UX](https://cloud.plex.com/sso), [plex-alkalmazások – iam](https://accounts.plex.com/), [mesterségek – gyermekgondozási nyilvántartások, részvétel, & pénzügyi nyomkövető rendszer](https://getcrafts.ca/craftsregistration) 

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD naplókat mostantól működik az Azure Log Analytics (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük, hogy most már továbbítás az Azure AD-naplók az Azure Log Analytics! A várt funkció segítségével még jobb hozzáférést biztosíthat a elemzés az üzleti, műveletek, és a biztonsági, valamint az infrastruktúra figyelése a megoldást. További információkért tekintse meg az [Azure-beli Azure Active Directoryi tevékenységek naplóit log Analytics most elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. október

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2018 október tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 14 új alkalmazásokról:

[Saját díjátadó pontok, a](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial) [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), a ambyint, a [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), a [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), a [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), a [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), a [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial) [, a Phraseanet,](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)a [kiértékelt](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), a [Workspot-szabályozás](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), a [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial) [, a Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="azure-ad-domain-services-email-notifications"></a>Az Azure AD Domain Services E-mail-értesítések

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Az Azure AD Domain Services konfigurációs hibák vagy problémák a felügyelt tartományhoz az Azure Portalon riasztásokat biztosít. Ezek a riasztások tartalmazzák a részletes útmutatókat, így anélkül, hogy forduljon az ügyfélszolgálathoz a probléma javításához próbálkozhat.

Októbertől az lesz az értesítési beállításokat a felügyelt tartományhoz tartozó, amikor új kapcsolatos riasztások történnek, egy e-mailt küld egy kijelölt csoport tagjainak, így nem kell folyamatosan ellenőrzése a portálon a frissítéseket.

További információ: [Azure ad Domain Services értesítési beállításai](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD portal támogatja a ForceDelete tartomány API használatával törli az egyéni tartományok 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Címtár-kezelés  
A **termék képességei:** Directory

Örömmel jelentjük, hogy mostantól használhatja a ForceDelete tartomány API aszinkron módon átnevezésével, mint például a felhasználók, csoportok és alkalmazások az egyéni tartománynevet (contoso.com) a biztonsági mutató hivatkozásokat a kezdeti alapértelmezett tartomány nevét (az egyéni tartománynevek törlése contoso.onmicrosoft.com).

Ez a változás segítségével gyorsabban törölni az egyéni tartománynevek, ha a szervezet már nem használja a nevét, vagy ha szeretné használni a tartománynév egy másik Azure AD-val.

További információ: [Egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok esetében frissültek a rendszergazdai szerepkör engedélyei

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Azt már megtörtént egy probléma javítása, adott rendszergazdai szerepkörök mostantól létrehozhat és frissíthet a dinamikus tagsági szabályok, anélkül, hogy a csoport tulajdonosa legyen.

A szerepkörök a következők:

- Globális rendszergazda

- Intune-rendszergazda

- Felhasználói rendszergazda

További információ: [dinamikus csoport létrehozása és állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Néhány külső gyártótól származó alkalmazás esetében egyszerűsödött az egyszeri bejelentkezés (SSO) konfigurálása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Tisztában vagyunk vele, hogy állítja be egyszeri bejelentkezést (SSO) szoftverfrissítési szolgáltatásként (SaaS) alkalmazások kihívást jelenthet az egyes alkalmazások konfiguráció egyedi jellege miatt. Elkésztettünk egy leegyszerűsített konfigurációs élményt biztosít az egyszeri bejelentkezés beállításai a következő külső SaaS-alkalmazások automatikus feltöltéséhez:

- Zendesk

- Az ArcGis Online

- Jamf Pro

Az egykattintásos élmény használatának megkezdéséhez nyissa meg az alkalmazás **Azure Portal** > **SSO konfigurációs** lapját. További információ: Saas- [alkalmazások integrációja Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Az Azure Active Directory - hol található a adatait? lap

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** GoLocal

Válassza ki a vállalat régióját a **Azure Active Directoryból – itt** megtekintheti, hogy melyik Azure-adatközpontban található az Azure ad-adatok az összes Azure ad-szolgáltatáshoz. Specifikus szerint szűrheti az adatokat az Azure AD-szolgáltatások számára, a vállalat.

A szolgáltatás eléréséhez és további információkért tekintse meg a [Azure Active Directory-hol található az adatai](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új üzembe helyezési terv érhető el a Saját alkalmazások hozzáférési panelen

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

Tekintse meg a saját alkalmazások hozzáférési paneljén elérhető új központi telepítési csomagot (https://aka.ms/deploymentplans).
A saját alkalmazások hozzáférési panel itt rendelkező felhasználók számára egyetlen helyen megtalálhatja és elérheti a saját alkalmazásokban. Ezen a portálon a felhasználók önkiszolgáló lehetőségeket biztosít, például alkalmazások és a csoportok hozzáférést kér vagy erőforrásokhoz való hozzáférés kezelését ezek mások nevében is biztosít.

További információ: [Mi a My apps portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új lappal (Hibaelhárítás és támogatás) bővült az Azure Portal Bejelentkezések weblapja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

A Azure Portal **bejelentkezési** lapjának új **Hibaelhárítás és támogatás** lapján segítséget nyújt a rendszergazdáknak az Azure ad-bejelentkezésekkel kapcsolatos problémák elhárításában. Az új lapon a hibakód, a hibaüzenet és a Szervizelési javaslatok (ha vannak) a probléma megoldásához nyújtanak segítséget. Ha nem tudja megoldani a problémát, a **Másolás a vágólapra** lehetőség használatával új módszert biztosítunk a támogatási jegy létrehozásához, amely feltölti a **kérelem azonosítóját** és **dátumát (UTC)** a naplófájlhoz a támogatási jegyben.  

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozására szolgáló egyénibővítmény-tulajdonságok továbbfejlesztett támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Ezzel a frissítéssel mostantól az **Egyéni bővítmény tulajdonságainak beolvasása** hivatkozásra kattintva megtekintheti a dinamikus felhasználói csoport-szerkesztőt, megadhatja az egyedi alkalmazás azonosítóját, és megkaphatja a dinamikus tagsági szabály létrehozásakor használni kívánt egyéni bővítmény-tulajdonságok teljes listáját. Ez a lista is frissíthetők úgy, hogy minden olyan új egyéni bővítmény tulajdonságainak lekérése az adott alkalmazáshoz.

A dinamikus tagsági szabályok egyéni bővítmény-tulajdonságainak használatával kapcsolatos további információkért lásd a [bővítmény tulajdonságai és az egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) című témakört.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)listáján:

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Már támogatott a Windows 7/8/8.1 zárolási képernyőjéről történő önkiszolgáló jelszómódosítás

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Miután beállította ezt az új szolgáltatást, a felhasználók a Windows 7, a Windows 8 vagy a Windows 8,1 operációs rendszert futtató eszközök **zárolási** képernyőjén egy hivatkozást fognak látni a jelszavuk visszaállítására. A hivatkozásra kattintva a felhasználó rendszer végigvezeti a azonos jelszóvisszaállítási folyamatot a webböngészőn keresztül.

További információ: a [jelszó-visszaállítás engedélyezése a Windows 7, 8 és 8,1](https://aka.ms/ssprforwindows78) rendszerből

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változással kapcsolatos értesítés: a hitelesítő kódok a későbbiekben már nem lesznek újrafelhasználhatók 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. szeptember

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 szeptember tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [a](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial) [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), a [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), a [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), a [Ambrus kiry noemi Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), a [hópehely](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), a [NavigoCloud, a Figma, a](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)JOIN.me, a ZEPHYRSSO, a Silverback, a Riverbed Xirrus EasyPass, a [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), a Enlyft SSO az Azure-hoz, surveymonkey, [összehívás](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Bővült a támogatott jogcím-átalakítási módszerek köre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Bevezetjük az új jogcím-átalakítási módszereket, ToLower () és ToUpper (), amelyek az SAML-tokenekre alkalmazhatók az SAML-alapú **egyszeri bejelentkezés konfigurációs** oldaláról.

További információ: [az SAML-tokenben kiadott jogcímek testreszabása az Azure ad-ben nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissült az SAML-alapú alkalmazáskonfigurációs kezelőfelület (előzetes verzió)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

A frissített SAML-alapú alkalmazás-konfigurációs felhasználói Felületet részeként láthatja:

- Egy frissített útmutató felületet, az SAML-alapú alkalmazások konfigurálásához.

- Mi az a hiányzó vagy helytelen a konfiguráció a kapcsolatos láthatóságot.

- Lehetővé teszi, hogy az e-mail címeket lejárati tanúsítványt az értesítésre.

- Új jogcím-átalakítási módszerek, ToLower() ToUpper() és még.

- Töltse fel a saját jogkivonat-aláíró tanúsítvány a vállalati alkalmazások lehetőséget.

- Is be lehet állítani az alkalmazások SAML NameID-formátum, és a NameID értéket állítja be Címtárbővítmények módja.

A frissített nézet bekapcsolásához kattintson az **egyszeri bejelentkezési** oldal tetején található **új felhasználói felület kipróbálása** hivatkozásra. További információ: [oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása alkalmazáshoz Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)használatával.

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Módosultak az Azure Active Directory-beli IP-címtartományok

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Platform

Nagyobb IP-címtartományok vezetünk be az Azure ad Szolgáltatásba, ami azt jelenti, ha konfigurálta az Azure AD IP-címtartományok esetében a tűzfalak, útválasztók és hálózati biztonsági csoportok kell azokat. Így nem kell a tűzfalat, útválasztó vagy a hálózati biztonsági csoportok IP-címtartomány konfigurációk újra módosítása esetén az Azure AD új végpontjait hozzáadja ezt a frissítést végzünk. 

Hálózati forgalom ezen új tartományok áthelyezése a következő két hónapban. Szolgáltatás megszakításmentes folytatásához, hozzá kell adnia a frissített értékeket az IP-címek 2018. szeptember 10. előtt:

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy nem távolítja a régi IP-címtartományokat, mindaddig, amíg az összes, a hálózati forgalom át lett helyezve az új tartományokat. Az áthelyezéssel és a régi tartományok eltávolításával kapcsolatos további információkért lásd: [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változással kapcsolatos értesítés: a hitelesítő kódok a későbbiekben már nem lesznek újrafelhasználhatók 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Önkiszolgáló jelszóregisztráció (SSPR) és többtényezős hitelesítés (MFA) – összevont biztonságiadat-kezelés

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés
 
Ez az új funkció lehetővé teszi a biztonsági adataikat (mint például a telefonszám, mobilalkalmazás és így tovább) kezelheti az SSPR és a egy egyetlen helyen, és felület; MFA képest a korábban, ahol tette két különböző helyen.

SSPR vagy MFA használatával személyek is használható az átszervezett felületet. Ezenkívül ha a szervezet nem kényszeríti a többtényezős hitelesítés vagy az SSPR regisztrációt, személyek továbbra is regisztrálhatja bármilyen MFA vagy az SSPR biztonsági adatai módszer engedélyezett a munkahelyen a saját alkalmazások portálról.

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) a kijelölt csoport vagy a bérlő összes felhasználója esetében. Az átszervezett felülettel kapcsolatos további információkért tekintse meg a [konvergens élmény blogját](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új (csak a HTTP használatát támogató) cookie-beállítás az Azure AD Application Proxy-alkalmazásokban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Létezik egy új beállítás, amely **csak HTTP-cookie-kat** használ az alkalmazásproxy alkalmazásaiban. Ez a beállítás biztosítja, beleértve a HTTP-válaszfejléc mindkét alkalmazásproxy hozzáférési és munkamenet-cookie-khoz a HTTPOnly jelző, hozzáférés leállítása a cookie-val ügyféloldali parancsfájl és további megakadályozza a műveleteket, például a Másolás adja meg a további biztonsági vagy a cookie-k módosítását. Bár ez a jelző korábban még nem használt, a cookie-kat mindig lettek titkosítva, és nem megfelelő módosításokat elleni védelem érdekében az SSL-kapcsolat használatával.

Ez a beállítás nem kompatibilis az alkalmazások az ActiveX-vezérlők, például a távoli asztal használatával. Ha ebben a helyzetben, azt javasoljuk, hogy kapcsolja ki ezt a beállítást.

A csak HTTP-alapú cookie-k beállításával kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>A Privileged Identity Management (PIM) az Azure-erőforrások esetében támogatja a felügyeleti csoport típusú erőforrásokat

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Just-In-Time aktiválási és hozzárendelési beállítások is érvényesek a felügyeleti csoport erőforrástípusok, ugyanúgy, mint az előfizetések, erőforráscsoportok és erőforrások (például virtuális gépek, alkalmazásszolgáltatások, és további) már nincs. Ezenkívül bárki egy szerepkör, amely rendszergazdai hozzáférést biztosít a felügyeleti csoport felderítése és kezelése a PIM erőforrás.

A PIM-és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [Azure-erőforrások felderítése és kezelése Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Jelenleg a PIM használata szerepkör aktiválásakor is igénybe vehet az engedélyek érvénybe több mint 10 perc. Ha úgy dönt, hogy az alkalmazás-hozzáférést, amely jelenleg nyilvános előzetes verzióban érhető el, használja a rendszergazdák hozzáférhetnek az Azure AD portálra, amint az aktiválási kérelem befejeződött.

Jelenleg alkalmazás elérése csak támogatja az Azure AD-portál felületének és az Azure-erőforrások. További információ a PIM-ről és az alkalmazás-hozzáférésről: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. augusztus

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 augusztus tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Szarvascsőrű](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline kötetlen](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs-mobil-és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [meta Networks-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [így teszünk](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-költségelszámolás](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív Tableau-támogatás már elérhető az Azure AD Application Proxyban

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

A frissítés az OpenID Connect, az üzem előtti hitelesítési protokoll az OAuth 2.0-s Kódmegadás protokoll már nem kell az alkalmazásproxy használatával a Tableau használandó további konfigurációt elvégezni. Ez a protokoll változás is segít alkalmazásproxy hatékonyabban támogatják a több modern alkalmazások csak a HTTP átirányítást, gyakran támogatott JavaScript és HTML-címkék használatával.

A tabló natív támogatásával kapcsolatos további információkért tekintse meg [Az Azure ad Application proxy most a natív tabló-támogatással foglalkozó](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)témakört.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Már támogatott a Google identitásszolgáltatóként való beállítása a vállalatközi vendégfelhasználók számára (előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

A szervezet a Google összevonási beállításával engedélyezheti, meghívott Gmail felhasználók bejelentkezési a megosztott alkalmazások és erőforrások a meglévő Google-fiók létrehozása a személyes Microsoft-Account (msa-k) vagy egy Azure AD-fiók nélkül.

Egy választható nyilvános előzetes kiadásról. A Google Federation szolgáltatással kapcsolatos további információkért lásd: a [Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Az Azure Active Directory értesítő e-mailjeinek fejlesztései

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure Active Directory (Azure AD) e-mailek most szolgáltatás egy új megjelenés, valamint a módosítások a feladó e-mail címe és a feladó megjelenített neve, amikor a következő szolgáltatások által küldött:
 
- Az Azure AD hozzáférési felülvizsgálatok
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejáró Tanúsítványértesítéseket
- Vállalati alkalmazás-kiépítés szolgáltatási értesítések
 
Az e-mail-értesítések, a következő e-mail címre küldi, és a megjelenített neve:

- E-mail-cím: azure-noreply@microsoft.com
- Megjelenített név: Microsoft Azure
 
Az új e-mail-tervekre és további információkra például az [e-mailes értesítések az Azure ad PIM-ben](https://go.microsoft.com/fwlink/?linkid=2005832)című témakörben olvashat.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD tevékenységnaplói most már elérhetők az Azure Monitorban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-Tevékenységnaplók mostantól elérhetők az Azure monitor (az Azure platform kiterjedő figyelési szolgáltatás) nyilvános előzetes verzióban érhető el. Az Azure Monitor kínál hosszú távú adatmegőrzés és zökkenőmentes integráció mellett ezek a fejlesztések:

- Hosszú távú megőrzése a naplófájlok irányítva a saját Azure storage-fiókba.

- Zökkenőmentes SIEM-integráció, nem kell írnia, vagy egyéni parancsfájlok karbantartása.

- Zökkenőmentes integráció a saját egyéni megoldások, elemzési eszközök vagy az incidenskezelés megoldásokat.

Az új képességekkel kapcsolatos további információkért tekintse meg a blog [Azure ad-tevékenységek naplói a Azure monitor Diagnostics szolgáltatásban már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) verzióban és a dokumentációban [Azure Active Directory a tevékenység naplóit a Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)című témakörben.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentéshez hozzáadott feltételes hozzáférési információk

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-biztonsági & védelme
 
Ez a frissítés lehetővé teszi, hogy láthatja, hogy mely házirendek kiértékelése a felhasználó bejelentkezésekor mellett szabályzat eredménye. A jelentés emellett mostantól tartalmazza a felhasználó által használt, így azonosíthatja a régi protokoll forgalom ügyfélalkalmazás típusa. Jelentés bejegyzések most is kereshető a korrelációs Azonosítót, amely tekintheti meg a felhasználók felé néző hibaüzenet és azonosítása és megoldása a megfelelő bejelentkezési kérelem használható.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>A régi hitelesítések megtekintése a bejelentkezések tevékenységnaplói révén

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése
 
A bejelentkezési tevékenység naplóiban a **Client app (ügyfélalkalmazás** ) mező bevezetésével az ügyfelek már láthatják az örökölt hitelesítést használó felhasználókat. Az ügyfelek hozzáférhetnek ezekhez az adatokhoz a bejelentkezési MS Graph API használatával vagy az Azure AD-portál bejelentkezési tevékenység naplófájljaiban, ahol az **ügyfélalkalmazás** vezérlőelem használatával szűrheti a régi hitelesítéseket. Tekintse meg a dokumentáció További részletekért.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>2018 júliusától új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 július tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Innovációs központ](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bizonyos adminisztrációs egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC-előkészítés, [ipass-SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [képernyőfelvétel-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified osztályterem, [Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)bevezetése, Bomgar-alapú [távoli támogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagine webvízió](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [ismeretek alapja](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>2018. július – új felhasználókiépítési funkció az SaaS-alkalmazás-integrációkban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 július hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bónusz](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók kiépítését támogató összes alkalmazás listáját itt tekintheti meg: [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Az árván maradt és ismétlődő attribútumok szinkronizálásából fakadó hibák javításának egyszerűbb módja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** AD-kapcsolat  
A **termék képességei:** & Jelentéskészítés figyelése
 
Az Azure AD Connect Health önkiszolgáló szervizelést kell végeznie, jelölje ki és hárítsa el a szinkronizálási hibák mutatja be. Ez a szolgáltatás hibaelhárítása a duplikált attribútummal szinkronizálási hibák, és kijavítja a rendszer árva objektumokat az Azure ad-ből. A diagnosztika a következő előnyökkel jár:

- Duplikált attribútummal szinkronizálási hibák, adott javításokat nyújtó leszűkíti

- A javítás vonatkozik, kijelölve, az Azure AD-forgatókönyvek esetén egyetlen lépésben hibák megoldása

- Nincs frissítés vagy a konfiguráció be-és a funkció használatához szükséges

További információ: [duplikált attribútum-szinkronizálási hibák diagnosztizálása és szervizelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Az Azure AD és az MSA bejelentkezési felületének vizuális frissítései

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés

Frissítettük a felhasználói felületen, a Microsoft online services bejelentkezési élményt, például az Office 365 és az Azure. Ez a változás lehetővé teszi a képernyők, kevésbé zsúfolt és egyszerűbb. További információ erről a változásról: az [Azure ad bejelentkezési élményének közelgő újdonságai](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Az Azure AD Connect új kiadása – 2018. július

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Az Azure AD Connect legújabb kiadása tartalmazza: 

- Hibajavítások és a támogatási frissítéseket. 

- A Ping összevonni integráció általános elérhetősége

- A legújabb SQL 2012-ügyfél frissítései 

További információ erről a frissítésről [: Azure ad Connect verzió kiadási előzményei](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A használati feltételek végfelhasználói felhasználói felületének frissítése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

Frissítjük a jelen használati feltételek végfelhasználó felhasználói felületén az elfogadási karakterlánc.

**Aktuális szöveg** [TenantName] erőforrásokhoz férhet hozzá, el kell fogadnia a használati feltételeket.<br>**Új szöveg.** [TenantName] erőforrás eléréséhez olvassa el a használati feltételeket.

**Aktuális szöveg:** Az elfogadás kiválasztása azt jelenti, hogy elfogadja a fenti használati feltételeket.<br>**Új szöveg:** Az elfogadás gombra kattintva erősítse meg, hogy elolvasta és megértette a használati feltételeket.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollok és alkalmazások használatát

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az átmenő hitelesítés most már támogatja az örökölt protokollok és alkalmazások. Most már teljes mértékben támogatja a következő korlátozások vonatkoznak:

- Felhasználói bejelentkezések örökölt Office ügyfélalkalmazások számára, az Office 2010 és Office 2013, anélkül, hogy a modern hitelesítést.

- A hozzáférést az naptár megosztása és a rendelkezésre állási információk az Exchange hibrid környezetek az Office 2010-et csak.

- Felhasználói bejelentkezések Skype az üzleti ügyfélalkalmazások számára a modern hitelesítés nélkül.

- Felhasználói bejelentkezések a PowerShell 1.0-s verzióját.

- Az Apple Device Enrollment Program (Apple DEP), az iOS beállítási asszisztens használatával. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Az önkiszolgáló jelszóátállításhoz és a többtényezős hitelesítéshez használt biztonsági adatok kezelésének összevonása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Ez az új funkció lehetővé teszi, hogy a felhasználók saját biztonsági adatok (például telefonszám, e-mail címét, mobilalkalmazás és így tovább) önkiszolgáló jelszó-visszaállítás (SSPR) és a multi-factor Authentication (MFA) egyetlen felületen kezeljék. Felhasználók többé nem kell az azonos biztonsági információkat kelljen regisztrálniuk az SSPR és a többtényezős hitelesítés a két különböző környezeteket. Az új felületet is vonatkozik, akik rendelkeznek vagy az SSPR, vagy a többtényezős hitelesítés.

Ha egy szervezet nem érvényesíti az MFA-t vagy a SSPR-regisztrációt, akkor a felhasználók a **saját alkalmazások** portálon regisztrálhatják biztonsági adataikat. Itt a felhasználók regisztrálhatnak semmilyen metódus az MFA vagy az SSPR engedélyezve van. 

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) felhasználók vagy a bérlő minden felhasználók egy kiválasztott csoportja számára.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A Microsoft Authenticator alkalmazással igazolhatja személyazonosságát a jelszava átállításakor

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazda jogosultságú igazolják egy értesítést vagy a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazást) kód jelszó alaphelyzetbe állítása. Rendszergazdák bekapcsolása után a önkiszolgáló jelszó-visszaállítási mód, mobilalkalmazás aka.ms/mfasetup vagy aka.ms/setupsecurityinfo keresztül regisztráló felhasználók használhatja mobilalkalmazásukkal ellenőrzési módszerként a jelszó alaphelyzetbe állítása során.

Mobilalkalmazás-értesítés csak be kell kapcsolni, szabályzata előírja a két módszer a jelszó részeként.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Módosítsa az értesítés: a delegált engedélyezési folyamat használata az Azure AD tevékenység naplók API-alkalmazások biztonsági javítás

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az erősebb biztonsági kényszerítés miatt módosítani kellett a delegált engedélyezési folyamatot használó alkalmazások engedélyeit az [Azure ad-tevékenység naplófájljainak API-jai](https://aka.ms/aadreportsapi)eléréséhez. Ez a módosítás 2018. **június 26-án**lép működésbe.

Ha bármely, az alkalmazások az Azure AD tevékenység API-k, kövesse az alábbi lépéseket annak érdekében, hogy az alkalmazás nem lesz történik, a módosítás után új.

**Az alkalmazás engedélyeinek frissítése**

1. Jelentkezzen be a Azure Portalba, válassza a **Azure Active Directory**lehetőséget, majd válassza az **alkalmazás-regisztrációk**lehetőséget.
2. Válassza ki az Azure AD Activity logs API-t használó alkalmazást, válassza a **Beállítások**lehetőséget, válassza a **szükséges engedélyek**lehetőséget, majd válassza ki a **Windows Azure Active Directory** API-t.
3. A **hozzáférés engedélyezése** panel **meghatalmazott engedélyek** területén jelölje be a **Címtáradatok olvasása** melletti jelölőnégyzetet, majd kattintson a **Mentés**gombra.
4. Válassza az **engedélyek megadása**lehetőséget, majd válassza az **Igen**lehetőséget.
    
    >[!Note]
    >A következőkhöz biztosítanak engedélyeket: az alkalmazás globális rendszergazdának kell lennie.

További információkért tekintse meg az előfeltételek az Azure AD Reporting API-hoz való hozzáférésének előfeltételei az [engedélyek megadása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) területen.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Az Azure AD-szolgáltatásokhoz, a PCI DSS TLS-beállítások konfigurálása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform

Transport Layer Security (TLS) protokoll, amely két kommunikáló alkalmazás közötti adatvédelmi és adatintegritás biztosít, és a jelenleg használt leggyakrabban telepített biztonsági protokoll.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy a TLS és SSL (SSL) korai verzióit le kell tiltani az új és biztonságosabb alkalmazás-protokollok engedélyezése mellett, a megfelelőség pedig **2018. június 30-ig**érvényes. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz és PCI-DSS szabvány igényelnek, le kell tiltania a TLS 1.0. A TLS több verziói elérhetők, de a TLS 1.2 a legújabb verzió érhető el az Azure Active Directory Services. Erősen ajánlott közvetlenül a TLS 1.2-es ügyfél-kiszolgáló és a böngésző és a kiszolgáló kombinációk áthelyezése.

Elavult böngészők nem támogatja a TLS újabb verziók, például a TLS 1.2. Ha szeretné megtekinteni, hogy a böngésző mely verzióit támogatja a TLS, lépjen a [QUALYS SSL Labs](https://www.ssllabs.com/) -webhelyre, és kattintson a **böngésző tesztelése**elemre. Javasoljuk, hogy a böngésző legújabb verziójára frissítse és lehetőleg engedélyezése csak a TLS 1.2-es.

**A TLS 1,2 engedélyezése böngésző szerint**

- **A Microsoft Edge és az Internet Explorer (mindkettő az Internet Explorerben van beállítva)**

    1. Nyissa meg az Internet Explorert, válassza az **eszközök** > az Internetbeállítások > a **speciális** **lehetőséget** .
    2. A **Biztonság** területen válassza a **TLS 1,2 használata**lehetőséget, majd kattintson **az OK gombra**.
    3. Minden böngészőablakot bezárni, majd indítsa újra az Internet Explorerben. 

- **Google Chrome**

    1. Nyissa meg a Google Chrome-t, írja be a *Chrome://Settings/* a címsorba, majd nyomja le az **ENTER**billentyűt.
    2. Bontsa ki a **speciális** beállításokat, lépjen a **Rendszerterületen, és válassza a** **Proxybeállítások megnyitása**lehetőséget.
    3. Az **Internet tulajdonságok** mezőben válassza a **speciális** lapot, lépjen a **biztonság** területen válassza a **TLS 1,2 használata**lehetőséget, majd kattintson az **OK gombra**.
    4. Minden böngészőablakot bezárni, majd indítsa újra a Google Chrome-ban.

- **Mozilla Firefox**

    1. Nyissa meg a Firefox programot, írja be a *about: config* fájlt a címsorba, majd nyomja le az **ENTER**billentyűt.
    2. Keresse meg a kifejezést, a *TLS*-t, majd válassza ki a **Security. TLS. Version. max** bejegyzést.
    3. A **3** érték megadásával kényszerítse a böngészőt, hogy a TLS 1,2-es verziót használja, majd válassza az **OK gombot**.

        >[!NOTE]
        >A Firefox 60,0-es verziója támogatja a TLS 1,3-et, így a Security. TLS. Version. max értéket is beállíthatja **4**értékre.

    4. Minden böngészőablakot bezárni, majd indítsa újra a Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. június

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 június tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 15 új alkalmazásokról:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), a [Music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), az [SAML 1,1 token engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), a felszínek [, az](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)automatikus [feladat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [végpontok biztonsági mentése](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [SkyHigh-hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), a Smartway2, a [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), a [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), a [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [a helyszíni SharePoint, a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial) [CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), a [Vidyard,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)a [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Nyilvános előzetes verzióban érhető el az Azure AD jelszóvédelem

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Felhasználói hitelesítés

Segítségével az Azure AD jelszóvédelem kiiktathatja könnyen kitalálni a jelszót a környezetből. Mivel ezek a jelszavak segítséget nyújt egy jelszó megfelelő típusú támadás a biztonsági sérülés kockázata.

Pontosabban az Azure AD jelszóvédelem nyújt segítséget:

- A munkahelyi fiókok védelmét is az Azure AD-ben és a Windows Server Active Directory (AD). 
- Leállítja a felhasználók listáját a leggyakrabban használt jelszavak több mint 500, és több mint 1 millió karakter helyettesítő változata ezeket a jelszavakat a jelszavak használatával.
- Felügyelheti az Azure AD-jelszó védelmi egyetlen helyről az Azure AD-portálon is az Azure ad és helyszíni Windows Server AD.

Az Azure AD jelszavas védelemmel kapcsolatos további információkért lásd: [a helytelen jelszavak kizárása a szervezetben](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "minden vendég" – a használati feltételek létrehozása során létrehozott feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A használati feltételek létrehozása során a rendszer létrehoz egy új feltételes hozzáférési szabályzatot a "minden vendég" és az "összes alkalmazás" számára is. Az új csoportházirend-sablon létrehozásának és érvényesítési folyamat egyszerűsítésével vendégek újonnan létrehozott használati feltételek vonatkoznak.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "egyéni" feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A használati feltételek létrehozásakor létrejön egy új "egyéni" feltételes hozzáférési házirend-sablon is. Ez az új házirend-sablon lehetővé teszi a felhasználási feltételek létrehozását, majd a feltételes hozzáférési szabályzat létrehozási paneljén való azonnali megnyitását anélkül, hogy manuálisan kellene navigálnia a portálon.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Üzembe helyezése az Azure multi-factor Authentication az új és átfogó útmutatást

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás-biztonsági & védelme
 
Kiadás új részletes útmutatást nyújt az Azure multi-factor Authentication (MFA) telepítése a szervezetben.

Az MFA üzembe helyezési útmutatójának megtekintéséhez nyissa meg a [személyazonosság telepítési útmutatók](https://aka.ms/DeploymentPlans) tárházát a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD delegált felügyeleti szerepkör a nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** Access Control

A rendszergazdák mostantól delegálhatja kezelési feladatokat a globális rendszergazdai szerepkör hozzárendelése nélkül. Az új szerepkörök és funkciók a következők:

- **Új standard szintű Azure AD-rendszergazdai szerepkörök:**

    - **Alkalmazás-rendszergazda.** Minden alkalmazást, beleértve a regisztráció, egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és a licencelés, alkalmazást proxybeállításokat és jóváhagyás minden aspektusa kezelhető képesek (kivéve az Azure AD-erőforrások).

    - **Cloud Application Administrator.** Biztosít minden, az alkalmazás-rendszergazda funkcióit, kivéve az alkalmazásproxy, mert ez nem biztosít helyszíni hozzáférés.

    - **Alkalmazás fejlesztője.** Lehetővé teszi az alkalmazások regisztrációjának létrehozását, még akkor is, ha a **felhasználók regisztrációjának engedélyezése** lehetőség ki van kapcsolva.

- **Tulajdonos (az alkalmazáson belüli regisztráció és a vállalati alkalmazások beállítása, hasonlóan a csoport tulajdonosi folyamatához:**
 
    - **Az alkalmazás regisztrációjának tulajdonosa.** A saját tulajdonú alkalmazásregisztráció, beleértve az alkalmazás-jegyzékfájl, és további tulajdonosok hozzáadása minden aspektusa kezelhető szerepkörök.

    - **Vállalati alkalmazás tulajdonosa.** Szerepkörök a vállalati tulajdonban lévő alkalmazások, beleértve az egyszeri bejelentkezési beállításainak, alkalmazás-hozzárendelések és jóváhagyás számos szempontból kezelése (kivéve az Azure AD-erőforrások).

A nyilvános előzetes verzióval kapcsolatos további információkért tekintse [meg az Azure ad delegált alkalmazás-felügyeleti szerepkörök nyilvános előzetesét.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute-támogatás módosításai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Platform  

A szoftver egy szolgáltatott szoftverként, például az Azure Active Directory (Azure AD) úgy tervezték, hogy leginkább végigkövetésével közvetlenül az internethez, anélkül, hogy az expressroute-on vagy bármely más személyes VPN-alagutat. Ezért az Azure AD-szolgáltatások ExpressRoute az Azure-beli nyilvános és az Azure-közösségeket használó Microsoft-partnerekkel való támogatását követően leállítjuk. **2018** Ez a változás által érintett szolgáltatások előfordulhat, hogy figyelje meg, hogy az Azure AD-forgalom fokozatosan pótlása ExpressRoute-ból az interneten.

Amíg tudjuk módosítani a támogatási azzal is tisztában van vannak továbbra is olyan helyzetekben, ahol szüksége lehet használ egy dedikált Kapcsolatcsoportok a hitelesítési forgalomhoz. Ez az oka Azure ad-ben továbbra is támogatja a bérlőnkénti IP-címtartomány korlátozások használata az ExpressRoute és a szolgáltatások már a Microsoft társviszony-létesítés az "Egyéb Office 365 Online szolgáltatások"-Közösség tagjaival. Ha a szolgáltatásokat érintő eseményekről, de az ExpressRoute van szüksége, tegye a következőket:

- **Ha Azure-beli nyilvános társ-összevonást használ.** Lépjen a Microsoft-partneri kapcsolatra, és regisztráljon a **másik Office 365 Online Services (12076:5100)** Közösségre. További információ arról, hogyan lehet áthelyezni az Azure-ban a nyilvános Microsoft-partnerektől a [Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) -partnerek felé.

- **Ha Microsoft-társat használ.** Regisztráljon a **másik Office 365 online Service (12076:5100)** Közösségre. Az útválasztási követelményekkel kapcsolatos további információkért tekintse meg a ExpressRoute-útválasztási követelmények cikk a [BGP-Közösségek támogatása című szakaszát](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) .

Ha továbbra is dedikált áramköröket kell használnia, a Microsoft-fiókkal foglalkozó csapatának kell megadnia, hogy hogyan kérhet engedélyt a **másik Office 365 online szolgáltatás (12076:5100)** Közösség használatára. A MS Office-felügyelt felülvizsgáló Bizottság ellenőrzi-e ezeket a Kapcsolatcsoportok kell, és győződjön meg arról, hogy megszegéseinek műszaki tartja őket. Az Office 365-höz útvonalszűrők létrehozására tett kísérlet nem engedélyezett előfizetések hibaüzenetet fog kapni. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek a használati feltételek a Microsoft Graph API-k

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Fejlesztői élmény
 
Az Azure AD használati feltételeinek felügyeleti működéséhez Microsoft Graph API-kat adtunk hozzá. Létrehozhatja, frissítheti és törölheti a használati feltételeket.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Identitás-szolgáltatóként az Azure AD B2C az Azure AD több-bérlős végpont hozzáadása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Egyéni szabályzatok használatával most már hozzáadhat az Azure AD közös végpont identitásszolgáltatójaként az Azure AD B2C-ben. Ez lehetővé teszi, hogy az összes olyan jelentkezik be az alkalmazások az Azure AD-felhasználó bejegyzés hibaérzékeny pont. További információ [: Azure Active Directory B2C: lehetővé teszi a felhasználók számára, hogy egyéni szabályzatok használatával bejelentkezzenek egy több-bérlős Azure ad-identitásba](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címeket használnak az alkalmazások bárhonnan elérheti az Azure AD-alkalmazásproxy és a saját alkalmazások bejelentkezési bővítménye

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO
 
Felhasználók most már elérheti alkalmazások belső URL-címeket, még ha a vállalati hálózaton kívülről keresztül a saját alkalmazások biztonságos bejelentkezési bővítménye az Azure ad használatával. Ez minden olyan alkalmazással, amely bármely böngészőben, amelyen telepítve van hozzáférési Panel böngészőbővítményének használatánál az Azure AD-alkalmazásproxy használatával közzétett fog működni. Az URL-cím átirányítási funkció automatikusan engedélyezve lesz, miután egy felhasználó bejelentkezik a bővítményt. A bővítmény letölthető a [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), a [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)és a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)számára.

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Az Azure Active Directory - adatok Európában Európa-ügyfelek számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** GoLocal

Ügyfeleknek az Európai szükséges az Európai maradjon, és Európai adatközpontok értekezlet védelmében, és az Európai törvényi kívül nem replikálódnak. Ez a [cikk](https://go.microsoft.com/fwlink/?linkid=872328) részletesen ismerteti, hogy milyen információk lesznek tárolva az Európai adatközpontokban, valamint az adatközpontokon kívül tárolt információkkal kapcsolatos részleteket is tartalmaz. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználók SaaS-alkalmazás Integrációk – 2018. május

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Azure ad-ben automatizálhatja a létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolítását teszi lehetővé. 2018 május hozzáadtuk a felhasználók a következő alkalmazások az Azure AD-alkalmazásgyűjtemény támogatása:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Sarokköve OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók üzembe helyezését támogató összes alkalmazás listáját itt tekintheti meg: [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok Azure AD hozzáférési felülvizsgálatok és alkalmazás-hozzáférés mostantól lehetőséget nyújt az ismétlődő értékelések

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási
 
Hozzáférési felülvizsgálat csoportok és alkalmazások már általánosan elérhető az Azure AD Premium P2 részeként.  A rendszergazdák tudják a hozzáférési felülvizsgálatok például havonta vagy negyedévente rendszeres időközönként automatikusan ismétlődni csoporttagságok és alkalmazás-hozzárendelések konfigurálása.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure Active Directory-naplók (bejelentkezési és hitelesítési) mostantól elérhetők az MS Graph használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése
 
Az Azure AD tevékenységeket tartalmazó naplók, amely tartalmazza a bejelentkezések és auditnaplók, mostantól elérhetők az MS Graph használatával. A Microsoft közzétette a két végpontok keresztül az MS Graph ezek a naplók eléréséhez. Az első lépésekhez tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az Azure ad Reporting API-k programozott eléréséhez. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Élmény és a egy szervezeti hagyja a B2B-érvényesítési fejlesztései

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

**Egyszeri visszaváltás:** Miután megoszt egy erőforrást egy vendég felhasználóval a B2B API használatával – nem kell elküldenie egy különleges meghívó e-mailt. A legtöbb esetben a vendégfelhasználó az erőforrás eléréséhez, és megnyílik a beváltási élmény szerinti keresztül. Nincs több hatással miatt kimaradt e-maileket. Nincs több kell megadniuk a Vendég "Volt, érvényesítési hivatkozásra kattintva a rendszer akkor küld?". Ez azt jelenti, miután SPO a meghívó Managert használja – zavaros mellékleteket is rendelkezhet – belső és külső – bármely érvényesítési állapotát az összes felhasználó azonos kanonikus URL-CÍMÉT.

**Modern beváltási élmény:** Nincs több osztott képernyős beváltási Kezdőlap. A modern a felhasználók látják az adatvédelmi nyilatkozat a meghívó szervezetet, felhasználói jóváhagyás, ugyanúgy, mint az ilyen harmadik felek alkalmazásaihoz.

**A vendég felhasználói elhagyhatják a szervezeti** szervezetet: Ha a felhasználó kapcsolata egy szervezettel már véget ért, a szervezete elhagyhatja őket. Nincs több történt a meghívó szervezeti admin "el kell távolítani", több ritikus támogatási jegyeket.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – 2018. május

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 május tettünk elérhetővé összevonással 18 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozása [, Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe rabul Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel [, arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Az Azure Active Directory új részletes üzembe helyezési útmutatók

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory
 
Új, lépésenkénti útmutató a Azure Active Directory (Azure AD) üzembe helyezéséhez, beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), az egyszeri bejelentkezés (SSO), a feltételes hozzáférés (CA), az App proxy, a felhasználók kiépítése, a Active Directory összevonási szolgáltatások (AD FS) (ADFS) Átmenő hitelesítés (PTA ESP) és az ADFS jelszavas kivonatának szinkronizálása (PHS).

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg az [Identity Deployment Guides](https://aka.ms/DeploymentPlans) -tárházat a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése, – további alkalmazások betöltése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Ha nem találja az alkalmazás / egyszerű szolgáltatások? Hozzáadtunk további alkalmazásokat a vállalati alkalmazások betöltéséhez összes alkalmazások listáját. Alapértelmezés szerint 20 alkalmazás mutatjuk be. Most kattintson a lehetőségre **, majd** a további alkalmazások megjelenítéséhez. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** AD-kapcsolat  
A **termék képességei:** Identitás-életciklus kezelése
 
Az aad Connect kiadása tartalmaz egy nyilvános előzetes verziója a pingfederate-tel, az integráció május fontos biztonsági frissítések, számos hibajavítást és új nagyszerű új hibaelhárítási eszközei. A kibocsátási megjegyzések [itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)találhatók.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Az Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási

A hozzáférési felülvizsgálatok a csoportok és alkalmazások most már általánosan elérhetők az Azure AD Premium P2 részeként. Automatikusan a felülvizsgáló módosítások alkalmazásához, csoport vagy alkalmazás, a hozzáférési felülvizsgálatot követően a rendszergazdák konfigurálhatják. A rendszergazda azt is megadhatja, folyamatos hozzáférésre a felhasználó, mi történik, ha a felülvizsgálók nem válaszol, távolítsa el a hozzáférést, férjenek vagy rendszer ajánlásokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Azonosító-jogkivonatokat már nem adható vissza a lekérdezés response_mode használatával új alkalmazások esetében. 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A 2018 április 25-én vagy azt követően létrehozott alkalmazások nem igényelhetnek **id_token** a **lekérdezési** response_mode használatával.  Ekkor az Azure AD beágyazott OIDC előírásoknak, és segítséget nyújt az alkalmazások támadási felület csökkentése.  Az 2018. április 25. előtt létrehozott alkalmazások nem tiltják le a **lekérdezési** response_mode response_type **id_token**.  A visszaadott hiba, amikor a HRE id_token kér, a **AADSTS70007: "Query" nem támogatott "response_mode" érték a jogkivonat kérésekor**.

A **töredék** és a **form_post** response_modes továbbra is működik – új alkalmazásobjektumok létrehozásakor (például az alkalmazás proxyjának használatakor) response_modes, mielőtt új alkalmazást hozna létre.  

---
 
## <a name="april-2018"></a>2018. április 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Az Azure AD B2C hozzáférési tokent is általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C 

Most már elérheti az Azure AD B2C által védett webes API-k hozzáférési jogkivonatok használatával. A funkció nyilvános előzetes verzióról való általánosan elérhető A felhasználói felület konfigurálása az Azure AD B2C-alkalmazást és a webes API-kat úgy lett továbbfejlesztve, és más kisebb fejlesztések történtek.
 
További információt a [Azure ad B2C: hozzáférési tokenek igénylése](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)című témakörben talál.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Egyszeri bejelentkezés beállításainak a SAML-alapú alkalmazások tesztelése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Egyszeri bejelentkezési SAML-alapú alkalmazások konfigurálása, ha már tudjuk tesztelni az integrációt a lapon. Bejelentkezés során hibát észlel, ha a hibát a tesztelési élményt biztosíthat, és az Azure AD nyújt megoldási lépéseket az adott probléma megoldásához.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Az SAML-alapú egyszeri bejelentkezések hibakeresése Azure Active Directory-alkalmazásokban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei mostantól felhasználónkénti jelentéskészítéssel rendelkeznek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
A rendszergazdák mostantól egy adott használati feltételek kiválasztása, és tekintse meg az összes felhasználót, hogy hozzájárult, hogy, hogy a jelen használati feltételek és milyen dátum/idő, került sor.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Az Azure AD Connect Health: Kockázatos IP-cím az AD FS extranetes fiókzárolási védelem 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** & Jelentéskészítés figyelése

Connect Health mostantól támogatja a képességére IP-címek, amely meghaladja a küszöbértéket hibás felhasználónév/jelszó bejelentkezések óránkénti vagy napi rendszerességgel. Ez a szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés IP-cím és az óránként/naponta alapján testre szabható küszöbértékkel hozza létre a sikertelen bejelentkezések száma.
- E-mail alapú értesítések megjelenítése, ha egy adott IP-cím túllépte a küszöbértéket, hibás felhasználónév/jelszó bejelentkezések egy óránként/naponta történik.
- A letöltési lehetőséget ehhez az adatok részletes elemzés

További információ: [kockázatos IP-jelentés](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás konfigurációs metaadatfájl vagy URL-címe

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

A vállalati alkalmazások oldalon rendszergazdák feltölthet egy SAML-metaadatfájl SAML-alapú bejelentkezés az AAD-galéria és a katalógusban nem szereplő alkalmazás konfigurálásához.

Az Azure AD alkalmazás összevonási metaadatainak URL-címe segítségével ezenkívül a célzott alkalmazás egyszeri bejelentkezés konfigurálása.

További információ: [az egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek a Azure Active Directory alkalmazás-galériában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD használati feltételek már általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 

Az Azure AD használati feltételei a nyilvános előzetes verzióról általánosan elérhetővé váltanak.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Az adott szervezetek a B2B-felhasználók meghívások engedélyezése vagy letiltása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

Mostantól megadhatja, mely kívánt megosztás és együttműködés az Azure AD B2B együttműködés a fiókpartner-szervezetek. Ehhez hozzon létre adott választhatja engedélyezik vagy megtagadják a tartományok. Tartomány le van tiltva, ezek a képességek használatával, amikor az alkalmazottak már nem küldhet meghívókat személyek ebben a tartományban.

Ez segít, hogy ki férhet hozzá az erőforrásokat, miközben lehetővé teszi a zökkenőmentes felhasználói élmény engedélyezett.

Ez a VÁLLALATKÖZI együttműködési funkció minden Azure Active Directory ügyfél számára elérhető, és prémium szintű Azure AD-funkciókkal, például a feltételes hozzáféréssel és az identitás-védelemmel együtt használható a külső üzleti felhasználók aláírásának részletesebb szabályozása érdekében. és hozzáférés.

További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény elérhető új összevont alkalmazások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2018 április tettünk elérhetővé összevonással 13 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

Az HCM, [a FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), a [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), a [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), a [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), a [szervezeti diagram most](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), a [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), a [AppNeta Teljesítményfigyelő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), a [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , a [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), a [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), a polc, a [Biztonság](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

Azure Active Directory (Azure AD) B2B együttműködési lehetőségeket az Azure ad a fiókpartner-szervezetek vendégfelhasználók használó szervezetek, most már megadhat a B2B-felhasználók a helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások az SAML-alapú hitelesítés vagy az integrált Windows-hitelesítés (IWA) a Kerberos által korlátozott delegálás (KCD).

További információkért lásd: [B2B-felhasználók engedélyezése az Azure ad-ben a helyszíni alkalmazásokhoz való hozzáféréshez](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Egyszeri bejelentkezés az integrációs oktatóanyagok lekérése az Azure Marketplace-ről

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
**Termék képesség:** harmadik féltől származó integráció

Ha az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) -en felsorolt alkalmazások TÁMOGATJÁK az SAML-alapú egyszeri bejelentkezést, a letöltés gombra kattintva elérhetővé **válik** az alkalmazáshoz társított integrációs oktatóanyag. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Nagyobb teljesítmény az Azure ad-ben a felhasználók automatikus átadása a SaaS-alkalmazásokban

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Korábban, az Azure Active Directory-felhasználó kiépítési összekötőket az olyan SaaS-alkalmazások (például Salesforce, ServiceNow és a Box) használó ügyfelek sikerült teljesítménycsökkenést tapasztal, ha az Azure AD-bérlőt tartalmazott több mint 100 000 összevont felhasználók és csoportok, és meghatározni, hogy mely felhasználókat kell létrehozni felhasználó és csoport-hozzárendelések használta.

2018. április 2. fontos teljesítményjavítás üzembe helyezése az Azure AD létesítési szolgáltatás, amely nagy mértékben csökkentheti az Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálás végrehajtásához szükséges idő a.

Ennek eredményeképpen a számos ügyfél, amely eddig szinkronizálások az alkalmazásoknak a tartott, hány nap vagy soha nem fejeződött be, most belül percek vagy órák során hiba.

További információ: [Mi történik a kiépítés során?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Az önkiszolgáló jelszó-visszaállítást a hibrid Azure ad-ben a Windows 10-es zárolási képernyőről csatlakoztatott gépek

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés
 
Frissítettük a Windows 10-es SSPR funkció támogatását olyan gépeken, amelyek hibrid Azure AD-hez. Ez a funkció érhető el a Windows 10 RS4 lehetővé teszi, hogy a felhasználók visszaállíthatják a jelszavukat a Windows 10-es gép a zárolási képernyőről. Felhasználók, akik engedélyezve van, és új jelszó önkiszolgáló kérésére regisztrált Ez a szolgáltatás képes használni.

További információ: [Az Azure ad-jelszó alaphelyzetbe állítása a bejelentkezési képernyőn](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március
 
### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítés

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az Azure AD elküld egy értesítést, ha egy gyűjtemény egy tanúsítványt, vagy a katalógusban nem szereplő alkalmazás érvényessége hamarosan lejár. 

Egyes felhasználók nem kapják meg a vállalati alkalmazásokhoz SAML-alapú egyszeri bejelentkezés konfigurálva értesítések. A probléma megoldódott. Az Azure AD elküldi az értesítést a tanúsítványok, 7, 30 és 60 napon belül lejár. Ön ezt az eseményt naplóiban láthatja. 

További információkért lásd:

- [Az összevont egyszeri bejelentkezés tanúsítványainak kezelése Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Tevékenység-jelentések naplózása a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C-vel a Twitter és a GitHub Identitásszolgáltatók

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Most már hozzáadhat Twitter- és GitHub identitásszolgáltatójaként az Azure AD B2C-ben. Twitter kerül át a nyilvános előzetes verzióban általánosan elérhető GitHub kiadásra nyilvános előzetes verzióban érhető el.

További információ: [Mi az az Azure ad B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngésző-hozzáférés korlátozása Intune Managed Browser használatával az Azure AD alkalmazás-alapú feltételes hozzáférés iOS-hez és Androidhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
**Már nyilvános előzetes verzióban érhető el!**

**Egyszeri bejelentkezés Intune Managed Browser:** Az alkalmazottak használhatnak egyszeri bejelentkezést a natív ügyfeleken (például a Microsoft Outlookban) és az Azure AD-hez csatlakoztatott összes alkalmazás Intune Managed Browser.

**Intune Managed Browser feltételes hozzáférési támogatás:** Mostantól megkövetelheti, hogy az alkalmazottak az Intune által felügyelt böngészőt használják az alkalmazás alapú feltételes hozzáférési szabályzatok használatával.

Erről a [blogbejegyzésben](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)olvashat bővebben.

További információkért lásd:

- [Alkalmazás-alapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed Browser-szabályzatok konfigurálása](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Alkalmazás Webalkalmazásproxy-parancsmagok a Powershell általánosan elérhető a modul

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control
 
Alkalmazásproxy parancsmagok támogatása már általánosan elérhető a Powershell-modult az! Ehhez szükség, hogy legyen naprakész a Powershell-modulok – Ha Ön több mint egy év mögött, egyes parancsmagok leállhat. 

További információ: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Közvetlen egyszeri bejelentkezés használatával nem interaktív protokoll által támogatott natív Office 365-ügyfelek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A felhasználó Office 365-ügyfél natív használatával (verzió 16.0.8730.xxxx és újabb) csendes bejelentkezési felületet közvetlen egyszeri bejelentkezés használatával. A támogatási szolgáltatását az hozzáadását a nem interaktív (WS-Trust) protokoll az Azure AD.

További információ: [how bejelentkezni egy natív ügyfélen zökkenőmentes egyszeri bejelentkezéssel?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>A felhasználók kapnak a beavatkozás nélküli bejelentkezést, a közvetlen egyszeri bejelentkezés, ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD-bérlő végpontok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A felhasználók csendes bejelentkezési élményt nyújtanak, zökkenőmentes egyszeri bejelentkezéssel, ha egy alkalmazás (például `https://contoso.sharepoint.com`) bejelentkezési kéréseket küld az Azure AD bérlői végpontoknak – vagyis `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` – az Azure AD közös végpontja (`https://login.microsoftonline.com/common/<...>`) helyett.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Közvetlen egyszeri bejelentkezés bevezetése felhasználók Intranet zóna beállítását csak egy Azure ad-ben URL-cím helyett a korábban a két URL-címet hozzá kell adnia

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Ha a zökkenőmentes SSO-t a felhasználók számára szeretné kideríteni, csak egy Azure AD-URL-címet kell hozzáadnia a felhasználók intranetes zónájának beállításaihoz a csoportházirend használatával Active Directory: `https://autologon.microsoftazuread-sso.com`. Korábban az ügyfelek kellett adjon hozzá két URL-címet.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2018 március tettünk elérhetővé összevonással 15 új alkalmazásokról, az alkalmazáskatalógusban támogatja:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), segéd: FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Az Azure-erőforrások PIM szolgáltatás általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Ha az Azure AD Privileged Identity Management-címtárbeli szerepkörökhöz tartozó használ, mostantól használhatja a PIM időhöz kötött access és a hozzárendelés képességek például az előfizetések, erőforráscsoportok, virtuális gépeket és bármely más, a támogatott erőforrás Azure-erőforrások szerepköreihez tartozó az Azure Resource Manager által. Többtényezős hitelesítés kikényszerítéséhez a Just-In-Time szerepkörök aktiválása során, és jóváhagyott módosítása a windows együttes aktiválások ütemezése. Ez a kiadás emellett bővült a fejlesztések többek között egy frissített felhasználói Felületet, jóváhagyási munkafolyamatokat, valamint a hamarosan lejáró szerepkörök bővítése, és lejárt szerepkörök megújítása a nyilvános előzetes verzióban nem érhető el.

További információ: [PIM for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Nem kötelező jogcímek, az alkalmazások jogkivonatok (nyilvános előzetes verzió) hozzáadása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD-alkalmazás mostantól az egyéni vagy választható jogcímkérések JWTs vagy SAML jogkivonatokat.  Ezek a jogcímeket a felhasználó vagy bérlői kapcsolatos, amelyek nem tartoznak a jogkivonat mérete vagy alkalmazhatósági megkötések miatt alapértelmezés szerint.  Ez jelenleg nyilvános előzetes verzióban elérhető az Azure AD-alkalmazások az 1.0-s és 2.0-s verziójú végpontok.  Információ dokumentációjában milyen jogcímek lehet hozzáadni, a tanúsítványkérelmeket az alkalmazásjegyzék szerkesztése.  

További információ: [opcionális jogcímek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja PKCE biztonságosabb OAuth-folyamatok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Vegye figyelembe a PKCE, amely lehetővé teszi több biztonságos kommunikáció során az OAuth 2.0 engedélyezési kód engedélyezési folyamatával támogatása az Azure AD-docs frissítve lett-e.  Az 1.0-s és 2.0-s verziójú végpontok S256 mind a titkosítatlan szöveges code_challenges támogatottak. 

További információ: [engedélyezési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Az összes felhasználói attribútum értékei a Workday Get_Workers API-ban elérhető kiépítés támogatása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
A nyilvános előzetes verziója bejövő való üzembe helyezést, a Workday az Active Directory és az most már az Azure AD képes kinyerni és kiépítés a Workday Get_Workers API-ban elérhető összes attribútum értéket támogatja. Ez hozzáadja a több száz további standard szintű csomag támogatja, és tartalmazza a szükséges kezdeti verziójában a Workday túlmutató egyéni attribútumok bejövő összekötő kiépítése.

További információkért lásd: [a munkanap felhasználói attribútumai listájának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A dinamikus csoporttagság statikusra, és fordítva módosítása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés
 
Akkor lehet módosítani egy csoport tagságát kezeléséről. Ez akkor hasznos, ha meg szeretné tartani csoport neve és azonosítója a rendszeren, hogy a csoport bármely meglévő hivatkozások még érvényesek; Új csoport létrehozása miatt frissíteni kellene az ezeket a hivatkozásokat.
Frissítettük az Azure AD felügyeleti központban az funkciót. Most már használó ügyfelek átalakíthatják a meglévő csoportokat a dinamikus tagsági hozzárendelt tagságot és fordítva. A meglévő PowerShell-parancsmagok is továbbra is elérhetők.

További információ: a [Azure Active Directory csoportok dinamikus tagsági szabályai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Közvetlen egyszeri bejelentkezés továbbfejlesztett kijelentkezési viselkedés

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Korábban még akkor is, ha a felhasználók explicit módon az Azure AD által védett alkalmazáshoz kijelentkezik, ezek lenne automatikusan megtörténik vissza a közvetlen egyszeri bejelentkezés használatával, ha azok megpróbált hozzáférni egy Azure AD-alkalmazást újra a vállalati hálózaton belül, tartományhoz csatlakoztatott eszközök. Ezzel a kijelentkezési használata támogatott.  Ez lehetővé teszi, hogy a felhasználók kiválaszthatják az azonos vagy eltérő Azure AD-fiók jelentkezzen be újra, alatt automatikusan megtörténik a közvetlen egyszeri bejelentkezés használata helyett.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Application Proxy Connector ezen verziója, amely a 1.5.402.0

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Identitás-biztonsági & védelme
 
A connector ezen verziója fokozatosan vezetjük be November keresztül. A connector új verziója az alábbi újításokat tartalmazza:

- Az összekötő már tartományi szintű cookie-k helyett altartomány szintjének beállítása. Ez biztosítja, hogy egy egyenletesebb egyszeri Bejelentkezéses felhasználói élmény, és elkerülhető az redundáns hitelesítési kérések.
- Darabolásos kódolás kérelmek támogatása
- Továbbfejlesztett összekötő állapotfigyelés 
- Számos hibajavítás és stabilitási fejlesztések

További információ: az [Azure ad Application proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>2018. február
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigáció a felhasználók és csoportok kezelése

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Címtár-kezelés  
A **termék képességei:** Directory

Egyszerűsítettük a navigációt a felhasználók és csoportok kezelése. Akkor most már elhagyhatja a címtár áttekintő közvetlenül, a lista az összes olyan felhasználó, a könnyebb elérhetőség érdekében a törölt felhasználók listájához. Is elérheti a címtár áttekintő a közvetlenül a csoportok, a könnyebb elérhetőség érdekében a csoport felügyeleti beállítások listája. És még a címtár áttekintő lapján kereshet egy felhasználó, csoport, a vállalati alkalmazás vagy alkalmazásregisztráció. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Bejelentkezések és naplózási jelentések a 21Vianet által működtetett Microsoft Azure-ban (Azure China 21Vianet)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure Stack  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure Active Directory naplózási jelentések mostantól elérhetők a 21Vianet (Azure China 21Vianet) példányok által működtetett Microsoft Azure-ban. A következő naplók kapcsolódnak foglalja magában:

- **Bejelentkezési tevékenységek naplói** – a bérlőhöz társított összes bejelentkezési naplót tartalmazza.

- **Önkiszolgáló jelszó-naplózási naplók** – tartalmazza az összes SSPR-naplót.

- **Címtár-felügyeleti naplók** – tartalmazza a címtár-felügyelettel kapcsolatos naplókat, például a felhasználók felügyeletét, az alkalmazások felügyeletét és egyebeket.

Ezek a naplók is, hogy a környezet működésébe elemzéseket kaphat. A megadott adatokkal a következőket teheti:

- Határozza meg, hogyan az alkalmazások és szolgáltatások vannak az egyes a felhasználók számára.

- Végezzen hibaelhárítást a hibákat, amelyek megakadályozzák a felhasználók hozzáférése a munkájukat.

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"A jelentés olvasó" szerepkör (nem rendszergazdai szerepkör) használata az Azure AD-Tevékenységjelentések megtekintése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Tartozó ügyfelek visszajelzés engedélyezése nem rendszergazda szerepkörök férnek hozzá az Azure Active Directory-naplókat, engedélyeztük a lehetőségét, hogy a felhasználók számára a "Jelentés olvasó" szerepkör a bejelentkezések hozzáférési és naplózási tevékenység belül az Azure Portalon, valamint a Graph API-k használatával. 

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az EmployeeID jogcím felhasználói attribútumként és felhasználói azonosítóként

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az **AlkalmazottKód** -t felhasználói azonosítóként és felhasználói attribútumként is konfigurálhatja a felhasználók és a vállalatközi vendégek számára az SAML-alapú bejelentkezési alkalmazásokban a vállalati alkalmazás felhasználói felületén.

További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Helyettesítő karakterek használata az Azure AD-alkalmazásproxy egyszerűsített felügyelet

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Felhasználói hitelesítés
 
Alkalmazás központi telepítésének megkönnyíti, valamint csökkentheti a felügyelettel járó többletterhelést, mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételének lehetősége. Helyettesítő karaktert tartalmazó alkalmazás közzététele, hajtsa végre a szabványos alkalmazás-közzétételi folyamat, de használja egy helyettesítő karaktert a belső és külső URL-címeket.

További információ: [helyettesítő alkalmazások a Azure Active Directory alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfigurálását támogató új parancsmagok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Platform

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Platform

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform
 
Az Azure AD Connect az az előnyben részesített eszköz szinkronizálhatja az Azure AD között, és a helyszíni adatforráshoz, beleértve a Windows Server Active Directory és az LDAP adatait.

>[!Important]
>A build bemutatja a séma és a szinkronizálási szabály módosításokat. Az Azure AD Connect szinkronizálási szolgáltatás elindít egy teljes importálást és teljes szinkronizálást lépéseket frissítés után. További információ a viselkedés módosításáról: a [teljes szinkronizálás késleltetése a frissítés után](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ebben a kiadásban a következő frissítéseket és a változások rendelkezik:

**Kijavított problémák**

- Javítsa ki a időzítési háttérfeladatok laphoz Partíciószűrés a következő lapra történő váltáskor.

- Kijavítva a hiba, amely engedély nélküli hozzáférési kísérlet során a configdb elemre egyéni művelet okozza.

- Kijavítva a hiba, helyreállítás az sql-kapcsolat időtúllépése.

- Kijavítva a hiba, ahol a SAN helyettesítő karaktereket is tartalmazó tanúsítványok előfeltétel-ellenőrzés sikertelen.

- Kijavítva a hiba, okozó miiserver.exe összeomlási AAD connector exportálás során.

- Kijavítva a hiba, ahol a egy helytelen jelszómegadási kísérlet DC bejelentkezve futtatását okozta az AAD connect varázsló konfigurációjának módosítása

**Új funkciók és Újdonságok**
 
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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Az Azure AD alkalmazás-alapú feltételes hozzáféréssel együtt használható Intune App Protection szabályzatokat támogató alkalmazások

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

További alkalmazásokat is felvettünk, amelyek támogatják az alkalmazás-alapú feltételes hozzáférést. Most már hozzáférhet az Office 365-höz és más Azure AD-hez csatlakozó felhőalkalmazások ezeket a jóváhagyott ügyfélalkalmazások használatával.

A következő alkalmazások szerint február végétől lesznek hozzáadva:

- Microsoft Power BI

- A Microsoft indítója

- A Microsoft számlázás

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>A Mobile Experience Használati feltételek frissítése 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának

Ha megjelenik a használati feltételek, akkor a problémák megtekintése lehetőségre kattinthat **? Kattintson ide**. A hivatkozásra kattintva megnyílik a használati feltételeket, natív módon az eszközön. A betűméret a dokumentum vagy eszköz a képernyő méretétől függetlenül nagyításra, és igény szerint, olvassa el a dokumentumot. 

---
 
## <a name="january-2018"></a>2018. január
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

A 2018 január összevonási támogatással a következő új alkalmazások az alkalmazáskatalógusban lettek hozzáadva:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelem-kezelő szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont könyvtár és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Jelentkezzen be a további észlelt kockázattal érintett

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Az észlelt kockázatok észlelésével kapcsolatos betekintés az Azure AD-előfizetéshez van kötve. Az Azure AD Premium P2 kiadás az a legrészletesebb információkat minden mögöttes észlelés beolvasása.

A prémium szintű Azure AD P1 kiadással a licenc által nem érintett észlelések nem jelennek meg, mivel a kockázatkezelési szolgáltatás a további kockázatok észlelésével jelentkezik.

További információ: [Azure Active Directory kockázati észlelések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazásokhoz a felhasználó hozzáférési paneljein elrejtése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

Most már jobban kezelhető hogyan Office 365-alkalmazások jelennek meg az új felhasználói beállítás segítségével a felhasználók hozzáférési paneljein. Ez a beállítás hasznos lehet a felhasználó hozzáférési paneljein alkalmazások számának csökkentése Ha inkább csak Office-alkalmazások jelennek meg az Office portálon. A beállítás a **felhasználói beállítások** területen található, és a **felhasználók csak az Office 365-alkalmazásokat látják az Office 365-portálon**.

További információ: [alkalmazás elrejtése a felhasználói felületről Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Zökkenőmentes bejelentkezési engedélyezve a jelszó SSO közvetlenül az alkalmazás URL-alkalmazásokba 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

A saját alkalmazások webböngésző-bővítmény már elérhető kényelmes eszköz használatával, amely áttekintést nyújt a saját alkalmazások egyszeri bejelentkezés lehetőségét a parancsikont a böngészőben. Telepítés után a felhasználó a böngészőben, amely az alkalmazások gyors hozzáférést biztosít számukra egy gofri ikon látható. Felhasználók mostantól igénybe veheti:

- Lehetővé teszi a közvetlenül bejelentkezni jelszó SSO-alapú alkalmazások az alkalmazás bejelentkezési lapján
- Indítsa el minden olyan alkalmazást kész keresési funkciója
- A bővítmény a legutóbb használt alkalmazások parancsikonjai
- A bővítmény a Microsoft Edge, a Chrome és a Firefox számára érhető el.
 
További információt a [saját alkalmazások biztonságos bejelentkezési bővítménnyel](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)foglalkozó témakörben talál.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élmény a klasszikus Azure portálon kivonása

**Írja be a következőt:** Elavult   
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Directory

8 2018 január, az Azure AD felügyeleti-től a klasszikus Azure portál felület kivonásra került. A klasszikus Azure portál maga funkciókészletét együtt történt. A későbbiekben az Azure ad [felügyeleti központot](https://aad.portal.azure.com) kell használnia az Azure ad portálon alapuló felügyeletéhez.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor-webportál kivonása

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Directory
 
2018. január 8. a PhoneFactor-webportál visszavontuk. Az MFA-kiszolgáló felügyeletéhez használt ezen a portálon, de ezekhez a függvényekhez rendelkezik át lett helyezve az Azure Portalon a portal.azure.com webhelyen. 

Az MFA-konfiguráció a következő helyen található: **Azure Active Directory \> MFA-kiszolgáló**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-életciklus kezelése  


Az általánosan elérhető az új Azure Active Directory felügyeleti konzol és az új API-k már elérhető a tevékenység és a biztonsági jelentések, a jelentéskészítő API-k az a "/ jelentések" végpont kivonásra került, 2017. December 31-ig a végén.

**Mi érhető el?**

Az új felügyeleti konzol az átmenet részeként elérhetővé vált 2 új API-k az Azure AD-Tevékenységnaplók beolvasása. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelések API-n keresztül érhetők el Microsoft Graphban.

További információkért lásd:

- [Ismerkedés a Azure Active Directory Reporting API-val](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [A Azure Active Directory Identity Protection és a Microsoft Graph első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
Most nyissa meg a hozzáférési panelen és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Lépjen a [MyApps portálra](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a listából. 

3. A **profilban**válassza a **használati feltételek áttekintése**elemet. 

4. Most már megtekintheti a használati feltételeket a elfogadva. 

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezési felület

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD és a Microsoft identitás-fiókrendszer előkészíthetik a korábbinál sokoldalúbban úgy, hogy egy egységes megjelenést és működést. Emellett az Azure AD bejelentkezési oldal gyűjti a felhasználónév először egy második képernyőn a hitelesítő adatok követ.

További információ: [Az Azure ad új bejelentkezési felülete már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)verzióban érhető el.
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérések: egy új "bejelentkezve szeretnék maradni" tapasztalattal az Azure AD-be

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD bejelentkezési oldalának **bejelentkezett állapotban** jelölőnégyzete egy új, a sikeres hitelesítés után megjelenő üzenettel lett lecserélve. 

Ha **igennel** válaszol erre a kérésre, a szolgáltatás állandó frissítési jogkivonatot biztosít. Ez a viselkedés ugyanaz, mint amikor bejelölte a a **bejelentkezve** szeretnék maradni jelölőnégyzetet a régi felületén. Összevont bérlők esetén ez a kérdés mutatja be, miután sikeresen hitelesíteni az összevont szolgáltatással.

További információ [: kevesebb bejelentkezési kérés: az Azure ad-ben az új "bejelentkezve tartom" élmény előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)verzióban érhető el. 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>A használati feltételek elfogadása előtt ki kell bővíteni kell a konfiguráció hozzáadása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
Lehetőség a rendszergazdák számára szükséges a felhasználókat a feltételeket, mielőtt elfogadja a használati feltételek megtekintése.

Válassza ki **a** be vagy **ki** lehetőséget, hogy a felhasználók kiterjesszék a használati feltételeket. A **on** beállítás megadásához a felhasználóknak meg kell tekinteniük a használati feltételeket az elfogadásuk előtt.

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Jogosult szerepkör-hozzárendelések hatókörrel rendelkező aktiválás

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Használhatja a hatókörön belüli aktiválási kevesebb, mint az eredeti hozzárendelés alapértelmezett autonómia jogosult Azure-erőforrás szerepkör-hozzárendelések aktiválása. Ilyen például, ha a kapott az előfizetés tulajdonosaként a bérlőben. Hatókörön belüli-aktiválási aktiválhatja a tulajdonosi szerepkör legfeljebb öt erőforrások (például az erőforráscsoportok és virtuális gépek) előfizetésen belül található. Az aktiválás felmerülő előfordulhat, hogy az esélye, kritikus fontosságú Azure-erőforrások nemkívánatos módosítások végrehajtása.

További információ: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Az Azure AD-alkalmazásgyűjtemény új összevont alkalmazások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2017 December vezettünk be, ezeket az új összevonási alkalmazásokat az app-galériában támogatja:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI digitális kirakat](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe [, FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [kép működik](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure ad-integráció](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO a Bamboo by Solution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for bitbucket by Solution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure ad-integráció.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamatokat

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Az Azure AD-címtárbeli szerepkörökhöz tartozó jóváhagyási munkafolyamat szolgáltatás általánosan elérhető.

Jóváhagyási munkafolyamat, a rendszerjogosultságú szerepkör rendszergazdák megkövetelhetik, szerepkör-aktiválás kéréséhez jogosult-szerepkör tagjai az emelt szintű szerepkör használatához. Több felhasználó és csoport is lehet delegált jóváhagyó felelőssége. Jogosult szerepkör tagjai értesítéseket kaphat, amikor a jóváhagyás befejeződött, és azok szerepét aktív.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Az átmenő hitelesítés: a Skype for Business-támogatás

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Átmenő hitelesítés most támogatja a felhasználói bejelentkezéseket a Skype vállalati verzióhoz való ügyfél, amely támogatja a modern hitelesítést, amely tartalmazza az online és a hibrid topológiák. 

További információ: [a Skype for Business-topológiák, amelyek modern hitelesítéssel támogatottak](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC (előzetes verzió) az Azure AD Privileged Identity Management frissítések

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Az Azure AD Privileged Identity Management (PIM) az Azure szerepköralapú hozzáférés-vezérlés (RBAC) a nyilvános előzetes verzióban frissítéssel az alábbi műveleteket hajthatja végre:

* Ezen éppen elég felügyelettel.
* Erőforrás-szerepkörökkel aktiváláshoz jóváhagyásra van szükség.
* Ütemezzen egy jövőbeli aktiválását egy szerepkör, amely jóváhagyásra van szüksége a két Azure AD és az Azure RBAC-szerepkörökhöz.
 
További információ: [Privileged Identity Management for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Access Control szolgáltatás kivonása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Access Control szolgáltatás  
A **termék képességei:** Access Control szolgáltatás 

Az Azure Active Directory Access Control (más néven a hozzáférés-vezérlés szolgáltatás) kivezetjük késői 2018-ban. Az elkövetkező néhány hét fog adni egy részletes ütemezés és a magas szintű áttelepítési útmutató tartalmaz további információt. Hagyhatja megjegyzéseket ezen az oldalon bármilyen kérdése van, a hozzáférés-vezérlés szolgáltatással kapcsolatos, és a egy csapattag rájuk válaszolni fog.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune Managed Browser böngésző-hozzáférés korlátozása 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

Office 365-höz és más Azure AD-hez kapcsolódó felhőalapú alkalmazáshoz való böngészőalapú hozzáférés egy jóváhagyott alkalmazáshoz, az Intune Managed Browser használatával korlátozhatja. 

Mostantól a következő feltételt állíthatja be az alkalmazás alapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Böngésző

**Mi a változás hatása?**

Használja ezt az állapotot még ma, hozzáférés le lesz tiltva. Az előzetes verzió érhető el, ha minden hozzáférést, a managed browser alkalmazást használatára lesz szükség. 

Keresse meg ezt a funkciót, és a közelgő blogok és kibocsátási megjegyzések további információt. 

További információ: [feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)listáján:

- [Microsoft-Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek több nyelv támogatása

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának

A rendszergazdák most már hozhat létre új használati feltételek, amelyek több PDF-dokumentumok tartalmazzák. Megjelölheti a PDF-dokumentumok egy megfelelő nyelvet. Felhasználók preferenciáik alapján megfelelő nyelven jelennek meg a PDF-fájlt. Ha nem szerepel, az alapértelmezett nyelv jelenik meg.

---
 
### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó-visszaírási ügyfél állapotát

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Most már megtekintheti a helyi jelszó-visszaírási ügyfél állapotát. Ez a beállítás a [jelszó-visszaállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) oldal helyszíni **integráció** szakaszában érhető el. 

Probléma merül fel a helyszíni visszaírási ügyfélhez a kapcsolattal rendelkező, lásd: egy hibaüzenet, amely a következőket tartalmazza:

- Miért nem lehet kapcsolódni a helyszíni visszaírási ügyfélhez információk.
- Dokumentáció, amely segítséget nyújt a problémák elhárításához mutató hivatkozást. 

További információ: helyszíni [integráció](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-alkalmazás-alapú feltételes hozzáférés 
 
**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Identitás biztonsága és védelme

Mostantól az [Azure ad-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)használatával korlátozhatja az Office 365 és más Azure ad-hez kapcsolódó felhőalapú alkalmazások elérését az Intune app Protection-szabályzatokat támogató [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) számára. Az Intune alkalmazásvédelmi szabályzatai segítségével konfigurálhatja, és ezek ügyfélalkalmazások számára a vállalati adatok védelmét.

Az [eszközökön alapuló](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) feltételes hozzáférési szabályzatok [alkalmazásának](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) kombinálásával rugalmasan kezelheti a személyes és a vállalati eszközökre vonatkozó adatvédelmet.

Az alábbi feltételek és vezérlők mostantól használhatók az alkalmazás-alapú feltételes hozzáféréssel:

**Támogatott platform feltétele**

- iOS
- Android

**Ügyfélalkalmazások állapota**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése

További információ: [Azure ad alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Az Azure Portalon az Azure AD-eszközök kezelése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Identitás biztonsága és védelme

Most már megtalálhatja az Azure ad-hez csatlakoztatott eszközök és a egy helyen az eszközzel kapcsolatos tevékenységeket. Nincs új felügyeleti felület kezeli az eszközidentitások és a beállítások az Azure Portalon. Ebben a kiadásban a következőket teheti:

- Az Azure AD-ben feltételes hozzáféréshez elérhető összes eszköz megtekintése.
- Tekintse meg a tulajdonságokat, többek között a hibrid Azure AD-hez csatlakoztatott eszközök.
- Keresse meg a BitLocker-kulcsok az Azure AD-hez csatlakoztatott eszközök, az eszköz Intune-nal, és további kezelését.
- Az Azure AD-eszközzel kapcsolatos beállítások kezelése.

További információ: [eszközök kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Támogatás macOS platformként az Azure AD feltételes hozzáféréshez 

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme 

Mostantól belefoglalhatja (vagy kizárhatja) a macOS-t az Azure AD feltételes hozzáférési szabályzatában található eszköz-platform feltételként. MacOS-gépeken, a támogatott eszközplatformok igény szerinti hozzáadásával a következőket teheti:

- **MacOS-eszközök regisztrálása és kezelése az Intune használatával.** Más platformokon, például iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el a macOS-hez egységes regisztrációk ehhez. Az új céges portál alkalmazás macOS-hez készült segítségével egy eszközöm Intune-nal, és regisztrálja az Azure ad-ben.
- **Győződjön meg arról, hogy a macOS-eszközök megfelelnek a szervezetnek az Intune-ban definiált megfelelőségi szabályzatoknak.** Az Intune-ban az Azure Portalon mostantól beállíthat is macOS-eszközök megfelelőségi szabályzatainak. 
- **Az Azure AD-alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő macOS-eszközökre.** A feltételes hozzáférési szabályzat létrehozásához a macOS-t külön eszköz platformként kell megválasztani. Most már készíthet macOS-specifikus feltételes hozzáférési szabályzatokat az Azure-beli megcélzott alkalmazás számára.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló kiterjesztése az Azure multi-factor Authentication 

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:**  Multi-Factor Authentication  
A **termék képességei:** Felhasználói hitelesítés

A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication a hitelesítési infrastruktúra felhőalapú multi-factor Authentication funkcióinak hozzáadja a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló kiterjesztésű telefonhívás, szöveges üzenet vagy telefonos alkalmazás ellenőrzés adhat a meglévő hitelesítési folyamatot. Nem kell telepítése, konfigurálása és új kiszolgálók karbantartása. 

Ez a bővítmény olyan szervezeteknek, amelyek számára védelmet kíván biztosítani virtuális magánhálózati kapcsolatok az Azure multi-factor Authentication-kiszolgáló üzembe helyezése nélkül lett létrehozva. A hálózati házirend-kiszolgáló bővítmény úgy működik, mint az adapter beállításai között, adja meg a hitelesítés második tényezőjét, a RADIUS- és felhőalapú Azure multi-factor Authentication összevont, vagy a szinkronizált felhasználók.

További információ: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy végleg eltávolítani a törölt felhasználók

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Directory 

Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Törölt felhasználói visszaállítása. 
- Felhasználó végleges törlése.

**A kipróbáláshoz:**

1. Az Azure AD felügyeleti központban válassza a **kezelés** szakaszban az [összes felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) lehetőséget. 

2. A **Megjelenítés** listából válassza a **nemrég törölt felhasználók**lehetőséget. 

3. Válassza ki egy vagy több nemrégiben törölt felhasználók, és vagy visszaállíthatja a őket, vagy véglegesen törli azokat.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez
 
**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások lettek hozzáadva a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)listájához:

- A Microsoft Planner
- Azure Information Protection 

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzatban található vezérlők közötti "vagy" használata 

**Írja be a következőt:** Módosított funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme
 
Most már használhatja a "vagy a" (a kijelölt vezérlők egyikének megkövetelése) használatát a feltételes hozzáférés-vezérléshez. Ez a funkció segítségével szabályzatok létrehozása a "vagy" közötti hozzáférés-vezérlést. Ez a funkció segítségével például olyan szabályzatot, amely egy felhasználó jelentkezhet be a multi-factor Authentication szolgáltatás használata "vagy" a megfelelő eszköz lehet szükséges.

További információ: [vezérlők az Azure ad feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>A valós idejű kockázati észlelések összesítése

**Írja be a következőt:** Módosított funkció    
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás biztonsága és védelme

Azure AD Identity Protection az összes olyan valós idejű kockázati észlelést, amely egy adott napon belül ugyanabból az IP-címről származik, az egyes kockázati észlelési típusok esetében összesítve lesz. Ez a változás korlátozza a kockázati észlelések mennyiségét, ami a felhasználói biztonság módosítása nélkül jelenik meg.

Az alapul szolgáló valós idejű észlelését működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha rendelkezik egy olyan bejelentkezési kockázat-biztonsági házirendet, állítsa be a multi-factor Authentication szolgáltatás blokkolja a hozzáférést, továbbra is aktivált minden kockázatos bejelentkezés során.
 
---
 
## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések kivezetjük

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-életciklus kezelése  

Az Azure Portalon a következőket tartalmazza:

- Egy új Azure AD felügyeleti konzolján.
- Új API-k tevékenység és a biztonsági jelentések.
 
Új funkciók miatt a jelentést, a/Reports végpont API-k visszavontuk, 2017. December 10. 

---

### <a name="automatic-sign-in-field-detection"></a>Az automatikus bejelentkezési mezők

**Írja be a következőt:** Rögzített   
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Egyszeri bejelentkezés  

Az Azure AD automatikus bejelentkezési mezők egy HTML-felhasználói név és jelszó mezőt megjelenítő alkalmazások esetében támogatja. Ezek a lépések az [alkalmazások bejelentkezési mezőinek automatikus rögzítését ismertetik](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Ezt a képességet a [Azure Portal](https://aad.portal.azure.com) **vállalati alkalmazások** lapján található *nem* katalógusbeli alkalmazás hozzáadásával érheti el. Ezen felül az új alkalmazás **egyszeri bejelentkezési** módját **jelszó alapú egyszeri bejelentkezésre**is beállíthatja, MEGadhat egy webes URL-címet, majd mentheti a lapot.
 
Ez a funkció egy szolgáltatási hiba miatt ideiglenesen letiltotta. A probléma megoldódott, és az automatikus bejelentkezési mezők észlelési érhető el újra.

---

### <a name="new-multi-factor-authentication-features"></a>A multi-factor Authentication szolgáltatás új funkciók

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Multi-Factor Authentication  
A **termék képességei:** Identitás biztonsága és védelme  

Többtényezős hitelesítés (MFA), a szervezet védelme fontos részét képezi. Ahhoz, hogy több adaptív hitelesítő adatokat, és a felhasználói élményt a zökkenőmentes, a következő funkciók lettek hozzáadva: 

- Multi-factor Authentication ellenőrző eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentést, amely programozott hozzáférést biztosít a többtényezős hitelesítés eredménye.
- Az MFA konfigurálása az Azure AD-konfigurációjának több mélyen integrált élmény az Azure Portalon.

A nyilvános előzetes verziója, az MFA felügyeleti és jelentéskészítési az alapvető Azure AD-konfigurációs felület integrált részét képezik. Mostantól felügyelheti a MFA felügyeleti portálon funkciókat nyújt az Azure AD lehetőséget.

További információ: [az MFA-jelentéskészítés referenciája a Azure Portalban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Használati feltételek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának  

Az Azure AD használati feltételek szükséges információkkal, mint például a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatok felhasználók is használhatja.

Az Azure AD használati feltételek az alábbiakhoz használhatja:

- A szervezet minden tagjára vonatkozó általános használati
- Specifikus használati feltételek a felhasználói attribútumok (például orvosi nővérek és) vagy a belföldi és nemzetközi alkalmazottak, dinamikus csoportok által végzett alapján
- Specifikus használati feltételek a nagy hatású üzleti alkalmazások, mint például a Salesforce elérése

További információ: az [Azure ad használati feltételei](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management fejlesztései

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management  

Az Azure AD Privileged Identity Management kezelheti, irányíthatja, és a szervezet Azure-erőforrások (előzetes verzió) való hozzáférés figyelése:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Összes erőforrást az Azure Portalon az Azure RBAC-funkciókat használó igénybe veheti a biztonsági és az Azure AD Privileged Identity Management által biztosított lehetőségeket alkalmazásélettartam-kezelési képességgel.

További információ: [Privileged Identity Management Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Betartásának  

Szervezetek számára a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelheti a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. Felülvizsgálók-e, hogy a vendégek folyamatos hozzáférést a hozzáférési felülvizsgálatok által nyújtott alapján eldöntését is.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információ: [Azure ad hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Saját alkalmazások és az Office 365 appindítóján a külső alkalmazások elrejtése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Egyszeri bejelentkezés  

Mostantól hatékonyabban kezelheti a felhasználói portálon megjelenő alkalmazásokat egy új **Hide app** -tulajdonság használatával. Azokban az esetekben, ahol a Háttérszolgáltatásokhoz vagy ismétlődő csempék és legyen zsúfolt felhasználók alkalmazás kilövők megjelenni alkalmazáscsempék alkalmazások elrejtéséhez. A váltógomb a harmadik féltől származó alkalmazás **Tulajdonságok** részében található, és **látható a felhasználó számára?** Egy alkalmazás programozás útján PowerShell-lel is elrejthetők. 

További információ: [harmadik féltől származó alkalmazás elrejtése a felhasználó Azure ad-beli felhasználói felületéről](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Mi érhető el?**

 Az új felügyeleti konzol, a két új API-kkal való áttérés részeként az Azure Active Directory érhetők el naplók. Az új API-készletet biztosít gazdagabb szűrési és rendezési funkció részletesebb naplózás és a bejelentkezési tevékenységek biztosítása mellett. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelési API-n keresztül érhetők el Microsoft Graphban.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>A gyorsjavítás az Identity Managerhez

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése  

2017. szeptember 25, az Identity Manager 2016 Service Pack 1 (build 4.4.1642.0) gyorsjavítás kumulatív csomag érhető el. Ez a kumulatív csomag:

- Oldja fel a problémákat, és fejlesztéseket.
- Az összesítő frissítés, amely lecseréli az összes Identity Manager 2016 Service Pack 1 frissítés akár build 4.4.1459.0 Identity Manager 2016. 
- Megköveteli, hogy rendelkezik az Identity Manager 2016 4.4.1302.0 hozhat létre. 

További információ: a [gyorsjavítások kumulatív csomagja (Build 4.4.1642.0) elérhető a Identity Manager 2016 Service Pack 1 verzióhoz](https://support.microsoft.com/help/4021562). 

---
