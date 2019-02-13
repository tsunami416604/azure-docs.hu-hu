---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Lifesize felhővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Lifesize felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086ecf512fff1d6e9678278e81c1897f69eb151
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191571"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Oktatóanyag: Az Azure Active Directory-integráció Lifesize felhővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Lifesize Felhőbeli integrálása az Azure Active Directory (Azure AD).
Lifesize Felhőbeli integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben Lifesize felhőbe való hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett Lifesize felhőbe (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Lifesize felhőalapú Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Lifesize felhőalapú egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Lifesize felhőalapú támogatja **SP** által kezdeményezett egyszeri bejelentkezés

* Lifesize felhőalapú támogatja **automatikus** felhasználók átadása

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Lifesize felhő hozzáadása a katalógusból

Lifesize Felhőbeli integrálása az Azure AD beállítása, hozzá kell Lifesize felhőalapú a galériából a felügyelt SaaS-alkalmazások listájára.

**Lifesize felhő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Lifesize felhőalapú**, jelölje be **Lifesize felhőalapú** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Lifesize felhő](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és Lifesize felhővel az Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Lifesize felhőben hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Lifesize felhővel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Felhőalapú Lifesize egyszeri bejelentkezés konfigurálása](#configure-lifesize-cloud-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Lifesize felhőalapú tesztfelhasználót](#create-lifesize-cloud-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Lifesize felhőben.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Lifesize felhő, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Lifesize felhőalapú** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Lifesize felhőalapú tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-relay.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.lifesizecloud.com/ls/?acs`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.lifesizecloud.com/<companyname>`

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-cím: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-azonosító és továbbítási állapot ezeket az értékeket. Kapcsolattartó [Lifesize felhőalapú ügyfél-támogatási csapatának](https://www.lifesize.com/en/support) bejelentkezési URL-CÍMÉT, és azonosító értéket, és beszerezheti a továbbítási állapot értékét az oktatóanyag későbbi részében kifejtett egyszeri bejelentkezés konfigurálása. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Lifesize felhő beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Lifesize felhőalapú egyszeri bejelentkezés konfigurálása

1. Egyszeri bejelentkezés az alkalmazáshoz, jelentkezzen be a rendszergazdai jogosultságokat Lifesize felhőalkalmazásba konfigurált beolvasása.

2. A jobb felső sarokban kattintson a nevére, és kattintson a a **speciális beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. A most kattintson a speciális beállításokban az **egyszeri bejelentkezési konfiguráció** hivatkozásra. Megnyílik a példány SSO konfigurálási lapjához.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Most konfigurálja a következő értékeket a SSO-konfigurációs felhasználói felületen.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. A **identitásszolgáltató kibocsátója** szövegmezőjébe illessze be az értéket a **Azure Ad-azonosító** Azure Portalról másolt.

    b.  A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
  
    d. Adja meg az értéket az SAML attribútumleképezések Utónév szövegmező `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Az attribútum a SAML-leképezés az a **Vezetéknév** szövegmezőben adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Az attribútum a SAML-leképezés az a **E-mail** szövegmezőben adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. A kattintson a konfiguráció ellenőrzéséhez a **teszt** gombra.

    >[!NOTE]
    >A sikeres teszteléséhez szüksége a konfigurációs varázsló befejezéséhez az Azure ad-ben, és hozzáférést is biztosít a felhasználók vagy csoportok, akiknek a tesztet hajthat végre.

6. Az egyszeri bejelentkezés engedélyezése ehhez a a **SSO engedélyezése** gombra.

7. Ezután a **frissítés** gombot, amellyel az összes a beállítások lesznek mentve. Ezt a paramétert értéket hoz létre. Másolása paramétert értékét, ami akkor jön létre, a szövegmezőbe, illessze be a **továbbítási állapot** szövegmező alatt **Lifesize felhőalapú tartomány és URL-címek** szakaszban.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Lifesize felhőbe Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Lifesize felhőalapú**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Lifesize felhőalapú**.

    ![Az alkalmazások listáját a Lifesize felhőalapú hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-lifesize-cloud-test-user"></a>Tesztfelhasználó Lifesize felhő létrehozása

Ebben a szakaszban egy Britta Simon nevű Lifesize felhőbeli felhasználó hoz létre. Lifesize felhő támogatja a felhasználók automatikus átadása. Az Azure ad a sikeres hitelesítés után a felhasználó automatikusan kiépítheti az alkalmazásban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Lifesize felhőalapú csempére kattint, Lifesize felhőalkalmazás bejelentkezési lapjának szerezheti be. Itt meg kell adnia a felhasználónevét, és ezt követően átirányítja az alkalmazás kezdőlapjára mutató.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
