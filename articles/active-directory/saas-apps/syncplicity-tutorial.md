---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Syncplicity |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Syncplicity között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3df48fd42ca998e2a64f2fbe685047da16a8bae7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193482"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Syncplicity

Ebben az oktatóanyagban elsajátíthatja, hogyan Syncplicity integrálása az Azure Active Directory (Azure AD).

Syncplicity integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Syncplicity Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Syncplicity (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Syncplicity az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Syncplicity egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Syncplicity hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity hozzáadása a katalógusból
Az Azure AD integrálása a Syncplicity konfigurálásához hozzá kell Syncplicity a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Syncplicity hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Syncplicity**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. Az eredmények panelen válassza ki a **Syncplicity**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Syncplicity az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Syncplicity mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Syncplicity hivatkozás kapcsolata kell létrehozni.

Syncplicity, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Syncplicity tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Syncplicity tesztfelhasználó létrehozása](#creating-a-syncplicity-test-user)**  – egy megfelelője a Britta Simon Syncplicity, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Syncplicity alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Syncplicity** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Az a **Syncplicity tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.syncplicity.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Syncplicity ügyfél-támogatási csapatának](https://www.syncplicity.com/contact-us) beolvasni ezeket az értékeket. 
 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Az a **Syncplicity konfigurációs** területén kattintson **konfigurálása Syncplicity** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Jelentkezzen be a **Syncplicity** bérlő.

1. A felső menüben kattintson **rendszergazdai**válassza **beállítások**, és kattintson a **egyéni tartomány és az egyszeri bejelentkezés**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Az a **egyszeri bejelentkezéses (SSO)** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés \(egyszeri bejelentkezés\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. Az a **Custom Domain** szövegmezőbe írja be annak a tartománynak a nevét.
  
    b. Válassza ki **engedélyezve** , **egyszeri bejelentkezés állapotát**.

    c. Az a **entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.

    d. Az a **lap URL-címe be** szövegmezőjébe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    e. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** Azure Portalról másolt.

    f. A **szolgáltató Identitástanúsítványt**, kattintson a **fájl kiválasztása**, majd töltse fel a tanúsítványt, amely az Azure Portalról letöltött. 

    g. Kattintson a **MENTSE a MÓDOSÍTÁSOKAT**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-syncplicity-test-user"></a>Syncplicity tesztfelhasználó létrehozása
AAD-felhasználók számára jelentkezhetnek be akkor ki kell építeni Syncplicity alkalmazáshoz. Ez a szakasz ismerteti a Syncplicity az AAD felhasználói fiókokat hozhat létre.

**Üzembe helyez egy felhasználói fiókot Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Syncplicity** bérlő (például: `https://company.Syncplicity.com`).

1. Kattintson a **rendszergazdai** válassza **felhasználói fiókok**.

1. Kattintson a **felhasználó hozzáadása**.
   
    ![Felhasználók kezelése](./media/syncplicity-tutorial/ic769764.png "felhasználók kezelése")

1. Írja be a **E-mail állomásról** egy AAD-fiók kíván üzembe helyezni, válassza ki a **felhasználói** , **szerepkör**, és kattintson a **tovább**.
   
    ![Fiókadatok](./media/syncplicity-tutorial/ic769765.png "fiókadatok")
   
    >[!NOTE]
    >Az AAD fióktulajdonos beolvasása egy e-mailt és a telepítőre mutató erősítse meg, és a fiók aktiválásához. 
    > 

1. Válasszon ki egy csoportot, amely az új felhasználót kell tagjává válik, és kattintson a vállalata **tovább**.
   
    ![Csoporttagság](./media/syncplicity-tutorial/ic769772.png "csoporttagság")
   
    >[!NOTE]
    >Ha nincsenek felsorolva csoportok, kattintson a **tovább**. 
    > 

1. Válassza ki a felhasználó számítógépén Syncplicity a vezérlő alá helyezni, és kattintson a kívánt mappákat **tovább**.
   
    ![Syncplicity mappák](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappák")

>[!NOTE]
>Bármely más Syncplicity felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Syncplicity által biztosított API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Syncplicity Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Syncplicity**.

    ![Egyszeri bejelentkezés konfigurálása](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Syncplicity csempére kattint, meg kell lekérése automatikusan bejelentkezett az Syncplicity alkalmazáshoz.
## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

