---
title: 'Oktatóanyag: a SolarWinds Service Desk (korábban Samanage) konfigurálása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a SolarWinds Service Deskbe (korábban Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707266"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: a SolarWinds Service Desk (korábban Samanage) konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a SolarWinds Service Desk (korábban Samanage) és a Azure Active Directory (Azure AD) használatával kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [SolarWinds](https://www.samanage.com/pricing/) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrálás az új SolarWinds Service Desk-alkalmazásba

Ha már rendelkezik a SolarWinds Service Desktel való integrációval, tekintse meg a következő, a közelgő változásokról szóló szakaszt. Ha első alkalommal állítja be a SolarWinds-szolgáltatást, kihagyhatja ezt a szakaszt, és áthelyezheti a **támogatott funkciókra**.

#### <a name="whats-changing"></a>Mi változik?

* Változások az Azure AD-oldalon: a felhasználók Samange való kiépítésének engedélyezési módszere történelmileg **Egyszerű hitelesítés**. Hamarosan látni fogja, hogy az engedélyezési módszer **hosszú élettartamú titkos jogkivonatra**változott.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Mit kell tennem a meglévő egyéni integráció új alkalmazásba való átállításához?

Ha rendelkezik egy meglévő SolarWinds-szolgáltatással, amely érvényes rendszergazdai hitelesítő adatokkal rendelkezik, **nincs szükség beavatkozásra**. A rendszer automatikusan áttelepíti az ügyfeleket az új alkalmazásba. Ez a folyamat teljesen a színfalak mögött történik. Ha a meglévő hitelesítő adatok lejárnak, vagy ha újra engedélyezni szeretné az alkalmazáshoz való hozzáférést, hosszú élettartamú titkos jogkivonatot kell létrehoznia. Új jogkivonat létrehozásához tekintse meg a jelen cikk 2. lépését.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Honnan tudhatom meg, hogy az alkalmazás migrálása megtörtént-e? 

Az alkalmazás migrálása után a **rendszergazdai hitelesítő adatok** szakaszban a **rendszergazdai Felhasználónév** és a **rendszergazdai jelszó** mezők egyetlen **titkos jogkivonat** mezővel lesznek lecserélve.

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása a SolarWinds Service Desk-ben
> * Távolítsa el a felhasználókat a SolarWinds Service Desk szolgáltatásban, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a SolarWinds Service Desk között
> * Csoportok és csoporttagságok kiépítése a SolarWinds Service Desk szolgáltatásban
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) a SolarWinds Service Desk szolgáltatásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* A professzionális csomaggal rendelkező [SolarWinds Service Desk-bérlő](https://www.samanage.com/pricing/) .
* Egy felhasználói fiók a SolarWinds Service Desk-ben rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a SolarWinds Service Desk milyen adatleképezési](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)leképezéseket biztosít. 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>2. lépés A SolarWinds Service Desk konfigurálása az Azure AD-vel való kiépítés támogatásához

Titkos jogkivonat hitelesítéshez való létrehozásával kapcsolatban lásd az [oktatóanyag-tokenek hitelesítése az API-integrációhoz](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)című témakört.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>3. lépés A SolarWinds Service Desk hozzáadása az Azure AD Application Galleryből

Adja hozzá a SolarWinds Service Deskt az Azure AD-alkalmazás-katalógusból a SolarWinds Service Desk üzembe helyezésének kezeléséhez. Ha korábban már beállította a SolarWinds Service Desk egyszeri bejelentkezést, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel a SolarWinds Service Desk szolgáltatáshoz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>5. lépés Automatikus felhasználó-kiépítés konfigurálása a SolarWinds Service Desk szolgáltatáshoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a SolarWinds Service Desk számára az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **SolarWinds Service Desk**elemet.

3. Válassza ki a **kiépítés** lapot.

    ![A kiépítési lapot kijelölő képernyőkép.](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Képernyőfelvétel: a kiépítési mód beállítása automatikus.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.samanage.com` meg a **bérlői URL-címet**.  Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban**lett lekérve. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a SolarWinds Service Desk szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a SolarWinds Service Desk-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a kiválasztott tesztelési gomb megjelenítése.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a SolarWinds Service Desk-** be lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található SolarWinds. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a SolarWinds Service Desk felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a SolarWinds Service Desk API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      ![Samange felhasználói leképezések](./media/samanage-provisioning-tutorial/user-attributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget a SolarWinds Service Desk**elemre.

11. Tekintse át az Azure AD-ből szinkronizált SolarWinds az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a SolarWinds Service deskben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      ![Samange csoport-hozzárendelések](./media/samanage-provisioning-tutorial/group-attributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a SolarWinds Service Desk számára, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a SolarWinds szeretné kiépíteni a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Összekötő korlátozásai

Ha bejelöli a **minden felhasználó és csoport szinkronizálása** lehetőséget, és beállítja a SolarWinds Service Desk **roles** attribútum értékét, akkor az **alapértelmezett érték alatti értéket, ha a null (nem kötelező)** mezőt a következő formátumban kell megadni:

- {"displayName": "role"}, ahol a szerepkör a kívánt alapértelmezett érték.

## <a name="change-log"></a>Változási napló

* 09/14/2020 – a vállalat nevét két SaaS-oktatóanyagban módosította a Samanage-ről a SolarWinds Service deskre (korábban Samanage) https://github.com/ravitmorales .
* 04/22/2020 – frissített engedélyezési módszer az alapszintű hitelesítésből a hosszú élettartamú titkos jogkivonatba.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
