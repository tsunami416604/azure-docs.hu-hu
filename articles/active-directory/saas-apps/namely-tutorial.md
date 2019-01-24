---
title: 'Oktatóanyag: Az Azure Active Directory-integráció engedélyezésével |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory között, nevezetesen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: b76dfa7d16da487798281f5f31b9915ca9716ebc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808395"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Oktatóanyag: Az Azure Active Directory-integráció engedélyezésével

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az Azure Active Directory (Azure AD) nevezetesen.

Engedélyezésével az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki fér hozzá engedélyezésével az Azure AD-ben
- Engedélyezheti a felhasználók automatikusan el nevezetesen a bejelentkezett (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integráció engedélyezésével, hogy az a következőkre van szükség:

- Azure AD-előfizetés
- Egy nevezetesen egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Nevezetesen hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-namely-from-the-gallery"></a>Nevezetesen hozzáadása a katalógusból
Engedélyezésével az Azure AD-be, az integráció konfigurálásához hozzá kell nevezetesen a galériából a felügyelt SaaS-alkalmazások listájára.

**Nevezetesen adja hozzá a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **nevezetesen**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/tutorial_namely_search.png)

1. Az eredmények panelen válassza ki a **nevezetesen**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az úgynevezett "Britta Simon" tesztfelhasználó nevezetesen alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó nevezetesen mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a hivatkozás kapcsolata nevezetesen kell létrehozni.

Nevezetesen, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés engedélyezésével, hogy a kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Létrehozás egy nevezetesen tesztfelhasználó](#creating-a-namely-test-user)**  szeretné, hogy egy megfelelője a Britta Simon – a nevezetesen, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a nevezetesen alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés az nevezetesen, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **nevezetesen** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_samlbase.png)

1. Az a **nevezetesen tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.namely.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [nevezetesen ügyfél-támogatási csapatának](https://www.namely.com/contact/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_general_400.png)

1. Az a **nevezetesen konfigurációs** területén kattintson **konfigurálása nevezetesen** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a nevezetesen vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **vállalati**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_06.png) 

1. Kattintson a **Beállítások** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_07.png) 

1. Kattintson a **SAML**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_08.png) 

1. Az a **SAML-beállítások** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kattintson a **SAML engedélyezése**. 

    b. Az a **Identity provider egyszeri bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
    
    c. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **szolgáltató identitástanúsítványt** szövegmezőbe.
     
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-namely-test-user"></a>Létrehozás egy felhasználó nevezetesen tesztelése

Ez a szakasz célja, nevezetesen Britta Simon nevű felhasználó létrehozásához.

**Nevezetesen Britta Simon nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a nevezetesen vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **személyek**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_10.png) 

1. Kattintson a **Directory** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_11.png) 

1. Kattintson a **hozzáadása új személy**.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_12.png)

1. Az a **hozzáadása új személy** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Az a **Utónév** szövegmezőbe írja be **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be **Simon**.

    c. Az a **E-mail** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon való hozzáférés biztosításával nevezetesen Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Hozzárendelése Britta Simon, nevezetesen, hajtsa végre a következő lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **nevezetesen**.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Amikor rákattint a nevezetesen csempére a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett, a nevezetesen alkalmazás

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

