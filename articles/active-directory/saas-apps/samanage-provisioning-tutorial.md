---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval Samanage konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Samanage felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: e5a69fa2ee9a8c4baaeb6586627c7a9a3c9ba4a8
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347953"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Samanage konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Samanage és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Samanage bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

*   Az Azure AD-bérlő
*   A [Samanage bérlői](https://www.samanage.com/pricing/) a professzionális csomag
*   Samanage rendszergazdai engedélyekkel rendelkező felhasználói fiók

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Samanage Rest API-val](https://www.samanage.com/api/), a fiókok esetén a Professional csomag a Samanage fejlesztők számára elérhető.

## <a name="adding-samanage-from-the-gallery"></a>Samanage hozzáadása a katalógusból
Az Azure AD-felhasználók automatikus Samanage konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában Samanage hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Samanage hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]

3. Samanage hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Samanage**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **Samanage**, majd kattintson a **hozzáadása** gombra kattintva adhat hozzá Samanage a SaaS-alkalmazások listájára.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Samanage hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Samanage utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Samanage a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Samanage, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Samanage történő automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Samanage.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Samanage engedélyezése, a biztonsági utasítások megadott a [Samanage egyszeri bejelentkezéses oktatóanyag](samanage-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Samanage az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Válassza ki a Samanage SaaS-alkalmazások listájából.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. Válassza ki a **kiépítési** fülre.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **rendszergazdai felhasználónév** és **rendszergazdai jelszó** Samanage fiókja. Példák a következő értékek a következők:

    *   Az a **rendszergazdai felhasználónév** mezőben töltse fel a rendszergazdai fiók a Samanage bérlő felhasználóneve. Példa: admin@contoso.com.

    *   Az a **rendszergazdai jelszó** mezőben töltse ki a megfelelő rendszergazdai felhasználónév rendszergazdai fiók jelszavát.

6. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Samanage csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Samanage fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Samanage**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Tekintse át a Samanage a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Samanage levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Eszközcsoport-leképezéseket, alatt engedélyezéséhez a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Samanage**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Állítsa be **engedélyezve** való **Igen** csoportok szinkronizálásához. Tekintse át a csoportattribútumok Samanage a az Azure AD-ből szinkronizált a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Samanage levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Az Azure AD létesítési szolgáltatás Samanage engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. A felhasználók és/vagy a kívánt csoportok definiálása Samanage való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Samanage kiépítése](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Samanage által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
