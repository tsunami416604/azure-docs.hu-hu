---
title: "Oktatóanyag: Azure Active Directoryval integrált AppDynamics |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és AppDynamics között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: b818f5d24873007aff5a8713a11d368cbe799ee6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Oktatóanyag: Azure Active Directoryval integrált AppDynamics

Ebben az oktatóanyagban elsajátíthatja AppDynamics integrálása az Azure Active Directory (Azure AD).

AppDynamics integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a AppDynamics hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett AppDynamics (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs AppDynamics, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy AppDynamics egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből AppDynamics hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-appdynamics-from-the-gallery"></a>A gyűjteményből AppDynamics hozzáadása
Az Azure AD integrálása a AppDynamics konfigurálásához kell hozzáadnia AppDynamics a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből AppDynamics hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **AppDynamics**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_search.png)

5. Az eredmények panelen válassza ki a **AppDynamics**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján AppDynamics

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó AppDynamics a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a AppDynamics közötti kapcsolat kapcsolatot kell létrehozni.

AppDynamics, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a AppDynamics tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy AppDynamics tesztfelhasználó létrehozása](#creating-an-appdynamics-test-user)**  - való Britta Simon valami AppDynamics, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az AppDynamics alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés AppDynamics, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **AppDynamics** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. Az a **AppDynamics tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.saas.appdynamics.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [AppDynamics ügyfél-támogatási csoport](https://www.appdynamics.com/support/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_general_400.png)

6. A a **AppDynamics konfigurációs** kattintson **konfigurálása AppDynamics** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a AppDynamics vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **beállítások**, és kattintson a **felügyeleti**.
   
    ![Felügyeleti](./media/active-directory-saas-appdynamics-tutorial/ic790216.png "felügyeleti")

9. Kattintson a **hitelesítési szolgáltató** fülre.
   
    ![Hitelesítésszolgáltató](./media/active-directory-saas-appdynamics-tutorial/ic790224.png "hitelesítési szolgáltató")

10. Az a **hitelesítési szolgáltató** területen tegye a következőket:
   
    ![SAML-alapú konfigurációs](./media/active-directory-saas-appdynamics-tutorial/ic790225.png "SAML-konfigurációja")   

    a. Mint **hitelesítési szolgáltató**, jelölje be **SAML**.

    b. Az a **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. Az a **kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
       
    d. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **tanúsítvány** szövegmező

    e. Kattintson a **Save** (Mentés) gombra.

     ![Mentés](./media/active-directory-saas-appdynamics-tutorial/ic777673.png "mentése")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-appdynamics-test-user"></a>Egy AppDynamics tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AppDynamics bejelentkezni, akkor ki kell építenie a AppDynamics. AppDynamics, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a AppDynamics vállalati webhely.

2. Ugrás a **felhasználók**, és kattintson a  **+**  megnyitásához a **felhasználó létrehozása** párbeszédpanel.
   
    ![Felhasználók](./media/active-directory-saas-appdynamics-tutorial/ic790229.png "felhasználók")

3. Az a **felhasználó létrehozása** területen tegye a következőket:
   
    ![Hozzon létre felhasználói](./media/active-directory-saas-appdynamics-tutorial/ic790230.png "felhasználó létrehozása")
   
    a. Típus a **felhasználónév**, **neve**, **E-mail**, **új jelszó**, **új jelszót ismételje meg a** szeretné azokat a kapcsolódó szövegmezők rendelkezés érvényes AAD-fiók.

    b. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >Bármely más AppDynamics felhasználói fiók létrehozása eszközök vagy AppDynamics kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AppDynamics Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése AppDynamics, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **AppDynamics**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen AppDynamics csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az AppDynamics alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_203.png

