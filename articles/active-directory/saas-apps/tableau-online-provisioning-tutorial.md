---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Tableau Online |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének felhasználói fiókokat, a Tableau Online.
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
ms.openlocfilehash: f732eebd410a6b52a21a46925a29bf4676f7c8cb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270786"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása Tableau Online konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Tableau Online és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok, a Tableau Online bemutatásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

*   Az Azure AD-bérlő
*   A [Tableau Online bérlő](https://www.tableau.com/)
*   A Tableau Online rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Tableau Online Rest API-val](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), a Tableau Online fejlesztők számára elérhető.

## <a name="adding-tableau-online-from-the-gallery"></a>A katalógusból Tableau Online hozzáadása
A felhasználók automatikus átadása az Azure AD-, Tableau Online konfigurálása előtt hozzá kell Tableau Online az Azure AD alkalmazáskatalógusában a felügyelt SaaS-alkalmazások listájára.

**Adhat hozzá a Tableau Online az Azure AD alkalmazáskatalógusában, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Tableau Online**válassza **Tableau Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a tableau Online](common/search-new-app.png)

## <a name="assigning-users-to-tableau-online"></a>Felhasználók hozzárendelése a Tableau Online

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Mielőtt a felhasználók automatikus kiépítés engedélyezése és konfigurálása, akkor meg kell határoznia, mely felhasználók, illetve a csoportok az Azure ad-ben a Tableau online-hoz való hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Tableau Online utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Felhasználók hozzárendelése a Tableau Online fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Tableau Online a felhasználók automatikus konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Egy felhasználó a Tableau Online hozzárendelésekor jelöljön ki minden olyan érvényes alkalmazás-specifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>A Tableau Online automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok, a Tableau Online az Azure ad-ben a felhasználó és/vagy a csoport-hozzárendelések alapján történő konfigurálásáról.

> [!TIP]
> Előfordulhat, hogy meg SAML-alapú egyszeri bejelentkezés engedélyezése Tableau online, a biztonsági utasítások megadott a [Tableau egyszeri bejelentkezéses oktatóprogram](tableauonline-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása a Tableau online-hoz az Azure ad-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Tableau Online**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Tableau Online**.

    ![Az alkalmazások listáját a Tableau Online hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **tartomány**, **rendszergazdai felhasználónév**, **rendszergazdai jelszó**, és **tartalom URL-cím** , a Tableau Online-fiók:

   * Az a **tartomány** mezőben 6. lépés alapján altartomány feltöltéséhez.

   * Az a **rendszergazdai felhasználónév** mezőben töltse fel a rendszergazdai fiók a Clarizen bérlő felhasználóneve. Példa: admin@contoso.com.

   * Az a **rendszergazdai jelszó** mezőben töltse ki a megfelelő rendszergazdai felhasználónév rendszergazdai fiók jelszavát.

   * Az a **tartalom URL-címe** mezőben 6. lépés alapján altartomány feltöltéséhez.

6. Miután bejelentkezett a rendszergazdai fiók a Tableau online-hoz, a tartozó értékeket **tartomány** és **tartalom URL-címe** kinyerésének URL-címét a felügyelet lapon.

    * A **tartomány** a Tableau online fiók másolható át ezt az URL-címben:

        ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A **tartalom URL-címe** a Tableau online fiókot lehet másolni az ebben a szakaszban, és a fiók beállítása során egy értéket számít. Ebben a példában az értéke "contoso":

        ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > A **tartomány** eltérhetnek az itt láthatótól.

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD a Tableau Online csatlakozhatnak. Ha a kapcsolat hibája esetén, győződjön meg arról, a Tableau Online-fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Tableau**.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tekintse át a Tableau online az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Tableau Online frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Tableau**.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tekintse át a Tableau online az Azure AD-ből szinkronizált oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Tableau Online frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás Tableau online engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. A felhasználók és/vagy a csoportok, adja meg a Tableau Online kiépíteni definiálása válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![A tableau Online kiépítése](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás Tableau online által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
