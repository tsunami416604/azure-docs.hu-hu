---
title: 'Oktatóanyag: Azure Databricks SCIM-összekötő konfigurálása az automatikus felhasználói üzembe helyezéshez a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből Azure Databricks SCIM-összekötőhöz.
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
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370520"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Oktatóanyag: Azure Databricks SCIM-összekötő konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Azure Databricks SCIM-összekötőben és a Azure Active Directory (Azure AD) is el kell végeznie az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan előállítja a felhasználókat és csoportokat, hogy [Azure DATABRICKS scim-összekötőt](https://databricks.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása Azure Databricks SCIM-összekötőben
> * Azure Databricks SCIM-összekötőben lévő felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Azure Databricks SCIM-összekötő között
> * Csoportok és csoporttagságok kiépítése Azure Databricks SCIM-összekötőben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Rendszergazdai jogosultságokkal rendelkező Azure Databricks fiók.

## <a name="step-1-plan-your-provisioning-deployment"></a>1\. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Azure DATABRICKS scim-összekötő milyen adatleképezési](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leképezést szeretne. 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>2\. lépés Azure Databricks SCIM-összekötő konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Azure Databricks SCIM-létesítés beállításához adja hozzá erőforrásként a Azure Active Directory-bérlőben, és konfigurálja az alábbi beállításokkal.

    ![Azure Databricks telepítő](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Ha Azure Databricks személyes hozzáférési tokent szeretne előállítani, tekintse meg [ezt a témakört](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Másolja a **tokent**. Ez az érték a Azure Databricks SCIM Connector alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>3\. lépés Azure Databricks SCIM-összekötő hozzáadása az Azure AD Application Galleryből

Vegye fel Azure Databricks SCIM-összekötőt az Azure AD-alkalmazás-katalógusból a kiépítés felügyeletének megkezdéséhez Azure Databricks SCIM-összekötőhöz. Ha korábban már beállította Azure Databricks SCIM-összekötőt az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4\. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel hozzá Azure Databricks SCIM-összekötőhöz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>5\. lépés Automatikus felhasználó-kiépítés konfigurálása Azure Databricks SCIM-összekötőhöz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!NOTE]
> Ha többet szeretne megtudni a Azure Databricks "SCIM-végpontról, tekintse meg [ezt](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása Azure Databricks SCIM-összekötőhöz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Azure DATABRICKS scim-összekötő**lehetőséget.

    ![A Azure Databricks SCIM-összekötő hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a scim-végpont értékét a **bérlői URL-címben**. A bérlői URL-címnek `https://<region>.azuredatabricks.net/api/2.0/preview/scim` formátumban kell lennie, ahol a **régió** megtalálható a Azure Databricks kezdőlapjának URL-címében. A **westus** régió scim-végpontja például `https://westus.azuredatabricks.net/api/2.0/preview/scim`lesz. Adja meg a **titkos jogkivonatban**korábban lekért jogkivonat-értéket. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni Azure Databricks scim-összekötőhöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Azure Databricks SCIM-összekötő fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![kiépítési](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Azure Databricks scim-összekötő**lehetőséget.

9. Tekintse át az Azure AD-ból szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban Azure Databricks scim-összekötőre. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Azure Databricks scim-összekötő felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a Azure Databricks scim-összekötő API támogatja a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName|Sztring|
   |displayName|Sztring|
   |aktív|Logikai|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Azure Databricks scim-összekötőt**.

11. Tekintse át az Azure AD-ból szinkronizált csoportok attribútumait az **attribútum-hozzárendelési** szakaszban Azure Databricks scim-összekötőre. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Azure Databricks scim-összekötőben lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

     |Attribútum|Típus|
     |---|---|
     |displayName|Sztring|
     |tag|Referencia|

11. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Azure Databricks scim-összekötőt**.

12. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Azure Databricks SCIM-összekötőhöz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket Azure Databricks SCIM-összekötőhöz szeretne kiépíteni, ehhez válassza a **Beállítások** szakaszban lévő kívánt értékeket a **hatókörben** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6\. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
* Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
* Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* A Databricks mindig átalakítja a felhasználónevet, ha a SCIM-on keresztül elküldött nagybetűktől függetlenül a címtárba való mentéskor a saját felhasználónevére kerül.
* A Azure Databricks "SCIM API-user@contoso.comval rendelkező felhasználók számára jelenleg a kis-USER@contoso.com és nagybetűk megkülönböztetésére irányuló kérések

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
