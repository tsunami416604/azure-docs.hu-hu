---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Rightscale |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Rightscale között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: cda9af9b732643300b8f212471beff066f47b6b3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827363"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Oktatóanyag: Az Azure Active Directory-integráció a Rightscale

Ebben az oktatóanyagban elsajátíthatja, hogyan Rightscale integrálása az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Rightscale nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Rightscale Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Rightscale (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Rightscale, a következőkre van szükség:

- Azure AD-előfizetés
- A Rightscale egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Rightscale hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-rightscale-from-the-gallery"></a>Rightscale hozzáadása a katalógusból
Az Azure AD integrálása a Rightscale konfigurálásához hozzá kell Rightscale a galériából a felügyelt SaaS-alkalmazások listájára.

**Rightscale hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Rightscale**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. Az eredmények panelen válassza ki a **Rightscale**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Rightscale a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Rightscale mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Rightscale hivatkozás kapcsolatát kell létrehozni.

Rightscale, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Rightscale tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Rightscale tesztfelhasználó létrehozása](#creating-a-rightscale-test-user)**  – egy megfelelője a Britta Simon Rightscale, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Rightscale alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Rightscale, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Rightscale** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. Az a **Rightscale tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód** nem rendelkezik olyan lépéseket végrehajtani, mert az alkalmazás már előre integrált, az Azure-ral.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. Az a **Rightscale tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Kattintson a **speciális URL-beállítások megjelenítése**.

    b. Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://login.rightscale.com/`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_general_400.png)

1. Az a **Rightscale konfigurációs** területén kattintson **konfigurálása Rightscale** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált lekéréséhez kell bejelentkezést a RightScale bérlői rendszergazdaként az.

    a. A felső menüben kattintson a **beállítások** lapot, és válasszon **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Kattintson a "**új**" gombra kattintva adhat hozzá **az SAML-Identitásszolgáltatók**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. A szövegmezőben, **megjelenítendő név**, adjon meg a cég nevét.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Válassza ki **engedélyezése RightScale által kezdeményezett egyszeri Bejelentkezést használ egy észlelési végrehajtási módosító** bemeneti és a **tartománynév** a a szövegmező alatt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt **SAML egyszeri bejelentkezési végpont** a RightScale.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Illessze be az értéket a **SAML Entitásazonosító** az Azure Portalról másolt **SAML EntityID** a RightScale.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Kattintson a **böngésző** gombra kattintva töltse fel a tanúsítványt, amely az Azure Portalról letöltött.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Kattintson a **Save** (Mentés) gombra.
<CE>
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-rightscale-test-user"></a>Rightscale tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű RightScale hoz létre. Együttműködve [Rightscale ügyfél-támogatási csapatának](mailto:support@rightscale.com) a felhasználók hozzáadása a RightScale platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Rightscale Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Rightscale, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Rightscale**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.  

Ha a hozzáférési panelen a RightScale csempére kattint, akkor kell lekérése automatikusan bejelentkezett a RightScale alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

