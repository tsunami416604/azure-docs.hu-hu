---
title: 'Oktatóanyag: Azure Active Directoryval integrált GitHub |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a GitHub között.
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
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: c799e59dca6d84ce526e29f7b5714a2a0d9da788
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923515"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Oktatóanyag: Azure Active Directory-integráció a Githubon

Ebben az oktatóanyagban elsajátíthatja a GitHub integrálása az Azure Active Directory (Azure AD).

GitHub integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki elérheti a Githubon az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a GitHub (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Githubon, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Githubon az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. GitHub hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-github-from-the-gallery"></a>GitHub hozzáadása a gyűjteményből
Az Azure AD integrálása a Githubon konfigurálásához kell hozzáadnia GitHub a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Githubon a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **GitHub**, jelölje be **GitHub** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Githubon](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján GitHub.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a github megfelelőjére felhasználó a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Githubon közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Githubon tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[GitHub tesztfelhasználó létrehozása](#create-a-github-test-user)**  - való Britta Simon egy megfelelője a Githubon, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a GitHub-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a Githubon, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **GitHub** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/github-tutorial/tutorial_github_samlbase.png)

3. Az a **GitHub-tartomány és az URL-címek** területen tegye a következőket:

    ![GitHub-tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/github-tutorial/tutorial_github_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>/sso`

    b. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Itt javasoljuk, hogy az azonosító a karakterlánc egyedi értéket használja. Ugrás a GitHub felügyeleti szakasz beolvasása ezeket az értékeket.

4. Az a **felhasználói attribútumok** szakaszban jelölje be **felhasználói azonosító** user.mail szerint.

    ![Egyszeri bejelentkezés konfigurálása](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/github-tutorial/tutorial_github_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/github-tutorial/tutorial_general_400.png)

7. A a **GitHub konfigurációs** kattintson **konfigurálása GitHub** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![GitHub-konfiguráció](./media/github-tutorial/tutorial_github_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a Githubon szervezet webhely rendszergazdaként.

9. Navigáljon a **beállítások** kattintson **biztonsági**

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Ellenőrizze a **engedélyezése SAML-alapú hitelesítés** mezőbe, hogy nem jeleníti meg az egyszeri bejelentkezés konfigurációs mezők. Ezután az egyszeri bejelentkezési URL-cím segítségével egyetlen bejelentkezési URL-CÍMÉT az Azure AD-konfiguráció frissítése.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Adja meg a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure portálról másolta.

    b. Az a **kibocsátó** szövegmezőhöz Beillesztés **SAML Entitásazonosító** érték, amely az Azure portálról másolta.

    c. Nyissa meg a Jegyzettömbben az Azure portálról letöltött tanúsítvány illesztheti be a **nyilvános tanúsítvány** szövegmező.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Kattintson a **teszt SAML-alapú konfigurációs** annak ellenőrzésére, hogy nincs érvényesítési hibák vagy hibák egyszeri bejelentkezés során.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/github-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/github-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/github-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/github-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-github-test-user"></a>GitHub tesztfelhasználó létrehozása

Ez a szakasz célja a Githubon Britta Simon nevű felhasználót létrehozni. GitHub támogatja az automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](github-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

**Ha szeretne létrehozni a felhasználót manuálisan, akkor hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a Githubon vállalati webhely.

2. Kattintson a **személyek**.

    ![Személyek](./media/github-tutorial/tutorial_github_config_github_08.png "személyek")

3. Kattintson a **a meghívás tag**.

    ![Meghívott felhasználóknak](./media/github-tutorial/tutorial_github_config_github_09.png "meghívott felhasználóknak")

4. Az a **a meghívás tag** párbeszédpanel lapon, a következő lépésekkel:

    a. Az a **E-mail** szövegmezőhöz Britta Simon fiók e-mail címét.

    ![Felkérése](./media/github-tutorial/tutorial_github_config_github_10.png "felkérése")

    b. Kattintson a **meghívó küldése**.

    ![Felkérése](./media/github-tutorial/tutorial_github_config_github_11.png "felkérése")

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos fog egy e-maileket és hivatkozásra a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés GitHub használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**A GitHub Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **GitHub**.

    ![Az alkalmazások listáját a Githubon hivatkozás](./media/github-tutorial/tutorial_github_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel GitHub mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett a GitHub-alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



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

