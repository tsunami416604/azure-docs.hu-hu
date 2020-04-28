---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802509"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` -cím ![másolásával és beillesztésével: az RSS-hírcsatorna olvasójának ikonját](./media/whats-new/feed-icon-16x16.png) a hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="march-2020"></a>2020. március

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nem felügyelt Azure Active Directory fiók a B2B-frissítésben március 2021

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
**2021. március 31-ig**a Microsoft többé nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure Active Directory (Azure ad) fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. Ennek előkészítéseként javasoljuk, hogy az [egyszeri jelszavas hitelesítéssel küldje](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)el az e-maileket.

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörében lesznek

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörén kívül vannak. Visszajelzésünk szerint az ügyfelek a kiépítés hatókörében szeretnék, hogy az adott szerepkörrel rendelkező felhasználók felhasználják a felhasználókat. Dolgozunk a változtatások üzembe helyezésén, így minden új kiépítési konfiguráció lehetővé teszi a felhasználók számára az alapértelmezett hozzáférési szerepkör kiépítését. Fokozatosan megváltoztatjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassák a felhasználók ezzel a szerepkörrel való üzembe helyezését. Nincs szükség felhasználói beavatkozásra. A módosítás érvénybe lépjen a [dokumentáció](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) frissítése után.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Az Azure AD B2B együttműködés a 21Vianet (Azure China 21Vianet) bérlők által üzemeltetett Microsoft Azureban lesz elérhető.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködési képességek a 21Vianet (Azure China 21Vianet) bérlők által üzemeltetett Microsoft Azureban lesznek elérhetők, így az Azure China 21Vianet-bérlő felhasználói zökkenőmentesen dolgozhatnak más Azure China 21Vianet-bérlők felhasználóival. [További információ az Azure ad B2B együttműködésről](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B együttműködés meghívása e-mail újratervezése

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködés Meghívási szolgáltatása által küldött [e-maileket](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) a rendszer újratervezi, hogy meghívja a felhasználókat a címtárba, hogy a Meghívási adatokat és a felhasználó következő lépéseit világosabban hozza meg.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>A HomeRealmDiscovery szabályzat módosítása megjelenik a naplókban.

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Ellenőrzési  
A **termék képességei:** & jelentéskészítés figyelése
 
Rögzítettünk egy hibát, amelyben a [HomeRealmDiscovery szabályzat](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) módosításait nem vették fel a naplókba. Ekkor láthatja, hogy mikor és hogyan módosult a házirend, és hogy kivel. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Új összevont alkalmazások érhetők el a Azure AD alkalmazás Galleryben – március 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Március 2020-én ezeket a 51 új alkalmazásokat bővítettük az alkalmazás-katalógusban az összevonási támogatással: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML-alkalmazás](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [IPoint](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)- [Aster](https://demo.asterapp.io/login)szolgáltató, [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [contexxt.ai szféra](https://contexxt-sphere.com/login), [bölcsesség a Invictus, a](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial) [flare digitális aláírások](https://spark-dev.pixelnebula.com/login), [a Logz.IO-Felhőbeli megfigyelés, a](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial) [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), a BizzContact, a [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)Elqano, az MarketSignShare, a CrossKnowledge, az net Vision, a Compas, a Byggeweb, az [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial) [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial) [a](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)datadoggal [, a Zscaler, a](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)WatchTeams [, Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)A [,](https://www.devfinition.com/) [a](https://apps.apple.com/us/app/docia/id529058757) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [az](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [IP-platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [PIPEDRIVE](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase workshop](https://app.showcaseworkshop.com/) [BizzContact](https://bizzcontact.app/), [Greenlight integrációs platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), Greenlight- [kompatibilis hozzáférés-kezelés](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [grokkolom learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Mirador online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [MarketSignShare](http://www.signshare.com/) [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [bizarr Studio a digitális folyamatok automatizálásához](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX,](https://www.insuite.jp/) [sybo](https://www.systexsoftware.com.tw/), [brit](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-nap](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective Sdn](https://portal.kollective.app/login) [, Witivio,](https://app.witivio.com/) [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [, Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Az Azure AD B2B együttműködés Azure Government bérlők számára érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködési funkciói mostantól elérhetők a Azure Government bérlők között.  Annak megállapításához, hogy a bérlő tudja-e használni ezeket a képességeket, kövesse az utasításokat, [Hogyan állapítható meg, hogy a B2B együttműködés elérhető-e az Azure US government-bérlőben?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Az Azure-naplók Azure Monitor integrációja már elérhető a Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Az Azure AD-naplók Azure Monitor integrációja már elérhető a Azure Governmentban. Az Azure AD-naplókat (naplózási és bejelentkezési naplókat) átirányíthatja egy Storage-fiókba, az Event hub-ba és a Log Analyticsba. Tekintse meg a [részletes dokumentációt](https://aka.ms/aadlogsinamd) , valamint az Azure ad-forgatókönyvek [jelentéskészítéséhez és monitorozásához szükséges telepítési terveket](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) .

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identity Protection-frissítés Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel vesszük, hogy most már bevezetjük a frissített [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs) élményt a [Microsoft Azure Government portálon](https://portal.azure.us/). További információt a [bejelentési blogbejegyzésben](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)talál.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Vész-helyreállítás: a kiépítési konfiguráció letöltése és tárolása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure AD-kiépítési szolgáltatás a konfigurációs képességek széles körét biztosítja. Az ügyfeleknek el kell tudniuk menteni a konfigurációt, hogy később is hivatkozhatnak rá, vagy visszatérhetnek egy ismert jó verzióra. Lehetőség van arra, hogy a kiépítési konfigurációt JSON-fájlként töltse le, és töltse fel, amikor szüksége van rá. [További információ](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A SSPR (önkiszolgáló jelszó-visszaállítás) mostantól két kaput igényel a rendszergazdák számára a 21Vianet által üzemeltetett Microsoft Azureban (Azure China 21Vianet) 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Identitás-biztonsági & védelme
 
Korábban Microsoft Azure a 21Vianet (Azure China 21Vianet) által működtetett, az önkiszolgáló jelszó-visszaállítást (SSPR) használó rendszergazdák a saját jelszavaik visszaállításához csak egy "kaput" (Challenge) szükségesek az identitásuk bizonyításához. A nyilvános és más országos felhőkben a rendszergazdáknak általában két kaput kell használniuk, hogy igazolják identitásukat a SSPR használatakor. Mivel azonban nem támogatjuk az SMS-t és a telefonhívásokat az Azure China 21Vianet, a rendszergazdák az egykapus jelszó-visszaállítást engedélyezték.

SSPR-szolgáltatás paritást hozunk létre az Azure China 21Vianet és a nyilvános felhő között. A jövőben a rendszergazdáknak két kaput kell használniuk a SSPR használatakor. Az SMS, a telefonhívások és a hitelesítő alkalmazások értesítései és kódjai is támogatottak lesznek. [További információ](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>A jelszó hossza legfeljebb 256 karakter hosszúságú lehet

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD szolgáltatás megbízhatóságának biztosítása érdekében a felhasználói jelszavak már 256 karakterre korlátozódnak. Az ennél hosszabb jelszóval rendelkező felhasználókat a rendszer arra kéri, hogy változtassa meg a jelszavát a következő bejelentkezéskor, vagy forduljon a rendszergazdához, vagy az önkiszolgáló jelszó-visszaállítási funkció használatával.

Ez a változás a 2020. március 13-án, a (18:00 UTC), a AADSTS 50052, a InvalidPasswordExceedsMaxLength. További részletekért tekintse meg a [változtatási értesítéseket](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Az Azure AD bejelentkezési naplói mostantól elérhetők az összes ingyenes bérlő számára a Azure Portal

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Az ingyenes bérlőket használó ügyfeleink akár 7 napig is hozzáférhetnek az [Azure ad bejelentkezési naplóihoz a Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) . Korábban a bejelentkezési naplók csak prémium szintű Azure Active Directory licenccel rendelkező ügyfelek számára voltak elérhetők. Ezzel a módosítással minden bérlő hozzáférhet a naplókhoz a portálon keresztül.

> [!NOTE]
> Az ügyfeleknek továbbra is prémium szintű licencre (prémium szintű Azure Active Directory P1 vagy P2) van szükségük a bejelentkezési naplók eléréséhez Microsoft Graph API-n és Azure Monitoron keresztül.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>A címtárra kiterjedő csoportok beállításának elavult beállítása a csoportok általános beállításainál Azure Portal

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Annak érdekében, hogy az ügyfelek rugalmasabban hozzanak létre olyan, címtárra kiterjedő csoportokat, amelyek megfelelnek az igényeiknek **, a csoport**  > **általános** beállításai **közül a Azure Portal**, a [dinamikus csoportokra vonatkozó dokumentációra](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)mutató hivatkozást váltottunk ki. Továbbfejlesztettük a dokumentációt, hogy több útmutatást is tartalmazzon, így a rendszergazdák létrehozhatnak olyan összes felhasználói csoportot, amely tartalmazza vagy kizárják a vendég felhasználókat.

---

## <a name="february-2020"></a>2020. február

### <a name="upcoming-changes-to-custom-controls"></a>Az egyéni vezérlők közelgő módosításai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Azt tervezzük, hogy lecseréljük a jelenlegi egyéni vezérlők előzetesét egy olyan megközelítéssel, amely lehetővé teszi, hogy a partner által biztosított hitelesítési funkciók zökkenőmentesen működjenek a Azure Active Directory-rendszergazda és a végfelhasználói élményben. Napjainkban a partner MFA-megoldások a következő korlátozásokkal szembesülnek: csak jelszó megadása után működnek. más kulcsfontosságú helyzetekben nem szolgálnak MFA-ként a lépésenkénti hitelesítéshez; és nem integrálják a végfelhasználói vagy a rendszergazdai hitelesítő adatok kezelési funkcióit. Az új implementáció lehetővé teszi, hogy a partner által biztosított hitelesítési tényezők a főbb forgatókönyvek beépített tényezői mellett működjenek, beleértve a regisztrációt, a használatot, az MFA-jogcímeket, a hitelesítés lépéseit, a jelentéskészítést és a naplózást. 

Az egyéni vezérlők továbbra is támogatottak az előzetes verzióban az új terv mellett, amíg az általános rendelkezésre állást nem éri el. Ezen a ponton biztosítjuk az ügyfeleknek az új tervbe való Migrálás idejét. Az aktuális megközelítés korlátai miatt nem fogunk új szolgáltatókat bevezetni, amíg az új terv elérhetővé nem válik. Szorosan együttműködünk az ügyfelekkel és a szolgáltatókkal, és a lehető legszorosabban tájékoztatjuk az ütemtervet. [További információ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identitások biztonságos pontszáma – MFA-javító művelet frissítései

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Annak érdekében, hogy a vállalatok a vállalatnál működő házirendek alkalmazása során biztosítsák a legnagyobb biztonságot, a Microsoft biztonságos pontszáma a többtényezős hitelesítés (MFA) körébe tartozó három fejlesztési művelet eltávolítását és a kettő hozzáadását mutatja be.

A következő javítási műveletek el lesznek távolítva:

- Az összes felhasználó regisztrálása az MFA-hoz
- MFA megkövetelése minden felhasználótól
- MFA megkövetelése Azure AD-beli Kiemelt szerepkörökhöz

A következő javítási műveletek lesznek hozzáadva:

- Győződjön meg arról, hogy minden felhasználó teljes MFA-t tud biztosítani a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz

Az új fejlesztési műveletekhez regisztrálnia kell a felhasználókat vagy a rendszergazdákat az MFA-ban a címtárban, és meg kell határoznia a szervezeti igényeknek megfelelő házirend-készletet. A fő cél az, hogy rugalmasságot biztosítson, miközben a felhasználók és a rendszergazdák több tényezővel vagy kockázatalapú identitás-ellenőrzési kérésekkel is hitelesíthetők. Ez olyan biztonsági alapértékek beállítására is lehetőséget nyújt, amelyek lehetővé teszik a Microsoft számára, hogy mikor kell kipróbálnia a felhasználók számára az MFA-t, vagy több, hatókörön belüli döntéseket alkalmazó szabályzatot is A fejlesztési művelet frissítéseinek részeként az alapkonfiguráció-védelmi szabályzatok többé nem fognak szerepelni a pontozási számításokban. [Tudjon meg többet a Microsoft biztonságos pontszámáról](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU kiválasztása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services
 
Hallottunk visszajelzést arról, hogy Azure AD Domain Services ügyfeleink nagyobb rugalmasságot kívánnak kijelölni a példányok teljesítményi szintjeit. 2020. február 1-től kezdődően egy dinamikus modellből (ahol az Azure AD meghatározza a teljesítmény-és árképzési szintet az objektumok száma alapján) egy önálló kiválasztási modellre. Az ügyfelek mostantól olyan teljesítményszint közül választhatnak, amelyik megfelel a környezetének. Ez a módosítás azt is lehetővé teszi, hogy új forgatókönyveket, például erőforrás-erdőket és prémium funkciókat, például napi biztonsági mentéseket engedélyezzen. Az objektumok száma mostantól korlátlan az összes SKU esetében, de továbbra is az egyes rétegekhez tartozó objektumokra vonatkozó javaslatokat fogjuk biztosítani.

**Nincs szükség azonnali felhasználói beavatkozásra.** A meglévő ügyfelek esetében a 2020. február 1-én használt dinamikus rétegek az új alapértelmezett szintet határozzák meg. Ennek a változásnak az eredménye nem befolyásolja a díjszabást és a teljesítményt. A továbbiakban az Azure AD DS ügyfeleknek értékelniük kell a teljesítményre vonatkozó követelményeket a címtár méretének és a számítási feladatok jellemzőinek megváltozása után. A szolgáltatási szintek közötti váltás továbbra is leállási művelet marad, és a továbbiakban nem lesz automatikusan áthelyezve az ügyfelek új szintjére a címtáruk növekedése alapján. Emellett az árak nem növekednek, és az új díjszabás az aktuális számlázási modellel fog igazodni. További információkért tekintse meg az [Azure AD DS SKU-dokumentációt](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) és a [Azure ad Domain Services díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – február 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Február 2020-án a következő 31 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással: 

[IamIP szabadalmi platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 egyszeri bejelentkezés az Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)-hoz, [Barracuda e-mail-biztonsági szolgáltatás](https://ess.barracudanetworks.com/sso/azure), [ABa-jelentéskészítés](https://myaba.co.uk/client-access/signin/auth/msad) [válság esetén – online portál](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [méhész Azure ad-alapú adatösszekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry-értékelések](https://www.kornferry.com/solutions/kf-digital/kf-assess), Verkada- [parancs](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [Emődi-böngészés](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [új ereklye (korlátozott kiadás)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [túlium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [sablon kiválasztása csapatok számára](https://links.officeatwork.com/templatechooser-download-teams), [méhek](https://www.beesy.me/index.php/site/login), [egészségügyi támogatási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [fali](https://app.mural.co/signup), [kaptár](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink tanárok és iskolák számára](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDo](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [újranyomtatja az íróasztalt-article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – február 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-támogatás a FIDO2 biztonsági kulcsaihoz hibrid környezetekben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Bejelentjük az Azure AD támogatásának nyilvános előzetes verzióját a FIDO2 biztonsági kulcsaihoz a hibrid környezetekben. A felhasználók mostantól FIDO2 biztonsági kulcsokkal jelentkezhetnek be a hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökre, és zökkenőmentesen jelentkezhetnek be a helyszíni és a Felhőbeli erőforrásaiba. A hibrid környezetek támogatása a legtöbbet kért szolgáltatás volt a jelszóval nem rendelkező ügyfeleinktől, hiszen eredetileg az Azure AD-hez csatlakoztatott eszközök FIDO2-támogatásának nyilvános előzetes verzióját indítottuk el. A védett technológiák, például a biometria és a nyilvános/titkos kulcsos titkosítás használatával a jelszó-alapú hitelesítés kényelmes és egyszerű használatot biztosít. Ezzel a nyilvános előzetes verzióval mostantól modern hitelesítést használhat, például FIDO2 biztonsági kulcsokat a hagyományos Active Directory erőforrások eléréséhez. További információért keresse fel az [SSO-t a helyszíni erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

A kezdéshez látogasson el [a FIDO2 biztonsági kulcsok engedélyezése a bérlőhöz](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) lépésről lépésre. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Mostantól általánosan elérhető az új fiókom felhasználói felülete

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Végfelhasználói élmények
 
A fiókom, az összes végfelhasználói fiók-felügyeleti szükségletet leállító áruház már általánosan elérhető! A végfelhasználók az új webhelyet URL-címen keresztül érhetik el, vagy az új saját alkalmazások felületének fejlécében. További információ az önkiszolgáló funkciókról: az új felhasználói élmény a [saját fiók portálon – áttekintés](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>A fiókom webhely URL-címe frissült a myaccount.microsoft.com

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Végfelhasználói élmények
 
Az új fiókom végfelhasználói élménye a következő hónapban frissülni fog `https://myaccount.microsoft.com` az URL-címével. További információk a felhasználói élményről és az összes olyan fiókról, amely a [saját fiók portál súgójában](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)elérhető végfelhasználók számára biztosít segítséget.

---
 
## <a name="january-2020"></a>2020. január
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Az új My apps portál már általánosan elérhető

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Végfelhasználói élmények
 
Frissítse szervezetét az új My apps portálra, amely már általánosan elérhető! További információ az új portálról és gyűjteményekről: [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Az Azure AD-munkaterületek a gyűjteményeknek lettek átnevezve

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások   
A **termék képességei:** Végfelhasználói élmények
 
A munkaterületek, a szűrők rendszergazdák konfigurálhatják a felhasználói alkalmazásaikat, most gyűjteményekként lesznek hivatkozva. További információ arról, hogyan konfigurálhatja őket a [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)című témakörben.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefonos regisztráció és bejelentkezés egyéni szabályzattal (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A telefonos regisztrációval és bejelentkezéssel a fejlesztők és a vállalatok lehetővé tehetik ügyfeleiknek, hogy regisztráljanak, és jelentkezzenek be a felhasználó telefonszámára SMS-ben eljuttatott egyszeri jelszó használatával. Ez a funkció azt is lehetővé teszi, hogy az ügyfél megváltoztassa a telefonszámát, ha elvesztik a telefonjára való hozzáférést. Az egyéni szabályzatok erejével a telefonos regisztráció és bejelentkezés lehetővé teszi a fejlesztők és a vállalatok számára, hogy az oldal testreszabásával kommunikáljanak a márkájuk használatával. Megtudhatja, hogyan [állíthatja be a telefonos regisztrációt, és hogyan jelentkezhet be a Azure ad B2C egyéni házirendjeivel](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – január 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – január 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Január 2020-én ezeket a 33 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük: 

[Josa](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [gyors Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LEAVEBOT](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [, TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [üzemeltetett MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), munkavégző [vállalat](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB a Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe egy saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), Maxient- [kezelő szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [Áttekintés](https://portal.coreview.com/), [leverni Cloud Office 365 Connector](https://laxmi.squelch.io/login), [PingFlow hitelesítés](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), Taskize [Csatlakozás](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), Sandwai, EZRentOut [, AssetSonar](https://app.sandwai.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [akari virtuális asszisztens](https://akari.io/akari-virtual-assistant/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="two-new-identity-protection-detections"></a>Két új Identity Protection-észlelés

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme
 
Két új bejelentkezési típust adtunk hozzá a személyazonosság védelme érdekében: a gyanús beérkezett fájlok manipulációs szabályait és a lehetetlen utazást. Ezeket az offline észleléseket Microsoft Cloud App Security (MCAS) észleli, és a felhasználók és a bejelentkezési kockázat befolyásolása az Identity Protectionben. További információ ezekről az észlelésekről: [bejelentkezési kockázati típusok](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Megszakítási változás: az URI-töredékek nem lesznek végrehajtva a bejelentkezési átirányítás során

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
2020. február 8-án kezdődően, amikor egy felhasználó egy kérést küld a login.microsoftonline.com egy felhasználónak való bejelentkezéshez, a szolgáltatás hozzáfűz egy üres töredéket a kéréshez.  Ezzel megakadályozza, hogy a böngésző kitörölje a kérésben szereplő összes töredéket. Ehhez a viselkedéshez egyetlen alkalmazásnak sincs függősége. További [információkért lásd: a Microsoft](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) Identity platform dokumentációjában.

---

## <a name="december-2019"></a>2019. december

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP-SuccessFactors üzembe helyezése az Azure AD-ben és a helyszíni AD-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Mostantól integrálhatja az SAP SuccessFactors-t mérvadó identitási forrásként az Azure AD-ben. Ez az integráció segít automatizálni a teljes körű identitás-életciklust, beleértve a HR-alapú események, például az új bérletek vagy a megszűnések használatát az Azure AD-fiókok üzembe helyezésének vezérléséhez.

Az SAP-SuccessFactors bejövő üzembe helyezésének az Azure AD-be való beállításával kapcsolatos további információkért tekintse meg az [SAP SuccessFactors automatikus létesítésének konfigurálása](https://aka.ms/SAPSuccessFactorsInboundTutorial) oktatóanyagot.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Testreszabott e-mailek támogatása a Azure AD B2Cban (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Mostantól a Azure AD B2C használatával testreszabott e-maileket hozhat létre, amikor a felhasználók az alkalmazások használatára jelentkeznek. A DisplayControls (jelenleg előzetes verzióban) és egy külső gyártótól származó e-mail-szolgáltató (például a [SendGrid](https://sendgrid.com/), a [SparkPost](https://sparkpost.com/)vagy egy egyéni REST API) használatával saját e-mail-sablont használhat a cím és a tárgy **szövege alapján,** valamint támogathatja a honosítási és egyéni egyszeri jelszavas (OTP) beállításokat is.

További információ: [egyéni e-mail-ellenőrzés Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Alapkonfiguráció-házirendek cseréje biztonsági alapértékekkel

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás biztonsága és védelme

A hitelesítéshez a biztonságos, alapértelmezett modell részeként minden bérlőről eltávolítjuk a meglévő alapkonfiguráció-védelmi szabályzatokat. Ez az Eltávolítás a februári befejezést célozza meg. Ezeknek az alapkonfiguráció-védelmi házirendeknek a pótlása biztonsági alapértékek. Ha alapkonfiguráció-védelmi házirendeket használ, meg kell terveznie az új biztonsági alapértelmezett házirendre vagy a feltételes hozzáférésre való áttérést. Ha még nem használta ezeket a szabályzatokat, nem kell végrehajtania a műveletet.

További információ az új biztonsági alapbeállításokról: [Mik a biztonsági alapértékek?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) A feltételes hozzáférési szabályzatokkal kapcsolatos további információkért lásd: [általános feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>2019. november

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>A SameSite attribútum és a Chrome 80 támogatása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

A cookie-k számára a biztonságos alapértelmezett modell részeként a Chrome 80 böngésző megváltoztatja a cookie-k `SameSite` attribútum nélküli kezelésének módját. Minden olyan cookie, amely nem `SameSite` adja meg az attribútumot `SameSite=Lax`, a rendszer úgy kezeli, mintha a értékre lett állítva, ami azt eredményezi, hogy a Chrome blokkolja a több tartományon belüli cookie-megosztási forgatókönyveket, amelyektől az alkalmazás függ. A korábbi Chrome-viselkedés fenntartása érdekében használhatja az attribútumot `SameSite=None` , és hozzáadhat egy további `Secure` attribútumot, így a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a módosítás befejezését ütemezi a 2020. február 4-én.

Azt javasoljuk, hogy a fejlesztők az alábbi útmutató segítségével tesztelik alkalmazásaikat:

- Állítsa az alapértelmezett értéket a **biztonságos cookie használata** beállításnál az **Igen**értékre.

- Állítsa a **SameSite** attribútum alapértelmezett értékét **none**értékre.

- Adjon hozzá egy `SameSite` további **biztonságos**attribútumot.

További információ: a [közelgő SameSite cookie-változások a ASP.net és a ASP.net Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) , valamint az [ügyfelek webhelyeire és a Microsoft termékeire és szolgáltatásaira vonatkozó lehetséges fennakadások a Chrome 79-os és újabb verzióiban](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Új gyorsjavítás a Microsoft Identity Manager (rendszerhez) 2016 Service Pack 2 (SP2) rendszerhez

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Microsoft Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése

A gyorsjavítások kumulatív csomagja (Build 4.6.34.0) elérhető a következőhöz: Microsoft Identity Manager (2016 Service Pack 2 (SP2). Ez a frissítőcsomag elhárítja a problémákat, és olyan javításokat ad hozzá, amelyek a jelen frissítés által javított problémák és javításokkal foglalkozó szakaszban olvashatók.

További információ és a gyorsjavító csomag letöltése: [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0) kumulatív frissítés elérhető](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Új AD FS alkalmazás-tevékenység jelentés az alkalmazások Azure AD-be való átmigrálása érdekében (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Használja az új Active Directory összevonási szolgáltatások (AD FS) (AD FS) alkalmazás-tevékenység jelentését a Azure Portalban annak azonosításához, hogy mely alkalmazások képesek áttelepíteni az Azure AD-be. A jelentés az Azure AD-vel való kompatibilitás érdekében az összes AD FS alkalmazást felméri, ellenőrzi a problémákat, és útmutatást nyújt az egyes alkalmazások áttelepítésre való előkészítéséhez.

További információ: [az alkalmazások Azure ad-ba való áttelepítésére szolgáló AD FS alkalmazás-tevékenységi jelentés használata](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Új munkafolyamat a felhasználók számára rendszergazdai engedély kérése (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** Access Control

Az új rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdáknak, hogy hozzáférést biztosítanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tud beleegyezést adni, akkor a rendszergazda jóváhagyására vonatkozó kérést küldhet. A rendszer e-mailben küldi el a kérést, és egy, a Azure Portal által elérhető várólistába helyezi az összes olyan rendszergazdához, aki felülvizsgáló lett megjelölve. Ha egy felülvizsgáló egy függőben lévő kérelemre vonatkozó műveletet hajt végre, a kérelmező felhasználók értesítést kapnak a műveletről.

További információ: [a rendszergazdai engedélyekkel kapcsolatos munkafolyamat (előzetes verzió) konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>A választható jogcímek (nyilvános előzetes verzió) kezelésére vonatkozó új Azure AD alkalmazás regisztrációs jogkivonat konfigurációs felülete

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Fejlesztői élmény

A Azure Portal új **Azure ad alkalmazás regisztrációs jogkivonat-konfiguráció** panelje mostantól megjeleníti az alkalmazás-fejlesztőknek az alkalmazásokhoz választható jogcímek dinamikus listáját. Ez az új felület megkönnyíti az Azure AD-alkalmazások áttelepítésének egyszerűsítését, és minimálisra csökkenti a választható jogcímek hibás konfigurációit.

További információ: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Új kétfázisú jóváhagyási munkafolyamat az Azure AD-beli jogosultságok kezelésében (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Jogosultságok kezelése

Új kétlépcsős jóváhagyási munkafolyamatot vezettünk be, amely lehetővé teszi, hogy két jóváhagyót igényeljen a felhasználói kérések egy hozzáférési csomagba való jóváhagyásához. Beállíthatja például úgy, hogy a kérelmező felhasználó felettesének először jóvá kell hagynia, majd azt is megkövetelheti, hogy az erőforrás-tulajdonos jóváhagyja a jóváhagyást. Ha az egyik jóváhagyó nem hagyja jóvá, a hozzáférés nem engedélyezett.

További információ: [hozzáférési csomagra vonatkozó kérelem-és jóváhagyási beállítások módosítása az Azure ad-jogosultságok kezelésében](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>A saját alkalmazások oldal frissítései az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan tekinthetik meg és érhetik el a frissített saját alkalmazások felületét. Ez az új felület az új munkaterületeket is tartalmazza, ami megkönnyíti a felhasználók számára az alkalmazások keresését és rendszerezését.

További információ az alkalmazások új felületéről és a munkaterületek létrehozásáról: [munkaterületek létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>A Google közösségi AZONOSÍTÓjának támogatása az Azure AD B2B együttműködéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** Felhasználói hitelesítés

A Google közösségi azonosítók (Gmail-fiókok) Azure AD-ben való használatának új támogatása lehetővé teszi, hogy egyszerűbben működjenek együtt a felhasználók és partnerek számára. A partnerek számára már nem szükséges új, Microsoft-specifikus fiókot létrehozni és felügyelni. A Microsoft Teams mostantól teljes mértékben támogatja a Google-felhasználókat minden ügyfélen, valamint a közös és a Bérlővel kapcsolatos hitelesítési végpontokon.

További információ: a [Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)számára.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile-támogatás feltételes hozzáféréshez és egyszeri bejelentkezéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az iOS és Android rendszerhez készült Microsoft Edge Azure AD mostantól támogatja az Azure AD egyszeri bejelentkezést és a feltételes hozzáférést:

- **Microsoft Edge egyszeri bejelentkezés (SSO):** Az egyszeri bejelentkezés mostantól a natív ügyfeleken (például a Microsoft Outlookban és a Microsoft Edge-ben) is elérhető az összes Azure AD-hez kapcsolódó alkalmazáshoz.

- **Microsoft Edge feltételes hozzáférés:** Az alkalmazás-alapú feltételes hozzáférési szabályzatok segítségével a felhasználóknak Microsoft Intune által védett böngészőket kell használniuk, például a Microsoft Edge-t.

További információk a Microsoft Edge feltételes hozzáférésről és egyszeri bejelentkezésről: a [Microsoft Edge Mobile támogatása a feltételes hozzáféréshez és az egyszeri bejelentkezéshez már általánosan elérhető](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogbejegyzés. További információ arról, hogyan állíthatja be az alkalmazásokat az [alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) vagy az [eszközökön alapuló feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)használatával: [webes elérés kezelése Microsoft Intune szabályzattal védett böngésző használatával](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-jogosultságok kezelése (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Jogosultságok kezelése

Az Azure AD-jogosultságok kezelése egy új identitás-irányítási szolgáltatás, amely segít a szervezeteknek az identitások és a hozzáférések életciklusának nagy léptékű kezelésében. Ez az új funkció a hozzáférési kérelmek munkafolyamatainak automatizálásával, a hozzárendelések, a felülvizsgálatok és a lejárati csoportok, alkalmazások és a SharePoint Online-webhelyeken való lejárattal segít.

Az Azure AD-jogosultságok kezelése révén hatékonyabban kezelheti a hozzáférést az alkalmazottak és a szervezeten kívüli felhasználók számára is, akiknek szükségük van az erőforrásokhoz való hozzáférésre.

További információ: [Mi az az Azure ad-jogosultságok kezelése?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció  

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

[SAP Cloud platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [prioritási mátrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – november 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

November 2019-én a következő 21 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly címkéjű](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife portál](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal egyszeri bejelentkezés (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko kiskereskedelmi](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & innováció kezelése](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), Netskope [felhasználói hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW [online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis, Jisc](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [Student választópolgár regisztráció](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Új és továbbfejlesztett Azure AD Application Gallery

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Frissítettük az Azure AD-alkalmazás katalógusát, így könnyebben megtalálhatja azokat az előre integrált alkalmazásokat, amelyek támogatják a kiépítés, az OpenID Connect és az SAML használatát a Azure Active Directory bérlőn.

További információ: [alkalmazás hozzáadása a Azure Active Directory bérlőhöz](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Megnövelt alkalmazás-szerepkör definíciójának hossza 120 és 240 karakter között

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Meghallottuk ügyfeleinktől, hogy egyes alkalmazások és szolgáltatások alkalmazás-szerepkör-definíciós értékének hossza túl rövid 120 karakternél. Válaszként növelte a szerepkör-érték definíciójának maximális hosszát 240 karakterre.

Az alkalmazás-specifikus szerepkör-definíciók használatával kapcsolatos további információkért lásd: az [alkalmazás szerepköreinek hozzáadása az alkalmazásban, és fogadása a jogkivonatban](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

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

Néhány héttel a Chrome 80 böngésző kiadása előtt azt tervezzük, hogy az alkalmazásproxy-cookie-k hogyan kezelik az **SameSite** attribútumot. A Chrome 80 kiadásával a **SameSite** attribútumot nem megadó cookie-ket a rendszer úgy kezeli, mintha a értékre lett `SameSite=Lax`állítva.

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

A közeljövőben az Azure AD-fiókkal rendelkező felhasználók többé nem tudják regisztrálni és kezelni az átszervezett alkalmazásokat az Application Registration Portal (apps.dev.microsoft.com) használatával, vagy a Azure Portal Alkalmazásregisztrációk (örökölt) felhasználói felületén regisztrálhatnak és kezelhetnek alkalmazásokat.

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
A **termék képességei:** & jelentéskészítés figyelése

Új **bejelentkezési** oldalunk lett hozzáadva (https://mysignins.microsoft.com) így a szervezet felhasználóinak megtekinthetik a legutóbbi bejelentkezési Előzményeiket, hogy megnézzék a szokatlan tevékenységeket. Ez az új oldal lehetővé teszi, hogy a felhasználók lássák a következőket:

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

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – október 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

A 2019-es verzióban az alábbi 35 új alkalmazásokat bővítettük az App Gallery összevonási támogatásával:

[Válság esetén – mobil](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [tapintat](https://tact.ai/assistant/), [OpusCapita cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), HunchBuzz, [Freshworks](https://login.hunchbuzz.com/integrations/azure/process), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial) [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [tartalom](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue koordináta – Európa](https://www.hirevue.com/), [HireVue koordináta-USOnly](https://www.hirevue.com/), [HIREVUE koordináta-USA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass portál](https://login.xirrus.com/azure-signup), [paylocityben](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail LUCK!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamer](https://theteamie.com/), [a csapatok sebessége](https://velocity.peakup.org/teams/login), [signl4 használatával](https://account.signl4.com/manage), [Emődi Navigálás IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega pont](https://pi.ompnt.com/), [beszélő e-mail az Intune-hoz (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [beszéd e-mailek az Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [iHealthHome](https://ihealthnav.com/account/signin)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

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
- Hitelesítési módszerek
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
