---
title: 'Oktatóanyag: A 15Five konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a 15Five 15Five-ra való kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059246"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Oktatóanyag: A 15Five konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a 15Five és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok 15Five-ra történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [A 15Five bérlő](https://www.15five.com/pricing/).
* Egy felhasználói fiók a 15Five-ban rendszergazdai engedélyekkel.

## <a name="assigning-users-to-15five"></a>Felhasználók hozzárendelése a 15Five-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a 15Five-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a 15Five-hoz rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Fontos tippek a felhasználók 15Five-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve 15Five az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Ha egy felhasználót a 15Five-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-15five-for-provisioning"></a>15Five beállítása a kiépítéshez

A 15Five konfigurálása az Azure AD automatikus felhasználói kiépítéshez, engedélyeznie kell az SCIM-kiépítést a 15Five-on.

1. Jelentkezzen be a [15Five felügyeleti konzolra.](https://my.15five.com/) Keresse meg a **Szolgáltatások > integrációk .**

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/integration.png)

2.  Kattintson a **SCIM 2.0 gombra.**

    ![15Five felügyeleti konzol](media/15five-provisioning-tutorial/image00.png)

3.  Keresse meg az **SCIM-integrációt > OAuth token létrehozása elemre.**

    ![15Öt Add SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Másolja az **SCIM 2.0 alap URL-címének** és **hozzáférési jogkivonatának értékeit.** Ez az érték a **bérlői URL-cím** és a **titkos jogkivonat** mezőjében lesz megadva a 15Five alkalmazás kiépítése lapján az Azure Portalon.
    
    ![15Öt Add SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Add 15Five a galériából

A 15Five konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a 15Five-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**A 15Five hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **15Five**értéket, válassza a **15Five** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![15Öt az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Automatikus felhasználói kiépítés konfigurálása a 15Five értékre 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a 15Five-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a 15Five-ra, a [15Five Single sign-on tutorial](15five-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>A 15Five automatikus üzembe létesítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **15Five**lehetőséget.

    ![A 15Five hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5.  A rendszergazdai hitelesítő adatok szakaszban adja meg az **SCIM 2.0 alap URL-címét és a hozzáférési jogkivonat** értékeit, amelyeket korábban a **bérlői URL-cím,** illetve a **titkos jogkivonat** lekért. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD a 15Five-hoz kapcsolódjon. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a 15Five fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a 15Five-ra**lehetőséget.

    ![15Öt felhasználói leképezés](media/15five-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ről a 15Five-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a 15Five felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![15Öt felhasználói attribútum](media/15five-provisioning-tutorial/userattribute.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a 15Five-ra**lehetőséget.

    ![15Öt csoportleképezés](media/15five-provisioning-tutorial/groupmapping.png)

11. Tekintse át az Azure AD-ről a 15Five-ra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a 15Five csoportcsoportokkal egyeztethetők a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![15Öt csoportattribútum](media/15five-provisioning-tutorial/groupattribute.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás 15Five engedélyezéséhez módosítsa a **Kiépítési állapot** ot **bekapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a 15Five-ra a kívánt értékek kiválasztásával a **Hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a 15Five-on végrehajtott összes műveletet.

    Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésének jelentéskészítése című témakörben](../app-provisioning/check-status-user-account-provisioning.md) olvashat bővebben.
    
## <a name="connector-limitations"></a>Összekötő korlátai

* 15Az Öt nem támogatja a kemény törléseket a felhasználók számára.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)
