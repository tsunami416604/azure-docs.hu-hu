---
title: 'Oktatóanyag: Azure Active Directory-integráció laposabb fájlokkal |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és laposabb fájlok között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: e7c2d842a9e6ebe431577aa2ca133c9016f26812
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35931432"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Oktatóanyag: Azure Active Directoryval integrált laposabb fájlok

Ebben az oktatóanyagban elsajátíthatja laposabb fájlok integrálása az Azure Active Directory (Azure AD).

Laposabb fájlok integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a laposabb fájlok hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett laposabb fájlok (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs laposabb fájlok, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy laposabb fájlok egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Laposabb fájlok hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-flatter-files-from-the-gallery"></a>Laposabb fájlok hozzáadása a gyűjteményből
Az Azure AD integrálása a laposabb fájlok konfigurálásához szüksége laposabb fájlok hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Laposabb-fájlok hozzáadása a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **laposabb fájlok**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

5. Az eredmények panelen válassza ki a **laposabb fájlok**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján laposabb fájlokat.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó laposabb fájlokban a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a laposabb fájlok közötti kapcsolat kapcsolatot kell létrehozni.

Laposabb fájlokban, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Laposabb fájlok az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Laposabb fájlok tesztfelhasználó létrehozása](#creating-a-flatter-files-test-user)**  - való egy megfelelője a Britta Simon laposabb fájlok, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az laposabb fájlok alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés laposabb fájlokkal megadásához a következő lépésekkel:**

1. Az Azure portálon a a **laposabb fájlok** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

3. Az a **laposabb fájlok tartomány és az URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_general_400.png)

6. A a **laposabb fájlok konfigurációs** kattintson **laposabb fájlok konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

7. Bejelentkezés a laposabb fájlok alkalmazást rendszergazdaként.

8. Kattintson a **IRÁNYÍTÓPULT**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

9. Kattintson a **beállítások**, majd hajtsa végre a következő lépéseket és a **vállalati** lapon: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Válassza ki **SAML 2.0 hitelesítéshez használandó**.
    
    b. Kattintson a **SAML konfigurálása**.

8. Az a **SAML-alapú konfigurációs** párbeszédpanelen hajtsa végre a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Az a **tartomány** szövegmező, írja be a regisztrált tartománya.
   
    >[!NOTE]
    >Ha még nem rendelkezik egy regisztrált tartományt, mégis, lépjen kapcsolatba a laposabb fájlok támogatja-e a csapat keresztül [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. A **identitási szolgáltató URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** másolta, amely Azure-portálon alkotnak.
   
    c.  Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **szolgáltató Identitástanúsítvány** szövegmező.

    d. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-flatter-files-test-user"></a>Laposabb fájlok tesztfelhasználó létrehozása

Ez a szakasz célja laposabb fájlok Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta laposabb fájlok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **laposabb fájlok** vállalati hely rendszergazdaként.

2. A bal oldali navigációs ablaktábláján kattintson **beállítások**, majd kattintson a **felhasználók** fülre.
   
    ![Fájlok laposabb felhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kattintson a **felhasználó hozzáadása**. 

4. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Fájlok laposabb felhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Az a **Utónév** szövegmezőhöz típus **Britta**.
   
    b. Az a **Vezetéknév** szövegmezőhöz típus **Simon**. 
   
    c. Az a **E-mail cím** szövegmező, írja be a Britta meg e-mail címét az Azure portálon.
   
    d. Kattintson a **nyújt**.   


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés laposabb fájlok Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Fájlokhoz hozzárendelni kívánt Britta Simon laposabb, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **laposabb fájlok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen laposabb fájlok csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az laposabb fájlok alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

