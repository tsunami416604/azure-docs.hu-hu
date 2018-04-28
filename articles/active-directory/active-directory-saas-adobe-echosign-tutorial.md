---
title: 'Oktatóanyag: Azure Active Directory-integráció Adobe bejelentkezési |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Adobe bejelentkezési között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 71aa0af2b3b47c1d9960e72aa36c2d5aae80f140
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Azure Active Directory-integráció Adobe bejelentkezési

Ebben az oktatóanyagban elsajátíthatja Adobe bejelentkezési integrálása az Azure Active Directory (Azure AD).

Az Adobe bejelentkezési integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér az Adobe bejelentkezési Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Adobe bejelentkezéshez (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Adobe bejelentkezési, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Adobe bejelentkezési egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Az Adobe bejelentkezési hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adobe-sign-from-the-gallery"></a>Az Adobe bejelentkezési hozzáadása a gyűjteményből
Az Azure AD integrálása a Adobe bejelentkezési konfigurálásához kell hozzáadnia az Adobe bejelentkezési a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Az Adobe hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Adobe bejelentkezési**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Az eredmények panelen válassza ki a **Adobe bejelentkezési**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Adobe bejelentkezési

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Adobe bejelentkezési a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Adobe bejelentkezési közötti kapcsolat kapcsolatot kell létrehozni.

Az Adobe bejelentkezési, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Adobe bejelentkezési tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Az Adobe bejelentkezési tesztfelhasználó létrehozása](#creating-an-adobe-sign-test-user)**  - való egy megfelelője a Britta Simon Adobe bejelentkezési, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az Adobe bejelentkezési alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés Adobe bejelentkezési megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Adobe bejelentkezési** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Az a **Adobe bejelentkezési tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.echosign.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Adobe bejelentkezési ügyfél-támogatási csoport](https://helpx.adobe.com/in/contact/support.html) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. A a **Adobe bejelentkezési konfigurációs** kattintson **konfigurálása Adobe bejelentkezési** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Adobe bejelentkezési vállalati webhely rendszergazdaként.

8. A SAML menüben kattintson a **Fiókbeállítások**, és kattintson a **SAML beállítások**.
   
    ![Fiók](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "fiók")

9. Az a **SAML beállítások** területen tegye a következőket:
  
    ![SAML-alapú beállítások](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
    a. Mint **SAML mód**, jelölje be **SAML kötelező**.
   
    b. Válassza ki **engedélyezése Adobe bejelentkezési fiókot a rendszergazdák a Adobe bejelentkezési hitelesítő adataival bejelentkezni**.
   
    c. Mint **felhasználó létrehozása**, jelölje be **keresztül SAML hitelesített felhasználók automatikus hozzáadása**.

    d. Beillesztés **SAML Entitásazonosító**, amely az Azure portálról másolta a **entitás azonosítója/kiállító URL-cím** szövegmező.
    
    e. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolta a **bejelentkezési URL-cím/egyszeri bejelentkezési végpont** szövegmező.
   
    f. Beillesztés **Sign-Out URL-cím**, amely az Azure portálról másolta a **kijelentkezési URL-cím/SLO végpont** szövegmező.

    g. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **IdP tanúsítvány** szövegmező

    h. Kattintson a **módosítások mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-adobe-sign-test-user"></a>Az Adobe bejelentkezési tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Adobe bejelentkezési bejelentkezni, akkor ki kell építenie az Adobe jelentkezzen be. Adobe bejelentkezési, ha egy kézi tevékenység.

>[!NOTE]
>Bármely más Adobe bejelentkezési felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Adobe bejelentkezési által nyújtott API-k. 

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Adobe bejelentkezési** vállalati hely rendszergazdaként.

2. A felső menüben kattintson **fiók**, és a bal oldali navigációs ablakában kattintson az **felhasználók és csoportok**, majd kattintson a **hozzon létre egy új felhasználót**.
   
    ![Fiók](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "fiók")
   
3. Az a **új felhasználó létrehozása** területen tegye a következőket:
   
    ![Hozzon létre felhasználói](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "felhasználó létrehozása")
   
    a. Típus a **E-mail cím**, **Utónév**, és **Vezetéknév** szeretné azokat a kapcsolódó szövegmezők rendelkezés érvényes AAD-fiók.
   
    b. Kattintson a **létrehozza a felhasználó**.

>[!NOTE]
>Az Azure Active Directory fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Adobe bejelentkezési Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Az Adobe bejelentkezési Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Adobe bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen Adobe bejelentkezési csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Adobe bejelentkezési alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png

