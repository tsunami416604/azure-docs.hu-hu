---
title: 'Oktatóanyag: Azure Active Directory-integráció az Mimecast felügyeleti konzol |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Mimecast felügyeleti konzol és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 27c9606357d9599fa56e4045606f8d9046722e7f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041721"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Oktatóanyag: Azure Active Directory-integráció az Mimecast felügyeleti konzol

Ebben az oktatóanyagban elsajátíthatja, hogyan Mimecast felügyeleti konzol integrálása az Azure Active Directory (Azure AD).

Mimecast felügyeleti konzol integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Mimecast felügyeleti konzol az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Mimecast felügyeleti konzolra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Mimecast felügyeleti konzol az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A felügyeleti konzol Mimecast egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Felügyeleti konzol Mimecast hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Felügyeleti konzol Mimecast hozzáadása a katalógusból
Az Azure AD integrálása a Mimecast felügyeleti konzol konfigurálása, hozzá kell Mimecast felügyeleti konzol a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Felügyeleti konzol Mimecast hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Mimecast felügyeleti konzol**, jelölje be **Mimecast felügyeleti konzol** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Mimecast felügyeleti konzol](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Mimecast felügyeleti konzol a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Mimecast felügyeleti konzol mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a kapcsolódó felhasználó Mimecast felügyeleti konzol közötti kapcsolat kapcsolatot kell létrehozni.

Mimecast felügyeleti konzol, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Mimecast felügyeleti konzollal tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy rendszergazdai konzol Mimecast tesztfelhasználót](#create-a-mimecast-admin-console-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Mimecast felügyeleti konzolon.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Mimecast felügyeleti konzol alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Mimecast felügyeleti konzol, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Mimecast felügyeleti konzol** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Az a **Mimecast rendszergazdai konzol tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mimecast rendszergazdai konzol tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > A bejelentkezési URL-címet az adott régió.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/mimecast-admin-console-tutorial/tutorial_general_400.png)

6. Az a **Mimecast rendszergazdai konzol konfiguráció** területén kattintson **Mimecast felügyeleti konzol konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Mimecast rendszergazdai konzol konfiguráció](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a Mimecast rendszergazdai konzolt rendszergazdaként.

8. Lépjen a **szolgáltatások \> alkalmazás**.

    ![Szolgáltatások](./media/mimecast-admin-console-tutorial/ic794998.png "szolgáltatások")

9. Kattintson a **hitelesítési profilok**.

    ![Hitelesítési profilok](./media/mimecast-admin-console-tutorial/ic794999.png "hitelesítési profilok")
    
10. Kattintson a **új hitelesítési profilt**.

    ![Új hitelesítés profilok](./media/mimecast-admin-console-tutorial/ic795000.png "új hitelesítési profilok")

11. Az a **hitelesítési profilt** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Hitelesítési profilt](./media/mimecast-admin-console-tutorial/ic795015.png "hitelesítési profilt")
    
    a. Az a **leírás** szövegmezőbe írja be a konfiguráció nevét.
    
    b. Válassza ki **SAML-hitelesítés Mimecast felügyeleti konzolon**.
    
    c. Mint **szolgáltató**válassza **Azure Active Directory**.
    
    d. Beillesztés **SAML Entitásazonosító**, azokat az Azure Portalról másolt a **kiállítójának URL-címe** szövegmezőbe.
    
    e. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, azokat az Azure Portalról másolt a **bejelentkezési URL-cím** szövegmezőbe.

    f. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt a **kijelentkezési URL-címe** szövegmezőbe.
    
    >[!NOTE]
    >A bejelentkezési URL-cím és a kijelentkezési URL-cím értéke ugyanazok a Mimecast felügyeleti konzolon.
    
    g. Nyissa meg a Jegyzettömbben, az Azure portálról letöltött a base-64 tanúsítvány, távolítsa el az első sort ("*--*") és az utolsó sort ("*--*"), másolja be azt, a maradék tartalmat a vágólapra, majd illessze be azt a **Identitástanúsítványt szolgáltató (Metadata)** szövegmezőbe.
    
    h. Válassza ki **egyszeri bejelentkezés engedélyezése**.
    
    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/mimecast-admin-console-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/mimecast-admin-console-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Felügyeleti konzol Mimecast tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mimecast felügyeleti konzol szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Mimecast felügyeleti konzolra. Mimecast felügyeleti konzol esetén kiépítése a manuális feladat.

* Szeretne regisztrálni egy tartományban, felhasználók létrehozása előtt.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mimecast felügyeleti konzol** rendszergazdaként.
2. Lépjen a **könyvtárak \> belső**.
   
   ![Könyvtárak](./media/mimecast-admin-console-tutorial/ic795003.png "könyvtárak")
3. Kattintson a **új tartomány regisztrálása**.
   
   ![Új tartomány regisztrálása](./media/mimecast-admin-console-tutorial/ic795004.png "új tartomány regisztrálása")
4. Az új tartomány létrehozása után kattintson **új cím**.
   
   ![Új cím](./media/mimecast-admin-console-tutorial/ic795005.png "új cím")
5. Az új cím párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Mentés](./media/mimecast-admin-console-tutorial/ic795006.png "mentése")
   
   a. Írja be a **E-mail cím**, **globális név**, **jelszó**, és **jelszó megerősítése** attribútumai érvényes Azure AD-fiók szeretné üzembe helyezheti azokat a kapcsolódó szöveges mezőkben.

   b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Mimecast felügyeleti konzol felhasználói fiók létrehozása eszközöket és Mimecast felügyeleti konzol által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Mimecast felügyeleti konzolra a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Mimecast felügyeleti konzol, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Mimecast felügyeleti konzol**.

    ![Az alkalmazások listáját a Mimecast felügyeleti konzolon hivatkozásra](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Mimecast felügyeleti konzol csempére kattint, kell lekérése automatikusan bejelentkezett a felügyeleti konzol Mimecast alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-admin-console-tutorial/tutorial_general_203.png

