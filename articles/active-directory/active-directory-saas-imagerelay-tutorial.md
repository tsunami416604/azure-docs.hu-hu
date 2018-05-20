---
title: 'Oktatóanyag: Azure Active Directoryval integrált kép továbbítási |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a lemezkép továbbítási között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b4ed73ca669ede9c206abd653a0b991edc2b1063
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Oktatóanyag: Azure Active Directoryval integrált kép továbbító

Ebben az oktatóanyagban elsajátíthatja kép továbbítási integrálása az Azure Active Directory (Azure AD).

Kép továbbítási integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a kép továbbítási hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett kép irányítja (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD rendszerrel történő integráció konfigurálása kép továbbító, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- Egy kép továbbítási egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Kép továbbítási hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-image-relay-from-the-gallery"></a>Kép továbbítási hozzáadása a gyűjteményből
Az Azure AD integrálása a kép továbbítási konfigurálásához kell hozzáadnia kép továbbító a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a kép továbbító a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **kép továbbítási**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. Az eredmények panelen válassza ki a **kép továbbítási**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján kép továbbító.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a kép továbbítási megfelelőjére felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a kép továbbítási közötti kapcsolat kapcsolatot kell létrehozni.

Kép továbbítási, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a kép továbbítási tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy kép továbbítási tesztfelhasználó létrehozása](#creating-an-image-relay-test-user)**  - való egy megfelelője a Britta Simon kép továbbító, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a lemezkép Relay alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés konfigurálása kép továbbítási, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **kép továbbítási** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. Az a **kép továbbítási tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.imagerelay.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [kép továbbítási ügyfél-támogatási csoport](http://support.imagerelay.com/) beolvasni ezeket az értékeket. 
 


4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. A a **kép továbbítási konfigurációs** területén kattintson **konfigurálása kép továbbítási** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out szolgáltatás URL-CÍMÉT és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a kép továbbítási vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson a **felhasználók és engedélyek** munkaterhelés.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Kattintson a **hozzon létre új engedély**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. Az a **egyetlen bejelentkezést a beállítások** munkaterhelés, jelölje be a **csak bejelentkezés egyszeri bejelentkezés útján lehet ennek a csoportnak a** jelölőnégyzetet, majd kattintson a **mentése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Ugrás a **Fiókbeállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Lépjen a **egyetlen bejelentkezést a beállítások** munkaterhelés.
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. Az a **SAML beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. A **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    b. A **kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyetlen Sign-Out URL-címe** ami Azure-portálon másolta.

    c. Mint **név Azonosítóformátumának**, jelölje be **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.

    d. Mint **kötelező beállítások a szolgáltató (kép továbbító) a érkező kéréseket**, jelölje be **FELADÁS egy vagy több kötelező**.

    e. A **x.509 tanúsítvány**, kattintson a **frissítés tanúsítvány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. A letöltött tanúsítvány megnyitása a Jegyzettömbben, másolja a tartalmat, és illessze be az x.509 tanúsítvány szövegmező.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. A **fordítója Felhasználólétesítés** szakaszban jelölje be a **fordítója Felhasználólétesítés engedélyezése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Válassza ki az engedélycsoport (például **SSO alapvető**) amely megengedett csak keresztül egyszeri bejelentkezés bejelentkezni.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-image-relay-test-user"></a>Egy kép továbbítási tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon meghívta lemezképét továbbítási felhasználó létrehozásához.

**A felhasználó Britta Simon meghívta lemezképét továbbítási létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a kép továbbítási vállalati webhely rendszergazdaként.

2. Nyissa meg a **felhasználók és engedélyek** válassza **egyszeri Bejelentkezéses felhasználó létrehozása**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Adja meg a **E-mail**, **Utónév**, **Vezetéknév**, és **vállalati** ellátásához, majd válassza ki az engedélycsoport (például egyszeri bejelentkezés alapvető) amely a csoport, amely csak keresztül egyszeri bejelentkezés bejelentkezhessen a kívánt felhasználó.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés kép továbbítási Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Kép továbbítási Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **kép továbbítási**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.    

A hozzáférési panelen kép továbbítási csempére kattintva, meg kell beolvasása automatikusan bejelentkezett a kép továbbítási alkalmazásba.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

