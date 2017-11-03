---
title: "Oktatóanyag: Azure Active Directoryval integrált üzemeltetett grafit |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az üzemeltetett grafit között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: f6ed02cc67be4090402a115c30819ff6cff99c99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Oktatóanyag: Azure Active Directoryval integrált üzemeltetett grafit

Ebben az oktatóanyagban elsajátíthatja üzemeltetett grafit integrálása az Azure Active Directory (Azure AD).

Üzemeltetett grafit integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure ad-ben üzemeltetett grafit hozzáféréssel rendelkező
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az üzemeltetett grafit (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs üzemeltetett grafit konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy üzemeltetett grafit egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Üzemeltetett grafit hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-hosted-graphite-from-the-gallery"></a>Üzemeltetett grafit hozzáadása a gyűjteményből
Az Azure AD integrálása a üzemeltetett grafit konfigurálásához kell hozzáadnia üzemeltetett grafit a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az üzemeltetett grafit a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **üzemeltetett grafit**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

5. Az eredmények panelen válassza ki a **üzemeltetett grafit**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapuló szolgáltatott grafit.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a grafit üzemeltetett megfelelőjén felhasználót a felhasználók az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a üzemeltetett grafit közötti kapcsolat kapcsolatot kell létrehozni.

Üzemeltetett grafit, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az üzemeltetett grafit tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Üzemeltetett grafit tesztfelhasználó létrehozása](#creating-a-hosted-graphite-test-user)**  - való egy megfelelője a Britta Simon üzemeltetett grafit, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az üzemeltetett grafit alkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés üzemeltetett grafit, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **üzemeltetett grafit** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

3. Az a **üzemeltetett grafit tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP kezdeményezett mód**, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.hostedgraphite.com/metadata/<user id>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.hostedgraphite.com/complete/saml/<user id>`

4. Az a **üzemeltetett grafit tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **Szolgáltató kezdeményezett mód**, hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Kattintson a **megjelenítése speciális URL-beállításainak** beállítás

    b. Az a **URL-cím bejelentkezési** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor kell frissíteni ezeket az értékeket a tényleges azonosítója, válasz és bejelentkezési az URL-címe. Ahhoz, hogy ezeket az értékeket, elvégezheti a hozzáférés -> az alkalmazás oldalán, vagy forduljon a SAML-alapú telepítő [üzemeltetett grafit támogatási csoport](mailto:help@hostedgraphite.com).
    >
 
5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_400.png)

7. A a **üzemeltetett grafit konfigurációs** kattintson **üzemeltetett grafit konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

8. Bejelentkezés az üzemeltetett grafit bérlő rendszergazdaként.

9. Lépjen a **SAML-alapú telepítő oldalán** az oldalsávon (**Access -> SAML-alapú telepítő**).
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

10. URL-egyezniük hajtható végre a konfigurációt a **üzemeltetett grafit tartomány és az URL-címek** szakasza az Azure-portálon.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

11. A **entitás vagy a kibocsátó azonosító** és **Egyszeri bejelentkezési URL-cím** szövegmezőből, illessze be az értékét **SAML Entitásazonosító** és **SAML-alapú egyszeri bejelentkezési URL-címe** amely Azure-portálon másolta. 
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

12. Jelölje ki "**írásvédett**" mint **felhasználói szerepkör alapértelmezett**.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

13. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure portálról letöltött, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

14. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Üzemeltetett grafit tesztfelhasználó létrehozása

Ez a szakasz célja üzemeltetett grafit Britta Simon nevű felhasználót létrehozni. Üzemeltetett grafit támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az üzemeltetett grafit elérésére, ha még nem létezik tett kísérlet során.

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha szeretné-e a üzemeltetett grafit támogatási csoportjához keresztül < mailto:help@hostedgraphite.com >. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés üzemeltetett grafit Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Az üzemeltetett grafit Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **üzemeltetett grafit**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési Panel üzemeltetett grafit mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett az üzemeltetett grafit alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png

