---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Optimizely |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Optimizely között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 19be60a7a2767b8ea2185e1336b3155143260fb6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180756"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Optimizely

Ebben az oktatóanyagban elsajátíthatja, hogyan Optimizely integrálása az Azure Active Directory (Azure AD).

Optimizely integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Optimizely Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Optimizely (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Optimizely az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Optimizely egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Optimizely hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-optimizely-from-the-gallery"></a>Optimizely hozzáadása a katalógusból

Az Azure AD integrálása a Optimizely konfigurálásához hozzá kell Optimizely a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Optimizely hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Optimizely**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. Az eredmények panelen válassza ki a **Optimizely**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Optimizely az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Optimizely mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Optimizely hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Optimizely a.

Az Azure AD egyszeri bejelentkezés az Optimizely tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy Optimizely tesztfelhasználó létrehozása](#creating-an-optimizely-test-user)**  – egy megfelelője a Britta Simon Optimizely, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Optimizely alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Optimizely, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Optimizely** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Az a **Optimizely tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.optimizely.net/<instance name>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:  `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Ezek az értékek nem a valós. A tényleges bejelentkezési URL-cím és az azonosítója, az oktatóanyag későbbi részében ismertetett frissíteni az értéket.

4. Optimizely alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. Kattintson a **megtekintése és egyéb felhasználói attribútumok szerkesztése** a jelölőnégyzetet a **felhasználói attribútumok** szakaszban bontsa ki az attribútumokat. Hajtsa végre az alábbi lépéseket az egyes a megjelenített attribútumok-

    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |
    | e-mail | user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be a **attribútumnév** a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_general_400.png)

8. Az a **Optimizely konfigurációs** területén kattintson **konfigurálása Optimizely** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. Az egyszeri bejelentkezés konfigurálása **Optimizely** oldalán, lépjen kapcsolatba Ügyfélmenedzserével Optimizely, és adja meg a letöltött **tanúsítvány (Base64)**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím**.

10. Az e-mailben válaszul Optimizely biztosít a bejelentkezési URL-cím (SP által kezdeményezett egyszeri Bejelentkezést) és azonosítója (Service Provider entitás azonosítója) értékeit.

    a. Másolás a **SP által kezdeményezett egyszeri bejelentkezési URL-cím** megadott Optimizely, és illessze be azokat a **bejelentkezési URL-** szövegmezőjébe **Optimizely tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Másolás a **Service Provider Entitásazonosító** megadott Optimizely, és illessze be azokat a **azonosító** szövegmezőjébe **Optimizely tartomány és URL-címek** szakaszban az Azure Portalon.

11. Egy másik böngészőablakban bejelentkezés Optimizely alkalmazását.

12. Kattintson a jobb sarokban található fiók felső neve, majd **fiókbeállításokat**.

    ![Az Azure AD egyszeri bejelentkezés](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. A fiók lapon jelölje be a jelölőnégyzetet **SSO engedélyezése** az egyszeri bejelentkezést az alatt a **áttekintése** szakaszban.
  
    ![Az Azure AD egyszeri bejelentkezés](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. Kattintson a **Mentés** gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** Britta Simon.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-an-optimizely-test-user"></a>Egy Optimizely tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Optimizely hoz létre.

1. A kezdőlapon válassza **közreműködők** fülre.

2. Kattintson az új közreműködő hozzáadása a projekthez, **új közreműködő**.
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_10.png)

3. Adja meg az e-mail-cím, és hozzárendelheti őket egy szerepkört. Kattintson a **meghívása**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/optimizely-tutorial/create_aaduser_11.png)

4. A meghívó e-mailt kapnak. E-mail-cím használatával kell Optimizely bejelentkezni.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Optimizely Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Optimizely, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Optimizely**.

    ![Egyszeri bejelentkezés konfigurálása](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Optimizely csempére kattint, meg kell lekérése automatikusan bejelentkezett az Optimizely alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png
