---
title: 'Oktatóanyag: Azure Active Directory-integráció az EverBridge |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és EverBridge között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 283379131b02f4ea115052f051ef0114efab1997
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423191"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Azure Active Directory-integráció az EverBridge

Ebben az oktatóanyagban elsajátíthatja, hogyan EverBridge integrálása az Azure Active Directory (Azure AD).

EverBridge integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá EverBridge Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett EverBridge (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

EverBridge az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy EverBridge egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. EverBridge hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-everbridge-from-the-gallery"></a>EverBridge hozzáadása a katalógusból
Az Azure AD integrálása a EverBridge konfigurálásához hozzá kell EverBridge a katalógusból a felügyelt SaaS-alkalmazások listájára.

**EverBridge hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **EverBridge**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/tutorial_everbridge_search.png)

1. Az eredmények panelen válassza ki a **EverBridge**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés EverBridge a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó EverBridge mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó EverBridge hivatkozás kapcsolata kell létrehozni.

EverBridge, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az EverBridge tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy EverBridge tesztfelhasználó létrehozása](#creating-an-everbridge-test-user)**  – egy megfelelője a Britta Simon EverBridge, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és EverBridge alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés EverBridge, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **EverBridge** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_samlbase.png)

1. Az a **EverBridge tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sso.everbridge.net/<companyname>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://manager.everbridge.net/saml/SSO/<companyname>/alias/defaultAlias`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [EverBridge támogatási csapatának](mailto:support@everbridge.com) beolvasni ezeket az értékeket.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_general_400.png)

1. Az a **EverBridge konfigurációs** területén kattintson **konfigurálása EverBridge** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_configure.png) 

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált lekéréséhez kell az rendszergazdaként Everbridge bérlőben való bejelentkezést.

1. A felső menüben kattintson a **beállítások** lapot, és válasszon **egyszeri bejelentkezés** alatt **biztonsági**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. Az a **neve** szövegmezőbe írja be a szolgáltató azonosító neve (például: a vállalat neve).
   
    b. Az a **API neve** szövegmezőbe írja be az API neve.
   
    c. Kattintson a **fájl kiválasztása** gombra kattintva töltse fel a metaadat-fájlt, amely az Azure Portalról letöltött.
   
    d. Válassza ki a SAML-identitás helyen **identitás a tulajdonos utasítás NameIdentifier elemében van**.
   
    e. A a **Identity Provider bejelentkezési URL-cím** szövegmezőjébe illessze be az Azure AD SAML egyszeri bejelentkezési URL-cím értékét.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. Válassza ki a szolgáltató által kezdeményezett kérelem kötés, **HTTP-átirányítás**.

    g. Kattintson a **Mentés** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/everbridge-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-everbridge-test-user"></a>Egy EverBridge tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Everbridge hoz létre. Együttműködve [EverBridge támogatási csapatának](mailto:support@everbridge.com) a felhasználók hozzáadása az Everbridge platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés EverBridge Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel EverBridge, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **EverBridge**.

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Everbridge csempére kattint, meg kell lekérése automatikusan bejelentkezett az Everbridge alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/everbridge-tutorial/tutorial_general_01.png
[2]: ./media/everbridge-tutorial/tutorial_general_02.png
[3]: ./media/everbridge-tutorial/tutorial_general_03.png
[4]: ./media/everbridge-tutorial/tutorial_general_04.png

[100]: ./media/everbridge-tutorial/tutorial_general_100.png

[200]: ./media/everbridge-tutorial/tutorial_general_200.png
[201]: ./media/everbridge-tutorial/tutorial_general_201.png
[202]: ./media/everbridge-tutorial/tutorial_general_202.png
[203]: ./media/everbridge-tutorial/tutorial_general_203.png

