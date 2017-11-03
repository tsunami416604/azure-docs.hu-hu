---
title: "Oktatóanyag: Azure Active Directoryval integrált QuickHelp |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és QuickHelp között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: jeedes
ms.openlocfilehash: 1c72b0ddee636090129dab7a5c7ec6ffd452434a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Oktatóanyag: Azure Active Directoryval integrált QuickHelp

Ebben az oktatóanyagban elsajátíthatja QuickHelp integrálása az Azure Active Directory (Azure AD).

QuickHelp integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a QuickHelp hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett QuickHelp (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs QuickHelp, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy QuickHelp egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből QuickHelp hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-quickhelp-from-the-gallery"></a>A gyűjteményből QuickHelp hozzáadása
Az Azure AD integrálása a QuickHelp konfigurálásához kell hozzáadnia QuickHelp a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből QuickHelp hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **QuickHelp**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_search.png)

5. Az eredmények panelen válassza ki a **QuickHelp**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján QuickHelp.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó QuickHelp a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a QuickHelp közötti kapcsolat kapcsolatot kell létrehozni.

QuickHelp, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a QuickHelp tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[QuickHelp tesztfelhasználó létrehozása](#creating-a-quickhelp-test-user)**  - való Britta Simon valami QuickHelp, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az QuickHelp alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés QuickHelp, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **QuickHelp** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

3. Az a **QuickHelp tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://quickhelp.com/<instancename>/#/Login`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.quickhelp.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [QuickHelp ügyfél-támogatási csoport](https://support.quickhelp.com/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-quickhelp-tutorial/tutorial_general_400.png) 

6. Bejelentkezés a QuickHelp vállalati webhely rendszergazdaként.

7. Kattintson a felső menüben **Admin**.
   
    ![Egyszeri bejelentkezés konfigurálása][21]

8. Az a **QuickHelp Admin** menüben kattintson a **beállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása][22]

9. Kattintson a **hitelesítési beállítások**.

10. Az a **hitelesítési beállítások** lapon, a következő lépések
   
    ![Egyszeri bejelentkezés konfigurálása][23]
   
    a. Mint **egyszeri bejelentkezés típusa**, jelölje be **WSFederation**.
   
    b. A letöltött Azure metaadatok fájl feltöltéséhez, kattintson **Tallózás**, keresse meg a fájlt, kattintson end **metaadatok feltöltése**.
   
    c. Az a **E-mail** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
   
    d. Az a **Utónév** szövegmezőhöz `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
   
    e. Az a **Vezetéknév** szövegmezőhöz `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
   
    f. Az a **műveletsávon**, kattintson a **mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-quickhelp-test-user"></a>QuickHelp tesztfelhasználó létrehozása

Ez a szakasz célja QuickHelp Britta Simon nevű felhasználót létrehozni.
Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó QuickHelp az Azure AD-felhasználóhoz van. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a QuickHelp közötti kapcsolat kapcsolatot kell létrehozni.

QuickHelp támogatja közvetlenül az időponthoz kötött kiosztást. Ez azt jelenti, ha szükséges, a felhasználói fiók automatikusan létrejön a QuickHelp és a fiók kapcsolódik az Azure AD-fiókot.

Nincs ebben a szakaszban az Ön művelet elem.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés QuickHelp Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése QuickHelp, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **QuickHelp**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  

Ha a hozzáférési panelen QuickHelp csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az QuickHelp alkalmazására.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
