---
title: 'Oktatóanyag: Az Envoy konfigurálása a felhasználók automatikus átadása az Azure Active Directoryban |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Envoy felhasználói fiókokat.
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
ms.openlocfilehash: a68a8ec7c5df678918add4fcc519cc1dd918cb86
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168076"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása az Envoy konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania az Envoy és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésére és megszüntetni hozzárendeléseket felhasználók és/vagy az Envoy csoportok bemutatása.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Az Envoy bérlő](https://envoy.com/pricing/)
* Egy felhasználói fiókot az Envoy rendszergazdai jogosultságokkal rendelkezik.

## <a name="add-envoy-from-the-gallery"></a>Az Envoy hozzáadása a katalógusból

Konfigurálja az Envoy felhasználók automatikus átadása az Azure ad-vel, mielőtt szüksége az Azure AD alkalmazáskatalógusában Envoy hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Envoy hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Envoy**válassza **Envoy** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az envoy a találatok listájában](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Felhasználók hozzárendelése az Envoy

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Mielőtt a felhasználók automatikus kiépítés engedélyezése és konfigurálása, akkor meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben az Envoy hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Envoy utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Felhasználók hozzárendelése az Envoy fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve az Envoy a felhasználók automatikus konfiguráció teszteléséhez. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése az Envoy, jelöljön ki minden olyan érvényes alkalmazás-specifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Az Envoy történő automatikus felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja az Envoy.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés az Envoy engedélyezése, a biztonsági utasítások megadott a [Envoy egyszeri bejelentkezéses oktatóanyag](envoy-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása az Envoy az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Envoy**.

    ![Az Envoy hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban bemeneti `https://app.envoy.com/scim/v2` a **bérlői URL-cím**. Beolvasni a **titkos jogkivonat** Envoy fiókja, kövesse az útmutató 6. lépésben leírtak szerint.

6. Jelentkezzen be a [Envoy felügyeleti konzol](https://dashboard.envoy.com/login). Kattintson a **Integrációk**.

    ![Az envoy Integrációk](media/envoy-provisioning-tutorial/envoy01.png)

    Kattintson a **telepítése** számára a **a Microsoft Azure SCIM-integráció**.

    ![Az envoy telepítése](media/envoy-provisioning-tutorial/envoy02.png)

    Kattintson a **mentése** a **minden felhasználó szinkronizálása**. 

    ![Mentse az envoy](media/envoy-provisioning-tutorial/envoy03.png)

    Beolvasni a titkos kulcs-Token feltöltése.
    
    ![Az envoy OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. 5\. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD az Envoy csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, az Envoy fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók az Envoy**.
    
    ![Az envoy felhasználói attribútumok](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Tekintse át a az Envoy az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókokat az Envoy a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Az envoy felhasználói attribútumok](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Envoy**.

    ![Az envoy Felhasználóleképezéseket](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Tekintse át a szinkronizált Azure AD-ből, az Envoy oszlopainál a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével megfelelnek az Envoy a frissítési műveletek. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Az envoy Felhasználóleképezéseket](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás az Envoy engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

16. Határozza meg a felhasználók és/vagy a csoportok, adja meg az Envoy való kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás az Envoy által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

