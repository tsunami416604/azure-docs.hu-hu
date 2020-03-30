---
title: 'Oktatóanyag: Az Atlassian Cloud konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat az Atlassian Cloud szolgáltatásba.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059334"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: Az Atlassian Cloud konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa az Atlassian Cloud és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonzisztsa a felhasználókat és/vagy csoportokat az Atlassian Cloud szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Atlassian Cloud-bérlő](https://www.atlassian.com/licensing/cloud)
* Az Atlassian Cloud rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

> [!NOTE]
> Az Azure AD-kiépítési integráció az **Atlassian Cloud SCIM API-ra**támaszkodik, amely az Atlassian Cloud csapatok számára érhető el.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud hozzáadása a galériából

Mielőtt konfigurálna az Atlassian Cloud-ot az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia az Atlassian Cloud-ot az Azure AD alkalmazásgalériából a felügyelt SaaS-alkalmazások listájához.

**Az Atlassian Cloud hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Atlassian Cloud**kifejezést, válassza az **Atlassian Cloud** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Atlassian Cloud az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Felhasználók hozzárendelése az Atlassian Cloud szolgáltatáshoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük az Atlassian Cloud szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az Atlassian Cloud-hoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Fontos tippek a felhasználók Atlassian Cloud hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az Atlassian Cloud az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót az Atlassian Cloud-hoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Automatikus felhasználói kiépítés konfigurálása az Atlassian Cloud szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le az Atlassian Cloudban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az Atlassian Cloud szolgáltatáshoz az [Atlassian Cloud egyszeri bejelentkezési oktatóanyagutasításait követve.](atlassian-cloud-tutorial.md) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Az Atlassian Cloud automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza **az Összes alkalmazás**lehetőséget, majd válassza az **Atlassian Cloud**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Atlassian Cloud**lehetőséget.

    ![Az Atlassian Cloud hivatkozás a Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Nyissa meg az [Atlassian Szervezeti igazgató](https://admin.atlassian.com) **> válassza ki a > könyvtárat.**

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Kattintson **a Felhasználói kiépítés gombra,** majd a **Könyvtár létrehozása**gombra. Másolja a **címtár alap URL-címét** és **a tulajdonosi jogkivonatot** a **bérlői URL-címbe** és a **titkos jogkivonat** mezőkbe.

    ![Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![kiépítés Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Cloud kiépítés Atlassian cloud kiépítés](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** és az Atlassian Cloud-fiók **titkos jogkivonatát.** Ilyen értékek például a következők:

   * A **Bérlő URL-cím** mezőjében töltse ki az Atlassian tól kapott adott bérlői végpontot, a 6. Például: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * A **Titkos jogkivonat** mezőben a 6.

8. A 7. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy atlaszi felhőfiókja rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Kattintson a **Mentés** gombra.

11. A **Leképezések** csoportban válassza az **Azure Active Directory-felhasználók szinkronizálása az Atlassian Cloud szolgáltatással**lehetőséget.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Tekintse át az Azure AD-ről az Atlassian Cloud szolgáltatással szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kiválasztott attribútumok az Atlassian Cloud felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása atlaszfelhővel**lehetőséget.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Tekintse át az Azure AD és az Atlassian Cloud között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kiválasztott attribútumok az Atlassian Cloud csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

16. Az Atlassian Cloud Azure AD-kiépítési szolgáltatásának engedélyezéséhez módosítsa a **Kiépítés állapotát** **be beállításra** a **Beállítások** szakaszban.

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az Atlassian Cloud szolgáltatásba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Atlassian felhőkiépítés](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által az Atlassian Cloud-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* Az Atlassian Cloud csak ellenőrzött tartományokból engedélyezi a felhasználók [kiépítését.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* Az Atlassian Cloud ma nem támogatja a csoportátnevezést. Ez azt jelenti, hogy az Azure AD-ben egy csoport displayName-jének módosításai nem frissülnek, és nem jelennek meg az Atlassian Cloud ban.
* A **levelezési** felhasználói attribútum értéke az Azure AD-ben csak akkor van feltöltve, ha a felhasználó rendelkezik egy Microsoft Exchange-postaládával. Ha a felhasználó nem rendelkezik ilyen, ajánlott egy másik kívánt attribútum hozzárendelése az Atlassian Cloud **e-mail attribútumhoz.**

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png