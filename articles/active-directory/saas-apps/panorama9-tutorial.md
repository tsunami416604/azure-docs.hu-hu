---
title: 'Oktatóanyag: Azure Active Directory-integráció az Panorama9 |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Panorama9 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 7285ef4bea1dccb651360520bfe8bc4289d54f8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046039"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Oktatóanyag: Azure Active Directory-integráció az Panorama9

Ebben az oktatóanyagban elsajátíthatja, hogyan Panorama9 integrálása az Azure Active Directory (Azure AD).

Panorama9 integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Panorama9 Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Panorama9 (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Panorama9 az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Panorama9 egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Panorama9 hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-panorama9-from-the-gallery"></a>Panorama9 hozzáadása a katalógusból
Az Azure AD integrálása a Panorama9 konfigurálásához hozzá kell Panorama9 a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Panorama9 hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Panorama9**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/tutorial_panorama9_search.png)

5. Az eredmények panelen válassza ki a **Panorama9**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Panorama9 az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Panorama9 mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Panorama9 hivatkozás kapcsolata kell létrehozni.

Panorama9, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Panorama9 tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Panorama9 tesztfelhasználó létrehozása](#creating-a-panorama9-test-user)**  – egy megfelelője a Britta Simon Panorama9, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Panorama9 alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Panorama9, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Panorama9** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. Az a **Panorama9 tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://dashboard.panorama9.com/saml/access/3262`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Panorama9 ügyfél-támogatási csapatának](https://support.panorama9.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_general_400.png)

6. Az a **Panorama9 konfigurációs** területén kattintson **konfigurálása Panorama9** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

5. Egy másik böngészőablakban jelentkezzen be a Panorama9 vállalati hely rendszergazdaként.

6. A felső eszköztáron kattintson **kezelés**, és kattintson a **bővítmények**.
   
   ![Bővítmények](./media/panorama9-tutorial/ic790023.png "bővítmények")
7. Az a **bővítmények** párbeszédpanelen kattintson a **egyszeri bejelentkezés**.
   
   ![Egyszeri bejelentkezés](./media/panorama9-tutorial/ic790024.png "egyszeri bejelentkezés")
8. Az a **beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
   
   ![Beállítások](./media/panorama9-tutorial/ic790025.png "beállításai")
   
    a. A **identitásszolgáltató szolgáltató URL-címe** szövegmező, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
   
    b. A **tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be a **ujjlenyomat** tanúsítványt, amely az Azure Portalról másolt értékét.    
         
9. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-panorama9-test-user"></a>Panorama9 tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Panorama9 szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Panorama9 be.  

Panorama9, esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Panorama9** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **kezelés**, és kattintson a **felhasználók**.
   
  ![Felhasználók](./media/panorama9-tutorial/ic790027.png "felhasználók")

3. A felhasználók szakaszban kattintson a **+** új felhasználó hozzáadásához.

 ![Felhasználók](./media/panorama9-tutorial/ic790028.png "felhasználók")

4. A felhasználói adatok szakaszban keresse meg, írja be egy érvényes kíván létrehozni az Azure Active Directory-felhasználó e-mail-címét a **E-mail** szövegmezőbe.

5. A felhasználók szakaszban kattintson térjen **mentése**.
   
> [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Panorama9 Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Panorama9, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Panorama9**.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Panorama9 csempére kattint, akkor kell lekérése automatikusan bejelentkezett Panorama9 alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

