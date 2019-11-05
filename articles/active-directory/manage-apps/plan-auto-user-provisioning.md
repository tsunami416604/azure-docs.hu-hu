---
title: A felhasználók automatikus üzembe helyezésének megtervezése a Azure Active Directory
description: Útmutató az automatikus felhasználó-kiépítés tervezéséhez és végrehajtásához
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d1aec836f66ae2ebc007e920cf6ef8a4450919
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473342"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Automatikus felhasználóátadást használó üzembe helyezés tervezése

Számos szervezet olyan szoftveres (SaaS) alkalmazásokra támaszkodik, mint a ServiceNow, a Zscaler és a Slack a végfelhasználói hatékonyság érdekében. A hagyományos informatikai munkatársak olyan manuális üzembe helyezési módszerekre támaszkodtak, mint például a CSV-fájlok feltöltése vagy egyéni parancsfájlok használata a felhasználói identitások biztonságos kezelésére az egyes SaaS-alkalmazásokban. Ezek a folyamatok a hibák, a nem biztonságos és a nehezen kezelhetők.

A Azure Active Directory (Azure AD) automatikus felhasználó-kiépítés leegyszerűsíti ezt a folyamatot azáltal, hogy az üzleti szabályok alapján biztonságosan automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását az SaaS-alkalmazásokban. Ez az automatizálás lehetővé teszi, hogy hatékonyan méretezze az Identitáskezelés rendszereit mind a felhőalapú, mind a hibrid környezetekben, miközben kibővíti a felhőalapú megoldásoktól való függőségét.

A funkciók jobb megismeréséhez lásd: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) segítségével.

## <a name="learn"></a>Oktatóanyagok

A felhasználók üzembe helyezése létrehoz egy alapot a folyamatos identitás-irányításhoz, és javítja a mérvadó személyazonossági adatokra támaszkodó üzleti folyamatok minőségét.

### <a name="key-benefits"></a>Főbb előnyök

Az automatikus felhasználó-kiépítés engedélyezésének fő előnyei a következők:

* **Megnövekedett termelékenység**. Az SaaS-alkalmazásokban felhasználói identitásokat egyetlen felhasználói üzembe helyezési kezelőfelülettel kezelhet. Ez az illesztőfelület egyetlen kiépítési szabályzattal rendelkezik.

* **Kockázatkezelés**. Növelheti a biztonságot úgy, hogy a szerepköröket és/vagy a hozzáférést meghatározó alkalmazotti állapot vagy csoporttagságok alapján automatizálja a módosításokat.

* **Foglalkozzon a megfelelőséggel és a szabályozással**. Az Azure AD minden felhasználói kiépítési kérelem esetében támogatja a natív naplózási naplókat. A kérelmeket a forrás-és a célszámítógépen is végrehajtja a rendszer. Így nyomon követheti, hogy ki férhet hozzá az alkalmazásokhoz egyetlen képernyőről.

* **Csökkentse a költségeket**. Az automatikus felhasználó-kiépítés csökkenti a költségeket azáltal, hogy elkerüli a hatékonyságot és az emberi hibát a manuális kiépítés során. Ez csökkenti az egyéni fejlesztésű felhasználói megoldások, parancsfájlok és naplók igényét.

### <a name="licensing"></a>Licencelés

Az Azure AD bármely alkalmazás önkiszolgáló integrálását teszi lehetővé az alkalmazás-katalógus menüjében található sablonok használatával. A licencekre vonatkozó követelmények teljes listáját az [Azure ad licencelési lapján](https://azure.microsoft.com/pricing/details/active-directory/)tekintheti meg.

#### <a name="application-licensing"></a>Alkalmazás licencelése

Szüksége lesz az automatikusan kiépíteni kívánt alkalmazás (ok) megfelelő licencére. Beszélje meg az alkalmazás tulajdonosait, hogy az alkalmazáshoz rendelt felhasználók rendelkeznek-e a megfelelő licenccel az alkalmazás szerepköreihez. Ha az Azure AD a szerepkörök alapján felügyeli az automatikus kiosztást, az Azure AD-ben hozzárendelt szerepköröket az alkalmazás-licencekhez kell igazítani. Az alkalmazásban található helytelen licencek hibát okozhatnak a felhasználó üzembe helyezése/frissítése során.

### <a name="terms"></a>Fogalmak

Ez a cikk a következő kifejezéseket használja:

* SZIFILISZ-műveletek – felhasználói fiókokon végrehajtott műveletek: létrehozás, olvasás, frissítés, törlés.

* Egyszeri bejelentkezés (SSO) – lehetővé teszi, hogy a felhasználó egyszer jelentkezzen be, és hozzáférhessen az összes SSO-kompatibilis alkalmazáshoz. A felhasználók kiépítés kontextusában az SSO az a felhasználó, aki egyetlen fiókkal fér hozzá az összes olyan rendszerhez, amely a felhasználók automatikus kiépítés használja.

* Forrásoldali rendszer – az Azure AD által kiépített felhasználók tárháza. Az Azure AD a legtöbb előre integrált kiépítési összekötőhöz tartozó forrásoldali rendszer. Vannak azonban kivételek a felhőalapú alkalmazásokhoz, például az SAP, a munkanap és az AWS esetében. Például tekintse meg a [felhasználók üzembe helyezése a munkahelyről az ad-be](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)című témakört.

* Célrendszer – azon felhasználók tárháza, akikre az Azure AD kiépít. A célként megadott rendszer általában SaaS-alkalmazás, például ServiceNow, Zscaler és Slack. A célként megadott rendszer lehet egy helyszíni rendszer is, például az AD.

* [Rendszer a tartományok közötti Identitáskezelés (scim)](https://aka.ms/scimoverview) számára – egy nyílt szabvány, amely lehetővé teszi a felhasználók üzembe helyezésének automatizálását. A SCIM a felhasználói azonosító adatokat a Microsoft és a szolgáltatók, például a Salesforce vagy más olyan SaaS-alkalmazások között közli, amelyek felhasználói azonosító adatokat igényelnek.

### <a name="training-resources"></a>Erőforrások betanítása

| Segédanyagok és eszközök| Hivatkozás és leírás |
| - | - |
| Igény szerinti webináriumok| [Vállalati alkalmazások kezelése az Azure AD-vel](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Ismerje meg, hogyan segítheti az Azure AD az egyszeri bejelentkezést a vállalati SaaS-alkalmazásokhoz és a hozzáférés szabályozására vonatkozó ajánlott eljárásokhoz. |
| Videók| [Mi a felhasználók üzembe helyezése az aktív Azure-címtárban?](https://youtu.be/_ZjARPpI6NI) <br> [A felhasználók üzembe helyezésének központi telepítése az Active Directory Azure-címtárban](https://youtu.be/pKzyts6kfrw) <br> [A Salesforce integrálása az Azure AD-vel: a felhasználók üzembe helyezésének automatizálása](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Online képzések| SkillUp online: [Identitások kezelése](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Ismerje meg, hogyan integrálhatja az Azure AD-t számos SaaS-alkalmazással, és hogyan biztosíthatja a felhasználók hozzáférését az alkalmazásokhoz. |
| Könyvek| [Modern hitelesítés a Azure Active Directory for web Applications (fejlesztői segédlet) első kiadása](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Ez egy mérvadó, részletes útmutató, amellyel Active Directory hitelesítési megoldásokat hozhat létre az új környezetekhez. |
| Oktatóanyagok| Az [SaaS-alkalmazások Azure ad-vel való integrálásával](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)kapcsolatban tekintse meg az oktatóanyagok listáját. |
| Gyakori kérdések| [Gyakori kérdések](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) az automatikus felhasználó-kiépítési folyamatról |

### <a name="solution-architectures"></a>Megoldásarchitektúrák

Az Azure AD-kiépítési szolgáltatás a felhasználókat az SaaS-alkalmazásokhoz és más rendszerekhez is kiépíti az egyes alkalmazások gyártói által biztosított felhasználói felügyeleti API-végpontokhoz való csatlakozással. Ezek a felhasználói felügyeleti API-végpontok lehetővé teszik az Azure AD számára a felhasználók programozott módon történő létrehozását, frissítését és eltávolítását.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatikus felhasználó-kiépítés hibrid vállalatok számára

Ebben a példában a felhasználók és a csoportok egy helyszíni címtárhoz csatlakoztatott HR-adatbázisban jönnek létre. Az Azure AD-kiépítési szolgáltatás kezeli az automatikus felhasználó-kiépítési célokat a cél SaaS-alkalmazásokhoz.

 ![felhasználó kiépítés](media/auto-user-provision-dp/hybridprovisioning.png)

**Munkafolyamat leírása:**

1. A felhasználók/csoportok egy helyszíni HR-alkalmazásban/rendszerben, például az SAP-ben jönnek létre. 

1. Azure AD Connect ügynök az identitások (felhasználók és csoportok) ütemezett szinkronizálását futtatja a helyi AD-ből az Azure AD-be.

1. Az Azure AD kiépítési szolgáltatás megkezdi a [kezdeti ciklust](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) a forrásrendszer és a célként megadott rendszeren. 

1. Az Azure AD kiépítési szolgáltatás a kezdeti ciklus óta megváltoztatott bármely felhasználó és csoport forrásrendszer-rendszerét lekérdezi, és elküldi a módosításokat a [növekményes ciklusokban](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatikus felhasználó-kiépítés a csak felhőalapú vállalatok számára

Ebben a példában a felhasználó létrehozása az Azure AD-ben történik, az Azure AD-létesítési szolgáltatás pedig felügyeli az automatikus felhasználót a cél (SaaS) alkalmazások számára:

![2\. kép](media/auto-user-provision-dp/cloudprovisioning.png)

**Munkafolyamat leírása:**

1. A felhasználók/csoportok az Azure AD-ben jönnek létre.

1. Az Azure AD kiépítési szolgáltatás megkezdi a [kezdeti ciklust](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) a forrásrendszer és a célként megadott rendszeren. 

1. Az Azure AD kiépítési szolgáltatás lekérdezi a forrásrendszer minden olyan felhasználó és csoport számára, amely a kezdeti ciklus óta frissült, és végrehajtja a [növekményes ciklusokat](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>A Felhőbeli HR-alkalmazások automatikus felhasználó általi üzembe helyezése 

Ebben a példában a felhasználók és a csoportok egy Felhőbeli HR-alkalmazásban, például a munkanapokon jönnek létre.

![2\. kép](media/auto-user-provision-dp/workdayprovisioning.png)

1. A Cloud HR Systemben létrehozott fiókok
1. Az adatforgalom a helyszíni AD-be az Azure AD kiépítési szolgáltatásával és a kiépítési ügynökkel.
1. Azure AD Connect szinkronizálja az Azure AD-vel
1. Az e-mail-cím és a username attribútum visszaírható a Cloud HR-alkalmazásba.

A megoldás architektúrájának és központi telepítésének részletes ismertetését a következő témakörben találja [: oktatóanyag: a munkanap beállítása az automatikus felhasználó](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)kiépítéséhez.

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

Gondolja át, hogy a szervezetnek meg kell határoznia a felhasználói kiépítés üzembe helyezésére vonatkozó stratégiát a környezetében.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségi körök eltérő elvárásai vannak. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](https://aka.ms/deploymentplans) , és hogy a projektben érintett szerepköröket jól megértette az érintett felek és a projekt bemeneti és elszámoltathatóság dokumentálása révén.

### <a name="plan-communications"></a>Kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Javasoljuk, hogy az automatikus felhasználó-kiépítés kezdeti konfigurációjának tesztelési környezetben legyen a felhasználók kis részhalmaza, mielőtt az összes éles üzemben lévő felhasználónak méreteznie kellene.

#### <a name="best-practices-for-a-pilot"></a>Ajánlott eljárások a pilóták számára  

Egy próba lehetővé teszi egy kis csoport tesztelését, mielőtt mindenki számára elérhetővé tenné a funkciót. Győződjön meg arról, hogy a tesztelés részeként a szervezeten belül minden használati esetet alaposan teszteltek.

Az első hullámban célozza meg, a használhatóságot és az egyéb megfelelő felhasználókat, akik kipróbálhatják és elküldhetik a visszajelzést. Ezzel a visszajelzéssel tovább fejlesztheti a felhasználók számára elküldött kommunikációt és útmutatást, és betekintést nyerhet a támogatási munkatársak által látható problémák típusaiba.

Bővítse a bevezetést a felhasználók nagyobb csoportjaira a megcélzott csoport (ok) hatókörének növelésével. Ez a [dinamikus csoporttagság](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)használatával végezhető el, vagy manuálisan is hozzáadhatja a felhasználókat a megcélozott csoport (ok) hoz.

## <a name="plan-application-connections-and-administration"></a>Az alkalmazások kapcsolatainak és felügyeletének megtervezése

Az Azure AD portálon megtekintheti és kezelheti az összes olyan alkalmazást, amely támogatja a kiépítés használatát. Lásd: [alkalmazások keresése a portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal).

### <a name="determine-the-type-of-connector-to-use"></a>A használandó összekötő típusának meghatározása

Az automatikus kiépítés engedélyezéséhez és konfigurálásához szükséges tényleges lépések az alkalmazástól függően változnak. Ha az automatikusan kiépíteni kívánt alkalmazás megjelenik az [Azure ad SaaS-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)-katalógusban, akkor az [alkalmazás-specifikus integrációs oktatóanyagot](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) kell választania az előre integrált felhasználói üzembe helyezési összekötő konfigurálásához.

Ha nem, kövesse az alábbi lépéseket:

1. [Hozzon létre egy](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) , az előre integrált felhasználó-létesítési összekötőre vonatkozó kérelmet. Csapatunk együttműködik Önnel és az alkalmazás fejlesztővel, hogy az alkalmazást a platformon, ha az támogatja a SCIM-t.

1. Használja az [BYOA scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) általános felhasználói üzembe helyezési támogatását az alkalmazáshoz. Ez a követelmény, hogy az Azure AD-ben a felhasználók az előre integrált létesítési összekötő nélkül legyenek kiépítve az alkalmazásba.

1. Ha az alkalmazás képes a BYOA SCIM-összekötő használatára, tekintse meg a [BYOA scim Integration oktatóanyagot](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) az alkalmazás BYOA scim-összekötő konfigurálásához.

További információ: [milyen alkalmazásokat és rendszereket használhatok az Azure ad automatikus felhasználói üzembe](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) helyezéséhez?

### <a name="collect-information-to-authorize-application-access"></a>Adatgyűjtés az alkalmazás-hozzáférés engedélyezéséhez

Az automatikus felhasználó-kiépítés beállítása egy alkalmazáson belüli folyamat. Minden alkalmazáshoz [rendszergazdai hitelesítő adatokat](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) kell megadnia a célrendszer felhasználói felügyeleti végpontjának való kapcsolódáshoz.

Az alábbi képen a szükséges rendszergazdai hitelesítő adatok egy verziója látható:

![Kiépítési képernyő a felhasználói fiókok üzembe helyezési beállításainak kezeléséhez](media/auto-user-provision-dp/userprovisioning-admincredentials.png)

Míg egyes alkalmazásokhoz rendszergazdai Felhasználónév és jelszó szükséges, másoknak tulajdonosi jogkivonatot is igényelhetnek.

## <a name="plan-user-and-group-provisioning"></a>A felhasználók és csoportok kiépítési tervének megtervezése

Ha engedélyezi a felhasználók számára a vállalati alkalmazások kiosztását, a [Azure Portal](https://portal.azure.com/) attribútum-hozzárendelésen keresztül vezérli az attribútum értékeit.

### <a name="determine-operations-for-each-saas-app"></a>Az egyes SaaS-alkalmazások műveleteinek meghatározása

Minden alkalmazás rendelkezhet olyan egyedi felhasználói vagy csoport-attribútumokkal, amelyeket az Azure AD-ben lévő attribútumokhoz kell rendelni. Az alkalmazásnak csak egy részhalmaza lehet a rendelkezésre álló szifilisz-műveleteknek.

Az egyes alkalmazásokhoz a következő információkat dokumentálja:

* A rendszer a megcélzott rendszerek felhasználó-és csoport-objektumain végrehajtandó, szifilisz-kiépítési műveleteket hajtja végre. Előfordulhat például, hogy minden SaaS-alkalmazás üzleti tulajdonosa nem szeretné az összes lehetséges műveletet.

* A forrásoldali rendszeren elérhető attribútumok

* A célként megadott rendszeren elérhető attribútumok

* Attribútumok társítása a rendszerek között.

### <a name="choose-which-users-and-groups-to-provision"></a>Válassza ki, hogy mely felhasználók és csoportok legyenek kiépítve

Az automatikus felhasználó-kiépítés megvalósítása előtt meg kell határoznia az alkalmazáshoz kiépíteni kívánt felhasználókat és csoportokat.

* A [hatóköri szűrők](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) használatával olyan attribútum-alapú szabályokat határozhat meg, amelyek meghatározzák, hogy mely felhasználók legyenek kiépítve egy alkalmazáshoz.

* Ezután szükség szerint használja a [felhasználói és csoportos hozzárendeléseket](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) a további szűréshez.

### <a name="define-user-and-group-attribute-mapping"></a>Felhasználói és csoportos attribútumok társításának meghatározása

Az automatikus felhasználó-kiépítés megvalósításához meg kell határoznia az alkalmazáshoz szükséges felhasználói és csoportosítási attribútumokat. Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között van egy előre konfigurált attribútum és [attribútum-hozzárendelés](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) . Nem minden SaaS-alkalmazás engedélyezi a csoport attribútumait.

Az Azure AD támogatja a közvetlen attribútum-attribútum hozzárendelést, állandó értékek biztosítását vagy [kifejezések írását az attribútumok leképezéséhez](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Ez a rugalmasság részletesen szabályozza, hogy mi történjen a megtalált rendszer attribútumában. A [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) és a Graph Explorer használatával exportálhatja a felhasználók kiépítési attribútumait egy JSON-fájlba, majd importálhatja azt az Azure ad-be.

További információkért lásd: [felhasználói kiépítési attribútum testreszabása – SaaS-alkalmazások leképezése Azure Active Directory-ben](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

### <a name="special-considerations-for-user-provisioning"></a>A felhasználók üzembe helyezésének speciális szempontjai

Az üzembe helyezés utáni problémák csökkentése érdekében vegye figyelembe a következőket:

* Győződjön meg arról, hogy a forrás és a cél alkalmazások közötti felhasználói/csoport objektumok leképezésére használt attribútumok rugalmasak. Nem okozzák, hogy a felhasználók vagy csoportok helytelenül legyenek kiépítve, ha az attribútumok megváltoznak (például egy felhasználó a vállalat egy másik részére lép).

* Előfordulhat, hogy az alkalmazásoknak olyan konkrét korlátozásokkal és/vagy követelményekkel kell rendelkezniük, amelyeknek teljesülniük kell a felhasználók megfelelő működéséhez. A Slack például bizonyos attribútumok értékeit csonkolja. Az egyes alkalmazásokra vonatkozó [automatikus felhasználói üzembe helyezési oktatóanyagokat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) itt találja.

* Erősítse meg a séma konzisztenciáját a forrás-és a célszámítógépeken. Gyakori problémák például az egyszerű felhasználónév vagy a levelezés nem megfelelő attribútumokat tartalmaznak. Például az Azure AD-beli UPN az *john_smith@contoso.com* és az alkalmazásban *jsmith@contoso.com* . További információ: a [felhasználói és a csoport sémájának referenciája](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups).

## <a name="plan-testing-and-security"></a>A tesztelés és a biztonság tervezése

A telepítés minden fázisában ellenőrizze, hogy az eredmények a várt módon vannak-e tesztelve, és naplózza a kiépítési ciklusokat.

### <a name="plan-testing"></a>Tesztelési terv

Miután beállította az automatikus felhasználó-kiépítést az alkalmazáshoz, a tesztelési eseteket futtatva ellenőrizheti, hogy a megoldás megfelel-e a szervezet követelményeinek.

| Alkalmazási helyzetek| Várt eredmények |
| - | - |
| A felhasználó hozzá lett adva a célként megadott rendszerhez rendelt csoporthoz | A felhasználói objektum kiépítve a célként megadott rendszerbe. <br>A felhasználó be tud jelentkezni a célcsoportba, és végrehajtja a kívánt műveleteket. |
| A felhasználó el lett távolítva a célként megadott rendszerhez rendelt csoportból. | A felhasználói objektumot a rendszer kiépíti a célszámítógépen.<br>A felhasználó nem tud bejelentkezni a célként megadott rendszerbe. |
| A felhasználói adatok az Azure AD-ben bármilyen módon frissülnek | A frissített felhasználói attribútumok a célként megadott rendszeren a növekményes ciklust követően jelennek meg |
| A felhasználó hatókörön kívül van | A felhasználói objektum le van tiltva vagy törölve van. <br>Megjegyzés: Ez a viselkedés felül van bírálva a [munkanap kiépítés](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)során. |

### <a name="plan-security"></a>A biztonság megtervezése

Az üzembe helyezés részeként gyakran előfordul, hogy biztonsági felülvizsgálatra van szükség. Ha biztonsági felülvizsgálatra van szüksége, tekintse meg a számos Azure AD-tanulmányt [, amely](https://www.microsoft.com/download/details.aspx?id=36391) áttekintést nyújt az identitásról szolgáltatásként.

### <a name="plan-rollback"></a>Terv visszaállítása

Ha az automatikus felhasználó-kiépítési implementáció nem tud megfelelően működni az éles környezetben, a következő visszaállítási lépések segítséget nyújthatnak a korábbi ismert jó állapot visszaállításához:

1. Tekintse át a [kiépítési összefoglalás jelentését](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) és az üzembe helyezési [naplókat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) , és állapítsa meg, hogy milyen helytelen műveletek történtek az érintett felhasználókon és/vagy csoportokon.

1. A kiépítési naplók használatával határozza meg az érintett felhasználók és/vagy csoportok utolsó ismert helyes állapotát. Tekintse át a forrásoldali rendszereket (Azure AD vagy AD) is.

1. Az alkalmazás tulajdonosával az utolsó ismert jó állapotú értékek használatával frissítheti az alkalmazásban közvetlenül érintett felhasználókat és/vagy csoportokat.

## <a name="deploy-automatic-user-provisioning-service"></a>Automatikus felhasználó-kiépítési szolgáltatás üzembe helyezése

Válassza ki a megoldás követelményeihez igazodó lépéseket.

### <a name="prepare-for-the-initial-cycle"></a>Felkészülés a kezdeti ciklusra

Amikor az Azure AD-létesítési szolgáltatás első alkalommal fut, a forrásrendszer és a célrendszer kezdeti ciklusa minden felhasználói objektumról pillanatképet készít az egyes célszámítógépeken.

Az alkalmazások automatikus kiosztásának engedélyezésekor a kezdeti ciklus akár 20 perctől akár több óráig is eltarthat. Az időtartam az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függ. Lásd: [a kiépítési teljesítmény javítása](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish).

A kiépítési szolgáltatás mindkét rendszer állapotát a kezdeti ciklus után, a későbbi növekményes ciklusok teljesítményének növelésével tárolja.

### <a name="configure-automatic-user-provisioning"></a>A felhasználók automatikus átadásának konfigurálása

A [Azure Portal](https://portal.azure.com/) segítségével kezelheti a felhasználói fiókok automatikus üzembe helyezését és kiépítheti az azt támogató alkalmazások felügyeletét. Kövesse a Hogyan az [automatikus kiépítés beállítása egy alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) című szakasz lépéseit.

Az Azure AD-beli felhasználói kiépítési szolgáltatás a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)használatával is konfigurálható és kezelhető.

## <a name="manage-automatic-user-provisioning"></a>Automatikus felhasználó-kiépítés kezelése

Most, hogy üzembe helyezte, felügyelni kell a megoldást.

### <a name="monitor-user-provisioning-operation-health"></a>A felhasználó üzembe helyezési állapotának figyelése

A sikeres [kezdeti ciklust](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)követően az Azure ad kiépítési szolgáltatás határozatlan ideig, az egyes alkalmazásokra jellemző időközönként futtatja a növekményes frissítéseket, amíg az alábbi események egyike nem következik be:

* A szolgáltatás kézi leállítása megtörtént, és a rendszer elindítja az új kezdeti ciklust a [Azure Portal](https://portal.azure.com/)használatával, vagy a megfelelő [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) -parancs használatával.

* Az attribútum-hozzárendelések vagy a hatóköri szűrők változása új kezdeti ciklust indít el.

* A kiépítési folyamat magas szintű hibák miatt Karanténba kerül, és a karanténban marad, ha a rendszer automatikusan letiltja a műveletet.

Az események áttekintéséhez és a kiépítési szolgáltatás által végzett összes egyéb tevékenységhez tekintse meg az Azure AD- [kiépítési naplókat](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/manage-apps/context/manage-apps-context).

Annak megismeréséhez, hogy a kiépítési ciklusok mennyi ideig tartanak és figyeljenek a kiépítési feladatok állapotát, [ellenőrizheti a felhasználók üzembe](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)helyezésének állapotát.

### <a name="gain-insights-from-reports"></a>Betekintést nyerhet a jelentésekből

Az Azure AD [további betekintést](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) nyújt a szervezet felhasználó általi használatára és működési állapotára a naplók és a jelentések segítségével.

A rendszergazdáknak ellenőriznie kell a kiépítési összefoglaló jelentést a kiépítési feladatok működési állapotának figyeléséhez. A kiépítési szolgáltatás által végrehajtott összes tevékenységet az Azure AD-naplók rögzítik. Lásd [: oktatóanyag: jelentéskészítés a felhasználói fiókok automatikus üzembe](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning)helyezéséhez.

Javasoljuk, hogy fogadja el a jelentések tulajdonjogát, és használja ezeket a jelentéseket egy olyan lépésszám esetében, amely megfelel a szervezet igényeinek. Az Azure AD 30 napig őrzi meg a legtöbb naplózási adatát.

### <a name="troubleshoot"></a>Hibaelhárítás

A kiépítés során esetlegesen felmerülő problémák elhárításához tekintse meg az alábbi hivatkozásokat:

* [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)

* [Egy attribútum szinkronizálása a helyszíni Active Directory az Azure AD-be az alkalmazásba való kiépítéshez](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)

* [Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)

* [Probléma a rendszergazdai hitelesítő adatok mentésekor, miközben a felhasználók üzembe helyezését egy Azure Active Directory Gallery-alkalmazáshoz konfigurálja](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)

* [Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazásba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

* [Helytelenek a felhasználók egy Azure AD Gallery-alkalmazásban való üzembe helyezése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)

### <a name="helpful-documentation"></a>Hasznos dokumentáció

* [Kifejezések írása attribútum-hozzárendelésekhez](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

* [Az Azure AD szinkronizációs API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [A hatókörön kívüli felhasználói fiókok törlésének kihagyása](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)

* [Azure AD Connect kiépítési ügynök: verziók kiadásának előzményei](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)

#### <a name="resources"></a>Segédanyagok és eszközök

* [Termékkel kapcsolatos visszajelzések megadása](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Naprakészen tarthatja az Azure AD újdonságait](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack túlfolyó Azure AD-fórum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>További lépések
* [Automatikus felhasználó-kiépítés konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)

* [Kiépítési konfiguráció exportálása vagy importálása Microsoft Graph API használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)

* [Kifejezések írása az attribútum-hozzárendelésekhez az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
