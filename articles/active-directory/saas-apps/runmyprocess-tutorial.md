---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező RunMyProcess |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és RunMyProcess között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 7e7148b178b2fbbb2d2009889b1a80b56f3a1152
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807898"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező RunMyProcess

Ebben az oktatóanyagban elsajátíthatja, hogyan RunMyProcess integrálása az Azure Active Directory (Azure AD).

RunMyProcess integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá RunMyProcess Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett RunMyProcess (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

RunMyProcess az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy RunMyProcess egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség:[próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. RunMyProcess hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-runmyprocess-from-the-gallery"></a>RunMyProcess hozzáadása a katalógusból
Az Azure AD integrálása a RunMyProcess konfigurálásához hozzá kell RunMyProcess a katalógusból a felügyelt SaaS-alkalmazások listájára.

**RunMyProcess hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **RunMyProcess**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. Az eredmények panelen válassza ki a **RunMyProcess**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés RunMyProcess a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó RunMyProcess mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó RunMyProcess hivatkozás kapcsolata kell létrehozni.

RunMyProcess, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az RunMyProcess tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[RunMyProcess tesztfelhasználó létrehozása](#creating-a-runmyprocess-test-user)**  – egy megfelelője a Britta Simon RunMyProcess, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és RunMyProcess alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés RunMyProcess, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **RunMyProcess** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. Az a **RunMyProcess tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [RunMyProcess ügyfél-támogatási csapatának](mailto:support@runmyprocess.com) a gépkulcsengedélyek értékének. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. Az a **RunMyProcess konfigurációs** területén kattintson **konfigurálása RunMyProcess** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. Egy másik böngészőablakban, a bejelentkezés a RunMyProcess bérlői rendszergazdaként.

1. A bal oldali navigációs panelen kattintson a **fiók** válassza **konfigurációs**.
   
    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Lépjen a **hitelesítési módszer** szakaszt, és hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Mint **metódus**válassza **Samlv2 az SSO**. 

    b. Az a **egyszeri bejelentkezés átirányítási** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    c. Az a **kijelentkezési átirányítási** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    d. Az a **Névazonosító formátuma** szövegmezőbe írja be az értékét **azonosító formátuma** , **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.

    e. Másolja a letöltött tanúsítvány-fájl tartalmát, és illessze be azt a **tanúsítvány** szövegmezőbe. 
 
    f. Kattintson a **mentése** ikonra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-runmyprocess-test-user"></a>RunMyProcess tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók RunMyProcess jelentkezzen be, akkor ki kell építeni RunMyProcess be. RunMyProcess, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a RunMyProcess vállalati webhely.

1. Kattintson a **fiók** válassza **felhasználók** a bal oldali navigációs panelen, majd kattintson **új felhasználó**.
   
    ![Új felhasználó](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "új felhasználó")

1. Az a **felhasználói beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "profil") 
  
    a. Írja be a **neve** és **E-mail** egy érvényes Azure AD-fiókot, a kapcsolódó szövegmezőkben kiépítendő. 

    b. Válasszon egy **IDE nyelvi**, **nyelvi**, és **profil**. 

    c. Válassza ki **fiók létrehozása e-mailt küldjön számomra**. 

    d. Kattintson a **Save** (Mentés) gombra.
   
    >[!NOTE]
    >Bármely más RunMyProcess felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított RunMyProcess üzembe helyezni az Azure Active Directory felhasználói fiókokat. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés RunMyProcess Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel RunMyProcess, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **RunMyProcess**.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a RunMyProcess csempére kattint, meg kell lekérése automatikusan bejelentkezett az RunMyProcess alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

