---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező 123ContactForm |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és 123ContactForm között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 450fc8df69eb5ffb99cb6548e80cd4d3628e7419
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819441"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező 123ContactForm

Ebben az oktatóanyagban elsajátíthatja, hogyan 123ContactForm integrálása az Azure Active Directory (Azure AD).

123ContactForm integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá 123ContactForm Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a 123ContactForm (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

123ContactForm az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy 123ContactForm egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. 123ContactForm hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-123contactform-from-the-gallery"></a>123ContactForm hozzáadása a katalógusból
Az Azure AD integrálása a 123ContactForm konfigurálásához hozzá kell 123ContactForm a katalógusból a felügyelt SaaS-alkalmazások listájára.

**123ContactForm hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **123ContactForm**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/tutorial_123contactform_search.png)

5. Az eredmények panelen válassza ki a **123ContactForm**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó 123ContactForm az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó 123ContactForm mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó 123ContactForm hivatkozás kapcsolata kell létrehozni.

123ContactForm, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az 123ContactForm tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[123ContactForm tesztfelhasználó létrehozása](#creating-a-123contactform-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó 123ContactForm rendelkeznie.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és 123ContactForm alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés 123ContactForm, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **123ContactForm** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Az a **123ContactForm tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/url2.png)

    a. Kattintson a **speciális URL-beállítások megjelenítése** lehetőség

    b. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse ezeket a tényleges URL-címek és az oktatóanyag későbbi részében ismertetett azonosító értékét kell.
    
5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/tutorial_general_400.png)

7. Az egyszeri bejelentkezés konfigurálása **123ContactForm** oldalán, lépjen a [ https://www.123contactform.com/form-2709121/ ](https://www.123contactform.com/form-2709121/) , és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/submit.png) 

    a. Az a **E-mail** szövegmezőbe írja be az e-mail-címe a felhasználói azaz **BrittaSimon@Contoso.com**.

    b. Kattintson a **feltöltése** , és keresse meg a metaadatainak XML-fájl, amely már letöltötte az Azure Portalról.

    c. Kattintson a **KÜLDÉS űrlap**.

8. Az a **Microsoft Azure AD egyszeri bejelentkezés – alkalmazás beállításainak konfigurálása** hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/url3.png)

    a. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**, másolása a **azonosító** a példány értékét, és illessze be a **azonosító** szövegmezőjébe  **123ContactForm tartomány és URL-címek** szakaszban az Azure Portalon.
    
    b. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**, másolása a **válasz URL-cím** a példány értékét, és illessze be a **válasz URL-cím** szövegmezőjébe  **123ContactForm tartomány és URL-címek** szakaszban az Azure Portalon.

    c. Ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, másolása a **SIGN-ON URL** a példány értékét, és illessze be a **bejelentkezési URL-** szövegmezőjébe  **123ContactForm tartomány és URL-címek** szakaszban az Azure Portalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/123contactform-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-123contactform-test-user"></a>123ContactForm tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés 123ContactForm való hozzáférést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel 123ContactForm, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **123ContactForm**.

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/tutorial_123contactform_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a 123ContactForm csempére kattint, meg kell lekérése automatikusan bejelentkezett az 123ContactForm alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/123contactform-tutorial/tutorial_general_01.png
[2]: ./media/123contactform-tutorial/tutorial_general_02.png
[3]: ./media/123contactform-tutorial/tutorial_general_03.png
[4]: ./media/123contactform-tutorial/tutorial_general_04.png

[100]: ./media/123contactform-tutorial/tutorial_general_100.png

[200]: ./media/123contactform-tutorial/tutorial_general_200.png
[201]: ./media/123contactform-tutorial/tutorial_general_201.png
[202]: ./media/123contactform-tutorial/tutorial_general_202.png
[203]: ./media/123contactform-tutorial/tutorial_general_203.png

