---
title: "Oktatóanyag: Azure Active Directoryval integrált Intacct |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Intacct között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c203b192b9da0d280cbd7f6c123219242ee4a3d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Oktatóanyag: Azure Active Directoryval integrált Intacct

Ebben az oktatóanyagban elsajátíthatja Intacct integrálása az Azure Active Directory (Azure AD).

Intacct integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Intacct hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Intacct (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Intacct, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Intacct egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Intacct hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-intacct-from-the-gallery"></a>A gyűjteményből Intacct hozzáadása
Az Azure AD integrálása a Intacct konfigurálásához kell hozzáadnia Intacct a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Intacct hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Intacct**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_search.png)

5. Az eredmények panelen válassza ki a **Intacct**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Intacct.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Intacct a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Intacct közötti kapcsolat kapcsolatot kell létrehozni.

Intacct, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Intacct tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Intacct tesztfelhasználó létrehozása](#creating-an-intacct-test-user)**  - való Britta Simon valami Intacct, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Intacct alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Intacct, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Intacct** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_samlbase.png)

3. Az a **Intacct tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_url.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges válasz URL-címet. Ügyfél [Intacct támogatási csoport](https://us.intacct.com/support) lekérni ezt az értéket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_general_400.png)

6. A a **Intacct konfigurációs** kattintson **konfigurálása Intacct** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Intacct vállalati webhely rendszergazdaként.

8. Kattintson a **vállalati** fülre, majd **vállalati adatok**.

    ![Vállalati](./media/active-directory-saas-intacct-tutorial/ic790037.png "vállalati")

9. Kattintson a **biztonsági** fülre, majd **szerkesztése**.

    ![Biztonsági](./media/active-directory-saas-intacct-tutorial/ic790038.png "biztonsági")

10. Az a **egyszeri bejelentkezés (SSO)** területen tegye a következőket:

    ![Egyszeri bejelentkezés](./media/active-directory-saas-intacct-tutorial/ic790039.png "egyszeri bejelentkezés")

    a. Válassza ki **engedélyezése egyszeri bejelentkezéshez**.

    b. Mint **identitás szolgáltatótípus**, jelölje be **SAML 2.0**.

    c. A **kiállítójának URL-címe** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
   
    d. A **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    e. Nyissa meg a **base-64** kódolású Jegyzettömb-tanúsítványt, a tartalmának másolása a vágólapra és illessze be azt a **tanúsítvány** mezőbe.
   
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intacct-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-intacct-test-user"></a>Egy Intacct tesztfelhasználó létrehozása

Beállítása az Azure Active Directory-felhasználók, így Intacct való bejelentkezéshez, akkor ki kell építenie Intacct be. Intacct a kiépítés kézi tevékenység.

**Felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Intacct** bérlő.

2. Kattintson a **vállalati** fülre, majd **felhasználók**.

    ![Felhasználók](./media/active-directory-saas-intacct-tutorial/ic790041.png "felhasználók")
3. Kattintson a **Hozzáadás** fülre.

    ![Adja hozzá](./media/active-directory-saas-intacct-tutorial/ic790042.png "hozzáadása")
4. Az a **felhasználói adatok** területen tegye a következőket:

    ![Felhasználói adatok](./media/active-directory-saas-intacct-tutorial/ic790043.png "felhasználói adatok")

    a. Adja meg a **felhasználói azonosító**, a **Vezetéknév**, **Utónév**, a **E-mail cím**, a **cím**, és a **Phone** kiépítése a kívánt Azure AD-fiókot, a **felhasználói adatok** szakasz.

    b. Válassza ki a **rendszergazda jogosultságokkal** a kiépítés kívánt Azure AD-fiókot.
   
    c. Kattintson a **Save** (Mentés) gombra. Az Azure AD fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

>[!NOTE]
>Azure AD-felhasználói fiókok létrehozásához használhatja más Intacct felhasználói fiók létrehozása eszközök vagy Intacct által nyújtott API-k.
        
### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Intacct Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Intacct, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Intacct**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha a hozzáférési panelen Intacct csempére kattint, be kell automatikusan jelentkeznie az Intacct alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

