---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Zscaler béta |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Zscaler béta felhasználói fiókokat.
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
ms.author: v-ant-msft
ms.openlocfilehash: 54cf2924a94dda1d29baf048c866f019b82e1402
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056452"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Zscaler béta konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Zscaler bétaverziós és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok Zscaler bétaverzióra történő bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

* Az Azure AD-bérlő
* Zscaler béta bérlő
* Zscaler bétaverziós rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler béta SCIM API elérhető a Zscaler béta fejlesztők számára a vállalati csomaggal rendelkező fiókok esetében.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler béta hozzáadása a katalógusból

Konfigurálja az Azure AD-felhasználók automatikus Zscaler bétaverzió, mielőtt szüksége az Azure AD alkalmazáskatalógusában Zscaler béta hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Zscaler béta hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler béta**, jelölje be **Zscaler béta** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Zscaler bétaverzió](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Felhasználók hozzárendelése Zscaler bétaverzió

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Zscaler béta hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Zscaler béta utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Felhasználók hozzárendelése Zscaler béta fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Zscaler béta a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Egy felhasználó Zscaler bétaverzióra történő hozzárendelésekor a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Zscaler bétaverzióra történő automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy a Zscaler béta alapján csoportosítja felhasználói és/vagy a csoport-hozzárendelések az Azure ad-ben.

> [!TIP]
> Előfordulhat, hogy is engedélyezését választja SAML-alapú egyszeri bejelentkezés a Zscaler Beta, a biztonsági utasítások megadott a [Zscaler béta egyszeri bejelentkezéses oktatóanyag](zscaler-beta-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása a Zscaler Beta az Azure ad-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza ki **Zscaler béta**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler béta**.

    ![A Zscaler béta hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a Zscaler bétafiókjához 6. lépésben leírtak szerint.

6. Beszerzése a **bérlői URL-cím** és **jogkivonat titkos kulcs**, navigáljon a **Adminisztráció > hitelesítési beállítások** a Zscaler béta portál felhasználói felületén kattintson a  **SAML** alatt **hitelesítési típus**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kattintson a **SAML konfigurálása** megnyitásához a **konfigurációs SAML** beállítások.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Válassza ki **Enable SCIM-Based kiépítés** lekéréséhez **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Másolás a **alap URL-cím** való **bérlői URL-cím**, és **tulajdonosi jogkivonat** való **titkos jogkivonat** az Azure Portalon.

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Zscaler béta csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Zscaler bétafiókjához rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók Zscaler bétaverzióra**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Tekintse át a Zscaler bétaverzióra az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókokat a Zscaler béta a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok Zscaler bétaverzióra**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Tekintse át a Zscaler bétaverzióra az Azure AD-ből szinkronizált oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zscaler béta csoportjai megfelelően szolgálnak. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler Beta engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. A felhasználók és/vagy a kívánt csoportok definiálása Zscaler bétaverzióra történő kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zscaler béta kiépítése](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás béta Zscaler által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
