---
title: 'Oktatóanyag: A Blink beállítása az automatikus felhasználó-kiépítés Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 9ebcbf4a-0cf9-41c3-96af-d8ab6ab11639
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e1173d663e9b80636c80717d93f75b28bc377aa9
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156123"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Oktatóanyag: A Blink beállítása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa a Blink és Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése a Blink-re történjen.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a Microsoft Azure-előnézetek [kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Blink-bérlő](https://joinblink.com/pricing)
* Egy felhasználói fiók a Blink-ben rendszergazdai engedélyekkel.

## <a name="assigning-users-to-blink"></a>Felhasználók kiosztása a Blink-hoz

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok számára van szükség az Azure AD-hoz a Blink eléréséhez. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a Villogáshoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Fontos Tippek a felhasználók a Villogáshoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen a Blink elemhez rendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználó a Villogáshoz van rendelve, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-blink-for-provisioning"></a>A telepítő villog a kiépítés során

1. A scim-token igényléséhez jelentkezzen be support@joinblink.com a [támogatási esetre](https://help.joinblink.com/hc/requests/new) vagy az e-mailek **Blink-támogatására** . .

2.  Másolja a **scim hitelesítési tokent**. Ez az érték a Azure Portalban a Blink-alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében lesz megadva.

## <a name="add-blink-from-the-gallery"></a>Blink hozzáadása a katalógusból

Mielőtt a Blink-t konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, fel kell vennie a Blink-et az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájára.

**A következő lépésekkel adhatja hozzá a Blink-et az Azure AD-alkalmazás-katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Blink**kifejezést, válassza az eredmények panelen a **Pislogás** elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Pislogás az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Automatikus felhasználó-kiépítés beállítása a Blink-ra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le azokat a felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a Blink-hez, a [Blink egyszeri bejelentkezés oktatóanyagában](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)szereplő utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>A felhasználók automatikus üzembe helyezésének beállítása a Blink-hez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Pislogás**elemet.

    ![A Blink hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a kiépítés lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://api.joinblink.com/scim` adja meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen a Blink-hez kapcsolódni. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Blink-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Pislogás**lehetőséget.

    ![Blink felhasználói leképezések](media/blink-provisioning-tutorial/User_mappings.png)

9. Tekintse át az Azure AD-ból szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Update műveletekhez a Blink-ben használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Blink felhasználói attribútumok](media/blink-provisioning-tutorial/User_attributes.png)

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Villogáshoz, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat, akik számára a villogást szeretné kiépíteni, majd válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz hatókörében meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti az üzembe helyezési tevékenység jelentésre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Blink-ben végrehajtott összes műveletet ismertetik.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)

