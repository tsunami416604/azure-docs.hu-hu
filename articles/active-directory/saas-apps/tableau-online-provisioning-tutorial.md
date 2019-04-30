---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Tableau Online |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok, a Tableau Online megszüntetése.
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
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123754"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása Tableau Online konfigurálása

Ez az oktatóanyag azt ismerteti, a Tableau Online és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a felhasználók és csoportok, a Tableau Online megszüntetése végrehajtásához szükséges lépéseket.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD felhasználói épül. Ez a szolgáltatás leírása, hogyan működik, és gyakran ismételt kérdések kapcsolatos tudnivalókat lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az Azure Active Directory-as-szoftverszolgáltatások (SaaS) alkalmazások automatizálása](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik:

*   Egy Azure AD-bérlő.
*   A [Tableau Online bérlő](https://www.tableau.com/).
*   Egy felhasználói fiókot a Tableau Online rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Tableau Online Rest API-val](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Az API-t a Tableau Online fejlesztők számára érhető el.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adja hozzá a Tableau Online az Azure Marketplace-ről
Konfigurálása előtt Tableau Online az Azure AD-felhasználók automatikus számára, hozzá Tableau Online az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listája.

A Marketplace-ről, a Tableau Online hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**.

    ![Az Azure Active Directory ikonra](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Tableau Online** válassza **Tableau Online** az eredmény panelen. Az alkalmazás hozzáadásához válassza **Hozzáadás**.

    ![Az eredmények listájában a tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Felhasználók hozzárendelése a Tableau Online

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok, amelyek az alkalmazások az Azure AD hozzá lett rendelve a rendszer szinkronizálja.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, döntse el, mely felhasználók vagy csoportok Azure AD-ben a Tableau online-hoz való hozzáférésre van szükségük. Ezek a felhasználók vagy csoportok hozzárendelése a Tableau Online, kövesse a [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Felhasználók hozzárendelése a Tableau Online fontos tippek

*   Azt javasoljuk, hogy hozzárendelje egyetlen Azure AD-felhasználót a Tableau Online, a felhasználók automatikus konfiguráció teszteléséhez. Hozzárendelhet további felhasználókat vagy csoportokat később.

*   Tableau online-hoz való hozzárendelése egy felhasználó, jelölje ki bármely érvényes alkalmazásspecifikus szerepkör érhető el, ha a hozzárendelés párbeszédpanel. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Felhasználók automatikus átadása a Tableau Online konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás konfigurálásának lépéseit. Vele létrehozása, frissítése és tiltsa le a felhasználókat és csoportokat, a Tableau Online az Azure AD-felhasználó vagy csoport-hozzárendelések alapján.

> [!TIP]
> Emellett engedélyezheti az SAML-alapú egyszeri bejelentkezés a Tableau online-hoz. Kövesse az utasításokat a [Tableau egyszeri bejelentkezéses oktatóprogram](tableauonline-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, azonban ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Felhasználók automatikus átadása a Tableau online-hoz az Azure AD konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások** > **minden alkalmazás** > **Tableau Online**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Tableau Online**.

    ![Az alkalmazások listáját a Tableau Online hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![A tableau Online Kiépítési mód](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adjon meg a tartomány, a rendszergazdai felhasználónevet, a rendszergazdai jelszó és a tartalom URL-CÍMÉT, a Tableau Online-fiók:

   * Az a **tartomány** mezőben adja meg a 6. lépés alapján együtt.

   * Az a **rendszergazdai felhasználónév** mezőben adja meg a rendszergazdai fiók a Clarizen bérlő felhasználóneve. Például: admin@contoso.com.

   * Az a **rendszergazdai jelszó** mezőben adja meg a jelszót a rendszergazdai fiók, amely megfelel a rendszergazda felhasználóneve.

   * Az a **tartalom URL-címe** mezőben adja meg a 6. lépés alapján együtt.

6. Bejelentkezés után a rendszergazda fiókjához a Tableau online-hoz, megjelenik a értékeit **tartomány** és **tartalom URL-címe** az URL-címét a felügyelet lapon.

    * A **tartomány** a Tableau online fiók másolható át ezt az URL-címben:

        ![Tableau Online Domain](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **tartalom URL-címe** a Tableau online fiókot lehet másolni az ebben a szakaszban. Egy érték, amely a fiók beállítása során van definiálva. Ebben a példában az értéke "contoso":

        ![Tableau Online Content URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > A **tartomány** eltérhet az itt láthatótól.

7. 5. lépés látható a mezők kitöltése után válassza ki a **kapcsolat tesztelése** , győződjön meg arról, hogy az Azure AD a Tableau online-hoz csatlakozik. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Tableau Online-fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![Kapcsolat a tableau Online tesztelése](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Az a **értesítő e-mailt** mezőben adja meg a személy e-mail-címét vagy az üzembe helyezési hiba értesítést szeretne kapni a csoportban. Válassza ki a **e-mail-értesítés küldése, ha hiba történik** jelölőnégyzetet.

    ![A tableau Online értesítő e-mailt](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Mentés** gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Tableau**.

    ![A tableau Online felhasználók szinkronizálása](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tekintse át a Tableau online az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Tableau Online frissítési műveletek. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![A tableau Online egyező felhasználói attribútumok](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Tableau**.

    ![A tableau Online csoport szinkronizálása](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tekintse át a Tableau online az Azure AD-ből szinkronizált oszlopainál a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Tableau Online frissítési műveletek. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![A tableau Online egyező csoportattribútumok](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Hatókörszűrő konfigurálásához kövesse az utasításokat a [hatókörkezelési szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ahhoz, hogy az Azure AD létesítési szolgáltatás Tableau online-hoz, az a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

    ![Tableau Online Provisioning Status](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Adja meg a felhasználókat vagy csoportokat, amelyekhez szeretne üzembe helyezni, a Tableau online-hoz. Az a **beállítások** területen válassza ki a kívánt értékeket **hatókör**.

    ![Tableau Online Scope](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Ha készen áll rendelkezésre, válassza ki a **mentése**.

    ![Tableau Online Save](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó vagy csoport meghatározott **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az újabb szinkronizálás hosszabb időt vesz igénybe. Mindaddig, amíg az Azure AD létesítési szolgáltatás fut bekövetkezésük körülbelül 40 percenként. 

Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat, a kiépítési tevékenységek jelentésének szakaszt. A jelentés az Azure AD létesítési szolgáltatás Tableau online által végrehajtott összes műveletet ismerteti.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
