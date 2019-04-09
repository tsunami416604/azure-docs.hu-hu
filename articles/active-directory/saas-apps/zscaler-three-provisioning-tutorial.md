---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval Zscaler három konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Zscaler három felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: ed158ae825ec8aac24a57eb0f5a986b2124b66fb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273685"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása Zscaler három konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Zscaler három és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Zscaler három bemutatásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

* Az Azure AD-bérlő
* Zscaler három bérlő
* A Zscaler három rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler három SCIM API, a vállalati csomaggal rendelkező fiókok esetében Zscaler három fejlesztők számára elérhető.

## <a name="adding-zscaler-three-from-the-gallery"></a>A katalógusból Zscaler három hozzáadása

Az Azure AD-felhasználók automatikus a Zscaler három konfigurálása előtt hozzá kell Zscaler három, az Azure AD alkalmazáskatalógusában a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zscaler három, az Azure AD alkalmazáskatalógusában, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler három**válassza **Zscaler három** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában három Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-three"></a>Felhasználók hozzárendelése Zscaler három

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Zscaler három hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Zscaler három utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Felhasználók hozzárendelése Zscaler három fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Zscaler három a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Zscaler három, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-zscaler-three"></a>Zscaler három történő automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok a Zscaler három Azure AD-ben a felhasználó és/vagy a csoport-hozzárendelések alapján történő konfigurálásáról.

> [!TIP]
> Előfordulhat, hogy is engedélyezését választja SAML-alapú egyszeri bejelentkezés a Zscaler három, a biztonsági utasítások megadott a [Zscaler három egyszeri bejelentkezéses oktatóanyag](zscaler-three-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-three-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Zscaler három az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Zscaler három**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler három**.

    ![A Zscaler három hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** Zscaler három fiókja 6. lépésben leírtak szerint.

6. Beszerzése a **bérlői URL-cím** és **titkos jogkivonat**, navigáljon a **felügyelet > hitelesítési beállítások** a Zscaler három portál felhasználói felülete, és kattintson a  **SAML** alatt **hitelesítési típus**. 

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Kattintson a **SAML konfigurálása** megnyitásához **konfigurációs SAML** beállítások.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Válassza ki **Enable SCIM-Based kiépítés** lekéréséhez **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Másolás a **alap URL-cím** való **bérlői URL-cím** és **tulajdonosi jogkivonat** való **titkos jogkivonat** az Azure Portalon.

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat Zscaler három. Ha a kapcsolat hibája esetén, győződjön meg arról, Zscaler három fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Zscaler három**.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Tekintse át a a három Zscaler az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Zscaler három levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Zscaler három**.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Tekintse át a szinkronizált Azure AD-ből, a három Zscaler oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zscaler három csoportjai megfelelően használt. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler három engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Adja meg a felhasználók és/vagy a csoportok, adja meg Zscaler három való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zscaler három kiépítése](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a három Zscaler által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
