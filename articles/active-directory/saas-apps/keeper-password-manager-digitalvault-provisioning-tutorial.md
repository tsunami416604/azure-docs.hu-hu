---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval üzemeltetőjének jelszókezelő & digitális tároló konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének üzemeltetőjének jelszókezelő & digitális Vault felhasználói fiókokat.
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
ms.date: 04/25/2019
ms.author: zchia
ms.openlocfilehash: b7b096bd1ecf0a4df37ed4de0cf618216dccc2bc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159465"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása üzemeltetőjének jelszókezelő & digitális tároló konfigurálása

Ez az oktatóanyag célja az, hogy a lépéseket kell végrehajtania a üzemeltetőjének jelszókezelő & digitális Vault és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok üzemeltetőjének jelszó bemutatása & Manager digitális tároló.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [A jelszókezelő üzemeltetőjének & digitális tároló bérlői](https://keepersecurity.com/pricing.html?t=e)
* Egy felhasználói fiókot a üzemeltetőjének jelszókezelő & digitális tároló rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Jelszókezelő üzemeltetőjének & digitális tároló hozzáadása a katalógusból

Mielőtt konfigurálná a üzemeltetőjének jelszókezelő & digitális tárolót a felhasználók automatikus átadása az Azure ad-vel, hozzá kell üzemeltetőjének jelszókezelő & digitális Vault az Azure AD alkalmazáskatalógusában a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD alkalmazáskatalógusában üzemeltetőjének jelszókezelő & digitális tár hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **üzemeltetőjének jelszókezelő & digitális tároló**, jelölje be **üzemeltetőjének jelszókezelő & digitális tároló** az eredmények panelen, majd kattintson a a **Hozzáadás**gombra kattintva vegye fel az alkalmazást.

    ![Jelszókezelő üzemeltetőjének & digitális tároló a találatok listájában](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Felhasználók hozzárendelése üzemeltetőjének jelszókezelő és digitális tároló

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben üzemeltetőjének jelszókezelő & digitális Vault hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok üzemeltetőjének jelszókezelő & digitális tároló utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Felhasználók hozzárendelése üzemeltetőjének jelszókezelő és digitális tároló fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve üzemeltetőjének jelszókezelő & digitális tárolót a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése üzemeltetőjének jelszókezelő és digitális tároló, a hozzárendelés párbeszédpanelen válassza ki bármilyen érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Felhasználók automatikus átadása üzemeltetőjének jelszókezelő & digitális tároló konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok üzemeltetőjének jelszókezelő konfigurálásának lépéseit, és az Azure ad-ben a felhasználó és/vagy a csoport-hozzárendelések alapján digitális tároló.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés üzemeltetőjének jelszókezelő & digitális Vault engedélyezése, a biztonsági utasítások megadott a [üzemeltetőjének jelszókezelő & digitális Vault egyszeri bejelentkezést az oktatóanyag](keeperpasswordmanager-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása üzemeltetőjének jelszókezelő & digitális Vault az Azure ad-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **üzemeltetőjének jelszókezelő & digitális Tároló**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **üzemeltetőjének jelszókezelő & digitális tároló**.

    ![A jelszókezelő üzemeltetőjének & digitális Vault hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a üzemeltetőjének jelszókezelő & digitális tároló fiók 6. lépésben leírtak szerint.

6. Jelentkezzen be a [üzemeltetőjének felügyeleti konzol](https://keepersecurity.com/console/#login). Kattintson a **rendszergazdai** , és válasszon ki egy meglévő csomópontot, vagy hozzon létre egy újat. Keresse meg a **kiépítési** lapot, és válasszon **metódus hozzáadása**.

    ![Üzemeltetőjének felügyeleti konzol](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-admin-console.png)

    Válassza ki **SCIM (a tartományok közötti Identity Management rendszer**.

    ![Üzemeltetőjének SCIM hozzáadása](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kattintson a **kiépítés jogkivonat létrehozása**.

    ![Üzemeltetőjének végpont létrehozása](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Másolja le az értékeket a **URL-cím** és **Token** , és illessze be őket **bérlői URL-cím** és **titkos jogkivonat** az Azure ad-ben. Kattintson a **mentése** a kiépítési üzemeltetőjének a telepítés befejezéséhez.

    ![Üzemeltetőjének jogkivonat létrehozása](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-token.png)

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat üzemeltetőjének jelszókezelő & digitális tároló. Ha a kapcsolat hibája esetén, győződjön meg arról, üzemeltetőjének jelszókezelő & digitális tároló fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók üzemeltetőjének jelszókezelő & digitális tároló**.

    ![Üzemeltetőjének Felhasználóleképezéseket](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Tekintse át a üzemeltetőjének jelszókezelő & digitális Vault az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek üzemeltetőjének jelszókezelő & digitális tárolóban levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Üzemeltetőjének felhasználói attribútumok](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok üzemeltetőjének jelszókezelő & digitális tároló**.

    ![Üzemeltetőjének csoport-hozzárendelések](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Tekintse át a csoportattribútumok üzemeltetőjének jelszókezelő & digitális Vault az Azure AD-ből szinkronizált a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek a üzemeltetőjének jelszókezelő & digitális tároló frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Üzemeltetőjének csoportattribútumok](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás üzemeltetőjének jelszókezelő & digitális tároló engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

16. Válassza ki a kívánt értékeket a üzemeltetőjének jelszókezelő & digitális tároló üzembe helyezése a felhasználók és/vagy a kívánt csoportok meghatározásához **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakaszban előrehaladásának figyeléséhez, és kövesse a hivatkozásokat tevékenységgel kapcsolatos jelentés, amely ismerteti az Azure AD létesítési szolgáltatás a üzemeltetőjének jelszókezelő által végrehajtott összes műveletet kiépítés & Digitális tároló.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Jelszókezelő üzemeltetőjének & digitális tárolót igényel **e-mailek** és **felhasználónév** szeretné, hogy az azonos érték, vagy attribútumok frissítéseit az más értéket fog módosítása.
* Jelszókezelő üzemeltetőjének & digitális tároló nem támogatja a felhasználó törlése, csak letiltja. A letiltott felhasználók fog megjelenni a üzemeltetőjének felügyelet konzol felhasználói felületén zárolva van.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
