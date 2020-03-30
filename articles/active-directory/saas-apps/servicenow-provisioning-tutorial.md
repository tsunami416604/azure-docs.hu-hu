---
title: 'Oktatóanyag: A ServiceNow konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és de-kiépítése felhasználói fiókok az Azure AD ServiceNow.
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
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205102"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Oktatóanyag: A ServiceNow konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti a ServiceNow és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseit. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [ServiceNow](https://www.servicenow.com/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a ServiceNow-ban
> * Felhasználók eltávolítása a ServiceNow szolgáltatásból, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a ServiceNow között
> * Csoportok és csoporttagságok kiépítése a ServiceNow szolgáltatásban
> * [Egyszeri bejelentkezés a](servicenow-tutorial.md) ServiceNow szolgáltatásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [A ServiceNow példánya](https://www.servicenow.com/) Calgary vagy magasabb
* Helsinki vagy újabb [ServiceNow Express példánya](https://www.servicenow.com/)
* Felhasználói fiók a ServiceNow-ban rendszergazdai szerepkörrel

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és a ServiceNow között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>2. lépés A ServiceNow konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Azonosítsa a ServiceNow-példány nevét. A példány nevét a ServiceNow eléréséhez használt URL-címben találja. Az alábbi példában a példány neve dev35214.

![ServiceNow-példány](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Hitelesítő adatok beszerzése egy rendszergazda a ServiceNow. Nyissa meg a ServiceNow felhasználói profilját, és ellenőrizze, hogy a felhasználó rendelkezik-e rendszergazdai szerepkörvel. 

![ServiceNow rendszergazdai szerepkör](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>3. lépés ServiceNow hozzáadása az Azure AD alkalmazásgyűjteményéből

Add ServiceNow az Azure AD alkalmazáskatalógusból a ServiceNow-hoz való kiépítés kezelésének megkezdéséhez. Ha korábban már bevan állítva servicenow sso használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel a ServiceNow szolgáltatáshoz, az Alapértelmezett hozzáférésszolgáltatástól eltérő szerepkört kell **választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása a ServiceNow szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>A ServiceNow automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ServiceNow**lehetőséget.

    ![A ServiceNow hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a ServiceNow rendszergazdai hitelesítő adatait és felhasználónevét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a ServiceNow szolgáltatáshoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a ServiceNow-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a ServiceNow szolgáltatással**lehetőséget.

9. Tekintse át az Azure AD és serviceNow között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a ServiceNow felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)akkor meg kell győződnie arról, hogy a ServiceNow API támogatja a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása servicenow-ra**lehetőséget.

11. Tekintse át az Azure AD és serviceNow között szinkronizált csoportattribútumokat az **Attribútum-leképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a ServiceNow csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás serviceNow engedélyezéséhez módosítsa a **kiépítési állapot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a ServiceNow-ba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

1. A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
2. Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
3. Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* **InvalidLookupReference:** Bizonyos attribútumok, például a Department és a Location kiépítésekor a ServiceNow-ban az értékeknek már létezniük kell a ServiceNow hivatkozási táblájában. Előfordulhat például, hogy két hely (Seattle, Los Angeles) és három részleg (Értékesítés, Pénzügy, Marketing) található a ServiceNow **táblanév beszúrása** táblájában. Ha olyan felhasználót próbál kiépíteni, ahol a részlege "Értékesítés", a helye pedig "Seattle", akkor sikeresen ki lesz építve. Ha megpróbál kiépíteni egy felhasználót az "Értékesítés" részleggel és a "LA" helynel, a felhasználó nem lesz kiépítve. Az LA helyet hozzá kell adni a ServiceNow referenciatáblájához, vagy az Azure AD felhasználói attribútumát frissíteni kell, hogy megfeleljen a ServiceNow formátumának. 
* **EntryjoiningPropertyValueIsMissing:** Tekintse át az [attribútum-hozzárendeléseket](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) az egyező attribútum azonosításához. Ennek az értéknek meg kell jelennie a kiépíteni kívánt felhasználón vagy csoporton. 
* Tekintse át a [ServiceNow SOAP API-t](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) a követelmények és korlátozások megértéséhez (például egy felhasználó országkódjának megadásához formázott formátum)
* Egyes ServiceNow-telepítések az Azure AD-kiépítési szolgáltatás ip-tartományait engedélyezni kell. Az Azure AD-kiépítési szolgáltatás fenntartott IP-tartományai itt találhatók [az](https://www.microsoft.com/download/details.aspx?id=56519) "AzureActiveDirectoryDomainServices" alatt.
* A létesítési kérelmek alapértelmezés szerint a https://{your-instance-name}.service-now.com/{table-name} címre kerülnek. Ha egyéni bérlői URL-címet igényel, a teljes URL-címet megadhatja a példány névmezőjében.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
