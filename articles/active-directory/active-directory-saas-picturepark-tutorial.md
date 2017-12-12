---
title: "Oktatóanyag: Azure Active Directoryval integrált Picturepark |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Picturepark között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 4d9fd7127a36e9a699a352dbe6899edd5ea99e92
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Oktatóanyag: Azure Active Directoryval integrált Picturepark

Ebben az oktatóanyagban elsajátíthatja Picturepark integrálása az Azure Active Directory (Azure AD).

Picturepark integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Picturepark hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Picturepark (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Picturepark, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Picturepark egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Picturepark hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-picturepark-from-the-gallery"></a>A gyűjteményből Picturepark hozzáadása
Az Azure AD integrálása a Picturepark konfigurálásához kell hozzáadnia Picturepark a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Picturepark hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Picturepark**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. Az eredmények panelen válassza ki a **Picturepark**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Picturepark.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Picturepark a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Picturepark közötti kapcsolat kapcsolatot kell létrehozni.

Picturepark, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Picturepark tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Picturepark tesztfelhasználó létrehozása](#creating-a-picturepark-test-user)**  - való Britta Simon valami Picturepark, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Picturepark alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Picturepark, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Picturepark** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Az a **Picturepark tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.picturepark.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Picturepark ügyfél-támogatási csoport](https://picturepark.com/about/contact/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. A a **Picturepark konfigurációs** kattintson **konfigurálása Picturepark** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Picturepark vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felügyeleti konzol**.
   
    ![Felügyeleti konzol](./media/active-directory-saas-picturepark-tutorial/ic795062.png "felügyeleti konzol")

9. Kattintson a **hitelesítési**, és kattintson a **identitás-szolgáltatóktól**.
   
    ![Hitelesítési](./media/active-directory-saas-picturepark-tutorial/ic795063.png "hitelesítés")

10. Az a **identitás szolgáltató konfigurálása** területen tegye a következőket:
   
    ![Szolgáltató konfigurálása identitás](./media/active-directory-saas-picturepark-tutorial/ic795064.png "identitás szolgáltató konfigurálása")
   
    a. Kattintson az **Add** (Hozzáadás) parancsra.
  
    b. Írja be a konfiguráció nevét.
   
    c. Válassza ki **alapértelmezett**.
   
    d. A **Issuer URI** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    e. A **megbízható kibocsátó görgetőgomb nyomtatás** szövegmezőhöz illessze be az értékét **ujjlenyomat** , amely a másolt **SAML-aláíró tanúsítványa** szakasz. 

11. Kattintson a **JoinDefaultUsersGroup**.

12. Beállítása a **Emailaddress** attribútumnak a **jogcím** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` kattintson **mentése**.

      ![Konfigurációs](./media/active-directory-saas-picturepark-tutorial/ic795065.png "konfiguráció")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-picturepark-test-user"></a>Picturepark tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Picturepark bejelentkezni, akkor ki kell építenie Picturepark be. Picturepark, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Picturepark** bérlő.

2. A felső eszköztáron kattintson **felügyeleti eszközök**, és kattintson a **felhasználók**.
   
    ![Felhasználók](./media/active-directory-saas-picturepark-tutorial/ic795067.png "felhasználók")

3. Az a **felhasználók áttekintése** lapra, majd **új**.
   
    ![Felhasználókezelés](./media/active-directory-saas-picturepark-tutorial/ic795068.png "felhasználók kezelése")

4. Az a **felhasználó létrehozása** párbeszédpanelen a következő lépésekkel a egy érvényes Azure Active Directory-felhasználó azt szeretné, hogy építse ki:
   
    ![Hozzon létre felhasználói](./media/active-directory-saas-picturepark-tutorial/ic795069.png "felhasználó létrehozása")
   
    a. Az a **E-mail cím** szövegmezőhöz típusa a **e-mail cím** felhasználó  **BrittaSimon@contoso.com** .  
   
    b. Az a **jelszó** és **jelszó megerősítése** szövegmezőből, típusa a **jelszó** a BrittaSimon. 
   
    c. Az a **Utónév** szövegmezőhöz típusa a **Utónév** felhasználó **Britta**. 
   
    d. Az a **Vezetéknév** szövegmezőhöz típusa a **Vezetéknév** felhasználó **Simon**.
   
    e. Az a **vállalati** szövegmezőhöz típusa a **vállalatnév** felhasználó. 
   
    f. Az a **ország** szövegmező, jelölje be a **ország** felhasználó.
  
    g. Az a **ZIP-** szövegmezőhöz típusa a **irányítószám** a város.
   
    h. Az a **Város** szövegmezőhöz típusa a **városnév** felhasználó.

    i. Válassza ki a **nyelvi**.
   
    j. Kattintson a **Create** (Létrehozás) gombra.

>[!NOTE]
>Bármely más Picturepark felhasználói fiók létrehozása eszközök vagy Picturepark kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Picturepark Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Picturepark, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Picturepark**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Picturepark csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Picturepark alkalmazására. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png

