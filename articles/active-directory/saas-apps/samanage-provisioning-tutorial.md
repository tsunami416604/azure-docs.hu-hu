---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Samanage |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok Samanage megszüntetése.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670920"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Samanage konfigurálása

Ez az oktatóanyag bemutatja a Samanage és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a felhasználók és csoportok Samanage megszüntetése végrehajtásához szükséges lépéseket.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD felhasználói épül. Ez a szolgáltatás leírása, hogyan működik, és gyakran ismételt kérdések kapcsolatos tudnivalókat lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az Azure Active Directory-as-szoftverszolgáltatások (SaaS) alkalmazások automatizálása](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik:

* Egy Azure AD-bérlő.
* A [Samanage bérlői](https://www.samanage.com/pricing/) a professzionális csomag.
* Egy felhasználói fiókot a Samanage rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Samanage Rest API-val](https://www.samanage.com/api/). Az API Samanage a fejlesztők a professzionális csomag rendelkező fiókokhoz érhető el.

## <a name="add-samanage-from-the-azure-marketplace"></a>Az Azure Marketplace-ről Samanage hozzáadása

Mielőtt Samanage konfigurál az Azure AD-felhasználók automatikus számára, hozzá Samanage az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listája.

A Marketplace-ről Samanage hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**.

    ![Az Azure Active Directory ikonra](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Samanage** válassza **Samanage** az eredmény panelen. Az alkalmazás hozzáadásához válassza **Hozzáadás**.

    ![Az eredmények listájában Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Felhasználók hozzárendelése Samanage

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok, amelyek az alkalmazások az Azure AD hozzá lett rendelve a rendszer szinkronizálja.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, döntse el, mely felhasználók vagy csoportok Azure AD-ben Samanage hozzáférésre van szükségük. Ezek a felhasználók vagy csoportok hozzárendelése Samanage, kövesse a [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage fontos tippek

*    Még ma, Samanage szerepköröket a rendszer dinamikusan és automatikusan kitölti az Azure Portal felhasználói felületén. Mielőtt Samanage szerepkört rendel felhasználókhoz, ellenőrizze, hogy egy kezdeti szinkronizálást befejeződött-e lekérni a legújabb szerepkörök Samanage bérlőben Samanage ellen.

*    Azt javasoljuk, hogy hozzárendelje egyetlen Samanage a kezdeti felhasználók automatikus konfiguráció tesztelése az Azure AD-felhasználót. Hozzárendelhet további felhasználókat és csoportokat később követően a tesztek sikere esetén.

*    Ha egy felhasználó Samanage rendeli, válassza ki bármilyen érvényes alkalmazásspecifikus szerepkör érhető el, ha a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Felhasználók automatikus átadása az Samanage konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás konfigurálásának lépéseit. Vele létrehozása, frissítése és tiltsa le a felhasználók vagy csoportok Samanage az Azure AD-felhasználó vagy csoport-hozzárendelések alapján.

> [!TIP]
> Emellett engedélyezheti az SAML-alapú egyszeri bejelentkezés az Samanage. Kövesse az utasításokat a [Samanage egyszeri bejelentkezéses oktatóanyag](samanage-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, azonban ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurálja a felhasználók automatikus átadása Samanage az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások** > **minden alkalmazás** > **Samanage**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Samanage**.

    ![Az alkalmazások listáját a Samanage hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Samanage Kiépítési mód](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a rendszergazdai felhasználónevét és a Samanage fiók rendszergazdai jelszavát. Példák a következő értékek a következők:

   * Az a **rendszergazdai felhasználónév** mezőben adja meg a rendszergazdai fiók a Samanage bérlő felhasználóneve. Például: admin@contoso.com.

   * Az a **rendszergazdai jelszó** mezőben adja meg a jelszót a rendszergazdai fiók, amely megfelel a rendszergazda felhasználóneve.

6. 5\. lépés látható a mezők kitöltése után válassza ki a **kapcsolat tesztelése** , győződjön meg arról, hogy az Azure AD Samanage csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, hogy a Samanage rendszergazdai engedélyekkel rendelkezzen, majd próbálkozzon újra.

    ![Kapcsolat Samanage tesztelése](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Az a **értesítő e-mailt** mezőben adja meg a személy e-mail-címét vagy az üzembe helyezési hiba értesítést szeretne kapni a csoportban. Válassza ki a **e-mail-értesítés küldése, ha hiba történik** jelölőnégyzetet.

    ![Samanage értesítő e-mailt](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Mentés** gombra.

9. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Samanage**.

    ![Samanage felhasználók szinkronizálása](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Tekintse át a Samanage a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Samanage levő felhasználói fiókokat. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Samanage egyező felhasználói attribútumok](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Eszközcsoport-leképezéseket, alatt engedélyezéséhez a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Samanage**.

    ![Samanage csoport szinkronizálása](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Állítsa be **engedélyezve** való **Igen** csoportok szinkronizálásához. Tekintse át a csoportattribútumok Samanage a az Azure AD-ből szinkronizált a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Samanage levő felhasználói fiókokat. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Samanage egyező csoportattribútumok](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Hatókörszűrő konfigurálásához kövesse az utasításokat a [hatókörkezelési szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Ahhoz, hogy az Azure AD létesítési szolgáltatás a Samanage, az a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

    ![Samanage kiépítési állapot](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Adja meg a felhasználókat vagy csoportokat, amelyekhez szeretne Samanage való kiépítéséhez. Az a **beállítások** területen válassza ki a kívánt értékeket **hatókör**. Amikor kiválasztja a **minden felhasználó és csoport szinkronizálása** lehetőséget, vegye figyelembe a korlátozások "Összekötő korlátozásokat." a következő szakaszban leírtak szerint

    ![Samanage hatókör](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Ha készen áll rendelkezésre, válassza ki a **mentése**.

    ![Samanage mentése](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó vagy csoport meghatározott **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az újabb szinkronizálás hosszabb időt vesz igénybe. Mindaddig, amíg az Azure AD létesítési szolgáltatás fut bekövetkezésük körülbelül 40 percenként. 

Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat, a kiépítési tevékenységek jelentésének szakaszt. A jelentés az Azure AD létesítési szolgáltatás a Samanage által végrehajtott összes műveletet ismerteti.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

Ha a **minden felhasználó és csoport szinkronizálása** lehetőséget, és a egy értéket a Samanage konfigurálása **szerepkörök** attribútum, az értéket a **alapértelmezett érték null esetén (nem kötelező)** mezőbe a következő formátumban kell megadni:

- {"displayName": "szerepkör"}, ahol az szerepkör alapértelmezett értéket.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
