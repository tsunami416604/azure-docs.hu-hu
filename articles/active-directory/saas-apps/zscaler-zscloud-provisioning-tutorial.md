---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Zscaler ZSCloud |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja a konfigurálása Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok Zscaler ZSCloud megszüntetése lesz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 13231fe75ff173999f3a7fa4728f583c6f04c54d
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579172"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Oktatóanyag: Configure Zscaler ZSCloud for automatic user provisioning

Ebben az oktatóanyagban elsajátíthatja a konfigurálása Azure Active Directory (Azure AD) automatikus kiépítése és a felhasználók és csoportok a Zscaler ZSCloud megszüntetése lesz.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD-felhasználót. Milyen a szolgáltatás a fontos részleteket nem, és hogyan működik, és a gyakori kérdésekre adott válaszok, lásd: [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Azure használati feltételeit előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure AD-bérlő.
* A Zscaler ZSCloud tenant.
* Egy felhasználói fiókot a Zscaler ZSCloud rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler ZSCloud SCIM API elérhető, vállalati fiókok esetében.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud hozzáadása a katalógusból

Zscaler ZSCloud beállítása az Azure AD-felhasználók automatikus, birtokában az Azure AD alkalmazáskatalógusában Zscaler ZSCloud hozzáadása a felügyelt SaaS-alkalmazások listája.

Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Lépjen a **vállalati alkalmazások** majd **minden alkalmazás**:

![Vállalati alkalmazások](common/enterprise-applications.png)

Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

![Válassza ki az új alkalmazás](common/add-new-app.png)

A Keresés mezőbe írja be a **Zscaler ZSCloud**. Válassza ki **Zscaler ZSCloud** az eredményeket, és válassza ki a **Hozzáadás**.

![Eredmények listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Assign users to Zscaler ZSCloud

Az Azure AD-felhasználók szükséges hozzáférést rendelni a kiválasztott alkalmazások használhassák azokat. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok Azure AD-alkalmazásokhoz hozzárendelt szinkronizálódnak.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, akkor meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Zscaler ZSCloud hozzáférésre van szükségük. Miután eldöntötte, hogy, hozzárendelheti ezeket a felhasználókat és csoportokat Zscaler ZSCloud utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Important tips for assigning users to Zscaler ZSCloud

* Azt javasoljuk, hogy először hozzárendel egy Zscaler ZSCloud a felhasználók automatikus konfiguráció tesztelése az Azure AD-felhasználót. További felhasználók és csoportok később is hozzárendelhet.

* Felhasználó hozzárendelése Zscaler ZSCloud, amikor kell a hozzárendelési párbeszédpanelen válassza ki a bármilyen érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="set-up-automatic-user-provisioning"></a>Felhasználók automatikus kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásának lépéseit, és az Azure ad-ben a felhasználó és csoport-hozzárendelések alapján Zscaler ZSCloud csoportokat.

> [!TIP]
> Érdemes azt is, az SAML-alapú egyszeri bejelentkezés a Zscaler ZSCloud engedélyezéséhez. Ha, kövesse az utasításokat a [Zscaler ZSCloud egyszeri bejelentkezéses oktatóanyag](zscaler-zsCloud-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül felhasználók automatikus átadása, de a két funkció kiegészíti egymást.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) válassza **vállalati alkalmazások** > **minden alkalmazás** > **Zscaler ZSCloud**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler ZSCloud**:

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza ki a **kiépítési** lapon:

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**:

    ![Az üzembe helyezési mód beállítása](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területén adja meg a **bérlői URL-cím** és **titkos jogkivonat** Zscaler ZSCloud fiókja, a következő lépésben leírtak szerint.

6. Beolvasni a **bérlői URL-cím** és **titkos jogkivonat**, lépjen a **felügyeleti** > **hitelesítési beállítások** a Zscaler a ZSCloud Portalon, és válassza **SAML** alatt **hitelesítési típus**:

    ![Zscaler ZSCloud Authentication Settings](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Válassza ki **SAML konfigurálása** megnyitásához a **SAML konfigurálása** ablakban:

    ![SAML-ablak beállítása](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Válassza ki **Enable SCIM-Based kiépítés** , és másolja a **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Illessze be az Azure Portalon a **alap URL-cím** be a **bérlői URL-cím** mezőbe, és a **tulajdonosi jogkivonat** be a **titkos jogkivonat** mezőbe.

7. Az értékek megadása után a **bérlői URL-cím** és **jogkivonat titkos kulcs** mezőben, válassza **kapcsolat tesztelése** , hogy az Azure AD Zscaler ZSCloud csatlakozhat. Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Zscaler ZSCloud fiókja rendelkezik rendszergazdai engedélyekkel, és próbálkozzon újra.

    ![A kapcsolat tesztelése](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni. Válassza ki **e-mail-értesítés küldése, ha hiba történik**:

    ![Értesítő e-mail beállítása](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Kattintson a **Mentés** gombra.

10. Az a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a ZscalerZSCloud**:

    ![Az Azure AD-felhasználók szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Tekintse át a Zscaler ZSCloud a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Zscaler ZSCloud levő felhasználói fiókokat. Válassza ki **mentése** véglegesíti a módosításokat.

    ![Attribútumleképezések](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Az a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, ZscalerZSCloud**:

    ![Az Azure AD-csoportok szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Tekintse át a szinkronizált Azure AD-ből, a Zscaler ZSCloud oszlopainál a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zscaler ZSCloud csoportjai megfelelően használt. Válassza ki **mentése** véglegesíti a módosításokat.

    ![Attribútumleképezések](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő témakör utasításait a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler ZSCloud engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban:

    ![Kiépítési állapot](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. A felhasználók és/vagy csoportokat, amelyekhez szeretne üzembe helyezni a Zscaler ZSCloud definiálása válassza ki a kívánt értékeket **hatókör** a a **beállítások** szakaszban:

    ![A hatókör értékei](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, válassza ki a **mentése**:

    ![Mentés kiválasztása](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó, és meghatározott csoportok **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás, mint az ezt követő szinkronizálások, mindaddig, amíg az Azure AD létesítési szolgáltatás fut-e 40 percenként kapcsolatban előforduló hosszabb időt vesz igénybe. Előrehaladás nyomon követheti a **szinkronizálás részleteivel** szakaszban. A kiépítési tevékenységek jelentésének, amely ismerteti az Azure AD létesítési szolgáltatás a Zscaler ZSCloud által végrehajtott összes műveletet is kövesse a hivatkozásokat.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
