---
title: 'Oktatóanyag: a Gyutacs konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Gyutacs.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: aacaa8ca7e0cd15b34f29479d38d7bc8d95001de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253698"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Gyutacs konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Gyutacs és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [Gyutacs](https://www.fuze.com/). A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Gyutacs-ben
> * Felhasználók eltávolítása a Gyutacs-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Gyutacs között
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/fuze-tutorial) a Gyutacs-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* [Egy Gyutacs-bérlő](https://www.fuze.com/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Gyutacs-ben.


## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Gyutacs között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>2. lépés Gyutacs konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Azure AD-vel való automatikus Gyutacs konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Gyutacs-on. 

1. Először vegye fel a kapcsolatot a Gyutacs képviselőjével a következő szükséges adatokkal:

    * A vállalatnál jelenleg használt Gyutacs-termékek listája.
    * A vállalat helyeihez tartozó Helykód listája.
    * A vállalathoz tartozó részleg-kódok listája.

2. Ezeket a SKU-ket és kódokat a Gyutacs-szerződésben és a konfigurációs dokumentumokban, vagy a Gyutacs képviselőjével is megtalálhatja.

3. A követelmények beérkezése után a Gyutacs-képviselő megadja az integráció engedélyezéséhez szükséges Gyutacs-hitelesítési jogkivonatot. Ez az érték a Gyutacs alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>3. lépés Gyutacs hozzáadása az Azure AD Application Galleryből

Vegyen fel Gyutacs az Azure AD-alkalmazás-katalógusból a Gyutacs való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Gyutacs az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat rendel a Gyutacs, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználók értékre van állítva, akkor ezt úgy szabályozhatja, hogy hozzárendel egy vagy két felhasználót az alkalmazáshoz. Ha a hatókör minden felhasználóra van beállítva, megadhat egy [attribútum-alapú hatóköri szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Gyutacs 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Gyutacs alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Gyutacs az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Gyutacs**lehetőséget.

    ![Az Gyutacs hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a scim-hitelesítési jogkivonat** értékét, amelyet korábban a **bérlői URL-cím** és a **titkos jogkivonat**Gyutacs-képviselőjétől kapott le. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Gyutacs. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Gyutacs-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL-token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Gyutacs**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Gyutacs. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Gyutacs felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |Active|Logikai|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás Gyutacs való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Gyutacs kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Gyutacs a **jogosultságok**nevű egyéni scim-attribútumokat támogatja. Ezek az attribútumok csak akkor hozhatók létre és nem frissíthetők. 

## <a name="change-log"></a>Változási napló

* 06/15/2020 – 10 kérelem/másodperc értékre igazított integrációs korlát

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).