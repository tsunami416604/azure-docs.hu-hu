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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f879ebd2f3628b8282342d730a5f3957cf2a615f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994987"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` az ![ RSS-hírcsatorna olvasójának ikonját a ](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="april-2020"></a>2020. április

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Általánosan elérhető a kombinált biztonsági adatok regisztrációs felülete

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Identitás-biztonsági & védelme

A Multi-Factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítás (SSPR) együttes regisztrációs felülete már általánosan elérhető. Ez az új regisztrációs élmény lehetővé teszi, hogy a felhasználók egyetlen, lépésenkénti folyamattal regisztráljanak az MFA-és SSPR. Amikor üzembe helyezi az új felhasználói élményt a szervezet számára, a felhasználók kevesebb idő alatt regisztrálhatnak, és kevesebb gondot biztosítanak. Tekintse meg a blogbejegyzést [itt](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés kiértékelése

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Identitás-biztonsági & védelme

A folyamatos hozzáférés kiértékelése egy új biztonsági funkció, amely lehetővé teszi a házirendek közel valós idejű kényszerítését az Azure ad hozzáférési jogkivonatokat használó függő entitások számára, ha az események az Azure AD-ben történnek (például a felhasználói fiókok törlése). Ezt a funkciót először a csapatok és az Outlook-ügyfelek számára tesszük elérhetővé. További részletekért tekintse meg a [blogot](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) és a [dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-bejelentkezés: a Firstline-feldolgozók bejelentkezhetnek az Azure AD-alapú alkalmazásokba a telefonszámuk és a jelszó nélkül

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Felhasználói hitelesítés

Az Office olyan mobil-első üzleti alkalmazásokat indít el, amelyek a nem hagyományos szervezeteknek, illetve a nagyméretű szervezetek alkalmazottainak, akik nem használják az e-maileket elsődleges kommunikációs módszerként. Ezek az alkalmazások megcélozják az alkalmazottakat, az íróasztal nélküli munkavégzőket, a helyszíni ügynököket vagy a kiskereskedelmi alkalmazottakat, akik nem kapnak e-mail-címet a munkaadótól, hozzáférhetnek a számítógéphez vagy a szolgáltatáshoz. Ez a projekt lehetővé teszi, hogy ezek az alkalmazottak bejelentkezzenek az üzleti alkalmazásokba egy telefonszám megadásával és egy kód roundtripping. További részletekért tekintse meg a [rendszergazdai dokumentációt](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) és a [végfelhasználói dokumentációt](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Belső felhasználók meghívása a B2B-együttműködés használatára

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** B2B

**A termék képességei:**

Bővítettük a B2B-Meghívási képességet, amely lehetővé teszi a meglévő belső fiókok meghívását a VÁLLALATKÖZI együttműködési hitelesítő adatok továbbítására. Ezt úgy teheti meg, hogy a meghívott e-mail-címre jellemző paraméterek mellett átadja a felhasználói objektumot a meghívó API-nak. A felhasználó objektumazonosító, UPN, csoporttagság, alkalmazás-hozzárendelés stb. változatlan marad, de a jövőben a B2B használatával hitelesíthető a saját bérlői hitelesítő adataival, nem pedig a meghívást megelőzően használt belső hitelesítő adatokkal. Részletekért tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>A feltételes hozzáféréshez már általánosan elérhető a csak jelentés üzemmód

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

Az [Azure ad feltételes hozzáféréshez csak jelentési mód](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. A szervezeten belül csak a jelentésekre vonatkozó házirendeket lehet tesztelni, és megismerni a hatásukat, mielőtt engedélyezné őket, így biztonságosabbá és egyszerűbbé teheti a telepítést. Az elmúlt néhány hónapban a csak jelentési mód erős bevezetését láttuk, amely a 26M-felhasználókra már érvényes a csak jelentési szabályzat hatálya alá. Ezzel a bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy nyomon követheti a szabályzatok hatását a létrehozásuk pillanatától kezdve. Továbbá azok számára, akik az MS Graph API-kat használják, a [jelentésekre vonatkozó szabályzatokat programozott módon is kezelheti](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>A feltételes hozzáférési információ és a jelentéskészítési munkafüzet általánosan elérhető

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

A feltételes hozzáférési információk [és a jelentéskészítési munkafüzet](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) a rendszergazdáknak az Azure ad feltételes hozzáférés összefoglaló nézetét adja meg a bérlőn. Az egyes szabályzatok kiválasztásának lehetőségével a rendszergazdák jobban megismerhetik, hogy az egyes szabályzatok hogyan és valós időben figyelik a módosításokat. A munkafüzet a Azure Monitorban tárolt adatstreameket adatfolyamként tárolja, amelyeket néhány percen belül megadhat az [utasításokat követve](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Az irányítópult könnyebben felderíthetővé tételéhez áthelyezte az Azure AD feltételes hozzáférés menüjének új megállapítások és jelentéskészítés lapjára.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférésre vonatkozó szabályzat részletei panel nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

Az új [szabályzat részletei panel](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) megjeleníti, hogy mely hozzárendelések, feltételek és vezérlőelemek teljesültek a feltételes hozzáférési szabályzat kiértékelése során. A panelt úgy érheti el, ha kiválasztja a **feltételes hozzáférés** vagy **csak jelentési** lapokon a bejelentkezési adatokat tartalmazó sort.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – április 2020

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Vállalati alkalmazások

**Termék képesség:** harmadik féltől származó integráció

A 2020. áprilisában a következő 31 új alkalmazást bővítettük az App Gallery összevonási támogatásával: 

[SincroPool-alkalmazások](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [belvízi adatgyűjtési csomag](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO for JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki irányítópult](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 mozgató](https://app.mover.io/login), [beszélő tevékenység](https://speakerengage.com/login.php), [becsületesen](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Kata](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling megoldások](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet Click](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [Empower](https://www.made-in-office.com/en/), [Forts Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Lakmusz](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [, GroupTalk,](https://recorder.grouptalk.com/) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [MongoDB felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [nitro termelékenység Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>A nyilvános előzetes verzióhoz elérhető oAuth2PermissionGrant-Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Nyilvános előzetes verzióban elérhető a oAuth2PermissionGrant különbözeti lekérdezése. Mostantól nyomon követheti a változtatásokat anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. [További információ.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Általánosan elérhető a Microsoft Graph Delta-lekérdezés támogatása a szervezeti kapcsolattartáshoz

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

A szervezeti kapcsolatok különbözeti lekérdezése általánosan elérhető! Mostantól nyomon követheti az üzemi alkalmazások változásait anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. Cserélje le az összes olyan meglévő kódot, amely folyamatosan lekérdezi a orgContact a különbözeti lekérdezés által, hogy jelentősen javítsa a teljesítményt. [További információ.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Általánosan elérhető az alkalmazás Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Az alkalmazások különbözeti lekérdezése általánosan elérhető! Mostantól nyomon követheti az üzemi alkalmazások változásait anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. Cserélje le az összes olyan meglévő kódot, amely folyamatosan lekérdezi az alkalmazásadatok értékét a különbözeti lekérdezéssel, hogy jelentősen javítsa a teljesítményt. [További információ.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>A nyilvános előzetes verzióban elérhető felügyeleti egységekre vonatkozó Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Nyilvános előzetes verzióban elérhető a fejlesztői élmény változása a felügyeleti egységekhez. Mostantól nyomon követheti a változtatásokat anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. [További információ.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>A hitelesítő telefonszámok és egyebek kezelése az új Microsoft Graph Beta API-kon

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Ezek az API-k kulcsfontosságú eszközt biztosítanak a felhasználók hitelesítési módszereinek kezeléséhez. Mostantól programozott módon előre regisztrálhat és kezelhet az MFA-hoz és az önkiszolgáló jelszó-visszaállításhoz (SSPR) használt hitelesítő. Ez az Azure MFA, a SSPR és a Microsoft Graph Spaces egyik legtöbbször kért funkciója volt. Az ebben a hullámban megjelent új API-k lehetővé teszi a következőket:

- Felhasználó hitelesítési telefonjának olvasása, hozzáadása, frissítése és eltávolítása
- Felhasználó jelszavának alaphelyzetbe állítása
- SMS-bejelentkezés be-és kikapcsolása

További információ: az [Azure ad hitelesítési módszereinek API-áttekintése](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Nyilvános előzetes verziójú felügyeleti egységek

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

A felügyeleti egységek lehetővé teszik olyan rendszergazdai engedélyek megadását, amelyek az Ön által meghatározott szervezeti egységre, régióra vagy más szegmensre korlátozódnak. A felügyeleti egységekkel engedélyeket delegálhat a regionális rendszergazdáknak, vagy megadhatja a szabályzatot egy részletességi szinten. A felhasználói fiók rendszergazdája például frissítheti a profil adatait, alaphelyzetbe állíthatja a jelszavakat, és hozzárendelheti a licenceket a felhasználók számára a felügyeleti egységben.

A felügyeleti egységek használatával a központi rendszergazda:

- Felügyeleti egység létrehozása az erőforrások decentralizált kezeléséhez
- Rendszergazdai engedélyekkel rendelkező szerepkör társítása csak az Azure AD-felhasználók számára egy felügyeleti egységben
- Szükség szerint töltse fel a felügyeleti egységeket a felhasználókkal és a csoportokkal

További információ: [felügyeleti egységek kezelése Azure Active Directory (előzetes verzió)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Nyomtató-rendszergazda és nyomtató-technikus beépített szerepkörei

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

**Nyomtató rendszergazdája**: az ezzel a szerepkörrel rendelkező felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a Microsoft univerzális nyomtatási megoldás összes nyomtatási konfigurációjának minden aspektusát, beleértve az univerzális nyomtatási összekötő beállításait is. Az összes delegált nyomtatási engedély iránti kérelem beleegyezése. A nyomtató rendszergazdái is hozzáférhetnek a nyomtatási jelentésekhez. 

**Nyomtató-technikus**: az ezzel a szerepkörrel rendelkező felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a nyomtató állapotát a Microsoft Universal Print megoldásban. Emellett az összes összekötő adatait is elolvashatják. Legfontosabb feladatok a nyomtató technikusa nem tud felhasználói engedélyeket beállítani a nyomtatók és a nyomtatók megosztásához. [További információ.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Hibrid Identity admin beépített szerepkör

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

Az ebben a szerepkörben lévő felhasználók engedélyezhetik, konfigurálhatják és kezelhetik a hibrid identitás Azure AD-ben való engedélyezésével kapcsolatos szolgáltatásokat és beállításokat. Ez a szerepkör lehetővé teszi az Azure AD konfigurálását a következő három támogatott hitelesítési módszer egyikére:&#8212;jelszó-kivonatolási szinkronizálás (PHS), átmenő hitelesítés (PTA) vagy összevonás (AD FS vagy harmadik féltől származó összevonási szolgáltató) &#8212;és a kapcsolódó helyszíni infrastruktúra üzembe helyezéséhez. A helyszíni infrastruktúra magában foglalja a létesítést és a PTA-ügynököket. Ez a szerepkör lehetővé teszi a zökkenőmentes egyszeri bejelentkezés (S-SSO) engedélyezését a nem Windows 10-es vagy nem Windows Server 2016 rendszerű számítógépek zökkenőmentes hitelesítésének engedélyezéséhez. Emellett ez a szerepkör lehetővé teszi a bejelentkezési naplók megtekintését, valamint a figyelési és hibaelhárítási célból az állapot és az elemzés elérését. [További információ.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Hálózati rendszergazda beépített szerepköre

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

Az ezzel a szerepkörrel rendelkező felhasználók áttekinthetik a Microsoft által a saját felhasználói helyükről érkező hálózati telemetria alapuló hálózati peremhálózati javaslatokat. Az Office 365-hez készült hálózati teljesítmény a megfelelő vállalati ügyfél-hálózati peremhálózati architektúrára támaszkodik, amely általában a felhasználói helyekre jellemző. Ez a szerepkör lehetővé teszi a felderített felhasználói helyszínek szerkesztését és a hálózati paraméterek konfigurálását az adott helyszíneken a továbbfejlesztett telemetria-mérések és kialakítási javaslatok megkönnyítése érdekében. [További információ.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Tömeges tevékenységek és letöltések az Azure AD felügyeleti portál felületén

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Felhasználói felügyelet

A **termék képességei:** Directory

Az Azure AD-ben a felhasználókra és csoportokra vonatkozó tömeges tevékenységeket is végrehajthat, ha feltölt egy CSV-fájlt az Azure AD felügyeleti portál felületén. Létrehozhat felhasználókat, törölhet felhasználókat, és meghívja a vendég felhasználókat. Hozzáadhat és eltávolíthat tagokat egy csoportból.

Az Azure AD-erőforrások listáját az Azure AD felügyeleti portál felületéről is letöltheti. Letöltheti a címtárban található felhasználók listáját, a címtárban lévő csoportok listáját és egy adott csoport tagjait.

További információkért tekintse meg a következőket:

- [Felhasználók létrehozása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) vagy [vendég felhasználók meghívása](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Felhasználók törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) vagy [törölt felhasználók visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Felhasználók listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) vagy [a csoportok listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- Tagok [hozzáadása (Importálás)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) vagy [tagok eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) , vagy a [tagok listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) egy csoport számára

---

### <a name="my-staff-delegated-user-management"></a>Saját munkatársak által delegált felhasználói felügyelet

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Felhasználói felügyelet

**A termék képességei:**

A saját munkatársak lehetővé teszik a Firstline-kezelők, például egy áruház kezelője számára, hogy a személyzet tagjai hozzáférhessenek az Azure AD-fiókokhoz. A központi ügyfélszolgálatra való támaszkodás helyett a szervezetek olyan gyakori feladatokat delegálnak, mint például a jelszavak alaphelyzetbe állítása vagy a telefonszámok módosítása a Firstline-kezelőhöz. A munkatársakkal egy olyan felhasználó, aki nem tud hozzáférni a fiókjához, mindössze néhány kattintással elérheti a hozzáférést, és nincs szükség ügyfélszolgálatra vagy informatikai személyzetre. További információ: a [felhasználók kezelése a saját munkatársakkal (előzetes verzió)](https://aka.ms/MyStaffAdminDocs) és a [felhasználói felügyelet delegálása a munkatársakkal (előzetes verzió)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Frissített végfelhasználói élmény a hozzáférési felülvizsgálatokban

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok

A **termék képességei:** Identitás-szabályozás

Frissítettük a véleményezői élményt az Azure AD hozzáférési felülvizsgálatokhoz a saját alkalmazások portálon. Április végén a felülvizsgálók, akik bejelentkeznek az Azure AD-hozzáférési felülvizsgálatok-felülvizsgálati élményre, egy szalagcím jelenik meg, amely lehetővé teszi, hogy a frissített felhasználói élményt kipróbálják a saját hozzáférésben. Vegye figyelembe, hogy a frissített hozzáférési felülvizsgálatok funkciója ugyanazokat a funkciókat kínálja, mint a jelenlegi élmény, de az új funkciókkal rendelkező továbbfejlesztett felhasználói felülettel lehetővé teheti, hogy a felhasználók hatékonyak legyenek. [A frissített felhasználói élményről itt olvashat bővebben](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Ez a nyilvános előzetes verzió a 2020. július végéig tart. Július végén a felülvizsgálók, akik nem választották be az előzetes verziót, automatikusan a hozzáférési felülvizsgálatok végrehajtásához lesznek irányítva. Ha szeretné, hogy a felülvizsgálók véglegesen áttérjenek az előnézeti élményre az alkalmazásom szolgáltatásban, küldjön [itt egy kérést](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>A munkanapon bejövő felhasználók kiépítési és visszaírási alkalmazásai mostantól támogatják a munkanap Web Services API legújabb verzióit

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

**A termék képességei:** 

Az ügyfelek visszajelzései alapján már frissítettük a munkanapon bejövő felhasználók kiépítési és visszaírási alkalmazásait a vállalati alkalmazás-katalógusban, hogy támogassák a munkanap webszolgáltatások (WWS) API legújabb verzióit. Ezzel a módosítással az ügyfelek megadhatják azt a WWS API-verziót, amelyet a kapcsolódási sztringben használni kívánnak. Ez lehetővé teszi az ügyfeleknek, hogy több HR-attribútumot is beolvassák a munkanap kiadásaiban. A munkanap visszaírási alkalmazás mostantól az ajánlott Change_Work_Contact_Info munkanap webszolgáltatást használja a Maintain_Contact_Info korlátainak leküzdéséhez.

Ha nem ad meg verziót a kapcsolódási karakterláncban, alapértelmezés szerint a (z) WWS v 21.1 a beérkező felhasználók üzembe helyezéséhez szükséges legfrissebb munkanapokra vált, a felhasználóknak frissíteniük kell a kapcsolódási karakterláncot az [oktatóanyagban](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) leírtak szerint, és frissíteniük kell a munkanapokhoz használt XPath-értékeket a [munkanap attribútumokra vonatkozó útmutatóban](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)leírtak szerint. 

Az új API-k visszaírási való használatához nincs szükség módosításra a munkanap visszaírási kiépítési alkalmazásban. A munkanap oldalon ellenőrizze, hogy a munkanap-integrációs rendszer felhasználói (ISU) fiókjának van-e engedélye a Change_Work_Contact üzleti folyamat meghívására az oktatóanyag szakaszban leírt módon, az [üzleti folyamat biztonsági házirendjének engedélyeinek konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Frissítettük az [oktatóanyagra vonatkozó útmutatót](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) , amely az új API-verzió támogatását tükrözi.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók mostantól a kiépítés hatókörében vannak

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

A **termék képességei:** Identitás-életciklus kezelése

Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörén kívül vannak. Visszajelzésünk szerint az ügyfelek a kiépítés hatókörében szeretnék, hogy az adott szerepkörrel rendelkező felhasználók felhasználják a felhasználókat. 2020. április 16-ától az összes új létesítési konfiguráció lehetővé teszi, hogy a felhasználók az alapértelmezett hozzáférési szerepkörrel legyenek kiépítve. Fokozatosan megváltoztatjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassák a felhasználók ezzel a szerepkörrel való üzembe helyezését. [További információ.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Kiépítési felhasználói felület frissítése

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

A **termék képességei:** Identitás-életciklus kezelése

Frissítettük a kiépítési tapasztalatot, hogy egy koncentráltabb felügyeleti nézetet hozzon létre. Ha egy már konfigurált vállalati alkalmazás kiépítési paneljére navigál, könnyedén nyomon követheti a kiépítés előrehaladását, és kezelheti az olyan műveleteket, mint például az indítás, a leállítás és a kiépítés újraindítása. [További információ.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A dinamikus csoport szabályának érvényesítése már elérhető a nyilvános előzetes verzióban

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Csoport kezelése

A **termék képességei:** Együttműködés

A Azure Active Directory (Azure AD) mostantól lehetővé teszi a dinamikus csoportok szabályainak érvényesítését. A **szabályok érvényesítése** lapon ellenőrizheti, hogy a dinamikus szabály a minta csoport tagjaira vonatkozik-e, hogy erősítse meg, hogy a szabály a várt módon működik-e. Dinamikus csoportokra vonatkozó szabályok létrehozásakor vagy frissítésekor a rendszergazdák tudni szeretnék, hogy egy felhasználó vagy egy eszköz tagja lesz-e a csoportnak. Ez segít kiértékelni, hogy egy felhasználó vagy eszköz teljesíti-e a szabályok feltételeit és a hibaelhárítás során a tagság nem várt időpontját.

További információkért lásd: [dinamikus csoporttagság-szabály (előzetes verzió) ellenőrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Az identitás biztonságos pontszáma – a biztonsági alapértékek és az MFA-javító művelet frissítései

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** N/A

A **termék képességei:** Identitás-biztonsági & védelme

**Az Azure ad-javító műveletek biztonsági alapértelmezett értékeinek támogatása:** A Microsoft biztonságos pontszáma frissíti a [biztonsági beállítások Azure ad-ben](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)történő támogatásának javítását, amely megkönnyíti a szervezet számára a gyakori támadások előre konfigurált biztonsági beállításainak védelmét. Ez hatással lesz a következő fejlesztési műveletekre:

- Győződjön meg arról, hogy minden felhasználó elvégezheti a többtényezős hitelesítést a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz
- Az örökölt hitelesítés blokkolására szolgáló házirend engedélyezése
 
**MFA-javító művelet frissítései:** Annak érdekében, hogy a vállalatok a vállalatnál működő házirendek alkalmazása során biztosítsák a legnagyobb biztonságot, a Microsoft biztonságos pontszáma három, a többtényezős hitelesítés körébe tartozó fejlesztési műveletet eltávolítottan, és kettőt vett fel.

A javítási műveletek el lettek távolítva:

- Az összes felhasználó regisztrálása a többtényezős hitelesítéshez
- MFA megkövetelése minden felhasználótól
- MFA megkövetelése Azure AD-beli Kiemelt szerepkörökhöz

További fejlesztési műveletek:

- Győződjön meg arról, hogy minden felhasználó elvégezheti a többtényezős hitelesítést a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz

Ezeknek az új fejlesztési műveleteknek a használatához regisztrálnia kell a felhasználókat vagy a rendszergazdákat a multi-Factor Authentication (MFA) szolgáltatásban a címtárban, és meg kell határoznia a szervezeti igényeihez illeszkedő megfelelő szabályzatokat. A fő cél az, hogy rugalmasságot biztosítson, miközben a felhasználók és a rendszergazdák több tényezővel vagy kockázatalapú identitás-ellenőrzési kérésekkel is hitelesíthetők. Ez olyan formában is megteheti a formáját, amely hatókörön belüli döntéseket alkalmaz, vagy a biztonsági alapértékeket (március 16.) állítja be, amelyek lehetővé teszik a Microsoft számára, hogy mikor kell megkérdőjelezni a felhasználókat az MFA-ban. [Tudjon meg többet a Microsoft biztonságos pontszám újdonságait ismertető cikkről](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>2020. március

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nem felügyelt Azure Active Directory fiók a B2B-frissítésben március 2021

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
**2021. március 31-ig**a Microsoft többé nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure Active Directory (Azure ad) fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. Ennek előkészítéseként javasoljuk, hogy az [egyszeri jelszavas hitelesítéssel küldje](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)el az e-maileket.

---

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

Örömmel vesszük, hogy most már bevezetjük a frissített [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs)   élményt a [Microsoft Azure Government portálon](https://portal.azure.us/). További információt a [bejelentési blogbejegyzésben](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)talál.

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

Annak érdekében, hogy az ügyfelek rugalmasabban hozzanak létre olyan, címtárra kiterjedő csoportokat, amelyek megfelelnek az igényeiknek **, a csoport** **Groups**  >  **általános** beállításai közül a Azure Portal, a [dinamikus csoportokra vonatkozó dokumentációra](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)mutató hivatkozást váltottunk ki. Továbbfejlesztettük a dokumentációt, hogy több útmutatást is tartalmazzon, így a rendszergazdák létrehozhatnak olyan összes felhasználói csoportot, amely tartalmazza vagy kizárják a vendég felhasználókat.

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
 
Az új fiókom végfelhasználói élménye a következő hónapban frissülni fog az URL-címével `https://myaccount.microsoft.com` . További információk a felhasználói élményről és az összes olyan fiókról, amely a [saját fiók portál súgójában](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)elérhető végfelhasználók számára biztosít segítséget.

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
 
A munkaterületek, a szűrők rendszergazdák konfigurálhatják a felhasználók alkalmazásai rendszerezését, mostantól gyűjteményekként lesznek hivatkozva. További információ arról, hogyan konfigurálhatja őket a [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)című témakörben.

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

A cookie-k számára a biztonságos alapértelmezett modell részeként a Chrome 80 böngésző megváltoztatja a cookie-k attribútum nélküli kezelésének módját `SameSite` . Minden olyan cookie, amely nem adja meg az `SameSite` attribútumot, a rendszer úgy kezeli, mintha a értékre lett állítva `SameSite=Lax` , ami azt eredményezi, hogy a Chrome blokkolja a több tartományon belüli cookie-megosztási forgatókönyveket, amelyektől az alkalmazás függ. A korábbi Chrome-viselkedés fenntartása érdekében használhatja az `SameSite=None` attribútumot, és hozzáadhat egy további `Secure` attribútumot, így a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a módosítás befejezését ütemezi a 2020. február 4-én.

Azt javasoljuk, hogy a fejlesztők az alábbi útmutató segítségével tesztelik alkalmazásaikat:

- Állítsa az alapértelmezett értéket a **biztonságos cookie használata** beállításnál az **Igen**értékre.

- Állítsa a **SameSite** attribútum alapértelmezett értékét **none**értékre.

- Adjon hozzá egy további `SameSite` **biztonságos**attribútumot.

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
