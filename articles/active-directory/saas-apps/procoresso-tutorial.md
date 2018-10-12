---
title: 'Oktatóanyag: Azure Active Directory-integráció Procore SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Procore egyszeri bejelentkezés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: ffc1e0d66761700cb0da1f998c2639afc5abb2c4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114056"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Azure Active Directory-integráció Procore SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan Procore SSO integrálása az Azure Active Directory (Azure AD).

Procore SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Procore egyszeri bejelentkezés az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Procore egyszeri bejelentkezés (egyszeri bejelentkezés)
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Procore SSO-val, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Procore SSO egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Procore SSO hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO hozzáadása a katalógusból
Procore SSO integrálása az Azure AD beállítása, hozzá kell Procore SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Procore SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Procore SSO**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/tutorial_procoresso_search.png)

1. Az eredmények panelen válassza ki a **Procore SSO**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a teszt "Britta Simon" nevű felhasználó Procore SSO-val az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Procore egyszeri bejelentkezés a mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a kapcsolódó felhasználó Procore egyszeri bejelentkezési hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Procore egyszeri bejelentkezés az.

Az Azure AD egyszeri bejelentkezés Procore SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Procore SSO tesztfelhasználó létrehozása](#creating-a-procore-sso-test-user)**  – van egy megfelelője a Britta Simon Procore SSO, amely kapcsolódik az Azure ad-ben ábrázolása számára.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és Procore SSO-alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Procore SSO-val, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **Procore SSO** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

1. Az a **Procore SSO-tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_url.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_general_400.png)

1. Az a **Procore egyszeri bejelentkezési konfiguráció** területén kattintson **Procore SSO konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Procore SSO** ügyféloldali, jelentkezzen be rendszergazdaként procore vállalat webhelye.

1. Az eszközkészlet legördülő lefelé, kattintson a **rendszergazdai** az egyszeri bejelentkezés beállításai lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_tool_admin.png)

1. Illessze be az értékeket a mezőkben, amint az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Az a **egyszeri bejelentkezést a kibocsátó URL-cím** mezőbe, illessze be az Azure Portalról másolt az SAML-entitás azonosítója.

    b. Az a **SAML bejelentkezési cél URL-cím** box, a SAML egyszeri bejelentkezési szolgáltatás URL-cím az Azure Portalról másolt beillesztése.

    c. Ezután nyissa meg a **metaadatainak XML** az Azure Portalról a fent letöltött és másolja a tanúsítványt a kódban nevű **X509Certificate**. Illessze be a másolt érték a **az egyszeri bejelentkezést x509 tanúsítvány** mezőbe.

1. Kattintson a **módosítások mentése**.

1. Ezek a beállítások után meg kell küldenie a a **tartománynév** (például: **contoso.com**) keresztül, amely jelentkezik be a Procore a [Procore támogatási csapatának](https://support.procore.com/) és csatlakoznak az aktiválja az összevont egyszeri bejelentkezés ahhoz a tartományhoz.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-procore-sso-test-user"></a>Procore SSO tesztfelhasználó létrehozása

Kérjük, kövesse az alábbi lépéseket a saját oldalán Procore tesztfelhasználó létrehozása.

1. Jelentkezzen be rendszergazdaként procore vállalat webhelye.  

1. Az eszközkészlet legördülő lefelé, kattintson a **Directory** a vállalati címtár lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_sso_directory.png)

1. Kattintson a **adjon hozzá egy személy** nyissa meg a képernyőt, és adja meg a beállítás hajtsa végre a következő beállítások -

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_add.png)

    a. Az a **Utónév** szövegmezőbe írja be a felhasználó első neve például **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be a felhasználó vezetékneve például **Simon**.

    c. Az a **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például **BrittaSimon@contoso.com**.

    d. Válassza ki **jogosultsági sablon** , **jogosultsági sablon alkalmazása később**.

    e. Kattintson a **Create** (Létrehozás) gombra.

1. Ellenőrizze, és az újonnan hozzáadott kapcsolattartó adatainak frissítése.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_check.png)

1. Kattintson a **mentés és küldés Invitiation** (ha szükséges mail keresztül) vagy **mentése** (mentse közvetlenül) a felhasználói regisztráció befejezéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával Procore SSO, Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Procore SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Procore SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). Ha a hozzáférési panelen a Procore SSO csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Procore egyszeri bejelentkezés alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

