---
title: "Oktatóanyag: Azure Active Directoryval integrált Boomi |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Boomi között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 1121d22beddf73fd2109a4b410422f76dd37478e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Oktatóanyag: Azure Active Directoryval integrált Boomi

Ebben az oktatóanyagban elsajátíthatja Boomi integrálása az Azure Active Directory (Azure AD).

Boomi integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Boomi hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Boomi (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Boomi, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Boomi egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Boomi hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-boomi-from-the-gallery"></a>A gyűjteményből Boomi hozzáadása
Az Azure AD integrálása a Boomi konfigurálásához kell hozzáadnia Boomi a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Boomi hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Boomi**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_search.png)

5. Az eredmények panelen válassza ki a **Boomi**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Boomi.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Boomi a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Boomi közötti kapcsolat kapcsolatot kell létrehozni.

Boomi, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Boomi tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Boomi tesztfelhasználó létrehozása](#creating-a-boomi-test-user)**  - való Britta Simon valami Boomi, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Boomi alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Boomi, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Boomi** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. Az a **Boomi tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://platform.boomi.com/sso/<accountname>/saml`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://platform.boomi.com/sso/<accountname>/saml`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Boomi támogatási csoport](https://boomi.com/company/contact/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png)

4. Boomi alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanel, az alábbi táblázatban szereplő minden egyes sorára hajtsa végre a következő lépéseket:

    | Attribútum neve | Attribútum-érték |
    | -------------- | --------------- |
    | FEDERATION_ID | User.mail |
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

7. A a **Boomi konfigurációs** kattintson **konfigurálása Boomi** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a Boomi vállalati webhely rendszergazdaként. 

9. Navigáljon a **vállalatnév** , és navigáljon **beállítása**.

10. Kattintson a **egyszeri bejelentkezési beállítások** lapra, és hajtsa végre a következő lépések.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Ellenőrizze **engedélyezése SAML-alapú egyszeri bejelentkezést** jelölőnégyzetet.

    b. Kattintson a **importálási** az Azure AD-be a letöltött tanúsítvány feltöltése **szolgáltató Identitástanúsítvány**.
    
    c. Az a **Identity Provider bejelentkezési URL-cím** szövegmező, írja be az értéket a **SAML-alapú egyszeri bejelentkezési URL-címe** az Azure AD alkalmazás-konfigurációs ablakban.

    d. Mint **összevonási azonosító hely**, jelölje be **összevonási azonosító FEDERATION_ID attribútum elem van** választógombot. 

    e. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-boomi-test-user"></a>Boomi tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Boomi bejelentkezni, akkor ki kell építenie a Boomi. Boomi, ha egy kézi tevékenység.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként a Boomi vállalati webhely.

2. A bejelentkezés után navigáljon **felhasználókezelés** , és navigáljon **felhasználók**.

    ![Felhasználók](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "felhasználók")

3. Kattintson a  **+**  ikon és a **Add/karbantartása felhasználói szerepkörök** párbeszédpanel nyílik meg.

    ![Felhasználók](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "felhasználók")

    ![Felhasználók](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "felhasználók")

    a. Az a **felhasználó e-mail címe** szövegmezőben, az e-mailt a felhasználó típusát, például BrittaSimon@contoso.com.
    
    b. Az a **Keresztnév** szövegmezőhöz felhasználói Britta például az első nevét.

    c. Az a **Vezetéknév** szövegmező, írja be például Simon felhasználó vezetékneve.
    
    d. Adja meg a felhasználó **összevonási azonosító**. Minden felhasználónak rendelkeznie kell egy összevonási azonosító, amely egyedileg azonosítja a felhasználó a fiókon belül.
    
    e. Rendelje hozzá a **általános jogú felhasználó** a felhasználói szerepkört. Nem rendelhető hozzá, a rendszergazda szerepkör mert, amely ad neki normál légkör hozzáférés, valamint az egyszeri bejelentkezéses hozzáférést.
    
    f. Kattintson az **OK** gombra.
    
    > [!NOTE]
    > A felhasználó nem kap meg az üdvözlő e-mailben értesítést tartalmazó egy jelszót, amely használható a AtomSphere fiók bejelentkezni, mert az identitásszolgáltató kezeli a jelszavát. Bármely más Boomi felhasználói fiók létrehozása eszközt, vagy rendelkezés AAD felhasználói fiókokhoz Boomi által nyújtott API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Boomi Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Boomi, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Boomi**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Boomi csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Boomi alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png

