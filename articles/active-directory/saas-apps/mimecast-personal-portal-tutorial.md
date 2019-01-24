---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Mimecast személyes portállal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és személyes portál Mimecast között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 24bd6fe29e05f84d78316d0bb3e8ffc0d25157f3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828036"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Oktatóanyag: Az Azure Active Directory-integráció Mimecast személyes portállal

Ebben az oktatóanyagban elsajátíthatja, hogyan Mimecast személyes portál integrálható az Azure Active Directory (Azure AD).
Személyes portál Mimecast integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Mimecast személyes Portal Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Mimecast személyes portálra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Mimecast személyes portállal, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Személyes portál Mimecast egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a személyes portál Mimecast **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Mimecast személyes portál hozzáadása a katalógusból

Az Azure AD-be Mimecast személyes portál integráció konfigurálásához, hozzá kell Mimecast személyes portál a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Mimecast személyes portál hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Mimecast személyes portál**, jelölje be **Mimecast személyes portál** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a Mimecast személyes portál](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Mimecast személyes portállal **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Mimecast személyes portálon hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Mimecast személyes portállal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Mimecast személyes portál egyszeri bejelentkezés konfigurálása](#configure-mimecast-personal-portal-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Mimecast személyes portál tesztfelhasználót](#create-mimecast-personal-portal-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Mimecast személyes portálon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Mimecast személyes portállal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Mimecast személyes portál** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mimecast személyes portál tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:

    | Régió  |  Érték | 
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Egyesült Államok   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Dél-Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Ausztrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse az értéket a tényleges azonosítója. Kapcsolattartó [Mimecast személyes portál ügyfél-támogatási csapatának](https://www.mimecast.com/customer-success/technical-support/) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Mimecast személyes portál beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Mimecast személyes portál egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast személyes portálra rendszergazdaként.

2. Lépjen a **szolgáltatások \> alkalmazások**.
   
    ![Alkalmazások](./media/mimecast-personal-portal-tutorial/ic794998.png "alkalmazások")

3. Kattintson a **hitelesítési profilok**.
   
    ![Hitelesítési profilok](./media/mimecast-personal-portal-tutorial/ic794999.png "hitelesítési profilok")

4. Kattintson a **új hitelesítési profilt**.
   
    ![Új hitelesítés profil](./media/mimecast-personal-portal-tutorial/ic795000.png "új hitelesítési profil")

5. Az a **hitelesítési profilt** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Hitelesítési profilt](./media/mimecast-personal-portal-tutorial/ic795001.png "hitelesítési profilt")
   
    a. Az a **leírás** szövegmezőbe írja be a konfiguráció nevét.
   
    b. Válassza ki **Mimecast személyes portál SAML-hitelesítés**.
   
    c. Mint **szolgáltató**válassza **Azure Active Directory**.
   
    d. A **kiállítójának URL-címe** szövegmezőjébe illessze be az értéket, **Azure Ad-azonosító**, Azure Portalról másolt.
   
    e. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.
   
    f. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

    g. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **Identitástanúsítványt szolgáltató (Metadata)** szövegmezőbe.

    h. Válassza ki **egyszeri bejelentkezés engedélyezése**.
   
    i. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon Mimecast személyes portálra a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Mimecast személyes portál**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Mimecast személyes portál**.

    ![Az alkalmazások listáját a személyes portál Mimecast hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-mimecast-personal-portal-test-user"></a>Személyes portál Mimecast tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók személyes portál Mimecast szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Mimecast személyes portált. Mimecast személyes portálon esetén kiépítése a manuális feladat.

Szeretne regisztrálni egy tartományban, felhasználók létrehozása előtt.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mimecast személyes portál** rendszergazdaként.

2. Lépjen a **könyvtárak \> belső**.
   
    ![Könyvtárak](./media/mimecast-personal-portal-tutorial/ic795003.png "könyvtárak")

3. Kattintson a **új tartomány regisztrálása**.
   
    ![Új tartomány regisztrálása](./media/mimecast-personal-portal-tutorial/ic795004.png "új tartomány regisztrálása")

4. Az új tartomány létrehozása után kattintson **új cím**.
   
    ![Új cím](./media/mimecast-personal-portal-tutorial/ic795005.png "új cím")

5. Az új cím párbeszédpanelen hajtsa végre az alábbi lépéseket egy érvényes Azure AD-fiókot kíván üzembe helyezni:
   
    ![Mentés](./media/mimecast-personal-portal-tutorial/ic795006.png "mentése")
   
    a. Az a **E-mail cím** szövegmezőbe írja be **E-mail cím** , a felhasználó **BrittaSimon@contoso.com**.
    
    b. Az a **globális név** szövegmezőbe írja be a **felhasználónév** , **BrittaSimon**.

    c. Az a **jelszó**, és **jelszó megerősítése** szövegmezőből, írja be a **jelszó** felhasználó.
   
    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Mimecast személyes portál felhasználói fiók létrehozása eszközöket és Mimecast személyes portál által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a személyes portál Mimecast csempére kattint, meg kell is automatikusan megtörténik a a Mimecast személyes portálra, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

