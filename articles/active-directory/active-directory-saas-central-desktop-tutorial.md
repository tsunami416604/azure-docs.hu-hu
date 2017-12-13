---
title: "Oktatóanyag: Azure Active Directoryval integrált központi asztali |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a központi asztali között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 62f1a1e2193d9693519bdea86196cf296d4b9780
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Azure Active Directory-integráció központi asztal

Ebben az oktatóanyagban elsajátíthatja központi asztali integrálása az Azure Active Directory (Azure AD).

Központi asztali integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér központi asztali szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett központi asztalra (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a központi asztal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy központi asztali egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Központi asztali hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-central-desktop-from-the-gallery"></a>Központi asztali hozzáadása a gyűjteményből
Az Azure AD integrálása a központi asztali konfigurálásához kell hozzáadnia központi asztali a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a központi asztali a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **központi asztali**, jelölje be **központi asztali** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában központi asztali](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján központi asztali.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó központi asztali a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a központi asztali közötti kapcsolat kapcsolatot kell létrehozni.

A központi asztali, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a központi Desktop tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy központi asztali tesztfelhasználó](#create-a-central-desktop-test-user)**  - való egy megfelelője a Britta Simon központi asztali, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a központi asztali alkalmazások konfigurálása egyszeri bejelentkezéshez.

**A központi asztal konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **központi asztali** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Az a **központi asztali tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk központi asztali tartomány és URL-címek](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.centraldesktop.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [központi asztali ügyfél-támogatási csoport](https://imeetcentral.com/contact-us) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. A a **központi asztali konfigurálása** kattintson **központi asztal konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Központi asztali konfigurálása](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Jelentkezzen be a **központi asztali** bérlő.

8. Nyissa meg a **beállítások**, kattintson a **speciális**, és kattintson a **egyszeri bejelentkezés**.

    ![A telepítő - speciális](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "telepítő – speciális")

9. Az a **egyetlen bejelentkezést a beállítások** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés beállításai](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "egyszeri bejelentkezés beállításai")
    
    a. Válassza ki **engedélyezése SAML v2 az egyszeri bejelentkezés**.
    
    b. Az a **egyszeri bejelentkezési URL-cím** szövegmező, illessze be a **SAML Entitásazonosító** érték, amely az Azure portálról másolta.
    
    c. Az a **Egyszeri bejelentkezési URL-cím** szövegmező, illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure portálról másolta.
    
    d. Az a **SSO kijelentkezési URL-cím** szövegmező, illessze be a **Sign-Out URL-cím** érték, amely az Azure portálról másolta.

10. Az a **üzenet aláírás-ellenőrzési módszer** területen tegye a következőket:

    ![Üzenet-aláírást ellenőrzési módszer](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "üzenet-aláírást ellenőrzési módszer") egy. Válassza ki **tanúsítvány**.
    
    b. Az a **SSO tanúsítvány** listáról válassza ki **RSH SHA256**.
    
    c. A Jegyzettömb alkalmazásban nyissa meg a letöltött tanúsítvány, tanúsítvány tartalmát másolja és illessze be azt a **SSO tanúsítvány** mező.
        
    d. Válassza ki **jeleníti meg a hivatkozást a SAMLv2 bejelentkezési**.
    
    e. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-central-desktop-test-user"></a>Központi asztali tesztfelhasználó létrehozása

Az Azure Active Directory-felhasználók jelentkezhetnek be kell hogy ki kell építenie a központi asztali alkalmazások. Ez a szakasz ismerteti az Azure AD-felhasználói fiókok létrehozása a központi asztali.

> [!NOTE]
> Központi asztali felhasználói fiók létrehozása eszközei vagy központi asztali által nyújtott API-kat használhatja az Azure AD-felhasználói fiókok létrehozásához

**Felhasználói fiókok központi asztalra telepítéséhez:**

1. Jelentkezzen be a központi asztali bérlő.

2. Ugrás a **személyek \> belső tagok**.

3. Kattintson a **belső tagok felvétele**.

    ![Személyek](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "személyek")
    
4. Az a **E-mail címet az új tagok** szövegmező, írja be az Azure AD-fiókot rendelkezés szeretne, és kattintson a **következő**.

    ![E-mail-címeket az új tagok](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "E-mail címet az új tagok")

5. Kattintson a **hozzáadása belső tag**.

    ![Adja hozzá a belső tag](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "belső tag hozzáadása")
   
   >[!NOTE]
   >A hozzáadott felhasználók kattintson a fiók aktiválásához szükséges megerősítési hivatkozást tartalmazó e-mailt fog kapni.
   
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés központi asztali Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése központi asztali, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **központi asztali**.

    ![Az alkalmazások listáját a központi asztali hivatkozás](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen központi asztali csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a központi asztali alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

