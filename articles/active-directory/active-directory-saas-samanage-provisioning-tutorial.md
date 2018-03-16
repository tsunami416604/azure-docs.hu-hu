---
title: "Oktatóanyag: Samanage konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Samanage felhasználói fiókokat."
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
ms.openlocfilehash: 8f11beff2c78386f4c3e8130c8e5d72465b8fe87
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Samanage konfigurálása

Ez az oktatóanyag célja az Samanage és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan létesítsen és leépíti a felhasználók és/vagy csoportok Samanage végrehajtandó lépések bemutatása.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő:

*   Az Azure Active Directory-bérlő
*   Egy Samanage bérlőt a [Professional](https://www.samanage.com/pricing/) a terv és jobban engedélyezve 
*   A Samanage rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Samanage REST API](https://www.samanage.com/api/), a Professional terven Samanage csoportokhoz érhető el vagy nagyobb.

## <a name="adding-samanage-from-the-gallery"></a>A gyűjteményből Samanage hozzáadása
Samanage konfigurálása az Azure AD-kiépítés automatikus felhasználóhoz, előtt kell az Azure AD application gallery Samanage hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery Samanage hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Samanage hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Samanage**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage4.png)

5. Az eredmények panelen válassza ki a **Samanage**, majd kattintson a **hozzáadása** Samanage hozzá SaaS-alkalmazások listájának gombra.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage6.png)

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage5.png)

## <a name="assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva. 

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, előtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok Samanage hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok Samanage itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Felhasználók hozzárendelése Samanage fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Samanage konfigurálása kiosztás automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Samanage, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Felhasználólétesítés automatikus Samanage konfigurálása

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók konfigurálásának lépéseit és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján Samanage csoportok.

> [!TIP]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezése a Samanage, utasítások találhatók a [Samanage egyszeri bejelentkezés az oktatóanyag](active-directory-saas-samanage-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást. További információkért lásd: a [Samanage egyszeri bejelentkezés az oktatóanyag](active-directory-saas-samanage-tutorial.md).

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése Samanage az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a Samanage Szolgáltatottszoftver-alkalmazáshoz a listából.
 
    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage7.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage8.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage9.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **rendszergazda felhasználóneve**, és **rendszergazdai jelszó** a Samanages fiók. Például a következő értékek a következők:

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a Samanage tenant a rendszergazdai fiók felhasználónevét. Példa: admin@contoso.com.

    *   Az a **rendszergazdai jelszó** mezőbe feltöltése a korábban megadott rendszergazdai felhasználónév megfelelő jelszót.

6. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD Samanage kapcsolódhatnak. Ha nem sikerül, ellenőrizze a Samanage fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage10.png)

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot, a létesítési hiba értesítéseket, és jelölje be a jelölőnégyzetet kell **e-mailben értesítést küld, ha hiba lép fel**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage11.png)

8. Kattintson a **Save** (Mentés) gombra.

9. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat Samanage**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage12.png)

10. Tekintse át a felhasználói attribútumok, az Azure AD a Samanage lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Samanage a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage13.png)

12. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-csoportot Samanage**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage14.png)

13. Tekintse át a Samanage szinkronizálódnak az Azure ad-csoport attribútumokat a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok felel meg a frissítési műveletek Samanage csoportok használhatók. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage15.png)

14. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./active-directory-saas-scoping-filters.md).

15. Az Azure AD szolgáltatás Samanage kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage16.png)

16. Válassza ki a kívánt értékeket a Samanage azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage17.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Samanage kiépítése](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage18.png)

Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a Samanage szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
