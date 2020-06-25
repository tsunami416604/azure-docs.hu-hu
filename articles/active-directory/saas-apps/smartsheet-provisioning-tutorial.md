---
title: 'Oktatóanyag: a Smartsheet konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 699eb37176d6737744fb0ba01f9f3f4a2d4e55b1
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318747"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Smartsheet konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Smartsheet és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [Smartsheet](https://www.smartsheet.com/pricing). A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Smartsheet-ben
> * Felhasználók eltávolítása a Smartsheet-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Smartsheet között
> * Egyszeri bejelentkezés a Smartsheet-be (ajánlott)

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* [Egy Smartsheet-bérlő](https://www.smartsheet.com/pricing).
* Felhasználói fiók Smartsheet Enterprise vagy Enterprise Premier csomaggal, rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Smartsheet között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>2. lépés Smartsheet konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Azure AD-vel való automatikus Smartsheet konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Smartsheet-on.

1. Jelentkezzen be **rendszergazdaként** a **[Smartsheet-portálon](https://app.smartsheet.com/b/home)** , és navigáljon a **fiók rendszergazdája**elemre.

    ![Smartsheet-fiók rendszergazdája](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Lépjen a **biztonsági vezérlők > felhasználó automatikus kiépítés > szerkesztés**gombra.

    ![Smartsheet biztonsági vezérlők](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adja hozzá és ellenőrizze az e-mail tartományokat azon felhasználók számára, akik számára az Azure AD-ből Smartsheet kíván kiépíteni. A **nincs engedélyezve** beállítás megadásával biztosíthatja, hogy a kiépítési műveletek csak az Azure ad-ből származzanak, valamint hogy a Smartsheet-felhasználók listája szinkronban legyen az Azure ad-hozzárendelésekkel.

    ![Smartsheet-felhasználó kiépítés](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Az érvényesítés befejeződése után aktiválni kell a tartományt. 

    ![Smartsheet aktiválása tartomány](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Az **alkalmazásokhoz és az integrációhoz**való navigálással létrehozhatja az automatikus felhasználó-kiépítés az Azure ad-vel való konfigurálásához szükséges **titkos jogkivonatot** .

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Válassza az **API-hozzáférés**lehetőséget. Kattintson az **új hozzáférési jogkivonat előállítása**elemre.

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Adja meg az API-hozzáférési jogkivonat nevét. Kattintson az **OK** gombra.

    ![Smartsheet telepítése](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Másolja az API-hozzáférési jogkivonatot, és mentse, mivel ez az egyetlen időpontig megtekinthető. Erre szükség van az Azure AD **titkos jogkivonat** mezőjében.

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>3. lépés Smartsheet hozzáadása az Azure AD Application Galleryből

Vegyen fel Smartsheet az Azure AD-alkalmazás-katalógusból a Smartsheet való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Smartsheet az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok Smartsheet való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* A Smartsheet és az Azure AD közötti felhasználói szerepkör-hozzárendelések paritásának biztosítása érdekében ajánlott ugyanazokat a szerepkör-hozzárendeléseket használni, amelyek a teljes Smartsheet-felhasználók listájában fel vannak töltve. A felhasználói lista Smartsheet való lekéréséhez navigáljon a **fiók rendszergazdája > a felhasználói felügyelet > további műveletek > a felhasználói lista letöltése (CSV)** elemre.

* Az alkalmazás egyes funkcióinak eléréséhez a Smartsheet-nek több szerepkörrel kell rendelkeznie. Ha többet szeretne megtudni a Smartsheet felhasználói típusairól és engedélyeiről, lépjen a [Felhasználói típusok és engedélyek](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)elemre.

*  Ha egy felhasználó több, a Smartsheet-ban hozzárendelt szerepkörrel rendelkezik, meg **kell** győződnie arról, hogy a szerepkör-hozzárendelések replikálva vannak az Azure ad-ben, így elkerülhető, hogy a felhasználók ne férhessenek hozzá a Smartsheet objektumokhoz. A Smartsheet minden egyes egyedi szerepkörét **hozzá kell rendelni** egy másik csoporthoz az Azure ad-ben. **Ezután a felhasználót hozzá kell adni** a kívánt szerepköröknek megfelelő csoportokhoz. 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Smartsheet 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Smartsheet alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Smartsheet az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Smartsheet**lehetőséget.

    ![Az Smartsheet hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a hozzáférési jogkivonat** azon értékeit, amelyeket a rendszer a **bérlői URL-cím** és a **titkos jogkivonat** Smartsheet korábban lekérdezett. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Smartsheet. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Smartsheet-fiók rendelkezik SysAdmin engedélyekkel, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Smartsheet**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Smartsheet. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Smartsheet felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |Active|Logikai|
   |cím|Sztring|
   |userName (Felhasználónév)|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |phoneNumbers [type EQ "work"]. Value|Sztring|
   |phoneNumbers [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |externalId|Sztring|
   |szerepkörök [elsődleges EQ "true"]. megjelenítés|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Type|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Value|Sztring|
   |roles|Sztring|
   urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|


10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás Smartsheet való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Smartsheet kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Smartsheet nem támogatja a Soft-delete használatát. Ha a felhasználó **aktív** attribútuma hamis értékre van állítva, a Smartsheet véglegesen törli a felhasználót.

## <a name="change-log"></a>Változási napló

* 06/16/2020 – a felhasználók számára a "Cost Center", a "Division", a "Manager" és a "Department" nagyvállalati szintű bővítmények támogatása.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
