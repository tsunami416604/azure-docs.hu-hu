---
title: 'Oktatóanyag: A vörösbegy konfigurálása a felhasználók automatikus kiépítési felAzure Active Directoryához | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: a3c65fcf224134d816f2bb15cdd9a6b82ec4bd02
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106150"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Oktatóanyag: A Robin konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa a Robin és Azure Active Directory (Azure AD) által elvégzendő lépéseket, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a multiplexelés automatikus kiépítésére és kiépítésére.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Robin-bérlő](https://robinpowered.com/pricing/)
* Egy felhasználói fiók a Robinban rendszergazdai engedélyekkel.

## <a name="assigning-users-to-robin"></a>Felhasználók kiosztása a Robinhoz

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá a Robinhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a következő utasításokat követve adhatja hozzá a Robinhoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Fontos Tippek a felhasználóknak a Robinhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen a Robin-hoz rendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a Robinhoz rendel, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-robin-for-provisioning"></a>A vörösbegy beállítása a kiépítés számára

1. Jelentkezzen be a [Robin felügyeleti konzolra](https://dashboard.robinpowered.com/login). Navigáljon a **> integrációk kezelése > SCIM > kezelése**elemre.

    ![Robin powered felügyeleti konzol](media/robin-provisioning-tutorial/robin-admin.png)

2.  Új szervezeti token létrehozása. Ha elveszíti ezt a jogkivonatot, mindig létrehozhat egy újat anélkül, hogy befolyásolná a meglévő felhasználókat.

    ![Robin powered Add SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Másolja a **scim hitelesítési tokent**. Ez az érték a Azure Portalban lévő Robin-alkalmazás létesítés lapjának titkos jogkivonat mezőjében lesz megadva.



## <a name="add-robin-from-the-gallery"></a>Robin hozzáadása a katalógusból

Mielőtt a Robint konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Robin elemet az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhat hozzá Robint az Azure AD Application Gallery-ből:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Robin**kifejezést, válassza a **Robin** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Robin az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Automatikus felhasználó-kiépítés beállítása a Vörösbegyre 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoportos hozzárendeléseken alapuló felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Robin számára, a [Robin egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)szereplő utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Az automatikus felhasználó-kiépítés beállítása a Robinhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Robin**elemet.

    ![A Robin powered hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://api.robinpowered.com/v1.0/scim-2` adja meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a robinhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Robin-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory-felhasználók szinkronizálása a robinhoz**lehetőséget.

    ![Robin powered User-hozzárendelések](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Robin felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Robin powered felhasználói attribútumok](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Robin**elemhez lehetőséget.

    ![Robin powered Group-leképezések](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítéshez használt Robin-csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Robin powered Group attribútumai](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Robin számára, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a Robinhoz, és válassza ki a kívánt értékeket a **Settings** ( **hatókör** ) területen.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti az üzembe helyezési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a robinon végrehajtott összes műveletet ismertetik.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

