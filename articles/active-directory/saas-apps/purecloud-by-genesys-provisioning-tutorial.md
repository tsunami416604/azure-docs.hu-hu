---
title: 'Oktatóanyag: a PureCloud konfigurálása a Genesys-ben a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Genesys által PureCloud.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370680"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Oktatóanyag: a PureCloud konfigurálása a Genesys-ben a felhasználók automatikus kiépítési felállításához

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Genesys és Azure Active Directory (Azure AD) mindkét PureCloud végre kell hajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és lefoglalja a felhasználókat és a csoportokat, hogy az Azure AD-létesítési szolgáltatással [PureCloud az Genesys](https://www.genesys.com) . A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a PureCloud-ben Genesys szerint
> * Felhasználók eltávolítása a PureCloud-ben a Genesys, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a PureCloud között Genesys szerint
> * Csoportok és csoporttagságok kiépítése a PureCloud-ben Genesys szerint
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) a Genesys PureCloud (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy PureCloud- [szervezet](https://help.mypurecloud.com/?p=81984).
* Egy OAuth-ügyfél létrehozásához [szükséges engedélyekkel](https://help.mypurecloud.com/?p=24360) rendelkező felhasználó.

## <a name="step-1-plan-your-provisioning-deployment"></a>1\. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a PureCloud milyen adatleképezést Genesys alapján](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>2\. lépés A PureCloud konfigurálása a Genesys használatával az Azure AD-vel való kiépítés támogatásához

1. Hozzon létre egy [OAuth-ügyfelet](https://help.mypurecloud.com/?p=188023) , amely konfigurálva van a PureCloud-szervezetben.
2. Jogkivonat létrehozása [a OAuth-ügyféllel](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Ha szeretné, hogy a csoporttagság automatikusan kiépíthető legyen a PureCloud-on belül, a PureCloud azonos névvel kell [létrehoznia](https://help.mypurecloud.com/?p=52397) a csoportot az Azure ad-ben.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3\. lépés PureCloud hozzáadása a Genesys-ből az Azure AD Application Galleryből

Az Azure AD Genesys származó PureCloud hozzáadásával megkezdheti a Genesys által PureCloud kiépítés kezelését. Ha korábban már beállította a Genesys for SSO PureCloud, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4\. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Ha a Genesys által PureCloud felhasználók és csoportok hozzárendelését végzi, az **alapértelmezett hozzáféréstől**eltérő szerepkört kell kijelölnie. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5\. lépés Az automatikus felhasználó-kiépítés konfigurálása a Genesys-PureCloud 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a PureCloud számára az Azure AD Genesys-ben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PureCloud a Genesys alapján**lehetőséget.

    ![A PureCloud by Genesys hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a PureCloud a Genesys API URL-cím és a OAuth-jogkivonat alapján a **bérlői URL** -cím és a **titkos jogkivonat** mezőiben. Az API URL-címe `{{API Url}}/api/v2/scim/v2`lesz strukturálva, a PureCloud-régió API URL-címének használatával a [PureCloud fejlesztői központból](https://developer.mypurecloud.com/api/rest/index.html). Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a PureCloud-hez Genesys. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Genesys-fiók PureCloud rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![kiépítési](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók PureCloud a Genesys alapján**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakasz Genesys PureCloud. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PureCloud által a Genesys for Update műveletekhez használt felhasználói fiókokkal egyeznek meg. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a Genesys API által biztosított PureCloud támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

     |Attribútum|Típus|
     |---|---|
     |userName|Sztring|
     |aktív|Logikai|
     |displayName|Sztring|
     |e-mailek [típus eq "work"] .value|Sztring|
     |Cím|Sztring|
     |phoneNumbers [típus eq "mobileszköz"] .value|Sztring|
     |phoneNumbers [típus eq "work"] .value|Sztring|
     |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
     |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy a Genesys PureCloud**.

11. Tekintse át az Azure AD-ból szinkronizált PureCloud az **attribútum-hozzárendelési** szakaszban található Genesys alapján. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PureCloud által a Genesys for Update műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra. A Genesys által PureCloud nem támogatja a csoportok létrehozását vagy törlését, és csak a csoportok frissítését támogatja.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tag|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a PureCloud számára a Genesys, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Genesys PureCloud szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6\. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
* Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
* Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
