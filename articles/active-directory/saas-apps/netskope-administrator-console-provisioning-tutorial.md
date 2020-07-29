---
title: 'Oktatóanyag: a Netskope-felügyeleti konzol konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Netskope felügyeleti konzol.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77061250"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Oktatóanyag: a Netskope felügyeleti konzol konfigurálása a felhasználók automatikus kiépítési felállításához

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Netskope felügyeleti konzol és Azure Active Directory (Azure AD) számára az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése a Netskope felügyeleti konzol.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Netskope felügyeleti konzol bérlő](https://www.netskope.com/)
* A Netskope felügyeleti konzol rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="assigning-users-to-netskope-administrator-console"></a>Felhasználók kiosztása Netskope felügyeleti konzol

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a Netskope felügyeleti konzol eléréséhez. Miután eldöntötte, hogy ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Netskope felügyeleti konzoléhez az alábbi útmutatást követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Fontos Tippek a felhasználók Netskope való hozzárendeléséhez felügyeleti konzol

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Netskope-felügyeleti konzol az automatikus felhasználó-létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha felügyeleti konzol Netskope rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Netskope-felügyeleti konzol beállítása a kiépítés számára

1. Jelentkezzen be a [Netskope felügyeleti konzol felügyeleti konzolon](https://netskope.goskope.com/). Navigáljon a **kezdőlap > beállítások**elemre.

    ![Netskope felügyeleti konzol felügyeleti konzol](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigáljon az **eszközökhöz**. Az **eszközök** menüben navigáljon a **Directory-eszközök > scim-integráció**elemre.

    ![Netskope felügyeleti konzol eszközök](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope felügyeleti konzol SCIM hozzáadása](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Görgessen le, és kattintson a **jogkivonat hozzáadása** gombra. Az **OAuth-ügyfél nevének hozzáadása** párbeszédpanelen adja meg az **ügyfél nevét** , és kattintson a **Save (Mentés** ) gombra.

    ![Netskope felügyeleti konzol jogkivonat hozzáadása](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope felügyeleti konzol-ügyfél neve](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Másolja a **scim-kiszolgáló URL-címét** és a **tokent**. Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőiben lesznek megadva a Netskope felügyeleti konzol alkalmazás üzembe helyezés lapján a Azure Portalban.

    ![Netskope felügyeleti konzol jogkivonat létrehozása](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Netskope-felügyeleti konzol hozzáadása a katalógusból

Mielőtt Netskope felügyeleti konzol az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Netskope felügyeleti konzol az Azure AD Application Gallery-ből.

**Ha Netskope felügyeleti konzol szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Netskope felügyeleti konzol**, válassza az **Netskope felügyeleti konzol** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Netskope felügyeleti konzol az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Automatikus felhasználó-kiépítés konfigurálása a Netskope felügyeleti konzol 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy felügyeleti konzol Netskope alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a Netskope felügyeleti konzol a [Netskope felügyeleti konzol egyszeri bejelentkezési oktatóanyagban](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a Netskope felügyeleti konzol SCIM-végpontról, olvassa el [ezt a témakört](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>A felhasználók automatikus kiépítési Netskope konfigurálása az Azure AD-ben felügyeleti konzol:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Netskope felügyeleti konzol**elemet.

    ![Az Netskope felügyeleti konzol hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim-kiszolgáló URL-címét** , amely korábban a **bérlői URL**-címben lett lekérve. Adja meg a **titkos jogkivonatban**korábban lekért **jogkivonat** -értéket. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Netskope felügyeleti konzolhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Netskope felügyeleti konzol fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Netskope felügyeleti konzol**lehetőséget.

    ![Netskope felügyeleti konzol felhasználói leképezések](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakasz Netskope felügyeleti konzol. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Netskope felügyeleti konzol felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Netskope felügyeleti konzol felhasználói attribútumok](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Netskope felügyeleti konzol**lehetőséget.

    ![Netskope felügyeleti konzol csoport-hozzárendelések](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált Netskope felügyeleti konzol az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Netskope-felügyeleti konzol csoportjaira vonatkoznak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Netskope felügyeleti konzol csoport attribútumai](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Netskope felügyeleti konzol, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Netskope felügyeleti konzol szeretne kiépíteni a **Settings (beállítások** ) szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a Netskope felügyeleti konzol.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

