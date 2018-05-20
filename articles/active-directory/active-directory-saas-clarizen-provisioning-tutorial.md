---
title: 'Oktatóanyag: Clarizen konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Clarizen felhasználói fiókokat.
services: active-directory
documentationcenter: ''
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
ms.author: v-ant-msft
ms.openlocfilehash: 779fa09781b5ea6e13450d609703bec0c08e38e4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Clarizen konfigurálása

Ez az oktatóanyag célja az Clarizen és Azure Active Directory (Azure AD) és az Azure AD konfigurálása automatikusan létesítsen és leépíti a felhasználók és/vagy csoportok Clarizen végrehajtandó lépések bemutatása.

> [!NOTE]
> Ez az oktatóanyag leírja egy összekötőt, az Azure AD-felhasználó kiépítési Service platformra épül. Ez a szolgáltatás funkciója, hogyan működik, és gyakran ismételt kérdések fontos tudnivalókat tartalmaz [felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő:

*   Az Azure AD-bérlő
*   Egy Clarizen bérlőt a [Enterprise Edition](https://www.clarizen.com/product/pricing/) a terv és jobban engedélyezve 
*   A Clarizen rendszergazdai jogosultságokkal rendelkező felhasználói fiókot

> [!NOTE]
> Az Azure AD-integrációs kiépítés támaszkodik a [Clarizen API](https://api.clarizen.com/v2.0/services/), Enterprise Edition terven Clarizen csoportokhoz érhető el vagy nagyobb.

## <a name="adding-clarizen-from-the-gallery"></a>A gyűjteményből Clarizen hozzáadása
Clarizen konfigurálása az Azure AD-kiépítés automatikus felhasználóhoz, előtt kell az Azure AD application gallery Clarizen hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD application gallery Clarizen hozzáadásához hajtsa végre az alábbi lépéseket:**

1. A a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Clarizen hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Clarizen**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. Az eredmények panelen válassza ki a **Clarizen**, majd kattintson a **hozzáadása** Clarizen hozzá SaaS-alkalmazások listájának gombra.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Felhasználók hozzárendelése Clarizen

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználókiépítése keretében csak a felhasználók és/vagy csoportok "hozzárendelt" egy az Azure AD alkalmazás szinkronizálva. 

Automatikus felhasználó-kiépítés engedélyezése és konfigurálása, előtt meg kell határoznia, melyik felhasználói és/vagy az Azure AD-csoportok Clarizen hozzáférésre van szükségük. Ha úgy döntött, hozzárendelheti a felhasználók és/vagy csoportok Clarizen itt cikk utasításait követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Felhasználók hozzárendelése Clarizen fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Clarizen konfigurálása kiosztás automatikus felhasználó teszteléséhez. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Clarizen, a hozzárendelés párbeszédpanelen válassza ki érvényes alkalmazás-specifikus szerepköröket (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem kiépítés tartoznak.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Felhasználólétesítés automatikus Clarizen konfigurálása 

Ez a szakasz végigvezeti az Azure AD szolgáltatás kiépítését, módosítása, és tiltsa le a felhasználók konfigurálásának lépéseit és/vagy az Azure AD felhasználói és/vagy a csoport-hozzárendelések alapján Clarizen csoportok.

> [!TIP]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezése a Clarizen, utasítások találhatók a [Clarizen egyszeri bejelentkezés az oktatóanyag](active-directory-saas-clarizen-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül automatikus a felhasználók átadása, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Konfigurálása automatikus felhasználókiépítése Clarizen az Azure AD-ben:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás**.

2. Válassza ki a Clarizen Szolgáltatottszoftver-alkalmazáshoz a listából.
 
    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adjon meg a **tartomány**, **rendszergazda felhasználóneve**, és **rendszergazdai jelszó** a Clarizen fiók. Például a következő értékek a következők:

    *   Az a **rendszergazda felhasználóneve** mezőbe feltöltése a Clarizen tenant a rendszergazdai fiók felhasználónevét. Példa: admin@contoso.com.

    *   Az a **rendszergazdai jelszó** mezőbe a megfelelő a rendszergazda felhasználóneve admin fiók jelszavát feltöltéséhez.

    *   Az a **tartomány** mezőben, a 6. lépés alapján altartomány feltöltéséhez.
    
6. Beolvasni a **serverLocation** alatt leírt lépéseket alapján Clarizen fiókja **hitelesítési** a [Clarizen tartozó Rest API útmutató](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Amint a serverLocation, használja a altartomány példának az URL-cím, az alábbi adatokkal való feltöltéséhez a **tartomány** mező.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. A program kitölti a mezőket az 5. lépés, után kattintson a **kapcsolat tesztelése** biztosításához az Azure AD Clarizen kapcsolódhatnak. Ha nem sikerül, ellenőrizze a Clarizen fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot, a létesítési hiba értesítéseket, és jelölje be a jelölőnégyzetet kell **e-mailben értesítést küld, ha hiba lép fel**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-felhasználókat Clarizen**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Tekintse át a felhasználói attribútumok, az Azure AD a Clarizen lettek szinkronizálva a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Clarizen a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. Az a **hozzárendelések** szakaszban jelölje be **szinkronizálása Azure Active Directory-csoportot Clarizen**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Tekintse át a Clarizen szinkronizálódnak az Azure ad-csoport attribútumokat a **attribútum leképezési** szakasz. A kiválasztott attribútumok **egyező** tulajdonságok felel meg a frissítési műveletek Clarizen csoportok használhatók. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Hatókörként szűrők konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](./active-directory-saas-scoping-filters.md).

15. Az Azure AD szolgáltatás Clarizen kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Válassza ki a kívánt értékeket a Clarizen azokat a felhasználók és/vagy csoportok, amelyeket meg szeretne meghatározásához **hatókör** a a **beállítások** szakasz.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Clarizen kiépítése](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a kezdeti szinkronizálás az összes felhasználót és/vagy csoportok meghatározott **hatókör** a a **beállítások** szakasz. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, körülbelül 40 percenként történik, amíg az Azure AD szolgáltatás kiépítését fut-e hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentést, amely ismerteti a Clarizen szolgáltatás kiépítését az Azure AD által végzett összes műveletet.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
