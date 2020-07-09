---
title: 'Oktatóanyag: a ServiceNow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: da62efff5db5c71b087657b0eec93f8dd4702665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84751503"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés ServiceNow konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a ServiceNow és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [ServiceNow](https://www.servicenow.com/) . A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a ServiceNow-ben
> * Felhasználók eltávolítása a ServiceNow-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a ServiceNow között
> * Csoportok és csoporttagságok kiépítése a ServiceNow-ben
> * [Egyszeri bejelentkezés](servicenow-tutorial.md) a ServiceNow-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Calgary vagy magasabb [ServiceNow-példány](https://www.servicenow.com/)
* [ServiceNow Express-példány](https://www.servicenow.com/) (Helsinki vagy újabb)
* Felhasználói fiók a ServiceNow-ben a rendszergazdai szerepkörrel

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a ServiceNow között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>2. lépés ServiceNow konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Azonosítsa a ServiceNow-példány nevét. A ServiceNow eléréséhez használt URL-címen megkeresheti a példány nevét. Az alábbi példában a példány neve dev35214.

   ![ServiceNow-példány](media/servicenow-provisioning-tutorial/servicenow_instance.png)

2. Hitelesítő adatok beszerzése egy rendszergazda számára a ServiceNow-ben. Navigáljon a felhasználói profilhoz a ServiceNow-ben, és ellenőrizze, hogy a felhasználó rendelkezik-e rendszergazdai szerepkörrel. 

   ![ServiceNow-rendszergazdai szerepkör](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

3. Győződjön meg arról, hogy a következő beállítások **le vannak tiltva** a ServiceNow:

   1. Válassza a **rendszerbiztonsági**  >  **magas biztonsági beállítások**  >  **alapszintű hitelesítés szükséges a bejövő séma-kérelmekhez**beállítást.
   2. Válassza a **Rendszertulajdonságok**  >  **webszolgáltatások**  >  **alapszintű hitelesítés szükséges a bejövő SOAP-kérelmekhez**lehetőséget.
     
   > [!IMPORTANT]
   > Ha a beállítás *engedélyezve*van, a kiépítési motor nem fog kommunikálni a ServiceNow.

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>3. lépés ServiceNow hozzáadása az Azure AD Application Galleryből

Vegyen fel ServiceNow az Azure AD-alkalmazás-katalógusból a ServiceNow való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a ServiceNow az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok ServiceNow való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a ServiceNow 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a ServiceNow az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **ServiceNow**lehetőséget.

    ![Az ServiceNow hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a ServiceNow rendszergazdai hitelesítő adatait és a felhasználónevet. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a ServiceNow. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a ServiceNow-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![kiépítési](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a ServiceNow**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő ServiceNow. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ServiceNow felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a ServiceNow API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a ServiceNow**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált ServiceNow az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ServiceNow tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás ServiceNow való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a ServiceNow kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* **InvalidLookupReference:** Bizonyos attribútumok, például a részleg és a ServiceNow-ben való kiépítés során az értékeknek már léteznie kell a ServiceNow található egyik táblázatban. Előfordulhat például, hogy két helyet (Seattle, Los Angeles) és három részleget (Sales, Finance, marketing) tartalmaz **a ServiceNow** . Ha olyan felhasználót próbál kiépíteni, amelyben a részlege "Sales", és a tartózkodási hely "Seattle", akkor a rendszer sikeresen kiépíti. Ha olyan felhasználót próbál kiépíteni, amely a "Sales" osztályt és a "LA" helyet kísérli meg, a felhasználó nem lesz kiépítve. A (z) LA helyet fel kell venni a ServiceNow hivatkozási táblájába, vagy az Azure AD felhasználói attribútumát frissíteni kell, hogy az megfeleljen a ServiceNow formátumának. 
* **EntryJoiningPropertyValueIsMissing:** A megfelelő attribútum azonosításához tekintse át az [attribútumok leképezéseit](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) . Ennek az értéknek jelen kell lennie a kiépíteni próbált felhasználón vagy csoportban. 
* Tekintse át a [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) -t, hogy megértse az összes követelményt és korlátozást (például a felhasználó országkód megadásának formátuma)
* A rendszer alapértelmezés szerint a kiépítési kérelmeket a https://{saját példányának neve}. Service-Now. com/{Table-Name} értékre továbbítja. Ha egyéni bérlői URL-címet igényel, a teljes URL-címet megadhatja a példány neve mezőben.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Ez a hiba azt jelzi, hogy a ServiceNow-példánnyal kommunikáló probléma történt. Győződjön meg arról, hogy a következő beállítások *le vannak tiltva* a ServiceNow:
   
   1. Válassza a **rendszerbiztonsági**  >  **magas biztonsági beállítások**  >  **alapszintű hitelesítés szükséges a bejövő séma-kérelmekhez**beállítást.
   2. Válassza a **Rendszertulajdonságok**  >  **webszolgáltatások**  >  **alapszintű hitelesítés szükséges a bejövő SOAP-kérelmekhez**lehetőséget.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
