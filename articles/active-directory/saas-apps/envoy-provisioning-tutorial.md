---
title: 'Oktatóanyag: a megbízottat konfigurálása a felhasználók automatikus üzembe helyezéséhez Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a megbízottat.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 3d10480f8668576b4a8c755d6a3aa48603ca61bf
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361075"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Oktatóanyag: a megbízottat konfigurálása automatikus felhasználó-kiépítési folyamathoz

Ez az oktatóanyag leírja, milyen lépéseket kell elvégeznie a megbízottat és a Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési [szolgáltatás használatával.](https://envoy.com/pricing/) A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a megbízottat
> * Felhasználók eltávolítása a megbízottat, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a megbízott között
> * Csoportok és csoporttagságok kiépítése a megbízottat
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial) a megbízottat (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [Egy megbízottat bérlő](https://envoy.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a megbízottat milyen adatleképezést szeretne leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>2. lépés A megbízottat konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a [megbízott felügyeleti konzolra](https://dashboard.envoy.com/login). Kattintson az **integrációk**elemre.

    ![A megbízottat integrációk](media/envoy-provisioning-tutorial/envoy01.png)

2. Kattintson a **telepítés** gombra a **Microsoft Azure scim-integrációhoz**.

    ![Megbízott telepítése](media/envoy-provisioning-tutorial/envoy02.png)

3. Kattintson a **Mentés** gombra az **összes felhasználó szinkronizálásához**. 

    ![Megbízott mentése](media/envoy-provisioning-tutorial/envoy03.png)

4. Másolja a **OAUTH tulajdonosi JOGkivonatát**. Ez az érték a Azure Portalban szereplő megbízott alkalmazás kiépítés lapjának **titkos jogkivonat** mezőjében lesz megadva.
    
    ![Megbízott OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>3. lépés Adja hozzá a megbízottat az Azure AD Application Galleryből

Vegyen fel egy megbízottat az Azure AD-alkalmazás-katalógusból a megbízott kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az SSO-t, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok megbízottat való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása a megbízottnak 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-beli megbízottat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **megbízottat**.

    ![A megbízottat mutató hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://app.envoy.com/scim/v2` meg a **bérlői URL-címet**. Adja meg a **OAUTH tulajdonosi jogkivonat** értékét, amely korábban a **titkos jogkivonatban**lett lekérve. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a megbízotthoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a megbízott fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

   ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/envoy-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a megbízottat**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a megbízott felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a megbízott API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |externalId|Sztring|
   |displayName|Sztring|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |preferredLanguage|Sztring|
   |Részleg|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |területi beállítás|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a megbízottat**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a megbízott csoportoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a megbízottat, módosítsa a **kiépítési állapotot** a **következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a megbízottat szeretné kiépíteni. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)