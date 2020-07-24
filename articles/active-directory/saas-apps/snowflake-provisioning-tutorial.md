---
title: 'Oktatóanyag: a hópehely konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Hópelyhekbe.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 46ebb122b0165d469b1c40871d5939e50a8595c9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016315"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Oktatóanyag: a hópehely konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a hópehely és Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [hópehely](https://www.Snowflake.com/pricing/)-t. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a hópehely-ban
> * A hópehely felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a hópehely között
> * Csoportok és csoporttagságok kiépítése a hópehely-ban
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial) a hópehely-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* [Egy hópehely bérlő](https://www.Snowflake.com/pricing/).
* Egy felhasználói fiók a hópehely-ban rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a hópehely milyen adatleképezést szeretne leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>2. lépés A hópehely konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt a hópehely-t konfigurálja az Azure AD-vel való automatikus felhasználói kiépítés során, engedélyeznie kell a SCIM-létesítést a hópehely-on.

1. Jelentkezzen be a hópehely felügyeleti konzolra. Adja meg az alább látható lekérdezést a munkalapon, és kattintson a **Futtatás**gombra.

    ![A hópehely felügyeleti konzol](media/Snowflake-provisioning-tutorial/image00.png)

2.  A SCIM hozzáférési token jön létre a hópehely bérlőhöz. A lekéréséhez kattintson az alábbi hivatkozásra.

    ![Hópehely SCIM hozzáadása](media/Snowflake-provisioning-tutorial/image01.png)

3. Másolja a generált jogkivonat értékét, és kattintson a **kész**gombra. Ez az érték a Azure Portalban lévő hópehely-alkalmazás létesítés lapjának **titkos jogkivonat** mezőjében lesz megadva.

    ![Hópehely SCIM hozzáadása](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>3. lépés A hópehely hozzáadása az Azure AD Application Galleryből

Adja hozzá a hópehely-t az Azure AD-alkalmazás-katalógusból a hópehely kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a hópehely-t az SSO-hoz, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel a hópehely-hoz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>5. lépés A felhasználók automatikus üzembe helyezésének beállítása a hópehely-ra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendeléseken alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le a hópehely-ban.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>A hópehely automatikus felhasználó általi üzembe helyezésének beállítása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **hópehely**elemet.

    ![A hópehely hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **SCIM 2,0 alap URL-címét és a hitelesítési jogkivonat** azon értékeit, amelyeket a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben korábban lekért. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a hópehely-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a hópehely-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a hópehely-hoz**lehetőséget.

10. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |Active|Logikai érték|
   |displayName|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: defaultRole|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: defaultWarehouse|Sztring|

11. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a hópehely-hoz**lehetőséget.

12. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált csoportok attribútumait az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely-beli csoportoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Hivatkozás|

13. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a hópehely-hez, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

15. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a hópehely-t szeretné kiépíteni, majd a **Beállítások** szakaszban válassza ki a kívánt értékeket a **hatókörben** . Ha ez a lehetőség nem érhető el, konfigurálja a szükséges mezőket a rendszergazdai hitelesítő adatok területen, majd kattintson a **Save (Mentés** ) gombra, és frissítse az oldalt. 

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A hópehely által generált SCIM tokenek 6 hónapon belül lejárnak. Vegye figyelembe, hogy ezeket a lejáratuk előtt frissíteni kell, hogy a kiépítési szinkronizálások továbbra is működőképesek legyenek. 

## <a name="change-log"></a>Módosítási napló

* 07/21/2020 – az összes felhasználó számára engedélyezett, helyreállítható törlés (az aktív attribútumon keresztül).

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések
* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).
