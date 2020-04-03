---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory újdonságait, például a legújabb kiadási megjegyzéseket, az ismert problémákat, a hibajavításokat, az elavult funkciókat és a közelgő módosításokat.
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
ms.date: 04/01/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b960bf63cae8b55ae2f66b6b809ee0d11229a312
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582776"
---
# <a name="whats-new-in-azure-active-directory"></a>Az Azure Active Directory újdonságai

>Kap értesítést, hogy mikor kell újra ezt az oldalt a `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` frissítések ![másolásával és](./media/whats-new/feed-icon-16x16.png) beillesztése ezt az URL-t: az RSS feed olvasó ikon feed olvasó.

Az Azure AD folyamatosan kap fejlesztéseket. Ahhoz, hogy naprakész legyen a legújabb fejleményekről, ez a cikk az ön számára tájékoztatást nyújt:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Változások tervei

Ez az oldal havonta frissül, ezért rendszeresen látogasson el. Ha hat hónapnál régebbi elemeket keres, megtalálhatja őket az [Archív az Azure Active Directory újdonságai című dokumentumban.](whats-new-archive.md)

---

## <a name="march-2020"></a>2020. március

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nem felügyelt Azure Active Directory-fiókok a B2B 2021 márciusi frissítésében

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C
 
**2021. március 31-től kezdődően**a Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure Active Directory (Azure AD) fiókok és bérlők b2b együttműködési forgatókönyvek létrehozásával. Ennek előkészítéseként javasoljuk, hogy iratkozzon fel [az egyszeri jelkód-hitelesítés e-mailben történő levelezésére.](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók kiépítésre kerülnek

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés
 
Korábban az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók nem rendelkeznek kiépítési hatókörrel. Visszajelzést hallottunk arról, hogy az ügyfelek azt szeretnék, hogy az ezzel a szerepkörrel rendelkező felhasználók kiépítés hatókörében legyenek. Dolgozunk egy módosítás üzembe helyezésén, hogy minden új létesítési konfiguráció lehetővé tegye az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók kiépítését. Fokozatosan módosítjuk a meglévő létesítési konfigurációk viselkedését, hogy ezzel a szerepkörrel támogassuk a felhasználók kiépítését. Nincs szükség vevői műveletre. A mint a módosítás életbe lép, közzétesszük a [dokumentációnk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) frissítését.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Az Azure AD B2B együttműködés a 21Vianet (Azure China 21Vianet) bérlői által üzemeltetett Microsoft Azure-ban lesz elérhető

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C
 
Az Azure AD B2B együttműködési képességei elérhetők lesznek a 21Vianet (Azure China 21Vianet) bérlők által üzemeltetett Microsoft Azure-ban, lehetővé téve az Azure China 21Vianet-bérlők számára, hogy zökkenőmentesen működjenek együtt más Azure China 21Vianet-bérlők felhasználóival. [További információ az Azure AD B2B együttműködésről.](https://docs.microsoft.com/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Az Azure AD B2B együttműködési meghívó e-mail újratervezése

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C
 
Az Azure AD B2B együttműködési meghívási szolgáltatás által küldött [e-maileket](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) a rendszer újrameghívja a címtárba, hogy a meghívó információkat és a felhasználó következő lépéseit egyértelműbbé tegye.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>A HomeRealmDiscovery házirend-módosításai megjelennek a naplókban

**Típus:** Rögzített  
**Szolgáltatás kategória:** Ellenőrzési  
**Termék képesség:** A & jelentés ének nyomon követése
 
Kijavítottunk egy hibát, amely miatt a [HomeRealmDiscovery házirend módosításai](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) nem szerepeltek a naplóban. Most már láthatja, hogy mikor és hogyan változott meg a politika, és ki. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Az Azure AD B2B együttműködés elérhető az Azure Government-bérlőkben

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** B2B/B2C
 
Az Azure AD B2B együttműködési funkciók mostantól elérhető néhány Azure Government-bérlők között.  Ha meg szeretné tudni, hogy a bérlő képes-e használni ezeket a képességeket, kövesse a [Hogyan állapítható meg, hogy a B2B-együttműködés elérhető-e az Azure US Government-beli bérlőmben.](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Az Azure Monitor-integráció az Azure-naplókhoz már elérhető az Azure Government ben

**Típus:** Új funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése
 
Az Azure Monitor integrációja az Azure AD-naplókkal már elérhető az Azure Governmentben. Az Azure AD-naplók (naplózási és bejelentkezési naplók) egy tárfiókba, az Event Hubra és a Log Analytics-be irányíthatók. Kérjük, tekintse meg a [részletes dokumentációt,](https://aka.ms/aadlogsinamd) valamint az Azure AD-forgatókönyvek [jelentéskészítési és figyelési figyelőterveit.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identitásvédelem frissítése az Azure Government ben

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Örömmel osztjuk meg, hogy most már bevezettük a frissített [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) élményt a [Microsoft Azure Government portálon.](https://portal.azure.us/) További információkért lásd [bejelentésünk blogbejegyzést](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Vészhelyreállítás: A kiépítési konfiguráció letöltése és tárolása

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés
 
Az Azure AD-kiépítési szolgáltatás a konfigurációs képességek gazdag készletét biztosítja. Az ügyfeleknek képesnek kell lenniük a konfiguráció mentésére, hogy később hivatkozhassanak rá, vagy visszaállíthatók legyenek egy ismert jó verzióra. Hozzáadtuk a lehetőséget, hogy letöltsd a kiépítési konfigurációt JSON-fájlként, és feltölthesd, amikor szüksége van rá. [További információ](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Az SSPR -hez (önkiszolgáló jelszó-visszaállítás) mostantól két kapuszükséges a 21Vianet (Azure China 21Vianet) által üzemeltetett Microsoft Azure-rendszergazdák számára. 

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Önkiszolgáló jelszó visszaállítása  
**Termék képesség:** Identitásbiztonsági & védelem
 
Korábban a Microsoft Azure által üzemeltetett 21Vianet (Azure China 21Vianet), a rendszergazdák segítségével önkiszolgáló jelszó-visszaállítás (SSPR) alaphelyzetbe állításához saját jelszavakat szükséges csak egy "kapu" (kihívás) személyazonosságuk igazolásához. A nyilvános és más nemzeti felhők, adminok általában két kaput kell használniuk, hogy igazolják személyazonosságukat, amikor sspr. Mivel azonban nem támogattuk az SMS- és telefonhívásokat az Azure China 21Vianet-ben, lehetővé tettük az egykapus jelszó visszaállítását a rendszergazdák számára.

SSPR-funkcióparitást hozunk létre az Azure China 21Vianet és a nyilvános felhő között. A továbbiakban az adminisztrátorok két kaput kell használniuk az SSPR használatakor. Az SMS-ek, a telefonhívások és az Authenticator alkalmazásértesítések és -kódok támogatottak lesznek. [További információ](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>A jelszó hossza legfeljebb 256 karakter ből állhat.

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Az Azure AD-szolgáltatás megbízhatóságának biztosítása érdekében a felhasználói jelszavak hossza mostantól 256 karakter. Azok a felhasználók, akikennél hosszabb jelszóval rendelkeznek, a későbbi bejelentkezéskor meg kell változtatniuk a jelszavukat, akár a rendszergazdájukkal, akár az önkiszolgáló jelszó-visszaállítási funkció használatával.

Ez a módosítás engedélyezve volt március 13-án, 2020, 10:00 PST (18:00 UTC), és a hiba AADSTS 50052, InvalidPasswordExceedsMaxLength. További részletekért tekintse meg a [változásról szóló értesítést.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Az Azure AD bejelentkezési naplói már elérhetők az összes ingyenes bérlő számára az Azure Portalon keresztül

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Jelentési  
**Termék képesség:** A & jelentés ének nyomon követése
 
Mostantól kezdve az ingyenes bérlőkkel rendelkező ügyfelek akár 7 napig is elérhetik az [Azure AD bejelentkezési naplóit az Azure Portalról.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) Korábban a bejelentkezési naplók csak az Azure Active Directory Prémium licenccel rendelkező ügyfelek számára voltak elérhetők. Ezzel a módosítással az összes bérlő hozzáférhet ezekhez a naplókhoz a portálon keresztül.

> [!NOTE]
> Az ügyfelektovábbra is szükség van egy prémium licenc (Azure Active Directory Premium P1 vagy P2) a bejelentkezési naplók eléréséhez a Microsoft Graph API-n és az Azure Monitoron keresztül.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Directory-szintű csoportok eprecikálása beállítás az Azure Portal csoportok általános beállításai ból

**Típus:** Elavult  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Együttműködés

Annak érdekében, hogy az ügyfelek rugalmasabban hozhassanak létre az igényeiknek leginkább megfelelő címtárszintű csoportokat, az Azure Portal **Általános csoport** > **General** beállításaiközött lévő **Directory-szintű csoportok** beállítást a [dinamikus csoportdokumentációra](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)mutató hivatkozásra cseréltük. Javítottuk a dokumentációt, hogy további utasításokat tartalmazzon, így a rendszergazdák létrehozhatnak minden felhasználócsoportot, amely vendégfelhasználókat tartalmaz vagy kizár.

---

## <a name="february-2020"></a>2020. február

### <a name="upcoming-changes-to-custom-controls"></a>Az egyéni vezérlők közelgő módosításai

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem
 
Azt tervezzük, hogy a jelenlegi egyéni vezérlők előnézetét egy olyan megközelítéssel cseréljük le, amely lehetővé teszi, hogy a partnerek által biztosított hitelesítési képességek zökkenőmentesen működjenek együtt az Azure Active Directory rendszergazdájával és a végfelhasználói élményekkel. Ma a partner MFA-megoldásokkal a következő korlátozásokkal szembesülnek: csak a jelszó megadása után működnek; más kulcsfontosságú forgatókönyvekben nem szolgálnak többszintű hitelesítésként a hitelesítés fokozásához; és nem integrálhatók a végfelhasználói vagy felügyeleti hitelesítő adatok kezelési funkcióival. Az új megvalósítás lehetővé teszi, hogy a partner által biztosított hitelesítési tényezők a kulcsfontosságú forgatókönyvek beépített tényezőivel együtt működjenek, beleértve a regisztrációt, a használatot, az MFA-jogcímeket, a hitelesítés fokozását, a jelentéskészítést és a naplózást. 

Az egyéni vezérlők továbbra is támogatottak az előnézetben az új terv mellett, amíg el nem érik az általános elérhetőséget. Ezen a ponton időt adunk az ügyfeleknek az új tervre való áttérésre. A jelenlegi megközelítés korlátai miatt nem fogunk új szolgáltatókat beszállni, amíg az új kialakítás elérhetővé nem válik. Szorosan együttműködünk az ügyfelekkel és a szolgáltatókkal, és ahogy közeledünk, kommunikálunk az idővonalon. [További információ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identitásbiztonságos pontszám – Az MFA fejlesztési műveletfrissítései

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem
 
Annak érdekében, hogy a vállalatok nak a vállalkozásukkal együttműködő házirendek alkalmazása mellett a legnagyobb biztonságot kell biztosítaniuk, a Microsoft Secure Score három, a többtényezős hitelesítésre (MFA) épülő fejlesztési műveletet távolít el, és kettőt ad hozzá.

A következő fejlesztési műveletek törlődnek:

- Az összes felhasználó regisztrálása az MFA-hoz
- MFA megkövetelése minden felhasználótól
- Többszintű szolgáltatás megkövetelése az Azure AD kiemelt szerepköreihez

A következő fejlesztési műveletekkel egészül ki:

- Annak biztosítása, hogy minden felhasználó teljesíthesse az MFA-t a biztonságos hozzáférés érdekében
- Felügyeleti szerepkörök höz többkori becs-e szükséges

Ezek az új fejlesztési műveletek megkövetelik a felhasználók vagy rendszergazdák regisztrálását az MFA-hoz a címtárban, és létre kell hozni a megfelelő szabályzatokat, amelyek megfelelnek a szervezeti igényeknek. A fő cél az, hogy a rugalmasság, miközben biztosítja az összes felhasználó és a rendszergazdák is hitelesíthető több tényező vagy a kockázatalapú identitás-ellenőrzési kéri. Ez biztonsági alapértelmezések beállításának formájában valósulhat meg, amelyek lehetővé teszik a Microsoft számára, hogy mikor hívja fel a felhasználókat az MFA-hoz, vagy több, hatókörrel rendelkező döntéseket alkalmazó szabályzatot. A fejlesztési művelet frissítések részeként az alapszintű védelmi szabályzatok a továbbiakban nem kerülnek be a pontozási számításokba. [További információ a Microsoft Secure Score újdonságairól.](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)

---

### <a name="azure-ad-domain-services-sku-selection"></a>Az Azure AD tartományszolgáltatások termékváltozatának kiválasztása

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások
 
Visszajelzést hallottunk arról, hogy az Azure AD Tartományi szolgáltatások ügyfelei nagyobb rugalmasságot szeretnének a példányaik teljesítményszintjének kiválasztásában. 2020. február 1-jétől egy dinamikus modellről (ahol az Azure AD határozza meg a teljesítményt és a tarifacsomagot az objektumok száma alapján) egy önválasztó modellre váltottunk. Mostantól az ügyfelek kiválaszthatják a környezetüknek megfelelő teljesítményszintet. Ez a módosítás azt is lehetővé teszi számunkra, hogy új forgatókönyveket, például az erőforráserdőket és a prémium funkciókat, például a napi biztonsági mentéseket engedélyezze. Az objektumok száma mostantól korlátlan az összes SKU-k, de továbbra is kínálunk objektumszám javaslatokat az egyes szintekhez.

**Nincs szükség azonnali ügyfélbeavatkozásra.** A meglévő ügyfelek esetében a 2020. Ennek a változásnak nincs árképzési vagy teljesítménybeli hatása. A továbbiakban az Azure AD DS-ügyfeleknek ki kell értékelniük a teljesítménykövetelményeket a címtárméret és a számítási feladatok jellemzőinek változásával. A szolgáltatási szintek közötti váltás továbbra is állásidő-mentes művelet lesz, és a továbbiakban nem helyezünk át automatikusan ügyfeleket új szintekre a címtár uk növekedése alapján. Továbbá nem lesz áremelés, és az új árak igazodnak a jelenlegi számlázási modellünkhöz. További információ: az [Azure AD DS SK dokumentációja](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) és az [Azure AD tartományi szolgáltatások díjszabási lapja.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Új összevont alkalmazások érhetők el az Azure AD App galériában - 2020.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2020 februárjában hozzáadtuk ezt a 31, Federation támogatással rendelkező új alkalmazást az alkalmazásgalériához: 

[IamIP Szabadalmi Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), Válság esetén - Online [portál,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Méhész Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry értékelések](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), Új [relikvia (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), Template [Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [FALKA](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink tanárok nak és iskoláknak](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Cikk Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Új létesítési összekötők az Azure AD alkalmazásgalériában – 2020.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Az Azure AD támogatása a FIDO2 biztonsági kulcsokhoz hibrid környezetekben

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
Bejelentjük az Azure AD-támogatás nyilvános előzetes verzióját hibrid környezetekben a FIDO2 biztonsági kulcsokhoz. A felhasználók mostantól a FIDO2 biztonsági kulcsaisegítségével bejelentkezhetnek a hibrid Azure AD-hez csatlakozott Windows 10-es eszközeikbe, és zökkenőmentesen bejelentkezhetnek a helyszíni és a felhőbeli erőforrásaikba. A hibrid környezetek támogatása a jelszónélküli ügyfeleink által leggyakrabban kért funkció, mivel először elindítottuk a FIDO2-támogatás nyilvános előzetes verzióját az Azure AD-hez csatlakozó eszközökben. A fejlett technológiákkal, például a biometriával és a nyilvános/titkos kulcsú titkosítással történő jelszó nélküli hitelesítés kényelmet és egyszerű használatot biztosít a biztonság érdekében. Ezzel a nyilvános előzetes verzióval modern hitelesítést, például FIDO2 biztonsági kulcsokat használhat a hagyományos Active Directory-erőforrások eléréséhez. További információ: [Egyszeri út a helyszíni erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Első lépésként keresse fel [a FIDO2 biztonsági kulcsainak engedélyezése a bérlő höz](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) lépésről lépésre. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Az új Saját fiók felület mostantól általánosan elérhető

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Saját profil/fiók  
**Termék képesség:** Végfelhasználói élmény
 
My Account, az egyablakos minden végfelhasználói fiók kezelési igényeit, most már általánosan elérhető! A végfelhasználók URL-címen vagy az új Saját alkalmazások felület fejlécében érhetik el ezt az új webhelyet. Tudjon meg többet az új élmény által elérhető összes önkiszolgáló funkcióról a [Saját fiókportál áttekintése oldalon.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>A fiókom webhelyének URL-címe myaccount.microsoft.com

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Saját profil/fiók  
**Termék képesség:** Végfelhasználói élmény
 
Az új Fiók végfelhasználói élmény a következő `https://myaccount.microsoft.com` hónapban frissíti az URL-címét. További információ a felhasználói élményről és az összes fiókönkiszolgáló képességről, amelyet a végfelhasználóknak kínál, a [My Account portal súgójában talál](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)további információt.

---
 
## <a name="january-2020"></a>2020. január
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Az új My Apps portál már általánosan elérhető

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Végfelhasználói élmény
 
Frissítse szervezetét az új My Apps portálra, amely már általánosan elérhető! További információ az új portálon és gyűjtemények [a Gyűjtemények létrehozása a My Apps portálon.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Az Azure AD munkaterületeit átnevezték gyűjteményekbe

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Saját alkalmazások   
**Termék képesség:** Végfelhasználói élmény
 
A munkaterületek, a rendszergazdák által konfigurálható szűrők a felhasználói alkalmazások rendszerezésére, mostantól gyűjteménynek lesznek nevezve. További információ a konfigurálásukról a [Gyűjtemények létrehozása a Saját alkalmazások portálon.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C-telefonra való feliratkozás és bejelentkezés egyéni szabályzattal (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C
 
A telefonszám-regisztrációval és bejelentkezéssel a fejlesztők és a vállalatok lehetővé tehetik ügyfeleik számára, hogy a felhasználó telefonszámára SMS-ben küldött egyszeri jelszóval regisztráljanak és jelentkezzenek be. Ez a funkció azt is lehetővé teszi, hogy az ügyfél módosítsa a telefonszámát, ha elveszíti a hozzáférést a telefonhoz. Az egyéni házirendek erejével a telefonos regisztráció és a bejelentkezés lehetővé teszi a fejlesztők és a vállalatok számára, hogy az oldal testreszabásán keresztül kommunikálják a márkájukat. Megtudhatja, hogyan [állíthatja be a telefonos regisztrációt és az egyéni szabályzatokkal való bejelentkezést az Azure AD B2C szolgáltatásban.](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Új létesítési összekötők az Azure AD alkalmazásgalériában – 2020.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában - 2020.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja
 
2020 januárjában hozzáadtuk ezt a 33 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access`, [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, és JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), Hosted [MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), `https://sites.lumapps.com/login` [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB microsoft](http://teams.smartdb.jp/login/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)csapatok , [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Magatartás Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [Ping AuthenticationFlow](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) `https://app.sandwai.com/`

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="two-new-identity-protection-detections"></a>Két új identitásvédelmi észlelés

**Típus:** Új funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem
 
Két új bejelentkezési csatolt észlelési típust adtunk hozzá az Identity Protection hez: gyanús beérkezett üzenetek manipulálására vonatkozó szabályokat és lehetetlen utazást. Ezeket az offline észleléseket a Microsoft Cloud App Security (MCAS) fedezte fel, és befolyásolják a felhasználó és a bejelentkezési kockázatot az Identitásvédelemben. Az észlelésekkel kapcsolatos további információkért tekintse meg [a bejelentkezési kockázati típusokat.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: URI töredékek nem kerül sor a bejelentkezési átirányítás

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés
 
2020. február 8-tól kezdődően, amikor egy kérést küldenek login.microsoftonline.com egy felhasználó bejelentkezéséhez, a szolgáltatás egy üres töredéket fűz a kérelemhez.  Ez megakadályozza az átirányítási támadások egy osztályát, mivel biztosítja, hogy a böngésző törölje a kérelemben lévő töredékeket. Egyetlen alkalmazásnak sem kell függenie ettől a viselkedéstől. További információt a [Módosítások megbontása](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) a Microsoft identity platform dokumentációjában című témakörben talál.

---

## <a name="december-2019"></a>2019. december

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Az SAP SuccessFactors kiépítése integrálása az Azure AD-be és a helyszíni AD-be (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Alkalmazáskiépítés  
**Termék képesség:** Identitáséletciklus-kezelés

Most már integrálhatja az SAP SuccessFactors-ot mérvadó identitásforrásként az Azure AD-ben. Ez az integráció segít automatizálni a végpontok közötti identitás életciklusát, beleértve a HR-alapú események, például az új hires vagy a felmondások használatával az Azure AD-fiókok kiépítésének szabályozásához.

Az SAP SuccessFactors azure AD-be való bejövő kiépítésbeállításáról az [SAP SuccessFactors automatikus kiépítési oktatóanyagkonfigurálása](https://aka.ms/SAPSuccessFactorsInboundTutorial) című témakörben talál további információt.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Személyre szabott e-mailek támogatása az Azure AD B2C-ben (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Most már használhatja az Azure AD B2C hozhat létre testre szabott e-maileket, amikor a felhasználók feliratkozik az alkalmazások használatára. A DisplayControls (jelenleg előzetes verzióban) és egy külső e-mail szolgáltató (például [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)vagy egyéni REST API) használatával használhatja saját e-mail sablonját, a **Címből** és a tárgy szövegét, valamint támogathatja a honosítást és az egyéni egyszeri jelszó (OTP) beállításokat.

További információ: [Egyéni e-mail-ellenőrzés az Azure Active Directory B2C-ben.](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Az alapházirendek lecserélése biztonsági alapértékekre

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Identitásbiztonság és -védelem

A hitelesítés hez alapértelmezés szerint biztonságos modell részeként eltávolítjuk a meglévő alapszintű védelmi szabályzatokat az összes bérlőből. Az eltávolítás tágítása február végén várható. Ezek az alapszintű védelmi házirendek a biztonsági alapértékek. Ha alapszintű védelmi házirendeket használt, meg kell terveznie, hogy az új biztonsági alapházirend-házirendre vagy a feltételes hozzáférésre lépjen. Ha még nem használta ezeket a házirendeket, nincs teendő.

Az új biztonsági alapértelmezésekről a [Mik a biztonsági alapértelmezések?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) A feltételes hozzáférési házirendekről a [Gyakori feltételes hozzáférési házirendek](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)című témakörben talál további információt.

---

## <a name="november-2019"></a>2019. november

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>A SameSite attribútum és a Chrome 80 támogatása

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** Felhasználói hitelesítés

A cookie-k alapértelmezett biztonságos modelljének részeként a Chrome 80 böngésző megváltoztatja, hogyan kezeli a cookie-kat az `SameSite` attribútum nélkül. Minden olyan cookie-t, `SameSite` amely nem adja meg az `SameSite=Lax`attribútumot, a kezelés úgy lesz kezelve, mintha az lett volna beállítva , ami azt eredményezi, hogy a Chrome blokkolja az okat a tartományok közötti cookie-megosztási forgatókönyveket, amelyektől az alkalmazás függhet. A régebbi Chrome-viselkedés fenntartása érdekében használhatja `SameSite=None` az `Secure` attribútumot, és hozzáadhat egy további attribútumot, így a webhelyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a tervek szerint 2020. február 4-ig fejezi be a módosítást.

Javasoljuk, hogy minden fejlesztőnk tesztelje alkalmazásait ezzel az útmutatóval:

- Állítsa a Biztonságos **cookie használata** beállítás alapértelmezett értékét **Igen**értékre.

- Állítsa a **SameSite** attribútum alapértelmezett értékét **Nincs**értékre.

- Adjon hozzá `SameSite` egy további attribútumot a **Secure**tulajdonsághoz.

További információ: [Közelgő SameSite cookie-változások ASP.NET és ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) és a potenciális zavar az ügyfél weboldalak és a Microsoft termékek és szolgáltatások Chrome [79-es és újabb verzió.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Új gyorsjavítás a Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) szervizcsomaghoz

**Típus:** Rögzített  
**Szolgáltatás kategória:** Microsoft Identity Manager  
**Termék képesség:** Identitáséletciklus-kezelés

A Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) szervizcsomaghoz gyorsjavítás-összesítő csomag (4.6.34.0) érhető el. Ez az összesítő csomag megoldja a problémákat, és hozzáadja a "A frissítésben javított problémák és fejlesztések" című szakaszban ismertetett fejlesztéseket.

További információt és a gyorsjavító csomag letöltését a [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) összegző frissítőcsomagjában talál.](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Új AD FS-alkalmazástevékenység-jelentés az alkalmazások Azure AD-be való áttelepítéséhez (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Az Azure Portalon az új Active Directory összevonási szolgáltatások (AD FS) alkalmazástevékenység-jelentés segítségével azonosíthatja, hogy mely alkalmazások telepíthetők át az Azure AD-be. A jelentés felméri az összes AD FS-alkalmazást az Azure AD-vel való kompatibilitás szempontjából, ellenőrzi az esetleges problémákat, és útmutatást ad az egyes alkalmazások áttelepítésre való előkészítéséhez.

További információ: [Az AD FS-alkalmazástevékenység-jelentés használata alkalmazások Azure AD-be való áttelepítéséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)című témakörben található.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Új munkafolyamat a felhasználók számára a rendszergazda i. hozzájárulásának kérelmezésére (Nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Hozzáférés-vezérlés

Az új rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdák számára, hogy rendszergazdai jóváhagyást igénylő alkalmazásokhoz biztosítsanak hozzáférést. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tudja megadni a beleegyezését, most rendszergazdai jóváhagyás iránti kérelmet küldhet. A kérelem e-mailben küldi el, és az Azure Portalról elérhető várólistába kerül az összes ellenőrzőként kijelölt rendszergazdáknak. Miután egy véleményező műveletet tesz egy függőben lévő kérelemmel, a kérelmező felhasználók értesítést kapnak a műveletről.

További információ: [Configure the admin consent workflow (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Új Azure AD-alkalmazásregisztrációk token konfigurációs felülete a választható jogcímek kezeléséhez (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Fejlesztői élmény

Az Azure-portál on az új **Azure AD-alkalmazásregisztrációk token konfigurációs** panelmost megmutatja az alkalmazásfejlesztőknek az alkalmazásaik opcionális jogcímeinek dinamikus listáját. Ez az új felület segít az Azure AD-alkalmazások áttelepítésének egyszerűsítésében és a jogcímek helytelen konfigurációjának minimalizálása érdekében.

További információ: [Választható jogcímek biztosítása az Azure AD-alkalmazáshoz című témakörben.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Új kétlépcsős jóváhagyási munkafolyamat az Azure AD jogosultságkezelésében (nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Jogosultságok kezelése

Bevezettünk egy új kétlépcsős jóváhagyási munkafolyamatot, amely lehetővé teszi, hogy két jóváhagyótól kérje meg a felhasználó hozzáférési csomaghoz való kérelmét. Beállíthatja például, hogy a kérelmező felhasználó kezelőjének először jóvá kell hagynia, és akkor az erőforrás tulajdonosának is jóváhagyhatja. Ha az egyik jóváhagyó nem hagyja jóvá, a hozzáférés nem engedélyezett.

További információt a [Hozzáférési csomag kérés- és jóváhagyási beállításainak módosítása az Azure AD-jogosultságkezelésben](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)című témakörben talál.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>A Saját alkalmazások lap frissítései az új munkaterületekkel együtt (Nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termékképesség:** harmadik fél integrációja

Mostantól testreszabhatja, hogy a szervezet felhasználói hogyan tekintsék meg és férjessék el a frissített Saját alkalmazások felületet. Ez az új felület tartalmazza az új munkaterület-funkciót is, amely megkönnyíti a felhasználók számára az alkalmazások keresését és rendszerezését.

Az új Saját alkalmazások élményről és a munkaterületek létrehozásáról a [Munkaterületek létrehozása a Saját alkalmazások portálon című témakörben](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)talál további információt.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google közösségi azonosító támogatása az Azure AD B2B együttműködéshez (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2b  
**Termék képesség:** Felhasználói hitelesítés

A Google közösségi azonosítók (Gmail-fiókok) Azure AD-ben való használatának új támogatása egyszerűbbé teszi az együttműködést a felhasználók és a partnerek számára. A továbbiakban nincs szükség arra, hogy partnerei új Microsoft-specifikus fiókot hozzanak létre és kezeljenek. A Microsoft Teams mostantól teljes mértékben támogatja a Google-felhasználókat az összes ügyfélen, valamint a közös és a bérlőkkel kapcsolatos hitelesítési végpontokon.

További információ: [A Google hozzáadása identitásszolgáltatóként a B2B vendégfelhasználók számára.](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile támogatás feltételes hozzáféréshez és egyszeri bejelentkezéshez (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Feltételes hozzáférés  
**Termék képesség:** Identitásbiztonsági & védelem

Az Azure AD for Microsoft Edge iOS és Android rendszeren mostantól támogatja az Azure AD egyszeri bejelentkezést és a feltételes hozzáférést:

- **Microsoft Edge egyszeri bejelentkezés (SSO):** Egyszeri bejelentkezés már elérhető a natív ügyfelek (például a Microsoft Outlook és a Microsoft Edge) az összes Azure AD-csatlakoztatott alkalmazások.

- **Microsoft Edge feltételes hozzáférés:** Az alkalmazásalapú feltételes hozzáférési szabályzatok on keresztül a felhasználóknak a Microsoft Intune által védett böngészőket, például a Microsoft Edge-et kell használniuk.

A feltételes hozzáférésről és a Microsoft Edge-rel való egyszeri bejelentkezésről további információt a [Microsoft Edge Mobile support for Conditional Access és az Single Sign-on Now Általánosan elérhető](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogbejegyzésében talál. Az ügyfélalkalmazások [alkalmazásalapú feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) vagy [eszközalapú feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)történő beállításáról a [Webes hozzáférés kezelése Microsoft Intune-alapú, házirenddel védett böngészővel](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)című témakörben talál további információt.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD jogosultságkezelés (általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Más  
**Termék képesség:** Jogosultságok kezelése

Az Azure AD jogosultságkezelés egy új identitás-irányítási funkció, amely segít a szervezeteknek az identitás és a hozzáférés életciklusának nagy méretekben kezelését. Ez az új funkció a hozzáférési kérelem munkafolyamatainak automatizálásával, a hozzárendelések elérésével, az ellenőrzésekkel és a lejárati idővel segíti a csoportok, alkalmazások és SharePoint Online-webhelyek közötti lejárati műveleteket.

Az Azure AD jogosultságkezelés, hatékonyabban kezelheti a hozzáférést mind az alkalmazottak, mind a szervezeten kívüli felhasználók számára, akiknek szükségük van ezekhez az erőforrásokhoz.

További információ: [Mi az Azure AD jogosultság kezelése?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiók kiépítésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja  

Most már automatizálhatja a felhasználói fiókok létrehozását, frissítését és törlését az újonnan integrált alkalmazásokhoz:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), Priority [Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Ha többet szeretne tudni arról, hogyan biztosíthatja hatékonyabban a szervezetet a felhasználói fiókok automatikus kiépítésével, olvassa el [a felhasználói kiépítés automatizálása saas-alkalmazásokba az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 novemberében a 21 Federation támogatással rendelkező új alkalmazást hozzáadtuk az alkalmazásgalériához:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access a tagok (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portál](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279` [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Új és továbbfejlesztett Azure AD alkalmazásgyűjtemény

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Frissítettük az Azure AD alkalmazásgalériát, hogy könnyebben megtalálhassanak olyan, előre integrált alkalmazásokat, amelyek támogatják a kiépítést, az OpenID Connectet és az SAML-t az Azure Active Directory-bérlőn.

További információ: [Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Az alkalmazásszerepkör-definíció hosszának megnövelt időtartama 120 karakterről 240 karakterre

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

Az ügyfelektől azt hallottuk, hogy egyes alkalmazásokban és szolgáltatásokban az alkalmazásszerepkör-definíciós érték hosszkori korlátja túl rövid 120 karakternél. Erre válaszul a szerepkörérték-definíció maximális hosszát 240 karakterre növeltük.

Az alkalmazásspecifikus szerepkör-definíciók használatáról az Alkalmazásszerepkörök hozzáadása az alkalmazásban című témakörben talál [további információt, és fogadja őket a jogkivonatban.](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)

---

## <a name="october-2019"></a>2019. október

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Az identitásRiskEvent API e-desia irda az Azure AD Identity Protection kockázatészleléséhez  

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

A fejlesztői visszajelzésekre adott válaszként az Azure AD Premium P2-előfizetők mostantól összetett lekérdezéseket hajthatnak végre az Azure AD Identity Protection kockázatészlelési adataival a Microsoft Graph új riskDetection API-jával. A meglévő [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API béta verzió leáll az adatok visszaadása körül **január 10, 2020**. Ha a szervezet az identityRiskEvent API-t használja, át kell térnie az új riskDetection API-ra.

Az új riskDetection API-ról további információt a [Kockázatészlelési API referenciadokumentációjában](https://aka.ms/RiskDetectionsAPI)talál.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Alkalmazásproxy-támogatás a SameSite attribútumhoz és a Chrome 80-hoz

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** Alkalmazásproxy  
**Termék képesség:** Hozzáférés-vezérlés

Néhány héttel a Chrome 80 böngésző kiadása előtt azt tervezzük, hogy frissítjük, hogyan kezelik az Alkalmazásproxy cookie-k a **SameSite** attribútumot. A Chrome 80 kiadásával minden olyan cookie-t, amely nem adja meg a `SameSite=Lax` **SameSite** attribútumot, a kezelés úgy lesz kezelve, mintha a .

A változás ból eredő esetleges negatív hatások elkerülése érdekében frissítjük az alkalmazásproxy-hozzáférést és a munkamenet-cookie-kat:

- A Biztonságos cookie **használata** beállítás alapértelmezett értékének beállítása **Igen**értékre.

- **A SameSite** attribútum alapértelmezett értékének beállítása **Nincs**értékre.

    >[!NOTE]
    > Az alkalmazásproxy-hozzáférési cookie-kat mindig is kizárólag biztonságos csatornákon keresztül továbbították. Ezek a módosítások csak a munkamenet-cookie-kra vonatkoznak.

Az alkalmazásproxy cookie-beállításairól további információt a [Cookie-beállítások a helyszíni alkalmazások Azure Active Directoryban való eléréséhez című témakörben talál.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Az alkalmazásregisztrációs portálon (apps.dev.microsoft.com) az alkalmazásregisztrációk (örökölt) és a konvergens alkalmazáskezelés már nem lesz elérhető

**Típus:** Változásmegtervezése  
**Szolgáltatás kategória:** N/a  
**Termék képesség:** Fejlesztői élmény

A közeljövőben az Azure AD-fiókkal rendelkező felhasználók a továbbiakban nem regisztrálhatnak és nem kezelhetik a konvergens alkalmazásokat az alkalmazásregisztrációs portálon (apps.dev.microsoft.com), illetve nem regisztrálhatnak és kezelhetnek alkalmazásokat az Azure-portálon az alkalmazásregisztrációk (örökölt) élményben.

Ha többet szeretne megtudni az új alkalmazásregisztrációk élményéről, tekintse meg az [alkalmazásregisztrációkat az Azure Portal oktatóútmutatójában.](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>A felhasználóknak a továbbiakban nem kell újra regisztrálniuk a felhasználónkénti többszintű többszintű hozzáférésről feltételes hozzáférésű többszintű többszintű rendszer-berendszeren alapuló programra való áttérés során

**Típus:** Rögzített  
**Szolgáltatás kategória:** Mfa  
**Termék képesség:** Identitásbiztonsági & védelem

Kijavítottunk egy ismert problémát, amely nek, amikor a felhasználóknak újra regisztrálniuk kellett, ha le voltak tiltva a felhasználónkénti többtényezős hitelesítéshez (MFA), majd feltételes hozzáférési szabályzaton keresztül engedélyezve voltak az MFA számára.

Ha meg szeretné követelni a felhasználókat, hogy újra regisztrálja, kiválaszthatja a **szükséges újraregisztrálni az MFA-beállítást** a felhasználó hitelesítési módszereiből az Azure AD portálon. A felhasználók felhasználónkénti többszintű telepítési rendszerről feltételes hozzáférésalapú többszintű makrofa-ra való áttelepítéséről a [Felhasználók felhasználónkénti több-támogatásból feltételes hozzáférésalapú többszintű telepítési rendszerré alakításácímű](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)témakörben talál további információt.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Új képességek az SAML-jogkivonat jogcíméhez való jogcímek átalakításához és küldéséhez

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termék képesség:** Sso

További képességekkel bővült, amelyek segítségével testre szabhatja és elküldheti a jogcímeket az SAML-jogkivonatban. Ezek az új képességek a következők:

- További jogcímek átalakítási függvények, segíta jogcím küld a jogcím módosítása.

- Több átalakítás alkalmazása egyetlen jogcímre.

- A jogcímforrás megadása a felhasználó típusa és azon csoport alapján, amelyhez a felhasználó tartozik.

Az új képességekről részletes tájékoztatást, többek között azok használatáról [a Vállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása című témakörben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)talál részletes információt.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Új bejelentkezések lap a végfelhasználók számára az Azure AD-ben

**Típus:** Új funkció  
**Szolgáltatás kategória:** Hitelesítések (bejelentkezések)  
**Termék képesség:** A & jelentés ének nyomon követése

Új **Bejelentkezési** lapot adtunk hozzáhttps://mysignins.microsoft.com) (hogy a szervezet felhasználói megtekinthessék a legutóbbi bejelentkezési előzményeiket, hogy ellenőrizzék a szokatlan tevékenységeket. Ez az új oldal lehetővé teszi a felhasználók számára, hogy a következőket lássák:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha a támadó sikeresen bejelentkezett a fiókjába, és milyen helyről.

- Milyen alkalmazásokat próbált a támadó elérni.

További információkért lásd: A [felhasználók most ellenőrizhetik a bejelentkezési előzményeket a szokatlan tevékenységblogért.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Az Azure AD tartományi szolgáltatások (Azure AD DS) áttelepítése klasszikusról Azure Resource Manager virtuális hálózatokra

**Típus:** Új funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Ügyfeleinknek, akik a klasszikus virtuális hálózatokon ragadtak - nagyszerű híreink vannak az Ön számára! Most már végrehajthat egy egyszeri áttelepítést egy klasszikus virtuális hálózatról egy meglévő Erőforrás-kezelő virtuális hálózatra. Az Erőforrás-kezelő virtuális hálózatára való áttérés után kihasználhatja a további és továbbfejlesztett funkciókat, például a részletes jelszóházirendeket, az e-mail értesítéseket és a naplónaplókat.

További információ: [Preview - Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Az Azure AD B2C lapszerződés elrendezésének frissítései

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Az Azure AD B2C lapszerződés 1.2.0-s verziójának néhány új módosítását vezettük be. Ebben a frissített verzióban most szabályozhatja az elemek betöltési sorrendjét, ami szintén segíthet megállítani a stíluslap (CSS) betöltésekor bekövetkező villódzást.

Az oldalegyezménymódosításai teljes listáját a [Verziómódosítási napló ban](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)találja.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Frissítés a Saját alkalmazások lapra az új munkaterületekkel együtt (Nyilvános előzetes verzió)

**Típus:** Új funkció  
**Szolgáltatás kategória:** Saját alkalmazások  
**Termék képesség:** Hozzáférés-vezérlés

Mostantól testreszabhatja, hogy a szervezet felhasználói hogyan tekintsék meg és férjessék el a vadonatúj Saját alkalmazások felületet, beleértve az új munkaterületek funkció használatát is, hogy megkönnyítsék számukra az alkalmazások keresését. Az új munkaterületek funkció szűrőként működik azon alkalmazások számára, amelyekhez a szervezet felhasználói már rendelkeznek hozzáféréssel.

Az új Saját alkalmazások felület ének bevezetésével és a munkaterületek létrehozásával kapcsolatos további információkért olvassa el [a Munkaterületek létrehozása a Saját alkalmazások (előzetes verzió) portálon című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>A havi aktív felhasználóalapú számlázási modell támogatása (Általános elérhetőség)

**Típus:** Új funkció  
**Szolgáltatás kategória:** B2C - Fogyasztói identitáskezelés  
**Termék képesség:** B2B/B2C

Az Azure AD B2C mostantól támogatja a havi aktív felhasználók (MAU) számlázását. A MAU-számlázás a hitelesítési tevékenységet egy naptári hónapsorán végzett egyedi felhasználók számán alapul. A meglévő ügyfelek bármikor átválthatnak erre az új számlázási módszerre.

2019. november 1-jétől minden új ügyfélnek automatikusan fizetnie kell ezzel a módszerrel. Ez a számlázási módszer a költségelőnyök és az előretervezés lehetősége révén előnyös az ügyfelek számára.

További információ: [Frissítés havi aktív felhasználók számlázási modell.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Új összevont alkalmazások érhetők el az Azure AD Alkalmazásgalériában – 2019.

**Típus:** Új funkció  
**Szolgáltatás kategória:** Vállalati alkalmazások  
**Termékképesség:** harmadik fél integrációja

2019 októberében hozzáadtuk ezt a 35 új, Federation támogatással rendelkező alkalmazást az alkalmazásgalériához:

[Válság esetén – Mobil,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial) [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Elégedett](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Koordináta - Európa](https://www.hirevue.com/), [HireVue Koordináta - USOnly](https://www.hirevue.com/), [HireVue Koordináta - US,](https://www.hirevue.com/) [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore,](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial) [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial) [, Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Speaking Email [for Intune (iPhone),](https://speaking.email/FAQ/98/email-access-via-microsoft-intune) [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Az alkalmazásokról további információt az [SaaS-alkalmazások Azure Active Directoryval való integrációja](https://aka.ms/appstutorial)című témakörben talál. Az alkalmazás az Azure AD alkalmazásgyűjteményben való listázásáról az [Alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://aka.ms/azureadapprequest)című témakörben talál további információt.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Összevont biztonság menüelem az Azure AD portálon

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Identitásvédelem  
**Termék képesség:** Identitásbiztonsági & védelem

Most már elérheti az összes rendelkezésre álló Azure AD biztonsági funkciók az új **biztonsági** menüelem, és a **keresősáv,** az Azure Portalon. Ezenkívül az új **Biztonsági** céloldal, az úgynevezett **Biztonság - Első lépések**, hivatkozásokat biztosít a nyilvános dokumentációnkhoz, biztonsági útmutatónkhoz és telepítési útmutatóinkhoz.

Az új **Biztonsági** menü a következőket tartalmazza:

- Feltételes hozzáférés
- Identity Protection
- Security Center
- Identitás biztonságos pontszáma
- Hitelesítési módszerek
- Mfa
- Kockázati jelentések – Kockázatos felhasználók, Kockázatos bejelentkezések, Kockázatészlelések
- És még tovább...

További információ: [Biztonság – Első lépések.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Automatikus megújítással bővített Office 365-csoportok lejárati házirendje

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Csoportkezelés  
**Termék képesség:** Identitáséletciklus-kezelés

Az Office 365-csoportok lejárati házirendje tovább lett fejlesztve, hogy automatikusan megújíthassa a tagjai által aktívan használt csoportokat. A csoportok automatikusan megújulnak az összes Office 365-alkalmazásban , például az Outlookban, a SharePointban és a Teamsben végzett felhasználói tevékenységek alapján.

Ez a fejlesztés segít csökkenteni a csoport lejárati értesítéseit, és biztosítja, hogy az aktív csoportok továbbra is elérhetők legyenek. Ha már rendelkezik aktív lejárati szabályzattal az Office 365-csoportokhoz, semmit sem kell tennie az új funkció bekapcsolásához.

További információt [az Office 365-csoportok lejárati szabályzatának konfigurálása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Frissített Azure AD tartományi szolgáltatások (Azure AD DS) létrehozási élmény

**Típus:** Megváltozott funkció  
**Szolgáltatás kategória:** Azure AD tartományi szolgáltatások  
**Termék képesség:** Azure AD tartományi szolgáltatások

Frissítettük az Azure AD tartományi szolgáltatásokat (Azure AD DS), hogy új és továbbfejlesztett létrehozási élményt nyújtson, így mindössze három kattintással hozhat létre felügyelt tartományt! Emellett most már feltöltheti és üzembe helyezheti az Azure AD DS-t egy sablonból.

További információt az [Oktatóanyag: Azure Active Directory tartományi szolgáltatások példányának létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)című témakörben talál.

---
