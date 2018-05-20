---
title: 'Oktatóanyag: Azure Active Directory-integráció Questetra BPM Suite |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Questetra BPM Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 122473da723cb101e0f0f9226b34aa3294477657
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Oktatóanyag: Azure Active Directory-integráció Questetra BPM csomaggal

Ebben az oktatóanyagban elsajátíthatja Questetra BPM Suite integrálása az Azure Active Directory (Azure AD).

Questetra BPM Suite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Questetra BPM Suite hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Questetra BPM Suite (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Questetra BPM csomaggal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Questetra BPM Suite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Questetra BPM csomag hozzáadása
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>A gyűjteményből Questetra BPM csomag hozzáadása
Az Azure AD integrálása a Questetra BPM Suite konfigurálásához kell hozzáadnia Questetra BPM Suite a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Questetra BPM Suite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Questetra BPM Suite**, jelölje be **Questetra BPM Suite** eredmény panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Adja hozzá a gyűjteményből](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Questetra BPM Suite "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Questetra BPM csomagban található a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Questetra BPM csomagban található hivatkozás kapcsolatának kell létrehozni.

Questetra BPM Suite, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Questetra BPM Suite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Questetra BPM Suite tesztfelhasználó létrehozása](#create-a-questetra-bpm-suite-test-user)**  - való egy megfelelője a Britta Simon Questetra BPM csomag, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Questetra BPM Suite alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Questetra BPM csomaggal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Questetra BPM Suite** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![SAML-alapú bejelentkezés](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. Az a **Questetra BPM Suite tartomány és az URL-címek** területen tegye a következőket:

    ![Questetra BPM Suite tartomány és az URL-címek szakasz](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Kaphat a következő két értéket **SP információk** a szakasz a **Questetra BPM Suite** vállalati helyre, amely később a oktatóanyag, vagy forduljon a [Questetra BPM Suite ügyfelekhez nyújtott támogatása Team](https://www.questetra.com/contact/). 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base 64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítványa szakasz](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. A a **Questetra BPM Suite konfigurációs** kattintson **Questetra BPM Suite konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Questetra BPM Suite konfigurációs szakasz](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a **Questetra BPM Suite** vállalati hely rendszergazdaként.

8. Kattintson a felső menüben **rendszerbeállítások**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][10]

9. Lehetőségre a **SingleSignOnSAML** kattintson **egyszeri Bejelentkezést (SAML)**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][11]

10. A a **Questetra BPM Suite** vállalati webhely, az a **SP információk** területen tegye a következőket:

    a. Másolás a **ACS URL-cím**, és illessze be azt a **URL-cím bejelentkezési** textbox a **Questetra BPM Suite tartomány és az URL-címek** rész az Azure-portálon.
    
    b. Másolás a **Entitásazonosító**, és illessze be azt a **azonosító** textbox a **Questetra BPM Suite tartomány és az URL-címek** rész az Azure-portálon.

11. Az a **Questetra BPM Suite** a vállalati webhely, az alábbi lépésekkel: 
   
    ![Egyszeri bejelentkezés konfigurálása][15]
   
    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.
   
    b. A **Entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
    
    c. A **bejelentkezési URL-címe** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
    
    d. A **kijelentkezési URL-címe** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
    
    e. Az a **NameID formátum** szövegmezőhöz típus `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Nyissa meg a **Base-64** kódolt tanúsítvány a Jegyzettömbben az Azure portálról letöltött, annak tartalmának másolása a vágólapra és illessze be azt a **érvényesítési tanúsítvány** szövegmező. 

    g. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Questetra BPM Suite tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Questetra BPM Suite nevű felhasználót létrehozni.

**A felhasználó Britta Simon Questetra BPM Suite nevű létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Questetra BPM Suite vállalati webhely.
2. Ugrás a **Rendszerbeállítások > Felhasználólista > Új felhasználó**. 
3. Új felhasználó párbeszédpanelen hajtsa végre a következő lépéseket: 
   
    ![Tesztfelhasználó létrehozása][300] 
   
    a. Az a **neve** szövegmezőhöz típus **neve** felhasználó **britta.simon@contoso.com**.
   
    b. Az a **E-mail** szövegmezőhöz típus **e-mail** felhasználó **britta.simon@contoso.com**
   
    c. Az a **jelszó** szövegmező, adjon meg egy **jelszó** felhasználó.
    
    d. Kattintson a **új felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Questetra BPM Suite Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Questetra BPM Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Questetra BPM Suite**.

    ![Questetra BPM Suite alkalmazások listájában](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Questetra BPM Suite csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Questetra BPM Suite alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

