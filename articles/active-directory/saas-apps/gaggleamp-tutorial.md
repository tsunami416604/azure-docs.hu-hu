---
title: 'Oktatóanyag: Azure Active Directory-integráció az GaggleAMP |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és GaggleAMP között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9cc1a4b7-964b-406b-9e0c-05cb1a7c9856
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: jeedes
ms.openlocfilehash: 828dd1e1dcef900a7105143088f6782032b4f22e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436512"
---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Oktatóanyag: Azure Active Directory-integráció az GaggleAMP

Ebben az oktatóanyagban elsajátíthatja, hogyan GaggleAMP integrálása az Azure Active Directory (Azure AD).

GaggleAMP integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá GaggleAMP Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett GaggleAMP (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

GaggleAMP az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy GaggleAMP egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. GaggleAMP hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-gaggleamp-from-the-gallery"></a>GaggleAMP hozzáadása a katalógusból
Az Azure AD integrálása a GaggleAMP konfigurálásához hozzá kell GaggleAMP a katalógusból a felügyelt SaaS-alkalmazások listájára.

**GaggleAMP hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **GaggleAMP**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/tutorial_gaggleamp_search.png)

1. Az eredmények panelen válassza ki a **GaggleAMP**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/tutorial_gaggleamp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés GaggleAMP a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó GaggleAMP mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó GaggleAMP hivatkozás kapcsolata kell létrehozni.

GaggleAMP, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az GaggleAMP tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[GaggleAMP tesztfelhasználó létrehozása](#creating-a-gaggleamp-test-user)**  – egy megfelelője a Britta Simon GaggleAMP, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és GaggleAMP alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés GaggleAMP, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **GaggleAMP** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_samlbase.png)

1. Az a **GaggleAMP tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_url.png)

     Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://accounts.gaggleamp.com/auth/saml/callback`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_url1.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://gaggleamp.com/i/<customerid>`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [GaggleAMP ügyfél-támogatási csapatának](mailto:sales@gaggleamp.com) lekérni ezt az értéket.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_general_400.png)

1. Az a **GaggleAMP konfigurációs** területén kattintson **konfigurálása GaggleAMP** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_configure.png) 

1. Egy másik böngészőben példányában, nyissa meg a SAML SSO lapot az Ön által a Gaggle támogatási csoportjának létrehozott (például: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).

1. Az a **SAML SSO** lapon, a következő lépésekkel:  
   
    ![GaggleAMP Single Sign-On](./media/gaggleamp-tutorial/tutorial_gaggleamp_06.png)

    a. Válassza ki **más** űrlap a **identitásszolgáltató** legördülő menüben.
    
    b. Az a **identitásszolgáltató kibocsátója** szövegmezőjébe illessze be az értéket, **kiállítójának URL-címe** Azure Portalról másolt.
    
    c. Az a **identitás szolgáltató egyszeri bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
    
    d. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
    
    e. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/gaggleamp-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-gaggleamp-test-user"></a>GaggleAMP tesztfelhasználó létrehozása

Ez a szakasz célja GaggleAMP Britta Simon nevű felhasználó létrehozásához. GaggleAMP támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az GaggleAMP elérésére, ha még nem létezik tett kísérlet során. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés GaggleAMP Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel GaggleAMP, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **GaggleAMP**.

    ![Egyszeri bejelentkezés konfigurálása](./media/gaggleamp-tutorial/tutorial_gaggleamp_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a GaggleAMP csempére kattint, meg kell lekérése automatikusan bejelentkezett az GaggleAMP alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/gaggleamp-tutorial/tutorial_general_04.png

[100]: ./media/gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/gaggleamp-tutorial/tutorial_general_201.png
[202]: ./media/gaggleamp-tutorial/tutorial_general_202.png
[203]: ./media/gaggleamp-tutorial/tutorial_general_203.png
