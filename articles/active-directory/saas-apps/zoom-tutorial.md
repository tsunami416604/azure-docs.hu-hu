---
title: 'Oktatóanyag: Nagyítás az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Nagyítás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ea60b774e4f3f61ecf715873c83198c2dad331f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166909"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Oktatóanyag: Nagyítás az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan nagyítás integrálása az Azure Active Directory (Azure AD).
Nagyítás integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Nagyítás Azure AD-ben.
* Engedélyezheti a felhasználóknak kell automatikus bejelentkezésre nagyítás (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs beállítja a nagyítás, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Nagyítás az egyszeri bejelentkezés engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Nagyítás támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból

Nagyítás az Azure AD-be, az integráció konfigurálásához hozzá kell Nagyítás a galériából a felügyelt SaaS-alkalmazások listájára.

**Nagyítás hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **nagyítás**válassza **nagyítás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában nagyítás](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Nagyítás nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó nagyítás hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Nagyítás tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Nagyítás egyszeri bejelentkezés konfigurálása](#configure-zoom-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Nagyítás tesztfelhasználó létrehozása](#create-zoom-test-user)**  – egy megfelelője a Britta Simon nagyítása, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Nagyítás konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **nagyítás** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Nagyítás tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.zoom.us`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `<companyname>.zoom.us`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [nagyítás ügyfél-támogatási csapatának](https://support.zoom.us/hc/en-us) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Nagyítás alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név) | Névtér  |  Adatforrás-attribútum|
    | ---------------| --------------- | --------- |
    | E-mail-cím  | user.mail  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail |
    | Utónév  | user.givenname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |
    | Vezetéknév  | user.surname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |
    | Telefonszám  | user.telephonenumber  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone |
    | Részleg  | user.department  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department |
    | szerepkör |    user.assignedrole |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role |

    > [!NOTE]
    > Kattintson a [Itt](https://docs.microsoft.com/en-gb/azure/role-based-access-control/role-assignments-portal) tudni, hogy az Azure AD-szerepkör konfigurálása

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Válassza ki a forrás, **attribútum**.

    d. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    e. Kattintson a **Ok**

    f. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Nagyítás előfordulhat, hogy számítson csoportos jogcímet SAML hasznos, ha létrehozott bármely majd forduljon [nagyítás ügyfél-támogatási csapatával](https://support.zoom.us/hc/en-us) a csoport adatokkal, hogy konfigurálhatják a csoportinformációk is, hogy a végén. Emellett meg kell adnia a kívánt objektum Azonosítóját [nagyítás ügyfél-támogatási csapatával](https://support.zoom.us/hc/en-us) úgy, hogy a végén. Kérjük, kövesse a [dokumentum](https://support.zoom.us/hc/en-us/articles/115005887566) megszerezni az objektum azonosítóját.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **nagyítás beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-zoom-single-sign-on"></a>Nagyítás egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Nagyítás vállalati hely rendszergazdaként.

2. Kattintson a **egyszeri bejelentkezés** fülre.

    ![Egyszeri bejelentkezés lap](./media/zoom-tutorial/ic784700.png "egyszeri bejelentkezés")

3. Kattintson a **biztonsági ellenőrzést** lapra, és folytassa a a **egyszeri bejelentkezés** beállításait.

4. Az egyszeri bejelentkezés szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés szakasz](./media/zoom-tutorial/ic784701.png "egyszeri bejelentkezés")

    a. A a **bejelentkezési oldal URL** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** Azure Portalról másolt.

    b. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe** Azure Portalról másolt.

    c. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató identitástanúsítványt** szövegmezőbe.

    d. A a **kibocsátó** szövegmezőjébe illessze be az értéket, **Azure Ad-azonosító** Azure Portalról másolt. 

    e. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > További információért látogasson el a Nagyítás dokumentációja [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés nagyítás Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **nagyítás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **nagyítás**.

    ![A Nagyítás hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-zoom-test-user"></a>Nagyítás tesztfelhasználó létrehozása

Ahhoz, hogy a nagyításhoz. Jelentkezzen be az Azure AD-felhasználók, akkor ki kell építeni Nagyítás be. Nagyítás, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **nagyítás** rendszergazdaként a vállalati webhely.

2. Kattintson a **fiókkezelés** fülre, majd **felhasználókezelés**.

3. Jelölje be a felhasználói felügyeleti csoport **felhasználók hozzáadása**.

    ![Felhasználókezelés](./media/zoom-tutorial/ic784703.png "felhasználók kezelése")

4. Az a **felhasználók hozzáadása** lapon, a következő lépésekkel:

    ![Felhasználók hozzáadása](./media/zoom-tutorial/ic784704.png "felhasználók hozzáadása")

    a. Mint **felhasználótípus**válassza **alapszintű**.

    b. Az a **e-mailek** beviteli mező, írja be az e-mail-címe érvényes Azure AD-fiók kíván üzembe helyezni.

    c. Kattintson a **Hozzáadás** parancsra.

> [!NOTE]
> Bármely más nagyítás felhasználói fiók létrehozása eszközöket használhatja, vagy nagyítás üzembe helyezni az Azure Active Directory által biztosított API-k felhasználói fiókok.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Nagyítás csempére kattint, meg kell kell automatikusan bejelentkezett a nagyítás, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
