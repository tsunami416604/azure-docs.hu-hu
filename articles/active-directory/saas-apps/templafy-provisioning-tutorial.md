---
title: 'Oktatóanyag: A Templafy konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Templafy felhasználói fiókjainak automatikus kiépítésére és kiépítésének kiirtására.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 230877d5-e466-4449-82c8-88cfa42f6501
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: e03bfc1d3ce6490528f795d4ae5a83a59f044b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064213"
---
# <a name="tutorial-configure-templafy-for-automatic-user-provisioning"></a>Oktatóanyag: A Templafy konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Templafy és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok Templafy-ba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Templafy bérlő.](https://www.templafy.com/pricing/)
* A Templafy felügyeleti engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-templafy"></a>Felhasználók hozzárendelése a Templafy-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Templafy-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Templafy-hoz rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy"></a>Fontos tippek a felhasználók Templafy-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve templafy az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Templafy-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-templafy-for-provisioning"></a>Templafy beállítása a kiépítéshez

Mielőtt konfigurálná a Templafy-t az Azure AD-vel való automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést a Templafy-n.

1. Jelentkezzen be a Templafy Felügyeleti konzolba. Kattintson a **Közigazgatás gombra.**

    ![Templafy felügyeleti konzol](media/templafy-provisioning-tutorial/image00.png)

2. Kattintson a **hitelesítési módra**.

    ![Templafy Hozzáadása SCIM](media/templafy-provisioning-tutorial/image01.png)

3. Másolja a **SCIM Api-kulcs** értékére. Ezt az értéket a Templafy-alkalmazás kiépítés lapján az Azure Portalon a **Titkos jogkivonat** mezőben adja meg.

    ![Templafy Hozzáadása SCIM](media/templafy-provisioning-tutorial/image02.png)

## <a name="add-templafy-from-the-gallery"></a>Templafy hozzáadása a galériából

A Templafy konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Templafy-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Templafy hozzáadása az Azure AD alkalmazáskatalógusból, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Templafy**, válassza **a Templafy** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Templafy az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-templafy"></a>Automatikus felhasználói kiépítés konfigurálása a Templafy rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Templafy-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Templafy számára, a [Templafy Single sign-on oktatóanyag](templafy-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-templafy-in-azure-ad"></a>A Templafy automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Templafy**lehetőséget.

    ![A Templafy hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://scim.templafy.com/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg a **titkos jogkivonat**korábbi, **scim API-kulcsának** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Templafy-hoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy templafy-fiókja rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Templafy szolgáltatásba**lehetőséget.

    ![Templafy felhasználói leképezések](media/templafy-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ről a Templafy-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Templafy felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Templafy felhasználói attribútumok](media/templafy-provisioning-tutorial/userattribute.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Templafy szolgáltatásba**lehetőséget.

    ![Templafy csoport leképezései](media/templafy-provisioning-tutorial/groupmapping.png)

11. Tekintse át az Azure AD-ről a Templafy-ra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Templafy csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Templafy csoport attribútumai](media/templafy-provisioning-tutorial/groupattribute.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez templafy, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Templafy-ba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Templafy-n végrehajtott összes műveletet.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.
    
## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
