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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 79a2bc9d517e3c292268a4a70f08936cb0325fbd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053087"
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

- Az Azure AD-előfizetéshez
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

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **GitHub**válassza **GitHub** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Githubon](./media/github-tutorial/tutorial_github_addfromgallery.png)

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

1. Az Azure Portalon az a **GitHub** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/github-tutorial/tutorial_github_samlbase.png)

3. Az a **GitHub tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![GitHub-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/github-tutorial/tutorial_github_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>/sso`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket kell. Itt javasoljuk, hogy az azonosító egyedi karakterlánc értékét használhatja. Nyissa meg az értékek lekéréséhez a GitHub-rendszergazda szakaszban.

4. Az a **felhasználói attribútumok** szakaszban jelölje be **felhasználóazonosító** user.mail szerint.

    ![Egyszeri bejelentkezés konfigurálása](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/github-tutorial/tutorial_github_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/github-tutorial/tutorial_general_400.png)

7. Az a **GitHub konfigurációs** területén kattintson **GitHub beállítása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![GitHub-konfiguráció](./media/github-tutorial/tutorial_github_configure.png) 

8. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezet hely rendszergazdaként.

9. Navigáljon a **beállítások** kattintson **biztonsági**

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Ellenőrizze a **engedélyezése SAML-hitelesítés** be, hogy felfedné az egyszeri bejelentkezés konfigurációs mezők. Ezután használja az egyszeri bejelentkezési URL-cím az egyszeri bejelentkezési URL-cím az Azure AD-konfigurációjának frissítése.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Adja meg a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt érték.

    b. Az a **kibocsátó** szövegmezőjébe illessze be **SAML Entitásazonosító** az Azure Portalról másolt érték.

    c. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Kattintson a **teszt SAML-konfigurációja** ellenőrizheti, hogy nincs érvényesítési hibák vagy az egyszeri bejelentkezés során hibák.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/github-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/github-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/github-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/github-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
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

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a GitHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **GitHub**.

    ![Az alkalmazások listáját a GitHub-hivatkozás](./media/github-tutorial/tutorial_github_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a GitHub csempére kattint, akkor kell lekérése automatikusan bejelentkezett a GitHub-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

