---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés automatikus méretezésének konfigurálása Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a nagyításhoz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80384075"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Oktatóanyag: nagyítás konfigurálása automatikus felhasználó-kiépítési művelethez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a nagyítás és a Azure Active Directory (Azure AD) szolgáltatásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és a csoportokat az Azure AD-létesítési [szolgáltatás használatával.](https://zoom.us/pricing/) A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a nagyításban
> * A felhasználók eltávolítása a nagyításban, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a nagyítás között
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) a nagyításhoz (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* [Egy nagyítási bérlő](https://zoom.us/pricing).
* Egy felhasználói fiók a nagyításban rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a nagyítás között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>2. lépés A nagyítás beállítása az Azure AD-vel való kiépítés támogatására

1. Jelentkezzen be a [nagyítási felügyeleti konzolba](https://zoom.us/signin). A bal oldali navigációs panelen navigáljon a **fejlesztők speciális > nagyításához** .

    ![Nagyítási integrációk](media/zoom-provisioning-tutorial/zoom01.png)

2. Navigáljon a **kezelés** elemre a lap jobb felső sarkában. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom02.png)

3. Navigáljon a létrehozott Azure AD-alkalmazáshoz. 
    
    ![Alkalmazás nagyítása](media/zoom-provisioning-tutorial/zoom03.png)

4. Válassza ki az **alkalmazás hitelesítő adatait** a bal oldali navigációs ablaktáblán.

    ![Alkalmazás nagyítása](media/zoom-provisioning-tutorial/zoom04.png)

5. Másolja és mentse az **JWT tokent**. Ez az érték a Azure Portalban a nagyítási alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében lesz megadva. Ha új, nem lejáró tokenre van szüksége, akkor újra kell konfigurálnia a lejárati időt, amely automatikusan létrehoz egy új tokent. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>3. lépés Nagyítás hozzáadása az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusban található nagyítás hozzáadásával megkezdheti a kiépítés kezelését a nagyításhoz. Ha korábban már beállította az egyszeri bejelentkezést (SSO), ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* A felhasználók és csoportok nagyításhoz való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a nagyításhoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Az automatikus felhasználó-kiépítés beállítása a nagyításhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Nagyítás**lehetőséget.

    ![A nagyítási hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg `https://api.zoom.us/scim` a **bérlői URL-címet**. Adja meg a **titkos jogkivonatban**korábban lekért **JWT-jogkivonat** értékét. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a nagyításhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a nagyítási fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Nagyítás kiépítés](./media/zoom-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók nagyításhoz**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakasz nagyításához. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt nagyítási műveletek felhasználói fiókjainak egyeztetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a nagyítási API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |Active|Logikai|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |e-mailek [type EQ "work"]|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a nagyításhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a nagyításhoz, és válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Összekötő korlátozásai
* A nagyítás csak legfeljebb 9 999 alapszintű felhasználót tesz lehetővé.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
