---
title: 'Oktatóanyag: a MediusFlow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548072"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés MediusFlow konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a MediusFlow és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [MediusFlow](https://www.mediusflow.com/) . A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a MediusFlow-ben
> * Felhasználók eltávolítása a MediusFlow-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a MediusFlow között
> * Csoportok és csoporttagságok kiépítése a MediusFlow-ben
> * Egyszeri bejelentkezés a MediusFlow-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Aktív MediusFlow-előfizetés minőségbiztosítási vagy üzemi Bérlővel.
* A MediusFlow felhasználói fiókjának rendszergazdai hozzáférési jogosultságokkal kell rendelkezniük a konfiguráció végrehajtásához a MediusFlow belül.
* Azok a vállalatok, amelyek a MediusFlow-bérlőhöz vannak hozzáadva, ahol a felhasználókat üzembe kell helyezni.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a MediusFlow között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>2. lépés MediusFlow konfigurálása az Azure AD-vel való kiépítés támogatásához

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktiválja a Microsoft 365 alkalmazást a MediusFlow-n belül
Először engedélyezze az Azure AD-bejelentkezés és az Azure AD konfigurációs funkciójának a MediusFlow-n belüli elérését az alábbi lépések végrehajtásával:

#### <a name="user-login"></a>Felhasználói bejelentkezés
Ha engedélyezni szeretné a bejelentkezést Microsoft 365/Azure AD-re, tekintse át a következőt: [this] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) cikk.

#### <a name="user-transfer-configuration"></a>Felhasználói átvitel konfigurációja
Ha engedélyezni szeretné az Azure AD-ből való kiépítési felhasználók konfigurációs portálját, olvassa el [ezt](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) a cikket.

#### <a name="configure-user-provisioning"></a>Felhasználók átadásának konfigurálása

1.  Jelentkezzen be a [MediusFlow felügyeleti konzolra](https://office365.cloudapp.mediusflow.com/) a bérlő azonosítójának megadásával.

    ![Hitelesítés](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Ellenőrizze a MediusFlow-vel való kapcsolatokat.

    ![Ellenőrzés](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Adja meg az Azure AD-bérlő AZONOSÍTÓját.

    ![adja meg a bérlő AZONOSÍTÓját](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   További tudnivalókat a [Gyakori kérdések](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) között találhat.

4. Mentse a konfigurációt.

    ![Hitelesítés](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Válassza a felhasználó kiépítés lehetőséget, majd kattintson **az OK**gombra.

    ![Hitelesítés](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Kattintson a **titkos kulcs előállítása**elemre. Másolja és mentse ezt az értéket. Ez az érték a MediusFLow alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.

    ![Hitelesítés](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Kattintson **az OK gombra**.

    ![Hitelesítés](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Ahhoz, hogy a felhasználók a szerepkörök, a vállalatok és egyéb általános konfigurációk előre definiált készletével importálva legyenek a MediusFlow-ben, először konfigurálnia kell. Először adja hozzá a konfigurációt az **Új konfiguráció hozzáadása**lehetőségre kattintva.

    ![Konfigurációk felhasználói](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Adja meg a felhasználók alapértelmezett beállításait. Ebben a nézetben az alapértelmezett attribútumot lehet beállítani. Ha a normál beállítások rendben vannak, elég, ha csak egy érvényes vállalat nevét adja meg. Mivel ezek a konfigurációs beállítások beolvasása a Mediusflow-ből történik, először konfigurálni kell őket. További információt a jelen cikk **Előfeltételek** című szakaszában talál.

    ![Konfigurációk felhasználói](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. A felhasználói konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

    ![Konfigurációk felhasználói](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. A felhasználó kiépítési hivatkozásának beszerzéséhez kattintson az **scim másolása hivatkozásra**. Másolja és mentse ezt az értéket. Ez az érték a MediusFLow alkalmazás **kiépítés** lapjának **bérlői URL-címe** mezőjében szerepel a Azure Portal.
 
    ![Konfigurációk felhasználói](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>3. lépés MediusFlow hozzáadása az Azure AD Application Galleryből

Vegyen fel MediusFlow az Azure AD-alkalmazás-katalógusból a MediusFlow való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a MediusFlow-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok MediusFlow való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, akkor azt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel hozzá az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a MediusFlow 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a MediusFlow az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **MediusFlow**lehetőséget.

    ![Az MediusFlow hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlő URL **-címében**korábban LEKÉRT bérlői URL-cím értékét. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban**lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a MediusFlow. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a MediusFlow-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

      ![kiépítési](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Válassza a **Mentés** lehetőséget.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a MediusFlow**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő MediusFlow. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MediusFlow felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a MediusFlow API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |név. displayName|Sztring|
   |Active|Logikai|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |név. formázott|Sztring|
   |externalID|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|


10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a MediusFlow**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált MediusFlow az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MediusFlow tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalID|Sztring|
      |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás MediusFlow való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a MediusFlow kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
