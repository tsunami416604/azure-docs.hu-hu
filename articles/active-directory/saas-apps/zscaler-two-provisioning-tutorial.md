---
title: 'Oktatóanyag: A Zscaler 2 konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok zscaler 2-re történő automatikus kiépítésére és megszüntetésére.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 364b106e7c1f01269ac02b0c2851f8824ea0f58c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062693"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler 2 konfigurálása automatikus felhasználói kiépítéshez

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt (Azure AD) a felhasználók és/vagy csoportok zscaler 2-re történő automatikus kiépítésére és megszüntetésére.

> [!NOTE]
> Ez az oktatóanyag ismerteti az Azure AD felhasználói létesítési szolgáltatásra épülő összekötő. A szolgáltatás működésével és működésével kapcsolatos fontos részleteket, valamint a gyakori kérdésekre adott válaszokat a [Felhasználói kiépítés és a SaaS-alkalmazások létesítésének automatizálása az Azure Active Directoryval kapcsolatban.](../active-directory-saas-app-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett lépések végrehajtásához a következőkre van szükség:

* Egy Azure AD-bérlő.
* Egy Zscaler 2 bérlő.
* Egy felhasználói fiók a Zscaler 2-ben rendszergazdai engedélyekkel.

> [!NOTE]
> Az Azure AD-kiépítési integráció a Zscaler Two SCIM API-t, amely elérhető a vállalati fiókok.

## <a name="add-zscaler-two-from-the-gallery"></a>Zscaler 2 hozzáadása a galériából

Mielőtt konfigurálja a Zscaler 2 automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia zscaler 2 az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Nyissa meg a **Vállalati alkalmazások,** és válassza a **Minden alkalmazás**lehetőséget:

![Vállalati alkalmazások](common/enterprise-applications.png)

Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

![Új alkalmazás kiválasztása](common/add-new-app.png)

A keresőmezőbe írja be a **Zscaler 2 értéket.** Az eredmények között válassza a **Zscaler Two elemet,** majd a **Hozzáadás**lehetőséget.

![Eredmények listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Felhasználók hozzárendelése a Zscaler 2-höz

Az Azure AD-felhasználóknak hozzáférést kell biztosítani a kiválasztott alkalmazásokhoz, mielőtt használhatnák őket. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Zscaler 2-höz. Miután úgy döntött, hogy ezeket a felhasználókat és csoportokat a Zscaler 2-hez rendelheti hozzá, ha követi a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz című útmutatóutasításait.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Fontos tippek a felhasználók Zscaler 2-hez való hozzárendeléséhez

* Azt javasoljuk, hogy először rendeljen hozzá egy Azure AD-felhasználó zscaler 2 az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználókat és csoportokat rendelhet hozzá.

* Amikor egy felhasználót hozzárendel a Zscaler 2-höz, a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-automatic-user-provisioning"></a>Automatikus felhasználói kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit a felhasználók és csoportok létrehozására, frissítésére és letiltására a Zscaler 2-ben az Azure AD felhasználói és csoport-hozzárendelései alapján.

> [!TIP]
> Előfordulhat, hogy saml-alapú egyszeri bejelentkezést is engedélyezni szeretne a Zscaler 2 számára. Ha igen, kövesse az utasításokat a [Zscaler Két egyszeri bejelentkezés bemutató](zscaler-two-tutorial.md). Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, de a két funkció kiegészíti egymást.

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **Zscaler 2**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler Two lehetőséget:**

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza a **Kiépítés** lapot:

    ![Zscaler két kiépítés](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **létesítési módot** **automatikusra:**

    ![A kiépítési mód beállítása](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a zscaler két fiók **bérlői URL-címét** és **titkos jogkivonatát,** a következő lépésben leírtak szerint.

6. A **bérlői URL és** a **titkos jogkivonat**lekéréséhez nyissa meg a **Cifikáta** > két portál felügyeleti**hitelesítési beállításait,** és válassza az **SAML** lehetőséget a **Hitelesítés típusa csoportban:**

    ![Zscaler két hitelesítési beállítások](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Az **SAML konfigurálása** lehetőséget választ az **SAML konfigurálása** ablak megnyitásához:

    ![SAML ablak konfigurálása](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Válassza **az Scim-alapú kiépítés engedélyezése** lehetőséget, és másolja az alap **URL-címet** és a **tulajdonosi tokent,** majd mentse a beállításokat. Az Azure Portalon illessze be az **alap URL-címet** a **bérlői URL-címbe** és a **tulajdonosi jogkivonatot** a **titkos jogkivonatmezőbe.**

7. Miután megadta az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezőkben, válassza **a Kapcsolat tesztelése** lehetőséget, hogy az Azure AD képes-e csatlakozni a Zscaler 2-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Zscaler Two fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![A kapcsolat tesztelése](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, amelynek meg kell kapnia a létesítési hibaértesítéseket. Válassza **az E-mail értesítés küldése hiba esetén**lehetőséget:

    ![Értesítési e-mail beállítása](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a ZscalerTwo rendszerhez**lehetőséget:

    ![Az Azure AD-felhasználók szinkronizálása](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD és a Zscaler 2 között szinkronizált felhasználói attribútumokat az **Attribútumleképezések** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler 2 felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Attribútumleképezések](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a ZscalerTwo szolgáltatással**lehetőséget:

    ![Azure AD-csoportok szinkronizálása](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD és a Zscaler 2 között szinkronizált csoportattribútumokat az **Attribútumleképezések** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Zscaler 2 csoportban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    ![Attribútumleképezések](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagának utasításait.](./../active-directory-saas-scoping-filters.md)

15. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Zscaler 2, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban:

    ![Kiépítés állapota](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Zscaler 2-be, ha a **Beállítások** szakasz **Hatókör csoportjában** kiválasztja a kívánt értékeket:

    ![Hatókör-értékek](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Ha készen áll a kiépítésre, válassza a **Mentés lehetőséget:**

    ![Mentés kiválasztása](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **e** területen definiált összes felhasználójának és csoportjának kezdeti **szinkronizálását.** A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, mindaddig, amíg az Azure AD-kiépítési szolgáltatás fut. A **folyamatot** a Szinkronizálás részletei szakaszban figyelheti. A kiépítési tevékenységjelentésre mutató hivatkozásokat is követhet, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Zscaler 2-n végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítéséről szóló jelentéskészítés című](../active-directory-saas-provisioning-reporting.md)témakörben olvashat.

## <a name="additional-resources"></a>További források

* [A felhasználói fiókok vállalati alkalmazásokhoz való kiépítésének kezelése](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
