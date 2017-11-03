---
title: "Oktatóanyag: Azure Active Directoryval integrált ThirdLight |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ThirdLight között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: ee7710cfea3a13907c0cc940a98c875bf83607a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Oktatóanyag: Azure Active Directoryval integrált ThirdLight

Ebben az oktatóanyagban elsajátíthatja ThirdLight integrálása az Azure Active Directory (Azure AD).

ThirdLight integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ThirdLight hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett ThirdLight (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs ThirdLight, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ThirdLight egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ThirdLight hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-thirdlight-from-the-gallery"></a>A gyűjteményből ThirdLight hozzáadása
Az Azure AD integrálása a ThirdLight konfigurálásához kell hozzáadnia ThirdLight a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből ThirdLight hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **ThirdLight**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_search.png)

5. Az eredmények panelen válassza ki a **ThirdLight**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján ThirdLight

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ThirdLight a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a ThirdLight közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** ThirdLight a.

Az Azure AD egyszeri bejelentkezést a ThirdLight tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[ThirdLight tesztfelhasználó létrehozása](#creating-a-thirdlight-test-user)**  - való Britta Simon valami ThirdLight, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ThirdLight alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés ThirdLight, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ThirdLight** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_samlbase.png)

3. Az a **ThirdLight tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.thirdlight.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.thirdlight.com/saml/sp`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és Identiifer. Ügyfél [ThirdLight ügyfél-támogatási csoport](https://www.thirdlight.com/support) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-thirdlight-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a ThirdLight vállalati webhely rendszergazdaként.

7. Ugrás a **konfigurációs \> rendszerfelügyelet**, és kattintson a **egy SAML2**.
   
    ![Rendszer-felügyeleti](./media/active-directory-saas-thirdlight-tutorial/ic805843.png "rendszerfelügyelet")

8. A egy SAML2 konfigurációs szakaszban hajtsa végre az alábbi lépéseket:
   
    ![SAML-alapú egyszeri bejelentkezést](./media/active-directory-saas-thirdlight-tutorial/ic805844.png "SAML-alapú egyszeri bejelentkezést.")   

     a. Válassza ki **egy SAML2 egyszeri bejelentkezés engedélyezése**.
 
     b. Mint **IdP metaadatok forrást**, jelölje be **terhelés IdP metaadatok XML-fájljából**.
 
     c. Nyissa meg a letöltött metaadatfájl, másolja a tartalmat, és illessze be azt a **IdP metaadatainak XML-kódja** szövegmező. 
     
     d. Kattintson a **menteni egy SAML2 beállítások**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a Britta Simon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-thirdlight-test-user"></a>ThirdLight tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ThirdLight bejelentkezni, akkor ki kell építenie a ThirdLight.  
ThirdLight, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **ThirdLight** vállalati hely rendszergazdaként.

2. Ugrás a **felhasználók** fülre.

3. Válassza ki **felhasználók és csoportok**.

4. Kattintson a **új felhasználó hozzáadása** gombra.

5. Adja meg **a felhasználónév, neve és leírása, e-mailek, válasszon egy előre definiált vagy új csoporttagok** egy érvényes AAD-fióknevet, amelyet kiépítését.

6. Kattintson a **Create** (Létrehozás) gombra.

>[!NOTE]
>Bármely más Thirdlight felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Thirdlight által nyújtott API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ThirdLight Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése ThirdLight, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ThirdLight**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen ThirdLight csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az ThirdLight alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_203.png

