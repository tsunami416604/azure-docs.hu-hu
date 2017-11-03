---
title: "Oktatóanyag: Azure Active Directoryval integrált GitHub |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a GitHub között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Oktatóanyag: Azure Active Directory-integráció a Githubon

Ebben az oktatóanyagban elsajátíthatja a GitHub integrálása az Azure Active Directory (Azure AD).

GitHub integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki elérheti a Githubon az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a GitHub (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Githubon, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A GitHub egyszeri bejelentkezés engedélyezve van az előfizetésben


> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. GitHub hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-github-from-the-gallery"></a>GitHub hozzáadása a gyűjteményből
Az Azure AD integrálása a Githubon konfigurálásához kell hozzáadnia GitHub a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Githubon a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **GitHub.com**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. Az eredmények panelen válassza ki a **GitHub**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján GitHub.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a github megfelelőjére felhasználó a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Githubon közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Githubon.

Az Azure AD egyszeri bejelentkezést a Githubon tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[GitHub tesztfelhasználó létrehozása](#creating-a-GitHub-test-user)**  - való Britta Simon egy megfelelője a Githubon, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és a GitHub-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a Githubon, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **GitHub** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Az a **GitHub-tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket, mint:`https://github.com/orgs/<entity-id>/sso`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor frissítheti ezeket az értékeket a tényleges rang URL-cím és azonosítója. Itt javasoljuk, hogy az azonosító a karakterlánc egyedi értéket használja. Ugrás a GitHub felügyeleti szakasz beolvasása ezeket az értékeket. 

4. Az a **felhasználói attribútumok** szakaszban jelölje be **felhasználói azonosító** user.mail szerint.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Az a **SAML-aláíró tanúsítványa** kattintson **hozzon létre új tanúsítvány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. A a **új tanúsítvány létrehozása** párbeszédpanel, kattintson a naptár ikonra, és válasszon egy **lejárati dátum**. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **új tanúsítvány aktiválásához** kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. Az előugró **helyettesítő tanúsítvány** ablak, kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. A a **GitHub konfigurációs** kattintson **konfigurálása GitHub** megnyitásához **bejelentkezés konfigurálása** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. Egy másik webes böngészőablakban jelentkezzen be a Githubon szervezet webhely rendszergazdaként.

12. Navigáljon a **beállítások** kattintson **biztonsági**

    ![Beállítások](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Ellenőrizze a **engedélyezése SAML-alapú hitelesítés** mezőbe, hogy nem jeleníti meg az egyszeri bejelentkezés konfigurációs mezők. Ezután az egyszeri bejelentkezési URL-cím segítségével egyetlen bejelentkezési URL-CÍMÉT az Azure AD-konfiguráció frissítése.

    ![Beállítások](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Adja meg a következő mezőket:

    a. **Jelentkezzen be az URL-cím**: Adja meg **SAML-alapú egyszeri bejelentkezést szolgáltatás URL-címe** a a **konfigurálása GitHub** szakasz Azure ad-val

    b. **Kibocsátó**: Adjon meg **SAML Entitásazonosító** a a **konfigurálása GitHub** szakasz Azure ad-val

    c. **Nyilvános tanúsítvány**: Nyissa meg a letöltött tanúsítvány az Azure AD egy fájlt, és másolja a tartalmat, beleértve a "BEGIN tanúsítvány" és "END CERTIFICATE"

    ![Beállítások](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Kattintson a **teszt SAML-alapú konfigurációs** annak ellenőrzésére, hogy nincs érvényesítési hibák vagy hibák egyszeri bejelentkezés során.

    ![Beállítások](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Kattintson a **mentése**

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 


### <a name="creating-a-github-test-user"></a>GitHub tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Githubon az Azure AD-felhasználók, akkor ki kell építenie a Githubon.  
GitHub, ha egy kézi tevékenység.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Githubon vállalati webhely.

2. Kattintson a **személyek**.

    ![Személyek](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "személyek")

3. Kattintson a **a meghívás tag**.

    ![Meghívott felhasználóknak](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "meghívott felhasználóknak")

4. Az a **a meghívás tag** párbeszédpanel lapon, a következő lépésekkel:

    a. Az a **E-mail** szövegmezőhöz Britta Simon fiók e-mail címét.

    ![Felkérése](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "felkérése")
    
    b. Kattintson a **meghívó küldése**.

    ![Felkérése](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "felkérése")

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos fog egy e-maileket és hivatkozásra a fiók megerősítéséhez, mielőtt aktívvá válik.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által használt hozzáférés biztosítása a GitHub használatára.

![Felhasználó hozzárendelése][200] 

**A GitHub Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **GitHub.com**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel GitHub mozaik gombra kattint, meg kell beolvasása bejelentkezett a GitHub-alkalmazásba. Akkor lesz naplózása szervezeti fiók azonban akkor kell jelentkezzen be a személyes fiókjával.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
