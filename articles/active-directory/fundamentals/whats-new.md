---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 793b9d1b33c244354841402babbd9177ce7ed19b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687715"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` a ![RSS-hírcsatorna olvasó ikonja](./media/whats-new/feed-icon-16x16.png) a hírcsatorna-olvasó.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="october-2019"></a>2019. október

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>A identityRiskEvent API elavult változata Azure AD Identity Protection kockázati észlelések esetén  

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

A fejlesztői visszajelzésekre adott válaszként prémium szintű Azure AD P2 előfizetők mostantól összetett lekérdezéseket végezhetnek a Azure AD Identity Protection kockázati észlelési adatain az új riskDetection API-val Microsoft Graph. A meglévő [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bétaverziója leállítja az adatvisszaadás **2020. január 10**. után. Ha a szervezete a identityRiskEvent API-t használja, térjen át az új riskDetection API-ra.

További információ az új riskDetection API-ról: [kockázatkezelési API-dokumentáció](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Alkalmazásproxy-támogatás a SameSite attribútumhoz és a Chrome 80

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Néhány héttel a Chrome 80 böngésző kiadása előtt azt tervezzük, hogy az alkalmazásproxy-cookie-k hogyan kezelik az **SameSite** attribútumot. A Chrome 80-as kiadásával a **SameSite** attribútumot nem megadó cookie-ket a rendszer úgy kezeli, mintha az `SameSite=Lax`értékre lett állítva.

A változás miatt potenciálisan negatív hatások elkerülése érdekében a következő lépésekkel frissítjük az alkalmazásproxy-hozzáférési és munkamenet-cookie-kat:

- A **Secure cookie használata** beállítás alapértelmezett értékének beállítása **Igen**.

- A **SameSite** attribútum alapértelmezett értékének beállítása **none**.

    >[!NOTE]
    > Az alkalmazásproxy-hozzáférési cookie-k továbbítása mindig kizárólag biztonságos csatornákon keresztül történt. Ezek a módosítások csak a munkamenet-cookie-kra vonatkoznak.

Az alkalmazásproxy cookie-beállításaival kapcsolatos további információkért lásd: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Az alkalmazás regisztrációs portálján (apps.dev.microsoft.com) Alkalmazásregisztrációk (örökölt) és az átszervezett alkalmazások felügyelete többé nem lesz elérhető.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Fejlesztői élmény

A közeljövőben az Azure AD-fiókkal rendelkező felhasználók többé nem tudják regisztrálni és kezelni az átszervezett alkalmazásokat az Application Registration Portal (apps.dev.microsoft.com) használatával, illetve a Alkalmazásregisztrációk (örökölt) alkalmazások regisztrálását és kezelését. élmény a Azure Portalban.

Ha többet szeretne megtudni az új Alkalmazásregisztrációk-élményről, tekintse meg a [Alkalmazásregisztrációk a Azure Portal betanítási útmutatójában](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>A felhasználókra már nincs szükség a felhasználónkénti MFA-ról a feltételes hozzáférés-alapú MFA-re való Migrálás során történő újraregisztráláshoz

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

Kijavítottunk egy ismert problémát, amely során a felhasználóknak újra regisztrálniuk kell, ha le voltak tiltva a felhasználónkénti Multi-Factor Authentication (MFA), majd feltételes hozzáférési szabályzattal engedélyezték az MFA-t.

Ha szeretné megkövetelni, hogy a felhasználók újra regisztráljanak, az Azure AD-portálon a felhasználó hitelesítési módszerei közül kiválaszthatja a **szükséges újbóli regisztrálási MFA** -beállítást. A felhasználók felhasználónkénti MFA-ról feltételes hozzáférés-alapú MFA-re való áttelepítésével kapcsolatos további információkért lásd: [felhasználók konvertálása FELHASZNÁLÓNKÉNTI MFA-ből feltételes hozzáférés-alapú MFA-ra](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Új funkciók a jogcímek átalakításához és küldéséhez az SAML-jogkivonatban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

További képességeket adtunk hozzá, amelyek segítségével testre szabhatja és elküldheti a jogcímeket az SAML-jogkivonatban. Ezek az új képességek a következők:

- További jogcím-átalakítási függvények, amelyek segítenek a jogcímben elküldött értékek módosításában.

- Több átalakítás alkalmazása egyetlen jogcímre.

- A jogcím forrásának megadása a felhasználó típusa és azon csoport alapján, amelyhez a felhasználó tartozik.

További információ ezekről az új képességekről, beleértve a használatuk módját is: [a SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Új bejelentkezési oldal a végfelhasználók számára az Azure AD-ben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** & Jelentéskészítés figyelése

Felvettünk egy új **saját bejelentkezési** oldalt (https://mysignins.microsoft.com), hogy a szervezet felhasználóinak megtekintsék a legutóbbi bejelentkezési Előzményeiket, hogy megnézzék a szokatlan tevékenységeket. Ez az új oldal lehetővé teszi, hogy a felhasználók lássák a következőket:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha egy támadó sikeresen bejelentkezett a fiókjába és a helyről.

- Milyen alkalmazások próbáltak hozzáférni a támadóhoz.

További információ: a felhasználók mostantól megtekinthetik a [szokatlan tevékenységek blogjának bejelentkezési előzményeit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD Domain Services (Azure AD DS) áttelepítése klasszikusról Azure Resource Manager virtuális hálózatokra

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Ügyfeleinknek, akik klasszikus virtuális hálózatokon ragadtak, nagyszerű hírekkel rendelkezünk! Most már elvégezheti a klasszikus virtuális hálózatról egy meglévő Resource Manager-alapú virtuális hálózatra történő egyszeri áttelepítést. A Resource Manager-alapú virtuális hálózatra való áttérés után kihasználhatja a további és frissített funkciókat, például a részletes jelszóházirendek, az e-mailes értesítések és a naplózási naplók előnyeit.

További információ: [előnézet – Azure ad Domain Services migrálása a klasszikus virtuális hálózati modellből a Resource Managerbe](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Az Azure AD B2C oldal-szerződés elrendezésének frissítései

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Bevezetünk néhány új módosítást a Azure AD B2C 1.2.0. Ebben a frissített verzióban mostantól szabályozhatja az elemek betöltési sorrendjét, ami segíthet a stíluslap (CSS) betöltését követően megjelenő vibrálás leállításában is.

A lap szerződésében történt módosítások teljes listáját a [verzió módosítása naplóban](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)tekintheti meg.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Frissítés a saját alkalmazások lapra az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Access Control

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan tekinthetik meg és érhetik el a vadonatúj saját alkalmazások felületét, beleértve az új munkaterületek funkció használatát is, hogy könnyebb legyen számukra az alkalmazások keresése. Az új munkaterületek funkció szűrőként működik azon alkalmazások esetében, amelyekhez a szervezet felhasználói már hozzáférnek.

További információ az alkalmazások új felhasználói élményének és a munkaterületek létrehozásáról: [munkaterületek létrehozása a saját alkalmazások (előzetes verzió) portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>A havi aktív felhasználó-alapú számlázási modell támogatása (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A Azure AD B2C mostantól támogatja a havi aktív felhasználók (MAU) számlázását. A MAU-számlázás a naptári hónap során a hitelesítési tevékenységgel rendelkező egyedi felhasználók számától függ. A meglévő ügyfelek bármikor válthatnak erre az új számlázási módra.

A 2019. november 1-től kezdődően az összes új ügyfél számlázása ezzel a módszerrel történik. Ez a számlázási módszer előnyökkel jár, és lehetővé teszi, hogy előre tervezze meg az ügyfeleket.

További információ: [frissítés a havi aktív felhasználói számlázási modellre](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konszolidált biztonsági menüpont az Azure AD-portálon

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Most már hozzáférhet az összes elérhető Azure AD-biztonsági szolgáltatáshoz az új **biztonsági** menüpontból, illetve a **keresősáv** Azure Portal. Emellett az új **biztonsági** Kezdőlap, az úgynevezett **Biztonság – első lépések**, a nyilvános dokumentációra, a biztonsági útmutatásra és az üzembe helyezési útmutatókra mutató hivatkozásokat is tartalmaz.

Az új **biztonsági** menü a következőket tartalmazza:

- Feltételes hozzáférés
- Identity Protection
- Security Center
- Identitás biztonságos pontszáma
- Hitelesítési módok
- MFA
- Kockázati jelentések – kockázatos felhasználók, kockázatos bejelentkezések, kockázati észlelések
- És még sok minden más...

További információ: [Biztonság – első lépések](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Az Office 365-csoportok elévülési szabályzatának továbbfejlesztése az automatikus megújítás révén

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Identitás-életciklus kezelése

Az Office 365-csoportok elévülési szabályzata a tagjai által aktívan használt csoportok automatikus megújítására lett kibővítve. A csoportok az Office 365-alkalmazások, például az Outlook, a SharePoint és a Teams felhasználói tevékenysége alapján automatikusan megújulnak.

Ez a fejlesztés segít csökkenteni a csoport lejárati értesítéseit, és segít biztosítani, hogy az aktív csoportok továbbra is elérhetők legyenek. Ha már rendelkezik aktív lejárati házirenddel az Office 365-csoportok számára, akkor semmit nem kell tennie az új funkció bekapcsolásához.

További információ: [az Office 365-csoportok elévülési szabályzatának konfigurálása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Frissített Azure AD Domain Services (Azure AD DS) létrehozási élmény

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Frissítettük Azure AD Domain Services (Azure AD DS) új és továbbfejlesztett létrehozási élményét, amely segít a felügyelt tartományok létrehozásában mindössze három kattintással! Emellett az Azure AD DS is feltöltheti és üzembe helyezheti egy sablonból.

További információ: [oktatóanyag: Azure Active Directory Domain Services példány létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>2019. szeptember

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Tervezze meg a változást: a Power BIi csomagok elavulttá váltása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

A 2019. október 1-től kezdődően a Power BI megkezdi az összes tartalomforrás érvénytelenítését, beleértve az Azure AD Power BI Content Pack csomagot is. A Content Pack alternatívájaként az Azure AD-munkafüzetek használatával betekintést nyerhet az Azure AD-hez kapcsolódó szolgáltatásaiba. További munkafüzetek érkeznek, beleértve a feltételes hozzáférési szabályzatokkal kapcsolatos munkafüzeteket a csak jelentés módban, az alkalmazások belefoglalási és egyéb információk alapján.

További információ a munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). További információ a tartalmi csomagok érvénytelenítéséről: a [Bejelentési Power bi sablon alkalmazásai általános elérhetősége](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blogbejegyzés.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>A profil átnevezése és integrálása a Microsoft Office Account (fiók) oldallal

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Együttműködés

Októbertől kezdve a saját profilom felhasználói élmény lesz. Ennek a változásnak a részeként, ahol jelenleg is azt mondja, **a profilom** **a fiókomra változik.** Az elnevezési változás és az egyes tervezési változások miatt a frissített élmény további integrációt tesz lehetővé a Microsoft Office Account (fiók) oldalával. Pontosabban hozzáférhet az Office-telepítésekhez és-előfizetésekhez az **áttekintő fiók** lapról, valamint az Office-hoz kapcsolódó kapcsolattartási beállítások az **Adatvédelem** lapról.

A saját profil (előzetes verzió) felülettel kapcsolatos további információkért tekintse meg a [profil (előzetes verzió) portál áttekintését](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Csoportok és tagok tömeges kezelése az Azure AD-portálon található CSV-fájlok használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon nyilvánosan elérhető a tömeges csoportok felügyeletének előzetes verziója. Mostantól egy CSV-fájlt és az Azure AD-portált is használhat a csoportok és a tagok listáinak kezeléséhez, többek között a következőket:

- Tagok hozzáadása vagy eltávolítása egy csoportból.

- A csoportok listájának letöltése a címtárból.

- A csoporttagok listájának letöltése egy adott csoportra vonatkozóan.

További információ: [tömeges Hozzáadás](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), tagok tömeges [eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), tagok [tömeges letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)és csoportos [letöltési csoportok listája](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>A dinamikus beleegyezikés mostantól egy új rendszergazdai engedélyezési végponton keresztül támogatott

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Létrehoztunk egy új rendszergazdai engedélyezési végpontot a dinamikus beleegyezett támogatáshoz, amely hasznos lehet olyan alkalmazásokhoz, amelyek a dinamikus engedélyezési modellt szeretnék használni a Microsoft Identity platformon.

További információ az új végpont használatáról: [a rendszergazdai engedélyezési végpont használata](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Új Azure AD globális olvasói szerepkör

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control

A 2019. szeptember 24-én kezdődően a globális olvasó nevű új Azure Active Directory (AD) szerepkört fogjuk elkezdeni. Ez a bevezetés az éles és a globális felhőalapú ügyfelekkel (GCC) kezdődik, és a világ minden részén elérhetővé válik.

A globális olvasó szerepkör a globális rendszergazda csak olvasási jogosultsággal rendelkező partnere. Az ebben a szerepkörben lévő felhasználók beolvashatják a beállításokat és a felügyeleti információkat Microsoft 365 szolgáltatások között, de nem végezhetnek felügyeleti műveleteket. Létrehoztuk a globális olvasói szerepkört a szervezet globális rendszergazdáinak számának csökkentése érdekében. Mivel a globális rendszergazdai fiókok hatékonyak és sebezhetők a támadásokkal szemben, javasoljuk, hogy kevesebb mint öt globális rendszergazdával rendelkezzen. Javasoljuk, hogy a globális olvasó szerepkört használja a tervezéshez, a naplózáshoz és a vizsgálatokhoz. Javasoljuk továbbá, hogy a globális olvasó szerepkört más korlátozott rendszergazdai szerepkörökkel, például az Exchange Administrator szolgáltatással együtt használja, hogy a globális rendszergazdai szerepkör nélkül is segítséget nyújtson a munkához.

A globális olvasó szerepkör az új Microsoft 365 felügyeleti központtal, az Exchange felügyeleti központtal, a Teams felügyeleti központtal, a Security Center, a megfelelőségi központtal, az Azure AD felügyeleti központtal és az Eszközkezelő felügyeleti központtal működik

>[!NOTE]
> A nyilvános előzetes verzió elején a globális olvasó szerepkör nem fog működni a következővel: SharePoint, Privileged Access Management, Ügyfélszéf, érzékenységi címkék, csapatok életciklusa, csapatoknak szóló jelentés & a Call Analytics, a Teams IP Phone-eszközök kezelése és a Teams alkalmazás Katalógus. Ezeknek a szolgáltatásoknak a célja, hogy a jövőben is működjenek együtt a szerepkörrel.

További információ: [rendszergazdai szerepkör engedélyei Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Helyszíni jelentéskészítő kiszolgáló elérése a Power BI Mobile alkalmazásból a Azure Active Directory Application Proxy használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

A Power BI Mobile App és az Azure AD Application Proxy közötti új integráció lehetővé teszi, hogy biztonságosan bejelentkezzen a Power BI Mobile alkalmazásba, és megtekintse a szervezete által a helyszíni Power BI jelentéskészítő kiszolgáló üzemeltetett jelentések bármelyikét.

A Power BI Mobile alkalmazással kapcsolatos információkért, beleértve az alkalmazás letöltésének helyét, tekintse meg a [Power bi webhelyet](https://powerbi.microsoft.com/mobile/). A Power BI Mobile App Azure AD Application Proxy használatával történő beállításával kapcsolatos további információkért lásd: [a távoli hozzáférés engedélyezése Power bi Mobile az azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A AzureADPreview PowerShell-modul új verziója érhető el

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

Új parancsmagok lettek hozzáadva az AzureADPreview modulhoz, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, beleértve a következőket:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect új verziója

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

A Azure AD Connect frissített verzióját közzétettük az ügyfelek automatikus frissítéséhez. Ez az új verzió számos új funkciót, javítást és hibajavítást tartalmaz. További információ erről az új verzióról [: Azure ad Connect: verzió kiadásának előzményei](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló, a 8.0.2 verziója már elérhető

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

Ha Ön egy meglévő ügyfél, aki az MFA-kiszolgálót 2019. július 1. előtt aktiválta, most már letöltheti az MFA-kiszolgáló legújabb verzióját (8.0.2-verzió). Ebben az új verzióban:

- Javítva lett egy probléma, így amikor az Azure AD Sync megváltoztat egy felhasználót a letiltotttól az engedélyezett értékre, a rendszer e-mailt küld a felhasználónak.

- Javítva a probléma, hogy az ügyfelek sikeresen frissíthetnek, miközben továbbra is használhatják a címkék funkciót.

- A koszovói (+ 383) országkód hozzáadva.

- Az egyszeri Mellőzés naplózása a MultiFactorAuthSvc. log naplófájlba lett hozzáadva.

- Továbbfejlesztett teljesítmény a Web Service SDK-hoz.

- Egyéb kisebb hibák kijavítása.

2019. július 1-től kezdődően a Microsoft az MFA-kiszolgáló új üzemelő példányokhoz való felajánlását állította le. A többtényezős hitelesítést igénylő új ügyfeleknek felhőalapú Azure-Multi-Factor Authentication kell használniuk. További információ: [felhőalapú Azure multi-Factor Authentication-telepítés tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>2019. augusztus

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A csoportok továbbfejlesztett keresése, szűrése és rendezése az Azure AD portálon érhető el (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon elérhetővé tettük a bővített csoportok kapcsolódó felhasználói élményének nyilvános előzetes verzióját. Ezek a fejlesztések megkönnyítik a csoportok és a tagok névsorának jobb kezelését a következő biztosításával:

- Speciális keresési funkciók, például alsztringek keresése a csoportok listájára.
- Speciális szűrési és rendezési beállítások a tag és a tulajdonos listán.
- Új keresési funkciók a tagok és a tulajdonosok listájához.
- A nagyméretű csoportok pontosabb csoportosítási száma.

További információ: [csoportok kezelése a Azure Portalban](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Új egyéni szerepkörök érhetők el az alkalmazás-regisztrálási felügyelethez (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control

Az Azure AD P1 vagy P2 előfizetéssel elérhető egyéni szerepkörök mostantól részletes hozzáférést biztosíthatnak, ha a szerepkör-definíciókat meghatározott engedélyekkel hozza létre, majd hozzárendeli a szerepköröket adott erőforrásokhoz. Jelenleg egyéni szerepköröket hozhat létre az alkalmazások regisztrálásának kezeléséhez szükséges engedélyekkel, majd hozzárendelheti a szerepkört egy adott alkalmazáshoz. További információ az egyéni szerepkörökről: [Egyéni rendszergazdai szerepkörök Azure Active Directoryban (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Ha további engedélyekre vagy erőforrásokra van szüksége, amelyek jelenleg nem láthatók, küldjön visszajelzést az [Azure feedback-webhelyre](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) , és felvesszük a kérést az Update Road-térképre.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Az új kiépítési naplók segítségével figyelheti és elháríthatja az alkalmazások üzembe helyezésének központi telepítését (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Az új kiépítési naplók segítenek a felhasználók és csoportok üzembe helyezésének figyelésében és hibakeresésében. Ezek az új naplófájlok a következőkkel kapcsolatos információkat tartalmazzák:

- A [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) sikeresen létrehozott csoportok
- Milyen szerepkörök lettek importálva a [Amazon Web Servicesból (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Milyen alkalmazottak lettek importálva a [munkanapokból](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

További információ: [jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Új biztonsági jelentések az összes Azure AD-rendszergazda számára (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Alapértelmezés szerint az összes Azure AD-rendszergazda hamarosan hozzáférhet a modern biztonsági jelentésekhez az Azure AD-n belül. Szeptember végéig a modern biztonsági jelentések tetején található szalagcím használatával visszatérhet a régi jelentésekhez.

A modern biztonsági jelentések további képességeket biztosítanak a régebbi verziókról, többek között a következőkről:

- Speciális szűrés és rendezés
- Tömeges műveletek, például a felhasználói kockázat elvetése
- Sérült vagy biztonságos entitások megerősítése
- Kockázati állapot, amely a következőkre terjed ki: veszélyeztetett, elvetett, szervizelt és megerősített sérült
- Új kockázattal kapcsolatos észlelések (prémium szintű Azure AD előfizetők számára elérhető)

További információ: [kockázatos felhasználók](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [kockázatos bejelentkezések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)és [kockázati észlelések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A felhasználó által hozzárendelt felügyelt identitás elérhető Virtual Machines és Virtual Machine Scale Sets (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felügyelt identitások az Azure-erőforrásokhoz  
A **termék képességei:** Fejlesztői élmény

A felhasználó által hozzárendelt felügyelt identitások mostantól általánosan elérhetők Virtual Machines és Virtual Machine Scale Sets számára. Ennek részeként az Azure olyan identitást hozhat létre az Azure AD-bérlőben, amelyet a használatban lévő előfizetés megbízhatónak tekint, és hozzárendelhető egy vagy több Azure-szolgáltatáshoz. A felhasználó által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](https://aka.ms/azuremanagedidentity)című témakört.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>A felhasználók a Mobile App vagy a Hardware token (általánosan elérhető) használatával állíthatják vissza a jelszavukat

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Azok a felhasználók, akik regisztráltak egy mobil alkalmazást a szervezettel, most visszaállíthatják a saját jelszavukat, ha jóváhagyják a Microsoft Authenticator alkalmazásból érkező értesítést, vagy megadhatnak egy kódot a Mobile App vagy a Hardware token használatával.

További információkért lásd [: Hogyan működik az Azure ad önkiszolgáló jelszó-visszaállítás](https://aka.ms/authappsspr). További információ a felhasználói élményről: [saját munkahelyi vagy iskolai jelszó átállítása – áttekintés](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>A ADAL.NET figyelmen kívül hagyja a MSAL.NET megosztott gyorsítótárat a következő esetekben:

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD Authentication Library (ADAL.NET) verziójának 5.0.0-előzetes verziójától kezdődően az alkalmazások fejlesztőinek [a Web Apps és a webes API-k esetében egy gyorsítótárat kell szerializálni](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Ellenkező esetben előfordulhat, hogy egyes forgatókönyvek, amelyek a [folyamaton](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)kívüli folyamatot használják, valamint `UserAssertion`bizonyos használati eseteit, a jogosultság megemelését eredményezhetik. A biztonsági rés elkerülése érdekében a ADAL.NET mostantól figyelmen kívül hagyja a Microsoft hitelesítési függvénytárát a DotNet (MSAL.NET) megosztott gyorsítótárához a következő esetekben:.

További információ erről a hibáról: [Azure Active Directory a hitelesítési függvénytár jogosultságszint-emelését érintő biztonsági rés](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – augusztus 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2019 augusztusában a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Polgári platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial) [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ-portál (Európa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy részvétel](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritási mátrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [CARBONITE Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [kézbesítés. Media™ portál](https://portal.deliver.media), [élvonalbeli oktatás](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat ad összekapcsolás](https://www.stashcat.com), [Pislogás](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial) [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), a [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [a Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), a [hám](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), a [Emődi stratégiai gondozása](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>A AzureAD PowerShell és a AzureADPreview PowerShell-modulok új verziói érhetők el

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

A AzureAD és a AzureAD előzetes verziójának PowerShell-moduljainak új frissítései elérhetők:

- Új `-Filter` paraméter lett hozzáadva a `Get-AzureADDirectoryRole` paraméterhez a AzureAD modulban. Ez a paraméter segít a parancsmag által visszaadott címtárbeli szerepkörök szűrésében.
- Új parancsmagok lettek hozzáadva az AzureADPreview modulhoz, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, beleértve a következőket:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>A dinamikus csoportházirend-szerkesztő felhasználói felületének fejlesztései a Azure Portal

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A Azure Portal elérhetővé tettük a dinamikus csoportházirend-szerkesztőt, amely megkönnyíti az új szabályok egyszerűbb beállítását, vagy a meglévő szabályok módosítását. Ez a kialakítás lehetővé teszi, hogy csak egy legfeljebb öt kifejezéssel hozzon létre szabályokat. Az eszköz tulajdonságainak listáját is frissítettük, hogy eltávolítsa az elavult eszköz tulajdonságait.

További információ: a [dinamikus tagsági szabályok kezelése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>A hozzáférési felülvizsgálatokkal használható új Microsoft Graph alkalmazás engedélyezése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás

Bevezetünk egy új Microsoft Graph alkalmazás engedélyt, `AccessReview.ReadWrite.Membership`, amely lehetővé teszi, hogy az alkalmazások automatikusan hozzanak létre és kérjenek hozzáférési felülvizsgálatokat a csoporttagságok és az alkalmazás-hozzárendelések számára. Ezt az engedélyt az ütemezett feladatok vagy az automatizálás részeként használhatja, anélkül, hogy bejelentkezett felhasználói környezetre lenne szükség.

További információkért tekintse meg a következő [példát: Azure ad hozzáférési felülvizsgálatok létrehozása Microsoft Graph app permissions with PowerShell blog használatával](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Az Azure AD-Tevékenységnaplók mostantól elérhetők a kormányzati Felhőbeli példányok számára Azure Monitor

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy az Azure AD-beli adatnaplók mostantól elérhetők a Azure Monitor kormányzati Felhőbeli példányaihoz. Most már küldhet Azure AD-naplókat a Storage-fiókjába vagy egy Event hub-ba az SIEM-eszközökkel, például a [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), a [splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)és a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)való integráláshoz. 

A Azure Monitor beállításával kapcsolatos további információkért lásd: [Az Azure ad-tevékenység naplófájljai Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Frissítse a felhasználókat az új, fokozott biztonsági információkkal kapcsolatos felhasználói élményre

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:**  Hitelesítések (bejelentkezések)   
A **termék képességei:** Felhasználói hitelesítés

2019. szeptember 25-én kikapcsoljuk a régi, nem továbbfejlesztett biztonsági információkat a felhasználói biztonsági adatok regisztrálásához és kezeléséhez, és csak az új, [továbbfejlesztett verzió](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)bekapcsolását. Ez azt jelenti, hogy a felhasználók többé nem fogják tudni használni a régi felhasználói élményt.

A fokozott biztonsági információkkal kapcsolatos további információkért tekintse meg a [rendszergazdai dokumentációt](https://aka.ms/securityinfodocs) és a [felhasználói dokumentációt](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Az új felhasználói élmény bekapcsolásához a következőket kell tennie:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként vagy felhasználói rendszergazdaként.

2. Nyissa meg **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése lehetőséget**.

3. A **felhasználók használhatják az előzetes verziójú funkciókat a biztonsági adatok regisztrálásához és kezeléséhez – bővített** terület, válassza a **kijelölt**lehetőséget, majd válasszon ki egy felhasználói csoportot, vagy válassza az **összes** lehetőséget a szolgáltatás bekapcsolásához a bérlő összes felhasználója számára.

4. A * * felhasználók használhatják az előzetes verziójú funkciókat a Security * * info * * * * terület regisztrálásához és kezeléséhez, válassza a **nincs**lehetőséget.

5. Mentse a beállításokat.

    A beállítások mentése után már nem fog tudni hozzáférni a régi biztonsági információkhoz.

>[!Important]
>Ha nem hajtja végre ezeket a lépéseket a 2019. szeptember 25. előtt, a Azure Active Directory bérlője automatikusan engedélyezve lesz a továbbfejlesztett felhasználói élményhez. Ha kérdése van, vegye fel velünk a kapcsolatot registrationpreview@microsoft.comcímen.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>A POST bejelentkezéseket használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Szabványok

2019. szeptember 2-ától kezdődően a POST metódust használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti a HTTP-szabványokkal szemben. A szóközök és a kettős idézőjelek (") nem lesznek eltávolítva a kérelmek űrlapjának értékeiből. Ezeknek a változásoknak nem kell megszüntetniük a meglévő ügyfeleket, és meg kell győződniük arról, hogy az Azure AD-ba küldött kérelmek minden alkalommal megbízhatóan kezelhetők.

További információ: az [Azure ad Breaking Changes – megjegyzések](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

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

Az Azure AD-ügyfelek mostantól szabályzatokat állíthatnak be a szervezet felhasználóinak és csoportjainak FIDO2 biztonsági kulcsainak kezeléséhez. A végfelhasználók emellett saját maguk is regisztrálhatják biztonsági kulcsaikat, a kulcsokkal bejelentkezhetnek a Microsoft-fiókjaikat a webhelyekre, miközben a-kompatibilis eszközökön vannak, valamint bejelentkezhetnek az Azure AD-hez csatlakoztatott Windows 10-es eszközökre.

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

A tökéletesítések a következők:

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
