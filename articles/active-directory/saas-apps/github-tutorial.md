---
title: 'Oktatóanyag: Azure Active Directory-integráció az GitHub |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a GitHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341157"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Oktatóanyag: Azure Active Directory-integráció a Githubon

Ebben az oktatóanyagban elsajátíthatja az Azure Active Directoryval (Azure AD) integrálása a GitHub.

Az Azure AD integrálása a GitHub nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a GitHub Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett github (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a GitHub, a következőkre van szükség:

- Azure AD-előfizetés
- A GitHub egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. GitHub hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-github-from-the-gallery"></a>GitHub hozzáadása a katalógusból
Konfigurálhatja az Azure AD integrálása a GitHub, hozzá kell GitHub a galériából a felügyelt SaaS-alkalmazások listájára.

**GitHub hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/github-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/github-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/github-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **GitHub**válassza **GitHub** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés GitHub, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a tartozó felhasználó, a GitHub egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a GitHub hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés GitHub-tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy GitHub-tesztfelhasználót](#create-a-github-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó GitHub-megfelelője a Britta Simon rendelkeznie.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a GitHub-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés GitHub, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **GitHub** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![image](./media/github-tutorial/tutorial_github_url.png) 

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>/sso`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket kell. Itt javasoljuk, hogy az azonosító egyedi karakterlánc értékét használhatja. Nyissa meg az értékek lekéréséhez a GitHub-rendszergazda szakaszban.

5. GitHub-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](./media/github-tutorial/i3-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    a. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Az a **forrásattribútum** listájához, válassza ki az attribútum értéke.

    c. Kattintson a **Save** (Mentés) gombra.
 
7. Az a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Az a **állítsa be a Githubot** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Az Azure AD-azonosító

    c. Kijelentkezési URL

    ![image](./media/github-tutorial/d1_samlsonfigure.png) 

9. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezet hely rendszergazdaként.

10. Navigáljon a **beállítások** kattintson **biztonsági**

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Ellenőrizze a **engedélyezése SAML-hitelesítés** be, hogy felfedné az egyszeri bejelentkezés konfigurációs mezők. Ezután használja az egyszeri bejelentkezési URL-cím az egyszeri bejelentkezési URL-cím az Azure AD-konfigurációjának frissítése.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Adja meg a következő mezőket:

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    b. Az a **kibocsátó** szövegmezőjébe illessze be **az Azure AD-azonosító** az Azure Portalról másolt érték.

    c. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

    d. Kattintson a **szerkesztése** ikonra kattintva szerkessze a **aláírás metódus** és **kivonatoló módszerrel** a **RSA-SHA1** és **SHA1**való **RSA-SHA256 algoritmust** és **SHA256** alább látható módon.

    ![image](./media/github-tutorial/tutorial_github_sha.png) 
    
13. Kattintson a **teszt SAML-konfigurációja** ellenőrizheti, hogy nincs érvényesítési hibák vagy az egyszeri bejelentkezés során hibák.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Kattintson a **Mentés** gombra.

> [!NOTE]
> Egyszeri bejelentkezés a GitHub egy adott szervezet számára, a GitHub hitelesíti, és nem helyettesíti a hitelesítés a GitHub magát. Ezért ha a felhasználó GitHub.com munkamenete lejárt, előfordulhat, hogy megkérdezi, hogy az egyszeri bejelentkezési folyamat során a GitHub/jelszó-hitelesítést.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/github-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/github-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-github-test-user"></a>GitHub tesztfelhasználó létrehozása

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

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés GitHub Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/github-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a GitHub csempére kattint, akkor kell lekérése automatikusan bejelentkezett a GitHub-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


