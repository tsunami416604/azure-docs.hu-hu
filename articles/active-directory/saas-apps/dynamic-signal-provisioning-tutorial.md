---
title: 'Oktatóanyag: A dinamikus védelem a felhasználók automatikus átadása az Azure Active Directory konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének dinamikus jel felhasználói fiókokat.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: f79bc083105f997b08f7cfa6f8e08a8f4f455455
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470549"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása dinamikus jel konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a dinamikus jel és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók, illetve dinamikus jel a csoportok bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [A bérlő egy dinamikus jel](https://dynamicsignal.com/)
* Egy felhasználói fiókot a dinamikus jel rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-dynamic-signal-from-the-gallery"></a>A dinamikus jel hozzáadása a katalógusból

Mielőtt konfigurálná a az Azure AD-felhasználók automatikus dinamikus jel, szüksége az Azure AD alkalmazáskatalógusában dinamikus jel hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában dinamikus jel hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **dinamikus jel**válassza **dinamikus jel** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a dinamikus jel](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Felhasználók hozzárendelése dinamikus jel

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Mielőtt a felhasználók automatikus kiépítés engedélyezése és konfigurálása, akkor meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure AD dinamikus jel hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok dinamikus jel utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Felhasználók hozzárendelése dinamikus jel fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzá van rendelve a felhasználók automatikus konfiguráció teszteléséhez dinamikus jel. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése dinamikus jel, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>A dinamikus jel történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok az Azure AD-ben a felhasználó és/vagy a csoport-hozzárendelések alapján dinamikus jel konfigurálásáról.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés a dinamikus jel engedélyezése, a biztonsági utasítások megadott a [dinamikus jel egyszeri bejelentkezéses oktatóanyag](dynamicsignal-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása számára jelzés dinamikus Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **dinamikus jel**.

    ![A dinamikus jel hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a dinamikus jel fiók 6. lépésben leírtak szerint.

6. A dinamikus jel felügyeleti konzolon lépjen a **Admin > Speciális > API**.

    ![A dinamikus jel kiépítése](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Másolás a **SCIM API URL-Címének** való **bérlői URL-cím**. Kattintson a **új jogkivonat létrehozása** létrehozni egy **tulajdonosi jogkivonat** , és másolja az értéket a **titkos jogkivonat**.

    ![A dinamikus jel kiépítése](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD dinamikus jel csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, a dinamikus jel fiókja rendelkezik rendszergazdai engedélyekkel, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a dinamikus jel**.

    ![A dinamikus jel Felhasználóleképezéseket](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Tekintse át a dinamikus jelre az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a dinamikus védelem a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![A dinamikus jel felhasználói attribútumok](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás dinamikus jel engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. Adja meg a felhasználók és/vagy a csoportok, adja meg dinamikus jel való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a dinamikus jel által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* A dinamikus jel nem támogatja a felhasználó végleges törlése az Azure ad-ből. Törli a felhasználó véglegesen a dinamikus jel, a művelet rendelkezik, a dinamikus jel felügyeleti konzol felhasználói felületén keresztül történjen. 
* A dinamikus jel jelenleg nem támogatja a csoportok.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

