---
title: 'Oktatóanyag: Azure Active Directory-integráció az Kronos |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Kronos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 31e8c990e39b3dc99ccd4dcda0a8d00ecb83b440
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435883"
---
# <a name="tutorial-azure-active-directory-integration-with-kronos"></a>Oktatóanyag: Azure Active Directory-integráció az Kronos

Ebben az oktatóanyagban elsajátíthatja, hogyan Kronos integrálása az Azure Active Directory (Azure AD).

Kronos integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Kronos Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Kronos (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Kronos az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A **Kronos munkaerő központi** SSO engedélyezése az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Kronos hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-kronos-from-the-gallery"></a>Kronos hozzáadása a katalógusból
Az Azure AD integrálása a Kronos konfigurálásához hozzá kell Kronos a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Kronos hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Kronos**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/tutorial_kronos_search.png)

1. Az eredmények panelen válassza ki a **Kronos**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/tutorial_kronos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Kronos az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Kronos mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Kronos hivatkozás kapcsolata kell létrehozni.

Kronos, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Kronos tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Kronos tesztfelhasználó létrehozása](#creating-a-kronos-test-user)**  – egy megfelelője a Britta Simon Kronos, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Kronos alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Kronos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Kronos** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_kronos_samlbase.png)

1. Az a **Kronos tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_kronos_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.kronos.net/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Kronos támogatási csoportjának](https://www.kronos.in/contact/en-in/form) beolvasni ezeket az értékeket.
 
1. Kronos alkalmazását a SAML helyességi feltételek vár egy megadott formátumban. Együttműködve [Kronos támogatási csoportjának](https://www.kronos.in/contact/en-in/form) , először azonosítsa a megfelelő felhasználói azonosító, amely az alkalmazás van hozzárendelve. Szánjon még az attribútuma, amely használja ezt a hozzárendelést szeretne vonatkozó útmutatást.
 
     A Microsoft használatát javasolja a **"NameIdentifier karakterláncra"** attribútumra, mint a felhasználói azonosító. Ezek az attribútumok értékeinek kezelheti a **"Felhasználói attribútumok"** szakasz alkalmazás integráció lapján.
     
     Az alábbi képernyőfelvételen látható erre egy példa látható. Itt mi van leképezve a **felhasználói azonosító (nameid)** a **ExtractMailPrefix()** funkcióját **user.userprincipalname**. Ez biztosítja, hogy az előtag értékének e-mail a felhasználó, amely az egyedi felhasználói azonosítót. Ez az alkalmazás Kronos minden sikeres válasz érkezik. 
     
    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_kronos_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanel:

    a. A felhasználói azonosító legördülő listában válassza ki a **ExtractMailPrefix**.

    b. Az a **Mail** legördülő listában válassza **user.userprincipalname**.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_kronos_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **Kronos** oldalon kell küldenie a letöltött **metaadatainak XML** való [Kronos támogatási csoportjának](https://www.kronos.in/contact/en-in/form). 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kronos-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-kronos-test-user"></a>Kronos tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Kronos hoz létre. Kronos alkalmazása szükséges, az alkalmazás egyszeri bejelentkezés végrehajtása előtt ki kell építeni a felhasználók. 

Együttműködik a [Kronos támogatási csoportjának](https://www.kronos.in/contact/en-in/form) ezek a felhasználók kiépítése az alkalmazásba. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Kronos Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Kronos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Kronos**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kronos-tutorial/tutorial_kronos_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés konfigurációja a hozzáférési panelen.

Ha a hozzáférési panelen a Kronos csempére kattint, meg kell lekérése automatikusan bejelentkezett az Kronos alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kronos-tutorial/tutorial_general_01.png
[2]: ./media/kronos-tutorial/tutorial_general_02.png
[3]: ./media/kronos-tutorial/tutorial_general_03.png
[4]: ./media/kronos-tutorial/tutorial_general_04.png

[100]: ./media/kronos-tutorial/tutorial_general_100.png

[200]: ./media/kronos-tutorial/tutorial_general_200.png
[201]: ./media/kronos-tutorial/tutorial_general_201.png
[202]: ./media/kronos-tutorial/tutorial_general_202.png
[203]: ./media/kronos-tutorial/tutorial_general_203.png

