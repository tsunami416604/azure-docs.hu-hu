---
title: 'Oktatóanyag: A Leapsome konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiirtsa a felhasználói fiókokat a Leapsome szolgáltatásba.
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e5837887325c06f9140a3f40eb183139782e2a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057464"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Oktatóanyag: A Leapsome konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Leapsome és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket, hogy az Azure AD-t úgy konfigurálja, hogy automatikusan kiépítse és dekontálta a felhasználókat és/vagy csoportokat a Leapsome szolgáltatásba.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* Egy [ugró bérlő.](https://www.Leapsome.com/en/pricing)
* A Leapsome rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-leapsome"></a>Felhasználók hozzárendelése a Leapsome-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Leapsome-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Leapsome-hoz rendelheti az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Fontos tippek a felhasználók Leapsome-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Leapsome az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót leapsome-hoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.


## <a name="setup-leapsome-for-provisioning"></a>Setup Leapsome a kiépítéshez

1. Jelentkezzen be [a Leapsome Admin Console - ba](https://www.Leapsome.com/app/#/login). Nyissa meg **a Beállítások > a rendszergazdai beállításokat.**

    ![Ugró ablakkezelő konzol](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Keresse meg **az Integrációk > SCIM-felhasználók kiépítése.**

    ![Ugrásszerűen Add SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Másolja az **SCIM hitelesítési tokenre**. Ezt az értéket a Titkos jogkivonat mezőben adja meg a Leapsome alkalmazás kiépítés lapján az Azure Portalon.

    ![Ugrásszerűen token létrehozása](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Leapsome hozzáadása a galériából

A Leapsome konfigurálása az Azure AD automatikus felhasználói kiépítéshez, hozzá kell adnia leapsome az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Leapsome hozzáadása az Azure AD alkalmazáskatalógusból, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Leapsome**( Leapsome ) lehetőséget, válassza a **Leapsome** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Leapsome az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Automatikus felhasználói kiépítés konfigurálása a Leapsome rendszerbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Leapsome-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy saml-alapú egyszeri bejelentkezés leapsome , utasításait követve a [Leapsome Single sign-on tutorial](Leapsome-tutorial.md). Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>A Leapsome automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Leapsome**lehetőséget.

    ![A Leapsome link az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://www.leapsome.com/api/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg a **titkos jogkivonat**korábbi, beolvasott **SCIM hitelesítési token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen a Leapsome-hoz csatlakozni. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Leapsome fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása ugrásszerűen**lehetőséget.

    ![Ugrásszerűen felhasználó leképezések](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Tekintse át az Azure AD-ről leapsome kapcsolatra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Leapsome felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Ugró bb felhasználói attribútumok](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása ugrásszerűen**lehetőséget.

    ![Ugrásszerűen csoportosított leképezések](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Tekintse át az Azure AD-ről leapsome kapcsolatra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Leapsome csoportban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Ugró csoport attribútumai](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez leapsome, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket leapaly-ra szeretne kiépíteni a **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Leapsome-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Leapsome igényel **userName,** hogy egyedi.
* Leapsome csak lehetővé teszi a munka e-mail címeket kell menteni.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
