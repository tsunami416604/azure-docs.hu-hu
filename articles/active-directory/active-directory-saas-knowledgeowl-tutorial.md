---
title: 'Oktatóanyag: Azure Active Directoryval integrált KnowledgeOwl |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és KnowledgeOwl között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 1ac63d512645b2eb9627798809ce699129799c94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Oktatóanyag: Azure Active Directoryval integrált KnowledgeOwl

Ebben az oktatóanyagban elsajátíthatja KnowledgeOwl integrálása az Azure Active Directory (Azure AD).

KnowledgeOwl integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér KnowledgeOwl szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett KnowledgeOwl (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs KnowledgeOwl, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy KnowledgeOwl egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből KnowledgeOwl hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-knowledgeowl-from-the-gallery"></a>A gyűjteményből KnowledgeOwl hozzáadása
Az Azure AD integrálása a KnowledgeOwl konfigurálásához kell hozzáadnia KnowledgeOwl a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből KnowledgeOwl hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **KnowledgeOwl**, jelölje be **KnowledgeOwl** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján KnowledgeOwl.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó KnowledgeOwl a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a KnowledgeOwl közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a KnowledgeOwl tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[KnowledgeOwl tesztfelhasználó létrehozása](#create-a-knowledgeowl-test-user)**  - való Britta Simon valami KnowledgeOwl, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az KnowledgeOwl alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés KnowledgeOwl, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **KnowledgeOwl** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Az a **KnowledgeOwl tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk KnowledgeOwl tartomány és az URL-címek](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk KnowledgeOwl tartomány és az URL-címek](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Ezek az értékek nincsenek valós. Ezek a tényleges azonosítóját, a válasz URL-CÍMEN és a bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett érték frissítése lesz szüksége.

5. A KnowledgeOwl alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-knowledgeowl-tutorial/attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke | Névtér|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d.From a **Namespace** listában, adja meg a névtér értéke az adott sorhoz feltüntetett.
    
    e. Kattintson az **OK** gombra.

7. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_400.png)
    
9. A a **KnowledgeOwl konfigurációs** kattintson **konfigurálása KnowledgeOwl** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![KnowledgeOwl konfiguráció](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. Egy másik webes böngészőablakban jelentkezzen be a KnowledgeOwl vállalati webhely rendszergazdaként.

11. Kattintson a **beállítások** majd **biztonsági**.

    ![KnowledgeOwl konfiguráció](./media/active-directory-saas-knowledgeowl-tutorial/configure1.png)

12. Görgessen lefelé, amely a **SAML SSO integrációs** , és hajtsa végre a következő lépéseket:
    
    ![KnowledgeOwl konfiguráció](./media/active-directory-saas-knowledgeowl-tutorial/configure2.png)

    a. Válassza ki **SAML SSO engedélyezése**.

    b. Másolás a **SP Entitásazonosító** értékét, és illessze be azt a **azonosítója (entitás azonosítója)** a a **KnowledgeOwl tartomány és az URL-címek** szakaszban az Azure portálon.

    c. Másolás a **SP bejelentkezési URL-cím** értékét, és illessze be azt a **bejelentkezés és válasz URL-címe** a szövegmezők a **KnowledgeOwl tartomány és az URL-címek** szakaszban az Azure portálon.

    d. Az a **IdP entityid beállítást** szövegmező, illessze be a **SAML Entitásazonosító** értéket, amely az Azure portálról másolta.

    e. Az a **IdP bejelentkezési URL-cím** szövegmező, illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** értéket, amely az Azure portálról másolta.

    f. Az a **IdP kijelentkezési URL-cím** szövegmező, illessze be a **Sign-Out URL-cím** értéket, amely az Azure portálról másolta

    g. A letöltött tanúsítvány formájában az Azure-portálon feltölthet a **IdP tanúsítvány feltöltése**.

    h. Kattintson a **térkép SAML attribútumok** attribútumok hozzárendelését, és hajtsa végre a következő lépéseket:
    
    ![KnowledgeOwl konfiguráció](./media/active-directory-saas-knowledgeowl-tutorial/configure3.png)

    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` azokat a **egyszeri bejelentkezési azonosító** szövegmező
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` azokat a **felhasználónév vagy E-mail** szövegmező.
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` azokat a **Keresztnév** szövegmező.
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` azokat a **Vezetéknév** szövegmező.
    * Kattintson a **Mentés** gombra.

    i. Kattintson a lap alján található **Mentés** gombra.

    ![KnowledgeOwl konfiguráció](./media/active-directory-saas-knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-knowledgeowl-test-user"></a>KnowledgeOwl tesztfelhasználó létrehozása

Ez a szakasz célja KnowledgeOwl Britta Simon nevű felhasználót létrehozni. KnowledgeOwl támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az KnowledgeOwl elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [KnowledgeOwl támogatási csoport](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés KnowledgeOwl Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése KnowledgeOwl, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **KnowledgeOwl**.

    ![Az alkalmazások listáját a KnowledgeOwl hivatkozás](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen KnowledgeOwl csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az KnowledgeOwl alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_203.png

