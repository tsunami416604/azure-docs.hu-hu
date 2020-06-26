---
title: 'Oktatóanyag: a 15Five konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 5752715b447965c2aad99e170217bc0adce94a78
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367631"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés 15Five konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a 15Five és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [15Five](https://www.15five.com/pricing/). A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával.

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a 15Five-ben
> * Felhasználók eltávolítása a 15Five-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a 15Five között
> * Csoportok és csoporttagságok kiépítése a 15Five-ben
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/15five-tutorial) a 15Five-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) .
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* [Egy 15Five-bérlő](https://www.15five.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a 15Five-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a 15Five között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>2. lépés 15Five konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Azure AD-vel való automatikus 15Five konfigurálása előtt engedélyeznie kell a SCIM-létesítést a 15Five-on.

1. Jelentkezzen be a [15Five felügyeleti konzolra](https://my.15five.com/). Navigáljon a **funkciók > integrációk elemre**.

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/integration.png)

2.  Kattintson a **SCIM 2,0**elemre.

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/image00.png)

3.  Navigáljon a **scim-integráció > OAuth-token létrehozásához**.

    ![15Five-SCIM hozzáadása](media/15five-provisioning-tutorial/image02.png)

4.  Másolja a **SCIM 2,0 alap URL-cím** és a **hozzáférési jogkivonat**értékeit. Ez az érték a **bérlői URL-cím** és a **titkos jogkivonat** mezőben jelenik meg a 15Five alkalmazás kiépítés lapján a Azure Portalban.
    
    ![15Five-SCIM hozzáadása](media/15five-provisioning-tutorial/image03.png)

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>3. lépés 15Five hozzáadása az Azure AD Application Galleryből

Vegyen fel 15Five az Azure AD-alkalmazás-katalógusból a 15Five való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a 15Five az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok 15Five való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a 15Five 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy 15Five alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a 15Five az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **15Five**lehetőséget.

    ![Az 15Five hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5.  A rendszergazdai hitelesítő adatok szakaszban adja meg a **SCIM 2,0 alap URL-címét és a hozzáférési jogkivonat** azon értékeit, amelyeket korábban a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a 15Five. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a 15Five-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a 15Five**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található 15Five. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.


   |Attribútum|Típus|
   |---|---|
   |Active|Logikai|
   |cím|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: 15Five: 2.0: felhasználó: hely|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: 15Five: 2.0: felhasználó: startDate|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a 15Five**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált 15Five az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |externalId|Sztring|
      |displayName|Sztring|
      |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás 15Five való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a 15Five kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

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

* a 15Five nem támogatja a Soft deletes használatát a felhasználók számára.

## <a name="change-log"></a>Változási napló

* 06/16/2020 – a vállalati bővítmény "Manager" attribútumának és egyéni attribútumának "location" és "Start date" támogatása a felhasználók számára.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).
