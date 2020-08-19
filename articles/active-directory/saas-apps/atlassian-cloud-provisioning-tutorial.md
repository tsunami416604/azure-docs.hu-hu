---
title: 'Oktatóanyag: a Atlassian-felhő konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus üzembe helyezéséhez és kiépítéséhez a Atlassian-felhőben.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 14418d4d280f4da629aecd5a95b5a49e6856e2c1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549387"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: a Atlassian-felhő konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Atlassian-felhőben és a Azure Active Directoryban (Azure AD) az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [Felhőbeli Atlassian](https://www.atlassian.com/licensing/cloud). A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Atlassian-felhőben
> * Felhasználók eltávolítása a Atlassian-felhőben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Atlassian-felhő között
> * Csoportok és csoporttagságok kiépítése a Atlassian-felhőben
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/atlassian-cloud-tutorial) a Atlassian-felhőbe (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* [Atlassian Felhőbeli bérlő](https://www.atlassian.com/licensing/cloud)
* Felhasználói fiók a Atlassian-felhőben rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Atlassian-felhő között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>2. lépés A Atlassian-felhő beállítása az Azure AD-vel való kiépítés támogatására

1. Navigáljon a [Atlassian Organization Manager](https://admin.atlassian.com) **> válassza ki a szervezeti > könyvtárat**.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Kattintson a **felhasználó üzembe** helyezése lehetőségre, majd a **könyvtár létrehozása**lehetőségre. Másolja a Atlassian-Felhőbeli alkalmazás üzembe helyezés lapján a **bérlői URL** -cím és a **titkos jogkivonat** mezőibe a **könyvtár alap URL-címét** és a **tulajdonosi jogkivonatot** , amelyet az Azure ad-portálon talál.

    ![Atlassian Cloud kiépítés a ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![ Atlassian felhőalapú kiépítés ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![ Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>3. lépés Atlassian-felhő hozzáadása az Azure AD Application Galleryből

Vegye fel a Atlassian-felhőt az Azure AD-alkalmazás-katalógusból a Atlassian-felhőbe való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Atlassian Cloud for SSO-t, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel a Atlassian-felhőhöz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Atlassian-felhőbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozza létre, frissítse és tiltsa le a Atlassian-felhőben lévő felhasználókat és/vagy csoportokat.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>A Atlassian-felhő automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd a **Atlassian-felhő**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Atlassian-felhő**elemet.

    ![Az Atlassian Felhőbeli hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Atlassian-felhő fiókjából korábban lekért **bérlői URL-címet** és **titkos** kódot. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Atlassian-felhőhöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Atlassian felhőalapú fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Atlassian a felhőbe**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található Atlassian-felhőbe. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Atlassian-felhőben lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |Active|Logikai|
   |név. familyName|Sztring|
   |név. givenName|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|   

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Atlassian a felhőbe**lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált Atlassian az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Atlassian-felhőben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Atlassian-felhőhöz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne építeni a Atlassian-felhőbe úgy, hogy a **Settings (beállítások** ) szakaszban kiválasztja a kívánt értékeket a **hatókörben** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

16. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Atlassian-felhő lehetővé teszi a felhasználók csak [ellenőrzött tartományokból](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)való üzembe helyezését.
* A Atlassian-felhő jelenleg nem támogatja a csoportok átnevezni. Ez azt jelenti, hogy az Azure AD-beli csoportok displayName-beli módosításait nem frissíti a rendszer, és a Atlassian-felhőben jelenik meg.
* Az Azure AD **levelezési** felhasználó attribútumának értéke csak akkor van feltöltve, ha a felhasználó rendelkezik Microsoft Exchange-postaládával. Ha a felhasználó nem rendelkezik ilyennel, ajánlott leképezni egy másik kívánt attribútumot az **e-mailek** attribútumhoz a Atlassian-felhőben.

## <a name="change-log"></a>Változási napló

* 06/15/2020 – a Batch-javítás támogatása a csoportokhoz.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png