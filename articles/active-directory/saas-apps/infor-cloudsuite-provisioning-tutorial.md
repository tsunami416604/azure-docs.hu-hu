---
title: 'Oktatóanyag: Az Infor CloudSuite konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az Infor CloudSuite szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057541"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Oktatóanyag: Az Infor CloudSuite konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az Infor CloudSuite és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonfektusát tegye az Infor CloudSuite szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Egy Infor CloudSuite-bérlő](https://www.infor.com/products/infor-os)
* Az Infor CloudSuite rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-infor-cloudsuite"></a>Felhasználók hozzárendelése az Infor CloudSuite szolgáltatáshoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az Infor CloudSuite szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az Infor CloudSuite-hez az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Fontos tippek a felhasználók Infor CloudSuite-hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve az Infor CloudSuite az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az Infor CloudSuite szolgáltatáshoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Az Infor CloudSuite beállítása kiépítéshez

1. Jelentkezzen be az [Infor CloudSuite felügyeleti konzolra.](https://www.infor.com/customer-center) Kattintson a felhasználó ikonra, majd keresse meg a **felhasználókezelést.**

    ![Infor CloudSuite felügyeleti konzol](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Kattintson a képernyő bal felső sarkában található menüikonra. Kattintson a **Manage gombra.**

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Nyissa meg az **SCIM-fiókokat.**

    ![Infor CloudSuite SCIM-fiók](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Admin felhasználó hozzáadása a plusz ikonra kattintva. Adjon meg **egy SCIM-jelszót,** és írja be ugyanazt a jelszót a **Jelszó megerősítése mezőbe.** A jelszó mentéséhez kattintson a mappa ikonra. Ekkor megjelenik egy **felhasználói azonosító, amelyet** a rendszergazdai felhasználó hoz létre.

    ![Infor CloudSuite rendszergazdai felhasználó](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite jelszó](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite azonosító](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. A tulajdonosi jogkivonat létrehozásához másolja a **felhasználói azonosítót** és az **SCIM-jelszót.** Illessze be őket a jegyzettömb++ kettősponttal elválasztva. A karakterlánc értékét úgy kódolja, hogy a **Beépülő modulok ra > a MIME-eszközökre > Basic64 Kódolás parancsra**navigál. 

    ![Infor CloudSuite azonosító](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Másolja a tulajdonosi jogkivonatot. Ez az érték az Azure Portalin az Infor CloudSuite-alkalmazás Kiépítés lapján a Titkos jogkivonat mezőben lesz megadva.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Infor CloudSuite hozzáadása a galériából

Mielőtt konfigurálna az Infor CloudSuite-et az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia az Infor CloudSuite-et az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

**Ha az Infor CloudSuite alkalmazást az Azure AD alkalmazásgyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Infor CloudSuite**kifejezést, válassza az **Infor CloudSuite** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Infor CloudSuite az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Automatikus felhasználói kiépítés konfigurálása az Infor CloudSuite szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az Infor CloudSuite-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Az Infor CloudSuite szolgáltatássaml-alapú egyszeri bejelentkezésengedélyezését is engedélyezheti az Infor CloudSuite szolgáltatásban, az [Infor CloudSuite egyszeri bejelentkezési oktatóanyagutasításait](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni az Infor CloudSuite SCIM-végpontjáról, olvassa el [ezt](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)a .

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Az Infor CloudSuite automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Infor CloudSuite**elemet.

    ![Az Infor CloudSuite hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg a titkos **jogkivonat**korábbi beolvasása kor beolvasott tulajdonosi token értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az Infor CloudSuite szolgáltatáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az Infor CloudSuite-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása az Infor CloudSuite szolgáltatással**lehetőséget.

    ![Infor CloudSuite felhasználói leképezések](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és az Infor CloudSuite között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az Infor CloudSuite felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Infor CloudSuite felhasználói attribútumok](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása az Infor CloudSuite szolgáltatással**lehetőséget.

    ![Infor CloudSuite csoportleképezések](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és az Infor CloudSuite között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az Infor CloudSuite csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Infor CloudSuite csoport attribútumai](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Infor CloudSuite Azure AD-kiépítési szolgáltatásának engedélyezéséhez módosítsa a **Kiépítés állapotát** **be beállításra** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az Infor CloudSuite szolgáltatásba a **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az Infor CloudSuite-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)