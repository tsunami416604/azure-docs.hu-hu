---
title: "Oktatóanyag: Azure Active Directoryval integrált xMatters OnDemand |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és xMatters OnDemand között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ac4d4b5d810aca41620a5af2d29b86f07c1c031f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Oktatóanyag: Azure Active Directoryval integrált xMatters kötegmérete

Ebben az oktatóanyagban elsajátíthatja xMatters OnDemand integrálása az Azure Active Directory (Azure AD).

XMatters OnDemand integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a xMatters OnDemand hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a xMatters OnDemand (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs xMatters OnDemand, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy OnDemand egyszeri bejelentkezés xMatters előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. XMatters OnDemand hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters OnDemand hozzáadása a gyűjteményből
Az Azure AD integrálása a xMatters OnDemand konfigurálásához kell hozzáadnia xMatters OnDemand a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből xMatters OnDemand hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **xMatters OnDemand**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. Az eredmények panelen válassza ki a **xMatters OnDemand**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a xMatters OnDemand "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure AD-ben a felhasználó van Ondemanddetection xMatters a párjukhoz felhasználó tudnia kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a xMatters hivatkozás kapcsolatának OnDemand kell létrehozni.

XMatters OnDemand, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az xMatters OnDemand tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[XMatters OnDemand tesztfelhasználó létrehozása](#creating-a-xmatters-ondemand-test-user)**  - kell rendelkeznie egy Britta Simon megfelelője a xMatters OnDemand, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a xMatters OnDemand-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés xMatters OnDemand, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **xMatters OnDemand** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. Az a **xMatters OnDemand-tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:   
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [xMatters OnDemand támogatási csoport](https://www.xmatters.com/company/contact-us/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** tanúsítvány helyileg a fájlt, majd mentse **c:\\XMatters OnDemand.cer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)
    
    > [!IMPORTANT]
    > A tanúsítvány továbbítani kell a [xMatters OnDemand támogatási csoport](https://www.xmatters.com/company/contact-us/). Az egyszeri bejelentkezés konfigurációs is véglegesítése előtt a xMatters támogatási csoport által feltöltött kell a tanúsítványt. 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_400.png)

6. A a **xMatters OnDemand konfigurációs** kattintson **xMatters OnDemand konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a XMatters OnDemand vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **Admin**, és kattintson a **vállalat adatait** a bal oldali navigációs sávon.
   
    ![Felügyeleti](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "rendszergazda")

9. Az a **SAML-alapú konfigurációs** lapon, a következő lépésekkel:
   
    ![SAML-alapú konfigurációs](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML-konfigurációja")
   
    a. Válassza ki **SAML engedélyezése**.
   
    b. Beillesztés **SAML Entitásazonosító**, amely az Azure-portálról másolta a **identitás Szolgáltatóazonosító** szövegmező.
   
    c. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálról másolta a **egyszeri bejelentkezési URL-cím** szövegmező.
   
    d. Beillesztés **Sign-Out URL-cím**, amely az Azure-portálról másolta a **egyetlen kijelentkezési URL-címet** szövegmező.
   
    e. Az oldalon a vállalat adatait a lap tetején kattintson **módosítások mentése**.
    
    ![A vállalati részletek](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "vállalati részletei")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-xmatters-ondemand-test-user"></a>XMatters OnDemand tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók XMatters OnDemand bejelentkezni, akkor ki kell építenie XMatters OnDemand be. XMatters Ondemanddetection, ha egy kézi tevékenység.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **XMatters OnDemand** bérlő.

2.  Kattintson a **felhasználók** lapra, majd kattintson **felhasználó hozzáadása**.
  
    ![Felhasználók](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "felhasználók")

3. Az a **hozzáadni egy felhasználót** területen tegye a következőket:
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "felhasználó hozzáadása")

    a. Válassza ki **aktív**.

    b. Az a **Felhasználóazonosító** szövegmező, a felhasználó a felhasználói azonosító típusát, például Brittasimon@contoso.com.
   
    c. Az a **Keresztnév** szövegmezőhöz Britta például a felhasználó első nevét.

    d. Az a **Vezetéknév** szövegmezőhöz típus Simon például a felhasználó vezetékneve.
    
    e. Az a **hely** szövegmező, adjon meg egy érvényes Azure érvényes hely rendelkezés kívánt AD-fiókot.
    
    f. Kattintson a **Save** (Mentés) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó xMatters OnDemand való hozzáférés biztosítása.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése xMatters OnDemand, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **xMatters OnDemand**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a xMatters OnDemand csempe a hozzáférési panelen, meg kell beolvasni automatikusan bejelentkezett a xMatters OnDemand alkalmazás számára.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_203.png

