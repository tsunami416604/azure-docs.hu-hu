---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Picturepark |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Picturepark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b458632ddb6e23ab1ddf642752aa0cbfc11f1ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839068"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Picturepark

Ebben az oktatóanyagban elsajátíthatja, hogyan Picturepark integrálása az Azure Active Directory (Azure AD).

Picturepark integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Picturepark Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Picturepark (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Picturepark az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Picturepark egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Picturepark hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-picturepark-from-the-gallery"></a>Picturepark hozzáadása a katalógusból
Az Azure AD integrálása a Picturepark konfigurálásához hozzá kell Picturepark a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Picturepark hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Picturepark**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. Az eredmények panelen válassza ki a **Picturepark**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Picturepark a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Picturepark mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Picturepark hivatkozás kapcsolata kell létrehozni.

Picturepark, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Picturepark tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Picturepark tesztfelhasználó létrehozása](#creating-a-picturepark-test-user)**  – egy megfelelője a Britta Simon Picturepark, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Picturepark alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Picturepark, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Picturepark** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. Az a **Picturepark tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.picturepark.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Picturepark ügyfél-támogatási csapatának](https://picturepark.com/about/contact/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_general_400.png)

1. Az a **Picturepark konfigurációs** területén kattintson **konfigurálása Picturepark** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Picturepark vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felügyeleti konzol**.
   
    ![Felügyeleti konzol](./media/picturepark-tutorial/ic795062.png "felügyeleti konzol")

1. Kattintson a **hitelesítési**, és kattintson a **Identitásszolgáltatók**.
   
    ![Hitelesítési](./media/picturepark-tutorial/ic795063.png "hitelesítés")

1. Az a **identitás-szolgáltató konfigurációjának** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Identitás-szolgáltató konfigurációjának](./media/picturepark-tutorial/ic795064.png "identitás szolgáltató konfigurációja")
   
    a. Kattintson a **Hozzáadás** parancsra.
  
    b. Adjon meg egy nevet a konfigurációnak.
   
    c. Válassza ki **alapértelmezett**.
   
    d. A **kibocsátó URI** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-** Azure Portalról másolt.
   
    e. A **megbízható kiállító ujjlenyomata** szövegmező, illessze be az értéket a **ujjlenyomat** másolta amely **SAML-aláíró tanúsítvány** szakaszban. 

1. Kattintson a **JoinDefaultUsersGroup**.

1. Beállítása a **Emailaddress** az attribútum a **jogcím** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` kattintson **mentése**.

      ![Konfigurációs](./media/picturepark-tutorial/ic795065.png "konfiguráció")

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-picturepark-test-user"></a>Picturepark tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Picturepark szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Picturepark be. Picturepark, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Picturepark** bérlő.

1. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felhasználók**.
   
    ![Felhasználók](./media/picturepark-tutorial/ic795067.png "felhasználók")

1. Az a **felhasználók áttekintése** lapra, majd **új**.
   
    ![Felhasználókezelés](./media/picturepark-tutorial/ic795068.png "felhasználók kezelése")

1. Az a **Create User** párbeszédpanelen a következő lépésekkel, egy érvényes Azure Active Directory-felhasználó szeretne üzembe helyezni:
   
    ![Felhasználó létrehozása](./media/picturepark-tutorial/ic795069.png "felhasználó létrehozása")
   
    a. Az a **E-mail cím** szövegmezőbe írja be a **e-mail-cím** felhasználó **BrittaSimon\@contoso.com**.  
   
    b. Az a **jelszó** és **jelszó megerősítése** szövegmezőből, írja be a **jelszó** BrittaSimon az. 
   
    c. Az a **Utónév** szövegmezőbe írja be a **Utónév** felhasználó **Britta**. 
   
    d. Az a **Vezetéknév** szövegmezőbe írja be a **Vezetéknév** felhasználó **Simon**.
   
    e. Az a **vállalati** szövegmezőbe írja be a **cégnév** felhasználó. 
   
    f. Az a **ország** szövegmezőbe, válassza ki a **ország** felhasználó.
  
    g. Az a **ZIP** szövegmezőbe írja be a **irányítószám** az városa.
   
    h. Az a **Város** szövegmezőbe írja be a **városnév** felhasználó.

    i. Válassza ki a **nyelvi**.
   
    j. Kattintson a **Create** (Létrehozás) gombra.

>[!NOTE]
>Eszközt is használhat bármilyen más Picturepark felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Picturepark által biztosított API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Picturepark Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Picturepark, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Picturepark**.

    ![Egyszeri bejelentkezés konfigurálása](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Picturepark csempére kattint, meg kell lekérése automatikusan bejelentkezett az Picturepark alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

