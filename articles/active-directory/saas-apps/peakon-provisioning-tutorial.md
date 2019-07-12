---
title: 'Oktatóanyag: Peakon felhasználók automatikus átadása az Azure Active Directory konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Peakon felhasználói fiókokat.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673144"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Peakon konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Peakon és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Peakon bemutatása.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek

* Egy Azure AD-bérlő.
* [A bérlő egy Peakon](https://peakon.com/us/pricing/).
* Egy felhasználói fiókot a Peakon rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-peakon"></a>Felhasználók hozzárendelése Peakon

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Peakon hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Peakon utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Felhasználók hozzárendelése Peakon fontos tippek 

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Peakon a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Peakon, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="set-up-peakon-for-provisioning"></a>Üzembe helyezés Peakon beállítása

1.  Jelentkezzen be a [Peakon felügyeleti konzol](https://app.Peakon.com/login). Kattintson a **konfigurációs**. 

    ![Peakon felügyeleti konzol](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Válassza ki **Integrációk**.
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Engedélyezése **kiépítési alkalmazott**.

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Másolja le az értékeket a **SCIM 2.0-s URL-cím** és **OAuth tulajdonosi jogkivonat**. Ezeket az értékeket fogja megadni a **bérlői URL-cím** és **titkos jogkivonat** mezőt a kiépítés lapon Peakon alkalmazását az Azure Portalon.

    ![Peakon jogkivonat létrehozása](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Peakon hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus Peakon konfigurálja, szüksége az Azure AD alkalmazáskatalógusában Peakon hozzáadása a felügyelt SaaS-alkalmazások listája.

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Peakon**válassza **Peakon** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Peakon](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Peakon történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Peakon.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Peakon engedélyezése, a biztonsági utasítások megadott a [Peakon egyszeri bejelentkezéses oktatóanyag](peakon-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Peakon az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Peakon**.

    ![Az alkalmazások listáját a Peakon hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **SCIM 2.0-s URL-cím** és **OAuth tulajdonosi jogkivonat** korábban lekért értékek **bérlői URL-cím** és **titkos jogkivonat** jelölik. Kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Peakon csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Peakon fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Bérlői URL-cím + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Peakon**.

    ![Peakon Felhasználóleképezéseket](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Tekintse át a Peakon a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Peakon levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Peakon felhasználói attribútumok](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Peakon által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Ki kell terjeszteni a Peakon egyéni SCIM felhasználói kiterjesztése, a rendelkezik Peakon az egyéni felhasználói attribútumok `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)