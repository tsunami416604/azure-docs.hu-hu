---
title: 'Oktatóanyag: Az Azure Active Directory integrálása a GitHub |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a GitHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 5afcec7a1dfd306cb87989e1a24cc662af183b5e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826258"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Oktatóanyag: Az Azure Active Directory-integráció a Githubon

Ebben az oktatóanyagban elsajátíthatja az Azure Active Directoryval (Azure AD) integrálása a GitHub.
Az Azure AD integrálása a GitHub nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a GitHub Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Githubra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a GitHub, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* GitHub egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a GitHub **SP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a GitHub [ **automatikus** felhasználók átadása](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>GitHub hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a GitHub, hozzá kell GitHub a galériából a felügyelt SaaS-alkalmazások listájára.

**GitHub hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **GitHub**válassza **GitHub** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a Githubon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján egy tesztfelhasználót nevű GitHub- **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a GitHub hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés GitHub-tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[GitHub egyszeri bejelentkezés konfigurálása](#configure-github-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a GitHub tesztfelhasználó](#create-github-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó GitHub-megfelelője a Britta Simon rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés GitHub, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **GitHub** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![GitHub-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://github.com/orgs/<entity-id>/sso`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket kell. Itt javasoljuk, hogy az azonosító egyedi karakterlánc értékét használhatja. Nyissa meg az értékek lekéréséhez a GitHub-rendszergazda szakaszban.

5. GitHub-alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **egyedi felhasználói azonosító** van **user.userprincipalname** , de a GitHub vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- |
    | Egyedi felhasználói azonosító | User.mail |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a Githubot** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-github-single-sign-on"></a>GitHub egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezet hely rendszergazdaként.

2. Navigáljon a **beállítások** kattintson **biztonsági**

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Ellenőrizze a **engedélyezése SAML-hitelesítés** be, hogy felfedné az egyszeri bejelentkezés konfigurációs mezők. Ezután használja az egyszeri bejelentkezési URL-cím az egyszeri bejelentkezési URL-cím az Azure AD-konfigurációjának frissítése.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Adja meg a következő mezőket:

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    b. Az a **kibocsátó** szövegmezőjébe illessze be **az Azure AD-azonosító** az Azure Portalról másolt érték.

    c. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

    d. Kattintson a **szerkesztése** ikonra kattintva szerkessze a **aláírás metódus** és **kivonatoló módszerrel** a **RSA-SHA1** és **SHA1**való **RSA-SHA256 algoritmust** és **SHA256** alább látható módon.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kattintson a **teszt SAML-konfigurációja** ellenőrizheti, hogy nincs érvényesítési hibák vagy az egyszeri bejelentkezés során hibák.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Kattintson a **Mentés** gombra.

> [!NOTE]
> Egyszeri bejelentkezés a GitHub egy adott szervezet számára, a GitHub hitelesíti, és nem helyettesíti a hitelesítés a GitHub magát. Ezért ha a felhasználó github.com munkamenete lejárt, előfordulhat, hogy megkérdezi, hogy az egyszeri bejelentkezési folyamat során a GitHub/jelszó-hitelesítést.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés GitHub Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **GitHub**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **GitHub**.

    ![Az alkalmazások listáját a GitHub-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-github-test-user"></a>GitHub tesztfelhasználó létrehozása

Ez a szakasz célja, a GitHub Britta Simon nevű felhasználó létrehozásához. GitHub támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](github-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a GitHub vállalati webhely.

2. Kattintson a **személyek**.

    ![Személyek](./media/github-tutorial/tutorial_github_config_github_08.png "személyek")

3. Kattintson a **meghívó tag**.

    ![Felhasználó meghívása](./media/github-tutorial/tutorial_github_config_github_09.png "felhasználó meghívása")

4. Az a **meghívó tag** párbeszédpanel lapon, a következő lépésekkel:

    a. Az a **E-mail** szövegmezőbe írja be a Britta Simon fiók e-mail-címét.

    ![Felkérése](./media/github-tutorial/tutorial_github_config_github_10.png "felkérése")

    b. Kattintson a **meghívó küldése**.

    ![Felkérése](./media/github-tutorial/tutorial_github_config_github_11.png "felkérése")

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a GitHub csempére kattint, meg kell automatikusan megtörténik a a githubon, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)