---
title: 'Oktatóanyag: a Myday konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Myday.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 59b4150a-9530-479b-9f62-a16c3d005dbe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2020
ms.author: Zhchia
ms.openlocfilehash: 15119a76e1e59b339a9012896a525a0ecc91f34d
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761532"
---
# <a name="tutorial-configure-myday-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Myday konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Myday és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [Myday](https://www.mydaycloud.com) . A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Myday-ben
> * Felhasználók eltávolítása a Myday-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Myday között
> * Csoportok és csoporttagságok kiépítése a Myday-ben
> * Egyszeri bejelentkezés a Myday-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Myday-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Myday között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-myday-to-support-provisioning-with-azure-ad"></a>2. lépés Myday konfigurálása az Azure AD-vel való kiépítés támogatásához

Forduljon a Myday képviselőjéhez vagy a támogatási csapathoz, hogy megkapja a **bérlői URL-címet** és a **titkos jogkivonatot**.

## <a name="step-3-add-myday-from-the-azure-ad-application-gallery"></a>3. lépés Myday hozzáadása az Azure AD Application Galleryből

Vegyen fel Myday az Azure AD-alkalmazás-katalógusból a Myday való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Myday-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok Myday való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-myday"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Myday 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-myday-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Myday az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Myday**lehetőséget.

    ![Az Myday hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlő URL **-címében**korábban LEKÉRT bérlői URL-cím értékét. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban**lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Myday. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Myday-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL-token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő Myday. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Myday felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a Myday API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |Active|Logikai|
   |displayName|Sztring|
   |cím|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |preferredLanguage|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |név. formázott|Sztring|
   |externalId|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |phoneNumbers [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers [type EQ "work"]. Value|Sztring|
   |szerepkörök [elsődleges EQ "true"]. megjelenítés|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Type|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|

10. A **leképezések** szakaszban válassza ki a **Azure Active Directory csoportok kiépítése**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált Myday az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Myday tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD-kiépítési szolgáltatás Myday való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Myday kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

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
