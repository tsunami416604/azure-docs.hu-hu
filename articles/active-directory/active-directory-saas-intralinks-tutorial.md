---
title: "Oktatóanyag: Azure Active Directoryval integrált Intralinks |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Intralinks között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: a1aee1b1bb039f8956c4ce3722b98db9af737f0f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Oktatóanyag: Azure Active Directoryval integrált Intralinks

Ebben az oktatóanyagban elsajátíthatja Intralinks integrálása az Azure Active Directory (Azure AD).

Intralinks integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Intralinks hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Intralinks (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Intralinks, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Intralinks egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Intralinks hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-intralinks-from-the-gallery"></a>A gyűjteményből Intralinks hozzáadása
Az Azure AD integrálása a Intralinks konfigurálásához kell hozzáadnia Intralinks a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Intralinks hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Intralinks**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_search.png)

5. Az eredmények panelen válassza ki a **Intralinks**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Intralinks.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Intralinks a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Intralinks közötti kapcsolat kapcsolatot kell létrehozni.

Intralinks, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Intralinks tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Intralinks tesztfelhasználó létrehozása](#creating-an-intralinks-test-user)**  - való Britta Simon valami Intralinks, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Intralinks alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Intralinks, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Intralinks** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_samlbase.png)

3. Az a **Intralinks tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Intralinks ügyfél-támogatási csoport](https://www.intralinks.com/contact-1) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_general_400.png)

6. Egyszeri bejelentkezés konfigurálása **Intralinks** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** [Intralinks támogatási csoport](https://www.intralinks.com/contact-1). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-intralinks-test-user"></a>Egy Intralinks tesztfelhasználó létrehozása

Ebben a szakaszban egy Intralinks Britta Simon nevű felhasználót hoz létre. Adjon együttműködve [Intralinks támogatási csoport](https://www.intralinks.com/contact-1) a felhasználók hozzáadása a Intralinks platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Intralinks Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Intralinks, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Intralinks**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="add-intralinks-via-or-elite-application"></a>Intralinks keresztül vagy a Elite alkalmazás hozzáadása

Intralinks összes alkalmazáshoz más Intralinks üzlet Nexus alkalmazás kivételével azonos egyszeri bejelentkezési identitás platformot használ. Ezért ha egyetlen más Intralinks alkalmazáshoz használni kívánt majd először létre kell egyszeri bejelentkezés konfigurálása egy elsődleges Intralinks alkalmazáshoz a fent leírt eljárás segítségével.

Ezután kövesse az alábbi eljárás egy másik Intralinks alkalmazás hozzáadása az Ön bérlőjében, amelyek kihasználhatják az egyszeri bejelentkezés az elsődleges alkalmazás. 

>[!NOTE]
>Ez a funkció csak az Azure AD Premium Termékváltozat használó ügyfelek számára elérhető és nem érhető el az ingyenes vagy alapszintű Termékváltozat ügyfelek.

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]


2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Intralinks**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_search.png)

5. A **alkalmazás Intralinks hozzáadása** hajtsa végre a következő lépéseket:

    ![Intralinks keresztül vagy a Elite alkalmazás hozzáadása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. A **neve** szövegmező, írja be például az alkalmazás megfelelő neve **Intralinks Elite**.

    b. Kattintson a **Hozzáadás** gombra.

6.  Az Azure portálon a a **Intralinks** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

7. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **társított bejelentkezés**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

8. Beolvasása a SP által kezdeményezett egyszeri bejelentkezési URL-CÍMÉT a [Intralinks team](https://www.intralinks.com/contact-1) más Intralinks alkalmazásához, és adja meg a **konfigurálása bejelentkezési URL-cím** alább látható módon. 
    
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     Az URL-cím bejelentkezési szövegmezőben írja be való bejelentkezéshez az Intralinks alkalmazáshoz a következő minta használatával a felhasználók által használt URL-cím:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

9. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-intralinks-tutorial/tutorial_general_400.png)

10. Rendelje hozzá az alkalmazás a felhasználók vagy csoportok, ahogy az a szakasz  **[hozzárendelése az Azure AD-teszt felhasználó](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Intralinks csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Intralinks alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png

