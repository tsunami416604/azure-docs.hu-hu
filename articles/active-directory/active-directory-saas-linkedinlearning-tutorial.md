---
title: 'Oktatóanyag: Azure Active Directory-integráció LinkedIn Learning segítségével |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a LinkedIn tanulási között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b0db212b215d2d53b32437d703394148bcc4ac92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590276"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Oktatóanyag: Azure Active Directory-integráció LinkedIn Learning segítségével

Ebben az oktatóanyagban elsajátíthatja LinkedIn tanulási integrálása az Azure Active Directory (Azure AD).

LinkedIn tanulási integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a LinkedIn tanulási hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a LinkedIn tanulási (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs LinkedIn tanulási, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A LinkedIn tanulási egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből LinkedIn tanulási hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-linkedin-learning-from-the-gallery"></a>A gyűjteményből LinkedIn tanulási hozzáadása
Az Azure AD integrálása a LinkedIn tanulási konfigurálásához kell hozzáadnia LinkedIn tanulási a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből LinkedIn tanulási hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **LinkedIn tanulási**. Az eredmények panelen kattintson a **LinkedIn tanulási** hozzáadása az alkalmazáshoz.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés LinkedIn tanulási "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LinkedIn szeretnének a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LinkedIn tanulási közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** LinkedIn szeretnének.

Az Azure AD az egyszeri bejelentkezés LinkedIn Learning segítségével tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[LinkedIn tanulási tesztfelhasználó létrehozása](#creating-a-linkedin-learning-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez LinkedIn tanulási alkalmazásában.

**Konfigurálása az Azure AD az egyszeri bejelentkezés LinkedIn tanulási, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **LinkedIn tanulási** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. Egy másik webes böngészőablakban bejelentkezés a LinkedIn tanulási bérlő rendszergazdaként.

4. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje ki, **tanulási - alapértelmezett** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Kattintson a **, vagy kattintson ide betölteni, és másolja az űrlap egyes mezőket** , és másolja **entitásazonosító** és **helyességi feltétel felhasználói hozzáférést (ACS) URL-címe**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. Az Azure-portál a **LinkedIn tanulási tartomány és az URL-címek**, hajtsa végre az alábbi lépéseket, ha azt szeretné, hogy az egyszeri bejelentkezés konfigurálása **IdP kezdeményezett** mód

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. A a **azonosító** szövegmező, adja meg a **Entitásazonosító** másolt LinkedIn portálról 

    b. A a **válasz URL-CÍMEN** szövegmező, adja meg a **helyességi feltétel felhasználói hozzáférést (ACS) URL-cím** másolt LinkedIn portálról

7. Ha azt szeretné, hogy az egyszeri bejelentkezés konfigurálása **Szolgáltató kezdeményezett**, majd kattintson a speciális URL-cím megjelenítése a konfigurációs szakaszban beállítás, és a bejelentkezési URL-cím konfigurálása a következő mintát:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. A LinkedIn tanulási alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Az alábbi képernyőfelvételen látható egy példa a. Az alapértelmezett érték **felhasználói azonosító** van **user.userprincipalname** de LinkedIn tanulási vár a képezhető le a felhasználó e-mail címmel. Az adott használhatja **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket a szervezet konfiguráció alapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. A **felhasználói attribútumok** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. A felhasználónak kell nevű négy jogcímeket adhatnak hozzá **e-mail**, **részleg**, **Keresztnév**, és **Vezetéknév** , és le kell képezni a értéke **user.mail**, **felhasználó.részleg**, **user.givenname**, és **user.surname** kulcsattribútumokkal.

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | e-mailben| user.mail |    
    | részleg| felhasználó.részleg |
    | Utónév| User.givenName |
    | Vezetéknév| User.surname |
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)
    
    a. Kattintson a **attribútum hozzáadása** a attribútum párbeszédpanel megnyitásához.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_04.png)

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

10. Hajtsa végre a következő lépéseket a **neve** attribútum -

    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinLearning-tutorial/url_update.png)

    b. Az URL-cím érték törlése a **névtér**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.

11. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

12. Kattintson a **Save** (Mentés) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

13. Ugrás a **LinkedIn rendszergazdai beállítások** szakasz. A feltöltés XML-fájl lehetőségre kattintva Azure-portálról letöltött XML-fájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Kattintson a **a** SSO engedélyezése. Egyszeri bejelentkezés állapota a **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-linkedin-learning-test-user"></a>LinkedIn tanulási tesztfelhasználó létrehozása

LinkedIn tanulási alkalmazás támogatja közvetlenül az az idő a felhasználók átadása, miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejönnek. A rendszergazda a beállítások lapon a a LinkedIn tanulási portál tükrözés a kapcsoló **automatikus hozzárendelése licencek** időben aktív közvetlenül az üzembe helyezési, és ez lesz is rendel egy licencet a felhasználó. További részletei, LinkedIn tanulási is támogatja a felhasználók automatikus átadása [Itt](active-directory-saas-linkedinlearning-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

   ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LinkedIn Learning Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése LinkedIn tanulási, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **LinkedIn tanulási**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel LinkedIn tanulási mozaik gombra kattint, szerezheti be az Azure bejelentkezési oldalra, és a sikeres bejelentkezést, után szerezheti be az LinkedIn tanulási alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-linkedinlearning-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png