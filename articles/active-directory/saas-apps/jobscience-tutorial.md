---
title: 'Oktatóanyag: Azure Active Directory-integráció az Jobscience |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Jobscience között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 92ff93f9836b1ab8157602569c8171f81b976d6f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422671"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Oktatóanyag: Azure Active Directory-integráció az Jobscience

Ebben az oktatóanyagban elsajátíthatja, hogyan Jobscience integrálása az Azure Active Directory (Azure AD).

Jobscience integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Jobscience Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Jobscience (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Jobscience az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Jobscience egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Jobscience hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience hozzáadása a katalógusból
Az Azure AD integrálása a Jobscience konfigurálásához hozzá kell Jobscience a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Jobscience hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Jobscience**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Az eredmények panelen válassza ki a **Jobscience**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Jobscience az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Jobscience mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Jobscience hivatkozás kapcsolata kell létrehozni.

Jobscience, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Jobscience tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Jobscience tesztfelhasználó létrehozása](#creating-a-jobscience-test-user)**  – egy megfelelője a Britta Simon Jobscience, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Jobscience alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Jobscience, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Jobscience** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Az a **Jobscience tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Ez az érték lekéréséhez [Jobscience ügyfél-támogatási csapatának](https://www.jobscience.com/support) vagy az SSO-profilból hoz létre amely kifejtett az oktatóanyag későbbi részében. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_general_400.png)

1. Az a **Jobscience konfigurációs** területén kattintson **konfigurálása Jobscience** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Jelentkezzen be rendszergazdaként a Jobscience vállalati webhely.

1. Lépjen a **telepítő**.
   
   ![A telepítő](./media/jobscience-tutorial/IC784358.png "beállítása")

1. A bal oldali navigációs ablaktáblán a a **Administer** területén kattintson **tartományok kezelése** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány** megnyitásához a **Saját tartomány** lapot. 
   
   ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "saját tartomány")

1. Győződjön meg arról, hogy a tartomány megfelelően van beállítva, győződjön meg arról, hogy "**4. lépés telepíti a felhasználók számára**", és tekintse át a "**beállításaim tartomány**".

    ![Tartományi felhasználó](./media/jobscience-tutorial/ic784377.png "tartományi felhasználóra")

1. Kattintson a Jobscience vállalati webhely **biztonsági vezérlők**, és kattintson a **egyszeri bejelentkezési beállításainak**.
    
    ![Biztonsági vezérlők](./media/jobscience-tutorial/ic784364.png "biztonsági vezérlőket")

1. Az a **egyszeri bejelentkezési beállításainak** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés beállításai](./media/jobscience-tutorial/ic781026.png "egyszeri bejelentkezés beállításai")
    
    a. Válassza ki **SAML engedélyezett**.

    b. Kattintson az **Új** lehetőségre.

1. Az a **SAML egyszeri bejelentkezési beállítás szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
    
    ![SAML egyszeri bejelentkezési beállítás](./media/jobscience-tutorial/ic784365.png "SAML egyszeri bejelentkezési beállítás")
    
    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét.

    b. A **kibocsátó** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító**, Azure Portalról másolt.

    c. Az a **entitásazonosító** szövegmezőbe írja be `https://salesforce-jobscience.com`

    d. Kattintson a **Tallózás** az Azure AD-tanúsítvány feltöltéséhez.

    e. Mint **SAML identitástípus**válassza **helyességi feltétel tartalmazza a felhasználói objektum összevonási Azonosítóját**.

    f. Mint **SAML identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentfier elemében van**.

    g. A **Identity Provider bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    h. A **Identity Provider kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, Azure Portalról másolt.

    i. Kattintson a **Save** (Mentés) gombra.

1. A bal oldali navigációs ablaktáblán a a **Administer** területén kattintson **tartományok kezelése** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány** megnyitásához a **Saját tartomány** lapot. 
    
    ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "saját tartomány")

1. Az a **saját tartomány** lap a **bejelentkezési oldal márkajelzési** területén kattintson **szerkesztése**.
    
    ![Bejelentkezési oldal márkajelzési](./media/jobscience-tutorial/ic767826.png "arculat megjelenítése a bejelentkezési oldal")

1. Az a **bejelentkezési oldal márkajelzési** lap a **hitelesítési szolgáltatás** részben, a neve a **SAML egyszeri bejelentkezési beállításainak** jelenik meg. Válassza ki, és kattintson a **mentése**.
    
    ![Bejelentkezési oldal márkajelzési](./media/jobscience-tutorial/ic784366.png "arculat megjelenítése a bejelentkezési oldal")

1. Beolvasni az SP által kezdeményezett egyszeri bejelentkezést a bejelentkezési URL-cím kattintson a a **egyszeri bejelentkezés beállításainak** a a **biztonsági vezérlők** menü szakaszban.

    ![Biztonsági vezérlők](./media/jobscience-tutorial/ic784368.png "biztonsági vezérlőket")
    
    Kattintson a fenti lépésben létrehozott SSO-profilra. A vállalat ezen a lapon látható az URL-cím az egyszeri bejelentkezéshez (például [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Jobscience jelentkezzen be, akkor ki kell építeni Jobscience be. Jobscience, esetén kiépítése a manuális feladat.

>[!NOTE]
>Bármely más Jobscience felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított Jobscience üzembe helyezni az Azure Active Directory felhasználói fiókokat.
>  
        
**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Jobscience** rendszergazdaként a vállalati webhely.

1. Nyissa meg a telepítő.
   
   ![A telepítő](./media/jobscience-tutorial/ic784358.png "beállítása")
1. Lépjen a **felhasználók kezelése \> felhasználók**.
   
   ![Felhasználók](./media/jobscience-tutorial/ic784369.png "felhasználók")
1. Kattintson a **új felhasználó**.
   
   ![Minden felhasználó](./media/jobscience-tutorial/ic784370.png "minden felhasználó")
1. Az a **felhasználó szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Felhasználó szerkesztése](./media/jobscience-tutorial/ic784371.png "felhasználó szerkesztése")
   
   a. Az a **Utónév** szövegmezőbe Britta például a felhasználó utónevét adja.
   
   b. Az a **Vezetéknév** szövegmezőbe írja be például a Simon a felhasználó vezetékneve.
   
   c. Az a **Alias** szövegmezőbe írja be a felhasználó például brittas aliasnevet.

   d. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

   e. Az a **felhasználónév** beviteli mező, írja be a felhasználónevet a felhasználó például Brittasimon@contoso.com.

   f. Az a **becenév** szövegmezőbe írja be a felhasználó például Simon nick nevét.

   g. Kattintson a **Save** (Mentés) gombra.

    
> [!NOTE]
> Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Jobscience Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Jobscience, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Jobscience**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Jobscience csempére kattint, meg kell lekérése automatikusan bejelentkezett az Jobscience alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

