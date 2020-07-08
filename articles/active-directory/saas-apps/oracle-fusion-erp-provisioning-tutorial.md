---
title: 'Oktatóanyag: az Oracle Fusion ERP konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Oracle Fusion ERP számára.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77061176"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Oktatóanyag: az Oracle Fusion ERP konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa az Oracle Fusion ERP és Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztását és kiépítését az Oracle Fusion ERP-re végezze.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure előnézetekhez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* Egy [Oracle Fusion ERP-bérlő](https://www.oracle.com/applications/erp/).
* Egy felhasználói fiók az Oracle Fusion ERP-ben rendszergazdai engedélyekkel.

## <a name="assign-users-to-oracle-fusion-erp"></a>Felhasználók kiosztása az Oracle Fusion ERP-hez 
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá az Oracle Fusion ERP-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az Oracle Fusion ERP-hez az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Fontos Tippek a felhasználók Oracle Fusion ERP-hez való hozzárendeléséhez 

 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az Oracle Fusion ERP-hez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel az Oracle Fusion ERP-hez, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Az Oracle Fusion ERP beállítása az üzembe helyezéshez

Mielőtt beállítja az Oracle Fusion ERP-t az Azure AD-vel való automatikus felhasználó-kiépítés során, engedélyeznie kell az SCIM-létesítést az Oracle Fusion ERP-ben.

1. Jelentkezzen be az [Oracle Fusion ERP felügyeleti konzolba](https://cloud.oracle.com/sign-in)

2. Kattintson a bal felső sarokban található navigátorra. Az **eszközök**területen válassza a **biztonsági konzol**lehetőséget.

    ![Oracle Fusion ERP – SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navigáljon a **felhasználókhoz**.
    
    ![Oracle Fusion ERP – SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Mentse a rendszergazdai felhasználói fiók felhasználónevét és jelszavát, amelyet az Oracle Fusion ERP felügyeleti konzolba való bejelentkezéshez fog használni. Ezeket az értékeket az Oracle Fusion ERP-alkalmazás üzembe helyezés lapjának **rendszergazdai Felhasználónév** és **jelszó** mezőiben kell megadni a Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP hozzáadása a katalógusból

Ha az Oracle Fusion ERP-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez szeretné konfigurálni, az Azure AD-alkalmazás-katalógusból hozzá kell adnia az Oracle Fusion ERP-t a felügyelt SaaS-alkalmazások listájához.

**Az Oracle Fusion ERP az Azure AD Application Galleryből való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Oracle Fusion ERP**kifejezést, majd az eredmények panelen válassza az **Oracle Fusion ERP** elemet.

    ![Oracle Fusion ERP az eredmények listájában](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Az automatikus felhasználó-kiépítés beállítása az Oracle Fusion ERP-be 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure ad-ben felhasználói és/vagy csoportos hozzárendelések alapján az Oracle Fusion ERP-ben a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az Oracle Fusion ERP-hez az [Oracle Fusion ERP egyszeri bejelentkezés oktatóanyagában](oracle-fusion-erp-tutorial.md)ismertetett utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

> [!NOTE]
> Az Oracle Fusion ERP SCIM-végpontjának megismeréséhez tekintse meg az [Oracle Applications Cloud általános szolgáltatásainak Rest APIét](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)ismertető témakört.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Gyutacs az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Oracle Fusion ERP**elemet.

    ![Az Oracle Fusion ERP hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` meg a **bérlői URL-címet**. Adja meg a rendszergazda **felhasználónevét** és jelszavát a korábban lekért rendszergazdai Felhasználónév és **jelszó** mezőkben. Kattintson az Azure AD és az Oracle Fusion ERP közötti **kapcsolat tesztelése** elemre. 

    ![Oracle Fusion ERP – SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az Oracle Fusion ERP**-be lehetőséget.

    ![Oracle Fusion ERP – SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Tekintse át az Azure AD-ból az Oracle Fusion ERP-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Oracle Fusion ERP – SCIM hozzáadása](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Oracle Fusion ERP-** re lehetőséget.

    ![Oracle Fusion ERP csoportos leképezések](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből az Oracle Fusion ERP-be szinkronizált csoportok attribútumait az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP-ben lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Oracle Fusion ERP csoport attribútumai](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Oracle Fusion ERP-hez készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne az Oracle Fusion ERP számára kiépíteni, ha a **Settings (beállítások** ) szakaszban szeretné kijelölni a kívánt értékeket a **hatókörben** .

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által az Oracle Fusion ERP-ben végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* Az Oracle Fusion ERP csak a SCIM-végpont alapszintű hitelesítését támogatja.
* Az Oracle Fusion ERP nem támogatja a csoportok üzembe helyezését.
* Az Oracle Fusion ERP szerepkörei az Azure AD-ben csoportokba vannak rendelve. Ahhoz, hogy szerepköröket rendeljen a felhasználókhoz az Azure AD-ből származó Oracle Fusion ERP-ben, hozzá kell rendelnie a felhasználókat az Oracle Fusion ERP szerepkörei után elnevezett kívánt Azure AD-csoportokhoz.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
