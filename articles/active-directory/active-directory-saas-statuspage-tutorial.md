---
title: "Oktatóanyag: Azure Active Directoryval integrált StatusPage |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és StatusPage között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: fa16cdec7b89404c140435fe57d5aa4b08cfa985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Oktatóanyag: Azure Active Directoryval integrált StatusPage

Ebben az oktatóanyagban elsajátíthatja StatusPage integrálása az Azure Active Directory (Azure AD).

StatusPage integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a StatusPage hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett StatusPage (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs StatusPage, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy StatusPage egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből StatusPage hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-statuspage-from-the-gallery"></a>A gyűjteményből StatusPage hozzáadása
Az Azure AD integrálása a StatusPage konfigurálásához kell hozzáadnia StatusPage a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből StatusPage hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **StatusPage**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_search.png)

5. Az eredmények panelen válassza ki a **StatusPage**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján StatusPage.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó StatusPage a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a StatusPage közötti kapcsolat kapcsolatot kell létrehozni.

StatusPage, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a StatusPage tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[StatusPage tesztfelhasználó létrehozása](#creating-a-statuspage-test-user)**  - való Britta Simon valami StatusPage, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az StatusPage alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés StatusPage, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **StatusPage** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. Az a **StatusPage tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Lépjen kapcsolatba a StatusPage támogatási csapatának [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)igényelni az egyszeri bejelentkezés beállításához szükséges metaadatok. 
    >
    >a. A kibocsátó értéket másol a metaadatok, és illessze be azt a **azonosító** szövegmező.
    >
    >b. A metaadatok, másolja a válasz URL-címet, és illessze be azt a **válasz URL-CÍMEN** szövegmező.

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_general_400.png)

6. A a **StatusPage konfigurációs** kattintson **konfigurálása StatusPage** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a StatusPage vállalati webhely rendszergazdaként.

8. Kattintson az eszköztáron **fiók kezelése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 

10. Kattintson a **egyszeri bejelentkezés** fülre. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 

11. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. Az a **SSO célként megadott URL** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    b. A letöltött tanúsítvány megnyitása a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **tanúsítvány** szövegmező. 

    c. Kattintson a **konfiguráció mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-statuspage-test-user"></a>StatusPage tesztfelhasználó létrehozása

Ez a szakasz célja StatusPage Britta Simon nevű felhasználót létrehozni.

StatusPage támogatja közvetlenül az időponthoz kötött kiosztást. Már engedélyezett legyen [az Azure AD konfigurálása egyszeri bejelentkezéshez](#configuring-azure-ad-single-sign-on).

**A felhasználó Britta Simon meghívta StatusPage létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a StatusPage vállalati webhely rendszergazdaként.

2. Kattintson a felső menüben **fiók kezelése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)

3. Kattintson a **csapattagok** fülre. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

4. Kattintson a **Hozzáadás CSAPATTAG**. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

5. Típus a **E-mail cím**, **Utónév**, és **Vezetéknév** szeretné azokat a kapcsolódó szövegmezők rendelkezés érvényes felhasználó. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

6. Mint **szerepkör**, válassza a **ügyfél rendszergazda**.

7. Kattintson a **fiók létrehozása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés StatusPage Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése StatusPage, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **StatusPage**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen StatusPage csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az StatusPage alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png

