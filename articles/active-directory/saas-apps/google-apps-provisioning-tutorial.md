---
title: 'Oktatóanyag: a G Suite konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a G Suite-be.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 27e34a66bb6dfa642b84bd8997b2b02c4981788e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551553"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: a G Suite konfigurálása automatikus felhasználók kiépítési feladatokhoz

Ez az oktatóanyag a G Suite és a Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket ismerteti az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti és kiosztja a [G Suite](https://gsuite.google.com/) -hoz tartozó felhasználókat és csoportokat az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

> [!NOTE]
> A G Suite-összekötőt nemrég, október 2019-én frissítették. A G Suite-összekötőn végrehajtott módosítások a következők:
>
> * További támogatás a G Suite-felhasználók és-csoportok további attribútumaihoz.
> * A G Suite-cél attribútumainak neve megegyezik az [itt](https://developers.google.com/admin-sdk/directory)definiált értékekkel.
> * Az alapértelmezett attribútum-hozzárendelések frissítve.

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása G Suite-ban
> * Ha már nincs szükség hozzáférésre, távolítsa el a felhasználókat a G Suite-ban
> * A felhasználói attribútumok szinkronizálása az Azure AD és a G Suite között
> * Csoportok és csoporttagságok kiépítése G Suite-ban
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) a G Suite-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* [G Suite-bérlő](https://gsuite.google.com/pricing.html)
* A G Suite felhasználói fiókja rendszergazdai jogosultságokkal.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg az [Azure ad és a G Suite közötti leképezési](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)adatok körét. 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>2. lépés A G Suite konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt konfigurálja a G Suite-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a SCIM-létesítést a G Suite-ban.

1. Jelentkezzen be a [G Suite felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiókjával, majd válassza a **Biztonság**elemet. Ha nem látja a hivatkozást, akkor előfordulhat, hogy a képernyő alján található **további vezérlők** menü alatt rejtve van.

    ![G Suite biztonság](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. A **Biztonság** lapon válassza az **API-hivatkozás**lehetőséget.

    ![G Suite API](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Válassza az **API-hozzáférés engedélyezése**lehetőséget.

    ![G Suite API engedélyezve](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Minden, a G Suite-nak kiépíteni kívánt felhasználó számára az **Azure ad-** beli felhasználónevét egy egyéni tartományhoz kell kötni. Például az így megjelenő felhasználóneveket a bob@contoso.onmicrosoft.com G Suite nem fogadja el. Másrészről bob@contoso.com elfogadjuk. A meglévő felhasználó tartományát az [itt](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)található útmutatás alapján módosíthatja.

4. Miután hozzáadta és ellenőrizte a kívánt egyéni tartományokat az Azure AD-vel, újra ellenőriznie kell azokat a G Suite-val. A G Suite tartományának ellenőrzéséhez tekintse meg a következő lépéseket:

    a. A [G Suite felügyeleti konzolon](https://admin.google.com/)válassza a **tartományok**lehetőséget.

    ![G Suite-tartományok](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Válassza **a tartomány hozzáadása vagy a tartományi alias**lehetőséget.

    ![G Suite tartomány hozzáadása](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Válassza a **másik tartomány hozzáadása**lehetőséget, majd írja be a hozzáadni kívánt tartomány nevét.

    ![G Suite – másik hozzáadása](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Válassza **a Folytatás lehetőséget, és ellenőrizze a tartomány tulajdonjogát**. Ezután kövesse a lépéseket annak ellenőrzéséhez, hogy a tartománynév tulajdonosa-e. A tartomány a Google-vel való ellenőrzésével kapcsolatos részletes utasításokért lásd: [a hely tulajdonjogának ellenőrzése](https://support.google.com/webmasters/answer/35179).

    e. Ismételje meg az előző lépéseket a G Suite-ba felvenni kívánt további tartományok esetében.

5. Ezután határozza meg, hogy melyik rendszergazdai fiókot szeretné használni a G Suite-ban a felhasználók üzembe helyezésének kezeléséhez. Navigáljon a **rendszergazdai szerepkörök**elemre.

    ![G Suite-rendszergazda](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. A fiók **rendszergazdai szerepköréhez** szerkessze az adott szerepkörhöz tartozó **jogosultságokat** . Ügyeljen arra, hogy az összes **rendszergazdai API-jogosultságot** engedélyezze, hogy ez a fiók használható legyen az üzembe helyezéshez.

    ![A G Suite rendszergazdai jogosultságai](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3. lépés G csomag hozzáadása az Azure AD Application Galleryből

Vegye fel a G Suite-t az Azure AD-alkalmazás-katalógusból a G Suite-ba való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a G Suite-ot az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel a G Suite-hoz, az **alapértelmezett hozzáféréstől**eltérő szerepkört kell kijelölnie. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5. lépés Automatikus felhasználó-kiépítés konfigurálása a G Suite-hoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!NOTE]
> A G Suite címtár API-végpontjának megismeréséhez tekintse meg a [Directory API](https://developers.google.com/admin-sdk/directory)-t.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>A G Suite automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget. A felhasználóknak be kell jelentkezniük a portal.azure.com, és nem fogják tudni használni a aad.portal.azure.com

    ![Vállalati alkalmazások panel](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Minden alkalmazás panel](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Az alkalmazások listában válassza a **G Suite**elemet.

    ![A G Suite-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** fület. kattintson az első **lépések**elemre.

    ![Kiépítés lap](common/provisioning.png)

      ![Első lépések panel](./media/google-apps-provisioning-tutorial/get-started.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés**elemre. A rendszer átirányítja az új böngészőablakban található Google-engedélyezési párbeszédpanelre.

      ![G Suite engedélyezése](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Győződjön meg arról, hogy az Azure AD-beli engedélyeket szeretné megadni a G Suite-bérlő módosításához. Válassza ki az **Elfogadás** lehetőséget.

     ![G Suite bérlői hitelesítés](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad tud csatlakozni a G Suite-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a G Suite-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra. Ezután próbálja megismételni az **Engedélyezés** lépést.

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Válassza a **Mentés** lehetőséget.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése**lehetőséget.

9. Tekintse át az Azure AD-ból a G Suite-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a G Suite API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |primaryEmail|Sztring|
   |kapcsolatok. [type EQ "Manager"]. Value|Sztring|
   |név. familyName|Sztring|
   |név. givenName|Sztring|
   |függesztve|Sztring|
   |externalIds. [type EQ "Custom"]. Value|Sztring|
   |externalIds. [type EQ "szervezet"]. Value|Sztring|
   |címek. [type EQ "work"]. Country|Sztring|
   |címek. [type EQ "work"]. streetAddress|Sztring|
   |címek. [írja be a következőt: EQ "work"]. régió|Sztring|
   |címek. [írja be a következőt: EQ "work"]. helység|Sztring|
   |címek. [type EQ "work"]. irányítószám|Sztring|
   |e-mailek. [írja be a következőt: EQ "work"]. címe|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. részleg|Sztring|
   |szervezetek. [type EQ "work"]. title|Sztring|
   |phoneNumbers. [type EQ "work"]. Value|Sztring|
   |phoneNumbers. [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers. [type EQ "work_fax"]. Value|Sztring|
   |e-mailek. [írja be a következőt: EQ "work"]. címe|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. részleg|Sztring|
   |szervezetek. [type EQ "work"]. title|Sztring|
   |phoneNumbers. [type EQ "work"]. Value|Sztring|
   |phoneNumbers. [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers. [type EQ "work_fax"]. Value|Sztring|
   |címek. [type EQ "Home"]. Country|Sztring|
   |címek. [type EQ "Home"]. formázott|Sztring|
   |címek. [type EQ "Home"]. helység|Sztring|
   |címek. [type EQ "Home"]. irányítószám|Sztring|
   |címek. [type EQ "Home"]. régió|Sztring|
   |címek. [type EQ "Home"]. streetAddress|Sztring|
   |címek. [type EQ "other"]. Country|Sztring|
   |címek. [type EQ "other"]. formázott|Sztring|
   |címek. [írja be a következőt: EQ "other"]. helység|Sztring|
   |címek. [type EQ "other"]. irányítószám|Sztring|
   |címek. [type EQ "other"]. régió|Sztring|
   |címek. [type EQ "other"]. streetAddress|Sztring|
   |címek. [type EQ "work"]. formázott|Sztring|
   |changePasswordAtNextLogin|Sztring|
   |e-mailek. [type EQ "Home"]. címe|Sztring|
   |e-mailek. [type EQ "other"]. címe|Sztring|
   |externalIds. [írja be a következőt: EQ "account"]. Value|Sztring|
   |externalIds. [type EQ "Custom"]. customType|Sztring|
   |externalIds. [type EQ "Customer"]. Value|Sztring|
   |externalIds. [type EQ "login_id"]. Value|Sztring|
   |externalIds. [type EQ "Network"]. Value|Sztring|
   |nemre. Type|Sztring|
   |GeneratedImmutableId|Sztring|
   |Azonosító|Sztring|
   |IMS. [type EQ "Home"]. protokoll|Sztring|
   |IMS. [type EQ "other"]. Protocol|Sztring|
   |IMS. [írja be a következőt: EQ "work"]. protokoll|Sztring|
   |includeInGlobalAddressList|Sztring|
   |ipWhitelisted|Sztring|
   |szervezetek. [type EQ "School"]. costCenter|Sztring|
   |szervezetek. [type EQ "iskola"]. részleg|Sztring|
   |szervezetek. [type EQ "School"]. domain|Sztring|
   |szervezetek. [type EQ "School"]. fullTimeEquivalent|Sztring|
   |szervezetek. [írja be a következőt: EQ "School"]. hely|Sztring|
   |szervezetek. [type EQ "School"]. név|Sztring|
   |szervezetek. [type EQ "iskola"]. szimbólum|Sztring|
   |szervezetek. [type EQ "iskola"]. title|Sztring|
   |szervezetek. [type EQ "work"]. costCenter|Sztring|
   |szervezetek. [type EQ "work"]. domain|Sztring|
   |szervezetek. [type EQ "work"]. fullTimeEquivalent|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. hely|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. név|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. szimbólum|Sztring|
   |OrgUnitPath|Sztring|
   |phoneNumbers. [type EQ "Home"]. Value|Sztring|
   |phoneNumbers. [type EQ "other"]. Value|Sztring|
   |weboldalak. [type EQ "Home"]. Value|Sztring|
   |weboldalak. [type EQ "other"]. Value|Sztring|
   |weboldalak. [type EQ "work"]. Value|Sztring|
   

10. A **leképezések** szakaszban válassza ki a **Azure Active Directory csoportok kiépítése**lehetőséget.

11. Tekintse át az Azure AD-ból a G Suite-be szinkronizált csoport attribútumait az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban a frissítési műveletekhez tartozó csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |e-mail|Sztring|
      |Tagok|Sztring|
      |name|Sztring|
      |leírás|Sztring|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. A G Suite-hoz készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a G Suite-hoz kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Settings (beállítások** ) szakaszban a megfelelő értékeket választja ki a **hatókörben** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

> [!NOTE]
> Ha a felhasználók már rendelkeznek egy meglévő személyes/fogyasztói fiókkal az Azure AD-felhasználó e-mail-címével, akkor előfordulhat, hogy a címtár-szinkronizálás végrehajtása előtt a Google átadási eszköz használatával feloldható néhány probléma.

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
