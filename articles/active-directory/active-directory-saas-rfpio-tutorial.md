---
title: "Oktatóanyag: Azure Active Directoryval integrált RFPIO |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és RFPIO között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 57bb448f1bb858d15a7ecaad5a9ba797f5d7d85e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Oktatóanyag: Azure Active Directoryval integrált RFPIO

Ebben az oktatóanyagban elsajátíthatja RFPIO integrálása az Azure Active Directory (Azure AD).

RFPIO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér RFPIO ki szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett RFPIO (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs RFPIO, a következőkre van szükség:

- Az Azure AD-előfizetés.
- RFPIO egyszeri bejelentkezést a alkalmas előfizetés.

> [!NOTE]
> Nem ajánlott, hogy éles környezetben segítségével ellenőrizze a lépéseket, ebben az oktatóanyagban.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, beszerezheti a [egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. RFPIO hozzáadása a gyűjteményből.
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés.

## <a name="add-rfpio-from-the-gallery"></a>Adja hozzá a RFPIO a gyűjteményből
Az Azure AD integrálása a RFPIO konfigurálásához kell hozzáadnia RFPIO a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

### <a name="to-add-rfpio-from-the-gallery"></a>A gyűjteményből RFPIO hozzáadása

1. Az a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Válassza ki **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **RFPIO**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. Az eredmények panelen válassza ki a **RFPIO**, majd válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján RFPIO.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a kapcsolat Újdonságok RFPIO megfelelőjére felhasználó, ezért a felhasználó az Azure AD között. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a RFPIO közötti kapcsolat kapcsolatot kell létrehozni.

RFPIO, rendelje hozzá a értékének **felhasználónév** értékeként Azure AD-ben **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a RFPIO tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**– ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#creating-an-azure-ad-test-user)**--az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[RFPIO tesztfelhasználó létrehozása](#creating-a-rfpio-test-user)**  --való Britta Simon valami RFPIO, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assigning-the-azure-ad-test-user)**--az Azure AD egyszeri bejelentkezéshez használandó Britta Simon engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  --működik, ha a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az RFPIO alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés RFPIO, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **RFPIO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Az a **RFPIO tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím:`https://www.rfpio.com`

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Ellenőrizze **megjelenítése speciális URL-beállításainak**.

    c. Az a **továbbítási állapotot** szövegmezőben adjon meg egy karakterláncértéket. Ügyfél [RFPIO támogatási csoport](https://www.rfpio.com/contact/) lekérni ezt az értéket. 

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód: 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím:`https://www.app.rfpio.com`

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. Egy másik webes böngészőablakban, jelentkezzen be a **RFPIO** webhely rendszergazdaként.

8. Kattintson az alsó bal sarok legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Kattintson a **szervezeti beállítások**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Kattintson a **szolgáltatások & integrációs**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. Az a **SAML SSO konfigurációs** kattintson **szerkesztése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. Ebben a szakaszban hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Másolja a **metaadatainak XML-kódja letöltött** és illessze be azt a **identitáskonfigurációs** mező.

    > [!NOTE]
    >Másolja a tartalmat a letöltött **metaadatainak XML-kódja** használata **Jegyzettömb ++** vagy megfelelő **XML-szerkesztő**. 

    b. Kattintson a **érvényesítése**.

    c. Miután rákattintott **érvényesítése**, tükrözés **SAML(Enabled)** a on.

    d. Kattintson a **nyújt**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-rfpio-test-user"></a>RFPIO tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók RFPIO bejelentkezni, akkor ki kell építenie a RFPIO.  
RFPIO, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a RFPIO vállalati webhely.

2. Kattintson az alsó bal sarok legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Kattintson a **szervezeti beállítások**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Kattintson a **CSAPATTAGOK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Kattintson a **tagok hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. Az a **új tagok hozzáadása** szakasz. Hajtsa végre az alábbi műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Adjon meg **E-mail cím** a a **adjon meg soronként egy e-mail** mező.

    b. Adja válassza **szerepkör** igényeinek megfelelően.

    c. Kattintson a **tagok hozzáadása**.
        
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés RFPIO Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése RFPIO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **RFPIO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha a hozzáférési panelen RFPIO csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az RFPIO alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

