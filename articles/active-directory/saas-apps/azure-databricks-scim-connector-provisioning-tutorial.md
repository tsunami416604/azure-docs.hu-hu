---
title: 'Oktatóanyag: Az Azure Databricks SCIM-összekötő konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni a felhasználói fiókokat az Azure AD-ről az Azure Databricks SCIM Connector-ra.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370520"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálja az Azure Databricks SCIM-összekötőt automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti az Azure Databricks SCIM Connector és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseket. Ha konfigurálva van, az Azure AD automatikusan rendelkezik és de-rendelkezések et az [Azure Databricks SCIM-összekötő](https://databricks.com/) az Azure AD-kiépítési szolgáltatás használatával a felhasználók és csoportok számára. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Azure Databricks SCIM-összekötőben
> * Felhasználók eltávolítása az Azure Databricks SCIM-összekötőben, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Azure Databricks SCIM Connector között
> * Csoportok és csoporttagságok kiépítése az Azure Databricks SCIM-összekötőben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy Azure Databricks-fiók rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és az Azure Databricks SCIM-összekötő között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>2. lépés Az Azure Databricks SCIM-összekötő konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Az Azure Databricks SCIM-kiépítés beállításához adja hozzá erőforrásként az Azure Active Directory-bérlőben, és konfigurálja az alábbi beállításokkal.

    ![Az Azure Databricks beállítása](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Személyes hozzáférési jogkivonat létrehozása az Azure Databricks-ben olvassa el [ezt](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)a .

3. Másolja a **token .** Ezt az értéket a titkos jogkivonat mezőben adja meg az Azure Databricks SCIM-összekötő alkalmazás kiépítés lapján az Azure Portalon.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>3. lépés Azure Databricks SCIM-összekötő hozzáadása az Azure AD alkalmazásgyűjteményéből

Azure Databricks SCIM-összekötő hozzáadása az Azure AD alkalmazásgalériából az Azure Databricks SCIM-összekötőbe való kiépítés megkezdéséhez. Ha korábban már beállította az Azure Databricks SCIM Connector for SSO használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel az Azure Databricks SCIM-összekötőhöz, ki kell választania egy másik szerepkört, mint **az alapértelmezett hozzáférés.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása az Azure Databricks SCIM-összekötőbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!NOTE]
> Ha többet szeretne megtudni az Azure Databricks SCIM-végpontjáról, olvassa el [ezt](https://docs.databricks.com/dev-tools/api/latest/scim.html
)a .

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Az Azure Databricks SCIM Connector automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Azure Databricks SCIM Connector lehetőséget.**

    ![Az Azure Databricks SCIM-összekötő hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg az SCIM végpontértékét a **bérlői URL-címben.** A bérlői URL-címnek abban a formátumban `https://<region>.azuredatabricks.net/api/2.0/preview/scim` kell lennie, amelyben a **régió** megtalálható az Azure Databricks kezdőlapjának URL-címében. Például egy SCIM végpont **a Westus** régióban lesz. `https://westus.azuredatabricks.net/api/2.0/preview/scim` Adja meg a titkos jogkivonat korábbi tokenjében korábban beolvasott **jogkivonatértéket.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az Azure Databricks SCIM-összekötőhöz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy az Azure Databricks SCIM-összekötő fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![Kiépítés](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása az Azure Databricks SCIM-összekötővel lehetőséget.**

9. Tekintse át az Azure AD-ről az Azure Databricks SCIM-összekötővel szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az Azure Databricks SCIM Connector felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)győződjön meg arról, hogy az Azure Databricks SCIM Connector API támogatja a felhasználók szűrése alapján az adott attribútum. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |displayName|Sztring|
   |Aktív|Logikai|

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása az Azure Databricks SCIM-összekötővel lehetőséget.**

11. Tekintse át az Azure AD és az Azure Databricks SCIM-összekötő között az **Attribútum-leképezés** szakaszban szinkronizált csoportattribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok az Azure Databricks SCIM-összekötő csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

     |Attribútum|Típus|
     |---|---|
     |displayName|Sztring|
     |tagok|Referencia|

11. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása az Azure Databricks SCIM-összekötővel lehetőséget.**

12. Az Azure Databricks SCIM-összekötő Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **Kiépítési állapot** ot **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket az Azure Databricks SCIM Connector-ba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

* A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
* Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
* Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* Databricks mindig átalakítja a felhasználónév értékek kisbetűs, ha a mentés a könyvtárba, függetlenül attól, hogy a nagybetűs küldünk nekik keresztül SCIM.
* Jelenleg az Azure Databricks SCIM API-jával szembeni kérelmek USER@contoso.com beérkeznek a felhasználók számára a user@contoso.comkis- és nagybetűket, így ha lekérdezzük, 0 találattal jelennek meg, ahogy tárolják.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../manage-apps/check-status-user-account-provisioning.md)
