---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Proxyclick |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Proxyclick felhasználói fiókokat.
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
ms.openlocfilehash: 2117f481d213c14f8feeb23eb8af3670db81cab4
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276638"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Proxyclick konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Proxyclick és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Proxyclick bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Egy Proxyclick bérlő](https://www.proxyclick.com/pricing)
* Egy felhasználói fiókot a Proxyclick rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a katalógusból

Az Azure AD-felhasználók automatikus Proxyclick konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában Proxyclick hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Proxyclick hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Proxyclick**válassza **Proxyclick** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Proxyclick](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Felhasználók hozzárendelése Proxyclick

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Proxyclick hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és csoportok Proxyclick utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Felhasználók hozzárendelése Proxyclick fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Proxyclick a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Proxyclick, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Proxyclick történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Proxyclick.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Proxyclick engedélyezése, a biztonsági utasítások megadott a [Proxyclick egyszeri bejelentkezéses oktatóanyag](proxyclick-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Proxyclick az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Proxyclick**.

    ![Az alkalmazások listáját a Proxyclick hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Beolvasni a **bérlői URL-cím** és **titkos jogkivonat** Proxyclick fiókja, kövesse az útmutató 6. lépésben leírtak szerint.

6. Jelentkezzen be a [Proxyclick felügyeleti konzol](https://app.proxyclick.com/login//?destination=%2Fdefault). Navigáljon a **beállítások** > **Integrációk** > **keresse meg a Marketplace-en**.

    ![Proxyclick Settings](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick Integrációk](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace-en](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Válassza ki **Azure ad-ben**. Kattintson a **telepítse most**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick telepítése](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Válassza ki **Felhasználókiépítés** kattintson **indítsa el az integrációs**. 

    ![Kiépítés Proxyclick felhasználó](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A megfelelő beállítások konfigurációs felhasználói Felületet kell most jelennek meg **beállítások** > **Integrációk**. Válassza ki **beállítások** alatt **Azure ad-ben (a felhasználók átadásának)** .

    ![Proxyclick létrehozása](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Annak a **bérlői URL-cím** és **titkos jogkivonat** itt.

    ![Proxyclick jogkivonat létrehozása](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. 5\. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Proxyclick csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Proxyclick fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Proxyclick**.

    ![Proxyclick Felhasználóleképezéseket](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Tekintse át a Proxyclick a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Proxyclick levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Proxyclick felhasználói attribútumok](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Az Azure AD létesítési szolgáltatás Proxyclick engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

15. A felhasználók és/vagy a kívánt csoportok definiálása Proxyclick való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

16. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Proxyclick által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Proxyclick igényel **e-mailek** és **felhasználónév** forrás ugyanazzal az értékkel rendelkeznie. Vagy attribútumok frissítéseit fog módosítsa a többi értéket.
* Proxyclick csoportok kiépítése nem támogatja.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

