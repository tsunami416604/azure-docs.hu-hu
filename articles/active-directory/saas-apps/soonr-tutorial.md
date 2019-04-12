---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Soonr munkahelyi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Soonr munkahelyi és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3be7d5b1a2aab37d3eb0863b3aa32d9163f5d63
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500538"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Soonr munkahelyi

Ebben az oktatóanyagban elsajátíthatja, hogyan Soonr munkahelyi integrálása az Azure Active Directory (Azure AD).
Soonr munkahelyi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, aki hozzáféréssel rendelkezik Soonr munkahelyi Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Soonr munkahelyhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Soonr munkahelyi konfigurálni az Azure AD-integráció, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Soonr munkahelyi egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Soonr munkahelyi **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-soonr-workplace-from-the-gallery"></a>Soonr munkahelyi hozzáadása a katalógusból

Az Azure AD integrálása a Soonr munkahelyi konfigurálásához hozzá kell Soonr munkahelyi a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Soonr munkahelyi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Soonr munkahelyi**, jelölje be **Soonr munkahelyi** az eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Soonr munkahelyi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és tesztelés az Azure AD egyszeri bejelentkezés Soonr munkahelyi nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Soonr munkahelyi hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Soonr munkahelyi tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Munkahelyi Soonr egyszeri bejelentkezés konfigurálása](#configure-soonr-workplace-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Soonr munkahelyi tesztfelhasználót](#create-soonr-workplace-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Soonr munkaterületen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Soonr munkahelyi, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Soonr munkahelyi** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Soonr munkahelyi tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<servername>.soonr.com/singlesignon/saml/SSO`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Soonr munkahelyi tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Soonr munkahelyi ügyfél-támogatási csapatának](https://awp.autotask.net/help/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **állítsa be a munkahelyi Soonr** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-soonr-workplace-single-sign-on"></a>Soonr munkahelyi egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Soonr munkahelyi** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [Soonr munkahelyi támogatása csapat](https://awp.autotask.net/help/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!Note]
> Ha Autotask munkahelyi konfigurálása segítségre van szüksége, tekintse meg [ezt oldal](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) juthat a munkahelyi fiókjával.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Soonr munkahelyi hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Soonr munkahelyi**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Soonr munkahelyi**.

    ![Az alkalmazások listáját a Soonr munkahelyi hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-soonr-workplace-test-user"></a>Soonr munkahelyi tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Soonr munkahelyi felhasználó hoz létre. Együttműködve [Soonr munkahelyi támogatási csapatának](https://awp.autotask.net/help/) a felhasználók hozzáadása az Soonr munkahelyi platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Soonr munkahelyi csempére kattint, akkor kell automatikusan megtörténik a a Soonr munkahelyhez, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

