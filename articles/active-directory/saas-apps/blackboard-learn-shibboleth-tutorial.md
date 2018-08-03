---
title: 'Oktatóanyag: Azure Active Directory-integráció az antik további - Shibboleth |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és további Antik - Shibboleth között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jeedes
ms.openlocfilehash: 1433d2f20c2b75815bce5164e43ff6a8d36407ee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440806"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Oktatóanyag: Azure Active Directory-integráció az antik további - Shibboleth

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a további Antik - shibboleth használatával, az Azure Active Directoryval (Azure AD).

Integrálás Antik további tudnivalók a shibboleth használatával, az Azure AD - nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá további Antik - Shibboleth Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Antik további - Shibboleth (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

További Antik - shibboleth-tel, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Antik további - Shibboleth egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás Antik további - Shibboleth a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Hozzáadás Antik további - Shibboleth a katalógusból
További Antik – az Azure AD-be a Shibboleth-integráció konfigurálásához hozzá kell Antik további - Shibboleth a galériából a felügyelt SaaS-alkalmazások listájára.

**Adjon hozzá további Antik - Shibboleth katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Antik további - Shibboleth**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_search.png)

1. Az eredmények panelen válassza ki a **Antik további - Shibboleth**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban az Azure AD egyszeri bejelentkezés az antik ismerje meg, tesztelése és konfigurálása – Shibboleth egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés használatát Azure ad-ben tudnia kell, a Antik ismerje meg, a partner felhasználó – a shibboleth használatával, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Antik további - hivatkozás kapcsolata Shibboleth kell létrehozni.

Antik további - shibboleth-tel, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Antik további – a shibboleth-tel, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy Antik további - Shibboleth tesztfelhasználó létrehozása](#creating-a-blackboard-learn---shibboleth-test-user)**  – egy megfelelője a Britta Simon Antik további - shibboleth használatával, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Antik további - Shibboleth-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Antik további - shibboleth-tel, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Antik további - Shibboleth** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_samlbase.png)

1. Az a **Antik további - Shibboleth-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`
 
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Antik további - Shibboleth ügyfél-támogatási csapatának](https://www.blackboard.com/forms/contact-us_form.aspx) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_general_400.png)
    
1. Az a **Antik további - Shibboleth konfigurációs** területén kattintson **konfigurálása Antik további - Shibboleth** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Antik további - Shibboleth** oldalon kell küldenie a letöltött **metaadatainak XML** és **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím**  való [Antik további - Shibboleth-ügyfélszolgálathoz](https://www.blackboard.com/forms/contact-us_form.aspx).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-blackboard-learn---shibboleth-test-user"></a>Egy Antik további - Shibboleth tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Antik további - Shibboleth felhasználói hoz létre. Együttműködik a [Antik további - Shibboleth támogatási csoport](https://www.blackboard.com/forms/contact-us_form.aspx) felhasználót is hozzáadhat a a Antik további - Shibboleth-platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Antik további - Shibboleth Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Antik további - shibboleth-tel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Antik további - Shibboleth**.

    ![Egyszeri bejelentkezés konfigurálása](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Antik ismerje meg - a hozzáférési panelen, a Shibboleth-csempére kattintva meg kell lekérése automatikusan bejelentkezett a Antik további - Shibboleth-alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_203.png

