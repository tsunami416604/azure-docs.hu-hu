---
title: 'Oktatóanyag: Azure Active Directory-integráció kis javításait |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és kis fejlesztései között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2f6cab5dd7c10e4036cdd2013c809142bf7ec846
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352565"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Oktatóanyag: Azure Active Directoryval integrált kis fejlesztései

Ebben az oktatóanyagban elsajátíthatja kis fejlesztései integrálása az Azure Active Directory (Azure AD).

Kis fejlesztései integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáférhet kis fejlesztései
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a kis fejlesztései (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához kis javításait, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy kis fejlesztései az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Kis fejlesztései hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-small-improvements-from-the-gallery"></a>Kis fejlesztései hozzáadása a gyűjteményből
Az Azure AD integrálása kisméretű fejlesztései konfigurálásához kell hozzáadnia kis fejlesztései a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a kis fejlesztései a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **kis fejlesztései**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_search.png)

5. Az eredmények panelen válassza ki a **kis fejlesztései**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján kis fejlesztései.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó kis fejlesztései a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a kis fejlesztései közötti kapcsolat kapcsolatot kell létrehozni.

Kis fejlesztései, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés kis javításait tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Kis fejlesztései tesztfelhasználó létrehozása](#creating-a-small-improvements-test-user)**  - való egy megfelelője a Britta Simon kis fejlesztései, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és kis fejlesztései alkalmazásában egyszeri bejelentkezés konfigurálása.

**Kis javításait konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **kis fejlesztései** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

3. Az a **kis fejlesztései tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.small-improvements.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [kis fejlesztései ügyfél-támogatási csoport](mailto:support@small-improvements.com) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_400.png)

6. A a **kis fejlesztései konfigurációs** kattintson **kis fejlesztései konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a kis fejlesztései vállalati webhely rendszergazdaként.

8. A fő irányítópult-oldalon, kattintson az **felügyeleti** a bal oldali gomb.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

9. Kattintson a **SAML SSO** gombot **integrációja** szakasz.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

10. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Az a **HTTP-végpont** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    b. A letöltött tanúsítvány megnyitása a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **x509 tanúsítvány** szövegmező. 

    c. Ha szeretne az egyszeri bejelentkezés és bejelentkezés hitelesítési lehetőséget a felhasználók rendelkeznek, majd ellenőrizze a **bejelentkezési azonosító és jelszó hozzáférésének engedélyezése túl** lehetőséget.  

    d. Adja meg a megfelelő értéket, az Egyszeri bejelentkezés gombra, a nevet a **SAML Rákérdezés** szövegmező.  

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-small-improvements-test-user"></a>Kis fejlesztései tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók kis fejlesztései bejelentkezni, akkor ki kell építenie kis fejlesztései be. Kis fejlesztései, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a kis fejlesztései vállalati webhely rendszergazdaként.

2. A kezdőlapról, nyissa meg a bal oldali, kattintson a menühöz **felügyeleti**.

3. Kattintson a **felhasználói Directory** gomb felhasználókezelés szakaszából. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

4. Kattintson a **felhasználók hozzáadása az**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

5. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket: 

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Adja meg a **Utónév** például a felhasználó **Britta**.

    b. Adja meg a **Vezetéknév** például a felhasználó **Simon**.

    c. Adja meg a **E-mail** például a felhasználó **brittasimon@contoso.com**. 

    d. Másik lehetőségként a személyes üzenetet adhat meg a **értesítő e-mail küldése** mezőbe. Ha nem szeretne az értesítés elküldéséhez, majd törölje a jelet a jelölőnégyzetből.

    e. Kattintson a **létrehozása a felhasználók**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés kis fejlesztései Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése kis fejlesztései, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **kis fejlesztései**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési Panel kis fejlesztései mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett kis fejlesztései Alkalmazásmódosítások.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png

