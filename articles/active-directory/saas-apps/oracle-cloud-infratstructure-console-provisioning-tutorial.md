---
title: 'Oktatóanyag: az Oracle felhőalapú infrastruktúra-konzol konfigurálása a felhasználók automatikus kiépítéséhez Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az Oracle felhőalapú infrastruktúra-konzolra.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94f5be93d95cc1c524e6db464cac68879ed0b0e7
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926790"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Oktatóanyag: az Oracle felhőalapú infrastruktúra-konzol konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket az Oracle Cloud Infrastructure konzolon és a Azure Active Directory (Azure AD) a felhasználók automatikus kiépítésének konfigurálásához kell végrehajtania. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az [Oracle felhőalapú infrastruktúra-konzolon](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Oracle Cloud Infrastructure konzolon
> * Távolítsa el a felhasználókat az Oracle Cloud Infrastructure konzolon, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Oracle felhőalapú infrastruktúra konzolja között
> * Csoportok és csoporttagságok kiépítése az Oracle Cloud Infrastructure konzolon
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) az Oracle Cloud Infrastructure Console-konzolra (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy Oracle-alapú felhőalapú infrastruktúra-vezérlési [bérlő](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Egy felhasználói fiók az Oracle Cloud Infrastructure-ben rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és az Oracle felhőalapú infrastruktúra-konzolja milyen adatleképezési](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leképezéseket. 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>2. lépés Az Oracle felhőalapú infrastruktúra konzoljának konfigurálása az Azure AD-vel való kiépítés támogatására

1. Jelentkezzen be az Oracle felhőalapú infrastruktúra konzoljának felügyeleti portálján. A képernyő bal felső sarkában navigáljon az **Identity >-összevonás**elemre.

    ![Oracle-rendszergazda](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Kattintson a lapon megjelenő URL-címre az Oracle Identity Cloud Service Console mellett.

    ![Oracle URL-cím](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Az új identitás-szolgáltató létrehozásához kattintson az **identitás hozzáadása** lehetőségre. Mentse a identitásszolgáltató-azonosítót a bérlői URL-cím részeként való használatra. Az **alkalmazások** lap melletti plusz ikonra kattintva hozzon létre egy OAuth-ügyfelet, és adja meg a IDCS Identity domain Administrator approle példányoknak.

    ![Oracle Cloud ikon](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Az alkalmazás konfigurálásához kövesse az alábbi képernyőképeket. Ha a konfiguráció elkészült, kattintson a **Mentés**gombra.

    ![Oracle-konfiguráció](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle-jogkivonat kiállítási szabályzata](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Az alkalmazás konfigurációk lapján bontsa ki az **általános információ** lehetőséget az ügyfél-azonosító és az ügyfél titkos kódjának lekéréséhez.

    ![Oracle-jogkivonat létrehozása](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. A titkos token Base64-kódolásához az ügyfél-azonosító és az ügyfél titkos kulcsát a következő formátumban kell megadni **: Client Secret**(ügyfél-azonosító). Mentse a titkos jogkivonatot. Ez az érték jelenik meg az Oracle Cloud Infrastructure Console alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében a Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3. lépés Oracle Cloud Infrastructure-konzol hozzáadása az Azure AD Application Galleryből

Vegye fel az Oracle Cloud Infrastructure-konzolt az Azure AD-alkalmazás-katalógusból az Oracle felhőalapú infrastruktúra-konzolra való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az Oracle felhőalapú infrastruktúra-konzolt az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel az Oracle Cloud Infrastructure-konzolhoz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása az Oracle felhőalapú infrastruktúra-konzolra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Az Oracle felhőalapú infrastruktúra konzoljának automatikus kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Oracle Cloud Infrastructure Console**elemet.

    ![Az Oracle felhőalapú infrastruktúra konzoljának hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** a formátumban `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Például: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban**lett lekérve. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak az Oracle Cloud Infrastructure-konzolhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az Oracle felhőalapú infrastruktúra-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![kiépítési](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása Oracle Cloud Infrastructure-konzolra**lehetőséget.

9. Tekintse át az Azure AD-ból az Oracle Cloud Infrastructure Console-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Cloud Infrastructure Console felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy az Oracle Cloud Infrastructure Console API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |userName (Felhasználónév)|Sztring|
      |Active|Logikai|
      |cím|Sztring|
      |e-mailek [type EQ "work"]. Value|Sztring|
      |preferredLanguage|Sztring|
      |név. givenName|Sztring|
      |név. familyName|Sztring|
      |címek [type EQ "work"]. formázott|Sztring|
      |címek [típus EQ "work"]. helység|Sztring|
      |címek [típus EQ "work"]. régió|Sztring|
      |címek [type EQ "work"]. irányítószám|Sztring|
      |címek [type EQ "work"]. Country|Sztring|
      |címek [type EQ "work"]. streetAddress|Sztring|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
      |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
      |urn: IETF: params: scim: sémák: Oracle: IDCs: bővítmény: felhasználó: felhasználó: bypassNotification|Logikai|
      |urn: IETF: params: scim: sémák: Oracle: IDCs: bővítmény: felhasználó: felhasználó: isFederatedUser|Logikai|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Oracle Cloud Infrastructure-konzolra**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az Oracle felhő-infrastruktúra konzolra az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használhatók az Oracle Cloud Infrastructure konzolon lévő csoportok egyeztetéséhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást az Oracle felhőalapú infrastruktúra konzolján, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni az Oracle felhőalapú infrastruktúra-konzolra. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
* Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
* Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
