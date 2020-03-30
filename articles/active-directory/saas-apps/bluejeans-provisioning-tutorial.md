---
title: 'Oktatóanyag: A BlueJeans konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok bluejeans-i automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059012"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Oktatóanyag: A BlueJeans konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a BlueJeans és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok bluejeans-i automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure AD-bérlő
* BlueJeans-bérlő a [Saját vállalat](https://www.BlueJeans.com/pricing) csomaggal vagy jobban engedélyezve
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a BlueJeans-ben

> [!NOTE]
> Az Azure AD-kiépítési integráció a [BlueJeans API-ra](https://BlueJeans.github.io/developer)támaszkodik, amely a Standard csomaggal vagy annál jobb csomaggal érhető el a BlueJeans csapatok számára.

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans hozzáadása a galériából

Mielőtt konfigurálja a BlueJeans automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia a BlueJeans az Azure AD alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a BlueJeans-t az Azure AD alkalmazásgyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **BlueJeans**kifejezést, válassza a **BlueJeans** elemet az eredménypanelen, majd a **Hozzáadás** gombra kattintva adja hozzá az alkalmazást.

    ![BlueJeans az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Felhasználók hozzárendelése a BlueJeans-hez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak a felhasználók és/vagy csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a BlueJeans-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a BlueJeans-hez rendelheti az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Fontos tippek a felhasználók BlueJeans-hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve BlueJeans az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a BlueJeans-hez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Automatikus felhasználói kiépítés konfigurálása a BlueJeans szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a BlueJeans-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a BlueJeans számára, a [BlueJeans egyszeri bejelentkezési oktatóanyagutasításait](bluejeans-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>A BlueJeans automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **BlueJeans**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **BlueJeans**lehetőséget.

    ![A BlueJeans hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja meg a BlueJeans-fiók **rendszergazdai felhasználónevét**és **rendszergazdai jelszavát.** Ilyen értékek például a következők:

   * A **Rendszergazdai felhasználónév** mezőben feltöltsd a bluejeans-i bérlő felügyeleti fiókjának felhasználónevét. Példa: admin@contoso.com.

   * A **Rendszergazdai jelszó** mezőben adja meg a rendszergazdai felhasználónévnek megfelelő jelszót.

6. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a BlueJeans-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a BlueJeans szolgáltatással**lehetőséget.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Tekintse át az Azure AD és a BlueJeans között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a BlueJeans felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

12. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a BlueJeans, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a BlueJeans-ba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![BlueJeans kiépítés](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a BlueJeans-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Bluejeans nem engedélyezi a 30 karakternél hosszabb felhasználóneveket.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
