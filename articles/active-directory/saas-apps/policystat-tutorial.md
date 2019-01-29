---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező PolicyStat |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és PolicyStat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 456d66b87f626391f6e81ffd7be244381f0978b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155986"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező PolicyStat

Ebben az oktatóanyagban elsajátíthatja, hogyan PolicyStat integrálása az Azure Active Directory (Azure AD).

PolicyStat integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá PolicyStat Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett PolicyStat (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

PolicyStat az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy PolicyStat egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. PolicyStat hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-policystat-from-the-gallery"></a>PolicyStat hozzáadása a katalógusból
Az Azure AD integrálása a PolicyStat konfigurálásához hozzá kell PolicyStat a katalógusból a felügyelt SaaS-alkalmazások listájára.

**PolicyStat hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **PolicyStat**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/tutorial_policystat_search.png)

1. Az eredmények panelen válassza ki a **PolicyStat**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés PolicyStat a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó PolicyStat mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó PolicyStat hivatkozás kapcsolata kell létrehozni.

PolicyStat, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az PolicyStat tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[PolicyStat tesztfelhasználó létrehozása](#creating-a-policystat-test-user)**  – egy megfelelője a Britta Simon PolicyStat, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és PolicyStat alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés PolicyStat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **PolicyStat** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. Az a **PolicyStat tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.policystat.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [PolicyStat ügyfél-támogatási csapatának](http://www.policystat.com/support/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. Ez a szakasz célja kidolgozására engedélyezése a felhasználóknak a hitelesítést PolicyStat fiókkal az Azure AD összevonási SAML protokoll használatával.

    A PolicyStat alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amelyhez egyéni attribútumleképezések való hozzáadása a **SAML-jogkivonat attribútumai** konfigurációja.  

     Az alábbi képernyőképen Ez egy példát mutat be.

     ![Attribútumok](./media/policystat-tutorial/tutorial_policystat_attribute.png "attribútumok")

1. Adja hozzá a szükséges attribútumleképezések, hajtsa végre az alábbi lépéseket:

    | Attribútum neve    |   Hodnota atributu |
    |------------------- | -------------------- |
    | egyedi azonosítója | ExtractMailPrefix([mail]) |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. Az a **attribútumnév** szövegmezőbe írja be **uid**.

    c. Az a **attribútumérték** szövegmezőben válassza **ExtractMailPrefix()**.    
   
    d. Az a **Mail** listáról válassza ki **User.mail**.
    
    e. Kattintson a **Ok**

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a PolicyStat vállalati hely rendszergazdaként.

1. Kattintson a **rendszergazdai** fülre, majd **egyszeri bejelentkezési konfigurációjának** bal oldali navigációs ablaktáblán.
   
    ![Rendszergazda menü](./media/policystat-tutorial/ic808633.png "Rendszergazda menü")

1. Az a **telepítő** szakaszban jelölje be **engedélyezése egyetlen bejelentkezési integrációs**.
   
    ![Egyszeri bejelentkezés beállításainak](./media/policystat-tutorial/ic808634.png "egyszeri bejelentkezés konfigurálása")

1. Kattintson a **attribútumok konfigurálása**, majd a **attribútumok konfigurálása** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés beállításainak](./media/policystat-tutorial/ic808635.png "egyszeri bejelentkezés konfigurálása")
   
    a. Az a **felhasználónév attribútum** szövegmezőbe írja be **uid**.

    b. Az a **First Name attribútum** szövegmezőbe írja be **firstname** felhasználó **Britta**.

    c. Az a **utolsó név attribútum** szövegmezőbe írja be **lastname** felhasználó **Simon**.

    d. Az a **E-mail attribútum** szövegmezőbe írja be **emailaddress** felhasználó **BrittaSimon@contoso.com**.

    e. Kattintson a **módosítások mentése**.

1. Kattintson a **az Identitásszolgáltató metaadatok**, majd a **az Identitásszolgáltató metaadatok** szakaszban, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés beállításainak](./media/policystat-tutorial/ic808636.png "egyszeri bejelentkezés konfigurálása")
   
    a. Nyissa meg a letöltött metaadatait tartalmazó fájl, a tartalmat másolja és illessze be azt a **az Identity Provider metaadatok** szövegmezőbe.

    b. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-policystat-test-user"></a>PolicyStat tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók PolicyStat szolgáltatásba való bejelentkezéshez, akkor ki kell építeni PolicyStat be.  

PolicyStat csak idő felhasználókiépítés támogatja. Ez azt jelenti, hogy nem kell manuálisan adja hozzá a felhasználók PolicyStat. A felhasználók automatikusan hozzáadják fogja az egyszeri bejelentkezés révén az első bejelentkezésnél.

>[!NOTE]
>Eszközt is használhat bármilyen más PolicyStat felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése PolicyStat által biztosított API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PolicyStat Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel PolicyStat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **PolicyStat**.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a PolicyStat csempére kattint, meg kell lekérése automatikusan bejelentkezett az PolicyStat alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

