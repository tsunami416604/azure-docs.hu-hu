---
title: 'Oktatóanyag: Figma felhasználók automatikus átadása az Azure Active Directory konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Figma felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: b71aa6709b1c93688ea3eece4ce3f4066f9a6b7a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673170"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Figma konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Figma és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Figma bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Egy Azure AD-bérlő.
* [A bérlő egy Figma](https://www.figma.com/pricing/).
* Egy felhasználói fiókot a Figma rendszergazdai jogosultságokkal rendelkezik.

## <a name="assign-users-to-figma"></a>Felhasználók hozzárendelése Figma.
Az Azure Active Directory egy fogalom, más néven hozzárendeléseket használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Figma hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Figma utasításokat követve:
 
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Felhasználók hozzárendelése Figma fontos tippek

 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Figma a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Figma, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva, a létesítési.

## <a name="set-up-figma-for-provisioning"></a>Üzembe helyezés Figma beállítása

Az Azure AD-felhasználók automatikus Figma konfigurálja, mielőtt kell Figma lévő egyes kiépítési információk lekéréséhez.

1. Jelentkezzen be a [Figma felügyeleti konzol](https://www.Figma.com/). Kattintson a fogaskerék ikonra a bérlő mellett.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Navigáljon a **általános > jelentkezzen be a beállítások frissítése**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Másolás a **Bérlőazonosító**. Ezt az értéket fogja segítségével hozza létre az SCIM végponti URL-cím adható meg, a **bérlői URL-cím** mezőt a kiépítés lapon Figma alkalmazását az Azure Portalon.

    ![Figma jogkivonat létrehozása](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Görgessen lefelé, és kattintson a **API jogkivonat készítése**.

    ![Figma jogkivonat létrehozása](media/Figma-provisioning-tutorial/token.png)

5. Másolás a **API-jogkivonat** értéket. Ezt az értéket fog megadni az **titkos jogkivonat** mezőt a kiépítés lapon Figma alkalmazását az Azure Portalon. 

    ![Figma jogkivonat létrehozása](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Figma hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus Figma konfigurálásához szüksége az Azure AD alkalmazáskatalógusában Figma hozzáadása a felügyelt SaaS-alkalmazások listája.

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Figma**válassza **Figma** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Figma](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Figma történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Figma.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Figma engedélyezése, a biztonsági utasítások megadott a [Figma egyszeri bejelentkezéses oktatóanyag](figma-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Figma az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Figma**.

    ![Az alkalmazások listáját a Figma hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://www.figma.com/scim/v2/<TenantID>` a **bérlői URL-cím** ahol **TenantID** lekért Figma korábban megadott érték. Bemenet a **API-jogkivonat** értékét **titkos jogkivonat**. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Figma csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Figma fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Figma**.

    ![Figma Felhasználóleképezéseket](media/Figma-provisioning-tutorial/figma05.png)

11. Tekintse át a Figma a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Figma levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Figma felhasználói attribútumok](media/Figma-provisioning-tutorial/figma06.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás Figma engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. A felhasználók és/vagy a kívánt csoportok definiálása Figma való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Figma által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)