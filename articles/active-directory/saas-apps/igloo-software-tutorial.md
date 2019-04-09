---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Igloo szoftverrel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Igloo szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 60ec322758a3c3b83e7b24cfba37daaec02fc4c0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270829"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Oktatóanyag: Az Azure Active Directory-integráció Igloo szoftverrel

Ebben az oktatóanyagban elsajátíthatja, hogyan Igloo szoftver integrálása az Azure Active Directory (Azure AD).
Igloo szoftver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Igloo szoftverek az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett Igloo szoftverre (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Igloo szoftverekkel rendelkező, a következő elemek szükségesek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Szoftver igloo egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Igloo szoftver támogatja-e **SP** által kezdeményezett egyszeri bejelentkezés
* Igloo szoftver támogatja-e **igény szerinti** felhasználók átadása

## <a name="adding-igloo-software-from-the-gallery"></a>Igloo szoftver hozzáadása a katalógusból

Az Azure AD-be Igloo szoftver integráció konfigurálásához, kell Igloo szoftver hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Igloo szoftver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Igloo szoftver**, jelölje be **Igloo szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában igloo szoftver](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Igloo szoftverrel **Britta Simon**.
Az egyszeri bejelentkezés működjön az Azure AD-felhasználót és a kapcsolódó felhasználó Igloo szoftveres hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés Igloo szoftverrel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Szoftver Igloo egyszeri bejelentkezés konfigurálása](#configure-igloo-software-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Igloo szoftver tesztfelhasználót](#create-igloo-software-test-user)**  – egy megfelelője a Britta Simon Igloo szoftver, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Igloo szoftverrel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Igloo szoftver** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Igloo szoftver tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<company name>.igloocommmunities.com`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [Igloo Szoftverügyfél támogatási csapatának](https://www.igloosoftware.com/services/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Igloo szoftver beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-igloo-software-single-sign-on"></a>Szoftver Igloo egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Igloo szoftver vállalati hely rendszergazdaként.

2. Nyissa meg a **vezérlőpultot**.

     ![Vezérlőpultot](./media/igloo-software-tutorial/ic799949.png "vezérlőpultot")

3. Az a **tagsági** lapra, majd **bejelentkezést a beállítások**.

    ![Bejelentkezési beállítások](./media/igloo-software-tutorial/ic783968.png "bejelentkezési beállítások")

4. A SAML-konfigurációja területen kattintson a **SAML-hitelesítés konfigurálása**.

    ![SAML-konfigurációja](./media/igloo-software-tutorial/ic783969.png "SAML-konfigurációja")

5. Az a **általános konfigurációs** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Általános konfiguráció](./media/igloo-software-tutorial/ic783970.png "általános konfiguráció")

    a. Az a **kapcsolatnevet** szövegmezőbe írjon be egy egyéni nevet a konfigurációnak.

    b. Az a **identitásszolgáltató bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Az a **identitásszolgáltató kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

    d. Válassza ki **kijelentkezési válaszok és a HTTP típusú** , **POST**.

    e. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **nyilvános tanúsítvány** szövegmezőbe.

6. Az a **válasz és a hitelesítési konfiguráció**, hajtsa végre az alábbi lépéseket:

    ![Válasz és a hitelesítési konfiguráció](./media/igloo-software-tutorial/IC783971.png "válasz és hitelesítés konfigurációját.")
  
    a. Mint **identitásszolgáltató**válassza **Microsoft ADFS**.

    b. Mint **azonosító típusának**válassza **E-mail cím**. 

    c. Az a **E-mail attribútum** szövegmezőbe írja be **emailaddress**.

    d. Az a **First Name attribútum** szövegmezőbe írja be **givenname**.

    e. Az a **utolsó név attribútum** szövegmezőbe írja be **Vezetéknév**.

7. Hajtsa végre az alábbi lépéseket a konfigurálás befejezéséhez:

    ![Felhasználó létrehozása bejelentkezési](./media/igloo-software-tutorial/IC783972.png "bejelentkezési felhasználó létrehozása") 

    a. Mint **bejelentkezési felhasználói létrehozása**válassza **új felhasználó létrehozása a helyen található, bejelentkezéskor**.

    b. Mint **bejelentkezési beállítások**válassza **használható SAML gombra a "Bejelentkezés" képernyőn**.

    c. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Igloo szoftver Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Igloo szoftver**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Igloo szoftver**.

    ![Az alkalmazások listáját a Igloo szoftver hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-igloo-software-test-user"></a>Tesztfelhasználó Igloo szoftver létrehozása

Nincs művelet elem, hogy a felhasználók átadásának Igloo szoftver konfigurálása.  

Hozzárendelt felhasználóval próbál bejelentkezni a hozzáférési panelen Igloo szoftver, a Igloo szoftver ellenőrzi, hogy a felhasználó létezik-e.  Ha nincs felhasználói fiók elérhető még, automatikusan létrehozott Igloo Software-től.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Igloo szoftver csempére kattint, akkor kell automatikusan megtörténik a a Igloo szoftver, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)