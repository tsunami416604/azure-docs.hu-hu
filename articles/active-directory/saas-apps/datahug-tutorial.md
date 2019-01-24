---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Datahug |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Datahug között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: a7a7a9cffa2dab3cc50a393f15d7419728502f8e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814223"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Datahug

Ebben az oktatóanyagban elsajátíthatja, hogyan Datahug integrálása az Azure Active Directory (Azure AD).

Datahug integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Datahug Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Datahug (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd. [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Datahug az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Datahug egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Datahug hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-datahug-from-the-gallery"></a>Datahug hozzáadása a katalógusból
Az Azure AD integrálása a Datahug konfigurálásához hozzá kell Datahug a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Datahug hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Datahug**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/tutorial_datahug_search.png)

1. Az eredmények panelen válassza ki a **Datahug**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Datahug az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Datahug mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Datahug hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Datahug a.

Az Azure AD egyszeri bejelentkezés az Datahug tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Datahug tesztfelhasználó létrehozása](#creating-a-datahug-test-user)**  – egy megfelelője a Britta Simon Datahug, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Datahug alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Datahug, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Datahug** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_samlbase.png)

1. Az a **Datahug tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_ur1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://apps.datahug.com/identity/<uniqueID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://apps.datahug.com/identity/<uniqueID>/acs`

1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Itt javasoljuk, hogy az egyedi érték a karakterlánc a azonosítója és a válasz URL-cím használatát. Kapcsolattartó [Datahug ügyfél-támogatási csapatának](http://datahug.com/about/contact-us/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_certificate.png) 

1.  Ellenőrizze **"Speciális tanúsítvány-aláírási beállítások megjelenítése"** , és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_cert.png)

    a. A **aláírási beállítás**válassza **bejelentkezési SAML helyességi feltétel**.
    
    b. A **aláírási algoritmus**válassza **SHA1**.
 
1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_general_400.png)
    
1. Az a **Datahug konfigurációs** területén kattintson **konfigurálása Datahug** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Datahug** oldalon kell küldenie a letöltött **metaadatainak XML**, **SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím**  való [Datahug támogatási](http://datahug.com/about/contact-us/). Ezt az alkalmazást, állítsa be a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/datahug-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-datahug-test-user"></a>Datahug tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Datahug jelentkezzen be, akkor ki kell építeni Datahug be.  
Datahug, ha a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Datahug vállalati webhely.

1. A kurzort a **fogaskerék** a jobb felső sarokban, majd kattintson a **beállításai**
   
   ![Alkalmazott hozzáadása](./media/datahug-tutorial/1.png)

1. Válasszon **személyek** , és kattintson a **felhasználó hozzáadása** lap

    ![Alkalmazott hozzáadása](./media/datahug-tutorial/2.png)

1. Írja be az e-mailt, hozzon létre egy olyan fiókkal, és kattintson a kívánt személy **Hozzáadás**.

    ![Alkalmazott hozzáadása](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Felhasználói regisztráció e-mail a kiválasztásával küldhet **üdvözlő e-mailt** jelölőnégyzetet.  
    > Létrehozásakor a Salesforce-fiók az üdvözlő e-mailt küld.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Datahug Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Datahug, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Datahug**.

    ![Egyszeri bejelentkezés konfigurálása](./media/datahug-tutorial/tutorial_datahug_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
Ha a hozzáférési panelen a Datahug csempére kattint, meg kell lekérése automatikusan bejelentkezett az Datahug alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/datahug-tutorial/tutorial_general_01.png
[2]: ./media/datahug-tutorial/tutorial_general_02.png
[3]: ./media/datahug-tutorial/tutorial_general_03.png
[4]: ./media/datahug-tutorial/tutorial_general_04.png

[100]: ./media/datahug-tutorial/tutorial_general_100.png

[200]: ./media/datahug-tutorial/tutorial_general_200.png
[201]: ./media/datahug-tutorial/tutorial_general_201.png
[202]: ./media/datahug-tutorial/tutorial_general_202.png
[203]: ./media/datahug-tutorial/tutorial_general_203.png

