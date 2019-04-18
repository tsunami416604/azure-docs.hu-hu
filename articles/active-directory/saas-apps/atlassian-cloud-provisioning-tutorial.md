---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval Atlassian felhő konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Atlassian felhőalapú felhasználói fiókok.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: f4e76121f7815702270d6601413ff7a4c2c25839
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270302"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása Atlassian felhő konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania az Atlassian felhőben és az Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok Atlassian felhőbe történő bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Az Atlassian felhőalapú bérlő](https://www.atlassian.com/licensing/cloud)
* Egy felhasználói fiók Atlassian felhőbeli rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a **Atlassian Cloud SCIM API**, amely Atlassian felhőalapú csapatok rendelkezésére áll.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Atlassian felhő hozzáadása a katalógusból

Atlassian felhő konfigurálása az Azure AD-felhasználók automatikus, mielőtt szüksége az Azure AD alkalmazáskatalógusában Atlassian felhő hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Atlassian felhőalapú hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Atlassian felhőalapú**, jelölje be **Atlassian felhőalapú** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Atlassian felhő](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Felhasználók hozzárendelése Atlassian felhő

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben Atlassian felhőbe való hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti ezen felhasználók és csoportok Atlassian felhőalapú utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Felhasználók hozzárendelése Atlassian felhőalapú fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Atlassian felhőalapú a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Atlassian felhőbe való hozzárendelésekor a felhasználó, a hozzárendelés párbeszédpanelen válassza ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Felhasználók automatikus átadása Atlassian felhőbe konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure ad-ben a felhasználó és/vagy a csoport-hozzárendelések alapján csoportok Atlassian felhőben.

> [!TIP]
> Előfordulhat, hogy is engedélyezését választja SAML-alapú egyszeri bejelentkezés Atlassian felhőhöz, a biztonsági utasítások megadott a [Atlassian felhőalapú egyszeri bejelentkezést az oktatóanyag](atlassian-cloud-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Atlassian felhőalapú Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza ki **Atlassian felhőalapú**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Atlassian felhőalapú**.

    ![Az alkalmazások listáját az Atlassian felhőalapú hivatkozásra](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím** és **titkos jogkivonat** az Atlassian Felhőbeli fiók. Példák a következő értékek a következők:

   * Az a **bérlői URL-cím** mezőben adja meg a megadott tenant végpont az Atlassian kap, 6. lépésben leírtak szerint. Példa: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Az a **titkos jogkivonat** mezőben töltse fel a titkos jogkivonat, 6. lépésben leírtak szerint.

6. Navigáljon a [szervezet Manager Atlassian](https://admin.atlassian.com) **> felhasználók átadásának** , majd kattintson a **Token létrehozása az**. Másolás a **Directory alap URL-cím** és **tulajdonosi jogkivonat** , a **bérlői URL-cím** és **jogkivonat titkos kulcs** mezők jelölik.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD Atlassian felhőalapú csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, Atlassian felhőalapú fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók Atlassian felhőbe**.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Tekintse át az Atlassian felhőbe az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok felel meg a frissítési műveletek Atlassian felhőalapú felhasználói fiókok használhatók. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok Atlassian felhőbe**.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Tekintse át a csoportattribútumok Atlassian felhőbe az Azure AD-ből szinkronizált a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek az Atlassian felhőben a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás Atlassian felhő engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Adja meg a felhasználók és/vagy a csoportok, adja meg Atlassian felhőben történő kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Atlassian felhőalapú Jogosultságkiosztási](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás Atlassian felhő által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Atlassian felhőalapú lehetővé teszi, hogy csak a felhasználók átadásának [ellenőrzött tartományok](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian felhő nem támogatja a csoport átnevezése még ma. Ez azt jelenti, hogy módosítania kellene az Azure AD-csoport megjelenített nem módosítása és Atlassian felhőalapú megjelennek.
* Értékét a **mail** felhasználói attribútum az Azure ad-ben csak van feltöltve, ha a felhasználó rendelkezik-e a Microsoft Exchange-postaládájába. Ha a felhasználó nem rendelkezik ilyennel, javasoljuk, hogy egy másik kívánt attribútumot leképezése a **e-mailek** Atlassian felhőben attribútum.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
