---
title: 'Oktatóanyag: A Dropbox for Business konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Dropbox for Business szolgáltatásba.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058422"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: A Dropbox for Business konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Dropbox for Business és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket, hogy az Azure AD automatikusan kiépítse és dekonfektusát a Dropbox for Business szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Dropbox üzleti bérlő](https://www.dropbox.com/business/pricing)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Dropbox vállalati verzióban.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dropbox hozzáadása a vállalkozásoknak a galériából

A Dropbox for Business konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, hozzá kell adnia a Dropbox for Business-t az Azure AD alkalmazásgalériából a felügyelt SaaS-alkalmazások listájához.

**Ha a Dropbox for Businessalkalmazást az Azure AD alkalmazásgalériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Dropbox for Business**kifejezést, válassza az eredménypanel **Dropbox vállalati verzió** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Dropbox vállalati verzió az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése a Dropbox vállalati verzióhoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Dropbox for Business szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Dropbox for Business-hez az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Fontos tippek a felhasználók Dropbox vállalati verzióhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Dropbox vállalati verzióhoz az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Dropbox vállalati verzióhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Automatikus felhasználói kiépítés konfigurálása a Dropbox for Business szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Dropbox for Businessban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Dropbox for Business számára, a [Dropbox for Business egyszeri bejelentkezési oktatóanyagutasításait](dropboxforbusiness-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>A Dropbox for Business automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Dropbox for Business**lehetőséget.

    ![A Dropbox vállalati verzió hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** kattintson az **Engedélyezés gombra.** Megnyílik egy Dropbox for Business bejelentkezési párbeszédablak egy új böngésző ablakban.

    ![Kiépítés ](common/provisioning-oauth.png)

6. A **Dropbox for Business bejelentkezésaz Azure AD-párbeszédablakhoz való kapcsolódáshoz** jelentkezzen be a Dropbox vállalati verzió bérlői szolgáltatásába, és ellenőrizze személyazonosságát.

    ![Dropbox vállalati verziós bejelentkezés](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Dropbox vállalati verziós fiókja rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-oauth.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Dropbox mappába**lehetőséget.

    ![Dropbox felhasználói leképezések](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Tekintse át az Azure AD-ről a Dropboxra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Dropbox felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Dropbox felhasználói attribútumai](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása a Dropbox mappába**lehetőséget.

    ![Dropbox-csoport leképezései](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Tekintse át az Azure AD-ről a Dropboxra szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Dropbox csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Dropbox-csoport attribútumai](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

15. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Dropbox, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Dropboxba, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

17. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Dropboxon végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai
 
* A Dropbox nem támogatja a meghívott felhasználók felfüggesztését. Ha egy meghívott felhasználót felfüggesztenek, az a felhasználó törlődik.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

