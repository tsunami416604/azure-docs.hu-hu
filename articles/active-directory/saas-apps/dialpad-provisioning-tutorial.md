---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Dialpad |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Dialpad felhasználói fiókokat.
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
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611781"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Dialpad konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Dialpad és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Dialpad bemutatása.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

> Ez az összekötő jelenleg előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Egy Azure AD-bérlő.
* [A bérlő egy Dialpad](https://www.dialpad.com/pricing/).
* Egy felhasználói fiókot a Dialpad rendszergazdai jogosultságokkal rendelkezik.

## <a name="assign-users-to-dialpad"></a>Felhasználók hozzárendelése Dialpad
Az Azure Active Directory egy fogalom, más néven hozzárendeléseket használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Dialpad hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Dialpad utasításokat követve:
 
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Felhasználók hozzárendelése Dialpad fontos tippek

 * Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Dialpad a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Dialpad, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva, a létesítési.

## <a name="setup-dialpad-for-provisioning"></a>A telepítő Dialpad kiépítéshez

Az Azure AD-felhasználók automatikus Dialpad konfigurálja, mielőtt kell Dialpad lévő egyes kiépítési információk lekéréséhez.

1. Jelentkezzen be a [Dialpad felügyeleti konzol](https://dialpadbeta.com/login) válassza **adminisztrátori beállítások**. Ügyeljen arra, hogy **vállalatom** kiválasztott elemet a legördülő listából. Navigáljon a **hitelesítési > API-kulcsok**.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Hozzon létre egy új kulcsot kattintva **kulcs hozzáadása** és a titkos jogkivonat tulajdonságainak konfigurálása.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kattintson a **érték megjelenítéséhez kattintson** legutóbb létrehozott API-kulcs gombra, és másolja a megjelenített értéket. Ezt az értéket fog megadni az **titkos jogkivonat** mezőt a kiépítés lapon Dialpad alkalmazását az Azure Portalon. 

    ![Dialpad jogkivonat létrehozása](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dialpad hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus Dialpad konfigurálja, szüksége az Azure AD alkalmazáskatalógusában Dialpad hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Dialpad hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Dialpad**válassza **Dialpad** az eredmények panelen.
    ![Az eredmények listájában Dialpad](common/search-new-app.png)

5. Keresse meg a **URL-cím** kiemelve alább a egy külön böngészőablakot. 

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. A jobb felső sarokban, válassza ki **bejelentkezés > használat Dialpad online**.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Mivel Dialpad OpenIDConnect alkalmazás, válassza a bejelentkezés a Microsoft munkahelyi fiókjával Dialpad az.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. A sikeres hitelesítés után fogadja el a hozzájárulást kérő lap hozzájárulási kérése. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Dialpad fiókjához.

    ![Dialpad Add SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Felhasználók automatikus átadása az Dialpad konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Dialpad.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Dialpad az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Dialpad**.

    ![Az alkalmazások listáját a Dialpad hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://dialpad.com/scim` a **bérlői URL-cím**. Az érték, amely kéri le, és a korábban mentett Dialpad a bemeneti **titkos jogkivonat**. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Dialpad csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Dialpad fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Dialpad**.

    ![Dialpad Felhasználóleképezéseket](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Tekintse át a Dialpad a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Dialpad levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Dialpad felhasználói attribútumok](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD létesítési szolgáltatás Dialpad engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

12. A felhasználók és/vagy a kívánt csoportok definiálása Dialpad való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

13. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Dialpad által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Összekötő-korlátozások
* Dialpad nem támogatja a csoport átnevezése még ma. Ez azt jelenti, hogy módosítania kellene a **displayName** csoportnak az Azure AD nem lesz frissítve és Dialpad megjelennek.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
