---
title: "Oktatóanyag: Azure Active Directoryval integrált ITRP |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ITRP között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: fae1c7b6b0e04c1e23123d3aee7913cb3131e645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Oktatóanyag: Azure Active Directoryval integrált ITRP

Ebben az oktatóanyagban elsajátíthatja ITRP integrálása az Azure Active Directory (Azure AD).

ITRP integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ITRP hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett ITRP (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs ITRP, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ITRP egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ITRP hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-itrp-from-the-gallery"></a>A gyűjteményből ITRP hozzáadása
ITRP az Azure ad integrálása konfigurálásához szüksége ITRP hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből ITRP hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **ITRP**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_search.png)

5. Az eredmények panelen válassza ki a **ITRP**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján ITRP

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ITRP a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a ITRP közötti kapcsolat kapcsolatot kell létrehozni.

ITRP, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a ITRP tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Tesztfelhasználó létrehozása egy ITRP](#creating-an-itrp-test-user)**  - való Britta Simon valami ITRP, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ITRP alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés ITRP, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ITRP** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_samlbase.png)

3. Az a **ITRP tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<tenant-name>.itrp.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [ITRP ügyfél-támogatási csoport](https://www.itrp.com/support) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_general_400.png)

6. A a **ITRP konfigurációs** kattintson **konfigurálása ITRP** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe és Sign-Out URL-cím** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a ITRP vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **beállítások**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/ic775570.png "ITRP")

8. A bal oldali navigációs ablakból válassza **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-itrp-tutorial/ic775571.png "egyszeri bejelentkezés")

9. Az egyszeri bejelentkezés konfigurációs szakaszban hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-itrp-tutorial/ic775572.png "egyszeri bejelentkezés")
    
    ![Egyszeri bejelentkezés](./media/active-directory-saas-itrp-tutorial/ic775573.png "egyszeri bejelentkezés")   

    a. Kattintson az **Engedélyezés** gombra.

    b. A **távoli napló kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    c. A **SAML SSO URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    d.In **tanúsítvány-ujjlenyomat** szövegmező, illessze be a **ujjlenyomat** tanúsítványt, amely az Azure-portálon másolta értékét. 
      
10. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-itrp-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-itrp-test-user"></a>Egy ITRP tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ITRP bejelentkezni, akkor ki kell építenie a ITRP.  

ITRP, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **ITRP** bérlő.

2. A felső eszköztáron kattintson **rekordok**.
   
    ![Felügyeleti](./media/active-directory-saas-itrp-tutorial/ic775575.png "rendszergazda")

3. Válassza ki a helyi menü **személyek**.
   
    ![Személyek](./media/active-directory-saas-itrp-tutorial/ic775587.png "személyek")

4. Kattintson a **adja hozzá az új személy** ("+").
   
    ![Felügyeleti](./media/active-directory-saas-itrp-tutorial/ic775576.png "rendszergazda")

5. Új személy hozzáadása párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználói](./media/active-directory-saas-itrp-tutorial/ic775577.png "felhasználó") 
      
    a. Típus a **neve**, **E-mail** egy érvényes AAD-fióknevet, amelyet kiépítését.

    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más ITRP felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz ITRP által nyújtott API-k. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ITRP Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése ITRP, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ITRP**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen ITRP csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az ITRP alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_203.png

