---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Zscaler ZSCloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Zscaler ZSCloud felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3f7fcd59bafe5619a1ef411bf81a6b8c3431f22c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087414"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Oktatóanyag: Configure Zscaler ZSCloud for automatic user provisioning

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Zscaler ZSCloud és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Zscaler ZSCloud bemutatásához.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../active-directory-saas-app-provisioning.md).
> 
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbiakkal:

*   Az Azure AD-bérlő
*   A Zscaler ZSCloud tenant
*   A Zscaler ZSCloud rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integráció támaszkodik a Zscaler ZSCloud SCIM API elérhető a Zscaler ZSCloud fejlesztők számára a vállalati csomaggal rendelkező fiókok esetében.

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud hozzáadása a katalógusból
Az Azure AD-felhasználók automatikus Zscaler ZSCloud konfigurálja, mielőtt szüksége az Azure AD alkalmazáskatalógusában Zscaler ZSCloud hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Zscaler ZSCloud hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]

3. Zscaler ZSCloud hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Zscaler ZSCloud**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/appsearch.png)

5. Az eredmények panelen válassza ki a **Zscaler ZSCloud**, majd kattintson a **hozzáadása** gombra kattintva adhat hozzá Zscaler ZSCloud a SaaS-alkalmazások listájára.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/appsearchresults.png)

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/appcreation.png)

## <a name="assigning-users-to-zscaler-zscloud"></a>Assigning users to Zscaler ZSCloud

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Zscaler ZSCloud hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Zscaler ZSCloud utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Important tips for assigning users to Zscaler ZSCloud

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Zscaler ZSCloud a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Zscaler ZSCloud, jelöljön ki minden olyan érvényes alkalmazás-specifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-zscaler-zscloud"></a>Configuring automatic user provisioning to Zscaler ZSCloud

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure ad-ben a felhasználói és/vagy a csoport-hozzárendelések alapján Zscaler ZSCloud csoportokat.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés a Zscaler ZSCloud engedélyezése, a biztonsági utasítások megadott a [Zscaler ZSCloud egyszeri bejelentkezéses oktatóanyag](zscaler-zsCloud-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-zscloud-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Zscaler ZSCloud az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Select Zscaler ZSCloud from your list of SaaS applications.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/appinstancesearch.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** a Zscaler ZSCloud fiók 6. lépésben leírtak szerint.

6. Beszerzése a **bérlői URL-cím** és **titkos jogkivonat**, navigáljon a **felügyelet > hitelesítési beállítások** a Zscaler ZSCloud portál felhasználói felülete, és kattintson a  **SAML** alatt **hitelesítési típus**. 

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Kattintson a **SAML konfigurálása** megnyitásához **konfigurációs SAML** beállítások. 

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)
    
    Válassza ki **Enable SCIM-Based kiépítés** lekéréséhez **alap URL-cím** és **tulajdonosi jogkivonat**, majd mentse a beállításokat. Másolás a **alap URL-cím** való **bérlői URL-cím** és **tulajdonosi jogkivonat** való **titkos jogkivonat** az Azure Portalon.

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Zscaler ZSCloud csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Zscaler ZSCloud fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)
    
8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet kell **e-mail-értesítés küldése, ha hiba történik**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Zscaler ZSCloud**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Tekintse át a Zscaler ZSCloud a az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Zscaler ZSCloud levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Zscaler ZSCloud**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Tekintse át a szinkronizált Azure AD-ből, a Zscaler ZSCloud oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zscaler ZSCloud csoportjai megfelelően használt. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Az Azure AD létesítési szolgáltatás a Zscaler ZSCloud engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. A felhasználók és/vagy a kívánt csoportok definiálása Zscaler ZSCloud való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakasz.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Zscaler ZSCloud által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
