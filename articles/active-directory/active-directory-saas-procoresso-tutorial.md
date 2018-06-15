---
title: 'Oktatóanyag: Azure Active Directoryval integrált Procore SSO |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Procore SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: eaa340e94311f5e70f473eab534df3fb3a87c866
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348655"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Azure Active Directoryval integrált Procore egyszeri bejelentkezés

Ebben az oktatóanyagban elsajátíthatja Procore SSO integrálása az Azure Active Directory (Azure AD).

Procore SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Procore SSO hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Procore SSO (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához Procore egyszeri bejelentkezési modellel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Procore SSO egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Procore SSO hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-procore-sso-from-the-gallery"></a>A gyűjteményből Procore SSO hozzáadása
Az Azure AD integrálása a Procore SSO konfigurálásához kell hozzáadnia Procore SSO a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Procore SSO hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Procore SSO**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. Az eredmények panelen válassza ki a **Procore SSO**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Procore SSO "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Procore SSO a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Procore SSO közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Procore SSO a.

Az Azure AD az egyszeri bejelentkezés Procore egyszeri bejelentkezési modellel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Procore SSO tesztfelhasználó létrehozása](#creating-a-procore-sso-test-user)**  - való egy megfelelője a Britta Simon Procore SSO, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az Procore SSO-alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Procore egyszeri bejelentkezési modellel, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **Procore SSO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Az a **Procore SSO-tartomány és az URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. A a **Procore SSO konfigurációs** kattintson **Procore SSO konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Procore SSO** ügyféloldali, jelentkezzen be rendszergazdaként a procore vállalati webhely.

8. Az eszközkészlet legördülő le, kattintson a **Admin** az SSO beállításai oldal megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Illessze be az értékeket a mezőkbe, lásd az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. Az a **egyszeri bejelentkezési kibocsátó URL-cím** mezőbe illessze be az SAML-entitás azonosítója az Azure-portálon átmásolva.

    b. Az a **SAML bejelentkezési cél URL-cím** mezőbe illessze be az Azure-portálon átmásolva a SAML-alapú egyszeri bejelentkezési URL-címe.

    c. Most nyissa meg a **metaadatainak XML-kódja** újabb Azure-portálról letöltött, és másolja a tanúsítvány nevű címkében **x.509**. Illessze be a másolt érték a **egyszeri bejelentkezés x509 tanúsítvány** mezőbe.

10. Kattintson a **módosítások mentése**.

11. Után ezeket a beállításokat, akkor el kell küldenie a **tartománynév** (pl. **contoso.com**) keresztül, amely jelentkezik be a Procore a [Procore támogatási csoport](https://support.procore.com/) és azok aktiválódik összevont egyszeri Bejelentkezéses ehhez a tartományhoz.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-procore-sso-test-user"></a>Procore SSO tesztfelhasználó létrehozása

Kérjük, kövesse a következő lépések végrehajtásával Procore tesztfelhasználó létrehozása az oldalon.

1. Rendszergazdaként a procore vállalati webhelyre való bejelentkezés.  

2. Az eszközkészlet legördülő le, kattintson a **Directory** a vállalati címtár lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Kattintson a **hozzáadása egy személy** a képernyőt, és írja be a beállítást hajtsa végre a következő beállítások -

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. Az a **Utónév** szövegmezőhöz típus felhasználó utónevét például **Britta**.

    b. Az a **Vezetéknév** szövegmező, például típus felhasználó vezetékneve **Simon**.

    c. Az a **E-mail cím** szövegmezőhöz típusa a felhasználó e-mail címét, például **BrittaSimon@contoso.com**.

    d. Válassza ki **engedély sablon** , **engedély sablon alkalmazása később**.

    e. Kattintson a **Create** (Létrehozás) gombra.

4. Ellenőrizze, és frissítse az újonnan hozzáadott ismerős részleteit.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Kattintson a **mentése és küldése Invitiation** (ha szükséges egy összehíváshoz mail keresztül) vagy **mentése** (Mentés közvetlenül) a felhasználói regisztráció befejezéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított saját hozzáférés Procore SSO Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Procore SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Procore SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586). Ha a hozzáférési panelen Procore SSO csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Procore SSO alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

