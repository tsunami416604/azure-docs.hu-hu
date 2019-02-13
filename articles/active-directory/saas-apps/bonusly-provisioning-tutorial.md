---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Bonusly |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Bonusly felhasználói fiókokat.
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
ms.date: 06/27/2018
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c14de0d09006aefb342590f613edb129ab5016
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173708"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Bonusly konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Bonusly és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Bonusly bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

*   Az Azure AD-bérlő
*   A [Bonusly bérlő](https://bonus.ly/pricing)
*   Bonusly rendszergazdai engedélyekkel rendelkező felhasználói fiók

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Bonusly Rest API](https://bonusly.gelato.io/reference), amely Bonusly a fejlesztők rendelkezésére áll.

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly hozzáadása a katalógusból
Mielőtt konfigurálná a Bonusly a felhasználók automatikus átadása az Azure ad-vel, szüksége az Azure AD alkalmazáskatalógusában Bonusly hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Bonusly hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Bonusly hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Bonusly**.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **Bonusly**, majd kattintson a **hozzáadása** gombra kattintva adhat hozzá Bonusly a SaaS-alkalmazások listájára.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/AppSearchResults.png)

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-bonusly"></a>Felhasználók hozzárendelése Bonusly

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak. 

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Bonusly hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Bonusly utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Felhasználók hozzárendelése Bonusly fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Bonusly a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Bonusly, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Bonusly történő automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Bonusly.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Bonusly engedélyezése, a biztonsági utasítások megadott a [Bonusly egyszeri bejelentkezéses oktatóanyag](bonus-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Bonusly az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Válassza ki a Bonusly SaaS-alkalmazások listájából.
 
    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/AppInstanceSearch.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **titkos jogkivonat** a Bonusly a fiók a 6. lépésben leírtak szerint.

6. A **titkos jogkivonat** a Bonusly fiók találhatók **rendszergazda > Vállalati > Integrációk**. Az a **Ha kód** területén kattintson a **API > új API Access Token létrehozása** hozhat létre egy új titkos jogkivonat.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. A következő képernyőn adjon meg egy nevet a hozzáférési jogkivonatot a megadott szövegmezőbe, majd nyomja le az **Api-kulcs létrehozása**. Az új jogkivonat néhány másodpercet a egy előugró ablak jelenik meg.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/Token02.png)

8. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Bonusly csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Bonusly fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/TestConnection.png)
    
9. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Kattintson a **Save** (Mentés) gombra.

11. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Bonusly**.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Tekintse át a Bonusly a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Bonusly levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Az Azure AD létesítési szolgáltatás Bonusly engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. A felhasználók és/vagy a kívánt csoportok definiálása Bonusly való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Bonusly kiépítése](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Bonusly által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
