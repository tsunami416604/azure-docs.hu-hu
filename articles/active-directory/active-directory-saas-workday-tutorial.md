---
title: "Oktatóanyag: Azure Active Directoryval integrált Workday |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Workday között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5e4d46f9a3954698fbbe3c80fd8a95f4cd87465b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Oktatóanyag: Azure Active Directoryval integrált munkanap

Ebben az oktatóanyagban elsajátíthatja Workday integrálása az Azure Active Directory (Azure AD).

Munkanapok integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Workday hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Workday (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkanapok, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Workday egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Munkanapok hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-workday-from-the-gallery"></a>Munkanapok hozzáadása a gyűjteményből
Az Azure AD integrálása a Workday konfigurálásához kell hozzáadnia Workday a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Workday a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Workday**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. Az eredmények panelen válassza ki a **Workday**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Workday "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Workday tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Workday közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Workday.

Az Azure AD egyszeri bejelentkezést a Workday tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Munkanapok tesztfelhasználó létrehozása](#creating-a-workday-test-user)**  - való Britta Simon egy megfelelője a Workday, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Workday-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezést a Workday megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Workday** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. Az a **Workday tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket, mint:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és a válasz URL-CÍMEN. A válasz URL-címe például altartomány kell rendelkeznie: www, wd2, wd3, wd3-impl, wd5, wd5-impl). Valami, amit például "*http://www.myworkday.com*" működik, de "*http://myworkday.com*" viszont nem. Ügyfél [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni ezeket az értékeket. 
 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. A a **Workday konfigurációs** kattintson **konfigurálása Workday** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. Egy másik webes böngészőablakban bejelentkezni a Workday vállalati webhely rendszergazdaként.

8. Ugrás a **menü \> munkaterület**.
   
    ![Munkaterület](./media/active-directory-saas-workday-tutorial/IC782923.png "munkaterület")

9. Ugrás a **felügyeleti fiók**.
   
    ![Felügyeleti fiók](./media/active-directory-saas-workday-tutorial/IC782924.png "felügyeleti fiók")

10. Ugrás a **bérlői beállításokat – biztonsági**.
   
    ![Bérlői biztonsági szerkesztése](./media/active-directory-saas-workday-tutorial/IC782925.png "bérlői biztonsági szerkesztése")

11. Az a **átirányítási URL-eket** területen tegye a következőket:
   
    ![Átirányítási URL-eket](./media/active-directory-saas-workday-tutorial/IC7829581.png "átirányítási URL-címek")
   
    a. Kattintson a **sort ad hozzá a**.
   
    b. Az a **bejelentkezési átirányítási URL-cím** szövegmező és a **Mobile átirányítási URL-cím** szövegmezőhöz típusa a **bejelentkezési URL-cím** megadott a **Workday tartomány és az URL-címek** szakasza az Azure-portálon.
   
    c. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **Sign-Out URL-cím**, majd illessze be azt a **kijelentkezési átirányítási URL-cím** szövegmező.
   
    d.  A **környezet** szövegmező, írja be a környezet nevét.  

    >[!NOTE]
    > A környezet attribútum értéke van kötve a bérlői URL-cím értéke:  
    >-Ha a tartomány neve a Workday-bérlői URL-cím kezdődik impl például: *https://impl.workday.com/\<bérlői\>/login-saml2.htmld*), a **környezet** attribútum Megvalósítási értékre kell állítani.  
    >-Ha a tartomány neve kezdődik valami mást, szeretné-e forduljon [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni a megfelelő **környezet** érték.

12. Az a **SAML-alapú telepítő** területen tegye a következőket:
   
    ![SAML-alapú telepítő](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML-alapú telepítő")
   
    a.  Válassza ki **SAML-alapú hitelesítés engedélyezéséhez**.
   
    b.  Kattintson a **sort ad hozzá a**.

13. A SAML-alapú identitás-szolgáltatóktól területen tegye a következőket:
   
    ![SAML-alapú identitás-szolgáltatóktól](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML-alapú identitás-szolgáltatóktól")
   
    a. Az identitás-szolgáltató neve szövegmezőben írja be a szolgáltató nevét (például: *SPInitiatedSSO*).
   
    b. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML Entitásazonosító** értékét, és illessze be azt a **kibocsátó** szövegmező.
   
    c. Válassza ki **engedélyezése a Workday kezdeményezett kijelentkezési**.
   
    d. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **Sign-Out URL-cím** értékét, és illessze be azt a **kijelentkezési kérelem URL-cím** szövegmező.

    e. Kattintson a **szolgáltató nyilvános kulcs Identitástanúsítvány**, és kattintson a **létrehozása**. 

    ![Hozzon létre](./media/active-directory-saas-workday-tutorial/IC782928.png "létrehozása")

    f. Kattintson a **x509 létrehozása nyilvános kulcs**. 

    ![Hozzon létre](./media/active-directory-saas-workday-tutorial/IC782929.png "létrehozása")


14. Az a **nézet x509 nyilvános kulcs** területen tegye a következőket: 
   
    ![Nézet x509 nyilvános kulcs](./media/active-directory-saas-workday-tutorial/IC782930.png "nézet x509 nyilvános kulcs") 
   
    a. Az a **neve** szövegmező, írja be a tanúsítvány nevét (például: *PPE\_SP*).
   
    b. Az a **érvényes a** szövegmező, írja be a tanúsítványhoz tartozó attribútum értéke érvénytelen.
   
    c.  Az a **érvényes való** szövegmező, írja be a tanúsítványhoz tartozó attribútum értéke érvénytelen.
   
    > [!NOTE]
    > Letölthető a érvényes dátumot és az érvényes, kattintson duplán a letöltött tanúsítvány az a dátum.  A dátumok találhatók a **részletek** fülre.
    > 
    >
   
    d.  Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, és másolja a tartalmát.
   
    e.  Az a **tanúsítvány** szövegmezőhöz a vágólap tartalmának beillesztése.
   
    f.  Kattintson az **OK** gombra.

15. Hajtsa végre a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurációs](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguráció")
   
    a.  Engedélyezze a **x509 titkos kulcspár**.
   
    b.  Az a **szolgáltatás Szolgáltatóazonosító** szövegmezőhöz típus **http://www.workday.com**.
   
    c.  Válassza ki **engedélyezése SP kezdeményezett SAML-alapú hitelesítés**.
   
    d.  Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** értékét, és illessze be azt a **IdP egyszeri bejelentkezési URL-címe** szövegmező.
   
    e. Válassza ki **nem Deflate a Szolgáltató által kezdeményezett hitelesítési kérelem**.
   
    f. Mint **hitelesítési kérelmek aláírási metódus**, jelölje be **SHA256**. 
   
    ![Hitelesítési kérelem aláírási metódus](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "hitelesítési kérelmek aláírás módja") 
   
    g. Kattintson az **OK** gombra. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-workday-test-user"></a>Munkanapok tesztfelhasználó létrehozása

Munkanapok át tesztfelhasználó beszerzéséhez kapcsolatba kell lépnie a [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html).
A [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) létrehozza, a felhasználó.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Workday Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Workday, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Workday**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

