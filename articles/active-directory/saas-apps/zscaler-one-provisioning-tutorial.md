---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval Zscaler egy konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és felhasználói fiókok Zscaler egy megszüntetése.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 3d6210fa215792fc7ff049eb597df5905926c193
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670888"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Oktatóanyag: A felhasználók automatikus átadása egy Zscaler konfigurálása

Ez az oktatóanyag bemutatja a Zscaler egy és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítése és a felhasználók és csoportok Zscaler egy megszüntetése végrehajtásához szükséges lépéseket.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, amely a kiszolgáló üzembe helyezése az Azure AD felhasználói épül. Ez a szolgáltatás leírása, hogyan működik, és gyakran ismételt kérdések kapcsolatos tudnivalókat lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az Azure Active Directory-as-szoftverszolgáltatások (SaaS) alkalmazások automatizálása](../active-directory-saas-app-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban elérhető. Az általános Microsoft Azure használati feltételek az előzetes verziójú funkciók további információkért lásd: [a Microsoft Azure előzetesekre vonatkozó kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik:

* Egy Azure AD-bérlő.
* A Zscaler egy bérlőt.
* Egy felhasználói fiókot a Zscaler egy rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD-integrációs kiépítése a Zscaler egy SCIM API támaszkodik. Az API-t a Zscaler egy fejlesztők számára a vállalati csomaggal rendelkező fiókokhoz érhető el.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adjon hozzá egy Zscaler az Azure Marketplace-ről

Mielőtt konfigurál egy Zscaler számára az Azure AD-felhasználók automatikus, hozzá Zscaler egy az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listája.

Adja hozzá a Zscaler egy a piactérről, kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a bal oldali navigációs panelen, **Azure Active Directory**.

    ![Az Azure Active Directory ikonra](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler egy** válassza **Zscaler egy** az eredmény panelen. Az alkalmazás hozzáadásához válassza **Hozzáadás**.

    ![Az eredmények listájában egy Zscaler](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Felhasználók hozzárendelése egy Zscaler

Az Azure Active Directory használ egy fogalom megértéséhez nevű *hozzárendelések* meghatározni, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók vagy csoportok, amelyek az alkalmazások az Azure AD hozzá lett rendelve a rendszer szinkronizálja.

Mielőtt konfigurálása és engedélyezése a felhasználók automatikus átadása, döntse el, mely felhasználók vagy csoportok Azure AD-ben Zscaler egy hozzáférésre van szükségük. Ezek a felhasználók vagy csoportok hozzárendelése egy Zscaler, kövesse a [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Felhasználók hozzárendelése egy Zscaler fontos tippek

* Azt javasoljuk, hogy hozzárendelje egyetlen Azure AD-felhasználót Zscaler egy, a felhasználók automatikus konfiguráció teszteléséhez. Hozzárendelhet további felhasználókat vagy csoportokat később.

* Zscaler egy felhasználó hozzárendelése, jelölje ki bármely érvényes alkalmazásspecifikus szerepkör érhető el, ha a hozzárendelés párbeszédpanel. A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Felhasználók automatikus átadása egy Zscaler konfigurálása

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás konfigurálásának lépéseit. Vele létrehozása, frissítése és tiltsa le a felhasználók vagy csoportok Zscaler egy Azure AD-ben a felhasználó vagy csoport-hozzárendelések alapján.

> [!TIP]
> Emellett engedélyezheti az SAML-alapú egyszeri bejelentkezés a Zscaler egy. Kövesse az utasításokat a [Zscaler egy egyszeri bejelentkezést az oktatóanyag](zscaler-One-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, azonban ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurálja a felhasználók automatikus átadása a Zscaler egy Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **vállalati alkalmazások** > **minden alkalmazás** > **Zscaler egy**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zscaler egy**.

    ![A Zscaler egy hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza ki a **kiépítési** fülre.

    ![Zscaler egy kiépítése](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zscaler egy kiépítési mód](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a **bérlői URL-cím** és **titkos jogkivonat** négyzeteken beállításait a Zscaler egy fiókot a 6. lépésben leírtak szerint.

6. A bérlői URL-CÍMÉT és a titkos jogkivonat beszerzéséhez lépjen a **felügyeleti** > **hitelesítési beállítások** a Zscaler egy portál felhasználói felületén. A **hitelesítési típus**válassza **SAML**.

    ![Zscaler egy hitelesítési beállítások](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Válassza ki **SAML konfigurálása** megnyitásához a **SAML konfigurálása** beállítások.

    ![Zscaler egy SAML konfigurálása](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Válassza ki **Enable SCIM-Based kiépítés** úgy szerezheti be a beállítások a **alap URL-cím** és **tulajdonosi jogkivonat**. Mentse a beállításokat. Másolás a **alap URL-cím** beállítást **bérlői URL-cím** az Azure Portalon. Másolás a **tulajdonosi jogkivonat** beállítást **titkos jogkivonat** az Azure Portalon.

7. 5\. lépés látható a mezők kitöltése után válassza ki a **kapcsolat tesztelése** győződjön meg arról, hogy az Azure AD is képes csatlakozni Zscaler egy. Ha a kapcsolat nem sikerül, ellenőrizze, hogy rendszergazdai engedélyekkel rendelkező a Zscaler egy fiókot, és próbálkozzon újra.

    ![Zscaler-kapcsolat egy tesztelése](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Az a **értesítő e-mailt** mezőben adja meg a személy e-mail-címét vagy az üzembe helyezési hiba értesítést szeretne kapni a csoportban. Válassza ki a **e-mail-értesítés küldése, ha hiba történik** jelölőnégyzetet.

    ![Zscaler egy értesítő e-mailt](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók Zscaler egy**.

    ![Zscaler egy felhasználói szinkronizálása](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Tekintse át a Zscaler egy, az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok a Zscaler egy frissítési műveletek. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Zscaler egy egyező felhasználói attribútumok](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportokat, Zscaler egy**.

    ![Zscaler egy csoport szinkronizálása](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Tekintse át a Zscaler egy, az Azure AD-ből szinkronizált oszlopainál a **attribútumleképezések** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a csoportok a Zscaler egy frissítési műveleteket. Szeretné menteni a módosításokat, válassza ki a **mentése**.

    ![Zscaler egy egyező csoportattribútumok](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrő konfigurálásához kövesse az utasításokat a [hatókörkezelési szűrő oktatóanyag](./../active-directory-saas-scoping-filters.md).

15. Ahhoz, hogy az Azure AD létesítési szolgáltatás a Zscaler egy, az a **beállítások** területén módosíthatja **üzembe helyezési állapotra** való **a**.

    ![Zscaler egy kiépítési állapot](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Adja meg a felhasználókat vagy csoportokat, amelyekhez szeretne üzembe helyezni egy Zscaler. Az a **beállítások** területen válassza ki a kívánt értékeket **hatókör**.

    ![Zscaler egy hatókör](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Ha készen áll rendelkezésre, válassza ki a **mentése**.

    ![Zscaler egy mentése](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó vagy csoport meghatározott **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az újabb szinkronizálás hosszabb időt vesz igénybe. Mindaddig, amíg az Azure AD létesítési szolgáltatás fut bekövetkezésük körülbelül 40 percenként. 

Használhatja a **szinkronizálás részleteivel** előrehaladásának figyeléséhez, és kövesse a hivatkozásokat, a kiépítési tevékenységek jelentésének szakaszt. A jelentés az Azure AD létesítési szolgáltatás az egyik Zscaler által végrehajtott összes műveletet ismerteti.

Olvassa el az Azure AD létesítési naplók információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
