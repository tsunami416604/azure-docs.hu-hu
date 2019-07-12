---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Leapsome |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Leapsome felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672697"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Leapsome konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Leapsome és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Leapsome bemutatása.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Egy Azure AD-bérlő.
* A [Leapsome](https://www.Leapsome.com/en/pricing) bérlő.
* Egy felhasználói fiókot a Leapsome rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-leapsome"></a>Felhasználók hozzárendelése Leapsome

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Leapsome hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Leapsome utasításokat követve:
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Felhasználók hozzárendelése Leapsome fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Leapsome a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Leapsome, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.


## <a name="setup-leapsome-for-provisioning"></a>A telepítő Leapsome kiépítéshez

1. Jelentkezzen be a [Leapsome felügyeleti konzol](https://www.Leapsome.com/app/#/login). Navigáljon a **beállítások > adminisztrátori beállítások**.

    ![Leapsome felügyeleti konzol](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navigáljon a **Integrációk > SCIM felhasználókiépítés**.

    ![Leapsome SCIM hozzáadása](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Másolás a **SCIM-hitelesítési tokent**. Ezt az értéket kell megadni a kiépítés lapon Leapsome alkalmazását az Azure Portalon a jogkivonat titkos kulcs mezőjében.

    ![Leapsome jogkivonat létrehozása](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Leapsome hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus Leapsome konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában Leapsome hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Leapsome hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Leapsome**válassza **Leapsome** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Leapsome](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Leapsome történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Leapsome.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Leapsome engedélyezése, a biztonsági utasítások megadott a [Leapsome egyszeri bejelentkezéses oktatóanyag](Leapsome-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Leapsome az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Leapsome**.

    ![Az alkalmazások listáját a Leapsome hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://www.leapsome.com/api/scim` a **bérlői URL-cím**. Bemenet a **SCIM-hitelesítési Token** értékét a korábban kapott **titkos jogkivonat**. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Leapsome csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Leapsome fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Leapsome**.

    ![Leapsome Felhasználóleképezéseket](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Tekintse át a Leapsome a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Leapsome levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Leapsome felhasználói attribútumok](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Leapsome**.

    ![Leapsome csoport-hozzárendelések](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Tekintse át a csoportattribútumok Leapsome a az Azure AD-ből szinkronizált a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek a Leapsome a frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Leapsome csoportattribútumok](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás Leapsome engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. A felhasználók és/vagy a kívánt csoportok definiálása Leapsome való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Leapsome által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Leapsome igényel **felhasználónév** egyedinek kell lennie.
* Leapsome csak lehetővé teszi, hogy a munkahelyi e-mail-címek menteni.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
