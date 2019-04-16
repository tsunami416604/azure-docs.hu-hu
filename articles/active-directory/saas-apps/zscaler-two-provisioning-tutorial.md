---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval Zscaler két konfigurálása |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja a konfigurálása Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok Zscaler két megszüntetése lesz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 837014fde6962f64d7da023a001a4c41089a0097
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578266"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása Zscaler két konfigurálása

Ebben az oktatóanyagban elsajátíthatja a konfigurálása Azure Active Directory (Azure AD) automatikus kiépítése és a felhasználók és csoportok a Zscaler két megszüntetése lesz.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD-felhasználót. Milyen a szolgáltatás a fontos részleteket nem, és hogyan működik, és a gyakori kérdésekre adott válaszok, lásd: [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Azure használati feltételeit előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure AD-bérlő.
* Zscaler két bérlő.
* Egy felhasználói fiókot a Zscaler két rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler két SCIM API, amely vállalati fiókok esetében érhető el.

## <a name="add-zscaler-two-from-the-gallery"></a>Zscaler két hozzáadása a katalógusból

Konfigurálása előtt Zscaler két számára az Azure AD-felhasználók automatikus, hozzá kell Zscaler két, az Azure AD alkalmazáskatalógusában a felügyelt SaaS-alkalmazások listájára.

Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Lépjen a **vállalati alkalmazások** majd **minden alkalmazás**:

![Vállalati alkalmazások](common/enterprise-applications.png)

Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

![Válassza ki az új alkalmazás](common/add-new-app.png)

A Keresés mezőbe írja be a **Zscaler két**. Válassza ki **Zscaler két** az eredményeket, és válassza ki a **Hozzáadás**.

![Eredmények listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Felhasználók hozzárendelése Zscaler két

Az Azure AD-felhasználók szükséges hozzáférést rendelni a kiválasztott alkalmazások használhassák azokat. Felhasználók automatikus átadása összefüggésében csak felhasználók vagy csoportok Azure AD-alkalmazásokhoz hozzárendelt szinkronizálódnak.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, akkor meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben két Zscaler hozzáférésre van szükségük. Miután eldöntötte, hogy, hozzárendelheti ezeket a felhasználókat és csoportokat Zscaler két utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Felhasználók hozzárendelése Zscaler két fontos tippek

* Azt javasoljuk, hogy először hozzárendel egy, a felhasználók automatikus konfiguráció teszteléséhez két Zscaler Azure AD-felhasználót. További felhasználók és csoportok később is hozzárendelhet.

* Rendel egy felhasználói Zscaler két, amikor kell a hozzárendelési párbeszédpanelen válassza ki a bármilyen érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="set-up-automatic-user-provisioning"></a>Felhasználók automatikus kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók és csoportok Zscaler két Azure AD-ben a felhasználó és csoport-hozzárendelések alapján való konfigurálásának lépéseit.

> [!TIP]
> Érdemes azt is, az SAML-alapú egyszeri bejelentkezés a Zscaler két engedélyezéséhez. Ha, kövesse az utasításokat a [Zscaler két egyszeri bejelentkezéses oktatóanyag](zscaler-two-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül felhasználók automatikus átadása, de a két funkció kiegészíti egymást.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) válassza **vállalati alkalmazások** > **minden alkalmazás** > **Zscaler két**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler két**:

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza ki a **kiépítési** lapon:

    ![Zscaler két kiépítése](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**:

    ![Az üzembe helyezési mód beállítása](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területén adja meg a **bérlői URL-cím** és **titkos jogkivonat** Zscaler két fiókja, a következő lépésben leírtak szerint.

6. Beolvasni a **bérlői URL-cím** és **titkos jogkivonat**, lépjen a **felügyeleti** > **hitelesítési beállítások** a Zscaler két portál és a select **SAML** alatt **hitelesítési típus**:

    ![Zscaler két hitelesítési beállítások](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Válassza ki **SAML konfigurálása** megnyitásához a **SAML konfigurálása** ablakban:

    ![SAML-ablak beállítása](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Válassza ki **Enable SCIM-Based kiépítés** , és másolja a **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Illessze be az Azure Portalon a **alap URL-cím** be a **bérlői URL-cím** mezőbe, és a **tulajdonosi jogkivonat** be a **titkos jogkivonat** mezőbe.

7. Az értékek megadása után a **bérlői URL-cím** és **titkos jogkivonat** mezőben, válassza **kapcsolat tesztelése** , hogy az Azure AD csatlakozhat Zscaler két. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Zscaler két fiók rendszergazdai engedélyekkel rendelkezik, és próbálkozzon újra.

    ![A kapcsolat tesztelése](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni. Válassza ki **e-mail-értesítés küldése, ha hiba történik**:

    ![Értesítő e-mail beállítása](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. Az a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a ZscalerTwo**:

    ![Az Azure AD-felhasználók szinkronizálása](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Tekintse át a a két Zscaler az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok a Zscaler két frissítési műveletek. Válassza ki **mentése** véglegesíti a módosításokat.

    ![Attribútumleképezések](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Az a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, ZscalerTwo**:

    ![Az Azure AD-csoportok szinkronizálása](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Tekintse át a szinkronizált Azure AD-ből, a két Zscaler oszlopainál a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek a Zscaler két a frissítési műveletek. Válassza ki **mentése** véglegesíti a módosításokat.

    ![Attribútumleképezések](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő témakör utasításait a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler két engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban:

    ![Kiépítési állapot](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Adja meg a felhasználók és/vagy csoportokat, amelyekhez szeretne üzembe helyezni a Zscaler két válassza ki a kívánt értékeket **hatókör** a a **beállítások** szakaszban:

    ![A hatókör értékei](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, válassza ki a **mentése**:

    ![Mentés kiválasztása](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó, és meghatározott csoportok **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás, mint az ezt követő szinkronizálások, mindaddig, amíg az Azure AD létesítési szolgáltatás fut-e 40 percenként kapcsolatban előforduló hosszabb időt vesz igénybe. Előrehaladás nyomon követheti a **szinkronizálás részleteivel** szakaszban. A kiépítési tevékenységek jelentésének, amely ismerteti az Azure AD létesítési szolgáltatás a két Zscaler által végrehajtott összes műveletet is kövesse a hivatkozásokat.

Olvassa el az Azure AD létesítési naplók kapcsolatos információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
