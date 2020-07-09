---
title: 'Oktatóanyag: a 8x8 konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a 8x8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: b200aabff1231cdf383d30ad2e671c4b46ca91bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196212"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés 8x8 konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a 8x8 Configuration Manager és az Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [8x8](https://www.8x8.com) . A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a 8x8-ben
> * Felhasználók eltávolítása a 8x8-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a 8x8 között
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) a 8x8-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* Bármely szint 8x8 X sorozatának előfizetése.
* Egy rendszergazdai engedéllyel rendelkező 8x8 felhasználói fiók [Configuration Managerban](https://vo-cm.8x8.com).
* [Az Azure ad-vel való egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) már konfigurálva van.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a 8x8 között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>2. lépés 8x8 konfigurálása az Azure AD-vel való kiépítés támogatásához

Ez a szakasz végigvezeti a 8x8 konfigurálásának lépésein az Azure AD-vel való kiépítés támogatásához.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>A felhasználó kiépítési hozzáférési jogkivonatának konfigurálása a 8x8-Configuration Managerban:

1. Jelentkezzen be [Configuration Managerba](https://vo-cm.8x8.com). Válassza az **Identitáskezelés**lehetőséget.

   ![Identitáskezelés](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Kattintson a **felhasználó kiépítési információinak megjelenítése** hivatkozásra a jogkivonat létrehozásához.

   ![Felhasználó kiépítés megjelenítése](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Másolja a **8x8 URL-címét** és a **8X8 API-jogkivonat** értékeit. Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lesznek megadva a 8x8 alkalmazás kiépítés lapján a Azure Portalban.

   ![URL-cím és jogkivonat másolása](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>3. lépés 8x8 hozzáadása az Azure AD Application Galleryből

Vegyen fel 8x8 az Azure AD-alkalmazás-katalógusból a 8x8 való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a 8x8 az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ez az egyszerűbb beállítás, amelyet a legtöbb felhasználó használ.

Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok 8x8 való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a 8x8 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy 8x8 alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a 8x8 az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Minden alkalmazás panel](./media/8x8-provisioning-tutorial/all-applications.png)

2. Az alkalmazások listában válassza a **8x8**lehetőséget.

    ![Az 8x8 hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** fület. kattintson az első **lépések**elemre.

    ![Kiépítés lap](common/provisioning.png)

   ![Első lépések panel](./media/8x8-provisioning-tutorial/get-started.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban másolja a **8x8 URL-címét** a Configuration Manager a **bérlői URL-címre**. Másolja a **8X8 API-tokent** Configuration Managerről **titkos jogkivonatba**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a 8x8. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a 8x8-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Kiépítés](./media/8x8-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő 8x8. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 8x8 felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a 8x8 API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Jegyzetek|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|A Felhasználónév és az összevonási azonosító beállítása|
   |externalId|Sztring||
   |Active|Logikai||
   |cím|Sztring||
   |e-mailek [type EQ "work"]. Value|Sztring||
   |név. givenName|Sztring||
   |név. familyName|Sztring||
   |phoneNumbers [type EQ "Mobile"]. Value|Sztring|Személyes kapcsolattartási szám|
   |phoneNumbers [type EQ "work"]. Value|Sztring|Személyes kapcsolattartási szám|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring||
   |urn: IETF: params: scim: sémák: kiterjesztés: 8x8:1.1: felhasználó: hely|Sztring|A felhasználó létrehozása után nem frissíthető|
   |területi beállítás|Sztring|Alapértelmezés szerint nincs leképezve|
   |timezone|Sztring|Alapértelmezés szerint nincs leképezve|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás 8x8 való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a 8x8 kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
2. Tekintse meg a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
