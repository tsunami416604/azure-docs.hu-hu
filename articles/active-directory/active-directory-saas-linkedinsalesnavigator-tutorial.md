---
title: 'Oktatóanyag: Azure Active Directoryval integrált LinkedInSalesNavigator |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és LinkedInSalesNavigator között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: 2c8dcf4ad49b8f261814c2d442bfdded221e8cbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349933"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Oktatóanyag: Azure Active Directory-integráció a LinkedIn értékesítési Navigator

Ebben az oktatóanyagban elsajátíthatja LinkedIn értékesítési Navigator integrálása az Azure Active Directory (Azure AD).

LinkedIn értékesítési Navigator integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a LinkedIn értékesítési Navigator hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a LinkedIn értékesítési Navigator (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha szeretné tudni, hogy az Azure AD SaaS integrálásáról további információkat, keresse meg a [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a LinkedIn értékesítési Navigator, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A LinkedIn értékesítési Navigator egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Kerülje az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből LinkedIn értékesítési Navigator hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>A gyűjteményből LinkedIn értékesítési Navigator hozzáadása
Az Azure AD integrálása a LinkedIn értékesítési Navigator konfigurálásához kell hozzáadnia LinkedIn értékesítési Navigator a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből LinkedIn értékesítési Navigator hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **LinkedIn értékesítési Navigator**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. Az eredmények panelen válassza ki a **LinkedIn értékesítési Navigator**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés LinkedIn értékesítési Navigator "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LinkedIn értékesítési Navigator a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LinkedIn értékesítési Navigator közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** LinkedIn értékesítési Navigator.

Az Azure AD egyszeri bejelentkezést a LinkedIn értékesítési Navigator tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[LinkedIn értékesítési Navigator tesztfelhasználó létrehozása](#creating-a-linkedin-sales-navigator-test-user)**  - való egy megfelelője a Britta Simon LinkedIn értékesítési Navigator, amely csatolva van a felhasználó az Azure AD ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az értékesítési Navigator LinkedIn-alkalmazásban.

**Az Azure AD egyszeri bejelentkezést a LinkedIn értékesítési Navigator megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **LinkedIn értékesítési Navigator** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen, a **mód** válasszon **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. Egy másik webes böngészőablakban bejelentkezés a **LinkedIn értékesítési Navigator** webhely rendszergazdaként.

4. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje ki, **értékesítési Navigator** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Kattintson a **, vagy kattintson ide betölteni, és másolja az űrlap egyes mezőket** , és másolja **entitásazonosító** és **helyességi feltétel felhasználói hozzáférést (ACS) URL-cím**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. Az Azure-portál a **LinkedIn értékesítési Navigator tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. A a **azonosító** szövegmező, adja meg a **Entitásazonosító** másolt LinkedIn portálról 

    b. A a **válasz URL-CÍMEN** szövegmező, adja meg a **helyességi feltétel felhasználói hozzáférést (ACS) URL-cím** másolt LinkedIn portálról

7. Ellenőrizze **megjelenítése speciális URL-beállításainak**, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. A **LinkedIn értékesítési Navigator** alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, amelyhez adja hozzá az egyéni attribútum-leképezésekhez SAML-jogkivonat attribútumok konfigurációjához. Az alábbi képernyőfelvételen szereplő példán látható. Az alapértelmezett érték **felhasználói azonosító** van **user.userprincipalname** de LinkedIn értékesítési Navigator vár képezhető le a felhasználó e-mail címmel. Használhat **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket a szervezet konfiguráció alapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. A **felhasználói attribútumok** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. A felhasználónak kell nevű négy jogcímeket adhatnak hozzá **e-mail**, **részleg**, **Keresztnév**, és **Vezetéknév** , és le kell képezni a értéke **user.mail**, **felhasználó.részleg**, **user.givenname**, és **user.surname** kulcsattribútumokkal.

    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mailben| user.mail |
    | részleg| felhasználó.részleg |
    | Utónév| User.givenName |
    | Vezetéknév| User.surname |
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Kattintson a **attribútum hozzáadása** a attribútum párbeszédpanel megnyitásához.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

10. Hajtsa végre a következő lépéseket a **neve** attribútum -

    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/url_update.png)

    b. Az URL-cím érték törlése a **névtér**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.

11. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Ugrás a **LinkedIn rendszergazdai beállítások** szakasz. Kattintson a **feltöltése XML-fájl** Azure-portálról letöltött metaadatok XML-fájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Kattintson a **a** SSO engedélyezése. Egyszeri bejelentkezés állapota a **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>LinkedIn értékesítési Navigator tesztfelhasználó létrehozása

Csatolt értékesítési Navigator alkalmazás támogatja közvetlenül az az idő szerinti (JIT) a felhasználók átadása, miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejönnek. Aktiválása **automatikusan a szükséges licencek kiosztása** licenc hozzárendelése a felhasználóhoz.
   
   ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LinkedIn értékesítési Navigator Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése LinkedIn értékesítési Navigator, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **LinkedIn értékesítési Navigator**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen LinkedIn értékesítési Navigator csempére kattint, szervezeti oldalra, ahol meg kell adnia a személyes LinkedIn fiók adatait a rendszer átirányítja. A személyes fiókot a LinkedIn üzleti fiókjával hivatkozik. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_203.png

