---
title: 'Oktatóanyag: Nagyítás konfigurálása az Azure Active Directory felhasználók automatikus átadása |} A Microsoft Docs'
description: 'Útmutató: Azure Active Directory beállítása az Automatikus kiépítés és megszüntetni hozzárendeléseket felhasználói fiókokat a nagyításhoz.'
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
ms.openlocfilehash: ca79b3901e3933e25c5be92673e0c5bcc464782d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167855"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Oktatóanyag: Nagyítás konfigurálása a felhasználók automatikus átadása

A jelen oktatóanyag célja a lépéseket kell végrehajtania a nagyítás és az Azure Active Directory (Azure AD) konfigurálása az Azure AD bemutatása automatikusan üzembe helyezése és megszüntetni hozzárendeléseket felhasználók és/vagy a nagyításhoz csoportok.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [kiegészítő használati feltételei a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

* Az Azure AD-bérlő
* [Nagyítás bérlő](https://zoom.us/pricing)
* Nagyítás rendszergazdai engedélyekkel rendelkező felhasználói fiókkal

## <a name="add-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból

Nagyítás beállítása a felhasználók automatikus átadása az Azure ad-vel, mielőtt szüksége az Azure AD alkalmazáskatalógusában nagyítás hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában nagyítás hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)** , a bal oldali navigációs panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a panel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **nagyítás**válassza **nagyítás** az eredmények panelen, majd kattintson a a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában nagyítás](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Felhasználók hozzárendelése nagyítás

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve az Azure AD-alkalmazáshoz hozzárendelt csoportok vannak szinkronizálva.

Konfigurálása és engedélyezése a felhasználók automatikus átadása előtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Nagyítás hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok nagyítás utasításokat követve:

* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Felhasználók hozzárendelése nagyítás fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a nagyításhoz. a felhasználók automatikus konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* A felhasználó a nagyításhoz hozzárendelésekor jelöljön ki minden olyan érvényes alkalmazás-specifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurálja a felhasználók automatikus átadása a nagyításhoz. 

Ez a szakasz végigvezeti a lépéseket az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásához, vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a nagyítást.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés a Nagyítás engedélyezése, a biztonsági utasítások megadott a [nagyítás egyszeri bejelentkezéses oktatóanyag](zoom-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurálja a felhasználók automatikus átadása Nagyítás az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **nagyítás**.

    ![A Nagyítás hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Kiépítés lapon](common/provisioning.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Kiépítés lapon](common/provisioning-automatic.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg `https://api.zoom.us/scim` a **bérlői URL-cím**. Beolvasni a **titkos jogkivonat** nagyítás fiókja, kövesse az útmutató 6. lépésben leírtak szerint.

6. Jelentkezzen be a [felügyeleti konzol nagyítás](https://zoom.us/signin). Navigáljon a **Speciális > fejlesztőknek nagyítás** a bal oldali navigációs ablaktáblán.

    ![Nagyítás Integrációk](media/zoom-provisioning-tutorial/zoom01.png)

    Navigáljon a **kezelés** az oldal jobb felső sarkában. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom02.png)

    Lépjen a létrehozott Azure AD-alkalmazás. 
    
    ![Nagyítás alkalmazás](media/zoom-provisioning-tutorial/zoom03.png)

    Válassza ki **alkalmazás hitelesítő adatok** a bal oldali navigációs ablaktáblán.

    ![Nagyítás alkalmazás](media/zoom-provisioning-tutorial/zoom04.png)

    Alább látható a JWT jogkivonat értékét a vizualizációhoz, és adjon meg, ez a **titkos jogkivonat** mezőt az Azure ad-ben. Ha egy új nem avuló jogkivonatot, újra kell konfigurálnia a lejárati ideje lesz automatikusan új token létrehozása. 

    ![Nagyítás telepítése](media/zoom-provisioning-tutorial/zoom05.png)

7. 5\. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD nagyítás csatlakozhat. Ha a kapcsolat hibája esetén, győződjön meg arról, nagyítás fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a nagyításhoz**.

    ![Nagyítás Felhasználóleképezéseket](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Tekintse át a a nagyításhoz. az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek nagyítás levő felhasználói fiókokat. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.
    
     ![Nagyítás Felhasználóleképezéseket](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Az Azure AD létesítési szolgáltatás a Nagyítás engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.
    
    ![Kiépítési állapot bekapcsolt](common/provisioning-toggle-on.png)

14. A felhasználók és/vagy a kívánt csoportok definiálása üzembe helyezni a nagyításhoz. Válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Hatókör-kiépítés](common/provisioning-scope.png)

15. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Üzembe helyezési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely az Azure AD létesítési szolgáltatás a Nagyítás által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások

* Nagyítás nem támogatja a csoportok kiépítése.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
