---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Zscaler |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Zscaler felhasználói fiókokat.
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
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 535c02b351c2a12261afd6ba09ea2645af0f06d3
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57577628"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Zscaler konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Zscaler és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Zscaler bemutatásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).

> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

*   Az Azure AD-bérlő
*   Zscaler-bérlői
*   A Zscaler rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler SCIM API elérhető a Zscaler fejlesztők számára a vállalati csomaggal rendelkező fiókok esetében.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a katalógusból
Az Azure AD-felhasználók automatikus Zscaler konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában Zscaler hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Zscaler hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]

3. Zscaler hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Zscaler**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/app-search.png)

5. Az eredmények panelen válassza ki a **Zscaler**, majd kattintson a **hozzáadása** gombra kattintva adhat hozzá a Zscaler a SaaS-alkalmazások listájára.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/app-search-results.png)

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/app-creation.png)

## <a name="assigning-users-to-zscaler"></a>Felhasználók hozzárendelése Zscaler

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Zscaler hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Zscaler utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Felhasználók hozzárendelése Zscaler fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Zscaler a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Zscaler, jelöljön ki minden olyan érvényes alkalmazás-specifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Zscaler történő automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Zscaler.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés a Zscaler engedélyezése, a biztonsági utasítások megadott a [Zscaler egyszeri bejelentkezéses oktatóanyag](zscaler-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Zscaler az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Válassza ki a Zscaler SaaS-alkalmazások listájából.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/app-instance-search.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a Zscaler-fiók, 6. lépésben leírtak szerint.

6. Beszerzése a **bérlői URL-cím** és **titkos jogkivonat**, navigáljon a **felügyelet > hitelesítési beállítások** a Zscaler portál felhasználói felülete, és kattintson a **SAML** alatt **hitelesítési típus**. 

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kattintson a **SAML konfigurálása** megnyitásához **konfigurációs SAML** beállítások. 

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/secret-token-2.png)
    
    Válassza ki **Enable SCIM-Based kiépítés** lekéréséhez **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Másolás a **alap URL-cím** való **bérlői URL-cím**, és **tulajdonosi jogkivonat** való **titkos jogkivonat** az Azure Portalon.

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Zscaler csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Zscaler fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/test-connection.png)
    
8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Zscaler**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Tekintse át a Zscaler a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Zscaler levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Zscaler**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Tekintse át a szinkronizált Azure AD-ből, a Zscaler oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zscaler csoportjai megfelelően használt. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Adja meg a felhasználók és/vagy a csoportok, adja meg Zscaler való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zscaler-kiépítés](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Zscaler által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
