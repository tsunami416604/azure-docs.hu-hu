---
title: Automatikus felhasználói üzembe helyezés tervezése az Azure Active Directoryhoz
description: Útmutató az automatikus felhasználói kiépítés tervezéséhez és végrehajtásához
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522410"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Automatikus felhasználóátadást használó üzembe helyezés tervezése

Számos szervezet támaszkodik a szoftver, mint szolgáltatás (SaaS) alkalmazások, például servicenow, Zscaler és a Tartalékidő a végfelhasználói hatékonyság érdekében. A korábbi informatikai munkatársak olyan manuális kiépítési módszerekre támaszkodtak, mint például a CSV-fájlok feltöltése, vagy az egyéni parancsfájlok használata a felhasználói identitások biztonságos kezeléséhez az egyes SaaS-alkalmazásokban. Ezek a folyamatok hibalehetőségek, nem biztonságosak és nehezen kezelhetők.

Az Azure Active Directory (Azure AD) automatikus felhasználói üzembe készítése leegyszerűsíti ezt a folyamatot azáltal, hogy biztonságosan automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását a SaaS-alkalmazásokban az üzleti szabályok alapján. Ez az automatizálás lehetővé teszi, hogy hatékonyan méretezhesse az identitáskezelő rendszereket mind a felhőalapú, mind a hibrid környezetekben, miközben bővíti a felhőalapú megoldásoktól való függőségüket.

A funkciók jobb megértése [érdekében olvassa el a felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval történő](../app-provisioning/user-provisioning.md) üzembeépítés automatizálása című témakört.

## <a name="learn"></a>Tanulás

A felhasználói kiépítés megteremti a folyamatos identitásszabályozás alapjait, és javítja a mérvadó identitásadatokon alapuló üzleti folyamatok minőségét.

### <a name="key-benefits"></a>Főbb előnyök

Az automatikus felhasználói kiépítés engedélyezésének legfontosabb előnyei a következők:

* **Nagyobb termelékenység**. A felhasználói identitások kezelése saas-alkalmazások egyetlen felhasználó létesítési felügyeleti felületen kezelhetők. Ez a felület egyetlen létesítési házirend-készlettel rendelkezik.

* **A kockázatok kezelése**. Növelheti a biztonságot, ha automatizálja a módosításokat az alkalmazottak állapota vagy a szerepköröket és/vagy a hozzáférést meghatározó csoporttagságok alapján.

* **A megfelelőség és az irányítás kezelése**. Az Azure AD támogatja a natív naplózási naplók minden felhasználói kiépítési kérelem. A kérelmek végrehajtása a forrás- és a célrendszerekben is megtörténik. Ez lehetővé teszi, hogy egyetlen képernyőről nyomon kövesse, hogy ki férhet hozzá az alkalmazásokhoz.

* **Csökkentse a költségeket**. Az automatikus felhasználói kiépítés csökkenti a költségeket azáltal, hogy elkerüli a hatékonysági hiányosságokat és a manuális kiépítéshez kapcsolódó emberi hibákat. Csökkenti az egyéni fejlesztésű felhasználói kiépítési megoldások, parancsfájlok és naplónaplók szükségességét.

### <a name="licensing"></a>Licencek

Az Azure AD az alkalmazásgaléria menüjében biztosított sablonok használatával biztosítja az alkalmazások önkiszolgáló integrációját. A licenckövetelmények teljes listáját az [Azure AD licencelési lapján találja.](https://azure.microsoft.com/pricing/details/active-directory/)

#### <a name="application-licensing"></a>Alkalmazás licencelése

Szüksége lesz a megfelelő licencekkel az automatikusan kiépíteni kívánt alkalmazás(ok)hoz. Beszélje meg az alkalmazás tulajdonosaival, hogy az alkalmazáshoz rendelt felhasználók rendelkeznek-e az alkalmazásszerepkörökhöz tartozó megfelelő licencekkel. Ha az Azure AD szerepkörök alapján kezeli az automatikus kiépítést, az Azure AD-ben hozzárendelt szerepköröknek igazodniuk kell az alkalmazáslicencekhez. Az alkalmazás nem megfelelő licencei hibákat okozhatnak a felhasználó kiépítése/frissítése során.

### <a name="terms"></a>Fogalmak

Ez a cikk a következő kifejezéseket használja:

* CRUD-műveletek – Felhasználói fiókokon végrehajtott műveletek: Létrehozás, Olvasás, Frissítés, Törlés.

* Egyszeri bejelentkezés (SSO) – Az a képesség, hogy a felhasználó egyszeri bejelentkezést és az összes egyszeri bejelentkezést engedélyezett alkalmazás eléréséhez. A felhasználói kiépítés környezetében egyszeri bejelentkezés eredménye, hogy a felhasználók egyetlen fiókkal rendelkeznek az automatikus felhasználói kiépítést használó összes rendszer eléréséhez.

* Forrásrendszer – A felhasználók tárháza, amelyből az Azure AD-ben kitér. Az Azure AD a legtöbb előre integrált létesítési összekötő forrásrendszere. Vannak azonban kivételek a felhőalapú alkalmazások, például az SAP, a Workday és az AWS. Például lásd: [Felhasználó kiépítése a Workday-től az AD-ig.](../saas-apps/workday-inbound-tutorial.md)

* Célrendszer – A felhasználók tárháza, amely az Azure AD rendelkezéseket. A célrendszer általában egy SaaS-alkalmazás, például ServiceNow, Zscaler és a Tartalékidő. A célrendszer lehet egy helyszíni rendszer is, például az AD.

* [Tartományok közötti identitáskezelés (SCIM)](https://aka.ms/scimoverview) – egy nyílt szabvány, amely lehetővé teszi a felhasználói kiépítés automatizálását. Az SCIM felhasználói identitásadatokat közöl az identitásszolgáltatók, például a Microsoft és a szolgáltatók, például a Salesforce vagy más, felhasználói identitásadatokat igénylő SaaS-alkalmazások között.

### <a name="training-resources"></a>Képzési források

| Források| Hivatkozás és leírás |
| - | - |
| Igény szerinti webináriumok| [Nagyvállalati alkalmazásai kezelése az Azure AD-vel](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Ismerje meg, hogy az Azure AD hogyan segíthet az SSO elérésében a vállalati SaaS-alkalmazások számára, és gyakorlati tanácsok a hozzáférés szabályozásához. |
| Videók| [Mi a felhasználói kiépítés az Active Azure Directoryban?](https://youtu.be/_ZjARPpI6NI) <br> [Hogyan telepítheti a felhasználói kiépítést az Active Azure Directoryban?](https://youtu.be/pKzyts6kfrw) <br> [A Salesforce integrálása az Azure AD-vel: A felhasználói kiépítés automatizálása](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Online tanfolyamok| SkillUp Online: [Identitások kezelése](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Ismerje meg, hogyan integrálhatja az Azure AD-t számos SaaS-alkalmazással, és hogyan biztosíthatja a felhasználói hozzáférést ezekhez az alkalmazásokhoz. |
| Könyvek| [Modern hitelesítés az Azure Active Directory webes alkalmazásokhoz (Developer Reference) 1st Edition szolgáltatással.](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)  <br> Ez egy mérvadó, részletes útmutató az Active Directory hitelesítési megoldásainak létrehozásához ezekhez az új környezetekhez. |
| Oktatóanyagok| Tekintse meg [az SaaS-alkalmazások Azure AD-vel való integrálásáról szóló oktatóanyagok listáját.](../saas-apps/tutorial-list.md) |
| GYIK| Gyakori kérdések az automatikus felhasználói kiépítéssel [kapcsolatban](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>Megoldásarchitektúrák

Az Azure AD-kiépítési szolgáltatás a felhasználók számára a SaaS-alkalmazások és más rendszerek csatlakoztatásával az egyes alkalmazások szállítói által biztosított felhasználói felügyeleti API-végpontok. Ezek a felhasználói felügyeleti API-végpontok lehetővé teszik az Azure AD számára a felhasználók programozott létrehozását, frissítését és eltávolítását.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatikus felhasználói kiépítés hibrid vállalatok számára

Ebben a példában a felhasználók és vagy csoportok egy helyszíni címtárhoz csatlakoztatott HR-adatbázisban jönnek létre. Az Azure AD-kiépítési szolgáltatás kezeli az automatikus felhasználói kiépítés a cél SaaS-alkalmazások.

 ![felhasználói kiépítés](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**A munkafolyamat leírása:**

1. A felhasználók/csoportok egy helyszíni HR-alkalmazásban/rendszerben, például az SAP-ban jönnek létre. 

1. **Az Azure AD Connect-ügynök** futtatja az identitások (felhasználók és csoportok) ütemezett szinkronizálását a helyi AD-ről az Azure AD-re.

1. **Az Azure AD-kiépítési szolgáltatás** megkezdi a [kezdeti ciklust](../app-provisioning/user-provisioning.md) a forrásrendszer és a célrendszer ellen. 

1. **Az Azure AD-kiépítési szolgáltatás** lekérdezi a forrásrendszert a kezdeti ciklus óta megváltozott felhasználók és csoportok számára, és leküldéses [módosításokat a növekményes ciklusokban.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatikus felhasználói kiépítés csak felhőalapú vállalatok számára

Ebben a példában a felhasználó létrehozása az Azure AD-ben történik, és az Azure AD-kiépítési szolgáltatás kezeli az automatikus felhasználói kiépítés a cél (SaaS) alkalmazások.

![2. kép](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**A munkafolyamat leírása:**

1. A felhasználók/csoportok az Azure AD-ben jönnek létre.

1. **Az Azure AD-kiépítési szolgáltatás** megkezdi a [kezdeti ciklust](../app-provisioning/user-provisioning.md) a forrásrendszer és a célrendszer ellen. 

1. **Az Azure AD-kiépítési szolgáltatás** lekérdezi a forrásrendszert a kezdeti ciklus óta frissített felhasználók és csoportok számára, és végrehajtja a [növekményes ciklusokat.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatikus felhasználói kiépítés felhőalapú HR-alkalmazásokhoz 

Ebben a példában a felhasználók és vagy csoportok egy felhőhr-alkalmazásban jönnek létre, például a Workday és a SuccessFactors. The Azure AD provisioning service and Azure AD Connect provisioning agent provisions the user data from the cloud HR app tenant into AD. Miután a fiókok frissítése az AD-ben, az Azure AD-vel az Azure AD Connecten keresztül szinkronizálódik, és az e-mail címek és a felhasználónév attribútumok visszaírhatók a felhőbeli HR-alkalmazás bérlői.

![2. kép](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **A HR-csapat** a tranzakciókat a felhőbeli HR-alkalmazás bérlőjében hajtja végre.
2.  **Az Azure AD-kiépítési szolgáltatás** futtatja az ütemezett ciklusokat a felhőbeli HR-alkalmazás bérlőjéből, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni az AD-vel való szinkronizáláshoz.
3.  **Az Azure AD-kiépítési szolgáltatás** meghívja az Azure AD Connect kiépítési ügynök egy kérelem hasznos, amely tartalmazza az AD-fiók létrehozása/frissítése/engedélyezése/letiltása műveleteket.
4.  **Az Azure AD Connect kiépítési ügynök** egy szolgáltatásfiók használatával kezeli az AD-fiók adatait.
5.  **Az Azure AD Connect** a különbözeti szinkronizálást futtatja a frissítések lekérése az AD-ben.
6.  **Az AD-frissítések** szinkronizálva vannak az Azure AD-vel. 
7.  **Az Azure AD-kiépítési szolgáltatás** visszaírja az e-mail attribútumot és a felhasználónevet az Azure AD-től a felhőbeli HR-alkalmazás bérlőjéhez.

## <a name="plan-the-deployment-project"></a>A telepítési projekt megtervezése

Vegye figyelembe, hogy a szervezeti igények et kell meghatározni a stratégia üzembe helyezéséhez felhasználói kiépítés a környezetben.

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek sikertelenek, az általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt van. A buktatókat elkerülve [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be,](https://aka.ms/deploymentplans) és hogy az érdekelt felek szerepei jól érthetőek legyenek az érdekelt felek, valamint a projekt bemenetének és elszámoltathatóságának dokumentálásával.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon kommunikáljon a felhasználókkal arról, hogy a felhasználói élményük hogyan változik, mikor változik meg, és hogyan szerezhet támogatást, ha problémákat tapasztalnak.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Azt javasoljuk, hogy az automatikus felhasználói kiépítés kezdeti konfigurációja egy tesztkörnyezetben legyen a felhasználók egy kis részhalmazával, mielőtt az éles környezetben lévő összes felhasználóra skálázná. Tekintse meg a kísérleti műveletek gyakorlati [tanácsait.](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)

#### <a name="best-practices-for-a-pilot"></a>Legjobb gyakorlatok egy kísérleti  

A próbaverzió lehetővé teszi, hogy tesztelje egy kis csoport telepítése előtt egy képesség mindenki számára. Győződjön meg arról, hogy a tesztelés részeként a szervezeten belüli minden egyes használati esetet alaposan tesztel.

Az első hullámban célozza meg az informatikai, a használhatósági és más megfelelő felhasználókat, akik tesztelhetik és visszajelzést adhatnak. Ezzel a visszajelzéssel tovább fejlesztheti a felhasználóknak küldött kommunikációt és utasításokat, és betekintést nyerhet abba, hogy a támogatási munkatársak milyen típusú problémákat láthatnak.

A felhasználók nagyobb csoportjaira való kiterjesztés a megcélzott csoport(ok) hatókörének növelésével. Ez történhet [dinamikus csoporttagsággal,](../users-groups-roles/groups-dynamic-membership.md)vagy a felhasználók manuális hozzáadásával a megcélzott csoport(ok)hoz.

## <a name="plan-application-connections-and-administration"></a>Az alkalmazáskapcsolatok és -felügyelet megtervezése

Az Azure AD-portál használatával megtekintheti és kezelheti az összes olyan alkalmazást, amely támogatja a kiépítést. Lásd: [Alkalmazások keresése a portálon.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

### <a name="determine-the-type-of-connector-to-use"></a>A használandó összekötő típusának meghatározása

Az automatikus kiépítés engedélyezéséhez és konfigurálásához szükséges tényleges lépések az alkalmazástól függően változnak. Ha az alkalmazás, amelyet automatikusan kiépíteni kíván, szerepel az [Azure AD SaaS alkalmazáskatalógusban,](../saas-apps/tutorial-list.md)akkor válassza ki az [alkalmazásspecifikus integrációs oktatóanyagot](../saas-apps/tutorial-list.md) az előre integrált felhasználói kiépítési összekötő konfigurálásához.

Ha nem, kövesse az alábbi lépéseket:

1. [Hozzon létre egy kérelmet](../develop/howto-app-gallery-listing.md) egy előre integrált felhasználói kiépítési összekötő. Csapatunk együttműködik Önnel és az alkalmazás fejlesztőjével, hogy beszálljon az alkalmazásunkba a platformunkra, ha támogatja az SCIM-et.

1. Használja a [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) általános felhasználói kiépítési támogatást az alkalmazáshoz. Ez a követelmény az Azure AD-nek, hogy a felhasználók az alkalmazásba egy előre integrált létesítési összekötő nélkül.

1. Ha az alkalmazás képes kihasználni a BYOA SCIM-összekötőt, akkor a [BYOA SCIM-integrációs oktatóanyagban](../app-provisioning/use-scim-to-provision-users-and-groups.md) konfigurálhatja a BYOA SCIM-összekötőt az alkalmazáshoz.

További információ: [Milyen alkalmazásokat és rendszereket használhatok az Azure AD automatikus felhasználói kiépítéssel?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Adatok gyűjtése az alkalmazáshoz való hozzáférés engedélyezéséhez

Az automatikus felhasználói kiépítés beállítása alkalmazásonkénti folyamat. Minden alkalmazáshoz [rendszergazdai hitelesítő adatokat](../app-provisioning/configure-automatic-user-provisioning-portal.md) kell megadnia a célrendszer felhasználói kezelési végpontjához való csatlakozáshoz.

Az alábbi képen a szükséges rendszergazdai hitelesítő adatok egy verziója látható:

![A felhasználói fiókok kiépítési beállításainak kezeléséhez szolgáló kiépítési képernyő](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Míg egyes alkalmazások rendszergazdai felhasználónevet és jelszót igényelnek, másoknak tulajdonosi jogkivonatra lehet szükségük.

## <a name="plan-user-and-group-provisioning"></a>Felhasználó- és csoportkiépítés megtervezése

Ha engedélyezi a felhasználói kiépítést a vállalati alkalmazásokhoz, az [Azure Portal](https://portal.azure.com/) az attribútumértékekattribútum-leképezésen keresztül szabályozza az attribútumértékeit.

### <a name="determine-operations-for-each-saas-app"></a>Az egyes SaaS-alkalmazások műveleteinek meghatározása

Minden alkalmazás rendelkezhet egyedi felhasználói vagy csoportattribútumokkal, amelyeket le kell képeznie az Azure AD attribútumaihoz. Előfordulhat, hogy az alkalmazás csak a CRUD-műveletek egy részhalmazával rendelkezik.

Minden kérelemre vonatkozóan a következő információkat kell dokumentálni:

* CRUD létesítési műveleteket kell végrehajtani a felhasználó és vagy a csoport objektumok a célrendszerek. Például minden SaaS-alkalmazás tulajdonosa nem szeretné az összes lehetséges műveletet.

* A forrásrendszerben elérhető attribútumok

* A célrendszerben elérhető attribútumok

* Attribútumok leképezése rendszerek között.

### <a name="choose-which-users-and-groups-to-provision"></a>A kiépítandó felhasználók és csoportok kiválasztása

Az automatikus felhasználói kiépítés megvalósítása előtt meg kell határoznia az alkalmazásba kiépítendő felhasználókat és csoportokat.

* [A hatókörszűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) használatával attribútumalapú szabályokat határozhat meg, amelyek meghatározzák, hogy mely felhasználók vannak kiépítve egy alkalmazásba.

* Ezután szükség szerint használja [a felhasználói és csoporthozzárendeléseket](../manage-apps/assign-user-or-group-access-portal.md) a további szűréshez.

### <a name="define-user-and-group-attribute-mapping"></a>Felhasználói és csoportattribútum-hozzárendelés definiálása

Az automatikus felhasználói kiépítés megvalósításához meg kell határoznia az alkalmazáshoz szükséges felhasználói és csoportattribútumokat. Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között az attribútumok és [attribútumleképezések](../app-provisioning/configure-automatic-user-provisioning-portal.md) előre konfigurált készlete található. Nem minden SaaS-alkalmazás engedélyezi a csoportattribútumokat.

Az Azure AD támogatja a közvetlen attribútum-attribútum leképezés, állandó értékek megadása, vagy [kifejezések írása attribútum leképezések.](../app-provisioning/functions-for-customizing-application-data.md) Ez a rugalmasság lehetővé teszi, hogy finoman szabályozhatja, hogy mi lesz feltöltve a megcélzott rendszer attribútumában. A [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) és a Graph Explorer segítségével exportálhatja a felhasználói kiépítési attribútum-hozzárendeléseket és sémát egy JSON-fájlba, és importálhatja azt az Azure AD-be.

További információ: [A felhasználói kiépítési attribútumok testreszabása saas-alkalmazásokhoz az Azure Active Directoryban című témakörben található.](../app-provisioning/customize-application-attributes.md)

### <a name="special-considerations-for-user-provisioning"></a>A felhasználók kiépítésével kapcsolatos különleges szempontok

A telepítés utáni problémák csökkentéséhez vegye figyelembe az alábbiakat:

* Győződjön meg arról, hogy a felhasználói/csoport objektumok forrás- és célalkalmazások közötti leképezéséhez használt attribútumok rugalmasak. Nem okozhatnak felhasználókat/csoportokat helytelenül, ha az attribútumok megváltoznak (például egy felhasználó a vállalat egy másik részére költözik).

* Alkalmazások lehetnek speciális korlátozások és/vagy követelmények, amelyeknek meg kell felelnia a felhasználói kiépítés megfelelő működéséhez. A Tartalékidő például csonkolja bizonyos attribútumok értékeit. Tekintse meg az egyes alkalmazásokra jellemző [automatikus felhasználói kiépítési oktatóanyagokat.](../saas-apps/tutorial-list.md)

* A séma konzisztenciájának ellenőrzése a forrás- és a célrendszerek között. A gyakori problémák közé tartoznak az olyan attribútumok, mint az UPN vagy a leveleket nem egyező. Például az Azure AD-ben *john_smith@contoso.com* az upn-készlet, *jsmith@contoso.com*az alkalmazásban és az alkalmazásban, ez. További információ: A [felhasználó és a csoport sémájának hivatkozása](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Terv tesztelése és biztonsága

A központi telepítés minden egyes szakaszában győződjön meg arról, hogy az eredmények a várt módon, és a kiépítési ciklusok naplózása.

### <a name="plan-testing"></a>Terv tesztelése

Miután konfigurálta az automatikus felhasználói kiépítést az alkalmazáshoz, teszteseteket futtat, hogy ellenőrizze, hogy ez a megoldás megfelel-e a szervezet követelményeinek.

| Forgatókönyvek| Várt eredmények |
| - | - |
| A felhasználó hozzáadódik a célrendszerhez rendelt csoporthoz | A felhasználói objektum ki van építve a célrendszerben. <br>A felhasználó bejelentkezhet a célrendszerbe, és végrehajthatja a kívánt műveleteket. |
| A felhasználó törlődik a célrendszerhez rendelt csoportból | A felhasználói objektum kivan építve a célrendszerben.<br>A felhasználó nem tud bejelentkezni a célrendszerbe. |
| A felhasználói adatok bármely módszerrel frissülnek az Azure AD-ben | A frissített felhasználói attribútumok egy növekményes ciklus után jelennek meg a célrendszerben |
| A felhasználó hatókörön kívül van | A felhasználói objektum le van tiltva vagy törlődik. <br>Megjegyzés: Ez a viselkedés felülbírálva van a [Workday kiépítéskor.](skip-out-of-scope-deletions.md) |

### <a name="plan-security"></a>A biztonság megtervezése

Gyakori, hogy egy biztonsági felülvizsgálatot kell szükség egy központi telepítés részeként. Ha biztonsági felülvizsgálatra van szüksége, tekintse meg a számos Azure [AD-tanulmányok,](https://www.microsoft.com/download/details.aspx?id=36391) amely áttekintést nyújt az identitás szolgáltatásként.

### <a name="plan-rollback"></a>Terv visszaállítása

Ha az automatikus felhasználói létesítési megvalósítás nem működik megfelelően az éles környezetben, az alábbi visszaállítási lépések segíthetnek a korábbi ismert jó állapotba való visszatérésben:

1. Tekintse át a [kiépítési összefoglaló jelentést](../app-provisioning/check-status-user-account-provisioning.md) és [a kiépítési naplókat,](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) hogy megállapíthassa, milyen helytelen műveletek történtek az érintett felhasználókon és/vagy csoportokon.

1. A kiépítési naplózási naplók segítségével határozza meg az érintett felhasználók és/vagy csoportok utolsó ismert helyes állapotát. Tekintse át a forrásrendszereket is (Azure AD vagy AD).

1. Az alkalmazás tulajdonosával együttműködve frissítse az alkalmazásban közvetlenül érintett felhasználókat és/vagy csoportokat az utolsó ismert helyes állapotértékek használatával.

## <a name="deploy-automatic-user-provisioning-service"></a>Automatikus felhasználói kiépítési szolgáltatás telepítése

Válassza ki a megoldáskövetelményeinek megfelelő lépéseket.

### <a name="prepare-for-the-initial-cycle"></a>Felkészülés a kezdeti ciklusra

Amikor az Azure AD-kiépítési szolgáltatás fut az első alkalommal, a kezdeti ciklus a forrásrendszer és a célrendszerek létrehoz egy pillanatképet az összes felhasználói objektumok minden célrendszer.

Ha engedélyezi az automatikus kiépítés egy alkalmazás, a kezdeti ciklus is eltarthat bárhol 20 perc től több óráig. Az időtartam az Azure AD-címtár méretétől és a kiépítési hatókörben lévő felhasználók számától függ. Lásd: [Hogyan javíthatja a kiépítési teljesítményt.](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

A létesítési szolgáltatás tárolja a két rendszer állapotát a kezdeti ciklus után, a következő növekményes ciklusok teljesítményének javítása.

### <a name="configure-automatic-user-provisioning"></a>A felhasználók automatikus átadásának konfigurálása

Az [Azure Portal](https://portal.azure.com/) használatával kezelheti az automatikus felhasználói fiók kiépítése és a kiépítés kivonása az alkalmazások, amelyek támogatják azt. Kövesse a [Hogyan állíthatom be az automatikus kiépítést egy alkalmazásba című](../app-provisioning/user-provisioning.md) részben leírt lépéseket?

Az Azure AD felhasználói létesítési szolgáltatás a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)használatával is konfigurálható és kezelhető.

## <a name="manage-automatic-user-provisioning"></a>Automatikus felhasználói kiépítés kezelése

Most, hogy üzembe helyezte, kezelnie kell a megoldást.

### <a name="monitor-user-provisioning-operation-health"></a>A felhasználó létesítési művelet állapotának figyelése

A sikeres [kezdeti ciklus](../app-provisioning/user-provisioning.md)után az Azure AD-kiépítési szolgáltatás az egyes alkalmazásokra jellemző időközönként növekményes frissítéseket futtat, amíg az alábbi események valamelyike be nem következik:

* A szolgáltatás manuálisan leáll, és egy új kezdeti ciklus az [Azure Portalon,](https://portal.azure.com/)vagy a megfelelő [Microsoft Graph API-parancs](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) használatával aktiválódik.

* Az új kezdeti ciklust az attribútumleképezések vagy a hatókörszűrők változása váltja ki.

* A létesítési folyamat a magas hibaarány miatt karanténba kerül, és több mint négy hétig karanténban marad, amikor automatikusan le lesz tiltva.

Tekintse át ezeket az eseményeket, és a kiépítési szolgáltatás által végzett összes egyéb tevékenységet tekintse meg az Azure AD [létesítési naplók.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)

A kiépítési ciklusok kiépítési folyamatának figyeléséhez [ellenőrizheti a felhasználói kiépítés állapotát.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

### <a name="gain-insights-from-reports"></a>Betekintést nyerhet a jelentésekből

Az Azure AD [további betekintést](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) nyújthat a szervezet felhasználói kiépítési használatába és működési állapotába a naplók és jelentések segítségével.

A rendszergazdáknak ellenőrizniük kell a kiépítési összefoglaló jelentést a kiépítési feladat működési állapotának figyeléséhez. A kiépítési szolgáltatás által végrehajtott összes tevékenység et az Azure AD naplózási naplói rögzítik. Lásd: [Oktatóanyag: Jelentés a felhasználói fiókok automatikus kiépítéséről.](../app-provisioning/check-status-user-account-provisioning.md)

Azt javasoljuk, hogy vállalja a felelősséget, és használja ezeket a jelentéseket a szervezet követelményeinek megfelelő ütemben. Az Azure AD 30 napig őrzi meg a legtöbb naplózási adatot.

### <a name="troubleshoot"></a>Hibaelhárítás

Az alábbi hivatkozásokon elháríthatja az esetlegesen felmerülő problémákat a kiépítés során:

* [Probléma a felhasználók Azure AD Gallery-alkalmazásba való kiépítésének konfigurálásakor](../app-provisioning/application-provisioning-config-problem.md)

* [Attribútum szinkronizálása a helyszíni Active Directoryból az Azure AD-hez egy alkalmazásba való kiépítéshez](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Az Azure AD Gallery-alkalmazásba való felhasználói kiépítés órákat vagy többet vesz igénybe](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Probléma a rendszergazdai hitelesítő adatok mentése az Azure Active Directory-csoportalkalmazásba való felhasználói kiépítés konfigurálása során](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazáshoz](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [A felhasználók helytelen készlete van kiépítve egy Azure AD Gallery-alkalmazáshoz](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Hasznos dokumentáció

* [Kifejezések írása attribútumleképezéshez](../app-provisioning/functions-for-customizing-application-data.md)

* [Az Azure AD szinkronizálási API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [A hatókörön kívül eső felhasználói fiókok törlésének kihagyása](skip-out-of-scope-deletions.md)

* [Azure AD Connect kiépítési ügynök: verziókiadási előzmények](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Források

* [Termékkel kapcsolatos visszajelzés küldése](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Az Azure AD újdonságairól naprakészen tartható](https://azure.microsoft.com/updates/?product=active-directory)

* [Az Azure AD-fórum túlcsordulása](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>További lépések
* [Automatikus felhasználói kiépítés konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [A kiépítési konfiguráció exportálása vagy importálása a Microsoft Graph API használatával](../app-provisioning/export-import-provisioning-configuration.md)

* [Kifejezések írása attribútumleképezéshez az Azure Active directoryban](../app-provisioning/functions-for-customizing-application-data.md)
