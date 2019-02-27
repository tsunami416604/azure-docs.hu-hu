---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler internetes hozzáférés rendszergazdája |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Internet-hozzáférés rendszergazdája Zscaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767c0dac960a67208a7f9f08a6158453fe1a576d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880036"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler internetes hozzáférés rendszergazdája

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler Internet-hozzáférés rendszergazdája integrálása az Azure Active Directory (Azure AD).
Zscaler Internet-hozzáférés rendszergazdája integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben a Zscaler internetes hozzáférés rendszergazdai hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Zscaler internetes hozzáférés rendszergazdája (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler internetes hozzáférés adminisztrátora, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Zscaler Internet-hozzáférés rendszergazdai előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja-e Internet-hozzáférés rendszergazdája Zscaler **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Internet-hozzáférés rendszergazdája Zscaler hozzáadása a katalógusból

Az Azure AD-be a Zscaler internetes hozzáférés rendszergazdája integráció konfigurálásához, hozzá kell Zscaler Internet-hozzáférés rendszergazdája a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Internet-hozzáférés rendszergazdája Zscaler hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler Internet-hozzáférés rendszergazdája**, jelölje be **Zscaler Internet-hozzáférés rendszergazdája** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

     ![Zscaler internetes hozzáférés adminisztrátora, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Zscaler Internet Access rendszergazda nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Zscaler internetes hozzáférés rendszergazdája hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Zscaler internetes hozzáférés rendszergazdája tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zscaler Internet Access rendszergazdai egyszeri bejelentkezés konfigurálása](#configure-zscaler-internet-access-administrator-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Zscaler Internet-hozzáférés rendszergazdája tesztfelhasználót](#create-zscaler-internet-access-administrator-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a Zscaler internetes hozzáférés rendszergazdája, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a Zscaler internetes hozzáférés rendszergazdája, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Zscaler Internet-hozzáférés rendszergazdája** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Zscaler Internet-hozzáférés rendszergazdai tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címet a követelmény alapján:

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címet a követelmény alapján:

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet-hozzáférés rendszergazdája alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML lap**, kattintson a **szerkesztése** gombra kattintva nyissa meg **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![Az attribútumkapcsolat](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név)  | Adatforrás-attribútum  |
    | ---------| ------------ |
    | Szerepkör     | user.assignedroles |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Az a **forrásattribútum** listájában, selelct az attribútum értéke.

    c. Kattintson az **OK** gombra.

    d. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Kattintson a [Itt](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) tudni, hogy az Azure AD-szerepkör konfigurálása

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **állítsa be Zscaler internetes hozzáférés rendszergazdája** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Zscaler Internet Access rendszergazdai egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zscaler Internet Access felügyeleti felhasználói Felületéhez.

2. Lépjen a **Adminisztráció > rendszergazdai felügyelet** , és hajtsa végre az alábbi lépéseket, és kattintson a Mentés gombra:

    ![Felügyeleti](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "felügyelete")

    a. Ellenőrizze **SAML-hitelesítés engedélyezése**.

    b. Kattintson a **feltöltése**töltheti fel az Azure SAML aláíró tanúsítvány az Azure Portalról letöltött a **nyilvános SSL-tanúsítvány**.

    c. Opcionálisan a fokozott biztonság érdekében adja hozzá a **kibocsátó** részletei a kibocsátó az SAML-válasz ellenőrzéséhez.

3. A rendszergazda felhasználói felületén hajtsa végre az alábbi lépéseket:

    ![Adminisztráció](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. A kurzort a **aktiválási** menüjének bal alsó.

    b. Kattintson a **aktiválása**.

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

Ebben a szakaszban engedélyezze Britta Simon Zscaler internetes hozzáférés adminisztrátora, a hozzáférés biztosításával az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Zscaler Internet-hozzáférés rendszergazdája**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Zscaler Internet-hozzáférés rendszergazdája**.

    ![A Zscaler Internet-hozzáférés rendszergazdája hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Internet-hozzáférés rendszergazdája Zscaler tesztfelhasználó létrehozása

Ez a szakasz célja egy Britta Simon Zscaler Internet Access rendszergazda nevű felhasználó létrehozásához. Zscaler Internet-hozzáféréssel nem támogatja a Just-In-Time kiépítése a rendszergazda egyszeri bejelentkezéshez. Manuálisan létrehozni a rendszergazdai fiók szükséges.
Bemutatjuk, hogyan hozhat létre egy rendszergazdai fiók tekintse meg Zscaler dokumentációt:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler Internet-hozzáférés rendszergazdája csempére kattint, akkor kell automatikusan megtörténik a a Zscaler Internet Access felügyeleti felhasználói felületéhez, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
