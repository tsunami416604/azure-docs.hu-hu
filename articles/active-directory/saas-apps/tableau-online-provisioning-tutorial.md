---
title: 'Oktatóanyag: A Tableau Online konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Tableau Online-ra való megszüntetésére.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064214"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Oktatóanyag: A Tableau Online konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja a Tableau Online és az Azure Active Directory (Azure AD) végrehajtásának lépéseit az Azure AD konfigurálásához a felhasználók és csoportok tablóonline automatikus kiépítéséhez és kiteljesítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatásra épül. A szolgáltatás működéséről, működéséről és a gyakori kérdésekről a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása az Azure Active Directoryval című](../app-provisioning/user-provisioning.md)témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy a következőket ismerteel:

*   Egy Azure AD-bérlő.
*   Egy [Tableau Online-bérlő](https://www.tableau.com/).
*   A Tableau Online rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

> [!NOTE]
> Az Azure AD-kiépítési integráció a [Tableau Online Rest API-t](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)támaszkodja. Ez az API a Tableau Online fejlesztői számára érhető el.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Tableau Online hozzáadása az Azure Piactérről
Mielőtt konfigurálja a Tableau Online-t az Azure AD-vel való automatikus felhasználói kiépítéshez, adja hozzá a Tableau Online-t az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

A Tableau Online marketplace-ről való hozzáadásához kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory ikonja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Tableau Online** kifejezést, és válassza a **Tableau Online** elemet az eredménypanelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Tableau Online az eredménylistában](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Felhasználók hozzárendelése a Tableau Online-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Tableau Online-hoz. Ha ezeket a felhasználókat vagy csoportokat hozzá szeretné rendelni a Tableau Online-hoz, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)című útmutató utasításait.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Fontos tippek a felhasználók Tabló online szolgáltatáshoz való hozzárendeléséhez

*   Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó a Tableau Online az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

*   Amikor egy felhasználót hozzárendel a Tableau Online-hoz, a hozzárendelés párbeszédpanelen jelöljön ki egy érvényes alkalmazásspecifikus szerepkört, ha rendelkezésre áll. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Automatikus felhasználói kiépítés konfigurálása a Tableau Online szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit. Segítségével hozhat létre, frissíthet és letilthatja a felhasználókat vagy csoportokat a Tableau Online-ban az Azure AD felhasználói vagy csoport-hozzárendelései alapján.

> [!TIP]
> Saml-alapú egyszeri bejelentkezést is engedélyezhet a Tableau Online számára. Kövesse a [Tableau Online egyszeri bejelentkezési oktatóanyagutasításait.](tableauonline-tutorial.md) Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Automatikus felhasználói kiépítés konfigurálása az Azure AD-ben a Tableau Online szolgáltatáshoz

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > **kiválasztása Minden alkalmazás,** > **a Tableau Online**.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Tableau Online**lehetőséget.

    ![A Tableau Online hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Tableau Online kiépítés](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Tableau online kiépítési mód](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja meg a Tableau Online-fiók tartományát, rendszergazdai felhasználónevét, rendszergazdai jelszavát és tartalomURL-címét:

   * A **Domain (Tartomány)** mezőben töltse ki az altartományt a 6.

   * A **Rendszergazdai felhasználónév** mezőben adja meg a Clarizen-bérlő rendszergazdai fiókjának felhasználónevét. Például: admin@contoso.com.

   * A **Rendszergazdai jelszó** mezőben adja meg a rendszergazdai felhasználónévnek megfelelő rendszergazdai fiók jelszavát.

   * A **Tartalom URL-címe** mezőben töltse ki az aldomaint a 6.

6. Miután bejelentkezett a Tableau Online felügyeleti fiókjába, a **tartomány** és a **tartalom URL-címének** értékeit a rendszergazdai lap URL-címéről szerezheti be.

    * A Tableau Online-fiók **tartománya** az URL-cím ezen részéből másolható:

        ![Tableau online tartomány](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A Tableau Online-fiók **tartalom-URL-címe** ebből a szakaszból másolható. Ez egy olyan érték, amely a fiók beállítása során van definiálva. Ebben a példában az érték "contoso":

        ![Tableau Online tartalom URL-címe](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > A **tartomány** eltérhet az itt láthatótól.

7. Miután kitöltötte az 5. **Test Connection** Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Tableau Online-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Tableau Online tesztkapcsolat](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Az **Értesítési e-mail mezőbe** írja be a személy vagy csoport e-mail címét a kiépítési hibaértesítések fogadásához. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Tableau Online értesítő e-mail](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Tableau-val**lehetőséget.

    ![Tableau Online felhasználói szinkronizálása](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tekintse át az Azure AD és a Tableau Online között szinkronizált felhasználói attribútumokat az **Attribútum-leképezések** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Tableau Online felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Tableau Online egyező felhasználói attribútumok](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Tableau-val lehetőséget.**

    ![Tableau Online csoport szinkronizálása](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tekintse át az Azure AD és a Tableau Online között szinkronizált csoportattribútumokat az **Attribútum-leképezések** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Tableau Online felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Tableau Online egyező csoport attribútumai](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. A hatókörszűrők konfigurálásához kövesse a [hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Az Azure AD-létesítési szolgáltatás engedélyezése a Tableau Online számára, a **Beállítások** szakaszban módosítsa a **kiépítés állapotát** Be állapotúra. **On**

    ![Tableau Online kiépítési állapota](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Adja meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Tableau Online szolgáltatásba. A **Beállítások csoportban** jelölje ki a kívánt értékeket a **Hatókör területen.**

    ![Tableau online hatóköre](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Tableau Online mentés](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ezek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenység jelentésre mutató hivatkozásokat. A jelentés ismerteti az Azure AD-kiépítési szolgáltatás a Tableau Online által végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
