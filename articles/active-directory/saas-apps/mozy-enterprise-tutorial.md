---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a vállalati Mozy |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Mozy nagyvállalati és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 4e7570afc5d359877f677e692678b09036da9ad3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240458"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Oktatóanyag: Az Azure Active Directory-integráció a vállalati Mozy

Ebben az oktatóanyagban elsajátíthatja, hogyan Mozy vállalati integrálása az Azure Active Directory (Azure AD).
Mozy vállalati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá nagyvállalati Mozy Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Mozy Enterprise (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Mozy vállalati, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Mozy vállalati szintű egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Mozy vállalati **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy vállalati hozzáadása a katalógusból

Az Azure AD-be Mozy vállalati integráció konfigurálásához, hozzá kell Mozy vállalati a galériából a felügyelt SaaS-alkalmazások listájára.

**Mozy vállalati hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Mozy vállalati**, jelölje be **Mozy vállalati** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Mozy Enterprise](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése a Mozy vállalati alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Mozy vállalati hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a vállalati Mozy tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Vállalati Mozy egyszeri bejelentkezés konfigurálása](#configure-mozy-enterprise-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Mozy vállalati tesztfelhasználót](#create-mozy-enterprise-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Mozy vállalat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés a vállalati Mozy, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Mozy vállalati** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mozy vállalati tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Mozy vállalati ügyfél-támogatási csapatának](http://support.mozy.com/) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Mozy vállalati beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Mozy vállalati szintű egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a vállalati Mozy vállalati hely rendszergazdaként.

2. Az a **konfigurációs** területén kattintson **hitelesítési házirend**.
   
    ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777314.png "hitelesítési házirend")

3. Az a **hitelesítési házirend** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777315.png "hitelesítési házirend")
   
    a. Válassza ki **címtárszolgáltatás** , **szolgáltató**.
   
    b. Válassza ki **LDAP leküldéses használatához**.
   
    c. Kattintson a **SAML-hitelesítés** fülre.
   
    d. Beillesztés **bejelentkezési URL-cím**, azokat az Azure Portalról másolt a **hitelesítési URL-cím** szövegmezőbe.
   
    e. Beillesztés **az Azure AD-azonosító**, azokat az Azure Portalról másolt a **SAML-végpont** szövegmezőbe.
   
    f. A Jegyzettömb alkalmazásban nyissa meg a letöltött base-64 kódolású tanúsítvány, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **SAML-tanúsítvány** szövegmezőbe.
   
    g. Válassza ki **SSO engedélyezése rendszergazdák számára a hálózati hitelesítő adataikkal jelentkezzenek be**.
   
    h. Kattintson a **módosítások mentése**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mozy vállalati Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Mozy vállalati**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Mozy vállalati**.

    ![Az alkalmazások listáját a Mozy vállalati hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-mozy-enterprise-test-user"></a>Mozy vállalati tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mozy Enterprise-ba való bejelentkezéshez, akkor ki kell építeni Mozy vállalati. Esetén Mozy Enterprise a manuális tevékenység kiépítése.

>[!NOTE]
>Bármely más Mozy vállalati felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Mozy vállalati által biztosított API-k.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mozy vállalati** bérlő.

2. Kattintson a **felhasználók**, és kattintson a **új felhasználó hozzáadása**.
   
    ![Felhasználók](./media/mozy-enterprise-tutorial/ic777317.png "felhasználók")
   
    >[!NOTE]
    >A **új felhasználó hozzáadása** beállítás csak akkor látható, csak ha **Mozy** alapján a szolgáltató van kiválasztva **hitelesítési házirend**. Ha SAML-hitelesítés van konfigurálva, majd a felhasználók automatikusan hozzáadja az egyszeri bejelentkezés révén az első bejelentkezésnél a.
    
3. Az új felhasználó párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználók hozzáadása](./media/mozy-enterprise-tutorial/ic777318.png "felhasználók hozzáadása")
   
    a. Az a **válasszon egy csoportot** listában, válasszon ki egy csoportot.
   
    b. Az a **milyen típusú felhasználói** listában, válasszon egy típust.
   
    c. Az a **felhasználónév** szövegmezőbe írja be az Azure AD-felhasználó nevét.
   
    d. Az a **E-mail** szövegmezőbe írja be az Azure AD-felhasználó e-mail-címét.
   
    e. Válassza ki **felhasználói utasítás e-mailt**.
   
    f. Kattintson a **felhasználó(k) hozzáadása**.

     >[!NOTE]
     > Miután létrehozta a felhasználó, egy e-mailt kapnak, amelyben a hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók az Azure AD-felhasználót.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Mozy vállalati csempére kattint, akkor kell automatikusan megtörténik a az Mozy Enterprise, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

