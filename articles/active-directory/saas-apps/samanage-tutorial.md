---
title: 'Oktatóanyag: Azure Active Directory-integráció az Samanage |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Samanage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 118ab72c9afc13c5792f229f9c7bc61d226553d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420574"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Oktatóanyag: Azure Active Directory-integráció az Samanage

Ebben az oktatóanyagban elsajátíthatja, hogyan Samanage integrálása az Azure Active Directory (Azure AD).

Samanage integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Samanage Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Samanage (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Samanage az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Samanage egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Samanage hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-samanage-from-the-gallery"></a>Samanage hozzáadása a katalógusból
Az Azure AD integrálása a Samanage konfigurálásához hozzá kell Samanage a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Samanage hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Samanage**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_search.png)

1. Az eredmények panelen válassza ki a **Samanage**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Samanage a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Samanage mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Samanage hivatkozás kapcsolata kell létrehozni.

Samanage, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Samanage tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Samanage tesztfelhasználó létrehozása](#creating-a-samanage-test-user)**  – egy megfelelője a Britta Simon Samanage, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Samanage alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Samanage, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Samanage** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

1. Az a **Samanage tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges bejelentkezési URL-és az azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében. További részletekért forduljon [Samanage ügyfél-támogatási csapatának](https://www.samanage.com/support).    
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_general_400.png)

1. Az a **Samanage konfigurációs** területén kattintson **konfigurálása Samanage** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT, és a SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Samanage vállalati hely rendszergazdaként.

1. Kattintson a **irányítópult** válassza **telepítő** bal oldali navigációs ablaktáblán.
   
    ![Irányítópult](./media/samanage-tutorial/tutorial_samanage_001.png "irányítópult")

1. Kattintson a **egyszeri bejelentkezési**.
   
    ![Egyszeri bejelentkezés](./media/samanage-tutorial/tutorial_samanage_002.png "egyszeri bejelentkezés")

1. Navigáljon a **bejelentkezési SAML használatával** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Bejelentkezési SAML használatával](./media/samanage-tutorial/tutorial_samanage_003.png "bejelentkezési SAML használatával")
 
    a. Kattintson a **engedélyezze az egyszeri bejelentkezési SAML-**.  
 
    b. Az a **identitásszolgáltató szolgáltató URL-címe** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.    
 
    c. Erősítse meg a **bejelentkezési URL-cím** megegyezik a **bejelentkezési URL-** , **Samanage tartomány és URL-címek** szakaszban az Azure Portalon.
 
    d. Az a **kijelentkezési URL-címe** szövegmezőbe írja be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.
 
    e. Az a **SAML kibocsátó** beviteli mező, írja be az alkalmazásazonosító URI-t állítsa be az identitásszolgáltató a.
 
    f. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben az Azure portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **illessze be az identitásszolgáltató x.509 tanúsítvány, az alábbi** szövegmezőbe.
 
    g. Kattintson a **felhasználók létrehozása, ha azok nem léteznek Samanage**.
 
    h. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-samanage-test-user"></a>Samanage tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Samanage jelentkezzen be, akkor ki kell építeni Samanage be.  
Samanage, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Samanage vállalati hely rendszergazdaként.

1. Kattintson a **irányítópult** válassza **telepítő** a bal oldali navigációs ablakban.
   
    ![A telepítő](./media/samanage-tutorial/tutorial_samanage_001.png "beállítása")

1. Kattintson a **felhasználók** lap
   
    ![Felhasználók](./media/samanage-tutorial/tutorial_samanage_006.png "felhasználók")

1. Kattintson a **új felhasználó**.
   
    ![Új felhasználó](./media/samanage-tutorial/tutorial_samanage_007.png "új felhasználó")

1. Írja be a **neve** és a **E-mail cím** egy Azure Active Directory-fiók kiépítése, és kattintson a kívánt **felhasználó létrehozása**.
   
    ![Felhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_008.png "felhasználó létrehozása")
   
   >[!NOTE]
   >Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik. Bármely más Samanage felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított Samanage üzembe helyezni az Azure Active Directory felhasználói fiókokat.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Samanage Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Samanage, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Samanage**.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Samanage csempére kattint, meg kell lekérése automatikusan bejelentkezett az Samanage alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

