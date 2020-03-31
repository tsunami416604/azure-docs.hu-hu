---
title: 'Oktatóanyag: A munkacsoport konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a munkacsoport felhasználói fiókjainak automatikus kiépítésére és kiépítésének kioldására.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: e9b1e93cf543836b282525c53756752630d5e4f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062793"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Oktatóanyag: A munkacsoport konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Workteam és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok automatikus kiépítéséhez és a Workteam-hez való automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Workteam-bérlő](https://workte.am/pricing.html)
* A Munkacsoport rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-workteam"></a>Felhasználók hozzárendelése a munkacsoporthoz 

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Workteam-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Munkacsoporthoz rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Fontos tippek a felhasználók munkacsoporthoz való hozzárendeléséhez 

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve workteam az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Munkacsoporthoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-workteam--for-provisioning"></a>A munkacsoport beállítása a kiépítéshez

Mielőtt konfigurálná a Workteam-et az Azure AD-vel való automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést a Workteam-en.

1. Jelentkezzen be a [Workteam be](https://app.workte.am/account/signin) Kattintson **a Szervezeti beállítások beállításai** > **gombra.**

    ![Workteam](media/workteam-provisioning-tutorial/settings.png)

2. Görgessen le az aljára, és engedélyezze a Workteam létesítési lehetőségeit.

    ![Workteam](media/workteam-provisioning-tutorial/icon.png)

3. Másolja az **alap url-t** és **a tulajdonosi tokent.** Ezek az értékek a **bérlői URL-cím**és a **titkos jogkivonat** mezőjében a Workteam-alkalmazás kiépítés lapján az Azure Portalon.

    ![Workteam](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Munkacsoport hozzáadása a gyűjteményből

Konfigurálása Workteam automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia munkacsoport ot az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Workteam hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Munkacsapat**, válassza a **Munkacsoport** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Munkacsoport az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Automatikus felhasználói kiépítés konfigurálása a Munkacsoport számára  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Workteam-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Workteam számára, a [Workteam Single sign-on oktatóanyag](workteam-tutorial.md)utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>A Workteam automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Munkacsoport**lehetőséget.

    ![A Munkacsoport hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg az **alap URL-cím** és a **tulajdonosi token** értékeket a **bérlői URL-cím,** illetve a **titkos jogkivonat** korábbi részében. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Workteam-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Munkacsoport-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása munkacsoportba**lehetőséget.

    ![Munkacsoport felhasználói leképezései](media/workteam-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ről a Workteam szolgáltatásra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Workteam felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Munkacsoport felhasználói attribútumai](media/workteam-provisioning-tutorial/userattribute.png)

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az Azure AD-kiépítési szolgáltatás engedélyezése a munkacsoport számára, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Munkacsoportba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ha többet szeretne tudni arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, lásd: [Mennyi ideig tart a felhasználók kiépítése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Az Aktuális **állapot** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenységjelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a workteam-en végrehajtott összes műveletet. További információ: [A felhasználói kiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Az Azure AD kiépítési naplók olvasásához olvassa el [az Automatikus felhasználói fiók kiépítésről szóló jelentéskészítés című témakört.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
