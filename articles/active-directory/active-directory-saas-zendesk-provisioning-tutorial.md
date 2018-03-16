---
title: "Oktatóanyag: Zendesk konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikus kiépítése és leépíti a Zendesk felhasználói fiókok."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: f0cf1fbe580d6e4ee2d99f1fb757bd13eae72d70
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Zendesk konfigurálása

Ez az oktatóanyag célja az Zendesk és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan létesítsen és leépíti a felhasználók és/vagy Zendesk-csoportokra végrehajtandó lépések bemutatása. 

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő:

*   Az Azure AD-bérlő
*   Egy Zendesk bérlőt a [vállalati](https://www.zendesk.com/product/pricing/) a terv és jobban engedélyezve 
*   A Zendesk rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction), elérhető a vállalati terven Zendesk-csoportok vagy nagyobb.

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a gyűjteményből
Zendesk konfigurálása az Azure AD-kiépítés automatikus felhasználóhoz, előtt kell az Azure AD application gallery Zendesk hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery Zendesk hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Zendesk hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Zendesk**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk6.png)

5. Az eredmények panelen válassza ki a **Zendesk**, majd kattintson a **hozzáadása** gombra kattintva adja hozzá ehhez a SaaS-alkalmazások listája.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva. 

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, mielőtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok ehhez hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok Zendesk itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve ehhez az automatikus felhasználó konfigurálása kiosztás teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó rendel a Zendesk, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Felhasználólétesítés automatikus Zendesk konfigurálása 

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók konfigurálásának lépéseit és/vagy az Azure AD-felhasználó vagy csoport hozzárendelések alapján Zendesk csoportok.

> [!TIP]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezése ehhez a, utasítások találhatók a [Zendesk egyszeri bejelentkezés az oktatóanyag](active-directory-saas-zendesk-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése ehhez az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki az ehhez a SaaS-alkalmazások listája.
 
    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk3.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk16.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. A a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, **titkos Token**, és **tartomány** a Zendesk-fiók. Például a következő értékek a következők:

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a Zendesk tenant a rendszergazdai fiók felhasználónevét. Példa: admin@contoso.com.

    *   Az a **titkos Token** mezőben, a titkos token feltöltése a 6. lépésben leírtak szerint.

    *   Az a **tartomány** mezőbe a Zendesk-bérlő altartomány feltöltéséhez.
    Például: A fiók bérlői URL-cím https://my-tenant.zendesk.com, a altartomány lenne **saját bérlőt**.

6. A **titkos Token** az ehhez a fiókhoz található **Admin > API > Beállítások**. 

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

7. Után a program kitölti a mezőket az 5. lépés, kattintson a **kapcsolat tesztelése** biztosításához az Azure AD Zendesk kapcsolódhatnak. Ha nem sikerül, ellenőrizze a Zendesk-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot, a létesítési hiba értesítéseket, és jelölje be a jelölőnégyzetet kell **e-mailben értesítést küld, ha hiba lép fel**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználók a Zendesk**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk10.png)

11. Tekintse át a felhasználói attribútumok, ehhez az Azure ad-lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat az ehhez a frissítési műveletek. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk11.png)

12. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-csoportot ZenDesk**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk12.png)

13. Tekintse át az ehhez a szinkronizált Azure ad-csoport attribútumait a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg az ehhez a frissítési műveletek csoportokhoz. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk13.png)

14. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./active-directory-saas-scoping-filters.md).

15. Az Azure AD szolgáltatás Zendesk-kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk14.png)

16. Válassza ki a kívánt értékeket a Zendesk azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk15.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zendesk kiépítése](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk18.png)


Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a Zendesk szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
