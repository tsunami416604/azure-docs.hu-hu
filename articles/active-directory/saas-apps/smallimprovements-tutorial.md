---
title: 'Oktatóanyag: Azure Active Directory-integráció kis javításait |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a kisebb fejlesztések között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 588e56c9ae22578c08dbca07c7c576fe8b577b58
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012334"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Oktatóanyag: Azure Active Directory-integráció az kis fejlesztései

Ebben az oktatóanyagban elsajátíthatja, hogyan kis fejlesztései integrálása az Azure Active Directory (Azure AD).

Kisebb fejlesztések integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a kisebb fejlesztések az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a kis fejlesztései (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Kisebb fejlesztések az Azure AD-integráció konfigurálása, a következőkre van szükség:

- Azure AD-előfizetés
- Egy kis fejlesztései egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Kisebb fejlesztések hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-small-improvements-from-the-gallery"></a>Kisebb fejlesztések hozzáadása a katalógusból
Kisebb fejlesztések integrálása az Azure AD beállítása, hozzá kell kis fejlesztései a galériából a felügyelt SaaS-alkalmazások listájára.

**Kisebb fejlesztések hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **kis fejlesztései**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. Az eredmények panelen válassza ki a **kis fejlesztései**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a teszt "Britta Simon" nevű felhasználó kis fejlesztései.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó kis fejlesztései a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó kis eredményez hivatkozás kapcsolata kell létrehozni.

Kisebb fejlesztések, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés kis javításait tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Kisebb fejlesztések tesztfelhasználó létrehozása](#creating-a-small-improvements-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon kis eredményez, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és kis fejlesztései alkalmazását az egyszeri bejelentkezés konfigurálása.

**Kisebb fejlesztések az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **kis fejlesztései** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. Az a **kisméretű fejlesztései tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.small-improvements.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [kis fejlesztései ügyfél-támogatási csapatának](mailto:support@small-improvements.com) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. Az a **kis fejlesztései konfigurációs** területén kattintson **kis fejlesztései konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a kisebb fejlesztések vállalati hely rendszergazdaként.

1. A fő irányítópult-oldalon, kattintson az **felügyeleti** a bal oldali gomb.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kattintson a **SAML SSO** gombra **Integrációk** szakaszban.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Az a **HTTP-végpontot** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    b. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **x509 tanúsítvány** szövegmezőbe. 

    c. Ha szeretne egyszeri Bejelentkezést és a bejelentkezési képernyőn hitelesítési lehetőséget a felhasználók számára elérhető rendelkezik, akkor ellenőrizze a **bejelentkezési/jelszó keresztüli hozzáférés engedélyezésére túl** lehetőséget.  

    d. Nevezze el az Egyszeri bejelentkezés gombra a megfelelő értéket a **SAML Rákérdezés** szövegmezőbe.  

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-small-improvements-test-user"></a>Kisebb fejlesztések tesztfelhasználó létrehozása

Kisebb fejlesztések jelentkezzen be az Azure AD-felhasználók engedélyezéséhez, ki kell építeni be kis fejlesztései. Kisebb fejlesztések esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a kisebb fejlesztések vállalati webhelyre rendszergazdaként.

1. A kezdőlapon nyissa meg a menüben kattintson a bal oldali, a **felügyeleti**.

1. Kattintson a **felhasználói címtárba** felhasználókezelés szakaszban gombra. 
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kattintson a **felhasználók hozzáadása**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket: 

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Adja meg a **Utónév** például a felhasználó **Britta**.

    b. Adja meg a **Vezetéknév** például a felhasználó **Simon**.

    c. Adja meg a **E-mail** például a felhasználó <strong>brittasimon@contoso.com</strong>. 

    d. Azt is beállíthatja, adja meg a személyes üzenet a **értesítő** mezőbe. Ha nem szeretne az értesítés elküldéséhez, akkor törölje ezt a jelölőnégyzetet.

    e. Kattintson a **felhasználók létrehozása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon hozzáférést biztosít a kisebb javítások által használandó Azure egyszeri bejelentkezést.

![Felhasználó hozzárendelése][200] 

**Kisebb fejlesztések Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **kis fejlesztései**.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.  

Ha a hozzáférési panelen a kisebb fejlesztések csempére kattint, meg kell lekérése automatikusan bejelentkezett a kisebb javítások alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

