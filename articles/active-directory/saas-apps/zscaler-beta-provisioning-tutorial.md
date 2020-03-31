---
title: 'Oktatóanyag: A Zscaler Beta konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok zscaler béta verziójára történő automatikus kiépítésére és kiépítésének kimerülésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062729"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler Béta beállítása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Zscaler Beta és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kibontsa a felhasználókat és/vagy csoportokat a Zscaler Béta verziójára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../active-directory-saas-app-provisioning.md)
>


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure AD-bérlő
* A Zscaler Béta bérlő
* A Zscaler Beta felhasználói fiókja rendszergazdai engedélyekkel

> [!NOTE]
> Az Azure AD-kiépítési integráció a Zscaler Beta SCIM API-ra támaszkodik, amely a Zscaler Beta fejlesztők számára érhető el a vállalati csomaggal rendelkező fiókokhoz.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta hozzáadása a galériából

A Zscaler Beta konfigurálása az Azure AD-vel való automatikus felhasználói kiépítéshez, hozzá kell adnia a Zscaler Beta-t az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Zscaler Beta-t az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Beta**kifejezést , válassza a **Zscaler Beta** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler Béta az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Felhasználók hozzárendelése a Zscaler Beta programhoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak a felhasználók és/vagy csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Zscaler Beta szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler Beta-hoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Fontos tippek a felhasználók Zscaler Beta programhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve zscaler béta az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Zscaler Béta értékhez rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Automatikus felhasználói kiépítés konfigurálása a Zscaler Beta rendszerbe

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Zscaler Beta-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Zscaler Beta számára, a [Zscaler Beta egyszeri bejelentkezési oktatóanyagutasításait](zscaler-beta-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>A Zscaler Beta automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Zscaler Beta**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler Beta**lehetőséget.

    ![A Zscaler Béta hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** és a Titkos **jogkivonata** a Zscaler béta fiók a 6.

6. A **bérlői URL és** a **titkos jogkivonat**beszerzéséhez keresse meg a **Felügyeleti > hitelesítési beállítások at** a Zscaler Beta portál felhasználói felületén, és kattintson az **SAML** elemre a **Hitelesítés típusa területen.**

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kattintson az **SAML konfigurálása** gombra az **SAML konfigurációja** beállításainak megnyitásához.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Válassza **az Scim-alapú kiépítés engedélyezése** lehetőséget az alap **URL-cím** és **a tulajdonosi token**lekéréséhez, majd mentse a beállításokat. Másolja az **alap URL-címet** **a bérlői URL-címre,** és **a tulajdonosi jogkivonatot** titkos **jogkivonatba** másolja az Azure Portalon.

7. Az 5. **Test Connection** Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Zscaler Béta fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Bátk-lizóval lehetőséget.**

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD és a Zscaler Beta között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler Béta verzió felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Bifzerbéta szolgáltatással**lehetőséget.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD és a Zscaler Beta között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler Beta csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](./../active-directory-saas-scoping-filters.md)található alábbi utasításokat.

15. Az Azure AD-kiépítési szolgáltatás zscaler béta engedélyezéséhez módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Zscaler Beta alkalmazásba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Zscaler bétakiépítés](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Zscaler Beta- szolgáltatáson végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../active-directory-saas-provisioning-reporting.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
