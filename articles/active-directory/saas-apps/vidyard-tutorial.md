---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Vidyard |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Vidyard között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 94e5893caa4dc19702f45e9b42727aab4f884bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273855"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Vidyard

Ebben az oktatóanyagban elsajátíthatja, hogyan Vidyard integrálása az Azure Active Directory (Azure AD).
Vidyard integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Vidyard Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Vidyard (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Vidyard az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Vidyard egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Vidyard **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

* Támogatja a Vidyard **igény szerinti** felhasználók átadása

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard hozzáadása a katalógusból

Az Azure AD integrálása a Vidyard konfigurálásához hozzá kell Vidyard a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Vidyard hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Vidyard**válassza **Vidyard** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Vidyard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Vidyard nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Vidyard hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Vidyard tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Vidyard egyszeri bejelentkezés konfigurálása](#configure-vidyard-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Vidyard tesztfelhasználót](#create-vidyard-test-user)**  – egy megfelelője a Britta Simon Vidyard, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Vidyard, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Vidyard** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Vidyard tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Vidyard tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Ezek a értékei nem valódi. A tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett frissíteni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **Vidyard beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-vidyard-single-sign-on"></a>Vidyard egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Vidyard szoftver vállalati hely rendszergazdaként.

2. A Vidyard irányítópultján válassza ki **csoport** > **biztonsági**

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure1.png)

3. Kattintson a **új profil** fülre.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure2.png)

4. Az a **SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure3.png)

    a. Adja meg az általános profil nevét a **profilnév** szövegmezőbe.

    b. Másolás **egyszeri bejelentkezési felhasználói bejelentkezési oldal** értékét, és illessze be azt **bejelentkezési URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    c. Másolás **ACS URL** értékét, és illessze be azt **válasz URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    d. Másolás **kibocsátó/metaadatok URL-címe** értékét, és illessze be azt **azonosító** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    e. Nyissa meg a letöltött tanúsítvány-fájlt a Jegyzettömbben az Azure Portalról, és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    f. Az a **SAML-végpont URL-címe** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    g. Kattintson a **Megerősítés** gombra.

5. Válassza az egyszeri bejelentkezés lap **hozzárendelése** mellett egy meglévő profilt

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Miután létrehozta az egyszeri bejelentkezési profil, rendelje hozzá minden olyan csoportot, amelyhez a felhasználók hozzáférést Azure-on keresztül kell. Ha a felhasználó nem létezik, amelyhez hozzá lettek rendelve a csoporton belül, Vidyard automatikusan hozzon létre egy felhasználói fiókot, és a valós idejű szerepkör hozzárendelése.

6. Válassza ki a szervezet csoport, amely látható a **rendelje hozzá a rendelkezésre álló csoportok**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure5.png)

7. A hozzárendelt csoportokat láthatja a **jelenleg hozzárendelt csoportok**. Válasszon egy szerepkört beállításával a szervezet, és kattintson a csoport **megerősítése**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > További információkért tekintse meg [feljegyzett](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Vidyard Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Vidyard**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Vidyard**.

    ![Az alkalmazások listáját a Vidyard hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-vidyard-test-user"></a>Vidyard tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Vidyard jön létre. Vidyard támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az Vidyard, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Vidyard támogatási csapatának](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Vidyard csempére kattint, meg kell lehet automatikusan bejelentkezett a Vidyard, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

