---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Dropbox Business |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Dropbox Business felhasználói fiókokat.
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
ms.date: 05/20/2019
ms.author: zchia
ms.openlocfilehash: c95346ff9026b7fc8c9141234fb3cde9c0809d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053499"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Dropbox Business konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania, a Dropbox és az Azure Active Directory (Azure AD) konfigurálása az Azure ad-ben való automatikus kiépítésének és megszüntetésének felhasználók és csoportok, a Dropbox Business bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [A Dropbox Business-bérlő](https://www.dropbox.com/business/pricing)
* Egy felhasználói fiókot a Dropboxban vállalati rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dropbox Business hozzáadása a katalógusból

Konfigurálja az Azure AD-felhasználók automatikus Dropbox Business, mielőtt szüksége az Azure AD alkalmazáskatalógusában Dropbox Business hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Dropbox Business hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Dropbox Business**válassza **Dropbox Business** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Dropbox Business az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése Dropbox Business

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure AD hozzá kell férniük a Dropboxba vállalati. Ha úgy döntött, rendelhet a felhasználók és csoportok Dropbox Business az alábbi utasításokat:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése Dropbox Business fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó Dropbox Business teszteléséhez a felhasználók automatikus konfigurációs van rendelve. További felhasználók és csoportok később is rendelhető.

* A felhasználó Dropbox Business való hozzárendelésekor a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Dropbox Business történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja, a Dropbox Business.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés Dropbox Business engedélyezése, a biztonsági utasítások megadott a [Dropbox Business egyszeri bejelentkezés az oktatóanyaghoz](dropboxforbusiness-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Dropbox Business az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Dropbox Business**.

    ![A Dropbox, az alkalmazások listáját az üzleti hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés**. A Dropbox bejelentkezési párbeszédpanel egy új böngészőablakban nyílik meg.

    ![Kiépítés ](common/provisioning-oauth.png)

6. Az a **jelentkezzen be a Dropbox összekapcsolása az Azure AD vállalati** párbeszédpanel, jelentkezzen be a Dropboxba üzleti bérlő, és igazolja személyazonosságát.

    ![Üzleti jelentkezzen be a Dropboxba](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 5 és 6 lépések elvégzése után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Dropbox Business csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, a Dropbox Business fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-oauth.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Dropboxba**.

    ![Dropbox Felhasználóleképezéseket](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Tekintse át a dropboxba az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókokat, a Dropbox a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Dropbox felhasználói attribútumok](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok a Dropboxba**.

    ![Dropbox-csoport-hozzárendelések](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Tekintse át az Azure AD-ból a dropboxba szinkronizált oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek a dropbox a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Dropbox csoportattribútumok](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás, a Dropbox engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

16. A felhasználók és/vagy a csoportok, adja meg a Dropboxba kiépítéséhez definiálása válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely által az Azure AD létesítési szolgáltatás a Dropboxban végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások
 
* Dropbox nem támogatja a meghívott felhasználó felfüggesztése. Egy meghívott felhasználó fel van függesztve, ha a felhasználó törlődik.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

