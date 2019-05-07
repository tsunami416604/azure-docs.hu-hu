---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval Comeet felvételi szoftver konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Comeet felvételi szoftver felhasználói fiókokat.
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
ms.date: 04/26/2019
ms.author: zchia
ms.openlocfilehash: 0f1b5f424a71aeccd4b1e57129c0f5b22ff158af
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159390"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Comeet felvételi szoftver konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Comeet felvételi szoftver és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók, illetve Comeet felvételi szoftverfrissítési csoportok bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [A bérlő egy Comeet felvételi szoftver](https://www.comeet.co/)
* Egy felhasználói fiókot a felvételi szoftver Comeet rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Comeet felvételi szoftver hozzáadása a katalógusból

Mielőtt konfigurálná a Comeet felvételi szoftvert a felhasználók automatikus átadása az Azure ad-vel, szüksége az Azure AD alkalmazáskatalógusában Comeet felvételi szoftver hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Comeet felvételi szoftver hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Comeet felvételi szoftver**, jelölje be **Comeet felvételi szoftver** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Szoftver felvétele comeet a találatok listájában](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Felhasználók hozzárendelése Comeet felvételi szoftver

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Comeet felvételi szoftver hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Comeet felvételi szoftver utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Felhasználók hozzárendelése Comeet felvételi szoftver fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Comeet felvételi szoftvert a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Egy felhasználó Comeet felvételi szoftver hozzárendelésekor bármilyen érvényes alkalmazás-specifikus szerepkört (ha elérhető) jelöljön ki a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Felhasználók automatikus átadása Comeet felvételi szoftver konfigurálása 

Ez a szakasz útmutatók bemutatja, hogyan konfigurálásáról az Azure AD létesítési szolgáltatás szeretne létrehozni, frissíteni, és tiltsa le a felhasználók, illetve Comeet felvételi szoftverfrissítési csoportok az Azure ad-ben a felhasználói és/vagy a csoport-hozzárendelések alapján.

> [!TIP]
> Előfordulhat, hogy is engedélyezését választja SAML-alapú egyszeri bejelentkezés Comeet felvételi szoftverek, a biztonsági utasítások megadott a [Comeet felvételi szoftver egyszeri bejelentkezéses oktatóanyag](comeetrecruitingsoftware-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Comeet felvételi szoftverek az Azure ad-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza ki **Comeet felvételi szoftver**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Comeet felvételi szoftver**.

    ![Az alkalmazások listáját a Comeet felvételi szoftver hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a Comeet felvételi szoftver fiók 6. lépésben leírtak szerint.

6. A a [Comeet felvételi szoftver felügyeleti konzol](https://app.comeet.co/), keresse meg **Comeet > Beállítások > hitelesítési > Microsoft Azure**, és másolja a **a vállalatjogkivonattitkoskulcs**értéket a következőre a **titkos jogkivonat** mezőt az Azure ad-ben.

    ![Comeet Szoftverellátáshoz felvétele](./media/comeetrecruitingsoftware-provisioning-tutorial/secret-token-1.png)
    

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Comeet felvételi szoftverek kapcsolódhatnak. Ha a kapcsolat hibája esetén, győződjön meg arról, Comeet felvételi szoftver fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-token.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Comeet**.

    ![Comeet Szoftverellátáshoz felvétele](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mappings.png)

11. Tekintse át a Comeet felvételi szoftverek az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok felel meg a frissítési műveletek Comeet felvételi szoftveres felhasználói fiókok használhatók. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Comeet Szoftverellátáshoz felvétele](./media/comeetrecruitingsoftware-provisioning-tutorial/user-mapping-attributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás Comeet felvételi szoftverek engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. Válassza ki a kívánt értékeket a Comeet felvételi szoftverek üzembe helyezése a felhasználók és/vagy a csoportok, adja meg meghatározásához **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás Comeet felvételi szoftverfrissítési által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Comeet felvételi szoftver jelenleg nem támogatja a csoportok.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
