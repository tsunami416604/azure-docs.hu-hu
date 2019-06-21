---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval 4me konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének 4me felhasználói fiókokat.
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
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: 0cfd760eb9d631bf6ab098afef0ef9b66c92cfa6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168154"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása 4me konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a 4me és az Azure Active Directory (Azure AD) konfigurálása automatikus kiépítésének és megszüntetésének felhasználók és csoportok 4me, hogy az Azure AD bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Egy 4me bérlő](https://www.4me.com/trial/)
* 4me rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="add-4me-from-the-gallery"></a>4me hozzáadása a katalógusból

Mielőtt konfigurálná a felhasználók automatikus átadása az Azure ad-vel 4me, szüksége az Azure AD alkalmazáskatalógusában 4me hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában 4me hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **4me**válassza **4me** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában 4me](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Felhasználók hozzárendelése 4me

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben 4me hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok 4me utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Felhasználók hozzárendelése 4me fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve 4me a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése 4me, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>4me történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok az Azure AD-ben a felhasználó és/vagy a csoport-hozzárendelések alapján 4me konfigurálásáról.

> [!TIP]
> Dönthet úgy is SAML-alapú egyszeri bejelentkezés 4me, az ahhoz szereplő utasítások a következő a [4me egyszeri bejelentkezéses oktatóanyag](4me-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása 4me az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **4me**.

    ![Az alkalmazások listáját a 4me hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Beolvasni a **bérlői URL-cím** és **titkos jogkivonat** 4me fiókja, kövesse az útmutató 6. lépésben leírtak szerint.

6. Jelentkezzen be a 4me felügyeleti konzolon. Navigáljon a **beállítások**.

    ![4me beállításai](media/4me-provisioning-tutorial/4me01.png)

    Írja be a **alkalmazások** a keresősávba.

    ![4me alkalmazások](media/4me-provisioning-tutorial/4me02.png)

    Nyissa meg a **SCIM** beolvasni a titkos kulcs Token és a SCIM-végpont legördülő listából.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. 5\. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD 4me csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, 4me fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a 4me**.

    ![4me Felhasználóleképezéseket](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Tekintse át a 4me a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok felel meg a felhasználói fiókok, a frissítési műveletek 4me szolgálnak. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![4me Felhasználóleképezéseket](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, 4me**.

    ![4me Felhasználóleképezéseket](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Tekintse át a csoportattribútumok 4me a az Azure AD-ből szinkronizált a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** megfelelnek a frissítési műveletek 4me a tulajdonságai használhatók. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Eszközcsoport-leképezéseket 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás 4me engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

16. A felhasználók és/vagy a kívánt csoportok definiálása 4me való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a 4me által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* 4me rendelkezik másik SCIM végponti URL-címek a tesztelési és éles környezetekben. A korábbi végződik **.qa** során az utóbbi ends **.com**
* generált 4me titkos jogkivonatokat a generáció a hónap lejárati dátummal rendelkeznek.
* nem támogatja a 4me **törlése** műveletek

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)