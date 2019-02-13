---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező RFPIO |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és RFPIO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af5c3d5d92dea804221b2285d4fa5b9cedae665
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168090"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező RFPIO

Ebben az oktatóanyagban elsajátíthatja, hogyan RFPIO integrálása az Azure Active Directory (Azure AD).

RFPIO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Akik szabályozhatja, ki férhet hozzá RFPIO Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett RFPIO (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

RFPIO az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetés.
- Egy RFPIO sign-a-engedélyezett előfizetéssel.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez használhatja a termelési környezetben nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. RFPIO hozzáadása a katalógusból.
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés.

## <a name="add-rfpio-from-the-gallery"></a>RFPIO hozzáadása a katalógusból
Az Azure AD integrálása a RFPIO konfigurálásához hozzá kell RFPIO a katalógusból a felügyelt SaaS-alkalmazások listájára.

### <a name="to-add-rfpio-from-the-gallery"></a>A katalógus RFPIO adhat hozzá

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Válassza ki **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **RFPIO**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. Az eredmények panelen válassza ki a **RFPIO**, majd válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés RFPIO a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, mi a kapcsolat RFPIO tartozó felhasználó, és a egy felhasználó Azure AD között van. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó RFPIO hivatkozás kapcsolata kell létrehozni.

RFPIO, rendelje hozzá a értékét **felhasználónév** értékeként az Azure AD-ben **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az RFPIO tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **Az Azure AD egyszeri bejelentkezés konfigurálása**– ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **Hozzon létre egy Azure ad-ben tesztfelhasználót**– az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **Hozzon létre egy RFPIO tesztfelhasználót** --van egy megfelelője a Britta Simon RFPIO, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **Rendelje hozzá az Azure ad-ben tesztfelhasználó**--Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **Egyszeri bejelentkezés tesztelése** --működik, ha a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és RFPIO alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés RFPIO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **RFPIO** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. Az a **RFPIO tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://www.rfpio.com`

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Ellenőrizze **speciális URL-beállítások megjelenítése**.

    c. Az a **továbbítási állapot** szövegmezőbe írja be a karakterlánc-érték. Kapcsolattartó [RFPIO támogatási csoportjának](https://www.rfpio.com/contact/) lekérni ezt az értéket. 

1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód: 

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    Az a **bejelentkezési URL-cím** szövegmezőbe írja be az URL-cím: `https://www.app.rfpio.com`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban, jelentkezzen be a **RFPIO** webhely rendszergazdaként.

1. Kattintson az alsó bal felső sarokban található legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app1.png)

1. Kattintson a **szervezeti beállítások**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app2.png)

1. Kattintson a **funkciók és integrációs**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app4.png)

1. Az a **SAML SSO-konfiguráció** kattintson **szerkesztése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app3.png)

1. Ebben a szakaszban végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app5.png)
    
    a. Másolja a tartalmat, a **letöltött metaadatainak XML** és illessze be azt a **identitás konfigurációs** mező.

    > [!NOTE]
    >Másolja a tartalmat a letöltött **metaadatainak XML** használata **Jegyzettömb ++** vagy megfelelő **XML-szerkesztő**. 

    b. Kattintson a **ellenőrzése**.

    c. Kattintás után **ellenőrzése**, tükrözés **SAML(Enabled)** beállítást.

    d. Kattintson a **Submit** (Küldés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-rfpio-test-user"></a>RFPIO tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók RFPIO jelentkezzen be, akkor ki kell építeni RFPIO be.  
RFPIO, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a RFPIO vállalati webhely.

1. Kattintson az alsó bal felső sarokban található legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app1.png)

1. Kattintson a **szervezeti beállítások**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app2.png)

1. Kattintson a **CSAPAT tagjai**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app6.png)

1. Kattintson a **tagok hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app7.png)

1. Az a **új tagok hozzáadása** szakaszban. Hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app8.png)

    a. Adjon meg **E-mail-cím** a a **adjon meg soronként egy e-mail** mező.

    b. Újra válassza **szerepkör** igényeinek megfelelően.

    c. Kattintson a **tagok hozzáadása**.
        
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés RFPIO Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel RFPIO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **RFPIO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha a hozzáférési panelen a RFPIO csempére kattint, meg kell lekérése automatikusan bejelentkezett az RFPIO alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

