---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása a Cisco Spark |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Cisco Spark felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77dab6ad0480bc1565c219766d17211995dcfc20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278412"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Oktatóanyag: Cisco Spark konfigurálása a felhasználók automatikus átadása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Cisco Spark és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók Cisco-alapú Spark bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* Cisco Spark bérlő
* A Cisco Spark rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Cisco Spark webszolgáltatás](https://developer.webex.com/getting-started.html), a Cisco Spark teamshez elérhető.

## <a name="adding-cisco-spark-from-the-gallery"></a>Cisco Spark hozzáadása a katalógusból

Konfigurálja a Cisco Spark az Azure AD-felhasználók automatikus átadása, mielőtt szüksége az Azure AD alkalmazáskatalógusában Cisco Spark hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Cisco Spark hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Cisco Spark**válassza **Cisco Spark** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Cisco Spark, a találatok listájában](common/search-new-app.png)

## <a name="assigning-users-to-cisco-spark"></a>Felhasználók hozzárendelése a Cisco Spark

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak.

Engedélyezése a felhasználók automatikus átadása és konfigurálása, mielőtt, meg kell határoznia, hogy mely felhasználók Azure AD-ben a Cisco Spark hozzáférésre van szükségük. Ha úgy döntött, utasításokat követve hozzárendelheti ezeket a felhasználókat Cisco Spark:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Felhasználók hozzárendelése a Cisco Spark fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Cisco Spark a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók később is rendelhető.

* Felhasználó Cisco Spark hozzárendelésekor a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Cisco-alapú Spark automatikus felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók a Cisco Spark az Azure AD felhasználói hozzárendelések alapján történő konfigurálásáról.

### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Cisco Spark az Azure ad-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Cisco Spark**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Cisco Spark**.

    ![Az alkalmazások listáját a Cisco Spark hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím**, és **titkos jogkivonat** a Cisco Spark-fiók.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/secrettoken1.png)

    * Az a **bérlői URL-cím** mezőben töltse fel a Cisco Spark SCIM API URL-CÍMÉT a bérlő, amelyhez formájában történik `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, ahol `[Tenant ID]` alfanumerikus karakterből áll, 6. lépésben leírtak szerint.

    * Az a **titkos jogkivonat** mezőben a titkos kulcs Token feltöltéséhez, 6. lépésben leírtak szerint.

6. A **Bérlőazonosító** és **titkos jogkivonat** a Cisco Spark fiókkal bejelentkezve találhatók a [Cisco Spark fejlesztői webhely](https://developer.webex.com/) rendszergazdai fiókkal. Bejelentkezve egyszer -

   * Nyissa meg a [első lépések lap](https://developer.webex.com/getting-started.html)

   * Görgessen le a [hitelesítés szakaszban](https://developer.webex.com/getting-started.html#authentication)
  
    ![Cisco Spark hitelesítési jogkivonat](./media/cisco-spark-provisioning-tutorial/SecretToken.png)

   * A mezőbe a alfanumerikus karakterlánc a **titkos jogkivonat**. Ez a token másolása a vágólapra

   * Nyissa meg a [első saját saját részletei lap](https://developer.webex.com/endpoint-people-me-get.html)
       * Győződjön meg arról, hogy vizsgálati üzemmód be Kapcsolva
       * Írja be a "Tulajdonos" és egy szóközt szót, majd illessze be az engedélyezési mező a jogkivonat titkos kulcs ![Cisco Spark hitelesítési Token](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Kattintson a Futtatás

   * A jobb oldalon, a válasz szövegben a **Bérlőazonosító** "orgId" néven jelenik meg:

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Cisco Spark csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, a Cisco Spark fiókja rendelkezik rendszergazdai engedélyekkel, és próbálkozzon újra.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók Cisco Spark**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Tekintse át a Cisco Spark az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Cisco Spark a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás Cisco Spark engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. A felhasználók és/vagy a csoportok, adja meg definiálása Cisco Spark kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Cisco Spark kiépítése](./media/cisco-spark-provisioning-tutorial/Save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás Cisco Spark által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Cisco Spark jelenleg Cisco a korai mező tesztelése (elektronikus átutalás) fázisban van. További információkért lépjen kapcsolatba [Cisco a támogatási csapat](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
