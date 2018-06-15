---
title: 'Oktatóanyag: Azure Active Directoryval integrált Jobscience |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Jobscience között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: cf6dccf02fa56b90ea4737e1d1c7b9a5e9d1d4af
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346230"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Oktatóanyag: Azure Active Directoryval integrált Jobscience

Ebben az oktatóanyagban elsajátíthatja Jobscience integrálása az Azure Active Directory (Azure AD).

Jobscience integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Jobscience hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Jobscience (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Jobscience, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Jobscience egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Jobscience hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-jobscience-from-the-gallery"></a>A gyűjteményből Jobscience hozzáadása
Az Azure AD integrálása a Jobscience konfigurálásához kell hozzáadnia Jobscience a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Jobscience hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Jobscience**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. Az eredmények panelen válassza ki a **Jobscience**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Jobscience

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Jobscience a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Jobscience közötti kapcsolat kapcsolatot kell létrehozni.

Jobscience, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Jobscience tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Jobscience tesztfelhasználó létrehozása](#creating-a-jobscience-test-user)**  - való Britta Simon valami Jobscience, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Jobscience alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Jobscience, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Jobscience** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. Az a **Jobscience tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Hozza ki ezt az értéket [Jobscience ügyfél-támogatási csoport](https://www.jobscience.com/support) vagy az SSO profilból hoz létre amely esetén, tekintse meg az oktatóanyag későbbi részében. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. A a **Jobscience konfigurációs** kattintson **konfigurálása Jobscience** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Jelentkezzen be rendszergazdaként a Jobscience vállalati webhely.

8. Ugrás a **telepítő**.
   
   ![A telepítő](./media/active-directory-saas-jobscience-tutorial/IC784358.png "beállítása")

9. A bal oldali navigációs panelen a a **Administer** kattintson **tartományok** bontsa ki a kapcsolódó szakaszt, és kattintson **saját tartomány** megnyitásához a **saját tartomány** lap. 
   
   ![Saját tartomány](./media/active-directory-saas-jobscience-tutorial/ic767825.png "saját tartomány")

10. Győződjön meg arról, hogy a tartomány megfelelően van beállítva, győződjön meg arról, hogy "**4. lépés telepíti a felhasználók számára**", és tekintse át a "**saját tartománybeállítások**".

    ![A tartományi felhasználó számára központilag telepített](./media/active-directory-saas-jobscience-tutorial/ic784377.png "tartományi felhasználó számára központilag telepített")

11. Kattintson a Jobscience vállalati hely **biztonsági vezérlők**, és kattintson a **egyszeri bejelentkezési beállítások**.
    
    ![Biztonsági vezérlők](./media/active-directory-saas-jobscience-tutorial/ic784364.png "biztonsági vezérlőket")

12. Az a **egyszeri bejelentkezési beállítások** területen tegye a következőket:
    
    ![Az egyszeri bejelentkezés beállítások](./media/active-directory-saas-jobscience-tutorial/ic781026.png "az egyszeri bejelentkezés beállításai")
    
    a. Válassza ki **SAML engedélyezett**.

    b. Kattintson az **Új** lehetőségre.

13. Az a **SAML-alapú egyszeri bejelentkezési beállítás szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![SAML-alapú egyszeri bejelentkezés beállítása](./media/active-directory-saas-jobscience-tutorial/ic784365.png "SAML-alapú egyszeri bejelentkezés beállítása")
    
    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét.

    b. A **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta.

    c. Az a **entitásazonosító** szövegmezőhöz típusa `https://salesforce-jobscience.com`

    d. Kattintson a **Tallózás** az Azure AD-tanúsítványok feltöltéséről.

    e. Mint **SAML identitástípus**, jelölje be **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**.

    f. Mint **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentfier elemében van**.

    g. A **Identity Provider bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    h. A **Identity Provider kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    i. Kattintson a **Save** (Mentés) gombra.

14. A bal oldali navigációs panelen a a **Administer** kattintson **tartományok** bontsa ki a kapcsolódó szakaszt, és kattintson **saját tartomány** megnyitásához a **saját tartomány** lap. 
    
    ![Saját tartomány](./media/active-directory-saas-jobscience-tutorial/ic767825.png "saját tartomány")

15. Az a **saját tartomány** lap a **bejelentkezési oldal vállalati arculata** kattintson **szerkesztése**.
    
    ![Bejelentkezési oldal vállalati arculatán alkalmazott](./media/active-directory-saas-jobscience-tutorial/ic767826.png "bejelentkezési oldal vállalati arculatán alkalmazott")

16. Az a **bejelentkezési oldal vállalati arculata** lap a **hitelesítési szolgáltatás** részben, a neve a **SAML SSO beállítások** jelenik meg. Válassza ki azt, és kattintson **mentése**.
    
    ![Bejelentkezési oldal vállalati arculatán alkalmazott](./media/active-directory-saas-jobscience-tutorial/ic784366.png "bejelentkezési oldal vállalati arculatán alkalmazott")

17. Az SP eléréséhez által kezdeményezett egyszeri bejelentkezési bejelentkezési URL-cím kattintson a a **egyszeri bejelentkezés beállítások** a a **biztonsági vezérlők** menü szakasz.

    ![Biztonsági vezérlők](./media/active-directory-saas-jobscience-tutorial/ic784368.png "biztonsági vezérlőket")
    
    Kattintson a fenti lépésben létrehozott egyszeri bejelentkezési profil. Ezen a lapon látható az egyszeri bejelentkezési URL-címen a vállalat (például [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Jobscience bejelentkezni, akkor ki kell építenie a Jobscience. Jobscience, ha egy kézi tevékenység.

>[!NOTE]
>Bármely más Jobscience felhasználói fiók létrehozása eszközök vagy API-k által biztosított Jobscience kiépítését Azure Active Directory felhasználói fiókokat.
>  
        
**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Jobscience** vállalati hely rendszergazdaként.

2. Ugrás a telepítőt.
   
   ![A telepítő](./media/active-directory-saas-jobscience-tutorial/ic784358.png "beállítása")
3. Ugrás a **felhasználók kezelése \> felhasználók**.
   
   ![Felhasználók](./media/active-directory-saas-jobscience-tutorial/ic784369.png "felhasználók")
4. Kattintson a **új felhasználó**.
   
   ![Minden felhasználó](./media/active-directory-saas-jobscience-tutorial/ic784370.png "minden felhasználó")
5. Az a **-felhasználó szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Felhasználó szerkesztése](./media/active-directory-saas-jobscience-tutorial/ic784371.png "felhasználó szerkesztése")
   
   a. Az a **Keresztnév** szövegmezőhöz Britta például a felhasználó utónevét adja.
   
   b. Az a **Vezetéknév** szövegmező, írja be a vezetéknevet Simon például felhasználó.
   
   c. Az a **Alias** szövegmező, írja be a felhasználó például brittas aliasnevet.

   d. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

   e. Az a **felhasználónév** szövegmező, írja be például a felhasználó a felhasználónév Brittasimon@contoso.com.

   f. Az a **becenév** szövegmező, írja be például a Simon felhasználó nick nevét.

   g. Kattintson a **Save** (Mentés) gombra.

    
> [!NOTE]
> Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Jobscience Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Jobscience, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Jobscience**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Jobscience csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Jobscience alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png

