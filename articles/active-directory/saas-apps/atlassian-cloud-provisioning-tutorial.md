---
title: 'Oktatóanyag: a Atlassian-felhő konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus üzembe helyezéséhez és kiépítéséhez a Atlassian-felhőben.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059334"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: a Atlassian-felhő konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Atlassian-felhőben és a Azure Active Directoryban (Azure AD) az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Felhőbeli Atlassian.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Atlassian Felhőbeli bérlő](https://www.atlassian.com/licensing/cloud)
* Felhasználói fiók a Atlassian-felhőben rendszergazdai engedélyekkel.

> [!NOTE]
> Az Azure AD kiépítési integrációja a **Atlassian Cloud scim API**-ra támaszkodik, amely a Atlassian Cloud Teams számára érhető el.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Atlassian-felhő hozzáadása a gyűjteményből

Mielőtt Atlassian a felhőt az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Atlassian-felhőt az Azure AD Application Galleryből a felügyelt SaaS-alkalmazások listájára.

**Ha a Atlassian-felhőt az Azure AD-alkalmazás-katalógusból szeretné hozzáadni, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Atlassian Cloud**kifejezést, válassza az **Atlassian Cloud** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Atlassian felhő az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Felhasználók Atlassian-felhőhöz rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai férhetnek hozzá a Atlassian-felhőhöz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Atlassian felhőhöz az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Fontos Tippek a felhasználók Atlassian-felhőhöz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Atlassian-felhőhöz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Atlassian felhőhöz, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Automatikus felhasználó-kiépítés beállítása a Atlassian-felhőbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozza létre, frissítse és tiltsa le a Atlassian-felhőben lévő felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Atlassian-felhőhöz, a [Atlassian-felhő egyszeri bejelentkezésének oktatóanyagában](atlassian-cloud-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>A Atlassian-felhő automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd a **Atlassian-felhő**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Atlassian-felhő**elemet.

    ![Az Atlassian Felhőbeli hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navigáljon a [Atlassian Organization Manager](https://admin.atlassian.com) **> Válassza ki a szervezeti > könyvtárat**.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Kattintson a **felhasználó üzembe** helyezése lehetőségre, majd a **könyvtár létrehozása**lehetőségre. Másolja a **könyvtár alap URL-címét** és a **tulajdonosi jogkivonatot** a **bérlői URL** -címre és a **titkos jogkivonat** mezőibe.

    ![Atlassian Cloud kiépítés](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud kiépítés](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud kiépítés](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Atlassian-felhő fiókjának **bérlői URL-címét** és **titkos jogkivonatát** . Ilyen értékek például a következők:

   * A **bérlői URL-cím** mezőben adja meg a Atlassian kapott adott bérlői végpontot a 6. lépésben leírtak szerint. Például: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * A **titkos jogkivonat** mezőben töltse ki a titkos jogkivonatot a 6. lépésben leírtak szerint.

8. A 7. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Atlassian-felhőhöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Atlassian felhőalapú fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Kattintson a **Save** (Mentés) gombra.

11. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Atlassian a felhőbe**lehetőséget.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található Atlassian-felhőbe. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Atlassian-felhőben lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Atlassian a felhőbe**lehetőséget.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Tekintse át az Azure AD-ből szinkronizált Atlassian az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Atlassian-felhőben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Atlassian-felhőhöz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne építeni a Atlassian-felhőbe úgy, hogy a **Settings (beállítások** ) szakaszban kiválasztja a kívánt értékeket a **hatókörben** .

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Atlassian-felhő kiépítés](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek a Atlassian-felhőben az Azure ad-kiépítési szolgáltatás által végrehajtott műveleteket ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Atlassian-felhő lehetővé teszi a felhasználók csak [ellenőrzött tartományokból](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)való üzembe helyezését.
* A Atlassian-felhő jelenleg nem támogatja a csoportok átnevezni. Ez azt jelenti, hogy az Azure AD-beli csoportok displayName-beli módosításait nem frissíti a rendszer, és a Atlassian-felhőben jelenik meg.
* Az Azure AD **levelezési** felhasználó attribútumának értéke csak akkor van feltöltve, ha a felhasználó rendelkezik Microsoft Exchange-postaládával. Ha a felhasználó nem rendelkezik ilyennel, ajánlott leképezni egy másik kívánt attribútumot az **e-mailek** attribútumhoz a Atlassian-felhőben.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png