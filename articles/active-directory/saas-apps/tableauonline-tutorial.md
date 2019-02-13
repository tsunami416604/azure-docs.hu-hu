---
title: 'Oktatóanyag: A Tableau Online az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tableau Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eee7adb7c4c0efc8a455d8dc74bc723644d006f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169020"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Oktatóanyag: A Tableau Online az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Tableau Online az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása Tableau Online nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Tableau Online az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Tableau Online (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Tableau Online, a következőkre van szükség:

- Azure AD-előfizetés
- A Tableau Online egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból Tableau Online hozzáadása
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-tableau-online-from-the-gallery"></a>A katalógusból Tableau Online hozzáadása
Az Azure AD integrálása a Tableau Online konfigurálásához hozzá kell Tableau Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Tableau Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Tableau Online**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. Az eredmények panelen válassza ki a **Tableau Online**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Tableau Online egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó, a Tableau Online mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Tableau Online hivatkozás kapcsolatát kell létrehozni.

A Tableau Online hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés Tableau online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Tableau Online tesztfelhasználó létrehozása](#creating-a-tableau-online-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Tableau online-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Tableau Online alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a Tableau Online, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Tableau Online** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. Az a **Tableau Online tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://sso.online.tableau.com`

    b. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban bejelentkezés, a Tableau Online alkalmazás. Lépjen a **beállítások** , majd **hitelesítési**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. SAML, a engedélyezéséhez **hitelesítési típusok** szakaszban. Ellenőrizze a **egyszeri bejelentkezéses SAML-** jelölőnégyzetet.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Görgessen lefelé, amíg **metaadatait tartalmazó fájl importálása a Tableau Online szolgáltatásba** szakaszban.  Kattintson a Tallózás gombra, és az Azure AD-ból letöltött metaadatait tartalmazó fájl importálása. Kattintson a **alkalmaz**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. Az a **felel meg a helyességi feltételek** szakaszban, a megfelelő identitásszolgáltató helyességi feltétel nevét a **e-mail-cím**, **Utónév**, és **Vezetéknév**. Ez az információ lekérése az Azure ad-ből: 
  
    a. Az Azure Portalon lépjen a **Tableau Online** alkalmazás integráció lapján.
    
    b. Az attribútumok területen válassza ki a **"megtekintése és egyéb felhasználói attribútumok szerkesztése"** jelölőnégyzetet. 
    
   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection.png)
      
    c. Másolja a névtér értéke attribútumait: givenname, e-mailek és a Vezetéknév, az alábbi lépések segítségével:

   ![Az Azure AD egyszeri bejelentkezés](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Kattintson a **user.givenname** érték 
    
    e. Másolja az értéket a **névtér** szövegmezőbe.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection2.png)

    f. Másolja ki a névteret értékeit az e-mailek és a Vezetéknév kövesse a fenti lépéseket.

    g. Váltson a Tableau Online alkalmazást, majd állítsa be a **Tableau online-hoz attribútumait** szakasz az alábbiak szerint:
     * E-mail cím: **mail** vagy **userprincipalname**
     * Utónév: **givenname**
     * Vezetéknév: **vezetékneve**
   
   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-tableau-online-test-user"></a>A Tableau Online tesztfelhasználó létrehozása

Ebben a szakaszban egy a Tableau Online Britta Simon nevű felhasználó hoz létre.

1. A **Tableau Online**, kattintson a **beállítások** , majd **hitelesítési** szakaszban. Görgessen le a **felhasználók kijelölése** szakaszban. Kattintson a **felhasználók hozzáadása** , majd **E-mail címeket**.
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Válassza ki **adja hozzá a felhasználók az egyszeri bejelentkezés (SSO) hitelesítést**. Az a **adja meg E-mail címeket** szövegmező hozzáadása britta.simon@contoso.com
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Tableau online-hoz való hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon Tableau online-hoz való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Tableau Online**.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen, a Tableau Online csempére kattint, akkor kell lekérése automatikusan bejelentkezett, a Tableau Online alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png
