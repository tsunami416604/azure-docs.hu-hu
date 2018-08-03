---
title: 'Oktatóanyag: Azure Active Directory-integráció az BetterWorks |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BetterWorks között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 11e496b91eabeb6034cba25c8d0c1f87855467f9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433143"
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Oktatóanyag: Azure Active Directory-integráció az BetterWorks

Ebben az oktatóanyagban elsajátíthatja, hogyan BetterWorks integrálása az Azure Active Directory (Azure AD).

BetterWorks integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá BetterWorks Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett BetterWorks (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

BetterWorks az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy BetterWorks egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. BetterWorks hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-betterworks-from-the-gallery"></a>BetterWorks hozzáadása a katalógusból
Az Azure AD integrálása a BetterWorks konfigurálásához hozzá kell BetterWorks a katalógusból a felügyelt SaaS-alkalmazások listájára.

**BetterWorks hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **BetterWorks**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/tutorial_betterworks_search.png)

1. Az eredmények panelen válassza ki a **BetterWorks**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó BetterWorks az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó BetterWorks mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó BetterWorks hivatkozás kapcsolata kell létrehozni.

BetterWorks, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az BetterWorks tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[BetterWorks tesztfelhasználó létrehozása](#creating-a-betterworks-test-user)**  – egy megfelelője a Britta Simon BetterWorks, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és BetterWorks alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BetterWorks, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **BetterWorks** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_samlbase.png)

1. Az a **BetterWorks tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**:

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.betterworks.com/saml2/metadata/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.betterworks.com/saml2/acs/`

1. Az a **BetterWorks tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_url1.png)

    a. Kattintson a **speciális URL-beállítások megjelenítése**.

    b. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.betterworks.com`

    > [!NOTE] 
    > Ezek nem tényleges értékek állnak. Az értékeket módosítsa a válasz URL-cím, a azonosítója és a tényleges bejelentkezési URL-címet. Kapcsolattartó [BetterWorks támogatási csoportjának](mailto:support@betterworks.com) beolvasni ezeket az értékeket.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_certificate.png)  

1. BetterWorks alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**attribútum**" az alkalmazás lapján. Az alábbi képernyőfelvételen látható erre egy példa látható. 

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_attribute.png)

1. Az a **SAML-jogkivonat attribútumai** párbeszédpanelen, az alábbi táblázatban szereplő minden egyes sorára hajtsa végre az alábbi lépéseket:
 
   | Attribútum neve | Attribútum értéke |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_officespace_05.png)

   b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható. 

   c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
   d. Kattintson az **OK** gombra.

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **BetterWorks** oldalon kell küldenie a letöltött **metaadatainak XML** való [BetterWorks támogatási csoportjának](mailto:support@betterworks.com).


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/betterworks-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-betterworks-test-user"></a>BetterWorks tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű BetterWorks hoz létre. Együttműködve [BetterWorks támogatási csoportjának](mailto:support@betterworks.com) a felhasználók hozzáadása az BetterWorks platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BetterWorks Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel BetterWorks, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **BetterWorks**.

    ![Egyszeri bejelentkezés konfigurálása](./media/betterworks-tutorial/tutorial_betterworks_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BetterWorks csempére kattint, meg kell lekérése automatikusan bejelentkezett az BetterWorks alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/betterworks-tutorial/tutorial_general_01.png
[2]: ./media/betterworks-tutorial/tutorial_general_02.png
[3]: ./media/betterworks-tutorial/tutorial_general_03.png
[4]: ./media/betterworks-tutorial/tutorial_general_04.png

[100]: ./media/betterworks-tutorial/tutorial_general_100.png

[200]: ./media/betterworks-tutorial/tutorial_general_200.png
[201]: ./media/betterworks-tutorial/tutorial_general_201.png
[202]: ./media/betterworks-tutorial/tutorial_general_202.png
[203]: ./media/betterworks-tutorial/tutorial_general_203.png

